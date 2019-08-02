---
title: Jak rejestrować okna dialogowe w modelu Conversation Learner — Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Dowiedz się, jak rejestrować okna dialogowe w modelu Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 388942e8195048d7528a54e5a290f1724c8e876b
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703974"
---
# <a name="how-to-log-dialogs-in-a-conversation-learner-model"></a>Jak rejestrować okna dialogowe w modelu Conversation Learner

Ten samouczek pokazuje, w jaki sposób okna dialogowe dzienników są wykorzystywane do lepszego uczenia się Conversation Learner modeli od zarejestrowanych interakcji z użytkownikami w świecie rzeczywistym.

## <a name="video"></a>Połączenia wideo

[![Samouczek dotyczący okien dialogowych dziennika — Podgląd](https://aka.ms/cl_Tutorial_v3_LogDialogs_Preview)](https://aka.ms/cl_Tutorial_v3_LogDialogs)

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga, aby ogólny samouczek bot był uruchomiony

    npm run tutorial-general

i model Pogoda utworzony w poprzednich samouczkach.

## <a name="details"></a>Szczegóły
Okna dialogowe dzienników są zarejestrowanymi dziennikami interakcji bot z użytkownikami końcowymi. Korzystając z tych okien dialogowych dziennika, można naprawić etykiety jednostek i wybrane akcje, aby zwiększyć wydajność modelu i ogólną wydajność systemu.

## <a name="steps"></a>Kroki

W interfejsie użytkownika sieci Web kliknij pozycję "Importowanie samouczków" i wybierz model o nazwie "samouczek-11-LogDialogs".

Ten model zawiera jedną jednostkę o nazwie "miasto" i akcje zaprojektowane w celu reagowania na zapytania o Pogoda w tym mieście. Dwa okna dialogowe uczenia zostały użyte do uczenia modelu, aby oczekiwania na wydajność były nieco niskie. Model ulepsza się z dodatkowymi szkoleniami i narażeniem na realne interakcje użytkowników.

### <a name="create-a-new-conversation"></a>Utwórz nową konwersację

1. Na panelu po lewej stronie kliknij pozycję "dzienniki okien dialogowych", a następnie przycisk "nowe okno dialogowe rejestrowania".
2. W panelu rozmowa, gdzie mówi "wpisz wiadomość...", wpisz "Austin Prognozowanie pogody"
3. Kliknij przycisk "gotowe do testowania".
4. Kliknij z listy okno dialogowe "Prognoza pogody Austin".
5. Kliknij wypowiedź "Austin prognozę pogody" w panelu rozmowy.
6. Kliknij pozycję "Austin", a następnie kliknij pozycję "miasto" na liście jednostek.
7. Kliknij przycisk "Prześlij zmiany".
    - Ta zmiana wartości jednostki powoduje zmianę elementu podrzędnego na konwersację od momentu, gdy mamy nowe wartości jednostki w pamięci. Późniejsze akcje prawdopodobnie staną się nieprawidłowe, szczególnie w odnoszących się do jednostki "miasto".
8. Kliknij "miasto"? wypowiedź w panelu rozmowy.
9. Wybierz odpowiedź, "Pogoda w Austin prawdopodobnie Sunny".
10. Kliknij przycisk "Zapisz jako okno dialogowe".
    - Szkolenie jest natychmiast wyłączone

![](../media/T11_logdialog.png)

Jedna Ostatnia uwaga. W zależności od potrzeb firmy funkcja rejestrowania konwersacji można wyłączyć, przechodząc do pozycji Ustawienia, a następnie usuwając zaznaczenie pola Rejestruj konwersacje.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wywołanie zwrotne wykrywania jednostek](./12-entity-detection-callback.md)
