---
title: Wizualizuj anomalii przy użyciu wykrywania usługi batch i usługa Power BI
titlesuffix: Azure Cognitive Services
description: Użyj interfejsu API wykrywanie anomalii i usługi Power BI do wizualizacji anomalie w danych szeregów czasowych.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 04/30/2019
ms.author: aahi
ms.openlocfilehash: de353c946da46b2bbe4e755e21e355f6bd18725b
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827142"
---
# <a name="tutorial-visualize-anomalies-using-batch-detection-and-power-bi"></a>Samouczek: Wizualizuj anomalii przy użyciu wykrywania usługi batch i usługa Power BI

Użyj tego samouczka, aby znaleźć anomalie w zestawie danych serii czasu jako zadania wsadowego. Za pomocą programu Power BI desktop, będą zająć pliku programu Excel, przygotować dane do interfejsu API wykrywanie anomalii, a także wizualizować statystyczne anomalie w całym jej.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Program Power BI Desktop do importowania i przekształcania zestawu danych serii czasu
> * Integracja usługi Power BI Desktop przy użyciu interfejsu API wykrywanie anomalii do wykrywania anomalii w usłudze batch
> * Umożliwia wizualizację anomalii w danych, zawierającą wartości z oczekiwaniami i jest dostępny i granice wykrywania anomalii.

## <a name="prerequisites"></a>Wymagania wstępne

