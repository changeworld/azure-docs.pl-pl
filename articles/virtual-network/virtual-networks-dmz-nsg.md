---
title: Przykład sieci obwodowej na platformie Azure — Tworzenie prostej sieci obwodowej z grup NSG | Dokumentacja firmy Microsoft
description: Tworzenie sieci obwodowej z grup zabezpieczeń sieci (NSG)
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jonor
ms.openlocfilehash: 68655ea03f53fe7100f67d111fcd3c8595bdf4c9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60362178"
---
# <a name="example-1--build-a-simple-dmz-using-nsgs-with-an-azure-resource-manager-template"></a>Przykład 1 — Tworzenie prostej sieci obwodowej za pomocą grup NSG z szablonem usługi Azure Resource Manager
[Wróć do strony zabezpieczeń granic najlepsze praktyki][HOME]

> [!div class="op_single_selector"]
> * [Szablon usługi Resource Manager](virtual-networks-dmz-nsg.md)
> * [Klasyczny — PowerShell](virtual-networks-dmz-nsg-asm.md)
> 
>

W tym przykładzie tworzy sieć obwodową pierwotnych z czterech serwerów z systemem Windows i grupy zabezpieczeń sieci. W tym przykładzie opisano każdej z sekcji odpowiedni szablon, aby zapewnić lepiej zrozumieć każdego kroku. Brak sekcji scenariusza ruchu zapewnienie krok po kroku omówiono sposób ruch będzie kontynuowana za pośrednictwem warstw zabezpieczeń w sieci obwodowej. Na koniec w odwołaniach sekcja jest kod kompletny szablon oraz instrukcje tworzenia tego środowiska do testowania i eksperymentowania z różnymi scenariuszami. 

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] 

![Przychodząca sieć obwodowa z grupy NSG][1]

## <a name="environment-description"></a>Opis środowiska
W tym przykładzie subskrypcja zawiera następujące zasoby:

* Pojedyncza grupa zasobów
* Sieć wirtualną z dwiema podsieciami; "Fronton" i "Wewnętrzna"
* Sieciowa grupa zabezpieczeń, która jest stosowana do obu podsieci
* Windows Server, który reprezentuje serwer aplikacji sieci web ("IIS01")
* Serwery dwa okna, które reprezentują serwery zaplecza aplikacji ("AppVM01", "AppVM02")
* Serwer Windows, który reprezentuje serwer DNS ("DNS01")
* Publiczny adres IP skojarzony z serwerem sieci web aplikacji

W sekcji odwołań znajduje się link do szablonu usługi Azure Resource Manager, który tworzy środowisko opisane w tym przykładzie. Tworzenie maszyn wirtualnych i sieci wirtualne, mimo, że wykonywane przez przykładowy szablon, nie są opisane szczegółowo w tym dokumencie. 

**Do tworzenia tego środowiska** (szczegółowe instrukcje znajdują się w sekcji odwołań do tego dokumentu);

1. Wdrażanie szablonu usługi Resource Manager platformy Azure na: [Szablony szybkiego startu platformy Azure][Template]
2. Zainstaluj przykładową aplikację na: [Przykładowy skrypt aplikacji][SampleApp]

>[!NOTE]
>Dla protokołu RDP do serwerów zaplecza w tym wystąpieniu na serwerze usług IIS jest używany jako "przesiadkowym." Pierwszy protokołu RDP na serwerze usług IIS, a następnie z RDP serwera usług IIS do serwerów zaplecza. Alternatywnie publiczny adres IP może być skojarzone z każdym serwerem kart Sieciowych dla protokołu RDP łatwiejsze.
> 
>

Poniższe sekcje zawierają szczegółowy opis sieciowej grupy zabezpieczeń i jak działa ona w tym przykładzie przez Instruktaż kluczowe wiersze szablonu usługi Azure Resource Manager.

