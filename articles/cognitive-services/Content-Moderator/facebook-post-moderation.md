---
title: Facebook łagodzenia zawartości z moderatora zawartości platformy Azure | Dokumentacja firmy Microsoft
description: Umiarkowany stron witryny Facebook z uczenia maszynowego na podstawie zawartości moderatora
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 09/18/2017
ms.author: sajagtap
ms.openlocfilehash: 316c7212c30e9fe1151cdf5ceabf875439ad4c65
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347196"
---
# <a name="facebook-content-moderation-with-content-moderator"></a>Facebook łagodzenia zawartości z zawartości moderatora

W tym samouczku firma Microsoft informacje o sposobie używania machine learning na podstawie zawartości moderatora ułatwia umiarkowane wpisów Facebook i komentarze.

Samouczek prowadzi użytkownika przez następujące kroki:

1. Tworzenie zespołu moderatora zawartości.
2. Tworzenie usługi Azure Functions, który nasłuchiwania zdarzeń HTTP z moderatora zawartości i Facebook.
3. Utwórz stronę w witrynie Facebook i aplikacji i podłącz go do moderatora zawartości.

Po wykonaniu czynności możemy Facebook będzie wysyłać zawartości opublikowane przez osoby odwiedzające moderatorowi zawartości. W oparciu o progi dopasowania, przepływy pracy moderatora zawartości publikowania zawartości albo utworzyć przeglądy w narzędziu przeglądu. 

Na poniższej ilustracji przedstawiono bloków konstrukcyjnych rozwiązania.

![Moderowanie postów w serwisie Facebook](images/tutorial-facebook-moderation.png)

## <a name="create-a-content-moderator-team"></a>Tworzenie zespołu moderatora zawartości

Zapoznaj się [szybkiego startu](quick-start.md) stronę, aby utworzyć konto moderatora zawartości i tworzenie zespołu.

## <a name="configure-image-moderation-workflow-threshold"></a>Konfiguruj przepływ pracy łagodzenia obrazu (próg)

Zapoznaj się [przepływy pracy](review-tool-user-guide/workflows.md) strony, aby skonfigurować niestandardowy obraz przepływu pracy (próg). Należy pamiętać, przepływ pracy **nazwa**.

## <a name="3-configure-text-moderation-workflow-threshold"></a>3. Konfiguruj przepływ pracy łagodzenia tekst (próg)

Wykonaj kroki podobne do [przepływy pracy](review-tool-user-guide/workflows.md) strony, aby skonfigurować niestandardowy tekst próg i przepływ pracy. Należy pamiętać, przepływ pracy **nazwa**.

![Konfiguruj przepływ pracy tekstu](images/text-workflow-configure.PNG)

Test przepływu pracy przy użyciu przycisku "Wykonania przepływu pracy".

![Przepływ testowej pracy w tekstu](images/text-workflow-test.PNG)

## <a name="create-azure-functions"></a>Tworzenie funkcji platformy Azure

