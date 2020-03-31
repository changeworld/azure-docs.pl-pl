---
title: Dostarczanie opcjonalnych oświadczeń do aplikacji usługi Azure AD | Azure
titleSuffix: Microsoft identity platform
description: Jak dodać niestandardowe lub dodatkowe oświadczenia do tokenów SAML 2.0 i JSON Web Tokens (JWT) wystawionych przez usługę Azure Active Directory.
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 3/11/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, keyam
ms.custom: aaddev
ms.openlocfilehash: 23d83b59c510f2565b2f66f78dad56c9c9592dd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136521"
---
# <a name="how-to-provide-optional-claims-to-your-azure-ad-app"></a>Jak: Dostarczanie opcjonalnych oświadczeń do aplikacji usługi Azure AD

Deweloperzy aplikacji mogą używać opcjonalnych oświadczeń w swoich aplikacjach usługi Azure AD, aby określić, które oświadczenia chcą w tokenach wysyłanych do ich aplikacji. 

Można użyć opcjonalnych oświadczeń do:

- Wybierz dodatkowe oświadczenia do uwzględnienia w tokenach dla aplikacji.
- Zmień zachowanie niektórych oświadczeń, które usługa Azure AD zwraca w tokenach.
- Dodawanie i uzyskiwanie dostępu do oświadczeń niestandardowych dla aplikacji.

Aby uzyskać listę standardowych oświadczeń, zobacz [token dostępu](access-tokens.md) i [id_token](id-tokens.md) dokumentację oświadczeń. 

Podczas gdy oświadczenia opcjonalne są obsługiwane zarówno w wersji 1.0 i v2.0 tokeny formatu, a także tokeny SAML, zapewniają one większość ich wartości podczas przenoszenia z wersji 1.0 do wersji 2.0. Jednym z celów [punktu końcowego platformy tożsamości firmy Microsoft w wersji 2.0](active-directory-appmodel-v2-overview.md) jest mniejsze rozmiary tokenów, aby zapewnić optymalną wydajność przez klientów. W rezultacie kilka oświadczeń wcześniej zawarte w tokenach dostępu i identyfikatora nie są już obecne w v2.0 tokenów i muszą być wymagane specjalnie dla aplikacji.

**Tabela 1: Możliwość zastosowania**

| Typ konta | żetony w wersji 1.0 | żetony wersji 2.0  |
|--------------|---------------|----------------|
| Osobiste konto Microsoft  | Nie dotyczy  | Obsługiwane |
| Konto Azure AD      | Obsługiwane | Obsługiwane |

## <a name="v10-and-v20-optional-claims-set"></a>Zestaw opcjonalnych oświadczeń wersji 1.0 i v2.0

