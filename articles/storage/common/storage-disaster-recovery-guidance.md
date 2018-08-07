---
title: Co należy zrobić w przypadku awarii usługi Azure Storage | Dokumentacja firmy Microsoft
description: Co należy zrobić w przypadku awarii usługi Azure Storage
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 07/15/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: bca4b13ea2a003ea428351bcff44944630387e1b
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2018
ms.locfileid: "39528014"
---
# <a name="what-to-do-if-an-azure-storage-outage-occurs"></a>Co zrobić po wystąpieniu awarii usługi Azure Storage
W firmie Microsoft pracujemy nad upewnij się, że nasze usługi są zawsze dostępne. Czasami wymusza poza naszym wpływ kontroli nam w sposób, aby spowodować przerwy w działaniu usługi nieplanowane w jednym lub kilku regionach. Aby obsługiwać te rzadko, firma Microsoft zapewnia następujące wskazówki wysokiego poziomu usługi Azure Storage.

## <a name="how-to-prepare"></a>Jak przygotować
Jest to bardzo ważne, aby każdy klient przygotował własny plan odzyskiwania po awarii. Próba odzyskiwanie po awarii magazynu zwykle obejmuje personel i zautomatyzowanych procedur, aby ponownie aktywować swoje aplikacje w działającym stanie. Zapoznaj się z dokumentacją Azure poniżej, aby utworzyć własny plan odzyskiwania po awarii:

* [Lista kontrolna dotycząca dostępności](https://docs.microsoft.com/azure/architecture/checklist/availability)
* [Projektowanie aplikacji odpornych na błędy dla platformy Azure](https://docs.microsoft.com/azure/architecture/resiliency/)
* [Usługa Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/)
* [Replikacja usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy)
* [Usługa Azure Backup](https://azure.microsoft.com/services/backup/)

## <a name="how-to-detect"></a>Jak wykryć
Zalecanym sposobem określenia stanu usługi platformy Azure jest do subskrybowania [pulpit nawigacyjny kondycji usługi platformy Azure](https://azure.microsoft.com/status/).

## <a name="what-to-do-if-a-storage-outage-occurs"></a>Co zrobić po wystąpieniu awarii usługi Storage
Jeśli co najmniej jedna usługa magazynu jest tymczasowo niedostępny w jednym lub wielu regionach, istnieją dwie opcje należy wziąć pod uwagę. Jeśli chcesz uzyskać natychmiastowy dostęp do danych należy rozważyć opcja 2.

### <a name="option-1-wait-for-recovery"></a>Opcja 1: Poczekaj, aż odzyskiwanie
W takim wypadku żadna akcja ze strony użytkownika jest wymagana. Pracujemy, aby przywrócić dostępność usługi platformy Azure. Możesz monitorować stan usługi na [pulpit nawigacyjny kondycji usługi platformy Azure](https://azure.microsoft.com/status/).

### <a name="option-2-copy-data-from-secondary"></a>Opcja 2: Kopiowanie danych z pomocniczego
Jeśli została wybrana opcja [dostęp do odczytu magazynu geograficznie nadmiarowego (RA-GRS)](storage-redundancy-grs.md#read-access-geo-redundant-storage) (zalecane) dla konta magazynu, masz dostęp do odczytu do danych z regionu pomocniczego. Użyj narzędzi takich jak [AzCopy](storage-use-azcopy.md), [programu Azure PowerShell](storage-powershell-guide-full.md)i [Biblioteka przenoszenia danych usługi Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/) do skopiowania danych z regionu pomocniczego do innego konta magazynu w unimpacted regionu, a następnie punktu aplikacji do tego magazynu konta dla obu dostępność odczytu i zapisu.

## <a name="what-to-expect-if-a-storage-failover-occurs"></a>Czego można oczekiwać, jeśli do przechowywania pracy awaryjnej
Jeśli została wybrana opcja [magazyn geograficznie nadmiarowy (GRS)](storage-redundancy-grs.md) lub [dostęp do odczytu magazynu geograficznie nadmiarowego (RA-GRS)](storage-redundancy-grs.md#read-access-geo-redundant-storage) (zalecane), usługi Azure Storage będą przechowywane dane trwałe w dwóch regionach (podstawowych i pomocniczych). W obu regionach usługi Azure Storage obsługuje stale wiele replik danych.

W przypadku regionalnej awarii wpływa Twojego regionu podstawowego, spróbujemy najpierw przywrócić usługę, w tym regionie. W zależności od charakteru po awarii i jej wpływ na środowisko, w niektórych sytuacjach rzadko firma Microsoft może nie można przywrócić regionu podstawowego. W tym momencie kolejności wykonamy geograficznie trybu failover. Replikacja danych między regionami jest proces asynchroniczny, które może obejmować opóźnienie, więc istnieje możliwość, że zmiany, które nie zostały jeszcze zreplikowane do regionu pomocniczego, mogą zostać utracone. Można tworzyć zapytania ["Czas ostatniej synchronizacji" konta usługi storage](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/) Aby uzyskać szczegółowe informacje o stanie replikacji.

Kilku kwestiach dotyczących środowiska pracy awaryjnej geo magazynu:

* Magazyn geograficznie pracy w trybie failover będzie wyzwalany tylko przez zespół usługi Azure Storage — użytkownik nie jest wymagana żadna akcja klienta.
* Istniejące punkty końcowe usługi magazynu, dla obiektów blob, tabel, kolejek i plików nie ulegną zmianie po włączeniu trybu failover; wpis DNS dostarczanego przez firmę Microsoft, należy zaktualizować, aby przełączyć się z regionu podstawowego do regionu pomocniczego.  Firma Microsoft przeprowadzi aktualizację, automatycznie jako część procesu pracy awaryjnej geo.
* Przed, a podczas pracy awaryjnej geo nie masz dostęp do zapisu do swojego konta magazynu ze względu na wpływ po awarii, ale nadal można odczytywać z pomocniczym w przypadku konta magazynu została skonfigurowana jako RA-GRS.
* Podczas pracy awaryjnej geo zostało ukończone i propagowane zmian systemu DNS, zostanie wznowione odczytu i zapisu do swojego konta magazynu; to wskazuje na przeznaczenie do pomocniczego punktu końcowego. 
* Należy pamiętać, że będziesz mieć dostęp do zapisu Jeśli masz GRS lub RA-GRS skonfigurowany dla konta magazynu. 
* Można tworzyć zapytania ["Ostatniej geograficznej czas pracy awaryjnej" konta magazynu](https://msdn.microsoft.com/library/azure/ee460802.aspx) Aby uzyskać więcej szczegółów.
* Po włączeniu trybu failover konto magazynu będzie można w pełni funkcjonalne, ale w stanie "obniżonej sprawności", ponieważ faktycznie znajduje się w region autonomiczny z nie możliwości replikacji geograficznej. Aby zmniejszyć to zagrożenie, firma Microsoft będzie przywrócić oryginalny regionu podstawowego i wykonaj geo-powrotu po awarii do przywrócenia stanu pierwotnego. Jeśli oryginalna region podstawowy jest nieodwracalny, firma Microsoft będzie przydzielić innego regionu pomocniczego.
  Aby uzyskać szczegółowe informacje na temat infrastruktury replikacji geograficznej usługi Azure Storage, można znaleźć w artykule na blogu magazynu zespołu o [opcji nadmiarowości i RA-GRS](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

## <a name="best-practices-for-protecting-your-data"></a>Najlepsze rozwiązania w zakresie ochrony danych
Istnieją pewne zalecanych sposobów, aby utworzyć kopię zapasową danych magazynu w regularnych odstępach czasu.

* Dyski maszyny Wirtualnej — używają [usługi Azure Backup](https://azure.microsoft.com/services/backup/) do tworzenia kopii zapasowych dysków maszyny Wirtualnej, z usługi Azure virtual machines.
* Blokowe obiekty BLOB — Włączanie [usuwania nietrwałego](../blobs/storage-blob-soft-delete.md) do ochrony przed usuwania na poziomie obiektu i zastępuje lub obiekty BLOB zostaną skopiowane do innego konta magazynu w innym regionie przy użyciu [AzCopy](storage-use-azcopy.md), [platformy Azure Program PowerShell](storage-powershell-guide-full.md), lub [Biblioteka przenoszenia danych usługi Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/).
* Tabele — używają [AzCopy](storage-use-azcopy.md) do wyeksportowania danych tabeli do innego konta magazynu w innym regionie.
* Pliki — używają [AzCopy](storage-use-azcopy.md) lub [programu Azure PowerShell](storage-powershell-guide-full.md) Aby skopiować pliki do innego konta magazynu w innym regionie.

Aby uzyskać informacje o tworzeniu aplikacji, które umożliwiają pełne wykorzystywanie zalet funkcji RA-GRS, zapoznaj [projektowanie wysoko dostępnych aplikacji przy użyciu magazynu RA-GRS](../storage-designing-ha-apps-with-ragrs.md)
