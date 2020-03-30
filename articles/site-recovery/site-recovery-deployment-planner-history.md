---
title: Historia wersji programu Planowania wdrażania usługi Azure Recovery
description: Znane różne wersje planisty wdrażania odzyskiwania witryny poprawki i znane ograniczenia wraz z ich datami wydania.
services: site-recovery
author: Daya-Patil
manager: carmonm
ms.topic: article
ms.service: site-recovery
ms.date: 10/16/2019
ms.author: dapatil
ms.openlocfilehash: bf32809f426f3bfcabd08ec3bd95e76202aa8f84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72433414"
---
# <a name="azure-site-recovery-deployment-planner-version-history"></a>Historia wersji programu Planowania wdrażania usługi Azure Recovery

Ten artykuł zawiera historię wszystkich wersji programu Azure Site Recovery Deployment Planner wraz z poprawkami, znanymi ograniczeniami w każdym z nich i ich datami wydania.

## <a name="version-251"></a>Wersja 2.51

**Data wydania: 22 sierpnia 2019 r.**

**Poprawki:**

- Naprawiono problem z zaleceniem kosztów w planowaniu wdrożenia w wersji 2.5

## <a name="version-25"></a>Wersja 2.5

**Data wydania: 29 lipca 2019**

**Poprawki:**

- W przypadku maszyn wirtualnych VMware i maszyn fizycznych zalecenie jest aktualizowane na podstawie replikacji na dyskach zarządzanych.
- Dodano obsługę systemu Windows 10 (x64), Windows 8.1 (x64), Windows 8 (x64), Windows 7 (x64) z dodatkiem SP1 lub nowszym

## <a name="version-24"></a>Wersja 2.4

**Data wydania: 17 kwietnia 2019**

**Poprawki:**

- Poprawiono zgodność systemu operacyjnego, w szczególności podczas obsługi błędów opartych na lokalizacji.
- Dodano maszyny wirtualne z szybkością zmiany danych do 20 Mb/s (zmianami) do listy kontrolnej zgodności.
- Ulepszone komunikaty o błędach
- Dodano obsługę vCenter 6.7.
- Dodano obsługę stacji roboczej Windows Server 2019 i Red Hat Enterprise Linux (RHEL).



## <a name="version-23"></a>Wersja 2.3

**Data wydania: 3 grudnia 2018 r.**

**Poprawki:**

- Rozwiązano problem, który uniemożliwiał Planiście wdrażania generowanie raportu z podana lokalizacją docelową i subskrypcją.

## <a name="version-22"></a>Wersja 2.2 

**Data wydania: 25 kwietnia 2018 r.**

**Poprawki:**

- Operacje GetVMList:
  - Naprawiono błąd, który powodował niepowodzenie listy GetVMList, jeśli określony folder nie istnieje. Teraz tworzy katalog domyślny lub tworzy katalog określony w parametrze pliku wyjściowego.
  - Dodano bardziej szczegółowe przyczyny awarii GetVMList.
- Dodano informacje o typie maszyny Wirtualnej jako kolumnę w arkuszu zgodnych maszyn wirtualnych raportu Planer wdrażania.
- Odzyskiwanie po awarii funkcji Hyper-V na platformie Azure:
  - Wykluczone maszyny wirtualne z udostępnionymi dyskami VHD i PassThrough z profilowania. Operacja Startprofiling pokazuje listę wykluczonych maszyn wirtualnych w konsoli.
  - Dodano maszyny wirtualne z więcej niż 64 dyskami do listy niezgodnych maszyn wirtualnych.
  - Zaktualizowano współczynnik kompresji replikacji początkowej (IR) i replikacji różnicowej (DR).
  - Dodano ograniczoną obsługę pamięci masowej SMB.

## <a name="version-21"></a>Wersja 2.1

**Data wydania: 3 stycznia 2018 r.**

**Poprawki:**

- Zaktualizowano raport programu Excel.
- Naprawiono błędy w operacji GetThroughput.
- Dodano opcję, aby ograniczyć liczbę maszyn wirtualnych do profilu lub wygenerować raport. Domyślny limit wynosi 1000 maszyn wirtualnych.
- Odzyskiwanie po awarii usługi VMware na platformie Azure:
  - Naprawiono błąd, który powodował, że maszyna wirtualna systemu Windows Server 2016 przechodziła do niezgodnej tabeli. 
  - Zaktualizowano komunikaty zgodności maszyn wirtualnych systemu Windows (Extensible Firmware Interface).
- Zaktualizowano vmware do platformy Azure i funkcji Hyper-V na platformie Azure, limit zmian danych maszyny Wirtualnej na maszynę wirtualną. 
- Zwiększona niezawodność analizowania plików listy maszyn wirtualnych.

## <a name="version-201"></a>Wersja 2.0.1

**Data wydania: 7 grudnia 2017**

**Poprawki:**

- Dodano zalecenie optymalizacji przepustowości sieci.

