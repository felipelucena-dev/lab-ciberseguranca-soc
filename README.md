cat << 'EOF' > README.md
# Laboratório de Cibersegurança & SOC - Diário de Bordo 🚀

Este repositório documenta a construção do meu laboratório prático para estudos de Defesa Cibernética e Resposta a Incidentes. O objetivo é criar um cenário controlado e isolado para treinar ataques e defesas sem riscos para minha máquina real.

---

## 🛠️ Fase 1: Infraestrutura e Rede
O primeiro passo é garantir que a base esteja isolada da internet.

*   **Instalação do VirtualBox:** `sudo apt install virtualbox`
*   **Criação da rede isolada:** `vboxmanage hostonlyif create`
*   **Atribuição de IP:** `vboxmanage hostonlyif ipconfig vboxnet0 --ip 192.168.56.1`
*   **Explicação:** A rede Host-Only cria uma conexão direta entre o meu computador e a VM, sem que a VM tenha acesso externo, essencial para simular cenários de laboratório.

### 💿 Aquisição do Sistema (ISO)
*   **Comando:** `wget https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/debian-12.5.0-amd64-netinst.iso`
*   **Explicação:** Baixo a ISO do Debian (versão minimalista) para que eu mesmo possa configurar os pacotes de segurança necessários posteriormente.

### 💻 Provisionamento da Máquina Virtual (VM)
Com o VirtualBox pronto, criei o "alvo":

*   **Criação:** `vboxmanage createvm --name "Lab-Debian-Alvo" --ostype "Debian_64" --register`
*   **Configuração de Hardware:** `vboxmanage modifyvm "Lab-Debian-Alvo" --memory 2048 --cpus 2 --nic1 hostonly --hostonlyadapter1 vboxnet0`
*   **Explicação:** Defini os recursos básicos (2GB RAM/2 CPUs) e forcei a máquina a usar a interface de rede vboxnet0 que criei anteriormente.

### 💾 Armazenamento e Mídia
Preparei os discos e o boot:

*   **Disco:** `vboxmanage createmedium disk --filename "Lab-Debian-Alvo.vdi" --size 20480`
*   **Controladoras:** Criei os barramentos SATA (para o disco) e IDE (para o drive de CD/DVD).
*   **Anexação:** `vboxmanage storageattach "Lab-Debian-Alvo" --storagectl "IDE" --port 0 --device 0 --type dvddrive --medium ~/lab-ciberseguranca-soc/debian-12.5.0-amd64-netinst.iso`
*   **Explicação:** Aqui simulo a inserção física de um cabo de dados (SATA) e de um disco de instalação (ISO) na máquina virtual.

### 🚀 Inicialização e Boot
*   **Comando:** `vboxmanage startvm "Lab-Debian-Alvo"`
*   **Explicação:** O comando dispara o boot da VM. Caso o caminho da ISO esteja errado, use o comando de storageattach novamente para corrigir e reiniciar a máquina.

### 🛡️ Troubleshooting (Resolução de Problemas - Fase 1)
Documentação dos erros encontrados e correções aplicadas durante o processo:

*   **O problema:** Ao tentar rodar o `apt update`, o sistema apresentava erros de assinatura, causados por uma divergência na data do sistema (o que impedia a verificação de certificados).
*   **A solução:**
    *   Acesso como administrador: `su -`
    *   Ajuste manual da data: `date -s "2026-08-15 09:45:00"`
    *   Nova execução: `apt update`
*   **Explicação:** A precisão do tempo é vital para a segurança em redes e sistemas Linux. Sem a data correta, os pacotes são vistos como não confiáveis.

---

## 🛡️ Fase 2: Implantação dos Sentinelas de Segurança
Com a infraestrutura base pronta, iniciamos a instalação, configuração e o entendimento operacional das ferramentas de monitoramento e defesa.

### 1. Auditoria e Logs do Kernel (Auditd)
Ferramenta essencial para monitorar ações críticas, chamadas de sistema e acessos a arquivos sensíveis.

*   **Instalação:** `sudo apt install -y auditd`
*   **Habilitação:** `sudo systemctl enable --now auditd`
*   **Verificação de Saúde:** `sudo systemctl status auditd --no-pager`
*   **Onde investigar:** Os logs brutos são gerados em `/var/log/audit/audit.log`.
*   **Visão do Analista:** O arquivo é denso; utilize o comando `ausearch` (ex: `ausearch -m USER_AUTH`) para filtrar eventos de autenticação e ações privilegiadas do kernel em vez de ler tudo manualmente.

### 2. Coleta de Logs e Proteção de Perímetro (Rsyslog e Fail2Ban)
Gerenciamento centralizado de logs e mitigação ativa contra ataques de força bruta no SSH.

*   **Instalação:** `sudo apt install -y rsyslog fail2ban`
*   **Habilitação:** `sudo systemctl enable --now rsyslog fail2ban`
*   **Verificação de Defesa:** `sudo fail2ban-client status sshd` (Exibe o status das prisões de IPs ativos).
*   **Monitoramento de Ataques:** `grep -i "failed" /var/log/auth.log | tail -n 20`
*   **Visão do Analista:** Permite identificar se o perímetro está sofrendo tentativas automatizadas de invasão por força bruta e se o firewall está bloqueando os invasores corretamente.

### 3. Integridade de Arquivos (AIDE)
Criação de linha de base criptográfica e verificação de integridade de arquivos críticos do sistema.

*   **Instalação:** `sudo apt install -y aide`
*   **Inicialização do Banco de Dados:** `sudo aideinit`
*   **Execução da Auditoria:** `sudo aide --config /etc/aide/aide.conf --check`
*   **Visão do Analista:** O AIDE compara o estado atual com a base inicial. Qualquer alteração em diretórios sensíveis como `/bin`, `/sbin` ou arquivos de configuração do sistema indica possível comprometimento ou instalação de backdoors.

### 4. Scanner de Vulnerabilidades e Hardening (Lynis)
Análise de conformidade, configuração de segurança local e boas práticas no sistema operacional.

*   **Instalação:** `sudo apt install -y lynis`
*   **Execução da Varredura:** `sudo lynis audit system`
*   **Onde ver o resultado:** O relatório detalhado é gerado em `/var/log/lynis-report.dat`.
*   **Visão do Analista:** Avalia o índice de segurança (*Hardening Index*) e aponta sugestões e avisos práticos (*Suggestions/Warnings*) para fechar brechas de configuração no Debian.

---

## 📋 Fase 3: A Rotina Operacional do Analista SOC (Plantão de Monitoramento)
Após a subida dos serviços, o trabalho de monitoramento exige validação contínua da saúde do ambiente. Estes são os comandos padrão executados na rotina:

1.  **Check-up de Saúde dos Sentinelas:**
    ```bash
    systemctl status rsyslog auditd fail2ban --no-pager
    ```
    *Explicação:* Garante que os serviços fundamentais de coleta de logs, auditoria e bloqueio perimetral continuem ativos e operantes.
2.  **Monitoramento de Acessos Suspeitos (Força Bruta):**
    ```bash
    grep -i "failed" /var/log/auth.log | tail -n 20
    ```
    *Explicação:* Analisa as últimas falhas de autenticação para detectar se há varreduras de senha ou ataques ativos contra o serviço SSH.
3.  **Auditoria de Integridade do Host:**
    ```bash
    sudo aide --config /etc/aide/aide.conf --check
    ```
    *Explicação:* Valida se houve adulteração não autorizada de binários ou arquivos vitais do sistema operacional desde o último check.
EOF
