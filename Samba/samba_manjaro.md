## SAMBA Install on Manjaro Linux

Настройка подключения к Samba-серверу на Manjaro Linux может быть выполнена несколькими способами. Вот базовый гайд для настройки клиента Samba на Manjaro Linux.

### Установка пакетов

Убедитесь, что у вас установлены необходимые пакеты. Если они не установлены, установите их с помощью `pacman`:

```bash
sudo pacman -S samba gvfs-smb smbclient
```

### Использование файлового менеджера (например, Thunar или Dolphin)

1. **Откройте файловый менеджер** и перейдите в раздел "Сеть" или введите в адресной строке `smb://`.
  
2. **Введите адрес сервера**: Вы можете ввести адрес в формате `smb://Server_IP_Address/Share_Name` или `smb://Server_Name/Share_Name`.

### Использование командной строки

1. **Создайте точку монтирования**: Создайте папку, в которой будут отображаться сетевые файлы.

    ```bash
    mkdir ~/samba-share
    ```

2. **Монтирование**: Вы можете воспользоваться командой `mount` для монтирования сетевого диска:

    ```bash
    sudo mount -t cifs //Server_IP_Address/Share_Name ~/samba-share -o username=Your_Username,password=Your_Password
    ```

### Использование `smbclient`

`smbclient` - это командно-строчный инструмент, который позволяет вам исследовать сетевые шары и работать с ними:

```bash
smbclient //Server_IP_Address/Share_Name -U Your_Username
```

После ввода пароля вы перейдете в интерактивный режим, где сможете использовать команды аналогичные FTP для работы с файлами.

### Автоматическое монтирование при загрузке

Если вы хотите, чтобы сетевой диск автоматически монтировался при загрузке системы, добавьте соответствующую строку в файл `/etc/fstab`.

```bash
//Server_IP_Address/Share_Name /path/to/mount/point cifs username=Your_Username,password=Your_Password 0 0
```

**Примечание**: В целях безопасности лучше хранить учетные данные в отдельном защищенном файле, а не прямо в `/etc/fstab`.

Теперь вы должны уметь подключаться к Samba-шарам с вашего компьютера на Manjaro Linux. Настройки могут варьироваться в зависимости от конфигурации сервера и требований к безопасности, но это должно дать вам базовое представление о том, как все настроить.


---

## SAMBA PASSWD

На сервере Samba, пароли и пользователи для доступа к сетевым папкам обычно устанавливаются с помощью утилиты `smbpasswd`. Прежде всего, убедитесь, что у вас есть правильно настроенный файл конфигурации Samba, обычно это `/etc/samba/smb.conf`.

### Добавление пользователя в систему

Если у вас ещё нет системного пользователя, который вы хотите использовать для Samba, создайте его с помощью команды `useradd`:

```bash
sudo useradd -M your_username
```

Здесь флаг `-M` означает, что не нужно создавать домашнюю папку для пользователя.

### Установка пароля в Samba

Теперь, чтобы установить или изменить пароль для пользователя Samba, выполните следующую команду:

```bash
sudo smbpasswd -a your_username
```

Затем вам будет предложено ввести новый пароль. Этот пароль будет использоваться для доступа к Samba-шарам.

### Активация пользователя

В некоторых случаях, после установки пароля, его ещё нужно активировать. Это можно сделать командой:

```bash
sudo smbpasswd -e your_username
```

### Настройка `smb.conf`

Убедитесь, что у вас в файле `/etc/samba/smb.conf` есть правильные настройки для вашего пользователя или группы пользователей. Например:

```ini
[Your_Share]
    path = /path/to/share
    valid users = your_username
    read only = no
```

### Перезапуск Samba

После всех изменений, перезапустите Samba для применения настроек:

```bash
sudo systemctl restart smbd
sudo systemctl restart nmbd
```

Теперь у вас должен быть настроенный пользователь с паролем для доступа к вашему Samba-серверу.

---

## SAMBA Errors

Если при попытке перезапустить службу Samba (smbd) вы получаете сообщение об ошибке `Failed to restart smbd.service: Unit smbd.service not found`, это может означать несколько вещей:

1. **Samba не установлен**: В этом случае установите Samba, используя пакетный менеджер вашего дистрибутива. На Manjaro или других системах на базе Arch Linux это можно сделать командой:
    ```bash
    sudo pacman -S samba
    ```

2. **Неправильное имя службы**: На некоторых дистрибутивах имя службы может отличаться. Попробуйте использовать `smb` вместо `smbd`:
    ```bash
    sudo systemctl restart smb
    ```

3. **Служба отключена или не активирована**: Если Samba установлен, но служба не активирована, попробуйте её включить и запустить:
    ```bash
    sudo systemctl enable smb
    sudo systemctl start smb
    ```

4. **Проверьте статус службы**: Используйте следующую команду, чтобы узнать статус службы:
    ```bash
    sudo systemctl status smb
    ```

5. **Логи и журналы**: Для дополнительной диагностики проблем вы можете проверить логи системы или журналы Samba. Журналы Samba обычно находятся в `/var/log/samba/`.

После устранения проблемы, не забудьте снова проверить ваш файл `smb.conf` и удостовериться, что все настроено правильно, прежде чем перезапускать службу.