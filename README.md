# Простой и элегантый способ обойти все блядкие блокировки от РКН на любом дистрибутиве linux

Хочется сразу сказать ПОШЕЛ НАХУЙ РКН. 

А теперь к сути:

Манипуляции подходят для любого дистрибутива linux, в том числе и на дристрибутивах, не имеющих systemd 

Нам понадобится AmneziaWg, а точнее его аналог под линукс (https://github.com/amnezia-vpn/amneziawg-linux-kernel-module) 
так же ставим Awg-tools (https://github.com/amnezia-vpn/amneziawg-tools)

Само собой на страницах AWG и AWG-tools есть максимально подробные и понятные способы установки и сборки

После установки переходим на эту прекрасную страницу (https://github.com/ImMALWARE/bash-warp-generator) и по инструкции генерируем WARP.conf

Перебрасываем его в путь /etc/amnezia/amneziawg/ 

Прописываем "sudo awg-quick up WARP"

Готово! После этих действий не вы сосете РКН, а он вам 

Для отмены сервиса прописываем "sudo awg-quick down WARP"
