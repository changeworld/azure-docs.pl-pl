---
title: 'Samouczek: Moderowanie zawartości serwisu Facebook - Content Moderator'
titlesuffix: Azure Cognitive Services
description: W tym samouczku dowiesz się, jak użyć usługi Content Moderator opartej na uczeniu maszynowym do ułatwienia moderowania wpisów i komentarzy w serwisie Facebook.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 01/18/2019
ms.author: pafarley
ms.openlocfilehash: 662eca2a727f3112f169ab8d669bf18c81700275
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60699572"
---
# <a name="tutorial-moderate-facebook-posts-and-commands-with-azure-content-moderator"></a>Samouczek: Moderuj wpisów w usłudze Facebook i poleceń przy użyciu usługi Azure Content Moderator

W tym samouczku dowiesz się, jak używać usługi Azure Content Moderator ułatwiające średni wpisów i komentarze, na stronie usługi Facebook. Facebook wyśle zawartości opublikowane przez osoby odwiedzające do usługi Content Moderator. Następnie przepływów pracy usługi Content Moderator będzie publikować zawartość lub tworzyć przeglądy w ramach narzędzie do przeglądu, w zależności od zawartości wyniki i progów. Zobacz [Build 2017 demonstracyjny materiał wideo dotyczący](https://channel9.msdn.com/Events/Build/2017/T6033) dla praktyczny przykład tego scenariusza.

Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> * Tworzenie zespołu usługi Content Moderator.
> * Tworzenie usługi Azure Functions, która nasłuchuje zdarzeń HTTP z usługi Content Moderator i serwisu Facebook.
> * Link stroną w serwisie Facebook do pakietu Content Moderator, za pomocą aplikacji usługi Facebook.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Ten diagram przedstawia każdego składnika w tym scenariuszu:

![Diagram przedstawiający Content Moderator odbieranie informacji z usługi Facebook za pośrednictwem "FBListener" i wysyłanie informacji przez "CMListener"](images/tutorial-facebook-moderation.png)

## <a name="prerequisites"></a>Wymagania wstępne

- Klucz subskrypcji pakietu Content Moderator. Aby zasubskrybować usługę Content Moderator i uzyskać klucz, postępuj zgodnie z instrukcjami z tematu [Create a Cognitive Services account (Tworzenie konta usług Cognitive Services)](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).
- A [konta w serwisie Facebook](https://www.facebook.com/).

## <a name="create-a-review-team"></a>Tworzenie zespołu do przeprowadzania przeglądu

Zapoznaj się [spróbuj pakietu Content Moderator w sieci web](quick-start.md) szybkiego startu, aby uzyskać instrukcje, jak zarejestrować się w celu [Content Moderator analizować narzędzie](https://contentmoderator.cognitive.microsoft.com/) i tworzenie zespołu. Zwróć uwagę na wartość **Identyfikator zespołu** na stronie **Poświadczenia**.

## <a name="configure-image-moderation-workflow"></a>Konfigurowanie przepływu pracy Moderowanie obrazów

Zapoznaj się [Definiuj, testy i przepływy pracy Użyj](review-tool-user-guide/workflows.md) przewodnika, aby utworzyć obraz niestandardowy przepływ pracy. Zezwalaj na automatyczne sprawdzanie obrazów w serwisie Facebook i niektórych do narzędzia do przeglądu usługi Content Moderator. Zwróć uwagę na przepływ pracy **nazwa**.

## <a name="configure-text-moderation-workflow"></a>Konfigurowanie przepływu pracy Moderowanie tekstu

Ponownie, zapoznaj się [Definiuj, testy i przepływy pracy Użyj](review-tool-user-guide/workflows.md) przewodnik; teraz utworzyć przepływ pracy niestandardowego tekstu. Umożliwi to pakiet Content Moderator automatycznie sprawdzać zawartości tekstowej. Zwróć uwagę na przepływ pracy **nazwa**.

![Konfigurowanie przepływu pracy tekstu](images/text-workflow-configure.PNG)

Testowanie przy użyciu przepływu pracy **wykonania przepływu pracy** przycisku.

![Testowanie przepływu pracy tekstu](images/text-workflow-test.PNG)

## <a name="create-azure-functions"></a>Tworzenie usługi Azure Functions

Zaloguj się do [witryny Azure Portal](https://portal.azure.com/) i wykonaj następujące czynności:

1. Utwórz aplikację usługi Azure Functions, jak pokazano na stronie usługi [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal).
2. Przejdź do nowo utworzonej aplikacji funkcji.
3. W aplikacji, przejdź do **funkcje platformy** kartę, a następnie wybierz pozycję **ustawienia aplikacji**. W **ustawienia aplikacji** części następnej strony, przewiń w dół listy, a następnie kliknij przycisk **Dodaj nowe ustawienie**. Dodaj następujące pary klucz/wartość
    
    | Nazwa ustawienia aplikacji | value   | 
    | -------------------- |-------------|
    | cm:TeamId   | Identyfikator zespołu usługi Content Moderator  | 
    | cm:SubscriptionKey | Klucz subskrypcji usługi Content Moderator — zobacz [Poświadczenia](review-tool-user-guide/credentials.md) | 
    | cm:region | Nazwa regionu usługi Content Moderator, bez spacji. Zobacz wcześniejszą uwagę. |
    | cm:ImageWorkflow | Nazwa przepływu pracy do uruchomienia na obrazach |
    | cm:TextWorkflow | Nazwa przepływu pracy do uruchomienia na tekście |
    | cm:CallbackEndpoint | Adres URL dla aplikacji funkcji CMListener, którą utworzysz w dalszej części tego przewodnika |
    | fb:VerificationToken | Token wpisu tajnego, używany również do subskrybowania zdarzeń kanału informacyjnego serwisu Facebook |
    | fb:PageAccessToken | Token dostępu do interfejsu API Graph serwisu Facebook nie wygasa i umożliwia funkcji ukrywanie i usuwanie wpisów w Twoim imieniu. |

    Kliknij przycisk **Zapisz** znajdujący się u góry strony.

1. Za pomocą **+** przycisku w okienku po lewej stronie, aby wyświetlić nowe okienko funkcji.

    ![Okienko funkcje platformy Azure z wyróżnionym przyciskiem Dodaj funkcję.](images/new-function.png)

    Następnie kliknij przycisk **+ nowa funkcja** w górnej części strony. Ta funkcja odbiera zdarzenia z serwisu Facebook. Utwórz tę funkcję, wykonując następujące czynności:

    1. Kliknij Kafelek, który jest wyświetlany komunikat **wyzwalacza Http**.
    1. Wprowadź nazwę **FBListener**. W polu **Authorization Level** (Poziom autoryzacji) powinna być ustawiona wartość **Function** (Funkcja).
    1. Kliknij pozycję **Utwórz**.
    1. Zastąp zawartość **run.csx** na podstawie zawartości **FbListener/run.csx**

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/FbListener/run.csx?range=1-160)]

1. Utwórz nową **wyzwalacza Http** funkcji o nazwie **CMListener**. Ta funkcja odbiera zdarzenia z usługi Content Moderator. Zastąp zawartość **run.csx** na podstawie zawartości **CMListener/run.csx**

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/CmListener/run.csx?range=1-106)]

