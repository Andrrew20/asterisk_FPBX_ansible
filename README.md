# Asterisk + FreePBX установка через Ansible

## Быстрая установка VoIP системы

### Что делает этот проект?

Автоматически устанавливает телефонную станцию (Asterisk) с веб-панелью управления (FreePBX) и настраивает 3 телефона.

### Что нужно для начала?

1. Сервер с Debian 12 или Ubuntu 22.04+
2. Ansible на вашем компьютере
3. SSH доступ к серверу

### Быстрый старт

**1. Установите Ansible:**

```bash
# На Ubuntu/Debian
sudo apt update
sudo apt install ansible -y

# На CentOS/RHEL
sudo yum install ansible -y
```

**2. Скопируйте файлы проекта:**

```bash
git clone https://github.com/your-repo/voip-ansible.git
cd voip-ansible
```

**3. Настройте сервер в файле `inventory.ini`:**

```ini
[voip_servers]
myserver ansible_host=ВАШ_IP_СЕРВЕРА ansible_user=root
```

**4. Настройте переменные в `group_vars/all.yaml`:**

```yaml
server_ip: "ВАШ_IP_СЕРВЕРА"
mysql_root_password: "ВашСложныйПароль"
freepbx_admin_password: "Admin@123"
```

**5. Запустите установку:**

```bash
ansible-playbook -i inventory.ini playbook.yaml
```

### Что будет установлено?

- Asterisk 20 (телефонная станция)
- FreePBX 17 (управление через браузер)
- MySQL база данных
- Веб-сервер NGINX
- 3 SIP телефона с номерами 101, 102, 103

### После установки

Откройте в браузере:

- **Панель управления:** http://ВАШ*IP*СЕРВЕРА/admin
- **Логин:** admin
- **Пароль:** Admin@123

### Настройка телефонов

Телефоны будут автоматически настроены:

- **101** - Phone 1 (пароль: phone101secret)
- **102** - Phone 2 (пароль: phone102secret)
- **103** - Phone 3 (пароль: phone103secret)

На телефонах укажите:

- SIP сервер: ваш IP сервера
- Логин: номер телефона (101, 102 или 103)
- Пароль: из таблицы выше

### Полезные команды

```bash
# Проверить статус сервисов
systemctl status asterisk

# Перезапустить Asterisk
asterisk -rx "core restart now"

# Посмотреть зарегистрированные телефоны
asterisk -rx "sip show peers"

# Просмотр логов
tail -f /var/log/asterisk/full
```

### Порты для открытия в брандмауэре

```bash
# Открыть порты
ufw allow 5060/tcp
ufw allow 5060/udp
ufw allow 5061/tcp
ufw allow 5061/udp
ufw allow 80/tcp
ufw allow 443/tcp
ufw allow 10000:20000/udp
```

### Частые проблемы

**1. Телефоны не регистрируются**

- Проверьте пароль
- Убедитесь что порт 5060 открыт
- Проверьте команду: `asterisk -rx "sip show peers"`

**2. FreePBX не открывается**

- Перезапустите веб-сервер: `systemctl restart nginx php8.2-fpm`
- Проверьте логи: `tail -f /var/log/nginx/error.log`

**3. Нет звука при звонке**

- Откройте порты 10000-20000 UDP
- Проверьте настройки кодеков в FreePBX

### Резервное копирование

```bash
# Создать резервную копию
/usr/local/bin/backup-voip.sh

# Восстановить из резервной копии
tar -xzf backup-voip.tar.gz -C /
fwconsole reload
```

### Контакты для поддержки

Если что-то не работает:

1. Проверьте логи: `/var/log/asterisk/full`
2. Перезапустите сервисы: `systemctl restart asterisk`
3. Если не помогло - создайте issue на GitHub

### Обновление системы

```bash
# Обновить пакеты
apt update && apt upgrade -y

# Обновить FreePBX модули
fwconsole ma upgradeall
```

---

**Готово!** Ваша телефонная станция запущена. Теперь можно:

1. Звонить между телефонами 101-103
2. Настроить голосовую почту (\*97)
3. Добавить новые телефоны через FreePBX
4. Настроить входящие/исходящие маршруты

Для дополнительных настроек используйте веб-интерфейс FreePBX.
