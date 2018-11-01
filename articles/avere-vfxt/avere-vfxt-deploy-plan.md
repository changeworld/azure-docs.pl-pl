---
title: Planowanie Avere system vFXT — platformy Azure
description: Wyjaśnia, robić przed wdrożeniem Avere vFXT dla platformy Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: f0e5523565dc561ed457dbc340835ad1889cb876
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/31/2018
ms.locfileid: "50634163"
---
# <a name="plan-your-avere-vfxt-system"></a>Planowanie systemu vFXT Avere

W tym artykule opisano sposób planowania nowego vFXT Avere dla klastra platformy Azure upewnić się, klastra, tworzonych jest umieszczony i rozmiary odpowiednie do potrzeb. 

Przed przystąpieniem do przechodzenia do portalu Azure Marketplace lub tworzenia maszyn wirtualnych, należy wziąć pod uwagę interakcje klastra z innymi elementami na platformie Azure. Należy zaplanować, gdzie będzie znajdować się w prywatnej sieci i podsieci zasobów klastra i zdecyduj, gdzie będzie można magazynu zaplecza. Upewnij się, że węzły klastra tworzone są wystarczająco dobra do obsługi przepływu pracy. 

Czytaj dalej, aby dowiedzieć się więcej.

## <a name="resource-group-and-network-infrastructure"></a>Zasób grupy i infrastruktury sieciowej

Należy wziąć pod uwagę, gdzie będzie można elementy usługi vFXT Avere wdrożenia platformy Azure. Poniższy diagram przedstawia możliwe rozmieszczenia dla vFXT Avere dla składników platformy Azure:

![Diagram przedstawiający klaster kontrolera i klaster maszyn wirtualnych w ramach jednej podsieci. Wokół podsieci granica jest granica sieci wirtualnej. Wewnątrz sieci wirtualnej jest sześciokąt, reprezentujący punkt końcowy usługi magazynowania; jest połączona z kreskowanym strzałką do usługi Blob storage spoza sieci wirtualnej.](media/avere-vfxt-components-option.png)

Podczas planowania systemu vFXT Avere infrastruktury sieci, należy przestrzegać następujących wytycznych:

* Wszystkie elementy powinny być zarządzane za pomocą z nowej subskrypcji utworzonej dla wdrożenia vFXT Avere. Ta strategia upraszcza śledzenie kosztów i oczyszczania i pomaga również limity przydziałów zasobów partycji. Ponieważ Avere vFXT jest używana z dużą liczbą klientów, izolowanie klientów i klastrów w ramach jednej subskrypcji chroni inne kluczowe dla działalności obciążenia z możliwości ograniczania podczas obsługi administracyjnej klienta zasobów.

* Znajdź systemami obliczeń klienta blisko vFXT klastra. Magazyn zaplecza może być bardziej zdalnego.  

* Dla uproszczenia klastra vFXT i lokalizacji maszyny Wirtualnej kontrolera klastra w tej samej sieci wirtualnej (vnet) i w tej samej grupie zasobów. Ponadto powinny używać tego samego konta magazynu. 

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
| Standardowa_D16s_v3 | 16  | 64 GiB  | 128 GiB  | 32 | 25,600 OPERACJE WE/WY <br/> 384 MB/s | 8000 MB/s (8) |
| Standardowa_E32s_v3 | 32  | 256 GiB | 512 GiB  | 32 | 51,200 OPERACJE WE/WY <br/> 768 MB/s | 16 000 MB/s (8)  |

Można skonfigurować pamięć podręczną dysku w każdym węźle, można rage od 1000 GB do 8000 GB. 1 TB na węzeł jest zalecany rozmiar pamięci dla węzłów Standard_D16s_v3 i 4 TB na węzeł jest zalecane w przypadku Standard_E32s_v3 węzłów.

Aby uzyskać dodatkowe informacje na temat tych maszyn wirtualnych przeczytaj następujące dokumenty Microsoft Azure:

* [Rozmiary maszyn wirtualnych ogólnego przeznaczenia](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general)
* [Rozmiary maszyn wirtualnych zoptymalizowanych pod kątem pamięci](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)

## <a name="account-quota"></a>Limitu przydziału konta

Upewnij się, że Twoja subskrypcja ma możliwość obsługi klastra vFXT Avere, a także systemy przetwarzania danych lub klienta używany. Odczyt [limitu przydziału dla klastra vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) Aby uzyskać szczegółowe informacje.

## <a name="back-end-data-storage"></a>Magazyn danych zaplecza

Gdy nie jest ona w pamięci podręcznej, będą zestaw roboczy przechowywane w nowym kontenerze obiektów Blob lub w istniejącej chmurze lub sprzęt, system magazynu?

Jeśli chcesz użyć usługi Azure Blob storage dla wewnętrznej, należy utworzyć nowy kontener w ramach tworzenia klastra vFXT. Użyj ``create-cloud-backed-container`` skrypt wdrażania i dostarczyć magazynu uwzględnić nowy kontener obiektów Blob. Ta opcja tworzy i konfiguruje nowy kontener, aby była gotowa do użycia jak klaster będzie gotowy. Odczyt [Utwórz węzły i skonfiguruj klaster](avere-vfxt-deploy.md#create-nodes-and-configure-the-cluster) Aby uzyskać szczegółowe informacje.

> [!NOTE]
> Tylko puste kontenery magazynu obiektów Blob może służyć jako filtrach core dla systemu vFXT Avere. VFXT musi mieć możliwość zarządzania jego magazynu obiektów bez konieczności zachować istniejące dane. 
>
> Odczyt [przenoszenia danych do klastra vFXT](avere-vfxt-data-ingest.md) Aby dowiedzieć się, jak skopiować dane do nowego kontenera klastra efektywnie przy użyciu komputerów klienckich i pamięci podręcznej vFXT Avere.

Jeśli chcesz użyć istniejącego systemu magazynu lokalnego, musisz Dodaj go do klastra vFXT, po jego utworzeniu. ``create-minimal-cluster`` Wdrożenia skrypt umożliwia utworzenie klastra vFXT z Brak magazynu zaplecza. Odczyt [skonfigurować magazyn](avere-vfxt-add-storage.md) szczegółowe instrukcje dotyczące sposobu dodawania istniejącego systemu magazynu do klastra vFXT Avere. 

## <a name="next-step-understand-the-deployment-process"></a>Następny krok: zrozumieć proces wdrażania

[Omówienie wdrażania](avere-vfxt-deploy-overview.md) zapewnia szerszej perspektywie wszystkie kroki potrzebne do utworzenia vFXT Avere dla systemu Azure i przygotować go do obsługi danych.  