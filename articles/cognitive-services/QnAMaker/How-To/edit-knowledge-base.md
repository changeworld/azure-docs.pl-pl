---
title: Edytowanie bazy wiedzy - QnA Maker
description: QnA Maker umożliwia zarządzanie zawartością bazy wiedzy, zapewniając łatwe w użyciu środowisko edycji.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 3cf5dcedd4d2cfa82fda002b71abca1e5bea4b18
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756717"
---
# <a name="edit-qna-pairs-in-your-knowledge-base"></a>Edytuj pary QnA w swojej bazie wiedzy

QnA Maker umożliwia zarządzanie zawartością bazy wiedzy, zapewniając łatwe w użyciu środowisko edycji.

Pary QnA są dodawane ze źródła danych, takiego jak plik lub adres URL, lub dodawane jako źródło redakcyjne. Źródło redakcyjne wskazuje, że para QnA została dodana ręcznie w portalu QnA. Wszystkie pary QnA są dostępne do edycji.

## <a name="add-an-editorial-qna-pair"></a>Dodawanie redakcyjnej pary QnA
1. Zaloguj się do [portalu QnA](https://www.qnamaker.ai/), a następnie wybierz bazę wiedzy, do aby dodać parę QnA.
1. Na stronie **EDIT** bazy wiedzy wybierz pozycję **Dodaj parę QnA,** aby dodać nową parę QnA.

1. W nowym wierszu pary QnA dodaj wymagane pola **Pytania** i **Odpowiedź.** Pozostałe pola są opcjonalne. Wszystkie pola można zmienić w dowolnym momencie.

1. Opcjonalnie dodaj **alternatywne zwroty**. Alternatywne sformułowanie jest dowolną formą pytania, które znacznie różni się od pierwotnego pytania, ale powinno dostarczyć tej samej odpowiedzi.

    Po opublikowaniu bazy wiedzy i [włączeniu aktywnego uczenia się](use-active-learning.md) program QnA Maker zbiera alternatywne opcje frazowania, które możesz zaakceptować. Te opcje są wybierane w celu zwiększenia dokładności przewidywania.

1. Opcjonalnie dodaj **metadane**. Aby wyświetlić metadane, wybierz opcję **Opcje widoku** w menu kontekstowym. Metadane zapewnia filtry odpowiedzi, które zapewnia aplikacja kliencka, takich jak bot czatu.

1. Opcjonalnie dodaj **monity uzupełniające**. Monity uzupełniające zapewniają dodatkowe ścieżki konwersacji do aplikacji klienckiej do przedstawienia użytkownikowi.

1. Wybierz **zapisz i trenuj,** aby zobaczyć prognozy, w tym nową parę QnA.

## <a name="edit-a-qna-pair"></a>Edytowanie pary QnA

Każde pole w dowolnej parze QnA można edytować, niezależnie od oryginalnego źródła danych. Niektóre pola mogą nie być widoczne ze względu na bieżące ustawienia **opcji widoku,** które można znaleźć na pasku narzędzi kontekstu.

## <a name="delete-a-qna-pair"></a>Usuwanie pary QnA

Aby usunąć qnA, kliknij ikonę **usuwania** po prawej stronie wiersza QnA. Jest to stała operacja. Nie można go cofnąć. Rozważ wyeksportowanie bazy wiedzy ze strony **Publikowania** przed usunięciem zestawów.

![Usuń parę QnA](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="find-the-qna-pair-id"></a>Znajdowanie identyfikatora pary QnA

Jeśli chcesz znaleźć identyfikator pary QnA, możesz go znaleźć w dwóch miejscach:

* Umieść wskaźnik myszy na ikonie usuwania w wierszu pary QnA, który Cię interesuje. Tekst aktywowania pola zawiera identyfikator pary QnA.
* Eksportuj bazę wiedzy. Każda para QnA w bazie wiedzy zawiera identyfikator pary QnA.

## <a name="add-alternate-questions"></a>Dodawanie pytań alternatywnych

Dodaj alternatywne pytania do istniejącej pary QnA, aby zwiększyć prawdopodobieństwo dopasowania do kwerendy użytkownika.

![Dodawanie pytań alternatywnych](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="linking-qna-pairs"></a>Łączenie par QnA

Łączenie par QnA jest dostarczane z [monitami uzupełniającymi](multiturn-conversation.md). Jest to logiczne połączenie między parami QnA, zarządzane na poziomie bazy wiedzy. Monity uzupełniające można edytować w portalu programu QnA Maker.

Nie można połączyć par QnA w metadanych odpowiedzi.

## <a name="add-metadata"></a>Dodawanie metadanych

Dodaj zestawy metadanych, najpierw wybierając **opcje widoku,** a następnie wybierając pozycję **Pokaż metadane**. Spowoduje to wyświetlone kolumny metadanych. Następnie wybierz **+** znak, aby dodać zestaw metadanych. Ten zestaw składa się z jednego klucza i jednej wartości.

## <a name="save-changes-to-the-qna-pairs"></a>Zapisywanie zmian w parach QnA

Okresowo **wybieraj pozycję Zapisz i trenuj** po dokonaniu zmian, aby uniknąć utraty zmian.

![Dodawanie metadanych](../media/qnamaker-how-to-edit-kb/add-metadata.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Współpraca nad bazą wiedzy](./collaborate-knowledge-base.md)

* [Zarządzanie zasobami platformy Azure używanymi przez program QnA Maker](set-up-qnamaker-service-azure.md)