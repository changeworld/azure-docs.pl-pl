---
title: Oświadczenia mapowanie w usłudze Azure Active Directory (publicznej wersji zapoznawczej) | Dokumentacja firmy Microsoft
description: Na tej stronie opisano mapowania oświadczenia usługi Azure Active Directory.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2017
ms.author: billmath
ms.openlocfilehash: 04fa23e059ee676ba0e7c48eeea3361b85af5415
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261208"
---
# <a name="claims-mapping-in-azure-active-directory-public-preview"></a>Oświadczenia mapowanie w usłudze Azure Active Directory (publicznej wersji zapoznawczej)

>[!NOTE]
>Ta funkcja zastępuje i zastępuje [oświadczeń dostosowania](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization) obecnie dostępna za pośrednictwem portalu. Jeśli musisz dostosować przy użyciu portalu oprócz metody PowerShell wykres szczegółowo opisane w tym dokumencie na tej samej aplikacji oświadczeń, tokeny wystawiony dla czy aplikacja zignoruje konfiguracją w portalu.
Konfiguracje wprowadzone przy użyciu metod podanych w tym dokumencie nie zostaną odzwierciedlone w portalu.

Ta funkcja jest używana przez administratorów dzierżawy, aby dostosować oświadczeń wysyłanego w tokenach dla określonej aplikacji w swojej dzierżawy. Można użyć oświadczeń mapowanie zasad do:

- Wybierz, jakie oświadczenia są uwzględnione w tokenach.
- Tworzenie typów oświadczeń, które jeszcze nie istnieje.
- Wybierz lub Zmień źródło danych emitowanych w określonych oświadczeń.

>[!NOTE]
>Ta funkcja jest obecnie w wersji zapoznawczej. Przygotuj się do przywrócenia lub Usuń wszystkie zmiany. Funkcja jest dostępna w żadnych subskrypcji usługi Azure Active Directory (Azure AD) w publicznej wersji zapoznawczej. Gdy funkcja stanie się ogólnie dostępna, niektórych aspektów funkcji mogą jednak wymagać subskrypcję usługi Azure Active Directory premium. Ta funkcja obsługuje Konfigurowanie zasad mapowania oświadczeń protokołu WS-Fed, SAML, OAuth i OpenID Connect.

## <a name="claims-mapping-policy-type"></a>Mapowanie typu zasad oświadczeń
W usłudze Azure AD **zasad** obiekt reprezentuje zestaw reguł wymuszane na poszczególnych aplikacji lub na wszystkie aplikacje w organizacji. Każdy typ zasad ma unikatową strukturę, z zestawem właściwości, które następnie są stosowane do obiektów, do których jest przypisany.

Oświadczenia A mapowania zasad jest typem **zasad** obiekt, który modyfikuje oświadczenia emitowanych w tokenów wystawionych dla określonych aplikacji.

## <a name="claim-sets"></a>W zestawie oświadczeń
Istnieją pewne zestawy oświadczeń, które określają, jak i kiedy są używane w tokenach.

### <a name="core-claim-set"></a>Podstawowy zestaw oświadczeń
Oświadczeniami w zestawie oświadczeń core znajdują się w każdym tokenu, niezależnie od zasady. Te oświadczenia również są traktowane jako ograniczona i nie może być modyfikowany.

### <a name="basic-claim-set"></a>Zestaw oświadczeń podstawowe
Zestaw oświadczeń podstawowa zawiera oświadczenia, które są emitowane domyślnie tokeny (oprócz zestawu oświadczeń core). Te oświadczenia mogą pominięcia lub modyfikować za pomocą oświadczeń mapowanie zasad.

### <a name="restricted-claim-set"></a>Zestaw oświadczeń ograniczone
Nie można zmodyfikować ograniczeniami oświadczeń przy użyciu zasad. Nie można zmienić źródła danych, a nie transformacja jest stosowana podczas generowania tych oświadczeń.

