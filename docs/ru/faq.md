# ЧАсто задаваемые ВОпросы


#### Мне необходимо открыть порт?

Это __не обязательно__, Вы можете просматривать и использовать Zeronet сайты не открывая порт.
Если Вы хотите создать новый сайт, то настоятельно рекомендуется открыть порт.

При старте Zeronet пытается открыть порт на Вашем роутере используя 
[UPnP](https://ru.wikipedia.org/wiki/UPnP), если это не получается, Вам надо сделать это самому:

- Получите доступ к веб-интерфейсу роутера по адресу [http://192.168.1.1](http://192.168.1.1)
или [http://192.168.0.1](http://192.168.0.1)
- Найдите и включите "Enable UPnP support" или похожую опцию и перезапустите ZeroNet.

Если всё равно не работает, попробуйте найти раздел 'port forwarding' в настройках роутера. [На YouTube есть описание.](https://www.youtube.com/watch?v=aQXJ7sLSz14) Надо сделать форвард порта 15441.


---


#### Сеть ZeroNet анонимна?

Zeronet не более анонимен, чем Bittorent, но конфиденциальность (невозможность определить, кто владелец комментария/сайта) увеличивается с увеличением числа пользователей сети и сайта.

ZeroNet создан для работы с анонимными сетями: Вы можете легко скрыть свой IP, используя Tor.


---


#### Как использовать ZeroNet с Tor browser?

В режиме Tor рекомендуется использовать ZeroNet из Tor Browser:

- Запустите Tor Browser
- Откройте `about:config` и подтвердите риск действий
- Найдите и дважды кликните `no_proxies_on`
- Введите `127.0.0.1` и нажмите OK
- Откройте [http://127.0.0.1:43110](http://127.0.0.1:43110) в браузере

Если Вы видите пустую страницу:

 - Нажмите кнопку NoScript's (первая на панели инструментов)
 - Выберите "Temporary allow all this page"
 - Перезагрузите страницу

---


#### Как использовать ZeroNet вместе с Tor?

Если Вы хотите скрыть ваш IP адрес, установите последнюю версию ZeroNet и нажмите Tor > Enable Tor for every connection на странице ZeroHello.

В Windows Tor включён в комплект ZeroNet. ZeroNet будет пытаться скачать и распаковать Tor при первом запуске. 
В случае ошибки по какой-либо причине, Вы можете установить его вручную по инструкции в `core\tools\tor\manual_install.txt`.

В других ОС, следуйте инструкциям в разделе "Как запустить ZeroNet с Tor под Linux/MacOS".

> __Tip:__ You can verify your IP address using ZeroNet's [Stats](http://127.0.0.1:43110/Stats) page.

> __Tip:__ If you get connection errors, make sure you have the latest version of Tor installed. (0.2.7.5+ required)


---


#### Как запустить ZeroNet с Tor под Linux/MacOS?

 - Установите Tor в соответствии с официальной инструкцией: [Linux](https://www.torproject.org/docs/tor-doc-unix.html.en) [Mac](https://www.torproject.org/docs/tor-doc-osx.html.en).
 - `sudo nano /etc/tor/torrc`
 - Удалите символ `#` в началах строк `ControlPort 9051` и `CookieAuthentication 1` (примерно ~57 строки)
 - Перезапустите tor
 - Добавьте себе разрешение читать auth cookie. В Debian Linux, команда будет `sudo usermod -a -G debian-tor [yourlinuxuser]`<br>(если у Вас не Debian, уточните группу у файла командой `ls -al /var/run/tor/control.authcookie`)
 - Перелогиньтесь, чтобы изменения разрешений подействовали

> __Tip:__ Use the `ls -ld /var/run/tor` command to make sure it has the correct `drwxr-sr-x` permission bits. (fix it with `chmod g+sx /var/run/tor/` if necessary)

> __Tip:__ You can verify if your Tor setup is running correctly using `echo 'PROTOCOLINFO' | nc 127.0.0.1 9051`

> __Tip:__ It's also possible to use Tor without modifying torrc (or to use older versions of Tor clients), by running `zeronet.py --tor disable --proxy 127.0.0.1:9050 --disable_udp`, but then you will lose ability to talk with other .onion addresses.


---

#### Возможно ли использовать конфигурационный файл?

Любой ключ конфигурации из командной строки может быть использован как опция в файле. 
Размещайте нужные опции построчно в файле `zeronet.conf` в верхнем уровне каталога zeronet (там, где zeronet.py). Например:

```
[global]
data_dir = my-data-dir
log_dir = my-log-dir
ui_restrict =
 1.2.3.4
 2.3.4.5
```

Список возможных опций можно получить командой `zeronet.py --help`

---


#### Как работать с Tor если мой провайдер или правительство блокирует его?

ZeroNet не включает [Tor pluggable transports](https://www.torproject.org/docs/pluggable-transports.html.en) ещё. 
Самый простой путь работать с Tor в цензурируемой сети это - запустить Tor browser, скофигурировать его для работы с pluggable transports, и использовать прокси и порт контроля Tor browser'а, запуская ZeroNet с `--tor_controller 127.0.0.1:9151 --tor_proxy 127.0.0.1:9150` или добавив эти параметры в `zeronet.conf`.

```
[global]
tor_controller = 127.0.0.1:9151
tor_proxy = 127.0.0.1:9150
```


---


#### Могу я использовать одно и то же имя на нескольких машинах?

Да, просто скопируйте файл `data/users.json` на новую машину.


---


#### Как создать "прикольный" (не .bit) адрес сайта?

Используйте [vanitygen](https://bitcointalk.org/index.php?topic=25804.0) чтобы его сгенерировать. Получив пару ключей, создайте каталог `data/1YourPublicKey...tCkBzAXTKvJk4uj8`. Размещайте там свои файлы.

Откройте страницу [http://127.0.0.1:43110/1YourPublicKey...tCkBzAXTKvJk4uj8/](http://127.0.0.1:43110/1YourPublicKey...tCkBzAXTKvJk4uj8/). Потяните кнопку `0` влево и используйте инструменты панели чтобы подписать ваш сайт.


---


#### Как зарегистрировать .bit домен?

Вы можете зарегистрировать .bit домен, используя [Namecoin](https://namecoin.info/).
Управляйте Вашими доменами из клиентского GUI или из [командной строки](https://github.com/namecoin/wiki/blob/master/How-to-register-and-configure-.bit-domains.md).

После регистрации Вам необходимо отредактировать запись вашего домена, добавив туда секцию zeronet, например:

```
{
...
    "zeronet": "1EU1tbG9oC1A8jz2ouVwGZyQ5asrNsE4Vr",
    "map": {
        "blog": { "zeronet": "1BLogC9LN4oPDcruNz3qo1ysa133E9AGg8" },
        "talk": { "zeronet": "1TaLk3zM7ZRskJvrh3ZNCDVGXvkJusPKQ" }
    },
...
}
```

> __Tip:__ Other possibilities to register .bit domains: [domaincoin.net](https://domaincoin.net/), [peername.com](https://peername.com/), [dotbit.me](https://dotbit.me/)

> __Tip:__ You can verify your domain on [namecha.in](http://namecha.in/), for example: [zeroid.bit](http://namecha.in/name/d/zeroid)

> __Tip:__ You should use only [lower-cased letters, numbers and - in your domains](http://wiki.namecoin.info/?title=Domain_Name_Specification_2.0#Valid_Domains).

> __Tip:__ To make ZeroHello display your domain name instead of your site's Bitcoin address, add a domain key to your content.json. ([Example](https://github.com/HelloZeroNet/ZeroBlog/blob/master/content.json#L6))


---


#### Могу я использовать сгенерированные адрес сайта/приватный ключ для получения платежей Bitcoin?

Да, это стандартный Bitcoin адрес. Приватный ключ имеет формат WIF и может быть импортирован в большинство клиентов.

> __Tip:__ It's not recommended to keep a high amount of money on your site's address, because you have to enter your private key every time you modify your site.


---


#### Что если какой-то хост содержит злонамеренный контент?

Zeronet сайты запускаются в песочнице и имеют такие же привилегии, как и любой другой сайт, который Вы посещаете в Internet.
У Вас есть полный контроль над тем, что Вы раздаёте. Если Вы обнаружите подозрительный контент, Вы можете удалить у себя этот сайт в любое время.


---


#### Is it possible to install ZeroNet to a remote machine?

Yes, you have to enable the UiPassword plugin by renaming the __plugins/disabled-UiPassword__ directory to __plugins/UiPassword__,
then start ZeroNet on the remote machine using <br>`zeronet.py --ui_ip "*" --ui_password anypassword`.
This will bind the ZeroNet UI webserver to all interfaces, but to keep it secure you can only access it by entering the given password.

> __Tip:__ You can also restrict the interface based on ip address by using `--ui_restrict ip1 ip2`.

> __Tip:__ You can specify the password in the config file by creating a `zeronet.conf` file and adding `[global]` and `ui_password = anypassword` lines to it.


---


#### Is there any way to track the bandwidth ZeroNet is using?

The sent/received bytes are displayed at ZeroNet's sidebar.<br>(open it by dragging the topright `0` button to left)

> __Tip:__ Per connection statistics page: [http://127.0.0.1:43110/Stats](http://127.0.0.1:43110/Stats)


---


#### What happens if two people use the same keys to modify a site?

Every content.json file is timestamped, the clients always accept the newest one with a valid signature.


---


#### Does ZeroNet use Bitcoin's blockchain?

No, ZeroNet only uses the cryptography of Bitcoin for site addresses and content signing/verification.
User identification is based on Bitcoin's [BIP32](https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki) format.

Namecoin's blockchain is being used for domain registrations, however clients do not download the blockchain. Blockchain metadata is instead passed over the ZeroNet network.


---


#### Does ZeroNet only support HTML, CSS websites?

ZeroNet is built for dynamic, real-time updated websites, but you can serve any kind of files using it, such as (VCS repositories, your own thin-client, database, etc.


---


#### How can I create a new ZeroNet site?

[Follow these instructions.](../using_zeronet/create_new_site/)

---


#### What happens when I access a site?

- When you want to open a new site it asks for visitor's IP addresses from BitTorrent trackers.
- Initially, a file named __content.json__ is downloaded, which holds all other filenames,
  __hashes__ and the site owner's cryptographic signature.
- The downloaded content.json file is __verified__ using the site's __address__ and the site owner's __signature__ from the file.
- Other files (html, css, js...) are then __downloaded__ and verified using their size and SHA512 hash from content.json.
- Each visited site then becomes __also served by you__.
- If the site owner (who has the private key for the site address) __modifies__ the site, then he/she signs
  the new content.json and __publishes it to peers__. After the peers have verified the file's
  integrity (using the signature), they __download the modified files__ and serve the new content to other peers.

More info:
 [ZeroNet sample sites](../using_zeronet/sample_sites/),
 [Slideshow about how ZeroNet works](https://docs.google.com/presentation/d/1_2qK1IuOKJ51pgBvllZ9Yu7Au2l551t3XBgyTSvilew/pub)
