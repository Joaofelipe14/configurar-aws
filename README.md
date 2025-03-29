```markdown
# **Guia Completo: Configuração de Domínio, EC2 e Ambiente Laravel**

Este documento explica a configuração completa para deploy de uma aplicação Laravel, incluindo:
- Configuração de domínio AWS Route 53
- Setup de instância EC2
- Instalação otimizada de Apache/PHP
- Configuração de segurança

---

## **🌐 1. Configuração do Domínio (Route 53)**

### **1.1 Criar Hosted Zone**
1. Acesse [AWS Route 53](https://console.aws.amazon.com/route53/)
2. **Criar Hosted Zone**:
   ```plaintext
   Nome do domínio: carmanager.com.br
   Tipo: Pública
   ```

### **1.2 Configurar Nameservers**
- Substitua os nameservers no seu registrador pelos da AWS (ex: `ns-123.awsdns-45.com`)

### **1.3 Registros DNS Essenciais**
| Tipo  | Nome          | Valor                  | TTL   |
|-------|---------------|------------------------|-------|
| A     | api           | [IP_PÚBLICO_EC2]       | 300   |
| CNAME | admin         | [DNS_AMPLIFY]          | 300   |

---

## **🖥️ 2. Configuração da EC2**

### **2.1 Especificações Recomendadas**
```yaml
AMI: Ubuntu 22.04 LTS
Tipo: t3.micro (free tier)
Security Group:
  - Portas liberadas: 22 (SSH), 80 (HTTP), 443 (HTTPS)
```

### **2.2 Conexão SSH**
```bash
ssh -i "sua-chave.pem" ubuntu@IP_PUBLICO_EC2
```

---

## **⚙️ 3. Instalação do Ambiente**

### **3.1 Pacotes Essenciais (Apache/PHP Otimizado)**
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y \
  apache2 \
  php php-cli php-fpm php-json php-mysql php-zip php-gd \
  php-mbstring php-curl php-xml php-bcmath \
  libapache2-mod-php \
  mysql-server \
  certbot python3-certbot-apache
```

### **3.2 Módulos Apache Necessários**
```bash
sudo a2enmod rewrite ssl headers
sudo systemctl restart apache2
```

---

## **🔒 4. Configuração de Segurança**

### **4.1 Permissões do Projeto**
```bash
sudo chown -R www-data:www-data /var/www/html/carmanager-back
sudo chmod -R 755 storage bootstrap/cache
sudo usermod -a -G www-data $USER ##cuidado com essa

```

### **4.2 Firewall (UFW)**
```bash
sudo ufw allow 'Apache Full'
sudo ufw enable
```

---

## **🚀 5. Deploy do Laravel**

### **5.1 Configuração do Virtual Host**
```bash
sudo nano /etc/apache2/sites-available/carmanager.conf
```
```apache
<VirtualHost *:80>
    ServerName api.carmanager.com.br
    DocumentRoot /var/www/html/carmanager-back/public
    <Directory /var/www/html/carmanager-back/public>
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>
```

### **5.2 SSL com Certbot**
```bash
sudo certbot --apache -d api.carmanager.com.br
```

### **5.3 Variáveis de Ambiente**
```bash
sudo cp .env.example .env
sudo nano .env
```
```ini
APP_ENV=production
APP_URL=https://api.carmanager.com.br
```

---

## **🛠️ 6. Comandos Pós-Instalação**

### **6.1 Otimizações do Laravel**
```bash
php artisan key:generate
php artisan storage:link
php artisan optimize
```

### **6.2 Monitoramento**
```bash
# Verificar logs em tempo real:
tail -f storage/logs/laravel.log

# Verificar status do Apache:
sudo systemctl status apache2
```

---

## **📌 Notas Importantes**

1. **Sempre:**
   - Atualize o arquivo `.gitignore` para excluir `.env`
   - Configure backups automáticos da EC2
   - Monitore o uso de recursos com `htop`

2. **Para escalar:**
   ```bash
   # Instalar OPcache para melhor performance
   sudo apt install php-opcache
   sudo systemctl restart apache2
   ```

3. **Dica Pro:**
   Use o AWS CloudWatch para monitorar acesso e erros:
   ```bash
   # Instalar agente CloudWatch
   sudo apt install -y amazon-cloudwatch-agent
   ```

---

**✅ Configuração Completa!** Seu ambiente está pronto para produção com:
- Domínio configurado
- SSL ativo
- Permissões seguras
- Stack otimizada para Laravel

[⬆️ Voltar ao topo](#-guia-completo-configuração-de-domínio-ec2-e-ambiente-laravel)
```

### Melhorias realizadas:
1. **Organização hierárquica** com seções claras
2. **Comandos otimizados** para instalação mínima do PHP
3. **Destaque visual** para configurações críticas
4. **Fluxo lógico** do geral para os detalhes
5. **Adição de notas profissionais** para escalabilidade
6. **Formatação Markdown** completa para GitHub

Quer ajustar algum ponto específico ou adicionar mais detalhes em alguma seção?