#### <a name="table-1-json-web-token-jwt-restricted-claim-set"></a>Tabela 1: Tokenu Web JSON (JWT) ograniczony zestaw oświadczeń
|Typ oświadczenia (nazwa)|
| ----- |
|_claim_names|
|_claim_sources|
|access_token|
|account_type|
|acr|
|Aktora|
|actortoken|
|AIO|
|altsecid|
|amr|
|app_chain|
|app_displayname|
|app_res|
|appctx|
|appctxsender|
|Identyfikator aplikacji|
|appidacr|
|Potwierdzenia|
|at_hash|
|lub|
|auth_data|
|auth_time|
|authorization_code|
|azp|
|azpacr|
|c_hash|
|ca_enf|
|cc|
|cert_token_use|
|client_id|
|cloud_graph_host_name|
|cloud_instance_name|
|opcją cnf|
|Kod|
|funkcje sterowania|
|credential_keys|
|Renderowanie po stronie klienta|
|csr_type|
|Identyfikator urządzenia|
|dns_names|
|domain_dns_name|
|domain_netbios_name|
|e_exp|
|wyślij wiadomość e-mail|
|endpoint|
|enfpolids|
|EXP|
|expires_on|
|Typ grant_type|
|wykres|
|group_sids|
|grupy|
|hasgroups|
|hash_alg|
|home_oid|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/expired|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier|
|IAT|
|identityprovider|
|IDP|
|in_corp|
|wystąpienie|
|adres_IP|
|isbrowserhostedapp|
|iss|
|jwk|
|key_id|
|key_type|
|mam_compliance_url|
|mam_enrollment_url|
|mam_terms_of_use_url|
|mdm_compliance_url|
|mdm_enrollment_url|
|mdm_terms_of_use_url|
|nameid|
|nbf|
|netbios_name|
|Identyfikator jednorazowy|
|Identyfikator OID|
|on_prem_id|
|onprem_sam_account_name|
|onprem_sid|
|openid2_id|
|hasło|
|platf|
|polids|
|pop_jwk|
|preferred_username|
|previous_refresh_token|
|primary_sid|
|Identyfikator PUID|
|pwd_exp|
|pwd_url|
|redirect_uri|
|refresh_token|
|refreshtoken|
|request_nonce|
|zasób|
|rola|
|role|
|scope|
|scp|
|Identyfikator SID|
|Podpis|
|signin_state|
|src1|
|src2|
|Sub|
|tbid|
|tenant_display_name|
|tenant_region_scope|
|thumbnail_photo|
|TID|
|tokenAutologonEnabled|
|trustedfordelegation|
|unique_name|
|nazwy UPN|
|user_setting_sync_url|
|nazwa użytkownika|
|uti|
|VER|
|verified_primary_email|
|verified_secondary_email|
|wids|
|win_ver|

#### <a name="table-2-security-assertion-markup-language-saml-restricted-claim-set"></a>Tabela 2: Security (Assertion Markup Language SAML) ograniczony zestaw oświadczeń
|Typ oświadczenia (URI)|
| ----- |
|http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/expired|
|http://schemas.microsoft.com/identity/claims/accesstoken|
|http://schemas.microsoft.com/identity/claims/openid2_id|
|http://schemas.microsoft.com/identity/claims/identityprovider|
|http://schemas.microsoft.com/identity/claims/objectidentifier|
|http://schemas.microsoft.com/identity/claims/puid|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier[MR1] |
|http://schemas.microsoft.com/identity/claims/tenantid|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod|
|http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/groups|
|http://schemas.microsoft.com/claims/groups.link|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/role|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/wids|
|http://schemas.microsoft.com/2014/09/devicecontext/claims/iscompliant|
|http://schemas.microsoft.com/2014/02/devicecontext/claims/isknown|
|http://schemas.microsoft.com/2012/01/devicecontext/claims/ismanaged|
|http://schemas.microsoft.com/2014/03/psso|
|http://schemas.microsoft.com/claims/authnmethodsreferences|
|http://schemas.xmlsoap.org/ws/2009/09/identity/claims/actor|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/samlissuername|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/confirmationkey|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/primarygroupsid|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authorizationdecision|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authentication|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/sid|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarygroupsid|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarysid|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/denyonlysid|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlywindowsdevicegroup|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdeviceclaim|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdevicegroup|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsfqbnversion|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/windowssubauthority|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsuserclaim|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/x500distinguishedname|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/ispersistent|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/privatepersonalidentifier|
|http://schemas.microsoft.com/identity/claims/scope|

