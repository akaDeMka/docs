# systemctl commands

```bash
systemctl list-units        # список всех активных модулей
systemctl list-units --all  # все модули
systemctl list-dependencies sshd.service    # лист зависимостей
systemctl show sshd.service     # список свойств
systemctl mask nginx.service    # деактивировать службу
systemctl edit nginx.service    # редактировать сервис   /etc/systemd/system
systemctl edit --full nginx.service
systemctl daemon-reload         # перезапуск systemd

systemctl get-default               # цель по умолчанию
systemctl set-default graphical.target  # установить другую цель
systemctl list-unit-files --type=target # вывести список целей
systemctl list-units --type=target  # активные цели

```
