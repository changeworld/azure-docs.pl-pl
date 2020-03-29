---
title: Wyodrębnianie informacji w programie Excel przy użyciu analizy tekstu i automatyzacji zasilania
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak wyodrębnić tekst programu Excel bez konieczności pisania kodu za pomocą analizy tekstu i automatyzacji zasilania.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 02/27/2019
ms.author: aahi
ms.openlocfilehash: fd70fe14d3765fb7c21b92f62b4d73564176baa2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78201189"
---
# <a name="extract-information-in-excel-using-text-analytics-and-power-automate"></a>Wyodrębnianie informacji w programie Excel przy użyciu analizy tekstu i automatyzacji zasilania 

W tym samouczku utworzysz przepływ automatyzacji zasilania, aby wyodrębnić tekst w arkuszu kalkulacyjnym programu Excel bez konieczności pisania kodu. 

Przepływ ten zajmie arkusz kalkulacyjny problemów zgłoszonych o kompleksie mieszkaniowym i klasyfikuje je do dwóch kategorii: hydraulika i inne. Będzie również wyodrębnić nazwiska i numery telefonów lokatorów, którzy je wysłali. Na koniec przepływ dołączy te informacje do arkusza programu Excel. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie przepływu za pomocą automatyzacji zasilania
> * Przekazywanie danych programu Excel z usługi OneDrive dla Firm
> * Wyodrębnianie tekstu z programu Excel i wysyłanie go do interfejsu API analizy tekstu 
> * Użyj informacji z interfejsu API, aby zaktualizować arkusz programu Excel.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Microsoft Azure. [Zacznij korzystać z bezpłatnej wersji próbnej](https://azure.microsoft.com/free/) lub [zaloguj się](https://portal.azure.com/).
- Zasób analizy tekstu. Jeśli go nie masz, możesz [go utworzyć w witrynie Azure portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) i użyć bezpłatnej warstwy, aby ukończyć ten samouczek.
- [Klucz i punkt końcowy,](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) który został wygenerowany podczas rejestracji.
- Arkusz kalkulacyjny zawierający problemy z dzierżawą. Przykładowe dane są dostarczane w usłudze GitHub
- Office 365 z usłudze OneDrive dla Firm.

## <a name="add-the-excel-file-to-onedrive-for-business"></a>Dodawanie pliku programu Excel do usługi OneDrive dla Firm

Pobierz przykładowy plik programu Excel z [gitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/TextAnalytics/sample-data/ReportedIssues.xlsx). Ten plik musi być przechowywany na koncie usługi OneDrive dla Firm.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/example-data.png" alt-text="Przykłady z pliku programu Excel.":::

Problemy są zgłaszane w tekście nieprzetworzonym. Użyjemy rozpoznawania nazwanych jednostek interfejsu API analizy tekstu, aby wyodrębnić nazwę osoby i numer telefonu. Następnie przepływ będzie szukać słowa "hydraulika" w opisie, aby skategoryzować problemy. 

## <a name="create-a-new-power-automate-workflow"></a>Tworzenie nowego przepływu pracy Automatyzacji zasilania

Przejdź do [witryny Power Automate](https://preview.flow.microsoft.com/)i zaloguj się. Następnie kliknij przycisk **Utwórz** i **zaplanowano przepływ**.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/flow-creation.png" alt-text="Ekran tworzenia przepływu.":::


Na stronie **Tworzenie zaplanowanego przepływu** zaiwniaj przepływ za pomocą następujących pól:

|Pole |Wartość  |
|---------|---------|
|**Nazwa przepływu**     | **Zaplanowany przegląd** lub inna nazwa.         |
|**Zaczynając**     |  Wprowadź bieżącą datę i godzinę.       |
|**Powtarzaj co**     | **1 godzina**        |

## <a name="add-variables-to-the-flow"></a>Dodawanie zmiennych do przepływu

> [!NOTE]
> Jeśli chcesz zobaczyć obraz ukończonego przepływu, możesz go pobrać z [GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/TextAnalytics/flow-diagrams). 

Utwórz zmienne reprezentujące informacje, które zostaną dodane do pliku programu Excel. Kliknij **pozycję Nowy krok** i wyszukaj opcję **Inicjuj zmienną**. Zrób to cztery razy, aby utworzyć cztery zmienne.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/initialize-variables.png" alt-text="Inicjuj zmienne.":::

Dodaj następujące informacje do utworzonych zmiennych. Reprezentują one kolumny pliku programu Excel. Jeśli jakieś zmienne są zwinięte, możesz je kliknąć, aby je rozwinąć.

| Akcja |Nazwa   | Typ | Wartość |
|---------|---------|---|---|
| Inicjowanie zmiennej | var_person | Ciąg | Person (Osoba) |
| Inicjowanie zmiennej 2 | var_phone | Ciąg | Phone_Number |
| Inicjowanie zmiennej 3 | var_plumbing | Ciąg | Hydraulika |
| Inicjowanie zmiennej 4 | var_other | Ciąg | inne | 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/flow-variables.png" alt-text="informacje zawarte w zmiennych przepływu":::

## <a name="read-the-excel-file"></a>Odczytywanie pliku programu Excel

Kliknij **pozycję Nowy krok** i wpisz program **Excel**, a następnie wybierz pozycję **Lista wierszy znajdujących się w tabeli** z listy akcji.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/list-excel-rows.png" alt-text="dodawanie wierszy programu Excel.":::

Dodaj plik programu Excel do przepływu, wypełniając pola w tej akcji. Ten samouczek wymaga, aby plik został przekazany do usługi OneDrive dla Firm.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/list-excel-rows-options.png" alt-text="dodawanie wierszy programu Excel.":::

Kliknij **pozycję Nowy krok** i dodaj zastosuj do **każdej** akcji.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action.png" alt-text="dodać polecenie zastosuj.":::

Kliknij **wybierz dane wyjściowe z poprzedniego kroku**. W wyświetlonym polu Zawartość dynamiczna wybierz **pozycję Wartość**.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/select-output.png" alt-text="Wybierz dane wyjściowe z pliku programu Excel.":::

## <a name="send-a-request-to-the-text-analytics-api"></a>Wysyłanie żądania do interfejsu API analizy tekstu

Jeśli jeszcze tego nie zrobiłeś, musisz utworzyć [zasób analizy tekstu](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) w witrynie Azure portal.

### <a name="create-a-text-analytics-connection"></a>Tworzenie połączenia analizy tekstu

W przycisku **Zastosuj do każdego**z nich kliknij pozycję Dodaj **akcję**. Przejdź do strony **klucza i punktu końcowego zasobu** analizy tekstu w witrynie Azure portal i uzyskaj klucz i punkt końcowy zasobu analizy tekstu.

W przepływie wprowadź następujące informacje, aby utworzyć nowe połączenie analizy tekstu.

> [!NOTE]
> Jeśli utworzono już połączenie analizy tekstu i chcesz zmienić szczegóły połączenia, kliknij wielokropek w prawym górnym rogu i kliknij **+ Dodaj nowe połączenie**.

| Pole           | Wartość                                                                                                             |
|-----------------|-------------------------------------------------------------------------------------------------------------------|
| Nazwa połączenia | Nazwa połączenia z zasobem analizy tekstu. Na przykład `TAforPowerAutomate`. |
| Klucz konta     | Klucz do zasobu analizy tekstu.                                                                                   |
| Adres URL witryny        | Punkt końcowy zasobu analizy tekstu.                                                       |

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-credentials.png" alt-text="Dodaj poświadczenia analizy tekstu do przepływu.":::

## <a name="extract-the-excel-content"></a>Wyodrębnianie zawartości programu Excel 

Po utworzeniu połączenia wyszukaj analizę **tekstu** i wybierz pozycję **Jednostki**. Spowoduje to wyodrębnić informacje z kolumny opisu problemu.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/extract-info.png" alt-text="Dodaj poświadczenia analizy tekstu do przepływu.":::

Kliknij pole **Tekst** i wybierz pozycję **Opis** z wyświetlonych okien zawartości dynamicznej. Wprowadź `en` język. (Kliknij pozycję Pokaż opcje zaawansowane, jeśli nie widzisz języka)

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/description-from-dynamic-content.png" alt-text="Dodaj poświadczenia analizy tekstu do przepływu.":::


## <a name="extract-the-person-name"></a>Wyodrębnianie imienia i nazwiska osoby

Następnie znajdziemy typ jednostki osoby w danych wyjściowych analizy tekstu. W obrębie **przycisku Zastosuj do każdej**z nich kliknij pozycję Dodaj akcję i utwórz **kolejną** **pozycję Zastosuj do każdej** akcji. Kliknij wewnątrz pola tekstowego i wybierz **pozycję Elementy** w wyświetlonym oknie Zawartość dynamiczna.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-2.png" alt-text="Dodaj poświadczenia analizy tekstu do przepływu.":::

W nowo utworzonej akcji **Zastosuj do każdej 2** kliknij pozycję **Dodaj akcję**i dodaj kontrolkę **Warunek.**

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/create-condition.png" alt-text="Dodaj poświadczenia analizy tekstu do przepływu.":::

W oknie Warunek kliknij pierwsze pole tekstowe. W oknie Zawartość dynamiczna wyszukaj **typ jednostek** i wybierz je.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/choose-entities-value.png" alt-text="Dodaj poświadczenia analizy tekstu do przepływu.":::

Upewnij się, że drugie pole **jest równe**. Następnie zaznacz trzecie pole i `var_person` wyszukaj je w oknie Zawartość dynamiczna. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/choose-variable-value.png" alt-text="Dodaj poświadczenia analizy tekstu do przepływu.":::

W warunku **Jeśli tak** wpisz w programie Excel, a następnie wybierz pozycję **Aktualizuj wiersz**.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/yes-column-action.png" alt-text="Dodaj poświadczenia analizy tekstu do przepływu.":::

Wprowadź informacje programu Excel i zaktualizuj pola **Kolumna klucza,** **Wartość klucza** i **Nazwa osoby.** Spowoduje to dołączenie nazwy wykrytej przez interfejs API do arkusza programu Excel. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/yes-column-action-options.png" alt-text="Dodaj poświadczenia analizy tekstu do przepływu.":::

## <a name="get-the-phone-number"></a>Uzyskaj numer telefonu

Zminimalizuj zastosuj do każdej akcji **2,** klikając nazwę. Następnie dodaj kolejny **Zastosuj do każdej** akcji, jak wcześniej. zostanie nazwany **Zastosuj do każdego 3**. Zaznacz pole tekstowe i dodaj encje jako dane **wyjściowe** dla tej akcji. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-3.png" alt-text="Dodaj poświadczenia analizy tekstu do przepływu.":::

W obrębie **Zastosuj do każdego 3**dodaj kontrolkę **Condition.** Będzie on nazwany **Stan 2**. W pierwszym polu tekstowym wyszukaj i dodaj **typ jednostek** z okna Zawartość dynamiczna. Upewnij się, że pole środkowe **jest równe**. Następnie w prawym polu tekstowym wprowadź plik `var_phone`. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-2-options.png" alt-text="Dodaj poświadczenia analizy tekstu do przepływu.":::

W warunek **Jeśli tak,** dodaj akcję **Aktualizuj wiersz.** Następnie wprowadź informacje, tak jak zrobiliśmy to powyżej, w kolumnie numery telefonów arkusza Excel. Spowoduje to dołączenie numeru telefonu wykrytego przez interfejs API do arkusza programu Excel. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-2-yes-column.png" alt-text="Dodaj poświadczenia analizy tekstu do przepływu.":::


## <a name="get-the-plumbing-issues"></a>Uzyskaj problemy z instalacją wodno-kanalizacyjną

Minimalizuj **Zastosuj do każdego 3,** klikając nazwę. Następnie utwórz inny **Zastosuj do każdego** w akcji nadrzędnej. Zaznacz pole tekstowe i dodaj elementy jako dane **wyjściowe** dla tej akcji z okna Zawartość dynamiczna. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-4.png" alt-text="Dodaj poświadczenia analizy tekstu do przepływu.":::


Następnie przepływ sprawdzi, czy opis problemu z wiersza tabeli programu Excel zawiera słowo "hydraulika". Jeśli tak, doda "hydraulika" w kolumnie IssueType. Jeśli nie, wprowadzimy "inne".

Wewnątrz Zastosuj do każdej akcji **4,** dodaj **formant condition.** Będzie on nazwany **Stan 3**. W pierwszym polu tekstowym wyszukaj i dodaj **opis** z pliku programu Excel, używając okna Zawartość dynamiczna. Upewnij się, że pole środkowe **zawiera**. Następnie w prawym polu tekstowym `var_plumbing`znajdź i wybierz pozycję . 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-3-options.png" alt-text="Dodaj poświadczenia analizy tekstu do przepływu.":::


W warunku **Jeśli tak** kliknij pozycję **Dodaj akcję**i wybierz pozycję Aktualizuj **wiersz**. Następnie wprowadź informacje, jak wcześniej. W kolumnie Typ `var_plumbing`problemu wybierz pozycję . Spowoduje to zastosowanie etykiety "hydraulika" do wiersza.

W obszarze **Jeśli nie ma** warunku, kliknij pozycję Dodaj **akcję**i wybierz pozycję **Aktualizuj wiersz**. Następnie wprowadź informacje, jak wcześniej. W kolumnie Typ `var_other`problemu wybierz pozycję . Spowoduje to zastosowanie etykiety "inne" do wiersza.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/plumbing-issue-condition.png" alt-text="Dodaj poświadczenia analizy tekstu do przepływu.":::

## <a name="test-the-workflow"></a>Testowanie przepływu pracy

W prawym górnym rogu ekranu kliknij pozycję **Zapisz**, a następnie **przetestuj**. Wybierz **pozycję Wykonam akcję wyzwalacza**. Kliknij **pozycję Zapisz & testu**, Uruchom **przepływ**, a następnie **gotowe**.

Plik programu Excel zostanie zaktualizowany na koncie usługi OneDrive. Będzie to wyglądać jak poniżej.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/updated-excel-sheet.png" alt-text="Zaktualizowany arkusz kalkulacyjny programu Excel.":::

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Poznaj więcej rozwiązań](../text-analytics-user-scenarios.md)
