---
title: Dostosowywanie oświadczeń dzierżawy usługi Azure AD (program PowerShell)
titleSuffix: Microsoft identity platform
description: Na tej stronie opisano mapowanie oświadczeń usługi Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: 49860504da8dd2a1b994a23a24df95f59c959c90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263194"
---
# <a name="how-to-customize-claims-emitted-in-tokens-for-a-specific-app-in-a-tenant-preview"></a>Jak: Dostosowywanie oświadczeń emitowanych w tokenach dla określonej aplikacji w dzierżawie (wersja zapoznawcza)

> [!NOTE]
> Ta funkcja zastępuje i zastępuje [dostosowywanie oświadczeń](active-directory-saml-claims-customization.md) oferowanych za pośrednictwem portalu dzisiaj. W tej samej aplikacji, jeśli dostosujesz oświadczenia przy użyciu portalu oprócz Graph/PowerShell metody szczegółowo w tym dokumencie, tokeny wydane dla tej aplikacji zignoruje konfigurację w portalu. Konfiguracje wykonane za pomocą metod opisanych w tym dokumencie nie zostaną odzwierciedlone w portalu.

Ta funkcja jest używana przez administratorów dzierżawy, aby dostosować oświadczenia emitowane w tokenach dla określonej aplikacji w ich dzierżawie. Zasady mapowania oświadczeń można użyć do:

- Wybierz, które oświadczenia są uwzględniane w tokenach.
- Utwórz typy oświadczeń, które jeszcze nie istnieją.
- Wybierz lub zmień źródło danych emitowanych w określonych oświadczeń.

> [!NOTE]
> Ta funkcja jest obecnie w publicznej wersji zapoznawczej. Przygotuj się na przywracanie lub usuwanie wszelkich zmian. Ta funkcja jest dostępna w dowolnej subskrypcji usługi Azure Active Directory (Azure AD) podczas publicznej wersji zapoznawczej. Jednak gdy funkcja staje się ogólnie dostępna, niektóre aspekty funkcji może wymagać subskrypcji usługi Azure AD premium. Ta funkcja obsługuje konfigurowanie zasad mapowania oświadczeń dla protokołów WS-Fed, SAML, OAuth i OpenID Connect.

## <a name="claims-mapping-policy-type"></a>Typ zasad mapowania oświadczeń

W usłudze Azure AD **obiekt zasad** reprezentuje zestaw reguł wymuszanych dla poszczególnych aplikacji lub wszystkich aplikacji w organizacji. Każdy typ zasad ma unikatową strukturę z zestawem właściwości, które są następnie stosowane do obiektów, do których są przypisane.

Zasady mapowania oświadczeń to typ **obiektu zasad,** który modyfikuje oświadczenia emitowane w tokenach wystawionych dla określonych aplikacji.

## <a name="claim-sets"></a>Zestawy oświadczeń

Istnieją pewne zestawy oświadczeń, które definiują, jak i kiedy są używane w tokenach.

| Zestaw oświadczeń | Opis |
|---|---|
| Podstawowy zestaw oświadczeń | Są obecne w każdym tokenie, niezależnie od zasad. Oświadczenia te są również uważane za ograniczone i nie mogą być modyfikowane. |
| Podstawowy zestaw oświadczeń | Zawiera oświadczenia, które są emitowane domyślnie dla tokenów (oprócz podstawowego zestawu oświadczeń). Można pominąć lub zmodyfikować podstawowe oświadczenia przy użyciu zasad mapowania oświadczeń. |
| Ograniczony zestaw oświadczeń | Nie można zmodyfikować za pomocą zasad. Nie można zmienić źródła danych i nie jest stosowana transformacja podczas generowania tych oświadczeń. |

### <a name="table-1-json-web-token-jwt-restricted-claim-set"></a>Tabela 1: Zestaw oświadczeń z ograniczeniami tokenu JSON Web Token (JWT)

