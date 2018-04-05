---
title: Przykład DMZ Azure — Tworzenie prostego DMZ z grup NSG | Dokumentacja firmy Microsoft
description: Tworzenie DMZ z grup zabezpieczeń sieci (NSG)
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
ms.openlocfilehash: ed172d552e1e4c9ee27c58abcd7ad2d98df21579
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="example-1--build-a-simple-dmz-using-nsgs-with-classic-powershell"></a>Przykład 1 — Tworzenie prostego DMZ, za pomocą grup NSG z klasycznego środowiska PowerShell
[Wróć do strony zabezpieczeń granic najlepsze praktyki][HOME]

> [!div class="op_single_selector"]
> * [Szablon usługi Resource Manager](virtual-networks-dmz-nsg.md)
> * [Classic — PowerShell](virtual-networks-dmz-nsg-asm.md)
> 
>

W tym przykładzie tworzy DMZ pierwotnych z czterech serwerów z systemem Windows i grupy zabezpieczeń sieci. W tym przykładzie przedstawiono wszystkich odpowiednich poleceń programu PowerShell w celu zapewnienia głębsze zrozumienie każdego kroku. Jest również sekcji scenariusza ruchu zapewnienie szczegółowe instrukcje, jak ruchu obejmującego warstw zabezpieczeń w strefie DMZ. Ponadto w odwołaniach sekcja jest kompletny kod i instrukcje do tworzenia tego środowiska, aby przetestować i wypróbować różne scenariusze. 

![Przychodzący DMZ z grupy NSG][1]

## <a name="environment-description"></a>Opis elementu środowiska
W tym przykładzie subskrypcja zawiera następujące zasoby:

* Dwie usługi w chmurze: "FrontEnd001" i "BackEnd001"
* Sieć wirtualną "CorpNetwork", z dwoma podsieciami; "FrontEnd" i "Wewnętrzna"
* Grupy zabezpieczeń sieci jest stosowany do obu podsieci
* Windows Server, który reprezentuje serwer sieci web aplikacji ("IIS01")
* Dwóch systemów windows Server, które reprezentują serwery zaplecza aplikacji ("AppVM01", "AppVM02")
* Windows server, który reprezentuje serwer DNS ("DNS01")

W sekcji odwołań jest skrypt programu PowerShell, która tworzy większość środowiska opisane w tym przykładzie. Tworzenie maszyn wirtualnych i sieci wirtualnych, chociaż są realizowane przez skrypt przykładowy nie opisano szczegółowo w tym dokumencie. 

Tworzenie środowiska;

1. Zapisz plik xml konfiguracji sieci zawarte w sekcji odwołań (zaktualizowany o nazwy, lokalizacji i IP adresów do dopasowania danego scenariusza)
2. Zaktualizuj zmienne użytkownika w skrypcie, aby dopasować środowiska, które jest skrypt do uruchomienia przed (subskrypcje, nazwy usługi, itp.)
3. Uruchom skrypt programu PowerShell

>[!Note]
>Region oznaczony w skrypcie programu PowerShell musi odpowiadać oznaczony w pliku xml konfiguracji sieci.
>
>

Po uruchomieniu skryptu pomyślnie dodatkowe opcjonalne kroki mogą zostać podjęte, w sekcji odwołań są dwa skrypty do konfiguracji serwera sieci web oraz serwer aplikacji z prostą aplikację sieci web umożliwia testowanie za pomocą tej konfiguracji DMZ.

Poniższe sekcje zawierają szczegółowy opis grup zabezpieczeń sieci i ich działania w tym przykładzie przez Instruktaż klucza wierszy skryptu programu PowerShell.

## <a name="network-security-groups-nsg"></a>Sieciowe grupy zabezpieczeń (NSG)
Na przykład grupy NSG jest wbudowana i następnie ładowane przy użyciu sześciu reguł. 

> [!TIP]
> Ogólnie rzecz biorąc należy najpierw utworzyć określonych reguł "Zezwalaj", a następnie ostatni bardziej ogólnym reguły "Deny". Określają priorytetem, do których zasady są oceniane pierwszej. Po znalezieniu ruchu do zastosowania do określonej reguły nie dalsze reguły są sprawdzane. Reguły NSG można zastosować w jednym kierunku ruchu przychodzącego lub wychodzącego (z punktu widzenia podsieci).
> 
> 

