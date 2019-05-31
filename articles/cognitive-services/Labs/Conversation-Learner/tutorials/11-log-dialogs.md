---
title: Jak rejestrować okien dialogowych w modelu uczeń konwersacji — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak rejestrować okien dialogowych w modelu uczeń konwersacji.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: f1e161fa05a77682d0b5eb1c6c21975ac87028a3
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66387664"
---
# <a name="how-to-log-dialogs-in-a-conversation-learner-model"></a>Jak rejestrować okien dialogowych w modelu uczeń konwersacji

Ten samouczek pokazuje, jak okien dialogowych dziennika są wykorzystywane do lepszego uczenia modeli uczeń konwersacji z zarejestrowane interakcje z użytkownikami w świecie rzeczywistym.

## <a name="video"></a>Połączenia wideo

[![Dziennik okien dialogowych samouczka (wersja zapoznawcza)](https://aka.ms/cl_Tutorial_v3_LogDialogs_Preview)](https://aka.ms/cl_Tutorial_v3_LogDialogs)

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga działa ogólne bot samouczek

    npm run tutorial-general

oraz model pogody, utworzony w poprzednich samouczkach.

## <a name="details"></a>Szczegóły
Okna dialogowe dziennika są zarejestrowane dzienniki Twój bot interakcji z użytkownikami końcowymi. Wykorzystanie tych okien dialogowych dziennika można naprawić etykiety jednostki i wybrane opcje akcji w celu poprawy wydajności i ogólnej wydajności systemu modelu.

## <a name="steps"></a>Kroki

W internetowym interfejsie użytkownika kliknij pozycję "Importuj samouczki" i wybierz model o nazwie "Samouczek-11-LogDialogs".

Ten Model zawiera jedną jednostkę o nazwie "city", a akcje mają odpowiadać na zapytania o pogodzie w określonym mieście. Dwa szkolenie okien dialogowych zostały użyte do nauczenia modelu, więc oczekiwania dotyczące wydajności są nieco niski. Model mogłyby poprawić o dodatkowe szkolenie i ograniczyć narażenie na interakcje użytkownika w świecie rzeczywistym.

### <a name="create-a-new-conversation"></a>Utwórz nową konwersację

1. W lewym panelu kliknij przycisk "Okien dialogowych dziennika", a następnie przycisk "Nowy dziennik Dialog".
2. W panelu rozmowy, w której wyświetlany jest tekst "Typ komunikatu...", wpisz "Austin pogody forecast"
3. Kliknij przycisk "Testowanie gotowe".
4. Kliknij okno dialogowe dziennika "Prognozę pogody Austin" z listy.
5. Kliknij przycisk wypowiedź "Prognozę pogody Austin" w panelu rozmowy.
6. Kliknij przycisk "Austin", a następnie kliknij "city" z listy jednostek.
7. Kliknij przycisk "Prześlij zmiany".
    - Ta zmiana w wartości jednostki spowodować podrzędnego zmiany do konwersacji, ponieważ mamy nowe wartości jednostki w pamięci. Późniejsze akcje prawdopodobnie stały się nieprawidłowe szczególnie te, które obejmujące jednostki "Miasto".
8. Kliknij przycisk "Które city"? Wypowiedź w panelu rozmowy.
9. Wybierz odpowiedź "pogody w Austin jest prawdopodobnie sunny".
10. Kliknij przycisk "Zapisz jako Train Dialog".
    - Szkolenie jest rozpoczęła się od razu

![](../media/T11_logdialog.png)

Ostatnie OneNote. W zależności od potrzeb biznesowych można wyłączyć funkcji rejestrowania konwersacji, przechodząc do ustawień i usuwając zaznaczenie pola "dziennik rozmów."

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wywołanie zwrotne wykrywania jednostki](./12-entity-detection-callback.md)
