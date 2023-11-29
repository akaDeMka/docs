# Linux troubleshoot commands

### check memory

```bash
free
cat /proc/meminfo
htop/top
sudo dmidecode - detailed memory hardware info
sudo dmidecode -t bios
dmidecode -t system
```

### Top Legend

- total - Total installed memory
- used - Memory currently in use by running processes (used= total – free – buff/cache)
- free - Unused memory (free= total – used – buff/cache)
- shared - Memory shared by multiple processes
- buffers - Memory reserved by the OS to allocate as buffers when process need them
- cached - Recently used files stored in RAM
- buff/cache - Buffers + Cache
- available - Estimation of how much memory is available for starting new applications, without swapping.

## CPU load

```bash
lscpu       # show CPU info
lsusb       # show usb info
lspci       # show PCI devices info
mpstat      # only CPU   mpstat -P ALL - показать загрузку по каждому ядру
sar <10>    # monitor CPU utilization after a specified amount of time
uptime
htop/top
vmstat
cat /proc/cpuinfo | grep 'cpu cores'    # количество ядер
ps axfu                                 # check resources by processors
```

### Top legend

- Load average - числа отражают число блокирующих процессов в очереди на исполнение в определенный временной интервал, а именно 1 минута, 5 минут и 15 минут, соответственно
- us,sy - CPU load (%)
- wa - input/output (disk) >80% abnormal

- us - Time spent in user space
- sy - Time spent in kernel space
- ni - Time spent running niced user processes (User defined priority)
- id - Time spent in idle operations
- wa - Time spent on waiting on IO peripherals (eg. disk)
- hi - Time spent handling hardware interrupt routines. (Whenever a peripheral unit want attention form the CPU, it literally pulls a line, to signal the CPU to service it)
- si - Time spent handling software interrupt routines. (a piece of code, calls an interrupt routine...)
- st - Time spent on involuntary waits by virtual cpu while hypervisor is servicing another processor (stolen from a virtual machine)

## iostat

```bash
install sysstat
vmstat
iostat -h                       #human mode
iostat -x                       #all info
nmon                            # like htop
strace                          # перехват системных вызовов процессора
pidof <service>                 # узнать pid процесса
strace -p 31796                 # подключить к процессу 31796
strace -e trace=stat nautilus   # фильтрация вывода
```

## DISK

```bash
lsblk               # show disks info
lsof                # all open files and processes
iotop               # monitor and display real-time Disk I/O and processes
df
du                  # all files
ls -l -h
lvdisplay           # Display All The Logical Volume
fdisk -l            # View All The Available Partitions
df -i /dev/sda1     # сколько Inode заюзано
```

## Network

```bash
sar -n DEV 1        # проверить пропускную сопсобность
dig <domain>
iperf               # нагрузочный тест (клиент-сервер)
tcpdump
netstat -r          # таблица роутинга, 
netstat -i          # инфо по интерфейсам, 
netstat -s          # статистика по tcp/udp
ping
arpwatch            # designed to monitor Address Resolution of (MAC and IP address changes)
Suricata            # Network Security and Intrusion Detection and Prevention Monitoring System for Linux, FreeBSD, and Windows
Nmon                # monitor all Linux resources such as CPU, Memory, Disk Usage, Network, Top processes, NFS, Kernel, and much more
traceroute          # UDP
```

## File descriptors

```bash
ls -l /proc/$$/fd/ - all open files for current process (file descriptors), $$ - current process PID
cat /proc/$$/fdinfo/0 - almost the same
```

## Шифрование

```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
#сгенирировать самоподписанный сертификат
openssl req -newkey rsa:2048 -nodes -keyout private.key -out request.csr
openssl x509 -req -days 365 -in request.csr -signkey private.key -out certificate.crt
```
