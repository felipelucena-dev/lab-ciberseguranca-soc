# Bloco 4: Monitoramento de Acessos Suspeitos & Força Bruta

## O que é este bloco e o que ele avalia?
* **O que ele significa:** É a rotina de investigação de segurança focada em identificar tentativas de invasão e ataques de força bruta contra o serviço SSH.
* **Na prática:** É o olho clínico do analista SOC. Saber filtrar logs de autenticação permite detectar se um atacante está tentando adivinhar senhas.

## Comando de Execução e Rotina Operacional

1. **Elevação de Privilégio:**
   * **Comando:** `su -`
   * **Nota:** O arquivo `/var/log/auth.log` exige privilégios de `root`. Caso receba "Permissão negada", utilize o `su -` para autenticar.

2. **Varredura de Falhas de Autenticação:**
   * **Comando:** `grep -i "failed" /var/log/auth.log | tail -n 20`
   * **Explicação:** Filtra as últimas 20 linhas que contêm a palavra "failed", focando em eventos recentes de tentativa de login negado.

## Visão Forense
* **Registro de Auditoria:** Se um usuário sem privilégios tentar forçar o uso do `sudo`, o sistema gera um log de incidente (`user NOT in sudoers file`). Isso é uma auditoria real de segurança.
