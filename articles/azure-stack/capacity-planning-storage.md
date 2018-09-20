---
title: Pojemność magazynu planowania dla usługi Azure Stack | Dokumentacja firmy Microsoft
description: Więcej informacji o pojemności Planowanie wdrożenia usługi Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2018
ms.author: jeffgilb
ms.reviewer: prchint
ms.openlocfilehash: 9ea46860817d60c2ffbde68c0fc5ae6f6ca14877
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2018
ms.locfileid: "46368784"
---
# <a name="azure-stack-storage-capacity-planning"></a>Planowanie pojemności magazynu stosu platformy Azure
Pojemność magazynu usługi Azure Stack informacje dotyczące planowania ułatwiających planowanie potrzeb dotyczących magazynowania te rozwiązania można znaleźć w poniższych sekcjach.

## <a name="uses-and-organization-of-storage-capacity"></a>Zastosowań i organizacji pojemność magazynu
Konfiguracji hiperkonwergentnej usługi Azure Stack umożliwia udostępnianie urządzeniami pamięci fizycznej. Trzech głównych części dostępnego magazynu należą do zakresu od infrastruktury, magazyn tymczasowy maszyn wirtualnych dzierżawy i magazynem obiektów blob, tabel i kolejek usługi Azure Storage spójne (ACS).

## <a name="spaces-direct-cache-and-capacity-tiers"></a>Bezpośrednie miejsca do magazynowania, pamięć podręczną i warstwy pojemności
Jest pojemność magazynu używane do systemu operacyjnego, lokalne rejestrowanie, zrzuty i innych magazynów tymczasowych infrastruktury potrzeb. Ta pojemność magazynu lokalnego jest oddzielić (urządzenia i pojemność) z urządzeń magazynujących, przenieść do zarządzania bezpośrednimi miejscami do magazynowania konfiguracji. W pozostałej części urządzenia magazynujące są umieszczane w pojedynczej puli pojemności, niezależnie od liczby serwerów w jednostce skalowania. Te urządzenia istnieją dwa typy: pamięć podręczna i pojemności.  Urządzenia pamięci podręcznej są po prostu czy — pamięć podręczna. Bezpośrednie miejsca do magazynowania spowoduje wykorzystanie tych urządzeń do zapisu i odczytu pamięci podręcznej. Możliwości te urządzenia pamięci podręcznej używane, nie są przekazywane do pojemności sformatowany, "visible" sformatowane-dysków wirtualnych. Pojemnościowe są używane w tym celu i podaj "Lokalizacja główna" danych zarządzanych przez miejsca do magazynowania.

Wszystkie pojemność magazynu jest przydzielany i zarządzane bezpośrednio przez infrastrukturę usługi Azure Stack. Operator konieczne wyborów dotyczących konfiguracji i alokacji, lub kliknij przycisk postępowania z opcjami, jeśli chodzi o zwiększanie pojemności. Te decyzje dotyczące projektu zostały wprowadzone w celu zapewnienia zgodności z wymaganiami rozwiązania i są automatyzowane podczas albo początkowej instalacji/wdrażania lub podczas rozszerzania pojemności. Szczegółowe informacje o odporności, zarezerwowanego miejsca na ponowne kompilowanie i inne szczegóły zostały uwzględnione w ramach projektu. 

Operatory można wybrać między wszystkie flash lub hybrydowej konfigurację magazynu:

![Planowanie pojemności usługi Azure storage](media/azure-stack-capacity-planning/storage.png)

W konfiguracji wszystkich flash pamięć podręczna jest NVMe dzięki szerokiemu wyborowi dyski SSD SATA i NVMe pojemności. W konfiguracji hybrydowej pamięć podręczna jest wybór między NVMe i dyski SSD SATA, gdy pojemność jest dysk twardy.

