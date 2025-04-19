#!/bin/bash
restore_dir="$HOME/user/restore"
backup_root="$HOME/user/Backup-"

latest_backup=$(find "$HOME/user" -maxdepth 1 -type d -name "Backup-*" -printf "%T@ %p\n" | sort -nr | head -1 | cut -d' ' -f2)

if [ -z "$latest_backup" ]; then
    echo "Ошибка: резервные копии не найдены." >&2
    exit 1
fi

mkdir -p "$restore_dir" || { echo "Ошибка: не удалось создать каталог $restore_dir." >&2; exit 1; }

find "$latest_backup" -type f ! -name "*.*-*-*" -print0 | while IFS= read -r -d '' file; do
    cp -- "$file" "$restore_dir/$(basename -- "$file")"
done

echo "Файлы из $latest_backup восстановлены в $restore_dir"
