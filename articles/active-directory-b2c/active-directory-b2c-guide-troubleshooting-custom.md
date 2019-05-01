---
title: Rozwiązywanie problemów dotyczących zasad niestandardowych w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Poznaj sposoby rozwiązywania błędy podczas pracy z zasad niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 5345069005f9161ff04fffb063fda99b7594ec08
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64703035"
---
# <a name="troubleshoot-azure-ad-b2c-custom-policies-and-identity-experience-framework"></a>Rozwiązywanie problemów z niestandardowych zasad usługi Azure AD B2C i platformy środowiska tożsamości

Jeśli używasz usługi Azure Active Directory B2C (Azure AD B2C) zasady niestandardowe, mogą pojawić się problemów z konfigurowaniem struktura środowiska tożsamości w formacie XML język zasad.  Uczenia się pisania niestandardowych zasad może być podobny uczenia się nowego języka. W tym artykule opisano narzędzia i wskazówki, które mogą pomóc Ci szybko odnaleźć i rozwiązać problemy. 

> [!NOTE]
> Ten artykuł koncentruje się na rozwiązywaniu problemów z konfiguracją zasad niestandardowych usługi Azure AD B2C. Go nie dotyczą aplikacji jednostki uzależnionej lub jego biblioteki tożsamości.

## <a name="xml-editing"></a>Edytowanie XML

Najbardziej typowych błędów w konfigurowaniu zasad niestandardowych jest nieprawidłowo w formacie XML. Niemal istotne jest dobrym edytora XML. Dobre edytora XML Wyświetla XML w sposób macierzysty, color-codes zawartości, powoduje wstępne wypełnienie typowe terminy, przechowuje elementy XML indeksowane i można sprawdzić poprawność ze schematem. Poniżej przedstawiono dwa naszych ulubionych edytory XML:

* [Visual Studio Code](https://code.visualstudio.com/)
* [Notepad++](https://notepad-plus-plus.org/)

Sprawdzanie poprawności schematu XML identyfikuje błędy przed przekazaniem pliku XML. W folderze głównym pakietu startowego pobrać definicji schematu XML TrustFrameworkPolicy_0.3.0.0.xsd. Aby uzyskać więcej informacji, w dokumentacji edytora XML, poszukaj *narzędzia XML* i *sprawdzanie poprawności kodu XML*.

Przegląd zasad XML może okazać się pomocne. Usługa Azure AD B2C odrzuca XML, wszelkie wykryte błędy formatowania. Czasami niepoprawnie sformatowana XML może spowodować komunikaty o błędach, które są mylące.

## <a name="upload-policies-and-policy-validation"></a>Przekazywanie zasad i sprawdzanie poprawności zasad

 Odbywa się automatycznie Walidacja przekazywania pliku XML. Większość błędów spowodować przekazywania nie powiedzie się. Walidacja obejmuje plik zasad, które przekazujesz. Zawiera on również łańcuch plików, które przekazywanie pliku, który odwołuje się do (pliku zasad jednostki uzależnionej ze stron, plik rozszerzenia i pliku podstawowego). 
 
 Oto typowe błędy sprawdzania poprawności.

Fragment kodu błędu: `... makes a reference to ClaimType with id "displaName" but neither the policy nor any of its base policies contain such an element`
* Wartość oświadczenia mógł zostać źle wpisany lub nie istnieje w schemacie.
* Element ClaimType wartości musi być zdefiniowany w co najmniej jeden z plików w ramach zasad. 
    Na przykład: `<ClaimType Id="socialIdpUserId">`
* Jeśli typ oświadczenia jest zdefiniowana w pliku rozszerzenia, ale jest również używany w profilu TechnicalProfile wartości w pliku podstawowego, przekazywanie pliku podstawowego powoduje wystąpienie błędu.

Fragment kodu błędu: `...makes a reference to a ClaimsTransformation with id...`
* Przyczyny błędu może być taka sama, jak w przypadku błędu typu oświadczenia.

Fragment kodu błędu: `Reason: User is currently logged as a user of 'yourtenant.onmicrosoft.com' tenant. In order to manage 'yourtenant.onmicrosoft.com', please login as a user of 'yourtenant.onmicrosoft.com' tenant`
* Sprawdź, czy identyfikator dzierżawy, wartość w **\<elementu TrustFrameworkPolicy\>** i **\<BasePolicy\>** elementy są zgodne w dzierżawie docelowej usługi Azure AD B2C.  

## <a name="troubleshoot-the-runtime"></a>Rozwiązywanie problemów z środowiska uruchomieniowego

* Użyj `Run Now` i `https://jwt.io` do testowania zasad niezależnie od usługi sieci web lub aplikacji mobilnej. Ta witryna sieci Web działa jak aplikacji jednostki uzależnionej. Wyświetla zawartość z sieci Web tokenu JSON (JWT) generowany przez zasady usługi Azure AD B2C. Aby utworzyć aplikacji testowej w struktura środowiska tożsamości, użyj następujących wartości:
    * Nazwa: TestApp
    * W sieci Web/sieci Web aplikacji interfejsu API: Nie
    * Natywny klient: Nie

* Aby śledzić wymiana wiadomości między przeglądarką klienta i usługi Azure AD B2C, należy użyć [Fiddler](https://www.telerik.com/fiddler). Może pomóc Ci wskazanie gdzie swoją podróż po użytkownik nie działa prawidłowo w etapów aranżacji.

* W **trybu opracowywania**, użyj **usługi Application Insights** do śledzenia działania swoją podróż po użytkownik struktura środowiska tożsamości. W **trybu opracowywania**, można zaobserwować, wymiana oświadczeń między struktura środowiska tożsamości i różnych dostawców oświadczeń, które są zdefiniowane w profilach technicznych, takich jak dostawców tożsamości, oparte na interfejsie API usługi, Katalog użytkowników w usłudze Azure AD B2C i innych usług, takich jak Azure wielu uwierzytelnianie wieloskładnikowe.  

## <a name="recommended-practices"></a>Zalecane praktyki

**Przechowywanie wielu wersji swoich scenariuszy. Je pogrupować w projekcie z aplikacją.** Podstawowy, rozszerzenia i jednostki uzależnionej strona pliki są bezpośrednio zależne od siebie nawzajem. Zapisz je jako grupa. Nowe funkcje zostaną dodane do swoich zasad, zachować oddzielnych wersji roboczych. Etap wersji roboczych w ramach własnego pliku system zawierające kod aplikacji, które współdziałają z.  Aplikacje mogą wywołać wiele różnych jednostki uzależnionej zasad firmy, w dzierżawie. Mogą one stać się zależne od oświadczenia, które spełniają oczekiwane z zasad usługi Azure AD B2C.

**Twórz i Testuj profile techniczne przy użyciu znanego użytkownika podróży.** Za pomocą zasad pakiet startowy przetestowane skonfigurować profile techniczne. Przetestuj je oddzielnie przed dołączać je do własnych podróży użytkownika.

**Twórz i Testuj podróży użytkownika z przetestowane profile techniczne.** Zmień kroki aranżacji podróży użytkownika przyrostowo. Stopniowo kompilować zamierzony scenariuszy.

## <a name="next-steps"></a>Kolejne kroki

* W usłudze GitHub, Pobierz [active-directory-b2c-custom-policy-starterpack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) pliku zip.
