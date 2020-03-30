---
title: Zarządzanie dostępem użytkowników w usłudze Azure Active Directory B2C | Dokumenty firmy Microsoft
description: Dowiedz się, jak identyfikować osoby niepełnoletnie, zbierać dane dotyczące daty urodzenia i kraju/regionu oraz akceptować warunki użytkowania w aplikacji przy użyciu usługi Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/24/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f04a3fea3801f917a3ae4aced04ef3824d1cfa82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184523"
---
# <a name="manage-user-access-in-azure-active-directory-b2c"></a>Zarządzanie dostępem użytkowników w usłudze Azure Active Directory B2C

W tym artykule omówiono sposób zarządzania dostępem użytkowników do aplikacji przy użyciu usługi Azure Active Directory B2C (Azure AD B2C). Zarządzanie dostępem w aplikacji obejmuje:

- Identyfikowanie nieletnich i kontrolowanie dostępu użytkownika do aplikacji.
- Wymaganie zgody rodziców dla osób niepełnoletnich na korzystanie z aplikacji.
- Zbieranie danych o narodzinach i kraju/regionie od użytkowników.
- Przechwytywanie warunków użytkowania umowy i dostępu gating.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="control-minor-access"></a>Kontroluj niewielki dostęp

Aplikacje i organizacje mogą zdecydować o zablokowaniu osobom niepełnoletnim korzystania z aplikacji i usług, które nie są kierowane do tej grupy odbiorców. Alternatywnie, aplikacje i organizacje mogą podjąć decyzję o przyjęciu nieletnich, a następnie zarządzaniu zgodą rodziców i dostarczać osobom niepełnoletnim dopuszczalne doświadczenia, zgodnie z zasadami biznesowymi i dozwolone przez regulacje.

Jeśli użytkownik jest identyfikowany jako pomocniczy, można ustawić przepływ użytkownika w usłudze Azure AD B2C na jedną z trzech opcji:

- **Wyślij podpisany JWT id_token z powrotem do aplikacji:** Użytkownik jest zarejestrowany w katalogu, a token jest zwracany do aplikacji. Następnie aplikacja przechodzi przez zastosowanie reguł biznesowych. Na przykład aplikacja może przejść do procesu zgody rodziców. Aby użyć tej metody, wybierz opcję otrzymywania **ageGroup** i **consentProvidedForMinor** oświadczeń z aplikacji.

- **Wyślij niepodpisany token JSON do aplikacji:** Usługa Azure AD B2C powiadamia aplikację, że użytkownik jest osobą niepełnoletnią i zapewnia stan zgody rodzicielskiej użytkownika. Następnie aplikacja przechodzi przez zastosowanie reguł biznesowych. Token JSON nie kończy pomyślnego uwierzytelniania za pomocą aplikacji. Aplikacja musi przetwarzać nieuwierzytyzowanego użytkownika zgodnie z roszczeniami zawartymi w tokenie JSON, które mogą zawierać **nazwę,** **adres e-mail,** **ageGroup**i **consentProvidedForMinor**.

- **Zablokuj użytkownika:** Jeśli użytkownik jest osobą niepełnoletnią, a zgoda rodziców nie została udzielona, usługa Azure AD B2C może powiadomić użytkownika, że jest zablokowany. Token nie jest wystawiany, dostęp jest zablokowany, a konto użytkownika nie jest tworzone podczas procesu rejestracji. Aby zaimplementować to powiadomienie, należy podać odpowiednią stronę zawartości HTML/CSS, aby poinformować użytkownika i przedstawić odpowiednie opcje. Wniosek o rejestrację nie wymaga dalszych działań.

## <a name="get-parental-consent"></a>Uzyskaj zgodę rodziców