## <a name="version-20"></a>Wersja 2.0

**Data wydania: 28 listopada 2017**

**Poprawki:**

- Dodano obsługę funkcji Hyper-V do odzyskiwania po awarii platformy Azure.
- Dodano kalkulator kosztów.
- Dodano sprawdzanie wersji systemu operacyjnego dla odzyskiwania po awarii VMware do platformy Azure, aby ustalić, czy maszyna wirtualna jest zgodna lub niezgodna z ochroną. Narzędzie używa ciągu wersji systemu operacyjnego, który jest zwracany przez serwer vCenter dla tej maszyny Wirtualnej. Jest to wersja systemu operacyjnego gościa, którą użytkownik wybrał podczas tworzenia maszyny Wirtualnej w VMware.

**Znane ograniczenia:**

- W przypadku odzyskiwania po awarii funkcji Hyper-V na platformie Azure `]`maszyna ``` ` ``` wirtualna z nazwą zawierającą znaki takie jak: `,`, `"` `[`, , i nie są obsługiwane. Jeśli profilowane, generowanie raportu zakończy się niepowodzeniem lub będzie miał niepoprawny wynik.
- W przypadku odzyskiwania po awarii platformy VMware do platformy Azure maszyna wirtualna z nazwą zawierającą przecinek nie jest obsługiwana. Jeśli profilowane, generowanie raportu kończy się niepowodzeniem lub będzie miał niepoprawny wynik.

## <a name="version-131"></a>Wersja 1.3.1

**Data wydania: 19 lipca 2017** 

**Poprawki:**

- Dodano obsługę dużych dysków (> 1 TB) w generowaniu raportów. Teraz można użyć Planisty wdrażania do planowania replikacji dla maszyn wirtualnych o rozmiarach dysków większych niż 1 TB (do 4095 GB).
Dowiedz się więcej na temat [obsługi dużych dysków w usłudze Azure Site Recovery](https://azure.microsoft.com/blog/azure-site-recovery-large-disks/)

## <a name="version-13"></a>Wersja 1.3

**Data wydania: 9 maja 2017**

**Poprawki:**

- Dodano obsługę dysku zarządzanego w generowaniu raportów. Liczba maszyn wirtualnych, które można umieścić na jednym koncie magazynu jest obliczana na podstawie tego, czy dysk zarządzany jest wybrany dla trybu failover/test failover.

## <a name="version-12"></a>Wersja 1.2

**Data wydania: 7 kwietnia 2017**

**Poprawki:**

- Dodano typ rozruchu (BIOS lub EFI) sprawdza dla każdej maszyny Wirtualnej, aby ustalić, czy maszyna wirtualna jest zgodna lub niezgodna z ochroną.
- Dodano informacje o typie systemu operacyjnego dla każdej maszyny wirtualnej w zgodnych maszynach wirtualnych i niezgodnych arkuszach maszyn wirtualnych.
- Dodano obsługę operacji GetThroughput dla regionów rządów STANÓW Zjednoczonych i chin microsoft azure.
- Dodano kilka testów wymagań wstępnych dla serwerów vCenter i ESXi.
- Naprawiono błąd, który powodował, że niepoprawny raport był generowany, gdy ustawienia regionalne są ustawione na inne niż angielskie.

## <a name="version-11"></a>Wersja 1.1

**Data wydania: 9 marca 2017**

**Poprawki:**

- Naprawiono błąd, który uniemożliwiał profilowanie maszyn wirtualnych, gdy istnieją co najmniej dwie maszyny wirtualne o tej samej nazwie lub adresIE IP w różnych hostach vCenter ESXi.
- Naprawiono błąd, który powodował wyłączenie kopiowania i wyszukiwania zgodnych maszyn wirtualnych i niezgodnych arkuszy maszyn wirtualnych.

## <a name="version-10"></a>Wersja 1.0

**Data wydania: 23 lutego 2017**

**Znane ograniczenia:**

- Obsługuje tylko scenariusze odzyskiwania po awarii platformy VMware na platformie Azure. W przypadku scenariuszy odzyskiwania po awarii funkcji Hyper-V na platformie Azure użyj [narzędzia do planowania pojemności funkcji Hyper-V](./site-recovery-capacity-planning-for-hyper-v-replication.md).
- Nie obsługuje operacji GetThroughput dla instytucji rządowych i administracji chińskiej w regionach Platformy Microsoft Azure w Chinach.
- Narzędzie nie może profilować maszyn wirtualnych, jeśli serwer vCenter ma co najmniej dwie maszyny wirtualne o tej samej nazwie lub adres IP w różnych hostach ESXi.
W tej wersji narzędzie pomija profilowanie w przypadku zduplikowanych nazw lub adresów IP w parametrze VMListFile. Obejście polega na profilowaniu maszyn wirtualnych przy użyciu hosta ESXi zamiast serwera vCenter. Upewnij się, że należy uruchomić jedno wystąpienie dla każdego hosta ESXi.
