---
title: Program Microsoft Graph dla usługi Azure Active Directory Identity Protection | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wykonywać zapytania dla programu Microsoft Graph dla listy zdarzeń o podwyższonym ryzyku i skojarzonych informacji z usługi Azure Active Directory.
services: active-directory
keywords: Usługa Azure active directory identity protection, zdarzenie o podwyższonym ryzyku, luk w zabezpieczeniach, zasadach zabezpieczeń program Microsoft Graph
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: fa109ba7-a914-437b-821d-2bd98e681386
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: joflore
ms.reviewer: sahandle
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3357cfd5e845346534f263c768b5cf6b6a38ea4e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60296303"
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Rozpoczynanie pracy z usługą Azure Active Directory Identity Protection i Microsoft Graph

Usługa Microsoft Graph programu Microsoft unified punkt końcowy interfejsu API i stroną główną [usługi Azure Active Directory Identity Protection](../active-directory-identityprotection.md) interfejsów API. Istnieją trzy interfejsy API, które udostępniają informacje o ryzykownych użytkowników i logowania. Pierwszym interfejsem API **identityRiskEvents**, służy do wykonywania zapytań programu Microsoft Graph, aby uzyskać listę [zdarzeń o podwyższonym ryzyku](../reports-monitoring/concept-risk-events.md) i skojarzonych informacji. Drugi interfejs API **riskyUsers**, pozwala do wykonywania zapytań w programie Microsoft Graph dla informacji o użytkownikach Identity Protection wykryte jako ryzyka. Trzeci interfejsu API, **signIn**, można wyszukać Microsoft Graph informacji na temat usługi Azure AD, logowania z określonymi właściwościami, które dotyczą stan ryzyka szczegółów i poziomu. Ten artykuł ułatwia rozpoczęcie pracy z łączenia z programem Microsoft Graph i wykonywania zapytań względem tych interfejsów API. Szczegółowe wprowadzenie, pełną dokumentację i dostępu do programu Graph Explorer, zobacz [witryny programu Microsoft Graph](https://graph.microsoft.io/) lub określonych dokumentacji dla tych interfejsów API:

* [identityRiskEvents interfejsu API](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta)
* [riskyUsers API](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta)
* [Logowanie za pomocą interfejsu API](https://docs.microsoft.com/graph/api/resources/signin?view=graph-rest-beta)


## <a name="connect-to-microsoft-graph"></a>Nawiązać połączenie z programu Microsoft graph

Istnieją cztery kroki uzyskiwania dostępu do danych ochronę tożsamości za pomocą programu Microsoft Graph:

1. Pobierz nazwę domeny.
2. Tworzenie nowej rejestracji aplikacji. 
3. Użyj tego wpisu tajnego i kilka innych rodzajów informacji do uwierzytelniania w programie Microsoft Graph, sposób ich otrzymywania tokenu uwierzytelniania. 
4. Używanie tego tokenu, aby wysyłać żądania do punktu końcowego interfejsu API i pobrać zwrócone dane Identity Protection.

Przed rozpoczęciem należy:

* Uprawnienia administratora, aby utworzyć aplikację w usłudze Azure AD
* Nazwa domeny dzierżawy usługi (np. contoso.onmicrosoft.com)


## <a name="retrieve-your-domain-name"></a>Pobieranie nazwy domeny 

1. [Zaloguj się](https://portal.azure.com) do witryny Azure portal jako administrator. 

2. W okienku nawigacji po lewej stronie kliknij **usługi Active Directory**. 
   
    ![Tworzenie aplikacji](./media/graph-get-started/41.png)


3. W **Zarządzaj** kliknij **właściwości**.

    ![Tworzenie aplikacji](./media/graph-get-started/42.png)

4. Skopiuj nazwę domeny.


## <a name="create-a-new-app-registration"></a>Tworzenie nowej rejestracji aplikacji

1. Na **usługi Active Directory** stronie **Zarządzaj** kliknij **rejestracje aplikacji**.

    ![Tworzenie aplikacji](./media/graph-get-started/42.png)


2. W menu u góry kliknij **rejestrowanie nowej aplikacji**.
   
    ![Tworzenie aplikacji](./media/graph-get-started/43.png)

3. Na **Utwórz** strony, wykonaj następujące czynności:
   
    ![Tworzenie aplikacji](./media/graph-get-started/44.png)

    a. W **nazwa** polu tekstowym wpisz nazwę aplikacji (na przykład: AADIP ryzyka zdarzenia interfejsu API aplikacji).
   
    b. Jako **typu**, wybierz opcję **aplikacji sieci Web i / lub interfejs API sieci Web**.
   
    c. W **adres URL logowania** polu tekstowym wpisz `http://localhost`.

    d. Kliknij pozycję **Utwórz**.

4. Aby otworzyć **ustawienia** na liście aplikacji kliknij nowo utworzoną aplikację rejestrację. 

5. Kopiuj **identyfikator aplikacji**.


## <a name="grant-your-application-permission-to-use-the-api"></a>Zezwolić aplikacji za pomocą interfejsu API

1. Na **ustawienia** kliknij **wymagane uprawnienia**.
   
    ![Tworzenie aplikacji](./media/graph-get-started/15.png)

2. Na **wymagane uprawnienia** w pasku narzędzi u góry, kliknij **Dodaj**.
   
    ![Tworzenie aplikacji](./media/graph-get-started/16.png)
   
3. Na **dostępu Dodaj interfejs API** kliknij **wybierz interfejs API**.
   
    ![Tworzenie aplikacji](./media/graph-get-started/17.png)

4. Na **wybierz interfejs API** wybierz **programu Microsoft Graph**, a następnie kliknij przycisk **wybierz**.
   
    ![Tworzenie aplikacji](./media/graph-get-started/18.png)

5. Na **dostępu Dodaj interfejs API** kliknij **wybierz uprawnienia**.
   
    ![Tworzenie aplikacji](./media/graph-get-started/19.png)

6. Na **Włącz dostęp za pomocą** kliknij **odczytać wszystkich informacji o podwyższonym ryzyku**, a następnie kliknij przycisk **wybierz**.
   
    ![Tworzenie aplikacji](./media/graph-get-started/20.png)

7. Na **dostępu Dodaj interfejs API** kliknij **gotowe**.
   
    ![Tworzenie aplikacji](./media/graph-get-started/21.png)

8. Na **wymagane uprawnienia** kliknij **Udziel uprawnień**, a następnie kliknij przycisk **tak**.
   
    ![Tworzenie aplikacji](./media/graph-get-started/22.png)



## <a name="get-an-access-key"></a>Uzyskiwanie klucza dostępu

1. Na **ustawienia** kliknij **klucze**.
   
    ![Tworzenie aplikacji](./media/graph-get-started/23.png)

2. Na **klucze** strony, wykonaj następujące czynności:
   
    ![Tworzenie aplikacji](./media/graph-get-started/24.png)

    a. W **Opis klucza** pole tekstowe, wpisz opis (na przykład *zdarzenie o podwyższonym ryzyku AADIP*).
    
    b. Jako **czas trwania**, wybierz opcję **w 1 rok**.

    c. Kliknij pozycję **Zapisz**.
   
    d. Skopiuj wartość klucza, a następnie wklej go w bezpiecznym miejscu.   
   
   > [!NOTE]
   > Jeśli w przypadku utraty tego klucza, trzeba będzie wrócić do tej sekcji i Utwórz nowy klucz. Klucz tajny ten klucz należy przechowywać: każdy, kto posiada on uzyskiwać dostęp do danych.
   > 
   > 

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-events-api"></a>Uwierzytelnianie w programie Microsoft Graph i wykonywania zapytań względem interfejsu API zdarzenia o podwyższonym ryzyku tożsamości

W tym momencie powinien mieć:

- Nazwa domeny dzierżawy

- Identyfikator klienta 

- Klucz 


Na potrzeby uwierzytelniania, Wyślij żądanie post `https://login.microsoft.com` z następującymi parametrami w treści:

- grant_type: "**client_credentials**"

-  Zasób: `https://graph.microsoft.com`

- client_id: \<Identyfikatora klienta\>

- wartość client_secret: \<klucz\>


W przypadku powodzenia spowoduje to zwrócenie tokenu uwierzytelniania.  
Do wywołania interfejsu API, należy utworzyć nagłówek przy użyciu następującego parametru:

    `Authorization`=”<token_type> <access_token>"


Podczas uwierzytelniania, można znaleźć typu token i token dostępu w zwrócony token.

Wyślij ten nagłówek jako żądanie do następującego adresu URL interfejsu API: `https://graph.microsoft.com/beta/identityRiskEvents`

Odpowiedzi, jeśli to się powiedzie, to zbiór tożsamości zdarzeń o podwyższonym ryzyku i powiązane dane w formacie OData JSON, który może być analizowana i obsługiwane zgodnie z potrzebami.

Poniżej przedstawiono przykładowy kod do uwierzytelniania i wywoływanie interfejsu API przy użyciu programu PowerShell.  
Wystarczy dodać Identyfikatora klienta, klucza tajnego i domena dzierżawy.

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

## <a name="query-the-apis"></a>Kwerendy interfejsów API

Te trzy interfejsy API zapewniają wiele możliwości, aby pobrać informacje o ryzykownych użytkowników i logowania w Twojej organizacji. Poniżej przedstawiono niektóre typowe przypadki użycia dla tych interfejsów API i prośby o przykłady skojarzone. Możesz uruchomić te zapytania, korzystającą z przykładowego kodu powyżej lub za pomocą [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).

### <a name="get-the-high-risk-and-medium-risk-events-identityriskevents-api"></a>Pobieranie zdarzeń o wysokim ryzyku i średniego ryzyka (identityRiskEvents interfejsu API)

Średni i o wysokim ryzyku zdarzenia reprezentują te, które mogą mieć możliwość wyzwalacza logowania Identity Protection i zasad ryzyka dla użytkownika. Ponieważ mają one średnim lub wysokim prawdopodobieństwo, że użytkownika próby logowania nie jest właścicielem uzasadnione tożsamości, korygowanie te zdarzenia należy traktować priorytetowo. 

```
GET https://graph.microsoft.com/beta/identityRiskEvents?`$filter=riskLevel eq 'high' or riskLevel eq 'medium'" 
```

### <a name="get-all-of-the-users-who-successfully-passed-an-mfa-challenge-triggered-by-risky-sign-ins-policy-riskyusers-api"></a>Pobieranie wszystkich użytkowników, którzy przeszli żądania uwierzytelniania MFA wyzwolone przez zasady ryzykownych logowań (riskyUsers interfejsu API)

Aby zrozumieć wpływ Identity Protection, że zasady na podstawie ryzyka na swojej organizacji można badać wszystkich użytkowników, którzy przeszli żądania uwierzytelniania MFA wyzwolone przez zasady ryzykownych logowań. Te informacje mogą ułatwić zrozumienie, które użytkownicy Identity Protection mógł błędnie wykryty w jako ryzyka, które autoryzowanych użytkowników wykonywać akcje, które sztucznej Inteligencji za ryzykowne.

```
GET https://graph.microsoft.com/beta/riskyUsers?$filter=riskDetail eq 'userPassedMFADrivenByRiskBasedPolicy'
```

### <a name="get-all-the-risky-sign-ins-for-a-specific-user-signin-api"></a>Pobierz wszystkie ryzykowne logowania dla określonego użytkownika (logowanie za pomocą interfejsu API)

Gdy uznasz, że użytkownik został złamany, możesz lepiej zrozumieć stanu ich ryzyka, pobierając wszystkie ich ryzykownych logowań. 

```
https://graph.microsoft.com/beta/identityRiskEvents?`$filter=userID eq '<userID>' and riskState eq 'atRisk'
```




## <a name="next-steps"></a>Kolejne kroki

Gratulacje, właśnie wykonana pierwszego wywołania w programie Microsoft Graph!  
Teraz możesz zbadać zdarzenia o podwyższonym ryzyku tożsamości i używania danych, jednak wedle uznania.


Aby dowiedzieć się więcej na temat programu Microsoft Graph i jak tworzyć aplikacje przy użyciu interfejsu API programu Graph, zapoznaj się z [dokumentacji](https://docs.microsoft.com/graph/overview) i znacznie więcej informacji na temat [witryny programu Microsoft Graph](https://developer.microsoft.com/graph). 


Aby uzyskać powiązane informacje Zobacz:

-  [Ochrona tożsamości w usłudze Azure Active Directory](../active-directory-identityprotection.md)

-  [Typy zdarzeń o podwyższonym ryzyku wykrywane przez usługi Azure Active Directory Identity Protection](../reports-monitoring/concept-risk-events.md)

- [Microsoft Graph](https://developer.microsoft.com/graph/)

- [Omówienie programu Microsoft Graph](https://developer.microsoft.com/graph/docs)

- [Główny usługi programu Azure AD Identity Protection](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityprotection_root)