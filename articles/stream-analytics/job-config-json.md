---
title: Pola Usługi Azure Stream Analytics JobConfig.json
description: W tym artykule wymieniono obsługiwane pola pliku JobConfig.json usługi Azure Stream Analytics używanego do tworzenia zadań w programie Visual Studio Code.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 27ed553035ce9d7abf57ffe93078df9c17b8408c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617959"
---
# <a name="azure-stream-analytics-jobconfigjson-fields"></a>Pola Usługi Azure Stream Analytics JobConfig.json

Poniższe pola są obsługiwane w pliku *JobConfig.json* używanym do [tworzenia zadania usługi Azure Stream Analytics przy użyciu programu Visual Studio Code.](quick-create-vs-code.md)

```json
{
    "DataLocale": "string",
    "OutputErrorPolicy": "string",
    "EventsLateArrivalMaxDelayInSeconds": "integer",
    "EventsOutOfOrderMaxDelayInSeconds": "integer",
    "EventsOutOfOrderPolicy": "string",
    "StreamingUnits": "integer",
    "CompatibilityLevel": "string",
    "UseSystemAssignedIdentity": "boolean",
    "GlobalStorage": {
        "AccountName": "string",
        "AccountKey": "string",
    },
    "DataSourceCredentialDomain": "string",
    "ScriptType": "string",
    "Tags": {}
}
```

|Nazwa|Typ|Wymagany|Wartość|
|----|----|--------|-----|
|DataLocale (DataLocale)|ciąg|Nie|Ustawienia regionalne danych zadania analizy strumienia. Wartość powinna być nazwą obsługiwanej. Domyślnie wartość 'en-US', jeśli nie określono żadnego.|
|ProdukcjaErrorPolicy|ciąg|Nie|Wskazuje zasady, które mają być stosowane do zdarzeń, które docierają do danych wyjściowych i nie mogą być zapisywane w magazynie zewnętrznym z powodu nieprawidłowego sformułowania (brak wartości kolumn, wartości kolumn o niewłaściwym typie lub rozmiarze). - Zatrzymaj się lub upuść|
|WydarzeniaLateArrivalMaxDelayInSekundy|liczba całkowita|Nie|Maksymalne dopuszczalne opóźnienie w sekundach, w których zdarzenia przybywające z opóźnieniem mogą być uwzględnione. Obsługiwany zakres wynosi od -1 do 1814399 (20.23:59:59 dni) i -1 służy do określania oczekiwania przez czas nieokreślony. Jeśli właściwość jest nieobecny, jest interpretowany mieć wartość -1.|
|WydarzeniaOutOfOrderMaxDelayInSekundy|liczba całkowita|Nie|Maksymalne dopuszczalne opóźnienie w sekundach, w których zdarzenia poza kolejnością można dostosować tak, aby były w porządku.|
|WydarzeniaOutOfOrderPolicy|ciąg|Nie|Wskazuje zasady, które mają być stosowane do zdarzeń, które przychodzą poza kolejnością w strumieniu zdarzeń wejściowych. - Regulacja lub upuść|
|Jednostki przesyłania strumieniowego|liczba całkowita|Tak|Określa liczbę jednostek przesyłania strumieniowego używanych przez zadanie przesyłania strumieniowego.|
|Compatibilitylevel|ciąg|Nie|Steruje niektórymi zachowaniami środowiska wykonawczego zadania przesyłania strumieniowego. - Dopuszczalne wartości to "1.0", "1.1", "1.2"|
|Użyj przypisanej nieidentycyjności systemu|wartość logiczna|Nie|Ustaw true, aby włączyć to zadanie do komunikowania się z innymi usługami platformy Azure jako sam przy użyciu tożsamości zarządzanej usługi Azure Active Directory.|
|GlobalStorage.AccountName|ciąg|Nie|Konto magazynu globalnego służy do przechowywania zawartości związanej z zadaniem analizy strumienia, takiej jak migawki danych referencyjnych SQL.|
|GlobalStorage.AccountKey|ciąg|Nie|Odpowiedni klucz dla globalnego konta magazynu.|
|DataSourceCredentialDomain|ciąg|Nie|Właściwość zarezerwowana dla magazynu lokalnego poświadczeń.|
|Scripttype|ciąg|Tak|Właściwość zarezerwowana do wskazanie typu tego pliku źródłowego. Dopuszczalna wartość to "JobConfig" dla JobConfig.json.|
|Tagi|Pary klucz-wartość JSON|Nie|Tagi to pary nazw i wartości, które umożliwiają kategoryzowanie zasobów i wyświetlanie skonsolidowanych rozliczeń przez zastosowanie tego samego tagu do wielu zasobów i grup zasobów. W nazwach tagów nie ma uwzględniania wielkości liter, a w przypadku wartości znaczników rozróżniana jest wielkość liter.|

## <a name="next-steps"></a>Następne kroki

* [Tworzenie zadania usługi Azure Stream Analytics w programie Visual Studio Code](quick-create-vs-code.md)
* [Testowanie zapytań usługi Stream Analytics lokalnie z przykładowymi danymi przy użyciu kodu programu Visual Studio](visual-studio-code-local-run.md)
* [Test usługi Stream Analytics zapytania lokalnie względem danych wejściowych strumienia na żywo przy użyciu programu Visual Studio Code Wdrażanie](visual-studio-code-local-run-live-input.md)
*[zadania usługi Azure Stream Analytics przy użyciu pakietu npm ciągłej/CIĄGŁOŚCI DYSKU CD](setup-cicd-vs-code.md)