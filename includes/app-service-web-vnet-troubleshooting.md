---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: 2d2a82552a846cedfa5da3bb6ec6df8a40b67732
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671488"
---
Chociaż ta funkcja jest łatwa do skonfigurowania, nie oznacza to, że Twoje środowisko nie będzie miało problemu. Jeśli wystąpią problemy z uzyskaniem dostępu do żądanego punktu końcowego, istnieją pewne narzędzia, których można użyć do testowania łączności z poziomu konsoli aplikacji. Istnieją dwie konsole, których można użyć. Jedna z nich jest konsolą kudu, a druga jest konsolą w Azure Portal. Aby nawiązać połączenie z konsolą kudu z poziomu aplikacji, przejdź do pozycji narzędzia-> kudu. Możesz również uzyskać dostęp do konsoli Kudo na stronie [sitename]. SCM. azurewebsites. NET. Po załadowaniu witryny sieci Web przejdź do karty konsola debugowania. Aby przejść do Azure Portal hostowanej konsoli, w aplikacji przejdź do pozycji narzędzia — > Konsola. 

#### <a name="tools"></a>Narzędzia
Narzędzia **ping**, **nslookup**i **tracert** nie będą przełączane przez konsolę ze względu na ograniczenia zabezpieczeń. Aby wypełnić wartość void, dodano dwa osobne narzędzia. W celu przetestowania funkcjonalności DNS dodaliśmy narzędzie o nazwie nameresolver. exe. Składnia jest następująca:

    nameresolver.exe hostname [optional: DNS Server]

Możesz użyć **nameresolver** , aby sprawdzić nazwy hostów, od których zależy aplikacja. W ten sposób możesz sprawdzić, czy masz jakieś nieprawidłowo skonfigurowane usługi DNS lub że nie masz dostępu do serwera DNS. Możesz zobaczyć serwer DNS, który będzie używany przez aplikację w konsoli programu, przeglądając zmienne środowiskowe WEBSITE_DNS_SERVER i WEBSITE_DNS_ALT_SERVER.

Następne narzędzie umożliwia przetestowanie łączności TCP z połączeniem hosta i portu. To narzędzie ma nazwę **tcpping** , a Składnia:

    tcpping.exe hostname [optional: port]

Narzędzie **tcpping** informuje o tym, czy można nawiązać połączenie z określonym hostem i portem. Można wyświetlić tylko sukces, jeśli: istnieje aplikacja nasłuchująca na hoście i w kombinacji portów i istnieje dostęp do sieci z aplikacji do określonego hosta i portu.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Debugowanie dostępu do hostowanych zasobów sieci wirtualnej
Istnieje wiele rzeczy, które mogą uniemożliwić aplikacji osiągnięcie określonego hosta i portu. Większość czasu jest jedną z trzech rzeczy:

* **Zapora jest w ten sposób.** Jeśli w ten sposób masz zaporę, zostanie osiągnięty limit czasu protokołu TCP. Limit czasu protokołu TCP wynosi 21 sekund w tym przypadku. Użyj narzędzia **tcpping** , aby przetestować łączność. Limity czasu protokołu TCP mogą być spowodowane wieloma niektórymi zaporami, ale w tym miejscu. 
* **Usługa DNS jest niedostępna.** Limit czasu DNS wynosi trzy sekundy na serwer DNS. Jeśli masz dwa serwery DNS, limit czasu wynosi 6 sekund. Użyj nameresolver, aby sprawdzić, czy usługa DNS działa. Należy pamiętać, że nie można użyć polecenia nslookup, które nie korzysta z serwera DNS skonfigurowanego dla sieci wirtualnej. W przypadku niedostępności może być dostępna zapora lub sieciowej grupy zabezpieczeń blokujące dostęp do systemu DNS lub może nie działać.

Jeśli te elementy nie odpowiadają na problemy, należy najpierw sprawdzić, jak: 

**Integracja z regionalną siecią wirtualną**
* Czy miejscem docelowym jest adres nieRFC1918y, a WEBSITE_VNET_ROUTE_ALL nie ma ustawionej wartości 1?
* Czy istnieje sieciowej grupy zabezpieczeń blokujące ruch wychodzący z podsieci integracji?
* w przypadku przechodzenia między ExpressRoute lub sieci VPN czy brama lokalna jest skonfigurowana do kierowania ruchu z kopii zapasowej na platformę Azure? Jeśli możesz uzyskać dostęp do punktów końcowych w sieci wirtualnej, ale nie w środowisku lokalnym, sprawdź trasy.
* Czy masz wystarczające uprawnienia do ustawiania delegowania w podsieci integracji? Podczas konfiguracji integracji regionalnej sieci wirtualnej Twoja podsieć integracji zostanie przedelegowana do firmy Microsoft. Web. Interfejs użytkownika integracji sieci wirtualnej automatycznie przekaże podsieć do firmy Microsoft. Web. Jeśli Twoje konto nie ma wystarczających uprawnień sieciowych do ustawiania delegowania, będziesz potrzebować kogoś, kto może ustawić atrybuty w podsieci integracji w celu delegowania podsieci. Aby ręcznie delegować podsieć integracji, przejdź do interfejsu użytkownika podsieci usługi Azure Virtual Network i ustaw delegowanie dla Microsoft. Web. 

