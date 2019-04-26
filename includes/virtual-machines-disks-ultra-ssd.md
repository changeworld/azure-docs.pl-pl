---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/24/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4b5d2de2e9ccd44517e083a435e127bd5678f002
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60564344"
---
# <a name="ultra-disks-preview-managed-disks-for-azure-virtual-machine-workloads"></a>Managed disks dla obciążeń maszyn wirtualnych platformy Azure w najwyższej dysków (wersja zapoznawcza)

Największa dyskami platformy Azure (wersja zapoznawcza) zapewnia wysoką przepływność, wysokiej operacje We/Wy i magazynu dyskowego niskie opóźnienia maszyn wirtualnych IaaS platformy Azure. Ta nowa oferta zapewnia początku wydajności wiersza w poziomach dostępności jako ofert istniejących dysków. Dodatkowe korzyści ultra dyski obejmują możliwość dynamicznie zmieniać wydajność dysku wraz z obciążeń, bez konieczności ponownego uruchamiania maszyn wirtualnych. Największa dyski są odpowiednie dla obciążeń intensywnie korzystających z danych, takich jak SAP HANA, najwyższej warstwy bazy danych i transakcji duże obciążenia.

## <a name="ultra-disk-features"></a>Funkcje Ultra dysku

**Usługa Managed disks**: Największa dyski są dostępne tylko jako dysków zarządzanych. Nie można wdrożyć Ultra dysków jako dysk niezarządzany lub stronicowych obiektów blob. Podczas tworzenia dysku zarządzanego, należy określić, jednostki sku dysku, wpisz jako UltraSSD_LRS i rozmiaru dysku, operacje We/Wy, i użytą przepływność i Azure tworzy i zarządza dysku.  

**Maszyny wirtualne**: Największa dyski zostały zaprojektowane do pracy, przy użyciu wszystkich Premium SSD, włączone jednostek SKU maszyn wirtualnych platformy Azure. ponieważ jest on aktualnie w wersji zapoznawczej, maszyny wirtualne mają rozmiar jako v3 ES/DS.

**Konfiguracja dynamicznej wydajności**: Największa dyski umożliwiają dynamicznie zmieniać wydajności (operacje We/Wy i przepływność) dysku oraz potrzeby związane z obciążeniem bez konieczności ponownego uruchamiania maszyn wirtualnych.

## <a name="scalability-and-performance-targets"></a>Cele dotyczące skalowalności i wydajności

Podczas aprowizowania ultra dyski należy niezależnie skonfigurować pojemność i wydajność dysku. Największa dysków pochodzą kilka stałych rozmiarach od 4 GiB maksymalnie 64 TiB, są wyposażone w modelu konfiguracji elastyczna wydajność umożliwiająca można niezależnie konfigurować operacje We/Wy i przepływność. Największa dysków może być wykorzystywana tylko jako dyski z danymi. Zalecamy używanie dysków Premium SSD jako dyski systemu operacyjnego.

Niektóre kluczowe funkcje ultra dyski są:

- Pojemność dysku: Największa dysków oferuje szeroką gamę różnych dysków o rozmiarach od 4 GiB maksymalnie 64 TiB.
- Disk IOPS: Dyski Ultra obsługują operacje We/Wy limitów 300 operacji We/Wy/GiB, maksymalnie 160 KB operacje We/Wy na dysk. Uzyskanie operacje We/Wy, które należy aprowizować, upewnij się, że wybrany dysk operacje We/Wy mniejsza niż na SEKUNDĘ maszyny Wirtualnej. Minimalna operacje We/Wy dysku jest 100 operacji We/Wy.
- Przepływność dysku: Z dyskami najwyższej przepływności pojedynczego dysku wynoszący 256 KiB/s dla każdego aprowizowane operacje We/Wy, maksymalnie 2000 MB/s na dysk (gdzie MB/s = 10 ^ 6 bajtów na sekundę). Przepływność dysku minimalną jest 1 MiB.

W poniższej tabeli podsumowano różne konfiguracje obsługiwane dla różnych rozmiarów dysków:  

### <a name="ultra-disks-managed-disk-offerings"></a>Największa dyski zarządzane ofert dysku

|Rozmiar dysku (GiB)  |Limity operacji We/Wy  |Limit przepływności (MB/s)  |
|---------|---------|---------|
|4     |1,200         |300         |
|8     |2,400         |600         |
|16     |4,800         |1,200         |
|32     |9,600         |2000         |
|64     |19,200         |2000         |
|128     |38,400         |2000         |
|256     |76,800         |2000         |
|512     |80,000         |2000         |
|1024 – 65 536 (rozmiary, w tym zakresie, zwiększając w przyrostach co 1 TiB)     |160,000         |2000         |

## <a name="pricing-and-billing"></a>Cennik i rozliczenia

Korzystając z najwyższej dysków, zostaną zastosowane następujące zagadnienia dotyczące rozliczeń:

- Rozmiar dysku zarządzanego
- Dysk zarządzany Aprowizowane operacje We/Wy
- Aprowizowana przepływność dysku zarządzanego
- Opłata za rezerwacji maszyny Wirtualnej Ultra dysku

### <a name="managed-disk-size"></a>Rozmiar dysku zarządzanego