## <a name="network-security-groups-nsg"></a>Sieciowe grupy zabezpieczeń (NSG)
W tym przykładzie grupa sieciowej grupy zabezpieczeń jest utworzone i następnie ładowany za pomocą sześciu reguł. 

>[!TIP]
>Ogólnie rzecz biorąc należy najpierw utworzyć określone reguły "Zezwalaj", a następnie ostatniego bardziej ogólnymi zasadami "Deny". Określają priorytetem, których zasady są oceniane pierwszy. Nie dodatkowe reguły są oceniane, po znalezieniu ruchu do zastosowania do określonej reguły. Reguły sieciowej grupy zabezpieczeń można stosować w jednym kierunku ruchu przychodzącego lub wychodzącego (z punktu widzenia podsieci).
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

Brak domyślną regułę ruchu wychodzącego, która umożliwia ruchu wychodzącego z Internetem. W tym przykładzie firma Microsoft zezwala na ruch wychodzący i nie modyfikowanie żadnych reguł dla ruchu wychodzącego. Aby zastosować zasady zabezpieczeń, aby ruch w obu kierunkach, routingu zdefiniowanego użytkownika jest wymagana i eksplorować w "W przykładzie 3", na [strony najlepsze rozwiązania w zakresie granic zabezpieczeń][HOME].

Każda reguła jest omówiona bardziej szczegółowo w następujący sposób:

1. Zasób sieciowej grupy zabezpieczeń musi być utworzone na potrzeby przechowywania reguły:

    ```JSON
    "resources": [
      {
        "apiVersion": "2015-05-01-preview",
        "type": "Microsoft.Network/networkSecurityGroups",
        "name": "[variables('NSGName')]",
        "location": "[resourceGroup().location]",
        "properties": { }
      }
    ]
    ``` 

2. Pierwszą regułę w tym przykładzie zezwala na ruch DNS między wszystkie sieci wewnętrznej na serwerze DNS w podsieci wewnętrznej bazy danych. Reguła ma kilka ważnych parametrów:
   * "destinationAddressPrefix" - prefiks adresu docelowego jest równa "10.0.2.4", aby ruch DNS będzie mógł uzyskać dostęp do serwera DNS.
   * "Direction" oznacza to, w jakim kierunku przepływu ruchu tej reguły staje się skuteczny. Kierunek jest z punktu widzenia podsieci lub maszyn wirtualnych (zależnie od tego, gdzie powiązana jest ta sieciowa grupa zabezpieczeń). Dlatego jeśli "Ruchu przychodzącego" ma kierunek ruchu wchodzi podsieci, reguła będzie stosowana i ruchu wychodzącego z podsieci nie mogą być naruszone przez tę regułę.
   * "Priority" Ustawia kolejność, w którym jest oceniana przepływu ruchu. Im mniejsza liczba wyższy priorytet. Gdy reguła ma zastosowanie do przepływu ruchu określone, nie dodatkowe reguły są przetwarzane. Dlatego jeśli reguła z priorytetem 1 zezwala na ruch oraz reguły o priorytecie 2 nie zezwala na ruch i obie reguły są stosowane do ruchu, a następnie dozwolone ruch będzie przepływać (ponieważ reguła 1 ma wyższy priorytet, jaki zajęło efektu i nie dodatkowe reguły były stosowane).
   * "Access" oznacza to, czy ruch dotyczy ta reguła jest zablokowane ("Deny"), czy dozwolone ("Zezwalaj").

     ```JSON
     "properties": {
     "securityRules": [
      {
        "name": "enable_dns_rule",
        "properties": {
          "description": "Enable Internal DNS",
          "protocol": "*",
          "sourcePortRange": "*",
          "destinationPortRange": "53",
          "sourceAddressPrefix": "VirtualNetwork",
          "destinationAddressPrefix": "10.0.2.4",
          "access": "Allow",
          "priority": 100,
          "direction": "Inbound"
        }
      },
     ```

