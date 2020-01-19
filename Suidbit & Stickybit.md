## Suidbit & Stickybit

### Suidbit

Bir program dosyasının suid bitini aktif hale getirmek (yani “ chmod +s prog ” gibi bir komut vermek), bu prog programını çalıştıran kullanıcıların, program çalıştığı sürece ve sadece bu program ile ilgili dosyalar açısından, program dosyasının sahibinin yetkilerine sahip olmalarını sağlar. Biraz karışık oldu ama, aşağıdaki örnek açıklayıcı olacaktır.şifresini değiştirmek isteyen bir kullanıcı passwd komutunu kullanacaktır.Bu program kullanıldığında, şifre değişikliği, sahibi root olan /etc/shadow dosyasında bir kayıt değişikliği yapğlmasını gerektirecektir. Ancak bu dosya, LINUX’un en önemli dosyalarrndan birisi olduğu için çok iyi korunmakta ve sahibi (yani root) dışında kimsenin bu dosyaya ne okuma, ne de yazma yetkisi bulunmamaktadır. işte suid kavramı bu soruna bir çözüm getirmektedir. passwd programının yer aldığı /usr/bin/passwd dosyasının suid biti set olduğu için, passwd komutunu veren kullanıcılar bu program çalıştığı sürece ve /etc/shadow dosyasına erişim söz konusu olduğunda geçici olarak root yetkilerine sahip olacaklardır.

```bash
-r-s--x--x 2 root root 512 Feb 24 09:56 passwd
# Bir dosyayı suid dosya yapmak için;
chmod u+s dosya_adi
```

### Stickybit

sticky özelliğine sahip dosyalar, ayrıntılı ls listelerinde bir t harfiyle gösterilir. Eğer “Dizinlere Erişim Yetkileri” bölümünü dikkatli okuduysanğz başkaları için yazma ve okuma yetkisi vereceğiniz bir dizinde yer alan dosyaların tehlikede olduğunu farketmiş olmalısınız. LINUX’ta bu tehlikeyi ortadan kaldırmak için “sticky bit” kullanılır. Bir dizinin “sticky bit”ini kullanarak o dizine yazma-okuma yetkileri ne olursa olsun, söz konusu dosya üzerindeki asıl yetkilerin, dosyanın kendi yetki kalıbı tarafından belirlenmesini sağlayabilirsiniz.

```bash
-rwxr-xr-t 2 root root 512 Feb 24 09:56 ls
# Bir dizinin “sticky bit”ini devreye sokmak için;
chmod +t dizin_adi
```

**Bir dizin için r (read) yetkiniz varsa:** O dizindeki dosyaların isimlerini ls komutu ile görebilirsiniz. Eğer x (execute) yetkiniz yoksa, bir dizin için ls komutunu kullanabilmenize karşın, `ls -l`  komutunu kullanamazsınız.

**Bir dizin için w (write) yetkiniz varsa:** O dizindeki dosyaların yerleşiminde değişiklikler yapabilirsiniz. Örneğin,dosyaların adını değiştirebilirsiniz veya dosyaları silebilirsiniz. Eğer bir dizin de w yetkiniz varsa, fakat o dizin içindeki bir dosyaya w yetkiniz yoksa, o dosyanın içeriğini değiştiremezsiniz, silemezsiniz veya adını değiştiremezsiniz. 

**Bir dizin için x (execute) yetkiniz varsa:** Çalışma dizinizi bu dizin olarak değiştirebilirsiniz. ( cd komutunu bu dizin için kullanabilirsiniz). Bir dizini çalışma dizini olarak kullanmak için r (read) yetkisi yeterli değildir; x yetkisi de gerekir. içinde gizli bilgiler olmayan ama gene de diğer kullanıcılar tarafından kurcalanmasını istemediğiniz dizinler için en uygun yetki düzenlemesi  rwxr-xr-x (755) olarak kabul edilir.

Eğer bir dizininizi sizden başka kimsenin kullanmasını ve içine bakmasını istemiyorsanız, “ chmod go-rwx dizin_adı ” komutuyla o dizinin erişim yetkilerini “ rwx------ ” (700) olarak ayarlayabilirsiniz. Bir dizin için w (write) ve x (execute) yetkiniz varsa: O dizinde yer alan bir dosyaya yazma yetkiniz olmasa bile o dosyayı silebilir veya adını değiştirebilirsiniz. Evet Bu birlikte yaşaması zor bir özellik O yüzden değerli dosyalarınızı içeren dizinler için kendinizden başka kimse için yazma yetkisi vermemelisiniz. Bir başka deyişle bir dizin için verilmiş olabilecek “ rwxrw-rw- ” ya da “ rwx---rwx ” gibi bir yetki kalıbı çok tehlikelidir. Bir nedenle bu tip biryetki kalıbı kullanmak zorunda kalırsanız o dizinin “sticky bit”ini de ayarlamalısınız.