---
title: Dostosowywanie oświadczeń emitowane w tokenach dla konkretnej aplikacji w dzierżawie usługi Azure AD (publiczna wersja zapoznawcza)
description: Ta strona zawiera opis Mapowanie oświadczeń w usłudze Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b770ee476fc5c1c334f53904539cc34cf962c62
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2019
ms.locfileid: "65546205"
---
# <a name="how-to-customize-claims-emitted-in-tokens-for-a-specific-app-in-a-tenant-preview"></a>Instrukcje: Dostosowywanie oświadczeń emitowane w tokenach dla konkretnej aplikacji w dzierżawie (wersja zapoznawcza)

> [!NOTE]
> Ta funkcja zastępuje i zastępuje [oświadczeń dostosowywania](active-directory-saml-claims-customization.md) do obecnie oferowanych w portalu. W tej samej aplikacji Jeśli dostosujesz oświadczeń przy użyciu portalu, oprócz metoda wykres/programu PowerShell, szczegółowo opisanych w tym dokumencie tokenów wystawiony dla czy aplikacja będzie ignorować konfiguracji w portalu. Konfiguracje wprowadzone za pomocą metod podanych w tym dokumencie nie zostaną odzwierciedlone w portalu.

Ta funkcja jest używana przez administratorów dzierżawy, aby dostosować oświadczeń emitowane w tokenach dla określonej aplikacji w ramach ich dzierżawy. Możesz użyć zasad Mapowanie oświadczeń w celu:

- Wybierz, jakie oświadczenia są uwzględnione w tokenach.
- Utworzyć typy oświadczeń, które jeszcze nie istnieje.
- Wybierz lub Zmień źródło danych utworzonych w określonych oświadczeń.

> [!NOTE]
> Ta funkcja jest obecnie w publicznej wersji zapoznawczej. Przygotuj się na przywracanie lub usuwanie wszelkich zmian. Ta funkcja jest dostępna w dowolnej subskrypcji usługi Azure Active Directory (Azure AD) w okresie publicznej wersji zapoznawczej. Gdy ta funkcja stanie się ogólnie dostępna, niektóre cechy funkcji mogą jednak wymagać subskrypcję usługi Azure AD premium. Ta funkcja obsługuje Konfigurowanie zasad Mapowanie oświadczeń dla protokołów WS-Fed, SAML, OAuth i OpenID Connect.

## <a name="claims-mapping-policy-type"></a>Mapowanie typu zasad oświadczeń

W usłudze Azure AD **zasad** obiekt reprezentuje zestaw reguł wymuszane na poszczególne aplikacje lub wszystkie aplikacje w organizacji. Każdego typu zasad ma unikatowy struktury, zestaw właściwości, które są następnie stosowane do obiektów, które są przypisane.

A oświadczeń mapowanie zasad jest typem **zasad** obiekt, który modyfikuje oświadczenia emitowane w tokeny wystawione dla określonych aplikacji.

## <a name="claim-sets"></a>W zestawie oświadczeń

Istnieją pewne zestawy oświadczeń, które określają, jak i kiedy są one używane w tokeny.

| Zestaw oświadczeń | Opis |
|---|---|
| Podstawowy zestaw oświadczeń | Znajdują się w każdy token, niezależnie od zasady. Te oświadczenia również są traktowane jako ograniczone i nie może być modyfikowany. |
| Zestaw oświadczeń podstawowe | Zawiera oświadczenia, które są emitowane przez domyślną tokeny (oprócz zestawu oświadczeń core). Można pominąć lub modyfikowania oświadczeń podstawowa przy użyciu oświadczeń mapowanie zasad. |
| Zestaw oświadczeń z ograniczeniami | Nie można modyfikować za pomocą zasad. Nie można zmienić źródła danych, a nie transformacja jest stosowana podczas generowania te oświadczenia. |

### <a name="table-1-json-web-token-jwt-restricted-claim-set"></a>Tabela 1: Tokenu Web JSON (JWT) ograniczony zestaw oświadczeń