3. Ta zasada umożliwia RDP przepływ ruchu z Internetu do portów protokołu RDP na każdym serwerze w powiązanej podsieci. 

    ```JSON
    {
      "name": "enable_rdp_rule",
      "properties": {
        "description": "Allow RDP",
        "protocol": "Tcp",
        "sourcePortRange": "*",
        "destinationPortRange": "3389",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "*",
        "access": "Allow",
        "priority": 110,
        "direction": "Inbound"
      }
    },
    ```

4. Ta reguła zezwala na ruch przychodzący z Internetu do trafień serwer sieci web. Ta zasada nie powoduje zmiany zachowania routingu. Ta reguła zezwala tylko ruch przeznaczony dla IIS01 do przekazania. Dlatego jeśli ruch z Internetu, gdyby serwer sieci web jako miejsca docelowego, ta zasada będzie zezwalała i zatrzymać dalsze przetwarzanie reguł. (W regule priorytetem 140 wszystkich innych ruch przychodzący z Internetu jest zablokowana). Tylko w przypadku przetwarzania ruchu HTTP, ta zasada można dodatkowo ograniczone umożliwia tylko docelowy Port 80.

    ```JSON
    {
      "name": "enable_web_rule",
      "properties": {
        "description": "Enable Internet to [variables('VM01Name')]",
        "protocol": "Tcp",
        "sourcePortRange": "*",
        "destinationPortRange": "80",
        "sourceAddressPrefix": "Internet",
        "destinationAddressPrefix": "10.0.1.5",
        "access": "Allow",
        "priority": 120,
        "direction": "Inbound"
        }
      },
    ```

5. Ta reguła zezwala na ruch do przekazania z serwera IIS01 serwerowi AppVM01 bloków reguł w późniejszym innych frontonu dla ruchu zaplecza. Aby zwiększyć tę regułę, jeśli port jest znany, że ma zostać dodany. Na przykład, jeśli na serwerze usług IIS powoduje osiągnięcie tylko program SQL Server na AppVM01, zakres portów docelowych należy zmienić "*" (wszystkie) 1433 (SQL port), dzięki czemu mniejszych przychodzących ataków na AppVM01 powinien aplikacji sieci web nigdy nie zostaną ujawnione.

    ```JSON
    {
      "name": "enable_app_rule",
      "properties": {
        "description": "Enable [variables('VM01Name')] to [variables('VM02Name')]",
        "protocol": "*",
        "sourcePortRange": "*",
        "destinationPortRange": "*",
        "sourceAddressPrefix": "10.0.1.5",
        "destinationAddressPrefix": "10.0.2.5",
        "access": "Allow",
        "priority": 130,
        "direction": "Inbound"
      }
    },
     ```

6. Reguły można użyć specjalny rodzaj prefiksem adresu o nazwie "Domyślna Tag", tagi te są dostarczane przez system identyfikatorów, które umożliwiają łatwe większych kategorię prefiksy adresów. Ta zasada taga domyślna "VirtualNetwork" dla prefiksu adresu docelowego oznaczającego dowolny adres wewnątrz sieci wirtualnej. Pozostałe etykiety prefiks to Internet i AzureLoadBalancer. Ta zasada nie zezwala na ruch z Internetu do serwerów w sieci. Za pomocą reguł priorytetem 110 i 120 efekt jest umożliwienie tylko ruch przychodzący z Internetu do zapory i porty protokołu RDP na serwerach i bloków, wszystkie inne elementy. Ta reguła jest "odporne na uszkodzenia" regułę blokującą wszystkie przepływy nieoczekiwany.

    ```JSON
    {
      "name": "deny_internet_rule",
      "properties": {
        "description": "Isolate the [variables('VNetName')] VNet from the Internet",
        "protocol": "*",
        "sourcePortRange": "*",
        "destinationPortRange": "*",
        "sourceAddressPrefix": "Internet",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Deny",
        "priority": 140,
        "direction": "Inbound"
      }
    },
     ```

