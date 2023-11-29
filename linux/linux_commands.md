# bash

## bash commands

```bash
#check oracle linux release
cat /etc/oracle-release

#ansible commands
ansible webservers -i hosts -m command -a "echo hello"

#обычный вывод в null
command 1> /dev/null

echo "this is a line" | tee -a file.txt

#remove repository
sudo add-apt-repository --remove ppa:jonathonf/ffmpeg-4

# время работы системы
uptime

# current user
whoami

parted
print free
```

### bash brackets

<https://dev.to/rpalo/bash-brackets-quick-reference-4eh6>

```bash
)               # inside shell
$()             # result into the string
(())            # for arifmetic - return 0 or 1(if result =0)
$(())           # put result into the string
[]              # = test
[[]]            # True/false testing
{ Single Curly Braces } # expansion and ranges 
echo "I am "{cool,great,awesome}

${dollar braces} # variable manipulation.
```

### if statment

<https://techrocks.ru/2019/01/21/bash-if-statements-tips/>

```bash
if [[ "$some_variable" == "good input" ]]; then
  echo "You got the right input."
elif [[ "$some_variable" == "ok input" ]]; then
  echo "Close enough"
else
  echo "No way Jose."
fi
```

### printf

<https://linuxhandbook.com/bash-printf/>  
<https://vitux.com/how-to-output-text-on-linux-shell/>

```bash
echo -e "Hello my name \vis John Doe. Hello! My name is \tJane Doe"
printf "Hello, %s! \n" Abhishek Prakash
Hello, Abhishek! 
Hello, Prakash!
```

#### Character Usage

- %s - String
- %c - Single character
- %d - Integers
- %o - Octal integers
- %x - Hexadecimal integers
- %f - Floating point
- %b - String with backslash escape character
- %% - Percent sign

```bash
printf "%10s| %5d\n" Age 23
Age|    23
printf "Name: %10.4s\n" Abhishek # .4 - количество символов строки
```

### Arrays

<https://www.linuxjournal.com/content/bash-arrays>
<https://habr.com/ru/companies/ruvds/articles/413725/>

```bash
${arr[*]}           # All of the items in the array
${!arr[*]}          # All of the indexes in the array
${#arr[*]}          # Number of items in the array
${#arr[0]}          # Length of item zero 
${#arr[@]}          # размер массива
arr=(Hello World)
arr[0]=Hello
arr[1]=World
echo ${arr[0]} ${arr[1]}
```

### for

<https://www.cyberciti.biz/faq/bash-for-loop/>  

```bash
for file in /etc/*
do
done
for i in {START..END..STEP}
for f in $(ls /nas/*.pdf)
```

### case

```bash
case EXPRESSION in
  PATTERN_1)
    STATEMENTS
    ;;

  PATTERN_2)
    STATEMENTS
    ;;

  PATTERN_N)
    STATEMENTS
    ;;

  *)
    STATEMENTS
    ;;
esac
```

### functions

<https://habr.com/ru/companies/ruvds/articles/327248/>

```bash
function myfunc {
}
$#                  # количество переданных функции аргументов
local var           # локальная переменная
source ./myscript   # подключение функций из скрипт (source)
```

### Special bash parameter	Meaning

<https://javarevisited.blogspot.com/2011/06/special-bash-parameters-in-script-linux.html>

- $! - bash script parameter is used to reference the process ID of the most recently executed command in background.
- \$\$ - is used to reference the process ID of bash shell itself
- $# - is quite a special bash parameter and it expands to a number of positional parameters in decimal.
- $0 - bash parameter is used to reference the name of the shell or shell script. so you can use this if you want to print the name of shell script.
- $- - (dollar hyphen) bash parameter is used to get current option flags specified during the invocation, by the set built-in command or set by the bash shell itself. Though this bash parameter is rarely used.
- $? - is one of the most used bash parameters and used to get the exit status of the most recently executed command in the foreground. By using this you can check whether your bash script is completed successfully or not.
- $_ - (dollar underscore) is another special bash parameter and used to reference the absolute file name of the shell or bash script which is being executed as specified in the argument list. This bash parameter is also used to hold the name of mail file while checking emails.
- $@ - (dollar at the rate) bash parameter is used to expand into positional parameters starting from one. When expansion occurs inside double-quotes, every parameter expands into separate words.
- $* - (dollar star) this is similar to $@ special bash parameter  only difference is when expansion occurs with double quotes, it expands to a single word with the value of each bash parameter separated by the first character of the IFS special environment variable.

