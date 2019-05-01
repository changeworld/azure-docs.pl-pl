---
title: Zdefiniuj profil techniczny SAML w zasadach niestandardowych w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: W przypadku zasad niestandardowych w usłudze Azure Active Directory B2C, należy zdefiniować profil techniczny SAML.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/21/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 4a2e8938aeaf75b3b0237962d16f2702968358f9
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64695578"
---
# <a name="define-a-saml-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Zdefiniuj profil techniczny SAML w zasadach niestandardowych usługi Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Usługa Azure Active Directory (Azure AD) B2C umożliwia dostawcy tożsamości SAML 2.0. W tym artykule opisano szczegóły profilu technicznego do interakcji z dostawcy oświadczeń, który obsługuje ten standardowy protokół. Za pomocą profil techniczny SAML użytkownik może tworzyć federacje ze dostawcy tożsamości opartej na SAML, takich jak [usług AD FS](active-directory-b2c-custom-setup-adfs2016-idp.md) i [Salesforce](active-directory-b2c-setup-sf-app-custom.md). Tej Federacji umożliwia użytkownikom Zaloguj się przy użyciu istniejącej społecznościowych lub tożsamościami w przedsiębiorstwie.

## <a name="metadata-exchange"></a>Wymiany metadanych

Metadane są informacje używane w protokole SAML do udostępnienia konfiguracji strony SAML, takich jak dostawcy usług lub dostawcy tożsamości. Metadane definiuje lokalizacji usług, takich jak logowanie i wylogowanie, certyfikaty, metoda logowania i nie tylko. Dostawca tożsamości używa metadanych wiedzieć, jak nawiązać połączenia z usługą Azure AD B2C. Metadane jest konfigurowana w formacie XML i może być podpisana przy użyciu podpisu cyfrowego, tak aby drugiej strony można zweryfikować integralność metadanych. Usługa Azure AD B2C Federację za pomocą dostawcy tożsamości SAML, działa jako dostawca usług inicjowanie żądanie języka SAML i oczekiwania na odpowiedź SAML. A w niektórych przypadkach akceptuje niechciane uwierzytelnianie SAML, która jest także znana jako dostawcy tożsamości inicjowane. 

Metadane można skonfigurować w obie strony jako "Statyczne metadane" lub "Dynamiczne metadane". W trybie statycznym możesz skopiować cały metadanych z jednej strony i ustaw ją w drugą stronę. W trybie dynamicznym należy ustawić adres URL metadanych, podczas gdy druga strona odczytuje konfigurację dynamicznie. Zasady są takie same, metadane profilu technicznego usługi Azure AD B2C w dostawcy tożsamości oraz ustawić metadanych dostawcy tożsamości w usłudze Azure AD B2C.

Każdy dostawca tożsamości SAML ma innych czynności, aby ujawnić, Ustaw dostawcę usług w tym przypadku usługi Azure AD B2C i ustawić metadanych usługi Azure AD B2C w dostawcy tożsamości. Sprawdź w dokumentacji dostawcy tożsamości w celu uzyskania wskazówek dotyczących sposobu wykonania tego zadania.

Poniższy przykład przedstawia adresu URL metadanych SAML profilu technicznego usługi Azure AD B2C:

```
https://your-tenant-name.b2clogin.com/your-tenant-name/your-policy/samlp/metadata?idptp=your-technical-profile
```

Zastąp następujące wartości:

- **Nazwa Twojej dzierżawy** nazwą dzierżawy, takich jak fabrikam.b2clogin.com.
- **Usługi zasad** nazwą zasady. Użyj zasad, w którym można skonfigurować profil techniczny SAML dostawcy lub zasadę, która dziedziczy tych zasad.
- **Twój profil techniczny** nazwą profilu technicznego dostawcy tożsamości SAML.

## <a name="digital-signing-certificates-exchange"></a>Cyfrowej wymiany certyfikatów podpisywania

Aby utworzyć relację zaufania między usługi Azure AD B2C i dostawcy tożsamości SAML, musisz podać prawidłową X509 certyfikatu z kluczem prywatnym. Możesz przekazać certyfikat przy użyciu klucza prywatnego (pfx) do magazynu kluczy zasad usługi Azure AD B2C. Usługa Azure AD B2C cyfrowo podpisuje żądanie języka SAML logowania przy użyciu certyfikatu, który należy podać. 

Certyfikat jest używany w następujący sposób:

