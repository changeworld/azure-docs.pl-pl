---
title: Planowanie systemu avere vFXT na platformie Azure
description: Wyjaśnia planowanie przed wdrożeniem programu avere vFXT dla platformy Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: rohogue
ms.openlocfilehash: 1317e900fd4448ded046ffea481313f8ea9f68e3
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256240"
---
# <a name="plan-your-avere-vfxt-system"></a>Planowanie systemu Avere vFXT

W tym artykule wyjaśniono, jak zaplanować nowy avere vFXT dla klastra platformy Azure, który jest ustalany odpowiednio do potrzeb. 

Przed przejściem do portalu Azure Marketplace lub utworzeniem jakichkolwiek maszyn wirtualnych należy rozważyć sposób, w jaki klaster będzie współdziałać z innymi elementami na platformie Azure. Zaplanuj, gdzie zasoby klastra będą znajdować się w sieci prywatnej i podsieciach, a następnie zdecyduj, gdzie będzie miał miejsce magazyn zaplecza. Upewnij się, że tworzone węzły klastra są wystarczająco duże, aby obsługiwały przepływ pracy. 

Czytaj dalej, aby dowiedzieć się więcej.

## <a name="resource-group-and-network-infrastructure"></a>Grupa zasobów i infrastruktura sieci

Rozważ, gdzie będą znajdować się elementy avere vFXT dla wdrożenia platformy Azure. Na poniższym diagramie przedstawiono możliwe rozmieszczenie składników avere vFXT for Azure:

![Diagram przedstawiający kontroler klastra i maszyny wirtualne klastra w jednej podsieci. Wokół granicy podsieci jest granicą sieci wirtualnej. Wewnątrz sieci wirtualnej to sześciokąt reprezentujący punkt końcowy usługi magazynu; jest ona połączona z kreskowaną strzałką do magazynu obiektów blob poza siecią wirtualną.](media/avere-vfxt-components-option.png)

Postępuj zgodnie z poniższymi wskazówkami podczas planowania infrastruktury sieciowej systemu avere vFXT:

* Wszystkie elementy powinny być zarządzane przy użyciu nowej subskrypcji utworzonej dla wdrożenia avere vFXT. Korzyści to: 
  * Prostsze śledzenie kosztów — umożliwia wyświetlanie i inspekcję wszystkich kosztów zasobów, infrastruktury i cykli obliczeniowych w ramach jednej subskrypcji.
  * Łatwiejsze czyszczenie — po zakończeniu pracy z projektem można usunąć całą subskrypcję.
  * Wygodne partycjonowanie przydziałów zasobów — ochrona innych krytycznych obciążeń z możliwych funkcji ograniczania zasobów przez izolowanie klientów i klastrów usługi avere vFXT w ramach jednej subskrypcji. Pozwala to uniknąć konfliktu podczas tworzenia dużej liczby klientów dla przepływu pracy obliczeniowej o wysokiej wydajności.

* Znajdź systemy obliczeniowe klienta blisko klastra vFXT. Magazyn zaplecza może być bardziej zdalny.  

* Klaster vFXT i maszyna wirtualna kontrolera klastra powinny znajdować się w tej samej sieci wirtualnej (VNET), w tej samej grupie zasobów i korzystać z tego samego konta magazynu. Szablon zautomatyzowanego tworzenia klastra obsługuje tę sytuację w większości sytuacji.

* Klaster musi znajdować się w własnej podsieci, aby uniknąć konfliktów adresów IP z klientami lub zasobami obliczeniowymi. 

* Szablon tworzenia klastra może utworzyć większość zasobów infrastruktury wymaganych dla klastra, w tym grup zasobów, sieci wirtualnych, podsieci i kont magazynu. Jeśli chcesz użyć już istniejących zasobów, upewnij się, że spełniają one wymagania zawarte w tej tabeli. 

  | Zasób | Użyć istniejącej? | Wymagania |
  |----------|-----------|----------|
  | Grupa zasobów | Tak, jeśli wartość jest pusta | Musi być pusty| 
  | Konto magazynu | Tak w przypadku łączenia istniejącego kontenera obiektów BLOB po utworzeniu klastra <br/>  Nie w przypadku tworzenia nowego kontenera obiektów BLOB podczas tworzenia klastra | Istniejący kontener obiektów BLOB musi być pusty <br/> &nbsp; |
  | Sieć wirtualna | Tak | Musi zawierać punkt końcowy usługi magazynu, jeśli tworzysz nowy kontener obiektów blob platformy Azure | 
  | Podsieć | Tak |   |

## <a name="ip-address-requirements"></a>Wymagania dotyczące adresów IP 

Upewnij się, że podsieć klastra ma wystarczająco duży zakres adresów IP, aby obsługiwał klaster. 

