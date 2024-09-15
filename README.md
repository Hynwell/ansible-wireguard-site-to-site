# WireGuard Site-to-Site VPN

Эта роль Ansible предназначена для настройки двух VPN серверов с использованием WireGuard в формате initiator-respondent или respondent-respondent.

## Оглавление

- [Требования](#требования)
- [Переменные роли](#переменные-роли)
- [Использование](#использование)
- [Конфигурация](#конфигурация)
- [Пример плейбука](#пример-плейбука)
- [Управление интерфейсами](#управление-интерфейсами)
- [Информация об авторе](#информация-об-авторе)

## Требования

- Роль тестировалась на Debian 11
- Должна работать на Ubuntu без проблем

## Переменные роли

| Переменная | Описание |
|------------|----------|
| `wireguard_name_vpn_interface` | Имя WireGuard интерфейса |
| `wireguard_listen_port` | Порт для прослушивания |
| `wireguard_address_interface` | IP-адрес интерфейса |
| `wireguard_allowed_ips` | Разрешенные IP-адреса |
| `wireguard_respondent_endpoint` | Endpoint респондента |
| `wireguard_persistent_keepalive` | Интервал keepalive |
| `wireguard_destroy` | Флаг для удаления VPN |

## Использование

### Запуск проекта для установки VPN

```bash
ansible-playbook -i inventary play.yaml
```

### Удаление VPN

```bash
ansible-playbook -i inventary play.yaml -e wireguard_destroy=true
```

## Конфигурация

- **Формат initiator-respondent**: Используется, когда есть только один внешний IP-адрес.
  - initiator: может находиться за NAT
  - respondent: требует внешний IP

- При использовании формата initiator-respondent:
  1. Закомментируйте `wireguard_respondent_endpoint` в `group_vars/initiator_vpn_server.yaml`
  2. Раскомментируйте `wireguard_persistent_keepalive`

> **Примечание**: `wireguard_persistent_keepalive` отправляет служебный пакет каждые 25 секунд для поддержания актуальности данных в таблице трансляции. Это важно, если WireGuard находится за NAT, чтобы избежать проблем со стабильностью связи.

## Пример плейбука

```yaml
- name: "WireGuard | Настройка VPN серверов"
  hosts: all
  become: yes
  roles:
    - role: ../wireguard-site-to-site
      tags:
        - install
        - configure
        - destroy
```

## Управление интерфейсами

Для ручного управления интерфейсами используйте следующие команды:

```bash
wg
wg-quick down name_interface 
wg-quick up name_interface
```
