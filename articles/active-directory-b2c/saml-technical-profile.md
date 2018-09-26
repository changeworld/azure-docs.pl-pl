---
title: Zdefiniuj profil techniczny SAML w zasadach niestandardowych w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: W przypadku zasad niestandardowych w usłudze Azure Active Directory B2C, należy zdefiniować profil techniczny SAML.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 301ae251413cc174f115479e9ebef2310aa83ba7
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47162446"
---
# <a name="define-a-saml-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Zdefiniuj profil techniczny SAML w zasadach niestandardowych usługi Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Usługa Azure Active Directory (Azure AD) B2C umożliwia dostawcy tożsamości SAML 2.0. W tym artykule opisano szczegóły profilu technicznego do interakcji z dostawcy oświadczeń, który obsługuje ten standardowy protokół. Z języka SAML profilu technicznego, który może tworzyć federacje z SAML opartą na dostawcy tożsamości, takich jak usługi AD FS i Salesforce, umożliwiając użytkownikom Zaloguj się przy użyciu istniejącej społecznościowych i tożsamościami w przedsiębiorstwie.

## <a name="metadata-exchange"></a>Wymiany metadanych

Metadane są informacje używane w protokole SAML do udostępnienia konfiguracji strony SAML, takich jak dostawcy usług lub dostawcy tożsamości. Metadane definiuje lokalizacji usług, takich jak logowanie i wylogowanie, certyfikaty, metoda logowania i nie tylko. Dostawca tożsamości używa metadanych wiedzieć, jak nawiązać połączenia z usługą Azure AD B2C. Metadane jest konfigurowana w formacie XML i może być podpisana przy użyciu podpisu cyfrowego, tak aby drugiej strony można zweryfikować integralność metadanych. Usługa Azure AD B2C Federację za pomocą dostawcy tożsamości SAML, działa jako dostawca usług inicjowanie żądanie języka SAML i oczekiwania na odpowiedź SAML. A w niektórych przypadkach, z wyjątkiem niechciane uwierzytelnianie SAML, która jest także znana jako dostawcy tożsamości inicjowane. 

Metadane można skonfigurować w obie strony jako "Statyczne metadane" lub "Dynamiczne metadane". W trybie statycznym możesz skopiować cały metadanych z jednej strony i ustaw ją w drugą stronę. W trybie dynamicznym należy ustawić adres URL metadanych, podczas gdy druga strona odczytuje konfigurację dynamicznie. Zasady są takie same, metadane profilu technicznego usługi Azure AD B2C w dostawcy tożsamości oraz ustawić metadanych dostawcy tożsamości w usłudze Azure AD B2C.

Każdy dostawca tożsamości SAML ma innych czynności, aby ujawnić, Ustaw dostawcę usług w tym przypadku usługi Azure AD B2C i ustawić metadanych usługi Azure AD B2C w dostawcy tożsamości. Sprawdź w dokumentacji dostawcy tożsamości w celu uzyskania wskazówek dotyczących sposobu wykonania tego zadania.

Poniższy przykład przedstawia adresu URL metadanych SAML profilu technicznego usługi Azure AD B2C:

```
https://login.microsoftonline.com/te/your-tenant/your-policy/samlp/metadata?idptp=your-technical-profile
```

Zastąp następujące wartości:

- **Twojej dzierżawy** nazwą dzierżawy, takich jak usługi tenant.onmicrosoft.com
- **Usługi zasad** nazwą zasady. Użyj zasad, w którym można skonfigurować profil techniczny SAML dostawcy lub zasadę, która dziedziczy tych zasad.
- **Twój profil techniczny** nazwą profilu technicznego dostawcy tożsamości SAML

## <a name="digital-signing-certificates-exchange"></a>Cyfrowej wymiany certyfikatów podpisywania

Aby utworzyć relację zaufania między usługi Azure AD B2C i dostawcy tożsamości SAML, musisz podać prawidłową X509 certyfikatu z kluczem prywatnym. Możesz przekazać certyfikat przy użyciu klucza prywatnego (pfx) do magazynu kluczy zasad usługi Azure AD B2C. Usługa Azure AD B2C cyfrowo podpisuje żądanie języka SAML logowania przy użyciu certyfikatu, który należy podać. 

Certyfikat jest używany w następujący sposób:

