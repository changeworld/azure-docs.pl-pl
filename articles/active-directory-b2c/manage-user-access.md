---
title: Zarządzanie dostępem użytkowników w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się, jak identyfikować osoby nieletnie, zbieraj daty urodzenia i kraju/regionu danych i uzyskać Akceptacja warunków użytkowania w aplikacji za pomocą usługi Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/24/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 88123cc24359daaf1c6fc7e3ceeed8f77f717c9a
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65228025"
---
# <a name="manage-user-access-in-azure-active-directory-b2c"></a>Zarządzanie dostępem użytkowników w usłudze Azure Active Directory B2C

W tym artykule omówiono sposób zarządzanie dostępem użytkowników do aplikacji za pomocą usługi Azure Active Directory (Azure AD) B2C. Zarządzanie dostępem do aplikacji obejmują:

- Identyfikowanie osoby nieletnie i kontrola dostępu użytkownika do aplikacji.
- Wymagające zgody rodziców nieletnim do użycia aplikacji.
- Zbieranie danych urodzenia i kraju/regionu od użytkowników.
- Przechwytywanie Umowy warunki użytkowania i uzyskania dostępu bramowego.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="control-minor-access"></a>Kontrola dostępu pomocnicza

Aplikacje i organizacje mogą zdecydować o Blokuj nieletnim za pomocą aplikacji i usług, które nie zostały jeszcze skierowane do tej grupy odbiorców. Alternatywnie aplikacji i organizacji może podjąć decyzję zaakceptować osoby nieletnie i później zarządzać zgody rodziców i dostarczanie środowisk dopuszczalną dla osoby nieletnie, zgodnie z ustawieniem reguł biznesowych i dozwolone przez rozporządzenie. 

Jeśli użytkownik jest identyfikowany jako pomocnicza, przepływ użytkownika można ustawić w usłudze Azure AD B2C do jednej z trzech opcji:

- **Wyślij podpisane id_token tokenów JWT do aplikacji**: Użytkownik jest zarejestrowany w katalogu, a token jest zwracana do aplikacji. Następnie aplikacja przechodzi przez stosowanie reguł biznesowych. Na przykład aplikacja może kontynuować proces uzyskuje zgodę rodzica. Aby użyć tej metody, wybierz opcję do odbierania **grupy wiekowej** i **consentProvidedForMinor** oświadczeń z aplikacji.

- **Wyślij token JSON bez znaku do aplikacji**: Usługa Azure AD B2C powiadamia aplikację, że użytkownik jest pomocnicze i zawiera informacje o stanie użytkownika uzyskuje zgodę rodzica. Następnie aplikacja przechodzi przez stosowanie reguł biznesowych. JSON token nie zostanie zakończone po pomyślnym uwierzytelnieniu w aplikacji. Aplikacja musi przetworzyć nieuwierzytelniony użytkownik zgodnie z oświadczenia zawarte w tokenie JSON, które mogą obejmować **nazwa**, **e-mail**, **grupy wiekowej**i **consentProvidedForMinor**.

- **Zablokuj użytkownikowi**: Jeśli użytkownik jest pomocnicze, a nie dostarczono zgody rodziców, usługi Azure AD B2C może powiadomić użytkownika dany użytkownik jest zablokowany. Wystawiony token nie, wówczas dostęp będzie zablokowany i konto użytkownika nie jest tworzone podczas podróży rejestracji. Aby zaimplementować to powiadomienie, należy podać odpowiednie strony zawartości HTML/CSS do informowania użytkowników i istnieje odpowiednie opcje. Żadne dalsze działania jest wymagane przez aplikację do nowej rejestracji.

## <a name="get-parental-consent"></a>Pobierz zgody rodziców

W zależności od rozporządzenie aplikacji zgody rodziców, może być konieczne przyznawane przez użytkownika, który jest weryfikowany jako osoba dorosła. Usługa Azure AD B2C nie zapewnia środowisko, aby zweryfikować wiek danej osoby, a następnie zezwolić na zweryfikowaną dla dorosłych udzielić zgody rodziców pomocnicze. To środowisko musi być podana przez innego dostawcy usług lub aplikacji.

Oto przykładowy przepływ użytkownika w celu zbierania zgody rodziców:

1. [Interfejsu API usługi Azure Active Directory Graph](/previous-versions/azure/ad/graph/api/api-catalog) operacji identyfikuje użytkownika jako pomocnicze i zwraca dane użytkownika do aplikacji w postaci bez znaku token JSON.

2. Aplikacja przetwarza JSON token i zawiera ekran z pomocnicze, powiadamianie mu, że wymagana jest zgoda rodzica i żądanie wyrażenia zgody przez nadrzędne w trybie online. 

3. Usługa Azure AD B2C pokazuje podróż Zaloguj się, że użytkownik zalogować się w zwykły sposób i wystawia token do aplikacji, która jest ustawiona w celu uwzględnienia **legalAgeGroupClassification = "minorWithParentalConsent"**. Aplikacja umożliwia zbieranie informacji o adres e-mail elementu nadrzędnego i sprawdza, czy element nadrzędny jest osobą dorosłą. Aby to zrobić, używa zaufanego źródła, takich jak national identyfikator pakietu office, weryfikacja licencji lub dowód karty kredytowej. Jeśli weryfikacja zakończy się pomyślnie, aplikacja wyświetli pomocnicza do logowania za pomocą przepływu użytkownika usługi Azure AD B2C. Jeśli zgoda zostanie odrzucona (na przykład, jeśli **legalAgeGroupClassification = "minorWithoutParentalConsent"**), usługi Azure AD B2C zwraca token JSON (nie logowania) do aplikacji w celu ponownego uruchomienia procesu zgody. Użytkownik może opcjonalnie można dostosowywać przepływ użytkownika, tak aby drobnych lub osobę dorosłą mogli odzyskać dostęp do konta pomocnicze, wysyłając kodu rejestracyjnego do jego adres e-mail lub adres e-mail osoby dorosłej w rekordzie.

4. Aplikacja udostępnia opcję do pomocnicze, aby można było odwołać zgody.

5. W przypadku drobnych lub osobą dorosłą odwołuje zgody, interfejsu API programu Graph usługi Azure AD może służyć do zmiany **consentProvidedForMinor** do **odmowa**. Aplikacja może też usunąć pomocnicze, w których zgody został odwołany. Użytkownik może opcjonalnie można dostosowywać przepływ użytkownika, tak aby uwierzytelniony pomocnicze (lub obiektu nadrzędnego, który jest przy użyciu jego konta), można odwołać zgody. Usługa Azure AD B2C rekordów **consentProvidedForMinor** jako **odmowa**.

Aby uzyskać więcej informacji na temat **legalAgeGroupClassification**, **consentProvidedForMinor**, i **grupy wiekowej**, zobacz [typ zasobu użytkownika](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/user). Aby uzyskać więcej informacji na temat atrybutów niestandardowych, zobacz [Wykorzystaj niestandardowe atrybuty do zbierania informacji o użytkownikach](active-directory-b2c-reference-custom-attr.md). Po wykonaniu atrybuty rozszerzone przy użyciu interfejsu API programu Graph usługi Azure AD możesz korzystać tylko długiej atrybutu, takich jak *extension_18b70cf9bb834edd8f38521c2583cd86_dateOfBirth*: *2011-01-01T00:00:00Z*.

## <a name="gather-date-of-birth-and-countryregion-data"></a>Zbierz daty urodzenia i kraju/regionu danych

Aplikacje mogą polegać na usłudze Azure AD B2C na potrzeby zbierania daty urodzenia (podana data urodzenia) i kraj/region informacji z wszystkich użytkowników podczas rejestracji. Jeśli te informacje nie istnieje, aplikacja mogą go żądać od użytkownika podczas następnego podróż uwierzytelnienia (logowania). Użytkownicy nie można kontynuować bez podawania ich informacji podana data urodzenia i kraju/regionu. Usługa Azure AD B2C używa informacji w celu ustalenia, czy poszczególne jest uważany za zgodnie z przepisami standardy tego kraju/regionu. 

Przepływ użytkownika można zbierać podana data urodzenia i kraju/regionu informacji i użycie usługi Azure AD B2C oświadczeń przekształcenia, aby określić **grupy wiekowej** i utrzymują się wynik (lub bezpośrednio utrwalania informacji podana data urodzenia i kraju/regionu) w katalog.

Poniższe kroki pokazują logikę, która jest używana do obliczania **grupy wiekowej** od daty urodzenia użytkownika:

1. Spróbuj znaleźć kraju, numer kierunkowy kraju, na liście. Jeśli kraj nie zostanie znaleziony, wrócić do **domyślne**.

2. Jeśli **MinorConsent** węzła jest obecny w elemencie kraju:

    a. Oblicz dzień, w którym użytkownik musi została utworzona w wziąć pod uwagę osoba dorosła. Na przykład, jeśli bieżąca data to 14 marca 2015 r. i **MinorConsent** 18, Data urodzenia musi być nie później niż 14 marca 2000.

    b. Porównaj Data urodzenia minimalne, oraz datę urodzenia rzeczywistych. Jeśli data urodzenia minimalna jest wcześniejsza niż data urodzenia użytkownika, zwraca obliczenia **pomocnicza** jako grupa wiekowa obliczeń.

3. Jeśli **MinorNoConsentRequired** węzeł znajduje się w elemencie kraju, powtarzaj kroki 2a i 2b przy użyciu wartości **MinorNoConsentRequired**. Zwraca dane wyjściowe 2b **MinorNoConsentRequired** Jeśli data urodzenia minimalna jest wcześniejsza niż data urodzenia użytkownika. 

4. Jeśli żadna obliczeń zwróci wartość true, zwraca obliczenia **treści dla dorosłych**.

Jeśli aplikacja niezawodnie zebrał podana data urodzenia lub kraj/region danych przy użyciu innych metod, aplikacji może używać interfejsu API programu Graph do zaktualizowania rekordu użytkownika z tymi informacjami. Na przykład:

- Jeśli użytkownik jest znany jako osoba dorosła, należy zaktualizować atrybutu katalogu **grupy wiekowej** o wartości **treści dla dorosłych**.
- Jeśli użytkownik jest znany jako pomocnicza, należy zaktualizować atrybutu katalogu **grupy wiekowej** o wartości **pomocnicza** i ustaw **consentProvidedForMinor**, odpowiednio.

Aby uzyskać więcej informacji na temat zbierania danych podana data urodzenia, zobacz [Użyj wieku w usłudze Azure AD B2C](basic-age-gating.md).

## <a name="capture-terms-of-use-agreement"></a>Przechwytywanie Umowa warunki użytkowania

Podczas opracowywania aplikacji zwykle przechwytywania użytkowników Akceptacja warunków użytkowania w aplikacjach bez lub tylko jest to pomocnicza udział z katalogu użytkowników. Jest to możliwe, jest jednak użyć przepływu użytkownika usługi Azure AD B2C w celu zbierania użytkownika Akceptacja warunków użytkowania, ograniczyć dostęp, jeśli akceptacji, nie ma przyznanych i wymuszanie akceptacji przyszłe zmiany warunki użytkowania, na podstawie daty akceptacji najnowsze i daty  najnowszą wersję warunków użytkowania.

**Warunki użytkowania** mogą również obejmować "Zgodę na udostępnianie danych z innych firm". W zależności od lokalnych regulacji prawnych i reguł biznesowych można gromadzić akceptacji użytkownika oba warunki, w połączeniu lub można zezwolić użytkownikowi zaakceptowanie jednego warunku, a nie inne.

W poniższych krokach opisano, jak można zarządzać z warunkami użytkowania:

1. Zapisz akceptacji warunków użytkowania i daty przyjęcia przy użyciu interfejsu API programu Graph i atrybutów rozszerzonych. Aby to zrobić za pomocą obu przepływy użytkownika wbudowanych i niestandardowych. Firma Microsoft zaleca, możesz utworzyć i używać **extension_termsOfUseConsentDateTime** i **extension_termsOfUseConsentVersion** atrybutów.

2. Utwórz wymagane pole wyboru z etykietą "Zaakceptuj warunki użytkowania", a następnie zapisz wynik podczas rejestracji. Aby to zrobić za pomocą obu przepływy użytkownika wbudowanych i niestandardowych.

