Desativando a solicitação de senha do Windows 11 via terminal
Preparação
Antes de iniciar, é importante entender que estaremos modificando o registro do Windows. O registro é um banco de dados hierárquico que armazena configurações de baixo nível para o sistema operacional Windows e aplicativos que optam por usá-lo. Modificações incorretas no registro podem causar problemas sérios, então é recomendável criar um ponto de restauração do sistema antes de prosseguir.
Passo 1: Abrir o PowerShell como administrador

Pressione a tecla Windows + X no teclado
No menu que aparecer, procure por "Windows Terminal (Admin)" ou "PowerShell (Admin)"
Clique com o botão direito e selecione "Executar como administrador"
Se aparecer uma solicitação do Controle de Conta de Usuário (UAC), clique em "Sim" para permitir que o PowerShell faça alterações no seu dispositivo

Alternativamente, você pode:

Clicar no menu Iniciar
Digitar "PowerShell"
Clicar com o botão direito no "Windows PowerShell" ou "PowerShell"
Selecionar "Executar como administrador"

Passo 2: Desativando o Windows Hello para aplicativos
O primeiro comando que vamos executar desativa o Windows Hello para aplicativos no nível do sistema:
reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\PassportForWork" /v Enabled /t REG_DWORD /d 0 /f
Vamos analisar o que cada parte deste comando faz:

reg add: Este é o comando para adicionar uma nova entrada no registro do Windows
"HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\PassportForWork": Este é o caminho completo para a chave do registro que estamos modificando

HKEY_LOCAL_MACHINE: Uma das cinco chaves raiz do registro, contém configurações específicas do computador
SOFTWARE\Policies\Microsoft\PassportForWork: O caminho para as configurações do Windows Hello


/v Enabled: Especifica que estamos adicionando ou modificando um valor chamado "Enabled"
/t REG_DWORD: Define o tipo de valor como DWORD (um valor numérico de 32 bits)
/d 0: Define o valor como 0 (desativado). Se fosse 1, estaria ativado.
/f: Força a substituição sem pedir confirmação, se o valor já existir

Este comando desativa o Windows Hello no nível do sistema, o que inclui a autenticação biométrica e por PIN para aplicativos.
Passo 3: Desativando o gerenciador de credenciais para o Microsoft Edge
O segundo comando desativa especificamente o gerenciador de credenciais para o navegador Microsoft Edge:
reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Edge" /v PasswordManagerEnabled /t REG_DWORD /d 0 /f
Analisando este comando:

reg add: Novamente, estamos adicionando uma entrada no registro
"HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Edge": O caminho para as configurações do Edge
/v PasswordManagerEnabled: Estamos modificando o valor que controla se o gerenciador de senhas está ativado
/t REG_DWORD: Define o tipo como DWORD
/d 0: Define como 0 (desativado)
/f: Força a substituição sem pedir confirmação

Se você usa outros navegadores como Google Chrome ou Firefox, pode ser necessário fazer configurações semelhantes. Para o Chrome, por exemplo, seria:
reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome" /v PasswordManagerEnabled /t REG_DWORD /d 0 /f
Passo 4: Desativando o provedor de credenciais do Windows
Para uma solução mais completa, podemos também desativar o provedor de credenciais web do Windows:
reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{B12744B8-5BB7-463a-B85E-BB7627E73002}" /v Disabled /t REG_DWORD /d 1 /f
Este comando:

Adiciona um valor chamado "Disabled" na chave do registro que controla o provedor de credenciais da web
Define-o como 1 (desativado)
O ID entre chaves é o identificador único do provedor de credenciais da web

Passo 5: Reiniciar o computador
Após executar esses comandos, é necessário reiniciar o computador para que as alterações tenham efeito completo. Você pode fazer isso com o comando:
shutdown /r /t 0
Onde:

/r: Indica que queremos reiniciar o computador
/t 0: Define o tempo de espera para 0 segundos (reinício imediato)

Verificando as alterações
Após reiniciar, você pode verificar se as alterações foram aplicadas corretamente executando:
reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\PassportForWork" /v Enabled
Isso deve retornar:
HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\PassportForWork
    Enabled    REG_DWORD    0x0
Revertendo as alterações (se necessário)
Se você precisar reverter essas alterações no futuro, pode usar os mesmos comandos, mas alterando o valor final:
reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\PassportForWork" /v Enabled /t REG_DWORD /d 1 /f
reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Edge" /v PasswordManagerEnabled /t REG_DWORD /d 1 /f
