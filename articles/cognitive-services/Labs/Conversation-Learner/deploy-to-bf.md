---
title: Jak wdrożyć robota uczeń konwersacji — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak wdrożyć bot uczeń konwersacji.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: ae984cc2e0f43b81b8aa2f08b3944886733c9054
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60848269"
---
# <a name="how-to-deploy-a-conversation-learner-bot"></a>Jak wdrożyć bot uczeń konwersacji

W tym dokumencie wyjaśniono, jak wdrożyć bot uczeń konwersacji — lokalnie lub na platformie Azure.

## <a name="prerequisite-determine-the-model-id"></a>Wymagania wstępne: określania Identyfikatora modelu 

Aby uruchomić bot poza UI uczeń konwersacji, należy ustawić uczeń konwersacji modelu identyfikator który będzie używać bot — czyli identyfikator modelu uczenia maszynowego w chmurze uczeń konwersacji.  (Z drugiej strony, uruchamiając bota przy użyciu interfejsu użytkownika uczeń konwersacji, interfejs użytkownika wybiera które identyfikatora modelu.).  

Oto jak uzyskać identyfikator modelu:

1. Rozpocznij bota i interfejsu użytkownika uczeń konwersacji.  Zobacz Przewodnik Szybki Start, aby uzyskać pełne instrukcje; Podsumowując:

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

2. Otwórz przeglądarkę, aby `http://localhost:5050` 

3. Kliknij w celu uzyskania Identyfikatora modelu uczeń konwersacji

4. Kliknij przycisk "Ustawienia" na pasku nawigacyjnym po lewej stronie.

5. "Identyfikator modelu" identyfikator GUID jest wyświetlany w górnej części strony.

## <a name="option-1-deploying-a-conversation-learner-bot-to-run-locally"></a>Opcja 1: Wdrażanie bot uczeń konwersacji, aby uruchomić lokalnie

Wdraża robota na komputerze lokalnym i pokazuje, jak można do niego dostęp przy użyciu emulatora usługi Bot Framework.

### <a name="configure-your-bot-for-access-outside-the-conversation-learner-ui"></a>Konfigurowanie bota dostępu poza UI uczeń konwersacji

Podczas uruchamiania lokalnego robota, Dodaj identyfikator aplikacji do botów `.env` pliku:

    ```
    CONVERSATION_LEARNER_MODEL_ID=<YOUR_MODEL_ID>
    ```

Następnie uruchom bota:

    ```
    [open a command window]
    cd my-bot
    npm start
    ```

Bot jest teraz uruchomiona lokalnie.  Można do niego dostęp za pomocą emulatora platformy Bot Framework.

### <a name="download-and-install-the-emulator"></a>Pobierz i zainstaluj emulator

    ```
    git clone https://github.com/Microsoft/BotFramework-Emulator
    npm install
    npm run build
    npm start
    ```

### <a name="connect-the-emulator-to-your-bot"></a>Emulator nawiązać połączenie z botem

1. W lewym górnym rogu emulatora, w polu "Wprowadź adres URL punktu końcowego", wprowadź `http://127.0.0.1:3978/api/messages`.  Pozostaw inne pola są puste, a następnie kliknij przycisk "Połącz".

2. Możesz teraz są konwersację z botem.

## <a name="option-2-deploy-to-azure"></a>Opcja 2: Wdrażanie na platformie Azure

Opublikuj bota uczeń konwersacji, podobnie jak chcesz opublikować innych bot tak samo. Na wysokim poziomie możesz przekazywanie kodu do witryny sieci Web hostowanej, ustaw wartości prawidłowej konfiguracji, a następnie zarejestrować bota przy użyciu różnych kanałów. Szczegółowe instrukcje znajdują się w tym wideo przedstawiający sposób publikowania bota przy użyciu usługi Azure Bot Service.

Po wdrożeniu bot i uruchomionych możesz nawiązać połączenie różnych kanałów takich jak Facebook, Teams, Skype itp. Korzystanie z usługi Azure Bot kanału rejestracji. Dokumentacja na temat tego procesu, zobacz: https://docs.microsoft.com/bot-framework/bot-service-quickstart-registration

Poniżej przedstawiono instrukcje krok po kroku dotyczące wdrażania Bot uczeń konwersacji na platformie Azure.  W instrukcjach założono, że Twoje źródło bot jest dostępny ze źródła oparta na chmurze, takich jak Azure DevOps Services, GitHub, BitBucket lub OneDrive i skonfiguruje Twój bot ciągłego wdrażania.

1. Zaloguj się do witryny Azure portal pod https://portal.azure.com

2. Utwórz nowy zasób "Bot aplikacji sieci Web" 

    1. Nazwij robota
    2. Kliknij pozycję "Szablonu Bota", wybierz pozycję "Node.js", wybierz opcję "Podstawowa", a następnie kliknij przycisk "Wybierz"
    3. Wybierz polecenie "Utwórz", aby utworzyć Bot aplikacji sieci Web.
    4. Poczekaj, aż zasób Bot aplikacji sieci Web ma zostać utworzony.

3. W witrynie Azure portal Edytuj zasób Bot aplikacji sieci Web, który został utworzony.

   1. Kliknij pozycję "Ustawienia aplikacji" element nawigacji po lewej stronie
   1. Przewiń w dół do sekcji "Ustawienia aplikacji"
   2. Dodaj następujące ustawienia:

       Zmienna środowiskowa | value
       --- | --- 
       CONVERSATION_LEARNER_SERVICE_URI | „https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/”
       CONVERSATION_LEARNER_MODEL_ID      | Identyfikator GUID identyfikatora aplikacji, uzyskany z poziomu interfejsu użytkownika uczeń konwersacji w obszarze "ustawienia" dla modelu >
       LUIS_AUTHORING_KEY               | Tworzenie klucza dla tego modelu usługi LUIS
       LUIS_SUBSCRIPTION_KEY            | Nie jest wymagane, ale zalecane do opublikowanych Boty, które należy unikać tworzenia limitu przydziału.
    
   4. Kliknij przycisk "Zapisz" u góry strony
   5. Otwórz element nav "Kompilacja" po lewej stronie
   6. Kliknij pozycję "Konfiguruj ciągłe wdrażanie" 
   7. Kliknij ikonę "Setup" w ramach wdrożenia
   8. Kliknij pozycję "Required ustawienia"
   9. Wybierz źródło, gdzie Twój kod bot jest dostępny i skonfigurować źródło.
