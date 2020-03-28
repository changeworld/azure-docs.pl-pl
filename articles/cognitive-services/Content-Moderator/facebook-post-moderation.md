---
title: 'Samouczek: Moderowanie treści na Facebooku - Moderator treści'
titleSuffix: Azure Cognitive Services
description: W tym samouczku dowiesz się, jak użyć usługi Content Moderator opartej na uczeniu maszynowym do ułatwienia moderowania wpisów i komentarzy w serwisie Facebook.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 9f25c845302d62e3bc9e230b4a6f8f2669f4ac35
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774268"
---
# <a name="tutorial-moderate-facebook-posts-and-commands-with-azure-content-moderator"></a>Samouczek: Moderowanie postów i poleceń na Facebooku za pomocą moderatora zawartości platformy Azure

W tym samouczku dowiesz się, jak używać programu Azure Content Moderator, aby moderować posty i komentarze na stronie na Facebooku. Facebook wyśle treści zamieszczone przez odwiedzających do usługi Content Moderator. Następnie przepływy pracy moderatora zawartości będą publikować zawartość lub tworzyć recenzje w narzędziu Recenzja, w zależności od wyników i progów zawartości. Zobacz [klip demonstracyjny kompilacji 2017,](https://channel9.msdn.com/Events/Build/2017/T6033) aby zapoznać się z roboczym przykładem tego scenariusza.

Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> * Tworzenie zespołu usługi Content Moderator.
> * Tworzenie usługi Azure Functions, która nasłuchuje zdarzeń HTTP z usługi Content Moderator i serwisu Facebook.
> * Połącz stronę na Facebooku z moderatorem treści za pomocą aplikacji na Facebooku.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

Ten diagram ilustruje każdy składnik tego scenariusza:

![Diagram Moderatora treści otrzymywania informacji z Facebooka za pośrednictwem "FBListener" i wysyłania informacji za pośrednictwem "CMListener"](images/tutorial-facebook-moderation.png)

> [!IMPORTANT]
> W 2018 r. Facebook wdrożył bardziej rygorystyczne zasady weryfikacji aplikacji Facebooka. Nie będzie można wykonać kroki tego samouczka, jeśli aplikacja nie została sprawdzona i zatwierdzona przez zespół recenzentów Facebooka.

## <a name="prerequisites"></a>Wymagania wstępne

- Klucz subskrypcji pakietu Content Moderator. Aby zasubskrybować usługę Content Moderator i uzyskać klucz, postępuj zgodnie z instrukcjami z tematu [Create a Cognitive Services account (Tworzenie konta usług Cognitive Services)](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).
- [Konto na Facebooku](https://www.facebook.com/).

## <a name="create-a-review-team"></a>Tworzenie zespołu do przeprowadzania przeglądu

Zapoznaj się z przewodnikiem Szybki start [try content w sieci Web,](quick-start.md) aby uzyskać instrukcje dotyczące rejestrowania się w narzędziu do przeglądania [moderatora zawartości](https://contentmoderator.cognitive.microsoft.com/) i tworzenia zespołu recenzentów. Zwróć uwagę na wartość **Identyfikator zespołu** na stronie **Poświadczenia**.

## <a name="configure-image-moderation-workflow"></a>Konfigurowanie przepływu pracy moderowania obrazu

Zapoznaj się z przewodnikiem [Definiowanie, testowanie i używanie przepływów pracy w](review-tool-user-guide/workflows.md) celu utworzenia niestandardowego przepływu pracy obrazu. Moderator treści użyje tego przepływu pracy do automatycznego sprawdzania obrazów na Facebooku i wysyłania ich do narzędzia Recenzja. Zanotuj **nazwę**przepływu pracy .

## <a name="configure-text-moderation-workflow"></a>Konfigurowanie przepływu pracy moderowania tekstu

Ponownie zapoznaj się z przewodnikiem [Definiowanie, testowanie i używanie przepływów pracy;](review-tool-user-guide/workflows.md) tym razem utwórz niestandardowy przepływ pracy tekstu. Content Moderator użyje tego przepływu pracy do automatycznego sprawdzania zawartości tekstowej. Zanotuj **nazwę**przepływu pracy .

![Konfigurowanie przepływu pracy tekstu](images/text-workflow-configure.PNG)

Przetestuj przepływ pracy za pomocą przycisku **Wykonaj przepływ pracy.**

![Testowanie przepływu pracy tekstu](images/text-workflow-test.PNG)

## <a name="create-azure-functions"></a>Tworzenie usługi Azure Functions

Zaloguj się do [witryny Azure portal](https://portal.azure.com/) i wykonaj następujące kroki:

1. Utwórz aplikację usługi Azure Functions, jak pokazano na stronie usługi [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal).
1. Przejdź do nowo utworzonej aplikacji funkcji.
1. W aplikacji przejdź do karty **Funkcje platformy** i wybierz pozycję **Konfiguracja**. W sekcji **Ustawienia aplikacji** na następnej stronie wybierz pozycję Nowe **ustawienie aplikacji,** aby dodać następujące pary kluczy/wartości:
    
    | Nazwa ustawienia aplikacji | value   | 
    | -------------------- |-------------|
    | cm:TeamId   | Identyfikator zespołu usługi Content Moderator  | 
    | cm:SubscriptionKey | Klucz subskrypcji usługi Content Moderator — zobacz [Poświadczenia](review-tool-user-guide/credentials.md) |
    | cm:region | Nazwa regionu usługi Content Moderator, bez spacji. Można to znaleźć w polu **Lokalizacja** na karcie **Przegląd** zasobu platformy Azure.|
    | cm:ImageWorkflow | Nazwa przepływu pracy do uruchomienia na obrazach |
    | cm:TextWorkflow | Nazwa przepływu pracy do uruchomienia na tekście |
    | cm:CallbackEndpoint | Adres URL aplikacji cmlistener funkcji, które zostaną utworzone w dalszej części tego przewodnika |
    | fb:VerificationToken | Tajny token, który tworzysz, używany do subskrybowania wydarzeń w aktualnościach na Facebooku |
    | fb:PageAccessToken | Token dostępu do interfejsu API Graph serwisu Facebook nie wygasa i umożliwia funkcji ukrywanie i usuwanie wpisów w Twoim imieniu. Otrzymasz ten token w późniejszym kroku. |

    Kliknij przycisk **Zapisz** u góry strony.

1. Wróć do karty **Funkcje platformy.** **+** **New function** Funkcja, którą zamierzasz utworzyć, będzie otrzymywać wydarzenia z Facebooka.

    ![Okienko Usługi platformy Azure z wyróżnionym przyciskiem Dodaj funkcję.](images/new-function.png)

    1. Kliknij na kafelku z **napisem Http trigger**.
    1. Wprowadź nazwę **FBListener**. W polu **Authorization Level** (Poziom autoryzacji) powinna być ustawiona wartość **Function** (Funkcja).
    1. Kliknij przycisk **Utwórz**.
    1. Zastąp zawartość **pliku run.csx** zawartością **fblistener/run.csx**

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/FbListener/run.csx?range=1-154)]

1. Utwórz nową funkcję **wyzwalacza Http** o nazwie **CMListener**. Ta funkcja odbiera zdarzenia z usługi Content Moderator. Zastąp zawartość **pliku run.csx** zawartością z **pliku CMListener/run.csx**

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/CmListener/run.csx?range=1-110)]

