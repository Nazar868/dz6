# backup.sh

```bash
#!/bin/bash

# Lock-файл для захисту від паралельного запуску
LOCK_FILE="/tmp/backup.lock"

# Перевірка кількості аргументів та існування каталогів
if [ "$#" -ne 2 ] || [ ! -d "$1" ] || [ ! -d "$2" ]; then
    echo "Usage: ./backup.sh <log_dir> <backup_dir>"
    exit 1
fi

LOG_DIR="$1"
BACKUP_DIR="$2"

# Перевірка наявності lock-файлу
if [ -e "$LOCK_FILE" ]; then
    echo "Backup already running"
    exit 1
fi

# Створюємо lock-файл
touch "$LOCK_FILE"

# Видаляємо lock-файл при будь-якому завершенні скрипта
trap 'rm -f "$LOCK_FILE"' EXIT

# Формування імені архіву
TIMESTAMP=$(date +"%Y-%m-%d_%H-%M")
ARCHIVE_NAME="logs_backup_${TIMESTAMP}.tar.gz"
ARCHIVE_PATH="${BACKUP_DIR}/${ARCHIVE_NAME}"

# Створення архіву з усіх файлів каталогу логів
tar -czf "$ARCHIVE_PATH" -C "$LOG_DIR" .

# Перевірка результату архівації
if [ $? -ne 0 ]; then
    echo "Backup failed"
    exit 2
fi

# Виведення повного шляху до створеного архіву
echo "Backup created: $(realpath "$ARCHIVE_PATH")"
```

## Запуск

```bash
chmod +x backup.sh
./backup.sh /path/to/logs /path/to/backup
```
