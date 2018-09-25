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
ms.openlocfilehash: 41e7f1aacfaf228b271aee0547d5f26a5967dafe
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47011313"
---
# <a name="ultra-ssd-preview-managed-disks-for-azure-virtual-machine-workloads"></a>Największa SSD (wersja zapoznawcza) dysków Managed Disks dla obciążeń maszyn wirtualnych platformy Azure

SSD Ultra platformy Azure (wersja zapoznawcza) zapewnia wysoką przepływność, wysokiej operacje We/Wy i magazynu dyskowego niskie opóźnienia dla maszyn wirtualnych IaaS platformy Azure. Ta nowa oferta zapewnia początku wydajności wiersza w poziomach dostępności jako ofert istniejących dysków. Dodatkowe korzyści Ultra SSD obejmują możliwość dynamicznie zmieniać wydajność dysku wraz z obciążeń, bez konieczności ponownego uruchamiania maszyn wirtualnych. Największa dysków SSD jest odpowiedni dla obciążeń intensywnie korzystających z danych, takich jak SAP HANA, najwyższej warstwy bazy danych i transakcji duże obciążenia.

## <a name="ultra-ssd-features"></a>Funkcje Ultra SSD

**Usługa Managed Disks**: Ultra dyski SSD są dostępne tylko jako dyski Managed Disks. Nie można wdrożyć Ultra dyski SSD jako dysków niezarządzanych i stronicowych obiektów Blob. Podczas tworzenia dysku zarządzanego, należy określić, jednostki sku dysku, wpisz jako UltraSSD_LRS i rozmiaru dysku, operacje We/Wy, i użytą przepływność i Azure tworzy i zarządza dysku.  

**Maszyny wirtualne**: Ultra SSD przeznaczone dla wszystkich maszyn wirtualnych platformy Azure w warstwie Premium SSD włączone jednostek SKU, jednak w czasie w wersji zapoznawczej będzie ograniczona do wystąpień maszyn wirtualnych w wersji 3 ES/DS rozmiarów maszyn wirtualnych.

**Dynamiczna konfiguracja wydajność**: Ultra SSD pozwalają dynamicznie zmieniać wydajności (operacje We/Wy i przepływność) dysku oraz potrzeby związane z obciążeniem bez konieczności ponownego uruchamiania maszyn wirtualnych.

## <a name="scalability-and-performance-targets"></a>Cele dotyczące skalowalności i wydajności

Podczas aprowizowania Ultra dyski SSD, masz możliwość niezależnie skonfigurować pojemność i wydajność dysku. Największa SSD pochodzą kilka stałych rozmiarach od 4 GiB maksymalnie 64 TiB oraz funkcji modelu konfiguracji elastyczna wydajność umożliwiająca można niezależnie konfigurować operacje We/Wy i przepływność. Dyski SSD Ultra tylko może być wykorzystywana jako dyski z danymi. Zalecamy używanie dysków Premium SSD jako dyski systemu operacyjnego.

Niektóre kluczowe funkcje Ultra dyski SSD są:

- Pojemność dysku: Ultra SSD oferuje szeroką gamę różnych dysków o rozmiarach z 4 GiB maksymalnie 64 TiB.
- : Operacje We/Wy Ultra SSD pamięć dyskowa limity operacji We/Wy 300 operacji We/Wy/GiB, maksymalnie 160 KB operacje We/Wy na dysk. Uzyskanie operacje We/Wy, które należy aprowizować, upewnij się, że wybrany dysk operacje We/Wy mniejsza niż na SEKUNDĘ maszyny Wirtualnej. Minimalna operacje We/Wy dysku jest 100 operacji We/Wy.
- Przepływność dysków: Przy użyciu najwyższej dysków SSD, przepływność pojedynczego dysku wynoszący 256 KiB/s dla każdego aprowizowane operacje We/Wy, maksymalnie 2000 MB/s na dysk (gdzie MB/s = 10 ^ 6 bajtów na sekundę). Przepływność dysku minimalną jest 1 MiB.

W poniższej tabeli podsumowano różne konfiguracje obsługiwane dla różnych rozmiarów dysków:  

### <a name="ultra-ssd-managed-disk-offerings"></a>Oferty dysków Ultra SSD Managed Disks

|Rozmiar dysku (GiB)  |Limity operacji We/Wy  |Limit przepływności (MB/s)  |
|---------|---------|---------|
|4     |1,200         |300         |
|8     |2,400         |600         |
|16     |4,800         |1,200         |
|32     |9600         |2.000         |
|64     |19 200         |2.000         |
|128     |szybkości 38 400         |2.000         |
|256     |76,800         |2.000         |
|512     |80,000         |2.000         |
|1024 – 65 536 (rozmiary, w tym zakresie, zwiększając w przyrostach co 1 TiB)     |160,000         |2.000         |

## <a name="pricing-and-billing"></a>Cennik i rozliczenia

Korzystając z najwyższej dysków SSD, zastosuj następujące zagadnienia dotyczące rozliczeń:

- Rozmiar dysku zarządzanego
- Dysk zarządzany Aprowizowane operacje We/Wy
- Aprowizowana przepływność dysku zarządzanego
- Największa opłata rezerwacji maszyny Wirtualnej z dysków SSD

### <a name="managed-disk-size"></a>Rozmiar dysku zarządzanego