* [Microsoft Power BI Desktop](https://powerbi.microsoft.com/get-started/), która jest dostępna bezpłatnie.
* Wskazuje programu excel (xlsx) pliku zawierającego dane szeregów czasowych. Przykładowe dane dla tego przewodnika Szybki Start można znaleźć na [GitHub](https://go.microsoft.com/fwlink/?linkid=2090962)

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

## <a name="load-and-format-the-time-series-data"></a>Ładowanie i formatowania danych szeregów czasowych

Aby rozpocząć, Otwórz w programie Power BI Desktop i załadować dane szeregów czasowych, które zostały pobrane z wymagań wstępnych. Ten plik programu excel zawiera szereg pary sygnatur czasowych i wartości uniwersalnego czasu koordynowanego (UTC).  

> [!NOTE]
> Usługa Power BI można użyć danych z wielu różnych źródeł, takich jak pliki CSV, programu SQL bazy danych, magazynu obiektów blob platformy Azure i inne.  

W głównym oknie programu Power BI Desktop kliknij **Home** wstążki. W **dane zewnętrzne** grupy wstążki, otwórz **Pobierz dane** menu listy rozwijanej i kliknij przycisk **Excel**.

![Obraz przedstawiający przycisk "Pobierz dane" w usłudze Power BI](../media/tutorials/power-bi-get-data-button.png)

Po wyświetleniu okna dialogowego, przejdź do folderu, do którego został pobrany plik xlsx przykład i zaznacz go. Po **Nawigator** zostanie wyświetlone okno dialogowe, kliknij przycisk **Arkusz1**, a następnie **Edytuj**.

![Obraz ekranu "Nawigator" źródła danych w usłudze Power BI](../media/tutorials/navigator-dialog-box.png)

Usługa Power BI przekonwertuje sygnatury czasowe w pierwszej kolumnie były `Date/Time` typu danych. Te znaczniki czasu musi zostać przekonwertowana na tekst w celu wysłania do interfejsu API wykrywanie anomalii. Edytor Power Query nie zostanie automatycznie otwarta, kliknij przycisk **Edytuj zapytania** na karcie home. 

Kliknij przycisk **Przekształcanie** wstążki w edytorze Power Query. W **dowolna kolumna** grupę, otwórz **— typ danych:** menu rozwijanego, a następnie wybierz **tekstu**.

![Obraz ekranu "Nawigator" źródła danych w usłudze Power BI](../media/tutorials/data-type-drop-down.png)

Gdy otrzymasz powiadomienie o zmianie typu kolumny, kliknij przycisk **Zastąp bieżący**. Później, kliknij przycisk **Zamknij i Zastosuj** lub **Zastosuj** w **Home** wstążki. 

## <a name="create-a-function-to-send-the-data-and-format-the-response"></a>Tworzenie funkcji do wysyłania danych i formatowanie odpowiedzi

Aby sformatować i Wyślij plik danych do interfejsu API wykrywanie anomalii, można wywoływać zapytania w tabeli utworzonej powyżej. W edytorze Power Query z **Home** wstążki, otwórz **nowe źródło** menu listy rozwijanej i kliknij przycisk **puste zapytanie**.

Upewnij się, że wybrano opcję nowe zapytanie, a następnie kliknij przycisk **edytora zaawansowanego**. 

![Obraz przycisku "Editor zaawansowane" w usłudze Power BI](../media/tutorials/advanced-editor-screen.png)

W ramach edytora zaawansowanego umożliwia poniższy fragment kodu Power Query M Wyodrębnij kolumny z tabeli oraz wysyłać je do interfejsu API. Później zapytanie Utwórz tabelę na podstawie odpowiedź w formacie JSON i przywrócić go. Zastąp `apiKey` zmiennej za pomocą prawidłowy klucz interfejsu API wykrywanie anomalii i `endpoint` z punktem końcowym usługi. Po wprowadzeniu zapytania do edytora zaawansowanego, kliknij przycisk **gotowe**.

```M
(table as table) => let

    apikey      = "[Placeholder: Your Anomaly Detector resource access key]",
    endpoint    = "[Placeholder: Your Anomaly Detector resource endpoint]/anomalydetector/v1.0/timeseries/entire/detect",
    inputTable = Table.TransformColumnTypes(table,{{"Timestamp", type text},{"Value", type number}}),
    jsontext    = Text.FromBinary(Json.FromValue(inputTable)),
    jsonbody    = "{ ""Granularity"": ""daily"", ""Sensitivity"": 95, ""Series"": "& jsontext &" }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Content-Type" = "application/json", #"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),

    respTable = Table.FromColumns({
                    
                     Table.Column(inputTable, "Timestamp")
                     ,Table.Column(inputTable, "Value")
                     , Record.Field(jsonresp, "IsAnomaly") as list
                     , Record.Field(jsonresp, "ExpectedValues") as list
                     , Record.Field(jsonresp, "UpperMargins")as list
                     , Record.Field(jsonresp, "LowerMargins") as list
                     , Record.Field(jsonresp, "IsPositiveAnomaly") as list
                     , Record.Field(jsonresp, "IsNegativeAnomaly") as list

                  }, {"Timestamp", "Value", "IsAnomaly", "ExpectedValues", "UpperMargin", "LowerMargin", "IsPositiveAnomaly", "IsNegativeAnomaly"}
               ),
    
    respTable1 = Table.AddColumn(respTable , "UpperMargins", (row) => row[ExpectedValues] + row[UpperMargin]),
    respTable2 = Table.AddColumn(respTable1 , "LowerMargins", (row) => row[ExpectedValues] -  row[LowerMargin]),
    respTable3 = Table.RemoveColumns(respTable2, "UpperMargin"),
    respTable4 = Table.RemoveColumns(respTable3, "LowerMargin"),

    results = Table.TransformColumnTypes(

                respTable4,
                {{"Timestamp", type datetime}, {"Value", type number}, {"IsAnomaly", type logical}, {"IsPositiveAnomaly", type logical}, {"IsNegativeAnomaly", type logical},
                 {"ExpectedValues", type number}, {"UpperMargins", type number}, {"LowerMargins", type number}}
              )

 in results
```

Wywołania zapytania w arkuszu danych, wybierając `Sheet1` poniżej **Wprowadzanie parametru**i kliknij przycisk **Invoke**. 

![Obraz przedstawiający przycisk "Zaawansowane Editor"](../media/tutorials/invoke-function-screenshot.png)

## <a name="data-source-privacy-and-authentication"></a>Prywatność źródła danych i uwierzytelniania

> [!NOTE]
> Należy pamiętać o zasady organizacji prywatność danych i dostęp. Zobacz [poziomy prywatności programu Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-privacy-levels) Aby uzyskać więcej informacji.

Może zostać komunikat ostrzegawczy, gdy próbują uruchomić zapytania, ponieważ korzysta ona z zewnętrznego źródła danych. 

![Obraz przedstawiający ostrzeżenie utworzone przez usługę Power BI](../media/tutorials/blocked-function.png)

Aby rozwiązać ten problem, kliknij przycisk **pliku**, i **opcje i ustawienia**. Następnie kliknij przycisk **opcje**. Poniżej **bieżący plik**, wybierz opcję **zachowania**, i **ignorowanie poziomów prywatności i potencjalne poprawianie wydajności**. 

Ponadto może zostać komunikat z prośbą do określenia, jak chcesz się połączyć z interfejsem API.

![Obraz przedstawiający żądanie, aby określić poświadczenia dostępu do](../media/tutorials/edit-credentials-message.png)

Aby rozwiązać ten problem, kliknij przycisk **edytowanie poświadczeń** w komunikacie. Gdy pojawi się okno dialogowe, wybierz **anonimowe** na anonimowe połączenia interfejsu API. Następnie kliknij przycisk **Connect** (Połącz). 

Później, kliknij przycisk **Zamknij i Zastosuj** w **Home** wstążki, aby zastosować zmiany.

## <a name="visualize-the-anomaly-detector-api-response"></a>Wizualizuj odpowiedzi interfejsu API wykrywanie anomalii

Na ekranie głównym usługi Power BI rozpocząć korzystanie z zapytań utworzone powyżej do wizualizacji danych. Najpierw wybierz **wykres liniowy** w **wizualizacje**. Następnie Dodaj sygnaturę czasową z wywołano funkcję do wykresu liniowego **osi**. Kliknij prawym przyciskiem myszy na nim, a następnie wybierz pozycję **sygnatura czasowa**. 

![Kliknij prawym przyciskiem myszy wartość sygnatury czasowej](../media/tutorials/timestamp-right-click.png)

Dodaj następujące pola z **wywołana funkcja** wykres **wartości** pola. Użyj Poniższy zrzut ekranu, aby ułatwić tworzenie wykresu.

    * Wartość
    * UpperMargins
    * LowerMargins
    * ExpectedValues

![Obraz nowy ekran szybka miara](../media/tutorials/chart-settings.png)

Po dodaniu pola, kliknij wykres, a rozmiar, tak aby pokazać wszystkie punkty danych. Wykres będzie wyglądać podobnie jak poniższy zrzut ekranu:

![Obraz nowy ekran szybka miara](../media/tutorials/chart-visualization.png)

### <a name="display-anomaly-data-points"></a>Wyświetl punkty danych anomalii

Po prawej stronie okna usługi Power BI poniżej **pola** okienku kliknij prawym przyciskiem myszy **wartość** w obszarze **zapytania wywołana funkcja**i kliknij przycisk **nowych możliwości szybkiego Miara**.

![Obraz nowy ekran szybka miara](../media/tutorials/new-quick-measure.png)

Na wyświetlonym ekranie zaznacz **filtrowana wartość** jako obliczeń. Ustaw **podstawowej wartości** do `Sum of Value`. Następnie przeciągnij `IsAnomaly` z **wywołana funkcja** polom **filtru**. Wybierz `True` z **filtru** menu rozwijanego.

![Obraz nowy ekran szybka miara](../media/tutorials/new-quick-measure-2.png)

Po kliknięciu przycisku **Ok**, będziesz mieć `Value for True` pola w dolnej części listy pól. Kliknij go prawym przyciskiem myszy, a następnie zmień jej nazwę na **anomalii**. Dodaj go do wykresu **wartości**. Następnie wybierz pozycję **Format** narzędzia i Ustaw typ osi x **podzielone**.

![Obraz nowy ekran szybka miara](../media/tutorials/format-x-axis.png)

Zastosowanie kolorów wykresu, klikając **Format** narzędzia i **kolory danych**. Wykres powinien wyglądać następująco:

![Obraz nowy ekran szybka miara](../media/tutorials/final-chart.png)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
>[Przesyłanie strumieniowe wykrywanie anomalii przy użyciu usługi Azure Databricks](anomaly-detection-streaming-databricks.md)
