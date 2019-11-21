# Примеры сайтов ZeroNet

## ZeroHello

Домашняя страница ZeroNet

 - Списки добавленных сайтов: Название, число пиров, дата изменения и т.д.
 - Действия с сайтами: Update, Pause, Resume, Delete, Check Files and Save as .zip
 - Клонирование сайта, чтобы получить свой собственный блог / форум
 - Статистика ZeroNet
 - Если есть обновление, ZeroNet может быть обновлён одним кликом

![ZeroHello](../img/zerohello.png)

Адрес: [1HeLLo4uzjaLetFx6NH3PMwFP3qbRbTf3D](http://127.0.0.1:43110/1HeLLo4uzjaLetFx6NH3PMwFP3qbRbTf3D)

[Исходный код](https://github.com/HelloZeroNet/ZeroHello)

---

## ZeroBlog

Демо-версия личного блога

 - Встроеный редактор
 - Синтакс markdown
 - Подсветка синтаксиса кода
 - Подписывание и публикация сайта через веб-интерфейс

Как это работает?

 - `data.json`, находящийся внутри сайта, содержит записи блога и комментарии. Для каждого пользователя он свой.
 - При нажатии `Sign & Publish new content`, у блогера запрашивается приватный ключ сайта (он был показан при [создании нового сайта с помощью команды zeronet.py siteCreate](create_new_site/))
 - Ваши клиенты ZeroNet подписывают новые/изменённые файлы и публикации непосредственно для других пиров
 - Сайт будет доступен для просмотра другим пользователям.

![ZeroBlog](../img/zeroblog.png)

Адрес: [1BLogC9LN4oPDcruNz3qo1ysa133E9AGg8](http://127.0.0.1:43110/1BLogC9LN4oPDcruNz3qo1ysa133E9AGg8) or [blog.zeronetwork.bit](http://127.0.0.1:43110/blog.zeronetwork.bit)

[Исходный код](https://github.com/HelloZeroNet/ZeroBlog)


---

## ZeroTalk

Демо-версия децентрализованного P2P форума

 - Создание, изменение, удаление и добавление сообщений и комментариев
 - Комментирование и изменение контента доставляются непосредственно другим собеседникам
 - Только Вы можете подписать и изменить Ваши файлы
 - Отображение новых комментариев в реальном времени

Как это работает?

 - Для написания сообщений и комментирования Вам надо запросить сертификат регистрации (криптографическую подпись) у провайдера ZeroID
 - После этого Вы можете публиковать Ваш контент непосредственно другим пирам

![ZeroTalk](../img/zerotalk.png)

Адрес: [1TaLkFrMwvbNsooF4ioKAY9EuxTBTjipT](http://127.0.0.1:43110/1TaLkFrMwvbNsooF4ioKAY9EuxTBTjipT) or [talk.zeronetwork.bit](http://127.0.0.1:43110/talk.zeronetwork.bit)

[Исходный код](https://github.com/HelloZeroNet/ZeroTalk)

---

## ZeroMail

Сайт для обмена end-to-end зашифрованными, распределёнными, P2P сообщениями. Для повышения конфиденциальности используются решения как и в BitMessage и адресат сообщения не раскрывается другим.

 - Использование ECIES для обмена секретами, AES256 для шифрования сообщений
 - Когда Вы впервые посещаете этот сайт, он добавляет Ваш публичный ключ в Ваш файл данных. С этого момента любой может послать Вам письмо
 - Каждый пользователь пытается дешифровать каждое письмо, это увеличивает конфиденциальность, делая невозможным найти адресата
 - Для уменьшения оверхеда письма и увеличения скорости расшифровки мы используем AES ключ повторно, но новый IV генерируется каждый раз

![ZeroTalk](../img/zeromail.png)

Адрес: [1MaiL5gfBM1cyb4a8e3iiL8L5gXmoAJu27](http://127.0.0.1:43110/1MaiL5gfBM1cyb4a8e3iiL8L5gXmoAJu27) or [mail.zeronetwork.bit](http://127.0.0.1:43110/mail.zeronetwork.bit)

[Исходнй код](https://github.com/HelloZeroNet/ZeroMail)

---

## ZeroMe

Децентрализованная P2P социальная сеть, подобная Twitter.

 - Сохраняет информацию о пользователе в реестре пользователей ZeroMe
 - Сохраняет сообщения и комментарии в сайтах слияния (MergerSites), называемых хабами (Hubs)
 - Загружает картинки как опциональные файлы
 - Отображение активности в реальном времени
 
![ZeroMe](../img/zerome.png)

Адрес: [1MeFqFfFFGQfa1J3gJyYYUvb5Lksczq7nH](http://127.0.0.1:43110/1MeFqFfFFGQfa1J3gJyYYUvb5Lksczq7nH)

[Исходный код](https://github.com/HelloZeroNet/ZeroMe)

---

## ReactionGIFs

Демо-версия опциональных файлов, которые скачиваются с других пиров только по запросу Вашего браузера

![ReactionGIFs](../img/reactiongifs.jpg)

Адрес: [1Gif7PqWTzVWDQ42Mo7np3zXmGAo3DXc7h](http://127.0.0.1:43110/1Gif7PqWTzVWDQ42Mo7np3zXmGAo3DXc7h)

[Исходный код](https://github.com/HelloZeroNet/ReactionGIFs)

---

## ZeroChat

Заключительный сайт для демонстрации безсерверного, основанного на SQL, обновляющегося в реальном времени Zeronet-чата менее чем из 100 строк кода.

 - Использует сертификаты ZeroID для аутентификации
 - Хранит сообщения в базе данных SQLite
 - Посылает сообщения и доставляет непосредственно другим пользователям в реальном времени
 - Обновляет сообщения в реальном времени как только они поступают

![ZeroChat](../img/zerochat.png)

Адрес: [1AvF5TpcaamRNtqvN1cnDEWzNmUtD47Npg](http://127.0.0.1:43110/1AvF5TpcaamRNtqvN1cnDEWzNmUtD47Npg)

Руководство по ZeroBlog:
 [Часть 1](http://127.0.0.1:43110/Blog.ZeroNetwork.bit/?Post:43:ZeroNet+site+development+tutorial+1),
 [Часть 2](http://127.0.0.1:43110/Blog.ZeroNetwork.bit/?Post:46:ZeroNet+site+development+tutorial+2)