Dyski zarządzane są naliczane na rozmiarze aprowizowanego. Usługi Azure maps zaprowizowany rozmiar (z zaokrągleniem) do najbliższej oferty rozmiaru dysku. Aby uzyskać szczegóły dotyczące rozmiarów dysków oferowane zobacz tabelę w powyższej sekcji cele skalowalności i wydajności. Każdy dysk jest mapowany na rozmiar dysku obsługiwane i w związku z tym rozliczane godzinowo. Na przykład jeśli zainicjowano obsługę administracyjną 200 GiB Ultra dysk SSD, a następnie usunięte 20 godzin, będzie zmapowana do 256 GiB ofertę rozmiar dysku i opłata jest naliczana dla 256 GiB przez 20 godzin. Jest to niezależnie od ilości rzeczywiste dane zapisane na dysku.

### <a name="managed-disk-provisioned-iops"></a>Dysk zarządzany Aprowizowane operacje We/Wy

Operacje We/Wy są: liczba żądań, które aplikacja wysyła do dysków w jednej sekundy. Operacji We/Wy może być kolejno lub losowo, odczytuje lub zapisuje. Np. rozmiar dysku aprowizowane operacje We/Wy są naliczane godzinowo. Aby uzyskać szczegółowe informacje o dysku, operacje We/Wy udostępniane Zobacz tabelę w powyższej sekcji cele skalowalności i wydajności.

### <a name="managed-disk-provisioned-throughput"></a>Aprowizowana przepływność dysku zarządzanego

Przepływność jest ilość danych, które aplikacja wysyła do dysków w określonym przedziale czasu, mierzony w bajtach na sekundę. Jeśli aplikacja działa dużych operacji wejścia/wyjścia, wymaga wysokiej przepływności.  

Istnieje relacja między przepływność i operacje We/Wy, jak pokazano na następującą formułę: operacje We/Wy x rozmiar operacji We/Wy = przepływności

W związku z tym jest ważne jest określenie optymalnej wartości przepustowości i operacji We/Wy, wymagane przez aplikację. Przy próbie optymalizacji, jeden, druga pobiera dotyczy także. Firma Microsoft zaleca, począwszy od przepływności odpowiadający 16 rozmiar KiB operacji We/Wy i dopasowywanie, jeśli potrzebny jest więcej przepływności.

Szczegółowe informacje dotyczące przepływności dysku obsługiwanych na dyski SSD Ultra zobacz tabelę w powyższej sekcji cele skalowalności i wydajności. Tak jak rozmiar dysku i operacje We/Wy aprowizowana przepływność jest rozliczana godzinowo MB/s aprowizowane.

### <a name="ultra-ssd-vm-reservation-fee"></a>Największa opłata rezerwacji maszyny Wirtualnej z dysków SSD

Wprowadzamy możliwość na maszynie Wirtualnej, który wskazuje, że maszyna wirtualna jest największa dysków SSD, które są zgodne. Największa SSD zgodnej maszyny Wirtualnej przydziela pojemność dedykowaną przepustowość między wystąpieniem maszyny Wirtualnej obliczeniowe i jednostki skali magazynu bloku, aby zoptymalizować wydajność i zmniejszyć opóźnienie. Dodanie tej funkcji na wynikach maszyn wirtualnych w Twoich rękach rezerwacji, która nakłada się tylko włączenie możliwości Ultra dysków SSD na maszynie Wirtualnej bez podłączania dysk Ultra SSD do niego. Jeśli dysk Ultra SSD jest dołączony do najwyższej SSD zgodnej maszyny Wirtualnej, ta opłata nie będzie stosowane. Ta opłata jest na procesor wirtualny vCPU, którego obsługę zainicjowano na maszynie Wirtualnej.

Zapoznaj się [Azure Disks stronę z cennikiem](https://azure.microsoft.com/pricing/details/managed-disks/) dla nowych dysków SSD najwyższej ceny szczegóły dostępne w ograniczonej wersji zapoznawczej.

### <a name="ultra-ssd-preview-scope-and-limitations"></a>Zakres wersji zapoznawczej Ultra dysków SSD i ograniczenia

W wersji zapoznawczej Ultra dyski SSD:

- Będzie początkowo obsługiwana w regionach wschodnie stany USA 2, w jednej strefie dostępności  
- Można używać tylko w przypadku stref dostępności (zestawy dostępności i jednego wdrożenia maszyn wirtualnych poza strefy będą ma możliwość dołączenia Ultra dysk SSD)
- Są obsługiwane tylko na maszynach wirtualnych v3 ES/DS
- Są dostępne tylko jako dyski z danymi i tylko rozmiar sektora fizycznego 4k pomocy technicznej  
- Może być utworzony tylko jako pustych dysków  
- Obecnie można wdrożyć tylko przy użyciu szablonów usługi Resource Manager, interfejsu wiersza polecenia i zestawu SDK języka Python.
- Będzie (jeszcze) pomocy technicznej dysku zestawy dostępności migawki, obrazy maszyn wirtualnych, zestawów skalowania maszyn wirtualnych i usługi Azure Disk Encryption.
- Nie (jeszcze) obsługuje integrację z usługą Azure Backup lub usługi Azure Site Recovery.
- Podobnie jak w przypadku [większość wersji zapoznawczych](https://azure.microsoft.com/support/legal/preview-supplemental-terms/), ta funkcja nie należy używać w przypadku obciążeń produkcyjnych do momentu ogólna dostępność (GA).