---
title: Rozwiązywanie problemów z zasadami niestandardowymi w usłudze Azure Active Directory B2C
description: Dowiedz się więcej o podejściach do rozwiązywania błędów podczas pracy z zasadami niestandardowymi w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2f65e98cec04991fe9edef1b81bcb3ecc3d93d76
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186368"
---
# <a name="troubleshoot-azure-ad-b2c-custom-policies-and-identity-experience-framework"></a>Rozwiązywanie problemów z niestandardowymi zasadami usługi Azure AD B2C i strukturą środowiska tożsamości

Jeśli używasz zasad niestandardowych usługi Azure Active Directory B2C (Azure AD B2C), mogą wystąpić problemy z konfigurowaniem struktury środowiska tożsamości w formacie XML języka zasad. Nauka pisania niestandardowych zasad może być jak nauka nowego języka. W tym artykule opisujemy kilka narzędzi i wskazówek, które mogą pomóc w wykrywaniu i rozwiązywaniu problemów.

W tym artykule skupiono się na rozwiązywaniu problemów z konfiguracją zasad niestandardowych usługi Azure AD B2C. Nie dotyczy aplikacji jednostki uzależniającej ani jej biblioteki tożsamości.

## <a name="xml-editing"></a>Edycja XML

Najczęstszym błędem podczas konfigurowania zasad niestandardowych jest nieprawidłowo sformatowany kod XML. Dobry edytor XML jest prawie niezbędny. Wyświetla natywnie zawartość kodów XML, wstępnie wypełnia typowe terminy, utrzymuje elementy XML indeksowane i może sprawdzać poprawność względem schematu XML.

Dwa z naszych ulubionych edytorów są [Visual Studio Code](https://code.visualstudio.com/) i [Notepad ++](https://notepad-plus-plus.org/).

Sprawdzanie poprawności schematu XML identyfikuje błędy przed przekazaniem pliku XML. W folderze głównym [pakietu startowego pobierz](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack)plik definicji schematu XML *TrustFrameworkPolicy_0.3.0.0.xsd*. Aby dowiedzieć się, jak używać pliku schematu XSD do sprawdzania poprawności w edytorze, poszukaj *narzędzi XML* i *sprawdzania poprawności XML* lub podobnych w dokumentacji edytora.

Przegląd reguł XML może okazać się pomocny. Usługa Azure AD B2C odrzuca wszelkie błędy formatowania XML, które wykrywa. Czasami niepoprawnie sformatowany kod XML może powodować komunikaty o błędach, które wprowadzają w błąd.

## <a name="upload-policies-and-policy-validation"></a>Zasady przekazywania i sprawdzanie poprawności zasad

Sprawdzanie poprawności pliku zasad XML jest wykonywane automatycznie podczas przekazywania. Większość błędów powoduje niepowodzenie przekazywania. Sprawdzanie poprawności obejmuje plik zasad, który przesyłasz. Obejmuje również łańcuch plików, do które odnosi się plik przekazywania (plik zasad jednostki uzależniającej, plik rozszerzeń i plik podstawowy).

Typowe błędy sprawdzania poprawności są następujące:

> Fragment błędu:`...makes a reference to ClaimType with id "displayName" but neither the policy nor any of its base policies contain such an element`

* ClaimType wartość może być błędnie lub nie istnieje w schemacie.
* Wartości ClaimType muszą być zdefiniowane w co najmniej jednym z plików w zasadach.
    Na przykład: `<ClaimType Id="issuerUserId">`
* Jeśli ClaimType jest zdefiniowany w pliku rozszerzeń, ale jest również używany w TechnicalProfile wartość w pliku podstawowym, przekazywanie pliku podstawowego powoduje błąd.

> Fragment błędu:`...makes a reference to a ClaimsTransformation with id...`

* Przyczyny tego błędu może być taka sama jak w przypadku błędu ClaimType.

> Fragment błędu:`Reason: User is currently logged as a user of 'yourtenant.onmicrosoft.com' tenant. In order to manage 'yourtenant.onmicrosoft.com', please login as a user of 'yourtenant.onmicrosoft.com' tenant`

* Sprawdź, czy wartość TenantId w `<TrustFrameworkPolicy\>` i `<BasePolicy\>` elementy są zgodne z docelową dzierżawą usługi Azure AD B2C.

## <a name="troubleshoot-the-runtime"></a>Rozwiązywanie problemów ze czasem wykonywania

* Użyj **uruchom** `https://jwt.ms` teraz i przetestować swoje zasady niezależnie od aplikacji internetowej lub mobilnej. Ta strona działa jak aplikacja strony uzależniającej. Wyświetla zawartość tokenu sieci web JSON (JWT), który jest generowany przez zasady usługi Azure AD B2C.

    Aby utworzyć aplikację testową, `https://jwt.ms` która może przekierować do inspekcji tokenu:

    [!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

* Aby śledzić wymianę wiadomości między przeglądarką klienta a usługą Azure AD B2C, należy użyć [programu Fiddler](https://www.telerik.com/fiddler). To może pomóc uzyskać wskazanie, gdzie podróż użytkownika nie działa w krokach aranżacji.

* W **trybie rozwoju**użyj [usługi Application Insights,](troubleshoot-with-application-insights.md) aby śledzić działanie środowiska tożsamości środowiska podróży użytkownika. W **trybie rozwoju**można obserwować wymianę oświadczeń między platformą środowiska tożsamości a różnymi dostawcami oświadczeń, które są zdefiniowane przez profile techniczne, takie jak dostawcy tożsamości, usługi oparte na interfejsie API, katalog użytkowników usługi Azure AD B2C i inne usługi, takie jak uwierzytelnianie wieloskładnikowe platformy Azure.

## <a name="recommended-practices"></a>Zalecane praktyki

**Zachowaj wiele wersji scenariuszy. Pogrupuj je w projekcie z aplikacją.** Pliki bazowe, rozszerzenia i jednostki uzależnione są bezpośrednio zależne od siebie nawzajem. Zapisz je jako grupę. Gdy nowe funkcje są dodawane do zasad, zachowaj oddzielne wersje robocze. Etap pracy wersji we własnym systemie plików z kodu aplikacji, z którymi współdziałają. Aplikacje mogą wywoływać wiele różnych zasad jednostki uzależniającej w dzierżawie. Mogą one stać się zależne od oświadczeń, których oczekują od zasad usługi Azure AD B2C.

**Opracowywać i testować profile techniczne ze znanymi podróżami użytkowników.** Użyj przetestowanych zasad pakietu startowego, aby skonfigurować swoje profile techniczne. Przetestuj je oddzielnie, zanim włączysz je do własnych podróży użytkownika.

**Twórz i testuj podróże użytkowników za pomocą przetestowanych profili technicznych.** Stopniowo zmieniaj kroki aranżacji podróży użytkownika. Stopniowo twórz zamierzone scenariusze.

## <a name="next-steps"></a>Następne kroki

Dostępne na GitHub, pobierz [active-directory-b2c-custom-policy-starterpack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) .zip archiwum. Można również sklonować repozytorium:

```
git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
```
