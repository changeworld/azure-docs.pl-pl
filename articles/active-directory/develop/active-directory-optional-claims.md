---
title: Dowiedz się, jak podaj opcjonalny oświadczenia do aplikacji usługi Azure AD | Dokumentacja firmy Microsoft
description: Wskazówki dotyczące dodawania niestandardowych lub dodatkowe oświadczenia języka SAML 2.0 i tokenów Web JSON (JWT) tokeny wystawione przez usługę Azure Active Directory.
documentationcenter: na
author: rwike77
services: active-directory
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/22/2019
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c0e5035331cbe4f54926f0ae60ae0c5c31f6a9a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66119722"
---
# <a name="how-to-provide-optional-claims-to-your-azure-ad-app"></a>Instrukcje: Podaj opcjonalne oświadczenia do aplikacji usługi Azure AD

Ta funkcja jest używana przez deweloperów aplikacji, aby określić, które oświadczenia, że chcą w tokenach wysyłanych do swoich aplikacji. Możesz użyć opcjonalnych oświadczeń:

- Wybierz dodatkowe oświadczenia, które mają zostać objęte tokenów dla aplikacji.
- Zmień zachowanie niektórych oświadczenia, które zwraca tokenów usługi Azure AD.
- Dodaj i dostęp do oświadczenia niestandardowe dla swojej aplikacji.

List standardowa oświadczenia, zobacz [token dostępu](access-tokens.md) i [id_token](id-tokens.md) oświadczeń dokumentacji. 

Gdy opcjonalnych oświadczeń są obsługiwane zarówno w wersji 1.0 i 2.0 tokeny format, a także tokeny SAML, zapewniają większość ich wartości, przy przechodzeniu w wersji 1.0 do wersji 2.0. Jednym z celów [punktu końcowego v2.0 usługi Azure AD](active-directory-appmodel-v2-overview.md) jest mniejsze rozmiary tokenu, aby zapewnić optymalną wydajność przez klientów. W wyniku kilku oświadczenia, wcześniej uwzględnione w dostępu i identyfikator tokenów nie są już dostępne w wersji 2.0 tokenów i musi monit o wpisanie specjalnie dla poszczególnych aplikacji.

**Tabela 1: Możliwości zastosowania**

| Typ konta | Tokenów w wersji 1.0 | Tokenów w wersji 2.0  |
|--------------|---------------|----------------|
| Osobiste konto Microsoft  | ND  | Obsługiwane|
| Konto Azure AD      | Obsługiwane | Obsługiwane |

## <a name="v10-and-v20-optional-claims-set"></a>W wersji 1.0 i opcjonalnych oświadczeń w wersji 2.0