| Typ oświadczenia (nazwa) |
| ----- |
| _claim_names |
| _claim_sources |
| access_token |
| account_type |
| acr |
| Aktor |
| actortoken |
| Aio |
| altsecid (altsecid) |
| Amr |
| app_chain |
| app_displayname |
| app_res |
| appctx ( appctx ) |
| appctxsender |
| Appid |
| appidacr ( appidacr ) |
| Potwierdzenia |
| at_hash |
| AUD |
| auth_data |
| auth_time |
| authorization_code |
| Azp |
| azpacr ( azpacr ) |
| c_hash |
| ca_enf |
| cc |
| cert_token_use |
| client_id |
| cloud_graph_host_name |
| cloud_instance_name |
| Cnf |
| kod |
| funkcje sterowania |
| credential_keys |
| Csr |
| csr_type |
| Deviceid |
| dns_names |
| domain_dns_name |
| domain_netbios_name |
| e_exp |
| email |
| endpoint |
| enfpolids |
| exp |
| expires_on |
| grant_type |
| Wykres |
| group_sids |
| grupy |
| grupy dyskusyjne |
| hash_alg |
| home_oid |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/expired` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` |
| Iat |
| dostawca tożsamości |
| Idp |
| in_corp |
| Wystąpienie |
| ipaddr |
| isbrowserhostedapp |
| Iss |
| jwk ( jwk ) |
| Key_id |
| Key_type |
| mam_compliance_url |
| mam_enrollment_url |
| mam_terms_of_use_url |
| mdm_compliance_url |
| mdm_enrollment_url |
| mdm_terms_of_use_url |
| nazwaid |
| Nbf |
| netbios_name |
| nonce |
| Oid |
| on_prem_id |
| onprem_sam_account_name |
| onprem_sid |
| openid2_id |
| hasło |
| platf (platf) |
| polidy |
| pop_jwk |
| preferred_username |
| previous_refresh_token |
| primary_sid |
| puid |
| pwd_exp |
| pwd_url |
| redirect_uri |
| refresh_token |
| odświeżanie |
| request_nonce |
| zasób |
| role (rola) |
| role |
| scope |
| Scp |
| Sid |
| Podpis |
| signin_state |
| src1 |
| src2 (src2) |
| Sub |
| tbid ( tbid ) |
| tenant_display_name |
| tenant_region_scope |
| thumbnail_photo |
| Tid |
| tokenAutologonEnabled |
| trustedfordelegation |
| unique_name |
| Upn |
| user_setting_sync_url |
| nazwa użytkownika |
| Uti |
| Ver |
| verified_primary_email |
| verified_secondary_email |
| wids |
| win_ver |

### <a name="table-2-saml-restricted-claim-set"></a>Tabela 2: Zestaw oświadczeń z ograniczeniami SAML

| Typ oświadczenia (URI) |
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

## <a name="claims-mapping-policy-properties"></a>Właściwości zasad mapowania oświadczeń

Aby kontrolować, jakie oświadczenia są emitowane i skąd pochodzą dane, należy użyć właściwości zasad mapowania oświadczeń. Jeśli zasady nie jest ustawiona, system wystawia tokeny, które zawierają podstawowy zestaw oświadczeń, podstawowy zestaw oświadczeń i wszelkie [opcjonalne oświadczenia,](active-directory-optional-claims.md) które aplikacja wybrała do odbioru.

### <a name="include-basic-claim-set"></a>Uwzględnij podstawowy zestaw oświadczeń

**Ciąg:** Zestaw IncludeBasicClaimSet

**Typ danych:** Wartość logiczna (prawda lub fałsz)

**Krótki opis:** Ta właściwość określa, czy podstawowy zestaw oświadczeń jest uwzględniony w tokenach, których dotyczą te zasady.

- Jeśli ustawiona wartość True, wszystkie oświadczenia w podstawowym zestawie oświadczeń są emitowane w tokenach, których dotyczą zasady. 
- Jeśli ustawiona na False, oświadczenia w podstawowym zestawie oświadczeń nie znajdują się w tokenach, chyba że są one indywidualnie dodawane we właściwości schematu oświadczeń tej samej zasady.

