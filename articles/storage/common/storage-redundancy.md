---
title: Nadmiarowość danych w usłudze Azure Storage | Microsoft Docs
description: Dane na koncie Microsoft Azure Storage są replikowane w celu zapewnienia trwałości i wysokiej dostępności. Opcje nadmiarowości obejmują Magazyn lokalnie nadmiarowy (LRS), magazyn strefowo nadmiarowy (ZRS), magazyn Geograficznie nadmiarowy (GRS), magazyn Geograficznie nadmiarowy do odczytu (RA-GRS), magazyn Geograficznie nadmiarowy (GZRS) (wersja zapoznawcza) i dostęp do odczytu strefy geograficznej — nadmiarowy Storage (RA-GZRS) (wersja zapoznawcza).
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 8025228275afeb3f23268db759eb7659b9887132
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2019
ms.locfileid: "71670784"
---
# <a name="azure-storage-redundancy"></a>Nadmiarowość usługi Azure Storage

Dane konta usługi Microsoft Azure Storage są zawsze replikowane w celu zapewnienia trwałości i wysokiej dostępności. Usługa Azure Storage kopiuje dane tak, aby były chronione przed planowanymi i nieplanowanymi zdarzeniami, w tym przejściowymi awariami sprzętowymi, siecią lub przestojem, a także ogromnymi katastrofami naturalnymi. Dane można replikować w tym samym centrum danych, w różnych centrach danych w tym samym regionie lub w regionach geograficznie rozdzielonych.