- Usługa Azure AD B2C generuje oraz podpisuje żądanie języka SAML, używając usługi Azure AD B2C klucza prywatnego certyfikatu. Żądanie języka SAML są wysyłane do dostawcy tożsamości, która weryfikuje żądanie przy użyciu usługi Azure AD B2C klucza publicznego certyfikatu. Certyfikat publiczny usługi Azure AD B2C jest dostępna za pośrednictwem profilu technicznego. Alternatywnie możesz ręcznie przekazać plik cer do dostawcy tożsamości SAML.
- Dostawca tożsamości rejestruje dane wysyłane do usługi Azure AD B2C przy użyciu dostawcy tożsamości klucza prywatnego certyfikatu. Usługa Azure AD B2C umożliwia sprawdzenie poprawności danych przy użyciu certyfikatu publicznego dostawcy tożsamości. Każdy dostawca tożsamości składa się z różnych kroków instalacji, sprawdź w dokumentacji swoich dostawców tożsamości w celu uzyskania wskazówek dotyczących sposobu wykonania tego zadania. W usłudze Azure AD B2C zasady, musi mieć dostęp do klucza publicznego certyfikatu przy użyciu metadanych dostawcy tożsamości.

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
  <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

## <a name="identity-provider-initiated-flow"></a>Przepływ zainicjował dostawcy tożsamości

W ramach jednej logowania jednokrotnego sesji (niechciane żądania) inicjowane przez dostawcę tożsamości niezamówionych odpowiedzi SAML są wysyłane do dostawcy usług w tym przypadku profil techniczny usługi Azure AD B2C. W tym przepływie użytkownika nie przechodzi przez aplikację sieci web najpierw, ale są kierowane do dostawcy tożsamości. Po wysłaniu żądania na stronie uwierzytelniania jest udostępniany użytkownikowi przez dostawcę tożsamości. Użytkownik kończy logowania, a następnie żądanie jest przekierowywane do usługi Azure AD B2C przy użyciu odpowiedzi SAML, która zawiera potwierdzenia. Usługa Azure AD B2C odczytuje potwierdzenia i generuje nowy token SAML i następnie przekierowuje użytkownika z powrotem do aplikacji jednostki uzależnionej. Przekierowań są wykonywane tylko przez **AssertionConsumerService** elementu **lokalizacji** właściwości.


![Dostawcy tożsamości SAML zainicjowane](media/saml-technical-profile/technical-profile-idp-saml-idp-initiated.png) 

W przypadku tworzenia dostawcy tożsamości inicjowania usługi flow, należy wziąć pod uwagę następujące wymagania dotyczące zasad:

- Pierwszy krok aranżacji musi być pojedynczym oświadczeń programu exchange, który wskazuje na profil techniczny SAML.
- Profil techniczny musi mieć metadanych z elementu o nazwie **IdpInitiatedProfileEnabled** równa `true`.
- Jednostki uzależnionej zasad firmy musi być uzależnionej SAML.
- Jednostki uzależnionej zasad firmy musi mieć metadanych z elementu o nazwie **IdpInitiatedProfileEnabled** równa `true`.
- Niechciane odpowiedzi musi być wysyłane do `/your-tenant/your-policy/samlp/sso/assertionconsumer` punktu końcowego. Stan przekazywania dołączone do odpowiedzi są przekazywane na jednostkę uzależnioną. Zastąp następujące wartości: **Twojej dzierżawy** nazwą dzierżawy. **Usługi zasad** nazwą jednostki uzależnionej zasady innych firm.
    
## <a name="protocol"></a>Protokół

**Nazwa** atrybutu elementu protokołu musi być równa `SAML2`. 

