---
title: Przykład sieci obwodowej na platformie Azure — Tworzenie prostej sieci obwodowej z grup NSG | Dokumentacja firmy Microsoft
description: Tworzenie sieci obwodowej z grup zabezpieczeń sieci (NSG)
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: ''
ms.assetid: f8622b1d-c07d-4ea6-b41c-4ae98d998fff
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jonor
ms.openlocfilehash: 115a459c6a9e4ea96931c89272a49396f0656258
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60362229"
---
# <a name="example-1--build-a-simple-dmz-using-nsgs-with-classic-powershell"></a>Przykład 1 — Tworzenie prostej sieci obwodowej za pomocą grup NSG z klasycznego środowiska PowerShell
[Wróć do strony zabezpieczeń granic najlepsze praktyki][HOME]

> [!div class="op_single_selector"]
> * [Szablon usługi Resource Manager](virtual-networks-dmz-nsg.md)
> * [Klasyczny — PowerShell](virtual-networks-dmz-nsg-asm.md)
> 
>

W tym przykładzie tworzy sieć obwodową pierwotnych z czterech serwerów z systemem Windows i grupy zabezpieczeń sieci. W tym przykładzie przedstawiono listę odpowiednich poleceń programu PowerShell, aby zapewnić lepiej zrozumieć każdego kroku. Jest również sekcji scenariusza ruchu zapewnienie szczegółowe instrukcje, jak ruchu obejmującego warstw zabezpieczeń w sieci obwodowej. Na koniec w odwołaniach sekcja jest kompletny kod i instrukcje umożliwiające tworzenie tego środowiska do testowania i eksperymentowania z różnymi scenariuszami. 

![Przychodząca sieć obwodowa z grupy NSG][1]

## <a name="environment-description"></a>Opis środowiska
W tym przykładzie subskrypcja zawiera następujące zasoby:

* Dwie usługi w chmurze: "FrontEnd001" i "BackEnd001"
* Sieć wirtualną "CorpNetwork" z dwiema podsieciami; "Fronton" i "Wewnętrzna"
* Sieciowa grupa zabezpieczeń, która jest stosowana do obu podsieci
* Windows Server, który reprezentuje serwer aplikacji sieci web ("IIS01")
* Serwery dwa okna, które reprezentują serwery zaplecza aplikacji ("AppVM01", "AppVM02")
* Serwer Windows, który reprezentuje serwer DNS ("DNS01")

W sekcji odwołań istnieje skrypt programu PowerShell, który tworzy większość środowiska opisane w tym przykładzie. Tworzenie maszyn wirtualnych i sieci wirtualne, chociaż są wykonywane tylko przez przykładowy skrypt, nie opisano szczegółowo w tym dokumencie. 

Do tworzenia środowiska;

1. Zapisz plik xml konfiguracji sieci znajdujących się w części odwołania (zaktualizowane przy użyciu nazwy, lokalizacji i adresów IP adresy, które mają odpowiadać danym scenariuszu)
2. Zaktualizuj zmienne użytkownika w skrypcie, aby dopasować środowiska, które skrypt ma być uruchamiana względem (subskrypcji, nazwy usług itp.)
3. Uruchom skrypt programu PowerShell

>[!Note]
>Region oznaczony w skrypcie programu PowerShell musi odpowiadać oznaczony w pliku xml konfiguracji sieci.
>
>

Po uruchomieniu skryptu pomyślnie dodatkowe opcjonalne kroki mogą zostać podjęte, w sekcji odwołań są dwa skrypty do konfiguracji serwera sieci web oraz serwer aplikacji z prostą aplikację sieci web umożliwia testowanie za pomocą tej konfiguracji sieci obwodowej.

Poniższe sekcje zawierają szczegółowy opis grupy zabezpieczeń sieci i jak działają w tym przykładzie na Instruktaż kluczowych wierszy skryptu programu PowerShell.

## <a name="network-security-groups-nsg"></a>Sieciowe grupy zabezpieczeń (NSG)
W tym przykładzie grupa sieciowej grupy zabezpieczeń jest utworzone i następnie ładowany za pomocą sześciu reguł. 

> [!TIP]
> Ogólnie rzecz biorąc należy najpierw utworzyć określone reguły "Zezwalaj", a następnie ostatniego bardziej ogólnymi zasadami "Deny". Określają priorytetem, których zasady są oceniane pierwszy. Nie dodatkowe reguły są oceniane, po znalezieniu ruchu do zastosowania do określonej reguły. Reguły sieciowej grupy zabezpieczeń można stosować w jednym kierunku ruchu przychodzącego lub wychodzącego (z punktu widzenia podsieci).
> 
> 

