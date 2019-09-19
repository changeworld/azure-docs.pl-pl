---
title: Rozwiązywanie problemów z kondycją zaplecza na platformie Azure Application Gateway
description: W tym artykule opisano Rozwiązywanie problemów z kondycją zaplecza dla usługi Azure Application Gateway
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: article
ms.date: 08/30/2019
ms.author: surmb
ms.openlocfilehash: 1fd4e9156e29133b1db4fe9ab9a0825eb1aa3b55
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097577"
---
<a name="troubleshoot-backend-health-issues-in-application-gateway"></a>Rozwiązywanie problemów z kondycją zaplecza w Application Gateway
==================================================

<a name="overview"></a>Omówienie
--------

Application Gateway domyślnie sonduje serwery zaplecza, aby sprawdzić ich stan kondycji i czy są gotowe do obsłużenia żądań. Użytkownicy mogą tworzyć niestandardowe sondy, a także wspominać o nazwie hosta, ścieżce do sondowania oraz kodów stanu, które mają być akceptowane w dobrej kondycji. W obu przypadkach, jeśli serwer zaplecza nie odpowiada prawidłowo, Application Gateway oznaczy serwer jako w złej kondycji i zatrzyma przekazywanie żądania do serwera. Po pomyślnym rozpoczęciu odpowiedzi serwer zostanie wznowiony.

### <a name="how-to-check-backend-health"></a>Sprawdzanie kondycji zaplecza