- Usługa Azure AD B2C generuje oraz podpisuje żądanie języka SAML, używając usługi Azure AD B2C klucza prywatnego certyfikatu. Żądanie języka SAML są wysyłane do dostawcy tożsamości, która weryfikuje żądanie przy użyciu usługi Azure AD B2C klucza publicznego certyfikatu. Certyfikat publiczny usługi Azure AD B2C jest dostępna za pośrednictwem profilu technicznego. Alternatywnie możesz ręcznie przekazać plik cer do dostawcy tożsamości SAML.
- Dostawca tożsamości rejestruje dane wysyłane do usługi Azure AD B2C przy użyciu dostawcy tożsamości klucza prywatnego certyfikatu. Usługa Azure AD B2C umożliwia sprawdzenie poprawności danych przy użyciu certyfikatu publicznego dostawcy tożsamości. Każdy dostawca tożsamości składa się z różnych kroków instalacji, sprawdź w dokumentacji dostawcy tożsamości w celu uzyskania wskazówek dotyczących sposobu wykonania tego zadania. W usłudze Azure AD B2C zasady, musi mieć dostęp do klucza publicznego certyfikatu przy użyciu metadanych dostawcy tożsamości.

Certyfikat z podpisem własnym jest dopuszczalne w przypadku większości scenariuszy. W środowiskach produkcyjnych, zaleca się używanie X509 certyfikat wystawiony przez urząd certyfikacji. Ponadto zgodnie z opisem w dalszej części tego dokumentu, w środowisku produkcyjnym nie można wyłączyć podpisywania po obu stronach protokołu SAML.

Na poniższym diagramie przedstawiono wymiany metadanych i certyfikatów:

![Wymiana metadanych i certyfikatów](media/saml-technical-profile/technical-profile-idp-saml-metadata.png)


## <a name="digital-encryption"></a>Cyfrowe szyfrowanie

Aby zaszyfrować potwierdzenia odpowiedzi SAML, dostawca tożsamości zawsze używa klucza publicznego certyfikatu szyfrowania w profilu technicznym usługi Azure AD B2C. Gdy usługi Azure AD B2C należy do odszyfrowania danych używa prywatna część certyfikatu szyfrowania.

Aby zaszyfrować potwierdzenia odpowiedzi SAML:

1. Przekaż prawidłowy X509 certyfikatu przy użyciu klucza prywatnego (pfx) do magazynu kluczy zasad usługi Azure AD B2C.
2. Dodaj **CryptographicKey** elementu z identyfikatorem `SamlAssertionDecryption` do profilu technicznego **CryptographicKeys** kolekcji. Ustaw **identyfikatorze StorageReferenceId** nazwę klucza zasad utworzonego w kroku 1.
3. Ustawianie profilu technicznego **WantsEncryptedAssertions** do `true`.
4. Aktualizowanie dostawcy tożsamości z nowymi metadanymi profil techniczny usługi Azure AD B2C. Powinien zostać wyświetlony **KeyDescriptor** z **użyj** właściwością `encryption` zawierający klucz publiczny certyfikatu.

Poniższy przykład przedstawia sekcję usługi Azure AD B2C profilu technicznego szyfrowania metadanych:

```XML
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```
    
## <a name="protocol"></a>Protokół

**Nazwa** atrybutu elementu protokołu musi być równa `SAML2`. 

## <a name="output-claims"></a>Oświadczeń danych wyjściowych
 
**OutputClaims** element zawiera listę oświadczeń zwrócona przez dostawcę tożsamości SAML, w obszarze `AttributeStatement` sekcji. Może być konieczne mapowania nazwy oświadczenia, zdefiniowane w zasadach do nazwy zdefiniowane w dostawcy tożsamości. Mogą również obejmować oświadczenia, które nie są zwracane przez dostawcę tożsamości, tak długo, jak można ustawić `DefaultValue` atrybutu.
 
Do odczytu dla asercji SAML **NamedId** w **podmiotu** znormalizowane oświadczenia, ustaw oświadczenie **PartnerClaimType** do `assertionSubjectName`. Upewnij się, że **NameId** jest pierwsza wartość w potwierdzenie XML. Podczas definiowania potwierdzenie więcej niż jednej usługi Azure AD B2C wybiera wartości podmiotu, od ostatniego potwierdzenia.
 
**OutputClaimsTransformations** element może zawierać zbiór **OutputClaimsTransformation** elementy, które są używane do modyfikowania oświadczeń danych wyjściowych lub wygenerować nowe.
 
Poniższy przykład przedstawia oświadczenia zwrócona przez dostawcę tożsamości usługi Facebook:

- **IssuerUserId** oświadczeń jest mapowany na **assertionSubjectName** oświadczenia.
- **Imię** oświadczeń jest mapowany na **givenName** oświadczenia.
- **Nazwisko** oświadczeń jest mapowany na **nazwisko** oświadczenia.
- **DisplayName** oświadczenia bez mapowania nazw.
- **E-mail** oświadczenia bez mapowania nazw.
 