---

## <a name="configure-the-facebook-page-and-app"></a>Konfigurowanie aplikacji i strony w serwisie Facebook
1. Utwórz aplikację serwisu Facebook.

    ![Strona w serwisie Facebook dla deweloperów](images/facebook-developer-app.png)

    1. Przejdź do [witryny dewelopera serwisu Facebook](https://developers.facebook.com/)
    2. Kliknij pozycję **Moje aplikacje**.
    3. Dodaj nową aplikację.
    1. Nazwa elementu
    1. Wybierz **elementów Webhook -> zestaw w górę**
    1. Wybierz **strony** w menu rozwijane i wybierz pozycję **subskrybować ten obiekt**
    1. Podaj **adres URL funkcji FBListener** jako adres URL wywołania zwrotnego i **zweryfikuj token** skonfigurowany w obszarze **ustawień aplikacji funkcji**
    1. Po zasubskrybowaniu przewiń w dół do kanału informacyjnego, a następnie wybierz pozycję **subskrybuj**.

2. Utwórz stronę serwisu Facebook.

    1. Przejdź do serwisu [Facebook](https://www.facebook.com/bookmarks/pages) i utwórz **nową stronę w serwisie Facebook**.
    2. Zezwól aplikacji serwisu Facebook na dostęp do tej strony, wykonując następujące czynności:
        1. Przejdź do [eksploratora interfejsu API Graph](https://developers.facebook.com/tools/explorer/).
        2. Wybierz pozycję **Aplikacja**.
        3. Wybierz pozycję **Token dostępu do strony** i wyślij żądanie **Get**.
        4. Kliknij pozycję **identyfikatora strony** w odpowiedzi.
        5. Teraz dołącz ciąg **/subscribed_apps** w adresie URL i wyślij żądanie **Get** (pusta odpowiedź).
        6. Prześlij żądanie **Post**. Uzyskasz odpowiedź w formie **success: true**.

3. Utwórz niewygasający token dostępu do interfejsu API Graph.

    1. Przejdź do [eksploratora interfejsu API Graph](https://developers.facebook.com/tools/explorer/).
    2. Wybierz opcję **Aplikacja**.
    3. Wybierz opcję **Uzyskaj token dostępu użytkownika**.
    4. W obszarze **Wybierz uprawnienia** wybierz opcję **manage_pages** i **publish_pages**.
    5. Użyjemy **tokenu dostępu** (token krótkotrwały) w następnym kroku.

4. W kilku następnych krokach będziemy używać narzędzia Postman.

    1. Otwórz narzędzie **Postman** (lub pobierz je [tutaj](https://www.getpostman.com/)).
    2. Zaimportuj te dwa pliki:
        1. [Postman Collection](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/Facebook%20Permanant%20Page%20Access%20Token.postman_collection.json)
        2. [Postman Environment](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FB%20Page%20Access%20Token%20Environment.postman_environment.json)       
    3. Zaktualizuj te zmienne środowiskowe:
    
        | Klucz | Wartość   | 
        | -------------------- |-------------|
        | appId   | Tutaj wstaw identyfikator aplikacji serwisu Facebook  | 
        | appSecret | Tutaj wstaw wpis tajny aplikacji serwisu Facebook | 
        | short_lived_token | Wstaw krótkotrwały token dostępu użytkownika, który został wygenerowany w poprzednim kroku |
    4. Teraz uruchom 3 interfejsy API wymienione w kolekcji: 
        1. Wybierz pozycję **Generate Long-Lived Access Token** (Wygeneruj długotrwały token dostępu) i kliknij przycisk **Send** (Wyślij).
        2. Wybierz pozycję **Get User ID** (Uzyskaj identyfikator użytkownika) i kliknij przycisk **Send** (Wyślij).
        3. Wybierz pozycję **Get Permanent Page Access Token** (Uzyskaj trwały token dostępu do strony) i kliknij przycisk **Send** (Wyślij).
    5. Skopiuj wartość **access_token** z odpowiedzi i przypisz ją do ustawienia aplikacji **fb:PageAccessToken**.

Rozwiązanie wysyła wszystkie obrazy i tekst opublikowane na stronie serwisu Facebook do usługi Content Moderator. Przepływy pracy, które wcześniej skonfigurowano są wywoływane. Zawartość, które nie zostały spełnione kryteria zdefiniowane w przepływach pracy przekazywane do recenzji w ramach narzędzie do przeglądu. Pozostała część zawartości zostanie opublikowany automatycznie.

## <a name="next-steps"></a>Kolejne kroki

Podczas pracy z tym samouczkiem skonfigurowano program do analizowania obrazów produktów w celu oznaczenia ich tagami określającymi typ produktu i ułatwienia zespołowi przeprowadzającemu przegląd podejmowanie świadomych decyzji dotyczących moderacji treści. Teraz możesz dowiedzieć się więcej o szczegółach moderowania obrazów.

> [!div class="nextstepaction"]
> [Moderowanie obrazów](./image-moderation-api.md)
