---
title: Jak utworzyć "Hello world" Conversation Learner model-Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Dowiedz się, jak utworzyć model Conversation Learner "Hello world".
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: bb1d053af8813f05872c56d3b5609f2d7d7d5d8c
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705639"
---
# <a name="how-to-create-a-hello-world-model-with-conversation-learner"></a>Jak utworzyć model "Hello world" z Conversation Learner

W tym samouczku pokazano, jak rozpocząć pracę z Conversation Learner, w tym tworzenie akcji, uczenie interakcyjnego bot oraz wprowadzanie poprawek rejestrowanych okien dialogowych, które pochodzą od użytkowników końcowych.

## <a name="video"></a>Połączenia wideo

[![Podgląd samouczka Hello world](https://aka.ms/cl_Tutorial_v3_HelloWorld_Preview)](https://aka.ms/cl_tutorial_v3_helloworld)


## <a name="requirements"></a>Wymagania
Jeśli jeszcze tego nie zrobiono, upewnij się, że zostały wykonane wszystkie kroki instalacji, `.env` w tym tworzenie pliku z kluczem tworzenia Luis.  Aby uzyskać szczegółowe informacje, zobacz [Szybki Start](../quickstart.md) .

Ten samouczek wymaga, aby ogólny samouczek bot był uruchomiony

    npm run tutorial-general

## <a name="steps"></a>Kroki

Uruchom stronę główną w interfejsie użytkownika sieci Web.

### <a name="create-the-model"></a>Tworzenie modelu
1. Kliknij przycisk "nowy model".
2. W polu "nazwa" wpisz "Hello world".
3. Kliknij przycisk "Utwórz".

Powinien być teraz widoczny widok utworzonego modelu.

### <a name="create-an-action"></a>Tworzenie akcji
1. Na panelu po lewej stronie kliknij pozycję akcje, a następnie przycisk "nowa akcja".
    - Akcją może być wiadomość tekstowa, która Conversation Learner powraca do użytkownika, wywołania interfejsu API lub karty.
2. W odpowiedzi "bot"... " Typ pola "Hello".
    - Jest to odpowiedź, którą zwróci bot.
3. Kliknij przycisk "Utwórz".

Utworzono pierwszą akcję, którą może wykonać bot, na przykład zwracają tekstową odpowiedź.

### <a name="train-dialogs"></a>Okna dialogowe uczenia
Jest to miejsce, w którym można nauczyć model odpowiedzi na wyrażenia długości użytkownika.

#### <a name="first-training-dialog"></a>Okno dialogowe pierwszego szkolenia

1. Na panelu po lewej stronie kliknij pozycję "szkolenie okna dialogowego", a następnie przycisk "okno dialogowe nowego uczenia".
2. Wpisz "Witaj" i naciśnij klawisz ENTER.
    - Przykładem, co użytkownik może powiedzieć na początku rozmowy.
3. Kliknij przycisk "akcje oceny".
4. Wybierz pozycję "Hello".
    - W tym przykładowym oknie dialogowym zostało po prostu wykonane jedno pełne. 
5. Wpisz odpowiedź użytkownika, "Pożegnanie".
6. Kliknij przycisk "akcje oceny".
7. Kliknij przycisk "+ Akcja".
8. Wpisz "Pożegnanie" w odpowiedzi "bot"... " , a następnie kliknij przycisk "Utwórz".
    - Zwróć uwagę, że bot odpowiedział na utworzoną właśnie akcję.
9. Kliknij przycisk "Zapisz". 
    - To spowoduje zakończenie tego okna dialogowego szkolenia i jego zapisanie.

Teraz masz jedno okno dialogowe szkolenia w modelu wraz z pojedynczą jednostką i dwiema akcjami.

#### <a name="second-training-dialog"></a>Drugie okno dialogowe szkolenia
Wykonajmy jeszcze jedno szkolenie i zobacz, jak reaguje bot.

1. Kliknij przycisk "okno dialogowe nowego uczenia".
2. Wpisz "Witaj"
    - Jest to podobne do pierwszego okna dialogowego i oczekujemy, że otrzymasz dobry wynik z bot.
3. Kliknij przycisk "akcje oceny".
    - Pozycja i Ocena mogą nadal nie być wystarczająco dokładne i mogą wymagać dodatkowego szkolenia.
4. Wybierz pozycję "Hello".
5. Wpisz odpowiedź użytkownika "Bye".
6. Kliknij przycisk "akcje oceny".
7. Wybierz "Pożegnanie"
8. Kliknij przycisk "Zapisz".

### <a name="log-dialogs"></a>Okna dialogowe dziennika
Jest to miejsce, w którym można testować, wyświetlać i poprawiać konwersacje, których ty lub prawdziwy użytkownicy mieli bot.

#### <a name="test-the-model-as-an-end-user"></a>Testowanie modelu jako użytkownika końcowego
1. Na panelu po lewej stronie kliknij pozycję "dzienniki okien dialogowych", a następnie przycisk "nowe okno dialogowe rejestrowania".
2. Wpisz "Witaj tam".
3. Poczekaj chwilę, bot powinna odpowiedzieć automatycznie przy użyciu "Hello"
4. Wprowadź "byebye"
5. Poczekaj chwilę, a następnie ponownie bot powinna odpowiedzieć automatycznie przy użyciu "Hello".
6. Kliknij przycisk "gotowe do testowania".

#### <a name="view-and-correct-a-user-conversation"></a>Wyświetlanie i poprawianie konwersacji użytkownika
Korzystając z okien dialogowych dziennika, można wyświetlić listę konwersacji użytkowników przechowywanych z bot. Można je również edytować, aby skorygować odpowiedzi bot i zapisać interakcje jako okna dialogowe szkoleń. Aby to zrobić:
1. W siatce kliknij dziennik konwersacji.
2. Kliknij ostatnią akcję bot, na przykład. "Hello".
3. Wybierz "Pożegnanie" Aby poprawić bot.
4. Kliknij przycisk "Zapisz jako okno dialogowe".

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wprowadzenie do szkolenia](./02-intro-to-training.md)