Profil techniczny zwraca również wartość oświadczenia, które nie są zwracane przez dostawcę tożsamości: 
 
- **IdentityProvider** oświadczenia, który zawiera nazwę dostawcy tożsamości.
- **AuthenticationSource** oświadczenie z wartością domyślną **socialIdpAuthentication**.
 
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

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| PartnerEntity | Yes | Adres URL metadanych SAML dostawcy tożsamości. Skopiuj metadanych dostawcy tożsamości i dodaj ją w CDATA element `<![CDATA[Your IDP metadata]]>` |
| WantsSignedRequests | Nie | Wskazuje, czy profil techniczny wymaga wszystkie wychodzące żądania uwierzytelniania były podpisane. Możliwe wartości: `true` lub `false`. Wartość domyślna to `true`. Jeśli wartość jest równa `true`, **SamlMessageSigning** klucza kryptograficznego musi być określona, a wszystkie żądania uwierzytelniania wychodzące są podpisane. Jeśli wartość jest równa `false`, **SigAlg** i **podpisu** parametrów (ciąg zapytania lub parametr post) zostały pominięte w żądaniu. Te metadane kontroluje również metadanych **AuthnRequestsSigned** atrybut, który jest wysyłany w metadanych profil techniczny usługi Azure AD B2C, który jest udostępniany za pomocą dostawcy tożsamości. Usługa Azure AD B2C nie podpisania żądania, jeśli wartość **WantsSignedRequests** w profilu technicznym metadanych jest równa `false` metadanych dostawcy tożsamości i **WantAuthnRequestsSigned** jest Ustaw `false` lub nie jest określony. |
| XmlSignatureAlgorithm | Nie | Metoda, która korzysta z usługi Azure AD B2C do podpisania żądania języka SAML. Te metadane określa wartość **SigAlg** parametru (ciąg zapytania lub parametr post) żądania języka SAML. Możliwe wartości: `Sha256`, `Sha384`, `Sha512`, lub `Sha1`. Upewnij się, że konfigurowanie algorytm podpisu po obu stronach za pomocą tej samej wartości. Użyj tylko certyfikat obsługuje algorytm. | 
| WantsSignedAssertions | Nie | Wskazuje, czy profil techniczny wymaga wszystkie potwierdzenia przychodzące były podpisane. Możliwe wartości: `true` lub `false`. Wartość domyślna to `true`. Jeśli wartość jest równa `true`, wszystkich sekcji potwierdzenia `saml:Assertion` wysyłane przez tożsamość musi być podpisany dostawcy usługi Azure AD B2C. Jeśli wartość jest równa `false`, dostawca tożsamości nie należy zarejestrować potwierdzenia, ale nawet wtedy, gdy istnieje, usługa Azure AD B2C nie weryfikuje podpisu. Te metadane kontroluje również flagi metadanych **WantsAssertionsSigned**, czyli danych wyjściowych w metadanych profil techniczny usługi Azure AD B2C, który jest udostępniany za pomocą dostawcy tożsamości. Wyłączenie sprawdzania poprawności potwierdzenia również warto wyłączyć sprawdzanie poprawności podpisu odpowiedzi (Aby uzyskać więcej informacji, zobacz **ResponsesSigned**). |
| ResponsesSigned | Nie | Możliwe wartości: `true` lub `false`. Wartość domyślna to `true`. Jeśli wartość jest równa `false`, dostawca tożsamości nie należy zarejestrować odpowiedzi SAML, ale nawet wtedy, gdy istnieje, usługa Azure AD B2C nie weryfikuje podpisu. Jeśli wartość jest równa `true`, odpowiedzi SAML przesłanej przez dostawcę tożsamości do usługi Azure AD B2C jest podpisany i muszą być weryfikowane. Jeśli wyłączysz weryfikacji odpowiedzi SAML, również może chcesz wyłączyć potwierdzenia weryfikacji podpisu (Aby uzyskać więcej informacji, zobacz **WantsSignedAssertions**). |
| WantsEncryptedAssertions | Nie | Wskazuje, czy profil techniczny wymaga wszystkich potwierdzeń przychodzących do zaszyfrowania. Możliwe wartości: `true` lub `false`. Wartość domyślna to `false`. Jeśli wartość jest równa `true`, muszą być podpisane potwierdzenia wysyłane przez dostawcę tożsamości do usługi Azure AD B2C i **SamlAssertionDecryption** klucza kryptograficznego musi być określona. Jeśli wartość jest równa `true`, zawiera metadane profilu technicznego usługi Azure AD B2C **szyfrowania** sekcji. Dostawca tożsamości czyta metadane i szyfruje potwierdzenia odpowiedzi SAML przy użyciu klucza publicznego, który znajduje się w metadanych profil techniczny usługi Azure AD B2C. Jeśli włączysz szyfrowanie potwierdzeń, trzeba będzie również może wyłączyć sprawdzanie poprawności podpisu odpowiedzi (Aby uzyskać więcej informacji, zobacz **ResponsesSigned**). | 
| IdpInitiatedProfileEnabled | Nie | Wskazuje, czy włączono profil sesji rejestracji jednokrotnej, zainicjowanego przez profil dostawcy tożsamości SAML. Możliwe wartości: `true` lub `false`. Wartość domyślna to `false`. W usłudze flow inicjowane przez dostawcę tożsamości użytkownik jest uwierzytelniany zewnętrznie i niechciane odpowiedź jest wysyłana do usługi Azure AD B2C, a następnie używa token, wykonuje kroki aranżacji, która wysyła następnie odpowiedź do aplikacji jednostki uzależnionej. |
| NameIdPolicyFormat | Nie | Określa ograniczenia na identyfikator nazwy, która ma być używany do reprezentowania żądanego tematu. W przypadku pominięcia używać dowolnego typu Identyfikator obsługiwany przez dostawcę tożsamości dla żądanego tematu. Przykład: `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`. **NameIdPolicyFormat** mogą być używane z **NameIdPolicyAllowCreate**. Sprawdź dokumentację dostawcy tożsamości dla wskazówek, o których nazwa zasady ID są obsługiwane. |
| NameIdPolicyAllowCreate | Nie | Korzystając z **NameIdPolicyFormat**, można również określić `AllowCreate` właściwość **NameIDPolicy**. Wartość metadanych jest `true` lub `false` do wskazania, czy dostawca tożsamości może utworzyć nowe konto podczas przepływu logowania. Sprawdź w dokumentacji dostawcy tożsamości w celu uzyskania wskazówek dotyczących sposobu wykonania tego zadania. |
| AuthenticationRequestExtensions | Nie | Opcjonalne protokołu komunikatu rozszerzenia elementy, które uzgodnią w usłudze Azure AD BC i dostawcy tożsamości. Rozszerzenia są prezentowane w formacie XML. Możesz dodać dane XML w elemencie CDATA `<![CDATA[Your IDP metadata]]>`. Sprawdź dokumentację dostawcy tożsamości, aby dowiedzieć się, jeśli element rozszerzenia jest obsługiwany. |
| IncludeAuthnContextClassReferences | Nie | Określa jedno lub więcej odwołań identyfikatora URI identyfikujący klasy kontekst uwierzytelniania. Na przykład, aby zezwolić użytkownikowi logowanie za pomocą nazwy użytkownika i hasło tylko, ustaw wartość `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`. Aby umożliwić logowania za pomocą nazwy użytkownika i hasła, za pośrednictwem chronionego sesji (SSL/TLS), należy określić `PasswordProtectedTransport`. Spójrz na dokumentację dostawcy tożsamości dla wskazówek **AuthnContextClassRef** identyfikatory URI, które są obsługiwane. |
| IncludeKeyInfo | Nie | Wskazuje, czy żądanie uwierzytelniania SAML powiązania ma wartość, zawiera klucz publiczny certyfikatu `HTTP-POST`. Możliwe wartości: `true` lub `false`. |

