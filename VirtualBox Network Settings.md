## VirtualBox Network Settings

### NAT (Network Address Translation)

Ana işletim sistemi sanal makina ile kendisi arasına bir NAT Engine kurar (2 tane NAT olarak ayarlanmış sanal makina var ise ana makina 2 si içinde ayrı ayrı NAT Engine oluşturur.) ve bu NAT Engine IP Adresi değişimi yapar. Ana makina sanal makinalar için IP atar. 2 sanal makina NAT ile ayarlanmıi ise bu 2 sanal makina birbirleri ile haberleşemez. Ana makina ile sanal makina haberleşemezler fakat sanal makina dış internete çıkabilir.

### NAT (Network Address Translation) Network

Bu seçenek için sanal NAT ağı oluşturmamız gerekiyor bunun için file > preferences > network kısmından yeni bir NAT Network oluşturmamız gerekiyor. Ana işletim sistemi sanal makinalar için bir sanal ağ oluşturur (Ana makina sanal makinalar için farklı IP Adresleri atar.) ve NAT Network olarak ayarlanmış bütün sanal makinaları bu ağa dahil eder 1 tane NAT Engine kullanılır. Sanal makinalar kendi aralarında haberleşebilir fakat Ana makina ile haberleşemezler, Dış 
internete çıkabilirler.

### Bridged

Ana işletim sistemi sanal makinayı sanki farklı bir makina imiş gibi görür ve sanal makina ana cihazın bağlı olduğu modemden direk IP alır ve bu sayede sanal makina ile ana makina aynı ağda olmuş olur. Sanal makinalar kendi aralarında haberleşebilir, Dış internete çıkabilirler aynı zamanda ana makina ile iletişime geçebilirler.

### Internal Network

Ana makina Sanal makinalar için bir iç ağ kurar ve IP Adresi dağıtımı yapar. Sanal makinalar kendi aralarında iletişim kurabilir fakat ana makina ve Dış internet ile iletişim kuramazlar.

### Host-Only Network

Bunun için VirtualBox ta file > Host Network Manager kısmından bir sanal ağ oluşturmamız gerekiyor oluşturduktan sonra bu seçeneği kullanabiliriz. Bu seçenek ise sanal makinalar için bir sanal iç ağ kurar ve sanal makinalar kendi aralarında ve ana makina ile haberleşebilir fakat Dış internete çıkamazlar.



| Network Type                        | Guest - Other Guests | Host - Guest | Guest - External Network |
| :---------------------------------- | :------------------: | :----------: | :----------------------: |
| Network Address Translation (NAT)   |          -           |      -       |            +             |
| Network Address Translation Service |          +           |      -       |            +             |
| Bridged Networking                  |          +           |      +       |            +             |
| Internal Networking                 |          +           |      -       |            -             |
| Host-only Networking                |          +           |      +       |            -             |