Krótkie podsumowanie bezpośrednimi miejscami do magazynowania i konfiguracja magazynu usługi Azure Stack jest następująca:
- Jednej puli miejsca do magazynowania na jednostkę skali (wszystkie urządzenia magazynujące są skonfigurowane w ramach pojedynczej puli)
- Dyski wirtualne są tworzone jako trzy Lustro kopiowania, aby uzyskać najlepszą wydajność i odporność
- Każdy wirtualny dysk jest sformatowany w systemie plików ReFS
- Pojemność dysków wirtualnych jest obliczana, a następnie przydzielane w sposób, aby pozostawić jedno urządzenie pojemności ilość pojemność danych nieprzydzielone w puli. Jest to odpowiednik jeden dysk o pojemności na serwer.
- Każdego systemu plików ReFS ma włączone szyfrowanie danych magazynowanych w funkcji BitLocker. 

— Dyski wirtualne utworzone automatycznie i ich możliwości są następujące:




|Name (Nazwa)|Obliczenia pojemności|Opis|
|-----|-----|-----|
|Urządzenie lokalne/rozruchowe|Minimalna 340 GB<sup>1</sup>|Magazyn poszczególnych serwerów dla obrazów systemu operacyjnego i maszynami wirtualnymi infrastruktury "local"|
|Infrastruktura|3,5 TB|Całe użycie infrastruktury Azure Stack|
|VmTemp|Zobacz poniżej<sup>2</sup>|Maszyny wirtualne dzierżawcy zostały dołączone dyski tymczasowe i te dane są przechowywane w te dyski wirtualne|
|ACS|Zobacz poniżej <sup>3</sup>|Pojemność na platformie Azure Storage spójne obsługi obiektów blob, tabel i kolejek|

<sup>1</sup> pojemność magazynu co najmniej wymagane przez partnerów rozwiązania usługi Azure Stack.

<sup>2</sup> rozmiaru wirtualnego dysku używanego dla dysków tymczasowych maszyny wirtualnej dzierżawcy jest obliczana jako stosunek pamięci fizycznej serwera. Jak podano w poniższych tabelach rozmiarów maszyn wirtualnych IaaS platformy Azure, dysk tymczasowy to stosunek pamięci fizycznej, przypisany do maszyny wirtualnej. Alokacja gotowe "tymczasowy magazyn na"dysku w usłudze Azure Stack będzie odbywać się w sposób, aby przechwycić większość przypadków użycia, ale może okazać się niemożliwe do spełnienia wszystkich potrzeb dotyczących magazynowania dysku tymczasowego. Współczynnik wybrany odbywa się udostępnianie podczas nie zużywa większość pojemność magazynu rozwiązania tylko pojemności dysku tymczasowego magazynu tymczasowego. Dysk magazynu tymczasowego jest tworzony na serwerze w jednostce skalowania. Pojemność magazynu tymczasowego nie będzie się zwiększać ponad 10% ogólnej dostępności pojemności w puli magazynów, jednostki skalowania. Obliczenie jest coś, tak jak w poniższym przykładzie:

```
  DesiredTempStoragePerServer = PhysicalMemory * 0.65 * 8
  TempStoragePerSolution = DesiredTempStoragePerServer * NumberOfServers
  PercentOfTotalCapacity = TempStoragePerSolution / TotalAvailableCapacity
  If (PercentOfTotalCapacity <= 0.1)
      TempVirtualDiskSize = DesiredTempStoragePerServer
  Else
      TempVirtualDiskSize = (TotalAvailableCapacity * 0.1) / NumberOfServers
```

<sup>3</sup> — dyski wirtualne utworzone do użytku przez usługi ACS umożliwiają proste dzielenie pozostałej pojemności. Jak wspomniano, wszystkie wirtualne dyski są dublowane trzystopniowo i nieprzydzielonego jest jeden dysk pojemności, przez które pojemności dla każdego serwera. Różne — dyski wirtualne wymienionych powyżej są przydzielane najpierw, a pozostałe zasoby są używane do usługi ACS-dysków wirtualnych.

## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się więcej o arkusz kalkulacyjny planowania pojemności usługi Azure Stack](capacity-planning-spreadsheet.md)