Poniżej wymieniono zestaw opcjonalnych oświadczeń dostępnych domyślnie dla aplikacji do użycia. Aby dodać niestandardowe oświadczenia opcjonalne dla aplikacji, zobacz [Rozszerzenia katalogów](#configuring-directory-extension-optional-claims), poniżej. Podczas dodawania oświadczeń do **tokenu dostępu**oświadczenia dotyczą tokenów dostępu żądanych *dla* aplikacji (internetowego interfejsu API), a nie oświadczeń wymaganych *przez* aplikację. Bez względu na to, jak klient uzyskuje dostęp do interfejsu API, odpowiednie dane są obecne w tokenie dostępu, który jest używany do uwierzytelniania w interfejsie API.

> [!NOTE]
> Większość tych oświadczeń mogą być zawarte w JWTs dla v1.0 i v2.0 tokenów, ale nie saml tokenów, z wyjątkiem przypadków wymienionych w kolumnie Typ tokenu. Konta konsumenckie obsługują podzbiór tych oświadczeń, oznaczony w kolumnie "Typ użytkownika".  Wiele z wymienionych oświadczeń nie mają zastosowania do użytkowników `tenant_ctry` konsumenckich (nie mają dzierżawy, więc nie ma wartości).  

**Tabela 2: opcjonalny zestaw oświadczeń w wersji 1.0 i v2.0**

| Nazwa                       |  Opis   | Typ tokenu | Typ użytkownika | Uwagi  |
|----------------------------|----------------|------------|-----------|--------|
| `auth_time`                | Czas, w którym użytkownik po raz ostatni uwierzytelnił się. Zobacz specyfikacja OpenID Connect.| Jwt        |           |  |
| `tenant_region_scope`      | Region dzierżawy zasobów | Jwt        |           | |
| `home_oid`                 | Dla użytkowników-gościa identyfikator obiektu użytkownika w dzierżawie domowej użytkownika.| Jwt        |           | |
| `sid`                      | Identyfikator sesji, używany do wylogowywania użytkownika w sesji. | Jwt        |  Konta osobiste i usługi Azure AD.   |         |
| `platf`                    | Platforma urządzeń    | Jwt        |           | Ograniczone do zarządzanych urządzeń, które mogą weryfikować typ urządzenia.|
| `verified_primary_email`   | Pochodzą z primaryauthoritative email użytkownika      | Jwt        |           |         |
| `verified_secondary_email` | Pochodzą z użytkownika SecondaryAuthoritativeEmail   | Jwt        |           |        |
| `enfpolids`                | Wymuszone identyfikatory zasad. Lista identyfikatorów zasad, które zostały ocenione dla bieżącego użytkownika. | Jwt |  |  |
| `vnet`                     | Informacje o specyfikatorze sieci wirtualnej. | Jwt        |           |      |
| `fwd`                      | Adres IP.| Jwt    |   | Dodaje oryginalny adres IPv4 żądającego klienta (gdy wewnątrz sieci wirtualnej) |
| `ctry`                     | Kraj użytkownika | Jwt |  | Usługa Azure `ctry` AD zwraca opcjonalne oświadczenie, jeśli jest obecny, a wartość oświadczenia jest standardowy dwuliterowy kod kraju, takich jak FR, JP, SZ i tak dalej. |
| `tenant_ctry`              | Kraj dzierżawcy zasobów | Jwt | | |
| `xms_pdl`          | Preferowana lokalizacja danych   | Jwt | | W przypadku dzierżawców wielogeograficznych preferowaną lokalizacją danych jest trzyliterowy kod przedstawiający region geograficzny, w którym znajduje się użytkownik. Aby uzyskać więcej informacji, zobacz [dokumentację usługi Azure AD Connect dotyczącą preferowanej lokalizacji danych](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-preferreddatalocation).<br/>Na przykład: `APC` dla Azji i Pacyfiku. |
| `xms_pl`                   | Preferowany przez użytkownika język  | Jwt ||Preferowany język użytkownika, jeśli jest ustawiony. Pochodzą z ich dzierżawy domu, w scenariuszach dostępu gościa. Sformatowany LL-CC ("en-us"). |
| `xms_tpl`                  | Preferowany język dzierżawy| Jwt | | Preferowany język dzierżawy zasobów, jeśli jest ustawiony. Sformatowany LL ("pl"). |
| `ztdid`                    | Identyfikator wdrożenia bez dotykowy | Jwt | | Tożsamość urządzenia używana w [programie Windows AutoPilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) |
| `email`                    | Adresowalna wiadomość e-mail dla tego użytkownika, jeśli użytkownik go ma.  | JWT, SAML | MSA, Azure AD | Ta wartość jest domyślnie uwzględniana, jeśli użytkownik jest gościem w dzierżawie.  Dla użytkowników zarządzanych (użytkowników wewnątrz dzierżawy), należy zażądać za pośrednictwem tego opcjonalnego oświadczenia lub, tylko w wersji 2.0, z zakresem OpenID.  W przypadku użytkowników zarządzanych adres e-mail musi być ustawiony w [portalu administracyjnym pakietu Office](https://portal.office.com/adminportal/home#/users).| 
| `groups`| Opcjonalne formatowanie oświadczeń grupy |JWT, SAML| |Używane w połączeniu z GroupMembershipClaims ustawienie w [manifeście aplikacji](reference-app-manifest.md), które muszą być również ustawione. Szczegółowe informacje można znaleźć w [przypadku roszczeń grupy](#configuring-groups-optional-claims) poniżej. Aby uzyskać więcej informacji na temat oświadczeń grupy, zobacz [Jak skonfigurować oświadczenia grupy](../hybrid/how-to-connect-fed-group-claims.md)
| `acct`             | Stan konta użytkowników w dzierżawie. | JWT, SAML | | Jeśli użytkownik jest członkiem dzierżawy, `0`wartość jest . Jeśli są gośćmi, wartość `1`jest . |
| `upn`                      | Oświadczenie UserPrincipalName. | JWT, SAML  |           | Mimo że to oświadczenie jest automatycznie uwzględniane, można określić je jako opcjonalne oświadczenie, aby dołączyć dodatkowe właściwości, aby zmodyfikować jego zachowanie w przypadku użytkownika gościa.  |

## <a name="v20-specific-optional-claims-set"></a>Zestaw opcjonalnych oświadczeń specyficznych dla wersji 2.0

Te oświadczenia są zawsze uwzględniane w tokenach usługi Azure AD w wersji 1.0, ale nie są uwzględniane w tokenach w wersji 2.0, chyba że jest to wymagane. Te oświadczenia mają zastosowanie tylko do JWTs (tokeny identyfikatorów i tokenów dostępu). 

**Tabela 3: oświadczenia opcjonalne tylko w wersji 2.0**

| Oświadczenie JWT     | Nazwa                            | Opis                                | Uwagi |
|---------------|---------------------------------|-------------|-------|
| `ipaddr`      | Adres IP                      | Adres IP, z na który klient się zalogował.   |       |
| `onprem_sid`  | Lokalny identyfikator zabezpieczeń |                                             |       |
| `pwd_exp`     | Czas wygaśnięcia hasła        | Data wygaśnięcia hasła. |       |
| `pwd_url`     | Zmień adres URL hasła             | Adres URL, który użytkownik może odwiedzić, aby zmienić swoje hasło.   |   |
| `in_corp`     | Wewnątrz sieci firmowej        | Sygnalizuje, że klient loguje się z sieci firmowej. Jeśli tak nie jest, roszczenie nie jest uwzględniane.   |  Na podstawie [zaufanych](../authentication/howto-mfa-mfasettings.md#trusted-ips) ustawień usług IP w umioł.    |
| `nickname`    | Nick                        | Dodatkowa nazwa użytkownika. Pseudonim jest oddzielony od imienia lub nazwiska. Wymaga `profile` zakresu.| 
| `family_name` | Nazwisko                       | Zawiera nazwisko, nazwisko lub nazwisko użytkownika zgodnie z definicją w obiekcie użytkownika. <br>"family_name":"Miller" | Obsługiwane w usługach MSA i usłudze Azure AD. Wymaga `profile` zakresu.   |
| `given_name`  | Imię                      | Zawiera pierwszą lub "podana" nazwę użytkownika, ustawioną na obiekcie użytkownika.<br>"given_name": "Frank"                   | Obsługiwane w usługach MSA i usłudze Azure AD .  Wymaga `profile` zakresu. |
| `upn`         | Nazwa główna użytkownika | Identifer dla użytkownika, który może być używany z parametrem username_hint.  Nie jest trwały identyfikator dla użytkownika i nie powinny być używane do klucza danych. | Zobacz [dodatkowe właściwości](#additional-properties-of-optional-claims) poniżej konfiguracji oświadczenia. Wymaga `profile` zakresu.|

### <a name="additional-properties-of-optional-claims"></a>Dodatkowe właściwości oświadczeń opcjonalnych

Niektóre oświadczenia opcjonalne można skonfigurować tak, aby zmienić sposób zwracania oświadczenia. Te dodatkowe właściwości są najczęściej używane do migracji aplikacji lokalnych z różnymi oczekiwaniami `include_externally_authenticated_upn_without_hash` danych (na przykład pomaga`#`klientom, którzy nie mogą obsługiwać znaczników mieszania ( ) w sieci UPN)

**Tabela 4: Wartości konfigurowania oświadczeń opcjonalnych**

| Nazwa właściwości  | Dodatkowa nazwa właściwości | Opis |
|----------------|--------------------------|-------------|
| `upn`          |                          | Może być używany zarówno dla odpowiedzi SAML i JWT oraz dla tokenów w wersji 1.0 i v2.0. |
|                | `include_externally_authenticated_upn`  | Zawiera niania UPN gościa przechowywane w dzierżawie zasobów. Na przykład: `foo_hometenant.com#EXT#@resourcetenant.com` |             
|                | `include_externally_authenticated_upn_without_hash` | Tak samo jak powyżej, z`#`tą różnicą, że`_`znaki skrótu ( ) są zastępowane podkreśleniami ( ), na przykład`foo_hometenant.com_EXT_@resourcetenant.com` |

#### <a name="additional-properties-example"></a>Przykład dodatkowych właściwości

    ```json
        "optionalClaims": 
         {
             "idToken": [ 
            { 
                      "name": "upn", 
                      "essential": false,
                  "additionalProperties": [ "include_externally_authenticated_upn"]  
                    }
                 ]
        }
    ```

Ten obiekt OptionalClaims powoduje, że token identyfikatora zwrócony do klienta zawiera oświadczenie upn z dodatkowymi informacjami o dzierżawie i dzierżawie zasobów. Oświadczenie `upn` jest zmieniane tylko w tokenie, jeśli użytkownik jest gościem w dzierżawie (który używa innego identyfikatora IDP do uwierzytelniania). 

## <a name="configuring-optional-claims"></a>Konfigurowanie oświadczeń opcjonalnych

> [!IMPORTANT]
> Tokeny dostępu są **zawsze** generowane przy użyciu manifestu zasobu, a nie klienta.  Tak więc `...scope=https://graph.microsoft.com/user.read...` w żądaniu zasób jest interfejsem API programu Microsoft Graph.  W związku z tym token dostępu jest tworzony przy użyciu manifestu interfejsu API programu Microsoft Graph, a nie manifestu klienta.  Zmiana manifestu dla aplikacji nigdy nie spowoduje tokenów dla interfejsu API programu Microsoft Graph wyglądać inaczej.  Aby sprawdzić, czy `accessToken` zmiany są w mocy, zażądaj tokenu dla aplikacji, a nie innej aplikacji.  


Można skonfigurować oświadczenia opcjonalne dla aplikacji za pośrednictwem interfejsu użytkownika lub manifestu aplikacji.

1. Przejdź do [witryny Azure portal](https://portal.azure.com). Wyszukaj i wybierz pozycję **Azure Active Directory**.
1. W sekcji **Zarządzanie** wybierz pozycję **Rejestracje aplikacji**.
1. Wybierz aplikację, dla której chcesz skonfigurować opcjonalne oświadczenia na liście.

**Konfigurowanie oświadczeń opcjonalnych za pośrednictwem interfejsu użytkownika:**

[![Pokazuje, jak skonfigurować oświadczenia opcjonalne przy użyciu interfejsu użytkownika](./media/active-directory-optional-claims/token-configuration.png)](./media/active-directory-optional-claims/token-configuration.png)

1. W sekcji **Zarządzanie** wybierz pozycję **Konfiguracja tokenu (wersja zapoznawcza)**.
2. Wybierz **dodaj oświadczenie opcjonalne**.
3. Wybierz typ tokenu, który chcesz skonfigurować.
4. Wybierz opcjonalne oświadczenia do dodania.
5. Kliknij przycisk **Dodaj**.

**Konfigurowanie oświadczeń opcjonalnych za pomocą manifestu aplikacji:**

[![Pokazuje, jak skonfigurować oświadczenia opcjonalne przy użyciu manifestu aplikacji](./media/active-directory-optional-claims/app-manifest.png)](./media/active-directory-optional-claims/app-manifest.png)

1. W sekcji **Zarządzanie** wybierz pozycję **Manifest**. Otworzy się edytor manifestów opartych na sieci Web, umożliwiający edycję manifestu. Opcjonalnie możesz wybrać pozycję **Pobierz** i edytować manifest lokalnie, a następnie użyć pozycji **Przekaż** w celu ponownego zastosowania go dla aplikacji. Aby uzyskać więcej informacji na temat manifestu aplikacji, zobacz [opis manifestu aplikacji usługi Azure AD .](reference-app-manifest.md)

    Następujący wpis manifestu aplikacji dodaje auth_time, ipaddr i upn opcjonalne oświadczenia do identyfikatora, dostępu i saml tokenów.

    ```json
        "optionalClaims":  
           {
              "idToken": [
                    {
                          "name": "auth_time", 
                          "essential": false
                     }
              ],
              "accessToken": [
                     {
                            "name": "ipaddr", 
                            "essential": false
                      }
              ],
              "saml2Token": [
                      {
                            "name": "upn", 
                            "essential": false
                       },
                       {
                            "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                            "source": "user", 
                            "essential": false
                       }
               ]
           }
    ```

2. Po skończeniu kliknij przycisk **Zapisz**. Teraz określone oświadczenia opcjonalne zostaną uwzględnione w tokenach dla aplikacji.    


### <a name="optionalclaims-type"></a>Typ OptionalClaims

Deklaruje opcjonalne oświadczenia wymagane przez aplikację. Aplikacja może skonfigurować opcjonalne oświadczenia, które mają być zwracane w każdym z trzech typów tokenów (token identyfikatora, token dostępu, token SAML 2), które może odbierać z usługi tokenu zabezpieczającego. Aplikacja może skonfigurować inny zestaw opcjonalnych oświadczeń, które mają być zwracane w każdym typie tokenu. Właściwość OptionalClaims jednostki Application jest obiektem OptionalClaims.

**Tabela 5: Właściwości typu OptionalClaims**

| Nazwa        | Typ                       | Opis                                           |
|-------------|----------------------------|-------------------------------------------------------|
| `idToken`     | Kolekcja (OptionalClaim) | Oświadczenia opcjonalne zwrócone w tokenie JWT ID. |
| `accessToken` | Kolekcja (OptionalClaim) | Oświadczenia opcjonalne zwrócone w tokenie dostępu JWT. |
| `saml2Token`  | Kolekcja (OptionalClaim) | Oświadczenia opcjonalne zwrócone w tokenie SAML.   |

### <a name="optionalclaim-type"></a>Opcjonalny typclaim

Zawiera opcjonalne oświadczenie skojarzone z aplikacją lub jednostką usługi. Właściwości idToken, accessToken i saml2Token typu [OptionalClaims](https://docs.microsoft.com/graph/api/resources/optionalclaims?view=graph-rest-1.0) jest kolekcją OptionalClaim.
Jeśli jest obsługiwany przez określone oświadczenie, można również zmodyfikować zachowanie OptionalClaim przy użyciu AdditionalProperties pola.

**Tabela 6: Właściwości typu OptionalClaim**

| Nazwa                 | Typ                    | Opis                                                                                                                                                                                                                                                                                                   |
|----------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | Nazwa oświadczenia opcjonalnego.                                                                                                                                                                                                                                                                           |
| `source`               | Edm.String              | Źródło (obiekt katalogu) oświadczenia. Istnieją wstępnie zdefiniowane oświadczenia i oświadczenia zdefiniowane przez użytkownika z właściwości rozszerzenia. Jeśli wartość źródła jest null, oświadczenie jest wstępnie zdefiniowane oświadczenie opcjonalne. Jeśli wartość źródło jest użytkownikiem, wartość we właściwości name jest właściwością rozszerzenia z obiektu użytkownika. |
| `essential`            | Edm.Boolean             | Jeśli wartość jest true, oświadczenie określone przez klienta jest konieczne, aby zapewnić płynne środowisko autoryzacji dla określonego zadania wymaganego przez użytkownika końcowego. Wartość domyślna to false.                                                                                                             |
| `additionalProperties` | Kolekcja (Edm.String) | Dodatkowe właściwości oświadczenia. Jeśli właściwość istnieje w tej kolekcji, modyfikuje zachowanie opcjonalnego oświadczenia określonego we właściwości name.                                                                                                                                               |
## <a name="configuring-directory-extension-optional-claims"></a>Konfigurowanie opcjonalnych oświadczeń rozszerzenia katalogu

Oprócz standardowego zestawu oświadczeń opcjonalnych można również skonfigurować tokeny w celu uwzględnienia rozszerzeń. Aby uzyskać więcej informacji, zobacz [dokumentację rozszerzenia Microsoft GraphProperty](https://docs.microsoft.com/graph/api/resources/extensionproperty?view=graph-rest-1.0) — należy pamiętać, że schemat i otwarte rozszerzenia nie są obsługiwane przez oświadczenia opcjonalne, tylko rozszerzenia katalogu w stylu AAD-Graph. Ta funkcja jest przydatna do dołączania dodatkowych informacji o użytkowniku, których aplikacja może używać — na przykład dodatkowego identyfikatora lub ważnej opcji konfiguracji ustawionej przez użytkownika. Zobacz przykład u dołu tej strony.

> [!NOTE]
> - Rozszerzenia schematu katalogu są funkcją tylko usługi Azure AD, więc jeśli manifest aplikacji żąda rozszerzenia niestandardowego, a użytkownik MSA loguje się do aplikacji, te rozszerzenia nie zostaną zwrócone.

### <a name="directory-extension-formatting"></a>Formatowanie rozszerzenia katalogu

Podczas konfigurowania opcjonalnych oświadczeń rozszerzenia katalogu przy użyciu manifestu aplikacji należy `extension_<appid>_<attributename>`użyć pełnej nazwy rozszerzenia (w formacie: ). Musi `<appid>` odpowiadać identyfikatorowi wniosku wniosku żądającego roszczenia. 

W ramach JWT oświadczenia te będą emitowane w `extn.<attributename>`następującym formacie nazwy: .

W ramach tokenów SAML te oświadczenia będą emitowane w następującym formacie URI:`http://schemas.microsoft.com/identity/claims/extn.<attributename>`

## <a name="configuring-groups-optional-claims"></a>Konfigurowanie grup oświadczeń opcjonalnych

   > [!NOTE]
   > Możliwość emitowania nazw grup dla użytkowników i grup zsynchronizowanych z lokalnie jest publiczna wersja zapoznawcza.

W tej sekcji opisano opcje konfiguracji w obszarze oświadczenia opcjonalne dotyczące zmiany atrybutów grupy używanych w oświadczeń grupy z domyślnego identyfikatora obiektu grupy na atrybuty zsynchronizowane z lokalnej usługi Windows Active Directory. Można skonfigurować grupy opcjonalne oświadczenia dla aplikacji za pośrednictwem interfejsu użytkownika lub manifestu aplikacji.

> [!IMPORTANT]
> Aby uzyskać więcej informacji, w tym ważne zastrzeżenia dotyczące publicznej wersji zapoznawczej oświadczeń grupy z atrybutów lokalnych, zobacz [Konfigurowanie oświadczeń grupy dla aplikacji z usługą Azure AD](../hybrid/how-to-connect-fed-group-claims.md).

**Konfigurowanie grup opcjonalnych oświadczeń za pośrednictwem interfejsu użytkownika:**
1. Logowanie się do [witryny Azure portal](https://portal.azure.com)
1. Po uwierzytelnieniu wybierz dzierżawę usługi Azure AD, wybierając ją w prawym górnym rogu strony.
1. Wybieranie **usługi Azure Active Directory** z menu po lewej stronie
1. W sekcji **Zarządzanie** wybierz pozycję **Rejestracje aplikacji**
1. Wybierz aplikację, dla której chcesz skonfigurować oświadczenia opcjonalne na liście
1. W sekcji **Zarządzanie** wybierz **konfigurację tokenu (wersja zapoznawcza)**
2. Wybierz **oświadczenie Dodaj grupy**
3. Wybierz typy grup do zwrócenia **(Wszystkie grupy,** **Grupa zabezpieczeń**lub **Zarolę katalogu).** Opcja **Wszystkie grupy** obejmuje **securitygroup**, **DirectoryRole**i **DistributionList**
4. Opcjonalnie: kliknij na określone właściwości typu tokenu, aby zmodyfikować wartość oświadczenia grup, aby zawierać atrybuty grupy lokalnej lub zmienić typ oświadczenia na rolę
5. Kliknij **przycisk Zapisz**

**Konfigurowanie grup opcjonalnych oświadczeń za pomocą manifestu aplikacji:**
1. Logowanie się do [witryny Azure portal](https://portal.azure.com)
1. Po uwierzytelnieniu wybierz dzierżawę usługi Azure AD, wybierając ją w prawym górnym rogu strony.
1. Wybieranie **usługi Azure Active Directory** z menu po lewej stronie
1. Wybierz aplikację, dla której chcesz skonfigurować oświadczenia opcjonalne na liście
1. W sekcji **Zarządzanie** wybierz pozycję **Manifest**
3. Dodaj następujący wpis za pomocą edytora manifestów:

   Prawidłowe wartości to:

   - "Wszystkie" (ta opcja obejmuje SecurityGroup, DirectoryRole i DistributionList)
   - "Grupa zabezpieczeń"
   - "DirectoryRole"

   Przykład:

    ```json
        "groupMembershipClaims": "SecurityGroup"
    ```

   Domyślnie identyfikatory obiektów grupy będą emitowane w wartości oświadczenia grupy.  Aby zmodyfikować wartość oświadczenia, która ma zawierać atrybuty grupy lokalnej lub zmienić typ oświadczenia na rolę, należy użyć konfiguracji OptionalClaims w następujący sposób:

3. Ustawianie opcjonalnych oświadczeń konfiguracji nazwy grupy.

   Jeśli chcesz, aby grupy w tokenie zawierały lokalne atrybuty grupy AD w sekcji oświadczeń opcjonalnych, określ, do którego typu tokenu należy zastosować opcjonalne oświadczenie, nazwę żądanego oświadczenia opcjonalnego i wszelkie wymagane dodatkowe właściwości.  Można wymienić wiele typów tokenów:

   - idToken dla tokenu identyfikatora OIDC
   - accessToken dla tokenu dostępu OAuth
   - Saml2Token dla tokenów SAML.

   > [!NOTE]
   > Typ Saml2Token ma zastosowanie zarówno do tokenów w formacie SAML1.1, jak i SAML2.0

   Dla każdego odpowiedniego typu tokenu zmodyfikuj oświadczenia grup, aby użyć sekcji OptionalClaims w manifeście. Schemat OptionalClaims jest następujący:

    ```json
       {
       "name": "groups",
       "source": null,
       "essential": false,
       "additionalProperties": []
       }
    ```

   | Schemat oświadczeń opcjonalnych | Wartość |
   |----------|-------------|
   | **Nazwa:** | Musi być "grupy" |
   | **Źródła:** | Nie używany. Pomiń lub określ wartość null |
   | **Niezbędne:** | Nie używany. Pomiń lub określ fałsz |
   | **additionalProperties:** | Lista dodatkowych właściwości.  Prawidłowe opcje to "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

   W additionalProperties wymagany jest tylko jeden z "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name".  Jeśli więcej niż jeden jest obecny, pierwszy jest używany, a inne ignorowane.

   Niektóre aplikacje wymagają informacji o grupie o użytkowniku w żądaniu roli.  Aby zmienić typ oświadczenia z oświadczenia grupy na oświadczenie roli, dodaj "emit_as_roles" do dodatkowych właściwości.  Wartości grupy zostaną wyemitowane w żądaniu roli.

   > [!NOTE]
   > Jeśli "emit_as_roles" jest używany wszystkie role aplikacji skonfigurowane, że użytkownik jest przypisany nie pojawi się w oświadczeń roli

**Przykłady:**

1) Emitowanie grup jako nazw grup w tokenach dostępu OAuth w formacie dnsDomainName\sAMAccountName

    
    **Konfiguracja interfejsu użytkownika:**

    [![Pokazuje, jak skonfigurować oświadczenia opcjonalne przy użyciu interfejsu użytkownika](./media/active-directory-optional-claims/groups-example-1.png)](./media/active-directory-optional-claims/groups-example-1.png)


    **Wpis manifestu aplikacji:**
    ```json
        "optionalClaims": {
            "accessToken": [{
            "name": "groups",
            "additionalProperties": ["dns_domain_and_sam_account_name"]
            }]
        }
    ```

 
    
2) Emituj nazwy grup, które mają być zwracane w formacie netbiosDomain\sAMAccountName jako oświadczenie ról w tokenach IDENTYFIKATORÓW SAML i OIDC

    **Konfiguracja interfejsu użytkownika:**

    [![Pokazuje, jak skonfigurować oświadczenia opcjonalne przy użyciu interfejsu użytkownika](./media/active-directory-optional-claims/groups-example-2.png)](./media/active-directory-optional-claims/groups-example-2.png)

    **Wpis manifestu aplikacji:**
    
    ```json
        "optionalClaims": {
        "saml2Token": [{
            "name": "groups",
            "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
        }],
        "idToken": [{
            "name": "groups",
            "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
        }]
    ``` 
     

## <a name="optional-claims-example"></a>Przykład oświadczeń opcjonalnych

W tej sekcji można przejść przez scenariusz, aby zobaczyć, jak można użyć funkcji oświadczeń opcjonalnych dla aplikacji.
Istnieje wiele opcji aktualizacji właściwości w konfiguracji tożsamości aplikacji, aby włączyć i skonfigurować oświadczenia opcjonalne:
-    Można użyć **interfejsu użytkownika konfiguracji tokenu (podglądu)** (patrz przykład poniżej)
-    Możesz użyć **manifestu** (patrz przykład poniżej). Przeczytaj [opis dokumentu manifestu aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest) najpierw wprowadzenie do manifestu.
-   Istnieje również możliwość zapisu aplikacji, która używa [interfejsu API programu Microsoft Graph](https://docs.microsoft.com/graph/use-the-api?context=graph%2Fapi%2F1.0&view=graph-rest-1.0) do aktualizowania aplikacji. Typ [OptionalClaims](https://docs.microsoft.com/graph/api/resources/optionalclaims?view=graph-rest-1.0) w przewodniku odwołania do interfejsu API programu Microsoft Graph może pomóc w konfigurowaniu oświadczeń opcjonalnych.

**Przykład:** W poniższym przykładzie użyjesz **interfejsu użytkownika konfiguracji tokenu (podglądu)** i **manifestu,** aby dodać opcjonalne oświadczenia do tokenów dostępu, identyfikatora i SAML przeznaczonych dla aplikacji. Różne oświadczenia opcjonalne zostaną dodane do każdego typu tokenu, który aplikacja może odbierać:
-    Tokeny identyfikatorów będą teraz zawierać numery UPN dla użytkowników`<upn>_<homedomain>#EXT#@<resourcedomain>`federowanych w pełnej formie ( ).
-    Tokeny dostępu, których inni klienci żądają dla tej aplikacji, będą teraz zawierać auth_time oświadczenia
-    Tokeny SAML będą teraz zawierać rozszerzenie schematu katalogu skypeId (w tym przykładzie identyfikator aplikacji dla tej aplikacji to ab603c56068041afb2f6832e2a17e237). Tokeny SAML będą udostępniać identyfikator `extension_skypeId`Skype jako .

**Konfiguracja interfejsu użytkownika:**

1. Logowanie się do [witryny Azure portal](https://portal.azure.com)

1. Po uwierzytelnieniu wybierz dzierżawę usługi Azure AD, wybierając ją w prawym górnym rogu strony.

1. Wybierz **usługę Azure Active Directory** z menu po lewej stronie.

1. W sekcji **Zarządzanie** wybierz pozycję **Rejestracje aplikacji**.

1. Znajdź aplikację, dla której chcesz skonfigurować opcjonalne oświadczenia na liście, i kliknij ją.

1. W sekcji **Zarządzanie** kliknij pozycję **Konfiguracja tokenu (wersja zapoznawcza)**.

1. Wybierz **dodaj oświadczenie opcjonalne**, wybierz typ tokenu **identyfikatora,** wybierz **pozycję UPN** z listy oświadczeń, a następnie kliknij przycisk **Dodaj**.

1. Wybierz **pozycję Dodaj oświadczenie opcjonalne**, wybierz typ tokenu programu **Access,** wybierz **auth_time** z listy oświadczeń, a następnie kliknij przycisk **Dodaj**.

1. Na ekranie Przegląd konfiguracji tokenu kliknij ikonę ołówka obok **upn**, kliknij przełącznik **Zewnętrznie uwierzytelniony,** a następnie kliknij przycisk **Zapisz**.

1. Wybierz **pozycję Dodaj oświadczenie opcjonalne**, wybierz typ tokenu **SAML,** wybierz **extn.skypeID** z listy oświadczeń (dotyczy tylko wtedy, gdy utworzono obiekt użytkownika usługi Azure AD o nazwie skypeID), a następnie kliknij przycisk **Dodaj**.

    [![Pokazuje, jak skonfigurować oświadczenia opcjonalne przy użyciu interfejsu użytkownika](./media/active-directory-optional-claims/token-config-example.png)](./media/active-directory-optional-claims/token-config-example.png)

**Konfiguracja manifestu:**
1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Po uwierzytelnieniu wybierz dzierżawę usługi Azure AD, wybierając ją w prawym górnym rogu strony.
1. Wybierz **usługę Azure Active Directory** z menu po lewej stronie.
1. Znajdź aplikację, dla której chcesz skonfigurować opcjonalne oświadczenia na liście, i kliknij ją.
1. W sekcji **Zarządzanie** kliknij pozycję **Manifest,** aby otworzyć edytor manifestów wbudowanych.
1. Manifest można edytować bezpośrednio za pomocą tego edytora. Manifest następuje schemat dla [Application jednostki](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)i automatycznie formatuje manifest po zapisaniu. Do `OptionalClaims` obiektu zostaną dodane nowe elementy.

    ```json
            "optionalClaims": {
                "idToken": [ 
                     { 
                        "name": "upn", 
                        "essential": false, 
                        "additionalProperties": [ "include_externally_authenticated_upn"]  
                     }
                     ],
                "accessToken": [ 
                      {
                        "name": "auth_time", 
                        "essential": false
                      }
                     ],
            "saml2Token": [ 
                  { 
                    "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                    "source": "user", 
                    "essential": true
                  }
                 ]
        ``` 


1. When you're finished updating the manifest, click **Save** to save the manifest.

## Next steps

Learn more about the standard claims provided by Azure AD.

- [ID tokens](id-tokens.md)
- [Access tokens](access-tokens.md)
