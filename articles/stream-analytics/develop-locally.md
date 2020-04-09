---
title: Lokalne opracowywanie i debugowanie zadań usługi Azure Stream Analytics
description: Dowiedz się, jak tworzyć i testować zadania usługi Azure Stream Analytics na komputerze lokalnym przed uruchomieniem ich w witrynie Azure portal.
ms.author: mamccrea
author: mamccrea
ms.topic: conceptual
ms.date: 03/31/2020
ms.service: stream-analytics
ms.openlocfilehash: 736fce1d4b347e36ad5c10ca89ad0627104a0232
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879845"
---
# <a name="develop-and-debug-azure-stream-analytics-jobs-locally"></a>Lokalne opracowywanie i debugowanie zadań usługi Azure Stream Analytics

Chociaż możesz tworzyć i testować zadania usługi Azure Stream Analytics w witrynie Azure portal, wielu deweloperów preferuje lokalne środowisko programistyczne. Usługa Stream Analytics ułatwia korzystanie z ulubionego edytora kodu i narzędzi programistycznych do tworzenia i testowania zadań ze strumieniami zdarzeń na żywo z usługi Azure Event Hub, Usługi IoT Hub i magazynu obiektów Blob przy użyciu w pełni funkcjonującego lokalnego środowiska wykonawczego pojedynczego węzła. Można również przesłać zadania do platformy Azure bezpośrednio z lokalnego środowiska programistycznego.

## <a name="local-development-environments"></a>Lokalne środowiska programistyczne

Sposób tworzenia zadań usługi Stream Analytics na komputerze lokalnym zależy od preferencji narzędzi i dostępności funkcji. Zobacz [porównanie funkcji usługi Azure Stream Analytics,](feature-comparison.md) aby zobaczyć, jakie funkcje są obsługiwane dla każdego środowiska programistycznego.

Środowiska w poniższej tabeli obsługują rozwój lokalny:

|Środowisko                              |Opis    |
|-----------------------------------------|------------|
|[Visual Studio Code](visual-studio-code-explore-jobs.md)| [Rozszerzenie narzędzia Azure Stream Analytics Tools](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa) dla programu Visual Studio Code umożliwia tworzenie, zarządzanie i testowanie zadania analizy strumienia zarówno lokalnie, jak i w chmurze za pomocą bogatego intellisense i natywnej kontroli źródła. Obsługuje tworzenie w systemach Linux, MacOS i Windows. Aby dowiedzieć się więcej, zobacz [Tworzenie zadania usługi Azure Stream Analytics w programie Visual Studio Code](quick-create-vs-code.md).|
|[Visual Studio 2019](stream-analytics-tools-for-visual-studio-install.md) |Narzędzia do analizy strumienia jest częścią tworzenia platformy Azure i przechowywania danych i przetwarzania obciążeń w programie Visual Studio. Za pomocą programu Visual Studio można napisać niestandardowe funkcje zdefiniowane przez użytkownika języka C# i deserializatory. Aby dowiedzieć się więcej, zobacz [Tworzenie zadania usługi Azure Stream Analytics przy użyciu programu Visual Studio](stream-analytics-quick-create-vs.md).|
|[Wiersz polecenia lub terminal](stream-analytics-tools-for-visual-studio-cicd.md)|Pakiet Azure Stream Analytics CI/CD NuGet zawiera narzędzia do tworzenia projektów programu Visual Studio, testowania lokalnego na dowolnym komputerze. Pakiet npm usługi Azure Stream Analytics CI/CD zawiera narzędzia do kompilacji projektu programu Visual Studio Code (który generuje szablon usługi Azure Resource Manager) na dowolnym komputerze.|

## <a name="next-steps"></a>Następne kroki

* [Testowanie zapytań usługi Stream Analytics lokalnie z przykładowymi danymi przy użyciu kodu programu Visual Studio](visual-studio-code-local-run.md)
* [Testowanie usługi Stream Analytics zapytania lokalnie względem danych wejściowych strumienia na żywo przy użyciu programu Visual Studio Code](visual-studio-code-local-run-live-input.md)
* [Testowanie zapytań usługi Stream Analytics lokalnie za pomocą programu Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Testowanie danych na żywo lokalnie przy użyciu narzędzi usługi Azure Stream Analytics dla programu Visual Studio](stream-analytics-live-data-local-testing.md)
