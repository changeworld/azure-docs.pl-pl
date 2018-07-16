---
title: Mapowanie oświadczeń w usłudze Azure Active Directory (publiczna wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Ta strona zawiera opis Mapowanie oświadczeń w usłudze Azure Active Directory.
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
ms.openlocfilehash: e6d2d8dfd6f7a40158b098983bd34bbd5d8271f0
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39049317"
---
# <a name="claims-mapping-in-azure-active-directory-public-preview"></a>Mapowanie oświadczeń w usłudze Azure Active Directory (publiczna wersja zapoznawcza)

>[!NOTE]
>Ta funkcja zastępuje i zastępuje [oświadczeń dostosowywania](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization) do obecnie oferowanych w portalu. W przypadku dostosowania przy użyciu portalu, oprócz metoda wykres/programu PowerShell, szczegółowo opisanych w tym dokumencie na tej samej aplikacji oświadczeń tokeny wystawiane, aplikacja będzie ignorować konfiguracji w portalu.
Konfiguracje wprowadzone za pomocą metod podanych w tym dokumencie nie zostaną odzwierciedlone w portalu.

Ta funkcja jest używana przez administratorów dzierżawy, aby dostosować oświadczeń emitowane w tokenach dla określonej aplikacji w ramach ich dzierżawy. Możesz użyć zasad w celu mapowanie oświadczeń:

- Wybierz, jakie oświadczenia są uwzględnione w tokenach.
- Utworzyć typy oświadczeń, które jeszcze nie istnieje.
- Wybierz lub Zmień źródło danych utworzonych w określonych oświadczeń.

>[!NOTE]
>Ta funkcja jest obecnie w publicznej wersji zapoznawczej. Przygotuj się na przywrócić lub usunąć wszelkie zmiany. Ta funkcja jest dostępna w dowolnej subskrypcji usługi Azure Active Directory (Azure AD) w okresie publicznej wersji zapoznawczej. Gdy ta funkcja stanie się ogólnie dostępna, niektóre cechy funkcji mogą jednak wymagać subskrypcji usługi Azure Active Directory — wersja premium. Ta funkcja obsługuje Konfigurowanie zasad Mapowanie oświadczeń dla protokołów WS-Fed, SAML, OAuth i OpenID Connect.

## <a name="claims-mapping-policy-type"></a>Mapowanie typu zasad oświadczeń
W usłudze Azure AD **zasad** obiekt reprezentuje zestaw reguł wymuszane poszczególne aplikacje lub wszystkie aplikacje w organizacji. Każdego typu zasad ma unikatowy struktury, zestaw właściwości, które są następnie stosowane do obiektów, które są przypisane.

A oświadczeń mapowanie zasad jest typem **zasad** obiekt, który modyfikuje oświadczenia emitowane w tokeny wystawione dla określonych aplikacji.

## <a name="claim-sets"></a>W zestawie oświadczeń
Istnieją pewne zestawy oświadczeń, które definiują, jak i kiedy są one używane w tokeny.

### <a name="core-claim-set"></a>Podstawowy zestaw oświadczeń
Oświadczenia w zestawie oświadczeń core znajdują się w każdy token, niezależnie od zasady. Te oświadczenia również są traktowane jako ograniczone i nie może być modyfikowany.

### <a name="basic-claim-set"></a>Zestaw oświadczeń podstawowe
Zestaw oświadczeń podstawowa zawiera oświadczenia, które są emitowane przez domyślną tokeny (oprócz podstawowy zestaw oświadczeń). Te oświadczenia można pominąć lub zmodyfikować przy użyciu oświadczeń mapowanie zasad.

### <a name="restricted-claim-set"></a>Zestaw oświadczeń z ograniczeniami
Nie można zmodyfikować ograniczeniami oświadczenia za pomocą zasad. Nie można zmienić źródła danych, a nie transformacja jest stosowana podczas generowania te oświadczenia.

#### <a name="table-1-json-web-token-jwt-restricted-claim-set"></a>Tabela 1: Tokenu Web JSON (JWT) ograniczony zestaw oświadczeń
|Typ oświadczenia (nazwa)|
| ----- |
|_claim_names|
|_claim_sources|
|access_token|
|account_type|
|acr|
|aktora|
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
|potwierdzenie|
|at_hash|
|AUD|
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
|grant_type|
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
|Dostawca tożsamości|
|dostawcy tożsamości|
|in_corp|
|wystąpienie|
|IPADDR|
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
|identyfikatora TID|
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

#### <a name="table-2-security-assertion-markup-language-saml-restricted-claim-set"></a>Tabela 2: Zabezpieczeń Assertion Markup Language (SAML) ograniczony zestaw oświadczeń
|Typ oświadczenia (identyfikator URI)|
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
Użyj właściwości oświadczenia mapowania zasad kontroli, jakie oświadczenia są emitowane i gdzie źródło danych. Jeśli żadna zasada jest ustawiona, system tokenów problemy, zawierający podstawowe oświadczenia zestawu, do zestawu oświadczeń podstawowe i wszystkich [opcjonalnych oświadczeń](develop/active-directory-optional-claims.md) wybrany aplikacji tak otrzymać.

### <a name="include-basic-claim-set"></a>Obejmują zestaw oświadczeń podstawowe

**Ciąg:** IncludeBasicClaimSet

**Typ danych:** atrybut typu wartość logiczna (True lub False)

**Podsumowanie:** ta właściwość określa, czy zestaw oświadczeń podstawowe znajduje się w tokeny wpływ tych zasad. 

- Jeśli ustawiono wartość True, wszystkie oświadczenia w zestawie oświadczeń podstawowe są emitowane w tokenach objęte zasadami. 
- Jeśli ma wartość False, oświadczenia w zestawie oświadczeń podstawowych nie są w tokenach, chyba że są one dodawane indywidualnie we właściwości schematu oświadczenia te same zasady.

>[!NOTE] 
>Oświadczenia w zestawie oświadczeń core znajdują się w każdy token, niezależnie od tego, co ta właściwość ma wartość. 

### <a name="claims-schema"></a>Schemat oświadczeń

**Ciąg:** ClaimsSchema

**Typ danych:** blob JSON z co najmniej jeden wpis schematu oświadczeń

**Podsumowanie:** ta właściwość określa, jakie oświadczenia są obecne w tokenach objęte zasadami, dodatkowo do zestawu oświadczeń podstawowe i podstawowego zestawu oświadczeń.
Dla każdego schematu oświadczenia wpisu zdefiniowane w tej właściwości niektóre informacje są wymagane. Należy określić, skąd pochodzą dane (**wartość** lub **par identyfikator/źródła**), i które roszczenie danych jest emitowany jako (**typu oświadczenia**).

### <a name="claim-schema-entry-elements"></a>Elementy wpis schematu oświadczeń

**Wartość:** element wartości definiuje wartość statyczną jako danych maszynowych w oświadczeniu.

**Para źródło/ID:** źródło i identyfikator elementów definiowania, gdzie źródło danych w oświadczeniu. 

Element źródłowy musi być ustawione na jedną z następujących czynności: 


- "user": dane w oświadczenie jest właściwością obiektu User. 
- "aplikacja": dane w oświadczenie jest właściwością nazwy głównej usługi aplikacji (klienta). 
- "Zasób": dane w oświadczenie jest właściwością zasobu jednostki usługi.
- "audience": dane w oświadczenie jest właściwością jednostki usługi, która jest odbiorców tokenu (klienta lub zasób nazwy głównej usługi).
- "Firma": dane w oświadczenie jest właściwością w obiekcie firmy dzierżawy zasobów.
- "przekształcenia": dane z oświadczenia pochodzą przekształcania oświadczeń (zobacz sekcję "Przekształcania oświadczeń" w dalszej części tego artykułu). 

Jeśli źródło jest przekształcania **TransformationID** elementu muszą być zawarte w tej definicji oświadczenia.

Elementu Identyfikatora Określa, która właściwość na "source" zawiera wartość oświadczenia. W poniższej tabeli wymieniono wartości Identyfikatora prawidłowy dla każdej wartości źródła.

#### <a name="table-3-valid-id-values-per-source"></a>Tabela 3: Nieprawidłowy identyfikator wartości dla każdego źródła
|Element źródłowy|ID|Opis|
|-----|-----|-----|
|Użytkownik|nazwisko|Nazwa rodziny|
|Użytkownik|Imię|Imię|
|Użytkownik|Nazwa wyświetlana|Nazwa wyświetlana|
|Użytkownik|Identyfikator obiektu|ObjectID|
|Użytkownik|poczta|Adres e-mail|
|Użytkownik|userprincipalname|Nazwa główna użytkownika|
|Użytkownik|Dział|Dział|
|Użytkownik|onpremisessamaccountname|Nazwy konta Sam lokalnie|
|Użytkownik|netbiosname|Nazwa NetBios|
|Użytkownik|dnsdomainname|Nazwa domeny DNS|
|Użytkownik|onpremisesecurityidentifier|Identyfikator zabezpieczeń w środowisku lokalnym|
|Użytkownik|companyname|Nazwa organizacji|
|Użytkownik|adres|Ulica i numer|
|Użytkownik|KodPocztowy|Kod pocztowy |
|Użytkownik|preferredlanguange|Preferowany język|
|Użytkownik|onpremisesuserprincipalname|Nazwa UPN w środowisku lokalnym|
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
|Użytkownik|othermail|Inne wiadomości E-mail|
|Użytkownik|Kraj|Kraj|
|Użytkownik|city|Miasto|
|Użytkownik|state|Stan|
|Użytkownik|stanowisko|Stanowisko|
|Użytkownik|EmployeeID|Identyfikator pracownika|
|Użytkownik|facsimiletelephonenumber|Numer faksu|
|Aplikacja zasobu, grupy odbiorców|Nazwa wyświetlana|Nazwa wyświetlana|
|Aplikacja zasobu, grupy odbiorców|obiekty|ObjectID|
|Aplikacja zasobu, grupy odbiorców|tags|Tag jednostki usługi|
|Firma|tenantcountry|Kraju dzierżawy|

**TransformationID:** elementu TransformationID należy podać tylko wtedy, gdy element źródła jest ustawiony na wartość "transformacji".

- Ten element musi być zgodny element identyfikator wpisu przekształcania w **ClaimsTransformation** właściwość, która definiuje, jak jest generowany danych dla tego oświadczenia.

**Typ oświadczenia:** **JwtClaimType** i **SamlClaimType** zdefiniować elementy, które roszczenie, ten wpis schematu oświadczenia odnosi się do.

- JwtClaimType musi zawierać nazwy oświadczenia, który był emitowany w tokenów Jwt.
- SamlClaimType musi zawierać identyfikator URI oświadczenia był emitowany w tokeny SAML.

>[!NOTE]
>Nie można użyć nazwy i identyfikatory URI oświadczeń w zestawie oświadczeń ograniczone dla elementów typu oświadczenia. Aby uzyskać więcej informacji zobacz sekcję "Wyjątki i ograniczenia" w dalszej części tego artykułu.

### <a name="claims-transformation"></a>Przekształcanie oświadczeń

**Ciąg:** ClaimsTransformation

**Typ danych:** obiektu blob JSON za pomocą co najmniej jeden wpis transformacji 

**Podsumowanie:** tej właściwości należy użyć do zastosowania typowych przekształceń do źródła danych, aby wygenerować dane wyjściowe do oświadczenia określone w schemacie oświadczeń.

**Identyfikator:** Użyj elementu ID, aby odwoływać się do tego wpisu przekształcania we wpisie TransformationID oświadczeń schematu. Ta wartość musi być unikatowy dla każdego wpisu transformacji, w ramach tych zasad.

**TransformationMethod:** elementu TransformationMethod identyfikuje, które jest wykonywane na potrzeby generowania danych oświadczenia.

Oparty na wybranej metody, oczekiwany jest zestaw danych wejściowych i wyjściowych. Są one zdefiniowane przy użyciu **InputClaims**, **InputParameters** i **OutputClaims** elementów.

#### <a name="table-4-transformation-methods-and-expected-inputs-and-outputs"></a>Tabela 4: Metody przekształcania i oczekiwane dane wejściowe i wyjściowe
|TransformationMethod|Oczekiwanych danych wejściowych|Oczekiwane dane wyjściowe|Opis|
|-----|-----|-----|-----|
|Join|ciąg1, ciąg2, separatora|oświadczenie outputClaim|Sprzężenia wejściowe ciągi przy użyciu separatora między. Na przykład: ciąg1: "foo@bar.com", ciąg2: "piaskownicy", separatora: "." skutkuje oświadczenie outputClaim: "foo@bar.com.sandbox"|
|ExtractMailPrefix|poczta|oświadczenie outputClaim|Wyodrębnia lokalnym składniku adresu e-mail. Na przykład: wiadomości e-mail: "foo@bar.com" skutkuje oświadczenie outputClaim: "foo". Jeśli nie \@ logowania jest obecny, a następnie ciąg wejściowy orignal jest zwracany, ponieważ jest.|

**InputClaims:** element InputClaims służy do przekazywania danych z wejścia schematu oświadczenia do przekształcenia. Zawiera dwa atrybuty: **ClaimTypeReferenceId** i **TransformationClaimType**.

- **ClaimTypeReferenceId** sprzężony z elementem identyfikator wpisu schematu oświadczenia, można znaleźć odpowiednich oświadczeń przychodzących. 
- **TransformationClaimType** służy do nadaj unikatową nazwę do tych danych wejściowych. Ta nazwa musi być zgodna oczekiwane dane wejściowe dla metody transformacji.

**InputParameters:** element InputParameters służy do przekazywania wartości stałej do przekształcenia. Zawiera dwa atrybuty: **wartość** i **identyfikator**.

- **Wartość** jest rzeczywista stała wartość do przekazania.
- **Identyfikator** służy do nadaj unikatową nazwę do tych danych wejściowych. Ta nazwa musi być zgodna oczekiwane dane wejściowe dla metody transformacji.

**OutputClaims:** element OutputClaims służy do przechowywania danych generowanych przez przekształcenie i powiązanie ich wpis schematu oświadczenia. Zawiera dwa atrybuty: **ClaimTypeReferenceId** i **TransformationClaimType**.

- **ClaimTypeReferenceId** sprzężony z Identyfikatorem wpisu schematu oświadczenia odnaleźć oświadczenia właściwe dane wyjściowe.
- **TransformationClaimType** służy do nadaj unikatową nazwę do tych danych wyjściowych. Ta nazwa musi odpowiadać oczekiwanych danych wyjściowych dla metody transformacji.

### <a name="exceptions-and-restrictions"></a>Wyjątki i ograniczenia

**Identyfikatora SAML NameID i nazwy UPN:** atrybutów, z których źródła wartości NameID i głównej nazwy użytkownika i przekształcenia oświadczeń, które są dozwolone, ale są ograniczone.

#### <a name="table-5-attributes-allowed-as-a-data-source-for-saml-nameid"></a>Tabela 5: Atrybuty dozwolone jako źródło danych dla identyfikatora SAML NameID
|Element źródłowy|ID|Opis|
|-----|-----|-----|
|Użytkownik|poczta|Adres e-mail|
|Użytkownik|userprincipalname|Nazwa główna użytkownika|
|Użytkownik|onpremisessamaccountname|Nazwy konta Sam lokalnie|
|Użytkownik|EmployeeID|Identyfikator pracownika|
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

#### <a name="table-6-transformation-methods-allowed-for-saml-nameid"></a>Tabela 6: Transformacja metody dozwolone dla identyfikatora SAML NameID
|TransformationMethod|Ograniczenia|
| ----- | ----- |
|ExtractMailPrefix|Brak|
|Join|Sufiks jest przyłączone do musi być zweryfikowaną domenę dzierżawy zasobów.|

### <a name="custom-signing-key"></a>Niestandardowy klucz podpisywania
Niestandardowego klucza podpisywania muszą być przypisane do obiektu jednostki usługi dla oświadczeń mapowania zasad, które zostały wprowadzone. Wszystkie tokeny wystawione, wpływ zasad, które są podpisane za pomocą tego klucza. Aplikacje należy skonfigurować tak, aby akceptować tokeny podpisane przy użyciu tego klucza. Dzięki temu po potwierdzeniu tokenów zostały zmodyfikowane przez twórcę mapowanie zasad oświadczeń. Chroni to aplikacje, od zasad utworzonych przez uczestników złośliwych działań Mapowanie oświadczeń.

### <a name="cross-tenant-scenarios"></a>Scenariusze międzydzierżawowe
Mapowanie zasad oświadczeń nie dotyczą użytkowników-gości. Jeżeli użytkownik-Gość próbuje uzyskać dostęp do aplikacji przy użyciu oświadczeń mapowanie zasady przypisane do jego nazwy głównej usługi, zgłaszany jest domyślny token (zasada nie ma znaczenia).

## <a name="claims-mapping-policy-assignment"></a>Mapowanie przypisania zasad oświadczeń
Mapowanie zasad oświadczeń można przypisać tylko do obiektów nazw głównych usług.

### <a name="example-claims-mapping-policies"></a>Przykład oświadczeń mapowania zasad

W usłudze Azure AD wiele scenariuszy są możliwe, gdy można dostosować emitowane w tokenach dla jednostek usług określonych oświadczeń. W tej sekcji części omówimy kilka typowych scenariuszy, które mogą pomóc w niejasny sposób używania Mapowanie typów zasad oświadczeń.

#### <a name="prerequisites"></a>Wymagania wstępne
Poniższe przykłady służy do tworzenia, aktualizacji, łączenie i usuwania zasad dla jednostki usługi. Jeśli jesteś nowym użytkownikiem usługi Azure AD, firma Microsoft zaleca, Dowiedz się o tym, jak uzyskać dzierżawę usługi Azure AD, przed wykonaniem tych przykładów. 

Aby rozpocząć pracę, wykonaj następujące czynności:


1. Pobierz najnowszy [modułu Azure AD PowerShell publicznej wersji zapoznawczej](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.127).
2.  Uruchom polecenie Connect, aby zalogować się do konta administratora usługi Azure AD. Uruchom to polecenie za każdym razem, Rozpocznij nową sesję.
    
     ``` powershell
    Connect-AzureAD -Confirm
    
    ```
3.  Aby wyświetlić wszystkie zasady, które zostały utworzone w Twojej organizacji, uruchom następujące polecenie. Zaleca się, że to polecenie jest uruchamiane po większość operacji w następujących scenariuszach, aby sprawdzić, czy zasady są tworzone zgodnie z oczekiwaniami.
   
    ``` powershell
        Get-AzureADPolicy
    
    ```
#### <a name="example-create-and-assign-a-policy-to-omit-the-basic-claims-from-tokens-issued-to-a-service-principal"></a>Przykład: Tworzenie i przypisywanie zasad, aby pominąć podstawowe oświadczeń z tokeny wystawione do nazwy głównej usługi.
W tym przykładzie utworzysz zasady, które powoduje usunięcie podstawowego zestawu oświadczeń z tokeny wystawione do podmiotów połączonej usługi.


1. Utwórz mapowanie zasad oświadczeń. Te zasady usługi połączonej do określonych jednostek, usuwa zestawu z tokenów oświadczeń podstawowe.
    1. Aby utworzyć zasady, uruchom następujące polecenie: 
    
     ``` powershell
    New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"false"}}') -DisplayName "OmitBasicClaims” -Type "ClaimsMappingPolicy"
    ```
    2. Aby wyświetlić nowe zasady, a aby pobrać zasady wymagane ObjectId, uruchom następujące polecenie:
    
     ``` powershell
    Get-AzureADPolicy
    ```
2.  Przypisz zasady do jednostki usługi. Należy również pobrać ObjectId usługę podmiotu zabezpieczeń. 
    1.  Aby wyświetlić nazwy główne usług wszystkich w organizacji, można tworzyć zapytania programu Microsoft Graph. Lub w programie Azure AD Graph Explorer Zaloguj się do swojego konta usługi Azure AD.
    2.  Jeśli masz identyfikator obiektu nazwy głównej usługi, uruchom następujące polecenie:  
     
     ``` powershell
    Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
    ```
#### <a name="example-create-and-assign-a-policy-to-include-the-employeeid-and-tenantcountry-as-claims-in-tokens-issued-to-a-service-principal"></a>Przykład: Tworzenie i przypisywanie zasad w celu włączenia EmployeeID i TenantCountry jako oświadczenia w tokeny wystawione do nazwy głównej usługi.
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
2.  Przypisz zasady do jednostki usługi. Należy również pobrać ObjectId usługę podmiotu zabezpieczeń. 
    1.  Aby wyświetlić nazwy główne usług wszystkich w organizacji, można tworzyć zapytania programu Microsoft Graph. Lub w programie Azure AD Graph Explorer Zaloguj się do swojego konta usługi Azure AD.
    2.  Jeśli masz identyfikator obiektu nazwy głównej usługi, uruchom następujące polecenie:  
     
     ``` powershell
    Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
    ```
#### <a name="example-create-and-assign-a-policy-that-uses-a-claims-transformation-in-tokens-issued-to-a-service-principal"></a>Przykład: Utwórz i przypisz zasady, które używa przekształcania oświadczenia w tokeny wystawione do nazwy głównej usługi.
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
2.  Przypisz zasady do jednostki usługi. Należy również pobrać ObjectId usługę podmiotu zabezpieczeń. 
    1.  Aby wyświetlić nazwy główne usług wszystkich w organizacji, można tworzyć zapytania programu Microsoft Graph. Lub w programie Azure AD Graph Explorer Zaloguj się do swojego konta usługi Azure AD.
    2.  Jeśli masz identyfikator obiektu nazwy głównej usługi, uruchom następujące polecenie: 
     
     ``` powershell
    Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
    ```
