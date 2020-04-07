---
title: Zaplanuj swój system VFXT Avere - Azure
description: W tym artykule opisano planowanie przed wdrożeniem avere vFXT for Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: rohogue
ms.openlocfilehash: 6acc1ffd197ddba4290ff7c0751b259d98a70927
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754392"
---
# <a name="plan-your-avere-vfxt-system"></a>Planowanie systemu Avere vFXT

W tym artykule wyjaśniono, jak zaplanować nowy klaster Avere vFXT dla platformy Azure, który jest pozycjonowany i dopasowywał się odpowiednio do Twoich potrzeb.

Przed udaniem się do portalu Azure Marketplace lub utworzeniem maszyn wirtualnych należy wziąć pod uwagę następujące szczegóły:

* W jaki sposób klaster będzie współdziałać z innymi zasobami platformy Azure?
* Gdzie powinny znajdować się elementy klastra w sieciach prywatnych i podsieciach?
* Jakiego typu magazynu zaplecza będzie używany i jak klaster będzie do niego uzyskiwać dostęp?
* Jak wydajne muszą być węzły klastra do obsługi przepływu pracy?

Czytaj dalej, aby dowiedzieć się więcej.

## <a name="learn-the-components-of-the-system"></a>Poznaj elementy systemu

Może być przydatne do zrozumienia składników systemu Avere vFXT for Azure po rozpoczęciu planowania.

* Węzły klastra — klaster składa się z co najmniej trzech maszyn wirtualnych skonfigurowanych jako węzły klastra. Więcej węzłów daje systemowi wyższą przepływność i większą pamięć podręczną.

* Pamięć podręczna — pojemność pamięci podręcznej jest dzielona równo między węzły klastra. Ustaw rozmiar pamięci podręcznej dla węzłów podczas tworzenia klastra; rozmiary węzłów są dodawane, aby stać się całkowity rozmiar pamięci podręcznej.

* Kontroler klastra — kontroler klastra jest dodatkową maszyną wirtualną znajdującą się wewnątrz tej samej podsieci co węzły klastra. Kontroler jest potrzebny do utworzenia klastra i bieżących zadań zarządzania.

* Magazyn zaplecza — dane, które mają być buforowane są przechowywane przez długi czas w systemie magazynu sprzętu lub kontenera obiektów blob platformy Azure. Można dodać magazyn po utworzeniu klastra Avere vFXT for Azure lub korzystając z magazynu obiektów Blob, można dodać i skonfigurować kontener podczas tworzenia klastra.

* Klienci — maszyny klienckie, które używają buforowanych plików, łączą się z klastrem przy użyciu wirtualnej ścieżki plików zamiast bezpośredniego uzyskiwania dostępu do systemów magazynowania. (Więcej informacji można przeczytać w [umiań Montaż klastra VFXT Avere).](avere-vfxt-mount-clients.md)

## <a name="subscription-resource-group-and-network-infrastructure"></a>Subskrypcja, grupa zasobów i infrastruktura sieciowa

Należy wziąć pod uwagę, gdzie będą elementy wdrożenia avere vFXT dla platformy Azure. Na poniższym diagramie przedstawiono możliwe rozmieszczenie składników Avere vFXT for Azure:

![Diagram przedstawiający kontroler klastra i maszyny wirtualne klastra w jednej podsieci. Wokół granicy podsieci jest granica sieci wirtualnej. Wewnątrz sieci wirtualnej jest sześciokąt reprezentujący punkt końcowy usługi magazynu; jest połączony strzałką przerywaną do magazynu obiektów Blob poza siecią wirtualną.](media/avere-vfxt-components-option.png)

Podczas planowania infrastruktury sieciowej klastra VXT avere:

* Utwórz nową subskrypcję dla każdego wdrożenia Avere vFXT dla platformy Azure. Zarządzaj wszystkimi składnikami w tej subskrypcji.

  Korzyści z korzystania z nowej subskrypcji dla każdego wdrożenia obejmują:
  * Prostsze śledzenie kosztów — wyświetlanie i inspekcja wszystkich kosztów z zasobów, infrastruktury i cykli obliczeniowych w jednej subskrypcji.
  * Łatwiejsze oczyszczanie — można usunąć całą subskrypcję po zakończeniu projektu.
  * Wygodne partycjonowanie przydziałów zasobów — izolowanie klientów vFXT avere i klastra w jednej subskrypcji w celu ochrony innych krytycznych obciążeń przed możliwym ograniczaniem zasobów. Ta separacja zapobiega konfliktom podczas wychowania dużej liczby klientów dla przepływu pracy obliczeń o wysokiej wydajności.

