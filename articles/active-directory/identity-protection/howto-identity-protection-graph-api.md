---
title: Interfejs API programu Microsoft Graph dla ochrony tożsamości usługi Azure Active Directory
description: Dowiedz się, jak wysyłać zapytania do wykrywania zagrożeń programu Microsoft Graph i skojarzonych informacji z usługi Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: f6fd62ccab4d6e32b23835d280732797e133ada8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671620"
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Wprowadzenie do usługi Azure Active Directory Identity Protection i Microsoft Graph

Microsoft Graph to ujednolicony punkt końcowy interfejsu API firmy Microsoft i strona główna interfejsów API [ochrony tożsamości usługi Azure Active Directory.](../active-directory-identityprotection.md) Istnieją cztery interfejsy API, które ujawniają informacje o ryzykownych użytkowników i logowaniach. Pierwszy interfejs API, **riskDetection**, umożliwia wykonywanie zapytań o program Microsoft Graph w celu uzyskania listy wykrywania ryzyka połączonego użytkownika i logowania oraz skojarzonych informacji o wykrywaniu. Drugi interfejs API, **riskyUsers**, umożliwia wykonywanie zapytań o program Microsoft Graph w celu uzyskania informacji o użytkownikach Identity Protection wykrytych jako ryzyko. Trzeci interfejs API, **signIn**, umożliwia kwerendę microsoft graph dla informacji na temat logowania usługi Azure AD z określonych właściwości związanych ze stanem ryzyka, szczegóły i poziom. Czwarty interfejs API, **identityRiskEvents**, umożliwia wykonywanie zapytań o program Microsoft Graph w celu uzyskania listy [wykrywania ryzyka](../reports-monitoring/concept-risk-events.md) i powiązanych informacji. Interfejs API identityRiskEvents zostanie przestarzały 10 stycznia 2020 r.; zamiast tego zalecamy użycie interfejsu API **riskDetections.** Ten artykuł rozpoczyna się od nawiązania połączenia z programem Microsoft Graph i wykonywania zapytań o te interfejsy API. Aby uzyskać szczegółowe wprowadzenie, pełną dokumentację i dostęp do Eksploratora wykresów, zobacz [witrynę programu Microsoft Graph](https://graph.microsoft.io/) lub dokumentację dotyczącą określonych odwołań dla tych interfejsów API:

* [riskDetection API](/graph/api/resources/riskdetection?view=graph-rest-beta)
* [riskyUsers API](/graph/api/resources/riskyuser?view=graph-rest-beta)
* [signIn API](/graph/api/resources/signin?view=graph-rest-beta)
* [identityRiskEvents API](/graph/api/resources/identityriskevent?view=graph-rest-beta) *zostanie przestarzały 10 stycznia 2020 r.*

## <a name="connect-to-microsoft-graph"></a>Łączenie się z wykresem firmy Microsoft

Istnieją cztery kroki uzyskiwania dostępu do danych ochrony tożsamości za pośrednictwem programu Microsoft Graph:

1. Pobierz nazwę domeny.
2. Utwórz nową rejestrację aplikacji. 
3. Użyj tego klucza tajnego i kilku innych informacji do uwierzytelniania w programie Microsoft Graph, gdzie otrzymasz token uwierzytelniania. 
4. Ten token służy do żądania do punktu końcowego interfejsu API i uzyskać dane ochrony tożsamości z powrotem.

Zanim zaczniesz, będziesz potrzebować:

* Uprawnienia administratora do tworzenia aplikacji w usłudze Azure AD
* Nazwa domeny dzierżawy (na przykład contoso.onmicrosoft.com)

## <a name="retrieve-your-domain-name"></a>Pobieranie nazwy domeny 

1. [Zaloguj](https://portal.azure.com) się do witryny Azure portal jako administrator. 
1. W lewym okienku nawigacji kliknij pozycję **Active Directory**. 

   ![Tworzenie aplikacji](./media/howto-identity-protection-graph-api/41.png)

1. W sekcji **Zarządzanie** kliknij pozycję **Właściwości**.

   ![Tworzenie aplikacji](./media/howto-identity-protection-graph-api/42.png)

1. Skopiuj nazwę domeny.

## <a name="create-a-new-app-registration"></a>Tworzenie nowej rejestracji aplikacji

1. Na stronie **Active Directory** w sekcji **Zarządzanie** kliknij pozycję **Rejestracje aplikacji**.

   ![Tworzenie aplikacji](./media/howto-identity-protection-graph-api/42.png)

1. W menu u góry kliknij pozycję **Nowa rejestracja aplikacji**.

   ![Tworzenie aplikacji](./media/howto-identity-protection-graph-api/43.png)

1. Na stronie **Tworzenie** wykonaj następujące czynności:

   ![Tworzenie aplikacji](./media/howto-identity-protection-graph-api/44.png)

   1. W pola tekstowym **Nazwa** wpisz nazwę aplikacji (na przykład: Aplikacja interfejsu API wykrywania ryzyka usługi Azure AD).

   1. Jako **typ**, wybierz **aplikację sieci Web i / lub interfejs API sieci Web**.

   1. W polach **tekstowych Wpisz** `http://localhost`polecenie .

   1. Kliknij przycisk **Utwórz**.
1. Aby otworzyć stronę **Ustawienia,** na liście aplikacji kliknij nowo utworzoną rejestrację aplikacji. 
1. Skopiuj **identyfikator aplikacji**.

## <a name="grant-your-application-permission-to-use-the-api"></a>Udzielanie uprawnień aplikacji do korzystania z interfejsu API

1. Na stronie **Ustawienia** kliknij pozycję **Wymagane uprawnienia**.

   ![Tworzenie aplikacji](./media/howto-identity-protection-graph-api/15.png)

1. Na stronie **Wymagane uprawnienia** na pasku narzędzi u góry kliknij pozycję **Dodaj**.

   ![Tworzenie aplikacji](./media/howto-identity-protection-graph-api/16.png)

1. Na stronie **Dodaj dostęp do interfejsu API** kliknij pozycję Wybierz interfejs **API**.

   ![Tworzenie aplikacji](./media/howto-identity-protection-graph-api/17.png)

1. Na stronie **Wybierz interfejs API** wybierz pozycję Microsoft **Graph**, a następnie kliknij przycisk **Wybierz**.

   ![Tworzenie aplikacji](./media/howto-identity-protection-graph-api/18.png)

1. Na stronie **Dodaj dostęp do interfejsu API** kliknij pozycję Wybierz **uprawnienia**.

   ![Tworzenie aplikacji](./media/howto-identity-protection-graph-api/19.png)

1. Na stronie **Włącz dostęp** kliknij pozycję Przeczytaj wszystkie informacje o **ryzyku tożsamości,** a następnie kliknij przycisk **Wybierz**.

   ![Tworzenie aplikacji](./media/howto-identity-protection-graph-api/20.png)

1. Na stronie **Dodaj dostęp do interfejsu API** kliknij pozycję **Gotowe**.

   ![Tworzenie aplikacji](./media/howto-identity-protection-graph-api/21.png)

1. Na stronie **Wymagane uprawnienia** kliknij pozycję **Udziel uprawnień**, a następnie kliknij przycisk **Tak**.

   ![Tworzenie aplikacji](./media/howto-identity-protection-graph-api/22.png)

## <a name="get-an-access-key"></a>Uzyskiwanie klucza dostępu

1. Na stronie **Ustawienia** kliknij pozycję **Klawisze**.

   ![Tworzenie aplikacji](./media/howto-identity-protection-graph-api/23.png)

1. Na stronie **Klawisze** wykonaj następujące czynności:

   ![Tworzenie aplikacji](./media/howto-identity-protection-graph-api/24.png)

   1. W polu **tekstowym Opis klucza** wpisz opis (na przykład *wykrywanie ryzyka usługi Azure AD).*
   1. Jako **Czas trwania**wybierz opcję W ciągu **1 roku**.
   1. Kliknij przycisk **Zapisz**.
   1. Skopiuj wartość klucza, a następnie wklej ją w bezpieczne miejsce.   
   
   > [!NOTE]
   > Jeśli utracisz ten klucz, będziesz musiał wrócić do tej sekcji i utworzyć nowy klucz. Zachowaj ten klucz w tajemnicy: każdy, kto go ma, może uzyskać dostęp do Twoich danych.
   > 

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-detections-api"></a>Uwierzytelnij się w programie Microsoft Graph i zapytaj interfejs API wykrywania ryzyka tożsamości

W tym momencie powinieneś mieć:

- Nazwa domeny dzierżawy
- Identyfikator klienta 
- Kluczem do 

Aby uwierzytelnić, wyślij żądanie `https://login.microsoft.com` wpisu z następującymi parametrami w treści:

- grant_type: "**client_credentials**"
- Zasobów:`https://graph.microsoft.com`
- client_id: \<identyfikator klienta\>
- client_secret: \<twój klucz\>

Jeśli się powiedzie, spowoduje to zwrot tokenu uwierzytelniania.  
Aby wywołać interfejs API, utwórz nagłówek z następującym parametrem:

```
`Authorization`="<token_type> <access_token>"
```

Podczas uwierzytelniania można znaleźć typ tokenu i token dostępu w tokenie zwróconym.

Wyślij ten nagłówek jako żądanie do następującego adresu URL interfejsu API:`https://graph.microsoft.com/beta/identityRiskEvents`

Odpowiedź, jeśli zakończy się pomyślnie, jest kolekcją wykrywania ryzyka tożsamości i skojarzonych danych w formacie OData JSON, które mogą być analizowane i obsługiwane zgodnie z zadaniem.

Oto przykładowy kod do uwierzytelniania i wywoływania interfejsu API przy użyciu programu PowerShell.  
Wystarczy dodać identyfikator klienta, klucz tajny i domenę dzierżawy.

```PowerShell
    $ClientID       = "<your client ID here>"        # Should be a ~36 hex character string; insert your info here
    $ClientSecret   = "<your client secret here>"    # Should be a ~44 character string; insert your info here
    $tenantdomain   = "<your tenant domain here>"    # For example, contoso.onmicrosoft.com

    $loginURL       = "https://login.microsoft.com"
    $resource       = "https://graph.microsoft.com"

    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    Write-Output $oauth

    if ($oauth.access_token -ne $null) {
        $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

        $url = "https://graph.microsoft.com/beta/identityRiskEvents"
        Write-Output $url

        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)

        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output $event
        }

    } else {
        Write-Host "ERROR: No Access Token"
    } 
```

## <a name="query-the-apis"></a>Kwerenda o interfejsy API

Te trzy interfejsy API zapewniają wiele możliwości pobierania informacji o ryzykownych użytkownikach i logowaniach w organizacji. Poniżej znajduje się kilka typowych przypadków użycia tych interfejsów API i skojarzonych przykładowych żądań. Te kwerendy można uruchamiać przy użyciu przykładowego kodu powyżej lub przy użyciu [Eksploratora grafów](https://developer.microsoft.com/graph/graph-explorer).

### <a name="get-all-of-the-offline-risk-detections-riskdetection-api"></a>Pobierz wszystkie wykrywania ryzyka w trybie offline (riskDetection API)

Dzięki zasadom ryzyka logowania ochrony tożsamości można stosować warunki, gdy ryzyko zostanie wykryte w czasie rzeczywistym. Ale co z wykrywaniem, które są wykrywane w trybie offline? Aby zrozumieć, jakie wykrycia wystąpiły w trybie offline, a zatem nie wyzwoliłoby zasad ryzyka logowania, można zbadać interfejs API riskDetection.

```
GET https://graph.microsoft.com/beta/riskDetections?$filter=detectionTimingType eq 'offline'
```

### <a name="get-all-of-the-users-who-successfully-passed-an-mfa-challenge-triggered-by-risky-sign-ins-policy-riskyusers-api"></a>Pobierz wszystkich użytkowników, którzy pomyślnie przeszedł wyzwanie mfa wywołane przez ryzykowne zasady logowania (riskyUsers API)

Aby zrozumieć wpływ zasad opartych na ryzyku ochrony tożsamości na organizację, można zbadać wszystkich użytkowników, którzy pomyślnie przeszli wyzwanie usługi MFA wywołane przez zasadę ryzykownych logów. Te informacje mogą pomóc w zrozumieniu, którzy użytkownicy Identity Protection mogą zostać błędnie wykryci jako zagrożeni i którzy z uprawnionych użytkowników mogą wykonywać działania, które sztuczna inteligencja uzna za ryzykowne.

```
GET https://graph.microsoft.com/beta/riskyUsers?$filter=riskDetail eq 'userPassedMFADrivenByRiskBasedPolicy'
```

### <a name="get-all-the-risky-sign-ins-for-a-specific-user-signin-api"></a>Pobierz wszystkie ryzykowne logowania dla określonego użytkownika (signIn API)

Jeśli uważasz, że użytkownik może zostać naruszony, można lepiej zrozumieć stan ryzyka, pobierając wszystkie ich ryzykowne logowania. 

```
https://graph.microsoft.com/beta/identityRiskEvents?`$filter=userID eq '<userID>' and riskState eq 'atRisk'
```
## <a name="next-steps"></a>Następne kroki

Gratulacje, właśnie się pierwsze połączenie z Microsoft Graph!  
Teraz możesz wyszukiwać wykrywanie ryzyka tożsamości i używać danych, jak tylko się z niego dodasz.

Aby dowiedzieć się więcej o programie Microsoft Graph i sposobie tworzenia aplikacji przy użyciu interfejsu API programu Graph, zapoznaj się z [dokumentacją](/graph/overview) i nie tylko w [witrynie programu Microsoft Graph](https://developer.microsoft.com/graph). 

Aby uzyskać powiązane informacje, zobacz:

- [Ochrona tożsamości w usłudze Azure Active Directory](../active-directory-identityprotection.md)
- [Typy wykrywania ryzyka wykryte przez usługę Azure Active Directory Identity Protection](../reports-monitoring/concept-risk-events.md)
- [Microsoft Graph](https://developer.microsoft.com/graph/)
- [Overview of Microsoft Graph](https://developer.microsoft.com/graph/docs) (Omówienie programu Microsoft Graph)
- [Katalog główny usługi Azure Identity Protection Service](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityprotection_root)
