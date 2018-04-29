---
title: Sieciowe grupy zabezpieczeń na platformie Azure | Microsoft Docs
description: Dowiedz się, jak izolować przepływ ruchu i sterować nim w sieciach wirtualnych przy użyciu rozproszonej zapory platformy Azure korzystającej z sieciowych grup zabezpieczeń.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: tysonn
ms.assetid: 20e850fc-6456-4b5f-9a3f-a8379b052bc9
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/11/2016
ms.author: jdial
ms.openlocfilehash: 87ca0a1cd9766d3ad76d0fe5dd29a34ec40ea276
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="filter-network-traffic-with-network-security-groups"></a>Filtrowanie ruchu sieciowego przy użyciu sieciowych grup zabezpieczeń

Sieciowa grupa zabezpieczeń (NSG, network security group) zawiera listę reguł zabezpieczeń, które blokują lub zezwalają na ruch sieciowy do zasobów połączonych z usługami Azure Virtual Network (VNet). Sieciowe grupy zabezpieczeń można skojarzyć z podsieciami, poszczególnymi maszynami wirtualnymi (model klasyczny) lub poszczególnymi interfejsami sieciowymi (NIC) dołączonymi do maszyn wirtualnych (model usługi Resource Manager). Jeśli sieciowa grupa zabezpieczeń jest skojarzona z podsiecią, te reguły są stosowane do wszystkich zasobów połączonych z tą podsiecią. Ruch można ograniczyć jeszcze bardziej, kojarząc sieciową grupę zabezpieczeń również z maszyną wirtualną lub kartą sieciową.
 
> [!NOTE]
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [model wdrażania przy użyciu usługi Azure Resource Manager i model klasyczny](../resource-manager-deployment-model.md). W tym artykule przedstawiono oba modele, ale firma Microsoft zaleca przeprowadzanie większości nowych wdrożeń z zastosowaniem modelu wdrażania przy użyciu usługi Azure Resource Manager.

## <a name="nsg-resource"></a>Zasób sieciowej grupy zabezpieczeń
Sieciowe grupy zabezpieczeń obejmują następujące właściwości:

| Właściwość | Opis | Ograniczenia | Zagadnienia do rozważenia |
| --- | --- | --- | --- |
| Name (Nazwa) |Nazwa sieciowej grupy zabezpieczeń |Musi być unikatowa w obrębie regionu.<br/>Może zawierać litery, cyfry, podkreślenia, kropki i łączniki.<br/>Musi zaczynać się literą lub cyfrą.<br/>Musi kończyć się literą, cyfrą lub podkreśleniem.<br/>Nie może przekraczać 80 znaków. |Ponieważ może być konieczne utworzenie kilku sieciowych grup zabezpieczeń, upewnij się, że dysponujesz konwencją nazewnictwa, która pozwala łatwo identyfikować ich funkcję. |
| Region |[Region](https://azure.microsoft.com/regions) świadczenia usługi Azure, w którym jest tworzona sieciowa grupa zabezpieczeń. |Sieciowe grupy zabezpieczeń można kojarzyć tylko z zasobami znajdującymi się w tym samym regionie co sieciowa grupa zabezpieczeń. |Aby się dowiedzieć, ile sieciowych grup zabezpieczeń może się znajdować w jednym regionie, przeczytaj artykuł dotyczący [limitów platformy Azure](../azure-subscription-service-limits.md#virtual-networking-limits-classic).|
| Grupa zasobów |[Grupa zasobów](../azure-resource-manager/resource-group-overview.md#resource-groups), w której istnieje sieciowa grupa zabezpieczeń. |Chociaż sieciowa grupa zabezpieczeń istnieje w grupie zasobów, może być skojarzona z zasobami w dowolnej grupie zasobów, jeśli tylko zasób jest częścią tego samego regionu świadczenia usługi Azure co sieciowa grupa zabezpieczeń. |Grupy zasobów służą do zarządzania wieloma zasobami równocześnie w ramach jednostki wdrożenia.<br/>Można rozważyć zgrupowanie sieciowej grupy zabezpieczeń z zasobami, z którymi jest ona skojarzona. |
| Reguły |Reguły ruchu przychodzącego i wychodzącego określające, jaki ruch jest dozwolony lub zablokowany. | |Zobacz sekcję [Reguły sieciowej grupy zabezpieczeń](#Nsg-rules) w tym artykule. |

> [!NOTE]
> Listy ACL oparte na punktach końcowych i sieciowe grupy zabezpieczeń nie są obsługiwane w tym samym wystąpieniu maszyny wirtualnej. Jeśli chcesz użyć sieciowej grupy zabezpieczeń, a masz już listę ACL punktów końcowych, najpierw usuń listę ACL punktów końcowych. Aby się dowiedzieć, jak usunąć listę ACL, przeczytaj artykuł [Managing Access Control Lists (ACLs) for Endpoints by using PowerShell](virtual-networks-acl-powershell.md) (Zarządzanie listami kontroli dostępu [ACL] dla punktów końcowych przy użyciu programu PowerShell).
> 

### <a name="nsg-rules"></a>Reguły sieciowej grupy zabezpieczeń
Reguły sieciowych grup zabezpieczeń obejmują następujące właściwości:

| Właściwość | Opis | Ograniczenia | Zagadnienia do rozważenia |
| --- | --- | --- | --- |
| **Nazwa** |Nazwa reguły. |Musi być unikatowa w obrębie regionu.<br/>Może zawierać litery, cyfry, podkreślenia, kropki i łączniki.<br/>Musi zaczynać się literą lub cyfrą.<br/>Musi kończyć się literą, cyfrą lub podkreśleniem.<br/>Nie może przekraczać 80 znaków. |Sieciowa grupa zabezpieczeń może zawierać kilka reguł, więc pamiętaj, aby postępować zgodnie z konwencją nazewnictwa, która umożliwia zidentyfikowanie funkcji reguły. |
| **Protokół** |Protokół odpowiadający regule. |TCP, UDP lub * |Wstawienie znaku * oznacza protokół ICMP (tylko ruch wschód-zachód), a także protokoły UDP i TCP. W ten sposób można zmniejszyć liczbę potrzebnych reguł.<br/>Z drugiej strony użycie znaku * może okazać się rozwiązaniem zbyt ogólnym, dlatego zaleca się używanie znaku * tylko w razie konieczności. |
| **Zakres portów źródłowych** |Zakres portów źródłowych odpowiadający regule. |Numer pojedynczego portu od 1 do 65535, zakres portów (np. 1–65535) lub * (dla wszystkich portów). |Porty źródłowe mogą być efemeryczne. Jeśli program kliencki nie korzysta z określonego portu, należy w większości przypadków użyć znaku *.<br/>Używaj zakresów portów możliwie często, aby uniknąć konieczności korzystania z wielu reguł.<br/>Wielu portów lub zakresów portów nie można oddzielać przecinkiem. |
| **Zakres portów docelowych** |Zakres portów docelowych odpowiadający regule. |Numer pojedynczego portu od 1 do 65535, zakres portów (np. 1–65535) lub \* (dla wszystkich portów). |Używaj zakresów portów możliwie często, aby uniknąć konieczności korzystania z wielu reguł.<br/>Wielu portów lub zakresów portów nie można oddzielać przecinkiem. |
| **Prefiks adresu źródłowego** |Prefiks adresu źródłowego lub znacznik odpowiadający regule. |Pojedynczy adres IP (np. 10.10.10.10), podsieć IP (np. 192.168.1.0/24), [tag usługi](#service-tags) lub * (dla wszystkich adresów). |Rozważ użycie zakresów, tagów usługi i znaków * w celu zmniejszenia liczby reguł. |
| **Prefiks adresu docelowego** |Prefiks adresu docelowego lub znacznik odpowiadający regule. | Pojedynczy adres IP (np. 10.10.10.10), podsieć IP (np. 192.168.1.0/24), [znacznik domyślny](#service-tags) lub * (dla wszystkich adresów). |Rozważ użycie zakresów, tagów usługi i znaków * w celu zmniejszenia liczby reguł. |
| **Kierunek** |Kierunek ruchu odpowiadający regule. |Ruch przychodzący lub wychodzący. |Reguły ruchu przychodzącego i wychodzącego są przetwarzane oddzielnie w zależności od kierunku. |
| **Priorytet** |Reguły są sprawdzane według ważności. Gdy reguła ma zastosowanie, żadne inne reguły nie są sprawdzane pod kątem dopasowania. | Liczba z zakresu od 100 do 4096. | Należy rozważyć utworzenie reguł przez przeskoczenie priorytetów o 100 dla każdej reguły, aby zostawić miejsce na nowe reguły, które mogą zostać utworzone w przyszłości. |
| **Dostęp** |Typ dostępu do zastosowania, jeśli reguła jest dopasowana. | Zezwolenie lub zablokowanie. | Pamiętaj, że jeśli dla pakietu nie zostanie odnaleziona reguła zezwalająca, zostanie on porzucony. |

Sieciowe grupy zabezpieczeń zawierają dwa zestawy reguł: zestaw reguł przychodzących i wychodzących. Priorytety reguł muszą być unikatowe w poszczególnych zestawach. 

![Przetwarzanie reguł sieciowej grupy zabezpieczeń](./media/virtual-network-nsg-overview/figure3.png) 

Na wcześniejszym rysunku przedstawiono, jak są przetwarzane reguły sieciowej grupy zabezpieczeń.

### <a name="default-tags"></a>Tagi systemowe

Tagi usług są dostarczanymi przez system identyfikatorami określającymi kategorię adresów IP. Tagów usług można używać we właściwościach **prefiksu adresu źródłowego** i **prefiksu adresu docelowego** dowolnej reguły zabezpieczeń. Dowiedz się więcej na temat [tagów usług](security-overview.md#service-tags).

### <a name="default-rules"></a>Domyślne reguły zabezpieczeń

Wszystkie sieciowe grupy zabezpieczeń zawierają zestaw domyślnych reguł zabezpieczeń. Reguł domyślnych nie można usunąć, ale ponieważ mają przypisany najniższy priorytet, mogą być zastąpione przez tworzone zasady. Dowiedz się więcej o [domyślnych regułach zabezpieczeń](security-overview.md#default-security-rules).

## <a name="associating-nsgs"></a>Kojarzenie sieciowych grup zabezpieczeń
W zależności od używanego modelu wdrażania sieciową grupę zabezpieczeń można skojarzyć z maszynami wirtualnymi, kartami sieciowymi i podsieciami w następujący sposób:

* **Maszyna wirtualna (tylko model klasyczny):** reguły zabezpieczeń są stosowane do całego ruchu do/z maszyny wirtualnej. 
* **Karta sieciowa (tylko model Resource Manager):** reguły zabezpieczeń są stosowane do całego ruchu do/z karty sieciowej, z którą jest skojarzona sieciowa grupa zabezpieczeń. Na maszynie wirtualnej wyposażonej w wiele kart sieciowych można stosować różne (lub takie same) sieciowe grupy zabezpieczeń do poszczególnych kart sieciowych. 
* **Podsieć (model Resource Manager i klasyczny):** reguły zabezpieczeń są stosowane do dowolnego ruchu do/z dowolnego zasobu połączonego z podsiecią.

Można skojarzyć różne sieciowe grupy zabezpieczeń z maszyną wirtualną (lub kartą sieciową, w zależności od modelu wdrażania) i podsiecią, z którą jest połączona karta sieciowa lub maszyna wirtualna. Reguły zabezpieczeń są stosowane do ruchu sieciowego według priorytetu w poszczególnych sieciowych grupach zabezpieczeń w następującej kolejności:

- **Ruch przychodzący**

  1. **Sieciowa grupa zabezpieczeń zastosowana do podsieci:** jeśli sieciowa grupa zabezpieczeń w podsieci zawiera regułę dopasowania w celu blokowania ruchu, pakiet jest porzucany.

  2. **Sieciowa grupa zabezpieczeń zastosowana do karty sieciowej** (model usługi Resource Manager) lub maszyny wirtualnej (model klasyczny): jeśli sieciowa grupa zabezpieczeń maszyny wirtualnej lub karty sieciowej zawiera regułę dopasowania blokującą ruch, pakiety są porzucane w maszynie wirtualnej lub karcie sieciowej, nawet jeśli sieciowa grupa zabezpieczeń w podsieci ma regułę dopasowania zezwalającą na ruch.

- **Ruch wychodzący**

  1. **Sieciowa grupa zabezpieczeń zastosowana do karty sieciowej** (model usługi Resource Manager) lub maszyny wirtualnej (model klasyczny): jeśli sieciowa grupa zabezpieczeń maszyny wirtualnej lub karty sieciowej zawiera regułę dopasowania blokującą ruch, pakiety są porzucane.

  2. **Sieciowa grupa zabezpieczeń zastosowana do podsieci:** jeśli sieciowa grupa zabezpieczeń podsieci zawiera regułę dopasowania blokującą ruch, pakiety są porzucane, nawet jeśli sieciowa grupa zabezpieczeń maszyny wirtualnej lub karty sieciowej ma regułę dopasowania zezwalającą na ruch.

> [!NOTE]
> Chociaż z podsiecią można skojarzyć tylko jedną sieciową grupę zabezpieczeń, maszynę wirtualną lub kartę sieciową, tę samą sieciową grupę zabezpieczeń można skojarzyć z dowolną liczbą zasobów.
>

## <a name="implementation"></a>Wdrażanie
Sieciowe grupy zabezpieczeń można wdrożyć w modelach wdrażania: usługi Resource Manager oraz klasycznym przy użyciu następujących narzędzi:

| Narzędzie wdrażania | Wdrożenie klasyczne | Resource Manager |
| --- | --- | --- |
| Azure Portal   | Yes | [Tak](virtual-networks-create-nsg-arm-pportal.md) |
| PowerShell     | [Tak](virtual-networks-create-nsg-classic-ps.md) | [Tak](tutorial-filter-network-traffic.md) |
| Interfejs wiersza polecenia platformy Azure **V1**   | [Tak](virtual-networks-create-nsg-classic-cli.md) | [Tak](tutorial-filter-network-traffic-cli.md) |
| Interfejs wiersza polecenia platformy Azure **V2**   | Nie | [Tak](tutorial-filter-network-traffic-cli.md) |
| Szablon usługi Azure Resource Manager   | Nie  | [Tak](template-samples.md) |

## <a name="planning"></a>Planowanie
Przed wdrożeniem sieciowych grup zabezpieczeń należy odpowiedzieć na następujące pytania:

1. Do jakich lub z jakich typów zasobów chcesz filtrować ruch? Możesz podłączyć zasoby, takie jak karty sieciowe, (model usługi Resource Manager), maszyny wirtualne (model klasyczny), usługi Cloud Services, środowiska usług aplikacji i usługa VM Scale Sets. 
2. Czy zasoby, do i z których chcesz filtrować ruch, są połączone z podsieciami w istniejących sieciach wirtualnych?

Aby uzyskać więcej informacji na temat planowania zabezpieczeń sieciowych na platformie Azure, przeczytaj artykuł o [usługach w chmurze i zabezpieczeniach sieciowych](../best-practices-network-security.md). 

## <a name="design-considerations"></a>Zagadnienia dotyczące projektowania
Jeśli znasz już odpowiedzi na pytania w sekcji [Planowanie](#Planning), przed zdefiniowaniem sieciowych grup zabezpieczeń sprawdź następujące sekcje:

### <a name="limits"></a>Limity
Istnieją ograniczenia liczby sieciowych grup zabezpieczeń zawartych w subskrypcji oraz liczby reguł dla poszczególnych sieciowych grup zabezpieczeń. Aby uzyskać więcej informacji o limitach, przeczytaj artykuł dotyczący [limitów platformy Azure](../azure-subscription-service-limits.md#networking-limits).

### <a name="vnet-and-subnet-design"></a>Projekt sieci wirtualnej i podsieci
Ze względu na to, że sieciowe grupy zabezpieczeń można zastosować do podsieci, można zminimalizować liczbę sieciowych grup zabezpieczeń przez grupowanie zasobów według podsieci i zastosowanie sieciowych grup zabezpieczeń do podsieci.  Jeśli zdecydujesz się zastosować sieciowe grupy zabezpieczeń do podsieci, może się okazać, że istniejące sieci wirtualne i podsieci nie zostały zdefiniowane z uwzględnieniem sieciowych grup zabezpieczeń. Istnieje możliwość zdefiniowania nowych sieci wirtualnych i podsieci obsługujących projekt sieciowej grupy zabezpieczeń, a także wdrożenia nowych zasobów do nowych podsieci. Następnie można zdefiniować strategię migracji, aby przenieść istniejące zasoby do nowych podsieci. 

### <a name="special-rules"></a>Reguły specjalne
Jeśli zablokujesz ruch dozwolony przez następujące reguły, infrastruktura nie będzie mogła komunikować się z istotnymi usługami platformy Azure:

* **Wirtualny adres IP węzła hosta:** podstawowe usługi infrastruktury, takie jak DHCP, DNS i monitorowanie kondycji, są realizowane za pośrednictwem zwirtualizowanego adresu IP hosta 168.63.129.16. Ten publiczny adres IP należy do firmy Microsoft i jest jedynym zwirtualizowanym adresem IP używanym do tego celu we wszystkich regionach. Ten adres IP mapuje do fizycznego adresu IP komputera serwera (węzła hosta) obsługującego maszynę wirtualną. Węzeł hosta pełni rolę przekaźnika DHCP, cyklicznego programu rozpoznawania nazw DNS i źródła sondy kondycji modułu równoważenia obciążenia oraz sondy kondycji komputera. Komunikacja z tym adresem IP nie jest atakiem.
* **Licencjonowanie (usługa zarządzania kluczami):** obrazy systemu Windows uruchomione na maszynach wirtualnych muszą być licencjonowane. W celu zapewnienia licencjonowania do serwerów hosta usługi zarządzania kluczami zostaje wysłane żądanie licencjonowania, które takie żądania obsługują. Żądanie jest wysyłane za pomocą portu 1688.

### <a name="icmp-traffic"></a>Ruch protokołu ICMP
Bieżące reguły sieciowej grupy zabezpieczeń uwzględniają tylko protokoły *TCP* lub *UDP*. Nie ma określonego znacznika dla protokołu *ICMP*. Ruch ICMP jest jednak dozwolony w ramach sieci wirtualnej za pomocą reguły domyślnej AllowVNetInBound, która zezwala na ruch do i z dowolnego portu oraz protokołu w ramach sieci wirtualnej.

### <a name="subnets"></a>Podsieci
* Weź pod uwagę liczbę warstw, której wymaga obciążenie. Każdą warstwę można wyizolować przy użyciu podsieci, do której z kolei można zastosować sieciową grupę zabezpieczeń. 
* Jeśli musisz wdrożyć podsieć dla bramy VPN lub obwodu ExpressRoute, **nie** stosuj sieciowej grupy zabezpieczeń do tej podsieci. Jeśli tak zrobisz, może nie działać łączność pomiędzy różnymi sieciami wirtualnymi lub między różnymi lokalizacjami. 
* Jeśli zachodzi potrzeba wdrożenia sieciowego urządzenia wirtualnego, połącz to urządzenie z jego własną podsiecią i utwórz trasy definiowane przez użytkownika do i z tego urządzenia. Możesz wdrożyć sieciową grupę zabezpieczeń poziomu podsieci, aby filtrować ruch do i z tej podsieci. Aby dowiedzieć się więcej o trasach definiowanych przez użytkownika, przeczytaj artykuł [Trasy definiowane przez użytkownika](virtual-networks-udr-overview.md).

### <a name="load-balancers"></a>Moduły równoważenia obciążenia
* Należy rozważyć użycie reguł równoważenia obciążenia i translatora adresów sieciowych dla każdego modułu równoważenia obciążenia używanego przez poszczególne obciążenia. Reguły translatora adresów sieciowych są powiązane z pulą zaplecza zawierającą wystąpienia ról kart sieciowych (model usługi Resource Manager) lub maszyn wirtualnych/usług Cloud Services (model klasyczny). Należy rozważyć utworzenie sieciowej grupy zabezpieczeń dla każdej puli zaplecza, zezwalającej tylko na ruch mapowany za pomocą reguł wdrożonych w modułach równoważenia obciążenia. Utworzenie sieciowej grupy zabezpieczeń dla każdej puli zaplecza pozwala zagwarantować, że ruch przychodzący bezpośrednio do puli zaplecza (z pominięciem modułu równoważenia obciążenia) jest również filtrowany.
* We wdrożeniach klasycznych tworzy się punkty końcowe, które mapują porty w module równoważenia obciążenia na porty na maszynach wirtualnych lub w wystąpieniach ról. Można również utworzyć osobisty publiczny moduł równoważenia obciążenia za pomocą usługi Resource Manager. Port docelowy dla ruchu przychodzącego to rzeczywisty port maszyny wirtualnej lub wystąpienie roli, a nie port udostępniany przez moduł równoważenia obciążenia. Port źródłowy i adres dla połączenia z maszyną wirtualną to port i adres na komputerze zdalnym w Internecie, a nie port i adres udostępniane przez moduł równoważenia obciążenia.
* Podczas tworzenia sieciowych grup zabezpieczeń do filtrowania ruchu przechodzącego przez usługę Azure Load Balancer zastosowany port źródłowy i zakres adresów dotyczą komputera źródłowego, a nie frontonu modułu równoważnia obciążenia. Port docelowy i zakres adresów dotyczą komputera docelowego, a nie frontonu modułu równoważenia obciążenia.
* Jeśli zablokujesz tag AzureLoadBalancer, działanie sond kondycji usługi Azure Load Balancer zakończy się niepowodzeniem, co może mieć wpływ na Twoją usługę.

### <a name="other"></a>Inne
* Listy kontroli dostępu (ACL) oparte na punktach końcowych i sieciowe grupy zabezpieczeń nie są obsługiwane w tym samym wystąpieniu maszyny wirtualnej. Jeśli chcesz użyć sieciowej grupy zabezpieczeń, a masz już listę ACL punktów końcowych, najpierw usuń listę ACL punktów końcowych. Informacje o sposobie usuwania listy ACL punktów końcowych zawiera artykuł [Manage endpoint ACLs](virtual-networks-acl-powershell.md) (Zarządzanie listami ACL opartymi na punktach końcowych).
* Korzystając z usługi Resource Manager, można użyć sieciowej grupy zabezpieczeń skojarzonej z kartą sieciową dla maszyn wirtualnych z wieloma kartami sieciowymi, aby włączyć zarządzanie (dostęp zdalny) dla poszczególnych kart sieciowych. Kojarzenie unikatowych sieciowych grup zabezpieczeń z poszczególnymi kartami sieciowymi umożliwia rozdzielenie typów ruchu między kartami sieciowymi.
* Podobnie jak w przypadku modułów równoważenia obciążenia podczas filtrowania ruchu z innych sieci wirtualnych należy użyć zakresu adresów źródłowych komputera zdalnego, a nie bramy łączącej sieci wirtualne.
* Wielu usług platformy Azure nie można łączyć z sieciami wirtualnymi. Jeśli zasób platformy Azure nie jest podłączony do sieci wirtualnej, nie można użyć sieciowej grupy zabezpieczeń do filtrowania ruchu do tego zasobu.  Przeczytaj dokumentację dotyczącą usług, których używasz, aby ustalić, czy można je podłączyć do sieci wirtualnych.

## <a name="sample-deployment"></a>Przykładowe wdrożenie
Aby zilustrować zastosowanie informacji znajdujących się w tym artykule, należy rozważyć typowy scenariusz dwuwarstwowej aplikacji pokazanej na poniższej ilustracji:

![Sieciowe grupy zabezpieczeń](./media/virtual-network-nsg-overview/figure1.png)

Na diagramie maszyny wirtualne *Web1* i *Web2* są połączone z podsiecią *FrontEnd*, a maszyny wirtualne *DB1* i *DB2* są połączone z podsiecią *BackEnd*.  Obie podsieci są częścią sieci wirtualnej *TestVNet*. Poszczególne składniki aplikacji działają w obrębie maszyny wirtualnej platformy Azure podłączonej do sieci wirtualnej. Ten scenariusz ma następujące wymagania:

1. Rozdzielenie ruchu między serwerami WEB i DB.
2. Reguły równoważenia obciążenia powodują przesyłanie ruchu z modułu równoważenia obciążenia do wszystkich serwerów sieci Web na porcie 80.
3. Reguły NAT modułu równoważenia obciążenia powodują przesyłanie ruchu przeznaczonego dla modułu równoważenia obciążenia na porcie 50001 do portu 3389 na maszynie wirtualnej WEB1.
4. Brak dostępu do maszyn wirtualnych frontonu lub zaplecza z Internetu, z wyjątkiem wymagań numer 2 i 3.
5. Brak wychodzącego dostępu do Internetu z serwerów WEB i DB.
6. Dostęp z podsieci FrontEnd jest dozwolony do portu 3389 dowolnego serwera sieci Web.
7. Dostęp z podsieci FrontEnd jest dozwolony do portu 3389 dowolnego serwera DB.
8. Dostęp z podsieci FrontEnd jest dozwolony do portu 1433 wszystkich serwerów DB.
9. Rozdzielenie ruchu administracyjnego (port 3389) i ruchu bazy danych (1433) na różne karty sieciowe na serwerach DB.

Wymagania od 1 do 6 (z wyjątkiem wymagań 3 i 4) są ograniczone do przestrzeni podsieci. Następujące sieciowe grupy zabezpieczeń spełniają poprzednie wymagania, minimalizując jednocześnie liczbę wymaganych sieciowych grup zabezpieczeń:

### <a name="frontend"></a>FrontEnd
**Reguły ruchu przychodzącego**

| Reguła | Dostęp | Priorytet | Zakres adresów źródłowych | Port źródłowy | Zakres adresów docelowych | Port docelowy | Protokół |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Allow-Inbound-HTTP-Internet | Zezwalaj | 100 | Internet | * | * | 80 | TCP |
| Allow-Inbound-RDP-Internet | Zezwalaj | 200 | Internet | * | * | 3389 | TCP |
| Deny-Inbound-All | Zablokuj | 300 | Internet | * | * | * | TCP |

**Reguły ruchu wychodzącego**

| Reguła | Dostęp | Priorytet | Zakres adresów źródłowych | Port źródłowy | Zakres adresów docelowych | Port docelowy | Protokół |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Deny-Internet-All |Zablokuj |100 | * | * | Internet | * | * |

### <a name="backend"></a>BackEnd
**Reguły ruchu przychodzącego**

| Reguła | Dostęp | Priorytet | Zakres adresów źródłowych | Port źródłowy | Zakres adresów docelowych | Port docelowy | Protokół |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Deny-Internet-All | Zablokuj | 100 | Internet | * | * | * | * |

**Reguły ruchu wychodzącego**

| Reguła | Dostęp | Priorytet | Zakres adresów źródłowych | Port źródłowy | Zakres adresów docelowych | Port docelowy | Protokół |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Deny-Internet-All | Zablokuj | 100 | * | * | Internet | * | * |

Następujące sieciowe grupy zabezpieczeń są tworzone i wiązane z kartami sieciowymi w tych maszynach wirtualnych:

### <a name="web1"></a>WEB1
**Reguły ruchu przychodzącego**

| Reguła | Dostęp | Priorytet | Zakres adresów źródłowych | Port źródłowy | Zakres adresów docelowych | Port docelowy | Protokół |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Allow-Inbound-RDP-Internet | Zezwalaj | 100 | Internet | * | * | 3389 | TCP |
| Allow-Inbound-HTTP-Internet | Zezwalaj | 200 | Internet | * | * | 80 | TCP |

> [!NOTE]
> Zakresem adresów źródłowych dla poprzednich reguł jest **Internet**, a nie wirtualny adres IP modułu równoważenia obciążenia. Port źródłowy ma wartość *, a nie 500001. Reguły NAT dla modułu równoważenia obciążenia nie są takie same jak reguły zabezpieczeń sieciowej grupy zabezpieczeń. Reguły zabezpieczeń sieciowej grupy zabezpieczeń są zawsze powiązane z oryginalnym źródłem i ostatecznym miejscem przeznaczenia ruchu, a **nie** z modułem równoważenia obciążenia między nimi. Usługa Azure Load Balancer zawsze zachowuje źródłowy adres IP i port.
> 
> 

### <a name="web2"></a>WEB2
**Reguły ruchu przychodzącego**

| Reguła | Dostęp | Priorytet | Zakres adresów źródłowych | Port źródłowy | Zakres adresów docelowych | Port docelowy | Protokół |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Deny-Inbound-RDP-Internet | Zablokuj | 100 | Internet | * | * | 3389 | TCP |
| Allow-Inbound-HTTP-Internet | Zezwalaj | 200 | Internet | * | * | 80 | TCP |

### <a name="db-servers-management-nic"></a>Serwery DB (karta sieciowa zarządzania)
**Reguły ruchu przychodzącego**

| Reguła | Dostęp | Priorytet | Zakres adresów źródłowych | Port źródłowy | Zakres adresów docelowych | Port docelowy | Protokół |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Allow-Inbound-RDP-Front-end | Zezwalaj | 100 | 192.168.1.0/24 | * | * | 3389 | TCP |

### <a name="db-servers-database-traffic-nic"></a>Serwery DB (karta sieciowa ruchu bazy danych)
**Reguły ruchu przychodzącego**

| Reguła | Dostęp | Priorytet | Zakres adresów źródłowych | Port źródłowy | Zakres adresów docelowych | Port docelowy | Protokół |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Allow-Inbound-SQL-Front-end | Zezwalaj | 100 | 192.168.1.0/24 | * | * | 1433 | TCP |

Ponieważ niektóre sieciowe grupy zabezpieczeń są skojarzone z poszczególnymi kartami sieciowymi, reguły mają zastosowanie do zasobów wdrożonych za pomocą usługi Resource Manager. Reguły są łączone dla podsieci i karty sieciowej w zależności od sposobu ich kojarzenia. 

## <a name="next-steps"></a>Następne kroki
* [Wdrażanie sieciowych grup zabezpieczeń (model usługi Resource Manager)](virtual-networks-create-nsg-arm-pportal.md).
* [Wdrażanie sieciowych grup zabezpieczeń (model klasyczny)](virtual-networks-create-nsg-classic-ps.md).
* [Manage NSG logs](virtual-network-nsg-manage-log.md) (Zarządzanie dziennikami sieciowej grupy zabezpieczeń).
* [Rozwiązywanie problemów z sieciowymi grupami zabezpieczeń](virtual-network-nsg-troubleshoot-portal.md)