Deklaratywnie są tworzone następujące reguły dla ruchu przychodzącego:

1. Wewnętrzny ruch DNS (port 53) jest dozwolony
2. Ruch RDP (port 3389) z Internetu do żadnej maszyny Wirtualnej jest dozwolone.
3. Ruch HTTP (port 80) z Internetu do serwera sieci web (IIS01) jest dozwolone.
4. Cały ruch (wszystkie porty) z IIS01 do AppVM1 jest dozwolone.
5. Cały ruch (wszystkie porty) z Internetu do całej sieci wirtualnej (obie podsieci) jest zabroniony.
6. Odmowa cały ruch (wszystkie porty) z podsieci frontonu do podsieci wewnętrznej bazy danych

Przy użyciu tych reguł powiązany z każdej podsieci, jeśli żądanie HTTP zostało przychodzące z Internetu do serwera sieci web, obie zasady 3 (Zezwalaj) i 5 (odmówić go) będą miały zastosowania, ale ponieważ reguła 3 ma wyższy priorytet tylko będą miały zastosowania i reguły 5 nie przybyły do gry. W związku z tym żądania HTTP będą dozwolone na serwerze sieci web. Jeśli ten sam ruch próbował uzyskać dostęp do serwera DNS01, reguły 5 (odmowa) będzie pierwszy do zastosowania, a ruch będzie niedozwolone do przekazania na serwer. Reguła 6 (Odmów) blokuje podsieci frontonu z rozmowie z podsieci wewnętrznej bazy danych (z wyjątkiem dozwolonego ruchu w regułach 1 i 4), ten zestaw reguł chroni sieć zaplecze w przypadku dokonywania atakująca aplikacja sieci web frontonu, osoba atakująca może mieć ograniczony dostęp do sieci wewnętrznej bazy danych "chronionej" (tylko dla zasobów udostępniane na serwerze AppVM01).

Brak domyślnej regule wychodzącej, która umożliwia ruchu wychodzącego do Internetu. Na przykład firma Microsoft zezwala na ruch wychodzący i nie modyfikowanie reguł wychodzących. Do blokowania ruchu w obu kierunkach, zdefiniowane routingu użytkownika jest wymagany i jest przedstawione w "W przykładzie 3" na [strony najlepsze rozwiązania w zakresie granic zabezpieczeń][HOME].

Każda reguła omówiono bardziej szczegółowo w następujący sposób (**Uwaga**: dowolny element w poniższej listy rozpoczynający się znakiem dolara (na przykład: $NSGName) jest zmienną użytkownika ze skryptu zamieszczone w tej sekcji tego dokumentu):

1. Najpierw sieciowej grupy zabezpieczeń muszą zostać skompilowane do przechowywania reguł:

    ```PowerShell
    New-AzureNetworkSecurityGroup -Name $NSGName `
        -Location $DeploymentLocation `
        -Label "Security group for $VNetName subnets in $DeploymentLocation"
    ```

2. Pierwsza reguła w tym przykładzie zezwala na ruch DNS od wszystkich sieci wewnętrznej na serwerze DNS w podsieci wewnętrznej bazy danych. Reguła ma pewne ważne parametry:
   
   * "Typ" oznacza, że w kierunku przepływu ruchu obowiązuje tej reguły. Kierunek jest z punktu widzenia podsieci lub maszyny wirtualnej (w zależności od tego, gdzie jest powiązany ten NSG). W związku z tym jeśli typ to "Przychodzącego" i ruchu jest wprowadzanie podsieci, reguła powinna zostać zastosowana i ruchu wychodzącego do podsieci nie może mieć wpływ na przez tę regułę.
   * "Priority" Ustawia kolejność, w jakiej są oceniane przepływu ruchu. Im mniejsza liczba wyższy priorytet. Jeśli reguła ma zastosowanie do określonego ruchu, żadne dalsze reguły są przetwarzane. W związku z tym jeśli reguła o priorytecie 1 zezwala na ruch i reguły o priorytecie 2 nie zezwala na ruch i obie zasady są stosowane do ruchu, a następnie ruch będzie dozwolony przepływ (ponieważ reguła 1 ma wyższy priorytet trwało efektu i żadne dodatkowe reguły zostały zastosowane).
   * "Akcja" oznacza, że jeśli ruch wpływ ta reguła jest zablokowane lub dozwolone.

    ```PowerShell    
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" `
        -Type Inbound -Priority 100 -Action Allow `
        -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[4] `
        -DestinationPortRange '53' `
        -Protocol *
    ```

