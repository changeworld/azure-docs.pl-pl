---
title: Listy fraz — LUIS
titleSuffix: Azure Cognitive Services
description: Użyj Language Understanding (LUIS), aby dodać funkcje aplikacji, które mogą ulepszyć wykrywanie lub przewidywania intencji i jednostek, które są kategoriami i wzorcami
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: diberry
ms.openlocfilehash: 0e3e4226eaaa0505eea96d8b3aca820f2327349e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467615"
---
# <a name="use-phrase-lists-to-boost-signal-of-word-list"></a>Użyj list fraz, aby zwiększyć liczbę sygnałów listy słów

Aby poprawić swoją dokładność, możesz dodać funkcje do aplikacji LUIS. Funkcje ułatwiają LUIS, dostarczając wskazówki, że określone słowa i frazy są częścią słownictwa domeny aplikacji. 

[Lista fraz](luis-concept-feature.md) zawiera grupę wartości (wyrazów lub fraz) należących do tej samej klasy i musi być traktowana podobnie (na przykład nazwy miast lub produktów). LUIS informacje o jednym z nich są również automatycznie stosowane do innych. Ta lista nie jest taka sama jak [Jednostka listy](reference-entity-list.md) (dokładne dopasowanie tekstu) pasujących wyrazów.

Lista fraz dodaje do słownictwa domeny aplikacji jako drugi sygnał LUIS o tych słowach.

Zapoznaj się z [pojęciami dotyczącymi funkcji](luis-concept-feature.md) , aby zrozumieć, kiedy i dlaczego należy używać listy fraz. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="add-phrase-list"></a>Dodaj listę fraz

LUIS umożliwia maksymalnie 10 list fraz na aplikację. 

1. Otwórz aplikację, klikając jej nazwę na stronie **Moje aplikacje** , a następnie kliknij pozycję **Kompiluj**, a następnie kliknij pozycję **listy fraz** w lewym panelu aplikacji. 

1. Na stronie **listy fraz** kliknij pozycję **Utwórz nową listę fraz**. 
 
1. W oknie dialogowym **Dodawanie listy fraz** wpisz `Cities` jako nazwę listy fraz. W polu **wartość** wpisz wartości listy fraz. Można wpisać jedną wartość naraz lub zestaw wartości rozdzielonych przecinkami, a następnie nacisnąć klawisz **Enter**.

    ![Dodawanie miejscowości z listy wyrazów](./media/luis-add-features/add-phrase-list-cities.png)

1. LUIS może zaproponować powiązane wartości, aby dodać je do listy fraz. Kliknij pozycję **zalecamy** , aby uzyskać grupę proponowanych wartości, które są semantycznie powiązane z dodanymi wartościami. Możesz kliknąć dowolne proponowane wartości lub kliknąć przycisk **Dodaj wszystko** , aby je dodać.

    ![Proponowane wartości listy fraz — Dodaj wszystko](./media/luis-add-features/related-values.png)

1. Kliknij **te wartości są zamienne** , jeśli dodane wartości listy fraz są alternatywami, których można użyć zamiennie.

    ![Proponowane wartości na liście fraz — wybierz pole, które jest zamienne](./media/luis-add-features/interchangeable.png)

1. Kliknij przycisk **Gotowe**. Lista fraz "miasta" została dodana do strony **list fraz** .

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> Listę fraz można usunąć lub dezaktywować z poziomu kontekstowego paska narzędzi na stronie **listy fraz** .

## <a name="next-steps"></a>Następne kroki

Po dodaniu, edycji, usunięciu lub dezaktywowaniu listy fraz należy ponownie [nauczyć się i przetestować aplikację](luis-interactive-test.md) , aby sprawdzić, czy wydajność jest zwiększona.
