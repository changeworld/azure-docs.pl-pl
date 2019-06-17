---
title: Wizualizuj dane na pulpicie nawigacyjnym usługi Power BI z usługi Azure IoT Central | Dokumentacja firmy Microsoft
description: Rozwiązania usługi Power BI dla usługi Azure IoT Central umożliwia wizualizowanie i analizowanie danych IoT Central.
ms.service: iot-central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 06/09/2019
ms.topic: conceptual
ms.openlocfilehash: 16d7691d59e0805c7f8cf565a599ca5e6a78a7cf
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67053047"
---
# <a name="visualize-and-analyze-your-azure-iot-central-data-in-a-power-bi-dashboard"></a>Wizualizowanie i analizowanie danych usługi Azure IoT Central na pulpicie nawigacyjnym usługi Power BI

*W tym temacie mają zastosowanie do administratorów.*

![Usługa Power BI rozwiązanie potoku](media/howto-connect-powerbi/iot-continuous-data-export.png)

Rozwiązania usługi Power BI dla usługi Azure IoT Central umożliwia twórz zaawansowane pulpity nawigacyjne usługi Power BI do monitorowania wydajności urządzeń IoT. Na pulpicie nawigacyjnym usługi Power BI możesz wykonywać następujące czynności:
- Śledzenie danych, ile urządzeń jest wysyłany wraz z upływem czasu
- Porównaj ilość danych telemetrii, stanów i zdarzenia
- Identyfikowanie urządzeń przesyłających raporty mnóstwo pomiarów
- Obserwowanie trendów historycznych pomiarów urządzenia
- Zidentyfikować problematyczne urządzenia, które wysyłania partii zdarzeń krytycznych

To rozwiązanie Konfiguruje potok, który przenosi dane na koncie magazynu obiektów Blob platformy Azure z [ciągły Eksport danych](howto-export-data.md). Te dane są przekazywane za pośrednictwem do usługi Azure Functions, Azure Data Factory i Azure SQL Database, przetwarzać i przekształcać dane. Dane wyjściowe można zwizualizować i analizowane w raporcie usługi Power BI, który można pobrać jako plik PBIX. Wszystkie te zasoby są tworzone w ramach subskrypcji platformy Azure, więc można dostosować do własnych potrzeb każdego składnika.

## <a name="get-the-power-bi-solution-for-azure-iot-centralhttpsakamsiotcentralpowerbisolutiontemplate-from-microsoft-appsource"></a>Pobierz [usługi Power BI Solution dla usługi Azure IoT Central](https://aka.ms/iotcentralpowerbisolutiontemplate) z usługi Microsoft AppSource.

## <a name="prerequisites"></a>Wymagania wstępne
Konfigurowanie rozwiązania wymaga następujących elementów:
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

## <a name="architecture"></a>Architektura
Wszystkie zasoby, które zostały utworzone są dostępne w witrynie Azure portal. Wszystko, co powinna być w jednej grupie zasobów.

![Azure Portal widok grupy zasobów](media/howto-connect-powerbi/azure-deployment.PNG)

Szczegółowe informacje na temat poszczególnych zasobów i jak zostanie wykorzystany jest opisane poniżej.

### <a name="azure-functions"></a>Azure Functions
Aplikacja funkcji platformy Azure pobiera wyzwalane każdorazowo, gdy nowy plik jest zapisywany do magazynu obiektów Blob. Funkcje wyodrębniania pól w ramach każdego pomiarów, urządzeń i plików szablonów urządzeń i wypełnienie kilka pośrednich tabel SQL ma być używany przez usługi Azure Data Factory.

### <a name="azure-data-factory"></a>Azure Data Factory
Usługa Azure Data Factory łączy się z bazą danych SQL jako połączona usługa. Jest ono wykonywane działania procedury składowanej, które przetwarza dane i zapisz go w tabelach analizy.

### <a name="azure-sql-database"></a>Azure SQL Database
Te tabele są tworzone automatycznie, aby wypełnić domyślnych raportów. Eksploruj te schematy w usłudze Power BI i tworzyć własne wizualizacje oparte na tych danych.

| Nazwa tabeli |
|------------|
|[analizy]. [Pomiarów]|
|[analizy]. Komunikaty [o]|
|[etap]. [Pomiarów]|
|[analytics].[Properties]|
|[analytics].[PropertyDefinitions]|
|[analizy]. [MeasurementDefinitions]|
|[analizy]. [Urządzeń]|
|[analizy]. [DeviceTemplates]|
|[dbo]. [date]|
|[dbo].[ChangeTracking]|

## <a name="estimated-costs"></a>Szacowane koszty

W tym miejscu jest zaangażowane oszacować koszty platformy Azure (funkcji platformy Azure, Data Factory, Azure SQL). Wszystkie ceny są podane w USD. Należy pamiętać, że ceny różnią się zależnie od regionu, dzięki czemu zawsze powinna wyszukiwać najnowszymi cenami poszczególnych usług, aby uzyskać rzeczywiste ceny.
Następujące wartości domyślne są ustawione dla Ciebie w szablonie (możesz zmodyfikować któregoś z powyższych po elementy Pobierz):

- Usługa Azure Functions: Plan usługi App Service w wersji S1, 74.40 $/ miesiąc
- S1 usługi Azure SQL, ~$30/month

Zachęcamy do zapoznania się z różne opcje cenowe i dostosować elementy do własnych potrzeb.

## <a name="resources"></a>Zasoby

Odwiedź stronę usługi AppSource, aby uzyskać [usługi Power BI Solution dla usługi Azure IoT Central](https://aka.ms/iotcentralpowerbisolutiontemplate).

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wiesz jak wizualizować dane w usłudze Power BI, poniżej przedstawiono sugerowany następnego kroku:

> [!div class="nextstepaction"]
> [Jak zarządzać urządzeniami](howto-manage-devices.md)