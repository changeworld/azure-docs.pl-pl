---
title: Przykład sieci obwodowej — ochrona sieci z sieci obwodowej, składający się z zaporą, routingiem zdefiniowanym przez użytkownika i sieciowymi grupami zabezpieczeń | Dokumentacja firmy Microsoft
description: Tworzenie sieci obwodowej (DMZ) z zaporą, routing zdefiniowany przez użytkownika (UDR) i sieciowymi grupami zabezpieczeń (NSG).
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
ms.openlocfilehash: 668862714b416bd89d3b5f82caf8b0305fccae54
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59426532"
---
# <a name="example-3-build-a-perimeter-network-to-protect-networks-with-a-firewall-udr-and-nsgs"></a>Przykład 3: Tworzenie sieci obwodowej, do ochrony sieci z zaporą, routingiem zdefiniowanym przez użytkownika i sieciowymi grupami zabezpieczeń

[Wróć do strony zabezpieczeń granic najlepsze praktyki][HOME]

W tym przykładzie utworzysz sieci obwodowej (znanej także jako DMZ, strefa zdemilitaryzowana i podsieć ekranowana). Przykład implementuje zapory, cztery serwery Windows, routingu zdefiniowanego przez użytkownika (UDR), przekazywanie dalej adresu IP i sieciowymi grupami zabezpieczeń (NSG). W tym artykule przedstawiono każdy odpowiednie polecenia, aby zapewnić lepiej zrozumieć każdego kroku. W sekcji scenariusza ruchu wyjaśniono również szczegółowo sposób ruch przechodzi przez warstwy obrony w sieci obwodowej. Na koniec sekcji odwołań zawiera kod i instrukcje dotyczące tworzenia tego środowiska, aby można było przetestować i eksperymentować z różnych scenariuszy.

![Sieci obwodowej dwukierunkowej z urządzenia WUS, sieciowej grupy zabezpieczeń i trasa zdefiniowana przez użytkownika][1]

## <a name="environment-setup"></a>Konfiguracja środowiska

W tym przykładzie użyto subskrypcji, która zawiera następujące składniki:

* Trzy usługi w chmurze: SecSvc001 FrontEnd001 i BackEnd001
* Sieć wirtualna (CorpNetwork) z trzech podsieci: SecNet frontonu i wewnętrznej bazy danych
* Wirtualne urządzenie sieciowe: Zapora połączona z podsiecią SecNet
* Serwer Windows, który reprezentuje serwer aplikacji sieci web: IIS01
* Dwa serwery Windows, które reprezentują serwery zaplecza aplikacji: AppVM01, AppVM02
* Windows server, który reprezentuje serwer DNS: DNS01

