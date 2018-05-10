---
title: Zarządzanie dostępem użytkowników w usłudze Azure AD B2C | Dokumentacja firmy Microsoft
description: Jak zidentyfikować osoby nieletnie, Zbierz daty urodzenia i kraju danych i uzyskać Akceptacja warunków użytkowania w aplikacji przy użyciu usługi Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 05/04/2018
ms.author: davidmu
ms.openlocfilehash: c44135a3069966b14d8760e4daa009ab8d39ccca
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="manage-user-access-in-azure-ad-b2c"></a>Zarządzanie dostępem użytkowników w usłudze Azure AD B2C

Ten artykuł zawiera informacje o sposobie zarządzanie dostępem użytkowników do aplikacji przy użyciu usługi Azure Active Directory (AD) B2C. Zarządzanie dostępem do aplikacji obejmują:

- Zidentyfikowanie osoby nieletnie i kontrolowanie dostępu do korzystania z aplikacji
- Wymaganie zgody rodzica osoby nieletnie do użycia aplikacji
- Zbieranie danych data urodzenia i kraju użytkownika
- Użyj przechwytywania warunków umowy i uzyskania dostępu bramowego

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

>[!Note] 
>Ten artykuł zawiera informacje, które mogą służyć do obsługi GDPR Twojego zobowiązań. Jeśli szukasz informacji ogólnych o GDPR zobacz [GDPR części portalu zaufania usługi](https://servicetrust.microsoft.com/ViewPage/GDPRGetStarted).

## <a name="control-minor-access"></a>Kontrola dostępu pomocnicze

Aplikacje i organizacje mogą zdecydować o zablokować osoby nieletnie z za pomocą aplikacji i usług, które nie zostały jeszcze skierowane do tych odbiorców. Alternatywnie aplikacje i organizacje mogą podjąć decyzję o zaakceptować osoby nieletnie później zarządzać zgodę jako rodzic i dostarczać środowiska dopuszczalną wartość dla osoby nieletnie, zgodnie z ustawieniem reguły biznesowe i dozwolone przez rozporządzenia. 

Jeśli użytkownik zostanie zidentyfikowana jako pomocniczy, przepływu użytkownika w usłudze Azure AD B2C może ustawić na jeden z trzech opcji:

- **Odesłania podpisem żądaniu JWT do aplikacji** — użytkownik jest zarejestrowany w katalogu i token jest zwracana do aplikacji. Następnie aplikacja będzie kontynuowana przy użyciu reguł biznesowych. Na przykład aplikacja może kontynuować proces zgodę jako rodzic. Aby to zrobić, możesz zdecydować, aby **ageGroup** i **consentProvidedForMinor** oświadczeń z aplikacji.
- **Wyślij niepodpisane token JSON do aplikacji** — usługi Azure AD B2C powiadamia aplikację, czy użytkownik jest pomocnicze i zawiera informacje o stanie użytkownika zgoda rodzica. Następnie aplikacja będzie kontynuowana przy użyciu reguł biznesowych. JSON token nie zostanie zakończone po pomyślnym uwierzytelnieniu z aplikacją. Aplikacja musi przetworzyć nieuwierzytelniony użytkownik zgodnie z oświadczenia uwzględnione w tokenie JSON, które mogą obejmować **nazwa**, **e-mail**, **ageGroup**i **consentProvidedForMinor**.
- **Blokuj użytkownika** — Jeśli użytkownik jest pomocnicze, a nie podano zgodę jako rodzic.  Usługa Azure AD B2C można przedstawić ekranu dla użytkownika, który informuje o blokowane.  Token nie jest wystawiony, dostęp będzie zablokowany, a konto użytkownika nie jest tworzone podczas podróży rejestracji. Aby zaimplementować to, musisz podać odpowiednie strony zawartości HTML/CSS do informowania użytkowników i obecne odpowiednie opcje. Nie dalsze akcje nie jest wymagane przez aplikację do nowej rejestracji.

## <a name="get-parental-consent"></a>Uzyskaj zgodę jako rodzic

W zależności od aplikacji rozporządzenia zgodę jako rodzic może być konieczne przyznane przez użytkownika zweryfikowany jako osoba dorosła.  Usługa Azure AD B2C nie zapewnia obsługi, aby zweryfikować wiek danej osoby, a następnie zezwolić zweryfikowano dorosłą udzielić zgody rodzica pomocnicze.  To środowisko musi zapewniać innego dostawcy usług lub aplikacji.

Poniżej przedstawiono przykład przepływu użytkownika do zbierania zgodę jako rodzic:

1. [Interfejsu API usługi Azure Active Directory Graph](https://msdn.microsoft.com/en-us/library/azure/ad/graph/api/api-catalog) operacja identyfikuje użytkownika jako pomocnicze i zwraca dane użytkownika do aplikacji w formie tokenu JSON bez znaku.
2. Aplikacja przetwarza JSON token i pokazuje ekranu do pomocniczej powiadamianie o tym, że zgodę jako rodzic jest wymagany i żądań zgody elementu nadrzędnego w trybie online. 
3. Usługa Azure AD B2C pokazuje podróży logowania zgodnie z którymi użytkownik może zalogować się normalnie i wystawia token do aplikacji, która jest ustawiona, aby uwzględnić **legalAgeGroupClassification = "minorWithParentalConsent"** zbiera dane aplikacji element nadrzędny adres e-mail i sprawdza, czy element nadrzędny jest osobę dorosłą przy użyciu zaufanego źródła, takich jak national identyfikator pakietu office, weryfikacja licencji lub potwierdzającego karty kredytowej. W przypadku powodzenia aplikacja wyświetli monit pomocnicze zalogowanie się przy użyciu usługi Azure AD B2C przepływu użytkownika. Jeśli odmówiono zgody (np. **legalAgeGroupClassification = "minorWithoutParentalConsent"**, usługi Azure AD B2C zwraca token JSON (nie nazwę logowania) do aplikacji, aby uruchomić ponownie proces zgody. Użytkownik może opcjonalnie można dostosowywać przepływ użytkownika, zgodnie z którymi pomocnicze lub osobą dorosłą może odzyskać dostęp do konta pomocnicze, wysyłając kod rejestracji do jego adresu e-mail lub adres e-mail dla dorosłych w rekordzie.
4. Aplikacja udostępnia opcję do pomocniczej, aby wycofać zgodę.
5. Gdy pomocnicza lub osoba dorosła odwołuje zgody, interfejsu API programu Azure AD Graph można używać do zmieniania **consetProvidedForMinor** do **odmowa**. Alternatywnie aplikacja może wybierz opcję usunięcia pomocnicze, w których zgody został odwołany. Użytkownik może opcjonalnie można dostosowywać przepływ użytkownika, zgodnie z którymi uwierzytelnionego pomocnicze (lub nadrzędnej przy użyciu jego konta) można wycofać zgodę. Azure AD B2C rekordów **consentProvidedForMinor** jako **odmowa**.

Aby uzyskać więcej informacji na temat **legalAgeGroupClassification**, **consentProvidedForMinor**, i **ageGroup**, zobacz [typu zasobu użytkownika](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/resources/user). Aby uzyskać więcej informacji dotyczących atrybutów niestandardowych, zobacz [Wykorzystaj niestandardowe atrybuty do zbierania informacji o użytkownikach](active-directory-b2c-reference-custom-attr.md). Gdy adresowania atrybuty rozszerzone przy użyciu interfejsu API Azure AD Graph, takie jak "extension_18b70cf9bb834edd8f38521c2583cd86_dateOfBirth" należy użyć długiej atrybutu: "2011-01-01T00:00:00Z"

## <a name="gather-date-of-birth-and-country-data"></a>Zbierz daty urodzenia i kraju danych

Aplikacje mogą polegać na usługi Azure AD B2C można zebrać datę urodzenia (podana data urodzenia) i kraju wszystkim użytkownikom podczas rejestracji. Jeśli podana data urodzenia lub kraj informacji jeszcze nie istnieje, go można poprosić użytkownika podczas następnego przebieg uwierzytelniania (logowanie). Użytkownicy nie będą mogli kontynuować bez podawania podana data urodzenia i kraju informacji. Na podstawie kraju i podana data urodzenia pod warunkiem, usługi Azure AD B2C Określa, czy poszczególne jest uznawane za pomocnicze zgodnie z normami tego kraju. 

Przepływ użytkownika można zebrać podana data urodzenia i kraju informacji i użycie usługi Azure AD B2C oświadczeń przekształcenia do określenia **ageGroup** i utrwalić wynik (lub utrwalić kraju i podana data urodzenia informacje bezpośrednio) w katalogu.

W poniższej procedurze pokazano logiki, które jest używane do obliczania **ageGroup** od daty urodzenia:

1. Spróbuj odnaleźć kraju przez kod kraju, na liście. Gdy kraju nie zostanie znaleziony, wrócić do **domyślne**.
2. Jeśli **MinorConsent** węzeł znajduje się w elemencie kraju:  <br>a. Obliczenia Minimalna data było użytkownika muszą na świat wziąć pod uwagę osobą dorosłą. Przykład: Data urodzenia jest 2015-3-14 i **MinorConsent** 18, Data urodzenia minimalna będzie 2000-3-14.
    <br>b. Porównaj Data urodzenia minimalna datę urodzenia rzeczywistych. Jeśli data urodzenia minimalna jest wcześniejsza niż data urodzenia użytkownika, zwraca obliczenia **pomocnicza** jako obliczanie wieku grupy.
3. Jeśli **MinorNoConsentRequired** węzeł znajduje się w elemencie kraju, powtarzaj kroki 2a i 2b przy użyciu wartości **MinorNoConsentRequired**. Zwraca dane wyjściowe 2b **MinorNoConsentRequired** Jeśli data urodzenia minimalna jest wcześniejsza niż data urodzenia użytkownika. 
4. Jeśli żadna obliczeń zwrócił wartość true, zwraca obliczenia **dla dorosłych**.

Jeśli aplikacja ma niezawodnie zebranych podana data urodzenia lub kraj danych za pomocą innych metod, aplikacja może używać interfejsu API programu GRAPH do zaktualizowania rekordu użytkownika z tymi informacjami. Na przykład:

- Jeśli użytkownik jest znany jako osoba dorosła, aktualizacja atrybutu katalogu **ageGroup** o wartości **dla dorosłych**.
- Jeśli użytkownik jest znany jako pomocniczy, należy zaktualizować atrybutu katalogu **ageGroup** o wartości **pomocnicza** i ustaw **consentProvidedForMinor** odpowiednio.

Aby uzyskać więcej informacji na temat zbierania danych podana data urodzenia, zobacz [przy użyciu wieku bramkowanie w usłudze Azure AD B2C](basic-age-gating.md).

## <a name="capture-terms-of-use-agreement"></a>Przechwyć Umowa warunki użytkowania

Podczas opracowywania aplikacji zwykle przechwytywania akceptacji użytkownika warunki użytkowania w swoich aplikacjach bez, lub tylko niewielkie udział z katalogu użytkowników.  Jest to możliwe, jednak do użycia usługi Azure AD B2C użytkownika przepływać do zbierania Umowy warunki użytkowania, ograniczanie dostępu, o ile nie otrzymuje akceptacji i wymusić akceptację przyszłe zmiany warunki użytkowania na podstawie daty ostatniej akceptacji i daty versio najnowsze n warunki użytkowania.

**Warunki użytkowania** mogą również obejmować "Oznacza zgodę na udostępnianie danych stron trzecich."  Dodatnią zaakceptowanie tych warunków przez użytkownika może technicznie zebranych jak łączyć lub użytkownik może być akceptuje jeden, a nie inne w zależności od obowiązującym i reguł biznesowych.

W poniższych krokach opisano możliwości zarządzania warunki użytkowania:

1. Rekordów akceptacji warunków użytkowania i daty przy użyciu interfejsu API programu Graph i atrybutów rozszerzonych. Można to zrobić za pomocą obu przepływów użytkownika wbudowanych i niestandardowych. Zaleca się tworzenia i używania **extension_termsOfUseConsentDateTime** i **extension_termsOfUseConsentVersion** atrybutów.
2. Utwórz pola wyboru wymagane zatytułowany "Zaakceptuj warunki użytkowania" i zapisz wynik podczas rejestracji. Można to zrobić za pomocą obu przepływów użytkownika wbudowanych i niestandardowych.
3. Usługa Azure AD B2C przechowuje warunki użytkowania i zgody. Interfejsu API programu Graph może służyć do zapytań dotyczących stanu każdego użytkownika, odczytując atrybutu rozszerzenia używane do rejestrowania odpowiedzi, na przykład czytać **termsOfUseTestUpdateDateTime**. Można to zrobić za pomocą obu przepływów użytkownika wbudowanych i niestandardowych.
4. Wymagaj akceptacji zaktualizowanych warunków użytkowania porównując daty na datę najnowszej wersji warunki użytkowania. To jest możliwe tylko przy użyciu przepływu użytkownika niestandardowego. Użyj atrybutu rozszerzonego **extension_termsOfUseConsentDateTime** i porównać wartości oświadczeń z **termsOfUseTextUpdateDateTime**, jeśli akceptacji jest stara, a następnie wymusić nowej akceptacji w przeciwnym razie własnym potwierdzony ekranu, blokowanie dostępu za pomocą zasad logiki.
5. Wymagaj akceptacji zaktualizowanych warunków użytkowania porównując numer wersji przyjęciu numer ostatniej zaakceptowana wersja. To jest możliwe tylko przy użyciu przepływu użytkownika niestandardowego. Użyj atrybutu rozszerzonego **extension_termsOfUseConsentDateTime** i porównać wartości oświadczeń z **extension_termsOfUseConsentVersion**, jeśli akceptacji jest stara, a następnie wymusić nowej akceptacji w przeciwnym razie własnym potwierdzony ekranu, blokowanie dostępu za pomocą zasad logiki.

Przechwytywanie warunki zgody Użyj widoczne dla użytkownika w następujących scenariuszach:

- Utworzeniem nowego użytkownika. Warunki użytkowania są wyświetlane są przechowywane w wyniku zgody.
- Użytkownik loguje się i już wcześniej zaakceptowane zgody na najnowszej lub aktywny warunki umowy. Warunki użytkowania nie są wyświetlane.
- Użytkownik loguje się i nie zaakceptował już zgody na najnowszej lub aktywny warunki użytkowania. Warunki użytkowania są wyświetlane są przechowywane w wyniku zgody.
- Użytkownik loguje się i została już zaakceptowana zgody na starszą warunki użytkowania, która jest teraz zaktualizować do nowszej wersji. Warunki użytkowania są wyświetlane są przechowywane w wyniku zgody.

Na poniższej ilustracji przedstawiono przepływ zalecane użytkownika:

![Przepływ użytkownika akceptacji](./media/manage-user-access/user-flow.png) 

Poniżej przedstawiono przykład warunków daty/godziny na podstawie użycia zgody w oświadczenia:

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

Poniżej przedstawiono przykład warunków wersji na podstawie użycia zgody w oświadczenia:

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

- Dowiedz się, jak usunąć i eksportowanie danych użytkownika w [zarządzać danymi użytkownika](manage-user-data.md)