> [!NOTE] 
> Oświadczenia w podstawowym zestawie oświadczeń są obecne w każdym tokenie, niezależnie od tego, co ta właściwość jest ustawiona. 

### <a name="claims-schema"></a>Schemat oświadczeń

**Ciąg:** ClaimsSchema (Roszczenia)

**Typ danych:** Obiekt blob JSON z co najmniej jednym wpisem schematu oświadczeń

**Krótki opis:** Ta właściwość definiuje, które oświadczenia są obecne w tokenach, których dotyczą zasady, oprócz podstawowego zestawu oświadczeń i zestawu oświadczeń podstawowych.
Dla każdego wpisu schematu oświadczenia zdefiniowanego w tej właściwości wymagane są pewne informacje. Określ, skąd pochodzą dane **(para wartość** lub **źródło/identyfikator)** i które oświadczenie, że dane są emitowane jako **(Typ oświadczenia).**

### <a name="claim-schema-entry-elements"></a>Elementy wpisu schematu oświadczeń

**Wartość:** Value Element definiuje wartość statyczną jako dane, które mają być emitowane w oświadczeniu.

**Źródło/ID para:** Źródło i identyfikator elementy określają, gdzie dane w oświadczeń pochodzą z. 

Ustaw element Source na jedną z następujących wartości: 

- "użytkownik": dane w oświadczeniu są właściwością obiektu Użytkownika. 
- "application": dane w oświadczeniu jest właściwością na jednostkę usługi aplikacji (klienta). 
- "zasób": dane w oświadczeniu jest właściwością jednostki usługi zasobów.
- "audience": dane w oświadczeniu jest właściwością jednostki usługi, która jest odbiorcą tokenu (klient lub podmiot usługi zasobów).
- "firma": dane w oświadczeniu jest właściwością na dzierżawcy zasobów firmy obiektu.
- "transformacja": dane w żądaniu pochodzą z przekształcania oświadczeń (zobacz sekcję "Transformacja oświadczeń" w dalszej części tego artykułu).

Jeśli źródłem jest transformacja, **TransformationID** element musi być uwzględniony w tej definicji oświadczenia, jak również.

Element identyfikatora identyfikuje właściwość w źródle zapewnia wartość oświadczenia. W poniższej tabeli wymieniono wartości identyfikatora prawidłowe dla każdej wartości source.

#### <a name="table-3-valid-id-values-per-source"></a>Tabela 3: Prawidłowe wartości identyfikatorów na źródło

| Element źródłowy | ID | Opis |
|-----|-----|-----|
| Użytkownik | surname | Nazwisko |
| Użytkownik | givenname | Imię |
| Użytkownik | displayname | Nazwa wyświetlana |
| Użytkownik | Objectid | ObjectID |
| Użytkownik | mail (poczta) | Adres e-mail |
| Użytkownik | Userprincipalname | Nazwa główna użytkownika |
| Użytkownik | department|Dział|
| Użytkownik | onpremisessamaccountname | Lokalna nazwa konta SAM |
| Użytkownik | netbiosname (nazwa netto)| Nazwa NetBios |
| Użytkownik | dnsdomename | Nazwa domeny DNS |
| Użytkownik | onpremisesecurityidentifier | Lokalny identyfikator zabezpieczeń |
| Użytkownik | Companyname| Nazwa organizacji |
| Użytkownik | Streetaddress | Ulica i numer |
| Użytkownik | Postalcode | Postal Code |
| Użytkownik | preferowanylanguange | Preferowany język |
| Użytkownik | onpremisesuserprincipalname | Lokalna lokalna łań. |
| Użytkownik | nazwa pocztowa | Pseudonim poczty |
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
| Użytkownik | innymail | Inna poczta |
| Użytkownik | country | Kraj |
| Użytkownik | city | Miasto |
| Użytkownik | state | Stan |
| Użytkownik | tytuł pracy | Stanowisko |
| Użytkownik | employeeid | Identyfikator pracownika |
| Użytkownik | numer facsimiletelephonenumber | Faksymile Numer telefonu |
| aplikacja, zasób, publiczność | displayname | Nazwa wyświetlana |
| aplikacja, zasób, publiczność | Sprzeciwił | ObjectID |
| aplikacja, zasób, publiczność | tags | Tag głównego dostawcy usługi |
| Firma | kraj dzierżawcy | Kraj najemcy |