Deklaratywne są tworzone następujące reguły dla ruchu przychodzącego:

1. Ruch DNS wewnętrzny (port 53) jest dozwolone.
2. Ruch protokołu RDP (port 3389) z Internetu do dowolnej maszyny Wirtualnej jest dozwolony.
3. Ruch HTTP (port 80) z Internetu do serwera sieci web (IIS01) jest dozwolone.
4. Cały ruch (wszystkich portów) z IIS01 do AppVM1 jest dozwolone.
5. Cały ruch (wszystkich portów) z Internetu do całej sieci wirtualnej (zarówno podsieci) zostanie odrzucone.
6. Cały ruch (wszystkich portów) z podsieci Frontend do podsieci zaplecza jest zabroniony.

Przy użyciu tych reguł powiązany z każdej podsieci, jeśli żądanie HTTP zostało ruch przychodzący z Internetu do serwera sieci web, obie reguły 3 (Zezwalaj na) i 5 (odmowa) będzie miało zastosowanie, ale ponieważ reguła 3 ma wyższy priorytet, tylko naliczona zostałaby i reguły 5 nie przybyły, aby wpływające na. Dlatego żądania HTTP dozwolone będzie serwer sieci web. Jeśli ten sam ruch próbował uzyskać dostęp do serwera DNS01, reguła 5 (odmowa) może być pierwszy do zastosowania, a ruch będzie nie można przekazać do serwera. Reguła 6 (Odmów) blokuje podsieć frontonu w rozmowie z podsieci wewnętrznej bazy danych (z wyjątkiem dozwolonego ruchu w regułach 1 i 4), ten zestaw reguł ochronę sieci wewnętrznej bazy danych w przypadku, gdy naruszeń osoba atakująca aplikacji sieci web we frontonie, osoba atakująca będzie mieć ograniczone dostęp do wewnętrznej bazy danych "chroniony" sieci (tylko do zasobów udostępnianych na serwerze AppVM01).

Brak domyślną regułę ruchu wychodzącego, która umożliwia ruchu wychodzącego z Internetem. W tym przykładzie firma Microsoft zezwala na ruch wychodzący i nie modyfikowanie żadnych reguł dla ruchu wychodzącego. Zablokować ruch w obu kierunkach, routingu zdefiniowanego użytkownika jest wymagana i zbadano w "W przykładzie 3", na [strony najlepsze rozwiązania w zakresie granic zabezpieczeń][HOME].

Każda reguła jest omówiona bardziej szczegółowo w następujący sposób (**Uwaga**: dowolnego elementu w następujące listy rozpoczynający się od znaku dolara (na przykład: $NSGName) jest zdefiniowany przez użytkownika zmienną ze skryptu zamieszczone w tej sekcji tego dokumentu):

1. Najpierw sieciowej grupy zabezpieczeń muszą zostać skompilowane do przechowywania reguł:

    ```PowerShell
    New-AzureNetworkSecurityGroup -Name $NSGName `
        -Location $DeploymentLocation `
        -Label "Security group for $VNetName subnets in $DeploymentLocation"
    ```

2. Pierwszą regułę w tym przykładzie zezwala na ruch DNS między wszystkie sieci wewnętrznej na serwerze DNS w podsieci wewnętrznej bazy danych. Reguła ma kilka ważnych parametrów:
   
   * "Type" oznacza, w jakim kierunku przepływu ruchu tej reguły staje się skuteczny. Kierunek jest z punktu widzenia podsieci lub maszyn wirtualnych (zależnie od tego, gdzie powiązana jest ta sieciowa grupa zabezpieczeń). Dlatego jeśli typ to "Ruchu przychodzącego" ruch wchodzi podsieci, reguła będzie stosowana i ruchu wychodzącego z podsieci nie mogą być naruszone przez tę regułę.
   * "Priority" Ustawia kolejność, w którym jest oceniana przepływu ruchu. Im mniejsza liczba wyższy priorytet. Gdy reguła ma zastosowanie do przepływu ruchu określone, nie dodatkowe reguły są przetwarzane. Dlatego jeśli reguła z priorytetem 1 zezwala na ruch oraz reguły o priorytecie 2 nie zezwala na ruch i obie reguły są stosowane do ruchu, a następnie dozwolone ruch będzie przepływać (ponieważ reguła 1 ma wyższy priorytet, jaki zajęło efektu i nie dodatkowe reguły były stosowane).
   * "Action" oznacza, jeśli ruch dotyczy ta reguła jest zablokowane lub dozwolone.

     ```PowerShell    
     Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" `
        -Type Inbound -Priority 100 -Action Allow `
        -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[4] `
        -DestinationPortRange '53' `
        -Protocol *
     ```

