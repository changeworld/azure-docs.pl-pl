---
title: Definiowanie i używanie przepływów pracy zawartości za pomocą narzędzia Recenzja — Moderator zawartości
titleSuffix: Azure Cognitive Services
description: Projektant przepływu pracy programu Azure Content Moderator służy do definiowania niestandardowych przepływów pracy i progów na podstawie zasad zawartości.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: pafarley
ms.openlocfilehash: 0be77dc1ac249c37f9b59078451d8fbe35f17458
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "72754253"
---
# <a name="define-and-use-moderation-workflows"></a>Definiowanie i używanie przepływów pracy moderowania

W tym przewodniku dowiesz się, jak skonfigurować i używać [przepływów pracy](../review-api.md#workflows) w witrynie sieci Web [narzędzia do przeglądania.](https://contentmoderator.cognitive.microsoft.com) Przepływy pracy to filtry dostosowane do chmury, których można używać do bardziej efektywnego obchodzenia się z zawartością. Przepływy pracy mogą łączyć się z różnymi usługami, aby filtrować zawartość na różne sposoby, a następnie podjąć odpowiednie działania. W tym przewodniku pokazano, jak używać łącznika moderatora zawartości (który jest domyślnie dołączony) do filtrowania zawartości i konfigurowania opinii ludzkich w typowym scenariuszu moderowania.

## <a name="create-a-new-workflow"></a>Tworzenie nowego przepływu pracy

Przejdź do [narzędzia Do recenzji moderatora zawartości](https://contentmoderator.cognitive.microsoft.com/) i zaloguj się. Na karcie **Ustawienia** wybierz pozycję **Przepływy pracy**.

![Ustawienie przepływów pracy](images/2-workflows-0.png)

Na następnym ekranie wybierz pozycję **Dodaj przepływ pracy**.

![Dodawanie przepływu pracy](images/2-workflows-1.png)

### <a name="assign-a-name-and-description"></a>Przypisywanie nazwy i opisu

Nadaj nazwę przepływowi pracy, wprowadź opis i wybierz, czy przepływ pracy będzie obsługiwał obrazy czy tekst.

![Nazwa i opis przepływu pracy](images/image-workflow-create.PNG)

### <a name="define-evaluation-criteria"></a>Definiowanie kryteriów oceny

Na następnym ekranie przejdź do sekcji **Jeśli.** W górnym menu rozwijanym wybierz pozycję **Warunek**. Pozwoli to skonfigurować warunek, na którym przepływ pracy podejmie działania. Jeśli chcesz użyć wielu warunków, wybierz **kombinację** zamiast tego. 

Następnie wybierz łącznik. W tym przykładzie użyto **moderatora zawartości**. W zależności od wybranego łącznika otrzymasz różne opcje dla danych wyjściowych. Zobacz sekcję Łączniki w przewodniku Po [ustawieniach](./configure.md#connectors) narzędzia Przegląd, aby dowiedzieć się, jak skonfigurować inne łączniki.

![Wybieranie łącznika przepływu pracy](images/image-workflow-connect-to.PNG)

Wybierz żądane dane wyjściowe, aby użyć i ustawić warunki, aby sprawdzić go.

![Definiowanie warunku przepływu pracy](images/image-workflow-condition.PNG)

### <a name="define-the-action"></a>Zdefiniuj akcję

Przejdź do sekcji **Następnie,** w której wybierzesz akcję. Poniższy przykład tworzy przegląd obrazu i przypisuje znacznik. Opcjonalnie można dodać alternatywną ścieżkę (Else) i ustawić dla tego akcję.

![Definiowanie akcji przepływu pracy](images/image-workflow-action.PNG)

### <a name="save-the-workflow"></a>Zapisywanie przepływu pracy

Zanotuj nazwę przepływu pracy; aby rozpocząć zadanie moderowania za pomocą interfejsu API przepływu pracy (zobacz poniżej). Na koniec zapisz przepływ pracy za pomocą przycisku **Zapisz** u góry strony.

## <a name="test-the-workflow"></a>Testowanie przepływu pracy

Teraz, po zdefiniowanie niestandardowego przepływu pracy, przetestuj go z przykładową zawartością. Przejdź do **opcji Przepływy pracy** i wybierz odpowiedni przycisk **Wykonaj przepływ pracy.**

![Test przepływu pracy](images/image-workflow-execute.PNG)

Zapisz ten [przykładowy obraz](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg) na dysku lokalnym. Następnie wybierz **pozycję Wybierz pliki i** przekaż obraz do przepływu pracy.

![Biegacz z cytatem nałożonym na obraz](images/sample-text.jpg)

### <a name="track-progress"></a>Śledzenie postępu

Postęp przepływu pracy można wyświetlić w następnym oknie podręcznym.

![Śledzenie wykonywania przepływu pracy](images/image-workflow-job.PNG)

### <a name="verify-workflow-action"></a>Weryfikowanie akcji przepływu pracy

Przejdź do karty **Obraz** w obszarze **Recenzja** i sprawdź, czy istnieje nowo utworzona recenzja obrazu.

![Przegląd obrazów](images/image-workflow-review.PNG)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku dowiesz się, jak skonfigurować i używać przepływów pracy moderowania z [narzędzia Content](https://contentmoderator.cognitive.microsoft.com)Moderator Review . Następnie zapoznaj się z [przewodnikiem interfejsu API REST,](../try-review-api-workflow.md) aby dowiedzieć się, jak programowo tworzyć przepływy pracy.
