---
title: Przykład sieci obwodowej — tworzenie sieci obwodowej do ochrony sieci z zapory, przez i NSG | Dokumentacja firmy Microsoft
description: Tworzenie sieci obwodowej za pomocą zapory, zdefiniowane przez użytkownika routingu (przez) i grupy zabezpieczeń sieci (NSG)
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: ''
ms.assetid: dc01ccfb-27b0-4887-8f0b-2792f770ffff
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: jonor;sivae
ms.openlocfilehash: fdb3c5cbd3acee90386352c6f180a71aa81f54fe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
---
# <a name="example-3--build-a-dmz-to-protect-networks-with-a-firewall-udr-and-nsg"></a>Przykład 3 — Tworzenie sieci obwodowej do ochrony sieci z zapory, przez i grupy NSG
[Wróć do strony zabezpieczeń granic najlepsze praktyki][HOME]

W tym przykładzie utworzy sieć obwodową z zapory, cztery serwery z systemem windows, użytkownik zdefiniowane routingu, przesyłania dalej protokołu IP i grup zabezpieczeń sieci. On również przeprowadzi wszystkich odpowiednich poleceń, aby zapewnić głębsze zrozumienie każdego kroku. Jest również sekcji scenariusza ruchu zapewnienie szczegółowe instrukcje, jak ruchu obejmującego warstw zabezpieczeń w sieci obwodowej. Ponadto w odwołaniach sekcja jest kompletny kod i instrukcje do tworzenia tego środowiska, aby przetestować i wypróbować różne scenariusze. 

![Dwukierunkowe sieci obwodowe NVA, NSG i przez][1]

## <a name="environment-setup"></a>Konfigurowanie środowiska
W tym przykładzie jest subskrypcji, która zawiera następujące elementy:

* Trzy usługi w chmurze: "SecSvc001", "FrontEnd001" i "BackEnd001"
* "CorpNetwork" przy użyciu trzech podsieci sieci wirtualnej: "SecNet", "Fronton" i "Wewnętrzna"
* Urządzenie wirtualne sieci, w tym przykładzie zaporą, połączony z podsiecią SecNet
* Windows Server, który reprezentuje serwer sieci web aplikacji ("IIS01")
* Dwa okna serwerów, które reprezentują aplikacji z powrotem kończyć serwery ("AppVM01", "AppVM02")
* Windows server, który reprezentuje serwer DNS ("DNS01")

W poniższej sekcji odwołań jest skrypt programu PowerShell, który zostanie skompilowany większość środowiska opisane powyżej. Tworzenie maszyn wirtualnych i sieci wirtualnych, chociaż są realizowane przez skrypt przykładowy nie opisano szczegółowo w tym dokumencie.

Aby utworzyć środowiska:

1. Zapisz plik xml konfiguracji sieci zawarte w sekcji odwołań (zaktualizowany o nazwy, lokalizacji i IP adresów do dopasowania danego scenariusza)
2. Zaktualizuj zmienne użytkownika w skrypcie, aby dopasować środowiska, które jest skrypt do uruchomienia przed (subskrypcje, nazwy usługi itp.)
3. Uruchom skrypt programu PowerShell

**Uwaga**: region oznaczony w skrypcie programu PowerShell musi odpowiadać oznaczony w pliku xml konfiguracji sieci.

Po pomyślnym uruchomieniu skryptu może podjąć następujące kroki skryptu po:

1. Konfigurowanie reguł zapory, ten temat znajdują się w sekcji poniżej: opis reguły zapory.
2. Opcjonalnie w sekcji odwołań są dwa skrypty do konfiguracji serwera sieci web oraz serwer aplikacji z prostą aplikację sieci web umożliwia testowanie za pomocą tej konfiguracji sieci obwodowej.

Po uruchomieniu skryptu pomyślnie zapory, zasady zostaną muszą zostać wykonane zostało to opisane w sekcji: reguł zapory.

## <a name="user-defined-routing-udr"></a>Zdefiniowane przez użytkownika routingu (przez)
Domyślnie następujące trasy systemowe są zdefiniowane jako:

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.0.0/16}     VNETLocal                            Active   Default    
         {0.0.0.0/0}       Internet                             Active   Default    
         {10.0.0.0/8}      Null                                 Active   Default    
         {100.64.0.0/10}   Null                                 Active   Default    
         {172.16.0.0/12}   Null                                 Active   Default    
         {192.168.0.0/16}  Null                                 Active   Default

VNETLocal jest zawsze prefiksy określonych adresów sieci wirtualnej dla tej sieci określonych (ie go ulegnie zmianie z sieci wirtualnej do sieci wirtualnej w zależności od tego, jak zdefiniowano każdej określonej sieci wirtualnej). Pozostałe tras systemowych są statyczne i domyślne jako powyżej.

Podobnie jak w przypadku priorytet trasy są przetwarzane przy użyciu metody najdłuższym prefiksu dopasowania (LPM), w związku z tym specyficzny trasy w tabeli powinna zostać zastosowana do podanego adresu.

W związku z tym w sieci wirtualnej do miejsca docelowego z powodu trasy 10.0.0.0/16 zostanie przekierowane ruchu (na przykład do serwera DNS01 10.0.2.4) przeznaczone dla sieci lokalnej (10.0.0.0/16). Innymi słowy 10.0.2.4, trasy 10.0.0.0/16 jest specyficzny trasy, mimo że 10.0.0.0/8 i 0.0.0.0/0 również można zastosować, ale ponieważ są one mniejsze określonych nie wpływają one na ten ruch. W związku z tym ruch do 10.0.2.4 mają następnego przeskoku lokalnej sieci wirtualnej i po prostu trasy do miejsca docelowego.

Jeśli ruch jest przeznaczona do 10.1.1.1, na przykład, trasy 10.0.0.0/16 nie zastosować, ale 10.0.0.0/8 będzie najbardziej konkretny i to ruch pomijane ("czarna holed"), ponieważ następnego przeskoku jest Null. 

Jeśli miejsce docelowe nie dotyczą prefiksy wartości Null ani prefiksy VNETLocal, a następnie wykonać najmniej określonej trasy, 0.0.0.0/0 oraz być kierowane z Internetem jako następnego przeskoku i w związku z tym out krawędzi internet platformy Azure.

Jeśli istnieją dwie identyczne prefiksów w tabeli tras, poniżej przedstawiono kolejność preferencji na podstawie atrybutu "source" tras:

1. "VirtualAppliance" = ręcznie dodawane do tabeli trasę zdefiniowany przez użytkownika
2. "Właściwość VPNGateway" = dynamiczny trasy protokołu BGP (gdy jest używany z sieci hybrydowe), dodane przez protokół dynamicznej sieci, te trasy może ulec zmianie jako protokół dynamicznej automatycznie uwzględnia zmiany w połączyć za pomocą sieci
3. "Default" = tras systemowych, lokalnej sieci wirtualnej i statyczne wpisy, jak pokazano w powyższej tabeli tras.

> [!NOTE]
> Użytkownik zdefiniowane routingu przez mogą teraz używać z usługi ExpressRoute i bram sieci VPN, aby wymusić wychodzące i przychodzące między lokalizacjami ruch będzie kierowany do sieci (NVA) urządzenia wirtualnego.
> 
> 

#### <a name="creating-the-local-routes"></a>Tworzenie tras lokalnych
W tym przykładzie są potrzebne dwie tabele routingu, jeden dla każdej podsieci frontonu i wewnętrznej bazy danych. Każda tabela została załadowana z tras statycznych, które są odpowiednie dla danej podsieci. Na potrzeby tego przykładu każda tabela zawiera trzy tras:

1. Definicja ruchu w podsieci lokalnej z następnego przeskoku zezwalająca na ruch podsieci lokalnej ominąć zaporę
2. Ruchu w sieci wirtualnej z przeskoku dalej zdefiniowany jako zapory, zastępuje domyślną regułę, umożliwiający lokalny ruch sieci wirtualnej do routingu bezpośredniego
3. Pozostałe cały ruch (0/0) z przeskoku dalej zdefiniowany jako zapory