* Znajdź systemy obliczeniowe klienta w pobliżu klastra vFXT. Pamięć zaplecza może być bardziej zdalna.  

* Zlokalizuj klaster vFXT i maszynę wirtualną kontrolera klastra razem — w szczególności powinny być:

  * W tej samej sieci wirtualnej
  * W tej samej grupie zasobów
  * Korzystanie z tego samego konta magazynu
  
  Szablon tworzenia klastra obsługuje tę konfigurację w większości sytuacji.

* Klaster musi znajdować się we własnej podsieci, aby uniknąć konfliktów adresów IP z klientami lub innymi zasobami obliczeniowymi.

* Szablon tworzenia klastra służy do tworzenia większości potrzebnych zasobów infrastruktury dla klastra, w tym grup zasobów, sieci wirtualnych, podsieci i kont magazynu.

  Jeśli chcesz użyć zasobów, które już istnieją, upewnij się, że spełniają wymagania w tej tabeli.

  | Zasób | Użyj istniejących? | Wymagania |
  |----------|-----------|----------|
  | Grupa zasobów | Tak, jeśli jest pusty | Musi być pusty|
  | Konto magazynu | **Tak,** jeśli połączenie istniejącego kontenera obiektów blob po utworzeniu klastra <br/>  **Nie,** jeśli tworzenie nowego kontenera obiektów Blob podczas tworzenia klastra | Istniejący kontener obiektów blob musi być pusty <br/> &nbsp; |
  | Sieć wirtualna | Tak | Musi zawierać punkt końcowy usługi magazynu podczas tworzenia nowego kontenera obiektów Blob platformy Azure |
  | Podsieć | Tak | Nie może zawierać innych zasobów |

## <a name="ip-address-requirements"></a>Wymagania dotyczące adresów IP

Upewnij się, że podsieć klastra ma wystarczająco duży zakres adresów IP do obsługi klastra.

Klaster Avere vFXT używa następujących adresów IP:

* Jeden adres IP zarządzania klastrem. Ten adres można przenieść z węzła do węzła w klastrze w razie potrzeby, dzięki czemu jest zawsze dostępny. Ten adres służy do łączenia się z narzędziem konfiguracyjnym Panelu sterowania Avere.
* Dla każdego węzła klastra:
  * Co najmniej jeden adres IP skierowany do klienta. (Wszystkie adresy skierowane do klienta są zarządzane przez *serwer vserver*klastra, który może przenosić adresy IP między węzłami w razie potrzeby).
  * Jeden adres IP do komunikacji klastra
  * Adres IP jednego wystąpienia (przypisany do maszyny Wirtualnej)

Jeśli używasz magazynu obiektów Blob platformy Azure, może również wymagać adresów IP z sieci wirtualnej klastra:  

* Konto magazynu obiektów Blob platformy Azure wymaga co najmniej pięciu adresów IP. Należy pamiętać o tym wymaganiu, jeśli magazyn obiektów Blob zostanie zlokalizowany w tej samej sieci wirtualnej co klaster.
* Jeśli używasz magazynu obiektów Blob platformy Azure, który znajduje się poza siecią wirtualną klastra, utwórz punkt końcowy usługi magazynu wewnątrz sieci wirtualnej. Punkt końcowy nie używa adresu IP.

Istnieje możliwość zlokalizowania zasobów sieciowych i magazynu obiektów Blob (jeśli jest używana) w różnych grupach zasobów z klastra.

## <a name="vfxt-node-size"></a>Rozmiar węzła vFXT

Maszyny wirtualne, które służą jako węzły klastra określić przepływność żądania i pojemność magazynu pamięci podręcznej. <!-- The instance type offered has been chosen for its memory, processor, and local storage characteristics. You can choose from two instance types, with different memory, processor, and local storage characteristics. -->

