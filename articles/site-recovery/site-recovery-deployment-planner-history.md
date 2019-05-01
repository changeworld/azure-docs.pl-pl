---
title: Historia wersji narzędzia planista wdrażania odzyskiwania lokacji platformy Azure
description: Znane różne wersje Planisty wdrażania odzyskiwania lokacji poprawek i znanych ograniczeń polecenia wraz z ich daty wydania.
services: site-recovery
author: Daya-Patil
manager: carmonm
ms.topic: article
ms.service: site-recovery
ms.date: 04/24/2019
ms.author: dapatil
ms.openlocfilehash: 2edf7ce3be1402a497ceab5b826a89ee43c5c39b
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64927375"
---
# <a name="azure-site-recovery-deployment-planner-version-history"></a>Historia wersji narzędzia planista wdrażania odzyskiwania lokacji platformy Azure

Ten artykuł zawiera historię wszystkich wersji Planisty wdrożenia usługi Azure Site Recovery wraz z poprawek, znane ograniczenia w każdym i ich daty wydania.

## <a name="version-24"></a>Wersja 2.4

**Data wydania: 17 kwietnia 2019 r.**

**Poprawki:**

- Zwiększona zgodność systemu operacyjnego, w szczególności obsługi błędów na podstawie lokalizacji.
- Dodano maszyny wirtualne z maksymalnie 20 MB/s danych zmień współczynnik zmian na liście kontrolnej zgodności.
- Ulepszone komunikaty o błędach
  - Dodano obsługę vCenter 6.7.
  - Dodano obsługę dla stacji roboczej systemu Windows Server 2019 i Red Hat Enterprise Linux (RHEL).



## <a name="version-23"></a>Wersja 2.3

**Data wydania: 3 grudnia 2018 r.**

**Poprawki:**

- Rozwiązano problem, który uniemożliwił planista wdrażania na Generowanie raportu przy użyciu lokalizacji docelowej i subskrypcji.

## <a name="version-22"></a>W wersji 2.2 

**Data wydania: 25 kwietnia 2018 r.**

**Poprawki:**

- Operacje GetVMList:
  - Rozwiązano problem powodujący GetVMList się niepowodzeniem, jeśli określony folder nie istnieje. Go teraz tworzy katalog domyślny albo tworzy katalog określony w parametrze plik wyjściowy.
  - Dodano bardziej szczegółowe przyczyny niepowodzenia GetVMList.
- Dodano informacje typu maszyny Wirtualnej jako kolumny w arkuszu zgodnych maszyn wirtualnych raportu planisty wdrożenia usługi.
- Funkcji Hyper-V do odzyskiwania po awarii platformy Azure:
  - Dyski wykluczone maszyn wirtualnych przy użyciu udostępnionych wirtualnych dysków twardych i przekazywanie z profilowania. Operacja Startprofiling pokazuje listę wykluczonych maszyn wirtualnych w konsoli.
  - Dodano maszyn wirtualnych przy użyciu więcej niż 64 dyski do listy niezgodnych maszyn wirtualnych.
  - Zaktualizowano replikacji początkowej (IR) i współczynnik kompresji replikacji (DR) delta.
  - Dodano ograniczoną obsługę magazynu SMB.

## <a name="version-21"></a>W wersji 2.1

**Data wydania: 3 stycznia 2018 r.**

**Poprawki:**

- Zaktualizowany raport programu Excel.
- Usunięte usterki w operacji GetThroughput.
- Dodano opcję, aby ograniczyć liczbę maszyn wirtualnych do profilowania i generowania raportu. Domyślny limit wynosi 1000 maszyn wirtualnych.
- Program VMware do odzyskiwania po awarii platformy Azure:
  - Rozwiązano problem z systemu Windows Server 2016 maszyny wirtualnej przesyłane do tabeli niezgodne. 
  - Zaktualizowano komunikaty o zgodności maszyn wirtualnych Windows interfejsu EFI (Extensible Firmware Interface).
- Zaktualizowano replikacji z oprogramowania VMware na platformę Azure oraz funkcji Hyper-V do platformy Azure, współczynnika zmian danych maszyny Wirtualnej limit dla maszyny Wirtualnej. 
- Ulepszone niezawodność podczas analizowania pliku listy maszyn wirtualnych.

## <a name="version-201"></a>Wersja 2.0.1

**Data wydania: 7 grudnia 2017 r.**

**Poprawki:**

- Dodano zalecenie w celu zoptymalizowania przepustowości sieci.

## <a name="version-20"></a>W wersji 2.0

**Data wydania: 28 listopada 2017 r.**

**Poprawki:**

