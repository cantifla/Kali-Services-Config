#!/bin/bash

# =================================================================
# SCRIPT: CONFIGURACIÓN AUTOMÁTICA DE SERVICIOS - KALI LINUX
# ESTUDIANTE: EDWIND DE JESUS (20240318)
# REPOSITORIO: Kali-Services-Config
# =================================================================

echo "--- Iniciando instalación y configuración total ---"

# 1. ACTUALIZACIÓN E INSTALACIÓN
sudo apt update
sudo apt install bsd-mailx libsasl2-modules apache2 cups cups-client printer-driver-cups-pdf -y

# 2. CONFIGURACIÓN DE CORREO (POSTFIX)
# Se utiliza postconf para editar el archivo sin crear duplicados
sudo postconf -e "relayhost = [smtp.gmail.com]:587"
sudo postconf -e "smtp_sasl_auth_enable = yes"
sudo postconf -e "smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd"
sudo postconf -e "smtp_sasl_security_options = noanonymous"
sudo postconf -e "smtp_tls_security_level = encrypt"
sudo postconf -e "smtp_use_tls = yes"

# Credenciales (REEMPLAZA ESTO ANTES DE CORRER EL SCRIPT)
echo "[smtp.gmail.com]:587 tu_correo@gmail.com:tu_clave_de_16_letras" | sudo tee /etc/postfix/sasl_passwd > /dev/null

sudo chmod 600 /etc/postfix/sasl_passwd
sudo postmap /etc/postfix/sasl_passwd
sudo systemctl restart postfix

# 3. CONFIGURACIÓN WEB (APACHE2)
sudo mkdir -p /var/www/holamundo
echo "<h1>Hola Mundo - Edwind de Jesus 20240318</h1>" | sudo tee /var/www/holamundo/index.html

# Cambio de DocumentRoot automático
sudo sed -i 's|DocumentRoot /var/www/html|DocumentRoot /var/www/holamundo|g' /etc/apache2/sites-available/000-default.conf
sudo systemctl restart apache2

# 4. CONFIGURACIÓN DE IMPRESIÓN (CUPS)
sudo systemctl start cups
sudo systemctl enable cups
sudo cupsctl --remote-admin --remote-any --share-printers
sudo usermod -aG lpadmin $USER

echo "-------------------------------------------------------"
echo "✅ CONFIGURACIÓN FINALIZADA CON ÉXITO"
echo "📂 Repositorio sugerido: Kali-Services-Config"
echo "-------------------------------------------------------"