3. Ta reguła zezwala na ruch RDP mogą przepływać z Internetu z portem RDP na dowolnym serwerze podsieci powiązania. Ta zasada wykorzystuje dwa typy specjalne prefiksy adresów; "VIRTUAL_NETWORK" i "INTERNET". Tagi te są łatwe większych kategorię prefiksy adresów.

    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
         Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" `
         -Type Inbound -Priority 110 -Action Allow `
         -SourceAddressPrefix INTERNET -SourcePortRange '*' `
         -DestinationAddressPrefix VIRTUAL_NETWORK `
         -DestinationPortRange '3389' `
         -Protocol *
    ```

4. Ta zasada umożliwia ruch przychodzący z Internetu trafienie serwera sieci web. Ta zasada nie powoduje zmiany zachowania routingu. Ta reguła zezwala tylko ruch kierowany do IIS01 do przekazania. W związku z tym jeśli ruch z Internetu miał serwera sieci web jako miejsca docelowego tej reguły spowoduje zezwolenie i zatrzymać dalsze przetwarzanie reguł. (W regule priorytetem 140 wszystkich innych przychodzącego ruchu internetowego jest zablokowana). Tylko w przypadku przetwarzania ruchu HTTP, ta zasada można dodatkowo ograniczony umożliwiają tylko docelowy Port 80.

    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
         Set-AzureNetworkSecurityRule -Name "Enable Internet to $VMName[0]" `
         -Type Inbound -Priority 120 -Action Allow `
         -SourceAddressPrefix Internet -SourcePortRange '*' `
         -DestinationAddressPrefix $VMIP[0] `
         -DestinationPortRange '*' `
         -Protocol *
    ```

5. Ta zasada umożliwia ruch z serwera IIS01 na serwerze AppVM01 nowsze bloków reguł innych frontonu dla ruchu wewnętrznej bazy danych. Aby poprawić tej reguły, jeśli znane jest port, który ma zostać dodany. Na przykład, jeśli serwer usług IIS jest naciśnięcie tylko programu SQL Server na AppVM01, zakres portów docelowych należy zmienić "*" (Any) 1433 (SQL port), dzięki czemu mniejsze przychodzących ataki AppVM01 aplikacji sieci web kiedykolwiek zagrożenia bezpieczeństwa.

    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule -Name "Enable $VMName[1] to $VMName[2]" `
        -Type Inbound -Priority 130 -Action Allow `
        -SourceAddressPrefix $VMIP[1] -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[2] `
        -DestinationPortRange '*' `
        -Protocol *
    ```

6. Ta reguła nie zezwala na ruch z Internetu do serwerów w sieci. Z regułami priorytetem 110 i 120 efekt jest umożliwienie tylko internet ruchu przychodzącego zapory i porty protokołu RDP na serwerach i bloków, wszystkie inne elementy. Ta reguła jest regułą "awaryjnego" Aby zablokować wszystkie przepływy nieoczekiwany.
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
7. Reguła końcowego nie zezwala na ruch z podsieci frontonu do podsieci wewnętrznej bazy danych. Ponieważ ta reguła jest tylko regułę ruchu przychodzącego, odwrotnej ruch jest dozwolony (z wewnętrznej bazy danych do serwera sieci Web).

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
#### <a name="allowed-internet-to-web-server"></a>(*Dozwolone*) Internetu do serwera sieci web
1. Użytkownik internet zażąda strony HTTP z FrontEnd001.CloudApp.Net (Internet ukierunkowane usługa w chmurze)
2. W chmurze usługi przekazuje ruch przez otwarty punkt końcowy na porcie 80 kierunku IIS01 (serwer sieci web)
3. Podsieci frontonu rozpoczyna przetwarzanie przychodzących reguł:
   1. Reguły NSG 1 DNS (Domain Name System) nie są spełnione, przejść do następnej reguły
   2. 2 reguły NSG (RDP) nie są spełnione, przejść do następnej reguły
   3. Zastosować grupy NSG zasady 3 (Internet w celu IIS01), ruch jest dozwolony, stop reguły przetwarzania