3. Usługa Azure AD B2C przechowuje warunki użytkowania i akceptacji użytkownika. Można użyć interfejsu API programu Graph do wykonywania zapytań w stan każdego użytkownika, zapoznając się do atrybutu rozszerzenia, która jest używana do rejestrowania odpowiedzi (np. odczytywać **termsOfUseTestUpdateDateTime**). Aby to zrobić za pomocą obu przepływy użytkownika wbudowanych i niestandardowych.

4. Wymagaj akceptacji zaktualizowanych warunków użytkowania przez porównanie daty przyjęcia do daty najnowszą wersję warunków użytkowania. Daty można porównać tylko przy użyciu przepływu użytkownika niestandardowego. Użyj atrybutów rozszerzonych **extension_termsOfUseConsentDateTime**i porównać wartości do oświadczeń o **termsOfUseTextUpdateDateTime**. W przypadku starego akceptacji wymusić nowy akceptacji, wyświetlając ekran samodzielnie. W przeciwnym razie można zablokować dostęp za pomocą reguł zasad.

5. Wymagaj akceptacji zaktualizowanych warunków użytkowania przez porównanie numer wersji akceptacji najbardziej aktualną liczbę zaakceptowana wersja. Numery wersji można porównać tylko przy użyciu przepływu użytkownika niestandardowego. Użyj atrybutów rozszerzonych **extension_termsOfUseConsentDateTime**i porównać wartości do oświadczeń o **extension_termsOfUseConsentVersion**. W przypadku starego akceptacji wymusić nowy akceptacji, wyświetlając ekran samodzielnie. W przeciwnym razie można zablokować dostęp za pomocą reguł zasad.

Można przechwycić warunki Użyj akceptacji w następujących scenariuszach:

- Nowy użytkownik loguje się w. Warunki użytkowania są wyświetlane, a wynik akceptacji jest zapisywany.
- Użytkownik loguje się wcześniej kto zaakceptował najnowsze lub aktywny warunki użytkowania. Warunki użytkowania nie są wyświetlane.
- Użytkownik loguje się który nie zaakceptował już najnowsze lub aktywny warunki użytkowania. Warunki użytkowania są wyświetlane, a wynik akceptacji jest zapisywany.
- Użytkownik loguje się kto zaakceptował już starszą wersję warunków użytkowania, które są teraz aktualizowane do najnowszej wersji. Warunki użytkowania są wyświetlane, a wynik akceptacji jest zapisywany.

Na poniższej ilustracji przedstawiono przepływ użytkownika zalecane:

![Przepływ użytkownika akceptacji](./media/manage-user-access/user-flow.png) 

Oto przykład warunki daty/godziny na podstawie użycia zgody w oświadczenie:

```
<ClaimsTransformations>
  <ClaimsTransformation Id="GetNewUserAgreeToTermsOfUseConsentDateTime" TransformationMethod="GetCurrentDateTime">
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentDateTime" TransformationClaimType="currentDateTime" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="IsTermsOfUseConsentRequired" TransformationMethod="IsTermsOfUseConsentRequired">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="extension_termsOfUseConsentDateTime" TransformationClaimType="termsOfUseConsentDateTime" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="termsOfUseTextUpdateDateTime" DataType="dateTime" Value="2098-01-30T23:03:45" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="result" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
```

Oto przykład warunki wersji na podstawie użycia zgody w oświadczenie:

```
<ClaimsTransformations>
  <ClaimsTransformation Id="GetEmptyTermsOfUseConsentVersionForNewUser" TransformationMethod="CreateStringClaim">
    <InputParameters>
      <InputParameter Id="value" DataType="string" Value=""/>
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="createdClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="GetNewUserAgreeToTermsOfUseConsentVersion" TransformationMethod="CreateStringClaim">
    <InputParameters>
      <InputParameter Id="value" DataType="string" Value="V1"/>
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="createdClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="IsTermsOfUseConsentRequiredForVersion" TransformationMethod="CompareClaimToValue">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="inputClaim1" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="compareTo" DataType="string" Value="V1" />
      <InputParameter Id="operator" DataType="string" Value="not equal" />
      <InputParameter Id="ignoreCase" DataType="string" Value="true" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="outputClaim" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations> 
```

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się, jak usunąć i wyeksportowania danych użytkownika, zobacz [zarządzanie danymi użytkowników](manage-user-data.md).