3. Ta zasada umożliwia RDP przepływ ruchu z Internetu do portów protokołu RDP na każdym serwerze w powiązanej podsieci. Ta reguła używa dwa specjalne rodzaje prefiksy adresów; "VIRTUAL_NETWORK" i "INTERNET". Tagi te są łatwy sposób większych kategorię prefiksy adresów.

    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
         Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" `
         -Type Inbound -Priority 110 -Action Allow `
         -SourceAddressPrefix INTERNET -SourcePortRange '*' `
         -DestinationAddressPrefix VIRTUAL_NETWORK `
         -DestinationPortRange '3389' `
         -Protocol *
    ```

4. Ta reguła zezwala na ruch przychodzący z Internetu do trafień serwer sieci web. Ta zasada nie powoduje zmiany zachowania routingu. Ta reguła zezwala tylko ruch przeznaczony dla IIS01 do przekazania. Dlatego jeśli ruch z Internetu, gdyby serwer sieci web jako miejsca docelowego, ta zasada będzie zezwalała i zatrzymać dalsze przetwarzanie reguł. (W regule priorytetem 140 wszystkich innych ruch przychodzący z Internetu jest zablokowana). Tylko w przypadku przetwarzania ruchu HTTP, ta zasada można dodatkowo ograniczone umożliwia tylko docelowy Port 80.

    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
         Set-AzureNetworkSecurityRule -Name "Enable Internet to $VMName[0]" `
         -Type Inbound -Priority 120 -Action Allow `
         -SourceAddressPrefix Internet -SourcePortRange '*' `
         -DestinationAddressPrefix $VMIP[0] `
         -DestinationPortRange '*' `
         -Protocol *
    ```

5. Ta reguła zezwala na ruch do przekazania z serwera IIS01 serwerowi AppVM01 bloków reguł w późniejszym innych frontonu dla ruchu zaplecza. Aby zwiększyć tę regułę, jeśli port jest znany, że ma zostać dodany. Na przykład, jeśli na serwerze usług IIS powoduje osiągnięcie tylko program SQL Server na AppVM01, zakres portów docelowych należy zmienić "*" (wszystkie) 1433 (SQL port), dzięki czemu mniejszych przychodzących ataków na AppVM01 powinien aplikacji sieci web nigdy nie zostaną ujawnione.

    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule -Name "Enable $VMName[1] to $VMName[2]" `
        -Type Inbound -Priority 130 -Action Allow `
        -SourceAddressPrefix $VMIP[1] -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[2] `
        -DestinationPortRange '*' `
        -Protocol *
    ```

6. Ta zasada nie zezwala na ruch z Internetu do serwerów w sieci. Za pomocą reguł priorytetem 110 i 120 efekt jest umożliwienie tylko ruch przychodzący z Internetu do zapory i porty protokołu RDP na serwerach i bloków, wszystkie inne elementy. Ta reguła jest "odporne na uszkodzenia" regułę blokującą wszystkie przepływy nieoczekiwany.
    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule `
        -Name "Isolate the $VNetName VNet from the Internet" `
        -Type Inbound -Priority 140 -Action Deny `
        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
        -DestinationAddressPrefix VIRTUAL_NETWORK `
        -DestinationPortRange '*' `
        -Protocol *
    ```