4. Ruch trafienia wewnętrzny adres IP serwera sieci web IIS01 (10.0.1.5)
5. IIS01 nasłuchuje ruchu w sieci web, otrzymuje tego żądania i rozpoczyna przetwarzanie żądania
6. IIS01 żąda od serwera SQL na AppVM01 informacji
7. Ponieważ nie ma żadnych reguł dla ruchu wychodzącego w podsieci frontonu, ruch jest dozwolony
8. Podsieci wewnętrznej bazy danych rozpoczyna się przetwarzanie przychodzących reguł:
   1. Reguły NSG 1 DNS (Domain Name System) nie są spełnione, przejść do następnej reguły
   2. 2 reguły NSG (RDP) nie są spełnione, przejść do następnej reguły
   3. Grupy NSG zasady 3 (Internet do zapory) nie są spełnione, przejść do następnej reguły
   4. 4 reguły NSG zastosować (IIS01 do AppVM01), ruch jest dozwolony, Zatrzymaj przetwarzania zasad
9. AppVM01 odbiera zapytanie SQL i odpowiada
10. Ponieważ nie ma żadnych reguł dla ruchu wychodzącego w podsieci wewnętrznej bazy danych, odpowiedzi jest dozwolona
11. Podsieci frontonu rozpoczyna przetwarzanie przychodzących reguł:
    1. Nie zasady grupy NSG, która ma zastosowanie do przychodzącego ruchu w podsieci wewnętrznej bazy danych do podsieci frontonu, aby żadna grupa NSG zasady stosowane
    2. Domyślna reguła system zezwala na ruch między podsieciami pozwala tego rodzaju ruch, ruch jest dozwolony.
12. Serwer usług IIS otrzyma odpowiedź SQL i kończy odpowiedź HTTP i przesyła do żądającego
13. Ponieważ nie ma żadnych reguł dla ruchu wychodzącego w podsieci frontonu odpowiedzi jest dozwolone, a internet użytkownik otrzymuje żądanej strony sieci web.

#### <a name="allowed-rdp-to-backend"></a>(*Dozwolone*) RDP do wewnętrznej bazy danych
1. Administrator serwera w Internecie żądań sesji protokołu RDP do AppVM01 na BackEnd001.CloudApp.Net:xxxxx gdzie xxxxx jest numerem losowo przypisany port protokołu RDP do AppVM01 (przypisanego portu można znaleźć w portalu Azure lub za pomocą programu PowerShell)
2. Podsieci wewnętrznej bazy danych rozpoczyna się przetwarzanie przychodzących reguł:
   1. Reguły NSG 1 DNS (Domain Name System) nie są spełnione, przejść do następnej reguły
   2. Zastosuj 2 reguły NSG (RDP), ruch jest dozwolony, stop reguły przetwarzania
3. Z nie reguł dla ruchu wychodzącego Zastosuj reguły domyślne i zwracany ruch jest dozwolony
4. Włączono sesji protokołu RDP
5. AppVM01 monit o podanie nazwy użytkownika i hasła

#### <a name="allowed-web-server-dns-look-up-on-dns-server"></a>(*Dozwolone*) wyszukiwanie nazwy DNS serwera sieci Web na serwerze DNS
1. Sieci Web serwera, IIS01, musi na www.data.gov strumieniowego źródła danych, ale musi rozpoznać adresu.
2. Konfigurację sieci dla listy sieci wirtualnej DNS01 (10.0.2.4 podsieci wewnętrznej bazy danych) jako podstawowy serwer DNS, IIS01 wysyła żądanie DNS DNS01
3. Nie reguł dla ruchu wychodzącego w podsieci frontonu, ruch jest dozwolony
4. Podsieci wewnętrznej bazy danych rozpoczyna się przetwarzanie przychodzących reguł:
   * Zastosuj reguły NSG 1 DNS (Domain Name System), ruch jest dozwolony, stop reguły przetwarzania
