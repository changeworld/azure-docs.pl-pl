---
title: Jak wdrożyć Conversation Learner bot-Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Dowiedz się, jak wdrożyć Conversation Learner bot.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 05fd83506aac26df33f18bec83dcadac8dee2d90
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705287"
---
# <a name="how-to-deploy-a-conversation-learner-bot"></a>Jak wdrożyć Conversation Learner bot

W tym dokumencie wyjaśniono, jak wdrożyć Conversation Learner bot — lokalnie lub na platformie Azure.

## <a name="prerequisite-determine-the-model-id"></a>Wymaganie wstępne: Określanie identyfikatora modelu 

Aby uruchomić bot poza interfejsem użytkownika Conversation Learner, należy ustawić identyfikator Conversation Learner modelu, który będzie używany przez bot, czyli Identyfikator modelu uczenia maszynowego w chmurze Conversation Learner.  (Z drugiej strony, gdy uruchamiasz bot za pomocą interfejsu użytkownika Conversation Learner, interfejs użytkownika wybiera Identyfikator modelu).  

Oto jak uzyskać identyfikator modelu:

1. Uruchom bot i interfejs użytkownika Conversation Learner.  Aby uzyskać pełne instrukcje, zobacz Przewodnik Szybki Start. Aby podsumować:

    W jednym oknie polecenia:

    ```
    [open a command window]
    cd my-bot
    npm start
    ```

    W oknie polecenia innym

    ```bash
    [open second command prompt window]
    cd cl-bot-01
    npm run ui
    ```

2. Otwórz przeglądarkę, aby`http://localhost:5050` 

3. Kliknij model Conversation Learner, dla którego chcesz uzyskać identyfikator

4. Kliknij pozycję "Ustawienia" na pasku nawigacyjnym po lewej stronie.

5. Identyfikator GUID "model ID" jest wyświetlany w górnej części strony.

## <a name="option-1-deploying-a-conversation-learner-bot-to-run-locally"></a>Option 1: Wdrażanie Conversation Learner Bot do lokalnego uruchamiania

Powoduje to wdrożenie Bot na komputerze lokalnym i pokazuje, w jaki sposób można uzyskać do niego dostęp przy użyciu emulatora programu bot Framework.

### <a name="configure-your-bot-for-access-outside-the-conversation-learner-ui"></a>Konfigurowanie Bot na potrzeby dostępu poza interfejsem użytkownika Conversation Learner

Podczas lokalnego uruchamiania bot należy dodać identyfikator aplikacji do `.env` pliku bot:

    ```
    CONVERSATION_LEARNER_MODEL_ID=<YOUR_MODEL_ID>
    ```

Następnie zacznij od bot:

    ```
    [open a command window]
    cd my-bot
    npm start
    ```

Bot jest teraz uruchomiona lokalnie.  Możesz uzyskać do niego dostęp za pomocą emulatora platformy bot Framework.

### <a name="download-and-install-the-emulator"></a>Pobieranie i Instalowanie emulatora

    ```
    git clone https://github.com/Microsoft/BotFramework-Emulator
    npm install
    npm run build
    npm start
    ```

### <a name="connect-the-emulator-to-your-bot"></a>Łączenie emulatora z bot

1. W lewym górnym rogu emulatora w polu "wprowadź adres URL punktu końcowego" `http://127.0.0.1:3978/api/messages`wprowadź.  Pozostaw pozostałe pola puste, a następnie kliknij przycisk "Połącz".

2. Teraz można przystąpić do bot.

## <a name="option-2-deploy-to-azure"></a>Opcja 2: Wdrażanie na platformie Azure

Opublikuj Conversation Learner bot podobne do tego tak samo jak w przypadku publikowania innych bot. Na wysokim poziomie można przekazać kod do hostowanej witryny sieci Web, ustawić odpowiednie wartości konfiguracji, a następnie zarejestrować bot z użyciem różnych kanałów. Szczegółowe instrukcje znajdują się w tym filmie wideo z informacjami o sposobach publikowania bot przy użyciu Azure Bot Service.

Po wdrożeniu bot i uruchomieniu można podłączyć do niego różne kanały, takie jak Facebook, zespoły, Skype itp. Korzystanie z rejestracji kanału usługi Azure bot. Aby zapoznać się z dokumentacją dotyczącą tego procesu, zobacz: https://docs.microsoft.com/bot-framework/bot-service-quickstart-registration

Poniżej przedstawiono instrukcje krok po kroku dotyczące wdrażania Conversation Learner Bot na platformie Azure.  W tych instrukcjach przyjęto założenie, że źródło bot jest dostępne z poziomu źródła opartego na chmurze, takiego jak Azure DevOps Services, GitHub, BitBucket lub OneDrive, i skonfiguruje Bot na potrzeby ciągłego wdrażania.

1. Zaloguj się do Azure Portal w https://portal.azure.com

2. Utwórz nowy zasób "bot aplikacji sieci Web" 

    1. Nadaj nazwę bot
    2. Kliknij pozycję "bot template", wybierz "Node. js", wybierz "Basic", a następnie kliknij przycisk "Select" (Wybierz).
    3. Kliknij pozycję "Utwórz", aby utworzyć aplikację sieci Web bot.
    4. Poczekaj na utworzenie zasobu bot aplikacji sieci Web.

3. W Azure Portal Edytuj właśnie utworzony zasób aplikacji sieci Web bot.

   1. Kliknij element nawigacyjny ustawienia aplikacji po lewej stronie
   1. Przewiń w dół do sekcji "Ustawienia aplikacji"
   2. Dodaj następujące ustawienia:

       Zmienna środowiskowa | value
       --- | --- 
       CONVERSATION_LEARNER_SERVICE_URI | "https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/"
       CONVERSATION_LEARNER_MODEL_ID      | Identyfikator GUID identyfikatora aplikacji uzyskany z interfejsu użytkownika Conversation Learner w obszarze "Ustawienia" dla modelu >
       LUIS_AUTHORING_KEY               | LUIS tworzenia klucza dla tego modelu
       LUIS_SUBSCRIPTION_KEY            | Niewymagane, ale zalecane dla opublikowanych botów, aby uniknąć użycia przydziału tworzenia.
    
   4. Kliknij pozycję "Zapisz" u góry strony
   5. Otwórz element nawigacji "build" po lewej stronie
   6. Kliknij pozycję "Skonfiguruj ciągłe wdrażanie" 
   7. Kliknij ikonę "instalacja" w obszarze wdrożenia
   8. Kliknij pozycję "wymagane ustawienia".
   9. Wybierz źródło, w którym jest dostępny kod bot, a następnie skonfiguruj źródło.
