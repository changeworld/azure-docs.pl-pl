---
title: Metadane federacji usługi Azure AD | Dokumenty firmy Microsoft
description: W tym artykule opisano dokument metadanych federacji publikowany przez usługę Azure Active Directory dla usług akceptujących tokeny usługi Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 01/07/2017
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: bcc44f61ccb7b4a19e7df39ab979669c5aa37da1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154903"
---
# <a name="federation-metadata"></a>Metadane federacji

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Usługa Azure Active Directory (Azure AD) publikuje dokument metadanych federacji dla usług skonfigurowanych do akceptowania tokenów zabezpieczających, które są wystawiane przez usługę Azure AD. Format dokumentu metadanych federacji jest opisany w [wersji 1.2 w języku federacji usług sieci Web (Federacja WS),](https://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html)który rozszerza [metadane języka OASIS Security Assertion Markup Language (SAML) w wersji 2.0](https://docs.oasis-open.org/security/saml/v2.0/saml-metadata-2.0-os.pdf).

## <a name="tenant-specific-and-tenant-independent-metadata-endpoints"></a>Punkty końcowe metadanych specyficznych dla dzierżawy i niezależnych od dzierżawy
Usługa Azure AD publikuje punkty końcowe specyficzne dla dzierżawy i niezależne od dzierżawy.

Punkty końcowe specyficzne dla dzierżawy są przeznaczone dla określonej dzierżawy. Metadane federacji specyficzne dla dzierżawy zawierają informacje o dzierżawie, w tym informacje o wystawcy specyficznym dla dzierżawy i o punktach końcowych. Aplikacje, które ograniczają dostęp do pojedynczej dzierżawy używać punktów końcowych specyficznych dla dzierżawy.

Punkty końcowe niezależne od dzierżawy zawierają informacje, które są wspólne dla wszystkich dzierżaw usługi Azure AD. Te informacje dotyczą dzierżaw hostowanych w *login.microsoftonline.com* i są udostępniane przez dzierżawców. Punkty końcowe niezależne od dzierżawy są zalecane dla aplikacji z wieloma dzierżawcami, ponieważ nie są skojarzone z żadną konkretną dzierżawą.

## <a name="federation-metadata-endpoints"></a>Punkty końcowe metadanych federacji
Usługa Azure AD publikuje `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`metadane federacji na poziomie .

Dla **punktów końcowych specyficznych dla dzierżawy** `TenantDomainName` może być jednym z następujących typów:

* Zarejestrowana nazwa domeny dzierżawy usługi Azure `contoso.onmicrosoft.com`AD, na przykład: .
* Niezmienny identyfikator dzierżawy domeny, na `72f988bf-86f1-41af-91ab-2d7cd011db45`przykład .

W przypadku **niezależnych od dzierżawy punktów końcowych** `TenantDomainName` jest `common`. Ten dokument zawiera listę tylko elementy metadanych federacji, które są wspólne dla wszystkich dzierżaw usługi Azure AD, które są hostowane w login.microsoftonline.com.

Na przykład punkt końcowy specyficzny dla `https://login.microsoftonline.com/contoso.onmicrosoft.com/FederationMetadata/2007-06/FederationMetadata.xml`dzierżawy może być . Punktem końcowym niezależnym [https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml)od dzierżawy jest . Dokument metadanych federacji można wyświetlić, wpisując ten adres URL w przeglądarce.

## <a name="contents-of-federation-metadata"></a>Zawartość metadanych federacji
Poniższa sekcja zawiera informacje wymagane przez usługi, które korzystają z tokenów wystawionych przez usługę Azure AD.

### <a name="entity-id"></a>Identyfikator jednostki
Element `EntityDescriptor` zawiera `EntityID` atrybut. Wartość atrybutu `EntityID` reprezentuje wystawcę, czyli usługę tokenu zabezpieczającego (STS), która wystawiła token. Ważne jest, aby sprawdzić poprawność wystawcy po otrzymaniu tokenu.

Następujące metadane pokazuje przykładowy `EntityDescriptor` element `EntityID` specyficzne dla dzierżawy z elementem.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="_b827a749-cfcb-46b3-ab8b-9f6d14a1294b"
entityID="https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db45/">
```
Można zastąpić identyfikator dzierżawy w punkcie końcowym niezależny od dzierżawy identyfikatorem `EntityID` dzierżawy, aby utworzyć wartość specyficzną dla dzierżawy. Wynikowa wartość będzie taka sama jak wystawca tokenu. Strategia umożliwia aplikacji wielodostępnych do sprawdzania poprawności wystawcy dla danej dzierżawy.

Następujące metadane pokazuje przykładowy `EntityID` element niezależny od dzierżawy. Należy pamiętać, `{tenant}` że jest to literał, a nie symbol zastępczy.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="="_0e5bd9d0-49ef-4258-bc15-21ce143b61bd"
entityID="https://sts.windows.net/{tenant}/">
```

### <a name="token-signing-certificates"></a>Certyfikaty podpisywania tokenów
Gdy usługa odbiera token, który jest wystawiany przez dzierżawcę usługi Azure AD, podpis tokenu musi zostać zweryfikowany przy pomocą klucza podpisywania, który jest publikowany w dokumencie metadanych federacji. Metadane federacji zawiera publiczną część certyfikatów, które dzierżawcy używają do podpisywania tokenu. W elemencie pojawią się nieprzetworzone bajty certyfikatu. `KeyDescriptor` Certyfikat podpisywania tokenu jest ważny do `use` podpisywania tylko wtedy, gdy wartość atrybutu jest `signing`.

Dokument metadanych federacji opublikowany przez usługę Azure AD może mieć wiele kluczy podpisywania, na przykład gdy usługa Azure AD przygotowuje się do aktualizacji certyfikatu podpisywania. Gdy dokument metadanych federacji zawiera więcej niż jeden certyfikat, usługa, która sprawdza tokeny, powinna obsługiwać wszystkie certyfikaty w dokumencie.

Następujące metadane przedstawia `KeyDescriptor` przykładowy element z kluczem podpisywania.

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

Element `KeyDescriptor` pojawia się w dwóch miejscach w dokumencie metadanych federacji; w sekcji specyficzne dla federacji WS i sekcji specyficzne dla SAML. Certyfikaty opublikowane w obu sekcjach będą takie same.

W sekcji specyficzne dla federacji WS czytnik metadanych Federacji WS będzie odczytywać certyfikaty z `RoleDescriptor` elementu o typie. `SecurityTokenServiceType`

Następujące metadane pokazuje `RoleDescriptor` przykładowy element.

```
<RoleDescriptor xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:fed="https://docs.oasis-open.org/wsfed/federation/200706" xsi:type="fed:SecurityTokenServiceType"protocolSupportEnumeration="https://docs.oasis-open.org/wsfed/federation/200706">
```

W sekcji specyficzne dla SAML czytnik metadanych Federacji WS `IDPSSODescriptor` będzie odczytywać certyfikaty z elementu.

Następujące metadane pokazuje `IDPSSODescriptor` przykładowy element.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```
Nie ma różnic w formacie certyfikatów specyficznych dla dzierżawy i niezależnych od dzierżawy.

### <a name="ws-federation-endpoint-url"></a>Adres URL punktu końcowego federacji WS
Metadane federacji zawiera adres URL, który jest azure ad używa do logowania jednokrotnego i pojedynczego wylogowania w protokole federacji WS. Ten punkt końcowy `PassiveRequestorEndpoint` pojawia się w elemencie.

Następujące metadane przedstawia `PassiveRequestorEndpoint` przykładowy element dla punktu końcowego specyficzne dla dzierżawy.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="https://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db45/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```
Dla punktu końcowego niezależnego od dzierżawy adres URL federacji WS jest wyświetlany w punkcie końcowym federacji WS, jak pokazano w poniższym przykładzie.

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
Metadane federacji zawiera adres URL, który usługa Azure AD używa do logowania jednokrotnego i pojedynczego wylogowania w protokole SAML 2.0. Te punkty końcowe `IDPSSODescriptor` pojawiają się w elemencie.

Adresy URL logowania i wylogowywania są wyświetlane w `SingleSignOnService` elementach i elementach. `SingleLogoutService`

Następujące metadane przedstawia `PassiveResistorEndpoint` przykład dla punktu końcowego specyficzne dla dzierżawy.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com /saml2" />
  </IDPSSODescriptor>
```

Podobnie punkty końcowe dla wspólnych punktów końcowych protokołu SAML 2.0 są publikowane w metadanych federacji niezależnych od dzierżawy, jak pokazano w poniższym przykładzie.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
  </IDPSSODescriptor>
```