Dyski zarządzane są naliczane na temat rozmiarów maszyn wirtualnych, które można wybrać podczas aprowizowania nowych maszyn wirtualnych platformy Azure. Platforma Azure mapuje aprowizowany rozmiar (zaokrąglony w górę) na najbliższy oferowany rozmiar dysku. Aby uzyskać szczegóły dotyczące rozmiarów dysków oferowane zobacz tabelę w powyższej sekcji cele skalowalności i wydajności. Każdy dysk mapowania rozmiar dysku obsługiwane i będą naliczane odpowiednio w systemie godzinowym. Na przykład jeśli zainicjowano obsługę administracyjną ultra dysk o pojemności 200 GiB, a następnie usunięte 20 godzin, będzie zmapowana do 256 GiB ofertę rozmiar dysku i opłata jest naliczana dla 256 GiB przez 20 godzin. Ta karta została oparta na zużycie godzin obliczeniowych niezależnie od ilości danych, w rzeczywistości zapisane na dysku.

### <a name="managed-disk-provisioned-iops"></a>Dysk zarządzany aprowizowane operacje We/Wy

Operacje We/Wy są: liczba żądań, które wysyła aplikacji do dysków na sekundę. Operacji wejścia/wyjścia można sekwencyjnego odczytu lub zapisu lub losowe odczytu lub zapisu. Na podstawie rozmiaru dysku lub liczba dysków dołączonych do maszyny Wirtualnej, to średnia liczba operacji We/Wy są naliczane godzinowo. Aby uzyskać szczegółowe informacje o dysku, operacje We/Wy udostępniane Zobacz tabelę w powyższej sekcji cele skalowalności i wydajności.

### <a name="managed-disk-provisioned-throughput"></a>Aprowizowana przepływność dysku zarządzanego

Przepływność jest ilość danych, które aplikacja wysyła do dysków w określonym przedziale czasu, mierzony w bajtach na sekundę. Jeśli aplikacja działa dużych operacji wejścia/wyjścia, wymaga wysokiej przepływności.  

Brak relacji między przepływność i operacje We/Wy, jak pokazano na następującą formułę:  Operacje We/Wy x rozmiar operacji We/Wy = przepływności

W związku z tym jest ważne jest określenie optymalnej wartości przepustowości i operacji We/Wy, wymagane przez aplikację. Przy próbie optymalizacji, jeden, druga pobiera dotyczy także. Firma Microsoft zaleca, począwszy od przepływności odpowiadający 16 rozmiar KiB operacji We/Wy i dopasowywanie, jeśli potrzebny jest więcej przepływności.

Szczegółowe informacje dotyczące przepływności dysku obsługiwanych na dyskach ultra zobacz tabelę w powyższej sekcji cele skalowalności i wydajności. Tak jak rozmiar dysku i operacje We/Wy aprowizowana przepływność jest rozliczana godzinowo MB/s aprowizowane.

### <a name="ultra-disk-vm-reservation-fee"></a>Opłata za rezerwacji maszyny Wirtualnej Ultra dysku

Wprowadzamy możliwość na maszynie Wirtualnej, który wskazuje, że maszyna wirtualna jest największa dysku zgodne. Największa dysku zgodnej maszyny Wirtualnej przydziela dedykowaną pojemność przepustowości między wystąpieniem maszyny Wirtualnej obliczeniowe i jednostki skali magazynu bloku, aby zoptymalizować wydajność i zmniejszyć opóźnienie. Dodanie tej funkcji na wynikach maszyn wirtualnych w Twoich rękach rezerwacji, która nakłada się tylko włączenie możliwości ultra dysku na maszynie Wirtualnej bez podłączania ultra dysku do niego. Gdy ultra dysk jest podłączony do najwyższej zgodnej maszyny Wirtualnej, ta opłata nie będzie stosowane. Ta opłata jest na procesor wirtualny vCPU, którego obsługę zainicjowano na maszynie Wirtualnej.

Zapoznaj się [Azure Disks stronę z cennikiem](https://azure.microsoft.com/pricing/details/managed-disks/) nowe ultra dysków cena szczegóły dostępne w ograniczonej wersji zapoznawczej.

### <a name="ultra-disk-preview-scope-and-limitations"></a>Zakres wersji zapoznawczej Ultra dysku i ograniczenia

W wersji zapoznawczej ultra dyski:

- Będzie początkowo obsługiwana w regionach wschodnie stany USA 2, w jednej strefie dostępności  
- Należy używać tylko z użyciem strefy dostępności (zestawy dostępności i jednego wdrożenia maszyn wirtualnych poza strefy będą ma możliwość dołączenia dysku ultra)
- Są obsługiwane tylko na maszynach wirtualnych v3 ES/DS
- Są dostępne tylko jako dyski z danymi i tylko rozmiar sektora fizycznego 4k pomocy technicznej  
- Może być utworzony tylko jako pustych dysków  
- Obecnie można wdrożyć tylko przy użyciu szablonów usługi Resource Manager, interfejsu wiersza polecenia i zestawu SDK języka Python.
- Będzie (jeszcze) pomocy technicznej dysku zestawy dostępności migawki, obrazy maszyn wirtualnych, zestawów skalowania maszyn wirtualnych i usługi Azure Disk Encryption.
- Nie (jeszcze) obsługuje integrację z usługą Azure Backup lub usługi Azure Site Recovery.
- Podobnie jak w przypadku [większość wersji zapoznawczych](https://azure.microsoft.com/support/legal/preview-supplemental-terms/), ta funkcja nie należy używać w przypadku obciążeń produkcyjnych do momentu ogólna dostępność (GA).
