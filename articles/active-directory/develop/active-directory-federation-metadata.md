---
title: Metadanych Federacji usługi Azure AD | Dokumentacja firmy Microsoft
description: W tym artykule opisano dokument metadanych Federacji, który publikuje usługi Azure Active Directory dla usług, które akceptują tokeny usługi Azure Active Directory.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: c2d5f80b-aa74-452c-955b-d8eb3ed62652
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: celested
ms.reviewer: dastrock
ms.custom: aaddev
ms.openlocfilehash: cfc79b451eafe7dcdd0b8f4285f92714138260bb
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="federation-metadata"></a>Metadane federacji
Azure Active Directory (Azure AD) publikuje dokument metadanych usług federacyjnych usługi skonfigurowanego do akceptowania na usługa Azure AD wystawia tokeny zabezpieczające. Format dokumentu metadanych federacji jest opisany w [Web Services Federation Language (WS-Federation) w wersji 1.2](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html), która rozszerza [metadane języka OASIS Security Assertion Markup Language (SAML) 2.0](http://docs.oasis-open.org/security/saml/v2.0/saml-metadata-2.0-os.pdf).

## <a name="tenant-specific-and-tenant-independent-metadata-endpoints"></a>Punkty końcowe metadanych niezależny od dzierżawcy i specyficzne dla dzierżawy
Usługi Azure AD publikuje punktów końcowych specyficznego dla dzierżawy i niezależny od dzierżawcy.

Punkty końcowe specyficznego dla dzierżawy są przeznaczone dla konkretnego dzierżawy. Metadane Federacji specyficznego dla dzierżawy zawierają informacje o dzierżawie, w tym informacje specyficzne dla dzierżawy wystawcy i punktu końcowego. Aplikacje, które ograniczają dostęp do pojedynczej dzierżawy korzystanie z punktów końcowych specyficznego dla dzierżawy.

Punkty końcowe niezależny od dzierżawcy Podaj informacje, które są wspólne dla dzierżaw wszystkie usługi Azure AD. Te informacje dotyczą hostowanej w lokalizacji dzierżawcy *login.microsoftonline.com* i jest współużytkowana przez dzierżawców. Niezależny od dzierżawcy punkty końcowe są zalecane dla wielodostępnych aplikacji, ponieważ nie są one powiązane z konkretnym dzierżawami.

## <a name="federation-metadata-endpoints"></a>Punkty końcowe metadanych Federacji
Usługi Azure AD publikuje metadanych federacji w `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`.

Aby uzyskać **punkty końcowe specyficznego dla dzierżawy**, `TenantDomainName` może być jedną z następujących typów:

* Dzierżawy zarejestrowaną nazwę domeny usługi Azure AD, takich jak: `contoso.onmicrosoft.com`.
* Niezmienne dzierżawy identyfikator domeny, takie jak `72f988bf-86f1-41af-91ab-2d7cd011db45`.

Aby uzyskać **punkty końcowe niezależny od dzierżawcy**, `TenantDomainName` jest `common`. Ten dokument zawiera listę elementów metadanych Federacji, które są wspólne dla wszystkich dzierżaw usługi Azure AD, które są hostowane na login.microsoftonline.com.

Na przykład może być punkt końcowy specyficznego dla dzierżawy `https://login.microsoftonline.com/contoso.onmicrosoft.com/FederationMetadata/2007-06/FederationMetadata.xml`. Punkt końcowy niezależny od dzierżawcy jest [ https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml ](https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml). Dokument metadanych usług federacyjnych można wyświetlić, wpisując adres URL w przeglądarce.

## <a name="contents-of-federation-metadata"></a>Zawartość metadanych Federacji
W poniższej sekcji przedstawiono informacje wymagane przez usługi, które korzystają z tokenów wystawiony przez usługę Azure AD.

### <a name="entity-id"></a>Identyfikator jednostki
`EntityDescriptor` Zawiera element `EntityID` atrybutu. Wartość `EntityID` atrybut reprezentuje wystawcy, oznacza to, że usługa tokenu zabezpieczającego (STS) która wystawiła token. Należy zweryfikować wystawca po odebraniu tokenu.

Następujące metadane Pokazuje przykładowy specyficznego dla dzierżawy `EntityDescriptor` element z `EntityID` elementu.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="_b827a749-cfcb-46b3-ab8b-9f6d14a1294b"
entityID="https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db45/">
```
Identyfikator dzierżawcy w punkcie końcowym niezależne od dzierżawcy można zastąpić swój identyfikator dzierżawy, aby utworzyć konkretny dzierżawy `EntityID` wartość. Wartość wynikową będzie taka sama jak wystawcy tokenów. Strategia umożliwia aplikacji wielodostępnych do sprawdzania poprawności wystawcy dla danej dzierżawy.

Następujące metadane Pokazuje przykładowy niezależny od dzierżawcy `EntityID` elementu. Należy pamiętać, że `{tenant}` jest literałem nie symbol zastępczy.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="="_0e5bd9d0-49ef-4258-bc15-21ce143b61bd"
entityID="https://sts.windows.net/{tenant}/">
```

### <a name="token-signing-certificates"></a>Certyfikaty podpisywania tokenu
Gdy usługa odbiera token wystawiony przez dzierżawę usługi Azure AD, podpisu tokenu musi zostać zweryfikowany przy użyciu klucza podpisywania, publikowana w dokumencie metadanych federacji. Metadane Federacji zawierają publicznej części o certyfikatach używanych przez dzierżawców do podpisywania tokenu. Bajty pierwotne certyfikatu są wyświetlane w `KeyDescriptor` elementu. Certyfikat podpisywania tokenu jest nieprawidłowy na potrzeby podpisywania tylko wtedy, gdy wartość `use` atrybutu `signing`.

Dokument metadanych Federacji, publikowane przez usługę Azure AD może mieć wielu kluczy podpisywania, np. gdy przygotowuje usługi Azure AD można zaktualizować certyfikat podpisywania. Gdy dokument metadanych usług federacyjnych zawiera więcej niż jeden certyfikat, to usługa, która jest sprawdzanie poprawności tokenów powinien obsługiwać wszystkie certyfikaty w dokumencie.

Następujące metadane Pokazuje przykładowy `KeyDescriptor` elementu przy użyciu klucza podpisywania.

```
<KeyDescriptor use="signing">
<KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
<X509Data>
<X509Certificate>
MIIDPjCCAiqgAwIBAgIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTIwNjA3MDcwMDAwWhcNMTQwNjA3MDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEArCz8Sn3GGXmikH2MdTeGY1D711EORX/lVXpr+ecGgqfUWF8MPB07XkYuJ54DAuYT318+2XrzMjOtqkT94VkXmxv6dFGhG8YZ8vNMPd4tdj9c0lpvWQdqXtL1TlFRpD/P6UMEigfN0c9oWDg9U7Ilymgei0UXtf1gtcQbc5sSQU0S4vr9YJp2gLFIGK11Iqg4XSGdcI0QWLLkkC6cBukhVnd6BCYbLjTYy3fNs4DzNdemJlxGl8sLexFytBF6YApvSdus3nFXaMCtBGx16HzkK9ne3lobAwL2o79bP4imEGqg+ibvyNmbrwFGnQrBc1jTF9LyQX9q+louxVfHs6ZiVwIDAQABo2IwYDBeBgNVHQEEVzBVgBCxDDsLd8xkfOLKm4Q/SzjtoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAA4IBAQAkJtxxm/ErgySlNk69+1odTMP8Oy6L0H17z7XGG3w4TqvTUSWaxD4hSFJ0e7mHLQLQD7oV/erACXwSZn2pMoZ89MBDjOMQA+e6QzGB7jmSzPTNmQgMLA8fWCfqPrz6zgH+1F1gNp8hJY57kfeVPBiyjuBmlTEBsBlzolY9dd/55qqfQk6cgSeCbHCy/RU/iep0+UsRMlSgPNNmqhj5gmN2AFVCN96zF694LwuPae5CeR2ZcVknexOWHYjFM0MgUSw0ubnGl0h9AJgGyhvNGcjQqu9vd1xkupFgaN+f7P3p3EVN5csBg5H94jEcQZT7EKeTiZ6bTrpDAnrr8tDCy8ng
</X509Certificate>
</X509Data>
</KeyInfo>
</KeyDescriptor>
  ```

`KeyDescriptor` Element jest wyświetlany w dwóch miejscach w dokumencie metadanych federacji; w sekcji WS federacyjnego określonego i sekcja specyficzne dla języka SAML. Certyfikaty opublikowane w obydwie sekcje będzie taka sama.

W sekcji WS federacyjnego określonego czytnika metadanych WS-Federation czytać certyfikatów z `RoleDescriptor` element z `SecurityTokenServiceType` typu.

Następujące metadane Pokazuje przykładowy `RoleDescriptor` elementu.

```
<RoleDescriptor xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:fed="http://docs.oasis-open.org/wsfed/federation/200706" xsi:type="fed:SecurityTokenServiceType"protocolSupportEnumeration="http://docs.oasis-open.org/wsfed/federation/200706">
```

W sekcji specyficzne dla języka SAML czytnika metadanych WS-Federation czytać certyfikatów z `IDPSSODescriptor` elementu.

Następujące metadane Pokazuje przykładowy `IDPSSODescriptor` elementu.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```
Nie ma żadnych różnic w formacie certyfikaty specyficznego dla dzierżawy i niezależny od dzierżawcy.

### <a name="ws-federation-endpoint-url"></a>Adres URL punktu końcowego usługi WS-Federation
Metadane Federacji zawierają adres URL, który jest używa usługi Azure AD dla jednego logowania i jednym wylogowania w protokole WS-Federation. Ten punkt końcowy jest wyświetlany w `PassiveRequestorEndpoint` elementu.

Następujące metadane Pokazuje przykładowy `PassiveRequestorEndpoint` elementu dla punktu końcowego specyficznego dla dzierżawy.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db45/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```
Dla punktu końcowego niezależny od dzierżawcy adres URL protokołu WS-Federation pojawia się w punktu końcowego usługi WS-Federation, jak pokazano w poniższym przykładzie.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/common/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```

### <a name="saml-protocol-endpoint-url"></a>Adres URL punktu końcowego protokołu SAML
Metadane Federacji zawierają adres URL, który używa usługi Azure AD dla jednego logowania i jednym wylogowania w protokole SAML 2.0. Te punkty końcowe są wyświetlane w `IDPSSODescriptor` elementu.

Adresy URL logowania i wylogowywania są wyświetlane w `SingleSignOnService` i `SingleLogoutService` elementy.

Następujące metadane Pokazuje przykładowy `PassiveResistorEndpoint` dla punktu końcowego specyficznego dla dzierżawy.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com /saml2" />
  </IDPSSODescriptor>
```

Podobnie punktów końcowych dla typowych punktów końcowych protokołu SAML 2.0, które są publikowane w metadanych Federacji niezależny od dzierżawcy, jak pokazano w poniższym przykładzie.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
  </IDPSSODescriptor>
```