| Typ oświadczenia (nazwa) |
| ----- |
| _claim_names |
| _claim_sources |
| access_token |
| account_type |
| acr |
| aktora |
| actortoken |
| AIO |
| altsecid |
| amr |
| app_chain |
| app_displayname |
| app_res |
| appctx |
| appctxsender |
| Identyfikator aplikacji |
| appidacr |
| potwierdzenie |
| at_hash |
| AUD |
| auth_data |
| auth_time |
| authorization_code |
| azp |
| azpacr |
| c_hash |
| ca_enf |
| cc |
| cert_token_use |
| client_id |
| cloud_graph_host_name |
| cloud_instance_name |
| opcją cnf |
| kod |
| funkcje sterowania |
| credential_keys |
| Renderowanie po stronie klienta |
| csr_type |
| Identyfikator urządzenia |
| dns_names |
| domain_dns_name |
| domain_netbios_name |
| e_exp |
| email |
| endpoint |
| enfpolids |
| EXP |
| expires_on |
| grant_type |
| wykres |
| group_sids |
| Grupy |
| hasgroups |
| hash_alg |
| home_oid |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/expired` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` |
| IAT |
| Dostawca tożsamości |
| dostawcy tożsamości |
| in_corp |
| wystąpienie |
| ipaddr |
| isbrowserhostedapp |
| iss |
| jwk |
| key_id |
| key_type |
| mam_compliance_url |
| mam_enrollment_url |
| mam_terms_of_use_url |
| mdm_compliance_url |
| mdm_enrollment_url |
| mdm_terms_of_use_url |
| nameid |
| nbf |
| netbios_name |
| Identyfikator jednorazowy |
| Identyfikator OID |
| on_prem_id |
| onprem_sam_account_name |
| onprem_sid |
| openid2_id |
| password |
| platf |
| polids |
| pop_jwk |
| preferred_username |
| previous_refresh_token |
| primary_sid |
| Identyfikator PUID |
| pwd_exp |
| pwd_url |
| redirect_uri |
| refresh_token |
| refreshtoken |
| request_nonce |
| zasób |
| rola |
| role |
| zakres |
| scp |
| Identyfikator SID |
| Podpis |
| signin_state |
| src1 |
| src2 |
| Sub |
| tbid |
| tenant_display_name |
| tenant_region_scope |
| thumbnail_photo |
| identyfikatora TID |
| tokenAutologonEnabled |
| trustedfordelegation |
| unique_name |
| upn |
| user_setting_sync_url |
| username |
| uti |
| VER |
| verified_primary_email |
| verified_secondary_email |
| wids |
| win_ver |

### <a name="table-2-saml-restricted-claim-set"></a>Tabela 2: SAML ograniczony zestaw oświadczeń

| Typ oświadczenia (identyfikator URI) |
| ----- |
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/expired`|
|`http://schemas.microsoft.com/identity/claims/accesstoken`|
|`http://schemas.microsoft.com/identity/claims/openid2_id`|
|`http://schemas.microsoft.com/identity/claims/identityprovider`|
|`http://schemas.microsoft.com/identity/claims/objectidentifier`|
|`http://schemas.microsoft.com/identity/claims/puid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier [MR1]`|
|`http://schemas.microsoft.com/identity/claims/tenantid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod`|
|`http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/groups`|
|`http://schemas.microsoft.com/claims/groups.link`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/role`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/wids`|
|`http://schemas.microsoft.com/2014/09/devicecontext/claims/iscompliant`|
|`http://schemas.microsoft.com/2014/02/devicecontext/claims/isknown`|
|`http://schemas.microsoft.com/2012/01/devicecontext/claims/ismanaged`|
|`http://schemas.microsoft.com/2014/03/psso`|
|`http://schemas.microsoft.com/claims/authnmethodsreferences`|
|`http://schemas.xmlsoap.org/ws/2009/09/identity/claims/actor`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/samlissuername`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/confirmationkey`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarygroupsid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authorizationdecision`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authentication`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/sid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarygroupsid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarysid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/denyonlysid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlywindowsdevicegroup`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdeviceclaim`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdevicegroup`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsfqbnversion`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowssubauthority`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsuserclaim`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/x500distinguishedname`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/ispersistent`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/privatepersonalidentifier`|
|`http://schemas.microsoft.com/identity/claims/scope`|

