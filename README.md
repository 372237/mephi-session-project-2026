# mephi-session-project-2026
# README — выполнение задания по РЕД ОС

**Номер зачётной книжки:** 372237

## 1. Установка дистрибутива
| Пункт | Как выполнено |
|-------|---------------|
| 1.1 Сеть по DHCP | Настроено в установщике, проверено `ip a` |
| 1.2 Имя хоста | hostnamectl set-hostname mephi-2026.domain.local|
| 1.3 Проверка связности | `ping -c 4 8.8.8.8 > ping.out` |

## 2. Управление ПО
| Пункт | Как выполнено |
|-------|---------------|
| 2.1 Обновление | `dnf update` |
| 2.2 Установка пакетов | `dnf install nginx libcap-ng-utils` |
| 2.3 Локальный RPM | `dnf download tcpdump --downloaddir /tmp`, затем `rpm -ivh`, история — `dnf history > dnf.out` |

## 3. Файловые системы
| Пункт | Как выполнено |
|-------|---------------|
| 3.1 Создание ФС | `parted --align opt /dev/sdb mktable gpt mkpart data 0% 100%`, затем `mkfs.ext4 -L MEPHI_WEB /dev/sdb1` |
| 3.2 Монтирование | `mkdir -p /mephi-web`, запись `LABEL=MEPHI_WEB /mephi-web ext4 defaults 0 2` в `/etc/fstab`, затем `mount /mephi-web` |

## 4. Сервисы
| Пункт | Как выполнено |
|-------|---------------|
| 4.1 nginx | `systemctl enable nginx --now` |
| 4.2 Журналирование | `journalctl -b -u nginx > journalctl.out` |

## 5. Управление доступом
| Пункт | Как выполнено |
|-------|---------------|
| 5.1 DAC | Группа `curators` (GID 4444), пользователи user1–3 (UID 5501–5503), группа `developers`. Директория `/data/mephi-2026` с правами `2770` и ACL: разработчики — `rw-`, кураторы — `r--`, остальные — `---` |
| 5.2 Привилегии | `chmod u-s /usr/sbin/tcpdump`, `setcap cap_net_raw,cap_net_admin+eip /usr/sbin/tcpdump`, проверка от user1 |
| 5.3 SELinux | Режим `enforcing`, контекст `httpd_sys_content_t` для `/mephi-web` через `semanage fcontext` и `restorecon` |

## 6. Аутентификация
| Пункт | Как выполнено |
|-------|---------------|
| 6.1 Вход кураторов | `usermod -s /sbin/nologin curator1 curator2` |
| 6.2 Пароли | `PASS_MAX_DAYS 90` в `/etc/login.defs`, `chage -M 90` для пользователей, `minlen = 12` в `/etc/security/pwquality.conf` |

## 7. Тестирование
| Пункт | Как выполнено |
|-------|---------------|
| 7.1 Web-страница | `echo "Hello from Student: 372237" > /mephi-web/index.html`, `chown nginx:nginx`, `chmod 644`, `restorecon` |
| 7.2 Проверка | `curl http://localhost/` — вывод `Hello from Student: 372237` |

## Артефакты

| Файл | Что проверяется |
|------|-----------------|
| mephi-screenshot.png | Визуальное подтверждение |
| history.out | Выполненные команды |
| ping.out | Работоспособность сети |
| dnf.out | Управление пакетами |
| stat.out | Установка прав доступа и контекста SELinux |
| journalctl.out | Запуск веб-сервера |
| getcap.out | Настройка привилегий |
| getenforce.out | Режим SELinux |
| curl.out | Результат тестирования |
| fstab | Настройка монтирования файловых систем |
| passwd | Управление пользователями |
| shadow | Управление паролями пользователей |
| group | Управление группами пользователей |
| pwquality.conf | Настройка пароля |
