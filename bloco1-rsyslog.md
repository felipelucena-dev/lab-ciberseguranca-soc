# Bloco 1: Rsyslog (systemd + rsyslog.service)

## O que é este bloco e o que ele avalia?
* **O que ele significa:** É o painel de saúde e status do **Rsyslog**, que é o serviço central de logs do sistema operacional Linux.
* **O que ele avalia/olha:** Ele verifica se o "diário oficial" da máquina está vivo e operando. É ele que recolhe as mensagens geradas por todos os aplicativos, serviços e falhas do sistema e as grava nos arquivos de log (como `/var/log/syslog`). 
* **Na prática do plantão:** Se este bloco estiver ativo, significa que a sua "caixa-preta" está gravando tudo o que acontece. Se ele cair, você perde a visibilidade dos eventos gerais da máquina.

## Análise Linha por Linha

1. **`Loaded: loaded (/usr/lib/systemd/system/rsyslog.service; enabled; preset: enabled)`**
   * **Significado e tradução:** 
     * `loaded`: O sistema encontrou e leu a configuração.
     * `/usr/lib/systemd/system/rsyslog.service`: Endereço exato no disco. A pasta `/usr` (arquivos de usuário), dentro de `/lib` (bibliotecas), guarda o subsistema de inicialização (`systemd/system`), onde fica a receita exata (`rsyslog.service`).
     * `enabled`: Ativado para iniciar sozinho quando o computador liga.
   * **Possível erro para olhar:** Se aparecer `disabled`, ele não vai ligar sozinho após um reboot.

2. **`Active: active (running) since Sun 2026-08-16 21:49:29 -03; 3min 12s ago`**
   * **Significado:** O estado atual em tempo real. Mostra que o serviço está ativo e rodando (`running`) desde a data e hora indicadas.
   * **Possível erro para olhar:** Se estiver escrito **`failed`** em vermelho, o serviço caiu e o sistema parou de registrar os logs.

3. **`Invocation: 79b415382ea74137b42b42eb9257667f`**
   * **Significado:** O código identificador único de sessão (*Invocation ID*). O systemd gera essa sequência toda vez que o serviço liga para rastrear logs específicos.
   * **Possível erro para olhar:** Se esse código ficar mudando sozinho sem parar, indica que o serviço está sofrendo quedas e reiniciando em loop (*crash loop*).

4. **`TriggeredBy: ● rsyslog.socket`**
   * **Significado:** O gatilho de disparo. O ponto preto (`●`) com `rsyslog.socket` indica que o Rsyslog tem um "sensor" de porta/rede que desperta o serviço principal só quando chegam novas mensagens.
   * **Possível erro para olhar:** Se o soquete associado falhar (`failed`), o Rsyslog perde o acionamento automático.

5. **`Docs: man:rsyslogd(8)`**
   * **Significado:** Atalho para a documentação (`man` = Manual) na seção 8 de comandos administrativos.
   * **Possível erro para olhar:** Linha informativa (não costuma apresentar falhas operacionais diretas).

6. **`man:rsyslog.conf(5)`**
   * **Significado:** Aponta para o manual do arquivo de configuração (`conf`) na seção 5 de formatos de arquivos.
   * **Possível erro para olhar:** Apenas informativo.

7. **`https://www.rsyslog.com/doc/`**
   * **Significado:** Link oficial para a documentação web do projeto.
   * **Possível erro para olhar:** Apenas informativo.

8. **`Main PID: 626 (rsyslogd)`**
   * **Significado:** O número de crachá de identificação na memória RAM (*Process ID* 626) do programa principal.
   * **Possível erro para olhar:** Se o PID sumir, o processo principal morreu.

9. **`Tasks: 4 (limit: 2318)`**
   * **Significado:** Linhas de execução simultâneas (threads) que o serviço está usando (4), com teto máximo de 2318.
   * **Possível erro para olhar:** Tarefas zeradas indicam travamento interno.

10. **`Memory: 3M (peak: 3.2M)`**
    * **Significado:** Consumo de Memória RAM atual (3 Megabytes) e o pico máximo alcançado (`3.2M`).
    * **Possível erro para olhar:** Um pico de memória disparando para gigabytes indica vazamento (*memory leak*).

11. **`CPU: 49ms`**
    * **Significado:** O tempo total acumulado de esforço real do processador desde que foi ligado (49 milissegundos).
    * **Possível erro para olhar:** Se a CPU travar em 100% de uso contínuo, há um loop de processamento.

12. **`CGroup: /system.slice/rsyslog.service`**
    * **Significado:** O Grupo de Controle (*Control Group*). Endereço lógico onde o systemd isolou a "fatia" para gerenciar os recursos de hardware.
    * **Possível erro para olhar:** Erros de vínculo na fatia de controle do systemd.

13. **`└─626 /usr/sbin/rsyslogd -n -iNONE`**
    * **Significado:** O comando binário executado para manter o processo `626` rodando, localizado em `/usr/sbin/rsyslogd` com os parâmetros `-n` e `-iNONE`.
    * **Possível erro para olhar:** Se o caminho do binário for alterado ou apagado, falhará no próximo boot.
