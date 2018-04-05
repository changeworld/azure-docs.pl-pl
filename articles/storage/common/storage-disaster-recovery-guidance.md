---
title: Co zrobić w przypadku awarii usługi Azure Storage | Dokumentacja firmy Microsoft
description: Co zrobić w przypadku awarii usługi Azure Storage
services: storage
documentationcenter: .net
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 8f040b0f-8926-4831-ac07-79f646f31926
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 1/19/2017
ms.author: tamram
ms.openlocfilehash: 3c313025917bba06675d3b2d844a6740fab89fbc
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="what-to-do-if-an-azure-storage-outage-occurs"></a>Co zrobić po wystąpieniu awarii usługi Azure Storage
Firma Microsoft pracujemy nad twardego upewnij się, że naszych usług są zawsze dostępne. Czasami wymusza poza naszych wpływ kontroli nam w sposób, aby spowodować usługi nieplanowanych przestojów w jednym lub więcej regionów. Aby ułatwić obsługę tych zdarzeń w rzadkich, udostępniamy wysokiego poziomu dotyczące usługi Azure Storage.

## <a name="how-to-prepare"></a>Jak przygotować
Jest to bardzo ważne, aby każdy klient przygotował własny plan odzyskiwania po awarii. Starań, aby dokonać odzyskiwania po awarii magazynu zwykle obejmuje zarówno personel, jak i zautomatyzowane procedury, aby ponownie uaktywnić aplikacji w działającym stanie. Zapoznaj się z dokumentacją Azure poniżej, aby utworzyć własnego planu odzyskiwania po awarii:

