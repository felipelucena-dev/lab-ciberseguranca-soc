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