## <a name="metadata"></a>Metadane

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| PartnerEntity | Yes | Adres URL metadanych SAML dostawcy tożsamości. Skopiuj metadanych dostawcy tożsamości i dodaj ją w CDATA element `<![CDATA[Your IDP metadata]]>` |
| WantsSignedRequests | Nie | Wskazuje, czy profil techniczny wymaga wszystkie wychodzące żądania uwierzytelniania były podpisane. Możliwe wartości: `true` lub `false`. Wartość domyślna to `true`. Jeśli wartość jest równa `true`, **SamlMessageSigning** klucza kryptograficznego musi być określona, a wszystkie żądania uwierzytelniania wychodzące są podpisane. Jeśli wartość jest równa `false`, **SigAlg** i **podpisu** parametrów (ciąg zapytania lub parametr post) zostały pominięte w żądaniu. Te metadane kontroluje również metadanych **AuthnRequestsSigned** atrybut, który jest wysyłany w metadanych profil techniczny usługi Azure AD B2C, który jest udostępniany za pomocą dostawcy tożsamości. |
| XmlSignatureAlgorithm | Nie | Metoda, która korzysta z usługi Azure AD B2C do podpisania żądania języka SAML. Te metadane określa wartość **SigAlg** parametru (ciąg zapytania lub parametr post) żądania języka SAML. Możliwe wartości: `Sha256`, `Sha384`, `Sha512`, lub `Sha1`. Upewnij się, że konfigurowanie algorytm podpisu po obu stronach za pomocą tej samej wartości. Użyj tylko certyfikat obsługuje algorytm. | 
| WantsSignedAssertions | Nie | Wskazuje, czy profil techniczny wymaga wszystkie potwierdzenia przychodzące były podpisane. Możliwe wartości: `true` lub `false`. Wartość domyślna to `true`. Jeśli wartość jest równa `true`, wszystkich sekcji potwierdzenia `saml:Assertion` wysyłane przez tożsamość musi być podpisany dostawcy usługi Azure AD B2C. Jeśli wartość jest równa `false`, dostawca tożsamości nie należy zarejestrować potwierdzenia, ale nawet wtedy, gdy istnieje, usługa Azure AD B2C nie weryfikuje podpisu. Te metadane kontroluje również flagi metadanych **WantsAssertionsSigned**, czyli danych wyjściowych w metadanych profil techniczny usługi Azure AD B2C, który jest udostępniany za pomocą dostawcy tożsamości. Wyłączenie sprawdzania poprawności potwierdzenia również warto wyłączyć sprawdzanie poprawności podpisu odpowiedzi (Aby uzyskać więcej informacji, zobacz **ResponsesSigned**). |
| ResponsesSigned | Nie | Możliwe wartości: `true` lub `false`. Wartość domyślna to `true`. Jeśli wartość jest równa `false`, dostawca tożsamości nie należy zarejestrować odpowiedzi SAML, ale nawet wtedy, gdy istnieje, usługa Azure AD B2C nie weryfikuje podpisu. Jeśli wartość jest równa `true`, odpowiedzi SAML przesłanej przez dostawcę tożsamości do usługi Azure AD B2C jest podpisany i muszą być weryfikowane. Jeśli wyłączysz weryfikacji odpowiedzi SAML, również może chcesz wyłączyć potwierdzenia weryfikacji podpisu (Aby uzyskać więcej informacji, zobacz **WantsSignedAssertions**). |
| WantsEncryptedAssertions | Nie | Wskazuje, czy profil techniczny wymaga wszystkich potwierdzeń przychodzących do zaszyfrowania. Możliwe wartości: `true` lub `false`. Wartość domyślna to `false`. Jeśli wartość jest równa `true`, muszą być podpisane potwierdzenia wysyłane przez dostawcę tożsamości do usługi Azure AD B2C i **SamlAssertionDecryption** klucza kryptograficznego musi być określona. Jeśli wartość jest równa `true`, zawiera metadane profilu technicznego usługi Azure AD B2C **szyfrowania** sekcji. Dostawca tożsamości czyta metadane i szyfruje potwierdzenia odpowiedzi SAML przy użyciu klucza publicznego, który znajduje się w metadanych profil techniczny usługi Azure AD B2C. Jeśli włączysz szyfrowanie potwierdzeń, trzeba będzie również może wyłączyć sprawdzanie poprawności podpisu odpowiedzi (Aby uzyskać więcej informacji, zobacz **ResponsesSigned**). | 
| IdpInitiatedProfileEnabled | Nie |Wskazuje, czy włączono profil sesji rejestracji jednokrotnej, zainicjowanego przez profil dostawcy tożsamości SAML. Możliwe wartości: `true` lub `false`. Wartość domyślna to `false`. W usłudze flow inicjowane przez dostawcę tożsamości użytkownik jest uwierzytelniany zewnętrznie i niechciane odpowiedź jest wysyłana do usługi Azure AD B2C, a następnie używa token, wykonuje kroki aranżacji, która wysyła następnie odpowiedź do aplikacji jednostki uzależnionej. |

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













