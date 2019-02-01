---
title: Planowanie Avere system vFXT — platformy Azure
description: Wyjaśnia, robić przed wdrożeniem Avere vFXT dla platformy Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: v-erkell
ms.openlocfilehash: c0304e290d18e6569e3fcc2efbab8af15727b80c
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55508165"
---
# <a name="plan-your-avere-vfxt-system"></a>Planowanie systemu Avere vFXT

W tym artykule opisano sposób planowania nowego vFXT Avere dla klastra platformy Azure upewnić się, klastra, tworzonych jest umieszczony i rozmiary odpowiednie do potrzeb. 

Przed przystąpieniem do przechodzenia do portalu Azure Marketplace lub tworzenia maszyn wirtualnych, należy wziąć pod uwagę interakcje klastra z innymi elementami na platformie Azure. Należy zaplanować, gdzie będzie znajdować się w prywatnej sieci i podsieci zasobów klastra i zdecyduj, gdzie będzie można magazynu zaplecza. Upewnij się, że węzły klastra tworzone są wystarczająco dobra do obsługi przepływu pracy. 

Czytaj dalej, aby dowiedzieć się więcej.

## <a name="resource-group-and-network-infrastructure"></a>Zasób grupy i infrastruktury sieciowej

Należy wziąć pod uwagę, gdzie będzie można elementy usługi vFXT Avere wdrożenia platformy Azure. Poniższy diagram przedstawia możliwe rozmieszczenia dla vFXT Avere dla składników platformy Azure:

![Diagram przedstawiający klaster kontrolera i klaster maszyn wirtualnych w ramach jednej podsieci. Wokół podsieci granica jest granica sieci wirtualnej. Wewnątrz sieci wirtualnej jest sześciokąt, reprezentujący punkt końcowy usługi magazynowania; jest połączona z kreskowanym strzałką do usługi Blob storage spoza sieci wirtualnej.](media/avere-vfxt-components-option.png)

Podczas planowania systemu vFXT Avere infrastruktury sieci, należy przestrzegać następujących wytycznych:

* Wszystkie elementy powinny być zarządzane za pomocą z nowej subskrypcji utworzonej dla wdrożenia vFXT Avere. Korzyści to: 
  * Prostsze śledzenie kosztów — wyświetlanie i inspekcji wszelką cenę z zasobów, infrastruktury i obliczeń cykli w ramach jednej subskrypcji.
  * Łatwiejsze Oczyszczanie — możesz usunąć całej subskrypcji po zakończeniu pracy z projektem.
  * Wygodne partycjonowanie zasobów przydziały — ochrona innych obciążeń o znaczeniu krytycznym z zasobów ograniczania przepustowości w przypadku wyświetlania dużej liczby używanych dla przepływu pracy o wysokiej wydajności obliczeniowej przez izolowanie klientów vFXT Avere klientów i klastra w systemie pojedynczej subskrypcji.

* Znajdź systemami obliczeń klienta blisko vFXT klastra. Magazyn zaplecza może być bardziej zdalnego.  

* Dla uproszczenia klastra vFXT i lokalizacji maszyny Wirtualnej kontrolera klastra w tej samej sieci wirtualnej (vnet) i w tej samej grupie zasobów. Ponadto powinny używać tego samego konta magazynu. (Kontroler klastra tworzy klaster i może również służyć do zarządzania klastrem wiersza polecenia).  

* Klaster musi znajdować się we własnej podsieci, aby uniknąć konfliktów adresów IP z klientami lub zasoby obliczeniowe. 

## <a name="ip-address-requirements"></a>Wymagania dotyczące adresów IP 

Upewnij się, że podsieć klastra ma wystarczająco duży zakres adresów IP do obsługi klastra. 

Klaster vFXT Avere używa następujących adresów IP:

* Adres IP zarządzania dla jednego klastra. Ten adres można przenosić między węzłami w klastrze, ale zawsze jest dostępny, dzięki czemu można połączyć się za pomocą narzędzia konfiguracji Avere Panelu sterowania.
* Dla każdego węzła klastra:
  * Co najmniej jeden adres IP ukierunkowane na klienta. (Wszystkie adresy ukierunkowane na klienta są zarządzane przez klaster *vserver*, która je przenosić między węzłami zgodnie z potrzebami.)
  * Jeden adres IP do komunikacji klastra
  * Adres IP jednego wystąpienia (przypisana do maszyny Wirtualnej)

Jeśli używasz usługi Azure Blob storage, również może następować po adresów IP z sieci wirtualnej klastra:  

* Konto magazynu obiektów Blob platformy Azure wymaga co najmniej pięciu adresów IP. Jeśli możesz znaleźć w tej samej podsieci co klaster magazynu obiektów Blob, należy pamiętać o to wymaganie.
* Jeśli używasz magazynu obiektów Blob platformy Azure, który jest poza siecią wirtualną dla klastra, należy utworzyć punkt końcowy usługi magazynu w sieci wirtualnej. Ten punkt końcowy nie używa adresu IP.

Masz możliwość lokalizowanie zasobów sieciowych i magazynu obiektów Blob (jeśli jest używany) w różnych grupach zasobów z klastra.

## <a name="vfxt-node-sizes"></a>rozmiary węzłów vFXT 

Maszyny wirtualne, które będzie służyć jako węzły klastra określają pojemność żądania przepływności i przestrzeni dyskowej pamięci podręcznej. Możesz wybrać spośród dwóch typów wystąpienia, za pomocą różnych pamięci, procesora i charakterystykę magazynu lokalnego. 

Każdy węzeł vFXT są identyczne. Oznacza to jeśli tworzysz klaster z trzema węzłami masz trzech maszyn wirtualnych z tego samego typu i rozmiaru. 

| Typ wystąpienia | Procesory wirtualne vCPU | Memory (Pamięć)  | Lokalny magazyn SSD  | Maks. liczba dysków danych | Przepływność dysku bez buforowania | Karta sieciowa (licznik) |
| --- | --- | --- | --- | --- | --- | --- |
| Standardowa_D16s_v3 | 16  | 64 GiB  | 128 GiB  | 32 | 25,600 IOPS <br/> 384 MB/s | 8000 MB/s (8) |
| Standardowa_E32s_v3 | 32  | 256 GiB | 512 GiB  | 32 | 51,200 IOPS <br/> 768 MB/s | 16 000 MB/s (8)  |

Można skonfigurować pamięć podręczną dysku w każdym węźle, można rage od 1000 GB do 8000 GB. 1 TB na węzeł jest zalecany rozmiar pamięci dla węzłów Standard_D16s_v3 i 4 TB na węzeł jest zalecane w przypadku Standard_E32s_v3 węzłów.

Aby uzyskać dodatkowe informacje na temat tych maszyn wirtualnych przeczytaj następujące dokumenty Microsoft Azure:

* [Rozmiary maszyn wirtualnych ogólnego przeznaczenia](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general)
* [Rozmiary maszyn wirtualnych zoptymalizowanych pod kątem pamięci](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)

## <a name="account-quota"></a>Limitu przydziału konta

Upewnij się, że Twoja subskrypcja ma możliwość obsługi klastra vFXT Avere, a także systemy przetwarzania danych lub klienta używany. Odczyt [limitu przydziału dla klastra vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) Aby uzyskać szczegółowe informacje.

## <a name="back-end-data-storage"></a>Magazyn danych zaplecza

Gdzie należy do klastra vFXT Avere przechowywania danych podczas nie znajduje się w pamięci podręcznej? Zdecyduj, czy zestaw roboczy będzie przechowywane długoterminowych na nowy kontener obiektów Blob lub w istniejącej chmurze lub system magazynowania sprzętu. 

Jeśli chcesz użyć usługi Azure Blob storage dla wewnętrznej, należy utworzyć nowy kontener w ramach tworzenia klastra vFXT. Ta opcja tworzy i konfiguruje nowy kontener, aby była gotowa do użycia jak klaster będzie gotowy. 