### globs:

<https://mywiki.wooledge.org/glob>

### linux variable

<https://www.digitalocean.com/community/tutorials/how-to-read-and-set-environmental-and-shell-variables-on-linux-ru>

```bash
printenv (env)  #переменные окружения
set             #переменные оболочки
export          #задать переменную окружения
unset           #удалить переменную
```

- **$SHELL**: описывает оболочку, где будет выполняться интерпретация любых команд, которые вы вводите. В большинстве случаев по умолчанию будет использоваться bash, но другие значения можно установить, если вы предпочитаете использовать другие варианты.
- **$TERM**: указывает тип терминала, который будет использоваться при запуске оболочки. Различные аппаратные терминалы могут имитироваться согласно различным операционным требованиям. Скорее всего вам не придется думать об этом.
- **$USER**: текущий пользователь, для которого выполнен вход.
- **$PWD**: текущий рабочий каталог.
- **$OLDPWD**: предыдущий рабочий каталог. Эта информация сохраняется оболочкой, чтобы выполнять переход к предыдущему каталогу с помощью команды cd -.
- **$LS_COLORS**: цветовые коды, которые используются для опционального добавления цветного вывода для команды ls. Эта команда используется для выделения различных типов файлов и предоставления пользователю большего количества информации при беглом просмотре.
- **$MAIL**: путь к почтовому ящику текущего пользователя.
- **$PATH**: список каталогов, которые система будет проверять при поиске команд. Когда пользователь вводит команду, система будет проверять каталоги в указанном здесь порядке при поиске исполняемого файла.
- **$LANG**: текущий язык и настройки локализации, включая кодирование символов.
- **$HOME**: домашний каталог текущего пользователя.
- **$_**: предыдущая выполненная команда.

### sed,awk

<https://habr.com/ru/companies/ruvds/articles/667490/>

```bash
sed 's/unix/linux/g' geekfile.txt - g - заменить все
sed -n 's/unix/linux/p' geekfile.txt
```

<https://habr.com/ru/companies/ruvds/articles/665084/>

### Systemd Units and Unit Files

https://www.digitalocean.com/community/tutorials/understanding-systemd-units-and-unit-files

### journlactl

<https://habr.com/ru/companies/ruvds/articles/533918/>

```bash
/etc/systemd/journald.conf
/run/log/journal 
journalctl -p 0             # вывод с уровнем важности
journalctl --list-boots
journalctl -b 0             # журнал с текущей загрузки
journalctl --since "2020-12-17" --until "2020-12-18 10:00:00"
journalctl -k               # сообщения ядра
journalctl -u NetworkManager.service
systemctl list-units --type=service
journalctl -f =tail -f
journalctl -e               # перемотка к концу
journalctl --vacuum-size=100M --vacuum-time=7d  #очистить журнал
```

### fstab

<https://help.ubuntu.ru/wiki/fstab>

```bash
UUID=7211313d-8b5e-4b96-be63-5dbbeb97f590 /               ext4    errors=remount-ro 0       1
```

- Что монтируем — некоторое блочное устройство, которое должно быть примонтировано
- Куда монтируем — точка монтирования - путь в корневой файловой системе к каталогу в который будет смонтировано устройство
- Тип файловой системы монтируемого раздела
- Опции монтирования файловой системы
- Индикатор необходимости делать резервную копию (как правило не используется и равно 0)
- Порядок проверки раздела (0- не проверять, 1 - устанавливается для корня, 2 - для остальных разделов).

### rsync

<https://phoenixnap.com/kb/how-to-rsync-over-ssh>
<https://wiki.enchtex.info/tools/console/scp>

```bash
rsync -a ~/Desktop/Dir1/ test@192.168.56.100:~/Desktop/test
scp -r root@server.my:/home/dir/ /home/local/my/
```

### logrotate

<https://linux.die.net/man/8/logrotate>

### find

```bash
find <место_поиска> <ключ-свойство> <значение_свойства>
find . -name "*<file-name>*"
```