## <a name="cryptographic-keys"></a>Klucze kryptograficzne

**CryptographicKeys** element zawiera następujące atrybuty:

| Atrybut |Wymagane | Opis |
| --------- | ----------- | ----------- |
| SamlMessageSigning |Yes | X509 certyfikat (zestawu kluczy RSA) ma być używany do podpisywania wiadomości protokołu SAML. Usługa Azure AD B2C używa tego klucza do podpisania żądania i wysyłać je do dostawcy tożsamości. |
| SamlAssertionDecryption |Yes | X509 certyfikat (zestawu kluczy RSA) ma być używany do odszyfrowywania wiadomości protokołu SAML. Ten certyfikat należy podać przez dostawcę tożsamości. Usługa Azure AD B2C używa tego certyfikatu do odszyfrowania danych wysłanych przez dostawcę tożsamości. |
| MetadataSigning |Nie | X509 certyfikatu (zestawu kluczy RSA) używany do podpisywania metadanych SAML. Usługa Azure AD B2C używa tego klucza do podpisania metadanych.  |

## <a name="examples"></a>Przykłady

- [Dodawanie usług AD FS jako dostawcy tożsamości SAML, za pomocą zasad niestandardowych](active-directory-b2c-custom-setup-adfs2016-idp.md)
- [Zaloguj się przy użyciu konta usługi Salesforce za pośrednictwem protokołu SAML](active-directory-b2c-setup-sf-app-custom.md)













