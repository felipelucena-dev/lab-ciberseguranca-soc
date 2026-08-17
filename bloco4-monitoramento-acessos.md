# Bloco 4: Monitoramento de Acessos Suspeitos & Força Bruta

## O que é este bloco e o que ele avalia?
* **O que ele significa:** É a rotina de investigação de segurança focada em identificar tentativas de invasão, varreduras de porta e ataques de força bruta contra o serviço de acesso remoto (SSH).
* **O que ele avalia/olha:** Ele examina os registros de autenticação do sistema operacional em busca de falhas repetidas de login, mapeando origens suspeitas e comportamentos anômalos.
* **Na prática do plantão:** É o olho clínico do analista SOC. Saber filtrar logs de autenticação rapidamente permite detectar se um atacante está tentando adivinhar senhas em looping na sua infraestrutura.

## Comando de Execução e Rotina Operacional

1. **Elevação de Privilégio (Obrigatória para Logs Protegidos):**
   * **Comando:** `su -`
   * **Por que é necessário:** O arquivo de log de autenticação (`/var/log/auth.log`) é restrito por razões de segurança do Linux. Usuários comuns não possuem permissão de leitura, gerando o erro de *Permissão negada*. É preciso autenticar-se como `root`.

2. **Varredura de Falhas de Autenticação:**
   * **Comando:** `grep -i "failed" /var/log/auth.log | tail -n 20`
   * **Explicação detalhada:**
     * `grep -i "failed"`: Filtra no arquivo todas as linhas que contêm a palavra "failed" (ignorando maiúsculas/minúsculas devido ao parâmetro `-i`).
     * `|` (Pipe): Envia o resultado do filtro para o próximo comando.
     * `tail -n 20`: Exibe apenas as últimas 20 ocorrências encontradas, focando nos eventos mais recentes.

## Visão Forense e Resposta do Sistema
* **Registro de Auditoria de Acesso Negado:** Se um usuário sem privilégios tentar forçar o uso do `sudo` para ler arquivos protegidos, o próprio sistema gera um log de incidente (ex: `user NOT in sudoers file`), documentando a tentativa não autorizada com data, hora e nome da máquina. Isso demonstra a eficácia das políticas de controle de acesso do Linux.
