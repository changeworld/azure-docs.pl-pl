---
title: Edytowanie bazy wiedzy - QnA Maker
description: QnA Maker umożliwia zarządzanie zawartością bazy wiedzy, zapewniając łatwe w użyciu środowisko edycji.
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 223ad3a607adc20bbe608598da9742d56788b2c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131655"
---
# <a name="edit-qna-sets-in-your-knowledge-base"></a>Edytuj zestawy QnA w swojej bazie wiedzy

QnA Maker umożliwia zarządzanie zawartością bazy wiedzy, zapewniając łatwe w użyciu środowisko edycji.

Zestawy QnA są dodawane ze źródła danych, takiego jak plik lub adres URL, lub dodawane jako źródło redakcyjne. Źródło redakcyjne wskazuje, że zestaw QnA został dodany do portalu QnA ręcznie. Wszystkie zestawy QnA są dostępne do edycji.

## <a name="add-an-editorial-qna-set"></a>Dodawanie redakcyjnego zestawu QnA
1. Zaloguj się do [portalu QnA](https://www.qnamaker.ai/), a następnie wybierz bazę wiedzy, do aby dodać zestaw QnA.
1. Na stronie **EDIT** bazy wiedzy wybierz pozycję **Dodaj zestaw QnA,** aby dodać nowy zestaw QnA.

1. W nowym wierszu zestawu QnA dodaj wymagane pola **Pytania** i **Odpowiedzi.** Pozostałe pola są opcjonalne. Wszystkie pola można zmienić w dowolnym momencie.

1. Opcjonalnie dodaj **alternatywne zwroty**. Alternatywne sformułowanie jest dowolną formą pytania, które znacznie różni się od pierwotnego pytania, ale powinno dostarczyć tej samej odpowiedzi.

    Po opublikowaniu bazy wiedzy i włączeniu aktywnego uczenia się program QnA Maker zbiera alternatywne opcje frazowania, które możesz zaakceptować. Te opcje są wybierane w celu zwiększenia dokładności przewidywania.

1. Opcjonalnie dodaj **metadane**. Aby wyświetlić metadane, wybierz opcję **Opcje widoku** w menu kontekstowym. Metadane zapewnia filtry odpowiedzi, które zapewnia aplikacja kliencka, takich jak bot czatu.

1. Opcjonalnie dodaj **monity uzupełniające**. Monity uzupełniające zapewniają dodatkowe ścieżki konwersacji do aplikacji klienckiej do przedstawienia użytkownikowi.

1. Wybierz **zapisz i trenuj,** aby zobaczyć prognozy, w tym nowy zestaw QnA.

## <a name="edit-a-qna-set"></a>Edytowanie zestawu QnA

Każde pole w dowolnym zestawie QnA można edytować, niezależnie od oryginalnego źródła danych. Niektóre pola mogą nie być widoczne ze względu na bieżące ustawienia **opcji widoku,** które można znaleźć na pasku narzędzi kontekstu.

## <a name="delete-a-qna-set"></a>Usuwanie zestawu QnA

Aby usunąć qnA, kliknij ikonę **usuwania** po prawej stronie wiersza QnA. Jest to stała operacja. Nie można go cofnąć. Rozważ wyeksportowanie bazy wiedzy ze strony **Publikowania** przed usunięciem zestawów.

![Usuwanie zestawu QnA](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="find-the-qna-set-id"></a>Znajdowanie identyfikatora zestawu QnA

Jeśli chcesz znaleźć identyfikator zestawu QnA, możesz go znaleźć w dwóch miejscach:

* Umieść wskaźnik myszy na ikonie usuwania w wierszu zestawu QnA, który Cię interesuje. Tekst aktywowania pola zawiera identyfikator zestawu QnA.
* Eksportuj bazę wiedzy. Każdy zestaw QnA w bazie wiedzy zawiera identyfikator zestawu QnA.

## <a name="add-alternate-questions"></a>Dodawanie pytań alternatywnych

Dodaj alternatywne pytania do istniejącego zestawu QnA, aby zwiększyć prawdopodobieństwo dopasowania do kwerendy użytkownika.

![Dodawanie pytań alternatywnych](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="linking-qna-sets"></a>Łączenie zestawów QnA

Łączenie zestawów QnA jest dostarczane z [monitami uzupełniającymi](multiturn-conversation.md). Jest to logiczne połączenie między zestawami QnA, zarządzanymi na poziomie bazy wiedzy. Monity uzupełniające można edytować w portalu programu QnA Maker.

Nie można połączyć zestawów QnA w metadanych odpowiedzi.

## <a name="add-metadata"></a>Dodawanie metadanych

Dodaj zestawy metadanych, najpierw wybierając **opcje widoku,** a następnie wybierając pozycję **Pokaż metadane**. Spowoduje to wyświetlone kolumny metadanych. Następnie wybierz **+** znak, aby dodać zestaw metadanych. Ten zestaw składa się z jednego klucza i jednej wartości.

## <a name="save-changes-to-the-qna-sets"></a>Zapisywanie zmian w zestawach QnA

Okresowo **wybieraj pozycję Zapisz i trenuj** po dokonaniu zmian, aby uniknąć utraty zmian.

![Dodawanie metadanych](../media/qnamaker-how-to-edit-kb/add-metadata.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Współpraca nad bazą wiedzy](./collaborate-knowledge-base.md)

* [Zarządzanie zasobami platformy Azure używanymi przez program QnA Maker](set-up-qnamaker-service-azure.md)