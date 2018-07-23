---
title: Jak utworzyć model uczeń konwersacji "Hello World" — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak utworzyć model uczeń konwersacji "Hello World".
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 70b8f25bd699cbdb069892d65bf766ef3953f59d
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2018
ms.locfileid: "39170875"
---
# <a name="how-to-create-a-hello-world-model-with-conversation-learner"></a>Jak utworzyć model "Hello World" przy użyciu uczeń konwersacji

W tym samouczku pokazano, jak rozpocząć pracę z uczeń konwersacji, takich jak tworzenie akcji, uczyć się interaktywnie i poprawiania rejestrowane okien dialogowych od użytkowników końcowych.

## <a name="video"></a>Połączenia wideo

[![Samouczek 1 (wersja zapoznawcza)](http://aka.ms/cl-tutorial-01-preview)](http://aka.ms/blis-tutorial-01)


## <a name="requirements"></a>Wymagania
Jeśli jeszcze nie, najpierw upewnij się, zostały ukończone wszystkie kroki konfiguracji, takich jak tworzenie `.env` pliku za pomocą usługi LUIS tworzenia klucza.  Zobacz [Szybki Start](https://github.com/Microsoft/ConversationLearner-Samples) Aby uzyskać szczegółowe informacje.

Ten samouczek wymaga działa ogólne bot samouczek

    npm run tutorial-general

## <a name="steps"></a>Kroki

Rozpocznij na stronie głównej w Interfejsie użytkownika sieci Web.

### <a name="create-the-model"></a>Tworzenie modelu
1. Kliknij przycisk Nowy Model
2. W polu Nazwa wprowadź Hello World
3. Kliknięcie pozycji Utwórz

### <a name="create-an-action"></a>Tworzenie akcji

1. Kliknij na podstawie modelu Hello World go uruchomić
2. Kliknij pozycję operacje, a następnie nowa akcja
    - Akcję można wiadomość SMS, która zwraca uczeń konwersacji, do użytkownika, wywołanie interfejsu API lub karty.
3. W odpowiedzi wpisz "Hello World!"
    - To jest odpowiedź, która zwróci robota
4. Kliknięcie pozycji Utwórz

Utworzono pierwszą rzeczą, czyli zauważyć bot zwracania odpowiedzi tekstu.

### <a name="train-the-bot"></a>Uczenie bota

#### <a name="create-the-first-dialog"></a>Pierwsze okno dialogowe tworzenia

1. Kliknij pozycję okna dialogowe szkolenie, polecenie nowe okno Train
2. Wprowadź przykładowy co użytkownik będzie wyświetlany tekst w żebranie konwersacji, na przykład "hello".
3. Kliknij wynik akcji
4. Wybierz pozycję "Hello World!"
    - Spowoduje to utworzenie jednego Włącz przykładowe okno dialogowe. 
2. Wprowadź "goodbye"
3. Kliknij wynik akcji
4. Kliknij pozycję Dodaj akcję, a następnie wprowadź "Goodbye"! w odpowiedzi następnie kliknij przycisk "Utwórz"
5. Kliknij przycisk Gotowe, nauczania. Spowoduje to zakończenie tego okna dialogowego szkolenia.

Masz teraz jedno okno nauczania w systemie.

#### <a name="continue-teaching-the-bot"></a>Kontynuuj, nauczania robota
Teraz należy jeden więcej szkoleń, a następnie zobacz, jak bot odpowiada.

1. Kliknij okno dialogowe Nowy szkolenie
2. Wprowadź "cześć istnieje"
    - Jest to podobne do okna dialogowego pierwszego i oczekujemy, że można uzyskać dobrą ocenę z bota.
2. Kliknij wynik akcji
    - Położenie i oceny nie może jeszcze wystarczająco dokładne i będzie wymagać dodatkowego nauczania.
3. Kliknij Wybierz obok "Hello World!"
4. Następnie wprowadź "bye"
5. Kliknij wynik akcji
6. Wybierz pozycję "Goodbye"!
7. Kliknij przycisk Gotowe, nauczania

![](../media/tutorial1_actions.PNG)

Firma Microsoft korzysta z inną sesją nauczania, aby zobaczyć, jak działa bota.

Powtórz powyższe kroki przy użyciu "Cześć" i "byebye" i Zapisz zmiany w pozycji, a wynik odpowiedzi botów, po kliknięciu wynik akcji.

Możesz teraz Powtórz kroki przy użyciu "howdy" i "good bye" i należy pamiętać, że oceniania poprawę przedstawia wyniki wskazujące bot dowiedziała się ta interakcja.

![](../media/tutorial1_dialogs.PNG)

### <a name="test-the-bot-as-an-end-user"></a>Testowanie bota jako użytkownik końcowy

1. Kliknij pozycję okna dialogowe dziennika, polecenie nowe okno Dziennik
2. Wpisz "hello there"
3. Następnie "bye"

Możesz również spróbować uruchomić konwersację z "bye" i należy pamiętać, bot odpowiedzi.

### <a name="view-conversations-in-the-log-dialogs"></a>Wyświetl konwersacje w dzienniku, okien dialogowych

W oknach dialogowych dziennika, możesz wyświetlić listę konwersacji, aktualizacji i zapisać interakcje jako szkolenia w oknach dialogowych. Aby to zrobić:

1. Kliknij dziennik konwersacji
2. Jeśli konwersacja wygląda dobrze, kliknij ostatnią akcję np. "Goodbye".
3. Kliknij, aby wybrać sugerowane odpowiedzi. 
    - Można również wybrać lub Dodaj kolejną akcję.
4. A następnie kliknij przycisk Gotowe, aby zapisać ten element jako okno dialogowe szkolenia.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Poczekaj chwilę i akcje-wait](./2-wait-vs-nonwait-actions.md)