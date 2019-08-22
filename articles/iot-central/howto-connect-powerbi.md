---
title: Wizualizowanie danych usługi Azure IoT Central na pulpicie nawigacyjnym Power BI | Microsoft Docs
description: Użyj rozwiązania Power BI dla IoT Central platformy Azure, aby wizualizować i analizować dane IoT Central.
ms.service: iot-central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 06/09/2019
ms.topic: conceptual
ms.openlocfilehash: 897b72dd7b292492e45cdc7401fe1acc5c86ca20
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877452"
---
# <a name="visualize-and-analyze-your-azure-iot-central-data-in-a-power-bi-dashboard"></a>Wizualizowanie i analizowanie danych IoT Central platformy Azure na pulpicie nawigacyjnym Power BI

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

*Ten temat ma zastosowanie do administratorów.*

![Potok rozwiązania Power BI](media/howto-connect-powerbi/iot-continuous-data-export.png)

Użyj rozwiązania Power BI dla IoT Central platformy Azure, aby utworzyć zaawansowany pulpit nawigacyjny Power BI do monitorowania wydajności urządzeń IoT. Na pulpicie nawigacyjnym Power BI można:
- Śledzenie ilości danych wysyłanych przez urządzenia z biegiem czasu
- Porównanie ilości danych między telemetrią, Stanami i zdarzeniami
- Zidentyfikuj urządzenia, które zgłaszają wiele pomiarów
- Obserwuj historyczne trendy pomiarów urządzeń
- Identyfikowanie problematycznych urządzeń, które wysyłają wiele krytycznych zdarzeń

To rozwiązanie konfiguruje potok, który pobiera dane z konta usługi Azure Blob Storage z [ciągłego eksportu danych](howto-export-data.md). Te dane są przesyłane przez program do Azure Functions, Azure Data Factory i Azure SQL Database do przetwarzania i przekształcania danych. Dane wyjściowe można wizualizować i analizować w Power BI raport, który można pobrać jako plik PBIX. Wszystkie te zasoby są tworzone w ramach subskrypcji platformy Azure, dzięki czemu można dostosować każdy składnik do swoich potrzeb.

## <a name="get-the-power-bi-solution-for-azure-iot-centralhttpsakamsiotcentralpowerbisolutiontemplate-from-microsoft-appsource"></a>Pobierz [Power BI rozwiązanie dla platformy Azure IoT Central](https://aka.ms/iotcentralpowerbisolutiontemplate) z Microsoft AppSource.

## <a name="prerequisites"></a>Wymagania wstępne
Skonfigurowanie rozwiązania wymaga następujących czynności:
- Dostęp do subskrypcji platformy Azure
- Wyeksportowane dane przy użyciu [ciągłego eksportu danych](howto-export-data.md) z aplikacji IoT Central. Zalecamy włączenie pomiarów, urządzeń i strumieni szablonów urządzeń w celu maksymalnego wykorzystania pulpitu nawigacyjnego Power BI.
- Power BI Desktop (Najnowsza wersja)
- Power BI Pro (Jeśli chcesz udostępnić pulpit nawigacyjny innym osobom)

## <a name="reports"></a>Raporty

Dwa raporty są generowane automatycznie. 

Pierwszy raport przedstawia historyczny widok pomiarów raportowanych przez urządzenia i dzieli różne typy pomiarów i urządzeń, które przesłały najwyższą liczbę pomiarów.

![Strona raportu Power BI 1](media/howto-connect-powerbi/template-page1-hasdata.PNG)

Drugi raport omówieniach bardziej szczegółowy i zawiera historyczny widok błędów i ostrzeżeń zgłoszonych. Przedstawiono w nim również, które urządzenia zgłaszają największą liczbę zdarzeń, a także szczegółowe zdarzenia błędów i zdarzenia ostrzegawcze.

![Strona raportu Power BI 2](media/howto-connect-powerbi/template-page2-hasdata.PNG)

## <a name="architecture"></a>Architektura
Do wszystkich utworzonych zasobów można uzyskać dostęp w Azure Portal. Wszystkie elementy powinny znajdować się w jednej grupie zasobów.

![Widok grupy zasobów w witrynie Azure Portal](media/howto-connect-powerbi/azure-deployment.PNG)

Szczegóły poszczególnych zasobów i sposobu ich użycia opisano poniżej.

### <a name="azure-functions"></a>Azure Functions
Aplikacja funkcji platformy Azure jest wyzwalana za każdym razem, gdy nowy plik zostanie zapisany w magazynie obiektów BLOB. Funkcje wyodrębniają pola w ramach każdego pomiaru, urządzeń i pliku szablonów urządzeń i wypełniają kilka pośrednich tabel SQL, które mają być używane przez Azure Data Factory.

### <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory nawiązuje połączenie z bazą danych SQL jako połączoną usługę. Uruchamia działania procedur składowanych, które przetwarzają dane i przechowują je w tabelach analizy.

### <a name="azure-sql-database"></a>Azure SQL Database
Te tabele są tworzone automatycznie w celu wypełnienia raportów domyślnych. Zapoznaj się z tymi schematami w Power BI i możesz utworzyć własne wizualizacje na tych danych.

| Nazwa tabeli |
|------------|
|[Analiza]. Pomiarów|
|[Analiza]. Komunikaty|
|[etap]. Pomiarów|
|[Analiza]. Aœciwoœci|
|[analytics].[PropertyDefinitions]|
|[Analiza]. [MeasurementDefinitions]|
|[Analiza]. Urządzeniem|
|[Analiza]. [DeviceTemplates]|
|[dbo]. dniu|
|[dbo]. Śledzenia zmian|

## <a name="estimated-costs"></a>Szacowane koszty

Poniżej przedstawiono oszacowanie kosztów platformy Azure (Azure Function, Data Factory, Azure SQL). Wszystkie ceny są podane w USD. Należy pamiętać, że ceny różnią się w zależności od regionu, więc zawsze należy szukać najnowszych cen poszczególnych usług w celu uzyskania rzeczywistych cen.
Następujące wartości domyślne są ustawiane w szablonie (można je zmodyfikować po skonfigurowaniu):

- Azure Functions: Plan App Service S1, $74.40 miesięcznie
- Azure SQL S1, ~ $30 miesięcznie

Zachęcamy do zapoznania się z różnymi opcjami cenowymi i dostosowaniem ich do Twoich potrzeb.

## <a name="resources"></a>Zasoby

Odwiedź stronę AppSource, aby uzyskać [Power BI rozwiązanie dla IoT Central platformy Azure](https://aka.ms/iotcentralpowerbisolutiontemplate).

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak wizualizować dane w Power BI, poniżej przedstawiono sugerowany następny krok:

> [!div class="nextstepaction"]
> [Jak zarządzać urządzeniami](howto-manage-devices.md)