7. Końcowa reguła nie zezwala na ruch z podsieci Frontend do podsieci wewnętrznej bazy danych. Ponieważ ta reguła jest tylko Reguła ruchu przychodzącego, ruch powrotny jest dozwolone (z wewnętrznej bazy danych do serwera sieci Web).

    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule `
        -Name "Isolate the $FESubnet subnet from the $BESubnet subnet" `
        -Type Inbound -Priority 150 -Action Deny `
        -SourceAddressPrefix $FEPrefix -SourcePortRange '*' `
        -DestinationAddressPrefix $BEPrefix `
        -DestinationPortRange '*' `
        -Protocol * 
    ```

## <a name="traffic-scenarios"></a>Scenariusze ruchu
#### <a name="allowed-internet-to-web-server"></a>(*Dozwolone*) Internet do serwera sieci web
1. Użytkownik internet żądań na stronie HTTP od FrontEnd001.CloudApp.Net (usługa chmury połączonego z Internetem)
2. Chmury usługi przekazuje ruch przez otwarty punkt końcowy na porcie 80 w kierunku IIS01 (serwer sieci web)
3. Podsieć frontonu rozpoczyna przetwarzanie reguł dla ruchu przychodzącego:
   1. 1 regułę sieciowej grupy zabezpieczeń (DNS, Domain Name System) nie zastosować, przenieść następną regułę
   2. 2 reguły sieciowej grupy zabezpieczeń (RDP) nie są spełnione, przenieść następną regułę
   3. Zastosuj 3 reguły sieciowej grupy zabezpieczeń (Internet do IIS01), ruch jest przetwarzanie reguł dozwolonych, Zatrzymaj
4. Ruch osiąga wewnętrzny adres IP serwera sieci web IIS01 (10.0.1.5)
5. IIS01 nasłuchuje ruchu w sieci web, odbiera żądania i rozpoczyna przetwarzanie żądania
6. IIS01 programu SQL Server na AppVM01 monituje o podanie informacji
7. Ponieważ nie ma żadnych reguł ruchu wychodzącego na podsieć frontonu, ruch jest dozwolony
8. Podsieci wewnętrznej bazy danych rozpoczyna się przetwarzanie reguł dla ruchu przychodzącego:
   1. 1 regułę sieciowej grupy zabezpieczeń (DNS, Domain Name System) nie zastosować, przenieść następną regułę
   2. 2 reguły sieciowej grupy zabezpieczeń (RDP) nie są spełnione, przenieść następną regułę
   3. 3 reguły sieciowej grupy zabezpieczeń (Internet do zapory) nie zastosować, przenieść następną regułę
   4. 4 reguły sieciowej grupy zabezpieczeń (IIS01 do AppVM01) są spełnione, ruch jest dozwolony, Zatrzymaj przetwarzanie reguł
9. AppVM01 odbiera zapytanie SQL i odpowiada
10. Ponieważ na podsieci wewnętrznej bazy danych nie ma żadnych reguł dla ruchu wychodzącego, odpowiedź jest dozwolone
11. Podsieć frontonu rozpoczyna przetwarzanie reguł dla ruchu przychodzącego:
    1. Istnieje nie reguły sieciowej grupy zabezpieczeń, która ma zastosowanie do ruchu przychodzącego ruchu sieciowego z podsieci zaplecza do podsieci frontonu, aby Brak sieciowej grupy zabezpieczeń reguły, zastosuj
    2. Reguła systemu domyślnej zezwalającej na ruch pomiędzy podsieciami umożliwia ten ruch, ruch jest dozwolony.
12. Na serwerze usług IIS odbiera odpowiedź SQL i kończy odpowiedź HTTP i wysyła do obiektu żądającego
13. Ponieważ brak reguł ruchu wychodzącego w podsieci Frontend jest dozwolony odpowiedzi i internet użytkownik otrzymuje żądanej strony sieci web.

#### <a name="allowed-rdp-to-backend"></a>(*Dozwolone*) protokołu RDP do wewnętrznej bazy danych
1. Administrator serwera w Internecie żądań sesji protokołu RDP, aby AppVM01 na BackEnd001.CloudApp.Net:xxxxx gdzie xxxxx jest liczbą losowo przypisany port dla protokołu RDP do AppVM01 (przypisanego portu można znaleźć w witrynie Azure portal lub za pośrednictwem programu PowerShell)
2. Podsieci wewnętrznej bazy danych rozpoczyna się przetwarzanie reguł dla ruchu przychodzącego:
   1. 1 regułę sieciowej grupy zabezpieczeń (DNS, Domain Name System) nie zastosować, przenieść następną regułę
   2. Stosowanie 2 reguły sieciowej grupy zabezpieczeń (RDP), ruch jest przetwarzanie reguł dozwolonych, Zatrzymaj
3. Za pomocą reguł ruchu wychodzącego domyślne reguły i ruch powrotny jest dozwolone
4. Sesję RDP jest włączona.
5. AppVM01 wyświetla monit o podanie nazwy użytkownika i hasła

#### <a name="allowed-web-server-dns-look-up-on-dns-server"></a>(*Dozwolone*) wyszukiwanie nazwy DNS serwera sieci Web na serwerze DNS
1. Sieci Web Server, IIS01, potrzeb w www.data.gov strumieniowych źródeł danych, ale musi rozpoznać adresu.
2. W konfiguracji sieci VNet list DNS01 (10.0.2.4 w podsieci wewnętrznej bazy danych) jako podstawowy serwer DNS, IIS01 wysyła żądania DNS DNS01
3. Nie reguł ruchu wychodzącego w podsieci Frontend jest dozwolony ruch
4. Podsieci wewnętrznej bazy danych rozpoczyna się przetwarzanie reguł dla ruchu przychodzącego:
   * Zastosuj 1 regułę sieciowej grupy zabezpieczeń (DNS, Domain Name System), ruch jest przetwarzanie reguł dozwolonych, Zatrzymaj
5. Serwer DNS odbiera żądanie
6. Serwer DNS nie ma adresu pamięci podręcznej i prosi główny serwer DNS w Internecie
7. Nie reguł ruchu wychodzącego w podsieci zaplecza ruch jest dozwolony.
8. Odpowiada internetowym serwerze DNS, ponieważ ta sesja została zainicjowana wewnętrznie, odpowiedź jest dozwolone.
9. Serwer DNS będzie buforować odpowiedź i odpowiada na żądanie początkowy powrót do IIS01
10. Nie reguł ruchu wychodzącego w podsieci zaplecza ruch jest dozwolony.
11. Podsieć frontonu rozpoczyna przetwarzanie reguł dla ruchu przychodzącego:
    1. Istnieje nie reguły sieciowej grupy zabezpieczeń, która ma zastosowanie do ruchu przychodzącego ruchu sieciowego z podsieci zaplecza do podsieci frontonu, aby Brak sieciowej grupy zabezpieczeń reguły, zastosuj
    2. Reguła systemowa domyślnej zezwalającej na ruch pomiędzy podsieciami będzie zezwalającej na ten ruch, więc ruch jest dozwolony
12. IIS01 odbiera odpowiedź od DNS01

#### <a name="allowed-web-server-access-file-on-appvm01"></a>(*Dozwolone*) pliku dostępu do serwera sieci Web na AppVM01
1. IIS01 poprosi o podanie pliku na AppVM01
2. Nie reguł ruchu wychodzącego w podsieci Frontend jest dozwolony ruch
3. Podsieci wewnętrznej bazy danych rozpoczyna się przetwarzanie reguł dla ruchu przychodzącego:
   1. 1 regułę sieciowej grupy zabezpieczeń (DNS, Domain Name System) nie zastosować, przenieść następną regułę
   2. 2 reguły sieciowej grupy zabezpieczeń (RDP) nie są spełnione, przenieść następną regułę
   3. 3 reguły sieciowej grupy zabezpieczeń (Internet do IIS01) nie zastosować, przenieść następną regułę
   4. 4 reguły sieciowej grupy zabezpieczeń (IIS01 do AppVM01) są spełnione, ruch jest dozwolony, Zatrzymaj przetwarzanie reguł
4. AppVM01 odbiera żądanie i odpowiada za pomocą pliku (przy założeniu, że dostęp jest autoryzowany)
5. Ponieważ na podsieci wewnętrznej bazy danych nie ma żadnych reguł dla ruchu wychodzącego, odpowiedź jest dozwolone
6. Podsieć frontonu rozpoczyna przetwarzanie reguł dla ruchu przychodzącego:
   1. Istnieje nie reguły sieciowej grupy zabezpieczeń, która ma zastosowanie do ruchu przychodzącego ruchu sieciowego z podsieci zaplecza do podsieci frontonu, aby Brak sieciowej grupy zabezpieczeń reguły, zastosuj
   2. Reguła systemu domyślnej zezwalającej na ruch pomiędzy podsieciami umożliwia ten ruch, ruch jest dozwolony.
7. Serwer IIS odbiera pliku

#### <a name="denied-web-to-backend-server"></a>(*Odmowa*) do serwera wewnętrznej bazy danych w sieci Web
1. Użytkownik internet próbuje dostęp do pliku na AppVM01 za pośrednictwem usługi BackEnd001.CloudApp.Net
2. Punkty końcowe nie są otwarte dla udziału plików, ten ruch nie przejdzie z usługą w chmurze, a nie dociera do serwera
3. W przypadku punktów końcowych, które były otwarte niektóre przyczyny, ten ruch mogłyby spowodować zablokowanie reguły sieciowej grupy zabezpieczeń 5 (Internetu do sieci wirtualnej)

#### <a name="denied-web-dns-look-up-on-dns-server"></a>(*Odmowa*) wyszukiwanie nazwy DNS w sieci Web na serwerze DNS
1. Użytkownik internet próbuje do wyszukania wewnętrzny rekord DNS na DNS01 za pośrednictwem usługi BackEnd001.CloudApp.Net
2. Punkty końcowe nie są otwarte w systemie DNS, ten ruch nie przejdzie z usługą w chmurze, a nie dociera do serwera
3. W przypadku punktów końcowych, które były otwarte niektóre przyczyny, ten ruch mogłyby spowodować zablokowanie reguły sieciowej grupy zabezpieczeń 5 (Internet z siecią wirtualną) (Uwaga: nie ma zastosowania tej reguły 1 domen (DNS) z dwóch przyczyn, najpierw adres źródłowy jest internet, ta zasada ma zastosowanie tylko do lokalnej sieci wirtualnej jako źródło również ta reguła ma regułę zezwalającą, więc go może nigdy nie zezwalają na ruch)

#### <a name="denied-web-to-sql-access-through-firewall"></a>(*Odmowa*) na dostęp SQL przez zaporę w sieci Web
1. Użytkownik internet żąda danych SQL z FrontEnd001.CloudApp.Net (usługa chmury połączonego z Internetem)
2. Punkty końcowe nie są otwarte dla programu SQL, ten ruch nie przejdzie z usługą w chmurze i w takich sytuacjach przydałaby dotrzeć do zapory
3. Jeśli punkty końcowe były otwarte niektóre przyczyny, z podsiecią Frontend rozpoczyna przetwarzanie reguł dla ruchu przychodzącego:
   1. 1 regułę sieciowej grupy zabezpieczeń (DNS, Domain Name System) nie zastosować, przenieść następną regułę
   2. 2 reguły sieciowej grupy zabezpieczeń (RDP) nie są spełnione, przenieść następną regułę
   3. Zastosuj 3 reguły sieciowej grupy zabezpieczeń (Internet do IIS01), ruch jest przetwarzanie reguł dozwolonych, Zatrzymaj
4. Ruch osiąga wewnętrzny adres IP IIS01 (10.0.1.5)
5. IIS01 nie nasłuchuje na porcie 1433, więc nie odpowiedź na żądanie

## <a name="conclusion"></a>Podsumowanie
W tym przykładzie jest stosunkowo prosta i proste sposób odizolowanie podsieci zaplecza z ruchu przychodzącego.

Więcej przykładów i przegląd granice zabezpieczeń sieci można znaleźć [tutaj][HOME].

## <a name="references"></a>Dokumentacja
### <a name="main-script-and-network-config"></a>Główne config skryptu i sieci
Pełny skrypt należy zapisać w pliku skryptu programu PowerShell. Zapisanie konfiguracji sieci w pliku o nazwie "NetworkConf1.xml."
Zmodyfikuj zmienne zdefiniowane przez użytkownika, zgodnie z potrzebami, a następnie uruchom skrypt.

#### <a name="full-script"></a>Pełny skrypt
Ten skrypt spowoduje, na podstawie zmiennych zdefiniowanych przez użytkownika;

1. Łączenie się z subskrypcją platformy Azure
2. Tworzenie konta magazynu
3. Tworzenie sieci wirtualnej z dwoma podsieciami, zgodnie z definicją w pliku konfiguracji sieci
4. Tworzenie czterech systemu windows server w maszynach wirtualnych
5. Konfigurowanie sieciowej grupy zabezpieczeń w tym:
   * Tworzenie sieciowej grupy zabezpieczeń
   * Zapełnianie reguły
   * Powiązanie odpowiednie podsieci sieciową grupę zabezpieczeń

Ten skrypt programu PowerShell można uruchamiać lokalnie na połączone z Internetem, komputera lub serwera.

> [!IMPORTANT]
> Po uruchomieniu tego skryptu można ostrzeżenia lub inne komunikaty informacyjne, które punktów obecności w programie PowerShell. Tylko komunikaty o błędach w kolorze czerwonym są zaniepokoić.
> 
>

```PowerShell
<# 
 .SYNOPSIS
  Example of Network Security Groups in an isolated network (Azure only, no hybrid connections)

 .DESCRIPTION
  This script will build out a sample DMZ setup containing:
   - A default storage account for VM disks
   - Two new cloud services
   - Two Subnets (FrontEnd and BackEnd subnets)
   - One server on the FrontEnd Subnet
   - Three Servers on the BackEnd Subnet
   - Network Security Groups to allow/deny traffic patterns as declared

  Before running script, ensure the network configuration file is created in
  the directory referenced by $NetworkConfigFile variable (or update the
  variable to reflect the path and file name of the config file being used).

 .Notes
  Security requirements are different for each use case and can be addressed in a
  myriad of ways. Please be sure that any sensitive data or applications are behind
  the appropriate layer(s) of protection. This script serves as an example of some
  of the techniques that can be used, but should not be used for all scenarios. You
  are responsible to assess your security needs and the appropriate protections
  needed, and then effectively implement those protections.

  FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
   IIS01      - 10.0.1.5

  BackEnd Service (BackEnd subnet 10.0.2.0/24)
   DNS01      - 10.0.2.4
   AppVM01    - 10.0.2.5
   AppVM02    - 10.0.2.6

