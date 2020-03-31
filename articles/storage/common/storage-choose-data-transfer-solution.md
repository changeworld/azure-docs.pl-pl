---
title: Wybierz rozwiązanie platformy Azure do przesyłania danych| Dokumenty firmy Microsoft
description: Dowiedz się, jak wybrać rozwiązanie platformy Azure do przesyłania danych na podstawie rozmiarów danych i dostępnej przepustowości sieci w danym środowisku
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: ada96d0d4b167e30cb6e271aa02d0d399a9ae7d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303090"
---
# <a name="choose-an-azure-solution-for-data-transfer"></a>Wybieranie rozwiązania platformy Azure do transferu danych

Ten artykuł zawiera omówienie niektórych typowych rozwiązań transferu danych platformy Azure. W tym artykule znajdują się również łącza do zalecanych opcji w zależności od przepustowości sieci w danym środowisku i rozmiaru danych, które mają zostać przesłane.

## <a name="types-of-data-movement"></a>Rodzaje przenoszenia danych

Przesyłanie danych może być w trybie offline lub za pośrednictwem połączenia sieciowego. Wybierz rozwiązanie w zależności od:

- **Rozmiar danych** — rozmiar danych przeznaczonych do przesyłania,
- **Częstotliwość transferu** - jednorazowe lub okresowe połkowanie danych oraz
- **Sieć** — przepustowość dostępna do przesyłania danych w danym środowisku.

Przenoszenie danych może być następujących typów:

- **Transfer offline przy użyciu urządzeń dostarczanych** — używaj fizycznych urządzeń dostarczanych, gdy chcesz wykonywać jednorazowy transfer danych zbiorczych w trybie offline. Firma Microsoft wysyła dysk lub bezpieczne specjalistyczne urządzenie. Alternatywnie można kupić i wysłać własne dyski. Skopiuj dane na urządzenie, a następnie wyślij je na platformę Azure, gdzie dane są przekazywane.  Dostępne opcje dla tej sprawy to Data Box Disk, Data Box, Data Box Heavy i Import/Export (użyj własnych dysków).

- **Transfer sieciowy** — przesyłasz dane na platformę Azure za pośrednictwem połączenia sieciowego. Można to zrobić na wiele sposobów.

    - **Interfejs graficzny** — jeśli od czasu do czasu przesyłasz tylko kilka plików i nie musisz zautomatyzować transferu danych, możesz wybrać narzędzie interfejsu graficznego, takie jak Azure Storage Explorer lub narzędzie do eksploracji opartej na sieci Web w witrynie Azure portal.
    - **Przesyłanie skryptów lub programowe** — możesz użyć zoptymalizowanych narzędzi programowych, które dostarczamy lub bezpośrednio wywoływać nasze interfejsy API/SDK REST. Dostępne narzędzia do skryptów to AzCopy, Azure PowerShell i Azure CLI. W przypadku interfejsu programowego użyj jednego z SDK dla .NET, Java, Python, Node/JS, C++, Go, PHP lub Ruby.
    - **Urządzenia lokalne** — dostarczamy urządzenie fizyczne lub wirtualne, które znajduje się w centrum danych i optymalizuje transfer danych za pośrednictwem sieci. Urządzenia te zapewniają również lokalną pamięć podręczną często używanych plików. Urządzeniem fizycznym jest data box edge, a urządzeniem wirtualnym jest brama pola danych. Oba działają na stałe w twoim lokalu i łączą się z platformą Azure za pośrednictwem sieci.
    - **Potok danych zarządzanych** — można skonfigurować potok w chmurze, aby regularnie przesyłać pliki między kilkoma usługami platformy Azure, lokalnie lub kombinacją dwóch. Użyj usługi Azure Data Factory, aby skonfigurować potoki danych i zarządzać nimi, a także przenosić i przekształcać dane do analizy.

Poniższa wizualizacja ilustruje wskazówki dotyczące wyboru różnych narzędzi transferu danych platformy Azure w zależności od przepustowości sieci dostępnej do przesyłania, rozmiaru danych przeznaczonego do transferu i częstotliwości transferu.

![Narzędzia do przesyłania danych platformy Azure](media/storage-choose-data-transfer-solution/azure-data-transfer-options-3.png)

**Górne limity urządzeń transferu w trybie offline — Dysk pola danych, Data Box i Data Box Heavy można rozszerzyć, składając wiele zamówień typu urządzenia.*

## <a name="selecting-a-data-transfer-solution"></a>Wybieranie rozwiązania do przesyłania danych

Odpowiedz na następujące pytania, aby ułatwić wybór rozwiązania do transferu danych:

- Czy dostępna przepustowość sieci jest ograniczona lub nie istnieje i chcesz przesyłać duże zestawy danych?
  
    Jeśli tak, zobacz: [Scenariusz 1: Przenoszenie dużych zestawów danych bez przepustowości sieci lub przy niskiej przepustowości sieci](storage-solution-large-dataset-low-network.md).
- Czy chcesz przesyłać duże zestawy danych przez sieć i masz umiarkowaną lub wysoką przepustowość sieci?

    Jeśli tak, zobacz: [Scenariusz 2: Przenoszenie dużych zestawów danych o umiarkowanej do wysokiej przepustowości sieci](storage-solution-large-dataset-moderate-high-network.md).
- Czy chcesz od czasu do czasu przesłać tylko kilka plików przez sieć?

    Jeśli tak, zobacz [Scenariusz 3: Przesyłanie małych zestawów danych o ograniczonej do umiarkowanej przepustowości sieci](storage-solution-small-dataset-low-moderate-network.md).
- Szukasz transferu danych w czasie w regularnych odstępach czasu?

    Jeśli tak, użyj opcji skryptowych/programowych opisanych w [scenariuszu 4: Okresowe przesyłanie danych](storage-solution-periodic-data-transfer.md).
- Szukasz ciągłego, ciągłego transferu danych?

    Jeśli tak, użyj opcji w [scenariuszu 4: Okresowe przesyłanie danych](storage-solution-periodic-data-transfer.md).

## <a name="data-transfer-feature-in-azure-portal"></a>Funkcja transferu danych w witrynie Azure portal

Możesz również przejść do konta usługi Azure Storage w witrynie Azure portal i wybrać funkcję **transferu danych.** Podaj przepustowość sieci w danym środowisku, rozmiar danych, które chcesz przesłać, oraz częstotliwość transferu danych. Zobaczysz optymalne rozwiązania transferu danych odpowiadające podanym informacjom. 

## <a name="next-steps"></a>Następne kroki

- [Pobierz wprowadzenie do Eksploratora usługi Azure Storage](https://azure.microsoft.com/resources/videos/introduction-to-microsoft-azure-storage-explorer/).
- [Przeczytaj przegląd AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10).
- [Korzystanie z programu Azure PowerShell z usługą Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-powershell-guide-full)
- [Szybki start: tworzenie, pobieranie i wystawianie obiektów blob za pomocą interfejsu wiersza polecenia platformy Azure](../blobs/storage-quickstart-blobs-cli.md)
- Dowiedz się więcej:

    - [Azure Data Box, Azure Data Box Disk i Azure Data Box Heavy dla transferów w trybie offline.](https://docs.microsoft.com/azure/databox/)
    - [Usługa Azure Data Box Gateway i azure data box edge dla transferów online](https://docs.microsoft.com/azure/databox-online/).
- [Dowiedz się, czym jest usługa Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview).
- Przesyłanie danych za pomocą interfejsów API REST

    - [W .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
    - [W języku Java](https://docs.microsoft.com/java/api/overview/azure/storage)