---

## <a name="configure-the-facebook-page-and-app"></a>Konfigurowanie aplikacji i strony w serwisie Facebook

1. Utwórz aplikację serwisu Facebook.

    ![strona programisty na Facebooku](images/facebook-developer-app.png)

    1. Przejdź do [witryny dewelopera serwisu Facebook](https://developers.facebook.com/)
    1. Kliknij pozycję **Moje aplikacje**.
    1. Dodaj nową aplikację.
    1. nazwij to coś
    1. Wybieranie **elementu Webhooks -> Konfigurowanie**
    1. Wybierz **pozycję Strona** w menu rozwijanym i wybierz pozycję **Subskrybuj ten obiekt**
    1. Podaj **adres URL funkcji FBListener** jako adres URL wywołania zwrotnego i **zweryfikuj token** skonfigurowany w obszarze **ustawień aplikacji funkcji**
    1. Po zasubskrybowaniu przewiń w dół do kanału informacyjnego, a następnie wybierz pozycję **subskrybuj**.
    1. Kliknij przycisk **Testuj** wiersz **kanału informacyjnego,** aby wysłać wiadomość testową do funkcji platformy Azure FBListener, a następnie naciśnij przycisk Wyślij do **mojego serwera.** Powinieneś zobaczyć żądanie odebrane na fblistener.

1. Utwórz stronę serwisu Facebook.

    > [!IMPORTANT]
    > W 2018 roku Facebook wdrożył bardziej rygorystyczne sprawdzanie aplikacji Facebooka. Nie będzie można wykonać sekcji 2, 3 i 4, jeśli twoja aplikacja nie została sprawdzona i zatwierdzona przez zespół recenzentów Facebooka.

    1. Przejdź do serwisu [Facebook](https://www.facebook.com/bookmarks/pages) i utwórz **nową stronę w serwisie Facebook**.
    1. Zezwól aplikacji serwisu Facebook na dostęp do tej strony, wykonując następujące czynności:
        1. Przejdź do [eksploratora interfejsu API Graph](https://developers.facebook.com/tools/explorer/).
        1. Wybierz **opcję Aplikacja**.
        1. Wybierz pozycję **Token dostępu do strony** i wyślij żądanie **Get**.
        1. Kliknij pozycję **identyfikatora strony** w odpowiedzi.
        1. Teraz dołącz ciąg **/subscribed_apps** w adresie URL i wyślij żądanie **Get** (pusta odpowiedź).
        1. Prześlij żądanie **Post**. Uzyskasz odpowiedź w formie **success: true**.

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

Rozwiązanie wysyła wszystkie obrazy i tekst opublikowane na stronie serwisu Facebook do usługi Content Moderator. Następnie są wywoływane przepływy pracy skonfigurowane wcześniej. Zawartość, która nie spełnia kryteriów zdefiniowanych w przepływach pracy, jest przekazywana do recenzji w narzędziu do recenzji. Pozostała część zawartości zostanie opublikowana automatycznie.

## <a name="next-steps"></a>Następne kroki

Podczas pracy z tym samouczkiem skonfigurowano program do analizowania obrazów produktów w celu oznaczenia ich tagami określającymi typ produktu i ułatwienia zespołowi przeprowadzającemu przegląd podejmowanie świadomych decyzji dotyczących moderacji treści. Teraz możesz dowiedzieć się więcej o szczegółach moderowania obrazów.

> [!div class="nextstepaction"]
> [Moderowanie obrazu](./image-moderation-api.md)