#>

# Fixed Variables
    $LocalAdminPwd = Read-Host -Prompt "Enter Local Admin Password to be used for all VMs"
    $VMName = @()
    $ServiceName = @()
    $VMFamily = @()
    $img = @()
    $size = @()
    $SubnetName = @()
    $VMIP = @()

# User-Defined Global Variables
  # These should be changes to reflect your subscription and services
  # Invalid options will fail in the validation section

  # Subscription Access Details
    $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

  # VM Account, Location, and Storage Details
    $LocalAdmin = "theAdmin"
    $DeploymentLocation = "Central US"
    $StorageAccountName = "vmstore02"

  # Service Details
    $FrontEndService = "FrontEnd001"
    $BackEndService = "BackEnd001"

  # Network Details
    $VNetName = "CorpNetwork"
    $FESubnet = "FrontEnd"
    $FEPrefix = "10.0.1.0/24"
    $BESubnet = "BackEnd"
    $BEPrefix = "10.0.2.0/24"
    $NetworkConfigFile = "C:\Scripts\NetworkConf1.xml"

  # VM Base Disk Image Details
    $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}

  # NSG Details
    $NSGName = "MyVNetSG"

# User-Defined VM Specific Configuration
    # Note: To ensure proper NSG Rule creation later in this script:
    #       - The Web Server must be VM 0
    #       - The AppVM1 Server must be VM 1
    #       - The DNS server must be VM 3
    #
    #       Otherwise the NSG rules in the last section of this
    #       script will need to be changed to match the modified
    #       VM array numbers ($i) so the NSG Rule IP addresses
    #       are aligned to the associated VM IP addresses.

    # VM 0 - The Web Server
      $VMName += "IIS01"
      $ServiceName += $FrontEndService
      $VMFamily += "Windows"
      $img += $SrvImg
      $size += "Standard_D3"
      $SubnetName += $FESubnet
      $VMIP += "10.0.1.5"

    # VM 1 - The First Application Server
      $VMName += "AppVM01"
      $ServiceName += $BackEndService
      $VMFamily += "Windows"
      $img += $SrvImg
      $size += "Standard_D3"
      $SubnetName += $BESubnet
      $VMIP += "10.0.2.5"

    # VM 2 - The Second Application Server
      $VMName += "AppVM02"
      $ServiceName += $BackEndService
      $VMFamily += "Windows"
      $img += $SrvImg
      $size += "Standard_D3"
      $SubnetName += $BESubnet
      $VMIP += "10.0.2.6"

    # VM 3 - The DNS Server
      $VMName += "DNS01"
      $ServiceName += $BackEndService
      $VMFamily += "Windows"
      $img += $SrvImg
      $size += "Standard_D3"
      $SubnetName += $BESubnet
      $VMIP += "10.0.2.4"

