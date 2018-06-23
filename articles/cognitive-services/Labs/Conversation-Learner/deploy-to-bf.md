---
title: Jak wdrożyć robotów uczeń konwersacji - kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak wdrożyć bot uczeń konwersacji.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 77cc998227d996a6e52b1b5629204da5dc735ede
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348592"
---
# <a name="how-to-deploy-a-conversation-learner-bot"></a>Jak wdrożyć bot uczeń konwersacji

Tym dokumencie opisano sposób wdrażania bot uczeń konwersacji — zarówno lokalnie lub na platformie Azure.

## <a name="prerequisite-determine-the-application-id"></a>Wymagania wstępne: identyfikator aplikacji ustalić 

Aby uruchomić bot poza interfejsu użytkownika uczeń konwersacji, należy ustawić uczeń konwersacji identyfikator aplikacji używanego bot — np. Identyfikator modelu uczenia maszynowego w chmurze uczeń konwersacji.  (Natomiast podczas uruchamiania bot za pośrednictwem interfejsu użytkownika uczeń konwersacji, interfejs użytkownika wybiera które identyfikator aplikacji.).  

Oto jak uzyskać identyfikator aplikacji:

1. Uruchom z bot i Interfejsie uczeń konwersacji.  Zobacz Przewodnik Szybki Start, aby uzyskać pełne instrukcje; Podsumowując:

    W oknie polecenia:

    ```
    [open a command window]
    cd my-bot
    npm start
    ```

    W innym oknie polecenia

    ```bash
    [open second command prompt window]
    cd cl-bot-01
    npm run ui
    ```

2. Otwórz przeglądarkę, aby http://localhost:5050 

3. Kliknij aplikację uczeń konwersacji, aby uzyskać identyfikator

4. Kliknij pozycję "Ustawienia" w pasku nawigacyjnym po lewej stronie.

5. Identyfikator GUID "Identyfikator aplikacji" jest wyświetlany w górnej części strony.

## <a name="option-1-deploying-a-conversation-learner-bot-to-run-locally"></a>Opcja 1: Wdrażanie bot uczeń konwersacji, aby uruchomić lokalnie

Wdraża robotów na komputerze lokalnym i pokazuje, jak można do niego dostęp przy użyciu emulatora Bot Framework.

### <a name="configure-your-bot-for-access-outside-the-conversation-learner-ui"></a>Konfigurowanie sieci bot dostępu poza interfejsu użytkownika uczeń konwersacji

Podczas uruchamiania lokalnego robotów, Dodaj identyfikator aplikacji do bot `.env` pliku:

    ```
    CONVERSATION_LEARNER_APP_ID=<YOUR_APP_ID>
    ```

Następnie uruchom z bot:

    ```
    [open a command window]
    cd my-bot
    npm start
    ```

Bot jest teraz uruchomiona lokalnie.  Można do niego dostęp z emulatora Bot Framework.

### <a name="download-and-install-the-emulator"></a>Pobierz i zainstaluj emulator

    ```
    git clone https://github.com/Microsoft/BotFramework-Emulator
    npm install
    npm run build
    npm start
    ```

### <a name="connect-the-emulator-to-your-bot"></a>Emulator nawiązać połączenie z bot

1. W lewym górnym rogu emulatora, w polu "Wprowadź adres URL punktu końcowego", wprowadź `http://127.0.0.1:3978/api/messages`.  Pozostałe pola puste, a następnie kliknij przycisk "Połącz".

2. Możesz teraz są konwersację z Twojej bot.

## <a name="option-2-deploy-to-azure"></a>Opcja 2: Wdrażanie na platformie Azure

Publikowanie z bot uczeń konwersacji podobne do taki sam sposób jak inne bot chcesz opublikować. Na wysokim poziomie Przekaż swój kod do witryny sieci Web hostowanej, ustaw wartości odpowiednią konfigurację i zarejestruj bot różnych kanałów. Szczegółowe instrukcje znajdują się w tym wideo przedstawiający sposób publikowania z bot przy użyciu usługi Azure Bot.

Po wdrożeniu bot i uruchomiony, możesz nawiązać połączenie różnych kanałów takich jak Facebook, zespołów, Skype itp. przy użyciu rejestracja kanału Bot Azure. Dokumentacja o tym procesie, zobacz: https://docs.microsoft.com/en-us/bot-framework/bot-service-quickstart-registration

Poniżej przedstawiono instrukcje krok po kroku dotyczące wdrażania Bot uczeń konwersacji na platformie Azure.  Te instrukcje założono, że jest dostępny ze źródła oparte na chmurze, takich jak VSTS GitHub, BitBucket albo OneDrive źródła bot i skonfiguruje Twojej bot do ciągłego wdrażania.

1. Zaloguj się do portalu Azure pod adresem https://portal.azure.com

2. Tworzenie nowego zasobu "Bot aplikacji sieci Web" 

    1. Nadaj nazwę bot
    2. Kliknij pozycję "Bot szablon", wybierz polecenie "Node.js", wybierz polecenie "Basic", a następnie kliknij przycisk "Select"
    3. Wybierz polecenie "Utwórz", aby utworzyć Bot aplikacji sieci Web.
    4. Poczekaj, aż zasobów Bot aplikacji sieci Web ma zostać utworzony.

3. W portalu Azure Edytuj zasób Bot aplikacji sieci Web, który został utworzony.

    1. Kliknij element nav "Ustawienia aplikacji", po lewej stronie
    1. Przewiń w dół do sekcji "Ustawienia aplikacji"
    2. Dodaj te ustawienia:

        Zmienna środowiskowa | wartość
        --- | --- 
        CONVERSATION_LEARNER_SERVICE_URI | „https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/”
        CONVERSATION_LEARNER_APP_ID      | Aplikacja identyfikatora GUID, uzyskane z interfejsu użytkownika uczeń konwersacji w obszarze "ustawienia" Aplikacja >
        LUIS_AUTHORING_KEY               | LUIS tworzenia klucza dla tej aplikacji
    
    4. W górnej części strony kliknij pozycję "Zapisz"
    5. Otwórz element nav "Kompilacji" po lewej stronie
    6. Kliknij pozycję "Konfiguruj ciągłe wdrażanie" 
    7. Kliknij ikonę "Instalacji" w obszarze wdrożenia
    8. Kliknij pozycję "Wymaga ustawienia"
    9. Wybierz źródło, gdzie dostępna jest opcja kodu bot i skonfigurować źródło.