Po utworzeniu tabele routingu są one powiązane z ich podsieci. Dla podsieci Frontend tabeli routingu raz utworzony i powiązany z podsiecią powinien wyglądać następująco:

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active 
         {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active


Na przykład poniższe polecenia są używane do kompilacji tabeli tras Dodaj trasy zdefiniowane przez użytkownika, a następnie powiązać tabeli tras do podsieci (Uwaga; wszystkie elementy poniżej rozpoczynający się od znaku dolara (np.: $BESubnet) są zmiennych zdefiniowanych przez użytkownika ze skryptu w Odwołanie do sekcji tego dokumentu):

1. Najpierw należy utworzyć podstawową tabelę routingu. Ta Wstawka kodu pokazano tworzenie tabeli podsieci wewnętrznej bazy danych. W skrypcie tworzona jest również odpowiedniego tabeli dla podsieci frontonu.
   
     Nowe AzureRouteTable-Name $BERouteTableName "
   
         -Location $DeploymentLocation `
         -Label "Route table for $BESubnet subnet"
2. Po utworzeniu tabeli tras można dodać trasy zdefiniowane przez określonego użytkownika. W tym przedstawiono cały ruch (0.0.0.0/0), zostaną przesłane przez urządzenie wirtualne (zmiennej $VMIP [0], używany do przekazywania adres IP przypisany podczas tworzenia urządzenia wirtualnego wcześniej w skrypcie). W skrypcie tworzona jest również odpowiednią regułę w tabeli frontonu.
   
     Get-AzureRouteTable $BERouteTableName | `
   
         Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
         -NextHopType VirtualAppliance `
         -NextHopIpAddress $VMIP[0]
3. Powyżej wejście dla trasy przesłoni "0.0.0.0/0" trasy domyślnej, ale domyślna reguła 10.0.0.0/16 nadal istniejące umożliwiające ruchu w sieci wirtualnej, aby kierować bezpośrednio do miejsca docelowego, a nie do urządzenia wirtualnego sieci. Aby poprawne to zachowanie reguły wykonaj musi zostać dodany.
   
        Get-AzureRouteTable $BERouteTableName | `
            Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
4. W tym momencie jest wybór ma zostać wykonane. Z powyższych na dwa sposoby cały ruch będzie przekierowywać do zapory w celu oceny, nawet ruch w ramach pojedynczej podsieci. Może to konieczne, jednak aby zezwolić na ruch w rozesłać lokalnie bez udziału z zapory innej podsieci, można dodać bardzo określona reguła. Ta trasa stanów, które dowolnego adresu destine dla podsieci lokalnej można po prostu kierować bezpośrednio (Typ następnego przeskoku = VNETLocal).
   
        Get-AzureRouteTable $BERouteTableName | `
            Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
            -NextHopType VNETLocal
5. Na koniec z tabeli routingu tworzone i wypełniane przy użyciu trasy zdefiniowane przez użytkownika, tabela musi teraz powiązać z podsiecią. W skrypcie frontonu tabelę tras jest także powiązany podsieci frontonu. Oto skryptu powiązanie dla podsieci wewnętrznej.
   
     Zestaw AzureSubnetRouteTable - VirtualNetworkName $VNetName "
   
        -SubnetName $BESubnet `
        -RouteTableName $BERouteTableName

## <a name="ip-forwarding"></a>Przesyłanie dalej IP
Funkcja pomocnika przez, jest przekazywanie adresów IP. To ustawienie na urządzenie wirtualne, które pozwala na odbieranie ruchu kierowanego nie specjalnie do urządzenia, a następnie przesyła ten ruch do końcowego miejsca docelowego.

Na przykład jeśli ruch z AppVM01 zgłasza żądanie do serwera DNS01 przez czy trasy to zapory. Z przesyłania dalej protokołu IP włączone ruchu dla miejsca docelowego DNS01 (10.0.2.4) zostanie zaakceptowane przez urządzenia (10.0.0.4) i następnie przekazywane do końcowego miejsca docelowego (10.0.2.4). Bez przekazywania adresów IP jest włączona Zapora ruch może nie zostać zaakceptowane przez urządzenia, mimo że tabeli tras ma zapory w następnym skoku. 

> [!IMPORTANT]
> Warto Pamiętaj, aby umożliwić przekazywanie adresów IP w połączeniu z routingiem zdefiniowane użytkownika.
> 
> 

Konfigurowanie przekazywania adresów IP jest jednego polecenia i może odbywać się w czasie tworzenia maszyny Wirtualnej. Przepływ w tym przykładzie fragment kodu jest na końcu skryptu i zgrupowana za pomocą polecenia przez:

1. Wywołanie wystąpienia maszyny Wirtualnej, które w tym przypadku jest Twoje urządzenie wirtualne zapory, a następnie włącz przesyłania dalej protokołu IP (Uwaga; dowolny element w czerwonym rozpoczynający się od znaku dolara (np.: $VMName[0]) jest zmienną zdefiniowanych przez użytkownika ze skryptu zamieszczone w tej sekcji tego dokumentu. Pierwsza maszyna wirtualna w tablicy maszyn wirtualnych, na przykład skryptu pracę bez żadnych modyfikacji reprezentuje zero w nawiasach [0], pierwsza maszyna wirtualna (VM 0) musi być zapory):
   
     Get-AzureVM-Name $VMName [0] - ServiceName $ServiceName [0] | `
   
        Set-AzureIPForwarding -Enable

## <a name="network-security-groups-nsg"></a>Sieciowe grupy zabezpieczeń (NSG)
W tym przykładzie grupa NSG jest wbudowana i następnie ładowane przy użyciu jednej reguły. Ta grupa jest następnie powiązać tylko z podsieci frontonu i wewnętrznej bazy danych (nie SecNet). Deklaratywnie budowanego następującą regułę:

1. Odmowa cały ruch (wszystkie porty) z Internetu do całej sieci wirtualnej (wszystkie podsieci)

Mimo że grupy NSG są używane w tym przykładzie, jego głównym celem jest jako dodatkowej warstwy obrony przed ręcznej konfiguracji. Chcemy, aby zablokować wszystkie przychodzący ruch z Internetu do każdego serwera sieci Web, lub podsieci wewnętrznej bazy danych, ruch powinny przepływać tylko za pośrednictwem podsieci SecNet do zapory (i następnie jeśli odpowiednią frontonu lub wewnętrznej bazy danych podsieci). Plus przy użyciu reguł przez w miejscu, dowolnego ruchu, który należy do podsieci frontonu lub wewnętrznej bazy danych może być kierowane limit do zapory (dzięki użyciu przez). Zapora będzie traktować jako asymetrycznego przepływu i spowoduje pominięcie ruchu wychodzącego. W związku z tym są trzy warstwy zabezpieczeń, ochrony podsieci frontonu i wewnętrznej bazy danych; 1) otwórz punktów końcowych na FrontEnd001 i BackEnd001 usługi w chmurze, 2) grupy NSG zezwalających na ruch z Internetu, 3) zapory usunięcie asymetrycznego ruchu.

Jeden punkt interesujące dotyczące grupy zabezpieczeń sieci, w tym przykładzie jest, że zawiera on tylko jedną regułę, pokazano poniżej, która jest odrzucanie ruch internetowy do całej sieci wirtualnej, obejmujące podsieci zabezpieczeń. 

    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet `
        from the Internet" `
        -Type Inbound -Priority 100 -Action Deny `
        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
        -DestinationAddressPrefix VIRTUAL_NETWORK `
        -DestinationPortRange '*' `
        -Protocol *

Jednak ponieważ grupa NSG jest powiązany tylko z podsieci frontonu i zaplecza, reguła nie jest przetwarzana na ruch przychodzący z podsiecią zabezpieczeń. W związku z tym mimo że reguły NSG mówi nie ruch internetowy do dowolnego adresu w sieci wirtualnej, ponieważ grupa NSG nigdy nie został powiązany z podsiecią zabezpieczeń, ruch będzie przepływać z podsiecią zabezpieczeń.

    Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
        -SubnetName $FESubnet -VirtualNetworkName $VNetName

    Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
        -SubnetName $BESubnet -VirtualNetworkName $VNetName

## <a name="firewall-rules"></a>Reguły zapory
Zapory zasady przekazywania zostanie muszą zostać utworzone. Ponieważ Zapora jest blokowanie lub funkcji przekazywania wszystkich przychodzące, wychodzące, sieć wirtualną wewnątrz ruchu wiele reguł zapory są wymagane. Ponadto cały ruch przychodzący nastąpi trafienie usługi zabezpieczeń publicznego adresu IP (na różnych portów), do przetworzenia przez zaporę. Najlepszym rozwiązaniem jest diagram logiczny przepływów przed rozpoczęciem konfigurowania podsieci i reguły zapory, aby uniknąć zmian później. Poniższa ilustracja jest widok logiczny reguły zapory, w tym przykładzie:

![Widok logiczny reguł zapory][2]

> [!NOTE]
> Oparte na urządzenie wirtualne sieci używana, porty zarządzania będą się różnić. W tym przykładzie, który odwołuje się do zapory NextGen Barracuda, który używa portów 22, 801 i 807. Zapoznaj się z dokumentacją dostawcy urządzenia można znaleźć odpowiednie porty używane do zarządzania urządzeniami używana.
> 
> 

### <a name="logical-rule-description"></a>Opis reguły logiczne
Na powyższym diagramie logiczne podsieci zabezpieczeń nie jest wyświetlane, ponieważ Zapora jest tylko zasobów tej podsieci, a ten diagram jest wyświetlany reguły zapory i sposób ich logicznie akceptować lub odrzucać przepływów ruchu sieciowego i nie rzeczywistej ścieżce routingiem. Ponadto zewnętrzne porty wybrany na potrzeby ruchu protokołu RDP są wyższe ranged portów (8014 — 8026) i wybranych do nieco wyrównany z ostatnich dwóch oktety lokalny adres IP dla czytelności łatwiejsze (np. adres serwera lokalnego 10.0.1.4 jest skojarzony z portu zewnętrznego 8014), jednak można użyć żadnych wyższej niekolidujących portów.

Na przykład potrzebujemy 7 typów reguł, te typy reguł są opisane w następujący sposób:

* Reguły zewnętrzne (dla ruchu przychodzącego):
  1. Zarządzanie reguła: Ta reguła przekierowania aplikacji zezwala na ruch do przekazania do portów zarządzania urządzenie wirtualne sieci.
  2. Zasady protokołu RDP (na każdym serwerze z systemem windows): następujące cztery reguły (po jednym dla każdego serwera) umożliwi Zarządzanie poszczególnych serwerów za pomocą protokołu RDP. Może to również być połączone w jedną regułę w zależności od możliwości urządzenie wirtualne sieci używane.
  3. Reguły ruchu aplikacji: Istnieją dwie reguły ruchu aplikacji pierwszy dla ruchu w sieci web frontonu i drugą dla ruchu zaplecza (np serwera sieci web do warstwy danych). Konfiguracja te reguły będzie zależą od architektury sieci (w którym są umieszczane serwerów) i przepływów ruchu (kierunku przepływów ruchu sieciowego i portów, do których są używane).
     * Pierwsza reguła będzie zezwalać na ruch rzeczywisty aplikacji do serwera aplikacji. Inne zasady Zezwalaj na zabezpieczenia, zarządzanie, itp., reguły aplikacji są co Zezwalaj użytkowników zewnętrznych lub usług, uzyskać dostęp do aplikacji. Na przykład jest jednym serwerze sieci web na porcie 80, w związku z tym pojedynczą aplikacji regułę zapory przekierować ruch przychodzący do zewnętrznego adresu IP do sieci web serwerów wewnętrzny adres IP. Sesja przekierowywania ruchu czy NAT się pod uwagę wewnętrznego serwera.
     * Drugą regułę ruchu aplikacji jest wewnętrzna reguły zezwalającej na serwerze sieci Web komunikował się z serwerem AppVM01 (ale nie AppVM02) za pomocą dowolnego portu.
* Wewnętrzne zasady (dla ruchu w sieci wirtualnej wewnątrz)
  1. Ruch wychodzący do Internetu reguły: Ta reguła będzie zezwalać na ruch z żadną siecią do przekazania do wybranych sieci. Ta reguła jest zwykle domyślna reguła już w zaporze, ale w stanie wyłączenia. Ta reguła powinna być włączona w tym przykładzie.
  2. Reguła DNS: Ta reguła zezwala tylko na ruch DNS (port 53) do przekazania do serwera DNS. Dla tego środowiska, większość ruchu z serwera sieci Web do wewnętrznej bazy danych jest zablokowany ta zasada umożliwia DNS w szczególności z żadnych podsieci lokalnej.
  3. Reguła podsieci do sieci: Ta reguła jest umożliwienie dowolnego serwera w podsieci wewnętrznej w celu połączenia z serwerem z podsieci frontonu (ale nie odwrotnie).
* Reguła awaryjnego (dla ruchu, który nie spełnia żadnego z powyższych):
  1. Odmów wszystkie reguły ruchu: To powinna zawsze być końcowego reguły (pod względem priorytetu), a jako takie Jeśli przepływa ruch nie pasuje do żadnej z powyższych zasad, które zostaną usunięte przez tę regułę. Jest to domyślna reguła i zwykle jest aktywna, żadnych modyfikacji nie są zwykle wymagane.

> [!TIP]
> Na drugiej reguły ruchu aplikacji każdy port jest dozwolone w przypadku łatwy w tym przykładzie w rzeczywistym scenariuszu najbardziej określonego portu i zakresy adresów mają być używane do zmniejszyć obszar ataków tej reguły.
> 
> 

<br />

> [!IMPORTANT]
> Po utworzeniu wszystkich powyższych reguł, należy przejrzeć priorytet każdej reguły, aby upewnić się, ruch ma być dozwolony lub odrzucany zgodnie z potrzebami. Na przykład reguły są w kolejności priorytetu. Jest łatwy do zostanie zablokowana z powodu nieprawidłowo uporządkowane reguły zapory. Co najmniej upewnij się, że zarządzania zapory sam jest zawsze bezwzględną reguły priorytet najwyższy.
> 
> 

### <a name="rule-prerequisites"></a>Reguły wymagań wstępnych
Jeden wstępnie wymagane oprogramowanie dla maszyny wirtualnej uruchomionej zapory są publiczne punkty końcowe. Zapory do przetwarzania ruchu sieciowego odpowiednie publiczne punkty końcowe musi być otwarty. Istnieją trzy typy ruchu w tym przykładzie; 1) ruch związany z zarządzaniem kontrola zapory i reguł zapory, 2) na ruch RDP do kontrolowania serwerów z systemem windows, a ruch 3) aplikacji. Są trzy kolumny typów ruchu w prawym górnym połowy widok logiczny reguł zapory powyżej.

> [!IMPORTANT]
> Należy pamiętać, że jest tutaj klucza takeway **wszystkie** ruchu rozpocznie się za pośrednictwem zapory. Tak do pulpitu zdalnego na serwerze IIS01, nawet jeśli jest już w usłudze w chmurze Front End i podsieci frontonu, dostępu do tego serwera firma Microsoft będzie muszą dla protokołu RDP w zaporze na porcie 8014, a następnie zezwalać wewnętrznie trasy żądania protokołu RDP z portem RDP IIS01 zapory. Przycisk "Połącz" portalu Azure nie będzie działać, ponieważ nie istnieje żadne bezpośrednią ścieżkę RDP IIS01 (o ile portalu można zobaczyć). Oznacza to, że wszystkie połączenia z Internetem będzie usługi zabezpieczeń i Port, np. secscv001.cloudapp.net:xxxx (odwołanie w schemacie powyżej mapowanie portu zewnętrznego i wewnętrznym adresem IP i Port).
> 
> 

Punkt końcowy można otworzyć zarówno w czasie tworzenia maszyny Wirtualnej lub po kompilacji jest przeprowadzane w przykładowy skrypt i przedstawionym poniżej na następujący fragment kodu (Uwaga; dowolnego elementu, począwszy od znak dolara (np.: $VMName[$i]) jest zmienną zdefiniowanych przez użytkownika ze skryptu zamieszczone w tej sekcji tego dokumentu. "$I" w nawiasach [$i] reprezentuje liczbę tablicy określonej maszyny Wirtualnej w tablicy maszyn wirtualnych):

    Add-AzureEndpoint -Name "HTTP" -Protocol tcp -PublicPort 80 -LocalPort 80 `
        -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | `
        Update-AzureVM

Mimo że nie wyraźnie przedstawiony tutaj ze względu na użycie zmiennych, ale punkty końcowe są **tylko** otworzyć zabezpieczeń usługi w chmurze. Pozwala to zapewnić, że cały ruch przychodzący jest obsługiwane (kierowane, translatora adresów Sieciowych porzucone) przez zaporę.

Klienta zarządzania musi być zainstalowana na komputerze Zarządzanie zaporą i tworzyć konfiguracje wymagane. Można znaleźć dostawcy dokumentacji zapory (lub inne NVA) do zarządzania urządzeniem. W pozostałej części tej sekcji i kolejnej sekcji, tworzenie reguł zapory, opisano konfigurację zapory, za pomocą klienta zarządzania dostawców (tj. portalu Azure lub programu PowerShell).

Instrukcje dotyczące pobierania klienta i nawiązywania połączenia z Barracuda używana w tym przykładzie można znaleźć tutaj: [Barracuda NG administratora](https://techlib.barracuda.com/NG61/NGAdmin)

Po zalogowaniu się na zaporze, ale przed utworzeniem reguły zapory, istnieją dwie klasy obiektu wymagań wstępnych, które mogą ułatwić tworzenie reguły łatwiejsze. Obiekty sieci i usługi.

W tym przykładzie trzy obiekty nazwanej sieci powinien być zdefiniowany (jeden dla podsieci frontonu i podsieci wewnętrznej bazy danych, a także obiekt sieci adresu IP serwera DNS). Aby utworzyć sieć o nazwie; począwszy od klienta pulpitu nawigacyjnego Barracuda NG administratora, przejdź na kartę Konfiguracja, w sekcji konfiguracji operacyjnych kliknij zestaw reguł, kliknij przycisk "Sieci" w menu obiekty zapory, a następnie kliknij przycisk Nowa, w menu Edycja sieci. Obiekt sieci teraz można utworzyć, dodając nazwy i prefiksu:

![Utwórz obiekt sieci frontonu][3]

Spowoduje to utworzenie nazwanej sieci dla podsieci frontonu, podobne obiekt powinien zostać utworzony dla tej podsieci wewnętrznej bazy danych. Teraz podsieci można łatwiej odwoływać się nazwa reguły zapory.

Dla obiekt serwera DNS:

![Utwórz obiekt serwera DNS][4]

Ten jedno odwołanie adres IP zostanie wykorzystana w regule DNS w dalszej części dokumentu.

Drugi obiekty wymagań wstępnych są obiektami usługi. Te będą stanowiły portów połączeń protokołu RDP dla każdego serwera. Ponieważ istniejący obiekt usługi protokołu RDP jest powiązany z domyślnym portem RDP, 3389, utworzyć nowych usług można zezwalać na ruch z zewnętrznego portów (8014 8026). Nowych portów można również dodać do istniejącej usługi protokołu RDP, ale w celu ułatwienia demonstracyjnych, można utworzyć regułę dla każdego serwera. Aby utworzyć nową regułę protokołu RDP na serwerze. począwszy od klienta pulpitu nawigacyjnego Barracuda NG administratora, przejdź na kartę Konfiguracja, w sekcji konfiguracji operacyjnych kliknij zestaw reguł, a następnie kliknij przycisk "Usługi" w menu obiekty zapory, przewiń w dół listę usług i wybierz usługę "RDP". Kliknij prawym przyciskiem myszy i wybierz Kopiuj, a następnie kliknij prawym przyciskiem myszy i wybierz Wklej. Teraz jest obiektem usługi RDP Copy1, które mogą być edytowane. Kliknij prawym przyciskiem myszy RDP Copy1 i wybierz polecenie Edytuj, usługa Edytuj obiekt okna punktu obecności się, jak pokazano poniżej:

![Kopiowanie reguły domyślnej protokołu RDP][5]

Następnie można edytować wartości do reprezentowania usługi protokołu RDP dla określonego serwera. Dla AppVM01 powyżej domyślną regułę RDP powinno zostać zmodyfikowane w celu uwzględnienia nowej nazwy usługi, opis i portu zewnętrznego RDP używany na rysunku 8 diagramie (Uwaga: porty nie zostaną zmienione z domyślnych RDP 3389 na porcie zewnętrznym używane dla tego określonego serwera, w przypadku AppVM01 porcie zewnętrznym jest 8025) zmodyfikowane usługi są wyświetlane poniżej :

![Reguła AppVM01][6]

Ten proces powtarza się do tworzenia usług protokołu RDP dla pozostałych serwerów; AppVM02 DNS01 i IIS01. Tworzenie tych usług będzie tworzenia reguły prostszy i bardziej oczywistymi w następnej sekcji.

> [!NOTE]
> Usługa protokołu RDP dla zapory nie jest wymagana dwóch powodów; 1) pierwsze zapory maszyny Wirtualnej jest obrazem opartymi na systemie Linux, więc SSH będzie używany port 22 na potrzeby zarządzania maszyny Wirtualnej zamiast protokołu RDP, a port 22 2) i dwa porty zarządzania są dozwolone w pierwszej regule zarządzania opisane poniżej, aby umożliwić łączność w procesie zarządzania.
> 
> 

### <a name="firewall-rules-creation"></a>Tworzenie reguły zapory
Istnieją trzy typy reguł zapory, w tym przykładzie, wszystkie mają różne ikony:

Reguła przekierowania aplikacji: ![przekierowania ikona aplikacji][7]

Reguła NAT docelowego: ![docelowym translacji adresów Sieciowych ikony][8]

Reguła przebiegu: ![Przekaż ikonę][9]

Więcej informacji na temat tych zasad można znaleźć w witrynie sieci web Barracuda.

Utworzenie następujących reguł (lub sprawdź, czy istniejących reguł domyślnych), począwszy od pulpitu nawigacyjnego klienta Barracuda NG administratora, przejdź do karty konfiguracji, w konfiguracji operacyjnej sekcji kliknij zestaw reguł. Siatki o nazwie "Main reguły" wyświetli istniejących reguł active i zdezaktywowane tej zapory. W prawym górnym rogu tej siatki jest mały, zielony "+", kliknij tutaj, aby utworzyć nową regułę (Uwaga: Zapora może być "zablokowana" zmian, jeśli widoczny przycisk oznaczony jako "Lock" i nie można utworzyć lub edytować reguły, kliknij ten przycisk "Odblokuj" zestaw reguł i Zezwól na edytowanie). Jeśli chcesz edytować istniejącą regułę, wybierz reguły, kliknij prawym przyciskiem myszy i wybierz pozycję Edytuj regułę.

Gdy reguły są tworzone i/lub zmodyfikowane, musi być przypisany do zapory i następnie aktywowany, jeśli nie zostanie to zrobione zmian reguły nie zacznie obowiązywać. Proces wypychania i aktywacji jest opisany poniżej opisy reguł Szczegóły.

Szczegółowe informacje na temat każdej reguły wymagane do wykonania w tym przykładzie są opisane w następujący sposób:

* **Zarządzanie reguły zapory**: ten przekierowania aplikacji reguła zezwala na ruch do przekazania do portów zarządzania urządzenie wirtualne sieci, w tym przykładzie zapory NextGen Barracuda. Porty zarządzania są 801, 807 i opcjonalnie 22. Wewnętrzne i zewnętrzne porty są takie same (tzn. bez translacji port). Reguły i ustawienia dostępu do danych, reguła domyślna i domyślnie włączona (w wersji zapory NextGen Barracuda 6.1).
  
    ![Reguły zapory zarządzania][10]

> [!TIP]
> Przestrzeń adresowa źródła w tej regule jest, jeżeli zakresów adresów IP zarządzania są znane, zmniejszenie ten zakres będzie również zmniejszyć obszar ataków na porty zarządzania.
> 
> 

* **Zasady protokołu RDP**: reguł NAT przeznaczenia tych umożliwi Zarządzanie poszczególnych serwerów za pomocą protokołu RDP.
  Istnieją cztery pola krytyczne potrzebne do utworzenia tej reguły:
  
  1. Źródło — Zezwalaj na RDP z dowolnego miejsca, odwołanie "Dowolne" jest używany w polu źródło.
  2. Usługi — użyj odpowiedniego obiektu usługi utworzonego wcześniej, w tym przypadku "AppVM01 RDP", zewnętrzne porty przekierowania do serwerów lokalny adres IP i port 3386 (domyślny port protokołu RDP). Jest to określona reguła dostępu RDP do AppVM01.
  3. Miejsce docelowe — powinien być *lokalnych portów w zaporze*, "DCHP 1 lokalny adres IP" lub eth0 używania statycznych adresów IP. Numer porządkowy (eth0, eth1 itp.) może się różnić, jeśli urządzenia sieciowe ma wiele interfejsów lokalnych. Jest to port zapory wysyła z (może być taki sam jak port odbierania), rzeczywista routingiem miejsce docelowe jest w polu listy docelowej.
  4. Przekierowanie — w tej sekcji informuje urządzenie wirtualne gdzie ostatecznie przekierować ruch. Najprostsza przekierowania jest umieszczenie adresów IP i Port (opcjonalnie) w polu listy docelowej. Jeśli port nie jest używany port docelowy dla ruchu przychodzącego żądania będzie używany (ie nie tłumaczenie), jeśli port jest wybrany port będzie również NAT oraz adres IP zajmie się.
     
     ![Reguły zapory protokołu RDP][11]
     
     Łącznie cztery reguły protokołu RDP potrzebne do utworzenia: 
     
     | Nazwa reguły | Serwer | Usługa | Listy docelowej |
     | --- | --- | --- | --- |
     | RDP-IIS01 |IIS01 |IIS01 PROTOKOŁU RDP |10.0.1.4:3389 |
     | RDP-DNS01 |DNS01 |DNS01 PROTOKOŁU RDP |10.0.2.4:3389 |
     | RDP-AppVM01 |AppVM01 |AppVM01 protokołu RDP |10.0.2.5:3389 |
     | RDP-AppVM02 |AppVM02 |AppVm02 protokołu RDP |10.0.2.6:3389 |

> [!TIP]
> Zawężanie zakresu pola źródłowego i usług w dół zmniejsza podatność na ataki. Najbardziej ograniczonym zakresie, co umożliwi funkcji powinny być używane.
> 
> 

* **Reguły ruchu aplikacji**: istnieją dwie reguły ruchu aplikacji pierwszy dla ruchu w sieci web frontonu i drugą dla ruchu zaplecza (np serwera sieci web do warstwy danych). Te zasady będą zależą od architektury sieci (w którym są umieszczane serwerów) i przepływów ruchu (kierunku przepływów ruchu sieciowego i portów, do których są używane).
  
    Najpierw omówiona jest reguła frontonu dla ruchu w sieci web:
  
    ![Reguła zapory w sieci Web][12]
  
    Ta reguła NAT docelowym zezwala na ruch rzeczywisty aplikacji do serwera aplikacji. Inne zasady Zezwalaj na zabezpieczenia, zarządzanie, itp., reguły aplikacji są co Zezwalaj użytkowników zewnętrznych lub usług, uzyskać dostęp do aplikacji. Na przykład jest jednym serwerze sieci web na porcie 80, w związku z tym reguły zapory jednej aplikacji przekierować ruch przychodzący do zewnętrznego adresu IP do sieci web serwerów wewnętrzny adres IP.
  
    **Uwaga**: czy istnieje nie portu w polu listy docelowej przypisany, w związku z tym przychodzący port 80 (lub 443 dla wybranej usługi) będzie używany w przekierowywanie serwera sieci web. Jeśli serwer sieci web nasłuchuje na innym porcie, na przykład portu 8080, pole listy docelowej może zostać zaktualizowana do 10.0.1.4:8080, aby umożliwić na przekierowanie portu.
  
    Następny reguły ruchu aplikacji jest wewnętrzna reguły zezwalającej na serwerze sieci Web komunikował się z serwerem AppVM01 (ale nie AppVM02) za pośrednictwem usługi:
  
    ![Reguły zapory AppVM01][13]
  
    Ta zasada przebiegu umożliwia dowolnego serwera IIS w podsieci frontonu w celu osiągnięcia AppVM01 (adres IP 10.0.2.5) na dowolnym porcie przy użyciu protokołu uzyskują dostęp do danych wymaganych przez aplikację sieci web.
  
    W tym zrzucie ekranu "\<jawne dest\>" jest używany w polu docelowym oznaczającego 10.0.2.5 jako miejsca docelowego. Może to być albo jawne pokazany lub a o nazwie obiektu Network (jak to zostało zrobione w wymaganiach wstępnych dotyczących serwera DNS). Jest to administratora zapory, które zostanie użyta metoda. Aby dodać 10.0.2.5 jako Explict Desitnation, kliknij dwukrotnie pierwszy pusty wiersz pod \<jawne dest\> i podaj adres w wyskakującym oknie.
  
    Z tą regułą przekazać odłączenia translatora adresów Sieciowych jest niezbędne, ponieważ jest to wewnętrzny ruchu, dlatego metodę połączenia może mieć wartości "No SNAT".
  
    **Uwaga**: sieć źródłową w tej regule jest dowolnego zasobu w podsieci frontonu, jeśli będą tylko jedną lub znanych określoną liczbę serwerów sieci web, obiekt sieci można utworzyć zasobu dokładniej do dokładnego adresów IP zamiast w całej podsieci frontonu.

> [!TIP]
> Ta reguła jest używana usługa "Any" przykładowej aplikacji ułatwiające skonfigurować i stosować, umożliwi to również ICMPv4 (ping) w jednej reguły. Jednak to nie jest zalecanym rozwiązaniem. Porty i protokoły ("usługi") powinny zostać zawężony do minimum, możliwe umożliwiający operacji aplikacji zmniejszyć obszar ataków tej granicy.
> 
> 

<br />

> [!TIP]
> Mimo że ta zasada zawiera odwołania do jawnego dest używany, spójnego podejścia powinny być używane w konfiguracji zapory. Zaleca się, że nazwanego obiektu sieci używanego w dla czytelności łatwiejsze i obsługi. Jawne dest służy tutaj pokazanie metody odniesienia i zazwyczaj nie jest zalecane (szczególnie w przypadku złożonych konfiguracji).
> 
> 

* **Ruch wychodzący do Internetu reguły**: przekazania tej reguły będzie zezwalać na ruch z żadną siecią źródłowego do przekazania do wybranej sieci docelowych. Ta reguła jest domyślna reguła zwykle już na zaporze Barracuda NextGen, ale jest w stanie wyłączenia. Kliknięcie prawym przyciskiem myszy tę regułę mają dostęp do polecenia uaktywnić reguły. Reguła pokazane została zmodyfikowana, aby dodać dwie podsieci lokalnej, które zostały utworzone jako odwołania w sekcji wymagań wstępnych tego dokumentu z atrybutem źródłowym tej reguły.
  
    ![Reguła ruchu wychodzącego][14]
* **Reguła DNS**: przekazać ta reguła zezwala tylko na ruch DNS (port 53) do przekazania do serwera DNS. Dla tego środowiska, większość ruchu z serwera sieci Web do wewnętrznej bazy danych jest zablokowany ta zasada umożliwia w szczególności DNS.
  
    ![Reguły zapory DNS][15]
  
    **Uwaga**: na tym ekranie jest dołączony zrzut metodę połączenia. Ponieważ ta reguła ma wewnętrzny adres IP ruch wewnętrzny adres IP, NATing nie jest wymagane, to metodę połączenia jest równa "No SNAT" dla tej reguły przebiegu.
* **Reguła podsieci do sieci**: przekazać ta reguła jest domyślna reguła, która została aktywowana i zmodyfikowane, aby każdy serwer w podsieci zaplecza w celu połączenia z serwerem w podsieci frontonu. Ta reguła jest ruchu wszystkie wewnętrznej tak SNAT nie można ustawić metodę połączenia.
  
    ![Reguła sieci wirtualnej wewnątrz zapory][16]
  
    **Uwaga**: pole wyboru dwukierunkowe nie zaznaczono (ani nie jest zaznaczone w większości reguł), to ma znaczenie dla tej reguły, ponieważ ułatwia to reguły "w jedną stronę", można zainicjować połączenia z podsieci wewnętrznej sieci frontonu, ale nie odwrotnie. Jeśli pole wyboru zostało zaznaczone, ta zasada umożliwiłyby ruch dwukierunkowy, który z naszych diagram logiczny nie jest wymagana.
* **Odmów wszystkie reguły ruchu**: zawsze powinien to być ostateczne reguły (pod względem priorytet) i jako taki Jeśli ruch przepływa nie pasuje do żadnego z poprzednim zasady zostaną usunięte przez tę regułę. Jest to domyślna reguła i zwykle jest aktywna, żadnych modyfikacji nie są zwykle wymagane. 
  
    ![Zapora regułę Odmów][17]

> [!IMPORTANT]
> Po utworzeniu wszystkich powyższych reguł, należy przejrzeć priorytet każdej reguły, aby upewnić się, ruch ma być dozwolony lub odrzucany zgodnie z potrzebami. Na przykład reguły są w kolejności ich powinny być wyświetlane w siatce Main przesyłania reguły w kliencie zarządzania Barracuda.
> 
> 

## <a name="rule-activation"></a>Reguły aktywacji
Z ruleset zmodyfikowane w specyfikacji diagram logiki zestaw reguł należy przekazać do zapory i następnie aktywowany.

![Aktywacja reguły zapory][18]

W prawym górnym rogu klienta zarządzania są klastra przycisków. Kliknij przycisk "Wyślij zmiany", aby wysłać zmodyfikowane reguły zapory, a następnie kliknij przycisk "Uaktywnij".

W aktywacji zestaw reguł zapory tej kompilacji w środowisku przykładzie zostanie zakończona.

## <a name="traffic-scenarios"></a>Scenariusze ruchu
> [!IMPORTANT]
> Takeway klucza ma należy pamiętać, że **wszystkie** ruchu rozpocznie się przez zaporę. Tak do pulpitu zdalnego na serwerze IIS01, nawet jeśli jest już w usłudze w chmurze Front End i podsieci frontonu, dostępu do tego serwera firma Microsoft będzie muszą dla protokołu RDP w zaporze na porcie 8014, a następnie zezwalać wewnętrznie trasy żądania protokołu RDP z portem RDP IIS01 zapory. Przycisk "Połącz" portalu Azure nie będzie działać, ponieważ nie istnieje żadne bezpośrednią ścieżkę RDP IIS01 (o ile portalu można zobaczyć). Oznacza to, że wszystkie połączenia z Internetem będzie usługi zabezpieczeń i Port, np. secscv001.cloudapp.net:xxxx.
> 
> 

W tych sytuacjach następujące reguły zapory powinny być stosowane:

1. Zarządzanie zaporą
2. RDP do IIS01
3. RDP do DNS01
4. RDP do AppVM01
5. RDP do AppVM02
6. Aplikacja ruchu w sieci Web
7. Ruch aplikacji do AppVM01
8. Ruch wychodzący do Internetu
9. Frontonu DNS01
10. Wewnątrz podsieci ruchu (zaplecza do tylko frontonu)
11. Odmów wszystkich

Zestaw reguł zapory rzeczywiste najprawdopodobniej będzie miał wiele reguł oprócz tych, reguły na dowolnym danym zapory będą także mieć różne numery niż wymienione w tym miejscu. Tej listy i skojarzone z nimi numery są zapewnienie istotność między tylko te reguły 11 i względny priorytet jeden z nich. Innymi słowy; na zaporze rzeczywiste "RDP do IIS01" może być numer reguły 5, ale tak długo, jak jest poniżej reguły "Zarządzanie zaporą" i powyżej reguły "RDP DNS01" czy Dopasuj z tej listy. Lista będzie również pomóc w poniższych scenariuszy, dzięki czemu skrócenia; np. "Reguła Zapory 9 (DNS)". Jednak cztery reguły protokołu RDP będzie można zbiorczo skrót, "reguły protokołu RDP" gdy scenariusz ruchu sieciowego jest związana z RDP.

Odwołaj również, czy grupy zabezpieczeń sieci w miejscu dla ruchu przychodzącego internet podsieci frontonu i wewnętrznej bazy danych.

#### <a name="allowed-internet-to-web-server"></a>(Dozwolone) Internet do serwera sieci Web
1. Strony internetowe użytkownika żądania HTTP z SecSvc001.CloudApp.Net (Internet ukierunkowane usługa w chmurze)
2. Chmury usługi przekazuje ruch przez otwarty punkt końcowy na porcie 80 do interfejsu zapory 10.0.0.4:80
3. Nie NSG przypisane do podsieci zabezpieczeń, tak reguły NSG systemu zezwolić na ruch do zapory
4. Ruch trafienia wewnętrzny adres IP zapory (10.0.1.4)
5. Zapora rozpoczyna przetwarzanie reguł:
   1. PD reguła 1 (PD Mgmt) nie są spełnione, przejść do następnej reguły
   2. Reguły Zapory 2-5 (zasad protokołu RDP) nie zastosować, przejść do następnej reguły
   3. PD zasady 6 (aplikacji: sieci Web) zastosować, ruch jest dozwolony, zapory NAT go do 10.0.1.4 (IIS01)
6. Podsieci frontonu rozpoczyna przetwarzanie przychodzących reguł:
   1. 1 reguły NSG (Zablokuj Internet) nie ma zastosowania (ruch został NAT czy przez zaporę, w związku z tym adres źródłowy jest teraz zaporą, który znajduje się w podsieci zabezpieczeń i widoczne dla podsieci Frontend NSG za ruch "lokalnie", w związku z tym jest dozwolone), przejść do następnej reguły
   2. Reguły NSG domyślne zezwolić na ruch podsieci do sieci, ruch jest dozwolony, Zatrzymaj przetwarzanie reguł NSG
7. IIS01 nasłuchuje ruchu w sieci web, otrzymuje tego żądania i rozpoczyna przetwarzanie żądania
8. Próby IIS01 inicjuje sesję FTP do AppVM01 podsieci wewnętrznej bazy danych
9. Trasy przez podsieci frontonu sprawia, że Zapora następnego skoku
10. Nie reguł dla ruchu wychodzącego w podsieci frontonu, ruch jest dozwolony
11. Zapora rozpoczyna przetwarzanie reguł:
    1. PD reguła 1 (PD Mgmt) nie są spełnione, przejść do następnej reguły
    2. Reguły Zapory 2-5 (zasad protokołu RDP) nie są spełnione, przejść do następnej reguły
    3. PD zasady 6 (aplikacji: sieci Web) nie są spełnione, przejść do następnej reguły
    4. PD zasady 7 (aplikacji: wewnętrznej bazy danych) jest stosowana, ruch jest dozwolony, zapora przekazuje ruch do 10.0.2.5 (AppVM01)
12. Podsieci wewnętrznej bazy danych rozpoczyna się przetwarzanie przychodzących reguł:
    1. Grupy NSG reguła 1 (Zablokuj Internet) nie są spełnione, przejść do następnej reguły
    2. Reguły NSG domyślne zezwolić na ruch podsieci do sieci, ruch jest dozwolony, Zatrzymaj przetwarzanie reguł NSG
13. AppVM01 odbiera żądanie i inicjuje sesję i odpowiada
14. Trasy przez podsieci wewnętrznej bazy danych powoduje, że Zapora następnego skoku
15. Ponieważ nie ma żadnych wychodzących reguł NSG podsieci wewnętrznej bazy danych, odpowiedzi jest dozwolone.
16. Ponieważ to zwraca ruchu na ustanowienie sesji zapora przekazuje odpowiedź z powrotem do serwera sieci web (IIS01)
17. Podsieci frontonu rozpoczyna przetwarzanie przychodzących reguł:
    1. Grupy NSG reguła 1 (Zablokuj Internet) nie są spełnione, przejść do następnej reguły
    2. Reguły NSG domyślne zezwolić na ruch podsieci do sieci, ruch jest dozwolony, Zatrzymaj przetwarzanie reguł NSG
18. Serwer usług IIS odbiera odpowiedź, wykonuje transakcję za pomocą AppVM01 i następnie kończy tworzenie odpowiedzi HTTP, odpowiedź HTTP są wysyłane do żądającego
19. Ponieważ nie ma żadnych reguł NSG dla ruchu wychodzącego w odpowiedzi jest dozwolone podsieci frontonu
20. Odpowiedź HTTP trafienia zapory i ponieważ to jest odpowiedź na ustanowienie sesji translatora adresów Sieciowych jest akceptowane przez zaporę
21. Zapora następnie przekierowuje odpowiedź z powrotem do użytkownika Internet
22. Ponieważ nie ma nie wychodzące reguły NSG lub przeskoków przez podsieci frontonu odpowiedzi jest dozwolone i użytkownik Internetu odbiera żądanej strony sieci web.

#### <a name="allowed-internet-rdp-to-backend"></a>(Dozwolone) Internet RDP do wewnętrznej bazy danych
1. Administrator serwera w Internecie żądań sesji protokołu RDP do AppVM01 za pośrednictwem SecSvc001.CloudApp.Net:8025, gdzie 8025 jest numer portu przypisany użytkownik reguły zapory "RDP AppVM01"
2. Usługi w chmurze przekazuje ruch przez otwarty punkt końcowy na porcie 8025 do interfejsu zapory na 10.0.0.4:8025
3. Nie NSG przypisane do podsieci zabezpieczeń, tak reguły NSG systemu zezwolić na ruch do zapory
4. Zapora rozpoczyna przetwarzanie reguł:
   1. PD reguła 1 (PD Mgmt) nie są spełnione, przejść do następnej reguły
   2. 2 reguły Zapory (RDP IIS) nie są spełnione, przejść do następnej reguły
   3. PD zasady 3 (RDP DNS01) nie są spełnione, przejść do następnej reguły
   4. Zastosować PD zasady 4 (RDP AppVM01), ruch jest dozwolony, zapory NAT na 10.0.2.5:3386 (portem RDP na AppVM01)
5. Podsieci wewnętrznej bazy danych rozpoczyna się przetwarzanie przychodzących reguł:
   1. 1 reguły NSG (Zablokuj Internet) nie ma zastosowania (ruch został NAT czy przez zaporę, w związku z tym adres źródłowy jest teraz zaporą, który znajduje się w podsieci zabezpieczeń i widoczne dla grupy NSG podsieci wewnętrznej bazy danych jako "lokalnie" ruchu, w związku z tym jest dozwolone), przejść do następnej reguły
   2. Reguły NSG domyślne zezwolić na ruch podsieci do sieci, ruch jest dozwolony, Zatrzymaj przetwarzanie reguł NSG
6. AppVM01 nasłuchuje ruchu protokołu RDP i odpowiada
7. Z wychodzących reguł NSG Zastosuj reguły domyślne, a zwracany ruch jest dozwolony
8. PRZEZ kieruje ruch wychodzący do zapory w następnym skoku
9. Ponieważ to zwraca ruchu na ustanowienie sesji zapora przekazuje odpowiedź do użytkowników Internetu
10. Włączono sesji protokołu RDP
11. AppVM01 wyświetli monit o hasło nazwy użytkownika

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Dozwolone) Wyszukiwania DNS serwera sieci Web na serwerze DNS
1. Sieci Web serwera, IIS01, musi na www.data.gov strumieniowego źródła danych, ale musi rozpoznać adresu.
2. Konfigurację sieci dla listy sieci wirtualnej DNS01 (10.0.2.4 podsieci wewnętrznej bazy danych) jako podstawowy serwer DNS, IIS01 wysyła żądanie DNS DNS01
3. PRZEZ kieruje ruch wychodzący do zapory w następnym skoku
4. Brak wychodzących reguł NSG są powiązane z podsieci frontonu, ruch jest dozwolony.
5. Zapora rozpoczyna przetwarzanie reguł:
   1. PD reguła 1 (PD Mgmt) nie są spełnione, przejść do następnej reguły
   2. Reguły Zapory 2-5 (zasad protokołu RDP) nie są spełnione, przejść do następnej reguły
   3. PD zasady 6 i 7 (zasady aplikacji) nie są spełnione, przejść do następnej reguły
   4. 8 reguły Zapory (do Internetu) nie są spełnione, przejść do następnej reguły
   5. Zastosuj reguły Zapory 9 DNS (Domain Name System), ruch jest dozwolony, zapora przekazuje ruch do 10.0.2.4 (DNS01)
6. Podsieci wewnętrznej bazy danych rozpoczyna się przetwarzanie przychodzących reguł:
   1. Grupy NSG reguła 1 (Zablokuj Internet) nie są spełnione, przejść do następnej reguły
   2. Reguły NSG domyślne zezwolić na ruch podsieci do sieci, ruch jest dozwolony, Zatrzymaj przetwarzanie reguł NSG
7. Serwer DNS odbiera żądanie
8. Serwer DNS nie ma adresu w pamięci podręcznej i prosi o główny serwer DNS w Internecie
9. PRZEZ kieruje ruch wychodzący do zapory w następnym skoku
10. Nie wychodzące reguły NSG podsieci wewnętrznej bazy danych, ruch jest dozwolony
11. Zapora rozpoczyna przetwarzanie reguł:
    1. PD reguła 1 (PD Mgmt) nie są spełnione, przejść do następnej reguły
    2. Reguły Zapory 2-5 (zasad protokołu RDP) nie są spełnione, przejść do następnej reguły
    3. PD zasady 6 i 7 (zasady aplikacji) nie są spełnione, przejść do następnej reguły
    4. Zastosuj 8 reguły Zapory (do Internetu), ruch jest dozwolony, sesja jest SNAT się do głównego serwera DNS w Internecie
12. Serwer DNS w Internecie odpowiada, ponieważ w tej sesji zostało zainicjowane przez zaporę, odpowiedź jest akceptowany przez zaporę
13. Jak jest to ustanowienie sesji, zapora przekazuje odpowiedź na serwerze inicjujący DNS01
14. Podsieci wewnętrznej bazy danych rozpoczyna się przetwarzanie przychodzących reguł:
    1. Grupy NSG reguła 1 (Zablokuj Internet) nie są spełnione, przejść do następnej reguły
    2. Reguły NSG domyślne zezwolić na ruch podsieci do sieci, ruch jest dozwolony, Zatrzymaj przetwarzanie reguł NSG
15. Serwer DNS odbiera i będzie buforować odpowiedź i następnie odpowiada na żądania początkowego do IIS01
16. Trasy przez podsieci wewnętrznej bazy danych powoduje, że Zapora następnego skoku
17. Wychodzące NSG nie istnieją żadne reguły w podsieci wewnętrznej bazy danych ruch jest dozwolony.
18. Jest to ustanowienie sesji na zaporze, odpowiedź jest przesyłany dalej przez zaporę do serwera IIS
19. Podsieci frontonu rozpoczyna przetwarzanie przychodzących reguł:
    1. Nie zasady grupy NSG, która ma zastosowanie do przychodzącego ruchu w podsieci wewnętrznej bazy danych do podsieci frontonu, aby żadna grupa NSG zasady stosowane
    2. Domyślna reguła system zezwala na ruch między podsieciami pozwala tego rodzaju ruch, ruch jest dozwolony
20. IIS01 odbiera odpowiedź z DNS01

#### <a name="allowed-backend-server-to-frontend-server"></a>(Dozwolone) Serwer wewnętrznej bazy danych do serwera frontonu
1. Administrator zalogowany do AppVM02 za pomocą protokołu RDP zażąda pliku bezpośrednio z serwera IIS01 za pomocą Eksploratora plików systemu windows
2. Trasy przez podsieci wewnętrznej bazy danych powoduje, że Zapora następnego skoku
3. Ponieważ nie ma żadnych wychodzących reguł NSG podsieci wewnętrznej bazy danych, odpowiedzi jest dozwolone.
4. Zapora rozpoczyna przetwarzanie reguł:
   1. PD reguła 1 (PD Mgmt) nie są spełnione, przejść do następnej reguły
   2. Reguły Zapory 2-5 (zasad protokołu RDP) nie są spełnione, przejść do następnej reguły
   3. PD zasady 6 i 7 (zasady aplikacji) nie są spełnione, przejść do następnej reguły
   4. 8 reguły Zapory (do Internetu) nie są spełnione, przejść do następnej reguły
   5. 9 reguły Zapory (DNS, Domain Name System) nie są spełnione, przejść do następnej reguły
   6. Zastosuj 10 reguły Zapory (Intra-podsieć), ruch jest dozwolony, zapora przekazuje ruch do 10.0.1.4 (IIS01)
5. Podsieci frontonu rozpoczyna przetwarzanie przychodzących reguł:
   1. Grupy NSG reguła 1 (Zablokuj Internet) nie są spełnione, przejść do następnej reguły
   2. Reguły NSG domyślne zezwolić na ruch podsieci do sieci, ruch jest dozwolony, Zatrzymaj przetwarzanie reguł NSG
6. Zakładając, że odpowiednie uwierzytelniania i autoryzacji, IIS01 akceptuje żądania i odpowiedzi
7. Trasy przez podsieci frontonu sprawia, że Zapora następnego skoku
8. Ponieważ nie ma żadnych reguł NSG dla ruchu wychodzącego w odpowiedzi jest dozwolone podsieci frontonu
9. Jak jest to istniejącej sesji na zaporze ta odpowiedź jest dozwolone i zapory zwraca odpowiedź AppVM02
10. Podsieci wewnętrznej bazy danych rozpoczyna się przetwarzanie przychodzących reguł:
    1. Grupy NSG reguła 1 (Zablokuj Internet) nie są spełnione, przejść do następnej reguły
    2. Reguły NSG domyślne zezwolić na ruch podsieci do sieci, ruch jest dozwolony, Zatrzymaj przetwarzanie reguł NSG
11. AppVM02 odbiera odpowiedź

#### <a name="denied-internet-direct-to-web-server"></a>(Odmowa) Internet bezpośrednio do serwera sieci Web
1. Internet użytkownik próbuje uzyskać dostęp serwer sieci web, IIS01, za pośrednictwem usługi FrontEnd001.CloudApp.Net
2. Ponieważ nie ma żadnych punktów końcowych otwarte dla ruchu HTTP, to nie przejdzie za pośrednictwem usługi w chmurze, a nie dociera do serwera
3. Jeśli punkty końcowe zostały otwarte jakiegoś powodu, NSG (Zablokuj Internet) w podsieci frontonu uniemożliwiają ten ruch
4. Na koniec trasy przez podsieci frontonu czy Wyślij cały ruch wychodzący z IIS01 do zapory w następnym skoku, a zapora będzie traktować jako asymetrycznego ruchu i upuść wychodzące odpowiedzi, w związku z tym są co najmniej trzy warstwy niezależne obrony pomiędzy Internetem i IIS01 za pośrednictwem jego uniemożliwia nieautoryzowanym niewłaściwego dostępu usługi w chmurze.

#### <a name="denied-internet-to-backend-server"></a>(Odmowa) Internet do serwera wewnętrznej bazy danych
1. Internet użytkownik próbuje uzyskać dostęp w pliku na AppVM01 za pośrednictwem usługi BackEnd001.CloudApp.Net
2. Ponieważ nie ma żadnych punktów końcowych otwarty do udziału plików, to nie przejdzie usługi w chmurze, a nie dociera do serwera
3. Jeśli punkty końcowe zostały otwarte jakiegoś powodu, NSG (Zablokuj Internet) umożliwia zablokowanie tego ruchu
4. Na koniec trasy przez czy Wyślij cały ruch wychodzący z AppVM01 do zapory w następnym skoku, a zapora będzie traktować jako asymetrycznego ruchu i upuść wychodzące odpowiedzi, w związku z tym są co najmniej trzy warstwy niezależne obrony pomiędzy Internetem i AppVM01 za pośrednictwem jego uniemożliwia nieautoryzowanym niewłaściwego dostępu usługi w chmurze.

#### <a name="denied-frontend-server-to-backend-server"></a>(Odmowa) Serwer frontonu do serwera wewnętrznej bazy danych
1. Przykładowa IIS01 zostało naruszone i działa złośliwego kodu w trakcie skanowania serwerów podsieci wewnętrznej bazy danych.
2. Trasy przez podsieci frontonu czy Wyślij cały ruch wychodzący z IIS01 do zapory w następnym skoku. Nie jest to coś, który może być modyfikowany przez złamany maszynę Wirtualną.
3. Zapora może przetwarzać ruchu, jeśli żądanie zostało AppVM01 lub serwera DNS podczas wyszukiwania DNS, które potencjalnie być dozwolony ruch przez zaporę (z powodu PD zasady 7 i 9). Wszelki inny ruch zostałby zablokowany przez PD zasady 11 (Odmów wszystko).
4. Jeśli zaawansowane wykrywanie zagrożeń został włączony na zaporze (nieuwzględnionego w tym dokumencie, zajrzyj do dokumentacji dostawcy urządzenia określoną sieć zaawansowane funkcje zagrożenia), nawet ruch, który będzie dozwolone przez zasady przekazywania podstawowe, omówiony w niniejszym dokumencie można zapobiec, jeśli ruch zawarte podpisów znanych lub wzorce, które Flaga regułę advanced threat.

#### <a name="denied-internet-dns-lookup-on-dns-server"></a>(Odmowa) Wyszukiwania DNS w Internecie na serwerze DNS
1. Internet użytkownik podejmuje próbę wyszukiwania wewnętrzny rekord DNS na DNS01 za pośrednictwem usługi BackEnd001.CloudApp.Net 
2. Ponieważ nie ma żadnych punktów końcowych otwarte dla ruchu DNS, to nie przejdzie za pośrednictwem usługi w chmurze, a nie dociera do serwera
3. Jeśli punkty końcowe zostały otwarte jakiegoś powodu, reguły NSG (Zablokuj Internet) w podsieci frontonu uniemożliwiają ten ruch
4. Na koniec tras przez podsieci wewnętrznej bazy danych będzie Wyślij cały ruch wychodzący z DNS01 do zapory w następnym skoku, a zapora będzie traktować jako asymetrycznego ruchu i upuść wychodzące odpowiedzi związku z tym są co najmniej trzy warstwy niezależne obrony między Internet i DNS01 za pośrednictwem jego uniemożliwia nieautoryzowanym niewłaściwego dostępu usługi w chmurze.

#### <a name="denied-internet-to-sql-access-through-firewall"></a>(Odmowa) Internet w celu SQL dostęp za pośrednictwem zapory
1. Internet użytkownik żąda danych SQL z SecSvc001.CloudApp.Net (Internet ukierunkowane usługa w chmurze)
2. Ponieważ nie ma żadnych punktów końcowych otwarte dla bazy danych SQL, to nie przejdzie usługi w chmurze i nie osiągnąć zapory
3. Jeśli punkty końcowe SQL były otwarte jakiegoś powodu, Zapora może rozpocząć przetwarzania zasad:
   1. PD reguła 1 (PD Mgmt) nie są spełnione, przejść do następnej reguły
   2. Reguły Zapory 2-5 (zasad protokołu RDP) nie zastosować, przejść do następnej reguły
   3. PD zasady 6 i 7 (zasady aplikacji) nie są spełnione, przejść do następnej reguły
   4. 8 reguły Zapory (do Internetu) nie są spełnione, przejść do następnej reguły
   5. 9 reguły Zapory (DNS, Domain Name System) nie są spełnione, przejść do następnej reguły
   6. 10 reguły Zapory (Intra-podsieci) nie są spełnione, przejść do następnej reguły
   7. Zastosować PD zasady 11 (Odmów wszystko), ruch jest przetwarzania zasad zablokowane, stop

## <a name="references"></a>Dokumentacja
### <a name="main-script-and-network-config"></a>Skrypt głównego i konfiguracji sieci
Zapisz skrypt pełna w pliku skryptu programu PowerShell. Zapisz konfigurację sieci w pliku o nazwie "NetworkConf2.xml".
Modyfikuj zmienne zdefiniowane przez użytkownika, zgodnie z potrzebami. Uruchom skrypt, a następnie postępuj zgodnie z instrukcjami instalacji reguły zapory powyżej.

#### <a name="full-script"></a>Pełna skryptu
Ten skrypt zostanie, na podstawie zmiennych zdefiniowanych przez użytkownika:

1. Łączenie się z subskrypcją platformy Azure
2. Utwórz nowe konto magazynu
3. Utwórz nową sieć wirtualną i trzy podsieci zgodnie z definicją w pliku konfiguracji sieci
4. Tworzenie maszyn wirtualnych pięć; zapory 1 i 4 systemu windows server maszyny wirtualne
5. Skonfiguruj przez w tym:
   1. Utworzenie dwóch nowych tabel tras
   2. Dodawanie do tabel tras
   3. Powiąż tabel do odpowiednich podsieci
6. Włącz przesyłanie dalej IP na analizę NVA
7. Skonfiguruj grupy NSG w tym:
   1. Tworzenie grupy NSG
   2. Dodanie reguły
   3. Powiązanie grupy NSG z odpowiednich podsieci

Ten skrypt programu PowerShell powinien zostać uruchomiony lokalnie na się, że połączenie internetowe, komputera lub serwera.

> [!IMPORTANT]
> Uruchomienie tego skryptu można ostrzeżenia lub inne komunikaty informacyjne, które pop w programie PowerShell. Tylko komunikaty o błędach na czerwono są przyczyną problemu.
> 
> 

    <# 
     .SYNOPSIS
      Example of DMZ and User Defined Routing in an isolated network (Azure only, no hybrid connections)

     .DESCRIPTION
      This script will build out a sample DMZ setup containing:
       - A default storage account for VM disks
       - Three new cloud services
       - Three Subnets (SecNet, FrontEnd, and BackEnd subnets)
       - A Network Virtual Appliance (NVA), in this case a Barracuda NextGen Firewall
       - One server on the FrontEnd Subnet
       - Three Servers on the BackEnd Subnet
       - IP Forwading from the FireWall out to the internet
       - User Defined Routing FrontEnd and BackEnd Subnets to the NVA

      Before running script, ensure the network configuration file is created in
      the directory referenced by $NetworkConfigFile variable (or update the
      variable to reflect the path and file name of the config file being used).

     .Notes
      Everyone's security requirements are different and can be addressed in a myriad of ways.
      Please be sure that any sensitive data or applications are behind the appropriate
      layer(s) of protection. This script serves as an example of some of the techniques
      that can be used, but should not be used for all scenarios. You are responsible to
      assess your security needs and the appropriate protections needed, and then effectively
      implement those protections.

      Security Service (SecNet subnet 10.0.0.0/24)
       myFirewall - 10.0.0.4

      FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
       IIS01      - 10.0.1.4

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

    # User Defined Global Variables
      # These should be changes to reflect your subscription and services
      # Invalid options will fail in the validation section

      # Subscription Access Details
        $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

      # VM Account, Location, and Storage Details
        $LocalAdmin = "theAdmin"
        $DeploymentLocation = "Central US"
        $StorageAccountName = "vmstore02"

      # Service Details
        $SecureService = "SecSvc001"
        $FrontEndService = "FrontEnd001"
        $BackEndService = "BackEnd001"

      # Network Details
        $VNetName = "CorpNetwork"
        $VNetPrefix = "10.0.0.0/16"
        $SecNet = "SecNet"
        $FESubnet = "FrontEnd"
        $FEPrefix = "10.0.1.0/24"
        $BESubnet = "BackEnd"
        $BEPrefix = "10.0.2.0/24"
        $NetworkConfigFile = "C:\Scripts\NetworkConf3.xml"

      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
        $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NextGen Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}

      # UDR Details
        $FERouteTableName = "FrontEndSubnetRouteTable"
        $BERouteTableName = "BackEndSubnetRouteTable"

      # NSG Details
        $NSGName = "MyVNetSG"

    # User Defined VM Specific Config
        # Note: To ensure UDR and IP forwarding is setup
        # properly this script requires VM 0 be the NVA.

        # VM 0 - The Network Virtual Appliance (NVA)
          $VMName += "myFirewall"
          $ServiceName += $SecureService
          $VMFamily += "Firewall"
          $img += $FWImg
          $size += "Small"
          $SubnetName += $SecNet
          $VMIP += "10.0.0.4"

        # VM 1 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.4"

        # VM 2 - The First Appliaction Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"

        # VM 3 - The Second Appliaction Server
          $VMName += "AppVM02"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.6"

        # VM 4 - The DNS Server
          $VMName += "DNS01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.4"

    # ----------------------------- #
    # No User Defined Varibles or   #
    # Configuration past this point #
    # ----------------------------- #

      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop

      # Create Storage Account
        If (Test-AzureName -Storage -Name $StorageAccountName) { 
            Write-Host "Fatal Error: This storage account name is already in use, please pick a diffrent name." -ForegroundColor Red
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

    If (Test-AzureName -Service -Name $SecureService) { 
        Write-Host "The SecureService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}

    If (Test-AzureName -Service -Name $FrontEndService) { 
        Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use" -ForegroundColor Green}

    If (Test-AzureName -Service -Name $BackEndService) { 
        Write-Host "The BackEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The BackEndService service name is valid for use." -ForegroundColor Green}

    If (-Not (Test-Path $NetworkConfigFile)) { 
        Write-Host 'The network config file was not found, please update the $NetworkConfigFile variable to point to the network config xml file.' -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The network config file was found" -ForegroundColor Green
            If (-Not (Select-String -Pattern $DeploymentLocation -Path $NetworkConfigFile)) {
                Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation varible is correct and the netowrk config file matches.' -ForegroundColor Yellow
                $FatalError = $true}
            Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}

    If ($FatalError) {
        Write-Host "A fatal error has occured, please see the above messages for more information." -ForegroundColor Red
        Return}
    Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}

    # Create VNET
        Write-Host "Creating VNET" -ForegroundColor Cyan 
        Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop

    # Create Services
        Write-Host "Creating Services" -ForegroundColor Cyan
        New-AzureService -Location $DeploymentLocation -ServiceName $SecureService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop

    # Build VMs
        $i=0
        $VMName | Foreach {
            Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
            If ($VMFamily[$i] -eq "Firewall") 
                { 
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Linux -LinuxUser $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                # Set up all the EndPoints we'll need once we're up and running
                # Note: All traffic goes through the firewall, so we'll need to set up all ports here.
                #       Also, the firewall will be redirecting traffic to a new IP and Port in a forwarding
                #       rule, so all of these endpoint have the same public and local port and the firewall
                #       will do the mapping, NATing, and/or redirection as declared in the firewall rules.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPWeb"    -Protocol tcp -PublicPort 8014 -LocalPort 8014 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPApp1"   -Protocol tcp -PublicPort 8025 -LocalPort 8025 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPApp2"   -Protocol tcp -PublicPort 8026 -LocalPort 8026 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPDNS01"  -Protocol tcp -PublicPort 8024 -LocalPort 8024 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: A SSH endpoint is automatically created on port 22 when the appliance is created.
                }
            Else
                {
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                    Remove-AzureEndpoint -Name "RemoteDesktop" | `
                    Remove-AzureEndpoint -Name "PowerShell" | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                }
            $i++
        }

    # Configure UDR and IP Forwarding
        Write-Host "Configuring UDR" -ForegroundColor Cyan

      # Create the Route Tables
        Write-Host "Creating the Route Tables" -ForegroundColor Cyan 
        New-AzureRouteTable -Name $BERouteTableName `
            -Location $DeploymentLocation `
            -Label "Route table for $BESubnet subnet"
        New-AzureRouteTable -Name $FERouteTableName `
            -Location $DeploymentLocation `
            -Label "Route table for $FESubnet subnet"

      # Add Routes to Route Tables
        Write-Host "Adding Routes to the Route Tables" -ForegroundColor Cyan 
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
            -NextHopType VNETLocal
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $FEPrefix `
            -NextHopType VNETLocal

      # Assoicate the Route Tables with the Subnets
        Write-Host "Binding Route Tables to the Subnets" -ForegroundColor Cyan 
        Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
            -SubnetName $BESubnet `
            -RouteTableName $BERouteTableName
        Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
            -SubnetName $FESubnet `
            -RouteTableName $FERouteTableName

     # Enable IP Forwarding on the Virtual Appliance
        Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] `
            |Set-AzureIPForwarding -Enable

    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan

      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"

      # Add NSG Rule
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 100 -Action Deny `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
            -Protocol *

      # Assign the NSG to two Subnets
        # The NSG is *not* bound to the Security Subnet. The result
        # is that internet traffic flows only to the Security subnet
        # since the NSG bound to the Frontend and Backback subnets
        # will Deny internet traffic to those subnets.
        Write-Host "Binding the NSG to two subnets" -ForegroundColor Cyan
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName

    # Optional Post-script Manual Configuration
      # Configure Firewall
      # Install Test Web App (Run Post-Build Script on the IIS Server)
      # Install Backend resource (Run Post-Build Script on the AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Configure Firewall" -ForegroundColor Gray
      Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
      Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
      Write-Host


#### <a name="network-config-file"></a>Plik konfiguracji sieci
Zapisz ten plik xml z lokalizacji zaktualizowane i dodać link do tego pliku do zmiennej $NetworkConfigFile w skrypcie powyżej.

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
              <Subnet name="SecNet">
                <AddressPrefix>10.0.0.0/24</AddressPrefix>
              </Subnet>
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

#### <a name="sample-application-scripts"></a>Przykładowe skrypty aplikacji
Jeśli chcesz zainstalować przykładową aplikację dla tego i innych przykładowych sieci obwodowych, jeden podano przy użyciu następującego łącza: [przykładowy skrypt aplikacji][SampleApp]

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3design.png "Dwukierunkowe sieci obwodowe NVA, NSG i przez"
[2]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3firewalllogical.png "Widok logiczny reguł zapory"
[3]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectfrontend.png "Utwórz obiekt sieci frontonu"
[4]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectdns.png "Utwórz obiekt serwera DNS"
[5]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpa.png "Kopiowanie reguły domyślnej protokołu RDP"
[6]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpb.png "Reguła AppVM01"
[7]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconapplicationredirect.png "Ikona przekierowania aplikacji"
[8]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/icondestinationnat.png "Ikona NAT docelowego"
[9]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconpass.png "Ikona — dostęp próbny"
[10]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulefirewall.png "Reguły zapory zarządzania"
[11]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulerdp.png "Reguły zapory protokołu RDP"
[12]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleweb.png "Reguła zapory w sieci Web"
[13]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleappvm01.png "Reguły zapory AppVM01"
[14]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleoutbound.png "Reguła ruchu wychodzącego"
[15]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledns.png "Reguły zapory DNS"
[16]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleintravnet.png "Reguła sieci wirtualnej wewnątrz zapory"
[17]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledeny.png "Zapora regułę Odmów"
[18]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/firewallruleactivate.png "Aktywacja reguły zapory"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
