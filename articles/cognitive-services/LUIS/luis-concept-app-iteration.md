---
title: Zrozumienie aplikacji iteracyjne LUIS projektowania - Azure | Dokumentacja firmy Microsoft
description: LUIS aplikacje wymagają iteracji projektu w celu przeszkolenia LUIS, aby uzyskać najlepsze wyodrębniania danych.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 02/12/2018
ms.author: v-geberr
ms.openlocfilehash: b7f8dd46dc8289322726934f330761b0f1ab94bd
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265941"
---
# <a name="authoring-cycle"></a>Tworzenie cyklu
LUIS uczy się najlepiej w iteracji cyklu zmian modelu, utterance przykłady publikowania i zbieranie danych z zapytania punktu końcowego. 

![Tworzenie cyklu](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-model"></a>Budowanie modelu LUIS
Model służy do ustalenia użytkownika pyta o (zamiar lub zamiar) i które części zapytania Podaj szczegóły (jednostki), które pomagają w ustaleniu odpowiedzi. 

Model musi być specyficzne dla domeny aplikacji, aby określić słowa i zwroty, czy są odpowiednie, a także typowe word porządkowania. 

Ten model zawiera opcje jednostek. 

## <a name="add-training-examples"></a>Dodaj przykłady szkolenia
LUIS musi zniesławiających przykładzie w lokalizacji docelowych. Przykłady muszą za mało odmianą word wybór i kolejność słów, aby można było ustalić, który zamiar utterance są przeznaczone. Każdy utterance przykładzie trzeba wszelkie wymagane dane oznaczone jako jednostek. 

Poinstruuj LUIS zignorowanie zniesławiających, które nie mają znaczenia dla domeny aplikacji, przypisując utterance do **Brak** celem. Wszelkie słów ani fraz, który nie ma potrzeby pobierane poza utterance nie trzeba być oznaczony jako. Brak jest etykiety dla słów ani fraz do ignorowania. 
<!--
## Not just yet
Do not add features such as a [phrase list](luis-concept-feature.md) feature in your first cycle. Phrase lists are phrases that would be specific to your app's subject area.  
-->
## <a name="train-and-publish-the-app"></a>Szkolenie i publikowanie aplikacji
Po utworzeniu zniesławiających różnych 10 – 15 w każdym polu Opcje z podmiotami wymagane etykietą, możesz uczenia LUIS, a następnie pobrać punktów końcowych. Upewnij się, że tworzenie aplikacji i publikowanie aplikacji, dzięki czemu jest on dostępny w [regionów punktu końcowego](luis-reference-regions.md) potrzebne. 

## <a name="https-endpoint-testing"></a>Testowanie punkt końcowy HTTPS
Można przetestować aplikację LUIS z punktem końcowym HTTPS znajduje się na **[publikowania](publishapp.md)** strony. Testowanie z punktu końcowego umożliwia LUIS wybrać wszystkie zniesławiających z niskim zaufania do przeglądu.  

## <a name="recycle"></a>Kosz
Po zakończeniu z cyklem tworzenia, możesz rozpocząć ponownie. Rozpocząć od sprawdzenia zniesławiających punktu końcowego, LUIS oznaczone bez obaw niski. Sprawdź te zniesławiających zamiar i jednostki. Po przejrzeniu zniesławiających listy przeglądu powinien być pusty.  

## <a name="batch-testing"></a>Testowanie usługi partia zadań
Testowanie partii jest sposób, aby zobaczyć, jak wiele zniesławiających przykładzie są oceniane przez LUIS. Przykłady powinno być nowe dla LUIS i powinna być poprawnie oznaczone zamierzone i ma LUIS można znaleźć jednostki. Wyniki testu wskazują, jak LUIS przeprowadza się w tym zestawie zniesławiających. 

## <a name="next-steps"></a>Kolejne kroki

Więcej informacji na temat pojęć [współpracy](luis-concept-collaborator.md).

[luis-reference-prebuilt-domains]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-domains