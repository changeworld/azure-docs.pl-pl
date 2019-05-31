---
title: Jak utworzyć model uczeń konwersacji "Hello World" — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak utworzyć model uczeń konwersacji "Hello World".
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: fe5d21fadef8f4452ba36259dbf89cefc78230de
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388064"
---
# <a name="how-to-create-a-hello-world-model-with-conversation-learner"></a>Jak utworzyć model "Hello World" przy użyciu uczeń konwersacji

W tym samouczku pokazano, jak rozpocząć pracę z uczeń konwersacji, m.in. tworzenia akcji nauczania Bot interaktywnego i wprowadzanie poprawek rejestrowane okien dialogowych, które pochodzą od użytkowników końcowych.

## <a name="video"></a>Połączenia wideo

[![Witaj świecie samouczka (wersja zapoznawcza)](https://aka.ms/cl_Tutorial_v3_HelloWorld_Preview)](https://aka.ms/cl_tutorial_v3_helloworld)


## <a name="requirements"></a>Wymagania
Jeśli jeszcze nie, najpierw upewnij się, zostały ukończone wszystkie kroki konfiguracji, takich jak tworzenie `.env` pliku za pomocą usługi LUIS tworzenia klucza.  Zobacz [Szybki Start](../quickstart.md) Aby uzyskać szczegółowe informacje.

Ten samouczek wymaga, że ogólne samouczek Bot działa

    npm run tutorial-general

## <a name="steps"></a>Kroki

Rozpocznij na stronie głównej w Interfejsie użytkownika sieci Web.

### <a name="create-the-model"></a>Tworzenie modelu
1. Kliknij przycisk "Nowy Model".
2. W polu "Nazwa" wpisz "Hello World".
3. Kliknij przycisk "Utwórz".

Powinien zostać wyświetlony widok modelu, który został utworzony.

### <a name="create-an-action"></a>Tworzenie akcji
1. Na lewym panelu kliknij przycisk "Akcje", a następnie przycisku "Nowa akcja".
    - Akcję można wiadomość SMS, która zwraca uczeń konwersacji, do użytkownika, wywołanie interfejsu API lub karty.
2. W "Botów odpowiedzi..." pola typu "Hello".
    - Jest to odpowiedź, która zwróci robota.
3. Kliknij przycisk "Utwórz".

Utworzono pierwszą akcję, która Bota mogą wykonywać, czyli zwracają tekst odpowiedzi.

### <a name="train-dialogs"></a>Szkolenie w oknach dialogowych
Jest to, gdzie uczenie modelu w sposób reagowania na wypowiedzi użytkowników.

#### <a name="first-training-dialog"></a>Pierwsze okno szkolenia

1. W lewym panelu kliknij przycisk "Okien dialogowych Train", a następnie przycisk "Nowy Train Dialog".
2. Typ "Hi" naciśnij klawisz enter.
    - Jako przykład to, jak użytkownik może Załóżmy, że na początku konwersacji.
3. Kliknij przycisk "Wynik akcje".
4. Select "Hello".
    - Pełne ruchu w tym oknie dialogowym przykład jest właśnie został ukończony. 
5. Wpisz odpowiedź użytkownika "Goodbye".
6. Kliknij przycisk "Wynik akcje".
7. Kliknij przycisk "+ akcji" przycisk.
8. Typ "Goodbye!" "Botów odpowiedzi..." pole, a następnie kliknij przycisk "Utwórz".
    - Należy zauważyć, że Bot odpowiedziała, zgłaszając akcji właśnie utworzony.
9. Kliknij przycisk "Zapisz". 
    - To będzie kończyć się i Zapisz to okno dialogowe szkolenia.

Masz teraz jedno okno szkolenia w modelu, wraz z pojedynczej jednostki i dwie akcje.

#### <a name="second-training-dialog"></a>Drugie okno dialogowe szkolenia
Przejdźmy wykonaj jeden więcej szkoleń i zobacz, jak reaguje robota.

1. Kliknij przycisk "Nowy Train Dialog".
2. Typ, "Hi"
    - Jest to podobne do okna dialogowego pierwszego i oczekujemy, że można uzyskać dobrą ocenę z Bota.
3. Kliknij przycisk "Wynik akcje".
    - Położenie i ocena może nadal być niewystarczająco dokładne i może wymagać dodatkowych szkoleń.
4. Select "Hello".
5. Wpisz odpowiedź użytkownika "tworzycie".
6. Kliknij przycisk "Wynik akcje".
7. Wybierz pozycję "Goodbye"!
8. Kliknij przycisk "Zapisz".

### <a name="log-dialogs"></a>Okna dialogowe dziennika
To, gdzie możesz przetestować, wyświetlić i poprawić rozmowy, których użytkownik lub rzeczywistych użytkowników z botem.

#### <a name="test-the-model-as-an-end-user"></a>Testowanie modelu jako użytkownik końcowy
1. W lewym panelu kliknij przycisk "Okien dialogowych dziennika", a następnie przycisk "Nowy dziennik Dialog".
2. Wpisz "hello istnieje".
3. Poczekaj chwilę, Bot powinien odpowiadać automatycznie ciągiem "Hello"
4. Wprowadź "byebye"
5. Poczekaj chwilę, ponownie Bot powinien odpowiadać automatycznie ciągiem "Hello".
6. Kliknij przycisk "Testowanie gotowe".

#### <a name="view-and-correct-a-user-conversation"></a>Wyświetlanie i popraw konwersacji użytkownika
Korzystając z okien dialogowych dziennika, można wyświetlić listę konwersacji użytkowników są przechowywane z botem. Można również edytować je, aby można było rozwiązać Bot odpowiedzi, a następnie zapisać interakcje jako szkolenia okien dialogowych. Aby to zrobić:
1. W siatce kliknij w dzienniku konwersacji.
2. Kliknij ostatnią akcję Bot np. "Hello".
3. Wybierz pozycję "Goodbye"! Aby poprawić robota.
4. Kliknij przycisk "Zapisz jako Train Dialog".

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wprowadzenie do szkolenia](./02-intro-to-training.md)