## <a name="claims-mapping-policy-properties"></a>Mapowanie właściwości zasad oświadczeń

Aby kontrolować, jakie oświadczenia są emitowane i skąd pochodzą dane, użyj właściwości mapowanie zasad oświadczeń. Jeśli nie ustawiono zasad, tokeny problemy z systemu, które obejmują podstawowe oświadczenia zestawu, do zestawu oświadczeń podstawowe i wszystkich [opcjonalnych oświadczeń](active-directory-optional-claims.md) wybrany aplikacji tak otrzymać.

### <a name="include-basic-claim-set"></a>Obejmują zestaw oświadczeń podstawowe

**Ciąg:** IncludeBasicClaimSet

**Typ danych:** Atrybut typu wartość logiczna (True lub False)

**Podsumowanie:** Ta właściwość określa, czy zestaw oświadczeń podstawowe znajduje się w tokeny wpływ tych zasad.

- Jeśli ustawiono wartość True, wszystkie oświadczenia w zestawie oświadczeń podstawowe są emitowane w tokenach objęte zasadami. 
- Jeśli ma wartość False, oświadczenia w zestawie oświadczeń podstawowych nie są w tokenach, chyba że są one dodawane indywidualnie we właściwości schematu oświadczenia te same zasady.

> [!NOTE] 
> Oświadczenia w zestawie oświadczeń core znajdują się w każdy token, niezależnie od tego, co ta właściwość ma wartość. 

### <a name="claims-schema"></a>Schemat oświadczeń

**Ciąg:** ClaimsSchema

**Typ danych:** Obiektu blob JSON za pomocą co najmniej jeden wpis schematu oświadczeń

**Podsumowanie:** Ta właściwość określa, jakie oświadczenia są obecne w tokeny, których dotyczą zasady, oprócz zestawu oświadczeń podstawowe i podstawowy zestaw oświadczeń.
Dla każdego schematu oświadczenia wpisu zdefiniowane w tej właściwości niektóre informacje są wymagane. Określ, skąd pochodzą dane (**wartość** lub **par identyfikator/źródła**), i które roszczenie danych jest emitowany jako (**typu oświadczenia**).

### <a name="claim-schema-entry-elements"></a>Elementy wpis schematu oświadczeń

**Wartość:** Element wartości definiuje wartość statyczną danych maszynowych w oświadczeniu.

**Para źródło/ID:** Elementy źródło i identyfikator definiują, gdzie źródło danych w oświadczeniu. 

Ustaw element źródłowy do jednej z następujących wartości: 

- "user": Dane w oświadczenie jest właściwością obiektu User. 
- "aplikacja": Dane w oświadczenie jest właściwością nazwy głównej usługi aplikacji (klienta). 
- "Zasób": Dane w oświadczenie jest właściwością jednostki usługi zasobów.
- "audience": Dane w oświadczenie jest właściwością jednostki usługi, która jest odbiorców tokenu (klienta lub zasób nazwy głównej usługi).
- "Firma": Dane w oświadczenie jest właściwością w obiekcie firmy dzierżawy zasobów.
- "przekształcenia": Dane w oświadczeniu pochodzą przekształcania oświadczeń (zobacz sekcję "Przekształcania oświadczeń" w dalszej części tego artykułu).

Jeśli źródło jest przekształcania **TransformationID** elementu muszą być zawarte w tej definicji oświadczenia.

Elementu Identyfikatora Określa, która właściwość na "source" zawiera wartość oświadczenia. W poniższej tabeli wymieniono wartości Identyfikatora prawidłowy dla każdej wartości źródła.

