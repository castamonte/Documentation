# Стандарты программирования, если Вы хотите участвовать в разработке ZeroNet
 - Следуйте правилам [PEP8](https://www.python.org/dev/peps/pep-0008/)
 - Лучше проще, чем сложнее
 - Преждевременная оптимизация - корень всего зла

### Именования
 - ClassNames: Capitalized, CamelCased
 - functionNames: начинаются с lowercase, camelCased
 - variable_names: lowercased, under_scored

### Переменные
 - file_path: путь к файлу относительно рабочего каталога (data/17ib6teRqdVgjB698T4cD1zDXKgPqpkrMg/css/all.css)
 - inner_path: файл относительно каталога сайта (css/all.css)
 - file_name: all.css
 - file: объект Python file
 - privatekey: приватный ключ сайта (без _)

### Каталоги и именование исходных файлов
 - Предпочтительно один класс в файле
 - Имена исходного файла и каталога соответствуют имени класса: WorkerManager class = Worker/WorkerManager.py
