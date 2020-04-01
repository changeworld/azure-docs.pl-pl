---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: 652d42d6e2d9e909c3a03bd82a3a36f91bc73807
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419548"
---
Funkcja jest łatwa w konfiguracji, ale to nie znaczy, że twoje doświadczenie będzie bezproblemowe. Jeśli wystąpią problemy z dostępem do żądanego punktu końcowego, istnieją pewne narzędzia, których można użyć do testowania łączności z konsoli aplikacji. Istnieją dwie konsole, których można użyć. Jednym z nich jest konsola Kudu, a druga jest konsolą w witrynie Azure portal. Aby dotrzeć do konsoli Kudu z aplikacji, przejdź do **narzędzia** > **Kudu**. Można również dotrzeć do konsoli Kudo w [sitename].scm.azurewebsites.net. Po załadowaniu witryny sieci Web przejdź do karty **Konsoli debugowania.** Aby uzyskać dane z aplikacji do konsoli hostowanego przez witrynę Azure portal, przejdź do**konsoli** **narzędzi** > .

#### <a name="tools"></a>narzędzia
Narzędzia **ping**, **nslookup**i **tracert** nie będą działać przez konsolę z powodu ograniczeń zabezpieczeń. Aby wypełnić pustkę, dodawane są dwa oddzielne narzędzia. Aby przetestować funkcjonalność DNS, dodaliśmy narzędzie o nazwie **nameresolver.exe**. Składnia jest następująca:

    nameresolver.exe hostname [optional: DNS Server]

Za pomocą nameresolver można sprawdzić nazwy hostów, od których zależy aplikacja. W ten sposób można sprawdzić, czy masz coś źle skonfigurowane z DNS lub być może nie mają dostępu do serwera DNS. Serwer DNS używany przez aplikację w konsoli można wyświetlić, patrząc na zmienne środowiskowe, WEBSITE_DNS_SERVER i WEBSITE_DNS_ALT_SERVER.

Za pomocą następnego narzędzia można przetestować łączność TCP z kombinacją hosta i portu. To narzędzie jest nazywane **tcpping** i składni jest:

    tcpping.exe hostname [optional: port]

Narzędzie **tcpping** informuje, czy można dotrzeć do określonego hosta i portu. Może pokazać sukces tylko wtedy, gdy istnieje aplikacja nasłuchiwania w kombinacji hosta i portu i istnieje dostęp do sieci z aplikacji do określonego hosta i portu.

#### <a name="debug-access-to-virtual-network-hosted-resources"></a>Debugowanie dostępu do zasobów hostowanych w sieci wirtualnej
Wiele rzeczy może uniemożliwić aplikacji dotarcie do określonego hosta i portu. W większości przypadków jest to jedna z następujących rzeczy:

* **Zapora jest w drodze.** Jeśli masz zapory w drodze, naciśnij limit czasu TCP. Limit czasu TCP wynosi 21 sekund w tym przypadku. Użyj narzędzia **tcpping,** aby przetestować łączność. Limity czasu TCP mogą być spowodowane przez wiele rzeczy poza zaporami, ale zaczynają się tam.
* **Usługa DNS nie jest dostępna.** Limit czasu DNS wynosi 3 sekundy na serwer DNS. Jeśli masz dwa serwery DNS, limit czasu wynosi 6 sekund. Użyj nameresolver, aby sprawdzić, czy system DNS działa. Nie można użyć nslookup, ponieważ nie używa systemu DNS, za pomocą którego skonfigurowano sieć wirtualną. Jeśli jest niedostępny, może istnieć zapora lub sieciowej sieciowej blokowania dostępu do SYSTEMU DNS lub może być w dół.

Jeśli te elementy nie odpowiadają na Twoje problemy, poszukaj najpierw takich rzeczy, jak:

**Regionalna integracja sieci wirtualnej**
* Czy miejsce docelowe jest adresem spoza RFC1918 i nie masz WEBSITE_VNET_ROUTE_ALL ustawionej na 1?
* Czy istnieje wyjście z sieciowej sieciowej blokujące wyjście z podsieci integracji?
* Jeśli wybierasz się przez usługę Azure ExpressRoute lub sieć VPN, czy twoja brama lokalna jest skonfigurowana do kierowania ruchu z powrotem na platformę Azure? Jeśli można dotrzeć do punktów końcowych w sieci wirtualnej, ale nie lokalnie, sprawdź trasy.
* Czy masz wystarczająco dużo uprawnień, aby ustawić delegowanie w podsieci integracji? Podczas konfiguracji integracji regionalnej sieci wirtualnej podsieć integracji jest delegowana do witryny Microsoft.Web. Interfejs użytkownika integracji sieci wirtualnej automatycznie deleguje podsieć do witryny Microsoft.Web. Jeśli Twoje konto nie ma wystarczających uprawnień sieciowych do ustawiania delegowania, potrzebujesz kogoś, kto może ustawić atrybuty w podsieci integracji, aby delegować podsieć. Aby ręcznie delegować podsieć integracji, przejdź do interfejsu użytkownika podsieci sieci wirtualnej platformy Azure i ustaw delegowanie dla witryny Microsoft.Web.