#### <a name="table-3-valid-id-values-per-source"></a>Tabela 3: Prawidłowe wartości Identyfikatora dla każdego źródła

| Source | ID | Opis |
|-----|-----|-----|
| Użytkownik | nazwisko | Nazwa rodziny |
| Użytkownik | Imię | Imię |
| Użytkownik | Nazwa wyświetlana | Nazwa wyświetlana |
| Użytkownik | Identyfikator obiektu | ObjectID |
| Użytkownik | poczta | Adres e-mail |
| Użytkownik | userprincipalname | Nazwa główna użytkownika |
| Użytkownik | Dział|Dział|
| Użytkownik | onpremisessamaccountname | Nazwa konta SAM w środowisku lokalnym |
| Użytkownik | netbiosname| NetBios Name |
| Użytkownik | dnsdomainname | Nazwa domeny DNS |
| Użytkownik | onpremisesecurityidentifier | Identyfikator zabezpieczeń w środowisku lokalnym |
| Użytkownik | companyname| Nazwa organizacji |
| Użytkownik | streetaddress | Ulica i numer |
| Użytkownik | KodPocztowy | Kod pocztowy |
| Użytkownik | preferredlanguange | Preferowany język |
| Użytkownik | onpremisesuserprincipalname | Nazwa UPN w środowisku lokalnym |
| Użytkownik | mailnickname | Pseudonim związany z pocztą |
| Użytkownik | extensionattribute1 | Atrybut rozszerzenia 1 |
| Użytkownik | extensionattribute2 | Atrybut rozszerzenia 2 |
| Użytkownik | extensionattribute3 | Atrybut rozszerzenia 3 |
| Użytkownik | extensionattribute4 | Atrybut rozszerzenia 4 |
| Użytkownik | extensionattribute5 | Atrybut rozszerzenia 5 |
| Użytkownik | extensionattribute6 | Atrybut rozszerzenia 6 |
| Użytkownik | extensionattribute7 | Atrybut rozszerzenia 7 |
| Użytkownik | extensionattribute8 | Atrybut rozszerzenia 8 |
| Użytkownik | extensionattribute9 | Atrybut rozszerzenia 9 |
| Użytkownik | extensionattribute10 | Atrybut rozszerzenia 10 |
| Użytkownik | extensionattribute11 | Atrybut rozszerzenia 11 |
| Użytkownik | extensionattribute12 | Atrybut rozszerzenia 12 |
| Użytkownik | extensionattribute13 | Atrybut rozszerzenia 13 |
| Użytkownik | extensionattribute14 | Atrybut rozszerzenia 14 |
| Użytkownik | extensionattribute15 | Atrybut rozszerzenia 15 |
| Użytkownik | othermail | Inne wiadomości E-mail |
| Użytkownik | Kraj | Kraj |
| Użytkownik | city | Miejscowość |
| Użytkownik | stan | Stan |
| Użytkownik | stanowisko | Stanowisko |
| Użytkownik | EmployeeID | Identyfikator pracownika |
| Użytkownik | facsimiletelephonenumber | Numer faksu |
| Aplikacja zasobu, grupy odbiorców | Nazwa wyświetlana | Nazwa wyświetlana |
| Aplikacja zasobu, grupy odbiorców | obiekty | ObjectID |
| Aplikacja zasobu, grupy odbiorców | tags | Tag jednostki usługi |
| Firma | tenantcountry | Kraju dzierżawy |

**TransformationID:** TransformationID element należy podać tylko wtedy, gdy element źródła jest ustawiony na wartość "transformacji".

- Ten element musi być zgodny element identyfikator wpisu przekształcania w **ClaimsTransformation** właściwość, która definiuje, jak jest generowany danych dla tego oświadczenia.

**Typ oświadczenia:** **JwtClaimType** i **SamlClaimType** zdefiniować elementy, które roszczenie, ten wpis schematu oświadczenia odnosi się do.

- JwtClaimType musi zawierać nazwy oświadczenia, który był emitowany w tokenów Jwt.
- SamlClaimType musi zawierać identyfikator URI oświadczenia był emitowany w tokeny SAML.

