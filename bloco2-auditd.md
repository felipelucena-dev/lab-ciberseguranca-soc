# Bloco 2: Auditd (systemd + auditd.service)

## O que é este bloco e o que ele avalia?
* **O que ele significa:** É o painel de status do **Auditd**, o subsistema de auditoria de segurança avançada do Kernel do Linux.
* **O que ele avalia/olha:** Ele monitora eventos profundos e privilegiados da máquina (como uso de comandos de superusuário, acesso a arquivos confidenciais e alterações de permissões críticas).
* **Na prática do plantão:** É o seu sentinela forense. Se um invasor tentar escalar privilégios ou mexer no que não deve, é o auditd que registra os rastros mais profundos.

## Análise Linha por Linha

1. **`Loaded: loaded (/usr/lib/systemd/system/auditd.service; enabled; preset: enabled)`**
   * **Significado e tradução:** 
     * `loaded`: O sistema encontrou e leu a configuração.
     * `/usr/lib/systemd/system/auditd.service`: Endereço exato no disco. As pastas `/usr/lib` (bibliotecas) guardam o subsistema de inicialização (`systemd/system`), onde fica a receita exata (`auditd.service`).
     * `enabled`: Ativado para iniciar sozinho quando o computador liga.
   * **Possível erro para olhar:** Se aparecer `disabled`, o kernel ficará sem auditoria automática após um reboot.

2. **`Active: active (running) since Sun 2026-08-16 21:49:28 -03; 3min 12s ago`**
   * **Significado:** O estado atual em tempo real. Mostra que o auditor está rodando ativamente desde a data e hora indicadas.
   * **Possível erro para olhar:** Se estiver escrito **`failed`** em vermelho, o subsistema de auditoria travou e ações críticas na máquina passarão sem rastreio.

3. **`Invocation: f79ba718c98b4269b7dd7dfceb0b8b41`**
   * **Significado:** O código identificador único de sessão (*Invocation ID*). O systemd gera essa sequência para rastrear estritamente esta inicialização específica do auditd.
   * **Possível erro para olhar:** Mudanças constantes nesse código indicam falhas internas e quedas seguidas (*crash loop*).

4. **`Docs: man:auditd(8)`**
   * **Significado:** Atalho para a documentação no manual do sistema (`man`) na seção 8 de utilitários administrativos.
   * **Possível erro para olhar:** Linha informativa (não apresenta falhas operacionais).

5. **`https://github.com/linux-audit/audit-documentation`**
   * **Significado:** Link oficial para a documentação técnica do projeto no GitHub.
   * **Possível erro para olhar:** Apenas informativo.

6. **`Process: 524 ExecStart=/usr/sbin/auditd (code=exited, status=0/SUCCESS)`**
   * **Significado:** Histórico do disparo. O processo `524` rodou o binário de auditoria (`/usr/sbin/auditd`), finalizou a partida (`code=exited`) e obteve êxito total (`status=0/SUCCESS`).
   * **Possível erro para olhar:** Se o status for diferente de zero (ex: `status=1`), o auditd falhou ao carregar suas regras de monitoramento.

7. **`Main PID: 540 (auditd)`**
   * **Significado:** O número de crachá (*Process ID* 540) do auditor rodando na memória RAM.
   * **Possível erro para olhar:** Se o PID sumir, o daemon principal do auditor morreu.

8. **`Tasks: 2 (limit: 2318)`**
   * **Significado:** Tarefas ou threads simultâneas executando (2), com limite máximo de 2318.
   * **Possível erro para olhar:** Tarefas zeradas indicam que o processo de auditoria travou internamente.

9. **`Memory: 2M (peak: 2.3M)`**
   * **Significado:** Consumo de memória RAM atual (2 MB) e o pico máximo alcançado (`2.3M`).
   * **Possível erro para olhar:** Disparos absurdos de memória indicam falhas de alocação no kernel.

10. **`CPU: 17ms`**
    * **Significado:** Esforço total acumulado de processamento desde o início (17 milissegundos).
    * **Possível erro para olhar:** CPU travada em 100% indica gargalo no processamento dos logs de auditoria.

11. **`CGroup: /system.slice/auditd.service`**
    * **Significado:** Grupo de Controle. Endereço lógico onde o systemd isolou a "fatia" de hardware dedicada a este auditor.
    * **Possível erro para olhar:** Falhas de vínculo na fatia de controle do systemd.

12. **`└─540 /usr/sbin/auditd`**
    * **Significado:** A sublinha com o comando binário real executado pelo systemd para manter o processo `540` ativo.
    * **Possível erro para olhar:** Se o caminho do binário for corrompido ou apagado, o serviço falhará no próximo boot.
