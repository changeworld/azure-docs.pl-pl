---
title: Blokowanie warstw wydajności magazynu obiektów blob — usługa Azure Storage
description: W tym artykule omówiono różnicę między warstwami wydajności premium i standardu dla magazynu obiektów blob bloku platformy Azure.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/12/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: ff82986b27d038c536872b07e1308b0d48fadaef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74270223"
---
# <a name="performance-tiers-for-block-blob-storage"></a>Warstwy wydajności dla magazynu obiektów blob

Ponieważ przedsiębiorstwa wdrażają aplikacje natywne dla chmury uwzględniające wydajność, ważne jest, aby mieć opcje ekonomicznego przechowywania danych na różnych poziomach wydajności.

Magazyn obiektów blob bloku platformy Azure oferuje dwie różne warstwy wydajności:

- **Premium:** zoptymalizowany pod kątem wysokich szybkości transakcji i jednocyfrowego stałego opóźnienia pamięci masowej
- **Standard:** zoptymalizowany pod kątem dużej wydajności i wysokiej przepustowości

Następujące zagadnienia dotyczą różnych warstw wydajności:

| Obszar |Standardowa wydajność  |Najwyższa wydajność  |
|---------|---------|---------|
|Dostępność w danym regionie     |   Wszystkie regiony      | W [wybranych regionach](https://azure.microsoft.com/global-infrastructure/services/?products=storage)       |
|Obsługiwane [typy kont magazynu](../common/storage-account-overview.md#types-of-storage-accounts)     |     Ogólnego przeznaczenia v2, BlobStorage, Ogólnego przeznaczenia v1    |    BlockBlobStorage     |
|Obsługuje [obiekty blob bloków o wysokiej przepływie](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/)     |    Tak, przy rozmiarach większych niż 4 MiB PutBlock lub PutBlob     |    Tak, przy rozmiarach powyżej 256 KiB PutBlock lub PutBlob    |
|Nadmiarowość     |     Zobacz [Typy kont magazynu](../common/storage-account-overview.md#types-of-storage-accounts)   |  Obecnie obsługuje tylko pamięć masową o nadmiarowej pojemności lokalnej (LRS) i magazynowanie redudant strefy (ZRS)<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>1</sup></div>     |

<div id="zone-redundant-storage"><sup>1.</sup> Magazyn nadmiarowy strefowy (ZRS) jest dostępny w wybranych regionach dla kont magazynu obiektów blob bloków bloków obiektów blob o wysokiej wydajności.</div>

Jeśli chodzi o koszty, wydajność w wersji premium zapewnia zoptymalizowane ceny dla aplikacji o wysokich stawkach transakcji, aby [obniżyć całkowity koszt magazynowania](https://azure.microsoft.com/blog/reducing-overall-storage-costs-with-azure-premium-blob-storage/) dla tych obciążeń.

## <a name="premium-performance"></a>Najwyższa wydajność

Magazyn bloków obiektów blob o wysokiej wydajności udostępnia dane za pośrednictwem sprzętu o wysokiej wydajności. Dane są przechowywane na dyskach PÓŁPRZEWODNIKOWYCH (SSD), które są zoptymalizowane pod kątem małych opóźnień. Dyski SSD zapewniają wyższą przepustowość w porównaniu z tradycyjnymi dyskami twardymi.

Pamięć masowa o wysokiej wydajności jest idealna dla obciążeń, które wymagają szybkiego i spójnego czasu reakcji. Najlepiej jest dla obciążeń, które wykonują wiele małych transakcji. Przykładowe obciążenia obejmują:

- **Obciążenia interaktywne**. Te obciążenia wymagają natychmiastowych aktualizacji i opinii użytkowników, takich jak e-commerce i aplikacje mapowania. Na przykład w aplikacji handlu elektronicznego rzadziej wyświetlane elementy prawdopodobnie nie są buforowane. Jednak muszą one być natychmiast wyświetlane klientowi na żądanie.

- **Analityka**. W scenariuszu IoT wiele mniejszych operacji zapisu może być wypychane do chmury co sekundę. Duże ilości danych mogą być pobierane, agregowane do celów analizy, a następnie usuwane niemal natychmiast. Możliwości wysokiego pozyskiwania magazynu obiektów blob bloku klasy premium sprawiają, że jest to efektywne dla tego typu obciążenia.

- **Sztuczna inteligencja/uczenie maszynowe (AI/ML)**. AI/ML zajmuje się zużyciem i przetwarzaniem różnych typów danych, takich jak wizualizacje, mowa i tekst. Ten wysokowydajny typ obciążenia obliczeniowego zajmuje się dużą ilością danych, które wymagają szybkiej reakcji i efektywnego czasu pozyskiwania danych.

- **Transformacja danych**. Procesy wymagające ciągłej edycji, modyfikacji i konwersji danych wymagają natychmiastowych aktualizacji. Aby uzyskać dokładną reprezentację danych, konsumenci tych danych muszą natychmiast zobaczyć te zmiany odzwierciedlone.

## <a name="standard-performance"></a>Standardowa wydajność

Standardowa wydajność obsługuje różne [warstwy dostępu](storage-blob-storage-tiers.md) do przechowywania danych w najbardziej opłacalny sposób. Jest zoptymalizowany pod kątem dużej pojemności i dużej przepustowości w dużych zestawach danych.

- **Zestawy danych odzyskiwania kopii zapasowych i odzyskiwania po awarii**. Standardowa wydajność pamięci masowej oferuje ekonomiczne warstwy, dzięki czemu jest idealnym przykładem użycia zarówno dla krótkoterminowych, jak i długoterminowych zestawów danych odzyskiwania po awarii, pomocniczych kopii zapasowych i archiwizacji danych zgodności.

- **Zawartość multimedialna**. Obrazy i filmy są często dostępne podczas pierwszego tworzenia i przechowywania, ale ten typ zawartości jest używany rzadziej w miarę starzenia się. Standardowa pamięć masowa zapewnia odpowiednie warstwy dostosowane do potrzeb zawartości multimedialnej. 

- **Masowe przetwarzanie danych**. Te rodzaje obciążeń są odpowiednie dla magazynu standardowego, ponieważ wymagają ekonomicznego magazynu o wysokiej przepływności zamiast spójnego niskiego opóźnienia. Duże, nieprzetworzone zestawy danych są przemieszczane do przetwarzania i ostatecznie migrują do warstw chłodniejszych.

## <a name="migrate-from-standard-to-premium"></a>Migracja ze standardu do premii

Nie można przekonwertować istniejącego konta magazynu o standardowej wydajności na konto magazynu obiektów blob blokowego o wydajności w wersji premium. Aby przeprowadzić migrację na konto magazynu wydajności premium, należy utworzyć konto BlockBlobStorage i przenieść dane na nowe konto. Aby uzyskać więcej informacji, zobacz [Tworzenie konta BlockBlobStorage](storage-blob-create-account-block-blob.md).

Aby skopiować obiekty blob między kontami magazynu, można użyć najnowszej wersji narzędzia wiersza polecenia [AzCopy.](../common/storage-use-azcopy-blobs.md) Inne narzędzia, takie jak usługa Azure Data Factory, są również dostępne do przenoszenia i przekształcania danych.

## <a name="blob-lifecycle-management"></a>Zarządzanie cyklem życia obiektów blob

Zarządzanie cyklem życia magazynu obiektów blob oferuje bogatą zasadę opartą na regułach:

- **Premium**: Wygasają dane po zakończeniu cyklu życia.
- **Standard**: Przejście danych do najlepszej warstwy dostępu i wygaśnie dane po zakończeniu cyklu życia.

Aby dowiedzieć się więcej, zobacz [Zarządzanie cyklem życia magazynu obiektów Blob platformy Azure](storage-lifecycle-management-concepts.md).

Nie można przenosić danych przechowywanych na koncie magazynu obiektów blob bloku premium między warstwami gorąca, chłodna i archiwizowana. Można jednak kopiować obiekty BLOB z konta magazynu obiektów blob do warstwy dostępu gorąca na *innym* koncie. Aby skopiować dane na inne konto, użyj interfejsu API [Umieść blok z adresu URL](/rest/api/storageservices/put-block-from-url) lub [AzCopy w wersji 10](../common/storage-use-azcopy-v10.md). Interfejs API **adresu URL Umieść blok od** synchronicznie kopiuje dane na serwerze. Wywołanie kończy się dopiero po przeniesieniu wszystkich danych z oryginalnej lokalizacji serwera do lokalizacji docelowej.

## <a name="next-steps"></a>Następne kroki

Oceniaj gorące, fajne i archiwizowane konta magazynu GPv2 i obiektów Blob.

- [Dowiedz się więcej o nawadnianiu danych obiektów blob z warstwy archiwum](storage-blob-rehydration.md)
- [Ocena użycia bieżących kont magazynu przez włączenie metryk usługi Azure Storage](../common/storage-enable-and-view-metrics.md)
- [Sprawdzanie cen gorących, chłodnych i archiwalnych w magazynach obiektów Blob i kontach GPv2 według regionów](https://azure.microsoft.com/pricing/details/storage/)
- [Sprawdzanie ceny transferu danych](https://azure.microsoft.com/pricing/details/data-transfers/)
