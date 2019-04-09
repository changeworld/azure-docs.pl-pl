---
title: Automatyzowanie aprowizacji w aplikacjach przy użyciu Standard SCIM w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Usługa Azure Active Directory automatycznie aprowizować użytkowników i grup do aplikacji lub tożsamość magazynu, który fronted przez usługę sieci web, za pomocą interfejsu zdefiniowane w specyfikacji protokołu Standard SCIM
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 4/03/2019
ms.author: celested
ms.reviewer: asmalser
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: a404b5e6769c7bb91b4f7b5830cea18372ec456d
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2019
ms.locfileid: "59007160"
---
# <a name="using-system-for-cross-domain-identity-management-scim-to-automatically-provision-users-and-groups-from-azure-active-directory-to-applications"></a>Przy użyciu systemu dla Standard międzydomenowe zarządzania tożsamościami (SCIM), aby automatycznie aprowizować użytkowników i grup z usługi Azure Active Directory do aplikacji

## <a name="overview"></a>Przegląd

Standard SCIM to standardowy protokół i schemat, który ma na celu dysku większej spójności w sposobu zarządzania tożsamościami w systemach. Gdy aplikacja obsługuje punkt końcowy Standard SCIM do zarządzania użytkownikami, usługa aprowizowania użytkowników w usłudze Azure AD mogą wysyłać żądania do tworzenie, modyfikowanie lub usuwanie przypisanych użytkowników i grup do tego punktu końcowego. 