[Odwołuje się do sekcji](#references) zawiera skrypt programu PowerShell, który tworzy większość środowiska opisane w tym miejscu. W tym artykule, w przeciwnym razie nie zapewnia szczegółowe instrukcje dotyczące tworzenia maszyn wirtualnych (VM) i sieci wirtualnych.

Aby skompilować środowiska:

1. Zapisz plik XML konfiguracji sieci objęte [w sekcji](#references). Należy go zaktualizować przy użyciu nazwy, lokalizacji i adresy IP, aby dopasować do danego scenariusza.
1. Zaktualizuj zmienne użytkownika w pełen skrypt, aby dopasować określonego środowiska (na przykład, subskrypcji, nazwy usługi i tak dalej).
1. Uruchom skrypt programu PowerShell.

> [!NOTE]
> Region określonego w skrypcie programu PowerShell musi odpowiadać określone w pliku XML konfiguracji sieci.

Po pomyślnym uruchomieniu skryptu, wykonaj następujące czynności:

1. Konfigurowanie reguł zapory. Zobacz [reguły zapory](#firewall-rules) sekcji.
1. Opcjonalnie użyj dwa skrypty w sekcji odwołań do skonfigurowania aplikacji sieci web na serwerze sieci web i aplikacji umożliwia testowanie tej konfiguracji sieci Obwodowej.

## <a name="user-defined-routing"></a>Trasy definiowane przez użytkownika

Domyślnie następujące trasy systemowe są definiowane jako:

    Effective routes :
     Address Prefix    Next hop type    Next hop IP address Status   Source
     --------------    -------------    ------------------- ------   ------
     {10.0.0.0/16}     VNETLocal                            Active   Default
     {0.0.0.0/0}       internet                             Active   Default
     {10.0.0.0/8}      Null                                 Active   Default
     {100.64.0.0/10}   Null                                 Active   Default
     {172.16.0.0/12}   Null                                 Active   Default
     {192.168.0.0/16}  Null                                 Active   Default

VNETLocal jest zawsze prefiksy adresów zdefiniowanych dla tej określonej sieci wirtualnej. Na przykład ją zmieni się z siecią wirtualną z siecią wirtualną, w zależności od tego, jak jest zdefiniowany każdej określonej sieci wirtualnej. Pozostałe tras systemowych są statyczne i domyślne zgodnie z informacjami.

Jeśli chodzi o priorytecie trasy są przetwarzane za pomocą metody najdłuższego prefiksu dopasowania (LPM). Dlatego bardziej konkretny od pozostałych trasy w tabeli ma zastosowanie do danego adresu.

W związku z tym, ruch przeznaczony dla serwera, takich jak DNS01 (10.0.2.4) w lokalnej sieci (10.0.0.0/16) odbywa się za pośrednictwem sieci wirtualnej z powodu trasy 10.0.0.0/16.  W przypadku 10.0.2.4 trasy 10.0.0.0/16 jest bardziej konkretny od pozostałych trasy. Ta reguła ma zastosowanie, nawet jeśli 10.0.0.0/8 i 0.0.0.0/0 może być również stosowane. Są one mniej konkretnych, więc nie wpływają one na ten ruch. Ruch do 10.0.2.4 ma lokalnej sieci wirtualnej jako jego następny przeskok, dzięki czemu jest kierowany do miejsca docelowego.

Na przykład tras 10.0.0.0/16 nie ma zastosowania do ruchu, który jest przeznaczony dla 10.1.1.1. Trasa systemowa 10.0.0.0/8 jest najbardziej szczegółowy, dzięki czemu ruch jest porzucany lub "black holed" ponieważ następny przeskok ma wartość Null.

Jeśli miejsce docelowe nie ma zastosowania do dowolnych prefiksy o wartości Null lub prefiksy VNETLocal, ruch następuje trasy (0.0.0.0/0). Go odbywa z Internetem w następnym skoku i z platformy Azure granicy Internetu.

Jeśli istnieją dwie identyczne prefiksy w tabeli tras, w kolejności preferencji opiera się na atrybut źródłowy trasy:

1. VirtualAppliance: Trasy zdefiniowane przez użytkownika są ręcznie dodawane do tabeli.
1. VPNGateway: Dynamiczne trasy (protokołu BGP gdy jest używane z sieci hybrydowych) dodane przez protokół dynamicznej sieci. Te trasy mogą ulec zmianie wraz z upływem czasu, ponieważ protokół dynamicznej automatycznie uwzględnia zmiany w sieci równorzędnej.
1. Domyślne: Trasy systemowe, lokalnej sieci wirtualnej i statyczne, jak pokazano w powyższej tabeli tras.

> [!NOTE]
> Można teraz używać routing zdefiniowany przez użytkownika (UDR) przy użyciu usługi ExpressRoute i bramami sieci VPN, aby wymusić wysyłanie ruchu między wieloma lokalizacjami wychodzące i przychodzące do wirtualnego urządzenia sieciowego (WUS).

### <a name="create-local-routes"></a>Tworzenie tras lokalnych

W tym przykładzie użyto dwóch tabel routingu, jeden dla podsieci frontonu i zaplecza. Każda tabela jest załadowana trasy statyczne, które są odpowiednie dla danej podsieci. Na potrzeby tego przykładu każda tabela zawiera trzy trasy:

1. Ruchu w podsieci lokalnej przy użyciu nie miejsca docelowego następnego skoku zdefiniowane. Ta trasa zezwala na ruch z podsieci lokalnej ominąć zaporę.
2. Ruchu w sieci wirtualnej z następnym przeskokiem zdefiniowany jako zapora. Ta trasa zastępuje domyślną regułę umożliwiającą lokalnego ruchu w sieci wirtualnej do rozsyłania bezpośrednio.
3. Cały ruch pozostałe (0/0) z następnym przeskokiem zdefiniowany jako zapora.

Po utworzeniu tabele routingu, są powiązane swoje podsieci. Tabela routingu podsieci frontonu powinny wyglądać:

    Effective routes :
     Address Prefix    Next hop type       Next hop IP address  Status   Source
     --------------    ------------------  -------------------  ------   ------
     {10.0.1.0/24}     VNETLocal                                Active
     {10.0.0.0/16}     VirtualAppliance    10.0.0.4             Active
     {0.0.0.0/0}       VirtualAppliance    10.0.0.4             Active

W tym przykładzie użyto następujących poleceń do tworzenia tabeli tras, Dodaj trasy zdefiniowane przez użytkownika, a następnie powiązania tabeli tras do podsieci. Elementy, które zaczynają się od `$`, takich jak `$BESubnet`, zmiennych zdefiniowanych przez użytkownika ze skryptu zamieszczone w tej sekcji.

1. Najpierw utwórz podstawowej tabeli routingu. Poniższy fragment kodu tworzy tabelę dla podsieci zaplecza. Pełen skrypt tworzy także odpowiedniej tabeli dla podsieci frontonu.

   ```powershell
   New-AzureRouteTable -Name $BERouteTableName `
       -Location $DeploymentLocation `
       -Label "Route table for $BESubnet subnet"
   ```

1. Po utworzeniu tabeli tras, możesz dodać określone trasy zdefiniowane przez użytkownika. Poniższy fragment kodu Określa, że cały ruch (0.0.0.0/0) odbywa się za pośrednictwem urządzenia wirtualnego. Zmienna `$VMIP[0]` służy do przekazywania adresu IP przypisanego podczas tworzenia urządzenia wirtualnego we wcześniejszej części skryptu. Pełen skrypt tworzy odpowiednią regułę w tabeli frontonu.

   ```powershell
   Get-AzureRouteTable $BERouteTableName | `
       Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
       -NextHopType VirtualAppliance `
       -NextHopIpAddress $VMIP[0]
   ```

1. Poprzedni wpis trasa zastępuje domyślną trasę "0.0.0.0/0", ale domyślna reguła 10.0.0.0/16 nadal zezwala na ruch w sieci wirtualnej, aby kierować bezpośrednio do miejsca docelowego, a nie wirtualnego urządzenia sieciowego. Aby rozwiązać ten problem, należy dodać następującą regułę:

   ```powershell
   Get-AzureRouteTable $BERouteTableName | `
       Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
       -NextHopType VirtualAppliance `
       -NextHopIpAddress $VMIP[0]
   ```

1. W tym momencie masz o dokonanie wyboru. Dwie reguły poprzedniego kierować cały ruch do zapory dla oceny, w tym ruch sieciowy w ramach pojedynczej podsieci. Możesz zechcieć to zachowanie. Jeśli nie, jednak można zezwalać na ruch w obrębie podsieci do rozsyłania lokalnie bez angażowania zapory. Dodaj inną określonej reguły, które bezpośrednio przekazuje dowolny adres skierowany do podsieci lokalnej (Typ następnego przeskoku = VNETLocal).

   ```powershell
   Get-AzureRouteTable $BERouteTableName | `
       Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
           -NextHopType VNETLocal
   ```

1. Na koniec po tabeli routingu jest utworzony i wypełniony trasy zdefiniowane przez użytkownika, należy do powiązania tabeli do podsieci. Poniższy fragment kodu tworzy powiązania tabeli dla podsieci zaplecza. Pełen skrypt także wiąże tabeli frontonu tras do podsieci frontonu.

   ```powershell
   Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
       -SubnetName $BESubnet `
       -RouteTableName $BERouteTableName
   ```

## <a name="ip-forwarding"></a>Przekazywanie IP

Przekazywanie dalej adresu IP to funkcja pomocnika trasy zdefiniowanej przez użytkownika. To ustawienie na urządzenie wirtualne umożliwia mu odbieranie ruchu nie jest skierowana do urządzenia, a następnie przekazują ruch do ostatecznego miejsca przeznaczenia.

Na przykład jeśli ruch z AppVM01 kieruje żądanie do serwera DNS01, trasa zdefiniowana przez użytkownika kieruje ruchem do zapory. Z włączoną funkcją przekazywania adresów IP ruch z lokalizacją docelową DNS01 (10.0.2.4) jest akceptowana przez urządzenie zapory (10.0.0.4) i następnie przekazywane do ostatecznego miejsca docelowego (10.0.2.4). Bez funkcji przekazywania IP jest włączona zapora, ruch nie zostanie zaakceptowany przez urządzenie nawet, jeśli tabela tras ma zapory w następnym skoku.

> [!IMPORTANT]
> Pamiętaj, aby włączyć przekazywanie dalej adresu IP w połączeniu z routingu zdefiniowanego przez użytkownika.

Przekazywanie dalej adresu IP można włączyć za pomocą jednego polecenia podczas tworzenia maszyny Wirtualnej. Możesz wywołać wystąpienia maszyny Wirtualnej, która jest Twoje urządzenie wirtualne zapory i włącza przekazywanie adresów IP. Należy pamiętać, że elementy w kolorze czerwonym, które zaczynają się od `$`, takiej jak `$VMName[0]`, zmiennych zdefiniowanych przez użytkownika ze skryptu zamieszczone w tej sekcji niniejszego dokumentu. Zero w nawiasy kwadratowe, `[0]`, reprezentuje pierwszą maszynę Wirtualną w tablicy, której maszyny wirtualne. Przykładowy skrypt do działają bez żadnych modyfikacji pierwsza maszyna wirtualna (VM, 0) musi być zapory. W skrypcie pełną fragment odpowiedni kod jest zgrupowany z poleceń trasy zdefiniowanej przez użytkownika blisko końca.

```powershell
Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] | `
    Set-AzureIPForwarding -Enable
```

## <a name="network-security-groups"></a>Grupy zabezpieczeń sieci

W tym przykładzie tworzenie sieciowej grupy zabezpieczeń (NSG), a następnie ładuje go z jednej reguły. Przykład następnie powiąże sieciowej grupy zabezpieczeń, tylko do podsieci frontonu i zaplecza (nie SecNet). Regułę, którą należy załadować do sieciowej grupy zabezpieczeń jest następująca:

* Odmowa cały ruch (wszystkich portów) z Internetu do całej sieci wirtualnej (wszystkie podsieci)

Mimo że sieciowe grupy zabezpieczeń są używane w tym przykładzie, ich głównym celem jest jako dodatkowej warstwy obrony przed ręczne błędnej konfiguracji. Chcesz zablokować cały ruch przychodzący z Internetu, albo z podsieciami frontonu i zaplecza. Ruch powinna przepływać tylko za pomocą podsieci SecNet do zapory, po upływie którego tylko odpowiednie ruch należy uzyskać kierowany do podsieci frontonu lub zaplecza. Ponadto reguły trasy zdefiniowanej przez użytkownika kierować cały ruch, który dociera do podsieci frontonu lub zaplecza do zapory. Zapora uznaje asymetrycznego przepływu i umieszcza ruchu wychodzącego.

Trzy warstwy zabezpieczeń chronią podsieciami frontonu i zaplecza:

1. Brak otwartych punktów końcowych na FrontEnd001 i BackEnd001 usług w chmurze
1. Sieciowe grupy zabezpieczeń zezwalają na ruch z Internetu
1. Zapora spada asymetrycznego ruchu

Interesującej kwestii dotyczących sieciowej grupy zabezpieczeń, w tym przykładzie jest, czy zawiera on tylko jedną regułę, pokazano poniżej. Ta zasada nie zezwala na ruch internetowy do całej sieci wirtualnej, w tym podsieć zabezpieczeń.

```powershell
Get-AzureNetworkSecurityGroup -Name $NSGName | `
    Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet `
    from the internet" `
    -Type Inbound -Priority 100 -Action Deny `
    -SourceAddressPrefix INTERNET -SourcePortRange '*' `
    -DestinationAddressPrefix VIRTUAL_NETWORK `
    -DestinationPortRange '*' `
    -Protocol *
```

Ponieważ sieciowa grupa zabezpieczeń jest powiązana tylko z podsieciami frontonu i zaplecza, reguła nie jest stosowane do ruchu przychodzącego do podsieci zabezpieczeń. W rezultacie ruch odbywa się do podsieci zabezpieczeń.

```powershell
Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
    -SubnetName $FESubnet -VirtualNetworkName $VNetName

Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
    -SubnetName $BESubnet -VirtualNetworkName $VNetName
```

## <a name="firewall-rules"></a>Reguły zapory

Musisz utworzyć reguły przekazywania w zaporze. Zapora blokuje lub przekazanie całego ruchu przychodzącego, ruchu wychodzącego i intra virtual-network, niezbędna, ponieważ wiele reguł zapory. Ponadto zapory musi przetworzyć cały ruch przychodzący na publiczny adres IP usługi zabezpieczeń (na różne porty). Aby uniknąć przeróbek później, postępuj zgodnie z najlepszym rozwiązaniem jest przez tworzenie diagramów przepływów logicznych przed rozpoczęciem konfigurowania podsieci i reguły zapory. Poniższa ilustracja jest widok logiczny reguł zapory, w tym przykładzie:

![Widok logiczny reguł zapory][2]

> [!NOTE]
> Porty zarządzania zależy od wirtualnego urządzenia sieciowego. Ten przykład przedstawia zapory NextGen Barracuda, która korzysta z portów 22, 801 i 807. Zapoznaj się dokumentacją dostawcy urządzenia, aby znaleźć odpowiednie porty do zarządzania urządzeniem.

### <a name="logical-rule-description"></a>Opis reguły logiczne

Powyższym diagramie logicznym nie pokazuje podsieci zabezpieczeń, ponieważ Zapora jest tylko zasobów tej podsieci. Ten diagram przedstawia reguł zapory, jak one logicznie blokują lub zezwalają na ruch, ale nie rzeczywiste kierowane ścieżki. Ponadto portów zewnętrznych, wybrany dla ruchu protokołu remote desktop protocol (RDP) są wyższe zakres portów (8014 — 8026), wybrany dla czytelność w celu zapewnienia zgodności z ostatnich dwóch oktety lokalnych adresów IP. Na przykład serwer lokalny, adres 10.0.1.4 jest skojarzony z portu zewnętrznego 8014. Można jednak używać żadnych wyższe konfliktu portów.

W tym przykładzie potrzebne są następujące typy reguł:

* Zewnętrzne reguły dla ruchu przychodzącego:
  1. Reguły zarządzania zapory: zezwala na ruch do przekazania do portów zarządzania wirtualnego urządzenia sieciowego.
  2. Reguły protokołu RDP dla każdego serwera systemu windows: umożliwia zarządzanie poszczególnych serwerów za pośrednictwem protokołu RDP.  W zależności od możliwości sieciowe urządzenie wirtualne można powiązać reguły do jednego.
  3. Reguły ruchu aplikacji: jeden dla ruchu w sieci web frontonu i jednej dla ruchu zaplecza (na przykład serwer sieci web do warstwy danych). Konfiguracja reguł zależy od ruchu i architektura sieci przepływów.

     * Pierwsza reguła zezwala na ruch rzeczywiste aplikacje do osiągnięcia serwera aplikacji. W przeciwieństwie do reguł zabezpieczeń, zarządzania i tak dalej reguł aplikacji umożliwiają użytkownikom zewnętrznym lub usług, uzyskać dostęp do aplikacji. W tym przykładzie ma jednym serwerze sieci web na porcie 80, który umożliwia pojedynczą aplikację regułę zapory do przekierowywania ruchu sieciowego, który jest przeznaczony dla zewnętrznego adresu IP, który zamiast tego przekierowywać do wewnętrznego adresu IP serwera sieci web. Sesja przekierowanego ruchu jest ponownie mapowany przez translatora adresów Sieciowych, do wewnętrznego serwera.
     * Drugą regułę ruchu aplikacji jest reguła zaplecza, aby zezwolić na serwerze sieci web na potrzeby kierowania ruchu do serwera AppVM01, ale nie z serwerem AppVM02 dowolnego portu.

* Wewnętrzny reguły dla ruchu intra virtual-network:
  1. Ruch wychodzący do Internetu reguły: zezwala na ruch z żadną siecią do przekazania do wybranych sieci. Ta reguła jest zazwyczaj domyślną w zaporze, ale w stanie wyłączenia. Włącz tę zasadę na potrzeby tego przykładu.
  2. Reguła DNS: zezwala tylko na ruch DNS (port 53) przekazać do serwera DNS. Dla tego środowiska większość ruchu z frontonu do zaplecza jest zablokowany. Ta zasada umożliwia specjalnie DNS z dowolnej podsieci lokalnej.
  3. Reguła podsieci do sieci: umożliwia dowolnego serwera w podsieci zaplecza w celu połączenia z serwerem z podsieci frontonu, ale nie odwrotnie.

* Odporne na uszkodzenia reguły ruchu, który nie spełnia żadnego z powyższych kryteriów:
  1. Odmów wszystkie reguły ruchu: zawsze końcowa reguła pod względem priorytetu. Jeśli przepływ ruchu nie pasuje do żadnej z powyższych zasad, ta reguła blokuje ją. Jest to reguła domyślna. Ponieważ często jest aktywna, potrzebne są żadnych modyfikacji.

> [!TIP]
> W drugiej reguły ruchu aplikacji każdy port jest dozwolona w celu uproszczenia w tym przykładzie. W przypadku rzeczywistych należy używać określonych portów i zakresów adresów, aby zmniejszyć obszar narażony na tę regułę.


> [!IMPORTANT]
> Po utworzeniu zasad, ważne jest przejrzenie priorytet każdej reguły, aby upewnić się, że ruch jest dozwolony lub zabroniony zgodnie z potrzebami. W tym przykładzie reguły są w kolejności priorytetów. To ułatwia zabezpieczenie przed zablokowaniem dostępu zapory Jeśli zasady są nieprawidłowo uporządkowane. Upewnij się ustawić regułę zapory zarządzania bezwzględne najwyższy priorytet.

### <a name="rule-prerequisites"></a>Reguły wymagań wstępnych

Publiczne punkty końcowe są wymagane dla maszyny wirtualnej z systemem zapory. Te publiczne punkty końcowe musi być otwarty, dzięki czemu zapora może przetwarzać ruchu. Istnieją trzy rodzaje ruchu w tym przykładzie:

1. Ruch związany z zarządzaniem do kontrolowania Zapora i reguły zapory
1. Ruch RDP do kontrolowania serwerów z systemem windows
1. Ruch aplikacji

Typy ruchu sieciowego są wyświetlane w górnej połowie zapory reguły powyższym diagramie logicznym.

> [!IMPORTANT]
> Należy pamiętać, że *wszystkich* ruchu przepływa przez zaporę. Do pulpitu zdalnego z serwerem IIS01 musisz nawiązać połączenie z zapory na porcie 8014, a następnie zezwolić na zaporze aby kierować żądania protokołu RDP wewnętrznie do portu IIS01 RDP. Witryny Azure portal **Connect** przycisk nie będzie działać, ponieważ nie ma bezpośredniego RDP ścieżki do IIS01 w zakresie, w jakim można zobaczyć w portalu. Wszystkie połączenia z Internetu są usługi zabezpieczeń i portu (na przykład secscv001.cloudapp.net:xxxx). Odwołują się powyżej diagram mapowania portów zewnętrznych i wewnętrznych adresu IP i portu.

Możesz otworzyć punktu końcowego w momencie utworzenia maszyny Wirtualnej lub po kompilacji. Przykładowy skrypt i poniższy fragment kodu Otwórz punkt końcowy, po utworzeniu maszyny Wirtualnej.

Należy pamiętać, że elementy, które zaczynają się od `$`, takich jak `$VMName[$i]`, zmiennych zdefiniowanych przez użytkownika ze skryptu zamieszczone w tej sekcji. `[$i]` Reprezentuje liczbę tablicy w określonej maszyny Wirtualnej w tablicy maszyn wirtualnych.

```powershell
Add-AzureEndpoint -Name "HTTP" -Protocol tcp -PublicPort 80 -LocalPort 80 `
    -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | `
    Update-AzureVM
```

Chociaż nie wyraźnie jest on wyświetlany w tym miejscu ze względu na zmienne, należy otwierać tylko te punkty końcowe w usłudze w chmurze zabezpieczeń. Ten środek ostrożności pomaga upewnić się, że zapora obsługuje cały ruch przychodzący, ma kierowane, ponownie mapowany przez translatora adresów Sieciowych lub porzucony.

Zainstaluj klienta zarządzania na komputerze do zarządzania zaporą i tworzenia wymaganych konfiguracji. Aby uzyskać szczegółowe informacje na temat zarządzania zaporą lub innego urządzenia WUS zajrzyj do dokumentacji dostawcy. Dalszej części tej sekcji, jak również **tworzenia reguł zapory** sekcji opisano konfigurację zapory. Użyj dostawcy zarządzania klienta, nie witryny Azure portal lub programu PowerShell.

Przejdź do [administratora NG Barracuda](https://techlib.barracuda.com/NG61/NGAdmin) do pobrania klient zarządzania i Dowiedz się, jak połączyć się z zapory Barracuda.

Po logujesz się do zapory, należy zdefiniować obiekty sieci i usługi przed utworzeniem reguł zapory. Te dwie klasy obiektu wymagań wstępnych możliwość tworzenia reguł łatwiejsze.

W tym przykładzie należy zdefiniować dla każdej trzy obiekty sieci o nazwie:

* Podsieci frontonu
* Podsieci zaplecza
* Adres IP serwera DNS

Aby utworzyć sieć nazwany na pulpicie nawigacyjnym klienta Barracuda NG administratora:

1. Przejdź do **Karta Konfiguracja**.
1. Wybierz **zestaw reguł** w **konfigurację operacyjną** sekcji
1. Wybierz **sieci** w obszarze **obiekty zapory** menu.
1. Wybierz **New** w **Edytuj sieci** menu.
1. Utwórz obiekt sieci przez dodanie nazwy i prefiksu:

   ![Utwórz obiekt front-ed sieci][3]

Poprzednie kroki tworzenia nazwanego sieci dla podsieci frontonu. Utwórz obiekt podobne w podsieci zaplecza. Teraz podsieci można łatwiej odwoływać się według nazwy w regułach zapory.

Aby uzyskać obiekt serwera DNS:

![Utwórz obiekt serwera DNS][4]

Ten jedno odwołanie adresu IP jest używana w regule DNS w dalszej części dokumentu.

Klasy obiektów zawiera obiekty usług, które reprezentują portów połączeń protokołu RDP dla każdego serwera. Istniejący obiekt usługi RDP jest powiązany z domyślnego portu RDP 3389. W związku z tym można tworzyć nowe usługi, aby zezwolić na ruch z portów zewnętrznych (8014 8026). Nowych portów można również dodać do istniejącej usługi protokołu RDP. Jednak w celu ułatwienia demonstracji, istnieje możliwość poszczególnych reguł dla każdego serwera. Aby utworzyć nową regułę protokołu RDP dla serwera z pulpitu nawigacyjnego klienta Barracuda NG administratora:

1. Przejdź do **Karta Konfiguracja**.
1. Wybierz **zestaw reguł** w **konfigurację operacyjną** sekcji.
1. Wybierz **usług** w obszarze **obiekty zapory** menu.
1. Przewiń w dół listę usług i wybierz pozycję **RDP**.
1. Kliknij prawym przyciskiem myszy i wybierz kopię, a następnie kliknij prawym przyciskiem myszy i wybierz wklejania.
1. Teraz jest obiektem usługi Copy1 protokołu RDP, które można edytować. Kliknij prawym przyciskiem myszy **RDP Copy1** i wybierz **Edytuj**.
1. **Edycji obiektu usługi** otwarte okno w górę, jak pokazano poniżej:

   ![Kopiuj domyślna reguła protokołu RDP][5]

1. Edytuj wartości do reprezentowania usługi protokołu RDP dla określonego serwera. Dla AppVM01, należy zmodyfikować domyślną regułę protokołu RDP, aby odzwierciedlać nową usługę **nazwa**, **opis**i zewnętrzny Port protokołu RDP, używany w diagramie rysunek 8. Należy pamiętać, że porty są zmieniane z domyślna RDP 3389 na porcie zewnętrznym dla tego określonego serwera. Na przykład port zewnętrznego AppVM01 jest 8025. Reguła zmodyfikowanej usługi jest następująca:

   ![Reguła AppVM01][6]

Powtórz ten proces, do tworzenia usług protokołu RDP dla pozostałych serwerów: AppVM02 DNS01 i IIS01. Te usługi należy reguły w następnej sekcji, prostsze utworzyć i bardziej oczywisty.

> [!NOTE]
> Usługi protokołu RDP dla zapory nie jest konieczna, ponieważ zapory maszyny Wirtualnej jest obrazem opartych na systemie Linux, dlatego protokół SSH jest używany na porcie 22 dla zarządzania maszyną Wirtualną, a nie protokołu RDP. Ponadto portu 22, a dwa inne porty są dozwolone w przypadku łączności zarządzania. Zobacz **reguły zarządzania zapory** w następnej sekcji.

### <a name="firewall-rules-creation"></a>Tworzenie reguły zapory

Istnieją trzy typy reguł zapory używane w tym przykładzie, wszystko różne ikony:

Reguła przekierowania aplikacji: ![Ikona przekierowania aplikacji][7]

Reguła NAT docelowa: ![Ikona translatora adresów Sieciowych docelowego][8]

Reguła — dostęp próbny: ![Przekaż ikonę][9]

Więcej informacji na temat tych reguł można znaleźć w witrynie sieci Web firmy Barracuda.

Aby utworzenie następujących reguł lub Sprawdź istniejące reguły domyślne:

1. Na pulpicie nawigacyjnym administratora NG Barracuda klienta, przejdź do **konfiguracji** kartę.
1. W **konfigurację operacyjną** zaznacz **zestaw reguł**.
1. **Reguły Main** Siatka zawiera istniejące active i dezaktywowane zasady dotyczące tej zapory. Zaznacz zielony **+** w prawym górnym rogu, aby utworzyć nową regułę. Jeśli Zapora jest zablokowana dla zmian, zobaczysz przycisk oznaczony **blokady** i nie można utworzyć lub edytować reguły. Wybierz **blokady** przycisk, aby odblokować zestawu reguł i Zezwól na edytowanie. Kliknij prawym przyciskiem myszy reguły, w której chcesz edytować, a następnie wybierz pozycję **edytowanie reguły**.

Po utworzeniu lub modyfikować żadnych reguł odesłać je do zapory i je aktywować. W przeciwnym razie zmiany reguł zaczyna obowiązywać. Proces wypychania i aktywacji jest opisany w [reguły aktywacji](#rule-activation).

Poniżej przedstawiono szczegółowe informacje na temat każdej reguły potrzebnym do ukończenia tego przykładu:

* **Reguły zarządzania zapory**: Ta reguła przekierowania aplikacji zezwala na ruch do przekazania do portów zarządzania sieciowe urządzenie wirtualne, w tym przykładzie zapora Barracuda NextGen. Porty zarządzania są 801, 807 i, opcjonalnie, 22. Porty wewnętrznych i zewnętrznych są takie same, bez translacji portu. Ta reguła jest wywoływana Konfigurowanie-MGMT-dostępu. Jest domyślną regułę i domyślnie włączona w Barracuda NextGen Firewall, wersji 6.1.
  
    ![Reguły zarządzania zapory][10]

  > [!TIP]
  > Przestrzeń adresowa źródła w tej regule jest **wszelkie**. Jeśli zakresy adresów IP zarządzania są znane, również zmniejszenie zakresu zmniejsza możliwości zaatakowania do portów zarządzania.

* **Reguły protokołu RDP**:  Te reguły translatora adresów Sieciowych docelowego Zezwalaj na zarządzanie poszczególnych serwerów za pośrednictwem protokołu RDP. Krytyczne pola te reguły są:
  * Źródło. Aby zezwolić na RDP z dowolnego miejsca, użyj odwołania **wszelkie** w polu źródłowym.
  * Usługa. Użyj obiektu usługi protokołu RDP, która została utworzona wcześniej: **AppVM01 RDP**. Zewnętrzne porty przekierowanie do serwera lokalnego adresu IP i domyślnego portu RDP 3386. Ta reguła określonych ma dostęp RDP do AppVM01.
  * Miejsce docelowe. Użyj lokalnego portu w zaporze: **Lokalny adres IP 1 DCHP** lub **eth0** Jeśli używasz statycznych adresów IP. Liczbę porządkową (eth0, eth1 itd.) może się różnić, jeśli urządzenia sieciowego ma wiele interfejsów lokalnych. Zapora używa tego portu do wysyłania i może być taki sam jak port odbierania. Rzeczywiste trasowane miejscem docelowym jest **listy docelowej** pola.
  * Przekierowanie. Skonfiguruj urządzenie wirtualne stwierdzić, dokąd przekierowywać ostatecznie ten ruch. Najprostsza przekierowania jest umieszczenie adresów IP w polu listy docelowej. Można także określić port, a następnie zmienia trasę translatora adresów Sieciowych, port i adres IP. Jeśli nie określisz port urządzenia wirtualnego używa port docelowy dla ruchu przychodzącego żądania.

    ![Reguła protokołu RDP][11]

    Utwórz cztery reguły protokołu RDP:

    | Nazwa reguły | Serwer | Usługa | Listy docelowej |
    | --- | --- | --- | --- |
    | RDP-to-IIS01 |IIS01 |IIS01 RDP |10.0.1.4:3389 |
    | RDP-to-DNS01 |DNS01 |DNS01 RDP |10.0.2.4:3389 |
    | RDP-to-AppVM01 |AppVM01 |AppVM01 protokołu RDP |10.0.2.5:3389 |
    | RDP-to-AppVM02 |AppVM02 |AppVm02 RDP |10.0.2.6:3389 |

  > [!TIP]
  > Zawężanie zakres pola źródłowego i usługa zmniejsza obszar narażony na ataki. Użyj najbardziej ograniczonym zakresie, który umożliwia funkcji.

* **Reguły ruchu aplikacji**: Istnieją dwie reguły ruchu aplikacji. Jeden jest przeznaczony dla ruchu frontonu sieci web. Druga dotyczy ruchu zaplecza, takich jak serwer sieci web do warstwy danych. Zasady te są zależne od architektury sieci oraz przepływy ruchu.
  
  * Reguła frontonu dla ruchu w sieci web:
  
    ![Reguła zapory w sieci Web][12]
  
    Ta reguła NAT do docelowej umożliwia aplikacji rzeczywisty ruch do serwera aplikacji. W odróżnieniu od zasad zabezpieczeń, zarządzania i etcetera reguł aplikacji umożliwiają użytkownikom zewnętrznym lub usług, uzyskać dostęp do aplikacji. W tym przykładzie ma jednym serwerze sieci web na porcie 80, który umożliwia pojedynczą aplikację regułę zapory do przekierowywania ruchu sieciowego, który jest przeznaczony dla zewnętrznego adresu IP, który zamiast tego przekierowywać do wewnętrznego adresu IP serwera sieci web. Sesja przekierowanego ruchu jest ponownie mapowany przez translatora adresów Sieciowych, do wewnętrznego serwera.

    > [!NOTE]
    > Istnieje nie portu przypisanego w **listy docelowej** pola. W związku z tym przychodzący port 80 (lub 443 dla wybranej usługi) jest używana podczas przekierowywania serwera sieci web. Jeśli serwer sieci web nasłuchuje na innym porcie, np. 8080, można zaktualizować pola listy docelowej do 10.0.1.4:8080, aby umożliwić na przekierowanie portów.
  
  * Reguła zaplecza pozwala serwera sieci web na komunikowanie się z serwerem AppVM01, ale nie AppVM02 za pośrednictwem **wszelkie** usługi:
  
    ![Reguła AppVM01 zapory][13]
  
    Ta zasada — dostęp próbny umożliwia dowolnego serwera usług IIS w podsieci frontonu w celu osiągnięcia AppVM01 (10.0.2.5) na dowolnym porcie przy użyciu dowolnego protokołu, dzięki czemu dane są dostępne dla aplikacji sieci web.
  
    W tym zrzucie ekranu `<explicit-dest>` jest używany w **docelowy** pola oznaczającego 10.0.2.5 jako miejsca docelowego. Adres IP można określić jawnie, jak pokazano na zrzucie ekranu. Umożliwia także nazwany obiekt sieci, takich jak w sekcji wymagania wstępne dla serwera DNS. Administrator zapory można wybrać metody. Aby dodać 10.0.2.5 jako jawne miejsce docelowe, kliknij dwukrotnie ikonę na pierwszym pustym wierszu w obszarze `<explicit-dest>` i podaj adres w oknie dialogowym, która zostanie otwarta.
  
    Z tą regułą — dostęp próbny odłączenia translatora adresów Sieciowych jest niezbędne, ponieważ ruchem wewnętrznym przez skierowanie go obsługuje. Możesz ustawić **metodę połączenia** do `No SNAT`.
  
    > [!NOTE]
    > Sieć źródłowa w tej regule jest dowolnego zasobu w podsieci frontonu, jeśli istnieje tylko jeden. Architektury określa znanych liczbę serwerów sieci web, po utworzeniu zasobu obiekt sieci na dokładniejszą do adresów IP dokładnego, a nie całej podsieci frontonu.

    > [!TIP]
    > Ta reguła jest używana usługa **wszelkie** przykładowej aplikacji ułatwiają konfigurowanie i używanie. Umożliwia ona ICMPv4 (ping) w jednej reguły. Jednak zmniejszyć ataku powierzchni granicę, zaleca się ograniczenie usługi portów i protokołów do minimum, możliwe, dzięki czemu działania aplikacji.

    > [!TIP]
    > Chociaż ta przykładowa reguła używa `<explicit-dest>` odwołania, należy użyć spójnego podejścia w całym konfigurację zapory. Zalecane jest łatwiejsze czytelności i możliwości obsługi przy użyciu nazwanego obiektu sieci. `<explicit-dest>` Pokazano, w tym miejscu jest tylko po to, aby wyświetlić alternatywne reference — metoda. Nie jest ogólnie zalecane jest, szczególnie w przypadku złożonych konfiguracji.

* **Reguły ruchu wychodzącego z Internetem**: Ta zasada — dostęp próbny zezwala na ruch z żadną siecią źródła do przekazania do wybranej sieci docelowej. Zapora Barracuda NextGen ma zwykle tę regułę "włączone" Domyślnie, ale w stanie wyłączenia. Kliknij prawym przyciskiem myszy na tej reguły, aby uzyskać dostęp do **reguły aktywacji** polecenia. Zmodyfikuj regułę pokazano na zrzucie ekranu, można dodać obiektów sieciowych dla podsieci zaplecza i frontonu z atrybutem źródłowym tej reguły. Te obiekty sieci został utworzony w sekcji wymagań wstępnych w tym artykule.
  
    ![Reguły ruchu wychodzącego zapory][14]

* **Reguła DNS**: Ta zasada — dostęp próbny zezwala tylko na ruch DNS (port 53) do przekazania do serwera DNS. Dla tego środowiska większość ruchu z frontonu do zaplecza jest zablokowany, więc ta reguła wyraźnie zezwala na ruch DNS.
  
    ![Reguła DNS][15]
  
    > [!NOTE]
    > **Metodę połączenia** ustawiono `No SNAT` ponieważ ta reguła ma wewnętrzny adres IP, aby ruch wewnętrzny adres IP, a nie rerouting za pośrednictwem translatora adresów Sieciowych jest wymagana.

* **Reguła podsieci do sieci**: Reguły domyślnej — dostęp próbny został aktywowany i zmodyfikowane, aby każdy serwer w podsieci zaplecza, aby nawiązać połączenie z dowolnego serwera w podsieci frontonu. Ta zasada coves tylko ruch wewnętrzny więc **metodę połączenia** można ustawić `No SNAT`.

    ![Reguły sieci wirtualnej wewnątrz zapory][16]
  
    > [!NOTE]
    > **Dwukierunkowej** zaznaczono pole wyboru nie w tym miejscu, dzięki czemu ta reguła ma zastosowanie tylko w jednym kierunku. Połączenie może być inicjowane z podsieci zaplecza sieć frontonu, ale nie odwrotnie. W przypadku zaznaczenia tego pola wyboru ta reguła umożliwiłby ruch dwukierunkowy, który możemy został określony jako niepożądanych w naszym diagram logiczny.

* **Odmów wszystkie reguły ruchu**: Ta reguła powinna być zawsze końcowa reguła pod względem priorytetu. Jeśli przepływ ruchu nie pasuje do żadnej z powyższych zasad, ta zasada powoduje, że można go porzucić. Reguła często jest aktywowana domyślnie, więc zmiany nie są wymagane.
  
    ![Zapora regułę Odmów][17]

> [!IMPORTANT]
> Po utworzeniu wszystkich powyższych zasad, przejrzyj priorytet każdej reguły, aby upewnić się, ruch jest dozwolony lub zablokowany zgodnie z potrzebami. W tym przykładzie reguły są wyświetlane w kolejności, w której powinny się pojawiać w klienta zarządzania Barracuda siatki główne reguł przekazywania.

## <a name="rule-activation"></a>Reguła aktywacji

Po został zmodyfikowany zestaw ze specyfikacjami diagram logikę reguł, należy przekazać zestawu reguł zapory i aktywuj go.

![Aktywacja reguły zapory][18]

Szukaj w prawym górnym rogu okna zarządzania klienta, a następnie wybierz pozycję **wysłać zmiany** można przekazać zmodyfikowany reguły zapory. Wybierz **aktywować**.

Po aktywowaniu zestawu reguł zapory na tym środowisku przykładowym zostało ukończone.

## <a name="traffic-scenarios"></a>Scenariusze ruchu

> [!IMPORTANT]
> Należy pamiętać, że *wszystkich* ruchu przepływa przez zaporę. Do pulpitu zdalnego z serwerem IIS01 musisz nawiązać połączenie z zapory na porcie 8014, a następnie zezwolić na zaporze aby kierować żądania protokołu RDP wewnętrznie do portu IIS01 RDP. Witryny Azure portal **Connect** przycisk nie będzie działać, ponieważ nie ma bezpośredniego RDP ścieżki do IIS01 w zakresie, w jakim można zobaczyć w portalu. Wszystkie połączenia z Internetu są usługi zabezpieczeń i portu (na przykład secscv001.cloudapp.net:xxxx). Odwołują się powyżej diagram mapowania portów zewnętrznych i wewnętrznych adresu IP i portu.

W tych scenariuszach następujące reguły zapory powinny być stosowane:

1. Zarządzanie Zaporą (systemu Windows PD Mgmt)
2. RDP to IIS01
3. RDP to DNS01
4. Połączenie przez RDP AppVM01
5. Połączenie przez RDP AppVM02
6. Ruchem aplikacji sieci Web
7. Ruch aplikacji AppVM01
8. Ruch wychodzący do Internetu
9. Fronton dla DNS01
10. Ruch wewnątrz podsieci (zaplecza do tylko fronton)
11. Odmów wszystkim

Twoje zestawu reguł zapory rzeczywiste najprawdopodobniej będzie wymagać więcej reguł niż te, w tym przykładzie. Prawdopodobnie będą mieć również liczby różnych priorytetów. Należy zapoznać się na tej liście, a skojarzone z nimi numery dla ich względny priorytet do siebie nawzajem. Na przykład reguła "RDP IIS01" może być regułę liczby 5 na zaporze rzeczywistych, ale tak długo, jak jego poniżej zapory "Zarządzanie" i powyżej "RDP do DNS01" reguła, zestaw wyrównuje zamiarem tej listy. Ta lista pomaga również uprościć instrukcji dotyczących scenariuszy, które należy wykonać. Na przykład "reguły zapory 9 (DNS)." Należy pamiętać, że cztery reguły protokołu RDP są nazywane "reguły protokołu RDP" podczas scenariusza ruch nie jest powiązana protokołu RDP.

Odwołaj również, czy sieciowe grupy zabezpieczeń (NSG) w miejscu, aby ruch przychodzący z Internetu na podsieciami frontonu i zaplecza.

### <a name="allowed-internet-to-web-server"></a>(Dozwolone) Internet do serwera sieci web

1. Użytkownik internet żąda strony HTTP z SecSvc001.CloudApp.Net (usługi w chmurze połączone z Internetem).
1. Usługi w chmurze przekazuje ruch przez otwarty punkt końcowy na porcie 80 do interfejsu zapory na 10.0.0.4:80.
1. Brak sieciowej grupy zabezpieczeń jest przypisany do podsieci zabezpieczeń, więc system reguły sieciowej grupy zabezpieczeń zezwalającą na ruch do zapory.
1. Ruch osiąga wewnętrzny adres IP zapory (10.0.1.4).
1. Zapora wykonuje przetwarzanie reguł:
   1. Reguła zapory 1 (PD Mgmt) nie ma zastosowania. Przenieś na następną regułę.
   1. Reguły zapory na 2 – 5 (protokół RDP reguły) nie mają zastosowania. Przenieś na następną regułę.
   1. Reguła zapory 6 (aplikacji: Dotyczy aplikacji sieci Web). Ruch jest dozwolony. Zapora zmienia trasę ruchu za pośrednictwem translatora adresów Sieciowych do 10.0.1.4 (IIS01).
1. Podsieci frontonu wykonuje przetwarzanie reguł dla ruchu przychodzącego:
   1. Nie ma zastosowania reguły sieciowej grupy zabezpieczeń 1 (internet bloku). Zapora przekierowane ten ruch za pośrednictwem translatora adresów Sieciowych, więc adres źródłowy jest teraz zapory. Ponieważ zaporę znajduje się w podsieci zabezpieczeń i pojawia się jako lokalny ruch do podsieci frontonu sieciowej grupy zabezpieczeń, ruch jest dozwolony. Przenieś na następną regułę.
   1. Domyślne reguły sieciowej grupy zabezpieczeń umożliwiają ruch podsieci do sieci, więc ten ruch jest dozwolony. Zatrzymaj przetwarzanie reguł sieciowej grupy zabezpieczeń.
1. IIS01 nasłuchuje ruchu w sieci web. Odbierze tego żądania i rozpoczyna przetwarzanie żądania.
1. IIS01 próbuje zainicjować sesję FTP, aby AppVM01 w podsieci zaplecza.
1. Trasa zdefiniowana przez użytkownika trasy podsieci frontonu sprawia, że Zapora następnego przeskoku.
1. Brak reguł ruchu wychodzącego w podsieci frontonu, więc ruch jest dozwolony.
1. Zapora rozpoczyna przetwarzanie reguł:
   1. Reguła zapory 1 (PD Mgmt) nie ma zastosowania. Przenieś na następną regułę.
   1. Reguły zapory na 2 – 5 (protokół RDP reguły) nie mają zastosowania. Przenieś na następną regułę.
   1. Reguła zapory 6 (aplikacji: Nie dotyczy aplikacji sieci Web). Przenieś na następną regułę.
   1. Reguła zapory 7 (aplikacji: zaplecze) ma zastosowania. Ruch jest dozwolony. Zapora przekazuje ruch do 10.0.2.5 (AppVM01).
1. Podsieci zaplecza wykonuje przetwarzanie reguł dla ruchu przychodzącego:
    1. Nie ma zastosowania reguły sieciowej grupy zabezpieczeń 1 (internet bloku). Przenieś na następną regułę.
    1. Domyślne reguły sieciowej grupy zabezpieczeń zezwalają na ruch podsieci do sieci. Ruch jest dozwolony. Zatrzymaj przetwarzanie reguł sieciowej grupy zabezpieczeń.
1. AppVM01 odbiera żądanie, inicjuje sesję i odpowiada.
1. Trasa zdefiniowana przez użytkownika trasy podsieci zaplecza sprawia, że Zapora następnego przeskoku.
1. Brak wychodzących reguł sieciowej grupy zabezpieczeń w podsieci zaplecza, umożliwić odpowiedzi.
1. Ponieważ ona zwraca ruchu na ustanowienie sesji, zapora przekazuje odpowiedź z powrotem do serwera sieci web (IIS01).
1. Podsieci frontonu wykonuje przetwarzanie reguł dla ruchu przychodzącego:
    1. Nie ma zastosowania reguły sieciowej grupy zabezpieczeń 1 (internet bloku). Przenieś na następną regułę.
    1. Domyślne reguły sieciowej grupy zabezpieczeń umożliwiają ruch podsieci do sieci, więc ten ruch jest dozwolony. Zatrzymaj przetwarzanie reguł sieciowej grupy zabezpieczeń.
1. Na serwerze usług IIS odbiera odpowiedź i zakończeniu transakcji z AppVM01. Następnie serwer zakończy tworzenia odpowiedzi HTTP i wysyła je do osoby zgłaszającej żądanie.
1. Brak wychodzących reguł sieciowej grupy zabezpieczeń podsieci frontonu, umożliwić odpowiedzi.
1. Odpowiedź HTTP uderza w zaporze. Ponieważ jest on na odpowiedź na ustanowienie sesji translatora adresów Sieciowych, Zapora akceptuje je.
1. Zapora przekierowuje odpowiedź z powrotem do użytkownika internetowego.
1. Nie ma żadnych reguł sieciowej grupy zabezpieczeń dla ruchu wychodzącego ani przeskoków trasy zdefiniowanej przez użytkownika w podsieci frontonu, odpowiedź jest dozwolone. Użytkownik internet otrzymuje żądanej strony sieci web.

### <a name="allowed-internet-rdp-to-back-end"></a>(Dozwolone) RDP z Internetu do zaplecza

1. Administrator serwera w Internecie żądań sesję RDP do AppVM01 za pośrednictwem SecSvc001.CloudApp.Net:8025. 8025 jest numerem portu przypisanego przez użytkownika dla reguły zapory 4 (protokół RDP AppVM01).
1. Usługi w chmurze przekazuje ruch przez otwarty punkt końcowy na porcie 8025 interfejsu zapory na 10.0.0.4:8025.
1. Brak sieciowej grupy zabezpieczeń jest przypisany do podsieci zabezpieczeń, więc system reguły sieciowej grupy zabezpieczeń zezwalają na ruch do zapory.
1. Zapora wykonuje przetwarzanie reguł:
   1. Reguła zapory 1 (PD Mgmt) nie ma zastosowania. Przenieś na następną regułę.
   1. Reguły zapory 2 (RDP IIS) nie ma zastosowania. Przenieś na następną regułę.
   1. Reguła zapory 3 (RDP DNS01) nie ma zastosowania. Przenieś na następną regułę.
   1. Reguły zapory na 4 (protokół RDP AppVM01) mają zastosowanie, dzięki czemu ruch jest dozwolony. Zapora zmienia trasę go za pośrednictwem translatora adresów Sieciowych do 10.0.2.5:3386 (portem RDP na AppVM01).
1. Podsieci zaplecza wykonuje przetwarzanie reguł dla ruchu przychodzącego:
   1. Nie ma zastosowania reguły sieciowej grupy zabezpieczeń 1 (internet bloku). Zapora przekierowane ten ruch za pośrednictwem translatora adresów Sieciowych, więc adres źródłowy jest teraz zaporą, która znajduje się w podsieci zabezpieczeń. Ona jest widoczna dla sieciowa grupa zabezpieczeń podsieci zaplecza za ruch lokalny i jest dozwolone. Przenieś na następną regułę.
   1. Domyślne reguły sieciowej grupy zabezpieczeń umożliwiają ruch podsieci do sieci, więc ten ruch jest dozwolony. Zatrzymaj przetwarzanie reguł sieciowej grupy zabezpieczeń.
1. AppVM01 nasłuchuje ruchu protokołu RDP i odpowiada.
1. Brak wychodzących reguł sieciowej grupy zabezpieczeń, domyślne reguły. Powrotny ruch jest dozwolony.
1. Trasa zdefiniowana przez użytkownika kieruje ruch wychodzący do zapory w następnym skoku.
1. Ponieważ ona zwraca ruchu na ustanowienie sesji, zapora przekazuje odpowiedź z powrotem do użytkownika internetowego.
1. Sesję RDP jest włączona.
1. AppVM01 wyświetli monit o nazwę użytkownika hasło.

### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Dozwolone) Wyszukiwanie DNS serwera sieci Web na serwerze DNS

1. Serwer sieci Web IIS01 żąda danych z kanału informacyjnego HTTP\:\/\/www.data.gov, ale musi rozpoznać adresu.
1. Konfigurację sieci dla sieci wirtualnej list DNS01 (10.0.2.4 w podsieci zaplecza) jako podstawowy serwer DNS. IIS01 spowoduje wysłanie żądania DNS DNS01.
1. Trasa zdefiniowana przez użytkownika kieruje ruch wychodzący do zapory w następnym skoku.
1. Brak wychodzących reguł sieciowej grupy zabezpieczeń są powiązane z podsieci frontonu. Ruch jest dozwolony.
1. Przetwarzanie reguł wykonywaną przez zaporę:
   1. Reguła zapory 1 (PD Mgmt) nie ma zastosowania. Przenieś na następną regułę.
   1. Reguły zapory na 2 – 5 (protokół RDP reguły) nie mają zastosowania. Przenieś na następną regułę.
   1. Reguły zapory, 6 i 7 (reguły aplikacji) nie mają zastosowania. Przenieś na następną regułę.
   1. Reguła zapory 8 (do Internetu) nie ma zastosowania. Przenieś na następną regułę.
   1. Zastosuj reguły zapory 9 (DNS). Ruch jest dozwolony. Zapora przekazuje ruch do 10.0.2.4 (DNS01).
1. Podsieci zaplecza wykonuje przetwarzanie reguł dla ruchu przychodzącego:
   1. Nie ma zastosowania reguły sieciowej grupy zabezpieczeń 1 (internet bloku). Przenieś na następną regułę.
   1. Domyślne reguły sieciowej grupy zabezpieczeń zezwalają na ruch podsieci do sieci. Ruch jest dozwolony. Zatrzymaj przetwarzanie reguł sieciowej grupy zabezpieczeń.
1. Serwer DNS odbiera żądanie.
1. Serwer DNS nie ma adresu pamięci podręcznej i prosi główny serwer DNS w Internecie.
1. Trasa zdefiniowana przez użytkownika kieruje ruch wychodzący do zapory w następnym skoku.
1. Ma nie wychodzące reguły sieciowej grupy zabezpieczeń w podsieci zaplecza na ruch jest dozwolony.
1. Przetwarzanie reguł wykonywaną przez zaporę:
   1. Reguła zapory 1 (PD Mgmt) nie ma zastosowania. Przenieś na następną regułę.
   1. Reguły zapory na 2 – 5 (protokół RDP reguły) nie mają zastosowania. Przenieś na następną regułę.
   1. Reguły zapory, 6 i 7 (reguły aplikacji) nie mają zastosowania. Przenieś na następną regułę.
   1. Zastosuj reguły zapory 8 (z Internetem). Ruch jest dozwolony. Sesja jest przekierowane za pośrednictwem SNAT do głównego serwera DNS w Internecie.
1. DNS w sieci internet serwer odpowiada. Tej sesji zostało zainicjowane przez zaporę, więc odpowiedzi jest akceptowany przez zaporę.
1. Ta sesja jest już ustanowione, dzięki czemu zapora przekazuje odpowiedź do serwera inicjujący DNS01.
1. Podsieci zaplecza wykonuje przetwarzanie reguł dla ruchu przychodzącego:
    1. Nie ma zastosowania reguły sieciowej grupy zabezpieczeń 1 (internet bloku). Przenieś na następną regułę.
    1. Domyślne reguły sieciowej grupy zabezpieczeń zezwala na ruch podsieci do sieci, aby ten ruch jest dozwolony. Zatrzymaj przetwarzanie reguł sieciowej grupy zabezpieczeń.
1. Serwer DNS odbiera będzie buforować odpowiedź i następnie odpowie na żądanie początkowy powrót do IIS01.
1. Trasa zdefiniowana przez użytkownika trasy podsieci zaplecza sprawia, że Zapora następnego przeskoku.
1. Brak wychodzących reguł sieciowej grupy zabezpieczeń istnieje w podsieci zaplecza, więc ruch jest dozwolony.
1. Ta sesja jest już ustanowione w zaporze, dzięki czemu zapora zmienia trasę odpowiedź z powrotem do serwera usług IIS.
1. Podsieci frontonu wykonuje przetwarzanie reguł dla ruchu przychodzącego:
    1. Istnieje nie reguły sieciowej grupy zabezpieczeń dla ruchu przychodzącego z podsieci zaplecza do podsieci frontonu, więc żaden z reguły sieciowej grupy zabezpieczeń.
    1. Domyślną regułę system zezwala na ruch między podsieciami. Ruch jest dozwolony.
1. IIS01 odbiera odpowiedź od DNS01.

### <a name="allowed-back-end-server-to-front-end-server"></a>(Dozwolone) Serwer zaplecza na serwerze frontonu

1. Administrator zalogowany do AppVM02 za pośrednictwem protokołu RDP zażąda pliku bezpośrednio z serwera IIS01 za pomocą Eksploratora plików systemu windows.
1. Trasa zdefiniowana przez użytkownika trasy podsieci zaplecza sprawia, że Zapora następnego przeskoku.
1. Brak wychodzących reguł sieciowej grupy zabezpieczeń w podsieci zaplecza, umożliwić odpowiedzi.
1. Zapora wykonuje przetwarzanie reguł:
   1. Reguła zapory 1 (PD Mgmt) nie ma zastosowania. Przenieś na następną regułę.
   1. Reguły zapory na 2 – 5 (protokół RDP reguły) nie mają zastosowania. Przenieś na następną regułę.
   1. Reguły zapory, 6 i 7 (reguły aplikacji) nie mają zastosowania. Przenieś na następną regułę.
   1. Reguła zapory 8 (do Internetu) nie ma zastosowania. Przenieś na następną regułę.
   1. Reguła zapory 9 (DNS), nie ma zastosowania. Przenieś na następną regułę.
   1. Zastosuj reguły zapory na 10 (Intra-podsieci). Ruch jest dozwolony. Zapora przekazuje ruch do 10.0.1.4 (IIS01).
1. Podsieci frontonu rozpoczyna przetwarzanie reguł dla ruchu przychodzącego:
   1. 1 regułę sieciowej grupy zabezpieczeń (internet bloku) nie zastosować, przenieść następną regułę
   1. Domyślne reguły sieciowej grupy zabezpieczeń umożliwiają ruch podsieci do sieci, więc ruch jest dozwolony. Zatrzymaj przetwarzanie reguł sieciowej grupy zabezpieczeń.
1. Zakładając, że odpowiednie uwierzytelnianie i autoryzacja, IIS01 akceptuje żądania i odpowiedzi.
1. Trasa zdefiniowana przez użytkownika trasy podsieci frontonu sprawia, że Zapora następnego przeskoku.
1. Brak wychodzących reguł sieciowej grupy zabezpieczeń podsieci frontonu, umożliwić odpowiedzi.
1. Ta sesja już istnieje w zaporze, więc ta odpowiedź jest dozwolony. Zapora zwraca odpowiedź do AppVM02.
1. Podsieci zaplecza wykonuje przetwarzanie reguł dla ruchu przychodzącego:
    1. Nie ma zastosowania reguły sieciowej grupy zabezpieczeń 1 (internet bloku). Przenieś na następną regułę.
    2. Domyślne reguły sieciowej grupy zabezpieczeń umożliwiają ruch podsieci do sieci, więc ruch jest dozwolony. Zatrzymaj przetwarzanie reguł sieciowej grupy zabezpieczeń.
1. AppVM02 odbiera odpowiedź.

### <a name="denied-internet-direct-to-web-server"></a>(Odrzucony) Internet bezpośrednio do serwera sieci web

1. Użytkownik internet próbuje uzyskać dostęp do serwera sieci web IIS01 za pośrednictwem usługi FrontEnd001.CloudApp.Net.
1. Istnieją punkty końcowe nie otwarty dla ruchu HTTP, więc ten ruch nie przechodzi przez usługę w chmurze. Ruch nie dociera do serwera.
1. Jeśli punkty końcowe są otwarte niektóre przyczyny, (internet bloku) sieciowej grupy zabezpieczeń w podsieci frontonu blokuje ruch.
1. Na koniec podsieci frontonu trasy zdefiniowanej przez użytkownika trasa wysyła cały ruch wychodzący z IIS01 do zapory w następnym skoku. Zapora uznaje asymetrycznego ruchu i umieszcza wychodzące odpowiedzi.

>Tak więc istnieją co najmniej trzech niezależnych warstw ochrony między Internetem a IIS01. Usługi w chmurze uniemożliwia dostęp nieautoryzowani lub nieodpowiednie.

### <a name="denied-internet-to-back-end-server"></a>(Odrzucony) Internet do serwera zaplecza

1. Użytkownik internet próbuje dostęp do pliku na AppVM01 za pośrednictwem usługi BackEnd001.CloudApp.Net.
2. Punkty końcowe nie są otwarte na potrzeby udostępniania plików, dzięki czemu to żądanie nie zakończy się pomyślnie z usługą w chmurze. Ruch nie dociera do serwera.
3. Jeśli punkty końcowe są otwarte niektóre przyczyny, sieciowej grupy zabezpieczeń (internet bloku) blokuje ten ruch.
4. Na koniec trasy zdefiniowanej przez użytkownika trasa wysyła cały ruch wychodzący z AppVM01 do zapory w następnym skoku. Zapora uznaje asymetrycznego ruchu i umieszcza wychodzące odpowiedzi.

> Tak więc istnieją co najmniej trzech niezależnych warstw ochrony między Internetem a AppVM01. Usługi w chmurze uniemożliwia dostęp nieautoryzowani lub nieodpowiednie.

### <a name="denied-front-end-server-to-back-end-server"></a>(Odrzucony) Serwer frontonu do serwera zaplecza

1. IIS01 zostanie naruszone i działa złośliwego kodu próby przeskanowania serwerów podsieci zaplecza.
1. Trasa zdefiniowana przez użytkownika trasy podsieci frontonu wysyła cały ruch wychodzący z IIS01 do zapory w następnym skoku. Zagrożonych maszyn wirtualnych nie można zmienić tego routingu.
1. Zapora przetwarza ruchu sieciowego. Jeśli żądanie jest AppVM01 lub serwera DNS dla wyszukiwania DNS, Zapora może potencjalnie zezwalać na ruch ze względu na reguły zapory, 7 i 9. Cały pozostały ruch jest blokowany przez reguły zapory 11 (Odmów wszystkiego).
1. Jeśli włączysz zaawansowane wykrywanie zagrożeń w zaporze, można zapobiegać ruchu, który zawiera znanych sygnatur lub wzorców, które Flaga regułę zaawansowanych zagrożeń. Ta miara może działać nawet wtedy, gdy ruch jest dozwolone zgodnie z regułami przekazywania podstawowych, które zostały omówione w tym artykule. Zaawansowane wykrywanie zagrożeń nie został opisany w tym dokumencie. Zobacz dokumentację dostawcy dla urządzenia sieciowego określonych zaawansowane możliwości zagrożeń.

### <a name="denied-internet-dns-lookup-on-dns-server"></a>(Odrzucony) Wyszukiwanie DNS w sieci Internet, na serwerze DNS

1. Użytkownik internet próbuje wyszukać wewnętrzny rekord DNS na DNS01 za pośrednictwem usługi BackEnd001.CloudApp.Net.
1. Punkty końcowe nie są otwarte dla ruch DNS, ten ruch nie przechodzi przez usługę w chmurze. Go nie dociera do serwera.
1. Jeśli punkty końcowe są otwarte niektóre przyczyny, reguła sieciowej grupy zabezpieczeń (Block internet), podsieci frontonu blokuje ruch.
1. Na koniec podsieci zaplecza trasy zdefiniowanej przez użytkownika trasa wysyła cały ruch wychodzący z DNS01 do zapory w następnym skoku. Zapora uznaje to asymetryczny ruchu i umieszcza wychodzące odpowiedzi.

> Tak więc istnieją co najmniej trzech niezależnych warstw ochrony między Internetem a DNS01. Usługi w chmurze uniemożliwia dostęp nieautoryzowani lub nieodpowiednie.

#### <a name="denied-internet-to-sql-access-through-firewall"></a>(Odrzucony) Internet w celu SQL dostęp przez zaporę

1. Użytkownik internet żąda danych SQL w SecSvc001.CloudApp.Net połączone z Internetem usłudze w chmurze.
1. Otwartych żadnych punktów końcowych dla programu SQL, aby ten ruch nie zakończy się pomyślnie z usługą w chmurze. Go nie dociera do zapory.
1. Jeśli punktów końcowych SQL są otwarte niektóre przyczyny, Zapora wykonuje przetwarzanie reguł:
   1. Reguła zapory 1 (PD Mgmt) nie ma zastosowania. Przenieś na następną regułę.
   1. Reguły zapory na 2 – 5 (protokół RDP reguły) nie mają zastosowania. Przenieś na następną regułę.
   1. Reguły zapory, 6 i 7 (reguł aplikacji) nie mają zastosowania. Przenieś na następną regułę.
   1. Reguła zapory 8 (do Internetu) nie ma zastosowania. Przenieś na następną regułę.
   1. Reguła zapory 9 (DNS), nie ma zastosowania. Przenieś na następną regułę.
   1. Reguła zapory 10 (Intra-podsieć), nie ma zastosowania. Przenieś na następną regułę.
   1. Zastosuj reguły zapory 11 (Odmów wszystkiego). Ruch jest blokowany. Zatrzymaj przetwarzanie reguł.

## <a name="references"></a>Dokumentacja

Ta sekcja zawiera następujące elementy:

* Pełen skrypt. Zapisz go w pliku skryptu programu PowerShell.
* Konfiguracja sieci. Zapisz go w pliku o nazwie NetworkConf2.xml.

Zmodyfikuj zmienne zdefiniowane przez użytkownika w plikach zgodnie z potrzebami. Uruchom skrypt, a następnie postępuj zgodnie z instrukcjami instalacji reguły zapory, wymienione we wcześniejszej części tego artykułu.

### <a name="full-script"></a>Pełny skrypt

Po ustawieniu zmiennych zdefiniowanych przez użytkownika, uruchom ten skrypt, aby:

1. Łączenie się z subskrypcją platformy Azure
1. Tworzenie nowego konta magazynu
1. Utwórz nową sieć wirtualną oraz trzy podsieci, zgodnie z definicją w pliku konfiguracji sieci
1. Tworzenie pięć maszyn wirtualnych: zapory i cztery maszyny wirtualne bramy Windows Server
1. Skonfiguruj trasy zdefiniowanej przez użytkownika:
   1. Utworzyć dwie nowe tabele tras
   1. Dodawanie tras do tabel
   1. Powiąż tabel do odpowiednich podsieci
1. Włączanie przekazywania adresów IP na urządzeniu WUS
1. Konfigurowanie sieciowej grupy zabezpieczeń:
   1. Tworzenie sieciowej grupy zabezpieczeń
   1. Dodawanie reguły
   1. Sieciowa grupa zabezpieczeń należy powiązać odpowiednie podsieci

Uruchom program PowerShell to skrypt lokalnie w Internecie połączone komputera lub serwera.

> [!IMPORTANT]
> Po uruchomieniu tego skryptu, ostrzeżenia lub inne komunikaty informacyjne mogą się pojawiać, w programie PowerShell. Tylko komunikaty o błędach czerwony są zaniepokoić.

```powershell
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
       - IP Forwarding from the FireWall out to the internet
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

        # VM 2 - The First Application Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"

        # VM 3 - The Second Application Server
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
    # No User Defined Variables or   #
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

      # Associate the Route Tables with the Subnets
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
        # since the NSG bound to the FrontEnd and BackEnd subnets
        # will Deny internet traffic to those subnets.
        Write-Host "Binding the NSG to two subnets" -ForegroundColor Cyan
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName

    # Optional Post-script Manual Configuration
      # Configure Firewall
      # Install Test Web App (Run Post-Build Script on the IIS Server)
      # Install BackEnd resource (Run Post-Build Script on the AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Configure Firewall" -ForegroundColor Gray
      Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
      Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
      Write-Host
```

### <a name="network-config-file"></a>Plik konfiguracji sieci

Zapisz ten plik XML z zaktualizowaną lokalizację. Zmiana `$NetworkConfigFile` zmienną pełen skrypt powyższe łącze do pliku konfiguracji sieci zapisane.

```xml
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
```

## <a name="next-steps"></a>Kolejne kroki

Można zainstalować aplikacji przykładowej, aby pomóc w tym przykładzie sieci obwodowej.

> [!div class="nextstepaction"]
> [Przykładowy skrypt aplikacji](./virtual-networks-sample-app.md)

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3design.png "Dwukierunkowe sieci obwodowe NVA, NSG i przez"
[2]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3firewalllogical.png "Widok logiczny reguł zapory"
[3]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectfrontend.png "Utwórz obiekt sieci frontonu"
[4]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectdns.png "Utwórz obiekt serwera DNS"
[5]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpa.png "Kopiuj domyślna reguła protokołu RDP"
[6]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpb.png "Reguła AppVM01"
[7]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconapplicationredirect.png "Ikona przekierowania aplikacji"
[8]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/icondestinationnat.png "Ikona translatora adresów Sieciowych docelowego"
[9]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconpass.png "Przekaż ikonę"
[10]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulefirewall.png "Reguły zarządzania zapory"
[11]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulerdp.png "Reguła protokołu RDP"
[12]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleweb.png "Reguła zapory w sieci Web"
[13]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleappvm01.png "Reguła AppVM01 zapory"
[14]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleoutbound.png "Reguły ruchu wychodzącego zapory"
[15]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledns.png "Reguła DNS"
[16]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleintravnet.png "Reguły sieci wirtualnej wewnątrz zapory"
[17]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledeny.png "Zapora regułę Odmów"
[18]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/firewallruleactivate.png "Aktywacja reguły zapory"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