Aby sprawdzić kondycję puli zaplecza, można użyć strony "kondycja zaplecza" w Azure Portal. Aby uzyskać stan kondycji, można użyć [interfejsu API](https://docs.microsoft.com/rest/api/application-gateway/applicationgateways/backendhealth) [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhealth?view=azps-2.6.0), [CLI](https://docs.microsoft.com/cli/azure/network/application-gateway?view=azure-cli-latest#az-network-application-gateway-show-backend-health)lub Rest. Sprawdź połączony artykuł względem każdej metody, aby uzyskać więcej informacji.

Stan pobrany przez każdą z wymienionych powyżej metod może mieć trzy typy, które są następujące:

1.  W dobrej kondycji

2.  W złej kondycji

3.  Nieznane

Jeśli kondycja zaplecza serwera jest w dobrej kondycji, oznacza to, że Application Gateway przekazują żądania do tego serwera. Ale jeśli kondycja zaplecza dla wszystkich serwerów w puli zaplecza jest zła lub nieznana, podczas uzyskiwania dostępu do aplikacji mogą wystąpić pewne problemy. W tym dokumencie opisano symptom, przyczynę i rozwiązanie poszczególnych błędów, które są wyświetlane, gdy serwery zaplecza mogą być w złej kondycji lub być nieznane.

<a name="backend-health-status-unhealthy"></a>Stan kondycji zaplecza w złej kondycji
-------------------------------

Jeśli kondycja zaplecza jest wyświetlana jako zła kondycja, zobaczysz ją w portalu, jak na poniższym zrzucie ekranu:

![Kondycja zaplecza Application Gateway — zła kondycja](./media/application-gateway-backend-health-troubleshooting/appgwunhealthy.png)

Lub jeśli używasz Azure PowerShell, interfejsu wiersza polecenia lub zapytania interfejsu API REST platformy Azure, zobaczysz odpowiedź podobną do przedstawionej poniżej:
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
Gdy stan serwera wewnętrznej bazy danych zostanie wyświetlony jako zła kondycja dla wszystkich serwerów w puli zaplecza, żądania nie zostaną przekazane do nich i 502 Application Gateway zwróci błąd nieprawidłowej bramy do żądającego klienta. Aby rozwiązać problem, Sprawdź kolumnę szczegóły karty kondycja zaplecza.

Komunikat wyświetlany w kolumnie szczegóły karty kondycja zaplecza zawiera bardziej szczegółowe informacje o problemie i na podstawie tych informacji można rozpocząć rozwiązywanie problemu.

> [!NOTE]
> Domyślne żądanie sondowania jest wysyłane w formacie \<protokołu\>://127.0.0.1:\<port\>/, na przykład <http://127.0.0.1/> w przypadku sondy http na porcie 80 i uwzględnia tylko odpowiedź kodów stanu HTTP 200-399 jako odpowiedź w dobrej kondycji. Protokół i port docelowy są dziedziczone z ustawień HTTP. Jeśli chcesz, aby Application Gateway badanie przy użyciu innego protokołu, nazwy hosta lub ścieżki i zaakceptowania inny kod stanu w dobrej kondycji, skonfiguruj sondę niestandardową i skojarz ją z ustawieniami protokołu HTTP.

<a name="error-messages"></a>Komunikaty o błędach
------------------------
#### <a name="backend-server-timeout"></a>Limit czasu serwera wewnętrznej bazy danych

**Komunikat:** Czas reakcji zaplecza na sondę kondycji bramy\'aplikacji jest większy niż próg limitu czasu w ustawieniu sondy.

**Może** Gdy Application Gateway wysyła żądanie sondy HTTP (S) do serwera wewnętrznej bazy danych, czeka na odpowiedź z serwera wewnętrznej bazy danych przez określony czas. Jeśli serwer wewnętrznej bazy danych nie odpowiada w wartości limitu czasu, zostanie oznaczony jako niezdrowy do momentu, aż zacznie reagować w czasie.

**Tłumaczenia** Sprawdź serwer zaplecza lub aplikację, tak aby nie odpowiadały w skonfigurowanym limicie czasu, i sprawdź także zależności aplikacji, na przykład jeśli baza danych ma jakiekolwiek problemy, co może prowadzić do opóźnień odpowiedzi. Jeśli masz świadomość zachowania aplikacji i powinien on reagować tylko po wartości limitu czasu, zwiększ wartość limitu czasu z ustawień sondy niestandardowej.
Musisz mieć niestandardową sondę, aby zmienić wartość limitu czasu. Jeśli chcesz dowiedzieć się, jak skonfigurować sondę niestandardową, [zapoznaj się z dokumentacją strony](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal).

Aby zwiększyć limit czasu, wykonaj następujące czynności:

1.  Bezpośredni dostęp do serwera wewnętrznej bazy danych i sprawdź czas odpowiedzi serwera na Tę stronę. Możesz użyć dowolnego narzędzia, aby uzyskać dostęp do programu, w tym przeglądarki korzystającej z narzędzi deweloperskich.

2.  Po pobraniu czasu na odpowiedź aplikacji kliknij kartę sondy kondycji i wybierz sondę, która jest skojarzona z ustawieniami protokołu HTTP.

3.  Wprowadź wartość limitu czasu, która jest większa niż czas odpowiedzi aplikacji (w sekundach).

4.  Zapisz ustawienia sondy niestandardowej i Sprawdź kondycję wewnętrznej bazy danych, jeśli jest ona teraz wyświetlana w dobrej kondycji.

#### <a name="dns-resolution-error"></a>Błąd rozpoznawania nazw DNS

**Komunikat:** Brama aplikacji nie może utworzyć sondy dla tego zaplecza. Dzieje się tak zazwyczaj, gdy nazwa FQDN zaplecza nie została prawidłowo wprowadzona. 

**Może** Jeśli pula zaplecza jest typu adresu IP/FQDN lub App Service, Application Gateway jest rozpoznawana jako adres IP nazwy FQDN wprowadzonej przy użyciu serwera DNS (niestandardowa lub Azure default) i próbuje nawiązać połączenie z serwerem na porcie TCP wymienionym w ustawieniach protokołu HTTP. Ale jeśli ten komunikat jest wyświetlany, sugeruje, że Application Gateway nie mógł pomyślnie rozpoznać adresu IP wprowadzonej nazwy FQDN.

**Tłumaczenia**

1.  Sprawdź, czy nazwa FQDN wprowadzona w puli zaplecza jest poprawna, a jeśli jest domeną publiczną, spróbuj ją rozwiązać z komputera lokalnego.

2.  W przypadku rozpoznania adresu IP może wystąpić problem z konfiguracją DNS w sieci wirtualnej.

3.  Sprawdź, czy sieć wirtualna jest skonfigurowana za pomocą niestandardowego serwera DNS. Jeśli tak jest, sprawdź serwer DNS, dlaczego nie jest w stanie rozpoznać adresu IP danej nazwy FQDN.

4.  Jeśli jest to domyślny system DNS platformy Azure, należy skontaktować się z rejestratorem nazw domen, jeśli zostało wykonane odpowiednie mapowanie rekordów lub rekordu CNAME.

5.  Jeśli domena jest prywatna/wewnętrzna, spróbuj rozwiązać ją z poziomu maszyny wirtualnej w tej samej sieci wirtualnej, a jeśli jest możliwe jej rozwiązanie, uruchom ponownie Application Gateway i sprawdź ponownie. Aby ponownie uruchomić Application Gateway, należy [zatrzymać](https://docs.microsoft.com/powershell/module/azurerm.network/stop-azurermapplicationgateway?view=azurermps-6.13.0) i [zacząć](https://docs.microsoft.com/powershell/module/azurerm.network/start-azurermapplicationgateway?view=azurermps-6.13.0) korzystać z poleceń programu PowerShell podanym w odpowiednich linkach dokumentu.

#### <a name="tcp-connect-error"></a>Błąd połączenia TCP

**Komunikat:** Nie można nawiązać połączenia z zapleczem przez bramę aplikacji.
Sprawdź, czy zaplecze reaguje na port używany dla sondy.
Sprawdź również, czy jakakolwiek sieciowej grupy zabezpieczeń/UDR/zapora blokuje dostęp do adresu IP i portu tego zaplecza

**Może** Po fazie rozpoznawania nazw DNS Application Gateway próbuje nawiązać połączenie z serwerem zaplecza na porcie TCP, który jest skonfigurowany w ustawieniach protokołu HTTP. Jeśli Application Gateway nie może ustanowić sesji TCP na określonym porcie, sonda zostanie oznaczona jako zła w złej kondycji.

**Rozwiązanie:** Jeśli widzisz ten błąd, sprawdź następujące kwestie:

1.  Sprawdź, czy możesz nawiązać połączenie z serwerem zaplecza na porcie wymienionym w ustawieniach protokołu HTTP przy użyciu przeglądarki lub programu PowerShell, na przykład możesz użyć polecenia: Test-NetConnection Connection-ComputerName www.bing.com-port 443

2.  Jeśli wymieniony port nie jest pożądanym portem, wprowadź poprawny numer portu dla Application Gateway, aby nawiązać połączenie z serwerem wewnętrznej bazy danych

3.  Jeśli nie można nawiązać połączenia z portem na komputerze lokalnym, a następnie:

    a.  Sprawdź ustawienia sieciowej grupy zabezpieczeń karty sieciowej i podsieci serwera zaplecza i sprawdź, czy są dozwolone połączenia przychodzące ze skonfigurowanym portem. Jeśli nie są dozwolone, Utwórz nową regułę, aby zezwolić na połączenia. Aby dowiedzieć się, jak utworzyć reguły sieciowej grupy zabezpieczeń, [zapoznaj się z dokumentacją strony](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic#create-security-rules).

    b.  Sprawdź ustawienia sieciowej grupy zabezpieczeń podsieci Application Gateway, jeśli jest dozwolony publiczny i prywatny ruch wychodzący, aby można było nawiązać połączenie. Sprawdź dokument połączony z (a), aby dowiedzieć się więcej o sposobie tworzenia reguł sieciowej grupy zabezpieczeń.
    ```azurepowershell
            $vnet = Get-AzVirtualNetwork -Name "vnetName" -ResourceGroupName "rgName"
            Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
    ```

    c.  Sprawdź ustawienia UDR w podsieci Application Gateway i serwera zaplecza dla wszelkich anomalii routingu. Upewnij się, że UDR nie kieruje ruchu z podsieci zaplecza. Na przykład sprawdź trasy do sieciowych urządzeń wirtualnych lub trasy domyślne anonsowane do podsieci bramy aplikacji za pośrednictwem usługi ExpressRoute/VPN.

    d.  Aby sprawdzić obowiązujące trasy i reguły dla karty sieciowej, można użyć następujących poleceń programu PowerShell.
    ```azurepowershell
            Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
            Get-AzEffectiveRouteTable -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
    ```
4.  Jeśli nie występują żadne problemy z usługą sieciowej grupy zabezpieczeń lub UDR, sprawdź, czy serwer zaplecza zawiera problemy związane z aplikacją, z powodu tego, którzy klienci nie mogą ustanowić sesji TCP dla skonfigurowanych portów. Kilka kwestii do sprawdzenia:

    e.  Otwórz wiersz polecenia (Win + R-\> cmd) i wprowadź netstat i naciśnij klawisz ENTER

    f.  Sprawdź, czy serwer nasłuchuje na skonfigurowanym porcie.
        Przykład:
    ```
            Proto Local Address Foreign Address State PID
            TCP 0.0.0.0:80 0.0.0.0:0 LISTENING 4
    ```
    g.  Jeśli tak nie jest, sprawdź ustawienia serwera sieci Web, na przykład powiązania witryny w usługach IIS, blok serwera w NGINX i hosta wirtualnego na platformie Apache

    h.  Sprawdź ustawienia zapory systemu operacyjnego, aby upewnić się, że ruch przychodzący do portu jest dozwolony

#### <a name="http-status-code-mismatch"></a>Niezgodność kodu stanu HTTP

**Komunikat:** Kod stanu odpowiedzi HTTP zaplecza\'nie jest zgodny z ustawieniem sondy. Oczekiwano: {HTTPStatusCode0} odebrano: {HTTPStatusCode1}.

**Może** Po nawiązaniu połączenia TCP i ukończeniu uzgadniania protokołu SSL (jeśli jest włączony protokół SSL), Application Gateway wyśle sondę jako żądanie HTTP GET do serwera wewnętrznej bazy danych. Jak wspomniano powyżej \<, domyślną sondą będzie protokół\>://127.0.0.1:\<port\>/i traktuje kody stanu odpowiedzi w próg 200-399 jako dobra kondycja. Jeśli serwer zwróci inny kod stanu, zostanie oznaczony jako nieprawidłowy dla tej wiadomości.

**Rozwiązanie:** W zależności od kodu odpowiedzi serwera wewnętrznej bazy danych można wykonać następujące czynności. Poniżej przedstawiono kilka typowych kodów stanu:

| **Error** | **Akcje** |
| --- | --- |
| Niezgodność kodu stanu sondy: Odebrane 401 | Sprawdź, czy serwer zaplecza wymaga uwierzytelniania. Sondy usługi Application Gateway nie mogą przekazywać poświadczeń do uwierzytelniania w tym momencie. Zezwalaj na \"użycie protokołu\" HTTP 401 w kodzie stanu sondy lub sondy do ścieżki, w której serwer nie wymaga uwierzytelniania. | |
| Niezgodność kodu stanu sondy: Odebrane 403 | Dostęp zabroniony. Sprawdź, czy dostęp do ścieżki jest dozwolony na serwerze zaplecza. | |
| Niezgodność kodu stanu sondy: Odebrane 404 | Nie znaleziono strony. Sprawdź ścieżkę nazwy hosta, jeśli jest ona dostępna na serwerze zaplecza. Zmień nazwę hosta lub ścieżkę na dostępną wartość. | |
| Niezgodność kodu stanu sondy: Odebrane 405 | Żądania sondowania Application Gateway korzystają z metody HTTP GET. Sprawdź, czy serwer zezwala na to. | |
| Niezgodność kodu stanu sondy: Odebrane 500 | Wewnętrzny błąd serwera. Sprawdź kondycję serwera wewnętrznej bazy danych i czy usługi są uruchomione. | |
| Niezgodność kodu stanu sondy: Odebrane 503 | Usługa jest niedostępna. Sprawdź kondycję serwera wewnętrznej bazy danych i czy usługi są uruchomione. | |

Lub, jeśli uważasz, że odpowiedź jest legit i chcesz, aby Application Gateway akceptowały inne kody stanu jako dobra kondycja, możesz utworzyć niestandardową sondę. Modyfikowanie tego elementu będzie przydatne w sytuacjach, gdy witryna sieci Web zaplecza wymaga uwierzytelnienia, a ponieważ żądania sondowania nie zawierają żadnych poświadczeń użytkownika, nie powiodą się i kod stanu HTTP 401 zostanie zwrócony przez serwer wewnętrznej bazy danych.

Aby utworzyć niestandardową sondę, wykonaj kroki opisane w [tym miejscu](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal).

#### <a name="http-response-body-mismatch"></a>Niezgodność treści odpowiedzi HTTP

**Komunikat:** Treść odpowiedzi HTTP zaplecza\'jest niezgodna z ustawieniem sondy. Odebrana treść odpowiedzi nie zawiera ciągu {String}.

**Może** Podczas tworzenia niestandardowej sondy można oznaczyć serwer zaplecza w dobrej kondycji, dopasowując ciąg z treści odpowiedzi. Można na przykład skonfigurować Application Gateway tak, aby akceptował "nieautoryzowane" jako ciąg do dopasowania. Jeśli odpowiedź serwera wewnętrznej bazy danych dla żądania sondowania zawiera ciąg "nieautoryzowane", zostanie oznaczony jako w dobrej kondycji.
W przeciwnym razie zostanie ona oznaczona jako w złej kondycji za pomocą tej wiadomości.

**Rozwiązanie:** Ten problem można rozwiązać, wykonując poniższe kroki:

1.  Uzyskaj dostęp do serwera wewnętrznej bazy danych lokalnie lub z komputera klienckiego w ścieżce sondy i sprawdź treść odpowiedzi.

2.  Sprawdź, Application Gateway konfiguracja sondy niestandardowej, aby sprawdzić, czy treść odpowiedzi jest zgodna ze skonfigurowanymi informacjami.

3.  Jeśli nie są zgodne, Zmień konfigurację sondy o poprawną wartość ciągu, która ma zostać zaakceptowana.

Więcej informacji na temat Application Gateway sondowania można znaleźć [tutaj](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching).

#### <a name="backend-server-certificate-invalid-ca"></a>Nieprawidłowy urząd certyfikacji dla certyfikatu serwera wewnętrznej bazy danych

**Komunikat:** Certyfikat serwera używany przez zaplecze nie jest podpisany przez dobrze znany urząd certyfikacji (CA). Dozwolonych zaplecza na bramie aplikacji, przekazując certyfikat główny certyfikatu serwera używanego przez zaplecze.

**Może** Kompleksowy protokół SSL z Application Gateway v2 wymaga zweryfikowania certyfikatu serwera wewnętrznej bazy danych, aby uważać serwer w dobrej kondycji.
Aby certyfikat SSL był zaufany, certyfikat serwera wewnętrznej bazy danych musi zostać wystawiony przez urząd certyfikacji, który znajduje się w zaufanym magazynie Application Gateway. Jeśli certyfikat nie został wystawiony przez zaufany urząd certyfikacji, na przykład certyfikaty z podpisem własnym, użytkownicy powinni przekazać certyfikat wystawcy do Application Gateway.

**Rozwiązanie:** Wykonaj poniższe kroki, aby wyeksportować i przekazać zaufany certyfikat główny do Application Gateway (kroki podane poniżej dotyczą klientów z systemem Windows).

1.  Zaloguj się na komputerze, na którym jest hostowana aplikacja

2.  Otwórz polecenie Uruchom, naciskając pozycję Win + R lub klikając prawym przyciskiem myszy przycisk Start i wybierając polecenie Uruchom

3.  Wprowadź certmgr. msc i naciśnij klawisz ENTER. Możesz również wyszukać Menedżera certyfikatów w menu Start.

4.  Znajdź certyfikat, zazwyczaj w obszarze \'Certyfikaty — osobiste\\certyfikaty\\\'bieżącego użytkownika, a następnie otwórz certyfikat

5.  We właściwościach certyfikatu wybierz kartę Ścieżka certyfikacji

6.  Wybierz certyfikat główny i wybierz opcję "Wyświetl certyfikat"

7.  We właściwościach certyfikatu przejdź do karty szczegóły

8.  Na karcie Szczegóły wybierz opcję "Kopiuj do pliku" i Zapisz plik w formacie X. 509 z kodowaniem Base-64 (. CER)

9.  Po zapisaniu Otwórz stronę Ustawienia protokołu HTTP Application Gateway w Azure Portal

10. Otwórz ustawienia protokołu HTTP i kliknij pozycję "Dodaj certyfikat" i Znajdź ostatnio zapisany plik certyfikatu

11. Kliknij przycisk Zapisz, aby zapisać ustawienia HTTP

Alternatywnie można wyeksportować certyfikat główny z komputera klienckiego, uzyskując bezpośrednio dostęp do serwera (pomijając Application Gateway) przy użyciu przeglądarki i eksportując certyfikat główny z przeglądarki.

Aby uzyskać szczegółowe instrukcje dotyczące wyodrębniania i przekazywania zaufanych certyfikatów głównych w Application Gateway, zobacz [tutaj](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku).

#### <a name="trusted-root-certificate-mismatch"></a>Niezgodność zaufanych certyfikatów głównych

**Komunikat:** Certyfikat główny certyfikatu serwera używanego w zapleczu nie jest zgodny z zaufanym certyfikatem głównym dodanym do bramy aplikacji. Upewnij się, że dodano prawidłowy certyfikat główny, aby dozwolonych zaplecza

**Może** Kompleksowy protokół SSL z Application Gateway v2 wymaga zweryfikowania certyfikatu serwera wewnętrznej bazy danych, aby uważać serwer w dobrej kondycji.
Aby certyfikat SSL był zaufany, certyfikat serwera wewnętrznej bazy danych musi zostać wystawiony przez urząd certyfikacji, który znajduje się w zaufanym magazynie Application Gateway. Jeśli certyfikat nie został wystawiony przez zaufany urząd certyfikacji, na przykład certyfikaty z podpisem własnym, użytkownicy powinni przekazać certyfikat wystawcy do Application Gateway.

Certyfikat przekazany do Application Gateway ustawień HTTP musi być zgodny z certyfikatem głównym certyfikatu serwera wewnętrznej bazy danych.

**Rozwiązanie:** Jeśli ten komunikat o błędzie zostanie wyświetlony powyżej, oznacza to, że występuje niezgodność między certyfikatem, który został przekazany do Application Gateway i przekazanym do serwera wewnętrznej bazy danych.

Wykonaj kroki 1-11 wymienione powyżej, aby przekazać do Application Gateway właściwy zaufany certyfikat główny.

Aby uzyskać szczegółowe instrukcje dotyczące wyodrębniania i przekazywania zaufanych certyfikatów głównych w Application Gateway, zobacz [tutaj](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku).
> [!NOTE]
> Ten błąd może również wystąpić, jeśli serwer wewnętrznej bazy danych nie wymienia kompletnego łańcucha certyfikatu, w tym pośredniego > głównej (jeśli dotyczy) — > liścia podczas uzgadniania TLS. Aby sprawdzić, można użyć poleceń OpenSSL z dowolnego klienta i nawiązać połączenie z serwerem wewnętrznej bazy danych przy użyciu skonfigurowanych ustawień sondy Application Gateway.

Na przykład
```
OpenSSL> s_client -connect 10.0.0.4:443 -servername www.example.com -showcerts
```
Jeśli dane wyjściowe nie zawierają pełnego łańcucha zwracanego certyfikatu, wyeksportuj certyfikat ponownie z kompletnym łańcuchem zawierającym certyfikat główny i skonfiguruj go na serwerze wewnętrznej bazy danych. 

POŁĄCZONO (00000188) \
głębokość = 0 OU = zweryfikowano kontrolę domeny, CN \*=. example.com \
błąd weryfikacji: numer = 20: nie można pobrać certyfikatu lokalnego wystawcy \
Weryfikuj zwrot: 1 \
głębokość = 0 OU = zweryfikowano kontrolę domeny, CN \*=. example.com \
Sprawdź błąd: numer = 21: nie można zweryfikować pierwszego certyfikatu \
Weryfikuj zwrot: 1 \
\-\-\-\
Łańcuch certyfikatów \
 0 s:/OU = zweryfikowano kontrolę domeny/CN = *. przykład. com \
   i:/C = US/St = Arizona/L = Scottsdale/O = GoDaddy. com, Inc./OU =http://certs.godaddy.com/repository//CN=Go Daddy Secure Certificate Authority-G2 \
\-----ROZPOCZNIJ CERTYFIKAT-----\
xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx\
\-----KOŃCOWY CERTYFIKAT-----

#### <a name="backend-certificate-invalid-common-name-cn"></a>Nieprawidłowa nazwa pospolita certyfikatu zaplecza (CN)

**Komunikat:** Nazwa pospolita (CN) certyfikatu wewnętrznej bazy danych nie pasuje do nagłówka hosta sondy.

**Może** Application Gateway sprawdza, czy nazwa hosta określona w ustawieniu protokołu HTTP zaplecza pasuje do nazwy pospolitej (CN) przedstawionej przez certyfikat SSL serwera wewnętrznej bazy danych. Jest to tylko zachowanie jednostki SKU Standard_v2 i WAF_v2. SNI jednostki SKU w warstwie Standardowa i WAF są ustawiane jako nazwa FQDN w adresie puli zaplecza.

W jednostce SKU v2, jeśli istnieje domyślna sonda (nie skonfigurowano żadnej niestandardowej sondy/skojarzonej), SNI zostanie ustawiona z nazwy hosta wymienionej w ustawieniach HTTP lub jeśli "Wybierz nazwę hosta z adresu zaplecza" jest wymieniony w ustawieniach HTTP, gdzie Pula adresów zaplecza zawiera prawidłowa nazwa FQDN.

Jeśli istnieje niestandardowa sonda skojarzona z ustawieniami protokołu HTTP, SNI zostanie ustawiona z nazwy hosta wymienionej w niestandardowej konfiguracji sondy lub jeśli opcja "Wybierz nazwę hosta z ustawień protokołu HTTP zaplecza" jest zaznaczona w niestandardowej sondy, zostanie ustawiona z nazwy hosta wymienionego w protokole HTTP Ustawienia.

W przypadku wybrania opcji "Wybierz nazwę hosta z adresu zaplecza" w ustawieniach protokołu HTTP Pula adresów zaplecza musi zawierać prawidłową nazwę FQDN.

Jeśli zobaczysz wyżej wymieniony komunikat o błędzie, oznacza to, że nazwa pospolita (CN) certyfikatu wewnętrznej bazy danych nie jest zgodna z nazwą hosta skonfigurowaną w sondie niestandardowej lub w ustawieniach protokołu HTTP (w przypadku wybrania opcji "Wybierz nazwę hosta z ustawień protokołu HTTP zaplecza"). Jeśli używasz sondy domyślnej, nazwa hosta zostanie ustawiona jako "127.0.0.1". Jeśli ta wartość nie jest wymagana, należy utworzyć niestandardową sondę i skojarzyć ją z ustawieniami protokołu HTTP.

**Rozwiązanie:**

Aby rozwiązać ten problem, wykonaj następujące czynności:

W przypadku systemu Windows:

1.  Zaloguj się na komputerze, na którym jest hostowana aplikacja

2.  Otwórz polecenie Uruchom, naciskając pozycję Win + R lub klikając prawym przyciskiem myszy przycisk Start i wybierając polecenie Uruchom

3.  Wprowadź certmgr. msc i naciśnij klawisz ENTER. Możesz również wyszukać Menedżera certyfikatów w menu Start.

4.  Znajdź certyfikat, zazwyczaj w obszarze \'Certyfikaty — osobiste\\certyfikaty\\\'bieżącego użytkownika, a następnie otwórz certyfikat

5.  Na karcie Szczegóły zapoznaj się z tematem certyfikatu.

6.  Sprawdź nazwę POSPOLITą certyfikatu ze szczegółowych informacji i wprowadź tę samą wartość w polu Nazwa hosta sondy niestandardowej lub ustawienia HTTP (w przypadku wybrania opcji "Wybierz nazwę hosta z ustawień protokołu HTTP zaplecza"). Jeśli nie jest to żądana nazwa hosta dla witryny sieci Web, należy uzyskać certyfikat dla tej domeny lub wprowadzić prawidłową nazwę hosta w konfiguracji sondy niestandardowej/Konfiguracja ustawienia http.

Dla systemu Linux przy użyciu OpenSSL

1.  Uruchom to polecenie w OpenSSL 
```
openssl x509 -in certificate.crt -text -noout
```

2.  Na wyświetlone właściwości Znajdź nazwę POSPOLITą certyfikatu i wprowadź tę samą wartość w polu Nazwa hosta w ustawieniach protokołu HTTP. Jeśli nie jest to żądana nazwa hosta dla witryny sieci Web, należy uzyskać certyfikat dla tej domeny lub wprowadzić prawidłową nazwę hosta w konfiguracji sondy niestandardowej/Konfiguracja ustawienia http.

#### <a name="backend-certificate-is-invalid"></a>Certyfikat zaplecza jest nieprawidłowy

**Komunikat:** Certyfikat zaplecza jest nieprawidłowy. Bieżąca \"Data nie należy do przedziału od prawidłowych\" od\" i \"do zakresu dat dla certyfikatu.

**Może** Każdy certyfikat jest dostarczany z ważnością, a połączenie HTTPS nie będzie bezpieczne, chyba że certyfikat SSL serwera jest prawidłowy.
Bieżące dane muszą znajdować się w przedziale od prawidłowym do zakresu. Jeśli nie, certyfikat będzie uznawany za nieprawidłowy i będzie to problem z zabezpieczeniami. W tym momencie Application Gateway oznaczy serwer wewnętrznej bazy danych jako w złej kondycji.

**Rozwiązanie:** Jeśli certyfikat SSL wygasł, odnów certyfikat u dostawcy i zaktualizuj ustawienia serwera przy użyciu nowego certyfikatu. Jeśli jest to certyfikat z podpisem własnym, należy wygenerować prawidłowy certyfikat i przekazać certyfikat główny do ustawień HTTP Application Gateway. W tym celu wykonaj poniższe kroki:

1.  Otwórz ustawienia protokołu HTTP Application Gateway w portalu

2.  Wybierz ustawienia HTTP, które mają wygasły certyfikat, wybierz pozycję Dodaj certyfikat i Otwórz nowy plik certyfikatu.

3.  Usuń stary certyfikat przy użyciu ikony usuwania obok certyfikatu, a następnie kliknij przycisk Zapisz.

#### <a name="certificate-verification-failed"></a>Weryfikacja certyfikatu nie powiodła się

**Komunikat:** Nie można zweryfikować ważności certyfikatu zaplecza. Aby dowiedzieć się, jak to zrobić, zaznacz opcję Otwórz diagnostykę protokołu SSL dla wiadomości skojarzonej z kodem błędu {errorCode}

**Może** Ten błąd występuje, gdy Application Gateway nie może zweryfikować ważności certyfikatu.

**Rozwiązanie:** Aby rozwiązać ten problem, Sprawdź certyfikat na serwerze, jeśli został on prawidłowo utworzony. Na przykład można użyć [OpenSSL](https://www.openssl.org/docs/man1.0.2/man1/verify.html) do zweryfikowania certyfikatu i jego właściwości, a następnie spróbuj ponownie przekazać certyfikat do ustawień http Application Gateway.

<a name="backend-health-status-unknown"></a>Nieznany stan kondycji zaplecza
-------------------------------
Jeśli kondycja zaplecza jest wyświetlana jako nieznana, będzie wyświetlana w portalu, jak na poniższym zrzucie ekranu:

![Kondycja zaplecza Application Gateway — nieznany](./media/application-gateway-backend-health-troubleshooting/appgwunknown.png)

W przypadku gdy kondycja zaplecza jest pokazana jako nieznana, może to być spowodowane jedną z następujących przyczyn:

1.  SIECIOWEJ grupy zabezpieczeń w podsieci Application Gateway blokuje dostęp przychodzący do portów 65503-65534 (SKU v1) lub 65200-65535 (jednostka SKU v2) z "Internet"
2.  UDR w podsieci Application Gateway z domyślną trasą (0.0.0.0/0) z następnym przeskokiem nie jako Internet
3.  Trasa domyślna anonsowana przez połączenie ExpressRoute/VPN z siecią wirtualną za pośrednictwem protokołu BGP
4.  Niestandardowy serwer DNS jest skonfigurowany w sieci wirtualnej, która nie jest w stanie rozpoznać nazw domen publicznych
5.  Application Gateway jest w złej kondycji

**Rozwiązanie:**

1.  Sprawdź, czy sieciowej grupy zabezpieczeń blokuje dostęp do portów 65503-65534 (SKU v1) lub 65200-65535 (wersja 2 SKU) z "Internet"

    a.  Na karcie Application Gateway "przegląd" Wybierz link Virtual Network/Subnet

    b.  Na karcie podsieci Virtual Network wybierz podsieć, w której został wdrożony Application Gateway

    c.  Sprawdź, czy istnieje sieciowej grupy zabezpieczeń skonfigurowany

    d.  Jeśli istnieje, wyszukaj ten zasób sieciowej grupy zabezpieczeń na karcie wyszukiwanie lub w obszarze wszystkie zasoby

    e.  W sekcji reguły ruchu przychodzącego Dodaj regułę ruchu przychodzącego zezwalającą na zakres portów docelowych 65503-65534 dla jednostki SKU w wersji 1 lub 65200-65535 v2 ze źródłem jako dowolna lub Internet

    f.  Kliknij przycisk Zapisz i sprawdź, czy można pomyślnie wyświetlić zaplecze w dobrej kondycji

    g.  Alternatywnie możesz to zrobić za poorednictwem [programu PowerShell/interfejsu wiersza polecenia](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)

2.  Sprawdź, czy UDR ma trasę domyślną (0.0.0.0/0) z następnym przeskokiem, a nie z Internetem
    
    a.  Wykonaj kroki 1. a i 1. b, aby określić podsieć

    b.  Sprawdź, czy zostały skonfigurowane jakieś UDR. Jeśli istnieje, wyszukaj zasób na pasku wyszukiwania lub w obszarze wszystkie zasoby

    c.  Sprawdź, czy istnieją trasy domyślne (0.0.0.0/0) z następnym przeskokiem nie jako Internet. Jeśli jest to urządzenie wirtualne lub Brama Virtual Network, należy się upewnić, że urządzenie wirtualne lub urządzenie lokalne będzie w stanie prawidłowo skierować pakiet z powrotem do internetowego miejsca docelowego bez modyfikowania pakietu

    d.  W przeciwnym razie zmień następny przeskok na Internet, kliknij przycisk Zapisz i Sprawdź kondycję zaplecza

3.  Trasa domyślna anonsowana przez połączenie ExpressRoute/VPN z siecią wirtualną za pośrednictwem protokołu BGP

    a.  Jeśli masz połączenie ExpressRoute/VPN z siecią wirtualną za pośrednictwem protokołu BGP i w przypadku anonsowania trasy domyślnej, musisz się upewnić, że pakiet jest kierowany z powrotem do lokalizacji docelowej Internetu bez jej modyfikowania

    b.  Możesz sprawdzić za pomocą opcji "Rozwiązywanie problemów z połączeniami" w portalu Application Gateway

    c.  Wybierz lokalizację docelową ręcznie jako dowolny adres IP z obsługą Internetu, taki jak "1.1.1.1" i port docelowy jako cokolwiek i sprawdź łączność.

    d.  Jeśli następnym przeskokiem jest Virtual Network brama, może istnieć trasa domyślna anonsowana za pośrednictwem ExpressRoute lub sieci VPN

4.  Jeśli w sieci wirtualnej jest skonfigurowany niestandardowy serwer DNS, sprawdź, czy serwery mogą rozpoznać domeny publiczne. W scenariuszach, w których Application Gateway musi nawiązać połączenie z domenami zewnętrznymi, takimi jak serwery OCSP lub sprawdzić stan odwołania certyfikatu itp., może być wymagane rozpoznawanie nazw domen publicznych.

5.  Aby sprawdzić, czy Application Gateway jest w dobrej kondycji, przejdź do opcji Resource Health w portalu i sprawdź, czy jest to "dobra kondycja". Jeśli widzisz stan złej kondycji lub obniżonej wydajności, [skontaktuj się z pomocą techniczną](https://azure.microsoft.com/support/options/).

<a name="next-steps"></a>Następne kroki
----------

Aby dowiedzieć się więcej na temat diagnostyki i rejestrowania Application Gateway, zobacz [tutaj](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).
