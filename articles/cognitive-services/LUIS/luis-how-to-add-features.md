---
title: Deskryptory — usługa LUIS
titleSuffix: Azure Cognitive Services
description: Użyj funkcji uspokajania języka (LUIS), aby dodać funkcje aplikacji, które mogą poprawić wykrywanie lub przewidywanie intencji i encji, które kategorii i wzorców
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74123121"
---
# <a name="use-descriptors-to-boost-signal-of-word-list"></a>Używanie deskryptorów do zwiększania sygnału listy wyrazów

Można dodać funkcje do aplikacji usługi LUIS, aby zwiększyć jej dokładność. Funkcje pomagają usługi LUIS, zapewniając wskazówki, że niektóre słowa i frazy są częścią słownictwa domeny aplikacji. 

[Deskryptor](luis-concept-feature.md) (lista fraz) zawiera grupę wartości (słów lub fraz), które należą do tej samej klasy i muszą być traktowane podobnie (na przykład nazwy miast lub produktów). Co usługa LUIS dowiaduje się o jednym z nich jest automatycznie stosowane do innych, jak również. Ta lista nie jest tym samym, co [encja listy](reference-entity-list.md) (dokładne dopasowanie tekstu) dopasowanych wyrazów.

Deskryptor dodaje do słownictwa domeny aplikacji jako drugi sygnał do usługi LUIS o tych wyrazach.

Przejrzyj [pojęcia funkcji,](luis-concept-feature.md) aby dowiedzieć się, kiedy i dlaczego należy używać deskryptora. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-descriptor"></a>Dodaj deskryptor

1. Otwórz aplikację, klikając jej nazwę na stronie **Moje aplikacje,** a następnie kliknij pozycję **Zbuduj**, a następnie kliknij **pozycję Deskryptory** w lewym panelu aplikacji. 

1. Na stronie **Deskryptory** kliknij pozycję **+ Dodaj deskryptor**. 
 
1. W oknie dialogowym **Tworzenie deskryptora listy nowych fraz** wprowadź nazwę, taką jak `Cities` deskryptor. W polu **Wartość** wpisz wartości deskryptorów, `Seattle`takie jak . Można wpisać jedną wartość naraz lub zestaw wartości oddzielonych przecinkami, a następnie **nacisnąć klawisz Enter**.

    > [!div class="mx-imgBorder"]
    > ![Dodawanie deskryptorów Miasta](./media/luis-add-features/add-phrase-list-cities.png)

    Po wprowadzeniu wystarczającej liczby wartości dla usługi LUIS pojawiają się sugestie. Możesz **+ Dodać wszystkie** proponowane wartości lub wybrać poszczególne terminy.

1. Zachowaj **Te wartości są wymienne** sprawdzane, czy dodane wartości deskryptora są alternatywami, które mogą być używane zamiennie.

1. Wybierz pozycję **Done** (Gotowe). Nowy deskryptor zostanie dodany do strony **Deskryptory.**

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> Deskryptor można usunąć lub dezaktywować z kontekstowego paska narzędzi na stronie **Deskryptory.**

## <a name="next-steps"></a>Następne kroki

Po dodaniu, edycji, usuwaniu lub dezaktywacji deskryptora ponownie [trenuj i przetestuj aplikację,](luis-interactive-test.md) aby sprawdzić, czy wydajność się poprawia.
