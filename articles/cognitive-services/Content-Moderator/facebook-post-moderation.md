---
title: Moderowanie zawartości usługi Facebook przy użyciu usługi Azure Content Moderator | Dokumentacja firmy Microsoft
description: Umiarkowany stron witryny Facebook przy użyciu uczenia maszynowego na podstawie pakietu Content Moderator
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 09/18/2017
ms.author: sajagtap
ms.openlocfilehash: 4570b514bef33c8dfa6d220ee4cd88ad068ba4d0
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2018
ms.locfileid: "41987531"
---
# <a name="facebook-content-moderation-with-content-moderator"></a>Moderowanie zawartości serwisu Facebook z Content Moderator

W tym samouczku będziemy Dowiedz się, jak użycie machine learning na podstawie pakietu Content Moderator ułatwia umiarkowane wpisów w usłudze Facebook i komentarze.

Samouczek przeprowadzi Cię przez następujące kroki:

1. Tworzenie zespołu pakietu Content Moderator.
2. Tworzenie usługi Azure Functions, która nasłuchiwać zdarzeń HTTP z pakietu Content Moderator i Facebook.
3. Tworzenie stroną w serwisie Facebook i aplikacji, a następnie podłącz go do pakietu Content Moderator.

Po wykonaniu czynności firma Microsoft Facebook wyśle zawartości opublikowane przez osoby odwiedzające do Content Moderator. Oparte na progach dopasowanie, przepływów pracy usługi Content Moderator publikowania zawartości albo utworzyć przeglądy w ramach narzędzie do przeglądu. 

Na poniższej ilustracji przedstawiono bloki konstrukcyjne rozwiązania.

![Moderowanie postów w serwisie Facebook](images/tutorial-facebook-moderation.png)

## <a name="create-a-content-moderator-team"></a>Tworzenie zespołu pakietu Content Moderator

Zapoznaj się [Szybki Start](quick-start.md) strony, aby utworzyć konto usługi Content Moderator i tworzenia zespołu.

## <a name="configure-image-moderation-workflow-threshold"></a>Konfigurowanie przepływu pracy moderowania obrazów (próg)

Zapoznaj się [przepływy pracy](review-tool-user-guide/workflows.md) strony, aby skonfigurować niestandardowy obraz przepływu pracy (próg). Należy pamiętać, przepływ pracy **nazwa**.

## <a name="3-configure-text-moderation-workflow-threshold"></a>3. Konfigurowanie przepływu pracy Moderowanie tekstu (próg)

Wykonaj kroki podobne do [przepływy pracy](review-tool-user-guide/workflows.md) strony, aby skonfigurować próg niestandardowego tekstu i przepływ pracy. Należy pamiętać, przepływ pracy **nazwa**.

![Skonfiguruj tekst przepływu pracy](images/text-workflow-configure.PNG)

Testowanie aplikacji logiki przy użyciu przycisku "Wykonania przepływu pracy".

![Przepływ testowej pracy w tekst](images/text-workflow-test.PNG)

## <a name="create-azure-functions"></a>Tworzenie funkcji platformy Azure

