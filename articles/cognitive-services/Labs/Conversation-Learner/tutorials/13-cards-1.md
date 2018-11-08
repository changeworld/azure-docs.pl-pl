---
title: Jak używać kart przy użyciu modelu uczeń konwersacji, część 1 — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak używać kart przy użyciu modelu uczeń konwersacji.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: da261beeec4f02dfa7c7cf9071e51dc17cf5c7cd
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51254387"
---
# <a name="how-to-use-cards-part-1-of-2"></a>Jak używać kart (część 1 z 2)

W tym samouczku pokazano, jak dodać i korzystać z prostego karty w Twoim czatbocie.

> [!NOTE]
> Uczeń konwersacji oczekuje plików definicji karty muszą znajdować się w katalogu o nazwie "kart", który znajduje się w katalogu gdzie bot została uruchomiona. Firma Microsoft ustawi tę można skonfigurować w przyszłości.

## <a name="video"></a>Połączenia wideo

[![Samouczek 13 (wersja zapoznawcza)](https://aka.ms/cl-tutorial-13-preview)](https://aka.ms/blis-tutorial-13)

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga działa ogólne bot samouczek

    npm run tutorial-general

## <a name="details"></a>Szczegóły

Karty są elementy interfejsu użytkownika, które umożliwiają użytkownikom wybrać określoną opcję w konwersacji. 

### <a name="open-the-demo"></a>Otwórz wersję demonstracyjną

Na liście modelu interfejsu użytkownika sieci web należy kliknąć samouczek-13 kart 1. 

### <a name="the-card"></a>Karta

Definicja karty jest w następującej lokalizacji: C:\<installedpath\>\src\cards\prompt.json.

System oczekuje, że można znaleźć definicji karty, w tym katalogu kart.

![](../media/tutorial13_prompt.PNG)

> [!NOTE]
> Zwróć uwagę, typ treści `TextBlock` i `{{question}}` symbolu zastępczego w polu tekstowym.
> Istnieją przedstawia dwa przyciski i tekst, który pobiera złożonych dla każdego.

### <a name="actions"></a>Akcje

Przygotowaliśmy trzy czynności. Jak widać poniżej pierwszej akcji jest kartą.

![](../media/tutorial13_actions.PNG)

Sprawdźmy, tworzenia karty typ akcji:

![](../media/tutorial13_cardaction.PNG)

> [!NOTE]
> Dane wejściowe zapytania i przyciski 1 i 2. Są odwołania do szablonu, na karcie, którym możesz wprowadzić pytanie i odpowiednich odpowiedzi. Można odwoływać się i używać jednostki lub kombinację tekstu i jednostek.

Ikonę oka pokazuje, jak wygląda karty.

### <a name="train-dialog"></a>Okno dialogowe szkolenie

Przejdźmy teraz przez okno dialogowe nauczania.

1. Kliknij przycisk okien dialogowych szkolenie, polecenie nowe okno pociągu.
1. Wprowadź "Cześć".
2. Kliknij wynik akcji.
3. Kliknij, aby wybrać "Monituj go w lewo lub w prawo".
    - Kliknięcie przycisku "left" lub "right" jest odpowiednikiem użytkownika, wpisując "left" lub "prawa" odpowiednio. 
4. Kliknij wynik akcji.
4. Kliknij Wybierz opcję "lewej". To działanie bez oczekiwania.
6. Kliknij, aby wybrać "Monituj go w lewo lub w prawo".
4. Kliknij przycisk "po prawej".
5. Kliknij wynik akcji.
3. Kliknij, aby wybrać "Right"
6. Kliknij, aby wybrać "Monituj go w lewo lub w prawo".
4. Kliknij przycisk Done testowania.

Teraz wiesz, jak działają kart. Są one definiowane w katalogu karty za szablonów json. Szablony ujawni w interfejsie użytkownika, który można wypełnić przy użyciu ciągu lub jednostki lub kombinacji obu.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Karty, część 2](./14-cards-2.md)
