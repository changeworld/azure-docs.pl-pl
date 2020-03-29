---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: 2d2a82552a846cedfa5da3bb6ec6df8a40b67732
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671488"
---
Chociaż funkcja jest łatwa w konfiguracji, nie oznacza to, że twoje doświadczenie będzie bezproblemowe. Jeśli wystąpią problemy z dostępem do żądanego punktu końcowego istnieją pewne narzędzia, których można użyć do testowania łączności z konsoli aplikacji. Istnieją dwie konsole, których można użyć. Jednym z nich jest konsola Kudu, a druga jest konsolą w witrynie Azure portal. Aby dotrzeć do konsoli Kudu z aplikacji, przejdź do narzędzia -> Kudu. Można również dotrzeć do konsoli Kudo w [sitename].scm.azurewebsites.net. Po załadowaniu witryny sieci Web przejdź do karty Konsoli debugowania. Aby przejść do konsoli hosta hostowanego w witrynie Azure portal, a następnie z aplikacji przejdź do konsoli narzędzia ->. 

#### <a name="tools"></a>Narzędzia
Narzędzia **ping**, **nslookup**i **tracert** nie będą działać przez konsolę ze względu na ograniczenia bezpieczeństwa. Aby wypełnić pustkę, dodano dwa oddzielne narzędzia. Aby przetestować funkcjonalność DNS, dodaliśmy narzędzie o nazwie nameresolver.exe. Składnia jest następująca:

    nameresolver.exe hostname [optional: DNS Server]

Za pomocą **nameresolver** można sprawdzić nazwy hostów, od których zależy aplikacja. W ten sposób można sprawdzić, czy masz coś nieprawidłowo skonfigurowany z DNS lub być może nie mają dostępu do serwera DNS. Serwer DNS, którego aplikacja będzie używać w konsoli, można wyświetlić, patrząc na zmienne środowiskowe WEBSITE_DNS_SERVER i WEBSITE_DNS_ALT_SERVER.

Następne narzędzie umożliwia przetestowanie łączności TCP z kombinacją hosta i portu. To narzędzie jest nazywane **tcpping** i składni jest:

    tcpping.exe hostname [optional: port]

Narzędzie **tcpping** informuje, czy można dotrzeć do określonego hosta i portu. Może pokazać sukces tylko wtedy, gdy: istnieje aplikacja nasłuchiwania na hoście i kombinacji portu i istnieje dostęp do sieci z aplikacji do określonego hosta i portu.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Debugowanie dostępu do zasobów hostowanych w sieci wirtualnej
Istnieje wiele rzeczy, które mogą uniemożliwić aplikacji dotarcie do określonego hosta i portu. W większości przypadków jest to jedna z trzech rzeczy:

* **Zapora jest w drodze.** Jeśli masz zapory w drodze, zostanie przesuń limit czasu TCP. Limit czasu TCP wynosi 21 sekund w tym przypadku. Użyj narzędzia **tcpping,** aby przetestować łączność. Limity czasu TCP mogą być spowodowane wieloma rzeczami poza zaporami, ale zaczynają się tam. 
* **Usługa DNS nie jest dostępna.** Limit czasu DNS wynosi trzy sekundy na serwer DNS. Jeśli masz dwa serwery DNS, limit czasu wynosi 6 sekund. Użyj nameresolver, aby sprawdzić, czy system DNS działa. Pamiętaj, że nie można użyć nslookup, ponieważ nie używa dns sieci wirtualnej jest skonfigurowany. Jeśli jest niedostępny, może istnieć zapora lub sieciowej sieciowej blokowania dostępu do SYSTEMU DNS lub może być w dół.

Jeśli te elementy nie odpowiadają na Twoje problemy, poszukaj najpierw takich rzeczy, jak: 

**regionalna integracja sieci wirtualnej**
* czy miejsce docelowe jest adresem spoza RFC1918 i nie masz WEBSITE_VNET_ROUTE_ALL ustawionego na 1?
* czy istnieje wyjście blokujące nsg z podsieci integracji?
* jeśli będzie przez ExpressRoute lub VPN, jest brama lokalna skonfigurowana do kierowania ruchu z powrotem do platformy Azure? Jeśli można dotrzeć do punktów końcowych w sieci wirtualnej, ale nie lokalnie, sprawdź trasy.
* czy masz wystarczająco dużo uprawnień, aby ustawić delegowanie w podsieci integracji? Podczas konfiguracji integracji regionalnej sieci wirtualnej podsieć integracji zostanie delegowana do witryny Microsoft.Web. Interfejs użytkownika integracji sieci wirtualnej automatycznie przedeleguje podsieci do witryny Microsoft.Web. Jeśli twoje konto nie ma wystarczających uprawnień sieciowych do ustawiania delegowania, potrzebny będzie ktoś, kto może ustawić atrybuty w podsieci integracji, aby delegować podsieć. Aby ręcznie delegować podsieć integracji, przejdź do interfejsu użytkownika podsieci sieci wirtualnej platformy Azure i ustaw delegowanie dla witryny Microsoft.Web. 

