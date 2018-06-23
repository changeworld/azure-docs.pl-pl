---
title: Jak utworzyć aplikację uczeń konwersacji przy użyciu środowiska Node.js — kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak utworzyć aplikację uczeń konwersacji przy użyciu środowiska Node.js.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: a3a51aa86a30b060c8dc4113da69462904d7df54
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349341"
---
# <a name="create-a-conversation-learner-application-using-nodejs"></a>Utwórz aplikację uczeń konwersacji przy użyciu środowiska Node.js

Uczeń konwersacji zmniejsza się złożoność tworzenia robotów. Umożliwia on hybrydowego programowanie — przepływ pracy umożliwiających odręcznego kodu oraz zmniejszyć liczbę kod wymagany do zapisania robotów uczenia maszynowego. Można nadal kodowane niektórych stałej części aplikacji, takich jak sprawdzanie, czy użytkownik jest zalogowany, lub wysłał żądanie interfejsu API, aby sprawdzić magazynu sklepu. Jednak inne zmiany w przypadku wyboru akcji i stanu może być rozpoznawane z okien dialogowych przykład przez specjalistę domeny lub dewelopera.

## <a name="invitation-required"></a>Zaproszenie wymagane

*Zaproszenie jest wymagane do uzyskania dostępu uczeń konwersacji projektu.*