> [!NOTE]
> Nie można użyć nazwy i identyfikatory URI oświadczeń w zestawie oświadczeń ograniczone dla elementów typu oświadczenia. Aby uzyskać więcej informacji zobacz sekcję "Wyjątki i ograniczenia" w dalszej części tego artykułu.

### <a name="claims-transformation"></a>Przekształcanie oświadczeń

**Ciąg:** ClaimsTransformation

**Typ danych:** Obiektu blob JSON za pomocą co najmniej jeden wpis transformacji 

**Podsumowanie:** Ta właściwość służy do stosowania typowych przekształceń do źródła danych, aby wygenerować dane wyjściowe do oświadczenia określone w schemacie oświadczeń.

**ID:** Użyj elementu ID, aby odwoływać się do tego wpisu przekształcania we wpisie TransformationID oświadczeń schematu. Ta wartość musi być unikatowy dla każdego wpisu transformacji, w ramach tych zasad.

**TransformationMethod:** TransformationMethod element identyfikuje, które jest wykonywane na potrzeby generowania danych oświadczenia.

Oparty na wybranej metody, oczekiwany jest zestaw danych wejściowych i wyjściowych. Zdefiniować dane wejściowe i wyjściowe przy użyciu **InputClaims**, **InputParameters** i **OutputClaims** elementów.

#### <a name="table-4-transformation-methods-and-expected-inputs-and-outputs"></a>Tabela 4: Metody przekształcania i oczekiwanych danych wejściowych i wyjściowych

|TransformationMethod|Oczekiwanych danych wejściowych|Oczekiwane dane wyjściowe|Opis|
|-----|-----|-----|-----|
|Dołączanie|ciąg1, ciąg2, separatora|oświadczenie outputClaim|Sprzężenia wejściowe ciągi przy użyciu separatora między. Na przykład: ciąg1: "foo@bar.com", ciąg2: "piaskownicy", separatora: "." skutkuje oświadczenie outputClaim: "foo@bar.com.sandbox"|
|ExtractMailPrefix|poczta|oświadczenie outputClaim|Wyodrębnia lokalnym składniku adresu e-mail. Na przykład: wiadomości e-mail: "foo@bar.com" skutkuje oświadczenie outputClaim: "foo". Jeśli nie \@ logowania jest obecny, a następnie oryginalnego ciągu wejściowego jest zwracany, ponieważ jest.|

**InputClaims:** Użyj elementu InputClaims do przekazania danych z wpisu schematu oświadczenia do przekształcenia. Zawiera dwa atrybuty: **ClaimTypeReferenceId** i **TransformationClaimType**.

- **ClaimTypeReferenceId** sprzężony z elementem identyfikator wpisu schematu oświadczenia, można znaleźć odpowiednich oświadczeń przychodzących. 
- **TransformationClaimType** służy do nadaj unikatową nazwę do tych danych wejściowych. Ta nazwa musi być zgodna oczekiwane dane wejściowe dla metody transformacji.

**InputParameters:** Element InputParameters służy do przekazywania wartości stałej do przekształcenia. Zawiera dwa atrybuty: **Wartość** i **identyfikator**.

- **Wartość** jest rzeczywista stała wartość do przekazania.
- **Identyfikator** służy do nadaj unikatową nazwę w danych wejściowych. Nazwa musi odpowiadać oczekiwane dane wejściowe dla metody transformacji.

**OutputClaims:** Element OutputClaims służy do przechowywania danych generowanych przez przekształcenie i powiązanie ich wpis schematu oświadczenia. Zawiera dwa atrybuty: **ClaimTypeReferenceId** i **TransformationClaimType**.

- **ClaimTypeReferenceId** sprzężony z Identyfikatorem wpisu schematu oświadczenia odnaleźć oświadczenia właściwe dane wyjściowe.
- **TransformationClaimType** służy do nadaj unikatową nazwę w danych wyjściowych. Nazwa musi odpowiadać jednej z oczekiwanych danych wyjściowych dla metody transformacji.

