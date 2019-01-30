---
title: Dodawanie intencji
titleSuffix: Language Understanding - Azure Cognitive Services
description: Dodawanie intencji z aplikacją usługi LUIS do identyfikowania grup pytania lub polecenia, które mają ten sam intencji.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.service: cognitive-services
ms.openlocfilehash: 81b963a56b35cc1206982680f8742c2fea9f2446
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55219332"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>Dodawanie intencji, aby określić użytkownika zamiar wypowiedzi

Dodaj [intencji](luis-concept-intent.md) z aplikacją usługi LUIS do identyfikowania grup pytania lub polecenia, które mają ten sam zamiar. 

Intencji odbywa się na pasku górnym menu nawigacyjnym **kompilacji** sekcji, a następnie w panelu po lewej stronie **intencji**. 

## <a name="add-intent"></a>Dodawanie intencji

1. Na stronie **Intents** (Intencje) wybierz pozycję **Create new intent** (Utwórz nową intencję).

1. W **Utwórz nowy opcję** okna dialogowego wprowadź nazwę elementu intent `GetEmployeeInformation`i kliknij przycisk **gotowe**.

    ![Dodawanie intencji](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

## <a name="add-an-example-utterance"></a>Dodaj wypowiedź przykład

Przykład wypowiedzi są przykładami tekst pytania użytkownika lub poleceń. Aby nauczyć Language Understanding (LUIS), należy dodać wypowiedzi przykład do intencji.

1. Na **GetEmployeeInformation** intencji szczegółów wpisz odpowiednie wypowiedź, można oczekiwać od użytkowników, takie jak `Does John Smith work in Seattle?` w polu tekstowym pod nazwę metody konwersji, a następnie naciśnij klawisz Enter.
 
    ![Strona Szczegóły zrzut ekranu z opcjami, z wyróżnioną pozycją wypowiedź](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

    Usługa LUIS konwertuje wszystkie wypowiedzi na małe litery i dodaje odstępy dookoła tokenów, takich jak łączniki.

## <a name="intent-prediction-discrepancy-errors"></a>Błędy niezgodności prognozowania intencji 

Wypowiedź w intencji może być rozbieżności intencji prognozowania metodę konwersji i ocenianie prognozy. Usługa LUIS wskazuje tę rozbieżność z czerwoną otoczkę wokół pozycji **etykietą intencji** w wierszu wypowiedź przykładu. 

![Strona Szczegóły zrzut ekranu z opcjami, z błędami rozbieżność prognozowania wypowiedź](./media/luis-how-to-add-intents/prediction-discrepancy-intent.png) 

W górnym menu nawigacyjnym wybierz **Train**. Niezgodność Prognozowanie jest teraz usunięte.

## <a name="add-a-custom-entity"></a>Dodaj jednostki niestandardowej

Po dodaniu wypowiedź intencji możesz zaznaczyć tekst z w ramach wypowiedź, aby utworzyć jednostkę niestandardową. Jednostki niestandardowej jest sposobem tekst tag do wyodrębnienia, wraz z poprawną intencji. 

1. Wybierz wyraz, `Seattle`, w polu wypowiedź. Nawiasy kwadratowe są rysowane wokół tekstu, a następnie zostanie wyświetlone menu rozwijanego. 

    ![Strona szczegółów zrzut ekranu z opcjami, tworzenie jednostki niestandardowej](./media/luis-how-to-add-intents/create-custom-entity.png) 

    W tym przykładzie wybiera pojedynczego wyrazu w celu oznaczenia jako jednostki. Jednostki można określić pojedynczy działa i fraz.

1. W polu u góry tekstu — menu, wprowadź `Location`, a następnie wybierz **Utwórz nową jednostkę**. 

    ![Strona szczegółów zrzut ekranu z opcjami, tworzenie jednostki niestandardowej o nazwie](./media/luis-how-to-add-intents/create-custom-entity-name.png) 

1. W **jakiego typu jednostki, czy chcesz utworzyć?** okno podręczne tworzenia jednostki, sprawdzić, czy **nazwa jednostki** jest _lokalizacji_i **typ jednostki**  jest _proste_. Wybierz pozycję **Done** (Gotowe).

## <a name="entity-prediction-discrepancy-errors"></a>Błędy niezgodności prognozowania jednostki 

Jednostka jest podkreślone na czerwono, aby wskazać [rozbieżność prognozowania jednostki](luis-how-to-add-example-utterances.md#entity-status-predictions). Ponieważ jest to pierwsze wystąpienie jednostki, nie są wystarczająco dużo przykłady dla usługi LUIS mieć o wysokim poziomie pewności oznakowaną ten tekst z poprawną jednostką. Ta niezgodność zostanie usunięta, gdy aplikacja jest uczony. 

![Strona szczegółów zrzut ekranu z opcjami, nazwa jednostki niestandardowej wyróżnione na niebiesko](./media/luis-how-to-add-intents/create-custom-entity-name-blue-highlight.png) 

Tekst zostanie wyróżniony kolorem niebieskim, wskazując jednostki.  

## <a name="add-a-prebuilt-entity"></a>Dodawanie wstępnie utworzone jednostki

Aby uzyskać informacje, zobacz [wstępnie utworzone jednostki](luis-how-to-add-entities.md#add-prebuilt-entity).

## <a name="using-the-contextual-toolbar"></a>Korzystanie z kontekstowych paska narzędzi

Po wybraniu na liście co najmniej jeden wypowiedzi przykład przez zaznaczenie pola wyboru po lewej stronie wypowiedź narzędzi powyżej listy wypowiedź można wykonywać następujące czynności:

* Ponowne przypisywanie intencji: przenoszenie utterance(s) do innego zamiaru
* Usuń utterance(s)
* Filtry jednostki: Pokaż tylko wypowiedzi zawierający filtrowane jednostkę
* Pokaż wszystkie / tylko błędy: Pokaż wypowiedzi z błędami prognozy lub Pokaż wszystkie wypowiedzi
* Wyświetl jednostki/tokenów: Pokaż widok jednostek przy użyciu nazwy podmiotu lub pokazać nieprzetworzony tekst wypowiedź
* Lupa: Wyszukaj wypowiedzi zawierających określony tekst

## <a name="working-with-an-individual-utterance"></a>Praca z poszczególnych wypowiedź

Następujące akcje można wykonać na poszczególnych wypowiedź z menu wielokropka z prawej strony wypowiedź:

* Edytuj: Zmień tekst wypowiedź
* Usuń: Usuń wypowiedź z zamiarem. Jeśli chcesz nadal wypowiedź, lepszą metodą jest przenieść ją do **Brak** intencji. 
* Dodawanie wzorca: Wzorzec umożliwia możesz swobodnie wspólnej wypowiedź oznaczyć wymienne tekst i tekstu można zignorować, zmniejszając potrzebę więcej wypowiedzi w intencji. 

**Etykietą intencji** kolumny pozwala zmienić opcje wypowiedź.

## <a name="train-your-app-after-changing-model-with-intents"></a>Uczenie aplikacji po zmianie modelu z opcjami

Po Dodawanie, edytowanie lub usuwanie intencji, [szkolenie](luis-how-to-train.md) i [publikowania](luis-how-to-publish-app.md) aplikację tak, aby zmiany zostaną zastosowane do endpoint zapytań. 

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat dodawania [wypowiedzi przykład](luis-how-to-add-example-utterances.md) z jednostkami. 
