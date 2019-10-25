---
title: Dowiedz się, jak dostarczać opcjonalne oświadczenia do aplikacji usługi Azure AD
titleSuffix: Microsoft identity platform
description: Przewodnik dodawania niestandardowych lub dodatkowych oświadczeń do tokenów tokenów sieci Web SAML 2,0 i JSON (JWT) wystawionych przez Azure Active Directory.
documentationcenter: na
author: rwike77
services: active-directory
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/03/2019
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b74e680979ccbcc94f8a49e993c6d64797ab80b1
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803412"
---
# <a name="how-to-provide-optional-claims-to-your-azure-ad-app"></a>Instrukcje: dostarczanie opcjonalnych oświadczeń do aplikacji usługi Azure AD

Deweloperzy aplikacji mogą używać opcjonalnych oświadczeń w swoich aplikacjach usługi Azure AD, aby określić, które oświadczenia mają być wysyłane do aplikacji. 

Opcjonalne oświadczenia można używać do:

- Wybierz dodatkowe oświadczenia do uwzględnienia w tokenach aplikacji.
- Zmień zachowanie niektórych oświadczeń zwracanych przez usługę Azure AD w tokenach.
- Dodawanie niestandardowych oświadczeń do aplikacji i uzyskiwanie do nich dostępu.

Listę oświadczeń standardowych można znaleźć w dokumentacji [token dostępu](access-tokens.md) i oświadczenia [id_token](id-tokens.md) . 

Chociaż opcjonalne oświadczenia są obsługiwane zarówno w tokenach w formacie v 1.0, jak i 2.0, jak również w przypadku tokenów języka SAML, zapewniają większość wartości podczas przechodzenia z wersji 1.0 do wersji 2.0. Jednym z celów [punktu końcowego platformy tożsamości w wersji 2.0 firmy Microsoft](active-directory-appmodel-v2-overview.md) jest mniejszy rozmiar tokenu, aby zapewnić optymalną wydajność przez klientów. W związku z tym kilka oświadczeń wcześniej uwzględnionych w tokenach dostępu i identyfikatorów nie jest już obecny w tokenach v 2.0 i musi być poproszony o odszukanie poszczególnych aplikacji.

**Tabela 1: możliwość zastosowania**

| Typ konta | tokeny v 1.0 | tokeny v 2.0  |
|--------------|---------------|----------------|
| konto Microsoft osobiste  | ND  | Obsługiwane |
| Konto Azure AD      | Obsługiwane | Obsługiwane |

## <a name="v10-and-v20-optional-claims-set"></a>zestaw oświadczeń opcjonalnych 1.0 i v 2.0