### <a name="exceptions-and-restrictions"></a>Wyjątki i ograniczenia

**Identyfikatora SAML NameID i głównej nazwy użytkownika:** Atrybuty, z których źródła wartości NameID i głównej nazwy użytkownika i przekształcenia oświadczeń, które są dozwolone, ale są ograniczone. Zobacz tabele 5 i 6, aby wyświetlić dozwolone wartości.

#### <a name="table-5-attributes-allowed-as-a-data-source-for-saml-nameid"></a>Tabela 5: Atrybuty można użyć jako źródła danych dla identyfikatora SAML NameID

|Source|ID|Opis|
|-----|-----|-----|
| Użytkownik | poczta|Adres e-mail|
| Użytkownik | userprincipalname|Nazwa główna użytkownika|
| Użytkownik | onpremisessamaccountname|Nazwy konta Sam lokalnie|
| Użytkownik | EmployeeID|Identyfikator pracownika|
| Użytkownik | extensionattribute1 | Atrybut rozszerzenia 1 |
| Użytkownik | extensionattribute2 | Atrybut rozszerzenia 2 |
| Użytkownik | extensionattribute3 | Atrybut rozszerzenia 3 |
| Użytkownik | extensionattribute4 | Atrybut rozszerzenia 4 |
| Użytkownik | extensionattribute5 | Atrybut rozszerzenia 5 |
| Użytkownik | extensionattribute6 | Atrybut rozszerzenia 6 |
| Użytkownik | extensionattribute7 | Atrybut rozszerzenia 7 |
| Użytkownik | extensionattribute8 | Atrybut rozszerzenia 8 |
| Użytkownik | extensionattribute9 | Atrybut rozszerzenia 9 |
| Użytkownik | extensionattribute10 | Atrybut rozszerzenia 10 |
| Użytkownik | extensionattribute11 | Atrybut rozszerzenia 11 |
| Użytkownik | extensionattribute12 | Atrybut rozszerzenia 12 |
| Użytkownik | extensionattribute13 | Atrybut rozszerzenia 13 |
| Użytkownik | extensionattribute14 | Atrybut rozszerzenia 14 |
| Użytkownik | extensionattribute15 | Atrybut rozszerzenia 15 |

#### <a name="table-6-transformation-methods-allowed-for-saml-nameid"></a>Tabela 6: Metody przekształcania dozwolone dla identyfikatora SAML NameID

| TransformationMethod | Ograniczenia |
| ----- | ----- |
| ExtractMailPrefix | Brak |
| Dołączanie | Sufiks jest przyłączone do musi być zweryfikowaną domenę dzierżawy zasobów. |

### <a name="custom-signing-key"></a>Niestandardowy klucz podpisywania

Niestandardowego klucza podpisywania muszą być przypisane do obiektu jednostki usługi dla oświadczeń mapowania zasad, które zostały wprowadzone. Dzięki temu po potwierdzeniu, że tokeny zostały zmodyfikowane przez twórcę mapowanie zasad oświadczeń i chroni aplikacje z oświadczeń mapowanie zasad utworzonych przez uczestników złośliwych działań.  Aplikacje, które mają oświadczeń mapowanie włączone, należy zaznaczyć specjalne identyfikator URI dla tokenu, ich kluczy podpisywania, dodając `appid={client_id}` do ich [OpenID Connect żądania metadanych](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document).  

### <a name="cross-tenant-scenarios"></a>Scenariusze międzydzierżawowe

Mapowanie zasad oświadczeń nie dotyczą użytkowników-gości. Jeśli użytkownik-Gość spróbuje uzyskać dostęp do aplikacji przy użyciu oświadczeń mapowanie zasady przypisane do jego nazwy głównej usługi, domyślny token wystawiono (zasada nie ma znaczenia).

## <a name="claims-mapping-policy-assignment"></a>Mapowanie przypisania zasad oświadczeń