Zestaw oświadczeń opcjonalne, domyślnie dostępne do użycia przez aplikacje są wymienione poniżej. Aby dodać opcjonalny oświadczenia niestandardowe dla swojej aplikacji, zobacz [rozszerzenia katalogów](#configuring-directory-extension-optional-claims)poniżej. Podczas dodawania oświadczeń **token dostępu**, zostaną zastosowane do tokenów dostępu do żądanego *dla* aplikacji (internetowego interfejsu API), nie tych *przez* aplikacji. Dzięki temu niezależnie od tego klienta, uzyskiwanie dostępu do interfejsu API, odpowiednie dane są obecne w tokenie dostępu, których używają do uwierzytelniania względem interfejsu API.

> [!NOTE]
> Większość te oświadczenia mogą być dołączane w tokenów Jwt dla wersji 1.0 i tokenów w wersji 2.0, ale nie tokeny SAML, z wyjątkiem w przypadku, gdy wskazane w kolumnie Typ tokenu. Konsumenckie obsługuje podzbiór tych oświadczeń, który jest oznaczony w kolumnie "Typ użytkownika".  Wiele z oświadczenia na liście nie dotyczą konsumentów (mają więc żaden z dzierżawców `tenant_ctry` nie ma wartości).  

**Tabela 2: W wersji 1.0 i 2.0 opcjonalne zestawu oświadczeń**

| Name (Nazwa)                       |  Opis   | Typ tokenu | Typ użytkownika | Uwagi  |
|----------------------------|----------------|------------|-----------|--------|
| `auth_time`                | Czas, kiedy użytkownik ostatnio uwierzytelniony. Zobacz specyfikacje OpenID Connect.| JWT        |           |  |
| `tenant_region_scope`      | Region zasobu dzierżawy | JWT        |           | |
| `home_oid`                 | Dla użytkowników-gości, identyfikator obiektu użytkownika w dzierżawie macierzystego użytkownika.| JWT        |           | |
| `sid`                      | Identyfikator sesji używany dla sesji użytkownika wylogowania. | JWT        |  Osobistych i kont usługi Azure AD.   |         |
| `platf`                    | Platforma urządzeń    | JWT        |           | Ograniczone do zarządzanych urządzeń, które można sprawdzić typ urządzenia.|
| `verified_primary_email`   | Źródło PrimaryAuthoritativeEmail użytkownika      | JWT        |           |         |
| `verified_secondary_email` | Źródło SecondaryAuthoritativeEmail użytkownika   | JWT        |           |        |
| `enfpolids`                | Identyfikatory wymuszanych zasad. Lista zasad identyfikatorów, które zostały ocenione dla bieżącego użytkownika. | JWT |  |  |
| `vnet`                     | Informacje o specyfikator sieci Wirtualnej. | JWT        |           |      |
| `fwd`                      | Adres IP.| JWT    |   | Dodaje oryginalny adres IPv4 klienta (wewnątrz sieci Wirtualnej) |
| `ctry`                     | Kraj użytkownika | JWT |  | Usługa Azure AD zwraca `ctry` opcjonalnego roszczenia, jeśli jest obecny, a wartość oświadczenia jest kod standardowa kraju dwuliterowych, takich jak FR, JP, SZ i tak dalej. |
| `tenant_ctry`              | Kraj zasobów dzierżawy | JWT | | |
| `xms_pdl`          | Preferowana lokalizacja danych   | JWT | | W przypadku dzierżaw wielu regionów geograficznych jest 3-literowy kod, przedstawiający region geograficzny, w której znajduje się użytkownik. Aby uzyskać więcej informacji, zobacz [program Azure AD Connect dokumentacji dotyczącej Preferowana lokalizacja danych](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-preferreddatalocation).<br/>Na przykład: `APC` dla Azja. |
| `xms_pl`                   | Preferowany język  | JWT ||Użytkownik preferowanego języka, jeśli ustawiona. Źródło ich głównej dzierżawy w scenariuszach dostęp gościa. Sformatowana LL DW ("en-us"). |
| `xms_tpl`                  | Dzierżawy preferowany język| JWT | | Dzierżawy zasobów preferowanego języka, jeśli ustawiona. LL sformatowany ("PL"). |
| `ztdid`                    | Bezobsługowa identyfikator wdrożenia | JWT | | Tożsamość urządzenia używana dla [rozwiązania Windows AutoPilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) |
| `email`                    | Adresy e-mail dla tego użytkownika, jeśli użytkownik ma jeden.  | JWT, SAML | MSA, AAD | Ta wartość jest domyślnie, jeśli użytkownik Gość w dzierżawie.  Dla zarządzanych użytkowników (te wewnątrz dzierżawy) jej należy wystąpić za pomocą tego opcjonalnego roszczenia, lub w wersji 2.0, z zakresu OpenID.  Dla zarządzanych użytkowników, adres e-mail musi być ustawiona w [portalu administracyjnego usługi Office](https://portal.office.com/adminportal/home#/users).| 
| `groups`| Opcjonalnie, formatowanie oświadczenia grupy |JWT, SAML| |Używane w połączeniu z ustawieniem GroupMembershipClaims w [manifest aplikacji](reference-app-manifest.md), które należy określić również. Aby uzyskać szczegółowe informacje, zobacz [grupy oświadczenia](#Configuring-group-optional claims) poniżej. Aby uzyskać więcej informacji na temat oświadczenia grupy zobacz [Konfigurowanie oświadczenia grupy](../hybrid/how-to-connect-fed-group-claims.md)
| `acct`             | Stan konta użytkowników w dzierżawie. | JWT, SAML | | Jeśli użytkownik jest członkiem dzierżawy, wartość jest `0`. Jeśli są one gościa, wartość jest `1`. |
| `upn`                      | Oświadczenie UserPrincipalName. | JWT, SAML  |           | Mimo że to oświadczenie jest automatycznie dołączane, możesz je określić jako opcjonalnego roszczenia, aby dołączyć dodatkowe właściwości, aby zmodyfikować jego zachowanie w przypadku użytkownika gościa.  |

### <a name="v20-optional-claims"></a>Opcjonalne oświadczeń w wersji 2.0

Te oświadczenia są zawsze dołączane w tokenach usługi Azure AD w wersji 1.0, ale nie zostały uwzględnione w tokenów w wersji 2.0, o ile nie jest wymagane. Te oświadczenia są tylko odpowiednie dla elementów Jwt (identyfikator tokenów i tokenów dostępu). 

**Tabela 3: Tylko do wersji 2.0 opcjonalnych oświadczeń**

| Token JWT oświadczeń     | Name (Nazwa)                            | Opis                                | Uwagi |
|---------------|---------------------------------|-------------|-------|
| `ipaddr`      | Adres IP                      | Adres IP klienta, zalogowany z.   |       |
| `onprem_sid`  | Identyfikator zabezpieczeń lokalnych |                                             |       |
| `pwd_exp`     | Czas wygaśnięcia hasła        | Data i godzina jaką hasło wygaśnie. |       |
| `pwd_url`     | Zmień hasło, adres URL             | Adres URL, który użytkownik może odwiedzić, aby zmienić swoje hasło.   |   |
| `in_corp`     | Inside Corporate Network        | Sygnały, jeśli klient jest logowania się z siecią firmową. Jeśli nie jesteś, oświadczenia nie jest uwzględniona.   |  Na podstawie wylogować się z [zaufane adresy IP](../authentication/howto-mfa-mfasettings.md#trusted-ips) ustawień w usłudze MFA.    |
| `nickname`    | Pseudonim                        | Dodatkową nazwę użytkownika, niezależnie od imię lub nazwisko. | 
| `family_name` | Nazwisko                       | Zawiera ostatni nazwę, nazwisko lub nazwę rodziny użytkownika, zgodnie z definicją w obiekcie użytkownika. <br>"family_name":"Miller" | Obsługiwane w zarządzanych kont usług i usługi AAD   |
| `given_name`  | Imię                      | Zawiera pierwszy lub "" Nazwa użytkownika, według stawki ustalonej obiektu user.<br>"given_name": "Piotr"                   | Obsługiwane w zarządzanych kont usług i usługi AAD  |
| `upn`         | Nazwa główna użytkownika | Identyfikator użytkownika, który może być używany z parametrem username_hint.  Nie trwały identyfikator dla użytkownika i nie należy używać do kluczowych danych. | Zobacz [dodatkowe właściwości](#additional-properties-of-optional-claims) poniżej dla konfiguracji oświadczenia. |

### <a name="additional-properties-of-optional-claims"></a>Dodatkowe właściwości opcjonalnych oświadczeń

Aby zmienić sposób, w jaki oświadczenie jest zwracany można skonfigurować kilka opcjonalnych oświadczeń. Te dodatkowe właściwości są najczęściej używane migracji aplikacji lokalnych przy użyciu różnych danych oczekiwania (na przykład `include_externally_authenticated_upn_without_hash` pomaga z klientami, które nie obsługują znaki hash (`#`) nazwę UPN)

**Tabela 4: Wartości dotyczące konfigurowania opcjonalnych oświadczeń**

| Nazwa właściwości  | Nazwa właściwości dodatkowe | Opis |
|----------------|--------------------------|-------------|
| `upn`          |                          | Może służyć dla odpowiedzi SAML i token JWT i tokenów w wersji 1.0 i 2.0. |
|                | `include_externally_authenticated_upn`  | Obejmuje gościa nazwy UPN jako przechowywane w dzierżawie zasobów. Na przykład: `foo_hometenant.com#EXT#@resourcetenant.com` |             
|                | `include_externally_authenticated_upn_without_hash` | Taki sam jak powyżej, z tą różnicą, że oznacza skrót (`#`) są zastępowane znakami podkreślenia (`_`), na przykład `foo_hometenant.com_EXT_@resourcetenant.com` |

#### <a name="additional-properties-example"></a>Przykład dodatkowe właściwości

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

Ten obiekt OptionalClaims powoduje, że identyfikator tokenu zwracana do klienta do uwzględnienia innej nazwy upn z dodatkowych głównej dzierżawy i informacje o zasobach dzierżawy. To spowoduje jedynie zmianę `upn` oświadczenia w tokenie, jeśli użytkownik Gość w dzierżawie, (które używa innego dostawcy tożsamości do uwierzytelniania). 

## <a name="configuring-optional-claims"></a>Konfigurowanie opcjonalnych oświadczeń

Można skonfigurować opcjonalny oświadczenia dla danej aplikacji, modyfikując manifest aplikacji (Zobacz przykład poniżej). Aby uzyskać więcej informacji, zobacz [opis artykułu manifestu aplikacji usługi Azure AD](reference-app-manifest.md).

> [!IMPORTANT]
> Tokeny dostępu są **zawsze** wygenerowane przy użyciu manifest zasobu, a nie klient.  Dlatego w żądaniu `...scope=https://graph.microsoft.com/user.read...` zasób jest wykresu.  W związku z tym token dostępu jest tworzony przy użyciu manifest wykres, a nie manifest klienta.  Zmiana manifestu aplikacji nigdy nie spowoduje, że tokeny dla wykresu, będzie wyglądać inaczej.  Aby sprawdzić, czy Twoje `accessToken` zmiany są stosowane, żądać tokenu dla aplikacji, nie innej aplikacji.  

**Schemat przykładowych:**

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

### <a name="optionalclaims-type"></a>Typ OptionalClaims

Deklaruje opcjonalnych oświadczeń, żądane przez aplikację. Aplikację można skonfigurować opcjonalny oświadczeń ma zostać zwrócone w każdej z trzech rodzajów tokenów (identyfikator tokenu, token, SAML 2 token dostępu) może odbierać z usługi tokenu zabezpieczającego. Aplikację można skonfigurować różne zestawy opcjonalnych oświadczeń, które ma zostać zwrócone w każdego typu tokenu. Właściwość OptionalClaims Jednostka aplikacji jest obiektem OptionalClaims.

**Tabela 5: Właściwości typu OptionalClaims**

| Name (Nazwa)        | Typ                       | Opis                                           |
|-------------|----------------------------|-------------------------------------------------------|
| `idToken`     | Kolekcja (OptionalClaim) | Opcjonalne oświadczenia zwrócone w token JWT Identyfikatora. |
| `accessToken` | Kolekcja (OptionalClaim) | Opcjonalne oświadczenia zwrócone w tokenie dostępu JWT. |
| `saml2Token`  | Kolekcja (OptionalClaim) | Opcjonalne oświadczenia zwrócone w tokenie języka SAML.   |

### <a name="optionalclaim-type"></a>Typ OptionalClaim

Zawiera opcjonalnego roszczenia skojarzone z aplikacją lub ta jednostka usługi. Właściwości idToken, accessToken i saml2Token [OptionalClaims](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) typ jest kolekcją OptionalClaim.
Jeśli jest obsługiwany przez określonych oświadczenia, można również zmodyfikować zachowanie OptionalClaim, korzystając z pola dodatkowe właściwości.

**Tabela 6: Właściwości typu OptionalClaim**

| Name (Nazwa)                 | Typ                    | Opis                                                                                                                                                                                                                                                                                                   |
|----------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | Nazwa opcjonalnego roszczenia.                                                                                                                                                                                                                                                                           |
| `source`               | Edm.String              | Źródło (obiektu katalogu) oświadczenia. Istnieją wstępnie zdefiniowane oświadczeń i zdefiniowanych przez użytkownika z właściwościami rozszerzenia. Jeśli wartość źródłowa jest równa null, oświadczenie jest wstępnie zdefiniowanych opcjonalnego roszczenia. Jeśli wartość źródłowa to użytkownika, wartość właściwości name jest właściwość rozszerzenia z obiektu użytkownika. |
| `essential`            | Edm.Boolean             | Jeśli ma wartość true, oświadczenia, określony przez klienta jest niezbędne do zapewnienia sprawnego autoryzacji umożliwiający określonego zadania, żądane przez użytkownika końcowego. Wartość domyślna to false.                                                                                                             |
| `additionalProperties` | Kolekcja (Edm.String) | Dodatkowe właściwości oświadczenia. Jeśli właściwość istnieje w tej kolekcji, modyfikuje zachowanie opcjonalnego roszczenia określony we właściwości name.                                                                                                                                               |
## <a name="configuring-directory-extension-optional-claims"></a>Konfigurowanie oświadczenia opcjonalne rozszerzenie katalogu

Oprócz zestawu standardowych opcjonalnych oświadczeń można również skonfigurować tokenów, aby uwzględnić rozszerzenia schematu katalogu. Aby uzyskać więcej informacji, zobacz [rozszerzenia schematu katalogu](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions). Ta funkcja jest przydatna do dołączania dodatkowych informacji dotyczących użytkowników, Twoja aplikacja może używać — na przykład, dodatkowe identyfikator lub opcji konfiguracji ważne, ustawionego przez użytkownika. 

> [!Note]
> Rozszerzenia schematu katalogu są funkcją tylko do usługi AAD, więc jeśli aplikacja manifestu żądań niestandardowego rozszerzenia i użytkownika konta Microsoft loguje się do aplikacji, te rozszerzenia nie zostaną zwrócone.

### <a name="directory-extension-formatting"></a>Rozszerzenie katalogu, formatowanie

W przypadku atrybutów rozszerzenia, należy użyć pełnej nazwy rozszerzenia (w formacie: `extension_<appid>_<attributename>`) w manifeście aplikacji. `<appid>` Musi być zgodny z Identyfikatorem aplikacji żądających oświadczeń. 

W ramach tokenu JWT, te oświadczenia będzie emitowane w następującym formacie nazwy: `extn.<attributename>`.

W tokeny SAML te oświadczenia będzie emitowane przy użyciu następującego formatu identyfikatora URI: `http://schemas.microsoft.com/identity/claims/extn.<attributename>`

## <a name="configuring-group-optional-claims"></a>Konfigurowanie opcjonalnych oświadczenia grupy

   > [!NOTE]
   > Możliwość emisji nazwy grup użytkowników i grupy synchronizowane z lokalnej jest publiczna wersja zapoznawcza

W tej sekcji omówiono opcje konfiguracji, w obszarze opcjonalne oświadczenia dotyczące zmieniania atrybutów grupy oświadczenia grupy z domyślny identyfikator obiektu grupy atrybutów synchronizowanych z lokalnej usługi Active Directory Windows
> [!IMPORTANT]
> Zobacz [Konfigurowanie oświadczenia grupy dla aplikacji przy użyciu usługi Azure Active Directory](../hybrid/how-to-connect-fed-group-claims.md) Aby uzyskać więcej informacji, łącznie z ważnymi zastrzeżeniami dla publicznej wersji zapoznawczej oświadczenia grupy z atrybutów w środowisku lokalnym.

1. W portalu usługi -> Azure Active Directory -> Aplikacja rejestracje -> Wybierz aplikacji -> manifestu

2. Włączyć oświadczenia członkostwo grupy, zmieniając groupMembershipClaim

   Prawidłowe wartości to:

   - "Wszystkie"
   - "SecurityGroup"
   - "DistributionList"
   - "DirectoryRole"

   Na przykład:

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   Domyślnie, których identyfikatory obiektów grupy będzie obliczanie w grupie wartości oświadczenia.  Aby zmodyfikować wartość oświadczenia, zawierają atrybuty grupy lokalnej lub zmienić typ oświadczenia roli, należy użyć OptionalClaims konfiguracji w następujący sposób:

3. Ustaw oświadczenia opcjonalny konfiguracji nazwę grupy.

   Jeśli chcesz do grup w tokenie zawierać lokalnych, w których atrybuty grupy usługi AD w sekcji opcjonalnych oświadczeń, określ, której typ tokenu opcjonalnego roszczenia powinny być stosowane do, nazwę opcjonalnego roszczenia, żądane i wszelkie dodatkowe właściwości żądanego.  Mogą być wyświetlane wiele typów tokenu:

   - idToken dla tokenu Identyfikacyjnego OIDC
   - accessToken dla tokenu dostępu OAuth/OIDC
   - Saml2Token tokeny SAML.

   > [!NOTE]
   > Typ Saml2Token dotyczy zarówno SAML1.1 i SAML2.0 tokeny format

   Dla każdego odpowiedniego typu tokenu zmodyfikuj oświadczenia grupy do użycia w sekcji OptionalClaims w manifeście. Schemat OptionalClaims jest w następujący sposób:

   ```json
   {
   "name": "groups",
   "source": null,
   "essential": false,
   "additionalProperties": []
   }
   ```

   | Schemat opcjonalnych oświadczeń | Wartość |
   |----------|-------------|
   | **Nazwa:** | Musi być "groups" |
   | **Źródło:** | Nie jest używany. Pomiń lub określ wartość null |
   | **podstawowe:** | Nie jest używany. Pomiń lub określić wartość false |
   | **additionalProperties:** | Lista właściwości dodatkowych.  Prawidłowe opcje to "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

   W dodatkowe właściwości, tylko jeden "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name" są wymagane.  Jeżeli istnieje więcej niż jeden, pierwszy jest używany i ignorowane przez inne osoby.

   Niektóre aplikacje wymagają grupy informacje o użytkowniku w oświadczenie roli.  Aby zmienić typ oświadczenia z oświadczenia grupy oświadczenie roli, należy dodać "emit_as_roles" do właściwości dodatkowych.  Wartości grupy będzie emitowane w oświadczenie roli.

   > [!NOTE]
   > Jeśli jest używany "emit_as_roles" wszystkie role aplikacji skonfigurowane są przypisane do użytkownika zostanie pojawia się w oświadczenie roli

**Przykłady:** Emituj grup jako nazwy grup w tokenów dostępu protokołu OAuth w formacie dnsDomainName\sAMAccountName

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

Aby emitować nazw grup, które mają zostać zwrócone w formacie netbiosDomain\sAMAccountName, zgodnie z rolami oświadczenia języka SAML i tokeny Identyfikatora OIDC:

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
 }

 ```

## <a name="optional-claims-example"></a>Przykład opcjonalnych oświadczeń

W tej sekcji można opisano scenariusz, aby zobaczyć, jak skorzystać z funkcji opcjonalnych oświadczeń dla aplikacji.
Brak dostępnych wiele opcji do aktualizacji właściwości na konfigurację tożsamości aplikacji, aby włączyć i skonfigurować opcjonalne oświadczeń:
-   Można zmodyfikować manifest aplikacji. W poniższym przykładzie użyje tej metody w celu konfiguracji. Odczyt [opis dokumentu manifestu aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest) pierwszy wprowadzenie do manifestu.
-   Istnieje również możliwość pisania aplikacji, która używa [interfejsu API programu Graph](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api) zaktualizowania aplikacji. [Jednostki i odwołania do typów złożonych](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) w dokumentacji interfejsu API programu Graph przewodnik pomaga w konfigurowaniu opcjonalnych oświadczeń.

**Przykład:** W poniższym przykładzie należy zmodyfikować manifest aplikacji, aby dodawać oświadczenia dostępu, identyfikator i SAML tokenów przeznaczonych dla aplikacji.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po użytkownik został uwierzytelniony, należy wybrać dzierżawy usługi Azure AD, wybierając je z prawym górnym rogu strony.
1. Wybierz **rejestracje aplikacji** z lewej strony.
1. Znajdź aplikację, którą chcesz skonfigurować opcjonalne oświadczeń na liście i kliknij go.
1. Na stronie aplikacji kliknij **manifestu** aby otworzyć Edytor manifestu w tekście. 
1. Można bezpośrednio edytować manifest za pomocą tego edytora. Manifest jest zgodna schematu dla [Jednostka aplikacji](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)i formaty automatyczne raz zapisać manifestu. Nowe elementy zostaną dodane do `OptionalClaims` właściwości.

    ```json
      "optionalClaims": 
      {
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
      }

    ```

    W tym przypadku różne oświadczenia opcjonalne zostały dodane do każdego rodzaju token, który aplikacja może odbierać. Tokeny Identyfikatora będą teraz zawierać nazwę UPN dla użytkowników federacyjnych w pełnej postaci (`<upn>_<homedomain>#EXT#@<resourcedomain>`). Tokeny dostępu, które inne komputery klienckie zażądają tej aplikacji będzie teraz obejmować oświadczenia auth_time. Tokeny SAML będzie teraz zawierać rozszerzenia schematu katalogu skypeId (w tym przykładzie identyfikator aplikacji dla tej aplikacji jest ab603c56068041afb2f6832e2a17e237). Tokeny SAML udostępni Identyfikator Skype jako `extension_skypeId`.

1. Po zakończeniu aktualizowania manifestu kliknij **Zapisz** można zapisać manifestu

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat standardowych oświadczenia dostarczane przez usługę Azure AD.

- [Tokeny Identyfikatora](id-tokens.md)
- [Tokeny dostępu](access-tokens.md)
