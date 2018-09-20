---
title: Wizualizuj dane na pulpicie nawigacyjnym usługi Power BI z usługi Azure IoT Central | Dokumentacja firmy Microsoft
description: Szablon rozwiązania usługi Azure IoT Central analizy usługi Power BI umożliwia wizualizowanie i analizowanie danych IoT Central.
ms.service: iot-central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/16/2018
ms.topic: conceptual
ms.openlocfilehash: 6f8bed8aba43d77cdfad49f4fe62a4c7aa2c5ce3
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2018
ms.locfileid: "46368073"
---
# <a name="visualize-and-analyze-your-azure-iot-central-data-in-a-power-bi-dashboard"></a>Wizualizowanie i analizowanie danych usługi Azure IoT Central na pulpicie nawigacyjnym usługi Power BI

*W tym temacie mają zastosowanie do administratorów.*

![Usługa Power BI rozwiązanie szablonu potoku](media/howto-connect-powerbi/iot-continuous-data-export.png)

Szablon rozwiązania usługi Azure IoT Central analizy usługi Power BI umożliwia twórz zaawansowane pulpity nawigacyjne usługi Power BI do monitorowania wydajności urządzeń IoT. Na pulpicie nawigacyjnym usługi Power BI możesz wykonywać następujące czynności:
- Śledzenie danych, ile urządzeń jest wysyłany wraz z upływem czasu
- Porównaj ilość danych telemetrii, stanów i zdarzenia
- Identyfikowanie urządzeń przesyłających raporty mnóstwo pomiarów
- Obserwowanie trendów historycznych pomiarów urządzenia
- Zidentyfikować problematyczne urządzenia, które wysyłania partii zdarzeń krytycznych

Ten szablon rozwiązania Konfiguruje potok, który przenosi dane na koncie magazynu obiektów Blob platformy Azure z [ciągły Eksport danych](howto-export-data.md). Te dane są przekazywane za pośrednictwem do usługi Azure Functions, Azure Data Factory i Azure SQL Database, przetwarzać i przekształcać dane. Dane wyjściowe można zwizualizować i analizowane w raporcie usługi Power BI, który można pobrać jako plik PBIX. Wszystkie te zasoby są tworzone w ramach subskrypcji platformy Azure, więc można dostosować do własnych potrzeb każdego składnika. Ten szablon rozwiązania jest całkowicie open source, dzięki czemu można dowiedzieć się więcej o architekturze i rozbudowuj rozwiązanie, odwiedzając [repozytorium GitHub](https://aka.ms/iotcentralgithubpowerbisolutiontemplate).

## <a name="get-the-azure-iot-central-analytics-solution-templatehttpsakamsiotcentralpowerbisolutiontemplate-from-microsoft-appsource"></a>Pobierz [szablon rozwiązania usługi Azure IoT Central Analytics](https://aka.ms/iotcentralpowerbisolutiontemplate) z Microsoft AppSource

## <a name="prerequisites"></a>Wymagania wstępne
Konfigurowanie szablonu wymaga następujących elementów:
- Dostęp do subskrypcji platformy Azure
- Wyeksportowane dane przy użyciu [ciągły Eksport danych](howto-export-data.md) z aplikacji IoT Central. Zalecane jest włączenie pomiarów, urządzeń i strumieni szablonu urządzenia do w pełni wykorzystać pulpit nawigacyjny usługi Power BI.
- Usługa Power BI Desktop (Najnowsza wersja)
- Usługa Power BI Pro (Jeśli chcesz udostępnić pulpit nawigacyjny)

## <a name="reports"></a>Raporty

Dwa raporty są generowane automatycznie. 

Pierwszy raport przedstawia widok historycznych pomiarów zgłaszane przez urządzenia i dzieli różnego rodzaju pomiarów i urządzeń, które wysłały największa liczba pomiarów dotyczących prawdziwych.

![Strona raportu usługi Power BI 1](media/howto-connect-powerbi/template-page1-hasdata.PNG)

Drugi raport bardziej omawia zdarzeń i pokazuje widok historyczny błędów i ostrzeżeń zgłaszanych. Pokazuje również, które urządzenia zgłaszanej największa liczba zdarzeń wszystkich programach i połączeniach, a także specjalnie zdarzeń błędu i ostrzeżenia.

![Strona raportu usługi Power BI 2](media/howto-connect-powerbi/template-page2-hasdata.PNG)

## <a name="resources"></a>Zasoby

Odwiedź stronę usługi AppSource, aby uzyskać [szablon rozwiązania usługi Azure IoT Central Analytics](https://aka.ms/iotcentralpowerbisolutiontemplate).

Odwiedź stronę [repozytorium GitHub](https://aka.ms/iotcentralgithubpowerbisolutiontemplate) Aby dowiedzieć się więcej o architekturze i rozbudowania danego rozwiązania.

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wiesz jak wizualizować dane w usłudze Power BI, poniżej przedstawiono sugerowany następnego kroku:

> [!div class="nextstepaction"]
> [Jak zarządzać urządzeniami](howto-manage-devices.md)