Replikacja gwarantuje, że Twoje konto magazynu spełnia warunki [Umowy dotyczącej poziomu usług (SLA) dla Magazynu](https://azure.microsoft.com/support/legal/sla/storage/) nawet w przypadku wystąpienia błędów. Zobacz umowę SLA, aby uzyskać informacje o gwarancjach usługi Azure Storage dotyczących trwałości i dostępności.

Usługa Azure Storage regularnie weryfikuje integralność danych przechowywanych przy użyciu cyklicznych testów nadmiarowości (CRCs). Jeśli wykryto uszkodzenie danych, zostanie ono naprawione przy użyciu nadmiarowych danych. Usługa Azure Storage oblicza również sumy kontrolne dla całego ruchu sieciowego w celu wykrycia uszkodzenia pakietów danych podczas przechowywania lub pobierania danych.

## <a name="choosing-a-redundancy-option"></a>Wybieranie opcji nadmiarowości

Podczas tworzenia konta magazynu można wybrać jedną z następujących opcji nadmiarowości:

[!INCLUDE [azure-storage-redundancy](../../../includes/azure-storage-redundancy.md)]

Poniższa tabela zawiera krótkie omówienie zakresu trwałości i dostępności poszczególnych strategii replikacji dla danego typu zdarzenia (lub zdarzenia podobnego wpływu).

| Scenariusz                                                                                                 | LRS                             | ZRS                              | GRS/RA-GRS                                  | GZRS/RA-GZRS (wersja zapoznawcza)                              |
| :------------------------------------------------------------------------------------------------------- | :------------------------------ | :------------------------------- | :----------------------------------- | :----------------------------------- |
| Niedostępność węzła w centrum danych                                                                 | Tak                             | Yes                              | Yes                                  | Tak                                  |
| Całe centrum danych (zona lub non-Zona) staną się niedostępne                                           | Nie                              | Yes                              | Yes                                  | Tak                                  |
| Awaria całego regionu                                                                                     | Nie                              | Nie                               | Yes                                  | Tak                                  |
| Dostęp do odczytu do danych (w zdalnym, replikowanym regionie geograficznie) w przypadku niedostępności całego regionu | Nie                              | Nie                               | Tak (z RA-GRS)                                   | Tak (z RA-GZRS)                                 |
| Zaprojektowano \_ w celu zapewnienia \_ trwałości obiektów w danym roku.                                          | co najmniej 99,999999999% (11 9) | co najmniej 99,9999999999% (12 9) | co najmniej 99.99999999999999% (16 9) | co najmniej 99.99999999999999% (16 9) |
| Obsługiwane typy kont magazynu                                                                   | GPv2, GPv1, BLOB                | GPv2                             | GPv2, GPv1, BLOB                     | GPv2                     |
| Umowa SLA dotycząca dostępności dla żądań odczytu | Co najmniej 99,9% (99% dla warstwy dostępu chłodnego) | Co najmniej 99,9% (99% dla warstwy dostępu chłodnego) | Co najmniej 99,9% (99% dla warstwy dostępu chłodnego) dla GRS<br /><br />Co najmniej 99,99% (99,9% dla warstwy dostępu chłodnego) dla usługi RA-GRS | Co najmniej 99,9% (99% dla warstwy dostępu chłodnego) dla GZRS<br /><br />Co najmniej 99,99% (99,9% dla warstwy dostępu chłodnego) dla usługi RA-GZRS |
| Umowa SLA dotycząca dostępności dla żądań zapisu | Co najmniej 99,9% (99% dla warstwy dostępu chłodnego) | Co najmniej 99,9% (99% dla warstwy dostępu chłodnego) | Co najmniej 99,9% (99% dla warstwy dostępu chłodnego) | Co najmniej 99,9% (99% dla warstwy dostępu chłodnego) |

Wszystkie dane na koncie magazynu są replikowane, w tym blokowe obiekty blob i dołączanie obiektów blob, stronicowych obiektów blob, kolejek, tabel i plików. Wszystkie typy kont magazynu są replikowane, chociaż ZRS wymaga konta magazynu ogólnego przeznaczenia w wersji 2.

Aby uzyskać informacje o cenach dla każdej opcji nadmiarowości, zobacz [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/). 

Aby uzyskać informacje na temat gwarancji usługi Azure Storage w zakresie trwałości i dostępności, zobacz umowę SLA dotyczącą [usługi Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/).

> [!NOTE]
> Usługa Azure Premium Storage obecnie obsługuje tylko Magazyn lokalnie nadmiarowy (LRS).

## <a name="changing-replication-strategy"></a>Zmiana strategii replikacji

Strategię replikacji konta magazynu można zmienić przy użyciu [Azure Portal](https://portal.azure.com/), [programu Azure PowerShell](storage-powershell-guide-full.md), [interfejsu wiersza polecenia platformy](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)Azure lub jednej z [bibliotek klienckich usługi Azure Storage](https://docs.microsoft.com/azure/index#pivot=sdkstools). Zmiana typu replikacji konta magazynu nie spowoduje wyłączenia.

> [!NOTE]
> Obecnie nie można użyć Azure Portal lub bibliotek klienckich usługi Azure Storage do przekonwertowania Twojego konta na ZRS, GZRS lub RA-GZRS. Aby przeprowadzić migrację konta do ZRS, zobacz [Magazyn strefowo nadmiarowy (ZRS) służący do tworzenia aplikacji usługi Azure Storage o wysokiej](storage-redundancy-zrs.md) dostępności w celu uzyskania szczegółowych informacji. Aby przeprowadzić migrację GZRS lub RA-GZRS, zobacz [Geograficznie nadmiarowy magazyn w celu zapewnienia wysokiej dostępności i maksymalnej trwałości (wersja zapoznawcza)](storage-redundancy-zrs.md) , aby uzyskać szczegółowe informacje.

### <a name="are-there-any-costs-to-changing-my-accounts-replication-strategy"></a>Czy istnieją jakiekolwiek koszty zmiany strategii replikacji mojego konta?

Jest to zależne od ścieżki konwersji. Określanie kolejności od najwyższego do najtańszych ofert nadmiarowości usługi Azure Storage LRS, ZRS, GRS, RA-GRS, GZRS i RA-GZRS. Na przykład przechodzenie *z* LRS do dowolnego innego typu replikacji wiąże się z dodatkowymi opłatami, ponieważ przenosisz do bardziej zaawansowanego poziomu nadmiarowości. Migrowanie *do* GRS lub RA-GRS spowoduje naliczenie opłaty za przepustowość ruchu wychodzącego, ponieważ dane (w regionie podstawowym) są replikowane do zdalnego regionu pomocniczego. Ta opłata jest naliczana jednorazowo podczas początkowej konfiguracji. Po skopiowaniu danych nie ma żadnych dodatkowych opłat związanych z migracją. Opłata jest naliczana tylko za replikowanie nowych lub aktualizacji istniejących danych. Aby uzyskać szczegółowe informacje o opłatach za przepustowość, zobacz [stronę z cennikiem usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

Jeśli przeprowadzisz migrację konta magazynu z usługi GRS do LRS, nie ma żadnych dodatkowych kosztów, ale zreplikowane dane są usuwane z lokalizacji pomocniczej.

Jeśli przeprowadzono migrację konta magazynu z usługi RA-GRS do usługi GRS lub LRS, to konto jest rozliczane jako RA-GRS przez dodatkowe 30 dni poza datą przekonwertowania.

## <a name="see-also"></a>Zobacz także

- [Magazyn lokalnie nadmiarowy (LRS): Niski koszt nadmiarowości danych dla usługi Azure Storage](storage-redundancy-lrs.md)
- [Magazyn strefowo nadmiarowy (ZRS): Aplikacje usługi Azure Storage o wysokiej dostępności](storage-redundancy-zrs.md)
- [Magazyn Geograficznie nadmiarowy (GRS): Replikacja między regionami w ramach usługi Azure Storage](storage-redundancy-grs.md)
- [Magazyn Geograficznie nadmiarowy (GZRS) w celu zapewnienia wysokiej dostępności i maksymalnej trwałości (wersja zapoznawcza)](storage-redundancy-gzrs.md)
- [Azure Storage scalability and performance targets](storage-scalability-targets.md) (Cele dotyczące skalowalności i wydajności usługi Azure Storage)
- [Projektowanie aplikacji o wysokiej dostępności przy użyciu magazynu RA-GRS](../storage-designing-ha-apps-with-ragrs.md)
- [Microsoft Azure Storage opcje nadmiarowości i dostęp do odczytu geograficznie nadmiarowego magazynu](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
- [Papier SOSP — Azure Storage: Usługa magazynu w chmurze o wysokiej dostępności z silną spójnością](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