7. Końcowa reguła nie zezwala na ruch z podsieci Frontend do podsieci wewnętrznej bazy danych. Ponieważ ta reguła jest tylko Reguła ruchu przychodzącego, ruch powrotny jest dozwolone (z wewnętrznej bazy danych do serwera sieci Web).

    ```JSON
    {
      "name": "deny_frontend_rule",
      "properties": {
        "description": "Isolate the [variables('Subnet1Name')] subnet from the [variables('Subnet2Name')] subnet",
        "protocol": "*",
        "sourcePortRange": "*",
        "destinationPortRange": "*",
        "sourceAddressPrefix": "[variables('Subnet1Prefix')]",
        "destinationAddressPrefix": "[variables('Subnet2Prefix')]",
        "access": "Deny",
        "priority": 150,
        "direction": "Inbound"
      }
    }
    ```

## <a name="traffic-scenarios"></a>Scenariusze ruchu
#### <a name="allowed-internet-to-web-server"></a>(*Dozwolone*) Internet do serwera sieci web
1. Użytkownik internet żądania na stronie HTTP z publicznego adresu IP karty Sieciowej skojarzonej z kartą Sieciową IIS01
2. Publiczny adres IP przekazuje ruch w sieci wirtualnej w kierunku IIS01 (serwer sieci web)
3. Podsieć frontonu rozpoczyna przetwarzanie reguł dla ruchu przychodzącego:
   1. 1 regułę sieciowej grupy zabezpieczeń (DNS, Domain Name System) nie zastosować, przenieść następną regułę
   2. 2 reguły sieciowej grupy zabezpieczeń (RDP) nie są spełnione, przenieść następną regułę
   3. Zastosuj 3 reguły sieciowej grupy zabezpieczeń (Internet do IIS01), ruch jest przetwarzanie reguł dozwolonych, Zatrzymaj
4. Ruch osiąga wewnętrzny adres IP serwera sieci web IIS01 (10.0.1.5)
5. IIS01 nasłuchuje ruchu w sieci web, odbiera żądania i rozpoczyna przetwarzanie żądania
6. IIS01 programu SQL Server na AppVM01 monituje o podanie informacji
7. Nie reguł ruchu wychodzącego w podsieci Frontend jest dozwolony ruch
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
12. Na serwerze usług IIS odbiera odpowiedź SQL i kończy odpowiedź HTTP i przesyła do osoby żądającej
13. Ponieważ nie ma żadnych reguł ruchu wychodzącego na podsieć frontonu, odpowiedź jest dozwolone i użytkownik Internet otrzymuje żądanej strony sieci web.

#### <a name="allowed-rdp-to-iis-server"></a>(*Dozwolone*) protokołu RDP do serwera IIS
1. Administrator serwera w Internecie żądań sesji protokołu RDP, aby IIS01 publicznego adresu IP karty sieciowej skojarzonej z kartą Sieciową IIS01 (ten publiczny adres IP można znaleźć za pośrednictwem portalu lub programu PowerShell)
2. Publiczny adres IP przekazuje ruch w sieci wirtualnej w kierunku IIS01 (serwer sieci web)
3. Podsieć frontonu rozpoczyna przetwarzanie reguł dla ruchu przychodzącego:
   1. 1 regułę sieciowej grupy zabezpieczeń (DNS, Domain Name System) nie zastosować, przenieść następną regułę
   2. Stosowanie 2 reguły sieciowej grupy zabezpieczeń (RDP), ruch jest przetwarzanie reguł dozwolonych, Zatrzymaj
4. Za pomocą reguł ruchu wychodzącego domyślne reguły i ruch powrotny jest dozwolone
5. Sesję RDP jest włączona.
6. IIS01 wyświetla monit o podanie nazwy użytkownika i hasła

>[!NOTE]
>Dla protokołu RDP do serwerów zaplecza w tym wystąpieniu na serwerze usług IIS jest używany jako "przesiadkowym." Pierwszy protokołu RDP na serwerze usług IIS, a następnie z RDP serwera usług IIS do serwerów zaplecza.
>
>

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

