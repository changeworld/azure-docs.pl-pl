---
title: Jak utworzyć model Conversation Learner przy użyciu środowiska Node. js-Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Dowiedz się, jak utworzyć model Conversation Learner przy użyciu środowiska Node. js.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 7ab32fb421a2c0db72652d1bbf12d312bffd5d1e
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706534"
---
# <a name="create-a-conversation-learner-model-using-nodejs"></a>Tworzenie modelu Conversation Learner przy użyciu środowiska Node. js

Conversation Learner zmniejsza złożoność kompilowania botów. Umożliwia to przepływ pracy tworzenia hybrydowego, dzięki czemu można napisać kod i uczenie maszynowe, aby zmniejszyć ilość kodu wymaganego do zapisu botów. Niektóre stałe części modelu, takie jak sprawdzanie, czy użytkownik jest zalogowany, lub żądanie interfejsu API w celu sprawdzenia magazynu magazynu, może być nadal kodowane. Jednak inne zmiany stanu i akcji można poznać z przykładowych okien dialogowych określonych przez eksperta lub dewelopera domeny.

## <a name="invitation-required"></a>Zaproszenie jest wymagane

*Do uzyskania dostępu do Conversation Learner projektu wymagane jest zaproszenie.*

Conversation Learner projektu składa się z zestawu SDK dodawanego do bot oraz usługi w chmurze, do której zestaw SDK uzyskuje dostęp do uczenia maszynowego.  W tej chwili dostęp do usługi w chmurze do konwersacji w projekcie jest wymagany przez zaproszenie.  Jeśli nie masz już zaproszenia, [Poproś o zaproszenie](https://aka.ms/conversation-learner-request-invite).  Jeśli użytkownik nie otrzymał zaproszenia, nie będzie mógł uzyskać dostępu do interfejsu API chmury.

## <a name="prerequisites"></a>Wymagania wstępne

- Node 8.5.0 lub wyższy i NPM 5.3.0 lub nowszy. Zainstaluj program [https://nodejs.org](https://nodejs.org)z.
  
- Klucz tworzenia LUIS:

  1. Zaloguj się [https://www.luis.ai](https://www.luis.ai)do.

  2. Kliknij nazwę w prawym górnym rogu, a następnie pozycję "Ustawienia"

  3. Klucz tworzenia jest pokazywany na wynikowej stronie

  (Twój klucz tworzenia LUIS służy 2 role.  Po pierwsze będzie on używany jako klucz tworzenia Conversation Learner.  Następnie Conversation Learner używa LUIS do wyodrębniania jednostek; klucz autorstwo LUIS służy do tworzenia modeli LUIS w Twoim imieniu)

- Przeglądarka Google Chrome w sieci Web. Zainstaluj program [https://www.google.com/chrome/index.html](https://www.google.com/chrome/index.html)z.

- narzędzia. Zainstaluj program [https://git-scm.com/downloads](https://git-scm.com/downloads)z.

- Programu vscode. Zainstaluj program [https://code.visualstudio.com/](https://code.visualstudio.com/)z. Uwaga Ta metoda jest zalecana, ale nie jest wymagana.

## <a name="quick-start"></a>Szybki start 

1. Zainstaluj i skompiluj:

    ```bash    
    git clone https://github.com/Microsoft/ConversationLearner-Samples cl-bot-01
    cd cl-bot-01
    npm install
    npm run build
    ```

    > [!NOTE]
    > W `npm install`programie można zignorować ten błąd w przypadku wystąpienia:`gyp ERR! stack Error: Can't find Python executable`

2. Konfiguruj:

   Utwórz plik o nazwie `.env` w katalogu. `cl-bot-01`  Zawartość pliku powinna być:

   ```
   NODE_ENV=development
   LUIS_AUTHORING_KEY=<your LUIS authoring key>
   ```

3. Rozpocznij bot:

    ```
    npm start
    ```

    Spowoduje to uruchomienie ogólnej pustej bot `cl-bot-01/src/app.ts`w.

3. Otwórz przeglądarkę, aby`http://localhost:3978`

Jesteś teraz używany Conversation Learner i można utworzyć i nauczyć model Conversation Learner.  

> [!NOTE]
> Po uruchomieniu Conversation Learner projektu jest dostępny z zaproszeniem.  Jeśli `http://localhost:3978/ui` zostanie wyświetlony błąd `403` http, oznacza to, że Twoje konto nie zostało zaproszone.  Zażądaj [zaproszenia](https://aka.ms/conversation-learner-request-invite).

## <a name="tutorials-demos-and-switching-between-bots"></a>Samouczki, pokazy i przełączanie między botów

W powyższych instrukcjach została uruchomiona ogólna pusta bot.  Aby uruchomić samouczek lub bot demonstracyjny:

1. Jeśli masz otwarty interfejs użytkownika sieci Web Conversation Learner, Wróć do listy modeli pod adresem `http://localhost:3978/ui/home`.
    
2. Jeśli jest uruchomiona inna bot (na `npm start` przykład `npm run demo-pizza`lub), Zatrzymaj ją.  Nie trzeba zatrzymać procesu interfejsu użytkownika lub zamknąć przeglądarki sieci Web.

3. Uruchom demonstrację bot z wiersza polecenia (krok 2 powyżej).  Demonstracje obejmują:

   ```bash
   npm run tutorial-general
   npm run tutorial-entity-detection
   npm run tutorial-session-callbacks
   npm run tutorial-api-calls
   npm run tutorial-hybrid
   npm run demo-password
   npm run demo-pizza
   npm run demo-storage
   ```

4. Jeśli jeszcze tego nie zrobiono, przełącz się do Conversation Learner internetowego interfejsu użytkownika w programie `http://localhost:3978/ui/home`Chrome, ładując polecenie. 

5. Kliknij pozycję "Importowanie samouczków" i wybierz model demonstracyjny w interfejsie użytkownika Conversation Learner, który odnosi się do rozpoczętej demonstracji.

Pliki źródłowe dla pokazów znajdują się w`cl-bot-01/src/demos`

## <a name="create-a-bot-which-includes-back-end-code"></a>Utwórz element bot, który zawiera kod zaplecza

1. Jeśli masz otwarty interfejs użytkownika sieci Web Conversation Learner, Wróć do listy modeli pod adresem `http://localhost:3978/ui/home`.
    
2. Jeśli bot jest uruchomiona (na przykład `npm run demo-pizza`), Zatrzymaj ją.  Nie trzeba zatrzymać procesu interfejsu użytkownika lub zamknąć przeglądarki sieci Web.

3. W razie potrzeby edytuj kod w `cl-bot-01/src/app.ts`.

4. Przebuduj i ponownie uruchom bot:

    ```bash    
    npm run build
    npm start
    ```

5. Jeśli jeszcze tego nie zrobiono, przełącz się do Conversation Learner internetowego interfejsu użytkownika w programie `http://localhost:3978/ui/home`Chrome, ładując polecenie. 

6. Utwórz nowy model Conversation Learner w interfejsie użytkownika i zacznij uczenie się.

7. Aby wprowadzić zmiany w `cl-bot-01/src/app.ts`kodzie, powtórz powyższe kroki, rozpoczynając od kroku 2.

## <a name="vscode"></a>VSCode

W programie programu vscode są uruchamiane konfiguracje dla każdej demonstracyjnej i dla "pustej bot" w `cl-bot-01/src/app.ts`.  `cl-bot-01` Otwórz folder w programie programu vscode.

## <a name="advanced-configuration"></a>Konfiguracja zaawansowana

Istnieje plik szablonu `.env.example` pokazujący zmienne środowiskowe, które można skonfigurować w celu skonfigurowania przykładów.

Można dostosować te porty, aby uniknąć konfliktów między innymi usługami uruchomionymi na komputerze przez dodanie `.env` pliku do katalogu głównego projektu:

```bash
cp .env.example .env
```

Ta funkcja korzysta z konfiguracji standardowej, która pozwala na uruchamianie bot lokalnie i rozpoczęcie korzystania z Conversation Learner.  (Później, aby wdrożyć Bot do środowiska bot, niektóre zmiany w tym pliku będą konieczne).

## <a name="support"></a>Pomoc techniczna

- Otaguj pytania dotyczące [Stack Overflow](https://stackoverflow.com) ze "poznawczem firmy Microsoft"
- Zażądaj funkcji na naszej [stronie głosowej użytkownika](https://aka.ms/conversation-learner-uservoice)
- Otwórz problem w naszym [repozytorium GitHub](https://github.com/Microsoft/ConversationLearner-Samples)

## <a name="contributing"></a>Udział

W tym projekcie przyjęto [Kodeks postępowania oprogramowania Open Source firmy Microsoft](https://opensource.microsoft.com/codeofconduct/). Aby uzyskać więcej informacji, zobacz [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) (Często zadawane pytania dotyczące kodeksu postępowania) lub wyślij wiadomość e-mail na adres [opencode@microsoft.com](mailto:opencode@microsoft.com) w przypadku jakichkolwiek dodatkowych pytań lub komentarzy.

## <a name="source-repositories"></a>Repozytoria źródłowe

- [conversationlearner — przykłady](https://github.com/Microsoft/ConversationLearner-Samples)
- [conversationlearner — zestaw SDK](https://github.com/Microsoft/ConversationLearner-SDK)
- [conversationlearner — modele](https://github.com/Microsoft/ConversationLearner-Models)
- [conversationlearner — interfejs użytkownika](https://github.com/Microsoft/ConversationLearner-UI)
- [conversationlearner — czat webchat](https://github.com/Microsoft/ConversationLearner-WebChat)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Hello World](./tutorials/01-hello-world.md)
