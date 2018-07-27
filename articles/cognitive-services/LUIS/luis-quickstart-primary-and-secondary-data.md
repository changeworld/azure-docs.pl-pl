---
title: Samouczek tworzenia aplikacji LUIS do wyodrębniania danych — Azure | Microsoft Docs
description: Z tego samouczka dowiesz się, jak utworzyć prostą aplikację LUIS, używając intencji i jednostki prostej do danych nauczonych maszynowo.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: v-geberr
ms.openlocfilehash: a4bf63b7a2fbbb26b8c121f5360aea0a5ca8a687
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952389"
---
# <a name="tutorial-7-add-simple-entity-and-phrase-list"></a>Samouczek: 7. Dodawanie jednostki prostej i listy fraz
W ramach tego samouczka utworzysz aplikację demonstrującą sposób wyodrębniania danych nauczonych maszynowo z wypowiedzi za pomocą jednostki **Simple** (prostej).

<!-- green checkmark -->
> [!div class="checklist"]
> * Omówienie jednostek prostych 
> * Tworzenie nowej aplikacji LUIS dla domeny zasobów ludzkich (HR, Human Resources) 
> * Dodawanie prostej jednostki do wyodrębnienia zadań z aplikacji
> * Uczenie i publikowanie aplikacji
> * Wysyłanie zapytania do punktu końcowego aplikacji w celu wyświetlenia odpowiedzi JSON usługi LUIS
> * Dodawanie listy fraz w celu wzmocnienia sygnału wyrazów związanych z zadaniami
> * Uczenie, publikowanie aplikacji i ponowne wykonywanie zapytania dotyczącego punktu końcowego