# ----------------------------- #
# No User-Defined Variables or  #
# Configuration past this point #
# ----------------------------- #    

  # Get your Azure accounts
    Add-AzureAccount
    Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
    Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop

  # Create Storage Account
    If (Test-AzureName -Storage -Name $StorageAccountName) { 
        Write-Host "Fatal Error: This storage account name is already in use, please pick a different name." -ForegroundColor Red
        Return}
    Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan 
          New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}

  # Update Subscription Pointer to New Storage Account
    Write-Host "Updating Subscription Pointer to New Storage Account" -ForegroundColor Cyan 
    Set-AzureSubscription –SubscriptionId $subID -CurrentStorageAccountName $StorageAccountName -ErrorAction Stop

# Validation
$FatalError = $false

If (-Not (Get-AzureLocation | Where {$_.DisplayName -eq $DeploymentLocation})) {
     Write-Host "This Azure Location was not found or available for use" -ForegroundColor Yellow
     $FatalError = $true}

If (Test-AzureName -Service -Name $FrontEndService) { 
    Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
    $FatalError = $true}
Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}

If (Test-AzureName -Service -Name $BackEndService) { 
    Write-Host "The BackEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
    $FatalError = $true}
Else { Write-Host "The BackEndService service name is valid for use." -ForegroundColor Green}

