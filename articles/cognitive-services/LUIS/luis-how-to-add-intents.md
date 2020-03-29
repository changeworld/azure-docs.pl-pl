---
title: Dodaj intencje — USŁUGA LUIS
titleSuffix: Azure Cognitive Services
description: Dodaj intencje do aplikacji usługi LUIS, aby zidentyfikować grupy pytań lub poleceń, które mają te same intencje.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: diberry
ms.service: cognitive-services
ms.openlocfilehash: 66a3350dee60772ce706af8995179dcd8c485b64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73904322"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>Dodaj intencje w celu określenia intencji użytkownika wypowiedzi

Dodaj [intencje](luis-concept-intent.md) do aplikacji usługi LUIS, aby zidentyfikować grupy pytań lub poleceń, które mają ten sam zamiar. 

Intencje są zarządzane z górnej sekcji **Kompilacja** paska nawigacyjnego, a następnie z **intencji**lewego panelu . 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-intent"></a>Dodawanie intencji

1. W [portalu podglądu usługi LUIS](https://preview.luis.ai)wybierz pozycję **Buduj,** aby wyświetlić intencje. 
1. Na stronie **Intencje** wybierz pozycję **+ Utwórz**.
1. W oknie dialogowym **Tworzenie nowego zamiaru** wprowadź `ModifyOrder`na przykład nazwę intencji i wybierz pozycję **Gotowe**.

    > [!div class="mx-imgBorder"]
    > ![Dodaj intencję](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

    Intencja potrzebuje wypowiedzi przykład.

## <a name="add-an-example-utterance"></a>Dodawanie przykładowego wypowiedź

Przykładowe wypowiedzi są przykładami tekstowymi pytań lub poleceń użytkownika. Aby nauczyć zrozumienie języka (LUIS), kiedy przewidzieć tę intencję, należy dodać wypowiedzi przykład do intencji. Usługa LUIS potrzebuje w zakresie od 15 do 30 wypowiedzi przykład, aby rozpocząć zrozumienie intencji. Nie należy dodawać wypowiedzi przykład zbiorczo. Każda wypowiedź powinna być starannie dobrana dla tego, jak jest inny niż przykłady już w intencji. 

1. Na stronie szczegółów intencji wprowadź odpowiedni wypowiedź oczekiwany `Deliver a large cheese pizza` od użytkowników, na przykład w polu tekstowym poniżej nazwy intencji, a następnie naciśnij klawisz Enter.
 
    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający stronę szczegółów intencji z wyróżnioną wypowiedź](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

    Usługa LUIS konwertuje wszystkie wypowiedzi na małe litery i dodaje spacje wokół [tokenów,](luis-language-support.md#tokenization) takich jak łączniki.

<a name="#intent-prediction-discrepancy-errors"></a>

## <a name="intent-prediction-errors"></a>Błędy przewidywania intencji 

Wypowiedź przykład w intencji może mieć błąd przewidywania intencji między intencji wypowiedź przykład jest obecnie w i intencji określone podczas szkolenia. 

Aby znaleźć błędy przewidywania wypowiedzi i je naprawić, użyj opcji **Filtruj** niepoprawne i niejasne w połączeniu z opcją **Widok** **widoku szczegółowego**. 

![Aby znaleźć błędy przewidywania wypowiedź i je naprawić, należy użyć opcji Filtr.](./media/luis-how-to-add-intents/find-intent-prediction-errors.png)

Gdy filtry i widok są stosowane, a istnieją wypowiedzi przykład z błędami, lista wypowiedź przykład pokazuje wypowiedzi i problemy.

> [!div class="mx-imgBorder"]
> ![! [Gdy filtry i widok są stosowane, a istnieją przykładowe wypowiedzi z błędami, na przykładowej liście wypowiedź pokazuje wypowiedzi i problemy.] (./media/luis-how-to-add-intents/find-errors-in-utterances.png)](./media/luis-how-to-add-intents/find-errors-in-utterances.png#lightbox)

Każdy wiersz pokazuje wynik przewidywania bieżącego szkolenia dla wypowiedź przykład, wynik najbliższego rywala, który jest różnica w tych dwóch wyników. 

### <a name="fixing-intents"></a>Naprawianie intencji

Aby dowiedzieć się, jak naprawić błędy przewidywania intencji, użyj [pulpitu nawigacyjnego podsumowania](luis-how-to-use-dashboard.md). Pulpit nawigacyjny podsumowania zawiera analizę ostatniego szkolenia w aktywnej wersji i oferuje najlepsze sugestie dotyczące naprawy modelu.  

## <a name="using-the-contextual-toolbar"></a>Korzystanie z kontekstowego paska narzędzi

Pasek narzędzi kontekstu zawiera inne akcje:

* Edytowanie lub usuwanie przykładowego wypowiedź
* Ponowne przypisywanie przykładowej wypowiedzi do innej intencji
* Filtry i widoki: pokaż tylko wypowiedzi zawierające filtrowane jednostki lub wyświetl szczegóły opcjonalne
* Wyszukiwanie przykładowych wypowiedzi

## <a name="train-your-app-after-changing-model-with-intents"></a>Trenuj aplikację po zmianie modelu z intencjami

Po dodaniu, edycji lub usuwaniu [intencji, trenuj](luis-how-to-train.md) i [publikuj](luis-how-to-publish-app.md) aplikację, aby zmiany były stosowane do zapytań o punkt końcowy. Nie trenuj po każdej zmianie. Trenuj po grupie zmian. 

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [dodawaniu przykładowych wypowiedzi](luis-how-to-add-example-utterances.md) z jednostkami. 