Na potrzeby tego artykułu jest wymagane bezpłatne konto usługi [LUIS](luis-reference-regions.md#luis-website), które umożliwia utworzenie aplikacji usługi LUIS.

## <a name="before-you-begin"></a>Przed rozpoczęciem
Jeśli nie masz aplikacji Human Resources z samouczka dotyczącego [jednostki złożonej](luis-tutorial-composite-entity.md), [zaimportuj](luis-how-to-start-new-app.md#import-new-app) kod JSON do nowej aplikacji w witrynie internetowej usługi [LUIS](luis-reference-regions.md#luis-website). Aplikacja do zaimportowania znajduje się w repozytorium [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-composite-HumanResources.json) usługi Github.

Jeśli chcesz zachować oryginalną aplikację Human Resources, sklonuj tę wersję na stronie [Settings](luis-how-to-manage-versions.md#clone-a-version) (Ustawienia) i nadaj jej nazwę `simple`. Klonowanie to dobry sposób na testowanie różnych funkcji usługi LUIS bez wpływu na oryginalną wersję aplikacji.  

## <a name="purpose-of-the-app"></a>Przeznaczenie aplikacji
Ta aplikacja pokazuje, jak wydobyć dane z wypowiedzi. Przeanalizujmy następujące wypowiedzi z czatbota:

|Wypowiedź|Nazwa zadania możliwa do wyodrębnienia|
|:--|:--|
|I want to apply for the new accounting job.|accounting|
|Please submit my resume for the engineering position.|engineering|
|Fill out application for job 123456|123456|

W tym samouczku dodasz nową jednostkę w celu wyodrębnienia nazwy zadania. 

## <a name="purpose-of-the-simple-entity"></a>Przeznaczenie jednostki prostej
Celem jednostki prostej w tej aplikacji LUIS jest nauczenie usługi LUIS, co to jest numer zadania i gdzie można go znaleźć w wypowiedzi. Część wypowiedzi, która jest zadaniem, może się zmienić dla różnych wypowiedzi i jest zależna od doboru wyrazów i długości wypowiedzi. Usługa LUIS potrzebuje przykładów zadań w dowolnej wypowiedzi we wszystkich intencjach.  

Nazwa zadania jest trudna do określenia, ponieważ może być rzeczownikiem, czasownikiem lub frazą składającą się z kilku wyrazów. Na przykład:

|Zadania|
|--|
|engineer|
|software engineer|
|senior software engineer|
|engineering team lead |
|air traffic controller|
|motor vehicle operator|
|ambulance driver|
|tender|
|extruder|
|millwright|

Ta aplikacja LUIS ma nazwy zadań w kilku intencjach. Oznaczając te wyrazy w wypowiedziach dla wszystkich intencji, usługa LUIS uczy się, czym jest zadanie, i poznaje dalsze sposoby wyszukiwania go w wypowiedziach.

## <a name="create-job-simple-entity"></a>Tworzenie prostej jednostki zadania

1. Upewnij się, że aplikacja Human Resources znajduje się w sekcji **Build** (Kompilacja) aplikacji LUIS. Możesz przejść do tej sekcji, wybierając pozycję **Build** (Kompilacja) na górnym pasku menu po prawej stronie. 

    [ ![Zrzut ekranu aplikacji LUIS z wyróżnioną pozycją Build (Kompilacja) na górnym prawym pasku nawigacyjnym](./media/luis-quickstart-primary-and-secondary-data/hr-first-image.png)](./media/luis-quickstart-primary-and-secondary-data/hr-first-image.png#lightbox)

2. Na stronie **Intents** (Intencje) wybierz intencję **ApplyForJob**. 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-select-applyforjob.png "Zrzut ekranu usługi LUIS z wyróżnioną intencją „ApplyForJob”")](media/luis-quickstart-primary-and-secondary-data/hr-select-applyforjob.png#lightbox)

3. W wypowiedzi `I want to apply for the new accounting job` wybierz pozycję `accounting`, wprowadź `Job` w górnym polu menu podręcznego, a następnie w menu podręcznym wybierz polecenie **Create new entity** (Utwórz nową jednostkę). 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png "Zrzut ekranu usługi LUIS z intencją „ApplyForJob” i wyróżnionymi krokami tworzenia jednostki")](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png#lightbox)

4. W oknie podręcznym sprawdź nazwę i typ jednostki, a następnie wybierz pozycję **Done** (Gotowe).

    ![Modalne okno dialogowe tworzenia prostej jednostki o nazwie Job (Zadanie) i typie Simple (Prosta)](media/luis-quickstart-primary-and-secondary-data/hr-create-simple-entity-popup.png)

5. W wypowiedzi `Submit resume for engineering position` oznacz etykietą wyraz `engineering` jako jednostkę Job (Zadanie). Zaznacz wyraz `engineering`, a następnie wybierz pozycję **Job** (Zadanie) w menu podręcznym. 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png "Zrzut ekranu usługi LUIS z wyróżnioną etykietą jednostki Job (Zadanie)")](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png#lightbox)

    Wszystkie wypowiedzi są oznaczone etykietami, ale pięć wypowiedzi nie wystarcza, aby nauczyć usługę LUIS o powiązanych z zadaniem wyrazach i frazach. Zadania, które używają wartości liczbowej, nie potrzebują większej liczby przykładów, ponieważ używają jednostki wyrażenia regularnego. Zadania, które są wyrazami lub frazami, wymagają co najmniej 15 dalszych przykładów. 

6. Dodaj więcej wypowiedzi i oznacz wyrazy lub frazy dotyczące zadania jako jednostkę **Job** (Zadanie). Typy zadań są ogólnie używane w ramach zatrudnienia na potrzeby usługi obsługującej zatrudnienie. Jeśli potrzebujesz zadań związanych z określoną branżą, wyrazy zadania powinny to odzwierciedlać. 

    |Wypowiedź|Jednostka Job (Zadanie)|
    |:--|:--|
    |I'm applying for the Program Manager desk in R&D|Program Manager|
    |Here is my line cook application.|line cook|
    |My resume for camp counselor is attached.|camp counselor|
    |This is my c.v. for administrative assistant.|administrative assistant|
    |I want to apply for the management job in sales.|management, sales|
    |This is my resume for the new accounting position.|accounting|
    |My application for barback is included.|barback|
    |I'm submitting my application for roofer and framer.|roofer, framer|
    |My c.v. for bus driver is here.|bus driver|
    |I'm a registered nurse. Here is my resume.|registered nurse|
    |I would like to submit my paperwork for the teaching position I saw in the paper.|teaching|
    |This is my c.v. for the stocker post in fruits and vegetables.|stocker|
    |Apply for tile work.|kafelek|
    |Attached resume for landscape architect.|landscape architect|
    |My curriculum vitae for professor of biology is enclosed.|professor of biology|
    |I would like to apply for the position in photography.|photography|git 

## <a name="label-entity-in-example-utterances-for-getjobinformation-intent"></a>Oznaczanie etykietą jednostki w przykładowych wypowiedziach dla intencji GetJobInformation
1. Wybierz pozycję **Intents** (Intencje) z menu po lewej.

2. Wybierz pozycję **GetJobInformation** z listy intencji. 

3. Oznacz etykietami zadania w przykładowych wypowiedziach:

    |Wypowiedź|Jednostka Job (Zadanie)|
    |:--|:--|
    |Is there any work in databases?|bazy danych|
    |Looking for a new situation with responsibilities in accounting (Szukam nowego wyzwania w obszarze księgowości)|accounting|
    |What positions are available for senior engineers?|senior engineers|

    Istnieją inne przykładowe wypowiedzi, ale nie zawierają one wyrazów związanych z zadaniem.

## <a name="train-the-luis-app"></a>Uczenie aplikacji LUIS
Usługa LUIS nie wie o zmianach intencji i jednostek (modelu), dopóki nie zostanie ich nauczona. 

1. W górnej części witryny internetowej usługi LUIS po prawej stronie wybierz przycisk **Train** (Ucz).

    ![Wybieranie przycisku Train (Ucz)](./media/luis-quickstart-primary-and-secondary-data/train-button.png)

2. Uczenie jest ukończone, gdy w górnej części witryny internetowej jest widoczny zielony pasek stanu potwierdzający powodzenie.

    ![Powiadomienie o pomyślnym ukończeniu uczenia](./media/luis-quickstart-primary-and-secondary-data/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publikowanie aplikacji w celu uzyskania adresu URL punktu końcowego
Aby uzyskać przewidywania usługi LUIS w czatbocie lub innej aplikacji, należy opublikować aplikację. 

1. W górnej części witryny usługi LUIS po prawej stronie wybierz przycisk **Publish** (Publikuj). 

2. Wybierz miejsce produkcyjne i przycisk **Publish** (Publikuj).

    [![](media/luis-quickstart-primary-and-secondary-data/publish-to-production.png "Zrzut ekranu przedstawiający stronę publikowania z wyróżnionym przyciskiem publikowania w miejscu produkcyjnym")](media/luis-quickstart-primary-and-secondary-data/publish-to-production.png#lightbox)

3. Publikowanie jest ukończone, gdy w górnej części witryny internetowej jest widoczny zielony pasek stanu potwierdzający powodzenie.

## <a name="query-the-endpoint-with-a-different-utterance"></a>Wysyłanie zapytania do punktu końcowego za pomocą różnych wypowiedzi
Na stronie **Publish** (Publikowanie) wybierz link **endpoint** (punkt końcowy) u dołu strony. 

[![](media/luis-quickstart-primary-and-secondary-data/publish-select-endpoint.png "Zrzut ekranu przedstawiający stronę publikowania z wyróżnionym punktem końcowym")](media/luis-quickstart-primary-and-secondary-data/publish-select-endpoint.png#lightbox)

Ta czynność spowoduje otwarcie nowego okna przeglądarki z adresem URL punktu końcowego na pasku adresu. Przejdź na koniec tego adresu URL i wprowadź ciąg `Here is my c.v. for the programmer job`. Ostatni parametr ciągu zapytania to `q`, czyli **query** (zapytanie) wypowiedzi. Ta wypowiedź jest inna niż wszystkie pozostałe oznaczone wypowiedzi, dlatego jest dobra do testowania i powinna zwrócić wypowiedzi `ApplyForJob`.

```JSON
{
  "query": "Here is my c.v. for the programmer job",
  "topScoringIntent": {
    "intent": "ApplyForJob",
    "score": 0.9826467
  },
  "intents": [
    {
      "intent": "ApplyForJob",
      "score": 0.9826467
    },
    {
      "intent": "GetJobInformation",
      "score": 0.0218927357
    },
    {
      "intent": "MoveEmployee",
      "score": 0.007849265
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.00349470088
    },
    {
      "intent": "Utilities.Confirm",
      "score": 0.00348804821
    },
    {
      "intent": "None",
      "score": 0.00319909188
    },
    {
      "intent": "FindForm",
      "score": 0.00222647213
    },
    {
      "intent": "Utilities.Help",
      "score": 0.00211193133
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.00172086991
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.00138010911
    }
  ],
  "entities": [
    {
      "entity": "programmer",
      "type": "Job",
      "startIndex": 24,
      "endIndex": 33,
      "score": 0.5230502
    }
  ]
}
```

## <a name="names-are-tricky"></a>Nazwy są trudne
Aplikacja LUIS znalazła prawidłową intencję z wysokim poziomem pewności i wyodrębniła nazwę zadania, ale nazwy mogą być skomplikowane. Spróbuj użyć wypowiedzi `This is the lead welder paperwork`.  

W poniższym kodzie JSON usługa LUIS odpowiada przy użyciu prawidłowej intencji `ApplyForJob`, ale nie wyodrębniła nazwy zadania `lead welder`. 

```JSON
{
  "query": "This is the lead welder paperwork.",
  "topScoringIntent": {
    "intent": "ApplyForJob",
    "score": 0.468558252
  },
  "intents": [
    {
      "intent": "ApplyForJob",
      "score": 0.468558252
    },
    {
      "intent": "GetJobInformation",
      "score": 0.0102701457
    },
    {
      "intent": "MoveEmployee",
      "score": 0.009442534
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.00639619166
    },
    {
      "intent": "None",
      "score": 0.005859333
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.005087704
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.00315379258
    },
    {
      "intent": "Utilities.Help",
      "score": 0.00259344373
    },
    {
      "intent": "FindForm",
      "score": 0.00193389168
    },
    {
      "intent": "Utilities.Confirm",
      "score": 0.000420796918
    }
  ],
  "entities": []
}
```

Ponieważ nazwa może być dowolna, usługa LUIS przewiduje jednostki dokładniej, jeśli ma listę fraz powiązanych z wyrazami, które umożliwiają wzmocnienie sygnału.

## <a name="to-boost-signal-add-jobs-phrase-list"></a>Dodawanie listy fraz zadań w celu wzmocnienia sygnału
Otwórz plik [jobs-phrase-list.csv](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/job-phrase-list.csv) z repozytorium Github LUIS-Samples. Lista zawiera ponad tysiąc wyrazów i fraz związanych z zadaniami. Wyszukaj na liście istotne w Twoim przypadku wyrazy dotyczące zadań. Jeśli odpowiednich wyrazów lub fraz nie ma na liście, dodaj własne.

1. W sekcji **Build** (Kompilacja) aplikacji LUIS wybierz pozycję **Phrase lists** (Listy fraz) w menu **Improve app performance** (Zwiększ wydajność aplikacji).

    [![](media/luis-quickstart-primary-and-secondary-data/hr-select-phrase-list-left-nav.png "Zrzut ekranu Phrase lists (Listy fraz) z wyróżnionym lewym przyciskiem nawigacji")](media/luis-quickstart-primary-and-secondary-data/hr-select-phrase-list-left-nav.png#lightbox)

2. Wybierz pozycję **Create new phrase list** (Utwórz nową listę fraz). 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-new-phrase-list.png "Zrzut ekranu z wyróżnionym przyciskiem Create new phrase list (Utwórz nową listę fraz)")](media/luis-quickstart-primary-and-secondary-data/hr-create-new-phrase-list.png#lightbox)

3. Nadaj nowej liście fraz nazwę `Jobs` i skopiuj listę z pliku jobs-phrase-list.csv do pola tekstowego **Values** (Wartości). Wybierz klawisz Enter. 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png "Zrzut ekranu podręcznego okna dialogowego tworzenia nowej listy fraz")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png#lightbox)

    Jeśli chcesz dodać więcej wyrazów do listy fraz, przejrzyj pozycję **Powiązane wartości** i dodaj odpowiednie pozycje. 

4. Wybierz pozycję **Save** (Zapisz), aby aktywować listę fraz.

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png "Zrzut ekranu podręcznego okna dialogowego tworzenia nowej listy fraz z wyrazami w polu wartości listy fraz")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png#lightbox)

5. [Naucz](#train-the-luis-app) i [opublikuj](#publish-the-app-to-get-the-endpoint-URL) aplikację ponownie, aby użyć listy fraz.

6. Wykonaj ponownie zapytanie w punkcie końcowym dla tej samej wypowiedzi: `This is the lead welder paperwork.`

    Odpowiedź JSON zawiera wyodrębnioną jednostkę:

    ```JSON
    {
        "query": "This is the lead welder paperwork.",
        "topScoringIntent": {
            "intent": "ApplyForJob",
            "score": 0.920025647
        },
        "intents": [
            {
            "intent": "ApplyForJob",
            "score": 0.920025647
            },
            {
            "intent": "GetJobInformation",
            "score": 0.003800706
            },
            {
            "intent": "Utilities.StartOver",
            "score": 0.00299335527
            },
            {
            "intent": "MoveEmployee",
            "score": 0.0027167045
            },
            {
            "intent": "None",
            "score": 0.00259556063
            },
            {
            "intent": "FindForm",
            "score": 0.00224019377
            },
            {
            "intent": "Utilities.Stop",
            "score": 0.00200693542
            },
            {
            "intent": "Utilities.Cancel",
            "score": 0.00195913855
            },
            {
            "intent": "Utilities.Help",
            "score": 0.00162656687
            },
            {
            "intent": "Utilities.Confirm",
            "score": 0.0002851904
            }
        ],
        "entities": [
            {
            "entity": "lead welder",
            "type": "Job",
            "startIndex": 12,
            "endIndex": 22,
            "score": 0.8295959
            }
        ]
    }
    ```

## <a name="phrase-lists"></a>Listy fraz
Dodanie listy frazy wzmocniło sygnał wyrazów na liście, ale **nie** jest ona używana jako dokładne dopasowanie. Lista fraz ma kilka zadań z pierwszym wyrazem `lead` i zadanie `welder`, ale nie ma zadania `lead welder`. Ta lista fraz dla zadań może być niekompletna. W miarę regularnego [przeglądania wypowiedzi punktu końcowego](luis-how-to-review-endoint-utt.md) i wyszukiwania innych wyrazów dotyczących zadań można dodawać je do listy fraz. Następnie należy ponownie nauczyć i opublikować aplikację.

## <a name="what-has-this-luis-app-accomplished"></a>Co wykonała ta aplikacja LUIS?
Ta aplikacja, zawierająca prostą jednostkę i listę fraz z wyrazami, zidentyfikowała intencję zapytania w języku naturalnym i zwróciła dane zadania. 

Twój czatbot ma teraz dość informacji, aby określić akcję główną ubiegania się o pracę i parametr tej akcji — przywoływane zadanie. 

## <a name="where-is-this-luis-data-used"></a>Gdzie są używane te dane usługi LUIS? 
Usługa LUIS skończyła obsługiwać to żądanie. Aplikacja wywołująca, taka jak czatbot, może pobrać wynik topScoringIntent oraz dane z jednostki, aby użyć interfejsu API innej firmy do wysłania informacji o zadaniu do przedstawiciela działu zasobów ludzkich. Jeśli istnieją inne opcje programowe dla bota lub aplikacji wywołującej, usługa LUIS nie obsłuży ich. Usługa LUIS określa jedynie intencję użytkownika. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Gdy aplikacja LUIS nie będzie już potrzebna, usuń ją. Wybierz pozycję **My apps** (Moje aplikacje) z menu w lewym górnym rogu. Wybierz wielokropek (**...**) po prawej stronie nazwy aplikacji na liście aplikacji i wybierz polecenie ***Delete*** (Usuń). W wyskakującym oknie dialogowym **Delete app?** (Usunąć aplikację?) wybierz pozycję **OK**.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dodawanie wstępnie skompilowanej jednostki KeyPhrase](luis-quickstart-intent-and-key-phrase.md)