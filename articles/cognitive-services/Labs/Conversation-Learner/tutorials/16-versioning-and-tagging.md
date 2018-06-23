---
title: Jak używać wersji i znakowanie z aplikacją uczeń konwersacji - kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak używać wersji i znakowanie z aplikacją uczeń konwersacji.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: ea013db078ff33f8597b0e15a8fc951e8ae320e8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348645"
---
# <a name="how-to-use-versioning-and-tagging"></a>Jak używać wersji i znakowanie

W tym samouczku ilustruje sposób tagu wersje aplikacji uczeń konwersacji i ustaw wersję jest "na żywo".  

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga przy użyciu emulatora bot można utworzyć okna dialogowe dziennika, nie interfejs sieci Web okna dialogowego dziennika.  

Ten samouczek wymaga działa ogólne bot samouczka:

    npm run tutorial-general

## <a name="details"></a>Szczegóły

Podczas edytowania, zawsze edytujesz tagu o nazwie "główny" — oznakowany wersji można utworzyć na podstawie wzorca (który zasadniczo Utwórz migawkę głównego), ale nie można edytować wersji oznakowanych.

## <a name="steps"></a>Kroki

### <a name="install-the-bot-framework-emulator"></a>Zainstaluj Bot framework emulator

- Przejdź do obszaru [https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator) (Ustawienia — Integracje i usługi).
- Pobierz i zainstaluj emulator.

### <a name="create-an-app"></a>Tworzenie aplikacji

1. Kliknij przycisk nowej aplikacji
2. W polu nazwy wpisz samouczek-16-Versioning
3. Kliknięcie pozycji Utwórz 
4. Kliknij przycisk Ustawienia
5. Skopiuj identyfikator aplikacji

### <a name="configure-the-emulator"></a>Skonfiguruj emulator

- W folderze głównym uczeń konwersacji Otwórz plik .env.
- Wklej jako wartość CONVERSATION_LEARNER_APP_ID identyfikator aplikacji
- Uruchom ponownie usługę uczeń konwersacji został zakończony z wiersza polecenia, a następnie ponowne uruchamianie:
 
    npm uruchom samouczek — ogólne 

### <a name="actions"></a>Akcje

Umożliwia tworzenie akcji:

1. Przełącz się do interfejsu użytkownika sieci Web.
1. Kliknij przycisk akcje, a następnie nowej akcji.
2. W odpowiedzi, wprowadź "cześć miejsca (wersja 1)".
3. Kliknij przycisk Zapisz.


![](../media/tutorial16_action1.PNG)

Tworzenie nowego tagu:

3. Kliknij pozycję "ustawienia" i utworzyć nowe "tag".
    - Wywołać ją "wersja 1"
4. Wartość "wersja 1" jako "na żywo".  
    - Ustawienie na żywo znacznik "w wersji 1" powoduje użyje kanałów przy użyciu tego bot "w wersji 1" tag.
    - Zmiany (zmiana akcje, jednostek, dodawanie train okien dialogowych) nie dotyczy oznakowanych wersje aplikacji.  
    - Do aplikacji (zmiana akcje, jednostek, dodając train okien dialogowych) są zawsze wprowadzone w tagu "master".  Innymi słowy "główny" jest tylko tag, który można zmienić; inne tagi są stałe migawki.
    - Okna dialogowe w interfejsie użytkownika uczeń konwersacji zawsze rejestruj Użyj wzorca (nie znacznik na żywo).

![](../media/tutorial16_v1_create.PNG)

Uwaga wersja została utworzona w ustawieniach:

![](../media/tutorial16_settings.PNG)

Dodajmy drugiej akcji:

1. Kliknij przycisk akcje, a następnie nowej akcji.
2. W odpowiedzi wprowadź "bye bye (wersja 2)".

Edytuj pierwszy akcji:

1. Kliknij opcję Akcje.
2. W obszarze Akcje kliknij "cześć miejsca (wersja 1)".
3. Zmień odpowiedź na "cześć miejsca (wersja 2)".

![](../media/tutorial16_hi_there_v2.PNG)

### <a name="switch-to-the-bot-emulator"></a>Przełącz się do emulatora bot

1. W bot interfejsu użytkownika wpisz "goodbye".
2. Uwaga bot odpowie "cześć miejsca (wersja 1)".
    - Oznacza to, że wersja 1 "na żywo". 

![](../media/tutorial16_bf_response.PNG)

### <a name="switch-to-the-web-ui"></a>Przełącz się do interfejsu użytkownika sieci Web

1. Kliknij dziennik okien dialogowych (Jeśli nie widzisz wszystkie okna dialogowe, Odśwież aplikacji).
2. Kliknij pozycję "cześć miejsca (wersja 2)"

Należy pamiętać, że firma Microsoft może wprowadzać poprawki z wszystkie aktualnie dostępne akcje. Te zmiany zostaną wprowadzone do wzorca.

Ma teraz widoczna, jak działa przechowywanie wersji i jak interakcyjnie bot przy użyciu emulatora framework Bot.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wersja demonstracyjna - resetowania hasła](./demo-password-reset.md)
