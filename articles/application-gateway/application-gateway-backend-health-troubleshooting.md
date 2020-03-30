---
title: Rozwiązywanie problemów z kondycją zaplecza w usłudze Azure Application Gateway
description: W tym artykule opisano sposób rozwiązywania problemów z kondycją wewnętrznej bazy danych dla bramy aplikacji platformy Azure
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: article
ms.date: 08/30/2019
ms.author: surmb
ms.openlocfilehash: 71e1f8be2af5556d86996175e8a1ddbccc9c7de1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72001667"
---
<a name="troubleshoot-backend-health-issues-in-application-gateway"></a>Rozwiązywanie problemów ze zdrowiem zaplecza w bramie aplikacji
==================================================

<a name="overview"></a>Omówienie
--------

Domyślnie usługa Azure Application Gateway sonduje serwery wewnętrznej bazy danych, aby sprawdzić ich stan kondycji i sprawdzić, czy są one gotowe do obsługi żądań. Użytkownicy mogą również tworzyć niestandardowe sondy, aby wspomnieć o nazwie hosta, ścieżce do sondowania i kodach stanu, które mają być zaakceptowane jako w dobrej kondycji. W każdym przypadku, jeśli serwer wewnętrznej bazy danych nie odpowiada pomyślnie, brama aplikacji oznacza serwer jako złej kondycji i zatrzymuje przekazywanie żądań do serwera. Po pomyślnym rozpoczęciu odpowiadania przez serwer brama aplikacji wznawia przekazywanie żądań.

### <a name="how-to-check-backend-health"></a>Jak sprawdzić kondycję zaplecza

