---
title: Przykład sieci obwodowej na platformie Azure — Tworzenie prostej sieci obwodowej z grup NSG | Dokumentacja firmy Microsoft
description: Tworzenie sieci obwodowej z grup zabezpieczeń sieci (NSG)
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jonor
ms.openlocfilehash: ec29e6b250f927a3a4a94ffdf83d6c7c0e325722
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="example-1--build-a-simple-dmz-using-nsgs-with-an-azure-resource-manager-template"></a>Przykład 1 — Tworzenie prostej sieci obwodowej za pomocą grup NSG z szablonem usługi Azure Resource Manager
[Wróć do strony zabezpieczeń granic najlepsze praktyki][HOME]

> [!div class="op_single_selector"]
> * [Szablon usługi Resource Manager](virtual-networks-dmz-nsg.md)
> * [Classic — PowerShell](virtual-networks-dmz-nsg-asm.md)
> 
>

W tym przykładzie tworzy sieć obwodową pierwotnych z czterech serwerów z systemem Windows i grupy zabezpieczeń sieci. W tym przykładzie przedstawiono sekcjach odpowiedni szablon zapewnienie głębsze zrozumienie każdego kroku. Brak sekcji scenariusza ruchu zapewnienie krok po kroku omówiono sposób ruch będzie kontynuowana za pośrednictwem warstw zabezpieczeń w sieci obwodowej. Na koniec w odwołaniach sekcja jest kod pełną szablonu i instrukcje dotyczące tworzenia tego środowiska, aby przetestować i wypróbować różne scenariusze. 

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] 

![Przychodząca sieć obwodowa z grupy NSG][1]

## <a name="environment-description"></a>Opis elementu środowiska
W tym przykładzie subskrypcja zawiera następujące zasoby:

* Pojedyncza grupa zasobów
* Sieć wirtualną z dwiema podsieciami; "FrontEnd" i "Wewnętrzna"
* Grupy zabezpieczeń sieci jest stosowany do obu podsieci
* Windows Server, który reprezentuje serwer sieci web aplikacji ("IIS01")
* Dwóch systemów windows Server, które reprezentują serwery zaplecza aplikacji ("AppVM01", "AppVM02")
* Windows server, który reprezentuje serwer DNS ("DNS01")
* Publiczny adres IP skojarzone z serwerem aplikacji sieci web

W sekcji odwołań ma łącza do szablonu usługi Azure Resource Manager, który tworzy środowisko opisane w tym przykładzie. Tworzenie maszyn wirtualnych i sieci wirtualnych, mimo że wykonywane przez szablon przykład nie opisano szczegółowo w tym dokumencie. 

**Do tworzenia tego środowiska** (szczegółowe informacje znajdują się w sekcji odwołań tego dokumentu);

1. Wdrażanie szablonu Azure Resource Manager w: [szablonów Szybki Start Azure][Template]
2. Instalowanie przykładowej aplikacji w: [przykładowy skrypt aplikacji][SampleApp]

>[!NOTE]
>Dla protokołu RDP do serwerów zaplecza w tym wystąpieniu na serwerze usług IIS jest używana jako "okno przeskoku". Pierwszy RDP do serwera IIS, a następnie z RDP serwera usług IIS do serwera zaplecza. Alternatywnie publicznego adresu IP może być skojarzony z każdym serwerem kart interfejsu Sieciowego protokołu RDP łatwiejsze.
> 
>

Poniższe sekcje zawierają szczegółowy opis grupy zabezpieczeń sieci i jak działa w tym przykładzie przez Instruktaż klucza wierszy szablon Menedżera zasobów Azure.

## <a name="network-security-groups-nsg"></a>Sieciowe grupy zabezpieczeń (NSG)
Na przykład grupy NSG jest wbudowana i następnie ładowane przy użyciu sześciu reguł. 

>[!TIP]
>Ogólnie rzecz biorąc należy najpierw utworzyć określonych reguł "Zezwalaj", a następnie ostatni bardziej ogólnym reguły "Deny". Określają priorytetem, do których zasady są oceniane pierwszej. Po znalezieniu ruchu do zastosowania do określonej reguły nie dalsze reguły są sprawdzane. Reguły NSG można zastosować w jednym kierunku ruchu przychodzącego lub wychodzącego (z punktu widzenia podsieci).
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

Brak domyślnej regule wychodzącej, która umożliwia ruchu wychodzącego do Internetu. Na przykład firma Microsoft zezwala na ruch wychodzący i nie modyfikowanie reguł wychodzących. Aby zastosować zasady zabezpieczeń do ruchu wychodzącego w obu kierunkach, zdefiniowane routingu użytkownika jest wymagana i jest przedstawione w "W przykładzie 3" na [strony najlepsze rozwiązania w zakresie granic zabezpieczeń][HOME].

