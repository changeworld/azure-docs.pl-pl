---
title: Definiowanie profilu technicznego SAML w zasadach niestandardowych
titleSuffix: Azure AD B2C
description: Zdefiniuj profil techniczny SAML w zasadach niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/13/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8c81d2bc499c3d9cae262ef62be2dac2d7280be7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183843"
---
# <a name="define-a-saml-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiowanie profilu technicznego SAML w zasadach niestandardowych usługi Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Usługa Azure Active Directory B2C (Azure AD B2C) zapewnia obsługę dostawcy tożsamości SAML 2.0. W tym artykule opisano specyfikę profilu technicznego do interakcji z dostawcą oświadczeń, który obsługuje ten znormalizowany protokół. Dzięki profilowi technicznemu SAML można łączyć się z dostawcą tożsamości opartym na SAML, takim jak [ADFS](identity-provider-adfs2016-custom.md) i [Salesforce.](identity-provider-salesforce-custom.md) Ta federacja umożliwia użytkownikom logowanie się przy przy pomocy istniejących tożsamości społecznościowych lub korporacyjnych.

## <a name="metadata-exchange"></a>Wymiana metadanych

Metadane to informacje używane w protokole SAML do udostępnienia konfiguracji strony SAML, takiej jak dostawca usług lub dostawca tożsamości. Metadane definiują lokalizację usług, takich jak logowanie i wylogowywanie, certyfikaty, metoda logowania i inne. Dostawca tożsamości używa metadanych, aby wiedzieć, jak komunikować się z usługą Azure AD B2C. Metadane są skonfigurowane w formacie XML i mogą być podpisane za pomocą podpisu cyfrowego, dzięki czemu druga strona może sprawdzić integralność metadanych. Gdy usługa Azure AD B2C federates z dostawcą tożsamości SAML, działa jako dostawca usług inicjując żądanie SAML i oczekiwanie na odpowiedź SAML. I, w niektórych przypadkach, akceptuje niechciane uwierzytelnianie SAML, który jest również znany jako dostawca tożsamości zainicjowane.

Metadane można skonfigurować w obu stronach jako "Metadane statyczne" lub "Metadane dynamiczne". W trybie statycznym można skopiować całe metadane z jednej strony i ustawić go w drugiej stronie. W trybie dynamicznym adres URL jest ustawiany na metadane, podczas gdy druga strona dynamicznie odczytuje konfigurację. Zasady są takie same, można ustawić metadane profilu technicznego usługi Azure AD B2C w dostawcy tożsamości i ustawić metadane dostawcy tożsamości w usłudze Azure AD B2C.

Każdy dostawca tożsamości SAML ma różne kroki, aby udostępnić i ustawić dostawcę usług, w tym przypadku usługi Azure AD B2C i ustawić metadane usługi Azure AD B2C w dostawcy tożsamości. Zapoznaj się z dokumentacją dostawcy tożsamości, aby uzyskać wskazówki, jak to zrobić.

W poniższym przykładzie pokazano adres URL do metadanych SAML profilu technicznego usługi Azure AD B2C:

```
https://your-tenant-name.b2clogin.com/your-tenant-name/your-policy/samlp/metadata?idptp=your-technical-profile
```

Zastąp następujące wartości:

- **nazwa dzierżawy** z nazwą dzierżawy, na przykład fabrikam.b2clogin.com.
- **z** nazwą zasad. Użyj zasad, w których skonfigurowano profil techniczny dostawcy SAML lub zasad, które dziedziczą z tej zasady.
- **twój profil techniczny** z nazwą profilu technicznego dostawcy tożsamości SAML.

## <a name="digital-signing-certificates-exchange"></a>Wymiana certyfikatów podpisywania cyfrowego