If (-Not (Test-Path $NetworkConfigFile)) { 
    Write-Host 'The network config file was not found, please update the $NetworkConfigFile variable to point to the network config xml file.' -ForegroundColor Yellow
    $FatalError = $true}
Else { Write-Host "The network configuration file was found" -ForegroundColor Green
        If (-Not (Select-String -Pattern $DeploymentLocation -Path $NetworkConfigFile)) {
            Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation variable is correct and the network config file matches.' -ForegroundColor Yellow
            $FatalError = $true}
        Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}

If ($FatalError) {
    Write-Host "A fatal error has occurred, please see the above messages for more information." -ForegroundColor Red
    Return}
Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}

# Create VNET
    Write-Host "Creating VNET" -ForegroundColor Cyan 
    Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop

# Create Services
    Write-Host "Creating Services" -ForegroundColor Cyan
    New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
    New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop

# Build VMs
    $i=0
    $VMName | Foreach {
        Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
        New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
            Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
            Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
            Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
            Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
            Remove-AzureEndpoint -Name "PowerShell" | `
            New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
            # Note: A Remote Desktop endpoint is automatically created when each VM is created.
        $i++
    }
    # Add HTTP Endpoint for IIS01
    Get-AzureVM -ServiceName $ServiceName[0] -Name $VMName[0] | Add-AzureEndpoint -Name HTTP -Protocol tcp -LocalPort 80 -PublicPort 80 | Update-AzureVM

# Configure NSG
    Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan

  # Build the NSG
    Write-Host "Building the NSG" -ForegroundColor Cyan
    New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"

  # Add NSG Rules
    Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" -Type Inbound -Priority 100 -Action Allow `
        -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[3] -DestinationPortRange '53' `
        -Protocol *

    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" -Type Inbound -Priority 110 -Action Allow `
        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
        -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '3389' `
        -Protocol *

    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internet to $($VMName[0])" -Type Inbound -Priority 120 -Action Allow `
        -SourceAddressPrefix Internet -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[0] -DestinationPortRange '*' `
        -Protocol *

    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable $($VMName[0]) to $($VMName[1])" -Type Inbound -Priority 130 -Action Allow `
        -SourceAddressPrefix $VMIP[0] -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[1] -DestinationPortRange '*' `
        -Protocol *

    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 140 -Action Deny `
        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
        -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
        -Protocol *

    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $FESubnet subnet from the $BESubnet subnet" -Type Inbound -Priority 150 -Action Deny `
        -SourceAddressPrefix $FEPrefix -SourcePortRange '*' `
        -DestinationAddressPrefix $BEPrefix -DestinationPortRange '*' `
        -Protocol *

    # Assign the NSG to the Subnets
        Write-Host "Binding the NSG to both subnets" -ForegroundColor Cyan
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName

# Optional Post-script Manual Configuration
  # Install Test Web App (Run Post-Build Script on the IIS Server)
  # Install Backend resource (Run Post-Build Script on the AppVM01)
  Write-Host
  Write-Host "Build Complete!" -ForegroundColor Green
  Write-Host
  Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
  Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
  Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
  Write-Host
```

#### <a name="network-config-file"></a>Plik konfiguracji sieci
Zapisz ten plik xml z lokalizacją zaktualizowane i dodać link do tego pliku do zmiennej $NetworkConfigFile w poprzednim skrypcie.

```XML
<NetworkConfiguration xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
  <VirtualNetworkConfiguration>
    <Dns>
      <DnsServers>
        <DnsServer name="DNS01" IPAddress="10.0.2.4" />
        <DnsServer name="Level3" IPAddress="209.244.0.3" />
      </DnsServers>
    </Dns>
    <VirtualNetworkSites>
      <VirtualNetworkSite name="CorpNetwork" Location="Central US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="FrontEnd">
            <AddressPrefix>10.0.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="BackEnd">
            <AddressPrefix>10.0.2.0/24</AddressPrefix>
          </Subnet>
        </Subnets>
        <DnsServersRef>
          <DnsServerRef name="DNS01" />
          <DnsServerRef name="Level3" />
        </DnsServersRef>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
  </VirtualNetworkConfiguration>
</NetworkConfiguration>
```

#### <a name="sample-application-scripts"></a>Przykładowe skrypty aplikacji
Jeśli chcesz zainstalować przykładową aplikację w tym i inne przykłady strefy DMZ, jedno zostało podane z łącza: [Przykładowy skrypt aplikacji][SampleApp]

## <a name="next-steps"></a>Kolejne kroki
* Aktualizacji, a następnie zapisz plik XML
* Uruchom skrypt programu PowerShell do tworzenia środowiska
* Zainstaluj przykładową aplikację.
* Testowanie różnego ruchu za pośrednictwem tej sieci obwodowej

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-asm/example1design.png "Przychodząca sieć obwodowa z grupy NSG"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md

