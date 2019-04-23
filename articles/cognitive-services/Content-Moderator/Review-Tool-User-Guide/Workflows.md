---
title: Definiowanie i korzystanie z zawartości przepływów pracy za pomocą narzędzie do przeglądu - Content Moderator
titlesuffix: Azure Cognitive Services
description: Azure Content Moderator projektanta przepływów pracy można użyć do definiowania niestandardowych przepływów pracy i wartości progowe, oparte na zawartości zasad.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 04/04/2019
ms.author: sajagtap
ms.openlocfilehash: 006f7d6691b8872aaa7ff8ccacff484585761d00
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59795801"
---
# <a name="define-and-use-moderation-workflows"></a>Definiowanie i korzystanie z przepływów pracy moderowania

W tym przewodniku dowiesz się, jak skonfigurować i używać [przepływy pracy](../review-api.md#workflows) na [narzędzie do przeglądu](https://contentmoderator.cognitive.microsoft.com) witryny sieci Web. Przepływy pracy są oparte na chmurze niestandardowe filtry, które można użyć, aby bardziej efektywnie obsługiwać zawartość. Przepływy pracy mogą łączyć się z różnych usług do filtrowania zawartości na różne sposoby, a następnie podjąć odpowiednie działania. Ten przewodnik pokazuje, jak używać łącznika pakiet Content Moderator, (która jest domyślnie włączone) do filtrowania zawartości i skonfiguruj ludziom dokonywanie w scenariuszu typowe moderowania.

## <a name="create-a-new-workflow"></a>Utwórz nowy przepływ pracy

Przejdź do [narzędzie do przeglądu usługi Content Moderator](https://contentmoderator.cognitive.microsoft.com/) i zaloguj się. Na **ustawienia** zaznacz **przepływy pracy**.

![Ustawienie przepływów pracy](images/2-workflows-0.png)

Na następnym ekranie Wybierz **Dodaj przepływ pracy**.

![Dodawanie przepływu pracy](images/2-workflows-1.png)

### <a name="assign-a-name-and-description"></a>Przypisz nazwę i opis

Nazwa przepływu pracy, a następnie wprowadź opis i wybierz, czy przepływ pracy będzie obsługiwać obrazy lub tekst.

![Nazwa przepływu pracy i opis](images/image-workflow-create.PNG)

### <a name="define-evaluation-criteria"></a>Zdefiniuj kryteria oceny

Na następnym ekranie, przejdź do **Jeśli** sekcji. W menu górnej listy rozwijanej wybierz **warunek**. Pozwoli to na konfigurowanie warunku, na którym przepływ pracy podejmiemy akcje. Jeśli chcesz użyć wielu warunków, wybierz opcję **kombinacji** zamiast tego. 

Następnie wybierz łącznik. W tym przykładzie użyto **pakietu Content Moderator**. W zależności od wybranych łącznika uzyskasz różne opcje danych wyjściowych. Zobacz [łączników](./configure.md#connectors) części przewodnika ustawienia narzędzia przeglądu, aby dowiedzieć się, jak skonfigurować inne łączniki.

![Wybierz łącznik przepływu pracy](images/image-workflow-connect-to.PNG)

Wybierz żądaną produktu wyjściowego i określenie warunków, aby sprawdzić, czy przed.

![Zdefiniuj warunek przepływu pracy](images/image-workflow-condition.PNG)

### <a name="define-the-action"></a>Definiowanie akcji

Przejdź do **następnie** sekcji, w którym możesz wybrać akcję. Poniższy przykład tworzy przeglądu obrazu i przypisuje tag. Opcjonalnie możesz dodać alternatywna ścieżka (w przeciwnym razie) i Ustaw akcję w tym także.

![Definiowanie akcji przepływu pracy](images/image-workflow-action.PNG)

### <a name="save-the-workflow"></a>Zapisz przepływ pracy

Zanotuj nazwę przepływu pracy; potrzebna jest nazwa, aby uruchomić zadanie Moderowanie przy użyciu interfejsu API przepływu pracy (patrz poniżej). Na koniec Zapisz w przepływie pracy używającym **Zapisz** znajdujący się u góry strony.

## <a name="test-the-workflow"></a>Testowanie przepływu pracy

Skoro zdefiniowano niestandardowego przepływu pracy, można ją przetestować przy użyciu przykładowej zawartości. Przejdź do **przepływy pracy** i następnie wybierz odpowiedni **wykonania przepływu pracy** przycisku.

![Test przepływu pracy](images/image-workflow-execute.PNG)

Zapisz ten adres [przykładowy obraz](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg) na dysk lokalny. Następnie wybierz pozycję **wybierz pliki** i przekazania obrazu do przepływu pracy.

![Moduł uruchamiający testy z ofertą na obraz](images/sample-text.jpg)

### <a name="track-progress"></a>Śledzenie postępu

Możesz wyświetlić postęp przepływu pracy w następnym oknie podręcznym.

![Śledzenia wykonywania przepływu pracy](images/image-workflow-job.PNG)

### <a name="verify-workflow-action"></a>Sprawdź akcji przepływu pracy

Przejdź do **obraz** karcie **Przejrzyj** i sprawdź, czy jest przegląd nowo utworzonego obrazu.

![Przegląd obrazów](images/image-workflow-review.PNG)

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku przedstawiono sposób konfigurowania i używania Moderowanie przepływów pracy, z usługi Content Moderator [narzędzie do przeglądu](https://contentmoderator.cognitive.microsoft.com). Następnie możesz zapoznać się [Podręcznik interfejsu API REST](../try-review-api-workflow.md) dowiesz się, jak programowo tworzyć przepływy pracy.