5. Serwer DNS odbiera żądanie
6. Serwer DNS nie ma adresu w pamięci podręcznej i prosi o główny serwer DNS w Internecie
7. Nie reguł dla ruchu wychodzącego w podsieci wewnętrznej bazy danych, ruch jest dozwolony
8. DNS w Internecie serwer odpowiada, ponieważ ta sesja została zainicjowana wewnętrznie, odpowiedź jest dozwolone.
9. Serwer DNS będzie buforować odpowiedź i odpowiada na żądania początkowego do IIS01
10. Nie reguł dla ruchu wychodzącego w podsieci wewnętrznej bazy danych, ruch jest dozwolony
11. Podsieci frontonu rozpoczyna przetwarzanie przychodzących reguł:
    1. Nie zasady grupy NSG, która ma zastosowanie do przychodzącego ruchu w podsieci wewnętrznej bazy danych do podsieci frontonu, aby żadna grupa NSG zasady stosowane
    2. Domyślna reguła system zezwala na ruch między podsieciami pozwala tego rodzaju ruch, ruch jest dozwolony
12. IIS01 odbiera odpowiedź z DNS01

#### <a name="allowed-web-server-access-file-on-appvm01"></a>(*Dozwolone*) pliku dostępu do serwera sieci Web na AppVM01
1. IIS01 żąda pliku na AppVM01
2. Nie reguł dla ruchu wychodzącego w podsieci frontonu, ruch jest dozwolony
3. Podsieci wewnętrznej bazy danych rozpoczyna się przetwarzanie przychodzących reguł:
   1. Reguły NSG 1 DNS (Domain Name System) nie są spełnione, przejść do następnej reguły
   2. 2 reguły NSG (RDP) nie są spełnione, przejść do następnej reguły
   3. Grupa NSG zasady 3 (Internet w celu IIS01) nie są spełnione, przejść do następnej reguły
   4. 4 reguły NSG zastosować (IIS01 do AppVM01), ruch jest dozwolony, Zatrzymaj przetwarzania zasad
4. AppVM01 odbiera żądanie i odpowiada plik (przy założeniu, że autoryzacji dostępu)
5. Ponieważ nie ma żadnych reguł dla ruchu wychodzącego w podsieci wewnętrznej bazy danych, odpowiedzi jest dozwolona
6. Podsieci frontonu rozpoczyna przetwarzanie przychodzących reguł:
   1. Nie zasady grupy NSG, która ma zastosowanie do przychodzącego ruchu w podsieci wewnętrznej bazy danych do podsieci frontonu, aby żadna grupa NSG zasady stosowane
   2. Domyślna reguła system zezwala na ruch między podsieciami pozwala tego rodzaju ruch, ruch jest dozwolony.
7. Serwer usług IIS odbiera plik

#### <a name="denied-web-to-backend-server"></a>(*Odmowa*) do serwera wewnętrznej bazy danych w sieci Web
1. Użytkownik internet próbuje uzyskać dostęp do pliku na AppVM01 za pośrednictwem usługi BackEnd001.CloudApp.Net
2. Ponieważ nie ma żadnych punktów końcowych otwarty do udziału plików, tego rodzaju ruch nie przejdzie usługi w chmurze, a nie dociera do serwera
3. Jeśli punktów końcowych, które były otwarte jakiegoś powodu, reguły NSG 5 (Internet do sieci wirtualnej) czy zablokować ruch

#### <a name="denied-web-dns-look-up-on-dns-server"></a>(*Odmowa*) wyszukiwanie nazwy DNS w sieci Web na serwerze DNS
1. Internet próby odszukania wewnętrzny rekord DNS na DNS01 za pośrednictwem usługi BackEnd001.CloudApp.Net
2. Ponieważ nie ma żadnych punktów końcowych otwarte dla serwera DNS, tego rodzaju ruch nie przejdzie usługi w chmurze, a nie dociera do serwera
3. Jeśli punktów końcowych, które były otwarte jakiegoś powodu, reguły NSG 5 (Internet do sieci wirtualnej) czy zablokować ruch (Uwaga: nie ma zastosowania tej reguły 1 (DNS) z dwóch przyczyn, najpierw adresu źródłowego jest internet, ta reguła ma zastosowanie tylko do lokalnej sieci wirtualnej jako źródło, również ta reguła jest Reguła zezwalająca, nigdy nie będzie go odmówić ruchu)

