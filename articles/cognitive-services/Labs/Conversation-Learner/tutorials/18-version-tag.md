---
title: Jak za pomocą usług znakowania wersji modelu uczeń konwersacji — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak używać przechowywanie wersji i znakowanie z modelem uczeń konwersacji.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 7a1d94700cf84068f918ecff4f4dd433358341e0
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388396"
---
# <a name="how-to-use-version-tagging"></a>Jak używać wersji znakowanie

Ten samouczek przedstawia sposób tag wersji modelu uczeń konwersacji, a następnie ustaw, która wersja jest "na żywo".  

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga Tworzenie dziennika okien dialogowych, nie dziennika okna dialogowego internetowego interfejsu użytkownika przy użyciu emulatora usługi Bot Framework.  

Ten samouczek wymaga, że ogólne samouczek Bot działa:

    npm run tutorial-general

## <a name="details"></a>Szczegóły

Oznakowane wersji modelu są statyczne; Nie można edytować ani zmieniać ich. Edytowanie modelu, są zawsze edycji, wzorzec wersji. Podczas dodawania nowego tagu uczeń konwersacji umożliwia przechwytywanie migawek modelu w danym momencie. 

Twój Bot będzie używać wersji modelu, który wybrano w wersji "Live", ale wszelkie rozmowy, które przedstawiono w nim będzie widoczny tylko wtedy, gdy ustawiono "Edycji Tag" z gałęzią "główną". Jeśli właściwość "Edycji Tag" modelu jest ustawiona na inny niż "Master", następnie można wyświetlić migawkę modelu, ale nie można go zmienić w dowolny sposób.

## <a name="steps"></a>Kroki

### <a name="install-the-bot-framework-emulator"></a>Zainstaluj Bot Framework Emulator

1. Przejdź do obszaru [https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator) (Ustawienia — Integracje i usługi).
2. Pobierz i zainstaluj emulator.

### <a name="create-a-model"></a>Tworzenie modelu

1. Z modelu listy stronie głównej kliknij `New Model` przycisku.
2. W `Name` pola typu "Samouczek-18-Versioning", naciśnij klawisz enter.
4. Na lewym panelu kliknij przycisk "Ustawienia".
5. Skopiuj zawartość pola CONVERSATION_LEARNER_MODEL_ID do Schowka.

### <a name="configure-the-emulator"></a>Konfigurowanie Emulator

1. W folderze głównym uczeń konwersacji Otwórz plik "ENV".
2. Dodaj linię do pliku "ENV" następująco:
    - `CONVERSATION_LEARNER_MODEL_ID=[paste-model-id-from-clipboard-here]`
3. Uruchom ponownie usługę uczeń konwersacji, zakończone, w wierszu polecenia, a następnie ponowne uruchomienie:
    - `npm run tutorial-general`
4. Bot Framework, w emulatorze Utwórz nową konfigurację bot, należy ustawić adres URL punktu końcowego `http://localhost:3978/api/messages`

### <a name="version-1"></a>Wersja 1

Utworzymy jednej akcji dla wersji 1.

1. W lewym panelu interfejs użytkownika sieci Web, kliknij przycisk "Akcje", a następnie kliknij przycisk `New Action` przycisku.
2. W "Response Botów" Wprowadź "cześć miejsca (wersja 1)".
3. Kliknij przycisk `Save`.

Obecnie firma Microsoft będzie Oznacz ten element jako "Wersja 1" modelu.

1. W panelu po lewej stronie kliknij pozycję "ustawienia", a następnie kliknij pozycję ![](../media/tutorial18_version_tags.PNG)ikonę "Wersji Tags", aby wyświetlić `New Tag` przycisk, który należy kliknąć opcję.
    - Nadaj jej nazwę na "Wersja 1"
1. W "na żywo Tag" listy rozwijanej wybierz pozycję "wersja 1".  
    - Teraz kanały przy użyciu tego Bota użyje "Wersja 1" Nasz Model.
    - Nie można zmienić jednostek, akcje i szkolenie okien dialogowych w tej wersji 1 modelu.
    - Wybierz "Wersja 1" jako "Edycja tagu" tylko będzie mogła wyświetlać modelu i nie można go edytować.
    - Pozostaw "edycji Tag" wartość "Master", jest tylko wersja modelu, który można edytować.

Zostanie wyświetlone "Wersja 1" w siatce "Wersji Tags".

### <a name="version-2"></a>Wersja 2

Firma Microsoft będzie teraz edytować nasz Model, w odróżnieniu od wersji 1.

1. W panelu po lewej stronie kliknij pozycję "Akcje".
2. W siatce Akcje kliknij pozycję "cześć miejsca (wersja 1)".
3. Zmiany pola "response Botów" "cześć miejsca (wersja 2)".
4. Kliknij przycisk `Save`.
5. Kliknij przycisk `New Action`.
6. W "Botów odpowiedzi" pola typu "bye bye (wersja 2)".

### <a name="confirm-bot-framework-emulator-is-using-version-1"></a>Upewnij się, że Bot Framework Emulator jest przy użyciu wersji 1

1. Bot Framework, w emulatorze wpisz komunikat, "Hej, istnieje".
2. Należy zauważyć, że Bot odpowiada za pomocą "cześć miejsca (wersja 1)".
    - Sprawdza, czy w wersji 1 jest "na żywo".

### <a name="view-the-conversation-logs-in-conversation-learner-web-ui"></a>Wyświetlanie dzienników konwersacji w sieci Web uczeń konwersacji interfejsu użytkownika

1. W lewym panelu, kliknij na "Okien dialogowych dziennik"
    - Jeśli nie widzisz wszystkie okna dialogowe, kliknij przycisk Odśwież.
2. Zwróć uwagę, tagu "Wersja 1" w siatce.
3. W siatce, wybierz polecenie "cześć miejsca (wersja 1)"

> [!NOTE]
> Firma Microsoft może wprowadzić poprawki, wybierając z wszystkie obecnie dostępne funkcje uczeń konwersacji, jednak te zmiany zostaną wprowadzone do gałęzi głównej, a nie w wersji 1.

Teraz wiesz, jak działa przechowywanie wersji i jak możesz korzystać z botem przy użyciu emulatora usługi Bot Framework.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Demonstracja — Resetowanie hasła](./demo-password-reset.md)
