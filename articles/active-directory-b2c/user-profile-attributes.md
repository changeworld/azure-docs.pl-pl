---
title: Atrybuty profilu użytkownika w usłudze Azure Active Directory B2C
description: Dowiedz się więcej o atrybutach typu zasobu użytkownika obsługiwanych przez profil użytkownika katalogu usługi Azure AD B2C. Dowiedz się więcej o wbudowanych atrybutach, rozszerzeniach i sposobie mapowania atrybutów na programie Microsoft Graph.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 3/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e921f0a40f53b1d08831047d1cb89ca26de41402
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80057290"
---
# <a name="user-profile-attributes"></a>Atrybuty profilu użytkownika

Twój profil użytkownika katalogu B2C usługi Azure Active Directory (Azure AD) zawiera wbudowany zestaw atrybutów, takich jak imię, nazwisko, miasto, kod pocztowy i numer telefonu. Profil użytkownika można rozszerzyć o własne dane aplikacji bez konieczności przechowywania danych zewnętrznych. Większość atrybutów, które mogą być używane z profilami użytkowników usługi Azure AD B2C są również obsługiwane przez program Microsoft Graph. W tym artykule opisano obsługiwane atrybuty profilu użytkownika usługi Azure AD B2C. Zwraca również uwagę na te atrybuty, które nie są obsługiwane przez program Microsoft Graph, a także atrybuty programu Microsoft Graph, które nie powinny być używane z usługą Azure AD B2C.

> [!IMPORTANT]
> Nie należy używać atrybutów wbudowanych lub rozszerzeń do przechowywania poufnych danych osobowych, takich jak poświadczenia konta, rządowe numery identyfikacyjne, dane posiadacza karty, dane konta finansowego, informacje o opiece zdrowotnej lub poufne informacje podstawowe.

Można również zintegrować z systemami zewnętrznymi. Na przykład można użyć usługi Azure AD B2C do uwierzytelniania, ale delegować do zewnętrznego zarządzania relacjami z klientami (CRM) lub bazy danych lojalności klientów jako autorytatywnego źródła danych klienta. Aby uzyskać więcej informacji, zobacz rozwiązanie [profilu zdalnego.](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile)

