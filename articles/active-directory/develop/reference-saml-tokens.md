---
title: Dowiedz się więcej o inny token, a typy obsługiwane przez usługę Azure AD | Dokumentacja firmy Microsoft
description: Przewodnik dla opis i ocena oświadczenia w tokeny SAML 2.0 i tokenów Web JSON (JWT), wystawiony przez usługę Azure Active Directory (AAD)
documentationcenter: na
author: CelesteDG
services: active-directory
manager: mtillman
editor: ''
ms.assetid: 166aa18e-1746-4c5e-b382-68338af921e2
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/22/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58a8d3b62fab7614375436846888b78113740ce2
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406614"
---
# <a name="azure-ad-saml-token-reference"></a>Dokumentacja programu Azure AD SAML tokenu

Azure Active Directory (Azure AD) emituje kilka rodzajów tokenów zabezpieczających w czasie przetwarzania każdego przepływu uwierzytelniania. W tym dokumencie opisano format, zabezpieczenia właściwości i zawartość każdego typu tokenu.

## <a name="claims-in-saml-tokens"></a>Oświadczenia w tokeny SAML

> [!div class="mx-codeBreakAll"]
> | Name (Nazwa) | Equivalent JWT Claim | Opis | Przykład |
> | --- | --- | --- | ------------|
> |Odbiorcy | `aud` |Adresat tokenu. Aplikację, która odbiera token musi sprawdzić, czy wartość odbiorców jest poprawny i odrzucić wszystkie tokeny przeznaczone dla różnych odbiorców. | `<AudienceRestriction>`<br>`<Audience>`<br>`https://contoso.com`<br>`</Audience>`<br>`</AudienceRestriction>`  |
> | Błyskawiczne uwierzytelnianie | |Rejestruje datę i godzinę wystąpienia uwierzytelniania. | `<AuthnStatement AuthnInstant="2011-12-29T05:35:22.000Z">` | 
> |Metoda uwierzytelniania | `amr` |Określa, jak został uwierzytelniony podmiot tokenu. | `<AuthnContextClassRef>`<br>`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password`<br>`</AuthnContextClassRef>` |
> |Imię | `given_name` |Zawiera pierwszy lub "" Nazwa użytkownika, według stawki ustalonej w obiekcie użytkownika usługi Azure AD. | `<Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname">`<br>`<AttributeValue>Frank<AttributeValue>`  |
> |Grupy | `groups` |Zawiera identyfikatory obiektów, reprezentujących podmiotu członkostw w grupie. Te wartości są unikatowe (patrz obiektu o identyfikatorze) i może być bezpiecznie stosowany w celu zarządzania dostępem, takie jak wymuszanie autoryzacji dostępu do zasobu. Grupy uwzględniane w oświadczenie grupy są skonfigurowane na podstawie poszczególnych aplikacji, za pomocą właściwości "groupMembershipClaims" manifestu aplikacji. Wartość null powoduje wyłączenie wszystkich grup, wartość "SecurityGroup" będzie zawierać tylko członkostwa grupy zabezpieczeń usługi Active Directory, a wartość "All" będzie zawierać grupy zabezpieczeń i listy dystrybucyjne usługi Office 365. <br><br> **Informacje o**: <br> Awaria liczbę grup, których użytkownik ma przekracza limit (150 dla protokołu SAML, 200 dla tokenów JWT), a następnie użycie nadwyżkowe zostanie dodane oświadczenie źródeł oświadczenia, wskazujący na punkt końcowy programu Graph, zawierający listę grup dla użytkownika. (w. | `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">`<br>`<AttributeValue>07dd8a60-bf6d-4e17-8844-230b77145381</AttributeValue>` |
> | Wskaźnik użycia nadwyżkowego grup | `groups:src1` | Dla żądań tokenów, które nie są ograniczone długość (zobacz `hasgroups` powyżej), ale nadal zbyt duży dla tokenu, łącze do listy grup pełny dla użytkownika zostaną dołączone. Dla języka SAML jest dodawany jako nowe oświadczenie zamiast `groups` oświadczenia. | `<Attribute Name=" http://schemas.microsoft.com/claims/groups.link">`<br>`<AttributeValue>https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects<AttributeValue>` |
> |Dostawca tożsamości | `idp` |Rejestruje dostawcę tożsamości, który uwierzytelnił podmiot tokenu. Ta wartość jest taka sama jak wartość oświadczenia wystawcy, chyba że konto użytkownika jest w innej dzierżawie niż wystawcy. | `<Attribute Name=" http://schemas.microsoft.com/identity/claims/identityprovider">`<br>`<AttributeValue>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/<AttributeValue>` |
> |IssuedAt | `iat` |Przechowuje czas, w którym został wystawiony token. Często służy do mierzenia świeżości tokenu. | `<Assertion ID="_d5ec7a9b-8d8f-4b44-8c94-9812612142be" IssueInstant="2014-01-06T20:20:23.085Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">` |
> |Wystawca | `iss` |Określa usługę tokenu zabezpieczającego (STS), który tworzy i zwraca token. W tokenach, które zwraca usługi Azure AD wystawca jest sts.windows.net. Identyfikator GUID w wartości wystawcy oświadczeń jest identyfikator dzierżawy katalogu usługi Azure AD. Identyfikator dzierżawy jest niezmienny i niezawodne identyfikator katalogu. | `<Issuer>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/</Issuer>` |
> |Nazwisko | `family_name` |Zawiera ostatni nazwę, nazwisko lub nazwę rodziny użytkownika, zgodnie z definicją w obiekcie użytkownika usługi Azure AD. | `<Attribute Name=" http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname">`<br>`<AttributeValue>Miller<AttributeValue>` |
> |Name (Nazwa) | `unique_name` |Udostępnia zrozumiałą wartość identyfikującą podmiot tokenu. Ta wartość nie musi być unikatowa w ramach dzierżawy i jest przeznaczony do użycia tylko w celach wyświetlania. | `<Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">`<br>`<AttributeValue>frankm@contoso.com<AttributeValue>`|
> |Identyfikator obiektu | `oid` |Zawiera unikatowy identyfikator obiektu w usłudze Azure AD. Ta wartość jest niezmienny i nie może być ponownie przypisywany ani ponownie. Użyj Identyfikatora obiektu, aby zidentyfikować obiekt w zapytaniach do usługi Azure AD. | `<Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">`<br>`<AttributeValue>528b2ac2-aa9c-45e1-88d4-959b53bc7dd0<AttributeValue>` |
> |Role | `roles` |Reprezentuje wszystkie role aplikacji, które temat ma przyznane bezpośrednio i pośrednio za pośrednictwem członkostwa w grupie i może służyć do wymuszania kontroli dostępu opartej na rolach. Role aplikacji są definiowane na podstawie poszczególnych aplikacji przy użyciu `appRoles` właściwości manifestu aplikacji. `value` Właściwości poszczególnych ról aplikacji jest wartość, która jest wyświetlana w oświadczenia ról. | `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/role">`|
> |Subject | `sub` |Identyfikuje jednostkę o tym, które token określa informacje, takie jak użytkownik aplikacji. Ta wartość jest niemodyfikowalna i nie może zostać przypisany, lub ponownie, dlatego może służyć do sprawdzania autoryzacji bezpiecznie. Ponieważ temat zawsze jest obecny w tokenach problemy dotyczące usługi Azure AD, zalecane jest korzystanie z tej wartości w systemie autoryzacji ogólnego przeznaczenia. <br> `SubjectConfirmation` nie jest oświadczenia. Opisuje sposób przedmiotem token jest weryfikowany. `Bearer` Wskazuje, że temat potwierdza zapoznały tokenu. | `<Subject>`<br>`<NameID>S40rgb3XjhFTv6EQTETkEzcgVmToHKRkZUIsJlmLdVc</NameID>`<br>`<SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />`<br>`</Subject>`|
> |Identyfikator dzierżawy | `tid` |Identyfikator niezmienne, jednorazowego, który identyfikuje dzierżawy katalogu, który wystawił token. Ta wartość umożliwia dostęp do zasobów katalogu specyficznym dla dzierżawy w aplikacji z wieloma dzierżawami. Na przykład można użyć tej wartości do identyfikowania dzierżawy w wywołaniu interfejsu API programu Graph. | `<Attribute Name="http://schemas.microsoft.com/identity/claims/tenantid">`<br>`<AttributeValue>cbb1a5ac-f33b-45fa-9bf5-f37db0fed422<AttributeValue>`|
> |Czas życia tokenu | `nbf`, `exp` |Definiuje przedział czasu, przez który token jest prawidłowy. Usługa, która sprawdza poprawność tokenu należy sprawdzić, że bieżąca data mieści się okres istnienia tokenu, else powinien on odrzucenie tokena. Usługa może pozwalać na pięć minut poza zakresem okres istnienia tokenu na wszelkie różnice w zegarze ("niesymetryczność czasu") między usługą Azure AD i usługi. | `<Conditions`<br>`NotBefore="2013-03-18T21:32:51.261Z"`<br>`NotOnOrAfter="2013-03-18T22:32:51.261Z"`<br>`>` <br>|

## <a name="sample-saml-token"></a>Przykładowe tokenu SAML

To jest przykład typowego tokenu języka SAML.

    <?xml version="1.0" encoding="UTF-8"?>
    <t:RequestSecurityTokenResponse xmlns:t="https://schemas.xmlsoap.org/ws/2005/02/trust">
      <t:Lifetime>
        <wsu:Created xmlns:wsu="https://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T05:15:47.060Z</wsu:Created>
        <wsu:Expires xmlns:wsu="https://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T06:15:47.060Z</wsu:Expires>
      </t:Lifetime>
      <wsp:AppliesTo xmlns:wsp="https://schemas.xmlsoap.org/ws/2004/09/policy">
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
      <t:RequestType>https://schemas.xmlsoap.org/ws/2005/02/trust/Issue</t:RequestType>
      <t:KeyType>http://schemas.xmlsoap.org/ws/2005/05/identity/NoProofKey</t:KeyType>
    </t:RequestSecurityTokenResponse>

## <a name="related-content"></a>Powiązana zawartość
* Zobacz program Azure AD Graph [operacje dotyczące zasad](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) i [jednostka zasad](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#policy-entity), aby dowiedzieć się więcej o zarządzaniu zasady okres istnienia tokenu za pośrednictwem interfejsu API programu Graph usługi Azure AD.
* Aby uzyskać więcej informacji i przykłady dotyczące zarządzania zasad za pomocą poleceń cmdlet programu PowerShell, w tym przykłady, zobacz [okresów istnienia tokenu można skonfigurować w usłudze Azure AD](active-directory-configurable-token-lifetimes.md). 
* Dodaj [oświadczeń niestandardowych i opcjonalnie](active-directory-optional-claims.md) do tokenów dla aplikacji.
* Użyj [jednokrotne logowanie jednokrotne (SSO) z SAML](single-sign-on-saml-protocol.md).
* Użyj [Azure logowanie się protokołu SAML](single-sign-out-saml-protocol.md)
