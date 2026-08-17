# Bloco 3: Fail2ban (systemd + fail2ban.service)

## O que é este bloco e o que ele avalia?
* **O que ele significa:** É o painel de status do **Fail2ban**, o software de proteção contra ataques de força bruta e invasões automatizadas.
* **O que ele avalia/olha:** Ele monitora arquivos de log em busca de falhas repetidas de login (como tentativas erradas de senha SSH) e bane temporariamente o endereço IP do atacante direto no firewall do Linux.
* **Na prática do plantão:** É o seu escudo de defesa ativa. Se ele cair ou falhar, a máquina fica vulnerável a milhares de tentativas automatizadas de invasão por força bruta sem nenhum bloqueio automático.

## Análise Linha por Linha

1. **`Loaded: loaded (/usr/lib/systemd/system/fail2ban.service; enabled; preset: enabled)`**
   * **Significado e tradução:** 
     * `loaded`: O system encontrou e leu a configuração.
     * `/usr/lib/systemd/system/fail2ban.service`: Endereço exato no disco do systemd para o serviço de banimento.
     * `enabled`: Ativado para iniciar sozinho quando o computador liga.
   * **Possível erro para olhar:** Se aparecer `disabled`, o servidor ficará totalmente desprotegido contra ataques de força bruta após um reboot.

2. **`Active: active (running) since Sun 2026-08-16 21:49:28 -03; 3min 12s ago`**
   * **Significado:** O estado atual em tempo real. Mostra que o protetor está ativo e vigiando desde a data e hora indicadas.
   * **Possível erro para olhar:** Se estiver escrito **`failed`** em vermelho, o fail2ban travou e os ataques passarão livres.

3. **`Invocation: d48c8b...`**
   * **Significado:** O código identificador único de sessão (*Invocation ID*) gerado pelo systemd para rastrear estritamente esta execução específica.
   * **Possível erro para olhar:** Variações bruscas ou reinicializações constantes indicam que o serviço está caindo e subindo sozinho.

4. **`Docs: man:fail2ban(1)`**
   * **Significado:** Atalho para o manual do comando na seção 1.
   * **Possível erro para olhar:** Linha informativa (sem impacto operacional direto).

5. **`Process: 610 ExecStart=/usr/bin/fail2ban-server (code=exited, status=0/SUCCESS)`**
   * **Significado:** Histórico da inicialização. O processo rodou o servidor fail2ban (`/usr/bin/fail2ban-server`), finalizou a carga inicial com sucesso total (`status=0/SUCCESS`).
   * **Possível erro para olhar:** Status diferente de zero indica falha ao iniciar o daemon de bloqueio.

6. **`Main PID: 625 (fail2ban-server)`**
   * **Significado:** O número de crachá (*Process ID* 625) do servidor rodando na memória RAM.
   * **Possível erro para olhar:** Ausência do PID significa que o serviço principal morreu.

7. **`Tasks: 3 (limit: 2318)`**
   * **Significado:** Quantidade de threads ativas executando a proteção (3).
   * **Possível erro para olhar:** Zero tarefas indica travamento interno do daemon.

8. **`Memory: 12.4M (peak: 13.1M)`**
   * **Significado:** Consumo de memória RAM atual (12.4 MB) e o pico máximo alcançado (`13.1M`).
   * **Possível erro para olhar:** Vazamentos anômalos de memória que esgotem os recursos da máquina.

9. **`CPU: 120ms`**
   * **Significado:** Esforço total de processamento acumulado desde o início (120 milissegundos).
   * **Possível erro para olhar:** CPU travada em 100% indica loop de processamento nos logs.

10. **`CGroup: /system.slice/fail2ban.service`**
    * **Significado:** Fatia de controle de hardware isolada pelo systemd para gerenciar este serviço.
    * **Possível erro para olhar:** Falhas de vínculo na fatia de controle do systemd.

11. **`└─625 /usr/bin/fail2ban-server -xf --async`**
    * **Significado:** O binário real rodando em segundo plano gerenciando ativamente as regras de banimento.
    * **Possível erro para olhar:** Se o caminho do binário for corrompido ou apagado, o fail2ban falhará no próximo boot.