**TransformID:** Element TransformationID musi być podany tylko wtedy, gdy source element jest ustawiony na "transformacja".

- Ten element musi być zgodny z elementem identyfikator wpisu transformacji we właściwości **ClaimsTransformation,** która definiuje sposób generowania danych dla tego oświadczenia.

**Typ oświadczenia:** **JwtClaimType** i **SamlClaimType** elementy definiują, które twierdzą, że ten wpis schematu oświadczenia odwołuje się do.

- JwtClaimType musi zawierać nazwę oświadczenia, które mają być emitowane w JWTs.
- SamlClaimType musi zawierać identyfikator URI oświadczenia, które mają być emitowane w tokenach SAML.

> [!NOTE]
> Nazwy i identyfikatory URI oświadczeń w zestawie oświadczeń z ograniczeniami nie mogą być używane dla elementów typu oświadczenia. Aby uzyskać więcej informacji, zobacz sekcję "Wyjątki i ograniczenia" w dalszej części tego artykułu.

### <a name="claims-transformation"></a>Przekształcanie oświadczeń

**Ciąg:** ReklamacjeTransformacja roszczeń

**Typ danych:** Obiekt blob JSON z co najmniej jednym wpisem transformacji 

**Krótki opis:** Ta właściwość służy do stosowania typowych przekształceń do danych źródłowych, do generowania danych wyjściowych dla oświadczeń określonych w schemacie oświadczeń.

**Identyfikator:** Użyj elementu Identyfikator, aby odwołać się do tego wpisu transformacji we wpisie Schemat oświadczeń TransformID. Ta wartość musi być unikatowa dla każdego wpisu transformacji w ramach tej zasady.

**TransformationMethod:** Element TransformationMethod identyfikuje, która operacja jest wykonywana w celu wygenerowania danych dla oświadczenia.

Na podstawie wybranej metody oczekuje się zestawu wejść i wyjść. Zdefiniuj wejścia i wyjścia za pomocą elementów **InputClaims**, **InputParameters** i **OutputClaims.**

#### <a name="table-4-transformation-methods-and-expected-inputs-and-outputs"></a>Tabela 4: Metody transformacji oraz oczekiwane wejścia i wyjścia

|TransformationMethod (Metodyka transformacji)|Oczekiwane dane wejściowe|Oczekiwane dane wyjściowe|Opis|
|-----|-----|-----|-----|
|Join|ciąg1, ciąg2, separator|outputClaim|Łączy ciągi wejściowe przy użyciu separatora pomiędzy. Na przykład: string1:"foo@bar.com, string2:"sandbox" , separator:"." powodujefoo@bar.com.sandboxoutputClaim:" "|
|ExtractMailPrefix (Poprawka)|mail (poczta)|outputClaim|Wyodrębnia lokalną część adresu e-mail. Na przykład: mail:"foo@bar.com" powoduje outputClaim:"foo". Jeśli \@ nie ma znaku, oryginalny ciąg wejściowy jest zwracany w stanie, w jakim jest.|

**WejściaClaims:** Użyj InputClaims element do przekazywania danych z wpisu schematu oświadczenia do transformacji. Ma dwa atrybuty: **ClaimTypeReferenceId** i **TransformationClaimType**.

- **ClaimTypeReferenceId** jest połączony z elementem identyfikatora wpisu schematu oświadczenia, aby znaleźć odpowiednie oświadczenie wejściowe. 
- **TransformationClaimType** jest używany do nadawanie unikatowej nazwy tego danych wejściowych. Ta nazwa musi być zgodna z jednym z oczekiwanych danych wejściowych dla metody transformacji.

**InputParameters:** Użyj InputParameters element przekazać stałą wartość do transformacji. Ma dwa atrybuty: **Wartość** i **identyfikator**.

