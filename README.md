# Простой и элегантый способ обойти все блядкие блокировки от РКН на любом дистрибутиве linux

Примечание: РАБОТАЕТ АБСОЛЮТНО ВСЕ НА ЛЮБОМ ПРОВАЙДЕРЕ! ВОЙСЫ В ДС, ЮТУБ, ТВИТТЕР И ТД

Хочется сразу сказать ПОШЕЛ НАХУЙ РКН. 

# А теперь к сути:

Манипуляции подходят для любого дистрибутива linux, в том числе и на дристрибутивах, не имеющих systemd 

Нам понадобится AmneziaWg, а точнее его аналог под линукс (https://github.com/amnezia-vpn/amneziawg-linux-kernel-module) 
так же ставим Awg-tools (https://github.com/amnezia-vpn/amneziawg-tools)

Само собой на страницах AWG и AWG-tools есть максимально подробные и понятные способы установки и сборки

После установки переходим на эту прекрасную страницу (https://github.com/ImMALWARE/bash-warp-generator) и по инструкции генерируем WARP.conf

Перебрасываем его в путь /etc/amnezia/amneziawg/ 

Прописываем "sudo awg-quick up WARP"

Готово! После этих действий не вы сосете РКН, а он вам 

Для отмены сервиса прописываем "sudo awg-quick down WARP"

# Примечание номер 2:

Если вам максимально впадлу делать все эти действия, загляните на страницу с клиентом AmneziaVPN (https://github.com/amnezia-vpn/amnezia-client)

Есть удобный бинарник для линукс, который кстати работает на wayland! Но все же максимальная стабильность данной проги выражается на debian-based дистрибутивах, так как изначально под него и заточено


# Примечание номер 3: 
Совсем недавно пересел на NixOs и понял что нашел свой дистрибутив, поэтому щас будет гайд на обьеб РКН на NixOs

Переключаемся на unstable канал, после ставим  amneziawg-tools и amneziawg-go (на удачу)
и самый главный аспект, это модуль ядра 

Я использую ядро zen, поэтому и модуль для ядра соответственно zen (но есть модули для обычного ядра, xanmod и хуё моё куча разных)

Сам модуль сначала скачиваем как дефол пакет: 

 environment.systemPackages = [
    pkgs.linuxKernel.packages.linux_zen.amneziawg
  ];

Затем в конфигурации добавляем эту богостроку 

boot.extraModulePackages = with config.boot.kernelPackages ; [ amneziawg ] ; 

(более подробно можете рассмотреть в моей конфигурации NixOs, которая есть в профиле)
(НАСТОЯТЕЛЬНО РЕКОМЕНДУЮ ОБОСРАТЬ И ВЫСМЕЯТЬ МОЙ КОНФИГ ДЛЯ NIXOS, А ТАК ЖЕ УКАЗАТЬ НА ОШИБКИ, ЕСЛИ ОНИ ЕСТЬ!!!)

Готово! Теперь осталось лишь создать путь /etc/amnezia/amneziawg, а после вкинуть туда WARP (ну и само собой sudo awg-quick up WARP)

# Примечание номер 4:

Щас расскажу как на арче эту хуйню выполнить (если ты еблан отсталый блять как я) 

Короче все пидорасы в aur репозитории хоть и выкладывают что-то похожее на модуль ядра, но оно нихуя не работает и ты сидишь как ебанат без модуля ядра 

Я еще эти пидорасы выкладывают свой блядски pkgbuild, заточенный под стандартное ядро linux, который блять ХАХАХА ВСЕ РАВНО НЕ РАБОТАЕТ

Вообщем берешь, вбиваешь "uname -a", где показывается твоя версия ядра (сделаю это примечание на своем примере так что возьмем условно мое ядро 6.12.10-zen1-1-zen)

Ты узнал свое ядро, а теперь хуяришь в интернет, где ты находишь исходный код своего блядского ядра и скачиваешь его. 

К примеру ты скачал tar.gz архив с исходным кодом СВОЕГО ЯДРА в папку Downloads, а после распаковал его, где в конце концов получаешь путь /home/user/Downloads/6.12.10-zen1-1-zen, где 6.12.10-zen1-1-zen является исходным кодом ядра

Ахуеть, заебись ты красава! Теперь возвращайся в amneziawg-linux-kernel-module/src, где ты пиздато вбиваешь команду "ln -s /home/user/Downloads/6.12.10-zen1-1-zen kernel"

Ну все, теперь ты можешь ебашить make и  sudo make install, а после пользоваться amneziawg как нормальный человек 
