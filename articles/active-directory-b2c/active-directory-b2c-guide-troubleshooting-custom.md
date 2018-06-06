---
title: Rozwiązywanie problemów dotyczących zasad niestandardowych w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Poznaj sposoby rozwiązywania błędy podczas pracy z niestandardowych zasad w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 05/07/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 22e7bc7bd275769f78ac2e482cb1af11e9404222
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34709618"
---
# <a name="troubleshoot-azure-ad-b2c-custom-policies-and-identity-experience-framework"></a>Rozwiązywanie problemów z niestandardowych zasad usługi Azure AD B2C i Framework obsługi tożsamości

Jeśli używasz usługi Azure Active Directory B2C zasad niestandardowych (Azure AD B2C), mogą pojawić się wyzwania konfigurowania tożsamości wystąpić Framework w formacie XML język zasad.  Learning można zapisać zasad niestandardowych można jak uczenia nowego języka. W tym artykule opisano narzędzia i wskazówki, które mogą ułatwić szybkie odnajdywanie i rozwiązywania problemów. 

> [!NOTE]
> Ten artykuł skupia się na rozwiązywaniu problemów z konfiguracją zasad niestandardowych usługi Azure AD B2C. Nie go adresów jednostki uzależnionej aplikacji firmy lub jego biblioteki tożsamości.

## <a name="xml-editing"></a>Edytowanie XML

Najbardziej typowe błąd z konfigurowaniem zasad niestandardowych jest nieprawidłowo sformatowany XML. Niemal ważne jest dobrym edytora XML. Dobrym edytora XML Wyświetla XML w sposób macierzysty, color-codes zawartości powoduje wstępne wypełnienie typowe terminy, przechowuje elementy XML indeksowane i sprawdzić ze schematem. Poniżej przedstawiono dwa naszych ulubionych edytory XML:

* [Visual Studio Code](https://code.visualstudio.com/)
* [Notatnik ++](https://notepad-plus-plus.org/)

Sprawdzanie poprawności schematu XML identyfikuje błędy, aby przesłać plik XML. W folderze głównym pakietu starter Pobierz definicję schematu XML TrustFrameworkPolicy_0.3.0.0.xsd. Aby uzyskać więcej informacji, w dokumentacji w edytorze XML, wyszukaj *narzędzia XML* i *sprawdzanie poprawności kodu XML*.

Przegląd reguł XML może okazać się przydatne. Usługa Azure AD B2C odrzuca wszystkie formatowania błędów wykrytych XML. Czasami niepoprawnie sformatowany XML może spowodować komunikaty o błędach, które są błędne.

## <a name="upload-policies-and-policy-validation"></a>Zasady przekazywania i sprawdzanie zasad

 Sprawdzanie poprawności kodu XML pliku przekazywania odbywa się automatycznie. Większość błędów spowodować niepowodzenie przekazywania. Sprawdzanie poprawności obejmuje plik zasad, które przekazujesz. Zawiera także łańcuch plików, które przekazywanego pliku odwołuje się do (pliku zasad jednostki uzależnionej strony, pliku rozszerzenia i plikiem podstawowym). 
 
 Następujące typowe błędy sprawdzania poprawności.

Fragment kodu błędu: `... makes a reference to ClaimType with id "displaName" but neither the policy nor any of its base policies contain such an element`
* Wartość typu oświadczenia mogą być zawiera błąd pisowni lub nie istnieje w schemacie.
* Typ oświadczenia wartości muszą być zdefiniowane w co najmniej jeden z plików w zasadach. 
    Na przykład: ` <ClaimType Id="socialIdpUserId">`
* Jeśli typ oświadczenia jest zdefiniowana w pliku rozszerzenia, ale jest również używana wartość TechnicalProfile w pliku podstawowego, przekazywanie pliku podstawowego powoduje błąd.

Fragment kodu błędu: `...makes a reference to a ClaimsTransformation with id...`
* Przyczyny tego błędu może być taki sam jak błąd typu oświadczenia.

Fragment kodu błędu: `Reason: User is currently logged as a user of 'yourtenant.onmicrosoft.com' tenant. In order to manage 'yourtenant.onmicrosoft.com', please login as a user of 'yourtenant.onmicrosoft.com' tenant`
* Sprawdź, czy wartość identyfikatora dzierżawy w **\<TrustFrameworkPolicy\>** i **\<BasePolicy\>** elementy dzierżawy usługi Azure AD B2C docelowej są zgodne.  

## <a name="troubleshoot-the-runtime"></a>Rozwiązywanie problemów z środowiska uruchomieniowego

* Użyj `Run Now` i `https://jwt.io` do testowania zasad niezależnie od sieci web lub aplikacji mobilnej. Ta witryna sieci Web działa jak jednostki uzależnionej aplikacji firmy. Wyświetla zawartość z sieci Web JSON tokenu (JWT) generowany przez zasady usługi Azure AD B2C. Aby utworzyć aplikację testu w ramach obsługi tożsamości, użyj następujących wartości:
    * Nazwa: TestApp
    * Aplikacja/interfejs API sieci Web w sieci Web: nie
    * Aplikacja Native client: nie

* Aby śledzić wymiany wiadomości między przeglądarki klienta i usługi Azure AD B2C, należy użyć [Fiddler](http://www.telerik.com/fiddler). Może pomóc Ci wskazanie gdzie podróży użytkownika nie działa prawidłowo w krokach Twojej aranżacji.

* W **tryb programowania**, użyj **usługi Application Insights** śledzenie działania podróży Framework obsługi tożsamości użytkownika. W **tryb programowania**, można zaobserwować wymiany oświadczeń w ramach obsługi tożsamości i różnych dostawców oświadczeń są definiowane przez techniczne profile, takie jak dostawców tożsamości, oparty na interfejsach API usług Katalog użytkowników w usłudze Azure AD B2C i innych usług, takich jak Azure kilku-Factor uwierzytelniania.  

## <a name="recommended-practices"></a>Zalecane praktyki

**Przechowywać wiele wersji scenariuszy. Grupowania ich w projekcie z aplikacją.** Podstawowy, rozszerzenia i jednostki uzależnionej plików firm są bezpośrednio zależne od siebie nawzajem. Zapisz je jako grupa. Nowe funkcje zostaną dodane do zasad, zachować różne wersje robocze. Etap wersji pracy w ramach własnego pliku system z kodu aplikacji, którymi wchodzić w interakcje.  Aplikacje mogą wywoływać wiele różnych jednostki uzależnionej zasady firmy, w dzierżawie. Mogą być zależne od oświadczenia, które oczekiwane z zasad usługi Azure AD B2C.

**Opracowanie i przetestowanie techniczne profile z użyciem podróże znanych użytkowników.** Przetestowany starter pakiet zasady umożliwiają konfigurowanie profili technicznych. Testowane oddzielnie przed włączyć je do własnych podróże użytkownika.

**Opracowanie i przetestowanie podróże użytkownika z profilami techniczne przetestowane.** Przyrostowo zmieniać kolejności kroków aranżacji przebieg użytkownika. Kompilacji stopniowego zamierzonego scenariuszy.

## <a name="next-steps"></a>Kolejne kroki

* W witrynie GitHub, Pobierz [active-directory-b2c-custom-policy-starterpack] (https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) pliku zip.