**wymagana integracja sieci wirtualnej bramy**
* to zakres adresów typu "punkt-lokacja" w zakresach RFC 1918 (10,0,0,0-10,255 255,255 / 172,16,10.0-172.31.255.255 / 192.168.0.0-192.168.255.255)?
* Czy brama jest pokazywany jako w portalu? Jeśli brama jest wyłączna, przyjmij ją z powrotem.
* Czy certyfikaty są zsynchronizowane, czy podejrzewasz, że konfiguracja sieci została zmieniona?  Jeśli certyfikaty są niezsynchronizowane lub podejrzewasz, że nastąpiła zmiana w konfiguracji sieci wirtualnej, która nie została zsynchronizowana z asps, a następnie naciśnij "Sieć synchronizacji".
* Jeśli przechodząc przez sieć VPN, jest brama lokalna skonfigurowana do kierowania ruchu z powrotem do platformy Azure? Jeśli można dotrzeć do punktów końcowych w sieci wirtualnej, ale nie lokalnie, sprawdź trasy.
* Czy próbujesz użyć bramy współistnienia, która obsługuje zarówno punkt do witryny, jak i usługi ExpressRoute? Bramy współistnienia nie są obsługiwane za pomocą integracji sieci wirtualnej.

Debugowanie problemów z siecią jest wyzwaniem, ponieważ nie można zobaczyć, co blokuje dostęp do określonej kombinacji host:port. Niektóre z przyczyn obejmują:

* Masz zaporę na hoście uniemożliwiając dostęp do portu aplikacji z zakresu adresów IP punktu do witryny. Przekraczanie podsieci często wymaga dostępu publicznego.
* Twój docelowy host jest w dół.
* Aplikacja jest w dół.
* Miałeś niewłaściwy adres IP lub nazwa hosta.
* Aplikacja nasłuchuje na innym porcie niż oczekiwano. Identyfikator procesu można dopasować do portu nasłuchiwania przy użyciu "netstat -aon" na hoście punktu końcowego. 
* Sieciowe grupy zabezpieczeń są skonfigurowane w taki sposób, że uniemożliwiają dostęp do hosta i portu aplikacji z zakresu adresów IP punktu do lokacji.

Pamiętaj, że nie wiesz, jaki adres będzie faktycznie używany przez aplikację. Może to być dowolny adres w podsieci integracji lub zakres adresów typu punkt-lokacja, więc musisz zezwolić na dostęp z całego zakresu adresów. 

Dodatkowe kroki debugowania obejmują:

* Połącz się z maszyną wirtualną w sieci wirtualnej i spróbuj dotrzeć do hosta zasobu:port stamtąd. Aby przetestować dostęp TCP, użyj polecenia **test-netconnection**polecenia programu PowerShell . Składnia jest następująca:

      test-netconnection hostname [optional: -Port]

* Przywołanie aplikacji na maszynie Wirtualnej i przetestowanie dostępu do tego hosta i portu z konsoli z aplikacji przy użyciu **tcppingu**

#### <a name="on-premises-resources"></a>Zasoby lokalne ####

Jeśli aplikacja nie może dotrzeć do zasobu lokalnie, sprawdź, czy można dotrzeć do zasobu z sieci wirtualnej. Użyj polecenia **test-netconnection** PowerShell, aby sprawdzić, czy dostęp TCP. Jeśli maszyna wirtualna nie może dotrzeć do zasobu lokalnego, połączenie sieci VPN lub usługi ExpressRoute może nie być poprawnie skonfigurowane.

Jeśli twoja wirtualna hostowana maszyna wirtualna może dotrzeć do systemu lokalnego, ale aplikacja nie może, przyczyną jest prawdopodobnie jeden z następujących powodów:

* Trasy nie są skonfigurowane z zakresami adresów podsieci ani zakresów adresów lokacji w bramie lokalnej.
* Sieciowe grupy zabezpieczeń blokują dostęp do zakresu adresów IP typu punkt-lokacja.
* Zapory lokalne blokują ruch z zakresu adresów IP typu punkt-lokacja.
* Próbujesz osiągnąć adres nie-RFC 1918 przy użyciu funkcji integracji regionalnej sieci wirtualnej.