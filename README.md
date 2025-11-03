
## Como resolver o erro "A configuração do diretório raiz do servidor da Web não é segura" no GLPI?

Siga os passos abaixo para ajustar corretamente a raiz do servidor web para o diretório seguro do GLPI:

1. **Edite o arquivo de configuração do Apache (exemplo: `glpi.conf`):**

   ```bash
   sudo nano /etc/apache2/sites-available/glpi.conf
   ```

   Altere o `DocumentRoot` e o bloco `<Directory>` para apontarem para `/var/www/glpi/public`:

   ```apache
   DocumentRoot /var/www/glpi/public
   <Directory /var/www/glpi/public>
       DirectoryIndex index.html index.htm index.php index.cgi
       Options -Indexes +FollowSymLinks
       AllowOverride All
       Require all granted
   </Directory>
   ```
   
2.1. **Crie o arquivo `.htaccess` no diretório `glpi` do GLPI:**

   ```bash
   sudo nano /var/www/glpi/.htaccess
   ```

   Adicione o seguinte conteúdo:

   ```apache
   <IfModule mod_rewrite.c>
       RewriteEngine On
       RewriteBase /
       RewriteEngine On
       RewriteCond %{REQUEST_URI} !^/public
       RewriteRule ^(.*)$ public/index.php [QSA,L]
   </IfModule>
   ```

2.2. **Crie o arquivo `.htaccess` no diretório `public` do GLPI:**

   ```bash
   sudo nano /var/www/glpi/public/.htaccess
   ```

   Adicione o seguinte conteúdo:

   ```apache
   <IfModule mod_rewrite.c>
       RewriteEngine On
       RewriteCond %{REQUEST_FILENAME} !-f
       RewriteCond %{REQUEST_FILENAME} !-d
       RewriteRule ^(.*)$ index.php [QSA,L]
   </IfModule>
   ```

3. **Ajuste as permissões do arquivo `.htaccess`:**

   ```bash
   sudo chown www-data:www-data /var/www/glpi/public/.htaccess
   sudo chmod 644 /var/www/glpi/public/.htaccess
   ```

4. **Habilite o módulo `mod_rewrite` e reinicie o Apache:**

   ```bash
   sudo a2enmod rewrite
   sudo systemctl restart apache2
   ```

Após isso, o GLPI deixará de exibir o aviso de configuração insegura da raiz do servidor web.
