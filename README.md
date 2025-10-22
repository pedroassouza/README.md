# README.md
Repositorio do meu primeiro teste utilizando kali e medusa.
PROJETO CYBERSEGURANÇA

Nesse projeto testei vulnerabilidades do Metasploitable 2 e DVWA utilizando do kali Linux e da ferramenta medusa.

Comecei olhando qual seria o IP alvo, que no caso estava no Metasploitable 2 e era o "192.168.98.128" (no meu caso), em seguida dentro do kali Linux utilizando o CMD verifiquei quais portas estavam abertas e a tinha algumas abertas, mas a principal e a que eu iria tirar proveito seria a FTP, o comando para verificar quais portas estvam abertos e o comando para adentrar a parte de ftp foi:

nmap -sV -p 21,22,80,445,139 192.168.98.128 = ver as portas abertas;

ftp 192.168.98.128 = adentrar o ftp do ip

Depois foi acessar o ftp desse ip pelo cmd, usando o usuário e login, para descobrir isso foi necessário criar um comando que da nomes possíveis de usuários e senhas, os comando eram;

echo -e "user\nmsfadmin\nadmin\nroot" > users.txt = para usuários;

echo -e "123456\npassword\nqwerty\nmsfadmin" > pass.txt = para senhas;

Depois disso ficar salvo, utilizei um código para que a medusa descobrisse qual seria o usuário e a senha usando essas possíveis combinações que foram dada;

medusa -h 192.168.98.128 -U users.txt -P pass.txt -M ftp -t 6

-h = IP alvo
-U lista de usuários;
-P = lista de senhas;
-M modulo a ser atacado
-t = tentativas simultâneas a serem feitas (threads utilizadas) 

Após isso ela me mostrou que o login e senha seria msfadmin e msfadmin respectivamente, depois disso tive acesso ao ftp desse ip alvo.

Depois fui testar como seria com um formulário on-line que no caso foi o DVWA.

Comecei apenas colocando um login e senha aleatórios e depois indo no modo de inspecionar do navegador > network > request;

Lá deu para verificar oque o servidor espera receber como parâmetros e isso vai ser útil para quando for utilizar o medusa, pois assim ela identifica oque é uma senha correta de uma errada.

Após isso criei mais uma wordlist e senhas possíveis para utilizar dentro da medusa e digitei os seguinte código:

echo -e "user\nmsfadmin\nadmin\nroot" > users.txt

echo -e "123456\npassword\nqwerty\nmsfadmin" > pass.txt 

medusa -h 192.168.56.102 -U users.txt -P pass.txt -M http \  
  -m PAGE:/dvwa/login.php \
  -m FORM:'username=^USER^&password=^PASS^&Login=Login' \
  -m FAIL:'Login failed' -t 6

No final ela me retornou que o usuário e senha seria: admin (user) e password (senha).  

Atenção: Após ela acertar essa combinação, ela deu que todas as seguintes também estavam corretas, oque se deve ao fato de que depois que ela acertou o primeiro os seguintes vieram com parâmetros de aberto pois ela já tinha feito o login, isso aconteceu apenas no modulo HTTP, diferente de ftp onde apenas um apareceu como correto.


Por fim também testei um ataque utilizando de enumeração e password spraying.

Utilizando os códigos: 

enum4linux -a 192.168.98.128 | tee enum4_output.txt;

E depois 

less enum4_output.txt;

Consigo acessar uma lista de usuários, assim eu consigo pegar todos eles e testar algumas senhas até que alguma seja a correta em algum deles, utilizo também de comandos para salvar a lista de usuários que quero que seja testado as senhas e as senhas que quero que seja testadas neles.

echo -e "user\nnmsfadmin\nservice" > smb_users.txt;

echo -e "password\n123456\nWelcome123\nnmsfadmin" > senhas_spray.txt

Após isso, digito outro código para que a medusa comece a testar as senhas.

medusa -h 192.168.56.102 -U smb_users.txt -P senhas_spray.txt -M smbnt -t 2 -T 50

Um pouco diferente do primeiro, pois esse faz com que as tentaaivas sejam mais espaçadas, assim evitando bloqueios.

Após isso ela viu que o usuário "admin" tinha como senha "admin". 

Para ver se estava correto utilizei o comando: 

smbclient -L //192.168.56.102 -U msfadmin;

Após isso vai pedir a senha do usuário e colocando ela corretamente obtive acesso a uma lista de compartilhamente que contia mais informações.

Depois desses teste as recomendações que eu tenho são de: bloqueio automático de IP após múltiplas tentativas incorretas, autenticações em 2 fatores, CAPTCHA, senhas fortes e usualmente trocadas, monitoramento inteligente com IA e também auditorias regulares para que se possam achar erros (se tiver) e corrigi-los antes que possam dar problemas.  
 

