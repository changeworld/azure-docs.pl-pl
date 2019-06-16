---
title: Jak używać kart przy użyciu modelu uczeń konwersacji, część 1 — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak używać kart przy użyciu modelu uczeń konwersacji.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: d0b87597c506aca5c3b4f6f3815f58656203ac3c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66389520"
---
# <a name="how-to-use-cards-part-1-of-2"></a>Jak używać kart (część 1 z 2)

W tym samouczku pokazano, jak dodać i korzystać z prostego karty w Twoim czatbocie.

> [!NOTE]
> Uczeń konwersacji aktualnie oczekuje, że pliki definicji karty muszą znajdować się w katalogu o nazwie "kart", który znajduje się w katalogu gdzie Bot została uruchomiona.

## <a name="video"></a>Połączenia wideo

[![Samouczek kart (wersja zapoznawcza)](https://aka.ms/cl_Tutorial_v3_Cards_Preview)](https://aka.ms/cl_Tutorial_v3_Cards)

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga działa ogólne bot samouczek

    npm run tutorial-general

## <a name="details"></a>Szczegóły

Karty są elementy interfejsu użytkownika, które umożliwiają użytkownikom wybrać określoną opcję w konwersacji. 

### <a name="open-the-demo"></a>Otwórz wersję demonstracyjną

W internetowym interfejsie użytkownika kliknij pozycję "Importuj samouczki", a następnie wybierz modelu o nazwie "Samouczek-15-kart".

### <a name="the-card"></a>Karta

Definicja karty jest w następującej lokalizacji: C:\<installedpath\>\src\cards\prompt.json.

System spodziewa się znaleźć Twojej definicji kart w tym katalogu "kart".

![](../media/tutorial13_prompt.PNG)

> [!NOTE]
> Zwróć uwagę, treści, w polu tekstowym wpisz "TextBlock" i "{{pytania}}" — symbol zastępczy.
> Istnieją przedstawia dwa przyciski i tekst, który pobiera złożonych dla każdego.

### <a name="actions"></a>Akcje

Przygotowaliśmy trzy czynności. Jak widać poniżej pierwszej akcji jest kartą.

![](../media/tutorial13_actions.PNG)

Sprawdźmy, tworzenia karty typ akcji:

![](../media/tutorial13_cardaction.PNG)

> [!NOTE]
> Karta zawiera trzy różne parametry - Wprowadź pytanie, przyciski 1 i 2. Te elementy są odwołania do szablonu, na karcie, w którym możesz wprowadzić pytanie i odpowiednich odpowiedzi. Można odwoływać się i używać jednostki lub kombinację tekstu i jednostek.

Ikonę oka pokazuje, jak wygląda karty.

### <a name="practicing-creating-card-actions"></a>Ćwiczenia czynności dotyczące tworzenia karty

1. Na lewym panelu kliknij przycisk "Akcje", a następnie przycisku "Nowa akcja".
2. Wybierz pozycję "Kart", "Typu akcji".
3. Wybierz pozycję "Monituj" z listy "Szablon".
4. W polu "zapytania" typu "Przejdź do lewej lub prawej"
5. W polu "button1" wpisz "po lewej"
6. W polu "button2" wpisz "po prawej"
7. Kliknij przycisk "Anuluj".

### <a name="train-dialog-using-an-adaptive-card"></a>Szkolenie z okna dialogowego, używając karta Adaptacyjna

1. W lewym panelu kliknij przycisk "Okien dialogowych Train", a następnie przycisk "Nowy Train Dialog".
2. W panelu rozmowy, w której wyświetlany jest tekst "Typ komunikatu...", wpisz "hi"
3. Kliknij przycisk "Wynik akcje".
4. Wybierz odpowiedź, "wiersz: zapytania: Przejdź do lewej lub prawej?"
    - Ikonę oka może służyć do karty w wersji zapoznawczej
5. W panelu rozmowy kliknij przycisk "Left" w wierszu renderowany.
6. Kliknij przycisk "Wynik akcje".
7. Wybierz odpowiedź "Po lewej"
8. Kliknij przycisk "Zapisz".
9. Wybierz odpowiedź, "wiersz: zapytania: Przejdź do lewej lub prawej?"
10. W panelu rozmowy kliknij przycisk "Right" w wierszu renderowany.
11. Kliknij przycisk "Wynik akcje".
12. Wybierz odpowiedź, "Right"

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Boty hybrydowe](./16-hybrid-bots.md)
