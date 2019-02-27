---
title: Nadmiarowość danych w usłudze Azure Storage | Dokumentacja firmy Microsoft
description: Dane na Twoim koncie usługi Microsoft Azure Storage są replikowane w celu zapewnienia trwałości i wysokiej dostępności. Opcje nadmiarowości obejmują magazyn lokalnie nadmiarowy (LRS), Magazyn strefowo nadmiarowy (ZRS), Magazyn geograficznie nadmiarowy (GRS) i dostęp do odczytu magazynu geograficznie nadmiarowego (RA-GRS).
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 01/18/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: cbe8970e3e7b924025a93b50f9b8c9e46643b349
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2019
ms.locfileid: "56872050"
---
# <a name="azure-storage-redundancy"></a>Nadmiarowość magazynu platformy Azure

Dane konta usługi Microsoft Azure Storage są zawsze replikowane w celu zapewnienia trwałości i wysokiej dostępności. Usługa Azure Storage dane są kopiowane, aby jest chroniony z planowanych i nieplanowanych zdarzeń wraz ze przejściowych awarii sprzętu, sieci lub awarii zasilania i ogromnych klęskami żywiołowymi. Można replikować dane w tym samym centrum danych, w centrach danych strefowych, w tym samym regionie lub w regionach podstawowemu.