#### <a name="denied-rdp-to-backend"></a>(*Odmowa*) protokołu RDP do wewnętrznej bazy danych
1. Internet użytkownik próbuje nawiązać połączenie RDP serwera AppVM01
2. Ponieważ nie istnieją żadne publicznych adresów IP skojarzone z tym serwerami kart interfejsu Sieciowego, ten ruch nigdy nie należy wprowadzić w sieci wirtualnej, a nie dociera do serwera
3. Jednak jeśli publiczny adres IP został włączony dla jakiegoś powodu, reguła sieciowej grupy zabezpieczeń 2 (RDP) czy zezwalają na ruch tego

>[!NOTE]
>Dla protokołu RDP do serwerów zaplecza w tym wystąpieniu na serwerze usług IIS jest używany jako "przesiadkowym." Pierwszy protokołu RDP na serwerze usług IIS, a następnie z RDP serwera usług IIS do serwerów zaplecza.
>
>

#### <a name="denied-web-to-backend-server"></a>(*Odmowa*) do serwera wewnętrznej bazy danych w sieci Web
1. Użytkownik internet próbuje uzyskać dostęp do pliku na AppVM01
2. Ponieważ nie istnieją żadne publicznych adresów IP skojarzone z tym serwerami kart interfejsu Sieciowego, ten ruch nigdy nie należy wprowadzić w sieci wirtualnej, a nie dociera do serwera
3. Jeśli publiczny adres IP został włączony dla jakiegoś powodu, ten ruch mogłyby spowodować zablokowanie reguły sieciowej grupy zabezpieczeń 5 (Internetu do sieci wirtualnej)

#### <a name="denied-web-dns-look-up-on-dns-server"></a>(*Odmowa*) wyszukiwanie nazwy DNS w sieci Web na serwerze DNS
1. Użytkownik internet próbuje do wyszukania wewnętrzny rekord DNS na DNS01
2. Ponieważ nie istnieją żadne publicznych adresów IP skojarzone z tym serwerami kart interfejsu Sieciowego, ten ruch nigdy nie należy wprowadzić w sieci wirtualnej, a nie dociera do serwera
3. Jeśli publiczny adres IP został włączony dla jakiegoś powodu, ten ruch mogłyby spowodować zablokowanie reguły sieciowej grupy zabezpieczeń 5 (Internet z siecią wirtualną) (Uwaga: czy reguła 1 (DNS) nie ma zastosowania, ponieważ adres źródłowy żądania jest internet i 1 reguła ma zastosowanie tylko do lokalnej sieci wirtualnej jako źródła)

#### <a name="denied-sql-access-on-the-web-server"></a>(*Odmowa*) dostęp SQL na serwerze sieci web
1. Użytkownik internet żąda danych SQL z IIS01
2. Ponieważ nie istnieją żadne publicznych adresów IP skojarzone z tym serwerami kart interfejsu Sieciowego, ten ruch nigdy nie należy wprowadzić w sieci wirtualnej, a nie dociera do serwera
3. Jeśli publiczny adres IP został włączony dla jakiegoś powodu, z podsiecią Frontend rozpoczyna przetwarzanie reguł dla ruchu przychodzącego:
   1. 1 regułę sieciowej grupy zabezpieczeń (DNS, Domain Name System) nie zastosować, przenieść następną regułę
   2. 2 reguły sieciowej grupy zabezpieczeń (RDP) nie są spełnione, przenieść następną regułę
   3. Zastosuj 3 reguły sieciowej grupy zabezpieczeń (Internet do IIS01), ruch jest przetwarzanie reguł dozwolonych, Zatrzymaj
4. Ruch osiąga wewnętrzny adres IP IIS01 (10.0.1.5)
5. IIS01 nie nasłuchuje na porcie 1433, więc nie odpowiedź na żądanie

