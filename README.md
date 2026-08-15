# Laboratório de Cibersegurança & SOC - Diário de Bordo

Este repositório documenta a construção do meu laboratório prático para estudos de Defesa Cibernética, Análise de Logs, Resposta a Incidentes e simulações controladas.

## 🛠️ Fase Atual: Configuração da Infraestrutura
* **Hipervisor:** Oracle VM VirtualBox (Versão 7.0) instalado no host Linux.
* **Isolamento de Rede:** Configuração planejada de Rede Host-Only para simulação de cenários de ataque e defesa sem exposição à internet pública.

## 🔧 Administração de Sistemas: Instalação e Automação via CLI
Procedimentos executados via terminal Linux:
1. **VirtualBox:** Configuração de repositórios Oracle e instalação via `apt`.
2. **Rede Host-Only:** Criação da interface de rede isolada via `vboxmanage`.

## 🌐 Arquitetura de Rede: Rede Isolada (Host-Only)
Para garantir a segurança nas simulações de incidentes, uma rede isolada foi configurada via CLI:
1. **Geração da Interface:** `vboxmanage hostonlyif create` (criação da interface `vboxnet0`).
2. **Definição de Roteamento Base:** `vboxmanage hostonlyif ipconfig vboxnet0 --ip 192.168.56.1` (atribuição do IP de gateway para o host).

## 💿 Aquisição de Mídia de Instalação (ISO)
* **Sistema Operacional:** Debian 12 (Netinst)
* **Método de Aquisição:** Download automatizado via CLI utilizando `wget`.
* **Propósito:** Servir como base para a criação da primeira Máquina Virtual (VM) isolada no ambiente de laboratório.

## 💻 Provisionamento de Máquina Virtual (VM) via CLI
Para estruturar o alvo de testes no laboratório, os comandos de criação e ajuste de hardware foram executados:
1. **Criação da VM:** `vboxmanage createvm --name "Lab-Debian-Alvo" --ostype "Debian_64" --register` (definição do perfil e registro no hipervisor).
2. **Atribuição de Recursos & Rede:** `vboxmanage modifyvm "Lab-Debian-Alvo" --memory 2048 --cpus 2 --nic1 hostonly --hostonlyadapter1 vboxnet0` (alocação de 2GB de RAM, 2 CPUs e isolamento na interface `vboxnet0`).

## 💾 Configuração de Armazenamento e Mídia (CLI)
Para habilitar a instalação do sistema operacional na VM isolada, a seguinte infraestrutura de hardware virtual foi configurada:
1. **Criação de Disco Rígido Virtual:** `vboxmanage createmedium disk ... --size 20480 --format VDI` (alocação de um disco VDI de 20 GB).
2. **Controladoras de Barramento:** Adição de controladoras SATA (para o disco rígido principal) e IDE (para a unidade óptica).
3. **Anexação de Mídia:** Conexão do disco rígido virtual e montagem da ISO do Debian 12 na unidade de CD/DVD virtual da VM.

## 🚀 Inicialização e Boot da VM (CLI)
* **Comando de Acionamento:** `vboxmanage startvm "Lab-Debian-Alvo"`
* **Status:** Máquina virtual ligada com sucesso, inicializando o instalador do Debian a partir da ISO montada na controladora IDE, pronta para provisionamento do sistema operacional alvo na rede isolada.

## 🛠️ Ajuste de Caminho de Mídia e Re-boot (CLI)
* **Correção:** Ajustado o caminho da ISO do Debian para o diretório correto do workspace (`~/lab-ciberseguranca-soc/`).
* **Comando de Reatribuição:** `vboxmanage storageattach ... --medium ~/lab-ciberseguranca-soc/debian-12.5.0-amd64-netinst.iso`
* **Status:** Máquina reiniciada com sucesso, agora com o instalador do Debian carregando corretamente na tela de boot.
