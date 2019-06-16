---
title: Metadane Federacji usługi Azure AD | Dokumentacja firmy Microsoft
description: W tym artykule opisano dokumentu metadanych Federacji usługi Azure Active Directory publikującym dla usług, które akceptują tokenów usługi Azure Active Directory.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: c2d5f80b-aa74-452c-955b-d8eb3ed62652
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: ryanwi
ms.reviewer: hirsin, dastrock
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32f105c0d4f8807b53d400a1c198edd504c0aef3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65544501"
---
# <a name="federation-metadata"></a>Metadane federacji
Azure Active Directory (Azure AD) publikuje Federacji dokumentu metadanych usług, który jest skonfigurowany do akceptowania tokenów zabezpieczających wystawianych przez urząd usługi Azure AD. Format dokumentu metadanych federacji jest opisany w [Web Services Federation Language (WS-Federation) w wersji 1.2](https://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html), która rozszerza [metadane języka OASIS Security Assertion Markup języka (SAML) 2.0](https://docs.oasis-open.org/security/saml/v2.0/saml-metadata-2.0-os.pdf).

## <a name="tenant-specific-and-tenant-independent-metadata-endpoints"></a>Specyficznym dla dzierżawy i punkty końcowe metadanych niezależne od dzierżawcy
Usługa Azure AD publikuje specyficznym dla dzierżawy i dzierżawy niezależne od punktów końcowych.

Punkty końcowe specyficznym dla dzierżawy są przeznaczone do określonej dzierżawy. Metadane Federacji specyficznym dla dzierżawy zawiera informacje o dzierżawie, w tym informacje specyficzne dla dzierżawy wystawcy i punktu końcowego. Aplikacje, które ograniczają dostęp do pojedynczej dzierżawy korzystanie z punktów końcowych specyficznym dla dzierżawy.

Punkty końcowe niezależne od dzierżawy zapewniają informacje, które są wspólne dla dzierżaw wszystkie usługi Azure AD. Te informacje dotyczą dzierżaw hostowanych na *login.microsoftonline.com* i jest współużytkowany przez dzierżawców. Niezależne od dzierżawcy punkty końcowe są zalecane dla wielodostępnych aplikacji, ponieważ nie są one powiązane z dzierżawami określonego.

## <a name="federation-metadata-endpoints"></a>Punkty końcowe metadanych Federacji
Usługa Azure AD publikuje metadanych federacji w `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`.

Aby uzyskać **punktów końcowych specyficznym dla dzierżawy**, `TenantDomainName` może być jedną z następujących typów:

* Dzierżawy zarejestrowaną nazwę domeny usługi Azure AD, takie jak: `contoso.onmicrosoft.com`.
* Niezmienne dzierżawy identyfikator domeny, takich jak `72f988bf-86f1-41af-91ab-2d7cd011db45`.

Aby uzyskać **punktów końcowych niezależne od dzierżawcy**, `TenantDomainName` jest `common`. Ten dokument zawiera listę elementów metadanych Federacji, które są wspólne dla wszystkich dzierżaw usługi Azure AD, które są hostowane na login.microsoftonline.com.

Na przykład może być punktem końcowym specyficznym dla dzierżawy `https://login.microsoftonline.com/contoso.onmicrosoft.com/FederationMetadata/2007-06/FederationMetadata.xml`. Punkt końcowy niezależne od dzierżawcy jest [ https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml ](https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml). Możesz wyświetlić dokument metadanych Federacji, wpisując ten adres URL w przeglądarce.

## <a name="contents-of-federation-metadata"></a>Zawartość, metadane Federacji
W poniższej sekcji przedstawiono informacje wymagane przez usługi, które korzystają z tokenów, wystawiony przez usługę Azure AD.

### <a name="entity-id"></a>Identyfikator jednostki
`EntityDescriptor` Element zawiera `EntityID` atrybutu. Wartość `EntityID` atrybut reprezentuje wystawcy, oznacza to, że usługa tokenu zabezpieczającego (STS), który wystawił token. Należy zweryfikować wystawcy, po otrzymaniu tokenu.

Następujące metadane pokazuje próbkę specyficznym dla dzierżawy `EntityDescriptor` element z `EntityID` elementu.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="_b827a749-cfcb-46b3-ab8b-9f6d14a1294b"
entityID="https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db45/">
```
Identyfikator dzierżawy w punkcie końcowym niezależne od dzierżawcy można zastąpić swój identyfikator dzierżawy, aby utworzyć specyficznym dla dzierżawy `EntityID` wartość. Wartość wynikowa będzie taka sama jak wystawcy tokenów. Strategia ta umożliwia aplikacji z wieloma dzierżawami sprawdzić poprawność wystawcy dla danej dzierżawy.

Następujące metadane pokazuje próbkę niezależne od dzierżawcy `EntityID` elementu. Należy pamiętać, że `{tenant}` jest literałem, nie symbol zastępczy.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="="_0e5bd9d0-49ef-4258-bc15-21ce143b61bd"
entityID="https://sts.windows.net/{tenant}/">
```

### <a name="token-signing-certificates"></a>Certyfikaty podpisywania tokenu
Gdy usługa odbiera token wystawiony przez dzierżawę usługi Azure AD, podpis tokenu można sprawdzić poprawności, przy użyciu klucza podpisywania, który został opublikowany w dokumentu metadanych federacji. Metadane Federacji zawierają części publicznej certyfikatach używanych przez dzierżawców do podpisywania tokenu. Bajtów raw certyfikatu są wyświetlane w `KeyDescriptor` elementu. Certyfikat podpisywania tokenu jest prawidłowy do podpisania tylko wtedy, gdy wartość `use` atrybut jest `signing`.

Dokument metadanych Federacji opublikowana przez usługę Azure AD może mieć wiele kluczy podpisywania, np. gdy usługa Azure AD jest przygotowywana do aktualizacji certyfikatu podpisywania. Gdy dokument metadanych Federacji zawiera więcej niż jeden certyfikat, to usługa, która weryfikuje tokeny powinien obsługiwać wszystkie certyfikaty w dokumencie.

Następujące metadane pokazuje próbkę `KeyDescriptor` elementu przy użyciu klucza podpisywania.

```
<KeyDescriptor use="signing">
<KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
<X509Data>
<X509Certificate>
MIIDPjCCAiqgAwIBAgIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTIwNjA3MDcwMDAwWhcNMTQwNjA3MDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEArCz8Sn3GGXmikH2MdTeGY1D711EORX/lVXpr+ecGgqfUWF8MPB07XkYuJ54DAuYT318+2XrzMjOtqkT94VkXmxv6dFGhG8YZ8vNMPd4tdj9c0lpvWQdqXtL1TlFRpD/P6UMEigfN0c9oWDg9U7Ilymgei0UXtf1gtcQbc5sSQU0S4vr9YJp2gLFIGK11Iqg4XSGdcI0QWLLkkC6cBukhVnd6BCYbLjTYy3fNs4DzNdemJlxGl8sLexFytBF6YApvSdus3nFXaMCtBGx16HzkK9ne3lobAwL2o79bP4imEGqg+ibvyNmbrwFGnQrBc1jTF9LyQX9q+louxVfHs6ZiVwIDAQABo2IwYDBeBgNVHQEEVzBVgBCxDDsLd8xkfOLKm4Q/SzjtoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAA4IBAQAkJtxxm/ErgySlNk69+1odTMP8Oy6L0H17z7XGG3w4TqvTUSWaxD4hSFJ0e7mHLQLQD7oV/erACXwSZn2pMoZ89MBDjOMQA+e6QzGB7jmSzPTNmQgMLA8fWCfqPrz6zgH+1F1gNp8hJY57kfeVPBiyjuBmlTEBsBlzolY9dd/55qqfQk6cgSeCbHCy/RU/iep0+UsRMlSgPNNmqhj5gmN2AFVCN96zF694LwuPae5CeR2ZcVknexOWHYjFM0MgUSw0ubnGl0h9AJgGyhvNGcjQqu9vd1xkupFgaN+f7P3p3EVN5csBg5H94jEcQZT7EKeTiZ6bTrpDAnrr8tDCy8ng
</X509Certificate>
</X509Data>
</KeyInfo>
</KeyDescriptor>
  ```

`KeyDescriptor` Element pojawia się w dwóch miejscach w dokumentu metadanych Federacji, w sekcji specyficzne dla języka SAML i WS-Federation określonych sekcji. Certyfikaty, opublikowane w obu sekcjach będą takie same.

W sekcji usługi WS-Federation określonego czytnika metadanych WS-Federation wczytane certyfikatów z `RoleDescriptor` element z `SecurityTokenServiceType` typu.

Następujące metadane pokazuje próbkę `RoleDescriptor` elementu.

```
<RoleDescriptor xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:fed="https://docs.oasis-open.org/wsfed/federation/200706" xsi:type="fed:SecurityTokenServiceType"protocolSupportEnumeration="https://docs.oasis-open.org/wsfed/federation/200706">
```

W sekcji specyficzne dla języka SAML czytnik metadanych WS-Federation wczytane certyfikatów z `IDPSSODescriptor` elementu.

Następujące metadane pokazuje próbkę `IDPSSODescriptor` elementu.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```
Nie ma żadnych różnic, w formacie specyficznym dla dzierżawy i dzierżawy niezależny certyfikatów.

### <a name="ws-federation-endpoint-url"></a>Adres URL punktu końcowego protokołu WS-Federation
Metadane Federacji zawierają adres URL, który jest używa usługi Azure AD na potrzeby logowania jednokrotnego i jednym wylogowywanie w protokole WS-Federation. Ten punkt końcowy jest wyświetlany w `PassiveRequestorEndpoint` elementu.

Następujące metadane pokazuje próbkę `PassiveRequestorEndpoint` elementu dla punktu końcowego specyficznym dla dzierżawy.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="https://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db45/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```
Dla punktu końcowego niezależne od dzierżawcy adres URL protokołu WS-Federation pojawia się w punkcie końcowym protokołu WS-Federation, jak pokazano w następującym przykładzie.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="https://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/common/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```

### <a name="saml-protocol-endpoint-url"></a>Adres URL punktu końcowego protokołu SAML
Metadane Federacji zawierają adres URL, do którego usługa Azure AD używa do logowania jednokrotnego i jednym wylogowywanie w protokole SAML 2.0. Te punkty końcowe są wyświetlane w `IDPSSODescriptor` elementu.

Zaloguj się i wylogowania adresy URL są wyświetlane w `SingleSignOnService` i `SingleLogoutService` elementów.

Następujące metadane pokazuje próbkę `PassiveResistorEndpoint` dla punktu końcowego specyficznym dla dzierżawy.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com /saml2" />
  </IDPSSODescriptor>
```

Podobnie punkty końcowe dla wspólnych punktów końcowych protokołu SAML 2.0 są publikowane w metadanych Federacji dzierżawy niezależny, jak pokazano w następującym przykładzie.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
  </IDPSSODescriptor>
```
