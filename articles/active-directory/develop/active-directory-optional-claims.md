---
title: Dostarczanie opcjonalnych oświadczeń do aplikacji usługi Azure AD | Azure
titleSuffix: Microsoft identity platform
description: Jak dodać niestandardowe lub dodatkowe oświadczenia do tokenów tokenów sieci Web SAML 2,0 i JSON (JWT) wystawionych przez Azure Active Directory.
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 12/08/2019
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, keyam
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcaf347eb91f8777b56bb2ea4d26985b2d75f645
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74967242"
---
# <a name="how-to-provide-optional-claims-to-your-azure-ad-app"></a>Instrukcje: dostarczanie opcjonalnych oświadczeń do aplikacji usługi Azure AD

Deweloperzy aplikacji mogą używać opcjonalnych oświadczeń w swoich aplikacjach usługi Azure AD, aby określić, które oświadczenia mają być wysyłane do aplikacji. 

Opcjonalne oświadczenia można używać do:

- Wybierz dodatkowe oświadczenia do uwzględnienia w tokenach aplikacji.
- Zmień zachowanie niektórych oświadczeń zwracanych przez usługę Azure AD w tokenach.
- Dodawanie niestandardowych oświadczeń do aplikacji i uzyskiwanie do nich dostępu.

Listę oświadczeń standardowych można znaleźć w dokumentacji [token dostępu](access-tokens.md) i [id_token](id-tokens.md) oświadczenia. 

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
| `groups`| Opcjonalne formatowanie oświadczeń grupy |JWT, SAML| |Używany w połączeniu z ustawieniem GroupMembershipClaims w [manifeście aplikacji](reference-app-manifest.md), który musi być również ustawiony. Aby uzyskać szczegółowe informacje, zobacz poniższe [oświadczenia grupy](#configuring-groups-optional-claims) . Aby uzyskać więcej informacji na temat oświadczeń grup [, zobacz Jak skonfigurować oświadczenia grupy](../hybrid/how-to-connect-fed-group-claims.md)
| `acct`             | Stan konta użytkowników w dzierżawie. | JWT, SAML | | Jeśli użytkownik jest członkiem dzierżawy, wartość jest `0`. Jeśli jest gościem, wartość jest `1`. |
| `upn`                      | Element UserPrincipalName. | JWT, SAML  |           | Chociaż to zgłoszenie jest automatycznie dołączane, można je określić jako opcjonalne, aby dołączyć dodatkowe właściwości, aby zmodyfikować jego zachowanie w przypadku użytkownika-gościa.  |

## <a name="v20-specific-optional-claims-set"></a>v 2.0 — zestaw oświadczeń opcjonalnych specyficznych

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
| `family_name` | Nazwisko                       | Zawiera nazwisko, nazwisko lub nazwę rodziny użytkownika, zgodnie z definicją w obiekcie użytkownika. <br>"family_name":"Miller" | Obsługiwane w usłudze MSA i usłudze Azure AD   |
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

> [!IMPORTANT]
> Tokeny dostępu są **zawsze** generowane przy użyciu manifestu zasobu, a nie klienta.  Dlatego w żądaniu `...scope=https://graph.microsoft.com/user.read...` zasób jest grafem.  W ten sposób token dostępu jest tworzony przy użyciu manifestu grafu, a nie manifestu klienta.  Zmiana manifestu dla aplikacji nigdy nie spowoduje, że tokeny dla grafu będą wyglądać inaczej.  Aby potwierdzić, że zmiany `accessToken` są obowiązujące, zażądaj tokenu dla aplikacji, a nie innej aplikacji.  

Opcjonalne oświadczenia dla aplikacji można skonfigurować za pomocą interfejsu użytkownika lub manifestu aplikacji.

1. Zaloguj się do [portalu Azure](https://portal.azure.com).
1. Po uwierzytelnieniu wybierz dzierżawę usługi Azure AD, wybierając ją w prawym górnym rogu strony.
1. Wybierz **Azure Active Directory** z menu po lewej stronie.
1. W sekcji **Zarządzanie** wybierz pozycję **rejestracje aplikacji**.
1. Wybierz aplikację, dla której chcesz skonfigurować oświadczenia opcjonalne.

**Konfigurowanie opcjonalnych oświadczeń za pomocą interfejsu użytkownika:**

[![przedstawiono sposób konfigurowania opcjonalnych oświadczeń przy użyciu interfejsu użytkownika](./media/active-directory-optional-claims/token-configuration.png)](./media/active-directory-optional-claims/token-configuration.png)

1. W sekcji **Zarządzanie** wybierz pozycję **Konfiguracja tokenu (wersja zapoznawcza)** .
2. Wybierz pozycję **Dodaj opcjonalne**pole.
3. Wybierz typ tokenu, który chcesz skonfigurować.
4. Wybierz opcjonalne oświadczenia do dodania.
5. Kliknij pozycję **Add** (Dodaj).

**Konfigurowanie opcjonalnych oświadczeń za pomocą manifestu aplikacji:**

[![przedstawiono sposób konfigurowania opcjonalnych oświadczeń przy użyciu manifestu aplikacji](./media/active-directory-optional-claims/app-manifest.png)](./media/active-directory-optional-claims/app-manifest.png)

1. W sekcji **Zarządzanie** wybierz pozycję **manifest**. Zostanie otwarty Edytor manifestu oparty na sieci Web, który umożliwia edycję manifestu. Opcjonalnie możesz wybrać pozycję **Pobierz** i edytować manifest lokalnie, a następnie użyć pozycji **Przekaż** w celu ponownego zastosowania go dla aplikacji. Aby uzyskać więcej informacji na temat manifestu aplikacji, zobacz [artykuł Omówienie manifestu aplikacji usługi Azure AD](reference-app-manifest.md).

    Następujący wpis manifestu aplikacji dodaje opcjonalne oświadczenia auth_time, IPADDR i UPN do identyfikatorów, dostępu i tokenów SAML.

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

2. When finished, click **Save**. Now the specified optional claims will be included in the tokens for your application.    


### OptionalClaims type

Declares the optional claims requested by an application. An application can configure optional claims to be returned in each of three types of tokens (ID token, access token, SAML 2 token) it can receive from the security token service. The application can configure a different set of optional claims to be returned in each token type. The OptionalClaims property of the Application entity is an OptionalClaims object.

**Table 5: OptionalClaims type properties**

| Name        | Type                       | Description                                           |
|-------------|----------------------------|-------------------------------------------------------|
| `idToken`     | Collection (OptionalClaim) | The optional claims returned in the JWT ID token. |
| `accessToken` | Collection (OptionalClaim) | The optional claims returned in the JWT access token. |
| `saml2Token`  | Collection (OptionalClaim) | The optional claims returned in the SAML token.   |

### OptionalClaim type

Contains an optional claim associated with an application or a service principal. The idToken, accessToken, and saml2Token properties of the [OptionalClaims](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) type is a collection of OptionalClaim.
If supported by a specific claim, you can also modify the behavior of the OptionalClaim using the AdditionalProperties field.

**Table 6: OptionalClaim type properties**

| Name                 | Type                    | Description                                                                                                                                                                                                                                                                                                   |
|----------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | The name of the optional claim.                                                                                                                                                                                                                                                                           |
| `source`               | Edm.String              | The source (directory object) of the claim. There are predefined claims and user-defined claims from extension properties. If the source value is null, the claim is a predefined optional claim. If the source value is user, the value in the name property is the extension property from the user object. |
| `essential`            | Edm.Boolean             | If the value is true, the claim specified by the client is necessary to ensure a smooth authorization experience for the specific task requested by the end user. The default value is false.                                                                                                             |
| `additionalProperties` | Collection (Edm.String) | Additional properties of the claim. If a property exists in this collection, it modifies the behavior of the optional claim specified in the name property.                                                                                                                                               |
## Configuring directory extension optional claims

In addition to the standard optional claims set, you can also configure tokens to include directory schema extensions. For more info, see [Directory schema extensions](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions). This feature is useful for attaching additional user information that your app can use – for example, an additional identifier or important configuration option that the user has set. See the bottom of this page for an example.

> [!NOTE]
> - Directory schema extensions are an Azure AD-only feature, so if your application manifest requests a custom extension and an MSA user logs into your app, these extensions will not be returned.
> - Azure AD optional claims only work with Azure AD Graph extensions and do not work with Microsoft Graph directory extensions. Both APIs require the `Directory.ReadWriteAll` permission, which can only be consented by admins.

### Directory extension formatting

When configuring directory extension optional claims using the application manifest, use the full name of the extension (in the format: `extension_<appid>_<attributename>`). The `<appid>` must match the ID of the application requesting the claim. 

Within the JWT, these claims will be emitted with the following name format:  `extn.<attributename>`.

Within the SAML tokens, these claims will be emitted with the following URI format: `http://schemas.microsoft.com/identity/claims/extn.<attributename>`

## Configuring groups optional claims

   > [!NOTE]
   > The ability to emit group names for users and groups synced from on-premises is Public Preview.

This section covers the configuration options under optional claims for changing the group attributes used in group claims from the default group objectID to attributes synced from on-premises Windows Active Directory. You can configure groups optional claims for your application through the UI or application manifest.

> [!IMPORTANT]
> See [Configure group claims for applications with Azure AD](../hybrid/how-to-connect-fed-group-claims.md) for more details including important caveats for the public preview of group claims from on-premises attributes.

**Configuring groups optional claims through the UI:**
1. Sign in to the [Azure portal](https://portal.azure.com)
1. After you've authenticated, choose your Azure AD tenant by selecting it from the top right corner of the page
1. Select **Azure Active Directory** from the left hand menu
1. Under the **Manage** section, select **App registrations**
1. Select the application you want to configure optional claims for in the list
1. Under the **Manage** section, select **Token configuration (preview)**
2. Select **Add groups claim**
3. Select the group types to return (**All Groups**, **SecurityGroup** or **DirectoryRole**). The **All Groups** option includes **SecurityGroup**, **DirectoryRole** and **DistributionList**
4. Optional: click on the specific token type properties to modify the groups claim value to contain on premises group attributes or to change the claim type to a role
5. Click **Save**

**Configuring groups optional claims through the application manifest:**
1. Sign in to the [Azure portal](https://portal.azure.com)
1. After you've authenticated, choose your Azure AD tenant by selecting it from the top right corner of the page
1. Select **Azure Active Directory** from the left hand menu
1. Select the application you want to configure optional claims for in the list
1. Under the **Manage** section, select **Manifest**
3. Add the following entry using the manifest editor:

   The valid values are:

   - "All" (this option includes SecurityGroup, DirectoryRole and DistributionList)
   - "SecurityGroup"
   - "DirectoryRole"

   For example:

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
   | **zewnętrz** | Nie jest używany. Pomiń lub określ wartość null |
   | **olejk** | Nie jest używany. Pomiń lub określ wartość false |
   | **AdditionalProperties** | Lista dodatkowych właściwości.  Prawidłowe opcje to "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

   W additionalProperties są wymagane tylko jeden z "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name".  Jeśli istnieje więcej niż jeden, zostanie użyta pierwsza wartość i wszystkie pozostałe zostały zignorowane.

   Niektóre aplikacje wymagają informacji o grupie dla użytkownika w ramach roszczeń ról.  Aby zmienić typ roszczeń na z na podstawie żądania grupy do roli, Dodaj "emit_as_roles" do dodatkowych właściwości.  Wartości grupy będą emitowane w ramach roszczeń ról.

   > [!NOTE]
   > Jeśli zostanie użyta wartość "emit_as_roles", wszystkie role aplikacji skonfigurowane do przypisania użytkownika nie będą wyświetlane w ramach tego żądania

**Przykłady:**

1) Emituj grupy jako nazwy grup w tokenach dostępu OAuth w formacie dnsDomainName\sAMAccountName

    
    **Konfiguracja interfejsu użytkownika:**

    [![przedstawiono sposób konfigurowania opcjonalnych oświadczeń przy użyciu interfejsu użytkownika](./media/active-directory-optional-claims/groups-example-1.png)](./media/active-directory-optional-claims/groups-example-1.png)


    **Wpis manifestu aplikacji:**
    ```json
        "optionalClaims": {
            "accessToken": [{
            "name": "groups",
            "additionalProperties": ["dns_domain_and_sam_account_name"]
            }]
        }
    ```

 
    
2) Emituj nazwy grup, które mają być zwracane w formacie netbiosDomain\sAMAccountName jako rolę roszczeń w tokenach identyfikatorów SAML i OIDC

    **Konfiguracja interfejsu użytkownika:**

    [![przedstawiono sposób konfigurowania opcjonalnych oświadczeń przy użyciu interfejsu użytkownika](./media/active-directory-optional-claims/groups-example-2.png)](./media/active-directory-optional-claims/groups-example-2.png)

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
     

## <a name="optional-claims-example"></a>Przykład opcjonalnych oświadczeń

W tej sekcji można zapoznać się z scenariuszem, aby dowiedzieć się, jak można użyć opcjonalnej funkcji oświadczeń dla aplikacji.
Dostępnych jest wiele opcji aktualizowania właściwości konfiguracji tożsamości aplikacji w celu włączenia i skonfigurowania oświadczeń opcjonalnych:
-    Możesz użyć interfejsu użytkownika **konfiguracji tokenu (wersja zapoznawcza)** (Zobacz przykład poniżej)
-    Możesz użyć **manifestu** (Zobacz przykład poniżej). Przeczytaj [dokument manifestu aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest) najpierw, aby zapoznać się z wprowadzeniem do manifestu.
-   Istnieje również możliwość napisania aplikacji, która używa [interfejs API programu Graph](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api) do aktualizowania aplikacji. [Informacje o jednostkach i typach złożonych](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) w podręczniku referencyjnym interfejs API programu Graph mogą pomóc w konfigurowaniu opcjonalnych oświadczeń.

**Przykład:** W poniższym przykładzie użyto interfejsu użytkownika **konfiguracji tokenu (wersja zapoznawcza)** i **manifestu** w celu dodania opcjonalnych oświadczeń do tokenów dostępu, identyfikatora i SAML przeznaczonych dla danej aplikacji. Różne opcjonalne oświadczenia zostaną dodane do każdego typu tokenu, który aplikacja może odbierać:
-    Tokeny identyfikatora będą teraz zawierać nazwę UPN dla użytkowników federacyjnych w pełnej formie (`<upn>_<homedomain>#EXT#@<resourcedomain>`).
-    Tokeny dostępu, które inne klienci żądają tej aplikacji, będą teraz zawierać auth_time
-    Tokeny SAML będą teraz zawierać rozszerzenie schematu katalogu skypeId (w tym przykładzie identyfikator aplikacji dla tej aplikacji to ab603c56068041afb2f6832e2a17e237). Tokeny SAML będą uwidaczniać identyfikator Skype jako `extension_skypeId`.

**Konfiguracja interfejsu użytkownika:**

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).

1. Po uwierzytelnieniu wybierz dzierżawę usługi Azure AD, wybierając ją w prawym górnym rogu strony.

1. Wybierz **Azure Active Directory** z menu po lewej stronie.

1. W sekcji **Zarządzanie** wybierz pozycję **rejestracje aplikacji**.

1. Znajdź aplikację, dla której chcesz skonfigurować opcjonalne oświadczenia na liście, a następnie kliknij ją.

1. W sekcji **Zarządzanie** kliknij pozycję **Konfiguracja tokenu (wersja zapoznawcza)** .

1. Wybierz pozycję **Dodaj opcjonalne oświadczenie**, wybierz typ tokenu **identyfikatora** , wybierz **nazwę UPN** z listy oświadczeń, a następnie kliknij przycisk **Dodaj**.

1. Wybierz pozycję **Dodaj opcjonalne oświadczenie**, wybierz typ tokenu **dostępu** , wybierz **auth_time** z listy oświadczeń, a następnie kliknij przycisk **Dodaj**.

1. Na ekranie przegląd konfiguracji tokenu kliknij ikonę ołówka obok **nazwy UPN**, kliknij przełącznik **uwierzytelniony zewnętrznie** , a następnie kliknij przycisk **Zapisz**.

1. Wybierz pozycję **Dodaj oświadczenie opcjonalne**, wybierz typ tokenu **SAML** , wybierz pozycję **Extn. skypeID** z listy oświadczeń (dotyczy to tylko sytuacji, gdy utworzono obiekt użytkownika usługi Azure AD o nazwie skypeID), a następnie kliknij przycisk **Dodaj**.

    [![przedstawiono sposób konfigurowania opcjonalnych oświadczeń przy użyciu interfejsu użytkownika](./media/active-directory-optional-claims/token-config-example.png)](./media/active-directory-optional-claims/token-config-example.png)

**Konfiguracja manifestu:**
1. Zaloguj się do [portalu Azure](https://portal.azure.com).
1. Po uwierzytelnieniu wybierz dzierżawę usługi Azure AD, wybierając ją w prawym górnym rogu strony.
1. Wybierz **Azure Active Directory** z menu po lewej stronie.
1. Znajdź aplikację, dla której chcesz skonfigurować opcjonalne oświadczenia na liście, a następnie kliknij ją.
1. W sekcji **Zarządzanie** kliknij pozycję **manifest** , aby otworzyć wbudowany edytor manifestu.
1. Możesz bezpośrednio edytować manifest za pomocą tego edytora. Manifest jest zgodny ze schematem elementu [Application Entity]. (https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest) i umożliwia Autoformatowanie manifestu po jego zapisaniu. Nowe elementy zostaną dodane do właściwości `OptionalClaims`.

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
