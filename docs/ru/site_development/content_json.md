# Структура content.json

Каждый ZeroNet сайт имеет файл `content.json`. ([Пример файла content.json](https://github.com/HelloZeroNet/ZeroTalk/blob/master/content.json))

В этом файле, помимо прочего, содержится список всех файлов Вашего сайта и подписи, созданные Вашим приватным ключом. Это сделано, чтобы аутентифицировать файлы сайта и избежать подделки (напр. только Вы или люди, которым Вы доверяете, могут изменять контент Вашего сайта).

Вот список ключевых слов, поддерживаемых `content.json`:


---

## Генерируются автоматически

_Эти ключи генерируются автоматически при создании или клонировании сайта._

### address

Адрес Вашего сайта

**Пример**: 1TaLk3zM7ZRskJvrh3ZNCDVGXvkJusPKQ


---


### address_index

BIP32 индекс подключа от Вашего начального числа BIP32 для адреса сайта. Добавляется автоматически, когда Вы клонируете сайт. Позволяет восстановить приватный ключ сайта по Вашему начальному числу BIP32.

**Пример**: 30926910

---


### cloned_from

Только для клонированных сайтов. Адрес сайта, который был склонирован.

**Пример**: 1BLogC9LN4oPDcruNz3qo1ysa133E9AGg8

---


### clone_root

Только для клонированных сайтов. Поддиректория сайта, с которой сайт был склонирован.

**Пример**: template-new


---


### files

Размеры и sha512 хэши автоматически загружаемых файлов Вашего сайта. Автоматически добавляются по команде `zeronet.py siteSign siteaddress privatekey`.

**Пример**:
```python
    "css/all.css": {
      "sha512": "869b09328f07bac538c313c4702baa5276544346418378199fa5cef644c139e8",
      "size": 148208
    }
```


---


### files_optional

Размеры и sha512 хэши опциональных файлов Вашего сайта. Автоматически добавляются по команде `zeronet.py siteSign siteaddress privatekey`.

**Пример**:
```python
    "data/myvideo.mp4": {
      "sha512": "538c09328aa52765443464135cef644c144346418378199fa5cef61837819538",
      "size": 832103
    }
```



---


### modified

Время создания content.json.

**Пример**: 1425857522.076


---


### sign (устарело)

ECDSA подпись содержимого content.json. (отсортированы по ключу, без пробелов и узлов `sign` и `signers_sign`). Для обратной совместимости, скоро будет удалено.

**Пример**:
```python
  "sign": [
    43117356513690007125104018825100786623580298637039067305407092800990252156956,
    94139380599940414070721501960181245022427741524702752954181461080408625270000
  ],
```


---


### signers_sign

Адреса возможных подписывающих основной content.json, подписанные приватным ключом сайта. Здесь возможны множественные включения, что позволяет использовать мультиподписи.

**Формат строки подписывающих**: [требуемое_количество_подписавших]:[адрес подписывающего],[адрес подписывающего]

*Пример*:
```
signs_required: 1:1PcxwuHYxuJEmM4ydtB1vbiAY6WkNgsz9G,1CK6KHY6MHgYvmRQ4PAafKYDrg1ejbH1cE
signers_sign: MEUCIQDuz+CzOVvFkv1P2ra9i5E1p1G0/1cOGecm7GpLpMLhuwIgBIbCL0YHXD1S2+x48QS5VO/rISrkdLiUR+o+x1X0y1A=
```

Подписанное сообщение выше подписано с помоью адреса "1PcxwuHYxuJEmM4ydtB1vbiAY6WkNgsz9G".

---


### signs

ECDSA подпись содержимого файла content.json:

 - удаляются узлы JSON `sign` и `signs`
 - делается дамп JSON с сортировкой ключей по алфавиту, без пробелов
 - дамп подписывается с использованием формата подписи сообщений Electrum Bitcoin:
    * [Кодирование сообщения](https://github.com/vbuterin/pybitcointools/blob/87806f3c984e258a5f30814a089b5c29cbcf0952/bitcoin/main.py#L405): `sha256("\x18" || "Bitcoin Signed Message:\n" || num_to_var_int(len(message)) || message)`
    * [Формат сериализации](https://github.com/MuxZeroNet/zerolib/blob/f13126e04bf99b1b416a7ea5b5cad7924cdc15a4/zerolib/integrity/bitcoin.py#L82-L93): `recovery_id || r || s`, где 27 ≤ recovery_id ≤ 30; длина подписи = 1 + 32 + 32 = 65 байт.
    * Двойная вертикальная черта `||` означает конкатенацию байтов.

**Пример**:
```python
  "signs": {
    "1TaLk3zM7ZRskJvrh3ZNCDVGXvkJusPKQ": "G6/QXFKvACPQ7LhoZG4fgqmeOSK99vGM2arVWkm9pV/WPCfc2ulv6iuQnuzw4v5z82qWswcRq907VPdBsdb9VRo="
  },
```
**Пример кода**
```python
import json
import btctools

privatekey = "super_secret_private_key"
privatekey_address = "private_key_address"

with open('content.json') as f:
    new_content = json.load(f)

del(new_content["signs"])  # Delete old signs
sign_content = json.dumps(new_content, sort_keys=True)
sign = btctools.ecdsa_sign(sign_content, privatekey)
new_content["signs"] = {}
new_content["signs"][privatekey_address] = sign
```

----


### zeronet_version

Версия ZeroNet использованная для создания файла content.json.

**Пример**: 0.2.5

---

## Опциональные настройки

_Эти опции могут быть добавлены, если требуется указанная функциональность._


### background-color

Цвет бэкграунда враппера

**Пример**: #F5F5F5


---


### cloneable

Позволяет клонировать сайт, если установлен в **true**.

To make your site properly cloneable you have to have a separate folder of data
files for a clean start (e.g. without any blog posts).  To do this you have to
add the **-default** postfix to your data files and directories.  During the
cloning process, only directories with the **-default** postfix are
copied. The postfix is removed from the new site.


---


### description

Description of your site, displayed under the site title on ZeroHello.

**Example**: Decentralized forum demo


---


### domain

Namecoin domain name of your site. ZeroHello will link to this if the user has Zeroname plugin enabled.

**Example**: Blog.ZeroNetwork.bit




---


### ignore

Do not sign files matching this pattern.

**Example**: `((js|css)/(?!all.(js|css))|data/users/.*)` (ignore all js and css files except all.js and all.css and don't add anything from the `data/users/` directory)

Note: [Some restrictions](#regular-expression-limitations) apply to regular expressions.

---


### includes

Include another content.json in the site. This is typically used for subsequent content.json files that are used to govern user data.

**Example**:

```python
"includes": {
  "data/users/content.json": {
    "signers": [  # Possible signers address for the file
      "1LSxsKfC9S9TVXGGNSM3vPHjyW82jgCX5f"
    ],
    "signers_required": 1 # The *number* of Valid signs required to accept the file (Multisig possibility),
    "files_allowed": "data.json", # Preg pattern for the allowed files in the include file
    "includes_allowed": false, # Whether nested includes are allowed
    "max_size": 10000, # Max allowed size of included content.json and files it signs (in bytes)
  }
}
```


---


### merged_type

The type of merger this site is a part of.

**Example**: `ZeroMe`


---


### optional

Preg pattern of optional files.

**Example**: `(data/mp4/.*|updater/.*)` (everything in `data/mp4` and `updater` directory is optional)

Note: [Some restrictions](#regular-expression-limitations) apply to regular expressions.

---


### signs_required

The **number** of valid signs required to accept the file. Allows for Multisig functionality.


**Example**: 1


---


### title

The site's title, visible in the browser title and on ZeroHello.

**Example**: ZeroTalk


----


### translate

Files need be translated. (use language json files in the `languages` directory)

**Example**: ["index.html", "js/all.js"]


----


### favicon

The site's favicon. Replaces the default ZeroNet logo with a site-specific icon. Can be a .ico, .png, .svg, etc.

**Example**: favicon.ico


----


### user_contents

Rules of allowed user content within the current directory.

Node                     | Description
                    ---  | ---
**archived**             | Delete the specified user content directory that is signed earlier than the specified timestamp (key: directory name, value: timestamp)
**archived_before**      | Delete all user content directory if that is signed earlier than the specified timestamp
**cert_signers**         | Accepted domains and valid signer addresses
**cert_signers_pattern** | Accepted cert signers regexp pattern
**permission_rules**     | Allowed file names and total directory size based on cert domain or authorization method
**permissions**          | Per-user permissions. (false = banned user)

**Example**:
```python
  "user_contents": {
    "archived": {
      "1165u6pt5mQNFjyhMVwy6azB7bZuQGLA9b": 1523088096
    },
    "archived_before": 1523088096,
    "cert_signers": {
      "zeroid.bit": [ "1iD5ZQJMNXu43w1qLB8sfdHVKppVMduGz" ]
    },
    "cert_signers_pattern": "1Zero[0-9].*",
    "permission_rules": {
      ".*": {
        "files_allowed": "data.json",
        "files_allowed_optional" : "\\.(png|jpeg|jpg|gif|webm|mp4|ogg|mp3|pdf|epub|zip|tar\\.gz)(\\.piecemap\\.msgpack)",
        "max_size": 10000,
        "max_size_optional": 10000000
      },
      "bitid/.*@zeroid.bit": { "max_size": 40000 },
      "bitmsg/.*@zeroid.bit": { "max_size": 15000 }
    },
    "permissions": {
      "bad@zeroid.bit": false,
      "nofish@zeroid.bit": { "max_size": 100000 }
    }
  }
```

Note: [Some restrictions](#regular-expression-limitations) apply to regular expressions.

----


### viewport

Content for the viewport meta tag. (Used for mobile-friendly pages)

**Example**: width=device-width, initial-scale=1.0


----

## Regular expression limitations

To avoid the [ReDoS](https://en.wikipedia.org/wiki/ReDoS) algorithmic complexity attack, the following restrictions are applied to each pattern:

 - `.` character is mandatory before repetition characters of `*,+,{`
 - Maximum 9 repetitions are allowed in a single pattern
 - The maximum length of a pattern is 255 characters

### Examples:

 - `((?!json).)*$` not allowed, because of `)` before the `*` character. Possible fix: `.*(?!json)$`
 - `(.*.epub|.*.jpg|.*.jpeg|.*.png|data/.*.gif|.*.avi|.*.ogg|.*.webm|.*.mp4|.*.mp3|.*.mkv|.*.eot)` not allowed, because it has 12 `.*` repetition patterns. Possible fix: `.*(epub|jpg|jpeg|png|data/gif|avi|ogg|webm|mp4|mp3|mkv|eot)`
