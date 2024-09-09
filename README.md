# CONFIG-prac-1
# Задание 1
```
grep '.*' /etc/passwd | cut -d':' -f1 | sort
```
![image](https://github.com/user-attachments/assets/9f084527-d314-407d-992f-78a87bcdd5d0)


# Задание 2
```
cat /etc/protocols | awk '{print $2, $1}' | sort -nr | head -5
```
![image](https://github.com/user-attachments/assets/0eb7ef82-030d-4185-9843-c25eb0583bb6)


# Задание 3
```
nano banner.sh
```

```
#!/bin/bash
 
if [ $# exit 1]; then
        echo "Использование: $0 \"Hello from RTU MIREA!\""
        exit 1
fi
 
text="$1"
text_length=${#text}
border_length=$((text_length + 4))
 
#Вывод верхней границы
echo "+$(printf -- '-%.0s' $(seq 1 $border_length))+"
 
# Вывод текста в баннере
echo "| $text |"
 
# Вывод нижней границы
echo "+$(printf -- '-%.0s' $(seq 1 $border_length))+"
```
```
#сделали файл исполняемым
chmod +x banner.sh
```
```
#запуск скрипта
./banner.sh "Hello from RTU MIREA!"
```
![image](https://github.com/user-attachments/assets/5684973e-6485-4084-849d-a9000da610d9)


# Задание 4
```
nano hello.c
```
```
#include <stdio.h>

void hello() {
    printf("Hello, world!\n");
}

int main() {
    hello();
    return 0;
}
```
```
grep -o -E '\b[_a-zA-Z][_a-zA-Z0-9]*\b' hello.c | sort | uniq
```
![image](https://github.com/user-attachments/assets/da79ac58-5018-4ded-b446-4fc07d82b970)




# Задание 5
```
nano reg
```
```
#!/bin/bash
 
if [ $# -ne 1 ]; then
        echo "Использование: $0 <команда>"
        exit 1
fi
 
command="$1"
 
if [ -x "$command" ]; then
        echo "Команда $command уже существует и имеет исполняемые права."
        exit 1
fi
 
if [ -f "$command" ]; then
        chmod +x "$command"
        cp "$command" /usr/local/bin/
        echo "Команда $command зарегистрирована и скопирована в /usr/local/bin/"
else
        echo "Команда $command не найдена."
        exit 1
fi
```
```
chmod +x reg
```
```
./reg banner.sh
```
```
cat reg
```
```
./reg banner.sh
```
```
sudo cp banner.sh /usr/local/bin/
```
```
ls -l /usr/local/bin/banner.sh
```
```
banner.sh "Hello from RTU MIREA!"
```
![image](https://github.com/user-attachments/assets/70fd607e-f7e9-4a44-886b-dfae77e44d0f)


# Задание 6
```
nano check_comments.sh
```
```
#!/bin/bash
 
if [ $# -ne 1 ]; then
        echo "Использование: $0 <путь_к_каталогу>"
        exit 1
fi
 
directory="$1"
 
# Проверяем, существует ли указанный каталог
if [ ! -d "$directory" ]; then
        echo "Каталог $directory не существует."
        exit 1
fi
 
# Проверяем наличие комментария в первой строке файла
check_comment() {
        local file="$1"
        local extension="${file##*.}"  # Получаем расширение файла
 
        # Если расширение файла соответствует .c, .js или .py
        if [[ "$extension" == "c" || "$extension" == "js" || "$extension" == "py" ]]; then
                # Считываем первую строку файла и проверяем, начинается ли она с комментария
                first_line=$(head -n 1 "$file")
                if [[ "$first_line" =~ ^[[:space:]]*\/\/.* || "$first_line" =~ ^[[:space:]]*#.* ]]; then
                        echo "Файл $file содержит комментарий в первой строке."
                else
                        echo "Файл $file не содержит комментария в первой строке."
                fi
        fi
}
```
```
chmod +x check_comments.sh
```
```
pwd
```
```
ls -R
```
```
./check_comments.sh
```
```
cat check_comments.sh
```
![image](https://github.com/user-attachments/assets/ce13b7b8-a1af-4e2f-9f95-725cbbec8398)


# Задание 7
```
nano find_duplicates.sh
```
```
#!/bin/bash

if [ $# -ne 1 ]; then
  echo "Использование: $0 <путь_к_каталогу>"
  exit 1
fi

directory="$1"

# Поиск дубликатов с использованием md5sum
find "$directory" -type f -print0 | xargs -0 md5sum | sort | uniq -d -w 32 | sed -r 's/^[0-9a-f]*( )//' | while read -r duplicate; do
  echo "Дубликат: $duplicate"
done
```
```
chmod +x find_duplicates.sh
```
```
./find_duplicates.sh /root
```
![image](https://github.com/user-attachments/assets/1bab1eb5-c95d-480f-a387-8a8864e0ff59)



# Задание 8
```
nano archive_files.sh
```
```
#!/bin/bash

if [ $# -ne 2 ]; then
  echo "Использование: $0 <расширение_файлов> <имя_архива>"
  exit 1
fi

extension="$1"
archive_name="$2"

# Поиск файлов с заданным расширением в текущем каталоге
files=$(find . -type f -name "*.$extension")

# Проверка наличия файлов
if [ -z "$files" ]; then
  echo "Файлов с расширением .$extension не найдено."
  exit 1
fi

# Архивация файлов
tar -czvf "$archive_name.tar.gz" $files

echo "Файлы с расширением .$extension были архивированы в $archive_name.tar.gz"
```
```
./archive_files.sh txt myarchive
```
```
tar -tzvf myarchive.tar.gz
```
```
ls -l
```
```
ls -ld
```
![image](https://github.com/user-attachments/assets/55316458-e194-4419-9e69-4b740333cd3f)



# Задание 9
```
nano replace_spaces.sh
```
```
#!/bin/bash

if [ $# -ne 2 ]; then
  echo "Использование: $0 <входной_файл> <выходной_файл>"
  exit 1
fi

input_file="$1"
output_file="$2"

# Проверка существования входного файла
if [ ! -f "$input_file" ]; then
  echo "Входной файл $input_file не существует."
  exit 1
fi

# Замена последовательностей из 4 пробелов на символ табуляции и запись в выходной файл
sed 's/    /\t/g' "$input_file" > "$output_file"

echo "Замена завершена. Результат сохранен в $output_file."
```
```
chmod +x replace_spaces.sh
```
```
echo "This is a test file with    four spaces." > input.txt
```
```
cat input.txt
```
```
./replace_spaces.sh input.txt output.txt
```
```
cat output.txt
```
```
od -c output.txt
```
![image](https://github.com/user-attachments/assets/b3323cf3-f133-44bd-84ee-f0806001cde0)



# Задание 10
```
nano find_empty_files.sh
```
```
#!/bin/bash

if [ $# -ne 1 ]; then
  echo "Использование: $0 <директория>"
  exit 1
fi

directory="$1"

# Проверка существования указанной директории
if [ ! -d "$directory" ]; then
  echo "Директория $directory не существует."
  exit 1
fi

##Поиск пустых текстовых файлов
find "$directory" -type f -empty -exec file {} \; | grep "empty" | cut -d: -f1
```
```
find /home/testdir -maxdepth 1 -type f -size 0c
```
![image](https://github.com/user-attachments/assets/2053ad36-dbb8-4a80-ab22-cef22b000e3b)