Replikacja gwarantuje, że Twoje konto magazynu spełnia warunki [Umowy dotyczącej poziomu usług (SLA) dla Magazynu](https://azure.microsoft.com/support/legal/sla/storage/) nawet w przypadku wystąpienia błędów. Zobacz umowę SLA, aby uzyskać informacje o gwarancjach usługi Azure Storage dotyczących trwałości i dostępności.

## <a name="choosing-a-redundancy-option"></a>Wybranie opcji nadmiarowości

Podczas tworzenia konta magazynu można wybrać jedną z następujących opcji nadmiarowości:

* [Magazyn lokalnie nadmiarowy (LRS)](storage-redundancy-lrs.md)
* [Magazyn strefowo nadmiarowy (ZRS)](storage-redundancy-zrs.md)
* [Magazyn geograficznie nadmiarowy (GRS)](storage-redundancy-grs.md)
* [Magazyn geograficznie nadmiarowy dostępny do odczytu (RA-GRS)](storage-redundancy-grs.md#read-access-geo-redundant-storage)

Poniższa tabela zawiera krótkie omówienie zakres trwałości i dostępności, która poszczególnych strategii replikacji zapewni dla danego typu zdarzenie (lub podobny wpływ na działalność).

| Scenariusz                                                                                                 | LRS                             | ZRS                              | GRS                                  | RA-GRS                               |
| :------------------------------------------------------------------------------------------------------- | :------------------------------ | :------------------------------- | :----------------------------------- | :----------------------------------- |
| Niedostępność węzła w centrum danych                                                                 | Yes                             | Yes                              | Yes                                  | Yes                                  |
| Całego centrum danych (strefowych lub strefowych) staje się niedostępny                                           | Nie                              | Yes                              | Yes                                  | Yes                                  |
| Awaria obejmujących cały region                                                                                     | Nie                              | Nie                               | Yes                                  | Yes                                  |
| Dostęp do odczytu do danych (w zdalnym, replikowany geograficznie region) w przypadku niedostępności obejmujących cały region | Nie                              | Nie                               | Nie                                   | Yes                                  |
| Zaprojektowana w celu zapewnienia \_ \_ trwałości obiektów w danym roku                                          | co najmniej 99,999999999% (11 9) | co najmniej 99,9999999999% (12 9) | co najmniej 99,99999999999999% (16 9) | co najmniej 99,99999999999999% (16 9) |
| Typy kont magazynu obsługiwanych                                                                   | Konta GPv1, GPv2 obiektów Blob                | GPv2                             | Konta GPv1, GPv2 obiektów Blob                     | Konta GPv1, GPv2 obiektów Blob                     |
| Umowa SLA dotycząca dostępności dla żądań odczytu | Co najmniej 99,9% (99% w przypadku warstwy dostępu chłodnego) | Co najmniej 99,9% (99% w przypadku warstwy dostępu chłodnego) | Co najmniej 99,9% (99% w przypadku warstwy dostępu chłodnego) | Co najmniej 99,99% (99,9% w przypadku warstwy dostępu chłodnego) |
| Umowa SLA dotycząca dostępności dla żądań zapisu | Co najmniej 99,9% (99% w przypadku warstwy dostępu chłodnego) | Co najmniej 99,9% (99% w przypadku warstwy dostępu chłodnego) | Co najmniej 99,9% (99% w przypadku warstwy dostępu chłodnego) | Co najmniej 99,9% (99% w przypadku warstwy dostępu chłodnego) |

Aby uzyskać informacje dla każdej opcji nadmiarowości, zobacz [cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/). 

Aby uzyskać informacje o usłudze Azure Storage gwarantuje potrzeby trwałości i dostępności, zobacz [umowę SLA usługi Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/).

> [!NOTE]
> Usługa Premium Storage obsługuje tylko lokalnie nadmiarowym (LRS).

## <a name="changing-replication-strategy"></a>Zmiana strategię replikacji
Firma Microsoft zezwala na zmianę strategii replikacji konta magazynu przy użyciu [witryny Azure portal](https://portal.azure.com/), [programu Azure Powershell](storage-powershell-guide-full.md), [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), lub jeden z wielu [ Biblioteki klienckie systemu Azure](https://docs.microsoft.com/azure/index?view=azure-dotnet#pivot=sdkstools). Zmiana typu replikacji konta magazynu nie skutkuje czas przestoju.

   > [!NOTE]
   > Aby przekonwertować konta magazynu ZRS nie można obecnie używać interfejsu API lub portalu. Jeśli chcesz przekonwertować replikacji konta magazynu ZRS, zobacz [magazyn strefowo nadmiarowy (ZRS)](storage-redundancy-zrs.md) Aby uzyskać szczegółowe informacje.
    
### <a name="are-there-any-costs-to-changing-my-accounts-replication-strategy"></a>Czy istnieją żadnych kosztów na zmieniające się strategię replikacji mojego konta?
To zależy od ścieżki konwersji. Kolejność z najtańszej najbardziej kosztowne oferty nadmiarowości mamy LRS, ZRS, GRS i RA-GRS. Na przykład, przechodząc *z* LRS miejscem spowodują Naliczanie dodatkowych opłat, ponieważ ma być bardziej zaawansowanych poziom nadmiarowości. Przechodząc *do* GRS lub RA-GRS zostaną naliczone opłaty przepustowość ruchu wychodzącego, ponieważ danych (w Twoim regionie podstawowym) jest replikowana do zdalnego regionu pomocniczego. Jest to jednorazowa opłata podczas początkowej instalacji. Po skopiowaniu danych nie będą naliczane dodatkowe opłaty konwersji. Możesz tylko naliczana opłata replikowanie dowolnego nowego lub aktualizacje istniejących danych. Szczegółowe informacje na temat opłatami za przepustowość, [stronę cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

Jeśli zmienisz z GRS LRS, nie ma żadnych dodatkowych kosztów, ale replikowane dane są usuwane z lokalizacji pomocniczej.

## <a name="see-also"></a>Zobacz także

- [Magazyn lokalnie nadmiarowy (LRS): Nadmiarowość danych niedrogiej usługi Azure Storage](storage-redundancy-lrs.md)
- [Magazyn strefowo nadmiarowy (ZRS): Aplikacje usługi Azure Storage o wysokiej dostępności](storage-redundancy-zrs.md)
- [Magazyn geograficznie nadmiarowy (GRS): Replikacji między regionami dla usługi Azure Storage](storage-redundancy-grs.md)
- [Azure Storage scalability and performance targets](storage-scalability-targets.md) (Cele dotyczące skalowalności i wydajności usługi Azure Storage)
- [Projektowanie wysoko dostępnych aplikacji przy użyciu magazynu RA-GRS](../storage-designing-ha-apps-with-ragrs.md)
- [Usługa Microsoft Azure Storage nadmiarowości opcje i dostęp do odczytu magazyn geograficznie nadmiarowy](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
- [Dokument SOSP — usługa Azure Storage: Usługi magazynu w chmurze o wysokiej dostępności przy użyciu silnej spójności](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
