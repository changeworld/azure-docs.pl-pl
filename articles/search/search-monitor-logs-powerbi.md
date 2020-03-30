---
title: Wizualizuj dzienniki i metryki wyszukiwania funkcji Azure Cognitive Search za pomocą usługi Power BI
description: Wizualizuj dzienniki i metryki wyszukiwania funkcji Azure Cognitive Search za pomocą usługi Power BI
manager: eladz
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: 618033e24236d26601fafaf7b0678ee83ec973e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650166"
---
# <a name="visualize-azure-cognitive-search-logs-and-metrics-with-power-bi"></a>Wizualizuj dzienniki i metryki wyszukiwania funkcji Azure Cognitive Search za pomocą usługi Power BI
[Usługa Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-what-is-azure-search) umożliwia przechowywanie dzienników operacji i metryk usługi dotyczących usługi wyszukiwania na koncie usługi Azure Storage. Ta strona zawiera instrukcje dotyczące wizualizacji tych informacji za pośrednictwem aplikacji Szablon usługi Power BI. Aplikacja zawiera szczegółowe informacje na temat usługi wyszukiwania, w tym informacje o wynikach wyszukiwania, indeksowania, operacji i metryk usługi.

Szablon usługi Power BI App **Azure Cognitive Search: Analizowanie dzienników i metryk** można znaleźć w [portalu Windowser bi Apps marketplace](https://appsource.microsoft.com/marketplace/apps).

## <a name="how-to-get-started-with-the-app"></a>Jak rozpocząć korzystanie z aplikacji
1. Włącz rejestrowanie diagnostyczne dla usługi wyszukiwania:
    1. Tworzenie lub identyfikowanie istniejącego [konta usługi Azure Storage,](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) na którym można archiwizować dzienniki
    1. Przejdź do usługi Azure Cognitive Search w witrynie Azure portal
    1. W sekcji Monitorowanie w lewej kolumnie wybierz **pozycję Ustawienia diagnostyczne**

        ![](media/search-monitor-logs-powerbi/diagnostic-settings.png)

    1. Wybierz **+ Dodaj ustawienie diagnostyczne**
    1. Sprawdź **archiwum na koncie magazynu,** podaj informacje o koncie magazynu i sprawdź **OperationLogs** i **AllMetrics**

        ![](media/search-monitor-logs-powerbi/add-diagnostic-setting.png)
    1. Wybierz pozycję **Zapisz**.

1. Po włączeniu rejestrowania użyj usługi wyszukiwania, aby rozpocząć generowanie dzienników i metryk. Trwa do godziny, zanim kontenery pojawią się w magazynie obiektów Blob z tych dzienników. Zobaczysz kontener **insights-logs-operationlogs** dla dzienników ruchu wyszukiwania i **kontenera insights-metrics-pt1m** dla metryk.

1. Znajdź aplikację Azure Cognitive Search Power BI w [portalu Power BI Apps](https://appsource.microsoft.com/marketplace/apps) i zainstaluj ją w nowym obszarze roboczym lub istniejącym obszarze roboczym. Aplikacja nosi nazwę **Azure Cognitive Search: Analyze Logs and Metrics**.

1. Po zainstalowaniu aplikacji wybierz aplikację z listy aplikacji w usłudze Power BI.

    ![](media/search-monitor-logs-powerbi/azure-search-app-tile.png)

1. Wybierz **połącz,** aby połączyć dane

    ![](media/search-monitor-logs-powerbi/get-started-with-your-new-app.png)

1. Wprowadź nazwę konta magazynu, który zawiera dzienniki i metryki. Domyślnie aplikacja będzie patrzeć na ostatnie 10 dni danych, ale tę wartość można zmienić za pomocą **days** parametru.

    ![](media/search-monitor-logs-powerbi/connect-to-storage-account.png)

1. Wybierz **opcję Klucz** jako metodę uwierzytelniania i podaj klucz konta magazynu. Wybierz **pozycję Prywatne** jako poziom prywatności. Kliknij przycisk Zaloguj i rozpocznij proces ładowania.

    ![](media/search-monitor-logs-powerbi/connect-to-storage-account-step-two.png)

1. Poczekaj na odświeżenie danych. Może to zająć trochę czasu w zależności od ilości posiadane dane. Możesz sprawdzić, czy dane są nadal odświeżane na podstawie poniższego wskaźnika.

    ![](media/search-monitor-logs-powerbi/workspace-view-refreshing.png)

1. Po zakończeniu odświeżania danych wybierz pozycję **Raport usługi Azure Cognitive Search,** aby wyświetlić raport.

    ![](media/search-monitor-logs-powerbi/workspace-view-select-report.png)

1. Upewnij się, że strona zostanie odświeżona po otwarciu raportu, aby została otwarta wraz z danymi.

    ![](media/search-monitor-logs-powerbi/powerbi-search.png)

## <a name="how-to-change-the-app-parameters"></a>Jak zmienić parametry aplikacji
Jeśli chcesz wizualizować dane z innego konta magazynu lub zmienić liczbę dni danych do kwerendy, wykonaj poniższe kroki, aby zmienić dni **i** **storageaccount** parametry.

1. Przejdź do aplikacji usługi Power BI, znajdź aplikację Azure Cognitive Search i wybierz przycisk **Edytuj aplikację,** aby wyświetlić obszar roboczy.

    ![](media/search-monitor-logs-powerbi/azure-search-app-tile-edit.png)

1. Wybierz **pozycję Ustawienia** z opcji zestawu danych.

    ![](media/search-monitor-logs-powerbi/workspace-view-select-settings.png)

1. Na karcie Zestawy danych zmień wartości parametrów i wybierz pozycję **Zastosuj**. Jeśli występuje problem z połączeniem, zaktualizuj poświadczenia źródła danych na tej samej stronie.

1. Przejdź z powrotem do obszaru roboczego i wybierz **pozycję Odśwież teraz** z opcji zestawu danych.

    ![](media/search-monitor-logs-powerbi/workspace-view-select-refresh-now.png)

1. Otwórz raport, aby wyświetlić zaktualizowane dane. Może być również konieczne odświeżenie raportu, aby wyświetlić najnowsze dane.

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Jeśli okaże się, że nie widzisz danych, wykonaj następujące kroki rozwiązywania problemów:

1. Otwórz raport i odśwież stronę, aby upewnić się, że wyświetlasz najnowsze dane. W raporcie znajduje się opcja odświeżania danych. Wybierz tę opcję, aby uzyskać najnowsze dane.

1. Upewnij się, że podana nazwa konta magazynu i klucz dostępu są poprawne. Nazwa konta magazynu powinna odpowiadać kontu skonfigurowanemu w dziennikach usługi wyszukiwania.

1. Upewnij się, że konto magazynu zawiera kontenery **insights-logs-operationlogs** i **insights-metrics-pt1m** i każdy kontener ma dane. Dzienniki i metryki będą znajdować się w kilku warstwach folderów.

1. Sprawdź, czy zestaw danych jest nadal odświeżony. Wskaźnik stanu odświeżania jest wyświetlany w kroku 8 powyżej. Jeśli nadal jest odświeżający, poczekaj, aż odświeżanie zostanie zakończone, aby otworzyć i odświeżyć raport.

## <a name="next-steps"></a>Następne kroki
[Dowiedz się więcej o usłudze Azure Cognitive Search](https://docs.microsoft.com/azure/search/)

[Co to jest usługa Power BI?](https://docs.microsoft.com/power-bi/fundamentals/power-bi-overview)

[Podstawowe pojęcia dla projektantów w usłudze Power BI](https://docs.microsoft.com/power-bi/service-basic-concepts)