#### <a name="denied-web-to-sql-access-through-firewall"></a>(*Odmowa*) sieci Web SQL dostępu przez zaporę
1. Użytkownik internet żąda danych SQL z FrontEnd001.CloudApp.Net (Internet ukierunkowane usługa w chmurze)
2. Ponieważ nie ma żadnych punktów końcowych otwarte dla bazy danych SQL, tego rodzaju ruch nie przejdzie usługi w chmurze i nie dotrzeć zapory
3. Jeśli punkty końcowe zostały otwarte jakiegoś powodu, podsieci frontonu rozpoczyna przetwarzanie przychodzącej reguły:
   1. Reguły NSG 1 DNS (Domain Name System) nie są spełnione, przejść do następnej reguły
   2. 2 reguły NSG (RDP) nie są spełnione, przejść do następnej reguły
   3. Zastosować grupy NSG zasady 3 (Internet w celu IIS01), ruch jest dozwolony, stop reguły przetwarzania
4. Wewnętrzny adres IP IIS01 trafienia ruchu (10.0.1.5)
5. IIS01 nie nasłuchuje na porcie 1433, więc nie ma odpowiedzi na żądanie

## <a name="conclusion"></a>Podsumowanie
W tym przykładzie jest stosunkowo proste i bezpośrednio do przodu sposobem izolowanie podsieci wewnętrznej z ruchu przychodzącego.

Więcej przykładów i Przegląd granic zabezpieczeń sieci można znaleźć [tutaj][HOME].

## <a name="references"></a>Dokumentacja
### <a name="main-script-and-network-config"></a>Główne config skryptu i sieci
Zapisz skrypt pełna w pliku skryptu programu PowerShell. Zapisz konfigurację sieci w pliku o nazwie "NetworkConf1.xml."
Modyfikuj zmienne zdefiniowane przez użytkownika, zgodnie z potrzebami, a następnie uruchom skrypt.

#### <a name="full-script"></a>Pełny skrypt
Ten skrypt zostanie, na podstawie zmiennych zdefiniowanych przez użytkownika;

1. Łączenie się z subskrypcją platformy Azure
2. Tworzenie konta magazynu
3. Tworzenie sieci wirtualnej z dwoma podsieciami, zgodnie z definicją w pliku konfiguracji sieci
4. Tworzenie maszyn wirtualnych serwera cztery systemu windows
5. Skonfiguruj grupy NSG w tym:
   * Utworzenie grupy NSG
   * Zapełnianie reguły
   * Powiązanie grupy NSG z odpowiednich podsieci

Ten skrypt programu PowerShell powinien zostać uruchomiony lokalnie na się, że połączenie internetowe, komputera lub serwera.

> [!IMPORTANT]
> Uruchomienie tego skryptu można ostrzeżenia lub inne komunikaty informacyjne, które pop w programie PowerShell. Tylko komunikaty o błędach na czerwono są przyczyną problemu.
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
Zapisz ten plik xml z lokalizacji zaktualizowane i dodać link do tego pliku do zmiennej $NetworkConfigFile w powyższy skrypt.

```XML
<NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
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
Jeśli chcesz zainstalować przykładową aplikację dla tego i innych przykłady DMZ, jeden podano przy użyciu następującego łącza: [przykładowy skrypt aplikacji][SampleApp]

## <a name="next-steps"></a>Kolejne kroki
* Aktualizowanie i Zapisz plik XML
* Uruchom skrypt programu PowerShell do tworzenia środowiska
* Instalowanie przykładowej aplikacji
* Testowanie różnych ruch za pośrednictwem tego DMZ

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-asm/example1design.png "Przychodzący DMZ z grupy NSG"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md