- Dodano obsługę funkcji Hyper-V do odzyskiwania po awarii platformy Azure.
- Kalkulator ponoszenia dodatkowych kosztów.
- Dodano sprawdzenia wersji oprogramowania VMware do odzyskiwania po awarii platformy Azure w celu ustalenia, czy maszyna wirtualna jest zgodna lub niezgodna z funkcją ochrony. Narzędzie wykorzystuje ciąg wersji systemu operacyjnego, który jest zwracany przez serwer vCenter dla tej maszyny Wirtualnej. Jest to wersja systemu operacyjnego gościa ten użytkownik został wybrany podczas tworzenia maszyny Wirtualnej w środowisku VMware.

**Znane ograniczenia:**

- Dla funkcji Hyper-V do odzyskiwania po awarii platformy Azure, maszyny Wirtualnej przy użyciu nazwy zawierające znaki, takie jak: `,`, `"`, `[`, `]`, i ``` ` ``` nie są obsługiwane. Jeśli profilowania, generowania raportu zakończy się niepowodzeniem lub będą mieć nieprawidłowe wyniki.
- Dla oprogramowania VMware do odzyskiwania po awarii platformy Azure maszyna wirtualna o nazwie zawierający przecinkami nie jest obsługiwane. Jeśli profilowane, zgłosić generowania kończy się niepowodzeniem, lub mają niepoprawny wynik.

## <a name="version-131"></a>Wersji 1.3.1

**Data wydania: 19 lipca 2017 r.** 

**Poprawki:**

- Dodano obsługę dużych dysków (> 1 TB) na potrzeby generowania raportów. Teraz możesz użyć Planisty wdrażania, aby zaplanować replikację maszyn wirtualnych z dyskami o rozmiarze większym niż 1 TB (maksymalnie 4095 GB).
Dowiedz się więcej na temat [obsługi dużych dysków w usłudze Azure Site Recovery](https://azure.microsoft.com/blog/azure-site-recovery-large-disks/)

## <a name="version-13"></a>W wersji 1.3

**Data wydania: 9 maja 2017 r.**

**Poprawki:**

- Dodano obsługę dysku zarządzanego na potrzeby generowania raportów. Liczba maszyn wirtualnych, które można umieścić na koncie magazynu jednego jest obliczana na podstawie, jeśli wybrano dysk zarządzany tryb Failover/testu pracy w trybie Failover.

## <a name="version-12"></a>w wersji 1.2

**Data wydania: 7 kwietnia 2017 r.**

**Poprawki:**

- Dodano rozruchu (BIOS lub EFI) sprawdzania typu dla każdej maszyny Wirtualnej w celu ustalenia, czy maszyna wirtualna jest zgodna lub niezgodna z funkcją ochrony.
- Dodano systemu operacyjnego wpisz informacje dotyczące poszczególnych maszyn wirtualnych zgodnych maszyn wirtualnych i niezgodnych maszyn wirtualnych z arkuszy.
- Dodano obsługę operację GetThroughput w regionach US Government i platformy Microsoft Azure (Chiny).
- Dodano kilka testów wymagań wstępnych dla serwerów vCenter i ESXi.
- Rozwiązano problem nieprawidłowy raport wprowadzenie generowane, gdy ustawienia regionalne są ustawione na innej niż angielska.

## <a name="version-11"></a>W wersji 1.1

**Data wydania: 9 marca 2017 r.**

**Poprawki:**

- Rozwiązano problem, który uniemożliwił profilowania maszyn wirtualnych, gdy istnieją co najmniej dwie maszyny wirtualne o takiej samej nazwie lub adresie IP na różnych hostach ESXi vCenter.
- Rozwiązano problem powodujący, kopiowanie i wyszukiwanie wyłączona dla zgodnych maszyn wirtualnych i niezgodnych maszyn wirtualnych z arkuszy.

## <a name="version-10"></a>W wersji 1.0

**Data wydania: 23 lutego 2017 r.**

**Znane ograniczenia:**

- Obsługuje tylko dla replikacji VMware – scenariuszy odzyskiwania po awarii platformy Azure. W przypadku funkcji Hyper-V do platformy Azure po awarii scenariuszy odzyskiwania, użyj [narzędzia planisty wydajności funkcji Hyper-V](./site-recovery-capacity-planning-for-hyper-v-replication.md).
- Nie obsługuje operację GetThroughput w regionach US Government i platformy Microsoft Azure (Chiny).
- Profil defincji narzędzia maszyn wirtualnych, jeśli serwer vCenter ma co najmniej dwie maszyny wirtualne o takiej samej nazwie lub adresie IP na różnych hostach ESXi.
W tej wersji narzędzie pomija profilowanie w przypadku zduplikowanych nazw lub adresów IP w parametrze VMListFile. Obejście polega na profilowaniu maszyn wirtualnych przy użyciu hosta ESXi zamiast serwera vCenter. Upewnij się, aby uruchomić jedno wystąpienie każdego hosta ESXi.
