---
title: Deskryptory — LUIS
titleSuffix: Azure Cognitive Services
description: Użyj Language Understanding (LUIS), aby dodać funkcje aplikacji, które może poprawić Prognozowanie intencje i podmioty lub wykrywania tej kategorii i wzorce
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: diberry
ms.openlocfilehash: 2b5046bb61dcafbba0b0540935e08777fbd747a5
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123121"
---
# <a name="use-descriptors-to-boost-signal-of-word-list"></a>Użyj deskryptorów, aby zwiększyć sygnał listy słów

Można dodać funkcje do aplikacji usługi LUIS, aby zwiększyć jego dokładność. Funkcje pomocy usługi LUIS, zapewniając wskazówek dotyczących tego określonych słów i fraz są częścią słownictwa domeny aplikacji. 

[Deskryptor](luis-concept-feature.md) (lista fraz) zawiera grupę wartości (wyrazów lub fraz), które należą do tej samej klasy i muszą być traktowane podobnie (na przykład nazwy miast lub produktów). Usługa LUIS się o jeden z nich jest automatycznie stosowany do pozostałych. Ta lista nie jest taka sama jak [Jednostka listy](reference-entity-list.md) (dokładne dopasowanie tekstu) pasujących wyrazów.

Deskryptor dodaje do słownictwa domeny aplikacji jako drugi sygnał LUIS o tych słowach.

Zapoznaj się z [pojęciami dotyczącymi funkcji](luis-concept-feature.md) , aby zrozumieć, kiedy i dlaczego należy używać deskryptora. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-descriptor"></a>Dodawanie deskryptora

1. Otwórz aplikację, klikając jej nazwę na stronie **Moje aplikacje** , a następnie kliknij pozycję **Kompiluj**, a następnie kliknij pozycję **deskryptory** w lewym panelu aplikacji. 

1. Na stronie **deskryptorów** kliknij pozycję **+ Dodaj deskryptor**. 
 
1. W oknie dialogowym **Tworzenie nowego deskryptora listy wyrazów** wprowadź nazwę, taką jak `Cities` dla deskryptora. W polu **wartość** wpisz wartości deskryptorów, takie jak `Seattle`. Wpisz jedną wartość lub zbiór wartości oddzielonych przecinkami, a następnie naciśnij klawisz **Enter**.

    > [!div class="mx-imgBorder"]
    > ![dodać miasta deskryptora](./media/luis-add-features/add-phrase-list-cities.png)

    Po wprowadzeniu wystarczającej wartości dla LUIS pojawia się sugestii. Możesz **dodać wszystkie** proponowane wartości lub wybrać poszczególne terminy.

1. Należy pamiętać, że **te wartości są sprawdzane zamiennie** , jeśli dodawane wartości deskryptora są alternatywami, które mogą być używane zamiennie.

1. Wybierz pozycję **Done** (Gotowe). Nowy deskryptor zostanie dodany do strony **deskryptorów** .

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> Można usunąć lub dezaktywować deskryptor z kontekstowego paska narzędzi na stronie **deskryptorów** .

## <a name="next-steps"></a>Następne kroki

Po dodaniu, edycji, usunięciu lub dezaktywowaniu deskryptora ponownie [nauczenie i przetestowanie aplikacji](luis-interactive-test.md) w celu sprawdzenia, czy wydajność jest zwiększona.