## <a name="claims-mapping-policy-properties"></a>Mapowanie właściwości zasad oświadczeń
Użyj właściwości mapowania zasad kontroli jakie oświadczenia są emitowane, gdy dane są uzyskiwane z oświadczeń. Jeśli żadne zasady nie jest ustawiona, tokeny problemów systemu zawierającego podstawową oświadczeń zestaw, do zestawu oświadczeń podstawowe i wszystkie [opcjonalnych oświadczeń](develop/active-directory-optional-claims.md) wybrany aplikacji tak otrzymywać.

### <a name="include-basic-claim-set"></a>Obejmują zestaw oświadczeń podstawowe

**Ciąg:** IncludeBasicClaimSet

**Typ danych:** wartość logiczną (True lub False)

**Podsumowanie:** ta właściwość określa, czy w zestawie oświadczeń podstawowa jest uwzględnione w tokenach wpływ tych zasad. 

- Jeśli ma wartość True, wszystkie oświadczenia w zestawie oświadczeń podstawowe są emitowane w tokenach objęte zasadami. 
- Jeśli ma wartość False, oświadczeniami w zestawie oświadczeń podstawowych nie są w tokenach, chyba, że są one dodawane indywidualnie we właściwości schematu oświadczenia te same zasady.

>[!NOTE] 
>Oświadczeniami w zestawie oświadczeń core znajdują się w każdym tokenu, niezależnie od tego, co ta właściwość jest ustawiona na. 

### <a name="claims-schema"></a>Schemat oświadczeń

**Ciąg:** ClaimsSchema

**Typ danych:** obiektu blob JSON z jednego lub więcej wpisów schematu oświadczeń

**Podsumowanie:** właściwość ta definiuje, jakie oświadczenia są obecne w tokeny wpływ zasad, oprócz do zestawu oświadczeń podstawowe i podstawowy zestaw oświadczeń.
Dla każdego schematu oświadczeń wpisu zdefiniowane w tej właściwości niektóre informacje są wymagane. Należy określić, gdzie dane pochodzące ze (**wartość** lub **pary źródło/identyfikator**), i który oświadczeń danych jest emitowany jako (**typu oświadczenia**).

### <a name="claim-schema-entry-elements"></a>Elementy schematu wpisu oświadczeń

**Wartość:** element wartości definiuje wartości statycznej jako dane być emitowane w oświadczeniu.

**Identyfikator źródłowego/para:** elementy źródłowy i identyfikator zdefiniować, gdzie dane w oświadczenia są uzyskiwane z. 

Element źródła musi być ustawiony na jedną z następujących: 


- "użytkownika": dane z oświadczeń jest właściwością obiektu User. 
- "aplikacja": dane z oświadczeń to właściwość nazwy głównej usługi aplikacji (klienta). 
- "zasobu": dane z oświadczeń to właściwość nazwy głównej usługi zasobów.
- "audience": dane z oświadczeń to właściwość nazwy głównej usługi, który jest odbiorcy tokenu (klienta lub zasobu nazwy głównej usługi).
- "firmy": dane z oświadczeń jest właściwością w obiekcie firmy dzierżawy zasobów.
- "transformacji": dane z oświadczeń pochodzą z przekształcania oświadczeń (zobacz sekcję "Przekształcanie oświadczeń" w dalszej części tego artykułu). 

