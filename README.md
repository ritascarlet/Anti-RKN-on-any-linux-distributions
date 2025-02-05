# Простой и элегантый способ обойти все блокировки от РКН на любом дистрибутиве linux

# Работает по принципу self-host, но я могу ошибаться, так как я не совсем профи с этих делах

# Данный гайд разделен на примечания, чтобы было удобнее найти способ имеено под свой дистрибутив

# Общая информация

Ранее вы могли видеть как люди на windows обходят блокировки с помощью amneziawg, так что наш способ тоже заключается в этом. 

Абсолютно для всех пользователей понадобится WARP.conf, который мы получаем по гайду из этот git репозитория (https://github.com/ImMALWARE/bash-warp-generator) 

!Важно помнить! 
Ваш WARP.conf всегда находится в /etc/amnezia/amneziawg/WARP.conf и нигде больше. Именно по этому пути amneziawg его ищет! 

Оприора названий:
# amneziawg - модуль ядра 

# amneziawg-tools - средства, необходимные для работы amneziawg

Amneziawg это форк WireGuard, использующий тунельное разделение трафика, так что РКН вряд-ли сможет это обойти 
(конечно пока не сделает полный рунет в нашей стране, но это невозможно)

# Примечание номер 1: Ubuntu, Debian (моя любимка) и остальные debian-based дистрибутивы
Не мучайте себя, просто зайдите на https://github.com/amnezia-vpn/amnezia-client 

В релизах вам будет доступен бинарный установщик с графическим интерфейсом, который нужно запустить с правами суперюзера, а после 
выполнить установку по принципу далее, далее, далее

# Примечание номер 2: Fedora, RedHat, CentOs и другие rpm-based дистрибутивы

Советую для начала присмотреться к это странице https://github.com/amnezia-vpn/amneziawg-linux-kernel-module 

```shell
sudo dnf copr enable amneziavpn/amneziawg
sudo dnf install amneziawg-dkms amneziawg-tools
```
Я уверен что способ с командами выше работает в Fedora, но не уверен что этот способ будет работать с RedHat, Cent0s и OpenSuse, так что 
для них советую искать amneziawg-tools и amneziawg в ваших репозиториях, а после следовать по разделу Общая информация

# Примечание номер 3: NixOs <3
Совсем недавно пересел на NixOs и понял что нашел свой дистрибутив, поэтому это примечание станет способом для обхода на NixOs

Переключаемся на unstable канал, а так же определяем модуль ядра

Я использую ядро zen, поэтому и модуль для ядра соответственно zen (Но там есть модули для разных ядер, такие как zen, xanmod и другие)

Устанавливаем модуль и нужные зависимости: 

 ```shell
 environment.systemPackages = with pkgs; [
   linuxKernel.packages.linux_zen.amneziawg
   amneziawg-go
   amneziawg-tools
  ];
 ```

Затем в конфигурации добавляем эту строку

 ```shell
boot.extraModulePackages = with config.boot.kernelPackages ; [ amneziawg ] ; 
 ```

(Более подробно можете рассмотреть в моей конфигурации NixOs, которая есть в профиле)

Готово! Теперь осталось лишь создать путь /etc/amnezia/amneziawg, а после копировать туда WARP (ну и само собой sudo awg-quick up WARP)

# Примечание номер 4: Любой другой дистрибутив (Arch linux, Void linux и тд)

Это примечание создано для всех дистрибутивов linux, так что оно будет основным 
(Примечание проверенно лично мною на таких дистрибутивах как arch, void, artix, devuan) 

Основа заключается в собрке модуля ядра по этой ссылке (https://github.com/amnezia-vpn/amneziawg-linux-kernel-module) 

Так же не забудь собрать amneziawg-tools по этой ссылке (https://github.com/amnezia-vpn/amneziawg-tools) 

Основные шаги: 

1) Клонируете amneziawg-linux-kernel-module и заходите в директорию src 

```shell
    git clone https://github.com/amnezia-vpn/amneziawg-linux-kernel-module.git
    cd amneziawg-linux-kernel-module/src
```

2) Теперь твоя основная задача найти исходный код твоего ядра.
   Чтобы узнать версию своего ядра, достаточно лишь использовать 
```shell
   uname -a
```
Кстати как мы знаем, бывает много сборок основного ядра linux, а именно zen, xanmod, ltsc и другие.

Поэтому я сделаю обьяснение на своем примере, а именно arch linux с ядром linux-zen 6.13.1.zen1-1 (кстати версию и сборку своего ядра я получил командой uname -a)

Понимаю, что поскольку данное ядро находится по стандарту в репозитроии arch, иду туда и после нахожу данную стрницу https://archlinux.org/packages/extra/x86_64/linux-zen/

Вижу ссылку на git репозитроий (https://github.com/zen-kernel/zen-kernel), захожу в releases и скачиваю исходный код своего ядра в tar.gz формате

После распаковываю исходный код своего ядра командой tar -xf. 

Отлично, осталось только сделать симлинк к архиву amneziawg-linux-kernel-module/src
Для этого находясь по пути amneziawg-linux-kernel-module/src, мы вбиваем комнду 
```shell
    ln -s /path/to/kernel/source kernel
```
где '/path/to/kernel/source' это путь до папки с вашим распакованным исходным кодом ядра (для примера возьму мою ситуацию, а именно ~/Downloads/6.13.1.zen1-1)

По итогу моя окончательная команда будет выглядеть так 

```shell
    ln -s ~/Downloads/6.13.1.zen1-1 kernel
```
3) Готово! Теперь перейдем к сборке

Я настоятельно рекомендую использовать dkms сборку, так как после обновления ядра вам не придется пересобирать модуль вручную

    ```shell
    sudo make dkms-install
    sudo dkms add -m amneziawg -v 1.0.0
    sudo dkms build -m amneziawg -v 1.0.0
    sudo dkms install -m amneziawg -v 1.0.0
    ```

4) Готово! Теперь можете создавать warp.conf и копировать его в директорию /etc/amnezia/amneziawg

Для использования 
    ```shell
    sudo awg-quick up WARP (для включения сервиса, где WARP это WARP.conf)
    sudo awg-quick down WARP (для выключения сервиса) 
    ```


# Примечание номер 5:



