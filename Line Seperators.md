## Line Seperators

Herhangi bir düz metinde satırın bittiğini ve bir alt satıra geçildiği bazı kodlarla belirtilir işletim sistemlerine göre bu komutlarda değişiklik gösterir.

> "UNIX" lerde CR (carriage return ("\r"))
>
> "GNU/Linux" larda (line feed("\n"))
>
> "Windows" ta ise (CRLF("\r\n"))

Windowsta yazılmış bir shell scripti GNU/Linux taşırsak düzgün bir şekilde çalışmayacaktır end of line, segmetation error alabiliriz.