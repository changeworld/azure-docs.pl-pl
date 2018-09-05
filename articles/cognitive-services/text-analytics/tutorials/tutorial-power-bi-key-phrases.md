---
title: Analiza tekstu przy użyciu usługi Power BI — Azure Cognitive Services | Microsoft Docs
description: Dowiedz się, jak za pomocą funkcji analizy tekstu wyodrębniać frazy kluczowe z tekstu w usłudze Power BI.
services: cognitive-services
author: luiscabrer
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: tutorial
ms.date: 3/07/2018
ms.author: luisca
ms.openlocfilehash: 2cdb93d44218627efdcb0360d8cf4a4eeeca177a
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/14/2018
ms.locfileid: "42889321"
---
# <a name="text-analytics-with-power-bi"></a>Analiza tekstu przy użyciu usługi Power BI

Usługa Microsoft Power BI przetwarza dane organizacji w atrakcyjne raporty, które można rozpowszechniać w całej organizacji, aby szybciej uzyskiwać bardziej szczegółowe informacje. Usługa analizy tekstu, składnik usług Cognitive Services na platformie Microsoft Azure, umożliwia wyodrębnianie najważniejszych fraz z tekstu za pośrednictwem interfejsu API fraz kluczowych. Używając tych narzędzi razem, można szybko zorientować się, o czym mówią klienci i jakie są ich opinie.

W ramach tego samouczka możesz dowiedzieć się, jak zintegrować program Power BI Desktop z interfejsem API fraz kluczowych, aby przy użyciu niestandardowej funkcji dodatku Power Query wyodrębnić najważniejsze frazy z opinii klientów. Z tych fraz utworzymy również chmurę słów.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

