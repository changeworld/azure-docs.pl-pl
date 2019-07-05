---
title: Porównanie funkcji usługi Azure Stream Analytics
description: W tym artykule porównano funkcje obsługiwane w chmurze usługi Azure Stream Analytics i zadania usługi IoT Edge w witrynie Azure portal, programu Visual Studio i Visual Studio Code.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 22d7ef90ee0cf4d09467516b7bb0664327b7dabe
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509783"
---
# <a name="azure-stream-analytics-feature-comparison"></a>Porównanie funkcji usługi Azure Stream Analytics

Usługi Azure Stream Analytics umożliwia tworzenie rozwiązań do transmisji strumieniowej w chmurze i na urządzeniach brzegowych IoT przy użyciu [witryny Azure portal](stream-analytics-quick-create-portal.md), [programu Visual Studio](stream-analytics-quick-create-vs.md), i [programu Visual Studio Code](quick-create-vs-code.md). Tabele w tym artykule Pokaż, które funkcje są obsługiwane przez każdej z platform dla obu typów zadań.

## <a name="cloud-job-features"></a>Funkcje zadania w chmurze


|Cecha  |Portal  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Wiele platform     |Mac</br>Linux</br>Windows         |Windows        |Mac</br>Linux</br>Windows          |
|Tworzenie skryptu     |Tak         |Yes         |Tak         |
|Funkcja Intellisense skryptu     |Wyróżnianie składni         |Wyróżnianie składni</br>Uzupełnianie kodu</br>Błąd znacznika         |Wyróżnianie składni</br>Uzupełnianie kodu</br>Błąd znacznika         |
|Definiowanie danych wejściowych, danych wyjściowych i konfiguracji zadania     |Tak         |Yes         |Tak         |
|Partycjonowanie danych wyjściowych obiektu blob     |Yes         |Yes         |Yes         |
|Usługa Power BI jako dane wyjściowe     |Tak         |Yes         |Nie         |
|Dane referencyjne bazy danych SQL     |Tak         |Yes         |Yes         |
|Niestandardowe właściwości komunikatu     |Tak         |Nie         |Nie         |
|Udostępniaj dane wejściowe i wyjściowe między wieloma zapytaniami     |Nie         |Yes         |Tak         |
|JavaScript — UDF i agregacji UDA     |Yes         |Tak         |Tylko Windows         |
|Usługi Machine Learning objaśnienia     |Tak, ale zapytanie nie można przetestować        |Tak, ale nie można przetestować lokalnie         |Nie         |
|Poziom zgodności     |1.0</br>1.1</br>1.2         |1.0</br>1.1</br>1.2          |1.0</br>1.1</br>1.2          |
|Funkcje wbudowane wykrywanie anomalii oparte na ML     |Tak         |Yes         |Tak         |
|Wbudowane funkcje Geoprzestrzenne     |Tak         |Yes         |Tak         |
|Testowanie zapytań z użyciem przykładowy plik     |Tak         |Yes         |Tak         |
|Dane lokalne testowanie na żywo     |Nie         |Yes         |Nie         |
|Wyświetlić zadania oraz jednostki zadania     |Tak         |Yes         |Tak         |
|Eksportuj zadania do lokalnego projektu     |Nie         |Yes         |Yes         |
|Przesyłanie, uruchamianie i zatrzymywanie zadań     |Tak         |Yes         |Yes         |
|Kontrola źródła     |Nie         |Yes         |Yes         |
|Obsługa ciągłej integracji/ciągłego wdrażania     |Częściowe         |Yes         |Yes         |
|Wyświetl zadania metryki i diagram     |Tak         |Tak         |Otwórz w portalu         |
|Wyświetl błędy podczas wykonywania zadania     |Yes         |Yes         |Nie         |
|Dzienniki diagnostyczne     |Tak         |Nie         |Nie         |


## <a name="iot-edge-job-features"></a>Funkcje zadania usługi IoT Edge

|Cecha  |Portal  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Tworzenie zadania     |Tak         |Yes         |Nie         |
|Kontrola źródła     |Nie         |Yes         |Nie         |
|Eksportuj zadania do lokalnego projektu     |Nie         |Yes         |Nie         |
|Testowanie zapytań z użyciem przykładowy plik     |Yes         |Yes         |Nie         |
|Udostępniaj dane wejściowe i wyjściowe między wieloma zapytaniami     |Nie         |Yes         |Nie         |
|C#FUNKCJI ZDEFINIOWANEJ PRZEZ UŻYTKOWNIKA     |Nie         |Yes         |Nie         |
|Przesyłanie, uruchamianie i zatrzymywanie zadań     |Tak         |Yes         |Nie         |
|Wyświetlić zadania oraz jednostki zadania     |Tak         |Yes         |Nie         |
|Wyświetl zadania metryki i diagram     |Yes         |Częściowe         |Nie         |
|Wyświetl błędy podczas wykonywania zadania     |Tak         |Częściowe         |Nie         |
|Obsługa ciągłej integracji/ciągłego wdrażania     |Nie         |Nie         |Nie         |


## <a name="next-steps"></a>Kolejne kroki

* [Azure Stream Analytics w usłudze IoT Edge](stream-analytics-edge.md)
* [Samouczek: Zapis C# funkcji zdefiniowanej przez użytkownika dla zadania usługi Azure Stream Analytics IoT Edge (wersja zapoznawcza)](stream-analytics-edge-csharp-udf.md)
* [Tworzenie zadań usługi Stream Analytics IoT Edge przy użyciu narzędzi programu Visual Studio](stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [Wyświetl zadania usługi Azure Stream Analytics przy użyciu programu Visual Studio](stream-analytics-vs-tools.md)
* [Eksplorowanie usługi Azure Stream Analytics za pomocą programu Visual Studio Code (wersja zapoznawcza)](vscode-explore-jobs.md)