Zaloguj się do [portalu zarządzania systemu Azure](https://portal.azure.com/) do utworzenia usługi Azure Functions. Wykonaj następujące kroki:

1. Utwórz aplikację funkcji platformy Azure, jak pokazano na [usługi Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal) strony.
2. Otwórz nowo utworzoną aplikację funkcji.
3. W aplikacji, przejdź do **funkcje platformy -> Ustawienia aplikacji**
4. Określ następujące opcje [ustawienia aplikacji](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings#settings):

> [!NOTE]
> **Cm: Region** powinna być nazwą region (bez spacji).
> Na przykład **westeurope**, nie Europa Zachodnia, **westcentralus**, nie zachodnio-środkowe stany USA i tak dalej.
>

| Ustawienia aplikacji | Opis   | 
| -------------------- |-------------|
| cm:TeamId   | Identyfikator zespołu usługi Content Moderator  | 
| cm:SubscriptionKey | Klucz subskrypcji pakietu Content Moderator — zobacz [poświadczeń](/review-tool-user-guide/credentials.md) | 
| cm:region | Usługi Content Moderator nazwa regionu, bez spacji. Zobacz uwagę poprzedni. |
| cm:ImageWorkflow | Nazwa przepływu pracy do uruchamiania na obrazach |
| cm:TextWorkflow | Nazwa przepływu pracy do uruchamiania na tekst |
| cm:CallbackEndpoint | Adres URL dla aplikacji funkcji CMListener, które utworzysz w dalszej części tego przewodnika |
| FB:VerificationToken | Token wpisu tajnego, również używana do subskrybowania serwisu Facebook źródła danych zdarzeń |
| FB:PageAccessToken | Token dostępu interfejsu api graph serwisu Facebook nie wygasa i umożliwia korzystanie z funkcji Ukryj/usuwać wpisy w Twoim imieniu. |

5. Utwórz nową **HttpTrigger-CSharp** funkcji o nazwie **FBListener**. Funkcja ta odbiera zdarzenia z usługi Facebook. Utwórz tę funkcję, wykonując następujące czynności:

    1. Zachowaj [tworzenia funkcji platformy Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal) strony otwarte dla odwołania.
    2. Kliknij przycisk **+** Dodaj, aby utworzyć nową funkcję.
    3. Zamiast wbudowanych szablonów, wybierz **wprowadzenie do funkcji własne niestandardowe** opcji.
    4. Kliknij Kafelek, który jest wyświetlany komunikat **HttpTrigger-CSharp**.
    5. Wprowadź nazwę **FBListener**. **Poziom autoryzacji** pola powinna być równa **funkcja**.
    6. Kliknij pozycję **Utwórz**.
    7. Zastąp zawartość **run.csx** na podstawie zawartości [ **FbListener/run.csx**](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FbListener/run.csx).

6. Utwórz nową **HttpTrigger-CSharp** funkcji o nazwie **CMListener**. Funkcja ta odbiera zdarzenia z usługi Content Moderator. Wykonaj następujące kroki, aby utworzyć tę funkcję.

    1. Zachowaj [tworzenia funkcji platformy Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal) strony otwarte dla odwołania.
    2. Kliknij przycisk **+** Dodaj, aby utworzyć nową funkcję.
    3. Zamiast wbudowanych szablonów, wybierz **wprowadzenie do funkcji własne niestandardowe** opcji.
    4. Kliknij Kafelek, który jest wyświetlany komunikat **HttpTrigger-CSharp**
    5. Wprowadź nazwę **CMListener**. **Poziom autoryzacji** pola powinna być równa **funkcja**.
    6. Kliknij pozycję **Utwórz**.
    7. Zastąp zawartość **run.csx** na podstawie zawartości [ **CMListener/run.csx**](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/CmListener/run.csx).

## <a name="configure-the-facebook-page-and-app"></a>Konfigurowanie stroną w serwisie Facebook i aplikacji
1. Tworzenie aplikacji usługi Facebook.

    1. Przejdź do [witryny dewelopera usługi Facebook](https://developers.facebook.com/)
    2. Kliknij pozycję **Moje aplikacje**.
    3. Dodaj nową aplikację.
    4. Wybierz **elementów Webhook -> Get pracę**
    5. Wybierz **strony -> subskrypcji w tym temacie**
    6. Podaj **FBListener Url** jako adres URL wywołania zwrotnego i **Zweryfikuj Token** skonfigurowanego w obszarze **ustawień aplikacji funkcji**
    7. Po subskrypcji, przewiń w dół do źródła danych, a następnie wybierz pozycję **subskrybowania**.

2. Utwórz stronę usługi Facebook.

    1. Przejdź do [Facebook](https://www.facebook.com/bookmarks/pages) i utworzyć **nową stronę w serwisie Facebook**.
    2. Zezwalaj aplikacji usługi Facebook, dostępu do tej strony wykonaj następujące czynności:
        1. Przejdź do [programu Graph Explorer interfejsu API](https://developers.facebook.com/tools/explorer/).
        2. Wybierz **aplikacji**.
        3. Wybierz **Token dostępu strony**, Wyślij **uzyskać** żądania.
        4. Kliknij przycisk **identyfikator strony** w odpowiedzi.
        5. Dołącz teraz **/subscribed_apps** w adresie URL i Wyślij **uzyskać** żądania (pustą odpowiedź).
        6. Prześlij **wpis** żądania. Uzyskaj odpowiedzi jako **Powodzenie: true**.

3. Utwórz token niewygasającym dostępu do interfejsu API programu Graph.

    1. Przejdź do [programu Graph Explorer interfejsu API](https://developers.facebook.com/tools/explorer/).
    2. Wybierz **aplikacji** opcji.
    3. Wybierz **Uzyskaj Token dostępu użytkownika** opcji.
    4. W obszarze **wybierz uprawnienia**, wybierz opcję **manage_pages** i **publish_pages** opcje.
    5. Firma Microsoft użyje **token dostępu** (krótki znajdowały się tokenu) w następnym kroku.

4. Używamy narzędzia Postman dla kilka następnych kroków.

    1. Otwórz **Postman** (lub jego [tutaj](https://www.getpostman.com/)).
    2. Zaimportuj te dwa pliki:
        1. [Kolekcja postman Collection](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/Facebook%20Permanant%20Page%20Access%20Token.postman_collection.json)
        2. [Środowiska postman](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FB%20Page%20Access%20Token%20Environment.postman_environment.json)       
    3. Zaktualizuj zmienne środowiskowe:
    
    | Klucz | Wartość   | 
    | -------------------- |-------------|
    | appId   | Wstaw identyfikator aplikacji usługi Facebook  | 
    | appSecret | Wstaw tutaj klucz tajny aplikacji Facebook | 
    | short_lived_token | Wstaw token dostępu użytkownika krótkotrwałe, który został wygenerowany w poprzednim kroku |
    4. Teraz uruchom 3 interfejsów API na liście w kolekcji: 
        1. Wybierz **Generuj Token dostępu Long-Lived** i kliknij przycisk **wysyłania**.
        2. Wybierz **Uzyskaj identyfikator użytkownika** i kliknij przycisk **wysyłania**.
        3. Wybierz **Get Token stałe strony na dostęp** i kliknij przycisk **wysyłania**.
    5. Kopiuj **access_token** z odpowiedzi i przypisać ją do ustawienia aplikacji **fb:PageAccessToken**.

Gotowe.

Rozwiązanie wysyła wszystkie obrazy i tekst opublikowanego na stronie w usłudze Facebook do usługi Content Moderator. Przepływy pracy, które wcześniej skonfigurowano są wywoływane. Zawartość, które nie zostały spełnione kryteria zdefiniowane w wynikach przepływów pracy w przeglądach w ramach narzędzie do przeglądu. Pozostała część zawartości zostanie opublikowany.

## <a name="license"></a>Licencja

Wszystkie zestawy SDK Microsoft Cognitive Services i przykłady są licencjonowane z licencją MIT. Aby uzyskać więcej informacji, zobacz [licencji](https://microsoft.mit-license.org/).

## <a name="developer-code-of-conduct"></a>Kodeks postępowania dewelopera

Deweloperzy korzystający z usług Cognitive Services, łącznie z tej biblioteki klienckiej & próbki, można oczekiwać, postępuj zgodnie z "dewelopera kodu z postępowania dla Microsoft Cognitive Services", podczas gdy znaleziono w http://go.microsoft.com/fwlink/?LinkId=698895.

## <a name="next-steps"></a>Kolejne kroki

1. [Obejrzyj pokaz (wideo)](https://channel9.msdn.com/Events/Build/2017/T6033) tego rozwiązania z Microsoft Build 2017.
1. [Przykład usługi Facebook w witrynie Github](https://github.com/MicrosoftContentModerator/samples-fbPageModeration)
1. https://docs.microsoft.com/azure/azure-functions/functions-create-github-webhook-triggered-function
2. http://ukimiawz.github.io/facebook/2015/08/12/webhook-facebook-subscriptions/
3. http://stackoverflow.com/questions/17197970/facebook-permanent-page-access-token