Aby sprawdzić kondycję puli wewnętrznej bazy danych, można użyć health **wewnętrznej** strony w witrynie Azure portal. Można też użyć [interfejsu Api programu Azure PowerShell,](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhealth?view=azps-2.6.0) [CLI](https://docs.microsoft.com/cli/azure/network/application-gateway?view=azure-cli-latest#az-network-application-gateway-show-backend-health)lub [REST](https://docs.microsoft.com/rest/api/application-gateway/applicationgateways/backendhealth).

Stan pobrany przez dowolną z tych metod może być jedną z następujących czynności:

- W dobrej kondycji

- W niedobrej kondycji

- Nieznane

Jeśli stan kondycji wewnętrznej bazy danych serwera jest w dobrej kondycji, oznacza to, że brama aplikacji przekaże żądania do tego serwera. Ale jeśli kondycja wewnętrznej bazy danych dla wszystkich serwerów w puli wewnętrznej bazy danych jest w złej kondycji lub nieznany, mogą wystąpić problemy podczas próby uzyskania dostępu do aplikacji. W tym artykule opisano symptomy, przyczynę i rozwiązanie dla każdego z wyświetlanych błędów.

<a name="backend-health-status-unhealthy"></a>Stan kondycji zaplecza: w złej kondycji
-------------------------------

Jeśli stan kondycji wewnętrznej bazy danych jest w złej kondycji, widok portalu będzie podobny do następującego zrzutu ekranu:

![Kondycja zaplecza bramy aplikacji — w złej kondycji](./media/application-gateway-backend-health-troubleshooting/appgwunhealthy.png)

Lub jeśli używasz kwerendy interfejsu API usługi Azure PowerShell, cli lub azure rest, otrzymasz odpowiedź podobną do następującej:
```azurepowershell
PS C:\Users\testuser\> Get-AzApplicationGatewayBackendHealth -Name "appgw1" -ResourceGroupName "rgOne"
BackendAddressPools :
{Microsoft.Azure.Commands.Network.Models.PSApplicationGatewayBackendHealthPool}
BackendAddressPoolsText : [
{
                              "BackendAddressPool": {
                                "Id": "/subscriptions/536d30b8-665b-40fc-bd7e-68c65f816365/resourceGroups/rgOne/providers/Microsoft.Network/applicationGateways/appgw1/b
                          ackendAddressPools/appGatewayBackendPool"
                              },
                              "BackendHttpSettingsCollection": [
                                {
                                  "BackendHttpSettings": {
                                    "TrustedRootCertificates": [],
                                    "Id": "/subscriptions/536d30b8-665b-40fc-bd7e-68c65f816365/resourceGroups/rgOne/providers/Microsoft.Network/applicationGateways/appg
                          w1/backendHttpSettingsCollection/appGatewayBackendHttpSettings"
                                  },
                                  "Servers": [
                                    {
                                      "Address": "10.0.0.5",
                                      "Health": "Healthy"
                                    },
                                    {
                                      "Address": "10.0.0.6",
                                      "Health": "Unhealthy"
                                    }
                                  ]
                                }
                              ]
                            }
                        ]
```
Po otrzymaniu stanu serwera wewnętrznej bazy danych w złej kondycji dla wszystkich serwerów w puli wewnętrznej bazy danych żądania nie są przekazywane do serwerów, a brama aplikacji zwraca błąd "502 Bad Gateway" do żądającego klienta. Aby rozwiązać ten problem, sprawdź **szczegóły** kolumny na **karcie Kondycja wewnętrznej bazy danych.**

Komunikat wyświetlany w kolumnie **Szczegóły** zawiera bardziej szczegółowe informacje na temat problemu i na ich podstawie można rozpocząć rozwiązywanie problemu.

> [!NOTE]
> Domyślne żądanie sondy jest \<wysyłane\>w formacie protokołu ://127.0.0.1:\<port\>/. Na przykład http://127.0.0.1:80 dla sondy http na porcie 80. Tylko kody stanu HTTP od 200 do 399 są uważane za zdrowe. Protokół i port docelowy są dziedziczone z ustawień HTTP. Jeśli chcesz, aby brama aplikacji sondować na inny protokół, nazwę hosta lub ścieżkę i rozpoznać inny kod stanu jako w dobrej kondycji, skonfiguruj niestandardową sondę i skojarz ją z ustawieniami HTTP.

<a name="error-messages"></a>Komunikaty o błędach
------------------------
#### <a name="backend-server-timeout"></a>Limit czasu serwera wewnętrznej bazy danych

**Komunikat:** Czas wykonany przez wewnętrznej bazy danych, aby odpowiedzieć na sondę kondycji bramy\'aplikacji s jest więcej niż próg limitu czasu w ustawieniu sondy.

**Przyczyna:** Po wysłaniu żądania sondy HTTP(S) do serwera wewnętrznej bazy danych, czeka na odpowiedź z serwera wewnętrznej bazy danych dla skonfigurowanego okresu. Jeśli serwer wewnętrznej bazy danych nie odpowiada w skonfigurowanym okresie (wartość limitu czasu), jest oznaczony jako w złej kondycji, dopóki nie zacznie odpowiadać w skonfigurowanym okresie limitu czasu ponownie.

**Rozdzielczość:** Sprawdź, dlaczego serwer lub aplikacja wewnętrznej bazy danych nie odpowiada w skonfigurowanym okresie limitu czasu, a także sprawdź zależności aplikacji. Na przykład sprawdź, czy baza danych ma żadnych problemów, które mogą wywołać opóźnienie w odpowiedzi. Jeśli znasz zachowanie aplikacji i powinna ona odpowiadać tylko po przesuwu czasu, zwiększ wartość limitu czasu z niestandardowych ustawień sondy. Aby zmienić wartość limitu czasu, musi istnieć niestandardowa sonda. Informacje na temat konfigurowania sondy niestandardowej [można znaleźć na stronie dokumentacji](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal).

Aby zwiększyć wartość limitu czasu, wykonaj następujące kroki:

1.  Dostęp bezpośrednio do serwera wewnętrznej bazy danych i sprawdź czas, przez który serwer musi odpowiedzieć na tej stronie. Za pomocą dowolnego narzędzia można uzyskać dostęp do serwera wewnętrznej bazy danych, w tym do przeglądarki za pomocą narzędzi programistycznych.

1.  Po zorientowali się, czas przeznaczony dla aplikacji, aby odpowiedzieć, wybierz **sondy kondycji** kartę, a następnie wybierz sondę, która jest skojarzona z ustawieniami HTTP.

1.  Wprowadź dowolną wartość limitu czasu, która jest większa niż czas odpowiedzi aplikacji w sekundach.

1.  Zapisz niestandardowe ustawienia sondy i sprawdź, czy kondycja wewnętrznej bazy danych jest teraz wyświetlana jako w dobrej kondycji.

#### <a name="dns-resolution-error"></a>Błąd rozpoznawania dns

**Komunikat:** Bramy aplikacji nie można utworzyć sondy dla tego zaplecza. Dzieje się tak zazwyczaj wtedy, gdy nazwa FQDN zaplecza nie została wprowadzona prawidłowo. 

**Przyczyna:** Jeśli pula zaplecza jest typu Adres IP/FQDN lub Usługa aplikacji, brama aplikacji jest rozpoznawana na adres IP nazwy FQDN wprowadzonej za pośrednictwem systemu nazw domen (DNS) (domyślnie niestandardowego lub domyślnego standardu Azure) i próbuje połączyć się z serwerem na porcie TCP wymienionym w ustawieniach HTTP. Ale jeśli ten komunikat jest wyświetlany, sugeruje, że brama aplikacji nie może pomyślnie rozpoznać adres IP wprowadzonej sieci FQDN.

**Rozdzielczość:**

1.  Sprawdź, czy nazwa FQDN wprowadzona w puli wewnętrznej bazy danych jest poprawna i czy jest to domena publiczna, a następnie spróbuj rozwiązać ją z komputera lokalnego.

1.  Jeśli można rozpoznać adres IP, może być coś nie tak z konfiguracją DNS w sieci wirtualnej.

1.  Sprawdź, czy sieć wirtualna jest skonfigurowana z niestandardowym serwerem DNS. Jeśli tak jest, sprawdź serwer DNS, dlaczego nie może rozpoznać adresu IP określonej nazwy FQDN.

1.  Jeśli używasz domyślnego dns platformy Azure, skontaktuj się z rejestratorem nazw domen, czy prawidłowe mapowanie rekordu lub CNAME zostało ukończone.

1.  Jeśli domena jest prywatna lub wewnętrzna, spróbuj ją rozwiązać z maszyny Wirtualnej w tej samej sieci wirtualnej. Jeśli możesz go rozwiązać, uruchom ponownie bramę aplikacji i sprawdź ponownie. Aby ponownie uruchomić bramę aplikacji, należy [zatrzymać](https://docs.microsoft.com/powershell/module/azurerm.network/stop-azurermapplicationgateway?view=azurermps-6.13.0) i [rozpocząć](https://docs.microsoft.com/powershell/module/azurerm.network/start-azurermapplicationgateway?view=azurermps-6.13.0) przy użyciu poleceń programu PowerShell opisanych w tych połączonych zasobach.

#### <a name="tcp-connect-error"></a>Błąd połączenia TCP

**Komunikat:** Brama aplikacji nie może połączyć się z zapleczem.
Sprawdź, czy wewnętrznej bazy danych reaguje na porcie używanym do sondy.
Sprawdź również, czy jakiekolwiek NSG /UDR/Firewall blokuje dostęp do ip i portu tego zaplecza

**Przyczyna:** Po zakończeniu fazy rozpoznawania dns brama aplikacji próbuje połączyć się z serwerem wewnętrznej bazy danych na porcie TCP skonfigurowanym w ustawieniach HTTP. Jeśli brama aplikacji nie może ustanowić sesji TCP na określonym porcie, sonda jest oznaczona jako w złej kondycji za pomocą tego komunikatu.

**Rozwiązanie:** Jeśli ten błąd zostanie wyświetlony, wykonaj następujące czynności:

1.  Sprawdź, czy można połączyć się z serwerem wewnętrznej bazy danych na porcie wymienionym w ustawieniach HTTP przy użyciu przeglądarki lub programu PowerShell. Na przykład uruchom następujące polecenie:`Test-NetConnection -ComputerName
    www.bing.com -Port 443`

1.  Jeśli wspomniany port nie jest żądanym portem, wprowadź poprawny numer portu dla bramy aplikacji, aby połączyć się z serwerem wewnętrznej bazy danych

1.  Jeśli nie możesz połączyć się na porcie z komputera lokalnego, a następnie:

    a.  Sprawdź ustawienia sieciowej grupy zabezpieczeń karty sieciowej i podsieci serwera wewnętrznej bazy danych oraz czy połączenia przychodzące ze skonfigurowanym portem są dozwolone. Jeśli tak nie jest, utwórz nową regułę zezwalania na połączenia. Aby dowiedzieć się, jak tworzyć reguły sieciowej sieciowej sieciowej, [zobacz stronę dokumentacji](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic#create-security-rules).

    b.  Sprawdź, czy ustawienia sieciowej sieciowej podsieci bramy aplikacji zezwalają na ruch publiczny i prywatny wychodzący, aby można było nawiązać połączenie. Sprawdź stronę dokumentu, która jest dostępna w kroku 3a, aby dowiedzieć się więcej o tworzeniu reguł sieciowej tożsamości z siecią roboczą.
    ```azurepowershell
            $vnet = Get-AzVirtualNetwork -Name "vnetName" -ResourceGroupName "rgName"
            Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
    ```

    d.  Sprawdź ustawienia tras zdefiniowanych przez użytkownika (UDR) bramy aplikacji i podsieci serwera wewnętrznej bazy danych pod kątem wszelkich anomalii routingu. Upewnij się, że UDR nie kieruje ruchu z podsieci wewnętrznej bazy danych. Na przykład sprawdź trasy do sieciowych urządzeń wirtualnych lub tras domyślnych anonsowanych do podsieci bramy aplikacji za pośrednictwem usługi Azure ExpressRoute i/lub sieci VPN.

    d.  Aby sprawdzić efektywne trasy i reguły dla karty sieciowej, można użyć następujących poleceń programu PowerShell:
    ```azurepowershell
            Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
            Get-AzEffectiveRouteTable -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
    ```
1.  Jeśli nie znajdziesz żadnych problemów z siecią NSG lub UDR, sprawdź serwer wewnętrznej bazy danych pod kątem problemów związanych z aplikacjami, które uniemożliwiają klientom ustanowienie sesji TCP na skonfigurowanych portach. Kilka rzeczy do sprawdzenia:

    a.  Otwórz wiersz polecenia (Win+R -\> `netstat`cmd), wprowadź i wybierz enter.

    b.  Sprawdź, czy serwer nasłuchuje na porcie, który jest skonfigurowany. Przykład:
    ```
            Proto Local Address Foreign Address State PID
            TCP 0.0.0.0:80 0.0.0.0:0 LISTENING 4
    ```
    d.  Jeśli nie nasłuchuje na skonfigurowanym porcie, sprawdź ustawienia serwera www. Na przykład: powiązania lokacji w usługach IIS, blok serwera w NGINX i host wirtualny w Apache.

    d.  Sprawdź ustawienia zapory systemu operacyjnego, aby upewnić się, że ruch przychodzący do portu jest dozwolony.

#### <a name="http-status-code-mismatch"></a>Niezgodność kodu stanu HTTP

**Komunikat:** Kod stanu odpowiedzi\'HTTP wewnętrznej bazy danych nie był zgodny z ustawieniem sondy. Oczekiwano:{HTTPStatusCode0} Odebrane:{HTTPStatusCode1}.

**Przyczyna:** Po nawiązaniu połączenia TCP i wykonaniu uzgadniania SSL (jeśli protokół SSL jest włączony), brama aplikacji wyśle sondę jako żądanie HTTP GET do serwera wewnętrznej bazy danych. Jak opisano wcześniej, domyślną \<sondą\>będzie protokół ://127.0.0.1:\<port\>/, i uznaje kody stanu odpowiedzi w rage 200 do 399 jako zdrowy. Jeśli serwer zwraca inny kod stanu, zostanie oznaczony jako w złej kondycji z tej wiadomości.

**Rozwiązanie:** W zależności od kodu odpowiedzi serwera wewnętrznej bazy danych można wykonać następujące kroki. Kilka wspólnych kodów stanu są wymienione tutaj:

| **Błąd** | **Akcje** |
| --- | --- |
| Niezgodność kodu stanu sondy: Odebrano 401 | Sprawdź, czy serwer wewnętrznej bazy danych wymaga uwierzytelniania. Sondy bramy aplikacji nie mogą przekazywać poświadczeń do uwierzytelniania w tym momencie. Zezwalaj \"na http 401\" w kodzie stanu sondy lub sondować ścieżkę, w której serwer nie wymaga uwierzytelniania. | |
| Niezgodność kodu stanu sondy: Odebrano 403 | Dostęp zabroniony. Sprawdź, czy dostęp do ścieżki jest dozwolony na serwerze wewnętrznej bazy danych. | |
| Niezgodność kodu stanu sondy: Odebrano 404 | Nie znaleziono strony. Sprawdź, czy ścieżka nazwy hosta jest dostępna na serwerze wewnętrznej bazy danych. Zmień nazwę hosta lub parametr ścieżki na wartość dostępną. | |
| Niezgodność kodu stanu sondy: Odebrano 405 | Żądania sondy dla bramy aplikacji używają metody HTTP GET. Sprawdź, czy serwer zezwala na tę metodę. | |
| Niezgodność kodu stanu sondy: Odebrano 500 | Wewnętrzny błąd serwera. Sprawdź kondycję serwera zaplecza i sprawdź, czy usługi są uruchomione. | |
| Niezgodność kodu stanu sondy: Odebrano 503 | Usługa niedostępna. Sprawdź kondycję serwera zaplecza i sprawdź, czy usługi są uruchomione. | |

Lub jeśli uważasz, że odpowiedź jest prawidłowa i chcesz, aby brama aplikacji akceptowała inne kody stanu jako w dobrej kondycji, możesz utworzyć niestandardową sondę. Takie podejście jest przydatne w sytuacjach, gdy witryna sieci Web wewnętrznej bazy danych wymaga uwierzytelniania. Ponieważ żądania sondy nie zawierają żadnych poświadczeń użytkownika, nie powiodą się one, a kod stanu HTTP 401 zostanie zwrócony przez serwer wewnętrznej bazy danych.

Aby utworzyć sondę niestandardową, wykonaj [następujące kroki](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal).

#### <a name="http-response-body-mismatch"></a>Niezgodność treści odpowiedzi HTTP

**Komunikat:** Treść odpowiedzi HTTP\'wewnętrznej bazy danych nie była zgodna z ustawieniem sondy. Treść odebranej odpowiedzi nie zawiera {string}.

**Przyczyna:** Podczas tworzenia sondy niestandardowej, masz możliwość oznaczenia serwera wewnętrznej bazy danych jako w dobrej kondycji przez dopasowanie ciągu z treści odpowiedzi. Na przykład można skonfigurować bramę aplikacji, aby akceptowała "nieautoryzowane" jako ciąg do dopasowania. Jeśli odpowiedź serwera wewnętrznej bazy danych dla żądania sondy zawiera **nieautoryzowany**ciąg, zostanie oznaczona jako W dobrej kondycji. W przeciwnym razie zostanie oznaczona jako w złej kondycji z tej wiadomości.

**Rozwiązanie:** Aby rozwiązać ten problem, wykonaj następujące kroki:

1.  Dostęp do serwera wewnętrznej bazy danych lokalnie lub z komputera klienckiego na ścieżce sondy i sprawdź treść odpowiedzi.

1.  Sprawdź, czy treść odpowiedzi w konfiguracji sondy niestandardowej bramy aplikacji jest zgodna z konfiguracją skonfigurowanych.

1.  Jeśli nie są zgodne, zmień konfigurację sondy, tak aby ma poprawną wartość ciągu do zaakceptowania.

Dowiedz się więcej o [dopasowywaniu sondy bramy aplikacji](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching).

#### <a name="backend-server-certificate-invalid-ca"></a>Nieprawidłowy urząd certyfikacji certyfikatu serwera wewnętrznej bazy danych

**Komunikat:** Certyfikat serwera używany przez zaplecze nie jest podpisany przez dobrze znany urząd certyfikacji (CA). Umieszczaj na białej liście wewnętrznej bazy danych w bramie aplikacji, przekazując certyfikat główny certyfikatu serwera używanego przez wewnętrznej bazy danych.

**Przyczyna:** Kompleksowy ssl z bramą aplikacji w wersji 2 wymaga weryfikacji certyfikatu serwera wewnętrznej bazy danych w celu uznania serwera za zdrowy.
Aby certyfikat SSL był zaufany, ten certyfikat serwera wewnętrznej bazy danych musi być wystawiony przez urząd certyfikacji, który znajduje się w zaufanym magazynie bramy aplikacji. Jeśli certyfikat nie został wystawiony przez zaufany urząd certyfikacji (na przykład, jeśli użyto certyfikatu z podpisem własnym), użytkownicy powinni przekazać certyfikat wystawcy do bramy aplikacji.

**Rozwiązanie:** Wykonaj następujące kroki, aby wyeksportować i przekazać zaufany certyfikat główny do bramy aplikacji. (Te kroki są przeznaczone dla klientów systemu Windows).

1.  Zaloguj się na komputerze, na którym znajduje się aplikacja.

1.  Wybierz pozycję Win+R lub kliknij prawym przyciskiem myszy przycisk **Start,** a następnie wybierz polecenie **Uruchom**.

1.  Wprowadź `certmgr.msc` i wybierz pozycję Wprowadź. Menedżera certyfikatów można również wyszukać w menu **Start.**

1.  Znajdź certyfikat, zazwyczaj `\Certificates - Current User\\Personal\\Certificates\`w , i otwórz go.

1.  Wybierz certyfikat główny, a następnie wybierz pozycję **Wyświetl certyfikat**.

1.  We właściwościach certyfikatu wybierz kartę **Szczegóły.**

1.  Na karcie **Szczegóły** wybierz opcję **Kopiuj do pliku** i zapisz plik w kodowanym kodowanym x.509 base-64 (. CER).

1.  Otwórz stronę **Ustawienia** HTTP bramy aplikacji w witrynie Azure portal.

1. Otwórz ustawienia HTTP, wybierz pozycję **Dodaj certyfikat**i znajdź właśnie zapisany plik certyfikatu.

1. Wybierz **pozycję Zapisz,** aby zapisać ustawienia HTTP.

Alternatywnie można wyeksportować certyfikat główny z komputera klienckiego, uzyskując bezpośredni dostęp do serwera (pomijając bramę aplikacji) za pośrednictwem przeglądarki i eksportując certyfikat główny z przeglądarki.

Aby uzyskać więcej informacji na temat wyodrębniania i przekazywania zaufanych certyfikatów głównych w bramie aplikacji, zobacz [Eksportowanie zaufanego certyfikatu głównego (dla jednostki SKU w wersji 2).](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku)

#### <a name="trusted-root-certificate-mismatch"></a>Niedopasowanie certyfikatów głównych zaufanych

**Komunikat:** Certyfikat główny certyfikatu serwera używanego przez wewnętrznej bazy danych nie jest zgodny z zaufanym certyfikatem głównym dodanym do bramy aplikacji. Upewnij się, że dodasz poprawny certyfikat główny, aby dodać do białej listy wewnętrznej bazy danych

**Przyczyna:** Kompleksowy ssl z bramą aplikacji w wersji 2 wymaga weryfikacji certyfikatu serwera wewnętrznej bazy danych w celu uznania serwera za zdrowy.
Aby certyfikat SSL był zaufany, certyfikat serwera wewnętrznej bazy danych musi być wystawiony przez urząd certyfikacji, który znajduje się w zaufanym magazynie bramy aplikacji. Jeśli certyfikat nie został wystawiony przez zaufany urząd certyfikacji (na przykład użyto certyfikatu z podpisem własnym), użytkownicy powinni przekazać certyfikat wystawcy do bramy aplikacji.

Certyfikat przekazany do ustawień HTTP bramy aplikacji musi być zgodny z certyfikatem głównym certyfikatu serwera wewnętrznej bazy danych.

**Rozwiązanie:** Jeśli zostanie wyświetlony ten komunikat o błędzie, istnieje niezgodność między certyfikatem, który został przekazany do bramy aplikacji, a certyfikatem, który został przekazany do serwera wewnętrznej bazy danych.

Wykonaj kroki 1-11 w powyższej metodzie, aby przekazać poprawny zaufany certyfikat główny do bramy aplikacji.

Aby uzyskać więcej informacji na temat wyodrębniania i przekazywania zaufanych certyfikatów głównych w bramie aplikacji, zobacz [Eksportowanie zaufanego certyfikatu głównego (dla jednostki SKU w wersji 2).](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku)
> [!NOTE]
> Ten błąd może również wystąpić, jeśli serwer wewnętrznej bazy danych nie wymienia pełnego łańcucha certyfikatu, w tym głównego > pośredniego (jeśli dotyczy) > Liść podczas uzgadniania TLS. Aby sprawdzić, można użyć poleceń OpenSSL z dowolnego klienta i połączyć się z serwerem wewnętrznej bazy danych przy użyciu skonfigurowanych ustawień w sondzie bramy aplikacji.

Przykład:
```
OpenSSL> s_client -connect 10.0.0.4:443 -servername www.example.com -showcerts
```
Jeśli dane wyjściowe nie pokazują pełnego łańcucha zwracanego certyfikatu, wyeksportuj certyfikat ponownie z pełnym łańcuchem, w tym certyfikatem głównym. Skonfiguruj ten certyfikat na serwerze wewnętrznej bazy danych. 

```
  CONNECTED(00000188)\
  depth=0 OU = Domain Control Validated, CN = \*.example.com\
  verify error:num=20:unable to get local issuer certificate\
  verify return:1\
  depth=0 OU = Domain Control Validated, CN = \*.example.com\
  verify error:num=21:unable to verify the first certificate\
  verify return:1\
  \-\-\-\
  Certificate chain\
   0 s:/OU=Domain Control Validated/CN=*.example.com\
     i:/C=US/ST=Arizona/L=Scottsdale/O=GoDaddy.com, Inc./OU=http://certs.godaddy.com/repository//CN=Go Daddy Secure Certificate Authority - G2\
  \-----BEGIN CERTIFICATE-----\
  xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx\
  \-----END CERTIFICATE-----
```

#### <a name="backend-certificate-invalid-common-name-cn"></a>Nieprawidłowa nazwa pospolita certyfikatu wewnętrznej bazy danych (CN)

**Komunikat:** Nazwa pospolita (CN) certyfikatu wewnętrznej bazy danych nie jest zgodna z nagłówkiem hosta sondy.

**Przyczyna:** Brama aplikacji sprawdza, czy nazwa hosta określona w ustawieniach HTTP wewnętrznej bazy danych jest zgodna z nazwą cn przedstawioną przez certyfikat SSL serwera wewnętrznej bazy danych. Jest to zachowanie Standard_v2 i WAF_v2 jednostki SKU. Standardowe i WAF SKU's Server Name Indication (SNI) jest ustawiona jako FQDN w adresie puli wewnętrznej bazy danych.

W jednostce SKU w wersji 2, jeśli istnieje domyślna sonda (nie skonfigurowano i nie skojarzono niestandardowej sondy), SNI zostanie ustawiona na podstawie nazwy hosta wymienionej w ustawieniach HTTP. Lub jeśli "Wybierz nazwę hosta z adresu wewnętrznej bazy danych" jest wymieniony w ustawieniach HTTP, gdzie pula adresów wewnętrznej bazy danych zawiera prawidłową nazwę FQDN, to ustawienie zostanie zastosowane.

Jeśli istnieje niestandardowa sonda skojarzona z ustawieniami HTTP, SNI zostanie ustawiona na podstawie nazwy hosta wymienionej w konfiguracji sondy niestandardowej. Lub jeśli **wybierz nazwę hosta z wewnętrznej bazy danych ustawienia HTTP** jest zaznaczone w sondzie niestandardowej, SNI zostanie ustawiona na podstawie nazwy hosta wymienionej w ustawieniach HTTP.

Jeśli **wybierz adres hosta z adresu wewnętrznej bazy danych** jest ustawiona w ustawieniach HTTP, pula adresów wewnętrznej bazy danych musi zawierać prawidłową numer FQDN.

Jeśli zostanie wyświetlony ten komunikat o błędzie, cn certyfikatu wewnętrznej bazy danych nie jest zgodny z nazwą hosta skonfigurowaną w sondzie niestandardowej lub ustawieniami HTTP (jeśli zaznaczona jest opcja **Wybierz nazwę hosta z ustawień HTTP wewnętrznej bazy danych).** Jeśli używasz sondy domyślnej, nazwa hosta zostanie ustawiona na **127.0.0.1**. Jeśli nie jest to żądana wartość, należy utworzyć niestandardową sondę i skojarzyć ją z ustawieniami HTTP.

**Rozwiązanie:**

Aby rozwiązać ten problem, wykonaj poniższe czynności.

W przypadku systemu Windows:

1.  Zaloguj się na komputerze, na którym znajduje się aplikacja.

1.  Wybierz pozycję Win+R lub kliknij prawym przyciskiem myszy przycisk **Start** i wybierz polecenie **Uruchom**.

1.  Wprowadź **plik certmgr.msc** i wybierz pozycję Enter. Menedżera certyfikatów można również wyszukać w menu **Start.**

1.  Znajdź certyfikat (zazwyczaj `\Certificates - Current User\\Personal\\Certificates`w) i otwórz certyfikat.

1.  Na karcie **Szczegóły** sprawdź **temat**certyfikatu .

1.  Sprawdź CN certyfikatu na podstawie szczegółów i wprowadź to samo w polu nazwy hosta sondy niestandardowej lub w ustawieniach HTTP (jeśli wybrano **pozycję Wybierz nazwę hosta z ustawień HTTP wewnętrznej bazy danych).** Jeśli nie jest to żądana nazwa hosta dla witryny sieci Web, musisz uzyskać certyfikat dla tej domeny lub wprowadzić poprawną nazwę hosta w niestandardowej konfiguracji sondy lub ustawienia HTTP.

Dla Linuksa za pomocą OpenSSL:

1.  Uruchom to polecenie w openssl:
    ```
    openssl x509 -in certificate.crt -text -noout
    ```

2.  Na wyświetlonych właściwościach znajdź cn certyfikatu i wprowadź to samo w polu nazwy hosta w ustawieniach http. Jeśli nie jest to żądana nazwa hosta dla witryny sieci Web, musisz uzyskać certyfikat dla tej domeny lub wprowadzić poprawną nazwę hosta w niestandardowej konfiguracji sondy lub ustawienia HTTP.

#### <a name="backend-certificate-is-invalid"></a>Certyfikat wewnętrznej bazy danych jest nieprawidłowy

**Komunikat:** Certyfikat wewnętrznej bazy danych jest nieprawidłowy. Bieżąca data nie \"mieści\" \"się\" w zakresie Ważny od i Ważny do daty na certyfikacie.

**Przyczyna:** Każdy certyfikat jest wyposażony w zakres ważności, a połączenie HTTPS nie będzie bezpieczne, chyba że certyfikat SSL serwera jest ważny. Bieżące dane muszą znajdować się w **zakresie prawidłowym od** i **prawidłowym do** zakresu. Jeśli tak nie jest, certyfikat jest uważany za nieprawidłowy, a to spowoduje utworzenie problemu zabezpieczeń, w którym brama aplikacji oznacza serwer wewnętrznej bazy danych jako w złej kondycji.

**Rozwiązanie:** Jeśli certyfikat SSL wygasł, odnowi certyfikat u dostawcy i zaktualizuj ustawienia serwera o nowy certyfikat. Jeśli jest to certyfikat z podpisem własnym, należy wygenerować prawidłowy certyfikat i przekazać certyfikat główny do ustawień HTTP bramy aplikacji. W tym celu wykonaj następujące kroki:

1.  Otwórz ustawienia HTTP bramy aplikacji w portalu.

1.  Wybierz ustawienie, które ma wygasły certyfikat, wybierz pozycję **Dodaj certyfikat**i otwórz nowy plik certyfikatu.

1.  Usuń stary certyfikat za pomocą ikony **Usuń** obok certyfikatu, a następnie wybierz pozycję **Zapisz**.

#### <a name="certificate-verification-failed"></a>Weryfikacja certyfikatu nie powiodła się

**Komunikat:** Nie można zweryfikować ważności certyfikatu wewnętrznej bazy danych. Aby znaleźć przyczynę, sprawdź otwórz diagnostykę SSL dla wiadomości skojarzonej z kodem błędu {errorCode}

**Przyczyna:** Ten błąd występuje, gdy brama aplikacji nie może zweryfikować ważności certyfikatu.

**Rozwiązanie:** Aby rozwiązać ten problem, sprawdź, czy certyfikat na serwerze został poprawnie utworzony. Na przykład można użyć [OpenSSL,](https://www.openssl.org/docs/man1.0.2/man1/verify.html) aby zweryfikować certyfikat i jego właściwości, a następnie spróbować ponownie załadować certyfikat do ustawień HTTP bramy aplikacji.

<a name="backend-health-status-unknown"></a>Stan kondycji zaplecza: nieznany
-------------------------------
Jeśli kondycja wewnętrznej bazy danych jest wyświetlana jako Nieznany, widok portalu będzie podobny do następującego zrzutu ekranu:

![Kondycja zaplecza bramy aplikacji — nieznany](./media/application-gateway-backend-health-troubleshooting/appgwunknown.png)

To zachowanie może wystąpić z co najmniej jednego z następujących powodów:

1.  Grupa sieciowa sieciowej w podsieci bramy aplikacji blokuje dostęp przychodzący do portów 65503-65534 (jednostka SKU w wersji 1) lub 65200-65535 (sku w wersji 2) z "Internetu".
1.  UDR w podsieci bramy aplikacji jest ustawiona na trasę domyślną (0.0.0.0/0), a następny przeskok nie jest określony jako "Internet".
1.  Trasa domyślna jest anonsowana przez połączenie Usługi ExpressRoute/VPN z siecią wirtualną za pośrednictwem protokołu BGP.
1.  Niestandardowy serwer DNS jest skonfigurowany w sieci wirtualnej, która nie może rozpoznać nazw domen publicznych.
1.  Brama aplikacji jest w stanie niezdrowej kondycji.

**Rozwiązanie:**

1.  Sprawdź, czy sieciowe sieciowe sieciowe blokuje dostęp do portów 65503-65534 (v1 SKU) lub 65200-65535 (v2 SKU) z **Internetu:**

    a.  Na karcie **Omówienie** bramy aplikacji wybierz **łącze Sieć wirtualna/Podsieć.**

    b.  Na karcie **Podsieci** sieci wirtualnej wybierz podsieć, w której wdrożono bramę aplikacji.

    d.  Sprawdź, czy skonfigurowano dowolną szkoliwę nsg.

    d.  Jeśli skonfigurowana jest grupa sieciowej grupy, wyszukaj ten zasób sieciowej grupy płciowych na karcie **Wyszukiwanie** lub w obszarze **Wszystkie zasoby**.

    e.  W sekcji **Reguły przychodzące** dodaj regułę przychodzącą, aby zezwolić na zakres portów docelowych 65503-65534 dla jednostki SKU w wersji 1 lub jednostki SKU w wersji 65200-65535 v2 z **zestawem źródło** jako **dowolny** lub **Internet**.

    f.  Wybierz **pozycję Zapisz** i sprawdź, czy można wyświetlić wewnętrznej bazy danych jako w dobrej kondycji. Alternatywnie można to zrobić za pośrednictwem [programu PowerShell/CLI](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

1.  Sprawdź, czy twój UDR ma trasę domyślną (0.0.0.0/0) z następnym przeskokiem nie ustawionym jako **Internet:**
    
    a.  Wykonaj kroki 1a i 1b, aby określić podsieć.

    b.  Sprawdź, czy nie skonfigurowano żadnych UDR. Jeśli tak, wyszukaj zasób na pasku wyszukiwania lub w obszarze **Wszystkie zasoby**.

    d.  Sprawdź, czy istnieją jakieś trasy domyślne (0.0.0.0/0) z następnym przeskokiem nie ustawionym jako **Internet**. Jeśli ustawieniem jest **urządzenie wirtualne** lub **brama sieci wirtualnej,** należy upewnić się, że urządzenie wirtualne lub urządzenie lokalne może prawidłowo przekierować pakiet z powrotem do miejsca docelowego internetu bez modyfikowania pakietu.

    d.  W przeciwnym razie zmień następny przeskok na **Internet**, wybierz pozycję **Zapisz**i sprawdź kondycję wewnętrznej bazy danych.

1.  Domyślna trasa anonsowana przez połączenie Usługi ExpressRoute/VPN z siecią wirtualną za pośrednictwem protokołu BGP:

    a.  Jeśli masz połączenie Usługi ExpressRoute/VPN z siecią wirtualną za pośrednictwem protokołu BGP i reklamujesz trasę domyślną, upewnij się, że pakiet jest kierowany z powrotem do miejsca docelowego Internetu bez modyfikowania go. Można sprawdzić za pomocą opcji **Rozwiązywanie problemów z połączeniem** w portalu bramy aplikacji.

    b.  Wybierz miejsce docelowe ręcznie jako dowolny adres IP z routingiem internetowym, taki jak 1.1.1.1. Ustaw port docelowy jako cokolwiek i sprawdź łączność.

    d.  Jeśli następnym przeskokiem jest brama sieci wirtualnej, może istnieć domyślna trasa anonsowana za pośrednictwem usługi ExpressRoute lub SIECI VPN.

1.  Jeśli w sieci wirtualnej skonfigurowano niestandardowy serwer DNS, sprawdź, czy serwer (lub serwery) może rozpoznać domeny publiczne. Rozpoznawanie nazw domen publicznych może być wymagane w scenariuszach, w których brama aplikacji musi dotrzeć do domen zewnętrznych, takich jak serwery USŁUGI OCSP lub sprawdzić stan odwołania certyfikatu.

1.  Aby sprawdzić, czy brama aplikacji jest w dobrej kondycji i działa, przejdź do opcji **Kondycja zasobów** w portalu i sprawdź, czy stan jest **w dobrej kondycji**. Jeśli widzisz stan **w złej kondycji** lub **stan awaryjny,** [skontaktuj się z pomocą techniczną](https://azure.microsoft.com/support/options/).

<a name="next-steps"></a>Następne kroki
----------

Dowiedz się więcej o [diagnostyce i rejestrowaniu bramy aplikacji](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).
