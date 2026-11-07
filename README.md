# VOID_IDList

ID-лист сервера Unturned «VOID: Молот войны»: все предметы, техника и прочие ассеты модов сервера с ID, GUID, названиями и иконками.

Пакет собирает программа **VOID ID-List Builder** из папки модов мастерской, бандлов игры и папки иконок `Unturned\Extras\Icons`. Руками здесь ничего не правится: всё перезаписывается при следующем экспорте.

## Что внутри

| Файл | Зачем |
|---|---|
| `manifest.json` | Короткая сводка и `content_hash`. Плагин и сайт читают его первым и качают `idlist.json`, только если хеш поменялся |
| `idlist.json` | Сам ID-лист: моды и ассеты, одна запись на строку |
| `icons/<guid>.png` | Иконки, имя файла = GUID ассета |
| `index.html` | Просмотр ID-листа в браузере (работает на GitHub Pages) |
| `FORMAT.md` | Описание всех полей для плагина VOID_System и сайта |

## Сейчас в пакете

| Мод | Ассетов |
|---|---|
| warhammer ([3467902563](https://steamcommunity.com/sharedfiles/filedetails/?id=3467902563)) | 59 |
| void_vehicles ([3467904371](https://steamcommunity.com/sharedfiles/filedetails/?id=3467904371)) | 32 |
| Unturned (базовая игра) | 2699 |

Всего ассетов: **2790**, иконок: **2331**.

## Ссылки

- Сводка: https://raw.githubusercontent.com/Hunter-KMV/VOID_IDList/main/manifest.json
- ID-лист: https://raw.githubusercontent.com/Hunter-KMV/VOID_IDList/main/idlist.json
- Иконка: https://raw.githubusercontent.com/Hunter-KMV/VOID_IDList/main/icons/<guid>.png

Репозиторий должен быть **публичным**: игра и сайт берут файлы по прямой ссылке без входа в GitHub.
