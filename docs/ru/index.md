## Что такое ZeroNet?

ZeroNet использует криптографию Bitcoin и технологию BitTorrent для постройки **децентрализованной цензуроустойчивой сети**.

Пользователи сети могут публиковать в ZeroNet статические и динамические вебсайты, посетители которых могут также и раздавать их. Вебсайт будет доступным пока хотя бы один его пользователь остаётся в онлайне.

Когда сайт обновляется его владельцем, все узлы, раздающие его (посещавшие ранее), получат инкрементальные обновления содержимого сайта.

ZeroNet поставляется со встроенной базой данных SQL. Это упрощает разработку сложных сайтов. База данных в дальнейшем синхронизируется на раздающих нодах через инкрементальные обновления.


## Почему?

* Мы верим в открытую, бесплатную сеть и связь без цензуры.
* Нет цензуры: Если что-либо опубликовано, это невозможно удалить.
* Нет единой точки отказа: Контент доступен, пока хотя бы один узел раздаёт его.
* Невозможно выключить: Это нигде, потому что это везде. Контент раздаётся любым пользователем, который этого хочет.
* Быстро: ZeroNet использует для доставки контента технологию BitTorrent, которая быстрее централизованных серверов.
* Работает автономно: У Вас есть доступ к сайту даже при отсутствии интернета.
* Безопасно: Владение контентом обеспечивается с помощью той же криптографии, что и ваш биткойн-кошелек.

[comment]: <> (I'm unsure about the following bit. Thoughts?)
[comment]: <> (# What problem is ZeroNet solving?)

[comment]: <> (When Tim Berners-Lee created the internet, he meant for it to be free. Not surveilled nor censored. And [he is still fighting for that](http://edition.cnn.com/2014/03/12/tech/web/tim-berners-lee-web-freedom/).)

[comment]: <> (The internet is centralized mainly in two places: Content and Domain Names (URLs) are hosted and controlled by central servers. If you control the central servers (and if you are powerful enough you do) you control the network.)

[comment]: <> (**Decentralized content storage**)

[comment]: <> (ZeroNet tackles the content storage problem by giving everyone the ability to store content. Site visitors can choose to store a website on their computers, and when they do this they also help to serve the site to other users. The site is online even if only one user is hosting it.)

[comment]: <> (**Shared DNS cache**)

[comment]: <> (Site addresses on ZeroNet are cached by all network members. When you type a ZeroNet site URL on your browser this will query other peers connected to you about the site. If one of these peers happen to have the site they will send it to you, if not, they will forward your query along.)

[comment]: <> (This architecture means that when a site URL is created, as long as one peer is serving it, there is no way to take the URL down.)


## Функции

 * Простая установка, не требующая настройки.
 * Авторизация без пароля, основанная на [BIP32](https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki): Ваш аккаунт защищён той же криптографией, что и Ваш биткойн-кошелёк.
 * Сайты обновляются в реальном времени, специально синхронизировать не нужно.
 * Поддержка .bit доменов Namecoin.
 * Поддержка SQL базы данных позволяет упростить разработку сайтов и ускорить их загрузку.
 * Анонимность: Полная поддержка сети Tor с её скрытыми сервисами .onion вместо адресов ipv4.
 * Шифрование соединений с помощью TLS.
 * Автоматическое открывание портов с помощью uPnP.
 * Поддержка многопользовательского плагина.
 * Работает со всеми браузерами и ОС.


## Как это работает?

* После инсталляции и запуска Zeronet, Вы открываете сайт по адресу
  `http://127.0.0.1:43110/{zeronet_site_address}` (например `http://127.0.0.1:43110/1HeLLo4uzjaLetFx6NH3PMwFP3qbRbTf3D`).
* Zeronet по сети BitTorrent находит узлы, раздающие сайт и скачивает контент (HTML, CSS, JS...) с них.
* Каждый посещённый сайт будет раздаваться клиентом. Сайты могут быть удалены с клиента или помещены в чёрный список если необходимо.
* Каждый сайт содержит список всех его файлов с их SHA512 хэшами и подписями, сгенерированными с помощью приватного ключа владельца сайта.
* Когда владелец сайта изменяет его содержимое, он подписывает новый список и публикует его раздающим узлам (пирам).
  Пиры, проверив целостность списка файлов (используя подписи), скачивают изменённые файлы и раздают новый контент другим пирам.

##### [Слайдшоу про криптографию ZeroNet, обновления контента, многопользовательские сайты &raquo;](https://docs.google.com/presentation/d/1_2qK1IuOKJ51pgBvllZ9Yu7Au2l551t3XBgyTSvilew/pub?start=false&loop=false&delayms=3000)


## Снимки экрана

![Скриншот](./img/zerohello.png)

![ZeroTalk](./img/zerotalk.png)

##### [Больше скриншотов &raquo;](/using_zeronet/sample_sites/)

## Ограничения

* <strike>Нет торрентоподобного разделения файлов для поддержки больших файлов</strike> (плагин BigFile разрешает это)
* Передача файлов не поддерживает сжатие <strike>и шифрование</strike> (добавлено шифрование TLS)
* Нет приватных сайтов

## Поддержите жизнь проекта

Bitcoin: 1QDhxQ6PraUZa21ET5fYUCPgdrwBomnFgX

[Страница поддержки проекта](help_zeronet/donate/)

### Спасибо!

* Больше информации, помощи, список изменений, zeronet сайтов: [http://www.reddit.com/r/zeronet/](http://www.reddit.com/r/zeronet/)
* Связь с нами: [#zeronet @ FreeNode](https://kiwiirc.com/client/irc.freenode.net/zeronet) или [gitter](https://gitter.im/HelloZeroNet/ZeroNet)