## <a name="conclusion"></a>Podsumowanie
W tym przykładzie jest stosunkowo prosta i proste sposób odizolowanie podsieci zaplecza z ruchu przychodzącego.

Więcej przykładów i przegląd granice zabezpieczeń sieci można znaleźć [tutaj][HOME].

## <a name="references"></a>Dokumentacja
### <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager
W tym przykładzie używa wstępnie zdefiniowany szablon usługi Azure Resource Manager w repozytorium GitHub, obsługiwane przez firmę Microsoft i Otwórz do społeczności. Tego szablonu można wdrożyć bezpośrednio z serwisu GitHub, lub pobrać i zmodyfikować odpowiednio do potrzeb. 

Szablon głównego znajduje się w pliku o nazwie "azuredeploy.json." Ten szablon można przesyłać za pośrednictwem programu PowerShell lub interfejsu wiersza polecenia (przy użyciu pliku skojarzone "azuredeploy.parameters.json") do wdrożenia tego szablonu. Czy mogę znaleźć Najprostszym sposobem jest użycie przycisku "Wdróż na platformie Azure" na stronie README.md w witrynie GitHub.

Aby wdrożyć szablon, który tworzy tego przykładu z serwisu GitHub i witryny Azure portal, wykonaj następujące kroki:

1. W przeglądarce przejdź do [szablonu][Template]
2. Kliknij przycisk "Wdróż na platformie Azure" (lub przycisk "Visualize", aby wyświetlić graficzną reprezentację tego szablonu)
3. Wprowadź konto magazynu, nazwę użytkownika i hasło w bloku parametrów, a następnie kliknij przycisk **OK**
5. Utwórz grupę zasobów dla tego wdrożenia (w tym celu należy użyć istniejącą grupę, ale zaleca się nową, aby uzyskać najlepsze wyniki)
6. W razie potrzeby zmień ustawienia subskrypcji i lokalizacji sieci wirtualnej.
7. Kliknij przycisk **Przejrzyj postanowienia prawne**, przeczytaj warunki i kliknij przycisk **zakupu** zgody.
8. Kliknij przycisk **Utwórz** umożliwiającą wdrażanie tego szablonu.
9. Po pomyślnym zakończeniu wdrażania przejdź do grupy zasobów utworzonej dla tego wdrożenia do wyświetlania zasobów konfigurowana wewnątrz.

>[!NOTE]
>Ten szablon umożliwia połączenie przez RDP tylko serwer IIS01 (znaleźć publiczny adres IP dla IIS01 w portalu). Dla protokołu RDP do serwerów zaplecza w tym wystąpieniu na serwerze usług IIS jest używany jako "przesiadkowym." Pierwszy protokołu RDP na serwerze usług IIS, a następnie z RDP serwera usług IIS do serwerów zaplecza.
>
>

Aby usunąć to wdrożenie, Usuń grupę zasobów i wszystkie zasoby podrzędne zostaną również usunięte.

#### <a name="sample-application-scripts"></a>Przykładowe skrypty aplikacji
Po pomyślnym uruchomieniu szablon, można skonfigurować serwer sieci web i serwerów aplikacji z prostą aplikację sieci web umożliwia testowanie za pomocą tej konfiguracji sieci obwodowej. Aby zainstalować przykładową aplikację w tym i inne przykłady strefy DMZ, jedno zostało podane z łącza: [Przykładowy skrypt aplikacji][SampleApp]

## <a name="next-steps"></a>Kolejne kroki

* Wdrażanie w tym przykładzie
* Tworzenie przykładowej aplikacji
* Testowanie różnych ruch za pośrednictwem tej sieci obwodowej

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-arm/example1design.png "Przychodząca sieć obwodowa z grupy NSG"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[Template]: https://github.com/Azure/azure-quickstart-templates/tree/master/301-dmz-nsg
[SampleApp]: ./virtual-networks-sample-app.md