Zaloguj się do [portalu zarządzania Azure](https://portal.azure.com/) można utworzyć funkcji platformy Azure. Wykonaj następujące kroki:

1. Tworzenie aplikacji funkcji platformy Azure, jak pokazano na [usługi Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal) strony.
2. Otwórz nowo utworzonej aplikacji funkcji.
3. W aplikacji, przejdź do **funkcji -> Ustawienia aplikacji**
4. Określ następujące opcje [ustawienia aplikacji](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings#settings):

> [!NOTE]
> **Cm: Region** powinna być nazwą obszaru (bez spacji).
> Na przykład **westeurope**, nie Europa Zachodnia, **westcentralus**, nie zachodnie centralnej nam i tak dalej.
>

| Ustawienia aplikacji | Opis   | 
| -------------------- |-------------|
| cm:TeamId   | Twoje identyfikator zawartości moderatora zespołu  | 
| cm:SubscriptionKey | Klucz subskrypcji moderatora zawartości — zobacz [poświadczeń](/review-tool-user-guide/credentials.md) | 
| cm:region | Z zawartości moderatora nazwa regionu, bez spacji. Zobacz poprzedni uwagi. |
| cm:ImageWorkflow | Nazwa przepływu pracy do uruchomienia na obrazach |
| cm:TextWorkflow | Nazwa przepływu pracy do uruchomienia w tekście |
| cm:CallbackEndpoint | Adres URL dla aplikacji funkcja CMListener utworzonego w dalszej części tego przewodnika |
| FB:VerificationToken | Tajny tokenu, również używane do subskrybowania serwisu Facebook źródła zdarzeń |
| FB:PageAccessToken | Token dostępu interfejsu api graph usługi Facebook nie wygasa i umożliwia funkcja Ukryj/usuwania wpisów w Twoim imieniu. |

5. Utwórz nową **HttpTrigger CSharp** funkcji o nazwie **FBListener**. Ta funkcja odbiera zdarzenia z usługi Facebook. Utwórz tę funkcję, wykonaj następujące czynności:

    1. Zachowaj [Tworzenie funkcji Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal) otwarte dla odwołania do strony.
    2. Kliknij przycisk **+** Dodaj, aby utworzyć nową funkcję.
    3. Zamiast wbudowanych szablonów, wybierz **przed rozpoczęciem pracy funkcji własne niestandardowe** opcji.
    4. Kliknij Kafelek z informacją, **HttpTrigger CSharp**.
    5. Wprowadź nazwę **FBListener**. **Poziom autoryzacji** pola powinna być równa **funkcja**.
    6. Kliknij przycisk **Utwórz**.
    7. Zastąp zawartość **run.csx** na podstawie zawartości [ **FbListener/run.csx**](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FbListener/run.csx).

6. Utwórz nową **HttpTrigger CSharp** funkcji o nazwie **CMListener**. Ta funkcja odbiera zdarzenia z usługi Facebook. Wykonaj następujące kroki, aby utworzyć tę funkcję.

    1. Zachowaj [Tworzenie funkcji Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal) otwarte dla odwołania do strony.
    2. Kliknij przycisk **+** Dodaj, aby utworzyć nową funkcję.
    3. Zamiast wbudowanych szablonów, wybierz **przed rozpoczęciem pracy funkcji własne niestandardowe** opcji.
    4. Kliknij Kafelek z informacją, **HttpTrigger CSharp**
    5. Wprowadź nazwę **CMListener**. **Poziom autoryzacji** pola powinna być równa **funkcja**.
    6. Kliknij przycisk **Utwórz**.
    7. Zastąp zawartość **run.csx** na podstawie zawartości [ **CMListener/run.csx**](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/CmListener/run.csx).

## <a name="configure-the-facebook-page-and-app"></a>Konfigurowanie strony Facebook i aplikacji
1. Tworzenie aplikacji usługi Facebook.

    1. Przejdź do [witryny dla deweloperów usługi Facebook](https://developers.facebook.com/)
    2. Polecenie **Moje aplikacje**.
    3. Dodaj nową aplikację.
    4. Wybierz **Get -> elementów Webhook uruchomiona**
    5. Wybierz **strony -> Subskrybuj do tego tematu**
    6. Podaj **FBListener Url** jako adres URL wywołania zwrotnego i **Zweryfikuj Token** można skonfigurować w obszarze **ustawienia aplikacji — funkcja**
    7. Po subskrypcji, przewiń w dół do źródła danych, a następnie wybierz **subskrypcji**.

2. Utwórz stronę usługi Facebook.

    1. Przejdź do [Facebook](https://www.facebook.com/bookmarks/pages) i Utwórz **Nowa strona Facebook**.
    2. Zezwalaj aplikacji usługi Facebook, dostępu do tej strony wykonaj następujące czynności:
        1. Przejdź do [Explorer interfejsu API programu Graph](https://developers.facebook.com/tools/explorer/).
        2. Wybierz **aplikacji**.
        3. Wybierz **Token dostępu strony**, Wyślij **uzyskać** żądania.
        4. Kliknij przycisk **identyfikator strony** w odpowiedzi.
        5. Dołącz teraz **/subscribed_apps** do adresu URL i wysyłania **uzyskać** żądania (pustą odpowiedź).
        6. Przedstawia **Post** żądania. Uzyskaj odpowiedzi jako **Powodzenie: true**.

3. Utwórz token dostępu do interfejsu API programu Graph nie wygasa.

    1. Przejdź do [Explorer interfejsu API programu Graph](https://developers.facebook.com/tools/explorer/).
    2. Wybierz **aplikacji** opcji.
    3. Wybierz **uzyskać tokenu dostępu użytkownika** opcji.
    4. W obszarze **wybierz uprawnienia**, wybierz pozycję **manage_pages** i **publish_pages** opcje.
    5. Używamy **token dostępu** (krótka żyła Token) w następnym kroku.

4. Używamy Postman dla następnych kilku krokach.

    1. Otwórz **Postman** (lub pobrać go [tutaj](https://www.getpostman.com/)).
    2. Zaimportuj następujące dwa pliki:
        1. [Kolekcja postman](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/Facebook%20Permanant%20Page%20Access%20Token.postman_collection.json)
        2. [Środowisko postman](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FB%20Page%20Access%20Token%20Environment.postman_environment.json)       
    3. Zaktualizuj zmienne środowiskowe:
    
    | Klucz | Wartość   | 
    | -------------------- |-------------|
    | appId   | Wstaw identyfikator aplikacji Facebook  | 
    | appSecret | Wstaw tutaj klucz tajny aplikacji Facebook | 
    | short_lived_token | Wstaw tokenu dostępu użytkownika krótko wygenerowany w poprzednim kroku |
    4. Teraz uruchom 3 interfejsów API na liście w kolekcji: 
        1. Wybierz **wygenerowania tokenu dostępu Long-Lived** i kliknij przycisk **wysyłania**.
        2. Wybierz **Uzyskaj identyfikator użytkownika** i kliknij przycisk **wysyłania**.
        3. Wybierz **uzyskać Token stałego strony na dostęp** i kliknij przycisk **wysyłania**.
    5. Kopiuj **' access_token '** wartości z odpowiedzi i przypisz je do ustawienia aplikacji **fb:PageAccessToken**.

Gotowe.

Rozwiązanie wysyła wszystkie obrazy i tekst, zamieszczony na stronę w witrynie Facebook moderatorowi zawartości. Przepływy pracy, które wcześniej skonfigurowano są wywoływane. Zawartość, które nie zostały spełnione kryteria zdefiniowane w wynikach przepływów pracy w recenzji, w narzędziu przeglądu. Pozostała część zawartość została opublikowana.

## <a name="license"></a>Licencja

Wszystkie zestawy SDK usługi kognitywnych firmy Microsoft i przykłady, jest udzielana z licencją MIT. Aby uzyskać więcej informacji, zobacz [licencji](https://microsoft.mit-license.org/).

## <a name="developer-code-of-conduct"></a>Kodeks postępowania dewelopera

Przy użyciu usług kognitywnych, łącznie z tej biblioteki klienta & próbki, deweloperzy mają wykonaj "Developer kodu z należy przeprowadzić dla Microsoft kognitywnych usługi", podczas gdy znaleziono w http://go.microsoft.com/fwlink/?LinkId=698895.

## <a name="next-steps"></a>Kolejne kroki

1. [Obejrzyj pokaz (klip wideo)](https://channel9.msdn.com/Events/Build/2017/T6033) tego rozwiązania z 2017 kompilacji firmy Microsoft.
1. [Przykładem Facebook w witrynie Github](https://github.com/MicrosoftContentModerator/samples-fbPageModeration)
1. https://docs.microsoft.com/azure/azure-functions/functions-create-github-webhook-triggered-function
2. http://ukimiawz.github.io/facebook/2015/08/12/webhook-facebook-subscriptions/
3. http://stackoverflow.com/questions/17197970/facebook-permanent-page-access-token