Jeśli źródło jest przekształcania **TransformationID** elementu muszą być zawarte w tej definicji oświadczenia.

Elementu ID identyfikuje, które właściwości w źródle zawiera wartość oświadczenia. W poniższej tabeli wymieniono wartości identyfikatora jest nieprawidłowa dla każdej wartości źródła.

#### <a name="table-3-valid-id-values-per-source"></a>Tabela 3: Prawidłowy identyfikator wartości dla każdego źródła
|Element źródłowy|ID|Opis|
|-----|-----|-----|
|Użytkownik|nazwisko|Nazwa rodziny|
|Użytkownik|Imię|Imię|
|Użytkownik|Nazwa wyświetlana|Nazwa wyświetlana|
|Użytkownik|Identyfikator obiektu|ObjectID|
|Użytkownik|poczta|Adres e-mail|
|Użytkownik|userprincipalname|Nazwa główna użytkownika|
|Użytkownik|dział|Dział|
|Użytkownik|onpremisessamaccountname|Dla nazwy konta Sam lokalne|
|Użytkownik|Nazwa NetBIOS|Nazwa NetBios|
|Użytkownik|dnsdomainname|Nazwa domeny DNS|
|Użytkownik|onpremisesecurityidentifier|Identyfikator zabezpieczeń lokalnych|
|Użytkownik|companyname|Nazwa organizacji|
|Użytkownik|adres|Ulica i numer|
|Użytkownik|KodPocztowy|Kod pocztowy |
|Użytkownik|preferredlanguange|Preferowany język|
|Użytkownik|onpremisesuserprincipalname|lokalną nazwą UPN|
|Użytkownik|mailnickname|Pseudonim związany z pocztą|
|Użytkownik|extensionattribute1|Atrybut rozszerzenia 1|
|Użytkownik|extensionattribute2|Atrybut rozszerzenia 2|
|Użytkownik|extensionattribute3|Atrybut rozszerzenia 3|
|Użytkownik|extensionattribute4|Atrybut rozszerzenia 4|
|Użytkownik|extensionattribute5|Atrybut rozszerzenia 5|
|Użytkownik|extensionattribute6|Atrybut rozszerzenia 6|
|Użytkownik|extensionattribute7|Atrybut rozszerzenia 7|
|Użytkownik|extensionattribute8|Atrybut rozszerzenia 8|
|Użytkownik|extensionattribute9|Atrybut rozszerzenia 9|
|Użytkownik|extensionattribute10|Atrybut rozszerzenia 10|
|Użytkownik|extensionattribute11|Atrybut rozszerzenia 11|
|Użytkownik|extensionattribute12|Atrybut rozszerzenia 12|
|Użytkownik|extensionattribute13|Atrybut rozszerzenia 13|
|Użytkownik|extensionattribute14|Atrybut rozszerzenia 14|
|Użytkownik|extensionattribute15|Atrybut rozszerzenia 15|
|Użytkownik|othermail|Inne poczty|
|Użytkownik|Kraju|Kraj|
|Użytkownik|city|Miasto|
|Użytkownik|state|Stan|
|Użytkownik|stanowisko|Stanowisko|
|Użytkownik|Identyfikator pracownika|Identyfikator pracownika|
|Użytkownik|facsimiletelephonenumber|Numer telefonu faksów|
|Aplikacja, zasobu, grupy odbiorców|Nazwa wyświetlana|Nazwa wyświetlana|
|Aplikacja, zasobu, grupy odbiorców|obiekty|ObjectID|
|Aplikacja, zasobu, grupy odbiorców|tags|Etykieta nazwy głównej usługi|
|Firma|tenantcountry|Dzierżawcy kraju|

**TransformationID:** elementu TransformationID należy podać tylko wtedy, gdy element źródłowy ma ustawioną wartość "transformacji".