Mapowanie zasad oświadczeń można przypisać tylko do obiektów nazw głównych usług.

### <a name="example-claims-mapping-policies"></a>Przykład oświadczeń mapowania zasad

W usłudze Azure AD wiele scenariuszy są możliwe, gdy można dostosować emitowane w tokenach dla jednostek usług określonych oświadczeń. W tej sekcji części omówimy kilka typowych scenariuszy, które mogą pomóc w niejasny sposób używania Mapowanie typów zasad oświadczeń.

#### <a name="prerequisites"></a>Wymagania wstępne

Poniższe przykłady służy do tworzenia, aktualizacji, łączenie i usuwania zasad dla jednostki usługi. Jeśli jesteś nowym użytkownikiem usługi Azure AD, zalecamy możesz [Dowiedz się więcej o tym, jak uzyskać dzierżawę usługi Azure AD](quickstart-create-new-tenant.md) przed wykonaniem tych przykładów.

Aby rozpocząć pracę, wykonaj następujące czynności:

1. Pobierz najnowszy [modułu Azure AD PowerShell publicznej wersji zapoznawczej](https://www.powershellgallery.com/packages/AzureADPreview).
1. Uruchom polecenie Connect, aby zalogować się do konta administratora usługi Azure AD. Uruchom to polecenie za każdym razem, Rozpocznij nową sesję.

   ``` powershell
   Connect-AzureAD -Confirm
   ```
1. Aby wyświetlić wszystkie zasady, które zostały utworzone w Twojej organizacji, uruchom następujące polecenie. Zaleca się, że to polecenie jest uruchamiane po większość operacji w następujących scenariuszach, aby sprawdzić, czy zasady są tworzone zgodnie z oczekiwaniami.

   ``` powershell
   Get-AzureADPolicy
   ```

#### <a name="example-create-and-assign-a-policy-to-omit-the-basic-claims-from-tokens-issued-to-a-service-principal"></a>Przykład: Tworzenie i przypisywanie zasad, aby pominąć podstawowe oświadczeń z tokeny wystawione do nazwy głównej usługi

W tym przykładzie utworzysz zasady, które powoduje usunięcie podstawowego zestawu oświadczeń z tokeny wystawione do podmiotów połączonej usługi.

1. Utwórz mapowanie zasad oświadczeń. Te zasady usługi połączonej do określonych jednostek, usuwa zestawu z tokenów oświadczeń podstawowe.
   1. Aby utworzyć zasady, uruchom następujące polecenie: 
    
      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"false"}}') -DisplayName "OmitBasicClaims" -Type "ClaimsMappingPolicy"
      ```
   2. Aby wyświetlić nowe zasady, a aby pobrać zasady wymagane ObjectId, uruchom następujące polecenie:
    
      ``` powershell
      Get-AzureADPolicy
      ```
1. Przypisz zasady do jednostki usługi. Należy również pobrać ObjectId usługę podmiotu zabezpieczeń.
   1. Aby wyświetlić nazwy główne usług wszystkich w organizacji, można tworzyć zapytania programu Microsoft Graph. Lub w programie Azure AD Graph Explorer Zaloguj się do swojego konta usługi Azure AD.
   2. Jeśli masz identyfikator obiektu nazwy głównej usługi, uruchom następujące polecenie:  
     
      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

#### <a name="example-create-and-assign-a-policy-to-include-the-employeeid-and-tenantcountry-as-claims-in-tokens-issued-to-a-service-principal"></a>Przykład: Tworzenie i przypisywanie zasad, aby uwzględnić EmployeeID i TenantCountry jako oświadczenia w tokeny wystawione do nazwy głównej usługi

W tym przykładzie utworzysz zasadę, która dodaje EmployeeID i TenantCountry tokeny wystawione do podmiotów połączonej usługi. Identyfikatorem EmployeeID jest emitowany jako nazwa typ oświadczenia w tokeny SAML i tokenów Jwt. TenantCountry jest emitowany jako typ oświadczenia country zarówno w tokeny SAML, jak i tokenów Jwt. W tym przykładzie firma Microsoft nadal obejmują podstawowe oświadczenia w tokeny.

1. Utwórz mapowanie zasad oświadczeń. Ta zasada, połączone z jednostki określonej usługi, dodaje EmployeeID i TenantCountry oświadczenia na tokeny.
   1. Aby utworzyć zasady, uruchom następujące polecenie:  
     
      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name","JwtClaimType":"name"},{"Source":"company","ID":"tenantcountry","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/country","JwtClaimType":"country"}]}}') -DisplayName "ExtraClaimsExample" -Type "ClaimsMappingPolicy"
      ```
    
   2. Aby wyświetlić nowe zasady, a aby pobrać zasady wymagane ObjectId, uruchom następujące polecenie:
     
      ``` powershell  
      Get-AzureADPolicy
      ```
