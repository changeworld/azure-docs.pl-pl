---
title: 'Szybki start: testowanie bazy wiedzy z pytaniami wsadowych'
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/08/2020
ms.author: diberry
ms.openlocfilehash: e16166c741b99c1af5b36f2c7ccd25b01f7544ba
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77108995"
---
# <a name="quickstart-test-knowledge-base-with-batch-questions-and-expected-answers"></a>Szybki start: testowanie bazy wiedzy z pytaniami wsadowymi i oczekiwanymi odpowiedziami

Narzędzie do testowania wsadowego programu QnA Maker umożliwia testowanie baz wiedzy w zasobie programu QnA Maker pod kątem oczekiwanych odpowiedzi, wyników zaufania i monitów z wieloma turami.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Utwórz [usługę QnA Maker](create-publish-knowledge-base.md) lub użyj istniejącej usługi, która używa języka angielskiego.
* Pobierz [przykładowy `.docx` plik z wieloma kolejkami](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)
* Pobierz [narzędzie do testowania wsadowego](https://aka.ms/qnamakerbatchtestingtool) `.zip` , wyodrębnij plik wykonywalny z pliku.

## <a name="sign-into-qna-maker-portal"></a>Zaloguj się do portalu QnA Maker

[Zaloguj się](https://www.qnamaker.ai/) do portalu QnA Maker.

## <a name="create-a-new-knowledge-base-from-the-multi-turn-sampledocx-file"></a>Tworzenie nowej bazy wiedzy z pliku sample.docx z wieloma kolejkami

1. Wybierz **pozycję Utwórz bazę wiedzy** na pasku narzędzi.
1. Pomiń **krok 1,** ponieważ powinieneś już mieć zasób QnA Maker, przechodząc do **kroku 2,** aby wybrać istniejące informacje o zasobie:
    * Identyfikator usługi Azure Active Directory
    * Nazwa subskrypcji platformy Azure
    * Nazwa usługi Azure QnA
    * Język - język angielski
1. Wprowadź nazwę `Multi-turn batch test quickstart` jako nazwę bazy wiedzy.

1. W **kroku 4**skonfiguruj ustawienia za pomocą poniższej tabeli:

    |Ustawienie|Wartość|
    |--|--|
    |**Włącz ekstrakcję wielu kolejek z plików URL, pdf lub docx.**|Zaznaczone|
    |**Domyślny tekst odpowiedzi**| `Batch test - default answer not found.`|
    |**+ Dodaj plik**|Wybierz pobraną `.docx` listę plików w wymaganiach wstępnych.|
    |**Chit-czat**|Wybierz **profesjonalny**|

1. W **kroku 5**wybierz pozycję **Utwórz swoją bazę wiedzy .**

    Po zakończeniu procesu tworzenia portal wyświetla edytowalną bazę wiedzy.

## <a name="save-train-and-publish-knowledge-base"></a>Zapisywanie, szkolenie i publikowanie bazy wiedzy

1. Wybierz **pozycję Zapisz i trenuj** z paska narzędzi, aby zapisać bazę wiedzy.
1. Wybierz **pozycję Publikuj** na pasku narzędzi, a następnie wybierz pozycję **Publikuj** ponownie, aby opublikować bazę wiedzy. Publikowanie udostępnia bazę wiedzy dla zapytań z publicznego punktu końcowego adresu URL. Po zakończeniu publikowania zapisz adres URL hosta i informacje o kluczu punktu końcowego wyświetlane na stronie **Publikowanie.**

    |Wymagane dane| Przykład|
    |--|--|
    |Opublikowany host|`https://YOUR-RESOURCE-NAME.azurewebsites.net`|
    |Opublikowany klucz|`XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX`(32 ciąg znaków `Endpoint` pokazany po )|
    |Identyfikator aplikacji|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`(36 znaków wyświetlanych `POST`jako część) |

## <a name="create-batch-test-file-with-question-ids"></a>Tworzenie pliku testu wsadowego z identyfikatorami pytań

Aby użyć narzędzia do testowania wsadowego, utwórz plik o nazwie `batch-test-data-1.tsv` z edytorem tekstu. Plik musi mieć następujące kolumny oddzielone kartą.

|Pola pliku wejściowego TSV|Uwagi|Przykład|
|--|--|--|
|Identyfikator bazy wiedzy|Identyfikator bazy wiedzy znajdujący się na stronie Publikowanie. Przetestuj kilka baz wiedzy w tej samej usłudze jednocześnie w jednym pliku przy użyciu różnych identyfikatorów bazy wiedzy w jednym pliku.|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`(36 znaków wyświetlanych `POST`jako część) |
|Pytanie|Tekst pytania, który użytkownik wprowadził. 1000 znaków maks.|`How do I sign out?`|
|Tagi metadanych|optional|`topic:power`używa formatu _klucz:wartość_|
|Parametr górny|optional|`25`|
|Identyfikator oczekiwanej odpowiedzi|optional|`13`|

W przypadku tej bazy wiedzy dodaj do pliku 3 wiersze tylko 2 wymagane kolumny. Pierwsza kolumna to identyfikator bazy wiedzy, a druga kolumna powinna być następującą listą pytań:

|Kolumna 2 - pytania|
|--|
|`Use Windows Hello to sign in`|
|`Charge your Surface Pro 4`|
|`Get to know Windows 10`|

Pytania te są dokładnym sformułowaniem z bazy wiedzy i powinny zwrócić 100 jako wynik zaufania.

Następnie dodaj kilka pytań, podobnych do tych pytań, ale nie dokładnie to samo w 3 kolejnych wierszach, używając tego samego identyfikatora bazy wiedzy:

|Kolumna 2 - pytania|
|--|
|`What is Windows Hello?`|
|`How do I charge the laptop?`|
|`What features are in Windows 10?`|

> [!CAUTION]
> Upewnij się, że każda kolumna jest oddzielona tylko ogranicznikiem kart. Spacje początkowe lub końcowe są dodawane do danych kolumny i spowoduje, że program zgłosić wyjątki, gdy typ lub rozmiar jest niepoprawna.

Plik testu wsadowego, po otwarciu w programie Excel, wygląda jak na poniższej ilustracji. Identyfikator bazy wiedzy został zastąpiony `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` ze względów bezpieczeństwa. W przypadku własnego testu wsadowego upewnij się, że w kolumnie jest wyświetlany identyfikator bazy wiedzy.

> [!div class="mx-imgBorder"]
> ![Wprowadzanie pierwszej wersji pliku tsv z testu wsadowego](../media/batch-test/batch-test-1-input.png)

## <a name="test-the-batch-file"></a>Testowanie pliku wsadowego

Uruchom program do testowania wsadowego przy użyciu następującego formatu interfejsu wiersza polecenia w wierszu polecenia.

Zastąp `YOUR-RESOURCE-NAME` i `ENDPOINT-KEY` własne wartości dla nazwy usługi i klucza punktu końcowego. Wartości te znajdują się na stronie **Ustawienia** w portalu QnA Maker.

```console
batchtesting.exe batch-test-data-1.tsv https://YOUR-RESOURCE-NAME.azurewebsites.net ENDPOINT-KEY out.tsv
```
Test kończy i generuje `out.tsv` plik:

> [!div class="mx-imgBorder"]
> ![Wyjęcie pierwszej wersji pliku tsv z testu wsadowego](../media/batch-test/batch-test-1-output.png)

Identyfikator bazy wiedzy został zastąpiony `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` ze względów bezpieczeństwa. W przypadku własnego testu wsadowego w kolumnie zostanie wyświetlony identyfikator bazy wiedzy.

Wynik testu wskaźnika zaufania w czwartej kolumnie pokazuje 3 najważniejsze pytania zwrócone wynik 100 zgodnie z oczekiwaniami, ponieważ każde pytanie jest dokładnie takie samo, jak pojawia się w bazie wiedzy. Ostatnie 3 pytania, z nowym brzmieniem pytania, nie zwracają 100 jako wynik zaufania. Aby zwiększyć wynik zarówno dla testu, jak i użytkowników, należy dodać więcej alternatywnych pytań do bazy wiedzy.

## <a name="testing-with-the-optional-fields"></a>Testowanie z opcjonalnymi polami

Po zrozumieniu formatu i procesu, można wygenerować plik testowy, aby uruchomić przeciwko bazie wiedzy ze źródła danych, takich jak z dzienników czatów.

Ponieważ źródło danych i proces są zautomatyzowane, plik testowy można uruchomić wiele razy z różnymi ustawieniami w celu określenia prawidłowych wartości.

Na przykład jeśli masz dziennik czatu i chcesz określić, który tekst dziennika czatu ma zastosowanie do pól metadanych, utwórz plik testowy i ustaw pola metadanych dla każdego wiersza. Uruchom test, a następnie przejrzyj wiersze, które pasują do metadanych. Ogólnie rzecz biorąc, mecze powinny być dodatnie, ale należy przejrzeć wyniki dla fałszywych alarmów. Fałszywy alarm to wiersz, który pasuje do metadanych, ale na podstawie tekstu nie powinien być zgodny.

## <a name="using-optional-fields-in-the-input-batch-test-file"></a>Używanie pól opcjonalnych w pliku testu wsadowego danych wejściowych

Użyj poniższego wykresu, aby dowiedzieć się, jak znaleźć wartości pól dla danych opcjonalnych.

|Numer kolumny|Kolumna opcjonalna|Lokalizacja danych|
|--|--|--|
|3|metadane|Eksportuj istniejącą bazę wiedzy dla istniejących _par klucza:wartości._|
|4|top|Zalecana `25` jest wartość domyślna.|
|5|Identyfikator zestawu pytań i odpowiedzi|Eksportuj istniejącą bazę wiedzy dla wartości identyfikatorów. Zwróć również uwagę, że identyfikatory zostały zwrócone w pliku wyjściowym.|

## <a name="add-metadata-to-the-knowledge-base"></a>Dodawanie metadanych do bazy wiedzy

1. W portalu QnA na stronie **Edycja** dodaj `topic:power` metadane następujących pytań:

    |Pytania|
    |--|
    |Ładowanie urządzenia Surface Pro 4|
    |Sprawdź poziom naładowania baterii|

    Dwa zestawy QnA mają zestaw metadanych.

    > [!TIP]
    > Aby wyświetlić metadane i identyfikatory QnA każdego zestawu, wyeksportuj bazę wiedzy. Wybierz stronę **Ustawienia,** a następnie `.xls` wybierz pozycję **Eksportuj** jako plik. Znajdź ten pobrany plik i otwórz go za pomocą przeglądu metadanych i identyfikatorów programu Excel.

1. Wybierz **pozycję Zapisz i trenuj**, a następnie wybierz stronę **Publikuj,** a następnie wybierz przycisk **Publikuj.** Te akcje udostępniają zmianę do testu wsadowego. Pobierz bazę wiedzy ze strony **Ustawienia.**

    Pobrany plik ma poprawny format metadanych i poprawny identyfikator zestawu pytań i odpowiedzi. Użyj tych pól w następnej sekcji

    > [!div class="mx-imgBorder"]
    > ![Eksportowana baza wiedzy z metadanymi](../media/batch-test/exported-knowledge-base-with-metadata.png)

## <a name="create-a-second-batch-test"></a>Tworzenie drugiego testu wsadowego

Istnieją dwa główne scenariusze testowania wsadowego:
* **Przetwarzaj pliki dziennika czatu** — określ najlepszą odpowiedź na wcześniej niewidoczne pytanie - najczęstszą sytuacją jest sytuacja, w której musisz przetworzyć plik dziennika zapytań, takich jak pytania użytkownika czatu bota. Utwórz test pliku wsadowego, zawierający tylko wymagane kolumny. Test zwraca najwyższą odpowiedź dla każdego pytania. To nie znaczy, że najlepszą odpowiedzią jest poprawna odpowiedź. Po zakończeniu tego testu przejdź do testu sprawdzania poprawności.
* **Test sprawdzania poprawności** — sprawdź poprawność oczekiwanej odpowiedzi. Ten test wymaga, aby wszystkie pytania i pasujące oczekiwane odpowiedzi w teście wsadowym zostały zatwierdzone. Może to wymagać pewnego procesu ręcznego.

Poniższa procedura zakłada, że scenariusz polega na przetwarzaniu dzienników czatu

1. Utwórz nowy plik testu wsadowego, aby uwzględnić dane opcjonalne, `batch-test-data-2.tsv`. Dodaj 6 wierszy z oryginalnego pliku wejściowego testu wsadowego, a następnie dodaj metadane, górę i identyfikator zestawu QnA dla każdego wiersza.

    Aby symulować zautomatyzowany proces sprawdzania nowego tekstu z dzienników czatu w bazie wiedzy, ustaw `topic:power`metadane dla każdej kolumny na tę samą wartość: .

    > [!div class="mx-imgBorder"]
    > ![Wprowadzanie drugiej wersji pliku tsv z testu wsadowego](../media/batch-test/batch-test-2-input.png)

1. Uruchom test ponownie, zmieniając nazwy plików wejściowych i wyjściowych, aby wskazać, że jest to drugi test.

    > [!div class="mx-imgBorder"]
    > ![Wyjście drugiej wersji pliku tsv z testu wsadowego](../media/batch-test/batch-test-2-output.png)

## <a name="test-results-and-an-automated-test-system"></a>Wyniki badań i zautomatyzowany system testowy

Ten testowy plik wyjściowy może być analizowany jako część zautomatyzowanego potoku testów ciągłych.

Ten konkretny wynik testu powinien być odczytywany jako: każdy wiersz został odfiltrowany z metadanymi, a ponieważ każdy wiersz nie odpowiadał metadanym w bazie wiedzy, domyślna odpowiedź dla tych niepasujących wierszy zwróconych ("nie znaleziono dobrego dopasowania w kb"). Z tych wierszy, które były zgodne, identyfikator QnA i wynik zostały zwrócone.

Wszystkie wiersze zwróciły etykietę niepoprawną, ponieważ żaden wiersz nie odpowiadał oczekiwanemu identyfikatorowi odpowiedzi.

Powinieneś być w stanie zobaczyć z tych wyników, że można wziąć dziennik czatu i użyć tekstu jako kwerendy każdego wiersza. Nie wiedząc nic o danych, wyniki mówią wiele o danych, które można następnie użyć do przodu:

* meta-dane
* Identyfikator QnA
* wynik

Czy filtrowanie za pomocą metadanych było dobrym pomysłem na test? Tak i nie. System testowy powinien tworzyć pliki testowe dla każdej pary metadanych, a także test bez par meta-danych.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie zamierzasz kontynuować testowania bazy wiedzy, usuń narzędzie pliku wsadowego i pliki testowe.

Jeśli nie zamierzasz nadal korzystać z tej bazy wiedzy, usuń bazę wiedzy, wykonując następujące czynności:

1. W portalu QnA Maker wybierz **pozycję Moje bazy wiedzy** z górnego menu.
1. Na liście baz wiedzy wybierz ikonę **Usuń** w wierszu bazy wiedzy tego przewodnika Szybki start.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API Reference (Dokumentacja interfejsu API REST usługi QnA Maker w wersji 4)](https://go.microsoft.com/fwlink/?linkid=2092179)
