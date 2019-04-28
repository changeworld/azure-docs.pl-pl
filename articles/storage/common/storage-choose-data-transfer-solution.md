---
title: Wybierz rozwiązanie na platformie Azure do transferu danych | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wybrać rozwiązanie na platformie Azure do transferu danych na podstawie ilości danych i dostępnej przepustowości sieci w Twoim środowisku
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 12/10/2018
ms.author: alkohli
ms.openlocfilehash: 4e2a182493b1e9de3d2ba9d586a9560e42fe0ecb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61484089"
---
# <a name="choose-an-azure-solution-for-data-transfer"></a>Wybierz rozwiązanie na platformie Azure do transferu danych

Ten artykuł zawiera omówienie niektórych typowych transferu danych na platformie Azure rozwiązań. Artykuł zawiera również linki się zalecane opcje w zależności od przepustowości sieci w Twoim środowisku oraz rozmiar danych, które zamierzasz przenieść.

## <a name="types-of-data-movement"></a>Typy przenoszenia danych

Transfer danych może być w trybie offline lub za pośrednictwem połączenia sieciowego. Wybierz swoje rozwiązanie, w zależności od usługi:

- **Rozmiar danych** — rozmiar danych przeznaczonych do transferu
- **Częstotliwość transferu** — jednorazowy lub okresowe pozyskiwania, i
- **Sieć** — przepustowość dostępną dla danych transferu w danym środowisku.

Przenoszenie danych może być następujących typów:

- **Przesyłanie w trybie offline przy użyciu gotowej do dostawy urządzeń** -urządzeń fizycznych pozwalającą należy używać celu transferu danych w trybie offline jednorazowe zbiorczego. Firma Microsoft wysyła dysku lub bezpiecznego wyspecjalizowane urządzenie. Alternatywnie można kupić i odeślij dyski własne. Kopiowanie danych na urządzeniu, a następnie odeślij ją na platformie Azure gdzie dane są przekazywane.  Dostępne opcje dla tego przypadku to dysku Data Box, urządzenia Data Box, duże pole danych i Import/Export (Użyj własne dyski).

- **Sieci transferu** — transfer danych na platformę Azure za pośrednictwem połączenia sieciowego. Można to zrobić na wiele sposobów.

    - **Interfejs graficzny** — Jeśli od czasu do czasu przesyłania kilku plików i nie trzeba zautomatyzować transfer danych, możesz wybrać narzędzia interfejsu graficznego, takiego jak Azure Storage Explorer lub narzędzia do eksploracji opartej na sieci web w witrynie Azure portal.
    - **Transfer inicjowanych przez skrypty lub programowy** — możesz użyć narzędzi zoptymalizowanych oprogramowania, firma Microsoft zapewnia lub bezpośrednio wywołać nasze interfejsy API REST/SDK. Dostępne narzędzia za pomocą skryptów są narzędzia AzCopy, programu Azure PowerShell i wiersza polecenia platformy Azure. Dla interfejsu programowego Użyj jednego z zestawów SDK dla platformy .NET, Java, Python, Node/JS, C++, Go, PHP lub Ruby.
    - **Lokalnymi urządzeniami** — Podaj możemy fizycznego lub wirtualnego urządzenia, który znajduje się w centrum danych i optymalizuje transfer danych za pośrednictwem sieci. Urządzenia te zapewniają również lokalnej pamięci podręcznej często używanych plików. Urządzenie fizyczne jest krawędź pola danych i urządzenie wirtualne jest bramy pola danych. Uruchamianie trwale w środowisku lokalnym i łączenie z platformą Azure za pośrednictwem sieci.
    - **Potok danych zarządzanych** — możesz skonfigurować potok chmury regularnie transferowania plików między kilka usług platformy Azure, lokalnie lub kombinację dwóch. Usługi Azure Data Factory umożliwia konfigurowanie i zarządzanie potoków danych oraz przenoszenia i przekształcania danych na potrzeby analizy.

Poniższa wizualizacja przedstawia wytyczne do wyboru różnych transferu danych systemu Azure tools, w zależności od przepustowość sieci dostępną dla transferu, rozmiar danych przeznaczonych do transferowania i częstotliwość transferu.

![Narzędzia dotyczące transferu danych systemu Azure](media/storage-choose-data-transfer-solution/azure-data-transfer-options-3.png)

**Górne limity urządzenia w trybie offline transfer - dysku Data Box, urządzenia Data Box i duże pole danych można rozszerzyć, umieszczając wiele zamówień typu urządzenia.*

## <a name="selecting-a-data-transfer-solution"></a>Wybranie rozwiązania transferu danych

Należy odpowiedzieć na następujące pytania, aby ułatwić wybór rozwiązania transferu danych:

- Jest dostępną przepustowość sieci ograniczony lub nie istnieje, i chcesz przenieść duże zestawy danych?
  
    Jeśli tak, zobacz: [Scenariusz 1: Transfer dużych zestawów danych bez lub niskiej przepustowości połączenia sieciowego](storage-solution-large-dataset-low-network.md).
- Czy chcesz przenieść dużych zestawów danych za pośrednictwem sieci i ma umiarkowany do Wysoka przepustowość sieci?

    Jeśli tak, zobacz: [Scenariusz 2: Przesyłanie dużych zestawów danych przy użyciu umiarkowany do Wysoka przepustowość sieci](storage-solution-large-dataset-moderate-high-network.md).
- Czy chcesz co pewien czas transferu kilka plików za pośrednictwem sieci?

    Jeśli tak, zobacz [Scenariusz 3: Przenoszenie małych zestawów danych za pomocą ograniczonego do Średnia przepustowość sieci](storage-solution-small-dataset-low-moderate-network.md).
- Szukasz transfer danych w momencie w regularnych odstępach czasu?

    Jeśli tak, użyj opcji inicjowanych przez skrypty programowe opisane w temacie [Scenariusz 4: Okresowe transfery](storage-solution-periodic-data-transfer.md).
- Szukasz transfer danych w toku, zapewniająca ciągłą?

    Jeśli tak, użyj opcji w [Scenariusz 4: Okresowe transfery](storage-solution-periodic-data-transfer.md).

## <a name="next-steps"></a>Kolejne kroki

- [Wprowadzenie do Eksploratora usługi Azure Storage](https://azure.microsoft.com/resources/videos/introduction-to-microsoft-azure-storage-explorer/).
- [Przeczytaj Omówienie narzędzia azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10).
- [Używanie programu Azure PowerShell z usługą Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-powershell-guide-full)
- [Użyj wiersza polecenia platformy Azure z usługą Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-azure-cli)
- Dowiedz się więcej:

    - [Usługa Azure Data Box, dysku Azure Data Box i Azure dane pole dużych transferów w trybie offline](https://docs.microsoft.com/azure/databox/).
    - [Azure brama pola danych i krawędź pola danych platformy Azure w przypadku transferów online](https://docs.microsoft.com/azure/databox-online/).
- [Aby dowiedzieć się o usłudze Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview).
- Przesyłanie danych za pomocą interfejsów API REST

    - [Na platformie .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
    - [W języku Java](https://docs.microsoft.com/java/api/overview/azure/storage/client)