Aby utworzyć zaufanie między usługą Azure AD B2C a dostawcą tożsamości SAML, musisz podać prawidłowy certyfikat X509 z kluczem prywatnym. Przekaż certyfikat z kluczem prywatnym (plik pfx) do magazynu kluczy zasad usługi Azure AD B2C. Usługa Azure AD B2C cyfrowo podpisuje żądanie logowania SAML przy użyciu certyfikatu, który podasz.

Certyfikat jest używany w następujący sposób:

- Usługa Azure AD B2C generuje i podpisuje żądanie SAML przy użyciu klucza prywatnego usługi Azure AD B2C certyfikatu. Żądanie SAML jest wysyłane do dostawcy tożsamości, który sprawdza poprawność żądania przy użyciu klucza publicznego usługi Azure AD B2C certyfikatu. Certyfikat publiczny usługi Azure AD B2C jest dostępny za pośrednictwem metadanych profilu technicznego. Alternatywnie można ręcznie przekazać plik cer do dostawcy tożsamości SAML.
- Dostawca tożsamości podpisuje dane wysyłane do usługi Azure AD B2C przy użyciu klucza prywatnego dostawcy tożsamości certyfikatu. Usługa Azure AD B2C sprawdza poprawność danych przy użyciu certyfikatu publicznego dostawcy tożsamości. Każdy dostawca tożsamości ma różne kroki do instalacji, zapoznaj się z dokumentacją dostawcy tożsamości, aby uzyskać wskazówki, jak to zrobić. W usłudze Azure AD B2C zasady muszą mieć dostęp do klucza publicznego certyfikatu przy użyciu metadanych dostawcy tożsamości.

Certyfikat z podpisem własnym jest dopuszczalne dla większości scenariuszy. W środowiskach produkcyjnych zaleca się użycie certyfikatu X509 wystawionego przez urząd certyfikacji. Ponadto, jak opisano w dalszej części tego dokumentu, dla środowiska nieprodukcyjnego można wyłączyć podpisywanie SAML po obu stronach.

Na poniższym diagramie przedstawiono metadane i wymianę certyfikatów:

![metadanych i wymiana certyfikatów](media/saml-technical-profile/technical-profile-idp-saml-metadata.png)

## <a name="digital-encryption"></a>Szyfrowanie cyfrowe

Aby zaszyfrować asercja odpowiedzi SAML, dostawca tożsamości zawsze używa klucza publicznego certyfikatu szyfrowania w profilu technicznym usługi Azure AD B2C. Gdy usługa Azure AD B2C musi odszyfrować dane, używa prywatnej części certyfikatu szyfrowania.

Aby zaszyfrować asercja odpowiedzi SAML:

1. Przekaż prawidłowy certyfikat X509 z kluczem prywatnym (plik pfx) do magazynu kluczy zasad usługi Azure AD B2C.
2. Dodaj **cryptographicKey** element z identyfikatorem `SamlAssertionDecryption` do profilu technicznego **CryptographicKeys** kolekcji. Ustaw **StorageReferenceId** na nazwę klucza zasad utworzonego w kroku 1.
3. Ustaw metadane profilu technicznego **WantsEncryptedAssertions** na `true`.
4. Zaktualizuj dostawcę tożsamości za pomocą nowych metadanych profilu technicznego usługi Azure AD B2C. Powinien zostać wyświetlony **KeyDescriptor** z **use** właściwości zestaw zawierający `encryption` klucz publiczny certyfikatu.

W poniższym przykładzie przedstawiono sekcję szyfrowania profilu technicznego usługi Azure AD B2C metadanych:

```XML
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

## <a name="protocol"></a>Protocol (Protokół)

Atrybut **Nazwa** elementu Protokołu musi być ustawiony `SAML2`na .

## <a name="output-claims"></a>Oświadczenia wyjściowe

**OutputClaims** element zawiera listę oświadczeń zwróconych przez dostawcę `AttributeStatement` tożsamości SAML w sekcji. Może być konieczne mapowanie nazwy oświadczenia zdefiniowanego w zasadach na nazwę zdefiniowaną w dostawcy tożsamości. Można również dołączyć oświadczenia, które nie są zwracane przez `DefaultValue` dostawcę tożsamości, tak długo, jak ustawić atrybut.

Aby odczytać twierdzenie SAML **NamedId** w **temacie** jako znormalizowane oświadczenie, `assertionSubjectName`ustaw oświadczenie **PartnerClaimType** na . Upewnij się, że **NameId** jest pierwszą wartością w xml potwierdzenia. Podczas definiowania więcej niż jednego potwierdzenia usługi Azure AD B2C wybiera wartość tematu z ostatniego potwierdzenia.

**OutputClaimsTransformations** element może zawierać kolekcję **OutputClaimsTransformation** elementów, które są używane do modyfikowania oświadczeń danych wyjściowych lub generowania nowych.

W poniższym przykładzie przedstawiono oświadczenia zwrócone przez dostawcę tożsamości Facebooka:

- Oświadczenie **identyfikatora issuerUserId** jest mapowane na oświadczenie **assertionSubjectName.**
- Oświadczenie **first_name** jest mapowane na **oświadczenie givenName.**
- Oświadczenie **last_name** jest odwzorowane na oświadczenie o **nazwisku.**
- Oświadczenie **displayName** bez mapowania nazw.
- Oświadczenie **e-mail** bez mapowania nazw.

Profil techniczny zwraca również oświadczenia, które nie są zwracane przez dostawcę tożsamości:

- **Oświadczenie identityProvider,** który zawiera nazwę dostawcy tożsamości.
- Oświadczenie **authenticationSource** z domyślną wartością **socialIdpAuthentication**.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="assertionSubjectName" />
  <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
  <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email"  />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>Metadane

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| PartnerEntity (PartnerEntity) | Tak | Adres URL metadanych dostawcy tożsamości SAML. Kopiowanie metadanych dostawcy tożsamości i dodawanie ich wewnątrz elementu CDATA`<![CDATA[Your IDP metadata]]>` |
| WantsSignedRequests | Nie | Wskazuje, czy profil techniczny wymaga podpisania wszystkich wychodzących żądań uwierzytelniania. Możliwe `true` wartości: `false`lub . Wartością domyślną jest `true`. Gdy wartość jest `true`ustawiona na , klucz kryptograficzny **SamlMessageSigning** musi zostać określony i wszystkie wychodzące żądania uwierzytelniania są podpisane. Jeśli wartość jest `false`ustawiona na , parametry **SigAlg** i **Signature** (ciąg zapytania lub parametr post) są pomijane w żądaniu. Te metadane sterują również atrybutem **metadanych AuthnRequestsSigned,** który jest odtwarzany w metadanych profilu technicznego usługi Azure AD B2C, który jest udostępniany dostawcy tożsamości. Usługa Azure AD B2C nie podpisuje żądania, jeśli wartość **WantsSignedRequests** w `false` metadanych profilu technicznego jest ustawiona i metadane dostawcy tożsamości **WantAuthnRequestsSigned** jest ustawiona `false` lub nie określono. |
| XmlSignatureAlgorithm | Nie | Metoda używana przez usługę Azure AD B2C do podpisania żądania SAML. Te metadane sterują wartością parametru **SigAlg** (ciąg zapytania lub parametr post) w żądaniu SAML. Możliwe `Sha256`wartości: `Sha384` `Sha512`, `Sha1`, , lub . Upewnij się, że algorytm podpisu po obu stronach ma tę samą wartość. Użyj tylko algorytmu obsługiwanego przez certyfikat. |
| WantsSignedAssertions (chęć 100 000) | Nie | Wskazuje, czy profil techniczny wymaga podpisania wszystkich potwierdzeń przychodzących. Możliwe `true` wartości: `false`lub . Wartością domyślną jest `true`. Jeśli wartość jest `true`ustawiona na `saml:Assertion` , wszystkie potwierdzenia sekcji wysyłane przez dostawcę tożsamości do usługi Azure AD B2C muszą być podpisane. Jeśli wartość jest `false`ustawiona na , dostawca tożsamości nie należy podpisywać potwierdzeń, ale nawet jeśli tak, usługi Azure AD B2C nie będzie sprawdzać poprawności podpisu. Te metadane steruje również flagą metadanych **WantsAssertionsSigned**, która jest wyprowadzana w metadanych profilu technicznego usługi Azure AD B2C, który jest udostępniany dostawcy tożsamości. Jeśli wyłączysz sprawdzanie poprawności potwierdzeń, możesz również wyłączyć sprawdzanie poprawności podpisu odpowiedzi (aby uzyskać więcej informacji, zobacz **ResponsesSigned**). |
| OdpowiedziPodpisane | Nie | Możliwe `true` wartości: `false`lub . Wartością domyślną jest `true`. Jeśli wartość jest `false`ustawiona na , dostawca tożsamości nie należy podpisać odpowiedzi SAML, ale nawet jeśli tak, usługi Azure AD B2C nie będzie sprawdzać poprawności podpisu. Jeśli wartość jest `true`ustawiona na , odpowiedź SAML wysłana przez dostawcę tożsamości do usługi Azure AD B2C jest podpisana i musi zostać zweryfikowana. Jeśli wyłączysz sprawdzanie poprawności odpowiedzi SAML, można również wyłączyć sprawdzanie poprawności podpisu asercji (aby uzyskać więcej informacji, zobacz **WantsSignedAssertions**). |
| WantsEncryptedAssertions | Nie | Wskazuje, czy profil techniczny wymaga szyfrowania wszystkich potwierdzeń przychodzących. Możliwe `true` wartości: `false`lub . Wartością domyślną jest `false`. Jeśli wartość jest `true`ustawiona na , potwierdzenia wysyłane przez dostawcę tożsamości do usługi Azure AD B2C muszą być podpisane i Klucz kryptograficzny **SamlAssertionDecryption** musi być określony. Jeśli wartość jest `true`ustawiona na , metadane profilu technicznego usługi Azure AD B2C zawiera sekcję **szyfrowania.** Dostawca tożsamości odczytuje metadane i szyfruje asercja odpowiedzi SAML za pomocą klucza publicznego, który znajduje się w metadanych profilu technicznego usługi Azure AD B2C. Jeśli włączysz szyfrowanie potwierdzeń, może być również konieczne wyłączenie sprawdzania poprawności podpisu odpowiedzi (aby uzyskać więcej informacji, zobacz **ResponsesSigned**). |
| IdpInitiatedProfileEnabled | Nie | Wskazuje, czy profil sesji logowania jednokrotnego jest włączony, który został zainicjowany przez profil dostawcy tożsamości SAML. Możliwe `true` wartości: `false`lub . Wartość domyślna to `false`. W przepływie zainicjowanym przez dostawcę tożsamości użytkownik jest uwierzytelniany zewnętrznie, a niezamówiona odpowiedź jest wysyłana do usługi Azure AD B2C, która następnie zużywa token, wykonuje kroki aranżacji, a następnie wysyła odpowiedź do aplikacji jednostki uzależnionej. |
| NazwaIDPolicyFormat | Nie | Określa ograniczenia identyfikatora nazwy, który ma być używany do reprezentowania żądanego tematu. W przypadku pominięcia można użyć dowolnego typu identyfikatora obsługiwanego przez dostawcę tożsamości dla żądanego tematu. Przykład: `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`. **NameIdPolicyFormat** może być używany z **NameIdPolicyAllowCreate**. Zapoznaj się z dokumentacją dostawcy tożsamości, aby uzyskać wskazówki dotyczące obsługiwanych zasad identyfikatorów nazw. |
| NazwaIdPolicyAllowCreate | Nie | Korzystając z **NameIdPolicyFormat,** można `AllowCreate` również określić właściwość **NameIDPolicy**. Wartość tych metadanych `true` `false` jest lub wskazać, czy dostawca tożsamości może utworzyć nowe konto podczas przepływu logowania. Zapoznaj się z dokumentacją dostawcy tożsamości, aby uzyskać wskazówki, jak to zrobić. |
| UwierzytelnianieRequestExtensions | Nie | Elementy rozszerzenia komunikatu opcjonalnego protokołu, które są uzgadniane między usługą Azure AD BC a dostawcą tożsamości. Rozszerzenie jest prezentowane w formacie XML. Dane XML można dodać wewnątrz `<![CDATA[Your IDP metadata]]>`elementu CDATA . Sprawdź dokumentację dostawcy tożsamości, aby sprawdzić, czy element rozszerzeń jest obsługiwany. |
| Dołącz Konferencje Klasy KontekstuUAuthna | Nie | Określa co najmniej jedno odwołanie do identyfikatora URI identyfikujące klasy kontekstu uwierzytelniania. Na przykład, aby zezwolić użytkownikowi na logowanie się tylko `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`przy użyciu nazwy użytkownika i hasła, ustaw wartość na . Aby zezwolić na logowanie się za pośrednictwem nazwy użytkownika i hasła `PasswordProtectedTransport`w ramach chronionej sesji (SSL/TLS), należy określić . Zapoznaj się z dokumentacją dostawcy tożsamości, aby uzyskać wskazówki dotyczące obsługiwanych identyfikatorów **URI AuthnContextClassRef.** Określ wiele identyfikatorów URI jako listę rozdzielanych przecinkami. |
| IncludeKeyInfo (IncludeKeyInfo) | Nie | Wskazuje, czy żądanie uwierzytelniania SAML zawiera klucz publiczny certyfikatu, gdy powiązanie jest ustawione na `HTTP-POST`. Możliwe `true` wartości: `false`lub . |
| IncludeClaimResolvingInClaimsHandling  | Nie | W przypadku oświadczeń wejściowych i wyjściowych określa, czy [rozpoznawanie oświadczeń](claim-resolver-overview.md) jest uwzględnione w profilu technicznym. Możliwe wartości: `true` `false`  , lub (domyślnie). Jeśli chcesz użyć programu rozpoznawania oświadczeń w profilu technicznym, ustaw to na `true`. |

## <a name="cryptographic-keys"></a>Klucze kryptograficzne

**CryptographicKey element** zawiera następujące atrybuty:

| Atrybut |Wymagany | Opis |
| --------- | ----------- | ----------- |
| SamlMessageSigning (Podpis samlMessage) |Tak | Certyfikat X509 (zestaw kluczy RSA) używany do podpisywania komunikatów SAML. Usługa Azure AD B2C używa tego klucza do podpisywania żądań i wysyłania ich do dostawcy tożsamości. |
| SamlAssertionDecryption (Szyfrowanie) |Tak | Certyfikat X509 (zestaw kluczy RSA) używany do odszyfrowywania komunikatów SAML. Ten certyfikat powinien być dostarczony przez dostawcę tożsamości. Usługa Azure AD B2C używa tego certyfikatu do odszyfrowania danych wysyłanych przez dostawcę tożsamości. |
| Podpisywanie metadanych |Nie | Certyfikat X509 (zestaw kluczy RSA) używany do podpisywania metadanych SAML. Usługa Azure AD B2C używa tego klucza do podpisywania metadanych.  |

## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły, aby zapoznać się z przykładami pracy z dostawcami tożsamości SAML w usłudze Azure AD B2C:

- [Dodawanie usługi ADFS jako dostawcy tożsamości SAML przy użyciu zasad niestandardowych](identity-provider-adfs2016-custom.md)
- [Zaloguj się przy użyciu kont Salesforce za pośrednictwem SAML](identity-provider-salesforce-custom.md)