Każdy węzeł vFXT będzie identyczny. Oznacza to, że jeśli utworzysz klaster z trzema węzłami, będziesz mieć trzy maszyny wirtualne tego samego typu i rozmiaru.

| Typ wystąpienia | Procesory wirtualne | Memory (Pamięć)  | Lokalna pamięć masowa SSD  | Maks. liczba dysków danych | Przepływność dysku bez buforów | Karta sieciowa (liczba) |
| --- | --- | --- | --- | --- | --- | --- |
| Standardowa_E32s_v3 | 32  | 256 GiB | 512 GiB  | 32 | 51 200 koi <br/> 768 deputowanych | 16 000 mb/s (8)  |

Pamięć podręczna dysku na węzeł jest konfigurowalna i może szaleć od 1000 GB do 8000 GB. 4 TB na węzeł jest zalecany rozmiar pamięci podręcznej dla Standard_E32s_v3 węzłów.

Aby uzyskać dodatkowe informacje na temat tych maszyn wirtualnych, przeczytaj dokumentację platformy Microsoft Azure: [Rozmiary maszyn wirtualnych zoptymalizowane pod kątem pamięci](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)

## <a name="account-quota"></a>Przydział konta

Upewnij się, że subskrypcja ma zdolność do uruchamiania klastra VFXT Avere, a także wszelkich używanych systemów obliczeniowych lub klienckich. Przeczytaj [przydział dla klastra vFXT, aby](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) uzyskać szczegółowe informacje.

## <a name="back-end-data-storage"></a>Przechowywanie danych zaplecza

Systemy magazynu zaplecza dostarczają pliki do pamięci podręcznej klastra, a także odbierają zmienione dane z pamięci podręcznej. Zdecyduj, czy zestaw roboczy będzie przechowywany długoterminowo w nowym kontenerze obiektów Blob, czy w istniejącym systemie magazynowania (w chmurze lub sprzęcie). Te systemy pamięci masowej zaplecza są nazywane *głównymi filerami.*

### <a name="hardware-core-filers"></a>Podstawowe filery sprzętowe

Po utworzeniu klastra należy dodać sprzętowe systemy pamięci masowej do klastra vFXT. Można używać różnych popularnych systemów sprzętowych, w tym systemów lokalnych, o ile system magazynowania można uzyskać z podsieci klastra.

Przeczytaj [pozycję Konfiguruj magazyn,](avere-vfxt-add-storage.md) aby uzyskać szczegółowe instrukcje dotyczące dodawania istniejącego systemu magazynu do klastra VFXT Avere.

### <a name="cloud-core-filers"></a>Filery z rdzeniem chmury

System Avere vFXT for Azure może używać pustych kontenerów obiektów blob do magazynu zaplecza. Kontenery muszą być puste po dodaniu do klastra — system vFXT musi być w stanie zarządzać magazynem obiektów bez konieczności zachowywania istniejących danych.