- **Wartość** jest rzeczywistą stałą wartością, która ma zostać przekazana.
- **Identyfikator** jest używany do nadawanie unikatowej nazwy danych wejściowych. Nazwa musi być zgodna z jednym z oczekiwanych danych wejściowych dla metody transformacji.

**OutputClaims:** Użyj OutputClaims element do przechowywania danych generowanych przez transformację i powiązać go z wpisem schematu oświadczenia. Ma dwa atrybuty: **ClaimTypeReferenceId** i **TransformationClaimType**.

- **ClaimTypeReferenceId** jest połączony z identyfikatorem wpisu schematu oświadczenia, aby znaleźć odpowiednie oświadczenie wyjściowe.
- **TransformationClaimType** jest używany do nadawanie unikatowej nazwy danych wyjściowych. Nazwa musi być zgodna z jednym z oczekiwanych wyjść dla metody transformacji.

### <a name="exceptions-and-restrictions"></a>Wyjątki i ograniczenia

**Nazwa SAML i nazwa UPN:** Atrybuty, z których pochodzą nameid i nazwy UPN wartości i przekształcenia oświadczeń, które są dozwolone, są ograniczone. Zobacz tabelę 5 i tabelę 6, aby zobaczyć dozwolone wartości.

#### <a name="table-5-attributes-allowed-as-a-data-source-for-saml-nameid"></a>Tabela 5: Atrybuty dozwolone jako źródło danych dla samol nameid

|Element źródłowy|ID|Opis|
|-----|-----|-----|
| Użytkownik | mail (poczta)|Adres e-mail|
| Użytkownik | Userprincipalname|Nazwa główna użytkownika|
| Użytkownik | onpremisessamaccountname|Nazwa konta sm w środowisku lokalnym|
| Użytkownik | employeeid|Identyfikator pracownika|
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

#### <a name="table-6-transformation-methods-allowed-for-saml-nameid"></a>Tabela 6: Metody transformacji dozwolone dla nazwy SAML

| TransformationMethod (Metodyka transformacji) | Ograniczenia |
| ----- | ----- |
| ExtractMailPrefix (Poprawka) | Brak |
| Join | Sufiks, który jest przyłączony musi być zweryfikowaną domeną dzierżawy zasobów. |

### <a name="custom-signing-key"></a>Niestandardowy klucz podpisywania