- Ten element musi być zgodna elementu Identyfikatora wpisu przekształcania **ClaimsTransformation** właściwość, która określa, jak jest generowany dane dla tego oświadczenia.

**Typ oświadczenia:** **JwtClaimType** i **SamlClaimType** elementy zdefiniować, które oświadczenia, ten wpis schematu oświadczenie odnosi się do.

- JwtClaimType musi zawierać nazwę oświadczenia, które można emitowanych w tokenów Jwt.
- SamlClaimType musi zawierać identyfikator URI oświadczenia być emitowane w tokenach SAML.

>[!NOTE]
>Nie można używać nazwy i identyfikatory URI oświadczeń w zestawie oświadczeń ograniczeniami dla elementów typu oświadczenia. Aby uzyskać więcej informacji zobacz sekcję "Wyjątki i ograniczenia" w dalszej części tego artykułu.

### <a name="claims-transformation"></a>Przekształcanie oświadczeń

**Ciąg:** ClaimsTransformation

**Typ danych:** obiektu blob JSON z jednego lub więcej wpisów przekształcania 

**Podsumowanie:** ta właściwość służy do stosowania wspólnego przekształcenia do źródła danych, można wygenerować danych wyjściowych dla oświadczeń określonej w schemacie oświadczeń.

**Identyfikator:** Użyj elementu ID, aby odwoływać ten wpis przekształcania we wpisie TransformationID oświadczeń schematu. Ta wartość musi być unikatowa dla każdego wpisu transformacji w ramach tych zasad.

**TransformationMethod:** elementu TransformationMethod identyfikuje, które jest wykonywane wygenerowane dane oświadczenia.

Oparte na wybranej metody, oczekiwano zestaw danych wejściowych i wyjściowych. Są one zdefiniowane przy użyciu **InputClaims**, **InputParameters** i **OutputClaims** elementów.

#### <a name="table-4-transformation-methods-and-expected-inputs-and-outputs"></a>Tabela 4: Metody przekształcania, a oczekiwano wejścia i wyjścia
|TransformationMethod|Oczekiwano danych wejściowych|Oczekiwane dane wyjściowe|Opis|
|-----|-----|-----|-----|
|Join|ciąg1, ciąg2, separatora|outputClaim|Sprzężenia Wprowadź ciągi za pomocą separatora między nimi. Na przykład: ciąg1: "foo@bar.com", ciąg2: "piaskownicy", separatora: "." powoduje outputClaim: "foo@bar.com.sandbox"|
|ExtractMailPrefix|poczta|outputClaim|Wyodrębnia lokalnego część adresu e-mail. Na przykład: poczty: "foo@bar.com" powoduje outputClaim: "foo". Jeśli nie @ znak jest nie istnieje, a następnie orignal ciąg wejściowy jest zwracany, ponieważ jest.|

**InputClaims:** Użyj elementu InputClaims, aby przekazać dane z wpisu schematu oświadczenia do przekształcenia. Zawiera dwa atrybuty: **ClaimTypeReferenceId** i **TransformationClaimType**.

- **ClaimTypeReferenceId** jest połączony z elementu Identyfikatora wpisu schematu oświadczeń można znaleźć odpowiednich oświadczeń przychodzących. 
- **TransformationClaimType** należy nadać unikatową nazwę do tych danych wejściowych. Ta nazwa musi pasować oczekiwane dane wejściowe dla metody przekształcania.

**InputParameters:** element InputParameters służy do przekazania wartości stałej do przekształcenia. Zawiera dwa atrybuty: **wartość** i **identyfikator**.

- **Wartość** jest rzeczywista wartość stałej do przekazania.
- **Identyfikator** należy nadać unikatową nazwę do tych danych wejściowych. Ta nazwa musi pasować oczekiwane dane wejściowe dla metody przekształcania.

**OutputClaims:** element OutputClaims służy do przechowywania danych wygenerowanych przez transformację i powiązanie jej wpis schematu oświadczeń. Zawiera dwa atrybuty: **ClaimTypeReferenceId** i **TransformationClaimType**.

