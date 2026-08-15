
# Laboratório de Cibersegurança & SOC - Diário de Bordo

Este repositório documenta a construção do meu laboratório prático para estudos de Defesa Cibernética e Resposta a Incidentes. O objetivo é criar um cenário controlado e isolado para treinar ataques e defesas sem riscos para minha máquina real.

## 🛠️ Fase 1: Infraestrutura e Rede
O primeiro passo é garantir que a base esteja isolada da internet.
* **Comandos principais:**
  * Instalação do VirtualBox: `sudo apt install virtualbox`
  * Criação da rede isolada: `vboxmanage hostonlyif create`
  * Atribuição de IP: `vboxmanage hostonlyif ipconfig vboxnet0 --ip 192.168.56.1`
* **Explicação:** A rede `Host-Only` cria uma conexão direta entre o meu computador e a VM, sem que a VM tenha acesso externo, essencial para simular cenários de laboratório.

## 💿 Aquisição do Sistema (ISO)
* **Comando:** `wget https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/debian-12.5.0-amd64-netinst.iso`
* **Explicação:** Baixo a ISO do Debian (versão minimalista) para que eu mesmo possa configurar os pacotes de segurança necessários posteriormente.

## 💻 Provisionamento da Máquina Virtual (VM)
Com o VirtualBox pronto, criei o "alvo":
1. **Criação:** `vboxmanage createvm --name "Lab-Debian-Alvo" --ostype "Debian_64" --register`
2. **Configuração de Hardware:** `vboxmanage modifyvm "Lab-Debian-Alvo" --memory 2048 --cpus 2 --nic1 hostonly --hostonlyadapter1 vboxnet0`
* **Explicação:** Defini os recursos básicos (2GB RAM/2 CPUs) e forcei a máquina a usar a interface de rede `vboxnet0` que criei anteriormente.

## 💾 Armazenamento e Mídia
Preparei os discos e o boot:
1. **Disco:** `vboxmanage createmedium disk --filename "Lab-Debian-Alvo.vdi" --size 20480`
2. **Controladoras:** Criei os barramentos SATA (para o disco) e IDE (para o drive de CD/DVD).
3. **Anexação:** `vboxmanage storageattach "Lab-Debian-Alvo" --storagectl "IDE" --port 0 --device 0 --type dvddrive --medium ~/lab-ciberseguranca-soc/debian-12.5.0-amd64-netinst.iso`
* **Explicação:** Aqui simulo a inserção física de um cabo de dados (SATA) e de um disco de instalação (ISO) na máquina virtual.

## 🚀 Inicialização e Boot
* **Comando:** `vboxmanage startvm "Lab-Debian-Alvo"`
* **Explicação:** O comando dispara o boot da VM. Caso o caminho da ISO esteja errado, use o comando de `storageattach` novamente para corrigir e reiniciar a máquina.

## 🛡️ Troubleshooting (Resolução de Problemas)
Documentação dos erros encontrados e correções aplicadas durante o processo:
* **O problema:** Ao tentar rodar o `apt update`, o sistema apresentava erros de assinatura, causados por uma divergência na data do sistema (o que impedia a verificação de certificados).
* **A solução:**
  1. Acesso como administrador: `su -`
  2. Ajuste manual da data: `date -s "2026-08-15 09:45:00"`
  3. Nova execução: `apt update`
* **Explicação:** A precisão do tempo é vital para a segurança em redes e sistemas Linux. Sem a data correta, os pacotes são vistos como não confiáveis.

## 🛡️ Fase 2: Implantação dos Sentinelas de Segurança

Com a infraestrutura base pronta, iniciamos a instalação e configuração das ferramentas de monitoramento, auditoria e defesa.

### 1. Auditoria e Logs do Kernel (Auditd)
Ferramenta essencial para monitorar ações críticas e chamadas de sistema.
* **Instalação:** `sudo apt install -y auditd`
* **Habilitação:** `sudo systemctl enable --now auditd`

### 2. Coleta de Logs e Proteção de Perímetro (Rsyslog e Fail2Ban)
Gerenciamento de logs e mitigação ativa contra ataques de força bruta no SSH.
* **Instalação:** `sudo apt install -y rsyslog fail2ban`
* **Habilitação:** `sudo systemctl enable --now rsyslog fail2ban`

### 3. Integridade de Arquivos (AIDE)
Criação de linha de base e verificação de integridade de arquivos críticos do sistema.
* **Instalação:** `sudo apt install -y aide`
* **Inicialização do Banco de Dados:** `sudo aideinit`

### 4. Scanner de Vulnerabilidades e Hardening (Lynis)
Análise de conformidade e segurança local do sistema operacional.
* **Instalação:** `sudo apt install -y lynis`
* **Execução da Varredura:** `sudo lynis audit system`