Niestandardowy klucz podpisywania musi być przypisany do obiektu jednostkowego usługi, aby zasady mapowania oświadczeń zostały zastosowane. Zapewnia to potwierdzenie, że tokeny zostały zmodyfikowane przez twórcę zasad mapowania oświadczeń i chroni aplikacje przed zasadami mapowania oświadczeń utworzonymi przez złośliwe podmioty. Aby dodać niestandardowy klucz podpisywania, można użyć polecenia `new-azureadapplicationkeycredential` cmdlet programu Azure PowerShell, aby utworzyć poświadczenia klucza symetrycznego dla obiektu aplikacji. Aby uzyskać więcej informacji na temat tego polecenia cmdlet programu Azure PowerShell, zobacz [New-AzureADApplicationKeyCredential](https://docs.microsoft.com/powerShell/module/Azuread/New-AzureADApplicationKeyCredential?view=azureadps-2.0).

Aplikacje z włączonym mapowaniem oświadczeń muszą zweryfikować `appid={client_id}` swoje klucze podpisywania tokenów, dołączając je do [żądań metadanych OpenID Connect.](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document) Poniżej znajduje się format dokumentu metadanych OpenID Connect, którego należy użyć: 

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid={client-id}
```

### <a name="cross-tenant-scenarios"></a>Scenariusze między dzierżawcami

Zasady mapowania oświadczeń nie mają zastosowania do użytkowników-gości. Jeśli użytkownik-gość próbuje uzyskać dostęp do aplikacji z zasadami mapowania oświadczeń przypisanymi do jednostki usługi, zostanie wystawiony domyślny token (zasada nie ma wpływu).

## <a name="claims-mapping-policy-assignment"></a>Przypisanie zasad mapowania oświadczeń

Zasady mapowania oświadczeń można przypisać tylko do obiektów jednostkowych usługi.

### <a name="example-claims-mapping-policies"></a>Przykładowe zasady mapowania oświadczeń

W usłudze Azure AD wiele scenariuszy jest możliwe, gdy można dostosować oświadczenia emitowane w tokenach dla określonych jednostek usługi. W tej sekcji przechodzimy przez kilka typowych scenariuszy, które mogą pomóc zrozumieć, jak używać typu zasad mapowania oświadczeń.

#### <a name="prerequisites"></a>Wymagania wstępne

W poniższych przykładach można utworzyć, zaktualizować, połączyć i usunąć zasady dla podmiotów usługi. Jeśli jesteś nowy w usłudze Azure AD, zaleca się, [aby dowiedzieć się, jak uzyskać dzierżawy usługi Azure AD](quickstart-create-new-tenant.md) przed kontynuowaniem tych przykładów.

Aby rozpocząć, wykonaj następujące czynności:

1. Pobierz najnowszą [publiczną wersję zapoznawczą modułu programu Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).
1. Uruchom polecenie Połącz, aby zalogować się do konta administratora usługi Azure AD. Uruchom to polecenie przy każdym uruchomieniu nowej sesji.

   ``` powershell
   Connect-AzureAD -Confirm
   ```
1. Aby wyświetlić wszystkie zasady utworzone w organizacji, uruchom następujące polecenie. Zaleca się uruchomienie tego polecenia po większości operacji w następujących scenariuszach, aby sprawdzić, czy zasady są tworzone zgodnie z oczekiwaniami.

   ``` powershell
   Get-AzureADPolicy
   ```

#### <a name="example-create-and-assign-a-policy-to-omit-the-basic-claims-from-tokens-issued-to-a-service-principal"></a>Przykład: Tworzenie i przypisywanie zasad w celu pominięcia podstawowych oświadczeń z tokenów wystawionych podmiotowi usługi

W tym przykładzie utworzysz zasadę, która usuwa podstawowy zestaw oświadczeń z tokenów wystawionych połączonym jednostkom usługi.

1. Tworzenie zasad mapowania oświadczeń. Ta zasada, połączone z określonych podmiotów usługi, usuwa podstawowy zestaw oświadczeń z tokenów.
   1. Aby utworzyć zasadę, uruchom to polecenie: 
    
      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"false"}}') -DisplayName "OmitBasicClaims" -Type "ClaimsMappingPolicy"
      ```
   2. Aby wyświetlić nowe zasady i uzyskać zasadę ObjectId, uruchom następujące polecenie:
    
      ``` powershell
      Get-AzureADPolicy
      ```
1. Przypisz zasady do jednostki usługi. Należy również uzyskać ObjectId jednostki usługi.
   1. Aby wyświetlić wszystkie podmioty usługi organizacji, można [zbadać interfejs API programu Microsoft Graph](/graph/traverse-the-graph). Lub w [Eksploratorze wykresu firmy Microsoft](https://developer.microsoft.com/graph/graph-explorer)zaloguj się do konta usługi Azure AD.
   2. Jeśli masz ObjectId jednostki usługi, uruchom następujące polecenie:  
     
      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

#### <a name="example-create-and-assign-a-policy-to-include-the-employeeid-and-tenantcountry-as-claims-in-tokens-issued-to-a-service-principal"></a>Przykład: Tworzenie i przypisywanie zasad w celu uwzględnienia EmployeeID i TenantCountry jako oświadczeń w tokenach wystawionych podmiotowi usługi

W tym przykładzie należy utworzyć zasadę, która dodaje EmployeeID i TenantCountry do tokenów wystawionych połączonym jednostkom usługi. EmployeeID jest emitowany jako typ oświadczenia nazwy w tokenach SAML i JWTs. TenantCountry jest emitowany jako typ oświadczenia kraju w tokenach SAML i JWTs. W tym przykładzie nadal uwzględniamy podstawowe oświadczenia ustawione w tokenach.

1. Tworzenie zasad mapowania oświadczeń. Ta zasada, połączone z określonych podmiotów usługi, dodaje EmployeeID i TenantCountry roszczeń do tokenów.
   1. Aby utworzyć zasadę, uruchom następujące polecenie:  
     
      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name","JwtClaimType":"name"},{"Source":"company","ID":"tenantcountry","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/country","JwtClaimType":"country"}]}}') -DisplayName "ExtraClaimsExample" -Type "ClaimsMappingPolicy"
      ```
    
   2. Aby wyświetlić nowe zasady i uzyskać zasadę ObjectId, uruchom następujące polecenie:
     
      ``` powershell  
      Get-AzureADPolicy
      ```
1. Przypisz zasady do jednostki usługi. Należy również uzyskać ObjectId jednostki usługi. 
   1. Aby wyświetlić wszystkie podmioty usługi organizacji, można [zbadać interfejs API programu Microsoft Graph](/graph/traverse-the-graph). Lub w [Eksploratorze wykresu firmy Microsoft](https://developer.microsoft.com/graph/graph-explorer)zaloguj się do konta usługi Azure AD.
   2. Jeśli masz ObjectId jednostki usługi, uruchom następujące polecenie:  
     
      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

#### <a name="example-create-and-assign-a-policy-that-uses-a-claims-transformation-in-tokens-issued-to-a-service-principal"></a>Przykład: Tworzenie i przypisywanie zasad, które używają transformacji oświadczeń w tokenach wystawionych podmiotowi usługi

W tym przykładzie należy utworzyć zasadę, która emituje oświadczenie niestandardowe "JoinedData" do JWTs wydane do połączonych podmiotów usługi. To oświadczenie zawiera wartość utworzoną przez połączenie danych przechowywanych w atrybute1 atrybutu extensionattribute1 w obiekcie użytkownika z ".sandbox". W tym przykładzie wykluczamy podstawowe oświadczenia ustawione w tokenach.

1. Tworzenie zasad mapowania oświadczeń. Ta zasada, połączone z określonych podmiotów usługi, dodaje EmployeeID i TenantCountry roszczeń do tokenów.
   1. Aby utworzyć zasadę, uruchom następujące polecenie:
     
      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema":[{"Source":"user","ID":"extensionattribute1"},{"Source":"transformation","ID":"DataJoin","TransformationId":"JoinTheData","JwtClaimType":"JoinedData"}],"ClaimsTransformations":[{"ID":"JoinTheData","TransformationMethod":"Join","InputClaims":[{"ClaimTypeReferenceId":"extensionattribute1","TransformationClaimType":"string1"}], "InputParameters": [{"ID":"string2","Value":"sandbox"},{"ID":"separator","Value":"."}],"OutputClaims":[{"ClaimTypeReferenceId":"DataJoin","TransformationClaimType":"outputClaim"}]}]}}') -DisplayName "TransformClaimsExample" -Type "ClaimsMappingPolicy"
      ```
    
   2. Aby wyświetlić nowe zasady i uzyskać zasadę ObjectId, uruchom następujące polecenie: 
     
      ``` powershell
      Get-AzureADPolicy
      ```
1. Przypisz zasady do jednostki usługi. Należy również uzyskać ObjectId jednostki usługi. 
   1. Aby wyświetlić wszystkie podmioty usługi organizacji, można [zbadać interfejs API programu Microsoft Graph](/graph/traverse-the-graph). Lub w [Eksploratorze wykresu firmy Microsoft](https://developer.microsoft.com/graph/graph-explorer)zaloguj się do konta usługi Azure AD.
   2. Jeśli masz ObjectId jednostki usługi, uruchom następujące polecenie: 
     
      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="see-also"></a>Zobacz też

Aby dowiedzieć się, jak dostosować oświadczenia wystawione w tokenie SAML za pośrednictwem witryny Azure portal, zobacz [Jak: Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji korporacyjnych](active-directory-saml-claims-customization.md)
