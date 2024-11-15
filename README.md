task1.sh

#!/bin/bash

# Функція для роботи з файлом
process_file() {
    read -p "Введіть маршрутне ім'я файлу: " filepath
    home_dir="$HOME"

    # Перевірка на початок шляху з '/'
    if [[ $filepath == /* ]]; then
        # Якщо шлях починається з /, то перевіряємо чи він починається з домашнього каталогу
        if [[ $filepath == $home_dir/* ]]; then
            target="$filepath"
        else
            echo "Помилка: шлях не починається з домашнього каталогу або відносний."
            return
        fi
    else
        # Якщо шлях відносний, додаємо його до домашнього каталогу
        target="$home_dir/$filepath"
    fi

    # Перевіряємо чи існує файл
    if [[ -f $target ]]; then
        # Якщо файл існує, виводимо його вміст
        cat $target
    else
        # Якщо файл не існує, створюємо його та записуємо заданий рядок
        echo "Файл не існує. Створюємо файл та записуємо рядок."
        echo "Введіть текст для запису в файл:"
        read text
        echo $text > $target
        echo "Файл створено і текст записано."
    fi
}

# Виклик функції
process_file

task2.sh

#!/bin/bash

# Функція для обробки файлів
process_files() {
    dir=$1
    string1=$2
    string2=$3

    # Знаходимо всі файли, що містять обидва рядки
    find $dir -type f | while read file; do
        if grep -q "$string1" "$file" && grep -q "$string2" "$file"; then
            # Видаляємо повторювані рядки
            sort -u "$file" -o "$file"
            echo "Оброблено файл: $file"
        fi
    done
}

# Виклик функції
process_files "$1" "$2" "$3"


task3.sh

#!/bin/bash

# Функція для обробки файлів у каталозі
process_large_files() {
    dir=$1
    size_limit=$2
    word=$3

    # Створюємо 3 нові каталоги
    mkdir -p "$dir/one_line"
    mkdir -p "$dir/two_lines"
    mkdir -p "$dir/three_lines"
    
    # Шукаємо файли, що важчі за заданий розмір
    find $dir -type f -size +"$size_limit"c | while read file; do
        count=$(grep -c "$word" "$file")
        if [[ $count -gt 0 ]]; then
            # Розподіляємо файли за кількістю рядків із заданим словом
            if [[ $count -eq 1 ]]; then
                mv "$file" "$dir/one_line/"
            elif [[ $count -eq 2 ]]; then
                mv "$file" "$dir/two_lines/"
            elif [[ $count -eq 3 ]]; then
                mv "$file" "$dir/three_lines/"
            else
                echo "Файл $file не відповідає умовам."
            fi
        else
            # Якщо не містить задане слово, виводимо його на екран
            echo "Файл не містить заданого слова: $file"
        fi
    done
}

# Виклик функції
process_large_files "$1" "$2" "$3"