**Integracja sieci wirtualnej wymaganej przez bramę**
* Jest zakresem adresów typu "punkt-lokacja" w zakresach RFC 1918 (10,0,0,0-10,255 255,255 / 172,16,10.0-172.31.255.255 / 192.168.0.0-192.168.255.255)?
* Czy brama jest pokazywany jako w portalu? Jeśli brama jest wyłączna, przyjmij ją z powrotem.
* Czy certyfikaty są wyświetlane jako zsynchronizowane, czy podejrzewasz, że konfiguracja sieci została zmieniona?  Jeśli certyfikaty są niezsynchronizowane lub podejrzewasz, że w konfiguracji sieci wirtualnej dokonano zmiany, która nie została zsynchronizowana z asp, wybierz **opcję Synchronizuj sieć**.
* Jeśli wybierasz się przez sieć VPN, czy brama lokalna jest skonfigurowana do kierowania ruchu z powrotem na platformę Azure? Jeśli można dotrzeć do punktów końcowych w sieci wirtualnej, ale nie lokalnie, sprawdź trasy.
* Czy próbujesz użyć bramy współistnienia, która obsługuje zarówno punkt do witryny, jak i usługi ExpressRoute? Bramy współistnienia nie są obsługiwane za pomocą integracji sieci wirtualnej.

Debugowanie problemów z siecią jest wyzwaniem, ponieważ nie można zobaczyć, co blokuje dostęp do określonej kombinacji host:port. Niektóre przyczyny obejmują:

* Masz zaporę na hoście, która uniemożliwia dostęp do portu aplikacji z zakresu adresów IP typu punkt-lokacja. Przekraczanie podsieci często wymaga publicznego dostępu.
* Twój docelowy host jest w dół.
* Aplikacja jest w dół.
* Miałeś niewłaściwy adres IP lub nazwa hosta.
* Aplikacja nasłuchuje na innym porcie niż oczekiwano. Identyfikator procesu można dopasować do portu nasłuchiwania przy użyciu "netstat -aon" na hoście punktu końcowego.
* Sieciowe grupy zabezpieczeń są skonfigurowane w taki sposób, aby uniemożliwić dostęp do hosta i portu aplikacji z zakresu adresów IP typu punkt-lokacja.

Nie wiesz, jaki adres aplikacja faktycznie używa. Może to być dowolny adres w podsieci integracji lub zakres adresów typu punkt-lokacja, więc musisz zezwolić na dostęp z całego zakresu adresów.

Dodatkowe kroki debugowania obejmują:

* Połącz się z maszyną wirtualną w sieci wirtualnej i spróbuj dotrzeć do hosta zasobów:port stamtąd. Aby przetestować dostęp TCP, użyj polecenia **test-netconnection**polecenia programu PowerShell . Składnia jest następująca:

      test-netconnection hostname [optional: -Port]

* Przywołanie aplikacji na maszynie wirtualnej i przetestowanie dostępu do tego hosta i portu z konsoli z aplikacji przy użyciu **tcpping .**

#### <a name="on-premises-resources"></a>Zasoby lokalne ####

Jeśli aplikacja nie może dotrzeć do zasobu lokalnie, sprawdź, czy możesz dotrzeć do zasobu z sieci wirtualnej. Użyj polecenia **test-netconnection** PowerShell, aby sprawdzić, czy dostęp TCP. Jeśli maszyna wirtualna nie może dotrzeć do zasobu lokalnego, połączenie sieci VPN lub usługi ExpressRoute może nie być poprawnie skonfigurowane.

Jeśli maszyna wirtualna hostowana w sieci wirtualnej może dotrzeć do systemu lokalnego, ale aplikacja nie może, przyczyną jest prawdopodobnie jeden z następujących powodów:

* Trasy nie są skonfigurowane z zakresami adresów podsieci ani zakresów adresów lokacji w bramie lokalnej.
* Sieciowe grupy zabezpieczeń blokują dostęp do zakresu adresów IP typu punkt-lokacja.
* Zapory lokalne blokują ruch z zakresu adresów IP typu punkt-lokacja.
* Próbujesz osiągnąć adres nie-RFC 1918 przy użyciu funkcji integracji regionalnej sieci wirtualnej.