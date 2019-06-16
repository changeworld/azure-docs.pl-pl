---
title: Jak utworzyć model uczeń konwersacji za pomocą środowiska Node.js — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak utworzyć model uczeń konwersacji za pomocą środowiska Node.js.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: cc071d59a387c8ae4982eacbce6812526f447788
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66388762"
---
# <a name="create-a-conversation-learner-model-using-nodejs"></a>Tworzenie modelu uczeń konwersacji za pomocą środowiska Node.js

Uczeń konwersacji zmniejsza złożoność tworzenie botów. Dzięki temu hybrydowego tworzenia przepływu pracy dzięki niemu agencje odręcznej kodu i uczenia maszynowego, aby zmniejszyć ilość kodu wymaganą do zapisania botów. Niektórych stałymi model, takich jak sprawdzanie, jeśli użytkownik jest zalogowany lub wysłał żądanie interfejsu API, aby sprawdzić magazynu sklepu nadal mogą być kodowane. Jednak inne zmiany w przypadku wyboru akcji i stanu można przedstawiono z okien dialogowych przykład podane przez eksperta domeny lub dewelopera.

## <a name="invitation-required"></a>Zaproszenia wymagane

*Zaproszenie jest wymagane do dostępu do projektu uczeń konwersacji.*

Uczeń konwersacji projekt składa się z zestawu SDK, Dodaj do bota i usługi w chmurze, które zestaw SDK, który uzyskuje dostęp do uczenia maszynowego.  W chwili obecnej dostęp do usługi w chmurze Odchudzony konwersacji projektu wymaga zaproszenia.  Jeśli użytkownik jeszcze nie został zaproszony już [poproś o zaproszenie](https://aka.ms/conversation-learner-request-invite).  Jeśli nie otrzymał zaproszenie, będziesz nie może uzyskać dostępu do interfejsu API w chmurze.

## <a name="prerequisites"></a>Wymagania wstępne

- Węzeł 8.5.0 lub nowszej i narzędzie npm 5.3.0 lub nowszej. Zainstaluj z [ https://nodejs.org ](https://nodejs.org).
  
- Usługa LUIS tworzenia klucza:

  1. Zaloguj się do [ https://www.luis.ai ](https://www.luis.ai).

  2. Kliknij swoją nazwę w prawym górnym rogu, a następnie na "ustawienia"

  3. Tworzenie klucza jest wyświetlany na stronie wynikowy

  (2 role służy usługi LUIS tworzenia klucza.  Po pierwsze będzie służyć jako swojej uczeń konwersacji tworzenia klucza.  Po drugie uczeń konwersacji używa usługi LUIS do działania funkcji wydobywania podmiotów; Usługa LUIS tworzenia klucza jest używany do tworzenia modeli usługi LUIS w Twoim imieniu)

- Przeglądarki Google Chrome. Zainstaluj z [ https://www.google.com/chrome/index.html ](https://www.google.com/chrome/index.html).

- git. Zainstaluj z [ https://git-scm.com/downloads ](https://git-scm.com/downloads).

- VSCode. Zainstaluj z [ https://code.visualstudio.com/ ](https://code.visualstudio.com/). Należy pamiętać, że jest to zalecane, nie jest wymagane.

## <a name="quick-start"></a>Szybki start 

1. Instalowanie i tworzenie:

    ```bash    
    git clone https://github.com/Microsoft/ConversationLearner-Samples cl-bot-01
    cd cl-bot-01
    npm install
    npm run build
    ```

    > [!NOTE]
    > Podczas `npm install`, możesz zignorować ten błąd, jeśli występuje: `gyp ERR! stack Error: Can't find Python executable`

2. Skonfiguruj:

   Utwórz plik o nazwie `.env` w katalogu `cl-bot-01`.  Zawartość pliku powinna być:

   ```
   NODE_ENV=development
   LUIS_AUTHORING_KEY=<your LUIS authoring key>
   ```

3. Rozpocznij bot:

    ```
    npm start
    ```

    Spowoduje to uruchomienie ogólnego bot pusty `cl-bot-01/src/app.ts`.

3. Otwórz przeglądarkę, aby `http://localhost:3978`

Obecnie używasz uczeń konwersacji i można tworzyć i uczenie modelu uczeń konwersacji.  

> [!NOTE]
> Po uruchomieniu projektu uczeń konwersacji jest dostępna na zaproszenie.  Jeśli `http://localhost:3978/ui` pokazuje HTTP `403` błąd, to oznacza, że Twoje konto nie otrzymało zaproszenia.  Proszę [poproś o zaproszenie](https://aka.ms/conversation-learner-request-invite).

## <a name="tutorials-demos-and-switching-between-bots"></a>W samouczkach, pokazy i przełączania się między botów

Zgodnie z instrukcjami pracę ogólnego bot puste.  Uruchom samouczek lub pokaz bot zamiast tego:

1. Jeśli masz uczeń konwersacji w sieci web, Otwórz interfejs użytkownika, wróć do listy modeli w `http://localhost:3978/ui/home`.
    
2. Jeśli jest uruchomiony inny bot (takich jak `npm start` lub `npm run demo-pizza`), zatrzymaj ją.  Nie musisz zatrzymać proces interfejsu użytkownika, lub zamknij przeglądarkę sieci web.

3. Bot pokaz należy uruchomić z wiersza polecenia (krok 2 powyżej).  Prezentacje obejmują:

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

4. Jeśli nie wiesz jeszcze, przełącz się do uczeń konwersacji interfejsu użytkownika sieci web w przeglądarce Chrome, ładując `http://localhost:3978/ui/home`. 

5. Kliknij pozycję "Importuj samouczki" i wybierz model pokaz w interfejsie użytkownika uczeń konwersacji, odpowiadającą wersję demonstracyjną, którego rozpoczęto.

Pokazy pliki źródłowe znajdują się w `cl-bot-01/src/demos`

## <a name="create-a-bot-which-includes-back-end-code"></a>Tworzenie botów, która zawiera kod zaplecza

1. Jeśli masz uczeń konwersacji w sieci web, Otwórz interfejs użytkownika, wróć do listy modeli w `http://localhost:3978/ui/home`.
    
2. Jeśli działa robota (takich jak `npm run demo-pizza`), zatrzymaj ją.  Nie musisz zatrzymać proces interfejsu użytkownika, lub zamknij przeglądarkę sieci web.

3. Jeśli to konieczne, Edytuj kod w `cl-bot-01/src/app.ts`.

4. Ponownie skompiluj i uruchom ponownie bot:

    ```bash    
    npm run build
    npm start
    ```

5. Jeśli nie wiesz jeszcze, przełącz się do uczeń konwersacji interfejsu użytkownika sieci web w przeglądarce Chrome, ładując `http://localhost:3978/ui/home`. 

6. Utwórz nowy model uczeń konwersacji w interfejsie użytkownika, a następnie uruchom nauczania.

7. Aby wprowadzić zmiany kodu na `cl-bot-01/src/app.ts`, powtórz powyższe kroki, zaczynając od kroku 2.

## <a name="vscode"></a>VSCode

W VSCode, są uruchamiane konfiguracje dla poszczególnych wersji demonstracyjnej i "pusty bot" w ramach `cl-bot-01/src/app.ts`.  Otwórz `cl-bot-01` folderu w VSCode.

## <a name="advanced-configuration"></a>Konfiguracja zaawansowana

Brak szablonu `.env.example` plik pokazuje, jakie środowisko zmienne możesz ustawić skonfigurowanie przykładów.

Można dostosować te porty, aby uniknąć konfliktów między innych usług uruchomionych na komputerze, dodając `.env` pliku w folderze głównym projektu:

```bash
cp .env.example .env
```

Ta metoda korzysta standardowej konfiguracji, która pozwala na uruchamianie bota lokalnie i rozpocząć korzystanie z uczeń konwersacji.  (Później do wdrożenia bota platformy Bot Framework, niektóre zmiany do tego pliku będą potrzebne.)

## <a name="support"></a>Pomoc techniczna

- Oznacz pytania [Stack Overflow](https://stackoverflow.com) za pomocą "microsoft cognitive"
- Prośba o funkcję na naszych [stronę z opiniami użytkowników](https://aka.ms/conversation-learner-uservoice)
- Otwórz problem w naszym [repozytorium GitHub](https://github.com/Microsoft/ConversationLearner-Samples)

## <a name="contributing"></a>Współtworzenie

W tym projekcie przyjęto [Kodeks postępowania oprogramowania Open Source firmy Microsoft](https://opensource.microsoft.com/codeofconduct/). Aby uzyskać więcej informacji, zobacz [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) (Często zadawane pytania dotyczące kodeksu postępowania) lub wyślij wiadomość e-mail na adres [opencode@microsoft.com](mailto:opencode@microsoft.com) w przypadku jakichkolwiek dodatkowych pytań lub komentarzy.

## <a name="source-repositories"></a>Repozytoriów źródłowych

- [Przykłady conversationlearner](https://github.com/Microsoft/ConversationLearner-Samples)
- [conversationlearner-sdk](https://github.com/Microsoft/ConversationLearner-SDK)
- [modele conversationlearner](https://github.com/Microsoft/ConversationLearner-Models)
- [Interfejs użytkownika conversationlearner](https://github.com/Microsoft/ConversationLearner-UI)
- [conversationlearner-webchat](https://github.com/Microsoft/ConversationLearner-WebChat)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Cześć ludzie](./tutorials/01-hello-world.md)
