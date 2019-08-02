---
title: Jak używać tagowania wersji z modelem Conversation Learner — Azure Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Dowiedz się, jak korzystać z wersji i tagowania z modelem Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 0279363c039e3ec3c2deac3bc7f71c32c547e9d1
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703375"
---
# <a name="how-to-use-version-tagging"></a>Jak używać tagowania wersji

W tym samouczku pokazano, jak oznaczyć wersje modelu Conversation Learner i ustawić wersję "Live".  

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga użycia emulatora programu bot Framework do tworzenia okien dialogowych dziennika, a nie interfejsu użytkownika sieci Web okna dialogowego.  

Ten samouczek wymaga, aby bot samouczek został uruchomiony:

    npm run tutorial-general

## <a name="details"></a>Szczegóły

Oznakowane wersje modelu są statyczne; nie można ich edytować ani zmieniać. Podczas edytowania modelu zawsze będziesz edytować wersję główną. Po dodaniu nowego tagu Conversation Learner przechwytuje migawkę modelu w tym momencie. 

Bot będzie używać wersji modelu, która została wybrana jako wersja "Live", ale wszystkie konwersacje, które ma, będą widoczne tylko wtedy, gdy "Tag edycji" jest ustawiony na "Master". Jeśli właściwość "Edytuj tag" modelu jest ustawiona na coś innego niż "Master", wówczas można wyświetlić migawkę modelu, ale nie można go zmienić w jakikolwiek sposób.

## <a name="steps"></a>Kroki

### <a name="install-the-bot-framework-emulator"></a>Instalowanie emulatora struktury bot Framework

1. Przejdź do obszaru [https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator) (Ustawienia — Integracje i usługi).
2. Pobierz i zainstaluj emulator.

### <a name="create-a-model"></a>Tworzenie modelu

1. Na stronie głównej listy modeli kliknij `New Model` przycisk.
2. `Name` W polu Typ pola "samouczek-18 — Obsługa wersji" naciśnij klawisz ENTER.
4. Na panelu po lewej stronie kliknij pozycję "Ustawienia".
5. Skopiuj zawartość pola CONVERSATION_LEARNER_MODEL_ID do Schowka.

### <a name="configure-the-emulator"></a>Konfigurowanie emulatora

1. W folderze głównym Conversation Learner Otwórz plik "ENV".
2. Dodaj wiersz do pliku "ENV" w następujący sposób:
    - `CONVERSATION_LEARNER_MODEL_ID=[paste-model-id-from-clipboard-here]`
3. Uruchom ponownie usługę Conversation Learner, polecenie z wiersza polecenia, a następnie uruchamiając program:
    - `npm run tutorial-general`
4. W emulatorze bot Framework Utwórz nową konfigurację bot, ustaw adres URL punktu końcowego na`http://localhost:3978/api/messages`

### <a name="version-1"></a>Wersja 1

Utworzymy jedną akcję dla wersji 1.

1. W lewym panelu interfejsu użytkownika sieci Web kliknij pozycję "akcje", a następnie kliknij `New Action` przycisk.
2. W polu "odpowiedź bot" wpisz "Witaj tam (wersja 1)".
3. Kliknij przycisk `Save`.

Teraz oznaczmy ten tag jako "wersję 1" modelu.

1. W lewym panelu kliknij pozycję "Ustawienia", a następnie kliknij ![](../media/tutorial18_version_tags.PNG)ikonę "znaczniki wersji", aby `New Tag` wyświetlić przycisk, który powinien zostać kliknięty.
    - Nadaj mu nazwę "wersja 1"
1. Na liście rozwijanej "tag dynamiczny" Wybierz pozycję "wersja 1".  
    - Teraz kanały używające tego bot będą używać wersji 1 w naszym modelu.
    - Nie można już zmienić tych okien dialogowych obiektów, akcji i pouczenia tego modelu w wersji 1.
    - W przypadku wybrania opcji "wersja 1" jako "tagu edycji" będziesz mieć możliwość wyświetlania tylko modelu i nie można go edytować.
    - Pozostaw "Tag edycji" ustawiony na "Master", jest to jedyna wersja modelu, który można edytować.

Teraz w siatce "znaczniki wersji" zobaczysz wartość "wersja 1".

### <a name="version-2"></a>Wersja 2

Teraz edytujemy nasz model w celu odróżnienia go od wersji 1.

1. W lewym panelu kliknij pozycję "akcje".
2. W siatce akcji kliknij pozycję "Witaj tam (wersja 1)".
3. Zmień pole "odpowiedź bot" na "Witaj tam (wersja 2)".
4. Kliknij przycisk `Save`.
5. Kliknij przycisk `New Action`.
6. W polu "odpowiedź bot" wpisz "Bye bye (wersja 2)".

### <a name="confirm-bot-framework-emulator-is-using-version-1"></a>Potwierdź, że emulator platformy bot Framework korzysta z wersji 1

1. W emulatorze bot Framework wpisz w komunikacie "Hej tam".
2. Zwróć uwagę, że bot reaguje na "Witaj tam (wersja 1)".
    - Spowoduje to sprawdzenie, czy wersja 1 ma wartość "Live".

### <a name="view-the-conversation-logs-in-conversation-learner-web-ui"></a>Wyświetlanie dzienników konwersacji w Conversation Learner interfejsie użytkownika sieci Web

1. W lewym panelu kliknij pozycję "dzienniki okien dialogowych"
    - Jeśli nie widzisz żadnych okien dialogowych, kliknij przycisk Odśwież.
2. Zwróć uwagę na tag "wersja 1" w siatce.
3. W siatce kliknij pozycję "Witaj tam (wersja 1)"

> [!NOTE]
> Możemy wprowadzić poprawki, wybierając spośród wszystkich aktualnie dostępnych Conversation Learner funkcje, ale te zmiany zostaną wprowadzone do wersji głównej, a nie do wersja 1.

Zobaczysz, jak działa wersja i w jaki sposób można korzystać z bot przy użyciu emulatora platformy bot Framework.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Jednostki wyliczenia i akcje ustawiania jednostek](./tutorial-enum-set-entity.md)