Zestaw opcjonalnych oświadczeń dostępnych domyślnie dla aplikacji do użycia znajduje się poniżej. Aby dodać niestandardowe oświadczenia opcjonalne dla aplikacji, zobacz [rozszerzenia katalogów](#configuring-directory-extension-optional-claims)poniżej. W przypadku dodawania oświadczeń do **tokenu dostępu**zostanie to zastosowane do tokenów dostępu żądanych *dla* aplikacji (internetowego interfejsu API), a nie *dla aplikacji* . Dzięki temu nie ma znaczenia, że klient uzyskuje dostęp do interfejsu API, odpowiednie dane znajdują się w tokenie dostępu używanym do uwierzytelniania w interfejsie API.

> [!NOTE]
> Większość tych oświadczeń można uwzględnić w JWTs dla tokenów v 1.0 i v 2.0, ale nie tokenów SAML, z wyjątkiem sytuacji, w których zaznaczono w kolumnie Typ tokenu. Konta konsumentów obsługują podzbiór tych oświadczeń, które są oznaczone w kolumnie Typ użytkownika.  Wiele z wymienionych oświadczeń nie dotyczy użytkowników indywidualnych (nie mają dzierżawy, więc `tenant_ctry` nie ma żadnej wartości).  

**Tabela 2: zestaw opcjonalnych zestawów zgłoszeń 1.0 i v 2.0**

| Nazwa                       |  Opis   | Typ tokenu | Typ użytkownika | Uwagi  |
|----------------------------|----------------|------------|-----------|--------|
| `auth_time`                | Godzina ostatniego uwierzytelnienia użytkownika. Zobacz OpenID Connect Connect spec.| JWT        |           |  |
| `tenant_region_scope`      | Region dzierżawy zasobów | JWT        |           | |
| `home_oid`                 | Dla użytkowników-Gości, identyfikator obiektu użytkownika w dzierżawie głównej użytkownika.| JWT        |           | |
| `sid`                      | Identyfikator sesji używany do wylogowywania użytkownika na sesję. | JWT        |  Konta osobiste i usługi Azure AD.   |         |
| `platf`                    | Platforma urządzeń    | JWT        |           | Ograniczone do zarządzanych urządzeń, które mogą weryfikować typ urządzenia.|
| `verified_primary_email`   | Źródłem z PrimaryAuthoritativeEmail użytkownika      | JWT        |           |         |
| `verified_secondary_email` | Źródłem z SecondaryAuthoritativeEmail użytkownika   | JWT        |           |        |
| `enfpolids`                | Wymuszane identyfikatory zasad. Lista identyfikatorów zasad, które zostały ocenione dla bieżącego użytkownika. | JWT |  |  |
| `vnet`                     | Informacje o specyfikatorze sieci wirtualnej. | JWT        |           |      |
| `fwd`                      | Adres IP.| JWT    |   | Dodaje oryginalny adres IPv4 klienta żądającego (w sieci wirtualnej) |
| `ctry`                     | Kraj użytkownika | JWT |  | Usługa Azure AD zwraca `ctry` opcjonalne, jeśli jest obecny, a wartość zgłoszenia to standardowy dwuliterowy kod kraju, taki jak FR, JP, SZ i tak dalej. |
| `tenant_ctry`              | Kraj dzierżawy zasobu | JWT | | |
| `xms_pdl`          | Preferowana lokalizacja danych   | JWT | | W przypadku dzierżaw geograficznych jest to 3-literowy kod pokazujący region geograficzny, w którym znajduje się użytkownik. Aby uzyskać więcej informacji, zobacz [dokumentację Azure AD Connect dotyczącą preferowanej lokalizacji danych](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-preferreddatalocation).<br/>Na przykład: `APC` Azja i Pacyfik. |
| `xms_pl`                   | Preferowany język użytkownika  | JWT ||Preferowany język użytkownika, jeśli jest ustawiony. Pochodzący od swojej dzierżawy domowej w scenariuszach dostępu gościa. Sformatowane — DW ("pl-US"). |
| `xms_tpl`                  | Preferowany język dzierżawy| JWT | | Preferowany język dzierżawy zasobu, jeśli został ustawiony. Sformatowane w szystkie ("en"). |
| `ztdid`                    | Identyfikator wdrożenia bez dotknięcia | JWT | | Tożsamość urządzenia używana na potrzeby [Autopilotażu systemu Windows](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) |
| `email`                    | Adres e-mail dla tego użytkownika, jeśli użytkownik go ma.  | JWT, SAML | MSA, Azure AD | Ta wartość jest uwzględniana domyślnie, jeśli użytkownik jest gościem w dzierżawie.  W przypadku użytkowników zarządzanych (w ramach dzierżawy) należy zażądać ich przez to opcjonalne żądanie lub tylko w wersji 3.0, z zakresem OpenID Connect.  W przypadku użytkowników zarządzanych adres e-mail musi być ustawiony w [portalu administracyjnym pakietu Office](https://portal.office.com/adminportal/home#/users).| 
| `groups`| Opcjonalne formatowanie oświadczeń grupy |JWT, SAML| |Używany w połączeniu z ustawieniem GroupMembershipClaims w [manifeście aplikacji](reference-app-manifest.md), który musi być również ustawiony. Aby uzyskać szczegółowe informacje, zobacz poniższe [oświadczenia grupy](#Configuring-group-optional claims) . Aby uzyskać więcej informacji na temat oświadczeń grup [, zobacz Jak skonfigurować oświadczenia grupy](../hybrid/how-to-connect-fed-group-claims.md)
| `acct`             | Stan konta użytkowników w dzierżawie. | JWT, SAML | | Jeśli użytkownik jest członkiem dzierżawy, wartość jest `0`. Jeśli jest gościem, wartość jest `1`. |
| `upn`                      | Element UserPrincipalName. | JWT, SAML  |           | Chociaż to zgłoszenie jest automatycznie dołączane, można je określić jako opcjonalne, aby dołączyć dodatkowe właściwości, aby zmodyfikować jego zachowanie w przypadku użytkownika-gościa.  |

### <a name="v20-optional-claims"></a>v 2.0 — opcjonalne oświadczenia

Te oświadczenia są zawsze uwzględniane w tokenach usługi Azure AD w wersji 1.0, ale nie są uwzględniane w tokenach v 2.0, chyba że jest to wymagane. Te oświadczenia mają zastosowanie tylko do JWTs (tokeny ID i tokeny dostępu). 

**Tabela 3: v 2.0 — tylko opcjonalne oświadczenia**

| Claim JWT     | Nazwa                            | Opis                                | Uwagi |
|---------------|---------------------------------|-------------|-------|
| `ipaddr`      | Adres IP                      | Adres IP, z którego zalogował się klient.   |       |
| `onprem_sid`  | Lokalny identyfikator zabezpieczeń |                                             |       |
| `pwd_exp`     | Czas wygaśnięcia hasła        | Data i godzina wygaśnięcia hasła. |       |
| `pwd_url`     | Zmień adres URL hasła             | Adres URL, który użytkownik może odwiedzić, aby zmienić hasło.   |   |
| `in_corp`     | Wewnątrz sieci firmowej        | Sygnalizuje, czy klient loguje się z sieci firmowej. W przeciwnym razie oświadczenia nie są uwzględniane.   |  Na podstawie ustawień [zaufanych adresów IP](../authentication/howto-mfa-mfasettings.md#trusted-ips) w usłudze MFA.    |
| `nickname`    | Pseudonim                        | Dodatkowa nazwa użytkownika, oddzielona od imię lub nazwisko. | 
| `family_name` | Nazwisko                       | Zawiera nazwisko, nazwisko lub nazwę rodziny użytkownika, zgodnie z definicją w obiekcie użytkownika. <br>"family_name": "Miller" | Obsługiwane w usłudze MSA i usłudze Azure AD   |
| `given_name`  | Imię                      | Określa imię i nazwisko użytkownika, zgodnie z ustawieniem obiektu użytkownika.<br>"given_name": "Piotr"                   | Obsługiwane w usłudze MSA i usłudze Azure AD  |
| `upn`         | Nazwa główna użytkownika | Identyfikator dla użytkownika, którego można użyć z parametrem username_hint.  Nie jest to trwały identyfikator użytkownika i nie należy go używać do kluczowych danych. | Zapoznaj się z [dodatkowymi właściwościami](#additional-properties-of-optional-claims) poniżej w celu skonfigurowania żądania. |

### <a name="additional-properties-of-optional-claims"></a>Dodatkowe właściwości oświadczeń opcjonalnych

Niektóre opcjonalne oświadczenia można skonfigurować w celu zmiany sposobu zwracania oświadczenia. Te dodatkowe właściwości są najczęściej używane do migracji aplikacji lokalnych z różnymi oczekiwaniami danych (na przykład `include_externally_authenticated_upn_without_hash` pomaga klientom, którzy nie mogą obsłużyć znaków hash (`#`) w UPN).

**Tabela 4: wartości konfiguracji opcjonalnych oświadczeń**

| Nazwa właściwości  | Dodatkowa nazwa właściwości | Opis |
|----------------|--------------------------|-------------|
| `upn`          |                          | Może być używany w przypadku odpowiedzi SAML i JWT oraz dla tokenów v 1.0 i v 2.0. |
|                | `include_externally_authenticated_upn`  | Zawiera nazwę UPN gościa przechowywaną w dzierżawie zasobów. Na przykład: `foo_hometenant.com#EXT#@resourcetenant.com` |             
|                | `include_externally_authenticated_upn_without_hash` | Tak samo jak powyżej, z tą różnicą, że znaki hash (`#`) są zastępowane znakami podkreślenia (`_`), na przykład `foo_hometenant.com_EXT_@resourcetenant.com` |

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

Ten obiekt OptionalClaims powoduje, że token identyfikatora zwracany do klienta zawiera inną nazwę UPN z dodatkowymi informacjami dzierżawy głównej i dzierżawy zasobów. Spowoduje to zmianę żądania `upn` w tokenie, jeśli użytkownik jest gościem w dzierżawie (który korzysta z innego dostawcy tożsamości na potrzeby uwierzytelniania). 

## <a name="configuring-optional-claims"></a>Konfigurowanie oświadczeń opcjonalnych

Można skonfigurować opcjonalne oświadczenia dla aplikacji, modyfikując manifest aplikacji (Zobacz przykład poniżej). Aby uzyskać więcej informacji, zobacz [artykuł Omówienie manifestu aplikacji usługi Azure AD](reference-app-manifest.md).

> [!IMPORTANT]
> Tokeny dostępu są **zawsze** generowane przy użyciu manifestu zasobu, a nie klienta.  Dlatego w żądaniu `...scope=https://graph.microsoft.com/user.read...` zasób jest grafem.  W ten sposób token dostępu jest tworzony przy użyciu manifestu grafu, a nie manifestu klienta.  Zmiana manifestu dla aplikacji nigdy nie spowoduje, że tokeny dla grafu będą wyglądać inaczej.  Aby potwierdzić, że zmiany `accessToken` są obowiązujące, zażądaj tokenu dla aplikacji, a nie innej aplikacji.  

**Przykładowy schemat:**

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

Deklaruje opcjonalne oświadczenia wymagane przez aplikację. Aplikacja może skonfigurować opcjonalne oświadczenia, które mają być zwracane w każdym z trzech typów tokenów (token identyfikatora, token dostępu, token SAML 2), które mogą zostać odebrane z usługi tokenu zabezpieczającego. Aplikacja może skonfigurować inny zestaw opcjonalnych oświadczeń do zwrócenia w każdym typie tokenu. Właściwość OptionalClaims obiektu aplikacji jest obiektem OptionalClaims.

**Tabela 5: właściwości typu OptionalClaims**

| Nazwa        | Typ                       | Opis                                           |
|-------------|----------------------------|-------------------------------------------------------|
| `idToken`     | Kolekcja (OptionalClaim) | Opcjonalne oświadczenia zwracane w tokenie identyfikatora JWT. |
| `accessToken` | Kolekcja (OptionalClaim) | Opcjonalne oświadczenia zwracane w tokenie dostępu JWT. |
| `saml2Token`  | Kolekcja (OptionalClaim) | Opcjonalne oświadczenia zwracane w tokenie języka SAML.   |

### <a name="optionalclaim-type"></a>Typ OptionalClaim

Zawiera opcjonalne powiązanie skojarzone z aplikacją lub jednostką usługi. Właściwości idToken, accessToken i saml2Token typu [OptionalClaims](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) to kolekcja OptionalClaim.
Jeśli jest to obsługiwane przez określone zgłoszenie, można również zmodyfikować zachowanie OptionalClaim przy użyciu pola AdditionalProperties.

**Tabela 6: właściwości typu OptionalClaim**

| Nazwa                 | Typ                    | Opis                                                                                                                                                                                                                                                                                                   |
|----------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | Nazwa opcjonalnego żądania.                                                                                                                                                                                                                                                                           |
| `source`               | Edm.String              | Źródło (obiekt katalogu) żądania. Istnieją wstępnie zdefiniowane oświadczenia i zdefiniowane przez użytkownika oświadczenia ze wszystkich właściwości rozszerzenia. Jeśli wartość źródłowa jest równa null, to jest to wstępnie zdefiniowane opcjonalne zgłoszenie. Jeśli wartością źródłową jest użytkownik, wartość we właściwości Nazwa jest właściwością rozszerzenia z obiektu użytkownika. |
| `essential`            | Edm.Boolean             | Jeśli wartość jest równa true, żądanie określone przez klienta jest konieczne, aby zapewnić bezproblemowe środowisko autoryzacji dla określonego zadania żądanego przez użytkownika końcowego. Wartość domyślna to false.                                                                                                             |
| `additionalProperties` | Kolekcja (EDM. String) | Dodatkowe właściwości żądania. Jeśli właściwość istnieje w tej kolekcji, modyfikuje zachowanie opcjonalnego żądania określonego we właściwości Nazwa.                                                                                                                                               |
## <a name="configuring-directory-extension-optional-claims"></a>Konfigurowanie opcjonalnych oświadczeń rozszerzenia katalogu

Oprócz standardowego opcjonalnego zestawu oświadczeń można także skonfigurować tokeny do dołączania rozszerzeń schematu katalogu. Aby uzyskać więcej informacji, zobacz [rozszerzenia schematu katalogu](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions). Ta funkcja jest przydatna do dołączania dodatkowych informacji o użytkownikach, które mogą być używane przez aplikację — na przykład dodatkowego identyfikatora lub ważnej opcji konfiguracji ustawionej przez użytkownika. 

> [!Note]
> - Rozszerzenia schematu katalogu to funkcja tylko usługi Azure AD, więc jeśli manifest aplikacji żąda niestandardowego rozszerzenia i loguje się do aplikacji, te rozszerzenia nie zostaną zwrócone.
> - Opcjonalne oświadczenia usługi Azure AD działają tylko z rozszerzeniem usługi Azure AD i nie działają z rozszerzeniem katalogu Microsoft Graph. Oba interfejsy API wymagają uprawnienia `Directory.ReadWriteAll`, które mogą być wysyłane tylko przez administratorów.

### <a name="directory-extension-formatting"></a>Formatowanie rozszerzenia katalogu

W przypadku atrybutów rozszerzenia Użyj pełnej nazwy rozszerzenia (w formacie: `extension_<appid>_<attributename>`) w manifeście aplikacji. `<appid>` musi być zgodna z IDENTYFIKATORem aplikacji żądającej żądania. 

W ramach tokenu JWT te oświadczenia będą emitowane przy użyciu następującego formatu nazwy: `extn.<attributename>`.

W ramach tokenów SAML te oświadczenia będą emitowane przy użyciu następującego formatu identyfikatora URI: `http://schemas.microsoft.com/identity/claims/extn.<attributename>`

## <a name="configuring-group-optional-claims"></a>Konfigurowanie opcjonalnych oświadczeń grupy

   > [!NOTE]
   > Możliwość emisji nazw grup dla użytkowników i grup synchronizowanych z lokalnej wersji zapoznawczej.

W tej sekcji omówiono opcje konfiguracji w obszarze opcjonalne oświadczenia dotyczące zmiany atrybutów grupy używanych w oświadczeniach grup z domyślnego identyfikatora obiektu grupy na atrybuty synchronizowane z lokalnymi Active Directory systemu Windows.

> [!IMPORTANT]
> Zobacz [Konfigurowanie oświadczeń grupy dla aplikacji w usłudze Azure AD](../hybrid/how-to-connect-fed-group-claims.md) , aby uzyskać więcej informacji, w tym ważne zastrzeżenia dla publicznej wersji zapoznawczej oświadczeń grup z atrybutów lokalnych.

1. W portalu > Azure Active Directory > — Rejestracja aplikacji — > Wybierz manifest > aplikacji

2. Włącz oświadczenia członkostwa w grupie, zmieniając groupMembershipClaim

   Prawidłowe wartości to:

   - Całą
   - Grupy securitygroup
   - "DistributionList"
   - "DirectoryRole"

   Na przykład:

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   Domyślnie identyfikatory obiektów grupy będą emitowane w wartości Claim Group.  Aby zmodyfikować wartość żądania w taki sposób, aby zawierała atrybuty grupy lokalnej, lub aby zmienić typ typu "rola", użyj konfiguracji OptionalClaims w następujący sposób:

3. Określ ustawienia nazw grup opcjonalne oświadczenia.

   Jeśli chcesz, aby grupy w tokenie zawierały atrybuty lokalnych grup usługi AD w sekcji opcjonalne oświadczenia Określ, które opcjonalne oświadczenie o typie tokenu należy zastosować do, nazwę opcjonalnego oświadczenia, a także wszystkie wymagane właściwości.  Można wymienić wiele typów tokenów:

   - idToken dla tokenu identyfikatora OIDC
   - accessToken dla tokenu dostępu OAuth/OIDC
   - Saml2Token dla tokenów SAML.

   > [!NOTE]
   > Typ Saml2Token ma zastosowanie do tokenów formatu SAML 1.1 i SAML 2.0

   Dla każdego odpowiedniego typu tokenu zmodyfikuj je, aby użyć sekcji OptionalClaims w manifeście. Schemat OptionalClaims jest następujący:

   ```json
   {
   "name": "groups",
   "source": null,
   "essential": false,
   "additionalProperties": []
   }
   ```

   | Opcjonalny schemat oświadczeń | Wartość |
   |----------|-------------|
   | **Nazwij** | Musi być "grupami" |
   | **zewnętrz** | Nieużywane. Pomiń lub określ wartość null |
   | **olejk** | Nieużywane. Pomiń lub określ wartość false |
   | **AdditionalProperties** | Lista dodatkowych właściwości.  Prawidłowe opcje to "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

   W additionalProperties są wymagane tylko jeden z "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name".  Jeśli istnieje więcej niż jeden, zostanie użyta pierwsza wartość i wszystkie pozostałe zostały zignorowane.

   Niektóre aplikacje wymagają informacji o grupie dla użytkownika w ramach roszczeń ról.  Aby zmienić typ roszczeń na z na podstawie zgłoszenia do roli, należy dodać "emit_as_roles" do dodatkowych właściwości.  Wartości grupy będą emitowane w ramach roszczeń ról.

   > [!NOTE]
   > Jeśli jest używana wartość "emit_as_roles", wszystkie role aplikacji skonfigurowane do przypisania użytkownika nie będą wyświetlane w ramach roszczeń ról

**Przykłady:** Emituj grupy jako nazwy grup w tokenach dostępu OAuth w formacie dnsDomainName\sAMAccountName

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

Aby emitować nazwy grup, które mają być zwracane w formacie netbiosDomain\sAMAccountName jako rolę role w tokenach identyfikatorów SAML i OIDC:

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

W tej sekcji można zapoznać się z scenariuszem, aby dowiedzieć się, jak można użyć opcjonalnej funkcji oświadczeń dla aplikacji.
Dostępnych jest wiele opcji aktualizowania właściwości konfiguracji tożsamości aplikacji w celu włączenia i skonfigurowania oświadczeń opcjonalnych:
-   Można zmodyfikować manifest aplikacji. W poniższym przykładzie zostanie użyta ta metoda. Przeczytaj [dokument manifestu aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest) najpierw, aby zapoznać się z wprowadzeniem do manifestu.
-   Istnieje również możliwość napisania aplikacji, która używa [interfejs API programu Graph](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api) do aktualizowania aplikacji. [Informacje o jednostkach i typach złożonych](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) w podręczniku referencyjnym interfejs API programu Graph mogą pomóc w konfigurowaniu opcjonalnych oświadczeń.

**Przykład:** W poniższym przykładzie zmodyfikujesz manifest aplikacji w celu dodania oświadczeń do dostępu, identyfikatora i tokenów SAML przeznaczonych dla aplikacji.

1. Zaloguj się do [portalu Azure](https://portal.azure.com).
1. Po uwierzytelnieniu wybierz dzierżawę usługi Azure AD, wybierając ją w prawym górnym rogu strony.
1. Wybierz pozycję **rejestracje aplikacji** z lewej strony.
1. Znajdź aplikację, dla której chcesz skonfigurować opcjonalne oświadczenia na liście, a następnie kliknij ją.
1. Na stronie Aplikacja kliknij pozycję **manifest** , aby otworzyć wbudowany edytor manifestu. 
1. Możesz bezpośrednio edytować manifest za pomocą tego edytora. Manifest jest zgodny ze schematem dla [jednostki aplikacji](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)i umożliwia Autoformatowanie manifestu po jego zapisaniu. Nowe elementy zostaną dodane do właściwości `OptionalClaims`.

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

    W takim przypadku inne opcjonalne oświadczenia zostały dodane do każdego typu tokenu, który aplikacja może odebrać. Tokeny identyfikatora będą teraz zawierać nazwę UPN dla użytkowników federacyjnych w pełnej formie (`<upn>_<homedomain>#EXT#@<resourcedomain>`). Tokeny dostępu, które inne klienci żądają tej aplikacji, będą teraz zawierać auth_time. Tokeny SAML będą teraz zawierać rozszerzenie schematu katalogu skypeId (w tym przykładzie identyfikator aplikacji dla tej aplikacji to ab603c56068041afb2f6832e2a17e237). Tokeny SAML będą uwidaczniać identyfikator Skype jako `extension_skypeId`.

1. Po zakończeniu aktualizowania manifestu kliknij przycisk **Zapisz** , aby zapisać manifest

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat standardowych oświadczeń dostarczonych przez usługę Azure AD.

- [IDENTYFIKATORY tokenów](id-tokens.md)
- [Tokeny dostępu](access-tokens.md)