Klaster avere vFXT używa następujących adresów IP:

* Jeden adres IP zarządzania klastrem. Ten adres może przechodzić z węzła do węzła w klastrze, ale jest zawsze dostępny, aby można było połączyć się z narzędziem konfiguracji panelu sterowania avere.
* Dla każdego węzła klastra:
  * Co najmniej jeden adres IP skierowany na klienta. (Wszystkie adresy związane z klientem są zarządzane przez *vserver*klastra, co może przenosić je między węzłami w razie konieczności).
  * Jeden adres IP do komunikacji z klastrem
  * Jeden adres IP wystąpienia (przypisany do maszyny wirtualnej)

W przypadku korzystania z usługi Azure Blob Storage mogą być również wymagane adresy IP z sieci wirtualnej klastra:  

* Konto magazynu obiektów blob platformy Azure wymaga co najmniej pięciu adresów IP. Należy pamiętać o tym wymaganiu, jeśli zlokalizujesz magazyn obiektów BLOB w tej samej sieci wirtualnej co klaster.
* Jeśli używasz magazynu obiektów blob platformy Azure, który znajduje się poza siecią wirtualną klastra, należy utworzyć punkt końcowy usługi magazynu w sieci wirtualnej. Ten punkt końcowy nie używa adresu IP.

Istnieje możliwość lokalizowania zasobów sieciowych i magazynu obiektów BLOB (jeśli są używane) w różnych grupach zasobów z klastra.

## <a name="vfxt-node-size"></a>rozmiar węzła vFXT

Maszyny wirtualne służące jako węzły klastra określają przepływność żądań i pojemność pamięci podręcznej. <!-- The instance type offered has been chosen for its memory, processor, and local storage characteristics. You can choose from two instance types, with different memory, processor, and local storage characteristics. -->

Każdy węzeł vFXT będzie identyczny. Oznacza to, że jeśli utworzysz klaster z trzema węzłami, będziesz mieć trzy maszyny wirtualne o tym samym typie i rozmiarze. 

| Typ wystąpienia | Procesorów wirtualnych vCPU | Pamięć  | Lokalny magazyn SSD  | Maksymalna liczba dysków danych | Przepływność dysku w pamięci podręcznej | Karta sieciowa (licznik) |
| --- | --- | --- | --- | --- | --- | --- |
| Standardowa_E32s_v3 | 32  | 256 GiB | 512 GiB  | 32 | LICZBA OPERACJI WE/WY 51 200 <br/> 768 MB/s | 16 000 MB/s (8)  |

Pamięć podręczna dysku na węzeł można skonfigurować i próg od 1000 GB do 8000 GB. Zalecany rozmiar pamięci podręcznej dla węzłów Standard_E32s_v3 to 4 TB na węzeł.

Aby uzyskać dodatkowe informacje na temat tych maszyn wirtualnych, Przeczytaj dokumentację Microsoft Azure:

* [Rozmiary maszyn wirtualnych zoptymalizowane pod kątem pamięci](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)

## <a name="account-quota"></a>Limit przydziału konta

Upewnij się, że Twoja subskrypcja ma pojemność do uruchomienia klastra avere vFXT, a także wszystkich używanych systemów obliczeniowych lub klienckich. Aby uzyskać szczegółowe informacje, zapoznaj [się z limitem przydziału dla klastra vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) .

## <a name="back-end-data-storage"></a>Magazyn danych zaplecza

Gdzie klaster avere vFXT ma przechowywać dane, gdy nie znajduje się w pamięci podręcznej? Zdecyduj, czy zestaw roboczy będzie przechowywany w długim czasie w nowym kontenerze obiektów blob, czy w istniejącym systemie magazynu w chmurze lub sprzętowym. 

Jeśli chcesz użyć magazynu obiektów blob platformy Azure dla zaplecza, Utwórz nowy kontener w ramach tworzenia klastra vFXT. Ta opcja tworzy i konfiguruje nowy kontener tak, aby był gotowy do użycia zaraz po gotowości klastra. 

