# whmcs-on-server #linux #ubuntu #whmcs #hosting
Instalando WHMCS diretamente no servodor linux sem auxilio de um painel de controle

Nesse documento vou ensina-lós a instalar alguns componentes na sua VPS para que você possa instalar o WHMCS Diretamente no servidor sem a necessidade de usar um painel de controle como cPanel por exemplo.

OBS: Este metodo foi testado apenas no servidor Ubuntu 20.04, Más creio que funcione em versões posteriores e na 18.04 tambem.

Comandos:

1. Atualizar pacotes do sistema:

```
sudo apt update
```

```
sudo apt upgrade
```

2. Liberar as portas:

```
ufw allow 80
ufw allow 443
```

3. Instalar o NGINX

```
apt install nginx -y
```

 Apos o programa ser instalado execute o comando abaixo.

 ```
 systemctl enable nginx
 ```

4. Instalar o php
   ```
   apt-get install php7.4 -y
   ```

   Após a instalação ser concluida execute o comando abaixo

   ```
   php --version
   ```

   Você deve obter uma resposta como algo parecida com esta.

- `PHP 7.4.3 (cli) (built: Oct  6 2020 15:47:56) ( NTS )
   Copyright (c) The PHP Group
   Zend Engine v3.4.0, Copyright (c) Zend Technologies
   with Zend OPcache v7.4.3, Copyright (c), by Zend Technologies`

   Se a resposta for positiva quer diser que seu php foi instalado corretamente com sucesso!

5. Instale PHP7.4-FPM e outras extensões

   ```
   apt-get install php7.4-fpm php7.4-cli php7.4-mysql php7.4-curl php7.4-json -y
   ```

 Ao Fim da instalaçao execute o comando abaixo por garantia para que seu php-fpm inicie juntamente com o sistema operacional.

 ```
 systemctl enable php7.4-fpm
 ```


   Crie uma pasta para o whmcs usando o comando abaixo.
   `
   mkdir /var/www/whmcs
   `
   
6. Configure o Nginx para processar arquivos PHP

   ```
   cd /etc/nginx/sites-available/
   ```
   ```
   nano whmcs.conf
   ```

Cole o codigo abaixo no arquivo que abrirá, lembre-se de alterar a linha onde esta `seudominio.com`.


 ```
 server {
        listen 80;
        server_name seudominio.com;
        root /var/www/whmcs;
        index info.php;

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php7.4-fpm.sock;
    }
}
 ```

 Feito isso precione CTRL + X no seu teclado e posteriormente, Y & ENTER para salvar o arquivo

7. Use o comando abaixo para ativar o arquivo criado.
   `
   sudo ln -s /etc/nginx/sites-available/whmcs.conf /etc/nginx/sites-enabled/whmcs.conf
   `

   Verifique se há erros no nginx usando
   `
   sudo nginx -t
   `

   Caso nao haja erros reinicie o servidor Web.
   `
   systemctl restart nginx
   `

9. Adicionando SSL
   Aqui atualize novamente os pacotes.
   `
   sudo apt update
   `

   Depois instale o CertBot para NGINX.
   `
    sudo apt install -y certbot
    sudo apt install -y python3-certbot-nginx
   `

   Agora é so voce instalar o certificado.
   `
   sudo certbot --nginx -d seudominio.com
   `

11. Antes de mais nada vamos instalar outras 2 extençoes exigidas pelo WHMCS

    `
    sudo apt install php7.4-xml
    `

   `
   sudo apt install php7.4-gd
   `

   Agora basta reiniciar o php-fpm.
   `
   sudo service php7.4-fpm restart
   `

Pronto! Finalizamos aqui a parte da configuraçao do servidor Web e suas dependencias.

