---
title: 'Samouczek: wizualizuj anomalie przy użyciu wykrywania partii i usługi Power BI'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać interfejsu API detektora anomalii i usługi Power BI do wizualizacji anomalii w danych szeregów czasowych.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: tutorial
ms.date: 03/05/2020
ms.author: aahi
ms.openlocfilehash: 1b486aaf0ce33e31433c2c3d0f7a1ff2c7089132
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "78402648"
---
# <a name="tutorial-visualize-anomalies-using-batch-detection-and-power-bi"></a>Samouczek: wizualizuj anomalie przy użyciu wykrywania partii i usługi Power BI

Ten samouczek służy do znajdowania anomalii w zestawie danych szeregów czasowych jako partii. Korzystając z programu Power BI desktop, należy wziąć plik programu Excel, przygotować dane dla interfejsu API detektora anomalii i wizualizować anomalie statystyczne w całym nim.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Importowanie i przekształcanie zestawu danych szeregów czasowych za pomocą programu Power BI Desktop
> * Integracja programu Power BI Desktop z interfejsem API detektora anomalii w celu wykrywania anomalii wsadowych
> * Wizualizuj anomalie znalezione w danych, w tym wartości oczekiwane i widoczne oraz granice wykrywania anomalii.

## <a name="prerequisites"></a>Wymagania wstępne
* [Subskrypcja platformy Azure](https://azure.microsoft.com/free/)
* [Program Microsoft Power BI Desktop](https://powerbi.microsoft.com/get-started/)jest dostępny bezpłatnie.
* Plik programu excel (xlsx) zawierający punkty danych szeregów czasowych. Przykładowe dane dla tego przewodnika Szybki start można znaleźć w [usłudze GitHub](https://go.microsoft.com/fwlink/?linkid=2090962)
* Po utworzeniu subskrypcji <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="platformy Azure utwórz"  target="_blank">zasób <span class="docon docon-navigate-external x-hidden-focus"></span> </a> analizy tekstu w witrynie Azure portal, aby uzyskać klucz i punkt końcowy. 
    * Potrzebny będzie klucz i punkt końcowy z zasobu, który tworzysz, aby połączyć aplikację z interfejsem API analizy tekstu. Zrobisz to później w przewodniku Szybki start.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

## <a name="load-and-format-the-time-series-data"></a>Ładowanie i formatowanie danych szeregów czasowych

Aby rozpocząć, otwórz program Power BI Desktop i załaduj dane szeregów czasowych pobrane z wymagań wstępnych. Ten plik programu Excel zawiera serię par sygnatur czasowych i par sygnatur czasowych skoordynowanego czasu uniwersalnego (UTC).  

> [!NOTE]
> Usługa Power BI może używać danych z wielu różnych źródeł, takich jak pliki csv, bazy danych SQL, magazyn obiektów blob platformy Azure i inne.  

W głównym oknie programu Power BI Desktop kliknij wstążkę **Narzędzia główne.** W grupie **Dane zewnętrzne** wstążki otwórz menu rozwijane **Pobierz dane** i kliknij pozycję **Excel**.

![Obraz przycisku "Pobierz dane" w usłudze Power BI](../media/tutorials/power-bi-get-data-button.png)

Po wyświetleniu okna dialogowego przejdź do folderu, w którym pobrano przykładowy plik xlsx, i wybierz go. Po **wyświetleniu** dialogu Nawigator kliknij pozycję **Arkusz1**, a następnie **edytuj**.

![Obraz ekranu źródła danych "Nawigator" w usłudze Power BI](../media/tutorials/navigator-dialog-box.png)

Usługa Power BI konwertuje sygnatury `Date/Time` czasowe w pierwszej kolumnie na typ danych. Te sygnatury czasowe muszą zostać przekonwertowane na tekst, aby można je było wysłać do interfejsu API detektora anomalii. Jeśli edytor dodatku Power Query nie zostanie automatycznie otwarty, kliknij pozycję **Edytuj kwerendy** na karcie Narzędzia główne. 

Kliknij wstążkę **Przekształcanie** w Edytorze dodatków Power Query. W grupie **Dowolna kolumna** otwórz menu rozwijane **Typ danych:** i wybierz polecenie **Tekst**.

![Obraz ekranu źródła danych "Nawigator" w usłudze Power BI](../media/tutorials/data-type-drop-down.png)

Po otrzymaniu powiadomienia o zmianie typu kolumny kliknij przycisk **Zamień bieżący**. Następnie kliknij przycisk **Zamknij & Zastosuj** lub **Zastosuj** na wstążce **Narzędzia domowe.** 

## <a name="create-a-function-to-send-the-data-and-format-the-response"></a>Tworzenie funkcji wysyłania danych i formatowania odpowiedzi

Aby sformatować i wysłać plik danych do interfejsu API detektora anomalii, można wywołać kwerendę w tabeli utworzonej powyżej. Na stronie Power Query Editor na wstążce **Narzędzia główne** otwórz menu rozwijane **Nowe źródło** i kliknij pozycję **Puste zapytanie**.

Upewnij się, że nowa kwerenda jest zaznaczona, a następnie kliknij pozycję **Edytor zaawansowany**. 

![Obraz przycisku "Zaawansowany edytor" w usłudze Power BI](../media/tutorials/advanced-editor-screen.png)

W Edytorze zaawansowanym użyj następującego fragmentu m dodatku Power Query, aby wyodrębnić kolumny z tabeli i wysłać je do interfejsu API. Następnie kwerenda utworzy tabelę z odpowiedzi JSON i zwróci ją. Zastąp zmienną `apiKey` prawidłowym kluczem `endpoint` API detektora anomalii i punktem końcowym. Po wprowadzeniu kwerendy do Edytora zaawansowanego kliknij przycisk **Gotowe**.

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

Wywołaj kwerendę w arkuszu `Sheet1` danych, zaznaczając poniżej **pozycję Enter Parameter**i kliknij przycisk **Wywołaj**. 

![Obraz przycisku "Edytor zaawansowany"](../media/tutorials/invoke-function-screenshot.png)

## <a name="data-source-privacy-and-authentication"></a>Prywatność i uwierzytelnianie źródeł danych

> [!NOTE]
> Należy pamiętać o zasadach organizacji dotyczących prywatności i dostępu do danych. Aby uzyskać więcej informacji, zobacz [Poziomy prywatności programu Power BI desktop.](https://docs.microsoft.com/power-bi/desktop-privacy-levels)

Podczas próby uruchomienia kwerendy może zostać wyświetlony komunikat ostrzegawczy, ponieważ wykorzystuje ona zewnętrzne źródło danych. 

![Obraz przedstawiający ostrzeżenie utworzone przez program Power BI](../media/tutorials/blocked-function.png)

Aby rozwiązać ten problem, kliknij pozycję **Plik**, a **następnie pozycję Opcje i ustawienia**. Następnie kliknij pozycję **Opcje**. Poniżej **dalsza część artykułu**Wybierz pozycję **Prywatność**i **Ignoruj poziomy prywatności i potencjalnie zwiększ wydajność.** 

Ponadto może pojawić się komunikat z prośbą o określenie sposobu łączenia się z interfejsem API.

![Obraz przedstawiający żądanie określenia poświadczeń dostępu](../media/tutorials/edit-credentials-message.png)

Aby rozwiązać ten problem, kliknij pozycję **Edytuj poświadczenia** w wiadomości. Po wyświetleniu okna dialogowego wybierz **pozycję Anonimowy,** aby anonimowo połączyć się z interfejsem API. Następnie kliknij przycisk **Connect** (Połącz). 

Następnie kliknij przycisk **Zamknij & Zastosuj** na wstążce Narzędzia **domowe,** aby zastosować zmiany.

## <a name="visualize-the-anomaly-detector-api-response"></a>Wizualizuj odpowiedź api detektora anomalii

Na głównym ekranie usługi Power BI zacznij używać zapytań utworzonych powyżej do wizualizacji danych. Najpierw wybierz **wykres liniowy** w **wizualizacjach**. Następnie dodaj sygnaturę czasową z wywoływatej funkcji do **osi**wykresu liniowego . Kliknij na niego prawym przyciskiem myszy i wybierz pozycję **Sygnatura czasowa**. 

![Kliknięcie prawym przyciskiem myszy wartości sygnatury czasowej](../media/tutorials/timestamp-right-click.png)

Do pola **Wartości** wykresu dodaj następujące pola z **funkcji wywoływane.** Skorzystaj z poniższego zrzutu ekranu, aby pomóc w budowaniu wykresu.

    * Wartość
    * Górnemarginy
    * DolneMarginy
    * Wartości oczekiwane

![Obraz nowego ekranu szybkiej miary](../media/tutorials/chart-settings.png)

Po dodaniu pól kliknij wykres i zmienić jego rozmiar, aby wyświetlić wszystkie punkty danych. Wykres będzie wyglądał podobnie do poniższego zrzutu ekranu:

![Obraz nowego ekranu szybkiej miary](../media/tutorials/chart-visualization.png)

### <a name="display-anomaly-data-points"></a>Wyświetlanie punktów danych anomalii

Po prawej stronie okna usługi Power BI poniżej okienka **POLA** kliknij prawym przyciskiem myszy **pozycję Wartość** w **obszarze Kwerenda funkcji wywoływanej,** a następnie kliknij pozycję **Nowa szybka miara**.

![Obraz nowego ekranu szybkiej miary](../media/tutorials/new-quick-measure.png)

Na wyświetlonym ekranie wybierz pozycję **Wartość filtrowana** jako obliczenie. Ustaw wartość `Sum of Value` **bazową** na . Następnie `IsAnomaly` przeciągnij z pól **Funkcja wywoływana** do **filtru**. Wybierz `True` z menu rozwijanego **Filtr.**

![Obraz nowego ekranu szybkiej miary](../media/tutorials/new-quick-measure-2.png)

Po kliknięciu `Value for True` **przycisku Ok,** na dole listy pól znajduje się pole. Kliknij go prawym przyciskiem myszy i zmień jego nazwę na **Anomaly**. Dodaj go do **wartości**wykresu . Następnie wybierz narzędzie **Format i** ustaw typ osi X na **Kategoryczny**.

![Obraz nowego ekranu szybkiej miary](../media/tutorials/format-x-axis.png)

Zastosuj kolory do wykresu, klikając narzędzie **Formatowanie** i **kolory danych**. Wykres powinien wyglądać następująco:

![Obraz nowego ekranu szybkiej miary](../media/tutorials/final-chart.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
>[Wykrywanie anomalii przesyłania strumieniowego za pomocą usługi Azure Databricks](anomaly-detection-streaming-databricks.md)