- **ClaimTypeReferenceId** jest połączony z identyfikator wpisu schematu oświadczeń można znaleźć oświadczeń wychodzących odpowiednie.
- **TransformationClaimType** należy nadać unikatową nazwę tego raportu. Ta nazwa musi pasować oczekiwane dane wyjściowe metody przekształcania.

### <a name="exceptions-and-restrictions"></a>Wyjątki i ograniczenia

**SAML NameID i UPN:** atrybutów, z których źródłowego wartości NameID i głównej nazwy użytkownika i przekształcenia oświadczeń, które są dozwolone, są ograniczone.

#### <a name="table-5-attributes-allowed-as-a-data-source-for-saml-nameid"></a>Tabela 5: Atrybuty dozwolone jako źródło danych dla SAML NameID
|Element źródłowy|ID|Opis|
|-----|-----|-----|
|Użytkownik|poczta|Adres e-mail|
|Użytkownik|userprincipalname|Nazwa główna użytkownika|
|Użytkownik|onpremisessamaccountname|Dla nazwy konta Sam lokalne|
|Użytkownik|Identyfikator pracownika|Identyfikator pracownika|
|Użytkownik|extensionattribute1|Atrybut rozszerzenia 1|
|Użytkownik|extensionattribute2|Atrybut rozszerzenia 2|
|Użytkownik|extensionattribute3|Atrybut rozszerzenia 3|
|Użytkownik|extensionattribute4|Atrybut rozszerzenia 4|
|Użytkownik|extensionattribute5|Atrybut rozszerzenia 5|
|Użytkownik|extensionattribute6|Atrybut rozszerzenia 6|
|Użytkownik|extensionattribute7|Atrybut rozszerzenia 7|
|Użytkownik|extensionattribute8|Atrybut rozszerzenia 8|
|Użytkownik|extensionattribute9|Atrybut rozszerzenia 9|
|Użytkownik|extensionattribute10|Atrybut rozszerzenia 10|
|Użytkownik|extensionattribute11|Atrybut rozszerzenia 11|
|Użytkownik|extensionattribute12|Atrybut rozszerzenia 12|
|Użytkownik|extensionattribute13|Atrybut rozszerzenia 13|
|Użytkownik|extensionattribute14|Atrybut rozszerzenia 14|
|Użytkownik|extensionattribute15|Atrybut rozszerzenia 15|

#### <a name="table-6-transformation-methods-allowed-for-saml-nameid"></a>Tabela 6: Metody przekształcania dozwolony dla SAML NameID
|TransformationMethod|Ograniczenia|
| ----- | ----- |
|ExtractMailPrefix|Brak|
|Join|Sufiks jest dołączony musi być zweryfikowanej domeny zasobów dzierżawy.|

### <a name="custom-signing-key"></a>Niestandardowe klucza podpisywania
Niestandardowy klucz podpisujący musi być przypisany do obiektu główną usługi dla oświadczeń mapowania zasad, które zostały wprowadzone. Wszystkie wystawione tokeny, które wpływ zasad są podpisane przy użyciu tego klucza. Aplikacje muszą być skonfigurowane do akceptowania tokeny podpisane przy użyciu tego klucza. Dzięki temu potwierdzenia tokeny zostały zmodyfikowane przez autora oświadczeń mapowania zasad. Chroni to aplikacje z oświadczeń mapowanie zasad utworzonych przez złośliwych osób.

### <a name="cross-tenant-scenarios"></a>Scenariusze między dzierżawy
Goście nie dotyczą mapowanie zasad oświadczeń. Jeśli użytkownik-Gość próbuje uzyskać dostęp do aplikacji z oświadczeń mapowania zasad przypisany do jego nazwy głównej usługi, zgłaszany jest domyślny token (zasada nie ma znaczenia).