Odczyt [tworzenie Avere vFXT dla platformy Azure](avere-vfxt-deploy.md#create-the-avere-vfxt-for-azure) Aby uzyskać szczegółowe informacje.

> [!NOTE]
> Tylko puste kontenery magazynu obiektów Blob może służyć jako filtrach core dla systemu vFXT Avere. VFXT musi mieć możliwość zarządzania jego magazynu obiektów bez konieczności zachować istniejące dane. 
>
> Odczyt [przenoszenia danych do klastra vFXT](avere-vfxt-data-ingest.md) Aby dowiedzieć się, jak skopiować dane do nowego kontenera klastra efektywnie przy użyciu komputerów klienckich i pamięci podręcznej vFXT Avere.

Jeśli chcesz użyć istniejącego systemu magazynu lokalnego, musisz Dodaj go do klastra vFXT, po jego utworzeniu. Odczyt [skonfigurować magazyn](avere-vfxt-add-storage.md) szczegółowe instrukcje dotyczące sposobu dodawania istniejącego systemu magazynu do klastra vFXT Avere.

## <a name="cluster-access"></a>Dostępu do klastra 

VFXT Avere klastra platformy Azure znajduje się w podsieci prywatnej, a klaster nie ma publicznego adresu IP. Konieczne jest posiadanie niektóre metody uzyskiwania dostępu do podsieci prywatnej dla połączeń klienta i administrowania klastrami. 

Dostęp do opcji należą:

* Przejdź do hosta — Przypisz publiczny adres IP do oddzielnych maszyny Wirtualnej w ramach sieci prywatnej, a go użyć do utworzenia tunelu protokołu SSL w węzłach klastra. 

  > [!TIP]
  > Jeśli publiczny adres IP jest ustawiony na kontroler klastra, możesz użyć go jako hosta przeskoku. Odczyt [kontrolera klastra, jak przejść hosta](#cluster-controller-as-jump-host) Aby uzyskać więcej informacji.

* Wirtualnej sieci prywatnej (VPN) — Konfigurowanie sieci VPN punkt lokacja i lokacja lokacja z siecią prywatną.

* Usługa ExpressRoute systemu Azure — Konfigurowanie prywatnego połączenia za pośrednictwem partnera usługi ExpressRoute. 

Aby uzyskać szczegółowe informacje na temat tych opcji, przeczytaj [dokumentacji usługi Azure Virtual Network o komunikację z Internetem](../virtual-network/virtual-networks-overview.md#communicate-with-the-internet).

### <a name="cluster-controller-as-jump-host"></a>Kontroler klastra, jak przejść hosta

Jeśli publiczny adres IP jest ustawiony na kontrolerze klastra, można użyć go jako hosta szybkie, nawiązać połączenia z klastrem vFXT Avere z poza podsieci prywatnej. Jednak ponieważ kontroler ma uprawnienia dostępu do modyfikowania węzłów klastra, powoduje to utworzenie małej zagrożenie bezpieczeństwa.  

Ze względów bezpieczeństwa z publicznym adresem IP należy użyć sieciowej grupy zabezpieczeń w celu umożliwienia dostępu przychodzącego wyłącznie za pośrednictwem portu 22. Opcjonalnie można dodatkowo zabezpieczyć systemu przez blokowanie szczegółów dostępu do sieci zakres adresów IP, źródłowy — oznacza to, Zezwalaj na połączenia tylko z komputerów, które mają być używane dla dostępu do klastra.

Podczas tworzenia klastra, można wybrać, czy należy utworzyć publiczny adres IP na kontrolerze klastra. 

* Jeśli tworzysz nową sieć wirtualną lub nowej podsieci kontrolera klastra zostanie przypisany publiczny adres IP.
* Jeśli wybierzesz istniejącej sieci wirtualnej i podsieci, kontroler klastra będzie miał tylko prywatnych adresów IP. 

## <a name="next-step-understand-the-deployment-process"></a>Następny krok: Omówienie procesu wdrażania

[Omówienie wdrażania](avere-vfxt-deploy-overview.md) zapewnia szerszej perspektywie wszystkie kroki potrzebne do utworzenia vFXT Avere dla systemu Azure i przygotować go do obsługi danych.  