Każda reguła omówiono bardziej szczegółowo w następujący sposób:

1. Do przechowywania reguł, można utworzyć wystąpienia zasobu sieciowej grupy zabezpieczeń:

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

2. Pierwsza reguła w tym przykładzie zezwala na ruch DNS od wszystkich sieci wewnętrznej na serwerze DNS w podsieci wewnętrznej bazy danych. Reguła ma pewne ważne parametry:
  * "destinationAddressPrefix" - reguły można używać specjalnego typu o nazwie "Domyślna Tag" prefiksu adresu, tagi te są dostarczane przez system identyfikatorów, które umożliwiają łatwe większych kategorię prefiksy adresów. Ta zasada taga domyślna "Internet" oznaczającego dowolny adres poza siecią wirtualną. Inne etykiety prefiks są VirtualNetwork i AzureLoadBalancer.
  * "Direction" oznacza, że w kierunku przepływu ruchu obowiązuje tej reguły. Kierunek jest z punktu widzenia podsieci lub maszyny wirtualnej (w zależności od tego, gdzie jest powiązany ten NSG). W związku z tym jeśli kierunek jest "Przychodzącego" i ruchu jest wprowadzanie podsieci, reguła powinna zostać zastosowana i ruchu wychodzącego do podsieci nie może mieć wpływ na przez tę regułę.
  * "Priority" Ustawia kolejność, w jakiej są oceniane przepływu ruchu. Im mniejsza liczba wyższy priorytet. Jeśli reguła ma zastosowanie do określonego ruchu, żadne dalsze reguły są przetwarzane. W związku z tym jeśli reguła o priorytecie 1 zezwala na ruch i reguły o priorytecie 2 nie zezwala na ruch i obie zasady są stosowane do ruchu, a następnie ruch będzie dozwolony przepływ (ponieważ reguła 1 ma wyższy priorytet trwało efektu i żadne dodatkowe reguły zostały zastosowane).
  * "Access" oznacza, że jeśli wpływ ta reguła jest zablokowane ("Deny") lub dozwolonych ("Zezwalaj").

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

3. Ta reguła zezwala na ruch RDP mogą przepływać z Internetu z portem RDP na dowolnym serwerze podsieci powiązania. 

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

4. Ta zasada umożliwia ruch przychodzący z Internetu trafienie serwera sieci web. Ta zasada nie powoduje zmiany zachowania routingu. Ta reguła zezwala tylko ruch kierowany do IIS01 do przekazania. W związku z tym jeśli ruch z Internetu miał serwera sieci web jako miejsca docelowego tej reguły spowoduje zezwolenie i zatrzymać dalsze przetwarzanie reguł. (W regule priorytetem 140 wszystkich innych przychodzącego ruchu internetowego jest zablokowana). Tylko w przypadku przetwarzania ruchu HTTP, ta zasada można dodatkowo ograniczony umożliwiają tylko docelowy Port 80.

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

5. Ta zasada umożliwia ruch z serwera IIS01 na serwerze AppVM01 nowsze bloków reguł innych frontonu dla ruchu wewnętrznej bazy danych. Aby poprawić tej reguły, jeśli znane jest port, który ma zostać dodany. Na przykład, jeśli serwer usług IIS jest naciśnięcie tylko programu SQL Server na AppVM01, zakres portów docelowych należy zmienić "*" (Any) 1433 (SQL port), dzięki czemu mniejsze przychodzących ataki AppVM01 aplikacji sieci web kiedykolwiek zagrożenia bezpieczeństwa.

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

6. Ta reguła nie zezwala na ruch z Internetu do serwerów w sieci. Z regułami priorytetem 110 i 120 efekt jest umożliwienie tylko internet ruchu przychodzącego zapory i porty protokołu RDP na serwerach i bloków, wszystkie inne elementy. Ta reguła jest regułą "awaryjnego" Aby zablokować wszystkie przepływy nieoczekiwany.

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

7. Reguła końcowego nie zezwala na ruch z podsieci frontonu do podsieci wewnętrznej bazy danych. Ponieważ ta reguła jest tylko regułę ruchu przychodzącego, odwrotnej ruch jest dozwolony (z wewnętrznej bazy danych do serwera sieci Web).

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
#### <a name="allowed-internet-to-web-server"></a>(*Dozwolone*) Internetu do serwera sieci web
1. Użytkownik internet zażąda strony HTTP od publicznego adresu IP karty Sieciowej, skojarzonych z IIS01 kart interfejsu Sieciowego
2. Publiczny adres IP przekazuje ruch do sieci wirtualnej kierunku IIS01 (serwer sieci web)
3. Podsieci frontonu rozpoczyna przetwarzanie przychodzących reguł:
  1. Reguły NSG 1 DNS (Domain Name System) nie są spełnione, przejść do następnej reguły
  2. 2 reguły NSG (RDP) nie są spełnione, przejść do następnej reguły
  3. Zastosować grupy NSG zasady 3 (Internet w celu IIS01), ruch jest dozwolony, stop reguły przetwarzania