W zależności od przepisów dotyczących aplikacji, może być konieczne udzielenie zgody rodzica przez użytkownika zweryfikowanego jako osoba dorosła. Usługa Azure AD B2C nie zapewnia środowiska, aby zweryfikować wiek danej osoby, a następnie zezwolić zweryfikowanej osobie dorosłej na udzielenie zgody rodzicielskiej osobie niepełnoletniej. To doświadczenie musi być zapewnione przez aplikację lub innego dostawcę usług.

Poniżej przedstawiono przykład przepływu użytkownika w celu zebrania zgody rodziców:

1. Operacja [interfejsu API programu Microsoft Graph](https://docs.microsoft.com/graph/use-the-api) identyfikuje użytkownika jako pomocniczego i zwraca dane użytkownika do aplikacji w postaci niepodpisanego tokenu JSON.

2. Aplikacja przetwarza token JSON i pokazuje ekran do osoby niepełnoletniej, powiadamiając ich, że wymagana jest zgoda rodziców i żądając zgody rodzica w trybie online.

3. Usługa Azure AD B2C pokazuje proces logowania, do których użytkownik może zalogować się normalnie i wystawia token do aplikacji, która ma zawierać **legalAgeGroupClassification = "minorWithParentalConsent"**. Aplikacja zbiera adres e-mail rodzica i sprawdza, czy rodzic jest osobą dorosłą. W tym celu używa zaufanego źródła, takiego jak krajowy urząd tożsamości, weryfikacja licencji lub dowód karty kredytowej. Jeśli weryfikacja zakończy się pomyślnie, aplikacja monituje nieu minora, aby zalogować się przy użyciu przepływu użytkownika usługi Azure AD B2C. Jeśli odmowa zgody (na przykład if **legalAgeGroupClassification = "minorWithoutParentalConsent"**), usługa Azure AD B2C zwraca token JSON (nie login) do aplikacji, aby ponownie uruchomić proces zgody. Opcjonalnie można dostosować przepływ użytkownika tak, aby osoba niepełnoletnia lub osoba dorosła mogła odzyskać dostęp do konta osoby niepełnoletniej, wysyłając kod rejestracyjny na adres e-mail osoby niepełnoletniej lub adres e-mail osoby dorosłej.

4. Aplikacja oferuje możliwość cofnięcia zgody przez osoby niepełnoletniej.

5. Gdy osoba niepełnoletnia lub osoba dorosła cofnie zgodę, interfejs API programu Microsoft Graph może służyć do zmiany **zgodyProvidedForMinor** **na odmowę.** Alternatywnie aplikacja może zdecydować się na usunięcie nieletniego, którego zgoda została cofnięta. Opcjonalnie można dostosować przepływ użytkownika, tak aby uwierzytelniony numer pomocniczy (lub rodzic korzystający z konta osoby niepełnoletniej) mógł cofnąć zgodę. Zgody rekordów usługi Azure AD **B2CProvidedForMinor** jako **odmowa**.

Aby uzyskać więcej informacji na temat **legalAgeGroupClassification**, **consentProvidedForMinor**i **ageGroup**, zobacz [Typ zasobu użytkownika](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/user). Aby uzyskać więcej informacji o atrybutach niestandardowych, zobacz [Zbieranie informacji o konsumentach za pomocą atrybutów niestandardowych.](user-flow-custom-attributes.md) Podczas adresowaniu atrybutów rozszerzonych przy użyciu interfejsu API programu Microsoft Graph należy użyć długiej wersji atrybutu, takiej jak *extension_18b70cf9bb834edd8f38521c2583cd86_dateOfBirth*: *2011-01-01T00:00:00Z*.

## <a name="gather-date-of-birth-and-countryregion-data"></a>Zbieranie danych o dacie urodzenia i kraju/regionie

Aplikacje mogą polegać na usłudze Azure AD B2C w celu zebrania informacji o dacie urodzenia (DOB) i kraju/regionie od wszystkich użytkowników podczas rejestracji. Jeśli te informacje jeszcze nie istnieją, aplikacja może zażądać go od użytkownika podczas następnej podróży uwierzytelniania (logowania). Użytkownicy nie mogą kontynuować bez podawania informacji o dob i kraju/regionie. Usługa Azure AD B2C używa informacji do określenia, czy dana osoba jest uważana za osobę niepełnoletnią zgodnie ze standardami regulacyjnymi tego kraju/regionu.

Dostosowany przepływ użytkownika może zbierać informacje DOB i kraju/regionu i używać transformacji oświadczeń usługi Azure AD B2C w celu określenia **grupy wiekowej** i utrwalenia wyniku (lub utrwalania informacji DOB i kraju/regionu bezpośrednio) w katalogu.

Następujące kroki pokazują logikę, która jest używana do obliczania **ageGroup** od daty urodzenia użytkownika:

1. Spróbuj znaleźć kraj według kodu kraju na liście. Jeśli kraj nie zostanie znaleziony, wróć do **domyślnego**.

2. Jeśli węzeł **MinorConsent** jest obecny w elemencie kraju:

    a. Oblicz datę, w którą użytkownik musi się urodzić, aby zostać uznanym za osobę dorosłą. Na przykład jeśli bieżąca data to 14 marca 2015 r. i **podrzędna zgodę** wynosi 18, data urodzenia musi być nie później niż 14 marca 2000 r.

    b. Porównaj minimalną datę urodzenia z rzeczywistą datą urodzenia. Jeśli minimalna data urodzenia przypada przed datą urodzenia użytkownika, obliczenie zwraca **wartość Minor** jako obliczenie grupy wiekowej.

3. Jeśli węzeł **MinorNoConsentRequired** znajduje się w elemencie kraju, powtórz kroki 2a i 2b przy użyciu wartości z **minornoconsentrequired**. Dane wyjściowe 2b zwraca **MinorNoConsentRequired,** jeśli minimalna data urodzenia jest przed datą urodzenia użytkownika.

4. Jeśli żadne z obliczeń nie zwraca wartości true, obliczenie zwraca **adult**.

Jeśli aplikacja niezawodnie zebrała dob lub dane kraju/regionu za pomocą innych metod, aplikacja może użyć interfejsu API wykresu, aby zaktualizować rekord użytkownika z tymi informacjami. Przykład:

- Jeśli wiadomo, że użytkownik jest osobą dorosłą, zaktualizuj atrybut katalogu **ageGroup** o wartości **Adult**.
- Jeśli użytkownik jest znany jako osoba niepełnoletnia, zaktualizuj atrybut katalogu **ageGroup** z wartością **Minor** i ustaw **zgodęProvidedForMinor**, odpowiednio.

Aby uzyskać więcej informacji na temat zbierania danych DOB, zobacz [Używanie wiekowania w usłudze Azure AD B2C](basic-age-gating.md).

## <a name="capture-terms-of-use-agreement"></a>Uchwycenie warunków użytkowania

Podczas tworzenia aplikacji, zwykle przechwytywania akceptacji użytkowników warunków użytkowania w ich aplikacji bez lub tylko niewielkie, udział z katalogu użytkownika. Możliwe jest jednak użycie przepływu użytkownika usługi Azure AD B2C w celu zebrania akceptacji przez użytkownika warunków użytkowania, ograniczenia dostępu, jeśli akceptacja nie zostanie udzielona, oraz wymuszenia akceptacji przyszłych zmian warunków użytkowania, w oparciu o datę ostatniej akceptacji i datę najnowszej wersji warunków użytkowania.

**Warunki użytkowania** mogą również zawierać "Zgodę na udostępnianie danych stronom trzecim". W zależności od lokalnych przepisów i reguł biznesowych, można zebrać akceptację użytkownika obu warunków w połączeniu, lub można zezwolić użytkownikowi na zaakceptowanie jednego, a nie drugiego.

W poniższych krokach opisano, jak zarządzać warunkami użytkowania:

1. Zapisz akceptację warunków użytkowania i datę akceptacji za pomocą interfejsu API wykresu i atrybutów rozszerzonych. Można to zrobić przy użyciu zarówno wbudowanych, jak i niestandardowych przepływów użytkowników. Zaleca się tworzenie i używanie **atrybutów extension_termsOfUseConsentDateTime** i **extension_termsOfUseConsentVersion.**

2. Utwórz wymagane pole wyboru z etykietą "Akceptuj warunki użytkowania" i zapisz wynik podczas rejestracji. Można to zrobić przy użyciu zarówno wbudowanych, jak i niestandardowych przepływów użytkowników.

3. Usługa Azure AD B2C przechowuje warunki użytkowania umowy i akceptacji użytkownika. Za pomocą interfejsu API wykresu można zbadać stan dowolnego użytkownika, odczytując atrybut rozszerzenia, który jest używany do rejestrowania odpowiedzi (na przykład **odczytywanie termsOfUseTestUpdateDateTime).** Można to zrobić przy użyciu zarówno wbudowanych, jak i niestandardowych przepływów użytkowników.

4. Wymagaj akceptacji zaktualizowanych warunków użytkowania, porównując datę przyjęcia z datą najnowszej wersji warunków użytkowania. Daty można porównać tylko przy użyciu niestandardowego przepływu użytkownika. Użyj atrybutu rozszerzonego **extension_termsOfUseConsentDateTime**i porównaj wartość z roszczeniem **termsOfUseTextUpdateDateTime**. Jeśli akceptacja jest stara, wymusz nową akceptację, wyświetlając ekran z własnym potwierdzeniem. W przeciwnym razie zablokuj dostęp przy użyciu logiki zasad.

5. Wymagaj akceptacji zaktualizowanych warunków użytkowania, porównując numer wersji akceptacji z najnowszym zaakceptowanym numerem wersji. Numery wersji można porównywać tylko przy użyciu niestandardowego przepływu użytkownika. Użyj atrybutu rozszerzonego **extension_termsOfUseConsentDateTime**i porównaj wartość z roszczeniem **extension_termsOfUseConsentVersion**. Jeśli akceptacja jest stara, wymusz nową akceptację, wyświetlając ekran z własnym potwierdzeniem. W przeciwnym razie zablokuj dostęp przy użyciu logiki zasad.

Można przechwytywać warunki akceptacji użytkowania w następujących scenariuszach:

- Nowy użytkownik się rejestruje. Warunki użytkowania są wyświetlane, a wynik akceptacji jest przechowywany.
- Loguje się użytkownik, który wcześniej zaakceptował najnowsze lub aktywne warunki użytkowania. Warunki użytkowania nie są wyświetlane.
- Loguje się użytkownik, który nie zaakceptował jeszcze najnowszych lub aktywnych warunków użytkowania. Warunki użytkowania są wyświetlane, a wynik akceptacji jest przechowywany.
- Loguje się użytkownik, który zaakceptował już starszą wersję warunków użytkowania, które są teraz aktualizowane do najnowszej wersji. Warunki użytkowania są wyświetlane, a wynik akceptacji jest przechowywany.

Na poniższej ilustracji przedstawiono zalecany przepływ użytkownika:

![Schemat schematu blokowego przedstawiający zalecany przepływ użytkownika akceptacyjnego](./media/manage-user-access/user-flow.png)

Poniżej przedstawiono przykład zgody na warunki użytkowania oparte na DateTime w oświadczeniu:

```xml
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

Poniżej przedstawiono przykład zgody na warunki użytkowania oparte na wersji w oświadczeniu:

```xml
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

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak usuwać i eksportować dane użytkowników, zobacz [Zarządzanie danymi użytkownika](manage-user-data.md).
- Aby zapoznać się z przykładem zasady niestandardowej implementacji monitu o warunki użytkowania, zobacz [A B2C IEF Custom Policy - Sign Up and Sign In with "Terms of Use" prompt](https://github.com/azure-ad-b2c/samples/tree/master/policies/sign-in-sign-up-versioned-tou).
