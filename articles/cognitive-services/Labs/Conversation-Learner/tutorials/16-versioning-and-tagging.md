---
title: Jak używać przechowywanie wersji i znakowanie z modelem uczeń konwersacji — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak używać przechowywanie wersji i znakowanie z modelem uczeń konwersacji.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: c7f23d989cbfa0ece9e404a0fe0feb68cf5fddb2
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2018
ms.locfileid: "39170549"
---
# <a name="how-to-use-versioning-and-tagging"></a>Jak używać przechowywanie wersji i znakowanie

Ten samouczek przedstawia sposób tag wersji modelu uczeń konwersacji, a następnie ustaw, która wersja jest "na żywo".  

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga przy użyciu emulatora usługi bot do utworzenia okien dialogowych dziennika, nie dziennika okna dialogowego internetowego interfejsu użytkownika.  

Ten samouczek wymaga działa ogólne bot samouczka:

    npm run tutorial-general

## <a name="details"></a>Szczegóły

Podczas edytowania, są zawsze Edycja tagu o nazwie "główną" — można tworzyć wersje oznakowane z głównej (która zasadniczo migawki główny), ale nie można edytować wersji oznakowane.

## <a name="steps"></a>Kroki

### <a name="install-the-bot-framework-emulator"></a>Zainstaluj Bot framework emulator

- Przejdź do obszaru [https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator) (Ustawienia — Integracje i usługi).
- Pobierz i zainstaluj emulator.

### <a name="create-an-model"></a>Tworzenie modelu

1. Kliknij przycisk Nowy Model
2. W polu Nazwa wprowadź samouczek-16-Versioning
3. Kliknięcie pozycji Utwórz 
4. Kliknij przycisk Ustawienia
5. Skopiuj identyfikator modelu

### <a name="configure-the-emulator"></a>Konfigurowanie emulator

- W folderze głównym uczeń konwersacji Otwórz plik env.
- Wklej identyfikator modelu z wartością CONVERSATION_LEARNER_MODEL_ID
- Uruchom ponownie usługę uczeń konwersacji, zakończone, w wierszu polecenia, a następnie ponowne uruchomienie:
 
    Uruchom samouczek ogólne npm 

### <a name="actions"></a>Akcje

Utwórz akcję:

1. Przełącz się do interfejsu użytkownika sieci Web.
1. Kliknij pozycję operacje, a następnie nową akcję.
2. W odpowiedzi, wprowadź "cześć miejsca (wersja 1)".
3. Kliknij pozycję Zapisz.


![](../media/tutorial16_action1.PNG)

Tworzenie nowego tagu:

3. Kliknij przycisk "ustawienia", a następnie utworzyć nowe "tag".
    - Wywołaj go "wersja 1"
4. Ustaw "wersja 1" jako "na żywo".  
    - Ustawienie na żywo tagu "wersja 1" powoduje użyje kanałów przy użyciu tego bota "wersja 1" tag.
    - Oznakowane wersjach modele nie ma wpływu zmian (zmiana akcje i jednostkami, szkolenie okna dialogowe Dodawanie).  
    - Zmiany w modelu (zmiana akcji, jednostki, szkolenie okna dialogowe Dodawanie) są zawsze wykonywane w tagu "główną".  Innymi słowy "główną" jest tylko znacznik, który można zmienić; inne tagi są stałe migawki.
    - Okna dialogowe w Interfejsie użytkownika uczeń konwersacji zawsze rejestruj użycia wzorca (nie na żywo tag).

![](../media/tutorial16_v1_create.PNG)

Wersja została utworzona w ustawieniach:

![](../media/tutorial16_settings.PNG)

Dodajmy drugiej akcji:

1. Kliknij pozycję operacje, a następnie nową akcję.
2. W odpowiedzi wprowadź "bye bye (wersja 2)".

Edytuj pierwszej akcji:

1. Kliknij menu Akcje.
2. W obszarze Akcje kliknij pozycję "cześć miejsca (wersja 1)".
3. Odpowiedzi na zmiany "cześć miejsca (wersja 2)".

![](../media/tutorial16_hi_there_v2.PNG)

### <a name="switch-to-the-bot-emulator"></a>Przełącz się do emulatora bot

1. W bot interfejsu użytkownika wprowadź "goodbye".
2. Bot odpowiada za pomocą "cześć miejsca (wersja 1)".
    - Ten pokazuje, że wersja 1 "na żywo". 

![](../media/tutorial16_bf_response.PNG)

### <a name="switch-to-the-web-ui"></a>Przejdź do interfejsu użytkownika sieci Web

1. Kliknij dziennik okien dialogowych (Jeśli nie widzisz wszystkie okna dialogowe, kliknij przycisk Odśwież).
2. Kliknij pozycję "cześć miejsca (wersja 2)"

> [!NOTE]
> Firma Microsoft może wprowadzić poprawki, wybierając z wszystkie aktualnie dostępne akcje. Wprowadzone zmiany będą nawiązywane z wzorcem.

Teraz wiesz, jak działa przechowywanie wersji i jak możesz korzystać z botem przy użyciu emulatora usługi Bot framework.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Demonstracja — Resetowanie hasła](./demo-password-reset.md)