W poniższej tabeli wymieniono atrybuty [typu zasobu użytkownika](https://docs.microsoft.com/graph/api/resources/user) obsługiwane przez profil użytkownika katalogu usługi Azure AD B2C. Zawiera następujące informacje o każdym atrybucie:

- Nazwa atrybutu używana przez usługę Azure AD B2C (po której następuje nazwa programu Microsoft Graph w nawiasach, jeśli jest inna)
- Typ danych atrybutu
- Opis atrybutu
- Jeśli atrybut jest dostępny w witrynie Azure portal
- Jeśli atrybut może być używany w przepływie użytkownika
- Jeśli atrybut może być używany w niestandardowych [zasadach profil techniczny usługi Azure AD](active-directory-technical-profile.md) i w której sekcji (&lt;InputClaims&gt;, &lt;OutputClaims&gt;, lub &lt;PersistedClaims&gt;)

|Nazwa     |Typ     |Opis|Portal Azure|Przepływy użytkowników|Zasady niestandardowe|
|---------|---------|----------|------------|----------|-------------|
|kontoWłasny  |Wartość logiczna|Czy konto użytkownika jest włączone lub wyłączone: **true,** jeśli konto jest włączone, w przeciwnym razie **false**.|Tak|Nie|Utrwalone, wyjście|
|grupa_wiekowa        |Ciąg|Grupa wiekowa użytkownika. Możliwe wartości: null, Undefined, Minor, Adult, NotAdult.|Tak|Nie|Utrwalone, wyjście|
|alternativeSecurityId ([Tożsamości](manage-user-accounts-graph-api.md#identities-property))|Ciąg|Tożsamość pojedynczego użytkownika z zewnętrznego dostawcy tożsamości.|Nie|Nie|Wejście, utrwalone, wyjście|
|alternativeSecurityIds ([tożsamości](manage-user-accounts-graph-api.md#identities-property))|alternatywna kolekcja zabezpieczeńId|Kolekcja tożsamości użytkowników od zewnętrznych dostawców tożsamości.|Nie|Nie|Utrwalone, wyjście|
|city            |Ciąg|Miasto, w którym znajduje się użytkownik. Maksymalna długość 128.|Tak|Tak|Utrwalone, wyjście|
|zgodaProvidedForMinor|Ciąg|Czy zgoda została udzielona osobom niepełnoletnim. Dozwolone wartości: null, przyznane, odrzucone lub nie wymagane.|Tak|Nie|Utrwalone, wyjście|
|country         |Ciąg|Kraj/region, w którym znajduje się użytkownik. Przykład: "USA" lub "UK". Maksymalna długość 128.|Tak|Tak|Utrwalone, wyjście|
|utworzonyDateTime|DateTime|Data utworzenia obiektu użytkownika. Tylko do odczytu.|Nie|Nie|Utrwalone, wyjście|
|tworzenieTyli    |Ciąg|Jeśli konto użytkownika zostało utworzone jako konto lokalne dla dzierżawy B2C usługi Azure Active Directory, wartość to LocalAccount lub nameCoexistence. Tylko do odczytu.|Nie|Nie|Utrwalone, wyjście|
|dateOfBirth     |Data|Data urodzenia.|Nie|Nie|Utrwalone, wyjście|
|department      |Ciąg|Nazwa działu, w którym działa użytkownik. Maksymalna długość 64.|Tak|Nie|Utrwalone, wyjście|
|displayName     |Ciąg|Wyświetlana nazwa użytkownika. Maksymalna długość 256.|Tak|Tak|Utrwalone, wyjście|
|fakssimileTelephoneNumer<sup>1</sup>|Ciąg|Numer telefonu faksu biznesowego użytkownika.|Tak|Nie|Utrwalone, wyjście|
|givenName       |Ciąg|Podane imię (imię) użytkownika. Maksymalna długość 64.|Tak|Tak|Utrwalone, wyjście|
|jobTitle (Tytuł pracy)        |Ciąg|Stanowisko użytkownika. Maksymalna długość 128.|Tak|Tak|Utrwalone, wyjście|
|immutableId     |Ciąg|Identyfikator, który jest zwykle używany dla użytkowników migrowanych z lokalnej usługi Active Directory.|Nie|Nie|Utrwalone, wyjście|
|legalAgeGroupKlasyfikacja|Ciąg|Legalna klasyfikacja grup wiekowych. Tylko do odczytu i obliczone na podstawie ageGroup i zgodyProvidedForMinor właściwości. Dozwolone wartości: null, minorWithOutParentalConsent, minorWithParentalConsent, minorNoParentalConsentRequired, notAdult i adult.|Tak|Nie|Utrwalone, wyjście|
|legalKraj<sup>1</sup>  |Ciąg|do celów prawnych.|Nie|Nie|Utrwalone, wyjście|
|mail (poczta)            |Ciąg|Adres SMTP dla użytkownika, nabob@contoso.comprzykład " ". Tylko do odczytu.|Nie|Nie|Utrwalone, wyjście|
|mailNickName    |Ciąg|Alias poczty dla użytkownika. Maksymalna długość 64.|Nie|Nie|Utrwalone, wyjście|
|telefon komórkowy (telefon komórkowy) |Ciąg|Podstawowy numer telefonu komórkowego dla użytkownika. Maksymalna długość 64.|Tak|Nie|Utrwalone, wyjście|
|netId (ida)           |Ciąg|Identyfikator netto.|Nie|Nie|Utrwalone, wyjście|
|Objectid        |Ciąg|Unikatowy identyfikator (GUID) na całym świecie, który jest unikatowym identyfikatorem użytkownika. Przykład: 12345678-9abc-def0-1234-56789abcde. Tylko do odczytu, niezmienne.|Tylko do odczytu|Tak|Wejście, utrwalone, wyjście|
|inneMaile      |Kolekcja ciągów|Lista dodatkowych adresów e-mail dla użytkownika. Przykład: ["bob@contoso.comRobert@fabrikam.com", " "].|Tak (alternatywny adres e-mail)|Nie|Utrwalone, wyjście|
|hasło        |Ciąg|Hasło do konta lokalnego podczas tworzenia użytkownika.|Nie|Nie|Trwały|
|hasłoPolicja     |Ciąg|Zasady hasła. Jest to ciąg składający się z różnych nazw zasad oddzielonych przecinkiem. czyli "DisablePasswordExpiration, DisableStrongPassword".|Nie|Nie|Utrwalone, wyjście|
|physicalDeliveryOfficeName (officeLokalizacja)|Ciąg|Lokalizacja biura w miejscu prowadzenia działalności użytkownika. Maksymalna długość 128.|Tak|Nie|Utrwalone, wyjście|
|Postalcode      |Ciąg|Kod pocztowy adresu pocztowego użytkownika. Kod pocztowy jest specyficzny dla kraju/regionu użytkownika. W Stanach Zjednoczonych ten atrybut zawiera kod pocztowy. Maksymalna długość 40.|Tak|Nie|Utrwalone, wyjście|
|preferowany Język    |Ciąg|Preferowany język dla użytkownika. Należy przestrzegać kodu ISO 639-1. Przykład: "en-US".|Nie|Nie|Utrwalone, wyjście|
|odświeżtokensValidFromDateTime|DateTime|Wszystkie tokeny odświeżania wystawione przed tym czasem są nieprawidłowe, a aplikacje otrzymają błąd podczas używania nieprawidłowego tokenu odświeżania w celu uzyskania nowego tokenu dostępu. W takim przypadku aplikacja będzie musiała uzyskać nowy token odświeżania, składając żądanie do punktu końcowego autoryzacji. Tylko do odczytu.|Nie|Nie|Dane wyjściowe|
|signInNames ([Tożsamości](manage-user-accounts-graph-api.md#identities-property)) |Ciąg|Unikatowa nazwa logowania użytkownika konta lokalnego dowolnego typu w katalogu. Użyj tego, aby uzyskać użytkownikowi z wartością logowania bez określania typu konta lokalnego.|Nie|Nie|Dane wejściowe|
|signInNames.userName ([Tożsamości](manage-user-accounts-graph-api.md#identities-property)) |Ciąg|Unikatowa nazwa użytkownika konta lokalnego w katalogu. Użyj tego, aby utworzyć lub uzyskać użytkownika z określoną nazwą użytkownika logowania. Określenie tego w PersistedClaims sam podczas operacji patch usunie inne typy signInNames. Jeśli chcesz dodać nowy typ signInNames, należy również utrwalić istniejące signInNames.|Nie|Nie|Wejście, utrwalone, wyjście|
|signInNames.phoneNumber ([Tożsamości](manage-user-accounts-graph-api.md#identities-property)) |Ciąg|Unikatowy numer telefonu użytkownika konta lokalnego w katalogu. Służy do tworzenia lub uzyskania użytkownika z określonym numerem telefonu logowania. Określenie tego w PersistedClaims sam podczas operacji patch usunie inne typy signInNames. Jeśli chcesz dodać nowy typ signInNames, należy również utrwalić istniejące signInNames.|Nie|Nie|Wejście, utrwalone, wyjście|
|signInNames.emailAddress ([Tożsamości](manage-user-accounts-graph-api.md#identities-property))|Ciąg|Unikatowy adres e-mail użytkownika konta lokalnego w katalogu. Służy do tworzenia lub uzyskania użytkownika z określonym adresem e-mail logowania. Określenie tego w PersistedClaims sam podczas operacji patch usunie inne typy signInNames. Jeśli chcesz dodać nowy typ signInNames, należy również utrwalić istniejące signInNames.|Nie|Nie|Wejście, utrwalone, wyjście|
|state           |Ciąg|Stan lub prowincja w adresie użytkownika. Maksymalna długość 128.|Tak|Tak|Utrwalone, wyjście|
|Streetaddress   |Ciąg|Adres ulicy miejsca prowadzenia działalności użytkownika. Maksymalna długość 1024.|Tak|Tak|Utrwalone, wyjście|
|strongAuthentication AlternativePhoneNumber<sup>1</sup>|Ciąg|Pomocniczy numer telefonu użytkownika, używany do uwierzytelniania wieloskładnikowego.|Tak|Nie|Utrwalone, wyjście|
|strongAuthenticationEmailAddress<sup>1</sup>|Ciąg|Adres SMTP dla użytkownika. Przykład:bob@contoso.com" " " Ten atrybut jest używany do logowania się za pomocą zasad dotyczących nazwy użytkownika, do przechowywania adresu e-mail użytkownika. Adres e-mail używany jest następnie w przepływie resetowania hasła.|Tak|Nie|Utrwalone, wyjście|
|strongAuthenticationTelymeracja<sup>1</sup>|Ciąg|Podstawowy numer telefonu użytkownika, używany do uwierzytelniania wieloskładnikowego.|Tak|Nie|Utrwalone, wyjście|
|surname         |Ciąg|Nazwisko użytkownika (nazwisko lub nazwisko). Maksymalna długość 64.|Tak|Tak|Utrwalone, wyjście|
|numer telefonu (pierwsze wejście firmPhones)|Ciąg|Podstawowy numer telefonu miejsca prowadzenia działalności użytkownika.|Tak|Nie|Utrwalone, wyjście|
|userPrincipalName    |Ciąg|Główna nazwa użytkownika (UPN). Nazwa UPN to nazwa logowania w stylu internetowym użytkownika oparta na standardzie internetowym RFC 822. Domena musi znajdować się w kolekcji zweryfikowanych domen dzierżawcy. Ta właściwość jest wymagana podczas tworzenia konta. Niezmienne.|Nie|Nie|Wejście, utrwalone, wyjście|
|usageLocation (korzystanieLokalizacja)   |Ciąg|Wymagane dla użytkowników, którzy otrzymają licencje ze względu na wymóg prawny, aby sprawdzić dostępność usług w krajach. Nie można anulować. Dwuliterowy kod kraju (norma ISO 3166). Przykłady: "US", "JP" i "GB".|Tak|Nie|Utrwalone, wyjście|
|Usertype        |Ciąg|Wartość ciągu, który może służyć do klasyfikowania typów użytkowników w katalogu. Wartość musi być elementem członkowskim. Tylko do odczytu.|Tylko do odczytu|Nie|Utrwalone, wyjście|
|userState (externalUserState)<sup>2</sup>|Ciąg|Tylko dla konta usługi Azure AD B2B wskazuje, czy zaproszenie jest PendingAcceptance lub Zaakceptowane.|Nie|Nie|Utrwalone, wyjście|
|userStateChangedOn (externalUserStateChangeDateTime)<sup>2</sup>|DateTime|Pokazuje sygnaturę czasowa dla ostatniej zmiany właściwości UserState.|Nie|Nie|Utrwalone, wyjście|
|<sup>1.</sup> Nie jest obsługiwana przez program Microsoft Graph<br><sup>2.</sup> Nie należy używać z usługą Azure AD B2C||||||


## <a name="extension-attributes"></a>Atrybuty rozszerzenia

Często musisz utworzyć własne atrybuty, jak w następujących przypadkach:

- Aplikacja skierowana do klienta musi się utrzymywać dla atrybutu, takiego jak **LoyaltyNumber.**
- Dostawca tożsamości ma unikatowy identyfikator użytkownika, taki jak **uniqueUserGUID,** który musi zostać zapisany.
- Niestandardowa podróż użytkownika musi być utrzymywać się dla stanu użytkownika, takiego jak **migrationStatus**.

Usługa Azure AD B2C rozszerza zestaw atrybutów przechowywanych na każdym koncie użytkownika. Atrybuty rozszerzenia [rozszerzają schemat](https://docs.microsoft.com/graph/extensibility-overview#schema-extensions) obiektów użytkownika w katalogu. Atrybuty rozszerzenia można zarejestrować tylko w obiekcie aplikacji, nawet jeśli mogą one zawierać dane dla użytkownika. Atrybut rozszerzenia jest dołączony do aplikacji o nazwie b2c-extensions-app. Nie należy modyfikować tej aplikacji, ponieważ jest ona używana przez usługę Azure AD B2C do przechowywania danych użytkownika. Tę aplikację można znaleźć w obszarze Rejestracje aplikacji usługi Azure Active Directory.

> [!NOTE]
> - Do 100 atrybutów rozszerzenia można zapisać na dowolnym koncie użytkownika.
> - Jeśli aplikacja b2c-extensions-app zostanie usunięta, te atrybuty rozszerzenia są usuwane od wszystkich użytkowników wraz z wszelkimi danymi, które zawierają.
> - Jeśli atrybut rozszerzenia zostanie usunięty przez aplikację, zostanie usunięty ze wszystkich kont użytkowników, a wartości zostaną usunięte.
> - Podstawowa nazwa atrybutu rozszerzenia jest generowana w formacie "Extension_" + Application ID + "_" + Nazwa atrybutu. Na przykład jeśli utworzysz atrybut rozszerzenia LoyaltyNumber, a identyfikator aplikacji b2c-extensions-app to 831374b3-bd50-41bf-aa54-263ec9e050fc, podstawową nazwą atrybutu rozszerzenia będzie: extension_831374b3bd5041bfaa54263ec9e050fc_ Numer Lojalności. Nazwa podstawowa jest używana podczas uruchamiania kwerend interfejsu API programu Graph w celu tworzenia lub aktualizowania kont użytkowników.

Następujące typy danych są obsługiwane podczas definiowania właściwości w rozszerzeniu schematu:

|Typ właściwości |Uwagi  |
|--------------|---------|
|Wartość logiczna    | Możliwe wartości: **prawda** lub **fałsz**. |
|DateTime   | Musi być określony w formacie ISO 8601. Będą przechowywane w utc.   |
|Liczba całkowita    | Wartość 32-bitowa.               |
|Ciąg     | Maksymalnie 256 znaków.     |

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o atrybutach rozszerzeń:
- [Rozszerzenia schematu](https://docs.microsoft.com/graph/extensibility-overview#schema-extensions)
- [Definiowanie atrybutów niestandardowych z przepływem użytkownika](user-flow-custom-attributes.md)
- [Definiowanie atrybutów niestandardowych za pomocą zasad niestandardowych](custom-policy-custom-attributes.md)
