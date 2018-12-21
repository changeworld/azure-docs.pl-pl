---
title: 'Samouczek: moderowanie zawartości serwisu Facebook — Azure Content Moderator'
titlesuffix: Azure Cognitive Services
description: Moderowanie stron serwisu Facebook za pomocą usługi Content Moderator.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: tutorial
ms.date: 09/18/2017
ms.author: sajagtap
ms.openlocfilehash: 048f2f40714fd7675c073337825d75443e58039b
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2018
ms.locfileid: "53255435"
---
# <a name="tutorial-facebook-content-moderation-with-content-moderator"></a>Samouczek: moderowanie zawartości serwisu Facebook przy użyciu usługi Azure Content Moderator

W tym samouczku dowiesz się, jak używać usługi Content Moderator opartej na uczeniu maszynowym, aby ułatwić moderowanie wpisów i komentarzy w serwisie Facebook.

Ten samouczek zawiera instrukcje wykonania następujących kroków:

1. Tworzenie zespołu usługi Content Moderator.
2. Tworzenie usługi Azure Functions, która nasłuchuje zdarzeń HTTP z usługi Content Moderator i serwisu Facebook.
3. Tworzenie aplikacji i strony w serwisie Facebook, a następnie łączenie ich z usługą Content Moderator.

Gdy skończymy, serwis Facebook będzie wysyłać zawartość opublikowaną przez osoby odwiedzające do usługi Content Moderator. Na podstawie progów dopasowania przepływy pracy usługi Content Moderator będą publikować zawartość lub tworzyć przeglądy w ramach narzędzia do przeglądu. 

Na poniższej ilustracji przedstawiono bloki konstrukcyjne rozwiązania.

![Moderowanie postów w serwisie Facebook](images/tutorial-facebook-moderation.png)

## <a name="create-a-content-moderator-team"></a>Tworzenie zespołu usługi Content Moderator

Zapoznaj się z informacjami na stronie [Szybki start](quick-start.md), zarejestruj się w usłudze Content Moderator i utwórz zespół.

## <a name="configure-image-moderation-workflow-threshold"></a>Konfigurowanie przepływu pracy moderowania obrazów (próg)

Zapoznaj się ze stroną [Przepływy pracy](review-tool-user-guide/workflows.md), aby skonfigurować niestandardowy przepływ pracy obrazów (próg). Zapamiętaj **nazwę** tego przepływu pracy.

## <a name="3-configure-text-moderation-workflow-threshold"></a>3. Konfigurowanie przepływu pracy moderowania tekstu (próg)

Wykonaj kroki podobne do podanych na stronie [Przepływy pracy](review-tool-user-guide/workflows.md), aby skonfigurować niestandardowy próg tekstu i przepływ pracy. Zapamiętaj **nazwę** tego przepływu pracy.

![Konfigurowanie przepływu pracy tekstu](images/text-workflow-configure.PNG)

Przetestuj przepływ pracy przy użyciu przycisku „Wykonaj przepływ pracy”.

![Testowanie przepływu pracy tekstu](images/text-workflow-test.PNG)

## <a name="create-azure-functions"></a>Tworzenie usługi Azure Functions