4. Ruch trafienia wewnętrzny adres IP serwera sieci web IIS01 (10.0.1.5)
5. IIS01 nasłuchuje ruchu w sieci web, otrzymuje tego żądania i rozpoczyna przetwarzanie żądania
6. IIS01 żąda od serwera SQL na AppVM01 informacji
7. Nie reguł dla ruchu wychodzącego w podsieci frontonu, ruch jest dozwolony
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
12. Serwer usług IIS otrzyma odpowiedź SQL i kończy odpowiedź HTTP i przesyła do osoby żądającej
13. Ponieważ nie ma żadnych reguł dla ruchu wychodzącego w podsieci frontonu, odpowiedź jest dozwolone i użytkownik Internet otrzymuje żądanej strony sieci web.

#### <a name="allowed-rdp-to-iis-server"></a>(*Dozwolone*) RDP do serwera IIS
1. Administrator serwera w Internecie żądań sesji protokołu RDP do IIS01 na publiczny adres IP karty sieciowej, skojarzonych z kart IIS01 (ten publiczny adres IP znajduje się za pośrednictwem portalu lub programu PowerShell)
2. Publiczny adres IP przekazuje ruch do sieci wirtualnej kierunku IIS01 (serwer sieci web)
3. Podsieci frontonu rozpoczyna przetwarzanie przychodzących reguł:
  1. Reguły NSG 1 DNS (Domain Name System) nie są spełnione, przejść do następnej reguły
  2. Zastosuj 2 reguły NSG (RDP), ruch jest dozwolony, stop reguły przetwarzania
4. Z nie reguł dla ruchu wychodzącego Zastosuj reguły domyślne i zwracany ruch jest dozwolony
5. Włączono sesji protokołu RDP
6. IIS01 monit o podanie nazwy użytkownika i hasła

>[!NOTE]
>Dla protokołu RDP do serwerów zaplecza w tym wystąpieniu na serwerze usług IIS jest używana jako "okno przeskoku". Pierwszy RDP do serwera IIS, a następnie z RDP serwera usług IIS do serwera zaplecza.
>
>

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

#### <a name="denied-rdp-to-backend"></a>(*Odmowa*) RDP do wewnętrznej bazy danych
1. Użytkownik internet próbuje RDP do serwera AppVM01
2. Ponieważ nie ma żadnych publiczne adresy IP skojarzone z tym serwerami kart interfejsu Sieciowego, tego rodzaju ruch nigdy nie wprowadzić sieci wirtualnej, a nie dociera do serwera
3. Jednak jeśli do publicznego adresu IP zostały włączone dla jakiegoś powodu, reguły NSG 2 (RDP) umożliwiałyby ten ruch

>[!NOTE]
>Dla protokołu RDP do serwerów zaplecza w tym wystąpieniu na serwerze usług IIS jest używana jako "okno przeskoku". Pierwszy RDP do serwera IIS, a następnie z RDP serwera usług IIS do serwera zaplecza.
>
>

#### <a name="denied-web-to-backend-server"></a>(*Odmowa*) do serwera wewnętrznej bazy danych w sieci Web
1. Użytkownik internet próbuje uzyskać dostęp do pliku na AppVM01
2. Ponieważ nie ma żadnych publiczne adresy IP skojarzone z tym serwerami kart interfejsu Sieciowego, tego rodzaju ruch nigdy nie wprowadzić sieci wirtualnej, a nie dociera do serwera
3. Jeśli do publicznego adresu IP zostały włączone dla jakiegoś powodu, reguły NSG 5 (Internet do sieci wirtualnej) czy zablokować ruch

#### <a name="denied-web-dns-look-up-on-dns-server"></a>(*Odmowa*) wyszukiwanie nazwy DNS w sieci Web na serwerze DNS
1. Internet próby odszukania wewnętrzny rekord DNS na DNS01
2. Ponieważ nie ma żadnych publiczne adresy IP skojarzone z tym serwerami kart interfejsu Sieciowego, tego rodzaju ruch nigdy nie wprowadzić sieci wirtualnej, a nie dociera do serwera
3. Jeśli do publicznego adresu IP zostały włączone dla jakiegoś powodu, reguły NSG 5 (Internet do sieci wirtualnej) czy zablokować ruch (Uwaga: Aby reguła 1 (DNS) nie ma zastosowania, ponieważ adres źródłowy żądania jest internet i 1 reguła ma zastosowanie tylko do lokalnej sieci wirtualnej jako źródło)

