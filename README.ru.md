[English](README.md) | **Русский**

# Еженедельный кэш get.dat файлов от runetfreedom

Еженедельный кеш geo-файлов для Xray / Happ.

Апстрим-репозитории обновляют свои `.dat` файлы ежедневно, из-за чего клиент тянет их каждый день. Этот репозиторий раз в неделю снимает срез апстрима и публикует его релизом — ссылки ниже стабильны и меняются только раз в неделю.

## Прямые ссылки на актуальные файлы

Подставьте их в настройки клиента вместо оригинальных:

| Файл | Ссылка |
|------|--------|
| `geoip.dat` | `https://github.com/TBXin/weekly-geo-cache/releases/latest/download/geoip.dat` |
| `geosite.dat` | `https://github.com/TBXin/weekly-geo-cache/releases/latest/download/geosite.dat` |
| `checksums.txt` | `https://github.com/TBXin/weekly-geo-cache/releases/latest/download/checksums.txt` |

`releases/latest/download/` всегда указывает на последний опубликованный релиз и отдаёт `302` на CDN GitHub, поэтому клиент должен следовать редиректам (Xray и Happ это умеют).

## Источники

Файлы берутся без изменений отсюда:

- **geoip.dat** — [runetfreedom/russia-blocked-geoip](https://github.com/runetfreedom/russia-blocked-geoip)  
  `https://raw.githubusercontent.com/runetfreedom/russia-blocked-geoip/release/geoip.dat`
- **geosite.dat** — [runetfreedom/russia-blocked-geosite](https://github.com/runetfreedom/russia-blocked-geosite)  
  `https://raw.githubusercontent.com/runetfreedom/russia-blocked-geosite/release/geosite.dat`

Содержимое файлов не модифицируется — это побайтовая копия апстрима на момент снятия среза.

## Как это работает

GitHub Actions ([`.github/workflows/mirror.yml`](.github/workflows/mirror.yml)) по расписанию:

1. Запускается каждый понедельник в 03:00 UTC.
2. Скачивает оба файла из апстрим-репозиториев.
3. Проверяет, что файлы не пустые и не являются HTML-ошибкой; считает SHA-256.
4. Публикует релиз с тегом вида `2026.08.31` и прикладывает `geoip.dat`, `geosite.dat`, `checksums.txt`.
5. Обновляет `last-sync.txt`, чтобы репозиторий не считался неактивным.

Запустить обновление вручную: **Actions → Weekly geo mirror → Run workflow**.

## Проверка целостности

```bash
curl -fsSLO https://github.com/TBXin/weekly-geo-cache/releases/latest/download/geoip.dat
curl -fsSLO https://github.com/TBXin/weekly-geo-cache/releases/latest/download/geosite.dat
curl -fsSL  https://github.com/TBXin/weekly-geo-cache/releases/latest/download/checksums.txt | sha256sum -c -
```

## Примечания

- Расписание GitHub Actions выполняется в UTC и может опаздывать на 10–60 минут, а при высокой нагрузке запуск иногда пропускается. Для недельного среза это некритично.
- Если workflow не запускался 60 дней, GitHub отключает расписание автоматически. Коммит `last-sync.txt` на каждом прогоне предотвращает это.
- Релизы не помечаются как pre-release — иначе ссылка `latest/download/` перестанет на них указывать.
- История релизов сохраняется, поэтому при поломке апстрима можно откатиться на любой прошлый срез, взяв ссылку из [Releases](../../releases).

## Лицензия

Репозиторий содержит только автоматизацию. Права на сами `.dat` файлы принадлежат авторам апстрим-проектов и распространяются на условиях их лицензий.