1. Przypisz zasady do jednostki usługi. Należy również pobrać ObjectId usługę podmiotu zabezpieczeń. 
   1. Aby wyświetlić nazwy główne usług wszystkich w organizacji, można tworzyć zapytania programu Microsoft Graph. Lub w programie Azure AD Graph Explorer Zaloguj się do swojego konta usługi Azure AD.
   2. Jeśli masz identyfikator obiektu nazwy głównej usługi, uruchom następujące polecenie:  
     
      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

#### <a name="example-create-and-assign-a-policy-that-uses-a-claims-transformation-in-tokens-issued-to-a-service-principal"></a>Przykład: Tworzenie i przypisywanie zasad, które używa przekształcania oświadczenia w tokeny wystawione do nazwy głównej usługi

W tym przykładzie należy utworzyć zasady, który emituje oświadczenia niestandardowego "JoinedData" do tokenów Jwt wystawione dla jednostki usługi połączonej. To oświadczenie zawiera wartość tworzony przez dołączenie danych przechowywanych w atrybucie extensionattribute1 w obiekcie użytkownika za pomocą ".sandbox". W tym przykładzie Wyłączamy podstawowe oświadczenia w tokeny.

1. Utwórz mapowanie zasad oświadczeń. Ta zasada, połączone z jednostki określonej usługi, dodaje EmployeeID i TenantCountry oświadczenia na tokeny.
   1. Aby utworzyć zasady, uruchom następujące polecenie:
     
      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema":[{"Source":"user","ID":"extensionattribute1"},{"Source":"transformation","ID":"DataJoin","TransformationId":"JoinTheData","JwtClaimType":"JoinedData"}],"ClaimsTransformations":[{"ID":"JoinTheData","TransformationMethod":"Join","InputClaims":[{"ClaimTypeReferenceId":"extensionattribute1","TransformationClaimType":"string1"}], "InputParameters": [{"ID":"string2","Value":"sandbox"},{"ID":"separator","Value":"."}],"OutputClaims":[{"ClaimTypeReferenceId":"DataJoin","TransformationClaimType":"outputClaim"}]}]}}') -DisplayName "TransformClaimsExample" -Type "ClaimsMappingPolicy"
      ```
    
   2. Aby wyświetlić nowe zasady, a aby pobrać zasady wymagane ObjectId, uruchom następujące polecenie: 
     
      ``` powershell
      Get-AzureADPolicy
      ```
1. Przypisz zasady do jednostki usługi. Należy również pobrać ObjectId usługę podmiotu zabezpieczeń. 
   1. Aby wyświetlić nazwy główne usług wszystkich w organizacji, można tworzyć zapytania programu Microsoft Graph. Lub w programie Azure AD Graph Explorer Zaloguj się do swojego konta usługi Azure AD.
   2. Jeśli masz identyfikator obiektu nazwy głównej usługi, uruchom następujące polecenie: 
     
      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="see-also"></a>Zobacz także

Aby dowiedzieć się, jak dostosowywanie oświadczeń wystawionych w tokenie języka SAML w witrynie Azure portal, zobacz [jak: Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw](active-directory-saml-claims-customization.md)