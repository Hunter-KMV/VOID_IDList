# Формат пакета VOID ID-List (schema `void-idlist`, версия 1)

Пакет — обычная папка (репозиторий GitHub) с JSON и PNG. Его читает плагин VOID_System (вкладка «ID-Лист») и будущий сайт проекта. Формат не зависит от языка: читается из C#, JavaScript, Python, PHP.

## Как читать

1. Скачать `manifest.json` (маленький).
2. Сравнить `content_hash` с сохранённым. Совпал — данные те же, ничего не качать.
3. Иначе скачать `idlist.json` и сохранить копию у себя (на случай, если GitHub недоступен).
4. Иконку брать по `icon_url`, а если его нет — `base_url + icon`.

Все ссылки начинаются с `base_url`, например `https://raw.githubusercontent.com/Hunter-KMV/VOID_IDList/main/`.
Если `raw.githubusercontent.com` медленный, тот же файл отдаёт CDN: `https://cdn.jsdelivr.net/gh/Hunter-KMV/VOID_IDList@main/idlist.json`.

> Unturned запоминает картинку по ссылке до перезапуска игры. Поэтому в `icon_url` в конце стоит `?v=<icon_hash>`: поменялась картинка — поменялась ссылка, и игроки увидят новую.

## manifest.json

| Поле | Тип | Что это |
|---|---|---|
| `schema` | строка | всегда `void-idlist` |
| `schema_version` | число | версия формата; при несовместимых изменениях растёт |
| `generated_at` | строка | время экспорта, UTC, ISO 8601 |
| `generator` | строка | программа и её версия |
| `project` | строка | `VOID: Молот войны` |
| `base_url` | строка | начало ссылок на файлы пакета (может быть пустым) |
| `content_hash` | строка | SHA-1 данных; не меняется, если ассеты и иконки те же |
| `files.idlist` | строка | `idlist.json` |
| `files.icons_dir` | строка | `icons/` |
| `counts.assets`, `counts.icons` | число | сколько ассетов и иконок |
| `counts.by_category` | объект | ассеты по категориям, например `{"ITEM": 104}` |
| `mods[]` | массив | `key`, `title`, `asset_count` |

## idlist.json

Корень: `schema`, `schema_version`, `generator`, `generated_at`, `project`, `base_url`, `categories[]` (`key`, `title`), `mods[]`, `assets[]`.

### mods[]

| Поле | Что это |
|---|---|
| `key` | ключ мода: номер в мастерской (`3467904371`) или `unturned` для базовой игры |
| `title` | название для показа |
| `source` | `workshop`, `game` или `folder` |
| `content_type` | тип из файла `*.meta`: `Item`, `Object`, `Map`… |
| `workshop_id`, `workshop_url` | только у модов мастерской |
| `steam_title`, `preview_url`, `steam_updated` | из Steam, если программа смогла их получить (`steam_updated` — unix-время) |
| `master_bundle` | имя мастербандла мода |
| `asset_count` | сколько ассетов мода в списке |

### assets[]

Всегда есть:

| Поле | Тип | Что это |
|---|---|---|
| `guid` | строка | GUID ассета, 32 символа без дефисов, строчные. **Главный ключ** — не меняется |
| `id` | число или `null` | ID из dat; `null`, если у ассета нет ID |
| `category` | строка | пространство ID (см. ниже) |
| `type` | строка | тип из dat: `Gun`, `Shirt`, `Vehicle`, `Vehicle_Redirector`… |
| `name` | строка | имя из `English.dat` (или из файла, см. `name_from_file`) |
| `mod` | строка | `key` мода из `mods[]` |
| `group` | строка | путь папок внутри мода до папки ассета, например `Items/Guns` |
| `file` | строка | имя ассета (имя dat-файла без расширения) |
| `icon` | строка или `null` | путь иконки в пакете: `icons/<guid>.png` |
| `icon_state` | строка | `ok`, `stale` (иконка старее dat — стоит перегенерировать), `empty` (картинка пустая, в пакет не попала), `none` |
| `path` | строка | путь dat-файла от папки мода |

Бывают, если есть в dat:

| Поле | Что это |
|---|---|
| `icon_hash` | 10 символов хеша картинки |
| `icon_url` | полная ссылка на иконку с `?v=icon_hash` (если задан `base_url`) |
| `description` | описание из `English.dat` |
| `names`, `descriptions` | все языки, если в моде есть не только `English.dat`: `{"English": "…", "Russian": "…"}` |
| `name_from_file` | `true`, если имени в `English.dat` нет и оно сделано из имени файла |
| `rarity` | `Common`, `Uncommon`, `Rare`, `Epic`, `Legendary`, `Mythical` |
| `useable`, `slot` | как в dat (`Gun`, `Primary`…) |
| `size` | `[ширина, высота]` в клетках инвентаря |
| `target_guid` | у `Vehicle_Redirector` — GUID техники, на которую он ссылается |
| `type_raw` | тип как записан в dat, если он длинный (`SDG.Unturned.VehicleRedirectorAsset, …`) |

### Категории и ID

У Unturned свой счёт ID в каждой категории, поэтому предмет 42000 и техника 42000 — разные вещи. Искать ассет нужно по паре `category` + `id` или по `guid`.

| `category` | Что | Чем выдаётся в игре |
|---|---|---|
| `ITEM` | предметы, одежда, оружие, баррикады | `/give`, `ItemManager` |
| `VEHICLE` | техника и её перекраски (`Vehicle_Redirector`) | `/vehicle`, `VehicleManager` |
| `OBJECT` | объекты карты, NPC-персонажи | — |
| `EFFECT` | эффекты и UI | `EffectManager` |
| `SPAWN` | таблицы спавна | — |
| `ANIMAL`, `RESOURCE`, `NPC`, `SKIN`, `MYTHIC` | животные, деревья и камни, диалоги/квесты/торговцы, скины, мифические эффекты | — |
| `OTHER` | всё остальное без ID | — |

## Пример: C# (плагин, Newtonsoft.Json есть в OpenMod)

```csharp
public class IdListAsset
{
    [JsonProperty("guid")] public string Guid;
    [JsonProperty("id")] public ushort? Id;
    [JsonProperty("category")] public string Category;
    [JsonProperty("type")] public string Type;
    [JsonProperty("name")] public string Name;
    [JsonProperty("mod")] public string Mod;
    [JsonProperty("icon_url")] public string IconUrl;
}
public class IdListFile
{
    [JsonProperty("schema_version")] public int SchemaVersion;
    [JsonProperty("base_url")] public string BaseUrl;
    [JsonProperty("assets")] public List<IdListAsset> Assets;
}
// var list = JsonConvert.DeserializeObject<IdListFile>(await http.GetStringAsync(baseUrl + "idlist.json"));
// var items = list.Assets.Where(a => a.Category == "ITEM");
// EffectManager.sendUIEffectImageURL(key, connection, true, "Icon_0", item.IconUrl);
```

## Пример: JavaScript (сайт)

```js
const base = 'https://raw.githubusercontent.com/Hunter-KMV/VOID_IDList/main/';
const man = await (await fetch(base + 'manifest.json')).json();
const list = await (await fetch(base + man.files.idlist)).json();
const items = list.assets.filter(a => a.category === 'ITEM');
```

## Изменения формата

- Новые поля могут появляться в любой момент — читатель должен пропускать незнакомые.
- Убрать или переименовать поле можно только с ростом `schema_version`.