Zaloguj się do [portalu zarządzania platformy Azure](https://portal.azure.com/), aby utworzyć usługę Azure Functions. Wykonaj następujące kroki:

1. Utwórz aplikację usługi Azure Functions, jak pokazano na stronie usługi [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal).
2. Otwórz nowo utworzoną aplikację funkcji.
3. W aplikacji przejdź do ekranu **Funkcje platformy -> Ustawienia aplikacji**
4. Określ następujące [ustawienia aplikacji](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings#settings):

> [!NOTE]
> **cm: Region**: nazwa regionu (bez spacji).
> Na przykład **westeurope** (europazachodnia), a nie Europa Zachodnia, **westcentralus** (zachodniośrodkowestanyusa), a nie Zachodnio-środkowe stany USA i tak dalej.
>

| Ustawienia aplikacji | Opis   | 
| -------------------- |-------------|
| cm:TeamId   | Identyfikator zespołu usługi Content Moderator  | 
| cm:SubscriptionKey | Klucz subskrypcji usługi Content Moderator — zobacz [Poświadczenia](review-tool-user-guide/credentials.md) | 
| cm:region | Nazwa regionu usługi Content Moderator, bez spacji. Zobacz wcześniejszą uwagę. |
| cm:ImageWorkflow | Nazwa przepływu pracy do uruchomienia na obrazach |
| cm:TextWorkflow | Nazwa przepływu pracy do uruchomienia na tekście |
| cm:CallbackEndpoint | Adres URL dla aplikacji funkcji CMListener, którą utworzysz w dalszej części tego przewodnika |
| fb:VerificationToken | Token wpisu tajnego, używany również do subskrybowania zdarzeń kanału informacyjnego serwisu Facebook |
| fb:PageAccessToken | Token dostępu do interfejsu API Graph serwisu Facebook nie wygasa i umożliwia funkcji ukrywanie i usuwanie wpisów w Twoim imieniu. |

5. Utwórz nową funkcję **HttpTrigger-CSharp** o nazwie **FBListener**. Ta funkcja odbiera zdarzenia z serwisu Facebook. Utwórz tę funkcję, wykonując następujące czynności:

    1. Zostaw stronę [tworzenia usługi Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal) otwartą, aby móc się do niej odwoływać.
    2. Kliknij przycisk **+** dodawania, aby utworzyć nową funkcję.
    3. Zamiast wbudowanych szablonów, wybierz opcję **Get started on your own/custom function** (Rozpocznij pracę samodzielnie/funkcja niestandardowa).
    4. Kliknij kafelek z etykietą **HttpTrigger-CSharp**.
    5. Wprowadź nazwę **FBListener**. W polu **Authorization Level** (Poziom autoryzacji) powinna być ustawiona wartość **Function** (Funkcja).
    6. Kliknij pozycję **Utwórz**.
    7. Zastąp zawartość pliku **run.csx** za pomocą zawartości pliku [**FbListener/run.csx**](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FbListener/run.csx).

6. Utwórz nową funkcję **HttpTrigger-CSharp** o nazwie **CMListener**. Ta funkcja odbiera zdarzenia z usługi Content Moderator. Wykonaj następujące kroki, aby utworzyć tę funkcję.

    1. Zostaw stronę [tworzenia usługi Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal) otwartą, aby móc się do niej odwoływać.
    2. Kliknij przycisk **+** dodawania, aby utworzyć nową funkcję.
    3. Zamiast wbudowanych szablonów, wybierz opcję **Get started on your own/custom function** (Rozpocznij pracę samodzielnie/funkcja niestandardowa).
    4. Kliknij kafelek z etykietą **HttpTrigger-CSharp**
    5. Wprowadź nazwę **CMListener**. W polu **Authorization Level** (Poziom autoryzacji) powinna być ustawiona wartość **Function** (Funkcja).
    6. Kliknij pozycję **Utwórz**.
    7. Zastąp zawartość pliku **run.csx** za pomocą zawartości pliku [**CMListener/run.csx**](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/CmListener/run.csx).

## <a name="configure-the-facebook-page-and-app"></a>Konfigurowanie aplikacji i strony w serwisie Facebook
1. Utwórz aplikację serwisu Facebook.

    1. Przejdź do [witryny dewelopera serwisu Facebook](https://developers.facebook.com/)
    2. Kliknij pozycję **Moje aplikacje**.
    3. Dodaj nową aplikację.
    4. Wybierz pozycję **Elementy webhook -> Rozpocznij**
    5. Wybierz pozycję **Strona -> Zasubskrybuj ten temat**
    6. Podaj **adres URL funkcji FBListener** jako adres URL wywołania zwrotnego i **zweryfikuj token** skonfigurowany w obszarze **ustawień aplikacji funkcji**
    7. Po zasubskrybowaniu przewiń w dół do kanału informacyjnego, a następnie wybierz pozycję **subskrybuj**.

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

Gotowe.

Rozwiązanie wysyła wszystkie obrazy i tekst opublikowane na stronie serwisu Facebook do usługi Content Moderator. Przepływy pracy, które skonfigurowano wcześniej, są wywoływane. Zawartość, która nie spełnia kryteriów zdefiniowanych w przepływach pracy, jest przeglądana w narzędziu do przeglądu. Pozostała zawartość jest publikowana.

## <a name="license"></a>Licencja

Wszystkie zestawy SDK usługi Microsoft Cognitive Services i przykłady podlegają licencji MIT. Aby uzyskać więcej szczegółów, zobacz [LICENSE](https://microsoft.mit-license.org/) (LICENCJA).

## <a name="developer-code-of-conduct"></a>Kodeks postępowania dewelopera

Deweloperzy korzystający z usługi Cognitive Services, łącznie z tą biblioteką kliencką i przykładami powinni postępować zgodnie z dokumentem „Developer Code of Conduct for Microsoft Cognitive Services” („Kodeks postępowania dla deweloperów usługi Microsoft Cognitive Services”) znajdującym się pod adresem http://go.microsoft.com/fwlink/?LinkId=698895.

## <a name="next-steps"></a>Następne kroki

1. [Obejrzyj pokaz (wideo)](https://channel9.msdn.com/Events/Build/2017/T6033) tego rozwiązania z wydarzenia Microsoft Build 2017.
1. [Przykład serwisu Facebook w witrynie GitHub](https://github.com/MicrosoftContentModerator/samples-fbPageModeration)
1. https://docs.microsoft.com/azure/azure-functions/functions-create-github-webhook-triggered-function
2. http://ukimiawz.github.io/facebook/2015/08/12/webhook-facebook-subscriptions/
3. http://stackoverflow.com/questions/17197970/facebook-permanent-page-access-token