Projekt konwersacji uczeń składa się z zestawu SDK, Dodaj do Twojej bot i usługi w chmurze, który zestawu SDK, który uzyskuje dostęp do usługi machine learning.  Obecnie dostęp do usługi w chmurze Leaner konwersacji projektu wymaga zaproszenia.  Jeśli użytkownik nie zostali zaproszeni już, [żądania zaproszenie](https://aka.ms/conversation-learner-request-invite).  Jeśli nie otrzymano zaproszenie, można nie można uzyskać dostępu do chmury interfejsu API.

## <a name="prerequisites"></a>Wymagania wstępne

- Węzeł 8.5.0 lub nowszej i npm 5.3.0 lub nowszej. Zainstaluj z [ https://nodejs.org ](https://nodejs.org).
  
- LUIS tworzenia klucza:

  1. Zaloguj się do [ http://www.luis.ai ](http://www.luis.ai).

  2. Kliknij nazwę użytkownika w prawym górnym rogu, a następnie na "ustawienia"

  3. Tworzenie klucza jest wyświetlany na stronie wynikowy

  (2 role służy Twojej LUIS tworzenia klucza.  Najpierw będzie służyć jako Twoje uczeń konwersacji tworzenia klucza.  Po drugie uczeń konwersacji używa LUIS wyodrębniania jednostki; LUIS tworzenia klucz służy do tworzenia modeli LUIS w Twoim imieniu)

- Google Chrome przeglądarki sieci web. Zainstaluj z [ https://www.google.com/chrome/index.html ](https://www.google.com/chrome/index.html).

- git. Zainstaluj z [ https://git-scm.com/downloads ](https://git-scm.com/downloads).

- VSCode. Zainstaluj z [ https://code.visualstudio.com/ ](https://code.visualstudio.com/). Należy pamiętać, że jest to zalecane, nie jest wymagane.

## <a name="quick-start"></a>Szybki start 

1. Instalowanie i kompilacji:

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
   LUIS_AUTHORING_KEY=<your LUIS authoring key>
   ```

3. Uruchom bot:

    ```
    npm start
    ```

    Ta funkcja jest uruchamiana ogólnego bot pusta `cl-bot-01/src/app.ts`.

3. Uruchom uczeń konwersacji interfejsu użytkownika:

    ```bash
    [open second command prompt window]
    cd cl-bot-01
    npm run ui
    ```

4. Otwórz przeglądarkę, aby http://localhost:5050 

Obecnie używasz uczeń konwersacji i można tworzyć i uczenie modelu uczeń konwersacji.  

> [!NOTE]
> Po uruchomieniu uczeń konwersacji projektu jest dostępna w zaproszeniu.  Jeśli http://localhost:5050 pokazuje HTTP `403` błąd, oznacza to, Twoje konto nie zostali zaproszeni.  Sprawdź [żądania zaproszenie](https://aka.ms/conversation-learner-request-invite).

## <a name="tutorials-demos-and-switching-between-bots"></a>Samouczki, pokazy i przełączanie między robotów

Powyższych instrukcji uruchomiona ogólnego bot puste.  Aby uruchomić samouczek lub pokaz bot zamiast tego:

1. Jeśli masz otworzyć Interfejs użytkownika sieci web uczeń konwersacji powrócić do listy aplikacji na http://localhost:5050/home.
    
2. Jeśli jest uruchomiony inny bot (takich jak `npm start` lub `npm run demo-pizza`), zatrzymaj ją.  Nie trzeba zatrzymać procesu interfejsu użytkownika, lub zamknij przeglądarkę sieci web.

3. Uruchom bot demonstracyjnej z poziomu wiersza polecenia (krok 2 powyżej).  Pokazy obejmują:

  ```bash
  npm run tutorial-general
  npm run tutorial-entity-detection
  npm run tutorial-session-callbacks
  npm run tutorial-api-calls
  npm run demo-password
  npm run demo-pizza
  npm run demo-storage
  npm run demo-vrapp
  ```

4. Jeśli nie masz już, przełącz się do konwersacji uczeń interfejsu użytkownika sieci web w przeglądarce Chrome ładując http://localhost:5050/home. 

5. Kliknij pozycję "Samouczki importu" (tylko należy jednak wykonać jeden raz).  To zajmie około minutę i skopiuje modeli uczeń konwersacji dla wszystkich samouczków na koncie uczeń konwersacji.

6. Polecenie modelu pokaz w interfejsie użytkownika uczeń konwersacji, umożliwiająca demonstracyjnej, który został uruchomiony.

Pliki źródłowe pokazy znajdują się w `cl-bot-01/src/demos`

## <a name="create-a-bot-which-includes-back-end-code"></a>Utwórz bot, który zawiera kod zaplecza

1. Jeśli masz otworzyć Interfejs użytkownika sieci web uczeń konwersacji powrócić do listy aplikacji na http://localhost:5050/home.
    
2. Jeśli działa robotów (takie jak `npm run demo-pizza`), zatrzymaj ją.  Nie trzeba zatrzymać procesu interfejsu użytkownika, lub zamknij przeglądarkę sieci web.

3. W razie potrzeby można edytować kodu w `cl-bot-01/src/app.ts`.

4. Ponowne skompilowanie i ponowne uruchomienie robot:

    ```bash    
    npm run build
    npm start
    ```

5. Jeśli nie masz już, przełącz się do konwersacji uczeń interfejsu użytkownika sieci web w przeglądarce Chrome ładując http://localhost:5050/home. 

6. Utwórz nową aplikację uczeń konwersacji w interfejsie użytkownika, a następnie uruchom nauczania.

7. Aby zmienić kod w `cl-bot-01/src/app.ts`, powtórz powyższe kroki, zaczynając od kroku 2.

## <a name="vscode"></a>VSCode

W VSCode, są uruchamiane konfiguracje dla poszczególnych pokaz i "bot pusty" w `cl-bot-01/src/app.ts`.  Otwórz `cl-bot-01` folderu w VSCode.

## <a name="advanced-configuration"></a>Konfiguracja zaawansowana

Brak szablonu `.env.example` plik zawiera środowiska zmiennych mogą ustawiać skonfigurować próbek.

Można dostosować te porty, aby uniknąć konfliktów między innymi usługami działającymi na tym komputerze przez dodanie `.env` plik do katalogu głównego projektu:

```bash
cp .env.example .env
```

Ta metoda korzysta konfiguracji standardowej, która pozwala na uruchamianie z bot lokalnie i rozpocząć korzystanie z uczeń konwersacji.  (Później na, aby wdrożyć z bot Bot Framework, niektóre zmiany w tym pliku będą potrzebne.)

## <a name="support"></a>Pomoc techniczna

- Oznacz pytania [przepełnienie stosu](https://stackoverflow.com) z "microsoft kognitywnych"
- Żądania funkcji na naszych [strony głos użytkownika](https://aka.ms/conversation-learner-uservoice)
- Otwórz problemu na naszych [repozytorium github](https://github.com/Microsoft/ConversationLearner-Samples)

## <a name="contributing"></a>Współtworzenie

W tym projekcie przyjęto [Kodeks postępowania oprogramowania Open Source firmy Microsoft](https://opensource.microsoft.com/codeofconduct/). Aby uzyskać więcej informacji, zobacz [kodu z postępowania — często zadawane pytania](https://opensource.microsoft.com/codeofconduct/faq/) lub skontaktuj się z [ opencode@microsoft.com ](mailto:opencode@microsoft.com) z jakichkolwiek dodatkowych pytań lub komentarzy.

## <a name="source-repositories"></a>Repozytoriów źródłowych

- [Przykłady conversationlearner](https://github.com/Microsoft/ConversationLearner-Samples)
- [zestaw sdk conversationlearner](https://github.com/Microsoft/ConversationLearner-SDK)
- [modele conversationlearner](https://github.com/Microsoft/ConversationLearner-Models)
- [conversationlearner interfejsu użytkownika](https://github.com/Microsoft/ConversationLearner-UI)
- [conversationlearner webchat](https://github.com/Microsoft/ConversationLearner-WebChat)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Cześć ludzie](./tutorials/1-hello-world.md)
