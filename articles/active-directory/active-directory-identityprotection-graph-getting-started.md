---
title: Program Microsoft Graph dla usługi Azure Active Directory Identity Protection | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wykonać zapytanie Microsoft Graph listę zdarzeń o podwyższonym ryzyku i skojarzonych informacji z usługi Azure Active Directory.
services: active-directory
keywords: ochronę tożsamości usługi Azure active directory, zdarzenie ryzyka, luki w zabezpieczeniach, zasady zabezpieczeń, Microsoft Graph
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: fa109ba7-a914-437b-821d-2bd98e681386
ms.service: active-directory
ms.component: protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: markvi
ms.reviewer: nigu
ms.custom: seohack1
ms.openlocfilehash: c6c59285fbb2c81e5e20d28171abfe806bd51122
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34713205"
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Wprowadzenie do usługi Azure Active Directory Identity Protection oraz Microsoft Graph
Program Microsoft Graph jest Microsoft unified punkt końcowy interfejsu API i stroną główną [Azure Active Directory Identity Protection](active-directory-identityprotection.md) interfejsów API. Pierwszy interfejsu API, **identityRiskEvents**, pozwala na zapytanie Microsoft Graph listę [ryzyka zdarzenia](active-directory-identityprotection-risk-events-types.md) i skojarzonych informacji. W tym artykule pobiera pracę podczas badania tego interfejsu API. Szczegółowe wprowadzenie, pełną dokumentację i dostęp do Eksploratora wykresu, zobacz [witryny Microsoft Graph](https://graph.microsoft.io/).


Istnieją cztery kroki w celu uzyskiwania dostępu do danych tożsamości ochrony za pomocą programu Microsoft Graph:

1. Pobierz nazwę domeny.
2. Tworzenie nowej rejestracji aplikacji. 
2. Użyć ten klucz tajny i kilka innych rodzajów informacji do uwierzytelnienia do programu Microsoft Graph, sposób ich otrzymywania tokenu uwierzytelniania. 
3. Użyj tego tokenu, aby żądania do punktu końcowego interfejsu API i wrócić tożsamości ochrony danych.

Przed rozpoczęciem pracy należy:

* Uprawnienia administratora, aby utworzyć aplikację w usłudze Azure AD
* Nazwa domeny Twojej dzierżawy (np. contoso.onmicrosoft.com)


## <a name="retrieve-your-domain-name"></a>Pobierz nazwę domeny 

1. [Zaloguj się](https://portal.azure.com) do portalu Azure jako administrator. 

2. W lewym okienku nawigacji, kliknij polecenie **usługi Active Directory**. 
   
    ![Tworzenie aplikacji](./media/active-directory-identityprotection-graph-getting-started/41.png)


3. W **Zarządzaj** kliknij **właściwości**.

    ![Tworzenie aplikacji](./media/active-directory-identityprotection-graph-getting-started/42.png)

4. Skopiuj nazwę domeny.


## <a name="create-a-new-app-registration"></a>Tworzenie nowej rejestracji aplikacji

1. Na **usługi Active Directory** strony w **Zarządzaj** kliknij **rejestracji aplikacji**.

    ![Tworzenie aplikacji](./media/active-directory-identityprotection-graph-getting-started/42.png)


2. W menu u góry kliknij **nowej rejestracji aplikacji**.
   
    ![Tworzenie aplikacji](./media/active-directory-identityprotection-graph-getting-started/43.png)

3. Na **Utwórz** wykonaj następujące czynności:
   
    ![Tworzenie aplikacji](./media/active-directory-identityprotection-graph-getting-started/44.png)

    a. W **nazwa** tekstowym, wpisz nazwę dla aplikacji (np.: Aplikacja interfejsu API zdarzenia ryzyka AADIP).
   
    b. Jako **typu**, wybierz pozycję **aplikacji sieci Web i / lub interfejs API sieci Web**.
   
    c. W **adres URL logowania** pole tekstowe, typ `http://localhost`.

    d. Kliknij przycisk **Utwórz**.

4. Aby otworzyć **ustawienia** na liście aplikacji kliknij polecenie rejestracji nowo utworzonej aplikacji. 

5. Kopiuj **identyfikator aplikacji**.


## <a name="grant-your-application-permission-to-use-the-api"></a>Zezwolić aplikacji za pomocą interfejsu API

1. Na **ustawienia** kliknij przycisk **wymagane uprawnienia**.
   
    ![Tworzenie aplikacji](./media/active-directory-identityprotection-graph-getting-started/15.png)

2. Na **wymagane uprawnienia** na pasku narzędzi u góry, kliknij przycisk **Dodaj**.
   
    ![Tworzenie aplikacji](./media/active-directory-identityprotection-graph-getting-started/16.png)
   
3. Na **dostępu Dodaj interfejsu API** kliknij przycisk **wybierz interfejs API**.
   
    ![Tworzenie aplikacji](./media/active-directory-identityprotection-graph-getting-started/17.png)

4. Na **wybierz interfejs API** wybierz **Microsoft Graph**, a następnie kliknij przycisk **wybierz**.
   
    ![Tworzenie aplikacji](./media/active-directory-identityprotection-graph-getting-started/18.png)

5. Na **dostępu Dodaj interfejsu API** kliknij przycisk **wybierz uprawnienia**.
   
    ![Tworzenie aplikacji](./media/active-directory-identityprotection-graph-getting-started/19.png)

6. Na **Włącz dostęp** kliknij przycisk **przeczytać wszystkie informacje o tożsamości zagrożenie**, a następnie kliknij przycisk **wybierz**.
   
    ![Tworzenie aplikacji](./media/active-directory-identityprotection-graph-getting-started/20.png)

7. Na **dostępu Dodaj interfejsu API** kliknij przycisk **gotowe**.
   
    ![Tworzenie aplikacji](./media/active-directory-identityprotection-graph-getting-started/21.png)

8. Na **wymagane uprawnienia** kliknij przycisk **udzielanie uprawnień**, a następnie kliknij przycisk **tak**.
   
    ![Tworzenie aplikacji](./media/active-directory-identityprotection-graph-getting-started/22.png)



## <a name="get-an-access-key"></a>Uzyskiwanie klucza dostępu

1. Na **ustawienia** kliknij przycisk **klucze**.
   
    ![Tworzenie aplikacji](./media/active-directory-identityprotection-graph-getting-started/23.png)

2. Na **klucze** wykonaj następujące czynności:
   
    ![Tworzenie aplikacji](./media/active-directory-identityprotection-graph-getting-started/24.png)

    a. W **klucza opis** tekstowym, wpisz opis (na przykład *AADIP ryzyka zdarzeń*).
    
    b. Jako **czas trwania**, wybierz pozycję **w 1 roku**.

    c. Kliknij pozycję **Zapisz**.
   
    d. Skopiuj wartość tego klucza, a następnie wklej go w bezpiecznym miejscu.   
   
   > [!NOTE]
   > Jeśli ten klucz zostanie utracone, konieczne będzie wróć do tej sekcji i Utwórz nowy klucz. Zachowaj ten klucz tajny: każdy użytkownik, który można uzyskać dostęp do danych.
   > 
   > 

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-events-api"></a>Uwierzytelnianie Microsoft Graph i wykonywać zapytania interfejsu API zdarzenia ryzyka tożsamości
W tym momencie powinny mieć:

- Nazwa domeny Twojej dzierżawy

- Identyfikator klienta 

- Klucz 


W celu uwierzytelnienia wysłanie żądania post do `https://login.microsoft.com` z następującymi parametrami w treści:

- Typ grant_type: "**client_credentials**"

-  Zasób: "**https://graph.microsoft.com**"

- client_id: \<Twojego Identyfikatora klienta\>

- client_secret: \<klucz\>


Jeśli to się powiedzie, to zwraca token uwierzytelniania.  
Aby wywołać interfejs API, należy utworzyć nagłówek z następującym parametrem:

    `Authorization`=”<token_type> <access_token>"


Podczas uwierzytelniania, można znaleźć typu token i token dostępu w tokenie zwrócony.

Wyślij ten nagłówek jako żądania do następującego adresu URL interfejsu API: `https://graph.microsoft.com/beta/identityRiskEvents`

Odpowiedź, w przypadku powodzenia jest zbierania zdarzeń o podwyższonym ryzyku tożsamości i skojarzone dane w formacie OData JSON, który może być analizowana i obsługiwane zgodnie z własnymi potrzebami.

Oto przykładowy kod do uwierzytelniania i wywołanie interfejsu API przy użyciu programu PowerShell.  
Wystarczy dodać Identyfikatora klienta, klucz tajny i domena dzierżawy.

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


## <a name="next-steps"></a>Kolejne kroki

Gratulacje, wystarczy wprowadzić Twoje pierwsze wywołanie do programu Microsoft Graph!  
Teraz można zbadać zdarzenia o podwyższonym ryzyku tożsamości i korzystanie z danych, jednak użytkownik mieści się w temacie.

Aby dowiedzieć się więcej na temat programu Microsoft Graph i jak tworzyć aplikacje przy użyciu interfejsu API programu Graph, zapoznaj się [dokumentacji](https://graph.microsoft.io/docs) i znacznie więcej informacji na temat [witryny Microsoft Graph](https://graph.microsoft.io/). Ponadto upewnij się, że zakładki [Azure AD Identity Protection API](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root) strony, która zawiera listę wszystkich interfejsów API ochrony tożsamości, które są dostępne na wykresie. Jak możemy dodać nowe sposoby pracy z ochrony tożsamości za pomocą interfejsu API, zobaczysz je na tej stronie.

Aby uzyskać odpowiednie informacje Zobacz:

-  [Ochronę tożsamości usługi Azure Active Directory](active-directory-identityprotection.md)

-  [Rodzaje zdarzeń o podwyższonym ryzyku wykrywanych przez Azure Active Directory Identity Protection](active-directory-identityprotection-risk-events-types.md)

- [Microsoft Graph](https://graph.microsoft.io/)

- [Omówienie programu Microsoft Graph](https://graph.microsoft.io/docs)

- [Katalogu głównego usługi programu Azure AD Identity Protection](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root)