> [!TIP]
> Jeśli chcesz użyć magazynu obiektów Blob platformy Azure dla zaplecza, utwórz nowy kontener jako część tworzenia klastra vFXT. Szablon tworzenia klastra można utworzyć i skonfigurować nowy kontener obiektów Blob, tak aby był gotowy do użycia, jak tylko klaster jest dostępny. Dodawanie kontenera później jest bardziej skomplikowane.
>
> Przeczytaj [artykuł Utwórz avere vFXT for Azure,](avere-vfxt-deploy.md#create-the-avere-vfxt-for-azure) aby uzyskać szczegółowe informacje.

Po dodaniu pustego kontenera magazynu obiektów blob jako głównego filera można skopiować do niego dane za pośrednictwem klastra. Użyj równoległego, wielowątkowego mechanizmu kopiowania. Przeczytaj Tryb [Przenoszenie danych do klastra vFXT,](avere-vfxt-data-ingest.md) aby dowiedzieć się, jak wydajnie kopiować dane do nowego kontenera klastra przy użyciu komputerów klienckich i pamięci podręcznej Avere vFXT.

## <a name="cluster-access"></a>Dostęp do klastra

Klaster Avere vFXT for Azure znajduje się w podsieci prywatnej, a klaster nie ma publicznego adresu IP. Musisz mieć jakiś sposób dostępu do podsieci prywatnej dla administrowania klastrem i połączeń klientów.

Opcje dostępu obejmują:

* Host szybkiego dostępu — przypisz publiczny adres IP do oddzielnej maszyny Wirtualnej w sieci prywatnej i użyj go do utworzenia tunelu TLS do węzłów klastra.

  > [!TIP]
  > Jeśli ustawisz publiczny adres IP na kontrolerze klastra, można go użyć jako hosta szybkiego dostępu. Przeczytaj [kontroler klastra jako hosta szybkiego dostępu, aby](#cluster-controller-as-jump-host) uzyskać więcej informacji.

* Wirtualna sieć prywatna (VPN) — konfigurowanie sieci VPN typu lokacja lub lokacja między siecią prywatną w sieciach platformy Azure i firmowych.

* Usługa Azure ExpressRoute — konfigurowanie połączenia prywatnego za pośrednictwem partnera usługi ExpressRoute.

Aby uzyskać szczegółowe informacje na temat tych opcji, przeczytaj [dokumentację usługi Azure Virtual Network dotyczącą komunikacji internetowej.](../virtual-network/virtual-networks-overview.md#communicate-with-the-internet)

### <a name="cluster-controller-as-jump-host"></a>Kontroler klastra jako host skoku

Jeśli ustawisz publiczny adres IP na kontrolerze klastra, możesz użyć go jako hosta szybkiego dostępu do kontaktu z klastrem VFXT Avere spoza podsieci prywatnej. Jednak ponieważ kontroler ma uprawnienia dostępu do modyfikowania węzłów klastra, stwarza to niewielkie zagrożenie bezpieczeństwa.

Aby zwiększyć bezpieczeństwo kontrolera z publicznym adresem IP, skrypt wdrażania automatycznie tworzy sieciową grupę zabezpieczeń, która ogranicza dostęp przychodzący tylko do portu 22. Można dodatkowo chronić system, blokując dostęp do zakresu adresów źródłowych IP — oznacza to, że zezwalaj tylko na połączenia z maszyn, których zamierzasz używać do uzyskiwania dostępu do klastra.

Podczas tworzenia klastra można wybrać, czy ma być utworzony publiczny adres IP na kontrolerze klastra.

* W przypadku utworzenia **nowej sieci wirtualnej** lub **nowej podsieci**kontroler klastra zostanie przypisany **publiczny** adres IP.
* Jeśli wybierzesz istniejącą sieć wirtualną i podsieć, kontroler klastra będzie miał tylko **prywatne** adresy IP.

## <a name="vm-access-roles"></a>Role dostępu do maszyn wirtualnych

Platforma Azure używa [kontroli dostępu opartej na rolach](../role-based-access-control/index.yml) (RBAC) do autoryzowania maszyn wirtualnych klastra do wykonywania niektórych zadań. Na przykład kontroler klastra wymaga autoryzacji do tworzenia i konfigurowania maszyn wirtualnych węzła klastra. Węzły klastra muszą mieć możliwość przypisywania lub ponownego przypisywania adresów IP do innych węzłów klastra.

Dwie wbudowane role platformy Azure są używane dla maszyn wirtualnych VFXT Avere:

* Kontroler klastra używa wbudowanej roli [Avere Contributor](../role-based-access-control/built-in-roles.md#avere-contributor).
* Węzły klastra używają wbudowanej roli [Avere Operator](../role-based-access-control/built-in-roles.md#avere-operator).

Jeśli chcesz dostosować role dostępu dla składników Avere vFXT, należy zdefiniować własną rolę, a następnie przypisać ją do maszyn wirtualnych w momencie ich tworzenia. Nie można użyć szablonu wdrożenia w portalu Azure Marketplace. Skonsultuj się z działem obsługi klienta i pomocą techniczną firmy Microsoft, otwierając bilet w portalu Azure, zgodnie z opisem w [temacie Uzyskaj pomoc dotyczącą systemu.](avere-vfxt-open-ticket.md)

## <a name="next-steps"></a>Następne kroki

[Omówienie wdrażania](avere-vfxt-deploy-overview.md) zawiera ogólny obraz kroków potrzebnych do utworzenia systemu Avere vFXT dla platformy Azure i przygotowania go do obsługi danych.
