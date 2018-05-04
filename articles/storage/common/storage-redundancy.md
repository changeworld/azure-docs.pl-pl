---
title: Replikacja danych w usłudze Azure Storage | Dokumentacja firmy Microsoft
description: Dane na koncie usługi Magazyn Microsoft Azure jest replikowana na potrzeby trwałości i wysokiej dostępności. Opcje replikacji obejmują magazyn lokalnie nadmiarowy (LRS), Magazyn strefowo nadmiarowy (ZRS) magazynu geograficznie nadmiarowego (GRS) i dostęp do odczytu magazynu geograficznie nadmiarowego (RA-GRS).
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 01/21/2018
ms.author: tamram
ms.openlocfilehash: 2b105cd05ace9be6ad24d092f2b12c7ad092188e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="azure-storage-replication"></a>Replikacja usługi Azure Storage

Dane konta usługi Microsoft Azure Storage są zawsze replikowane w celu zapewnienia trwałości i wysokiej dostępności. Replikacja usługi Azure Storage kopiuje danych, dzięki czemu jest on chroniony ze zdarzeń planowanych lub nieplanowanych od przejściowych awarii sprzętu, sieci lub awarie zasilania, ogromną klęski żywiołowej i tak dalej. Można replikować dane w tym samym centrum danych w centrach danych zonal w tym samym regionie, a nawet w różnych regionach.

Replikacja gwarantuje, że Twoje konto magazynu spełnia warunki [Umowy dotyczącej poziomu usług (SLA) dla Magazynu](https://azure.microsoft.com/support/legal/sla/storage/) nawet w przypadku wystąpienia błędów. Zobacz umowę SLA, aby uzyskać informacje o gwarancjach usługi Azure Storage dotyczących trwałości i dostępności.

## <a name="choosing-a-replication-option"></a>Wybranie opcji replikacji

Podczas tworzenia konta magazynu można wybrać jedną z następujących opcji replikacji:

* [Magazyn lokalnie nadmiarowy (LRS)](storage-redundancy-lrs.md)
* [Magazyn strefowo nadmiarowy (ZRS)](storage-redundancy-zrs.md)
* [Magazyn geograficznie nadmiarowy (GRS)](storage-redundancy-grs.md)
* [Magazyn geograficznie nadmiarowy dostępny do odczytu (RA-GRS)](storage-redundancy-grs.md#read-access-geo-redundant-storage)

Poniższa tabela zapewnia szybki przegląd zakres trwałość i dostępność zapewniającego każdej strategii replikacji dla danego typu zdarzenie (lub podobny wpływ).

| Scenariusz | LRS | ZRS | GRS | RA-GRS |
|:--- |:--- |:--- |:--- |:--- |
| Brak węzła w obrębie centrum danych |Yes |Yes |Yes |Yes
| Całym centrum danych (z systemem innym niż zonal lub zonal) jest niedostępny |Nie |Yes |Yes |Yes |
| Awarii całej regionu |Nie |Nie |Yes |Yes |
| Dostęp do odczytu do danych (w regionie zdalnego, replikacją geograficzną) w przypadku niedostępności całej regionu |Nie |Nie |Nie |Yes |
| Zaprojektowana w celu zapewnienia trwałości ___ obiektów w danym roku |co najmniej 99.999999999% (11 na 9)|co najmniej 99.9999999999% (12 na 9)|co najmniej 99.99999999999999% (16 na 9)|co najmniej 99.99999999999999% (16 na 9)|
| Dostępne typy kont magazynu ___ |GPv1, GPv2, Blob |GPv2 |GPv1, GPv2, Blob |GPv1, GPv2, Blob

Zobacz [cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/) o cenach informacji na temat opcji różnych nadmiarowości.

> [!NOTE]
> Magazyn w warstwie Premium obsługuje magazyn tylko lokalnie nadmiarowy (LRS). Aby uzyskać informacje o magazynie Premium, zobacz [magazyn w warstwie Premium: magazyn o wysokiej wydajności dla obciążeń maszyny wirtualnej Azure](../../virtual-machines/windows/premium-storage.md).

## <a name="changing-replication-strategy"></a>Zmiana strategii replikacji
Firma Microsoft zezwala na zmianę strategii replikacji konta magazynu przy użyciu [portalu Azure](https://portal.azure.com/), [programu Azure Powershell](storage-powershell-guide-full.md), [interfejsu wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), lub jeden z wielu [ Biblioteki klienta usługi Azure](https://docs.microsoft.com/azure/index?view=azure-dotnet#pivot=sdkstools). Zmiana typu replikacji konta magazynu nie powoduje czas przestoju.

   > [!NOTE]
   > Obecnie nie można użyć portalu lub interfejsu API, aby przekonwertować konta ZRS. Jednak firma Microsoft planuje obsługują migrowania do ZRS LRS, GRS i RA-GRS po ZRS jest ogólnie dostępna. Zobacz [magazyn Strefowo nadmiarowy (ZRS)](storage-redundancy-zrs.md) szczegółowe informacje.
    
### <a name="are-there-any-costs-to-changing-my-accounts-replication-strategy"></a>Czy istnieją kosztów na zmieniające się strategii replikacji Moje konto?
To zależy od ścieżki konwersji. Kolejność od najtańszej z najdroższych ofertą nadmiarowość mamy LRS, ZRS, GRS i RA-GRS. Na przykład przejście *z* LRS na niczego spowoduje naliczenie dodatkowych opłat, ponieważ będą bardziej złożone poziom nadmiarowości. Przechodzenie *do* GRS lub RA-GRS spowoduje naliczenie opłat przepustowości wyjście ponieważ danych (w tym regionie podstawowym) jest replikowana do regionu pomocniczego zdalnego. Jest to jednorazowa opłat podczas początkowej instalacji. Po skopiowaniu danych nie istnieją żadne dodatkowe opłaty konwersji. Zostanie naliczona tylko replikowania wszelkie nowe lub aktualizacje do istniejących danych. Aby uzyskać więcej informacji o opłatach przepustowości, zobacz [cennik usługi Azure Storage strony](https://azure.microsoft.com/pricing/details/storage/blobs/).

Jeśli zmienisz z GRS LRS, nie ma żadnych dodatkowych kosztów, ale replikowanych danych zostanie usunięta z lokacji dodatkowej.

## <a name="see-also"></a>Zobacz także

- [Magazyn lokalnie nadmiarowy (LRS): niedrogich nadmiarowość danych usługi Azure Storage](storage-redundancy-lrs.md)
- [Magazyn strefowo nadmiarowy (ZRS): aplikacje usługi Azure Storage o wysokiej dostępności](storage-redundancy-zrs.md)
- [Magazyn geograficznie nadmiarowy (GRS): replikacji między regionalne usługi Azure Storage](storage-redundancy-grs.md)
- [Azure cele wydajności i skalowalności magazynu](storage-scalability-targets.md)
- [Projektowanie aplikacji wysokiej dostępności przy użyciu magazynu RA-GRS](../storage-designing-ha-apps-with-ragrs.md)
- [Microsoft Azure Storage nadmiarowość opcje i dostęp do odczytu z magazynu geograficznie nadmiarowego magazynu ](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
- [SOSP Paper - Azure Storage: Wysoce dostępna usługa magazynu w chmurze z wysoki poziom spójności](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