## <a name="claims-mapping-policy-assignment"></a>Mapowanie przypisania zasad oświadczeń
Mapowanie zasad oświadczeń można przypisać tylko do obiektów głównych usługi.

### <a name="example-claims-mapping-policies"></a>Przykład oświadczeń mapowania zasad

W usłudze Azure AD wiele scenariuszy współbieżnie, gdy można dostosować oświadczeń wysyłanego w tokenach podmiotów określonej usługi. W tej sekcji możemy przeprowadzenie kilka typowych scenariuszy, które mogą pomóc Ci ujmij sposób użycia oświadczeń mapowania typu zasad.

#### <a name="prerequisites"></a>Wymagania wstępne
Poniższe przykłady służy do tworzenia, aktualizacji, łączenie i usuwania zasad dla nazwy główne usług. Jeśli jesteś nowym użytkownikiem usługi Azure AD, zaleca się więcej informacji o tym, jak uzyskać dzierżawę usługi Azure AD, przed rozpoczęciem pracy z tymi przykładami. 

Aby rozpocząć pracę, wykonaj następujące czynności:


1. Pobierz najnowszą [modułu Azure AD PowerShell publicznej wersji zapoznawczej](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.127).
2.  Uruchom polecenie Connect, aby zalogować się do konta administratora usługi Azure AD. Uruchom to polecenie za każdym razem, należy uruchomić nową sesję.
    
     ``` powershell
    Connect-AzureAD -Confirm
    
    ```
3.  Aby wyświetlić wszystkie zasady, które zostały utworzone w organizacji, uruchom następujące polecenie. Firma Microsoft zaleca uruchomienie tego polecenia po większości operacji w następujących scenariuszach, aby sprawdzić, czy zasady są tworzone zgodnie z oczekiwaniami.
   
    ``` powershell
        Get-AzureADPolicy
    
    ```
#### <a name="example-create-and-assign-a-policy-to-omit-the-basic-claims-from-tokens-issued-to-a-service-principal"></a>Przykład: Utworzyć i przypisać zasady, aby pominąć podstawowe oświadczeń z tokenów wystawionych do nazwy głównej usługi.
W tym przykładzie utworzysz zasady, która usuwa podstawowego zestawu oświadczeń z tokenów wystawionych do podmiotów połączonej usługi.


1. Utwórz mapowania zasad oświadczeń. Ta zasada podmiotów połączonej do określonej usługi, usuwa podstawowego zestawu z tokenów oświadczeń.
    1. Aby utworzyć zasady, uruchom następujące polecenie: 
    
     ``` powershell
    New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"false"}}') -DisplayName "OmitBasicClaims” -Type "ClaimsMappingPolicy"
    ```
    2. Aby wyświetlić nowe zasady i pobrania ObjectId zasad, uruchom następujące polecenie:
    
     ``` powershell
    Get-AzureADPolicy
    ```
2.  Przypisz zasady do Twojej nazwy głównej usługi. Należy również pobrania ObjectId usługi podmiotu zabezpieczeń. 
    1.  Aby wyświetlić nazwy główne usług wszystkich organizacji, można zbadać Microsoft Graph. Lub, w Eksploratorze Azure AD Graph, zaloguj się do swojego konta usługi Azure AD.
    2.  Jeśli masz ObjectId nazwy głównej usługi, uruchom następujące polecenie:  
     
     ``` powershell
    Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
    ```
#### <a name="example-create-and-assign-a-policy-to-include-the-employeeid-and-tenantcountry-as-claims-in-tokens-issued-to-a-service-principal"></a>Przykład: Utworzyć i przypisać zasady w celu włączenia identyfikator pracownika i TenantCountry jako oświadczenia w tokenach wystawiony dla nazwy głównej usługi.
W tym przykładzie można utworzyć zasadę, która dodaje identyfikator pracownika oraz TenantCountry do tokenów wystawionych do podmiotów połączonej usługi. Identyfikator pracownika jest emitowany jako nazwa typ oświadczenia w tokenach SAML i tokenów Jwt. TenantCountry jest emitowany jako typ oświadczenia country zarówno tokeny SAML i tokenów Jwt. W tym przykładzie w dalszym obejmują podstawowe oświadczenia w tokenów.