Wiele aplikacji, dla których usługa Azure AD obsługuje [wstępnie zintegrowane automatyczna aprowizacja użytkowników](../saas-apps/tutorial-list.md) zaimplementować Standard SCIM, ponieważ oznacza, że użytkownik otrzymywać powiadomienia o zmianie.  Oprócz wspomnianych, klienci mogą się łączyć aplikacje, które obsługują określonego profilu [specyfikacji protokołu 2.0 Standard SCIM](https://tools.ietf.org/html/rfc7644) przy użyciu opcji ogólnych integration "spoza galerii" w witrynie Azure portal. 

Głównym celem tego artykułu znajduje się na profil Standard SCIM 2.0, który implementuje usługi Azure AD, jako część jej ogólnego łącznika Standard SCIM dla aplikacji spoza galerii. Jednak pomyślne testowania aplikacji, która obsługuje standard SCIM z ogólnego usługi Azure AD connector jest krokiem do aplikacji w galerii usługi Azure AD jako obsługi aprowizacji użytkowników. Aby uzyskać więcej informacji na temat pobierania aplikacji w galerii aplikacji usługi Azure AD, zobacz [Microsoft Application Network](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx).
 

>[!IMPORTANT]
>Zachowanie wdrażania usługi Azure AD w Standard SCIM ostatniej aktualizacji od 18 grudnia 2018 r. Aby uzyskać informacji na temat co się zmieniło, zobacz [zgodności protokołu 2.0 Standard SCIM usługi aprowizacji użytkownika usługi Azure AD](application-provisioning-config-problem-scim-compatibility.md).

![][0]
*Rysunek 1: Inicjowanie obsługi administracyjnej z usługi Azure Active Directory do aplikacji lub tożsamość magazynu, który implementuje Standard SCIM*

W tym artykule jest podzielony na cztery sekcje:

* **[Inicjowanie obsługi administracyjnej użytkowników i grup do aplikacji innych firm, które obsługują standard SCIM 2.0](#provisioning-users-and-groups-to-applications-that-support-scim)**  — Jeśli organizacja korzysta z aplikacji innych firm, że implementuje profil 2.0 Standard SCIM przez usługę Azure AD obsługuje, można uruchomić zarówno automatyzacji aprowizację i cofanie aprowizacji użytkowników i grup już dziś.

* **[Zrozumienie implementacji usługi Azure AD w Standard SCIM](#understanding-the-azure-ad-scim-implementation)**  — Jeśli tworzysz aplikację, która obsługuje standard SCIM 2.0 interfejsem API zarządzania użytkownikami w tej sekcji opisano szczegółowo sposób implementacji klienta usługi Azure AD w Standard SCIM i jak powinny modelować Protokół usługi Standard SCIM żądania obsługi i odpowiedzi.
  
* **[Tworzenie punktu końcowego Standard SCIM przy użyciu bibliotek Microsoft CLI](#building-a-scim-endpoint-using-microsoft-cli-libraries)**  -Common Language Infrastructure (CLI) bibliotek oraz przykłady kodu przedstawiają sposób tworzenia punktu końcowego Standard SCIM i tłumaczenie wiadomości Standard SCIM.  

* **[Odwołanie do schematu użytkowników i grup](#user-and-group-schema-reference)**  — w tym artykule opisano schematów użytkowników i grup, jest obsługiwana przez implementację Standard SCIM programu Azure AD dla aplikacji spoza galerii. 

## <a name="provisioning-users-and-groups-to-applications-that-support-scim"></a>Inicjowanie obsługi administracyjnej użytkowników i grup do aplikacji obsługujących standard SCIM
Usługa Azure AD można skonfigurować, aby automatycznie aprowizować przypisanych użytkowników i grup do aplikacje, które implementują określonego profilu [protokołu 2.0 Standard SCIM](https://tools.ietf.org/html/rfc7644). Szczegółowe informacje na temat profilu są udokumentowane w artykule [zrozumienie implementacji usługi Azure AD w Standard SCIM](#understanding-the-azure-ad-scim-implementation).

Skontaktuj się z dostawcą aplikacji lub dokumentacji dostawcy aplikacji dla instrukcji zgodność z tymi wymaganiami.

>[!IMPORTANT]
>Implementacja Standard SCIM programu Azure AD jest zbudowana na użytkownika usługi Azure AD, inicjowania obsługi usługi, która jest przeznaczona do stale synchronizowania użytkowników między usługą Azure AD a aplikacją docelową i implementuje bardzo określony zbiór działań standardowych. Należy zrozumieć te zachowania, aby zrozumieć zachowanie klienta usługi Azure AD w Standard SCIM. Aby uzyskać więcej informacji, zobacz [co się dzieje podczas aprowizacji użytkowników?](user-provisioning.md#what-happens-during-provisioning).

### <a name="getting-started"></a>Wprowadzenie
Aplikacje, które obsługują profile Standard SCIM opisane w tym artykule może być połączona z usługą Azure Active Directory przy użyciu funkcji "aplikacji spoza galerii" w galerii aplikacji usługi Azure AD. Po nawiązaniu połączenia usługi Azure AD działa proces synchronizacji co 40 minut, gdzie go zapytania aplikacji Standard SCIM z punktu końcowego dla przypisanych użytkowników i grup i tworzy lub modyfikuje je zgodnie ze szczegółami przypisania.

**Łączenie aplikacji, która obsługuje standard SCIM:**

1. Zaloguj się do [portalu Azure Active Directory](https://aad.portal.azure.com). 

1. Wybierz **aplikacje dla przedsiębiorstw** z okienka po lewej stronie. Jest wyświetlana lista wszystkich skonfigurowanych aplikacji, w tym aplikacje, które zostały dodane z galerii.

1. Wybierz **+ Nowa aplikacja** > **wszystkich** > **aplikacji spoza galerii**.

1. Wprowadź nazwę aplikacji, a następnie wybierz pozycję **Dodaj** do utworzenia obiektu aplikacji. Nowa aplikacja zostanie dodany do listy aplikacji przeznaczonych dla przedsiębiorstw i otwiera do ekranu zarządzania w swojej aplikacji.
    
   ![][1]
   *Rysunek 2: Galerii aplikacji usługi Azure AD*
    
1. Na ekranie zarządzania aplikacji wybierz **aprowizacji** w panelu po lewej stronie.
1. W **inicjowania obsługi trybu** menu, wybierz opcję **automatyczne**.
    
   ![][2]
   *Rysunek 3: Konfigurowanie inicjowania obsługi w witrynie Azure portal*
    
1. W **adres URL dzierżawy** wprowadź adres URL punktu końcowego Standard SCIM aplikacji. Przykład: https://api.contoso.com/scim/v2/
1. Jeśli punkt końcowy Standard SCIM wymaga tokenu elementu nośnego OAuth od wystawcy innych niż Usługa Azure AD, następnie skopiuj wymagany token elementu nośnego OAuth do opcjonalnego **klucz tajny tokenu** pola. Jeśli to pole pozostanie puste, usługi Azure AD zawiera tokenu elementu nośnego OAuth wydawane z usługi Azure AD z każdym żądaniem. Aplikacje korzystające z usługi Azure AD jako dostawcy tożsamości można sprawdzić poprawność tej platformy Azure token wystawiony przez usługi AD.
1. Wybierz **Testuj połączenie** mają podejmować próby nawiązania połączenia z punktem końcowym Standard SCIM usługi Azure Active Directory. Jeśli próba nie powiedzie się, informacje o błędzie jest wyświetlany.  

    >[!NOTE]
    >**Testuj połączenie** odpytuje punkt końcowy Standard SCIM dla użytkownika, który nie istnieje, za pomocą losowy identyfikator GUID jako właściwość pasującego wybrane w konfiguracji usługi Azure AD. Oczekiwana reakcja poprawne jest pusty komunikat ListResponse Standard SCIM HTTP 200 OK. 

1. W przypadku próby połączenia się osiągnąć sukces aplikacji, wybierz **Zapisz** można zapisać poświadczeń administratora.
1. W **mapowania** sekcji, istnieją dwa zestawy można wybierać mapowań atrybutów: jeden dla obiektów użytkownika i jeden dla obiektów grupy. Wybierz każdą z nich Przejrzyj atrybuty, które są synchronizowane z usługi Azure Active Directory do swojej aplikacji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania użytkowników i grup w Twojej aplikacji dla operacji aktualizowania. Wybierz **Zapisz** aby zatwierdzić zmiany.

    >[!NOTE]
    >Opcjonalnie można wyłączyć synchronizację grupy obiektów, wyłączając mapowania "groups". 

1. W obszarze **ustawienia**, **zakres** pole definiuje, którzy użytkownicy i grupy są synchronizowane. Wybierz **Synchronizuj tylko przypisanych użytkowników i grup** (zalecane), aby zsynchronizować tylko użytkownicy i grupy przypisane w **użytkowników i grup** kartę.
1. Po zakończeniu konfiguracji ustaw **stanie aprowizacji** do **na**.
1. Wybierz **Zapisz** można uruchomić usługi aprowizacji usługi Azure AD. 
1. Jeśli synchronizacja tylko przypisanych użytkowników i grup (zalecane), pamiętaj o wybraniu **użytkowników i grup** kartę, a następnie Przypisz użytkowników lub grup, które mają być synchronizowane.

Po rozpoczęciu synchronizacji początkowej, możesz wybrać **dzienniki inspekcji** w panelu po lewej stronie, aby monitorować postęp, który zawiera wszystkie akcje wykonywane przez usługę aprowizacji w aplikacji. Aby uzyskać więcej informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](check-status-user-account-provisioning.md).

> [!NOTE]
> Synchronizacja początkowa trwa dłużej niż nowsze synchronizacje, które występują co około 40 minut, tak długo, jak usługa jest uruchomiona. 

## <a name="understanding-the-azure-ad-scim-implementation"></a>Zrozumienie implementacji usługi Azure AD w Standard SCIM

Jeśli tworzysz aplikację, która obsługuje standard SCIM 2.0 interfejsem API zarządzania użytkownikami w tej sekcji opisano szczegółowo sposób implementacji klienta usługi Azure AD w Standard SCIM i jak powinny modelować protokołu usługi Standard SCIM żądania obsługi i odpowiedzi. Gdy punkt końcowy usługi Standard SCIM zastało zaimplementowane, można ją przetestować, postępując zgodnie z procedurą opisaną w poprzedniej sekcji.

W ramach [specyfikacji protokołu 2.0 Standard SCIM](http://www.simplecloud.info/#Specification), aplikacja musi spełniać następujące wymagania:

* Obsługuje tworzenie użytkowników, a opcjonalnie także grupy, zgodnie z sekcji [3.3 protokołu Standard SCIM](https://tools.ietf.org/html/rfc7644#section-3.3).  
* Obsługuje modyfikowania użytkowników lub grup przy użyciu żądania PATCH zgodnie [sekcji 3.5.2 protokołu Standard SCIM](https://tools.ietf.org/html/rfc7644#section-3.5.2).  
* Obsługuje pobieranie znanych zasobów dla użytkownika lub grupy utworzone wcześniej zgodnie [sekcji 3.4.1 protokołu Standard SCIM](https://tools.ietf.org/html/rfc7644#section-3.4.1).  
* Obsługuje wykonywanie zapytań względem użytkowników lub grupy, zgodnie z sekcji [3.4.2 protokołu Standard SCIM](https://tools.ietf.org/html/rfc7644#section-3.4.2).  Domyślnie użytkownicy są pobierane przez ich `id` i sprawdzać za ich `username` i `externalid`, i grup są zgłaszane przez `displayName`.  
* Obsługa zapytań użytkownika według Identyfikatora i przez menedżera, zgodnie z sekcji 3.4.2 protokołu Standard SCIM.  
* Obsługuje wykonywanie zapytań dotyczących grup według Identyfikatora a według elementu członkowskiego, zgodnie z sekcji 3.4.2 protokołu Standard SCIM.  
* Akceptuje token elementu nośnego pojedynczego, uwierzytelniania i autoryzacji usługi Azure AD do aplikacji.

Podczas implementowania punktu końcowego Standard SCIM w celu zapewnienia zgodności z usługą Azure AD, należy przestrzegać następujących zasad ogólnych:

* `id` Właściwość jest wymagana dla wszystkich zasobów. Każdy odpowiedź, która zwraca zasobu należy upewnić się, każdy zasób ma tej właściwości, z wyjątkiem `ListResponse` członkom zero.
* Odpowiedź na żądanie filtr zapytania/powinna zawsze być `ListResponse`.
* Grupy są opcjonalne, lecz tylko obsługiwane, jeśli implementacja Standard SCIM obsługuje żądania PATCH.
* Nie obejmują całego zasobu w odpowiedzi poprawki.
* Microsoft Azure AD używa tylko następujące operatory:  
     - `eq`
     - `and`
* Nie wymagają dopasowanie uwzględniające na elementy strukturalne w Standard SCIM w konkretnej poprawki `op` wartości operacji, zgodnie z definicją w https://tools.ietf.org/html/rfc7644#section-3.5.2. Usługi Azure AD generuje wartość "op" jako `Add`, `Replace`, i `Remove`.
* Microsoft Azure AD zgłasza żądania do pobrania losowe użytkowników i grup, aby upewnić się, że punktu końcowego i poświadczenia są prawidłowe. Również odbywa się w ramach **Testuj połączenie** przepływ w [witryny Azure portal](https://portal.azure.com). 
* Atrybut, który może być odpytywany zasoby na powinna być ustawiona jako pasujący atrybut na tę aplikację w [witryny Azure portal](https://portal.azure.com). Aby uzyskać więcej informacji, zobacz [Dostosowywanie użytkownika aprowizacji mapowania atrybutów](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-customizing-attribute-mappings)

### <a name="user-provisioning-and-de-provisioning"></a>Inicjowanie obsługi użytkowników i anulowanie obsługi.
Poniższa ilustracja przedstawia komunikatów usługi Azure Active Directory i wysyła do usługi Standard SCIM do zarządzania cyklem życia użytkowników w aplikacji magazynu tożsamości.  

![][4]
*Rysunek 4: Użytkownik aprowizację i anulowanie obsługi sekwencji*

### <a name="group-provisioning-and-de-provisioning"></a>Grupa aprowizację i cofanie aprowizacji
Grupa aprowizację i cofanie aprowizacji są opcjonalne. Gdy zaimplementowana włączone, na poniższej ilustracji przedstawiono komunikaty tej usługi Azure AD wysyła do usługi Standard SCIM do zarządzania cyklem życia grupy w magazynie tożsamości aplikacji.  Te komunikaty różnią się od komunikatów o użytkownikach na dwa sposoby: 

* Żądania pobrania grupy określić atrybut elementów członkowskich mają być wykluczone z dowolnego zasobu w odpowiedzi na żądanie.  
* Żądania, aby ustalić, czy jest atrybut odwołania, ma określoną wartość są żądania o atrybucie elementów członkowskich.  

![][5]
*Rysunek 5: Grupa aprowizację i anulowanie obsługi sekwencji*

### <a name="scim-protocol-requests-and-responses"></a>Standard SCIM protokołu żądań i odpowiedzi
Ta sekcja zawiera przykład standard SCIM żądania wysyłanego przez klienta usługi Azure AD w Standard SCIM i przykład oczekiwanej odpowiedzi. Aby uzyskać najlepsze wyniki należy kodu aplikacji w celu obsłużenia tych żądań, w tym formacie i emitować oczekiwanej odpowiedzi.

>[!IMPORTANT]
>Aby dowiedzieć się, jak i kiedy usługa aprowizowania użytkowników w usłudze Azure AD emituje czynności opisane poniżej, zobacz [co się dzieje podczas aprowizacji użytkowników?](user-provisioning.md#what-happens-during-provisioning).

- [Operacje użytkownika](#user-operations)
  - [Utwórz użytkownika](#create-user)
    - [Żądanie](#request)
    - [Odpowiedź](#response)
  - [Pobieranie użytkownika](#get-user)
    - [Żądanie](#request-1)
    - [Odpowiedź](#response-1)
  - [Pobieranie użytkownika przez zapytanie](#get-user-by-query)
    - [Żądanie](#request-2)
    - [Odpowiedź](#response-2)
  - [Pobieranie użytkownika przez zapytanie - wyników](#get-user-by-query---zero-results)
    - [Żądanie](#request-3)
    - [Odpowiedź](#response-3)
  - [Aktualizuj użytkownika [wielowartościowe właściwości]](#update-user-multi-valued-properties)
    - [Żądanie](#request-4)
    - [Odpowiedź](#response-4)
  - [Aktualizuj użytkownika [właściwości pojedynczej wartości]](#update-user-single-valued-properties)
    - [Żądanie](#request-5)
    - [Odpowiedź](#response-5)
  - [Usuń użytkownika](#delete-user)
    - [Żądanie](#request-6)
    - [Odpowiedź](#response-6)
- [Operacje dotyczące grupy](#group-operations)
  - [Tworzenie grupy](#create-group)
    - [Żądanie](#request-7)
    - [Odpowiedź](#response-7)
  - [Pobierz grupę](#get-group)
    - [Żądanie](#request-8)
    - [Odpowiedź](#response-8)
  - [Pobierz grupę przez Nazwa wyświetlana](#get-group-by-displayname)
    - [Żądanie](#request-9)
    - [Odpowiedź](#response-9)
  - [Grupy aktualizacji [Państwa atrybuty]](#update-group-non-member-attributes)
    - [Żądanie](#request-10)
    - [Odpowiedź](#response-10)
  - [Grupy aktualizacji [Dodaj elementy członkowskie]](#update-group-add-members)
    - [Żądanie](#request-11)
    - [Odpowiedź](#response-11)
  - [Aktualizowanie grupy [Usuń]](#update-group-remove-members)
    - [Żądanie](#request-12)
    - [Odpowiedź](#response-12)
  - [Usuń grupę](#delete-group)
    - [Żądanie](#request-13)
    - [Odpowiedź](#response-13)

### <a name="user-operations"></a>Operacje użytkownika

* Użytkownicy mogą być przeszukiwane przy `userName` lub `email[type eq "work"]` atrybutów.  

#### <a name="create-user"></a>Utwórz użytkownika

###### <a name="request"></a>Żądanie
*POST/użytkownicy*
```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "active": true,
    "emails": [{
        "primary": true,
        "type": "work",
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com"
    }],
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName"
    },
    "roles": []
}
```

##### <a name="response"></a>Odpowiedź
*Utworzone 201 protokołu HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "48af03ac28ad4fb88478",
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```


#### <a name="get-user"></a>Pobieranie użytkownika

###### <a name="request"></a>Żądanie
*Pobierz /Users/5d48a0a8e9f04aa38008* 

###### <a name="response"></a>Odpowiedź
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5d48a0a8e9f04aa38008",
    "externalId": "58342554-38d6-4ec8-948c-50044d0a33fd",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_feed3ace-693c-4e5a-82e2-694be1b39934",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_22370c1a-9012-42b2-bf64-86099c2a1c22@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```
#### <a name="get-user-by-query"></a>Pobieranie użytkownika przez zapytanie

##### <a name="request"></a>Żądanie
*GET/użytkownicy? filtr = userName eq "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081"*

##### <a name="response"></a>Odpowiedź
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
        "id": "2441309d85324e7793ae",
        "externalId": "7fce0092-d52e-4f76-b727-3955bd72c939",
        "meta": {
            "resourceType": "User",
            "created": "2018-03-27T19:59:26.000Z",
            "lastModified": "2018-03-27T19:59:26.000Z"
            
        },
        "userName": "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081",
        "name": {
            "familyName": "familyName",
            "givenName": "givenName"
        },
        "active": true,
        "emails": [{
            "value": "Test_User_91b67701-697b-46de-b864-bd0bbe4f99c1@testuser.com",
            "type": "work",
            "primary": true
        }]
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="get-user-by-query---zero-results"></a>Pobieranie użytkownika przez zapytanie - wyników

##### <a name="request"></a>Żądanie
*GET/użytkownicy? filtr = userName eq "nie istnieje użytkownik"*

##### <a name="response"></a>Odpowiedź
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 0,
    "Resources": [],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="update-user-multi-valued-properties"></a>Aktualizuj użytkownika [wielowartościowe właściwości]

##### <a name="request"></a>Żądanie
*PATCH /Users/6764549bef60420686bc HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [
            {
            "op": "Replace",
            "path": "emails[type eq \"work\"].value",
            "value": "updatedEmail@microsoft.com"
            },
            {
            "op": "Replace",
            "path": "name.familyName",
            "value": "updatedFamilyName"
            }
    ]
}
```

##### <a name="response"></a>Odpowiedź
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "6764549bef60420686bc",
    "externalId": "6c75de36-30fa-4d2d-a196-6bdcdb6b6539",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_fbb9dda4-fcde-4f98-a68b-6c5599e17c27",
    "name": {
        "formatted": "givenName updatedFamilyName",
        "familyName": "updatedFamilyName",
        "givenName": "givenName"
    },
    "active": true,
    "emails": [{
        "value": "updatedEmail@microsoft.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="update-user-single-valued-properties"></a>Aktualizuj użytkownika [właściwości pojedynczej wartości]

##### <a name="request"></a>Żądanie
*PATCH /Users/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "userName",
        "value": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com"
    }]
}
```

##### <a name="response"></a>Odpowiedź
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5171a35d82074e068ce2",
    "externalId": "aa1eca08-7179-4eeb-a0be-a519f7e5cd1a",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "userName": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_49dc1090-aada-4657-8434-4995c25a00f7@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="delete-user"></a>Usuń użytkownika

##### <a name="request"></a>Żądanie
*DELETE /Users/5171a35d82074e068ce2 HTTP/1.1*

##### <a name="response"></a>Odpowiedź
*HTTP/1.1 204 No Content*

### <a name="group-operations"></a>Operacje dotyczące grupy

* Zawsze można tworzyć grup przy użyciu listy puste elementy członkowskie.
* Grupy mogą być przeszukiwane przy `displayName` atrybutu.
* Aktualizacja żądanie PATCH grupy powinny uzyskanie *HTTP 204 Brak zawartości* w odpowiedzi. Zwracanie treści z listą wszystkich elementów członkowskich nie jest zalecane.
* Nie obsługuje zwracania wszystkich elementów członkowskich grupy.

#### <a name="create-group"></a>Tworzenie grupy

##### <a name="request"></a>Żądanie
*/ Groups POST protokołu HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group", "http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/2.0/Group"],
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "id": "c4d56c3c-bf3b-4e96-9b64-837018d6060e",
    "displayName": "displayName",
    "members": [],
    "meta": {
        "resourceType": "Group"
    }
}
```

##### <a name="response"></a>Odpowiedź
*Utworzone 201 protokołu HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "927fa2c08dcb4a7fae9e",
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "displayName": "displayName",
    "members": []
}
```

#### <a name="get-group"></a>Pobierz grupę

##### <a name="request"></a>Żądanie
*GET/grupy/40734ae655284ad3abcc? excludedAttributes = składowe protokołu HTTP/1.1*

##### <a name="response"></a>Odpowiedź
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "40734ae655284ad3abcc",
    "externalId": "60f1bb27-2e1e-402d-bcc4-ec999564a194",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "displayName": "displayName",
}
```

#### <a name="get-group-by-displayname"></a>Pobierz grupę przez Nazwa wyświetlana

##### <a name="request"></a>Żądanie
*GET/groups? excludedAttributes = elementów członkowskich & filtr = displayName eq "displayName" HTTP/1.1*

##### <a name="response"></a>Odpowiedź
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
        "id": "8c601452cc934a9ebef9",
        "externalId": "0db508eb-91e2-46e4-809c-30dcbda0c685",
        "meta": {
            "resourceType": "Group",
            "created": "2018-03-27T22:02:32.000Z",
            "lastModified": "2018-03-27T22:02:32.000Z",
            
        },
        "displayName": "displayName",
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}
```
#### <a name="update-group-non-member-attributes"></a>Grupy aktualizacji [Państwa atrybuty]

##### <a name="request"></a>Żądanie
*PATCH /Groups/fa2ce26709934589afc5 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "displayName",
        "value": "1879db59-3bdf-4490-ad68-ab880a269474updatedDisplayName"
    }]
}
```

##### <a name="response"></a>Odpowiedź
*HTTP/1.1 204 No Content*

### <a name="update-group-add-members"></a>Grupy aktualizacji [Dodaj elementy członkowskie]

##### <a name="request"></a>Żądanie
*PATCH /Groups/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Add",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response"></a>Odpowiedź
*HTTP/1.1 204 No Content*

#### <a name="update-group-remove-members"></a>Aktualizowanie grupy [Usuń]

##### <a name="request"></a>Żądanie
*PATCH /Groups/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Remove",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response"></a>Odpowiedź
*HTTP/1.1 204 No Content*

#### <a name="delete-group"></a>Usuń grupę

##### <a name="request"></a>Żądanie
*DELETE /Groups/cdb1ce18f65944079d37 HTTP/1.1*

##### <a name="response"></a>Odpowiedź
*HTTP/1.1 204 No Content*


## <a name="building-a-scim-endpoint-using-microsoft-cli-libraries"></a>Tworzenie punktu końcowego Standard SCIM przy użyciu bibliotek Microsoft CLI
Standard SCIM usługi sieci web, które interfejsy są tworzone za pomocą usługi Azure Active Directory, można włączyć automatycznej aprowizacji użytkowników dla niemal każdego sklepu aplikacji lub tożsamości.

Poniżej przedstawiono, jak to działa:

1. Usługa Azure AD zapewnia wspólnej biblioteki language infrastructure (CLI), o nazwie Microsoft.SystemForCrossDomainIdentityManagement, dołączone do kodu, który próbek opisano poniżej. System integratorzy i programiści można użyć tej biblioteki do tworzenia i wdrażania punkt końcowy usługi sieci web opartych na użyciu SCIM mającym połączenie z dowolnej aplikacji magazynu tożsamości usługi Azure AD.
2. Mapowania są implementowane w usłudze sieci web, aby zamapować schemat standardowy użytkownika na schemat użytkownika oraz Protokół wymagany przez aplikację. 
3. Adres URL punktu końcowego jest zarejestrowany w usłudze Azure AD jako część niestandardową aplikację w galerii aplikacji.
4. Użytkownicy i grupy są przypisane do tej aplikacji w usłudze Azure AD. Podczas przypisywania jest umieścić w kolejce do synchronizacji z aplikacji docelowej. Proces synchronizacji, obsługa kolejki jest uruchamiane co 40 minut.

### <a name="code-samples"></a>Przykłady kodu
Aby ułatwić ten proces, [przykłady kodu](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) są dostarczane, której tworzenie Standard SCIM punktu końcowego usługi w sieci web i pokazują automatycznej aprowizacji. Próbka jest dostawcę, który przechowuje plik wierszy z wartościami rozdzielonymi przecinkami reprezentująca użytkowników i grup.    

**Wymagania wstępne**

* Visual Studio 2013 lub nowszy
* [Zestaw Azure SDK dla platformy .NET](https://azure.microsoft.com/downloads/)
* Windows maszyny, która obsługuje struktury programu ASP.NET 4.5 ma być używany jako punkt końcowy Standard SCIM. Ta maszyna musi być dostępny z poziomu chmury.
* [Subskrypcja platformy Azure w wersji próbnej lub licencjonowanej wersji programu Azure AD Premium](https://azure.microsoft.com/services/active-directory/)

### <a name="getting-started"></a>Wprowadzenie
Najłatwiejszym sposobem realizowania Standard SCIM punktu końcowego, który może akceptować żądania alokacji z usługi Azure AD jest, aby skompilować i wdrożyć przykładowy kod, który wyprowadza aprowizowanych użytkowników do pliku wartości rozdzielanych przecinkami (CSV).

#### <a name="to-create-a-sample-scim-endpoint"></a>Aby utworzyć punkt końcowy Standard SCIM próbki

1. Pobierz pakiet przykładowy kod w [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)
1. Rozpakuj pakiet i umieść go na komputerze Windows w lokalizacji takiej jak C:\AzureAD-BYOA-Provisioning-Samples\.
1. W tym folderze Uruchom projekt FileProvisioning\Host\FileProvisioningService.csproj w programie Visual Studio.
1. Wybierz **narzędzia** > **Menedżera pakietów NuGet** > **Konsola Menedżera pakietów**i wykonaj następujące polecenia, aby uzyskać Projekt FileProvisioningService do rozpoznawania odwołań rozwiązania:

   ```
    Update-Package -Reinstall
   ```

1. Skompiluj projekt FileProvisioningService.
1. Uruchamianie aplikacji wiersza polecenia w Windows (jako Administrator) i używanie **cd** polecenia Zmień katalog na Twoje **\AzureAD-BYOA-Provisioning-Samples\FileProvisioning\Host\bin\Debug**folderu.
1. Uruchom następujące polecenie, zastępując `<ip-address>` przy użyciu adresu IP adresu lub nazwy domeny komputera Windows:

   ```
    FileSvc.exe http://<ip-address>:9000 TargetFile.csv
   ```

1. W Windows w obszarze **ustawienia Windows** > **sieci & ustawień internetowych**, wybierz opcję **zapory Windows**  >   **Zaawansowane ustawienia**i Utwórz **reguły dla ruchu przychodzącego** umożliwiającą dostęp przychodzący do portu 9000.
1. Jeśli komputer Windows jest za routerem, router musi być skonfigurowane do uruchamiania tłumaczenia dostępu do sieci między jego port 9000, który jest uwidaczniany w Internecie i port 9000 na komputerze Windows. Ta konfiguracja jest wymagana dla usługi Azure AD na dostęp do tego punktu końcowego w chmurze.

#### <a name="to-register-the-sample-scim-endpoint-in-azure-ad"></a>Aby zarejestrować punkt końcowy Standard SCIM przykładowych w usłudze Azure AD

1. Zaloguj się do [portalu Azure Active Directory](https://aad.portal.azure.com). 

1. Wybierz **aplikacje dla przedsiębiorstw** z okienka po lewej stronie. Jest wyświetlana lista wszystkich skonfigurowanych aplikacji, w tym aplikacje, które zostały dodane z galerii.

1. Wybierz **+ Nowa aplikacja** > **wszystkich** > **aplikacji spoza galerii**.

1. Wprowadź nazwę aplikacji, a następnie wybierz pozycję **Dodaj** do utworzenia obiektu aplikacji. Utworzony obiekt aplikacji jest przeznaczony do reprezentowania aplikacji docelowej, można będzie aprowizacji i implementowanie logowania jednokrotnego dla i nie tylko punkt końcowy Standard SCIM.

1. Na ekranie zarządzania aplikacji wybierz **aprowizacji** w panelu po lewej stronie.

1. W **inicjowania obsługi trybu** menu, wybierz opcję **automatyczne**.
    
   ![][2]
   *Rysunek 6: Konfigurowanie inicjowania obsługi w witrynie Azure portal*
    
1. W **adres URL dzierżawy** wprowadź adres URL i port punktu końcowego usługi Standard SCIM udostępnianych przez internet. Wpis jest podobny do http://testmachine.contoso.com:9000 lub http://\<adres ip >: 9000 /, gdzie \<adres ip > jest internet udostępniane IP adresów. 

1. Jeśli punkt końcowy Standard SCIM wymaga tokenu elementu nośnego OAuth od wystawcy innych niż Usługa Azure AD, następnie skopiuj wymagany token elementu nośnego OAuth do opcjonalnego **klucz tajny tokenu** pola. Jeśli to pole pozostanie puste, usługi Azure AD będą zawierać tokenu elementu nośnego OAuth wydawane z usługi Azure AD z każdym żądaniem. Aplikacje, które korzystają z usługi Azure AD jako dostawcy tożsamości można sprawdzić poprawność tej usługi Azure AD — wystawiony token.

1. Wybierz **Testuj połączenie** mają podejmować próby nawiązania połączenia z punktem końcowym Standard SCIM usługi Azure Active Directory. Jeśli próba nie powiedzie się, informacje o błędzie jest wyświetlany.  

    >[!NOTE]
    >**Testuj połączenie** odpytuje punkt końcowy Standard SCIM dla użytkownika, który nie istnieje, za pomocą losowy identyfikator GUID jako właściwość pasującego wybrane w konfiguracji usługi Azure AD. Oczekiwana reakcja poprawne jest HTTP 200 OK przy użyciu pustego komunikatu ListResponse Standard SCIM

1. W przypadku próby połączenia się osiągnąć sukces aplikacji, wybierz **Zapisz** można zapisać poświadczeń administratora.

1. W **mapowania** sekcji, istnieją dwa zestawy można wybierać mapowań atrybutów: jeden dla obiektów użytkownika i jeden dla obiektów grupy. Wybierz każdą z nich Przejrzyj atrybuty, które są synchronizowane z usługi Azure Active Directory do swojej aplikacji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania użytkowników i grup w Twojej aplikacji dla operacji aktualizowania. Wybierz **Zapisz** aby zatwierdzić zmiany.

1. W obszarze **ustawienia**, **zakres** pole definiuje, które użytkownicy i grupy są synchronizowane. Wybierz **"Synchronizuj tylko przypisanych użytkowników i grup** (zalecane), aby zsynchronizować tylko użytkownicy i grupy przypisane w **użytkowników i grup** kartę.

1. Po zakończeniu konfiguracji ustaw **stanie aprowizacji** do **na**.

1. Wybierz **Zapisz** można uruchomić usługi aprowizacji usługi Azure AD. 

1. Jeśli synchronizacja tylko przypisanych użytkowników i grup (zalecane), pamiętaj o wybraniu **użytkowników i grup** kartę, a następnie Przypisz użytkowników lub grup, które mają być synchronizowane.

Po rozpoczęciu synchronizacji początkowej, możesz wybrać **dzienniki inspekcji** w panelu po lewej stronie, aby monitorować postęp, który zawiera wszystkie akcje wykonywane przez usługę aprowizacji w aplikacji. Aby uzyskać więcej informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](check-status-user-account-provisioning.md).

W ostatnim kroku weryfikacji próbki jest można otworzyć pliku TargetFile.csv w folderze \AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug na komputerze Windows. Po jego uruchomieniu procesu aprowizacji ten plik zawiera szczegółowe informacje o wszystkich przypisane i aprowizacji użytkowników i grup.

### <a name="development-libraries"></a>Biblioteki programistyczne
Aby opracować własne usługi sieci web, który jest zgodny ze specyfikacją Standard SCIM, najpierw zapoznać się z następującymi bibliotekami, które są obsługiwane przez firmę Microsoft, aby przyspieszyć proces programowania: 

- Wspólne biblioteki Language Infrastructure (CLI) są oferowane do użytku z językami oparte na tej infrastruktury, takich jak C#. Jedną z tych bibliotek Microsoft.SystemForCrossDomainIdentityManagement.Service, deklaruje interfejsu Microsoft.SystemForCrossDomainIdentityManagement.IProvider, pokazane na poniższej ilustracji. Developer, przy użyciu biblioteki będzie implementują ten interfejs, za pomocą klasy może być określone, objęty dostawcy. Biblioteki umożliwiają deweloperowi wdrażanie usługi sieci web, który jest zgodny ze specyfikacją Standard SCIM. Usługi sieci web mogą być albo hostowane w Internet Information Services lub dowolnego pliku wykonywalnego złożenia interfejsu wiersza polecenia. Żądanie jest tłumaczony na wywołania metody dostawcy, które będzie zaprogramowane przez projektanta do pracy na niektórych magazynu tożsamości.
  
   ![][3]
  
- [Express route obsługi](https://expressjs.com/guide/routing.html) zostały wprowadzone dostępne do przetwarzania node.js żądania obiektów reprezentująca wywołania (zgodnie z definicją w specyfikacji Standard SCIM), usługą sieci web środowiska node.js.   

### <a name="building-a-custom-scim-endpoint"></a>Tworzenie punktu końcowego niestandardowe Standard SCIM
Deweloperzy korzystający z biblioteki interfejsu wiersza polecenia mogą hostować usługi w ramach dowolnego zestawu pliku wykonywalnego interfejsu wiersza polecenia lub w Internet Information Services. Poniżej przedstawiono przykładowy kod dla innej usługi w ramach zestawu pliku wykonywalnego, pod adresem hostingu http://localhost:9000: 

    private static void Main(string[] arguments)
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IProvider and 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
      new DevelopersMonitor();
    Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
      new DevelopersProvider(arguments[1]);
    Microsoft.SystemForCrossDomainIdentityManagement.Service webService = null;
    try
    {
        webService = new WebService(monitor, provider);
        webService.Start("http://localhost:9000");

        Console.ReadKey(true);
    }
    finally
    {
        if (webService != null)
        {
            webService.Dispose();
            webService = null;
        }
    }
    }

    public class WebService : Microsoft.SystemForCrossDomainIdentityManagement.Service
    {
    private Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor;
    private Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider;

    public WebService(
      Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitoringBehavior, 
      Microsoft.SystemForCrossDomainIdentityManagement.IProvider providerBehavior)
    {
        this.monitor = monitoringBehavior;
        this.provider = providerBehavior;
    }

    public override IMonitor MonitoringBehavior
    {
        get
        {
            return this.monitor;
        }

        set
        {
            this.monitor = value;
        }
    }

    public override IProvider ProviderBehavior
    {
        get
        {
            return this.provider;
        }

        set
        {
            this.provider = value;
        }
    }
    }

Ta usługa musi mieć HTTP adres i serwerem uwierzytelniania certyfikat których główny urząd certyfikacji jest jednym z następujących nazw: 

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* VeriSign
* WoSign

Certyfikat uwierzytelniania serwera można powiązać z portem na hoście Windows za pomocą narzędzia powłoki sieciowej: 

    netsh http add sslcert ipport=0.0.0.0:443 certhash=0000000000003ed9cd0c315bbb6dc1c08da5e6 appid={00112233-4455-6677-8899-AABBCCDDEEFF}  

W tym miejscu wartość podana dla argumentu certhash jest odcisk palca certyfikatu, gdy wartość podana dla appid argument jest umownym identyfikatorem globalnie unikatowa.  

Do obsługi usługi w ramach Internetowe usługi informacyjne, deweloper będzie kompilacji zestaw biblioteki kodu interfejsu wiersza polecenia za pomocą klasy o nazwie uruchamiania w domyślnej przestrzeni nazw zestawu.  Poniżej przedstawiono przykład takiego klasy: 

    public class Startup
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor and  
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter starter;

    public Startup()
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
          new DevelopersMonitor();
        Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
          new DevelopersProvider();
        this.starter = 
          new Microsoft.SystemForCrossDomainIdentityManagement.WebApplicationStarter(
            provider, 
            monitor);
    }

    public void Configuration(
      Owin.IAppBuilder builder) // Defined in Owin.dll.  
    {
        this.starter.ConfigureApplication(builder);
    }
    }

### <a name="handling-endpoint-authentication"></a>Obsługa punktu końcowego uwierzytelniania
Żądania z usługi Azure Active Directory zawierać tokenu elementu nośnego OAuth 2.0.   Każda usługa odbiera żądanie, powinna uwierzytelniać wystawcy jako usługi Azure Active Directory dla z oczekiwaną dzierżawą usługi Azure Active Directory w celu uzyskania dostępu do usługi sieci web usługi Azure Active Directory Graph.  W tokenie wystawca jest identyfikowany przez oświadczenie iss, takich jak "iss": "https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/".  W tym przykładzie adres bazowy wartość oświadczenia https://sts.windows.net, identyfikuje usługi Azure Active Directory jako wystawca, podczas gdy względny adres segmentu, cbb1a5ac-f33b-45fa-9bf5-f37db0fed422, to unikatowy identyfikator dzierżawy usługi Azure Active Directory której token został wystawiony.  Jeśli token został wystawiony na potrzeby uzyskiwania dostępu do usługi sieci web usługi Azure Active Directory Graph, identyfikator tej usługi, 00000002-0000-0000-c000-000000000000, powinien być w wartości oświadczenia aud tokenu.  Każdej z aplikacji, które są zarejestrowane w jednej dzierżawie może pojawić się taki sam `iss` oświadczenia za pomocą żądań Standard SCIM.

Deweloperzy korzystający z biblioteki interfejsu wiersza polecenia obsługiwane przez firmę Microsoft do tworzenia usługi Standard SCIM może uwierzytelnić żądania z usługi Azure Active Directory przy użyciu pakietu Microsoft.Owin.Security.ActiveDirectory, wykonując następujące czynności: 

1. W dostawcy implementują właściwość Microsoft.SystemForCrossDomainIdentityManagement.IProvider.StartupBehavior przez on zwrócić metoda wywoływana zawsze wtedy, gdy usługa jest uruchomiona: 

   ```
     public override Action\<Owin.IAppBuilder, System.Web.Http.HttpConfiguration.HttpConfiguration\> StartupBehavior
     {
       get
       {
         return this.OnServiceStartup;
       }
     }

     private void OnServiceStartup(
       Owin.IAppBuilder applicationBuilder,  // Defined in Owin.dll.  
       System.Web.Http.HttpConfiguration configuration)  // Defined in System.Web.Http.dll.  
     {
     }
   ```

2. Dodaj następujący kod do tej metody, aby każde żądanie do dowolnych punktów końcowych usługi uwierzytelniony jako posiadających token wystawiony przez usługę Azure Active Directory dla określonego dzierżawcę, aby uzyskać dostęp do usługi sieci web Azure AD Graph: 

   ```
     private void OnServiceStartup(
       Owin.IAppBuilder applicationBuilder IAppBuilder applicationBuilder, 
       System.Web.Http.HttpConfiguration HttpConfiguration configuration)
     {
       // IFilter is defined in System.Web.Http.dll.  
       System.Web.Http.Filters.IFilter authorizationFilter = 
         new System.Web.Http.AuthorizeAttribute(); // Defined in System.Web.Http.dll.configuration.Filters.Add(authorizationFilter);

       // SystemIdentityModel.Tokens.TokenValidationParameters is defined in    
       // System.IdentityModel.Token.Jwt.dll.
       SystemIdentityModel.Tokens.TokenValidationParameters tokenValidationParameters =     
         new TokenValidationParameters()
         {
           ValidAudience = "00000002-0000-0000-c000-000000000000"
         };

       // WindowsAzureActiveDirectoryBearerAuthenticationOptions is defined in 
       // Microsoft.Owin.Security.ActiveDirectory.dll
       Microsoft.Owin.Security.ActiveDirectory.
       WindowsAzureActiveDirectoryBearerAuthenticationOptions authenticationOptions =
         new WindowsAzureActiveDirectoryBearerAuthenticationOptions()    {
         TokenValidationParameters = tokenValidationParameters,
         Tenant = "03F9FCBC-EA7B-46C2-8466-F81917F3C15E" // Substitute the appropriate tenant’s 
                                                       // identifier for this one.  
       };

       applicationBuilder.UseWindowsAzureActiveDirectoryBearerAuthentication(authenticationOptions);
     }
   ```

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>Obsługa aprowizacji i cofania aprowizacji użytkowników

1. Usługa Azure Active Directory kwerendę usługi dla użytkownika z wartością atrybutu externalId dopasowania wartość atrybutu mailNickname użytkownika w usłudze Azure AD. Kwerenda jest wyrażona jako żądania protokołu HTTP (Hypertext Transfer), takie jak następująco, w którym jyoung znajduje się przykład mailNickname użytkownika w usłudze Azure Active Directory.

    >[!NOTE]
    > Jest to tylko przykład. Nie wszyscy użytkownicy będą mieć atrybut mailNickname, a wartość, którą użytkownik ma nie mogą być unikatowe w katalogu. Atrybut używany do dopasowania (czyli w tym przypadku externalId) jest również, można skonfigurować w [mapowania atrybutów usługi Azure AD](customize-application-attributes.md).

   ````
    GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
    Authorization: Bearer ...
   ````
   Jeśli usługa została skompilowana przy użyciu biblioteki interfejsu wiersza polecenia obsługiwane przez firmę Microsoft do implementowania usługi Standard SCIM, żądanie jest tłumaczony na wywołanie do metody zapytania dostawcy usług.  Podpis tej metody jest następujący: 
   ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]> Query(
      Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
      string correlationIdentifier);
   ````
   Poniżej przedstawiono definicję interfejsu Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters: 
   ````
    public interface IQueryParameters: 
      Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
        System.Collections.Generic.IReadOnlyCollection <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
        { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
      system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
      { get; }
      System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
      { get; }
      string SchemaIdentifier 
      { get; }
    }

   ```
     GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
     Authorization: Bearer ...
   ```

   If the service was built using the Common Language Infrastructure libraries provided by Microsoft for implementing SCIM services, then the request is translated into a call to the Query method of the service’s provider.  Here is the signature of that method: 

   ```
     // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
     // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
     // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  
 
     System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]>  Query(
       Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
       string correlationIdentifier);
   ```

   Here is the definition of the Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters interface: 

   ```
     public interface IQueryParameters: 
       Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
     {
         System.Collections.Generic.IReadOnlyCollection  <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
         { get; }
     }

     public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
     {
       system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
       { get; }
       System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
       { get; }
       string SchemaIdentifier 
       { get; }
     }

     public interface Microsoft.SystemForCrossDomainIdentityManagement.IFilter
     {
         Microsoft.SystemForCrossDomainIdentityManagement.IFilter AdditionalFilter 
           { get; set; }
         string AttributePath 
           { get; } 
         Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator FilterOperator 
           { get; }
         string ComparisonValue 
           { get; }
     }

     public enum Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator
     {
         Equals
     }
   ```

   In the following sample of a query for a user with a given value for the externalId attribute, values of the arguments passed to the Query method are: 
   * parameters.AlternateFilters.Count: 1
   * parameters.AlternateFilters.ElementAt(0).AttributePath: "externalId"
   * parameters.AlternateFilters.ElementAt(0).ComparisonOperator: ComparisonOperator.Equals
   * parameters.AlternateFilter.ElementAt(0).ComparisonValue: "jyoung"
   * correlationIdentifier: System.Net.Http.HttpRequestMessage.GetOwinEnvironment["owin.RequestId"] 

2. If the response to a query to the web service for a user with an externalId attribute value that matches the mailNickname attribute value of a user doesn't return any users, then Azure Active Directory requests that the service provision a user corresponding to the one in Azure Active Directory.  Here is an example of such a request: 

   ````
    AUTORYZACJA https://.../scim/Users HTTP/1.1: Elementu nośnego...  Typu zawartości: aplikacja/Standard scim + json {"schematy": ["urn: ietf:params:scim:schemas:core:2.0:User", "urn: ietf:params:scim:schemas:extension:enterprise:2.0User"], "externalId": "jyoung", "userName": "jyoung", "aktywny": true, "adresy": ma wartość null,    "displayName": "Przynosi radość do małych", "wiadomości e-mail": [{"type": "działa", "value": "jyoung@Contoso.com", "primary": true}], "metadane": {"resourceType": "User"}, "name": {"familyName": "Małe", "imię": "Przynosi radość do"}, "phoneNumbers": preferredLa"ma wartość null, nguage": wartość null"title": ma wartość null,"dział": wartość null,"manager": wartość null}
   ````
   The CLI libraries provided by Microsoft for implementing SCIM services would translate that request into a call to the Create method of the service’s provider.  The Create method has this signature: 
   ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    Microsoft.SystemForCrossDomainIdentityManagement.Resource jest zdefiniowany w / / Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  

    System.Threading.Tasks.Task < Microsoft.SystemForCrossDomainIdentityManagement.Resource > Utwórz (Microsoft.SystemForCrossDomainIdentityManagement.Resource zasobów, ciąg correlationIdentifier);
   ````
   In a request to provision a user, the value of the resource argument is an instance of the Microsoft.SystemForCrossDomainIdentityManagement. Core2EnterpriseUser class, defined in the Microsoft.SystemForCrossDomainIdentityManagement.Schemas library.  If the request to provision the user succeeds, then the implementation of the method is expected to return an instance of the Microsoft.SystemForCrossDomainIdentityManagement. Core2EnterpriseUser class, with the value of the Identifier property set to the unique identifier of the newly provisioned user.  

3. To update a user known to exist in an identity store fronted by an SCIM, Azure Active Directory proceeds by requesting the current state of that user from the service with a request such as: 
   ````
    Pobierz ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1 autoryzacji: Elementu nośnego...
   ````
   In a service built using the CLI libraries provided by Microsoft for implementing SCIM services, the request is translated into a call to the Retrieve method of the service’s provider.  Here is the signature of the Retrieve method: 
   ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    Microsoft.SystemForCrossDomainIdentityManagement.Resource i / / Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters / / są zdefiniowane w Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    System.Threading.Tasks.Task < Microsoft.SystemForCrossDomainIdentityManagement.Resource > Pobierz (parametry Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters, ciąg correlationIdentifier);

    Interfejs publiczny Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters:   
        IRetrievalParameters {Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier ResourceIdentifier {get;}} interfejsu publicznego Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier {ciągiem identyfikatora {get; zestawu;} ciąg Microsoft.SystemForCrossDomainIdentityManagement.SchemaIdentifier {get; zestawu;}}
   ````
   In the example of a request to retrieve the current state of a user, the values of the properties of the object provided as the value of the parameters argument are as follows: 
  
   * Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

4. If a reference attribute is to be updated, then Azure Active Directory queries the service to determine whether the current value of the reference attribute in the identity store fronted by the service already matches the value of that attribute in Azure Active Directory. For users, the only attribute of which the current value is queried in this way is the manager attribute. Here is an example of a request to determine whether the manager attribute of a particular user object currently has a certain value: 

   If the service was built using the CLI libraries provided by Microsoft for implementing SCIM services, then the request is translated into a call to the Query method of the service’s provider. The value of the properties of the object provided as the value of the parameters argument are as follows: 
  
   * parameters.AlternateFilters.Count: 2
   * parameters.AlternateFilters.ElementAt(x).AttributePath: "ID"
   * parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
   * parameters.AlternateFilter.ElementAt(x).ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * parameters.AlternateFilters.ElementAt(y).AttributePath: "manager"
   * parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
   * parameters.AlternateFilter.ElementAt(y).ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
   * parameters.RequestedAttributePaths.ElementAt(0): "ID"
   * parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

   Here, the value of the index x can be 0 and the value of the index y can be 1, or the value of x can be 1 and the value of y can be 0, depending on the order of the expressions of the filter query parameter.   

5. Here is an example of a request from Azure Active Directory to an SCIM service to update a user: 
   ````
    Autoryzacja HTTP/1.1 ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 poprawki: Elementu nośnego...  Typu zawartości: aplikacja/Standard scim + json {"schematy": ["urn: ietf:params:scim:api:messages:2.0:PatchOp"], "Operacje": [{"op": "Dodaj", "path": "manager", "value": [{"$ref": "http://.../scim/Users/2819c223-7f76-453a-919d-413861904646", "value": "2819c223-7f76-453a-919d-413861904646"}]}]}
   ````
   The Microsoft CLI libraries for implementing SCIM services would translate the request into a call to the Update method of the service’s provider. Here is the signature of the Update method: 
   ````
    // System.Threading.Tasks.Tasks and // System.Collections.Generic.IReadOnlyCollection<T> // are defined in mscorlib.dll.  
    Microsoft.SystemForCrossDomainIdentityManagement.IPatch, / / Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, / / Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, / / Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, / / Microsoft.SystemForCrossDomainIdentityManagement.OperationName, / / Microsoft.SystemForCrossDomainIdentityManagement.IPath i / / Microsoft.SystemForCrossDomainIdentityManagement.OperationValue / są wszystkie zdefiniowane w Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 

    System.Threading.Tasks.Task aktualizacji (poprawka Microsoft.SystemForCrossDomainIdentityManagement.IPatch, ciąg correlationIdentifier);

    Interfejs publiczny Microsoft.SystemForCrossDomainIdentityManagement.IPatch {Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase PatchRequest {get; zestawu;} Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier ResourceIdentifier {get; zestawu;}        
    }

    Klasa publiczna PatchRequest2:    Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase {publicznych System.Collections.Generic.IReadOnlyCollection < Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation > operacji {get;}


   Jeśli usługa została skompilowana przy użyciu bibliotek Common Language Infrastructure obsługiwane przez firmę Microsoft do implementowania usługi Standard SCIM, żądanie jest tłumaczony na wywołanie do metody zapytania dostawcy usług. Wartość właściwości obiektu, podana jako wartość argumentu parametry są następujące: 
  
* Parametry. AlternateFilters.Count: 2
* parameters.AlternateFilters.ElementAt(x).AttributePath: „Identyfikator”
* parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
* Parametry. AlternateFilter.ElementAt(x). ComparisonValue:  "54D382A4-2050-4C03-94D1-E769F1D15682"
* parameters.AlternateFilters.ElementAt(y).AttributePath: "manager"
* Parametry. AlternateFilters.ElementAt(y). OperatorPorównania: ComparisonOperator.Equals
* Parametry. AlternateFilter.ElementAt(y). ComparisonValue:  "2819c223-7f76-453a-919d-413861904646"
* parameters.RequestedAttributePaths.ElementAt(0): „Identyfikator”
* Parametry. SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"

  W tym miejscu wartość indeksu x może być 0 i wartość y indeks może być 1, lub wartość x może być 1 i wartość y, może być 0, w zależności od kolejności wyrażeń parametr zapytania filtru.   

1. Oto przykład żądania z usługi Azure Active Directory z usługą Standard SCIM do zaktualizowania użytkownika: 

   ```
     PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
     Authorization: Bearer ...
     Content-type: application/scim+json
     {
       "schemas": 
       [
         "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
       "Operations":
       [
         {
           "op":"Add",
           "path":"manager",
           "value":
             [
               {
                 "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
                 "value":"2819c223-7f76-453a-919d-413861904646"}]}]}
   ```

   Biblioteki Microsoft Common Language Infrastructure dotyczące implementowania usługi Standard SCIM przetłumaczy żądania do wywołania metody aktualizacji dostawcy usług. Oto podpis metody aktualizacji: 

   ```
     // System.Threading.Tasks.Tasks and 
     // System.Collections.Generic.IReadOnlyCollection<T>
     // are defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IPatch, 
     // Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, 
     // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
     // Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, 
     // Microsoft.SystemForCrossDomainIdentityManagement.OperationName, 
     // Microsoft.SystemForCrossDomainIdentityManagement.IPath and 
     // Microsoft.SystemForCrossDomainIdentityManagement.OperationValue 
     // are all defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 

     System.Threading.Tasks.Task Update(
       Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch, 
       string correlationIdentifier);

     public interface Microsoft.SystemForCrossDomainIdentityManagement.IPatch
     {
     Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase 
       PatchRequest 
         { get; set; }
     Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
       ResourceIdentifier 
         { get; set; }        
     }

     public class PatchRequest2: 
       Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase
     {
     public System.Collections.Generic.IReadOnlyCollection
       <Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation> 
         Operations
         { get;}

     public void AddOperation(
       Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation operation);
     }

     public class PatchOperation
     {
     public Microsoft.SystemForCrossDomainIdentityManagement.OperationName 
       Name
       { get; set; }

     public Microsoft.SystemForCrossDomainIdentityManagement.IPath 
       Path
       { get; set; }

     public System.Collections.Generic.IReadOnlyCollection
       <Microsoft.SystemForCrossDomainIdentityManagement.OperationValue> Value
       { get; }

     public void AddValue(
       Microsoft.SystemForCrossDomainIdentityManagement.OperationValue value);
     }

     public enum OperationName
     {
       Add,
       Remove,
       Replace
     }

     public interface IPath
     {
       string AttributePath { get; }
       System.Collections.Generic.IReadOnlyCollection<IFilter> SubAttributes { get; }
       Microsoft.SystemForCrossDomainIdentityManagement.IPath ValuePath { get; }
     }

     public class OperationValue
     {
       public string Reference
       { get; set; }

       public string Value
       { get; set; }
     }
   ```

    W przykładzie żądania do zaktualizowania użytkownika obiektu podana jako wartość argumentu poprawki ma wartości tych właściwości: 
  
   * ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * ResourceIdentifier.SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"
   * (PatchRequest jako PatchRequest2). Operations.Count: 1
   * (PatchRequest jako PatchRequest2). Operations.ElementAt(0). OperationName: OperationName.Add
   * (PatchRequest jako PatchRequest2). Operations.ElementAt(0). Path.AttributePath: "manager"
   * (PatchRequest jako PatchRequest2). Operations.ElementAt(0). Value.Count: 1
   * (PatchRequest jako PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Odwołanie: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
   * (PatchRequest jako PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Wartość: 2819c223-7f76-453a-919d-413861904646

1. Aby anulować aprowizację użytkowników z magazynu tożsamości przez usługi Standard SCIM, usługi Azure AD wysyła żądanie takich jak: 

   ```
     DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
     Authorization: Bearer ...
   ```

   Jeśli usługa została skompilowana przy użyciu bibliotek Common Language Infrastructure obsługiwane przez firmę Microsoft do implementowania usługi Standard SCIM, żądanie jest tłumaczona na wywołanie metody usuwania, które dostawcy usług.   Ta metoda ma podpis: 

   ```
     // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
     // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
     System.Threading.Tasks.Task Delete(
       Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
         resourceIdentifier, 
       string correlationIdentifier);
   ```

   Obiekt udostępniany jako wartość argumentu resourceIdentifier ma wartości tych właściwości w przykładzie żądanie, aby anulować aprowizację użytkownika: 

1. Aby anulować aprowizację użytkowników z magazynu tożsamości przez usługi Standard SCIM, usługi Azure AD wysyła żądanie takich jak: 
   ````
    DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
   ````
   Jeśli usługa została skompilowana przy użyciu biblioteki interfejsu wiersza polecenia obsługiwane przez firmę Microsoft do implementowania usługi Standard SCIM, żądanie jest tłumaczona na wywołanie metody usuwania, które dostawcy usług.   Ta metoda ma podpis: 
   ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
    System.Threading.Tasks.Task Delete(
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
        resourceIdentifier, 
      string correlationIdentifier);
   ````
   Obiekt udostępniany jako wartość argumentu resourceIdentifier ma wartości tych właściwości w przykładzie żądanie, aby anulować aprowizację użytkownika: 
  
   * ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * ResourceIdentifier.SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="user-and-group-schema-reference"></a>Odwołanie do schematu użytkowników i grup
Usługa Azure Active Directory można udostępnić dwa typy zasobów w usługach sieci web Standard SCIM.  Te typy zasobów to użytkowników i grup.  

Zasoby użytkowników są identyfikowane przez identyfikator schematu `urn:ietf:params:scim:schemas:extension:enterprise:2.0:User`, który znajduje się w tej specyfikacji protokołu: https://tools.ietf.org/html/rfc7643.  Domyślne mapowanie atrybutów użytkowników w usłudze Azure Active Directory do atrybutów zasobów użytkownika znajduje się w tabeli 1.  

Grupy zasobów są identyfikowane przez identyfikator schematu `urn:ietf:params:scim:schemas:core:2.0:Group`. Tabela 2 zawiera domyślne mapowanie atrybutów grupy w usłudze Azure Active Directory do atrybutów grupy zasobów.  

### <a name="table-1-default-user-attribute-mapping"></a>Tabela 1: Domyślne mapowanie atrybutu użytkownika

| Użytkownika usługi Azure Active Directory | "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User" |
| --- | --- |
| IsSoftDeleted |aktywne |
| displayName |displayName |
| Facsimile-TelephoneNumber |wartość phoneNumbers [typ eq "faksu"] |
| givenName |name.givenName |
| Stanowisko |tytuł |
| poczta |wiadomości e-mail [typ eq "Praca"] .value |
| mailNickname |externalId |
| menedżer |menedżer |
| Telefon komórkowy |wartość phoneNumbers [eq wpisz "wyraz mobile"] |
| Identyfikator obiektu |ID |
| postalCode |.postalCode adresy [typ eq "Praca"] |
| proxy-Addresses |wiadomości e-mail [Wpisz eq "other"]. Wartość |
| physical-Delivery-OfficeName |adresy [Wpisz eq "other"]. Sformatowany |
| Adres |.streetAddress adresy [typ eq "Praca"] |
| nazwisko |name.familyName |
| Numer telefonu |wartość phoneNumbers [typ eq "Praca"] |
| user-PrincipalName |userName |

### <a name="table-2-default-group-attribute-mapping"></a>Tabela 2: Mapowanie atrybutów grupy domyślnej

| Grupa usługi Azure Active Directory | urn:ietf:params:scim:schemas:core:2.0:Group |
| --- | --- |
| displayName |externalId |
| poczta |wiadomości e-mail [typ eq "Praca"] .value |
| mailNickname |displayName |
| członkowie |członkowie |
| Identyfikator obiektu |ID |
| proxyAddresses |wiadomości e-mail [Wpisz eq "other"]. Wartość |


## <a name="related-articles"></a>Pokrewne artykuły:
* [Automatyzowanie użytkownika aprowizacji/Deprovisioning do aplikacji SaaS](user-provisioning.md)
* [Dostosowywanie mapowań atrybutów dla aprowizacji użytkowników](customize-application-attributes.md)
* [Pisanie wyrażeń do mapowania atrybutów](functions-for-customizing-application-data.md)
* [Filtrów określania zakresu na potrzeby aprowizacji użytkownika](define-conditional-rules-for-provisioning-user-accounts.md)
* [Powiadomienia związane z aprowizacją kont](user-provisioning.md)
* [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS](../saas-apps/tutorial-list.md)

<!--Image references-->
[0]: ./media/use-scim-to-provision-users-and-groups/scim-figure-1.png
[1]: ./media/use-scim-to-provision-users-and-groups/scim-figure-2a.png
[2]: ./media/use-scim-to-provision-users-and-groups/scim-figure-2b.png
[3]: ./media/use-scim-to-provision-users-and-groups/scim-figure-3.png
[4]: ./media/use-scim-to-provision-users-and-groups/scim-figure-4.png
[5]: ./media/use-scim-to-provision-users-and-groups/scim-figure-5.png