> [!div class="checklist"]
> * Program Microsoft Power BI Desktop. [Pobierz bezpłatnie](https://powerbi.microsoft.com/get-started/).
> * Konto platformy Microsoft Azure. [Zacznij korzystać z bezpłatnej wersji próbnej](https://azure.microsoft.com/free/) lub [zaloguj się](https://portal.azure.com/).
> * Klucz dostępu do funkcji analizy tekstu. [Utwórz konto](../../cognitive-services-apis-create-account.md), a następnie [uzyskaj klucz](../how-tos/text-analytics-how-to-access-key.md).
> * Komentarze klientów. [Pobierz nasze przykładowe dane](https://aka.ms/cogsvc/ta) lub użyj własnych.

## <a name="loading-customer-data"></a>Ładowanie danych klientów

Aby rozpocząć, otwórz program Power BI Desktop i załaduj plik CSV **FabrikamComments.csv**. Ten plik zawiera hipotetyczną aktywność z jednego dnia na forum pomocy technicznej fikcyjnej małej firmy.

> [!NOTE]
> Usługa Power BI może używać danych z wielu różnych źródeł, takich jak Facebook czy baza danych SQL. Więcej informacji można znaleźć na stronach [Integracja usługi Facebook z usługą Power BI](https://powerbi.microsoft.com/integrations/facebook/) i [Integracja programu SQL Server z usługą Power BI](https://powerbi.microsoft.com/integrations/sql-server/).

W głównym oknie programu Power BI Desktop znajdź grupę Dane zewnętrzne na wstążce Narzędzia główne. Wybierz pozycję **Plik tekstowy lub CSV** z menu rozwijanego **Pobierz dane** w tej grupie.

![[Przycisk Pobierz dane]](../media/tutorials/power-bi/get-data-button.png)

Zostanie wyświetlone okno dialogowe Otwieranie. Przejdź do folderu Pobrane lub dowolnego innego folderu zawierającego przykładowy plik danych. Kliknij pozycję `FabrikamComments.csv`, a następnie przycisk **Otwórz**. Zostanie wyświetlone okno dialogowe importowania pliku CSV.

![[Okno dialogowe importowania pliku CSV]](../media/tutorials/power-bi/csv-import.png)

W oknie dialogowym importowania pliku CSV można sprawdzić, czy program Power BI Desktop poprawnie wykrył zestaw znaków, ogranicznik, wiersze nagłówka i typy kolumn. Wszystkie te informacje są poprawne, dlatego klikniemy pozycję **Załaduj**.

Aby wyświetlić załadowane dane, przełącz na widok danych, używając przycisku Widok danych przy lewej krawędzi obszaru roboczego usługi Power BI. Zostanie otwarta tabela zawierająca nasze dane, podobnie jak w programie Microsoft Excel.

![[Widok początkowy zaimportowanych danych]](../media/tutorials/power-bi/initial-data-view.png)

## <a name="preparing-the-data"></a>Przygotowywanie danych

Zanim dane będą gotowe do przetworzenia przez usługę fraz kluczowych, może być konieczne ich przekształcenie w programie Power BI Desktop.

Na przykład nasze dane zawierają zarówno pole `subject`, jak i pole `comment`. Podczas wyodrębniania fraz kluczowych powinniśmy wziąć pod uwagę tekst w obu tych polach, a nie tylko w polu `comment`. Funkcja scalania kolumn w programie Power BI Desktop ułatwia to zadanie.

W głównym oknie programu Power BI Desktop otwórz Edytor zapytań, klikając pozycję **Edytuj zapytania** w grupie Dane zewnętrzne na wstążce Narzędzia główne. 

![[Grupa Dane zewnętrzne na wstążce Narzędzia główne]](../media/tutorials/power-bi/edit-queries.png)

Na liście zapytań po lewej stronie okna wybierz pozycję „FabrikamComments”, jeśli nie została jeszcze wybrana.

Teraz zaznacz obie kolumny (`subject` i `comment`) w tabeli. W celu wyświetlenia tych kolumn może być konieczne przewinięcie w poziomie. Najpierw kliknij nagłówek kolumny `subject`, a następnie przytrzymaj klawisz Control i kliknij nagłówek kolumny `comment`.

![[Zaznaczanie pól do scalenia]](../media/tutorials/power-bi/select-columns.png)

W grupie Kolumny tekstowe na wstążce Przekształć kliknij pozycję **Scal kolumny**. Zostanie wyświetlone okno dialogowe Scal kolumny.

![[Scalanie pól za pomocą okna dialogowego Scal kolumny]](../media/tutorials/power-bi/merge-columns.png)

W oknie dialogowym Scal kolumny wybierz Tabulator jako separator, a następnie kliknij przycisk **OK**. Gotowe!

Możesz również rozważyć odfiltrowanie pustych wiadomości, używając filtru Usuń puste lub usuwając znaki niedrukowalne przy użyciu przekształcenia Wyczyść. Jeśli dane zawierają kolumnę podobną do kolumny `spamscore` w naszym przykładowym pliku, możesz pominąć komentarze „spam”, używając filtru Liczba.

## <a name="understanding-the-api"></a>Opis interfejsu API

Interfejs API fraz kluczowych może przetworzyć nawet tysiąc dokumentów tekstowych w ramach jednego żądania HTTP. Usługa Power BI preferuje obsługę pojedynczych rekordów po kolei, dlatego nasze wywołania interfejsu API będą zawierać tylko jeden dokument. Dla każdego przetwarzanego dokumentu [interfejs API](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) wymaga poniższych pól.

| | |
| - | - |
| `id`  | Unikatowy identyfikator dla tego dokumentu w żądaniu. Odpowiedź również zawiera to pole. Dzięki temu podczas przetwarzania wielu dokumentów można łatwo skojarzyć wyodrębnione frazy kluczowe z dokumentem, z którego pochodzą. My przetwarzamy jeden dokument w ramach każdego żądania, dlatego już wiemy, z jakim dokumentem jest skojarzona odpowiedź, a pole `id` może być takie samo w każdym żądaniu.|
| `text`  | Tekst do przetworzenia. Pobieramy go z kolumny `Merged`, którą utworzyliśmy, zawierającej połączony tekst z wiersza tematu i komentarza. Interfejs API fraz kluczowych wymaga, aby te dane nie były dłuższe niż około 5000 znaków.|
| `language` | Kod reprezentujący język, w jakim jest napisany dokument. Wszystkie nasze wiadomości są w języku angielskim, dlatego w naszym zapytaniu możemy trwale zakodować język `en`.|

Aby przesłać te pola do interfejsu API fraz kluczowych, musimy połączyć je w dokument JSON (JavaScript Object Notation). Odpowiedź z tego żądania także jest dokumentem JSON, który musimy przeanalizować, aby wyłowić frazy kluczowe.

## <a name="creating-a-custom-function"></a>Tworzenie funkcji niestandardowej

Teraz możemy przystąpić do tworzenia funkcji niestandardowej, która zintegruje usługę Power BI z funkcją analizy tekstu. Program Power BI Desktop wywołuje funkcję dla każdego wiersza tabeli i tworzy nową kolumnę zawierającą wyniki.

Funkcja otrzymuje tekst do przetworzenia w formie parametru. Konwertuje ona dane na i z wymaganego dokumentu JavaScript Object Notation (JSON), a następnie wysyła żądanie HTTP do punktu końcowego interfejsu API fraz kluczowych. Po przeanalizowaniu odpowiedzi funkcja zwraca ciąg zawierający rozdzielaną przecinkami listę wyodrębnionych fraz kluczowych.

> [!NOTE]
> Funkcje niestandardowe programu Power BI Desktop pisze się w [języku formuł Power Query M](https://msdn.microsoft.com/library/mt211003.aspx), w skrócie „M”. M to funkcjonalny język programowania oparty na języku [F#](https://docs.microsoft.com/dotnet/fsharp/). Jednak nie trzeba być programistą, aby ukończyć ten samouczek. Wymagany kod znajduje się poniżej.

Nadal powinno być otwarte okno Edytora zapytań. Na wstążce Narzędzia główne kliknij pozycję **Nowe źródło** (w grupie Nowe zapytanie) i z menu rozwijanego wybierz pozycję **Puste zapytanie**. 

Na liście Zapytania zostanie wyświetlone nowe zapytanie, początkowo noszące nazwę Zapytanie1. Kliknij dwukrotnie ten wpis i nadaj mu nazwę `KeyPhrases`.

Teraz otwórz okno Edytora zaawansowanego, klikając pozycję **Edytor zaawansowany** w grupie Zapytanie na wstążce Narzędzia główne. Usuń kod, który znajduje się już w tym oknie, a następnie wklej poniższy kod. 

> [!NOTE]
> W poniższym przykładzie zakładamy, że punkt końcowy znajduje się pod adresem https://westus.api.cognitive.microsoft.com.  Funkcja analizy tekstu obsługuje tworzenie subskrypcji w 13 różnych regionach. Jeśli utworzysz konto usługi w innym regionie, upewnij się, że używasz punktu końcowego dla wybranego regionu. Powinien on być wyświetlany na stronie Przegląd w witrynie Azure Portal po wybraniu subskrypcji funkcji analizy tekstu.

```fsharp
// Returns key phrases from the text in a comma-separated list
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    keyphrases  = Text.Lower(Text.Combine(jsonresp[documents]{0}[keyPhrases], ", "))
in  keyphrases
```

W wierszu zaczynającym się od `apikey` wklej także swój klucz interfejsu API analizy tekstu uzyskany na pulpicie nawigacyjnym platformy Microsoft Azure. (Przed i za kluczem zostaw cudzysłowy). Następnie kliknij przycisk **Gotowe**.

## <a name="using-the-function"></a>Korzystanie z funkcji

Teraz możemy użyć funkcji niestandardowej, aby uzyskać frazy kluczowe zawarte w poszczególnych komentarzach naszych klientów i zapisać je w nowej kolumnie w tabeli. 

Pozostając w Edytorze zapytań, wróć do zapytania FabrikamComments, przejdź do wstążki Dodaj kolumnę i kliknij przycisk **Wywołaj funkcję niestandardową** w grupie Ogólne.

![[Przycisk Wywołaj funkcję niestandardową]](../media/tutorials/power-bi/invoke-custom-function-button.png)<br><br>

W oknie dialogowym Wywołaj funkcję niestandardową wprowadź `keyphrases` jako nazwę nowej kolumny. Wybierz naszą funkcję niestandardową `KeyPhrases` jako zapytanie funkcji. 

W oknie dialogowym zostanie wyświetlone nowe pole z pytaniem, które pole chcemy przekazać do naszej funkcji jako parametr `text`. Z menu rozwijanego wybierz pozycję `Merged` (kolumnę, którą utworzyliśmy wcześniej przez scalenie pól tematu i treści wiadomości).

![[Wywoływanie funkcji niestandardowej]](../media/tutorials/power-bi/invoke-custom-function.png)

Na koniec kliknij przycisk **OK**.

Jeśli wszystko jest gotowe, usługa Power BI wywołuje naszą funkcję raz dla każdego wiersza w tabeli, wykonując zapytania fraz kluczowych i dodając nową kolumnę do tabeli. Jednak zanim tak się stanie, może być konieczne określenie ustawień uwierzytelniania i prywatności.

## <a name="authentication-and-privacy"></a>Uwierzytelnianie i prywatność

Po zamknięciu okna dialogowego Wywołaj funkcję niestandardową może zostać wyświetlony transparent z prośbą o określenie sposobu łączenia z punktem końcowym fraz kluczowych.

![[transparent poświadczeń]](../media/tutorials/power-bi/credentials-banner.png)

Kliknij pozycję **Edytuj poświadczenia**, upewnij się, że w oknie dialogowym jest wybrana opcja Anonimowe, a następnie kliknij pozycję **Połącz**. 

> [!NOTE]
> Dlaczego anonimowe? Usługa analizy tekstu uwierzytelnia Cię przy użyciu klucza interfejsu API, dzięki czemu usługa Power BI nie musi podawać poświadczeń dla samego żądania HTTP.

![[ustawianie uwierzytelniania anonimowego]](../media/tutorials/power-bi/access-web-content.png)

Jeśli nawet po wybraniu dostępu anonimowego jest wyświetlany transparent Edytuj poświadczenia, być może nie został wklejony klucz interfejsu API. Sprawdź funkcję niestandardową `KeyPhrases` w Edytorze zaawansowanym, aby upewnić się, że jest tam klucz.

Następnie może zostać wyświetlony transparent z prośbą o podanie informacji na temat ochrony prywatności źródeł danych. 

![[transparent ochrony prywatności]](../media/tutorials/power-bi/privacy-banner.png)

Kliknij przycisk **Kontynuuj** i dla każdego źródła danych w oknie dialogowym wybierz pozycję Publiczne. Następnie kliknij przycisk **Zapisz**.

![[ustawianie prywatności źródła danych]](../media/tutorials/power-bi/privacy-dialog.png)

## <a name="creating-the-word-cloud"></a>Tworzenie chmury słów

Po uporaniu się ze wszystkimi wyświetlanymi transparentami kliknij pozycję **Zamknij i zastosuj** na wstążce Narzędzia główne, aby zamknąć Edytor zapytań.

Wykonanie niezbędnych żądań HTTP przez program Power BI Desktop potrwa chwilę. W przypadku każdego wiersza w tabeli nowa kolumna `keyphrases` zawiera frazy kluczowe wykryte w tekście przez interfejs API fraz kluczowych. 

Korzystając z tej kolumny, wygenerujmy chmurę słów. Aby rozpocząć, kliknij przycisk Raport w głównym oknie programu Power BI Desktop z lewej strony obszaru roboczego.

> [!NOTE]
> Dlaczego lepiej wygenerować chmurę słów przy użyciu wyodrębnionych fraz kluczowych, a nie pełnego tekstu poszczególnych komentarzy? Frazy kluczowe dostarczają nam *ważnych* słów z komentarzy naszych klientów, a nie po prostu *najpopularniejszych* słów. Ponadto liczba słów w chmurze wynikowej nie jest zniekształcona przez częste użycie słowa w stosunkowo małej liczbie komentarzy.

Jeśli nie masz jeszcze zainstalowanej wizualizacji niestandardowej Word Cloud, zainstaluj ją. W panelu Wizualizacje po prawej stronie obszaru roboczego kliknij przycisk z wielokropkiem (**...**) i wybierz pozycję **Zaimportuj ze sklepu**. Następnie wyszukaj słowo „cloud” i kliknij przycisk **Dodaj** obok wizualizacji Word Cloud. Usługa Power BI zainstaluje wizualizację Word Cloud i poinformuje cię o jej pomyślnym zainstalowaniu.

![[dodawanie wizualizacji niestandardowej]](../media/tutorials/power-bi/add-custom-visuals.png)<br><br>

Teraz stwórzmy naszą chmurę słów!

![[Ikona Word Cloud w panelu wizualizacji]](../media/tutorials/power-bi/visualizations-panel.png)

Najpierw kliknij ikonę Word Cloud w panelu Wizualizacje. W obszarze roboczym zostanie wyświetlony nowy raport. Przeciągnij pole `keyphrases` z panelu Pola do pola Kategoria w panelu Wizualizacje. Wewnątrz raportu pojawi się chmura słów.

Teraz przełącz na stronę Format panelu Wizualizacje. W kategorii Słowa ignorowane włącz opcję **Domyślne słowa ignorowane**, aby wyeliminować z chmury krótkie, popularne słowa, takie jak „z”. 

![[aktywowanie domyślnych słów ignorowanych]](../media/tutorials/power-bi/default-stop-words.png)

Przejdź nieco niżej w tym panelu, a następnie wyłącz opcje **Obróć tekst** i **Tytuł**.

![[aktywowanie trybu koncentracji uwagi]](../media/tutorials/power-bi/word-cloud-focus-mode.png)

Kliknij narzędzie trybu koncentracji uwagi w raporcie, aby uzyskać lepszy widok na naszą chmurę słów. Narzędzie rozwija chmurę słów, aby wypełnić cały obszar roboczy, jak pokazano poniżej.

![[Word Cloud]](../media/tutorials/power-bi/word-cloud.png)

## <a name="more-text-analytics-services"></a>Dodatkowe usługi analizy tekstu

Usługa analizy tekstu, jedna z usług Cognitive Services oferowanych na platformie Microsoft Azure, umożliwia również analizę opinii i wykrywanie języka. Wykrywanie języka jest szczególnie przydatne, gdy opinia klienta nie jest w języku angielskim.

Oba te interfejsy API są bardzo podobne do interfejsu API fraz kluczowych. Dzięki temu można zintegrować je z programem Power BI Desktop, używając niemal identycznych funkcji niestandardowych. Wystarczy jak poprzednio utworzyć puste zapytanie i wkleić do Edytora zaawansowanego odpowiedni kod podany poniżej. (Nie zapomnij o kluczu dostępu!) Następnie, tak jak wcześniej, użyj funkcji dodawania nowej kolumny do tabeli.

Poniższa funkcja analizy opinii zwraca wynik informujący o tym, na ile pozytywna jest opinia wyrażona w tekście.

```fsharp
// Returns the sentiment score of the text, from 0.0 (least favorable) to 1.0 (most favorable)
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    sentiment   = jsonresp[documents]{0}[score]
in  sentiment
```

Poniżej przedstawiono dwie wersje funkcji wykrywania języka. Pierwsza zwraca kod języka ISO (np. `en` w przypadku języka angielskiego), podczas gdy druga zwraca „przyjazną” nazwę (np. `English`). Można zauważyć, że obie wersje różnią się tylko ostatnim wierszem treści.

```fsharp
// Returns the two-letter language code (e.g. en for English) of the text
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    language    = jsonresp[documents]{0}[detectedLanguages]{0}[iso6391Name]
in  language
```
```fsharp
// Returns the name (e.g. English) of the language in which the text is written
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    language    = jsonresp[documents]{0}[detectedLanguages]{0}[name]
in  language
```

Na koniec przedstawiamy wariant już omówionej funkcji fraz kluczowych, który zwraca frazy w postaci obiektu listy, a nie pojedynczego ciągu fraz rozdzielonych przecinkami. 

> [!NOTE]
> Zwracanie pojedynczego ciągu miało na celu uproszczenie naszego przykładu chmury słów. Jednak lista jest bardziej elastycznym formatem do pracy ze zwracanymi frazami w usłudze Power BI. Obiektami listy w programie Power BI Desktop można manipulować przy użyciu grupy Kolumna strukturalna na wstążce Przekształć Edytora zapytań.

```fsharp
// Returns key phrases from the text as a list object
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    keyphrases  = jsonresp[documents]{0}[keyPhrases]
in  keyphrases
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat usługi analizy tekstu, języka formuł Power Query M lub usługi Power BI.

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API analizy tekstu](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)

> [!div class="nextstepaction"]
> [Dokumentacja języka Power Query M](//msdn.microsoft.com/library/mt211003.aspx)

> [!div class="nextstepaction"]
> [Dokumentacja usługi Power BI](//powerbi.microsoft.com/documentation/powerbi-landing-page/)