#### <a name="denied-sql-access-on-the-web-server"></a>(*Odmowa*) dostęp SQL na serwerze sieci web
1. Użytkownik internet żąda danych SQL z IIS01
2. Ponieważ nie ma żadnych publiczne adresy IP skojarzone z tym serwerami kart interfejsu Sieciowego, tego rodzaju ruch nigdy nie wprowadzić sieci wirtualnej, a nie dociera do serwera
3. Jeśli do publicznego adresu IP zostały włączone dla jakiegoś powodu, podsieci frontonu rozpoczyna przetwarzanie przychodzącej reguły:
  1. Reguły NSG 1 DNS (Domain Name System) nie są spełnione, przejść do następnej reguły
  2. 2 reguły NSG (RDP) nie są spełnione, przejść do następnej reguły
  3. Zastosować grupy NSG zasady 3 (Internet w celu IIS01), ruch jest dozwolony, stop reguły przetwarzania
4. Wewnętrzny adres IP IIS01 trafienia ruchu (10.0.1.5)
5. IIS01 nie nasłuchuje na porcie 1433, więc nie ma odpowiedzi na żądanie

## <a name="conclusion"></a>Podsumowanie
W tym przykładzie jest stosunkowo proste i bezpośrednio do przodu sposobem izolowanie podsieci wewnętrznej z ruchu przychodzącego.

Więcej przykładów i Przegląd granic zabezpieczeń sieci można znaleźć [tutaj][HOME].

## <a name="references"></a>Dokumentacja
### <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager
W tym przykładzie używa wstępnie zdefiniowanych szablonów usługi Azure Resource Manager w repozytorium GitHub obsługiwanego przez firmę Microsoft i dla społeczności. Tego szablonu można wdrożyć bezpośrednio z witryny GitHub, lub pobrać i zmodyfikować odpowiednio do potrzeb. 

Główny szablon znajduje się w pliku o nazwie "azuredeploy.json." Ten szablon może zostać przesłane za pomocą programu PowerShell lub interfejsu wiersza polecenia (przy użyciu pliku skojarzone "azuredeploy.parameters.json") do wdrożenia tego szablonu. Odnalezienie Najprostszym sposobem jest użycie przycisku "Wdrożenia do platformy Azure" na stronie README.md w witrynie GitHub.

Aby wdrożyć szablon, który tworzy w tym przykładzie z serwisu GitHub i portalu Azure, wykonaj następujące kroki:

1. W przeglądarce przejdź do [szablonu][Template]
2. Kliknij przycisk "Wdrożenia do platformy Azure" (lub przycisk "Wizualizacja", aby wyświetlić graficzną reprezentację tego szablonu)
3. Wprowadź konto magazynu, nazwę użytkownika i hasło w bloku parametrów, a następnie kliknij przycisk **OK**
5. Utwórz grupę zasobów dla tego wdrożenia (można użyć istniejącego, ale zalecane jest nowy, aby uzyskać najlepsze wyniki)
6. W razie potrzeby zmień ustawienia subskrypcji i lokalizacji sieci wirtualnej.
7. Kliknij przycisk **Przejrzyj postanowienia prawne**, przeczytaj warunki i kliknij przycisk **zakupu** do wyrażenia zgody.
8. Kliknij przycisk **Utwórz** aby rozpocząć wdrażanie tego szablonu.
9. Po pomyślnym wdrożeniu, przejdź do grupy zasobów utworzonej dla tego wdrożenia do wyświetlania zasobów, skonfigurowane w.

>[!NOTE]
>Ten szablon umożliwia RDP tylko serwera IIS01 (Znajdź publicznego adresu IP dla IIS01 w portalu). Dla protokołu RDP do serwerów zaplecza w tym wystąpieniu na serwerze usług IIS jest używana jako "okno przeskoku". Pierwszy RDP do serwera IIS, a następnie z RDP serwera usług IIS do serwera zaplecza.
>
>

Aby usunąć to wdrożenie, Usuń grupę zasobów i wszystkie zasoby podrzędne zostaną również usunięte.

#### <a name="sample-application-scripts"></a>Przykładowe skrypty aplikacji
Po pomyślnym uruchomieniu szablon, można skonfigurować serwer sieci web i serwerów aplikacji z prostą aplikację sieci web umożliwia testowanie za pomocą tej konfiguracji sieci obwodowej. Aby zainstalować przykładową aplikację dla tego i innych przykładowych sieci obwodowych, jeden podano przy użyciu następującego łącza: [przykładowy skrypt aplikacji][SampleApp]

## <a name="next-steps"></a>Kolejne kroki

* W tym przykładzie wdrożenia
* Tworzenie przykładowej aplikacji
* Testowanie różnych ruch za pośrednictwem tej sieci obwodowej

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-arm/example1design.png "Przychodząca sieć obwodowa z grupy NSG"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[Template]: https://github.com/Azure/azure-quickstart-templates/tree/master/301-dmz-nsg
[SampleApp]: ./virtual-networks-sample-app.md