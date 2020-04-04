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
ms.date: 04/02/2020
ms.author: diberry
ms.openlocfilehash: 7560fdcbfc77ea2655e8af641794478ead4c11c7
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631450"
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

1. Lista fraz można zastosować do całej aplikacji z ustawieniem **Globalne** lub do określonego modelu (intencji lub encji). Jeśli utworzysz listę fraz, jako _deskryptor_ z intencji lub encji, przełącznik jest ustawiony na nieglobalne. W takim przypadku znaczenie przełącznika jest, że deskryptor jest funkcją tylko tego modelu, w związku z tym _nie globalne_ do aplikacji.

1. Wybierz pozycję **Done** (Gotowe). Nowy deskryptor zostanie dodany do strony **Deskryptory.**

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> Deskryptor można usunąć lub dezaktywować z kontekstowego paska narzędzi na stronie **Deskryptory.**

## <a name="next-steps"></a>Następne kroki

Po dodaniu, edycji, usuwaniu lub dezaktywacji deskryptora ponownie [trenuj i przetestuj aplikację,](luis-interactive-test.md) aby sprawdzić, czy wydajność się poprawia.
