# MLOps: Inside Airbnb

Репозиторий для курсового проекта по дисциплине **MLOps** (HSE, BigDataAnalyst).

## Источник данных

В проекте используются открытые данные платформы **Inside Airbnb**: характеристики объектов недвижимости, локации,
описания и стоимость аренды.

Исходный датасет считывается напрямую из S3-совместимого хранилища (`boto3`, endpoint
`https://storage.yandexcloud.net`). При недоступности S3 ноутбук автоматически переключается на локальную копию
`data/train.csv`.

## Структура проекта

```text
.
├── .gitignore              # исключения окружений, секретов, данных и кэша
├── README.md               # этот файл
├── LICENSE                 # лицензия проекта
├── MLOps_Проект.pdf        # описание курсового проекта (локально, *.pdf в .gitignore)
├── data/
│   └── train.csv           # локальная копия датасета для fallback (не коммитится)
├── docker/
│   └── .gitkeep            # Dockerfile — для следующих этапов
├── env/
│   ├── environment.yml     # conda-окружение mlops26
│   ├── requirements.txt    # Python-зависимости (единый список)
│   └── .env                # секреты S3 (не коммитится)
├── notebooks/
│   └── 01_read_data.ipynb  # семинар 1: чтение данных из S3 + fallback
├── src/
│   └── .gitkeep            # Python-скрипты — для следующих этапов
└── tests/
    └── .gitkeep            # unit-тесты — для следующих этапов
```

> **Важно:** CSV-файлы, обученные модели (`*.pkl`, `*.joblib`) и S3-ключи не коммитятся в Git.
> Локальные каталоги IDE (`.idea/`) и кэш ноутбуков (`.ipynb_checkpoints/`) в репозиторий также не попадают.

## Настройка S3

Ключи доступа и параметры S3 передаются через **переменные окружения** и не попадают в репозиторий.

Файл с секретами уже создан в проекте `env/.env`.

Поддерживаемые имена переменных:

- `ACCESS_KEY_ID`;
- `SECRET_ACCESS_KEY`;
- `S3_BUCKET`
- `S3_KEY`.

Пример содержимого:

```bash
ACCESS_KEY_ID=<ACCESS_KEY_ID>
SECRET_ACCESS_KEY=<SECRET_ACCESS_KEY>
S3_BUCKET=mlops26-course-bucket
S3_KEY=prepared/train.csv
```

## Настройка окружения (Conda)

`env/environment.yml` создаёт окружение `mlops26` и устанавливает Python-зависимости из `env/requirements.txt`, лежащего
рядом.
`env/requirements.txt` — единственный список зависимостей он же используется в Docker и CI.

Minoconda должна быть установлена. Для создания окружения выполните:

```bash
conda create --file env/environment.yml
conda activate mlops26
python -m ipykernel install --user --name mlops26 --display-name "mlops26"
jupyter lab
```

Путь `-r requirements.txt` в pip-секции разрешается относительно самого `environment.yml`,
поэтому команду можно запускать из любого каталога.

Если окружение уже создано, а зависимости изменились:

```bash
conda env update -f env/environment.yml --prune
```

Альтернатива без conda — виртуальное окружение:

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r env/requirements.txt
```

> Если окружение создано из `env/environment.yml`, отдельно запускать
> `pip install -r env/requirements.txt` не нужно — зависимости уже установлены из него.

## Запуск ноутбука

```bash
conda activate mlops26
jupyter lab notebooks/01_read_data.ipynb
```

Выбрать ядро **Python (mlops26)** и выполнить ячейки.