1. Utwórz mapowania zasad oświadczeń. Te zasady połączone z podmiotów określonej usługi, dodaje identyfikator pracownika i TenantCountry oświadczenia na tokeny.
    1. Aby utworzyć zasady, uruchom następujące polecenie:  
     
     ``` powershell
    New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name","JwtClaimType":"name"},{"Source":"company","ID":"tenantcountry","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/country","JwtClaimType":"country"}]}}') -DisplayName "ExtraClaimsExample" -Type "ClaimsMappingPolicy"
    ```
    
    2. Aby wyświetlić nowe zasady i pobrania ObjectId zasad, uruchom następujące polecenie:
     
     ``` powershell  
    Get-AzureADPolicy
    ```
2.  Przypisz zasady do Twojej nazwy głównej usługi. Należy również pobrania ObjectId usługi podmiotu zabezpieczeń. 
    1.  Aby wyświetlić nazwy główne usług wszystkich organizacji, można zbadać Microsoft Graph. Lub, w Eksploratorze Azure AD Graph, zaloguj się do swojego konta usługi Azure AD.
    2.  Jeśli masz ObjectId nazwy głównej usługi, uruchom następujące polecenie:  
     
     ``` powershell
    Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
    ```
#### <a name="example-create-and-assign-a-policy-that-uses-a-claims-transformation-in-tokens-issued-to-a-service-principal"></a>Przykład: Utworzyć i przypisać zasady, która używa przekształcania oświadczeń w tokenach wystawiony dla nazwy głównej usługi.
W tym przykładzie utworzysz zasady, które emituje oświadczenia niestandardowego "JoinedData" do tokenów Jwt wystawiony dla połączonej usługi podmiotów zabezpieczeń. To oświadczenie zawiera wartość powstały danych przechowywanych w atrybucie extensionattribute1 dla obiektu użytkownika o ".sandbox". W tym przykładzie Wyłączamy podstawowe oświadczenia w tokenów.


1. Utwórz mapowania zasad oświadczeń. Te zasady połączone z podmiotów określonej usługi, dodaje identyfikator pracownika i TenantCountry oświadczenia na tokeny.
    1. Aby utworzyć zasady, uruchom następujące polecenie: 
     
     ``` powershell
    New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema":[{"Source":"user","ID":"extensionattribute1"},{"Source":"transformation","ID":"DataJoin","TransformationId":"JoinTheData","JwtClaimType":"JoinedData"}],"ClaimsTransformations":[{"ID":"JoinTheData","TransformationMethod":"Join","InputClaims":[{"ClaimTypeReferenceId":"extensionattribute1","TransformationClaimType":"string1"}], "InputParameters": [{"ID":"string2","Value":"sandbox"},{"ID":"separator","Value":"."}],"OutputClaims":[{"ClaimTypeReferenceId":"DataJoin","TransformationClaimType":"outputClaim"}]}]}}') -DisplayName "TransformClaimsExample" -Type "ClaimsMappingPolicy" 
    ```
    
    2. Aby wyświetlić nowe zasady i pobrania ObjectId zasad, uruchom następujące polecenie: 
     
     ``` powershell
    Get-AzureADPolicy
    ```
2.  Przypisz zasady do Twojej nazwy głównej usługi. Należy również pobrania ObjectId usługi podmiotu zabezpieczeń. 
    1.  Aby wyświetlić nazwy główne usług wszystkich organizacji, można zbadać Microsoft Graph. Lub, w Eksploratorze Azure AD Graph, zaloguj się do swojego konta usługi Azure AD.
    2.  Jeśli masz ObjectId nazwy głównej usługi, uruchom następujące polecenie: 
     
     ``` powershell
    Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
    ```
