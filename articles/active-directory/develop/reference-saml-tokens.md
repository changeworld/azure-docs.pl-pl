---
title: Typy oświadczeń tokenu usługi Azure AD &
description: Przewodnik do zrozumienia i oceny oświadczeń w tokenach SAML 2.0 i JSON Web Tokens (JWT) wystawionych przez usługę Azure Active Directory (AAD)
documentationcenter: na
author: rwike77
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/22/2018
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: b7ba4abd45fff8548c361f5e5ed44ef45fe32bbe
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80883444"
---
# <a name="azure-ad-saml-token-reference"></a>Odwołanie do tokenu SAML usługi Azure AD

Usługa Azure Active Directory (Azure AD) emituje kilka typów tokenów zabezpieczających w przetwarzaniu każdego przepływu uwierzytelniania. W tym dokumencie opisano format, właściwości zabezpieczeń i zawartość każdego typu tokenu.

## <a name="claims-in-saml-tokens"></a>Oświadczenia w tokenach SAML

> [!div class="mx-codeBreakAll"]
> | Nazwa | Równoważne oświadczenie JWT | Opis | Przykład |
> | --- | --- | --- | ------------|
> |Grupy odbiorców | `aud` |Zamierzonego odbiorcy tokenu. Aplikacja, która odbiera token, musi sprawdzić, czy wartość odbiorców jest poprawna i odrzucić wszystkie tokeny przeznaczone dla różnych odbiorców. | `<AudienceRestriction>`<br>`<Audience>`<br>`https://contoso.com`<br>`</Audience>`<br>`</AudienceRestriction>`  |
> | Błyskawiczne uwierzytelnianie | |Rejestruje datę i godzinę wystąpienia uwierzytelnienia. | `<AuthnStatement AuthnInstant="2011-12-29T05:35:22.000Z">` | 
> |Metoda uwierzytelniania | `amr` |Identyfikuje sposób uwierzytelniania obiektu tokenu. | `<AuthnContextClassRef>`<br>`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password`<br>`</AuthnContextClassRef>` |
> |Imię | `given_name` |Zawiera pierwszą lub "podana" nazwę użytkownika, ustawioną w obiekcie użytkownika usługi Azure AD. | `<Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname">`<br>`<AttributeValue>Frank<AttributeValue>`  |
> |Grupy | `groups` |Udostępnia identyfikatory obiektów, które reprezentują członkostwo podmiotu w grupie. Te wartości są unikatowe (zobacz identyfikator obiektu) i mogą być bezpiecznie używane do zarządzania dostępem, takie jak wymuszanie autoryzacji dostępu do zasobu. Grupy uwzględnione w żądaniu grup są konfigurowane na podstawie dla aplikacji, za pomocą właściwości "groupMembershipClaims" manifestu aplikacji. Wartość null wyklucza wszystkie grupy, wartość "SecurityGroup" będzie zawierać tylko członkostwo w grupie zabezpieczeń usługi Active Directory, a wartość "Wszystkie" będzie zawierać zarówno grupy zabezpieczeń, jak i listy dystrybucyjne usługi Office 365. <br><br> **Uwagi:** <br> Jeśli liczba grup, w których znajduje się użytkownik przekracza limit (150 dla SAML, 200 dla JWT), a następnie oświadczenie nadajności zostanie dodane źródła oświadczeń wskazujące punkt końcowy wykresu zawierający listę grup dla użytkownika. (w . | `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">`<br>`<AttributeValue>07dd8a60-bf6d-4e17-8844-230b77145381</AttributeValue>` |
> | Wskaźnik przeceny grup | `groups:src1` | W przypadku żądań tokenu, `hasgroups` które nie są ograniczone (patrz wyżej), ale nadal zbyt duże dla tokenu, zostanie uwzględnione łącze do pełnej listy grup dla użytkownika. W przypadku SAML jest on dodawany `groups` jako nowe roszczenie zamiast roszczenia. | `<Attribute Name=" http://schemas.microsoft.com/claims/groups.link">`<br>`<AttributeValue>https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects<AttributeValue>` |
> |Dostawca tożsamości | `idp` |Rejestruje dostawcę tożsamości, który uwierzytelnił podmiot tokenu. Wartość ta jest identyczna z wartością oświadczenia Emitenta, chyba że konto użytkownika znajduje się w innym najemcy niż emitent. | `<Attribute Name=" http://schemas.microsoft.com/identity/claims/identityprovider">`<br>`<AttributeValue>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/<AttributeValue>` |
> |IssuedAt | `iat` |Przechowuje czas, w którym token został wystawiony. Jest często używany do pomiaru świeżości tokenu. | `<Assertion ID="_d5ec7a9b-8d8f-4b44-8c94-9812612142be" IssueInstant="2014-01-06T20:20:23.085Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">` |
> |Wystawca | `iss` |Identyfikuje usługę tokenu zabezpieczającego (STS), która konstruuje i zwraca token. W tokenach, które zwraca usługi Azure AD, wystawca jest sts.windows.net. Identyfikator GUID w wartości oświadczenia wystawcy jest identyfikatorem dzierżawy katalogu usługi Azure AD. Identyfikator dzierżawy jest niezmiennym i niezawodnym identyfikatorem katalogu. | `<Issuer>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/</Issuer>` |
> |Nazwisko | `family_name` |Zawiera nazwisko, nazwisko lub nazwisko użytkownika zgodnie z definicją w obiekcie użytkownika usługi Azure AD. | `<Attribute Name=" http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname">`<br>`<AttributeValue>Miller<AttributeValue>` |
> |Nazwa | `unique_name` |Udostępnia zrozumiałą wartość identyfikującą podmiot tokenu. Ta wartość nie jest gwarantowana jest unikatowa w obrębie dzierżawy i jest przeznaczony do użycia tylko do celów wyświetlania. | `<Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">`<br>`<AttributeValue>frankm@contoso.com<AttributeValue>`|
> |Identyfikator obiektu | `oid` |Zawiera unikatowy identyfikator obiektu w usłudze Azure AD. Ta wartość jest niezmienna i nie można jej ponownie przypisać ani ponownie. Użyj identyfikatora obiektu, aby zidentyfikować obiekt w kwerendach do usługi Azure AD. | `<Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">`<br>`<AttributeValue>528b2ac2-aa9c-45e1-88d4-959b53bc7dd0<AttributeValue>` |
> |Role | `roles` |Reprezentuje wszystkie role aplikacji, które podmiot został przyznany zarówno bezpośrednio, jak i pośrednio za pośrednictwem członkostwa w grupie i może służyć do wymuszania kontroli dostępu opartej na rolach. Role aplikacji są definiowane na podstawie `appRoles` dla aplikacji, za pomocą właściwości manifestu aplikacji. Właściwość `value` każdej roli aplikacji jest wartością, która pojawia się w żądaniu ról. | `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/role">`|
> |Podmiot | `sub` |Identyfikuje podmiot zabezpieczeń, o którym token potwierdza informacje, takie jak użytkownik aplikacji. Ta wartość jest niezmienna i nie można jej ponownie przypisać ani ponownie użyć, więc może służyć do bezpiecznego przeprowadzania kontroli autoryzacji. Ponieważ temat jest zawsze obecny w tokenach problemów usługi Azure AD, zaleca się przy użyciu tej wartości w systemie autoryzacji ogólnego przeznaczenia. <br> `SubjectConfirmation`nie jest roszczeniem. Opisano w nim, jak obiekt tokenu jest weryfikowany. `Bearer`wskazuje, że uczestnik jest potwierdzony przez ich posiadanie tokenu. | `<Subject>`<br>`<NameID>S40rgb3XjhFTv6EQTETkEzcgVmToHKRkZUIsJlmLdVc</NameID>`<br>`<SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />`<br>`</Subject>`|
> |Identyfikator dzierżawy | `tid` |Niezmienny identyfikator, który nie nadaje się do ponownegoużynania identyfikator, który identyfikuje dzierżawy katalogu, który wystawił token. Tej wartości można użyć, aby uzyskać dostęp do zasobów katalogowych specyficznych dla dzierżawy w aplikacji wielodostępowej. Na przykład można użyć tej wartości do identyfikowania dzierżawy w wywołaniu interfejsu API wykresu. | `<Attribute Name="http://schemas.microsoft.com/identity/claims/tenantid">`<br>`<AttributeValue>cbb1a5ac-f33b-45fa-9bf5-f37db0fed422<AttributeValue>`|
> |Czas życia tokenu | `nbf`, `exp` |Definiuje przedział czasu, przez który token jest prawidłowy. Usługa, która sprawdza poprawność tokenu należy sprawdzić, czy bieżąca data jest w okresie istnienia tokenu, w przeciwnym razie należy odrzucić token. Usługa może zezwalać na maksymalnie pięć minut poza zakresem okresu istnienia tokenu, aby uwzględnić wszelkie różnice w czasie zegara ("pochylenie czasu") między usługą Azure AD a usługą. | `<Conditions`<br>`NotBefore="2013-03-18T21:32:51.261Z"`<br>`NotOnOrAfter="2013-03-18T22:32:51.261Z"`<br>`>` <br>|

## <a name="sample-saml-token"></a>Przykładowy token SAML

Jest to przykład typowego tokenu SAML.

    <?xml version="1.0" encoding="UTF-8"?>
    <t:RequestSecurityTokenResponse xmlns:t="http://schemas.xmlsoap.org/ws/2005/02/trust">
      <t:Lifetime>
        <wsu:Created xmlns:wsu="https://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T05:15:47.060Z</wsu:Created>
        <wsu:Expires xmlns:wsu="https://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T06:15:47.060Z</wsu:Expires>
      </t:Lifetime>
      <wsp:AppliesTo xmlns:wsp="http://schemas.xmlsoap.org/ws/2004/09/policy">
        <EndpointReference xmlns="https://www.w3.org/2005/08/addressing">
          <Address>https://contoso.onmicrosoft.com/MyWebApp</Address>
        </EndpointReference>
      </wsp:AppliesTo>
      <t:RequestedSecurityToken>
        <Assertion xmlns="urn:oasis:names:tc:SAML:2.0:assertion" ID="_3ef08993-846b-41de-99df-b7f3ff77671b" IssueInstant="2014-12-24T05:20:47.060Z" Version="2.0">
          <Issuer>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</Issuer>
          <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
            <ds:SignedInfo>
              <ds:CanonicalizationMethod Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#" />
              <ds:SignatureMethod Algorithm="https://www.w3.org/2001/04/xmldsig-more#rsa-sha256" />
              <ds:Reference URI="#_3ef08993-846b-41de-99df-b7f3ff77671b">
                <ds:Transforms>
                  <ds:Transform Algorithm="https://www.w3.org/2000/09/xmldsig#enveloped-signature" />
                  <ds:Transform Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#" />
                </ds:Transforms>
                <ds:DigestMethod Algorithm="https://www.w3.org/2001/04/xmlenc#sha256" />
                <ds:DigestValue>cV1J580U1pD24hEyGuAxrbtgROVyghCqI32UkER/nDY=</ds:DigestValue>
              </ds:Reference>
            </ds:SignedInfo>
            <ds:SignatureValue>j+zPf6mti8Rq4Kyw2NU2nnu0pbJU1z5bR/zDaKaO7FCTdmjUzAvIVfF8pspVR6CbzcYM3HOAmLhuWmBkAAk6qQUBmKsw+XlmF/pB/ivJFdgZSLrtlBs1P/WBV3t04x6fRW4FcIDzh8KhctzJZfS5wGCfYw95er7WJxJi0nU41d7j5HRDidBoXgP755jQu2ZER7wOYZr6ff+ha+/Aj3UMw+8ZtC+WCJC3yyENHDAnp2RfgdElJal68enn668fk8pBDjKDGzbNBO6qBgFPaBT65YvE/tkEmrUxdWkmUKv3y7JWzUYNMD9oUlut93UTyTAIGOs5fvP9ZfK2vNeMVJW7Xg==</ds:SignatureValue>
            <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
              <X509Data>
                <X509Certificate>MIIDPjCCAabcAwIBAgIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTQwMTAxMDcwMDAwWhcNMTYwMTAxMDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAkSCWg6q9iYxvJE2NIhSyOiKvqoWCO2GFipgH0sTSAs5FalHQosk9ZNTztX0ywS/AHsBeQPqYygfYVJL6/EgzVuwRk5txr9e3n1uml94fLyq/AXbwo9yAduf4dCHTP8CWR1dnDR+Qnz/4PYlWVEuuHHONOw/blbfdMjhY+C/BYM2E3pRxbohBb3x//CfueV7ddz2LYiH3wjz0QS/7kjPiNCsXcNyKQEOTkbHFi3mu0u13SQwNddhcynd/GTgWN8A+6SN1r4hzpjFKFLbZnBt77ACSiYx+IHK4Mp+NaVEi5wQtSsjQtI++XsokxRDqYLwus1I1SihgbV/STTg5enufuwIDAQABo2IwYDBeBgNVHQEEVzBVgBDLebM6bK3BjWGqIBrBNFeNoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAA4IBAQCJ4JApryF77EKC4zF5bUaBLQHQ1PNtA1uMDbdNVGKCmSp8M65b8h0NwlIjGGGy/unK8P6jWFdm5IlZ0YPTOgzcRZguXDPj7ajyvlVEQ2K2ICvTYiRQqrOhEhZMSSZsTKXFVwNfW6ADDkN3bvVOVbtpty+nBY5UqnI7xbcoHLZ4wYD251uj5+lo13YLnsVrmQ16NCBYq2nQFNPuNJw6t3XUbwBHXpF46aLT1/eGf/7Xx6iy8yPJX4DyrpFTutDz882RWofGEO5t4Cw+zZg70dJ/hH/ODYRMorfXEW+8uKmXMKmX2wyxMKvfiPbTy5LmAU8Jvjs2tLg4rOBcXWLAIarZ</X509Certificate>
              </X509Data>
            </KeyInfo>
          </ds:Signature>
          <Subject>
            <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">m_H3naDei2LNxUmEcWd0BZlNi_jVET1pMLR6iQSuYmo</NameID>
            <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
          </Subject>
          <Conditions NotBefore="2014-12-24T05:15:47.060Z" NotOnOrAfter="2014-12-24T06:15:47.060Z">
            <AudienceRestriction>
              <Audience>https://contoso.onmicrosoft.com/MyWebApp</Audience>
            </AudienceRestriction>
          </Conditions>
          <AttributeStatement>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
              <AttributeValue>a1addde8-e4f9-4571-ad93-3059e3750d23</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/tenantid">
              <AttributeValue>b9411234-09af-49c2-b0c3-653adc1f376e</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
              <AttributeValue>sample.admin@contoso.onmicrosoft.com</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname">
              <AttributeValue>Admin</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname">
              <AttributeValue>Sample</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">
              <AttributeValue>5581e43f-6096-41d4-8ffa-04e560bab39d</AttributeValue>
              <AttributeValue>07dd8a89-bf6d-4e81-8844-230b77145381</AttributeValue>
              <AttributeValue>0e129f4g-6b0a-4944-982d-f776000632af</AttributeValue>
              <AttributeValue>3ee07328-52ef-4739-a89b-109708c22fb5</AttributeValue>
              <AttributeValue>329k14b3-1851-4b94-947f-9a4dacb595f4</AttributeValue>
              <AttributeValue>6e32c650-9b0a-4491-b429-6c60d2ca9a42</AttributeValue>
              <AttributeValue>f3a169a7-9a58-4e8f-9d47-b70029v07424</AttributeValue>
              <AttributeValue>8e2c86b2-b1ad-476d-9574-544d155aa6ff</AttributeValue>
              <AttributeValue>1bf80264-ff24-4866-b22c-6212e5b9a847</AttributeValue>
              <AttributeValue>4075f9c3-072d-4c32-b542-03e6bc678f3e</AttributeValue>
              <AttributeValue>76f80527-f2cd-46f4-8c52-8jvd8bc749b1</AttributeValue>
              <AttributeValue>0ba31460-44d0-42b5-b90c-47b3fcc48e35</AttributeValue>
              <AttributeValue>edd41703-8652-4948-94a7-2d917bba7667</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/identityprovider">
              <AttributeValue>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</AttributeValue>
            </Attribute>
          </AttributeStatement>
          <AuthnStatement AuthnInstant="2014-12-23T18:51:11.000Z">
            <AuthnContext>
              <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
            </AuthnContext>
          </AuthnStatement>
        </Assertion>
      </t:RequestedSecurityToken>
      <t:RequestedAttachedReference>
        <SecurityTokenReference xmlns="https://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="https://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
          <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
      </t:RequestedAttachedReference>
      <t:RequestedUnattachedReference>
        <SecurityTokenReference xmlns="https://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="https://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
          <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
      </t:RequestedUnattachedReference>
      <t:TokenType>http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0</t:TokenType>
      <t:RequestType>http://schemas.xmlsoap.org/ws/2005/02/trust/Issue</t:RequestType>
      <t:KeyType>http://schemas.xmlsoap.org/ws/2005/05/identity/NoProofKey</t:KeyType>
    </t:RequestSecurityTokenResponse>

## <a name="related-content"></a>Zawartość pokrewna

* Zobacz [zasób zasad](https://docs.microsoft.com/graph/api/resources/policy?view=graph-rest-beta), aby dowiedzieć się więcej na temat zarządzania zasadami okresu istnienia tokenu przy użyciu interfejsu API programu Microsoft Graph.
* Aby uzyskać więcej informacji i przykłady dotyczące zarządzania zasadami za pomocą poleceń cmdlet programu PowerShell, w tym przykładów, zobacz [Konfigurowalne okresy istnienia tokenów w usłudze Azure AD.](../develop/active-directory-configurable-token-lifetimes.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) 
* Dodaj [niestandardowe i opcjonalne oświadczenia](../develop/active-directory-optional-claims.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) do tokenów aplikacji.
* Użyj [logowania jednokrotnego (logowania jednokrotnego) z SAML](single-sign-on-saml-protocol.md).
* Korzystanie z [protokołu SAML wylogowywania pojedynczego platformy Azure](single-sign-out-saml-protocol.md)
