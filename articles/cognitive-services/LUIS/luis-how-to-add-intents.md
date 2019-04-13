---
title: Dodawanie intencji
titleSuffix: Language Understanding - Azure Cognitive Services
description: Dodawanie intencji z aplikacją usługi LUIS do identyfikowania grup pytania lub polecenia, które mają ten sam intencji.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.subservice: language-understanding
ms.topic: article
ms.date: 04/01/2019
ms.author: diberry
ms.service: cognitive-services
ms.openlocfilehash: ed180563ea6138b3b4bab6092b39eeacf9dbf840
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/12/2019
ms.locfileid: "59521746"
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

> [!Note]
> Gdy czerwoną linią podlega wyraz lub frazę w polu wypowiedź przykładu, [Błąd prognozowania jednostki](luis-how-to-add-example-utterances.md#entity-status-predictions) wystąpił. Należy go poprawić. 

## <a name="add-a-custom-entity"></a>Dodaj jednostki niestandardowej

Po dodaniu wypowiedź intencji możesz zaznaczyć tekst z w ramach wypowiedź, aby utworzyć jednostkę niestandardową. Jednostki niestandardowej jest sposobem tekst tag do wyodrębnienia, wraz z poprawną intencji. 

Zobacz [Dodawanie jednostki do wypowiedź](luis-how-to-add-example-utterances.md) Aby dowiedzieć się więcej.

## <a name="entity-prediction-discrepancy-errors"></a>Błędy niezgodności prognozowania jednostki 

Jednostka jest podkreślone na czerwono, aby wskazać [rozbieżność prognozowania jednostki](luis-how-to-add-example-utterances.md#entity-status-predictions). Ponieważ jest to pierwsze wystąpienie jednostki, nie są wystarczająco dużo przykłady dla usługi LUIS mieć o wysokim poziomie pewności oznakowaną ten tekst z poprawną jednostką. Ta niezgodność zostanie usunięta, gdy aplikacja jest uczony. 

![Strona szczegółów zrzut ekranu z opcjami, nazwa jednostki niestandardowej wyróżnione na niebiesko](./media/luis-how-to-add-intents/create-custom-entity-name-blue-highlight.png) 

Tekst zostanie wyróżniony kolorem niebieskim, wskazując jednostki.  

## <a name="add-a-prebuilt-entity"></a>Dodawanie wstępnie utworzone jednostki

Aby uzyskać informacje, zobacz [wstępnie utworzone jednostki](luis-how-to-add-entities.md#add-a-prebuilt-entity-to-your-app).

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