* [Lista kontrolna dotycząca dostępności](https://docs.microsoft.com/azure/architecture/checklist/availability)
* [Projektowanie aplikacji odpornych na błędy dla platformy Azure](https://docs.microsoft.com/azure/architecture/resiliency/)
* [Usługa Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/)
* [Replikacja usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy)
* [Usługa Kopia zapasowa Azure](https://azure.microsoft.com/services/backup/)

## <a name="how-to-detect"></a>Jak wykryć
Zalecanym sposobem określić stanu usługi Azure jest subskrybować [pulpit nawigacyjny kondycji usługi Azure](https://azure.microsoft.com/status/).

## <a name="what-to-do-if-a-storage-outage-occurs"></a>Co zrobić, jeśli wystąpi awaria magazynu
Jeśli co najmniej jednej usługi magazynu są tymczasowo niedostępne na jeden lub więcej regionów, dostępne są dwie opcje należy wziąć pod uwagę. Jeśli chcesz natychmiastowy dostęp do danych, należy rozważyć opcję 2.

### <a name="option-1-wait-for-recovery"></a>Opcja 1: Poczekaj, aż odzyskiwanie
W takim przypadku jest wymagana żadna akcja ze strony użytkownika. Pracujemy nad dokładnie do przywrócenia dostępności usług Azure. Można monitorować stan usługi na [pulpit nawigacyjny kondycji usługi Azure](https://azure.microsoft.com/status/).

### <a name="option-2-copy-data-from-secondary"></a>Opcja 2: Kopiowanie danych z pomocniczego
Jeśli została wybrana opcja [dostęp do odczytu magazynu geograficznie nadmiarowego (RA-GRS)](storage-redundancy-grs.md#read-access-geo-redundant-storage) (zalecane) dla konta magazynu, konieczne będzie dostęp do odczytu danych z regionu pomocniczego. Można użyć narzędzia, takie jak [AzCopy](storage-use-azcopy.md), [programu Azure PowerShell](storage-powershell-guide-full.md)i [Biblioteka przenoszenia danych Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/) na skopiowanie danych z regionu pomocniczego do innego konta magazynu w regionie unimpacted, a następnie wskaż aplikacji do tego konta magazynu dla odczytu i zapisu dostępności.

## <a name="what-to-expect-if-a-storage-failover-occurs"></a>Czego można oczekiwać, jeśli magazyn pracy awaryjnej
Jeśli została wybrana opcja [magazyn geograficznie nadmiarowy (GRS)](storage-redundancy-grs.md) lub [dostęp do odczytu magazynu geograficznie nadmiarowego (RA-GRS)](storage-redundancy-grs.md#read-access-geo-redundant-storage) (zalecane), usługi Azure Storage będzie przechowywać swoje dane trwałe w dwóch regionach (podstawowych i pomocniczych). W obu regionów usługi Magazyn Azure przechowuje stale wiele replik danych.

W przypadku regionalnej awarii wpływa regionu podstawowego, spróbujemy najpierw przywrócić usługę w tym regionie. Zależne od charakteru po awarii i jej wpływ na środowisko, w niektórych rzadkich firma Microsoft nie można przywrócić regionu podstawowego. W tym momencie zostaną wykonane geograficznie trybu failover. Replikacja danych między region jest proces asynchroniczny, które obejmują opóźnienia, więc jest to możliwe, że zmiany, które nie zostały jeszcze zreplikowane w regionie pomocniczym mogą zostać utracone. Można zbadać ["Czas ostatniej synchronizacji" konta magazynu](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/) Aby uzyskać szczegółowe informacje o stanie replikacji.

Kilka punktów dotyczących środowiska pracy awaryjnej geo magazynu:

* Magazyn geograficznie pracy w trybie failover tylko zostanie wywołane przez zespół usługi Azure Storage — nie jest wymagane żadne akcje klienta.
* Istniejące punkty końcowe usługi magazynu, dla obiektów blob, tabel, kolejek i plików jest taka sama po trybu failover; wpis DNS oferowanych przez firmę Microsoft będzie muszą zostać zaktualizowane przejść z regionu podstawowego w regionie pomocniczym.  Microsoft przeprowadzi aktualizację automatycznie w ramach procesu pracy awaryjnej geo.
* Przed i w trybie failover geograficznie nie będziesz mieć dostęp do zapisu do konta magazynu ze względu na wpływ po awarii, ale można nadal odczytywać z lokacji dodatkowej Jeśli Twoje konto magazynu został skonfigurowany jako RA-GRS.
* Po zakończeniu trybu failover z magazynu geograficznie i zmiany DNS rozpropagowane, zostanie wznowione odczytu i zapisu do konta magazynu; to wskazuje na przeznaczenie jako dodatkowej punktu końcowego. 
* Należy pamiętać, że będzie miał dostęp zapisu, jeśli masz GRS lub RA-GRS skonfigurowanych dla konta magazynu. 
* Można zbadać ["Geograficznie trybu Failover ostatniego" konta magazynu](https://msdn.microsoft.com/library/azure/ee460802.aspx) Aby uzyskać więcej szczegółów.
* Po pracy awaryjnej konta magazynu będzie można w pełni funkcjonalne, ale w stanie "obniżeniem", ponieważ faktycznie znajduje się w regionie autonomiczny z nie możliwości replikacja geograficzna. Aby zmniejszyć to zagrożenie, firma Microsoft będzie przywrócić oryginalny regionu podstawowego i wykonaj geograficznie-powrotu po awarii do przywrócenia pierwotnego stanu. W przypadku oryginalnego regionu podstawowego jest nieodwracalny, firma Microsoft będzie przydzielić inny region pomocniczy.
  Więcej szczegółów na infrastrukturze replikacja geograficzna usługi Azure Storage, można znaleźć w artykule na blogu zespołu magazynu o [Opcje nadmiarowości i RA-GRS](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

## <a name="best-practices-for-protecting-your-data"></a>Najlepsze rozwiązania w zakresie ochrony danych
Brak niektórych zalecane sposoby kopii zapasowej danych magazynu na bieżąco.

* Dyski maszyny Wirtualnej — [usługi Kopia zapasowa Azure](https://azure.microsoft.com/services/backup/) do tworzenia kopii zapasowych dysków maszyny Wirtualnej, z maszyn wirtualnych platformy Azure.
* Blokowych obiektów blob — Utwórz [migawki](https://msdn.microsoft.com/library/azure/hh488361.aspx) każdego blokowania obiektów blob lub kopiowania obiektów blob na inne konto magazynu przy użyciu innego regionu [AzCopy](storage-use-azcopy.md), [programu Azure PowerShell](storage-powershell-guide-full.md), lub [Biblioteka przenoszenia danych Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/).
* Użyj tabel — [AzCopy](storage-use-azcopy.md) Aby wyeksportować dane w tabeli na innym koncie magazynu w innym regionie.
* Pliki — [AzCopy](storage-use-azcopy.md) lub [programu Azure PowerShell](storage-powershell-guide-full.md) do kopiowania plików na inne konto magazynu w innym regionie.

Aby uzyskać informacje o tworzeniu aplikacji, które korzystają z funkcji RA-GRS, zapoznaj się [Projektowanie wysoce dostępnych aplikacji przy użyciu magazynu RA-GRS](../storage-designing-ha-apps-with-ragrs.md)

