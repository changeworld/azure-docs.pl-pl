---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/15/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 72d87142f9b9c1f7bcb2b02281851bd1e29bc9c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78262136"
---
Dyski zarządzane platformy Azure oferuje obecnie cztery typy dysków, każdy typ jest przeznaczony do określonych scenariuszy klienta.

## <a name="disk-comparison"></a>Porównanie dysków

Poniższa tabela zawiera porównanie dysków ultra, dysków PÓŁPRZEWODNIKOWYCH premium (SSD), standardowych dysków SSD i standardowych dysków twardych (HDD) dla dysków zarządzanych, aby ułatwić podjęcie decyzji, czego należy użyć.

|   | Dysk w warstwie Ultra   | Dysk SSD w warstwie Premium   | Dysk SSD w warstwie Standardowa   | Dysk HDD w warstwie Standardowa   |
|---------|---------|---------|---------|---------|
|Typ dysku   |SSD   |SSD   |SSD   |HDD   |
|Scenariusz   |Obciążenia intensywnie korzystające z operacji we/wy, takie jak [SAP HANA,](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)bazy danych najwyższej warstwy (na przykład SQL, Oracle) i inne obciążenia wymagające dużych transakcji.   |Obciążenia produkcyjne i wrażliwe na wydajność   |Serwery internetowe, aplikacje dla przedsiębiorstw używane w niewielkim stopniu oraz tworzenie i testowanie rozwiązań   |Kopia zapasowa, rozwiązania niekrytyczne, rzadki dostęp   |
|Maksymalny rozmiar dysku   |65 536 gibibajtów (GiB)    |32 767 GiB    |32 767 GiB   |32 767 GiB   |
|Maksymalna przepustowość   |2000 MiB/s    |900 MiB/s   |750 MiB/s   |500 MiB/s   |
|Maks. liczba operacji we/wy na sekundę   |160 000    |20 000   |6000   |2000   |

## <a name="ultra-disk"></a>Dysk w warstwie Ultra

Dyski platformy Azure w warstwie Ultra oferują magazyn danych na dysku z wysoką przepływnością, dużą liczbą operacji we/wy na sekundę i stałym małym opóźnieniem dla maszyn wirtualnych usługi Azure IaaS. Niektóre dodatkowe korzyści z dysków ultra obejmują możliwość dynamicznej zmiany wydajności dysku, wraz z obciążeniami, bez konieczności ponownego uruchamiania maszyn wirtualnych (VM). Dyski w warstwie Ultra to rozwiązanie odpowiednie w przypadku obciążeń intensywnie korzystających z danych, takich jak platforma SAP HANA, bazy danych górnej warstwy i obciążenia z dużą liczbą transakcji. Dysków w warstwie Ultra można używać tylko jako dysków danych. Zalecamy używanie dysków SSD w warstwie Premium jako dysków systemu operacyjnego.

### <a name="performance"></a>Wydajność

Podczas aprowizowania ultra dysku można niezależnie skonfigurować pojemność i wydajność dysku. Dyski ultra są dostępne w kilku stałych rozmiarach, od 4 GiB do 64 TiB i posiadają elastyczny model konfiguracji wydajności, który pozwala na niezależną konfigurację Operacji We/Wy i przepustowości.

Niektóre kluczowe możliwości dysków ultra to:

- Pojemność dysku: Pojemność dysków ultra waha się od 4 GiB do 64 TiB.
- IOPS dysku: Dyski ultra obsługują limity we/wy we/wy 300 IOPS/GiB, maksymalnie do 160 K IOPS na dysk. Aby osiągnąć zainicjowane we/wy we/wy obsługi administracyjnej, upewnij się, że wybrane we/wy dotyczące we/wy są mniejsze niż limit we/wy maszyny Wirtualnej. Minimalna gwarantowana liczba we/wy na dysk to 2 we/wy/gib, przy czym ogólny poziom bazowy wynosi co najmniej 100 we/wy. Na przykład, jeśli miałeś dysk ultra 4 GiB, będziesz miał co najmniej 100 Operacji We/Wy, zamiast ośmiu operacji We/Wy.
- Przepustowość dysku: W przypadku dysków ultra limit przepływności pojedynczego dysku wynosi 256 KiB/s dla każdego aprowizowanego we/wy, maksymalnie do 2000 MB/s na dysk (gdzie MB/s = 10^6 bajtów na sekundę). Minimalna gwarantowana przepustowość na dysku wynosi 4KiB/s dla każdej aprowizowanej obsługi we/wy, przy ogólnym poziomie bazowym minimum 1 MB/s.
- Dyski ultra obsługują dostosowywanie atrybutów wydajności dysku (Usługi We/Wy i przepływność) w czasie wykonywania bez odłączania dysku od maszyny wirtualnej. Po wydaniu operacji zmiany rozmiaru wydajności dysku na dysku może upłynąć do godziny, aby zmiana faktycznie została uwzględniona. Istnieje limit czterech operacji zmieniania rozmiaru wydajności w oknie 24-godzinnym. Jest możliwe, aby operacja zmieniania rozmiaru wydajności uległa awarii z powodu braku przepustowości wydajności.

### <a name="disk-size"></a>Rozmiar dysku

|Rozmiar dysku (GiB)  |Czapka we/wy na IOPS  |Limit przepływności (MB/s)  |
|---------|---------|---------|
|4     |1200         |300         |
|8     |2,400         |600         |
|16     |4800         |1200         |
|32     |9600         |2000         |
|64     |19,200         |2000         |
|128     |38,400         |2000         |
|256     |76 800         |2000         |
|512     |80 000         |2000         |
|1,024-65,536 (rozmiary w tym zakresie wzrastające w przyrostach 1 TiB)     |160 000         |2000         |

### <a name="ga-scope-and-limitations"></a>Zakres i ograniczenia ga

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](managed-disks-ultra-disks-GA-scope-and-limitations.md)]