Aby uzyskać szczegółowe informacje, zapoznaj [się z tematem tworzenie avere vFXT dla platformy Azure](avere-vfxt-deploy.md#create-the-avere-vfxt-for-azure) .

> [!NOTE]
> Tylko puste kontenery magazynu obiektów BLOB mogą być używane jako podstawowe pliki dla systemu avere vFXT. VFXT musi być w stanie zarządzać magazynem obiektów bez konieczności zachowywania istniejących danych. 
>
> Aby dowiedzieć się, jak kopiować dane do nowego kontenera klastra przy użyciu komputerów klienckich i pamięci podręcznej vFXT avere, przeczytaj artykuł dotyczący [przeniesienia danych do klastra vFXT](avere-vfxt-data-ingest.md) .

Jeśli chcesz użyć istniejącego lokalnego systemu magazynu, musisz dodać go do klastra vFXT po jego utworzeniu. Przeczytaj temat [Konfigurowanie magazynu](avere-vfxt-add-storage.md) , aby uzyskać szczegółowe instrukcje dotyczące sposobu dodawania istniejącego systemu magazynu do klastra avere vFXT.

## <a name="cluster-access"></a>Dostęp do klastra 

Avere vFXT dla klastra platformy Azure znajduje się w podsieci prywatnej, a klaster nie ma publicznego adresu IP. Należy mieć pewną metodę uzyskiwania dostępu do podsieci prywatnej w celu administrowania klastrami i połączeń klientów. 

Opcje dostępu obejmują:

* Przeskocz hosta — Przypisz publiczny adres IP do osobnej maszyny wirtualnej w sieci prywatnej i użyj jej do utworzenia tunelu SSL do węzłów klastra. 

  > [!TIP]
  > Jeśli ustawisz publiczny adres IP na kontrolerze klastra, możesz go użyć jako hosta skoku. Aby uzyskać więcej informacji, Odczytaj [kontroler klastra jako hosta skoku](#cluster-controller-as-jump-host) .

* Wirtualna sieć prywatna (VPN) — Konfigurowanie sieci VPN typu punkt-lokacja lub lokacja-lokacja w sieci prywatnej.

* Azure ExpressRoute — skonfiguruj połączenie prywatne za pomocą partnera ExpressRoute. 

Aby uzyskać szczegółowe informacje na temat tych opcji, Przeczytaj dokumentację dotyczącą [platformy Azure Virtual Network](../virtual-network/virtual-networks-overview.md#communicate-with-the-internet).

### <a name="cluster-controller-as-jump-host"></a>Kontroler klastra jako hosta skoku

W przypadku ustawienia publicznego adresu IP na kontrolerze klastra można go użyć jako hosta skoku, aby skontaktować się z klastrem usługi avere vFXT spoza podsieci prywatnej. Niemniej jednak, ponieważ kontroler ma uprawnienia dostępu do modyfikowania węzłów klastra, powoduje to utworzenie małego zagrożenia bezpieczeństwa.  

W celu zwiększenia bezpieczeństwa kontrolera z publicznym adresem IP skrypt wdrażania automatycznie tworzy sieciową grupę zabezpieczeń, która ogranicza dostęp przychodzący tylko do portu 22. Aby zapewnić lepszą ochronę systemu, można zablokować dostęp do zakresu adresów IP źródła, czyli zezwalać na połączenia tylko z maszyn, które mają być używane do dostępu do klastra.

Podczas tworzenia klastra można określić, czy ma zostać utworzony publiczny adres IP na kontrolerze klastra. 

* W przypadku utworzenia nowej sieci wirtualnej lub nowej podsieci do kontrolera klastra zostanie przypisany publiczny adres IP.
* W przypadku wybrania istniejącej sieci wirtualnej i podsieci kontroler klastra będzie miał tylko prywatne adresy IP. 

## <a name="vm-access-roles"></a>Role dostępu do maszyny wirtualnej 

Platforma Azure używa [kontroli dostępu opartej na rolach](../role-based-access-control/index.yml) (RBAC) do autoryzacji maszyn wirtualnych klastra do wykonywania określonych zadań. Na przykład kontroler klastra wymaga autoryzacji do tworzenia i konfigurowania maszyn wirtualnych węzłów klastra. Węzły klastra muszą mieć możliwość przypisywania lub ponownego przypisywania adresów IP do innych węzłów klastra.

W przypadku maszyn wirtualnych avere vFXT są używane dwie wbudowane role platformy Azure: 

* Kontroler klastra używa wbudowanego [współautora avere](../role-based-access-control/built-in-roles.md#avere-contributor)roli. 
* Węzły klastra używają wbudowanego [operatora avere](../role-based-access-control/built-in-roles.md#avere-operator) roli

Jeśli musisz dostosować role dostępu dla składników avere vFXT, musisz zdefiniować własną rolę, a następnie przypisać ją do maszyn wirtualnych w momencie ich tworzenia. Nie można użyć szablonu wdrożenia w portalu Azure Marketplace. Skontaktuj się z działem obsługi klienta i pomocy technicznej firmy Microsoft, otwierając bilet w Azure Portal zgodnie z opisem w artykule [Uzyskiwanie pomocy dotyczącej systemu](avere-vfxt-open-ticket.md). 

## <a name="next-step-understand-the-deployment-process"></a>Następny krok: zrozumienie procesu wdrażania

[Przegląd wdrożenia](avere-vfxt-deploy-overview.md) zawiera Big Picture wszystkie kroki wymagane do utworzenia avere vFXT dla systemu Azure i przygotowania do obsługi danych.  