**Integracja sieci wirtualnej wymagana przez bramę**
* czy zakres adresów punkt-lokacja w zakresach RFC 1918 (10.0.0.0-10.255.255.255/172.16.0.0-172.31.255.255/192.168.0.0-192.168.255.255)?
* Czy brama ma być wyświetlana w portalu? Jeśli Brama nie działa, przenieś ją z powrotem.
* Czy certyfikaty są wyświetlane jako zsynchronizowane lub czy podejrzewasz, że konfiguracja sieci została zmieniona?  Jeśli certyfikaty nie są zsynchronizowane lub podejrzewasz, że wprowadzono zmiany w konfiguracji sieci wirtualnej, które nie zostały zsynchronizowane z nazywani, naciśnij pozycję "Synchronizuj sieć".
* Jeśli przechodzą przez sieć VPN, czy brama lokalna skonfigurowana do kierowania ruchu z kopii zapasowej na platformę Azure? Jeśli możesz uzyskać dostęp do punktów końcowych w sieci wirtualnej, ale nie w środowisku lokalnym, sprawdź trasy.
* Czy próbujesz użyć bramy współistnienia, która obsługuje oba punkty do lokacji i ExpressRoute? Bramy współistnienia nie są obsługiwane z integracją sieci wirtualnej.

Debugowanie problemów z siecią jest wyzwaniem, ponieważ nie można zobaczyć, co blokuje dostęp do określonego hosta: kombinacja portów. Niektóre z tych przyczyn obejmują:

* Na hoście istnieje Zapora uniemożliwiająca dostęp do portu aplikacji z zakresu adresów IP punktu do lokacji. Przekroczenia podsieci często wymagają dostępu publicznego.
* Host docelowy nie działa.
* Aplikacja nie działa.
* Masz nieprawidłowy adres IP lub nazwa hosta.
* Aplikacja nasłuchuje na innym porcie niż oczekiwano. Identyfikator procesu można dopasować do portu nasłuchiwania przy użyciu polecenia "netstat-Aon" na hoście punktu końcowego. 
* Sieciowe grupy zabezpieczeń są konfigurowane w taki sposób, że uniemożliwiają dostęp do hosta i portu aplikacji z zakresu adresów IP lokacji.

Należy pamiętać, że nie wiesz, jaki jest adres używany przez aplikację. Może to być dowolny adres w podsieci integracji lub zakres adresów punktu do lokacji, dlatego należy zezwolić na dostęp z całego zakresu adresów. 

Dodatkowe kroki debugowania obejmują:

* Nawiąż połączenie z maszyną wirtualną w sieci wirtualnej i spróbuj skontaktować się z hostem zasobów w tym miejscu: port. Aby przetestować dostęp do protokołu TCP, użyj polecenia programu PowerShell **test-NetConnection**. Składnia jest następująca:

      test-netconnection hostname [optional: -Port]

* Wywołaj aplikację na maszynie wirtualnej i przetestuj dostęp do tego hosta i portu z konsoli programu z poziomu aplikacji przy użyciu usługi **tcpping**

#### <a name="on-premises-resources"></a>Zasoby lokalne ####

Jeśli aplikacja nie może nawiązać połączenia z lokalnym zasobem, sprawdź, czy możesz uzyskać dostęp do zasobu z sieci wirtualnej. Użyj polecenia **test-NetConnection** PowerShell, aby sprawdzić dostęp do protokołu TCP. Jeśli maszyna wirtualna nie może nawiązać połączenia z zasobem lokalnym, połączenie sieci VPN lub ExpressRoute może być niepoprawnie skonfigurowane.

Jeśli hostowana maszyna wirtualna może nawiązać połączenie z systemem lokalnym, ale aplikacja nie może, przyczyna jest prawdopodobnie jedną z następujących przyczyn:

* Trasy nie są skonfigurowane z podsiecią lub wskazują zakresy adresów lokacji w bramie lokalnej.
* Sieciowe grupy zabezpieczeń blokują dostęp do zakresu adresów IP punkt-lokacja.
* Zapora lokalna blokuje ruch z zakresu adresów IP punkt-lokacja.
* Próbujesz nawiązać połączenie z adresem innym niż RFC 1918 przy użyciu funkcji integracji regionalnej sieci wirtualnej.