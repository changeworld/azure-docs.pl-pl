---
title: Standard scim aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak utworzyć punkt końcowy Standard scim, zintegrować interfejs API Standard scim z usługą Azure Active Directory i rozpocząć Automatyzowanie aprowizacji użytkowników i grup w aplikacjach.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: mimart
ms.reviewer: arvinh
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: c9c15a462692c257fac759998698679d9e59dc53
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/31/2019
ms.locfileid: "73242296"
---
# <a name="scim-user-provisioning-with-azure-active-directory"></a>Standard scim aprowizacji użytkowników przy użyciu Azure Active Directory

System do zarządzania tożsamościami między domenami ([Standard scim](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/bg-p/IdentityStandards)) jest standardowym protokołem i schematem, który ma na celu zwiększenie spójności w zakresie zarządzania tożsamościami w różnych systemach. Gdy aplikacja obsługuje punkt końcowy Standard scim do zarządzania użytkownikami, usługa aprowizacji użytkowników w usłudze Azure AD może wysyłać żądania, aby tworzyć, modyfikować lub usuwać przypisanych użytkowników i grupy do tego punktu końcowego.

Wiele aplikacji, dla których usługa Azure AD obsługuje [wstępnie zintegrowane automatyczne Inicjowanie obsługi użytkowników](../saas-apps/tutorial-list.md) , standard scim jako środek do odbierania powiadomień o zmianach użytkownika.  Oprócz tych klientów mogą łączyć aplikacje obsługujące określony profil [specyfikacji protokołu standard scim 2,0](https://tools.ietf.org/html/rfc7644) przy użyciu ogólnej opcji integracji "bez galerii" w Azure Portal.

Głównym celem tego artykułu jest profil Standard scim 2,0, który usługa Azure AD implementuje jako część ogólnego łącznika Standard scim dla aplikacji innych niż Galeria. Pomyślne testowanie aplikacji, która obsługuje standard scim za pomocą ogólnego łącznika usługi Azure AD, jest jednak krokiem do uzyskania aplikacji wymienionej w galerii usługi Azure AD w ramach obsługi aprowizacji użytkowników. Aby uzyskać więcej informacji na temat uzyskiwania aplikacji na liście w galerii aplikacji usługi Azure AD, zobacz [How to: list The Application w galerii aplikacji usługi Azure AD](../develop/howto-app-gallery-listing.md).

> [!IMPORTANT]
> Ostatnio Zaktualizowano zachowanie implementacji usługi Azure AD Standard scim w dniu 18 grudnia 2018. Aby uzyskać informacje na temat zmian, zobacz [zgodność protokołów standard scim 2,0 usługi Azure AD User Provisioning](application-provisioning-config-problem-scim-compatibility.md).

![przedstawia Inicjowanie obsługi z usługi Azure AD do aplikacji lub magazynu tożsamości][0]<br/>
*Rysunek 1: Inicjowanie obsługi z Azure Active Directory do aplikacji lub magazynu tożsamości implementującego Standard scim*

Ten artykuł jest podzielony na cztery sekcje:

* **[Inicjowanie obsługi administracyjnej użytkowników i grup w aplikacjach innych firm, które obsługują standard scim 2,0](#provisioning-users-and-groups-to-applications-that-support-scim)** — Jeśli Twoja organizacja korzysta z aplikacji innych firm, która IMPLEMENTUJE profil Standard scim 2,0, który obsługuje usługa Azure AD, możesz rozpocząć automatyzację zarówno aprowizacji, jak i już dziś cofnięto Inicjowanie obsługi użytkowników i grup.
* **[Opis implementacji usługi Azure AD Standard scim](#understanding-the-azure-ad-scim-implementation)** — Jeśli tworzysz aplikację, która obsługuje interfejs API zarządzania użytkownikami w systemie Standard scim 2,0, w tej sekcji szczegółowo opisano sposób implementacji klienta usługi Azure AD Standard scim oraz sposób modelowania protokołu Standard scim Obsługa żądań i odpowiedzi.
* **[Kompilowanie punktu końcowego Standard scim przy użyciu bibliotek interfejsu wiersza polecenia firmy Microsoft](#building-a-scim-endpoint-using-microsoft-cli-libraries)** Common Language Infrastructure — biblioteki interfejsu wiersza polecenia (CLI) i przykłady kodu pokazują, jak utworzyć punkt końcowy Standard scim i przetłumaczyć komunikaty Standard scim.  
* **[Odwołanie do schematu użytkownika i grupy](#user-and-group-schema-reference)** — zawiera opis schematu użytkownika i grupy obsługiwanego przez implementację Standard scim usługi Azure AD dla aplikacji spoza galerii.

## <a name="provisioning-users-and-groups-to-applications-that-support-scim"></a>Inicjowanie obsługi administracyjnej użytkowników i grup w aplikacjach, które obsługują standard scim

Usługę Azure AD można skonfigurować tak, aby automatycznie udostępniać przypisanym użytkownikom i grupom aplikacje implementujące konkretny profil [protokołu standard scim 2,0](https://tools.ietf.org/html/rfc7644). Informacje dotyczące profilu są udokumentowane w temacie [Implementacja usługi Azure AD Standard scim](#understanding-the-azure-ad-scim-implementation).

Skontaktuj się z dostawcą aplikacji lub dokumentacją dostawcy aplikacji, aby uzyskać instrukcje dotyczące zgodności z tymi wymaganiami.

> [!IMPORTANT]
> Implementacja usługi Azure AD Standard scim jest oparta na usłudze Azure AD User Provisioning, która została zaprojektowana w celu ciągłego utrzymywania synchronizacji użytkowników między usługą Azure AD a aplikacją docelową i implementuje bardzo konkretny zestaw standardowych operacji. Ważne jest, aby zrozumieć te zachowania, aby zrozumieć zachowanie klienta usługi Azure AD Standard scim. Aby uzyskać więcej informacji, zobacz [co się dzieje podczas aprowizacji użytkowników?](user-provisioning.md#what-happens-during-provisioning).

### <a name="getting-started"></a>Wprowadzenie

Aplikacje obsługujące profil Standard scim opisany w tym artykule mogą być połączone z Azure Active Directory przy użyciu funkcji "aplikacja bez galerii" w galerii aplikacji usługi Azure AD. Po nawiązaniu połączenia usługa Azure AD uruchamia proces synchronizacji co 40 minut, gdzie wysyła zapytanie do punktu końcowego Standard scim aplikacji dla przypisanych użytkowników i grup, a następnie tworzy lub modyfikuje je zgodnie z informacjami o przypisaniu.

**Aby połączyć aplikację, która obsługuje standard scim:**

1. Zaloguj się do [portalu Azure Active Directory](https://aad.portal.azure.com). Należy pamiętać, że możesz uzyskać dostęp do bezpłatnej wersji próbnej Azure Active Directory z licencjami P2, rejestrując się w [programie dla deweloperów](https://developer.microsoft.com/office/dev-program)
1. W okienku po lewej stronie wybierz pozycję **aplikacje dla przedsiębiorstw** . Zostanie wyświetlona lista wszystkich skonfigurowanych aplikacji, w tym aplikacji, które zostały dodane z galerii.
1. Wybierz pozycję **+ Nowa aplikacja** > **wszystkie** > **aplikacji spoza galerii**.
1. Wprowadź nazwę aplikacji, a następnie wybierz pozycję **Dodaj** , aby utworzyć obiekt aplikacji. Nowa aplikacja zostanie dodana do listy aplikacji dla przedsiębiorstw i zostanie otwarta na swoim ekranie zarządzania aplikacjami.

   Zrzut ekranu ![przedstawia galerię aplikacji usługi Azure AD][1]<br/>
   *Rysunek 2. Galeria aplikacji usługi Azure AD*

1. Na ekranie Zarządzanie aplikacjami wybierz opcję **Inicjowanie obsługi** w lewym panelu.
1. W menu **tryb aprowizacji** wybierz pozycję **automatycznie**.

   ![przykład: Strona aprowizacji aplikacji w Azure Portal][2]<br/>
   *Rysunek 3. Konfigurowanie aprowizacji w Azure Portal*

1. W polu **adres URL dzierżawy** wprowadź adres URL punktu końcowego Standard scim aplikacji. Przykład: https://api.contoso.com/scim/
1. Jeśli punkt końcowy Standard scim wymaga tokenu okaziciela OAuth od wystawcy innego niż usługa Azure AD, a następnie skopiuj wymagany token okaziciela OAuth do pola opcjonalnego **tokenu tajnego** . Jeśli to pole pozostanie puste, usługa Azure AD zawiera token okaziciela OAuth wystawiony przez usługę Azure AD za pomocą każdego żądania. Aplikacje korzystające z usługi Azure AD jako dostawca tożsamości mogą sprawdzić poprawność tego tokenu wystawionego przez usługę Azure AD.
1. Wybierz pozycję **Testuj połączenie** , aby uzyskać Azure Active Directory próbę nawiązania połączenia z punktem końcowym Standard scim. Jeśli próba nie powiedzie się, zostanie wyświetlony komunikat o błędzie.  

    > [!NOTE]
    > **Test connection** wysyła zapytanie do punktu końcowego Standard scim dla użytkownika, który nie istnieje, przy użyciu losowego identyfikatora GUID jako pasującej właściwości wybranej w konfiguracji usługi Azure AD. Oczekiwana prawidłowa odpowiedź to HTTP 200 OK z pustym komunikatem Standard scim ListResponse.

1. Jeśli próba nawiązania połączenia z aplikacją zakończyła się pomyślnie, wybierz pozycję **Zapisz** , aby zapisać poświadczenia administratora.
1. W sekcji **mapowania** istnieją dwa możliwe do wyboru zestawy mapowań atrybutów: jeden dla obiektów użytkownika i jeden dla obiektów grupy. Wybierz każdą z nich, aby przejrzeć atrybuty, które są synchronizowane z Azure Active Directory do aplikacji. Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowywania do użytkowników i grup w aplikacji w celu wykonywania operacji aktualizacji. Wybierz pozycję **Zapisz** , aby zatwierdzić wszelkie zmiany.

    > [!NOTE]
    > Można opcjonalnie wyłączyć synchronizowanie obiektów grupy przez wyłączenie mapowania "grupy".

1. W obszarze **Ustawienia**pole **zakres** określa, którzy użytkownicy i grupy są synchronizowane. Wybierz opcję **Synchronizuj tylko przypisani Użytkownicy i grupy** (zalecane) tylko do synchronizacji użytkowników i grup przypisanych na karcie **Użytkownicy i grupy** .
1. Po zakończeniu konfiguracji Ustaw **stan aprowizacji** na **włączone**.
1. Wybierz pozycję **Zapisz** , aby uruchomić usługę Azure AD Provisioning.
1. W przypadku synchronizacji tylko przypisanych użytkowników i grup (zalecane) należy wybrać kartę **Użytkownicy i grupy** i przypisać użytkowników lub grupy, które mają zostać zsynchronizowane.

Po rozpoczęciu pierwszego cyklu możesz wybrać opcję **dzienniki inspekcji** w lewym panelu, aby monitorować postęp, który pokazuje wszystkie akcje wykonywane przez usługę aprowizacji w aplikacji. Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](check-status-user-account-provisioning.md).

> [!NOTE]
> Cykl początkowy trwa dłużej niż w przypadku późniejszej synchronizacji, który występuje około co 40 minut, o ile usługa jest uruchomiona.

**Aby opublikować aplikację w galerii aplikacji usługi Azure AD:**

Jeśli tworzysz aplikację, która będzie używana przez więcej niż jedną dzierżawę, możesz ją udostępnić w galerii aplikacji usługi Azure AD. Dzięki temu organizacje mogą łatwo odnaleźć aplikację i skonfigurować obsługę administracyjną. Publikowanie aplikacji w galerii usługi Azure AD i udostępnianie jej innym osobom jest proste. Zapoznaj się z opisanymi [tutaj](https://docs.microsoft.com/azure/active-directory/develop/howto-app-gallery-listing)instrukcjami. 
## <a name="understanding-the-azure-ad-scim-implementation"></a>Omówienie implementacji usługi Azure AD Standard scim

Jeśli tworzysz aplikację, która obsługuje interfejs API zarządzania użytkownikami w systemie Standard scim 2,0, w tej sekcji szczegółowo opisano sposób implementacji klienta usługi Azure AD Standard scim i sposób modelowania obsługi żądań i odpowiedzi protokołu Standard scim. Po wdrożeniu punktu końcowego Standard scim można go przetestować, wykonując procedurę opisaną w poprzedniej sekcji.

W [specyfikacji protokołu standard scim 2,0](http://www.simplecloud.info/#Specification), aplikacja musi spełniać następujące wymagania:

* Obsługuje tworzenie użytkowników, a także grupy, zgodnie z sekcją [3,3 protokołu Standard scim](https://tools.ietf.org/html/rfc7644#section-3.3).  
* Obsługuje modyfikowanie użytkowników lub grup przy użyciu żądań poprawek, zgodnie z [sekcją 3.5.2 protokołu Standard scim](https://tools.ietf.org/html/rfc7644#section-3.5.2).  
* Obsługuje pobieranie znanego zasobu dla użytkownika lub grupy utworzonej wcześniej, zgodnie [z sekcją 1 protokołu Standard scim](https://tools.ietf.org/html/rfc7644#section-3.4.1).  
* Obsługuje kwerendy użytkowników lub grup, zgodnie z sekcją [3.4.2 protokołu Standard scim](https://tools.ietf.org/html/rfc7644#section-3.4.2).  Domyślnie użytkownicy są pobierani przez `id` i sprawdzani przez ich `username` i `externalid`, a grupy są pytani przez `displayName`.  
* Obsługuje zapytania użytkownika według identyfikatora i Menedżera, zgodnie z sekcją 3.4.2 protokołu Standard scim.  
* Obsługuje wykonywanie zapytań względem grup według identyfikatorów i elementów członkowskich, zgodnie z sekcją 3.4.2 protokołu Standard scim.  
* Akceptuje pojedynczy token okaziciela na potrzeby uwierzytelniania i autoryzacji usługi Azure AD w aplikacji.

Postępuj zgodnie z ogólnymi wskazówkami dotyczącymi wdrażania punktu końcowego Standard scim, aby zapewnić zgodność z usługą Azure AD:

* `id` jest właściwością wymaganą dla wszystkich zasobów. Każda odpowiedź zwracająca zasób powinna mieć pewność, że każdy zasób ma tę właściwość, z wyjątkiem `ListResponse` z zerowymi elementami członkowskimi.
* Odpowiedź na żądanie zapytania/filtru powinna zawsze być `ListResponse`.
* Grupy są opcjonalne, ale są obsługiwane tylko wtedy, gdy implementacja Standard scim obsługuje żądania PATCH.
* Nie trzeba uwzględniać całego zasobu w odpowiedzi na POPRAWKĘ.
* Microsoft Azure AD stosuje tylko następujące operatory:  
     - `eq`
     - `and`
* Nie należy wymagać dopasowania uwzględniającego wielkość liter w elementach konstrukcyjnych w Standard scim, w szczególności wartościach operacji `op`, zgodnie z definicją w https://tools.ietf.org/html/rfc7644#section-3.5.2. Usługa Azure AD emituje wartości elementu "op" jako `Add`, `Replace` i `Remove`.
* Microsoft Azure AD wykonuje żądania pobrania losowego użytkownika i grupy, aby upewnić się, że punkt końcowy i poświadczenia są prawidłowe. Jest również wykonywane w ramach przepływu **połączenia testowego** w [Azure Portal](https://portal.azure.com). 
* Atrybut, w którym można wykonywać zapytania o zasoby, powinien być ustawiony jako pasujący atrybut w aplikacji w [Azure Portal](https://portal.azure.com). Aby uzyskać więcej informacji, zobacz [Dostosowywanie mapowań atrybutów aprowizacji użytkowników](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings)

### <a name="user-provisioning-and-de-provisioning"></a>Inicjowanie obsługi użytkowników i Inicjowanie obsługi administracyjnej

Na poniższej ilustracji przedstawiono komunikaty, które Azure Active Directory wysyła do usługi Standard scim w celu zarządzania cyklem życia użytkownika w magazynie tożsamości aplikacji.  

![przedstawia][4] sekwencji aprowizacji użytkowników i anulowania aprowizacji<br/>
*Ilustracja 4. Inicjowanie obsługi użytkowników i anulowanie aprowizacji*

### <a name="group-provisioning-and-de-provisioning"></a>Inicjowanie obsługi administracyjnej grup i Inicjowanie obsługi administracyjnej

Inicjowanie obsługi grup i Inicjowanie obsługi administracyjnej są opcjonalne. Po zaimplementowaniu i włączeniu na poniższej ilustracji przedstawiono komunikaty wysyłane przez usługę Azure AD do usługi Standard scim w celu zarządzania cyklem życia grupy w magazynie tożsamości aplikacji.  Komunikaty te różnią się od komunikatów o użytkownikach na dwa sposoby:

* Żądania pobrania grup określają, że atrybut Members ma być wykluczony z dowolnego zasobu dostarczonego w odpowiedzi na żądanie.  
* Żądania, aby określić, czy atrybut odwołania ma pewne wartości są żądaniami dotyczącymi atrybutu Members.  

![przedstawia sekwencję aprowizacji i nieaprowizacji grupy][5]<br/>
*Rysunek 5: Inicjowanie obsługi grup i kolejność anulowania aprowizacji*

### <a name="scim-protocol-requests-and-responses"></a>Żądania i odpowiedzi protokołu Standard scim
Ta sekcja zawiera przykładowe żądania Standard scim emitowane przez klienta usługi Azure AD Standard scim i przykładowe oczekiwane odpowiedzi. Aby uzyskać najlepsze wyniki, należy zakodować aplikację do obsługi tych żądań w tym formacie i emitować oczekiwane odpowiedzi.

> [!IMPORTANT]
> Aby dowiedzieć się, jak i kiedy usługa aprowizacji użytkowników w usłudze Azure AD emituje opisane poniżej operacje, zobacz [co się dzieje podczas aprowizacji użytkowników?](user-provisioning.md#what-happens-during-provisioning).

- [Operacje użytkownika](#user-operations)
  - [Utwórz użytkownika](#create-user)
    - [Żądanie](#request)
    - [Odpowiedź](#response)
  - [Pobierz użytkownika](#get-user)
    - [Żądanie](#request-1)
    - [Odpowiedź](#response-1)
  - [Pobierz użytkownika według zapytania](#get-user-by-query)
    - [Żądanie](#request-2)
    - [Odpowiedź](#response-2)
  - [Pobierz użytkownika według zapytania — wyniki zerowe](#get-user-by-query---zero-results)
    - [Żądanie](#request-3)
    - [Odpowiedź](#response-3)
  - [Aktualizowanie użytkownika [właściwości wielowartościowe]](#update-user-multi-valued-properties)
    - [Żądanie](#request-4)
    - [Odpowiedź](#response-4)
  - [Aktualizowanie użytkownika [właściwości pojedynczej wartości]](#update-user-single-valued-properties)
    - [Żądanie](#request-5)
    - [Odpowiedź](#response-5)
  - [Usuń użytkownika](#delete-user)
    - [Żądanie](#request-6)
    - [Odpowiedź](#response-6)
- [Operacje grupy](#group-operations)
  - [Utwórz grupę](#create-group)
    - [Żądanie](#request-7)
    - [Odpowiedź](#response-7)
  - [Pobierz grupę](#get-group)
    - [Żądanie](#request-8)
    - [Odpowiedź](#response-8)
  - [Pobierz grupowanie według displayName](#get-group-by-displayname)
    - [Żądanie](#request-9)
    - [Odpowiedź](#response-9)
  - [Aktualizacja grupy [atrybuty niebędące elementami członkowskimi]](#update-group-non-member-attributes)
    - [Żądanie](#request-10)
    - [Odpowiedź](#response-10)
  - [Grupa aktualizacji [Dodaj członków]](#update-group-add-members)
    - [Żądanie](#request-11)
    - [Odpowiedź](#response-11)
  - [Grupa aktualizacji [usuwanie członków]](#update-group-remove-members)
    - [Żądanie](#request-12)
    - [Odpowiedź](#response-12)
  - [Usuń grupę](#delete-group)
    - [Żądanie](#request-13)
    - [Odpowiedź](#response-13)

### <a name="user-operations"></a>Operacje użytkownika

* Użytkownicy mogą wykonywać zapytania o atrybuty `userName` lub `email[type eq "work"]`.  

#### <a name="create-user"></a>Utwórz użytkownika

###### <a name="request"></a>Prośba

*Opublikuj/users*
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

*Utworzono protokół HTTP/1.1 201*
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

#### <a name="get-user"></a>Pobierz użytkownika

###### <a name="request-1"></a>Żądając
*Pobierz/users/5d48a0a8e9f04aa38008* 

###### <a name="response-1"></a>Odpowiedź (znaleziono użytkownika)
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

###### <a name="request"></a>Prośba
*Pobierz/users/5171a35d82074e068ce2* 

###### <a name="response-user-not-found-note-that-the-detail-is-not-required-only-status"></a>Odpowiedź (nie znaleziono użytkownika. Należy zauważyć, że szczegóły nie są wymagane, tylko stan.)

```json
{
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:Error"
    ],
    "status": "404",
    "detail": "Resource 23B51B0E5D7AE9110A49411D@7cca31655d49f3640a494224 not found"
}
```

#### <a name="get-user-by-query"></a>Pobierz użytkownika według zapytania

##### <a name="request-2"></a>Żądając

*POBRAĆ wartość/users? Filter = userName EQ "Test_User_dfeef4c5-5681 -4387-b016-bdf221e82081"*

##### <a name="response-2"></a>Reakcji

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

#### <a name="get-user-by-query---zero-results"></a>Pobierz użytkownika według zapytania — wyniki zerowe

##### <a name="request-3"></a>Żądając

*POBRAĆ wartość/users? Filter = userName EQ "nieistniejącego użytkownika"*

##### <a name="response-3"></a>Reakcji

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

#### <a name="update-user-multi-valued-properties"></a>Aktualizowanie użytkownika [właściwości wielowartościowe]

##### <a name="request-4"></a>Żądając

*Poprawka/users/6764549bef60420686bc HTTP/1.1*
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

##### <a name="response-4"></a>Reakcji

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

#### <a name="update-user-single-valued-properties"></a>Aktualizowanie użytkownika [właściwości pojedynczej wartości]

##### <a name="request-5"></a>Żądając

*Poprawka/users/5171a35d82074e068ce2 HTTP/1.1*
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

##### <a name="response-5"></a>Reakcji

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

##### <a name="request-6"></a>Żądając

*Usuń/users/5171a35d82074e068ce2 HTTP/1.1*

##### <a name="response-6"></a>Reakcji

*HTTP/1.1 204 Brak zawartości*

### <a name="group-operations"></a>Operacje grupy

* Grupy zawsze są tworzone z pustą listą członków.
* Do grup można odpytać atrybut `displayName`.
* Aktualizacja żądania poprawek grupy powinna spowodować, że w odpowiedzi *nie ma żadnej zawartości HTTP 204* . Zwracanie treści z listą wszystkich elementów członkowskich nie jest zalecane.
* Nie trzeba obsługiwać zwracania wszystkich elementów członkowskich grupy.

#### <a name="create-group"></a>Tworzenie grupy

##### <a name="request-7"></a>Żądając

*/Groups HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group", "http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/2.0/Group"],
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "displayName": "displayName",
    "meta": {
        "resourceType": "Group"
    }
}
```

##### <a name="response-7"></a>Reakcji

*Utworzono protokół HTTP/1.1 201*
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

##### <a name="request-8"></a>Żądając

*GET/Groups/40734ae655284ad3abcc? excludedAttributes = Members HTTP/1.1*

##### <a name="response-8"></a>Reakcji
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

#### <a name="get-group-by-displayname"></a>Pobierz grupowanie według displayName

##### <a name="request-9"></a>Żądając
*GET/Groups? excludedAttributes = memberss & Filter = displayName EQ "displayName" HTTP/1.1*

##### <a name="response-9"></a>Reakcji

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

#### <a name="update-group-non-member-attributes"></a>Aktualizacja grupy [atrybuty niebędące elementami członkowskimi]

##### <a name="request-10"></a>Żądając

*Poprawka/Groups/fa2ce26709934589afc5 HTTP/1.1*
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

##### <a name="response-10"></a>Reakcji

*HTTP/1.1 204 Brak zawartości*

### <a name="update-group-add-members"></a>Grupa aktualizacji [Dodaj członków]

##### <a name="request-11"></a>Żądając

*Poprawka/Groups/a99962b9f99d4c4fac67 HTTP/1.1*
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

##### <a name="response-11"></a>Reakcji

*HTTP/1.1 204 Brak zawartości*

#### <a name="update-group-remove-members"></a>Grupa aktualizacji [usuwanie członków]

##### <a name="request-12"></a>Żądając

*Poprawka/Groups/a99962b9f99d4c4fac67 HTTP/1.1*
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

##### <a name="response-12"></a>Reakcji

*HTTP/1.1 204 Brak zawartości*

#### <a name="delete-group"></a>Usuń grupę

##### <a name="request-13"></a>Żądając

*Usuń/Groups/cdb1ce18f65944079d37 HTTP/1.1*

##### <a name="response-13"></a>Reakcji

*HTTP/1.1 204 Brak zawartości*

## <a name="building-a-scim-endpoint-using-microsoft-cli-libraries"></a>Kompilowanie punktu końcowego Standard scim przy użyciu bibliotek interfejsu wiersza polecenia firmy Microsoft

Tworząc usługę sieci Web Standard scim, która jest obsługiwana przy użyciu Azure Active Directory, można włączyć automatyczne Inicjowanie obsługi użytkowników dla praktycznie dowolnej aplikacji lub magazynu tożsamości.

Oto jak to działa:

1. Usługa Azure AD udostępnia bibliotekę Common Language Infrastructure (CLI) o nazwie Microsoft. SystemForCrossDomainIdentityManagement, dołączoną do przykładów kodu opisującą poniżej. Integratory systemów i deweloperzy mogą używać tej biblioteki do tworzenia i wdrażania punktu końcowego usługi sieci Web opartej na Standard scim, który może połączyć usługę Azure AD z magazynem tożsamości dowolnej aplikacji.
2. Mapowania są zaimplementowane w usłudze sieci Web w celu zamapowania standardowego schematu użytkownika na schemat użytkownika i protokół wymagany przez aplikację. 
3. Adres URL punktu końcowego jest rejestrowany w usłudze Azure AD jako część aplikacji niestandardowej w galerii aplikacji.
4. Użytkownicy i grupy są przypisani do tej aplikacji w usłudze Azure AD. Po przypisaniu są one umieszczane w kolejce w celu synchronizacji z aplikacją docelową. Proces synchronizacji obsługujący kolejkę jest uruchamiany co 40 minut.

### <a name="code-samples"></a>Przykłady kodu

Aby ułatwić ten proces, należy podać [przykłady kodu](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) , które tworzą punkt końcowy usługi sieci Web Standard scim i przedstawiają automatyczną obsługę administracyjną. Przykładem jest dostawca, który przechowuje plik z wierszami wartości rozdzielonych przecinkami reprezentującymi użytkowników i grupy.

**Wymagania wstępne**

* Visual Studio 2013 lub nowszy
* [Zestaw Azure SDK dla platformy .NET](https://azure.microsoft.com/downloads/)
* Komputer z systemem Windows, który obsługuje ASP.NET Framework 4,5, do użycia jako punkt końcowy Standard scim. Ta maszyna musi być dostępna w chmurze.
* [Subskrypcja platformy Azure z wersją próbną lub licencjonowaną Azure AD — wersja Premium](https://azure.microsoft.com/services/active-directory/)

### <a name="getting-started"></a>Wprowadzenie

Najprostszym sposobem implementacji punktu końcowego Standard scim, który może akceptować żądania aprowizacji z usługi Azure AD, jest skompilowanie i wdrożenie przykładu kodu, który umożliwia użytkownikom zainicjowanie obsługi administracyjnej, do pliku z wartościami rozdzielanymi przecinkami (CSV).

#### <a name="to-create-a-sample-scim-endpoint"></a>Aby utworzyć przykładowy punkt końcowy Standard scim

1. Pobierz pakiet przykładowego kodu pod [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)
1. Rozpakuj pakiet i umieść go na komputerze z systemem Windows w lokalizacji takiej jak C:\AzureAD-BYOA-Provisioning-Samples\.
1. W tym folderze Uruchom projekt FileProvisioning\Host\FileProvisioningService.csproj w programie Visual Studio.
1. Wybierz pozycję **narzędzia** > **menedżer pakietów NuGet** > **konsola Menedżera pakietów**i wykonaj następujące polecenia dla projektu FileProvisioningService, aby rozpoznać odwołania do rozwiązania:

   ```powershell
    Update-Package -Reinstall
   ```

1. Kompiluj projekt FileProvisioningService.
1. Uruchom aplikację wiersza polecenia w systemie Windows (jako administrator), a następnie użyj polecenia **CD** , aby zmienić katalog na folder **\AzureAD-BYOA-Provisioning-Samples\FileProvisioning\Host\bin\Debug** .
1. Uruchom następujące polecenie, zastępując `<ip-address>` adresem IP lub nazwą domeny maszyny z systemem Windows:

   ```
    FileSvc.exe http://<ip-address>:9000 TargetFile.csv
   ```

1. W systemie Windows w obszarze **Ustawienia systemu windows** > **Sieć & Ustawienia internetowe**, wybierz**ustawienia zaawansowane** **zapory systemu Windows** >  i Utwórz **regułę ruchu przychodzącego** , która zezwala na dostęp przychodzący do portu 9000.
1. Jeśli komputer z systemem Windows znajduje się za routerem, router musi być skonfigurowany do uruchamiania translacji dostępu do sieci między portem 9000, który jest dostępny dla Internetu, a port 9000 na komputerze z systemem Windows. Ta konfiguracja jest wymagana, aby usługa Azure AD mogła uzyskiwać dostęp do tego punktu końcowego w chmurze.

#### <a name="to-register-the-sample-scim-endpoint-in-azure-ad"></a>Aby zarejestrować punkt końcowy Standard scim z przykładem w usłudze Azure AD

1. Zaloguj się do [portalu Azure Active Directory](https://aad.portal.azure.com). 
1. W okienku po lewej stronie wybierz pozycję **aplikacje dla przedsiębiorstw** . Zostanie wyświetlona lista wszystkich skonfigurowanych aplikacji, w tym aplikacji, które zostały dodane z galerii.
1. Wybierz pozycję **+ Nowa aplikacja** > **wszystkie** > **aplikacji spoza galerii**.
1. Wprowadź nazwę aplikacji, a następnie wybierz pozycję **Dodaj** , aby utworzyć obiekt aplikacji. Utworzony obiekt aplikacji jest przeznaczony do reprezentowania aplikacji docelowej, która ma zostać zainicjowana, i implementująca Logowanie jednokrotne dla programu, a nie tylko punkt końcowy Standard scim.
1. Na ekranie Zarządzanie aplikacjami wybierz opcję **Inicjowanie obsługi** w lewym panelu.
1. W menu **tryb aprowizacji** wybierz pozycję **automatycznie**.    
1. W polu **adres URL dzierżawy** wprowadź adres URL punktu końcowego Standard scim aplikacji. Przykład: https://api.contoso.com/scim/

1. Jeśli punkt końcowy Standard scim wymaga tokenu okaziciela OAuth od wystawcy innego niż usługa Azure AD, a następnie skopiuj wymagany token okaziciela OAuth do pola opcjonalnego **tokenu tajnego** . Jeśli to pole pozostanie puste, usługa Azure AD zawiera token okaziciela OAuth wystawiony przez usługę Azure AD za pomocą każdego żądania. Aplikacje korzystające z usługi Azure AD jako dostawca tożsamości mogą sprawdzić poprawność tego tokenu wystawionego przez usługę Azure AD.
1. Wybierz pozycję **Testuj połączenie** , aby uzyskać Azure Active Directory próbę nawiązania połączenia z punktem końcowym Standard scim. Jeśli próba nie powiedzie się, zostanie wyświetlony komunikat o błędzie.  

    > [!NOTE]
    > **Test connection** wysyła zapytanie do punktu końcowego Standard scim dla użytkownika, który nie istnieje, przy użyciu losowego identyfikatora GUID jako pasującej właściwości wybranej w konfiguracji usługi Azure AD. Oczekiwana prawidłowa odpowiedź to HTTP 200 OK z pustym komunikatem Standard scim ListResponse

1. Jeśli próba nawiązania połączenia z aplikacją zakończyła się pomyślnie, wybierz pozycję **Zapisz** , aby zapisać poświadczenia administratora.
1. W sekcji **mapowania** istnieją dwa możliwe do wyboru zestawy mapowań atrybutów: jeden dla obiektów użytkownika i jeden dla obiektów grupy. Wybierz każdą z nich, aby przejrzeć atrybuty, które są synchronizowane z Azure Active Directory do aplikacji. Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowywania do użytkowników i grup w aplikacji w celu wykonywania operacji aktualizacji. Wybierz pozycję **Zapisz** , aby zatwierdzić wszelkie zmiany.
1. W obszarze **Ustawienia**pole **zakres** określa, którzy użytkownicy i grupy są synchronizowane. Wybierz opcję **"Synchronizuj tylko przypisanych użytkowników i grupy** (zalecane) tylko do synchronizacji użytkowników i grup przypisanych na karcie **Użytkownicy i grupy** .
1. Po zakończeniu konfiguracji Ustaw **stan aprowizacji** na **włączone**.
1. Wybierz pozycję **Zapisz** , aby uruchomić usługę Azure AD Provisioning.
1. W przypadku synchronizacji tylko przypisanych użytkowników i grup (zalecane) należy wybrać kartę **Użytkownicy i grupy** i przypisać użytkowników lub grupy, które mają zostać zsynchronizowane.

Po rozpoczęciu pierwszego cyklu możesz wybrać opcję **dzienniki inspekcji** w lewym panelu, aby monitorować postęp, który pokazuje wszystkie akcje wykonywane przez usługę aprowizacji w aplikacji. Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](check-status-user-account-provisioning.md).

Ostatnim krokiem w weryfikacji przykładu jest otwarcie pliku TargetFile. csv w folderze \AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug na komputerze z systemem Windows. Po uruchomieniu procesu aprowizacji ten plik zawiera szczegółowe informacje o wszystkich przypisanych i zainicjowanych użytkownikach i grupach.

### <a name="development-libraries"></a>Biblioteki deweloperskie

Aby opracować własną usługę sieci Web, która jest zgodna ze specyfikacją Standard scim, najpierw zapoznaj się z następującymi bibliotekami dostarczanymi przez firmę Microsoft w celu przyspieszenia procesu tworzenia:

* Biblioteki Common Language Infrastructure (CLI) są dostępne do użycia w językach opartych na tej infrastrukturze, C#takich jak. Jedna z tych bibliotek, Microsoft. SystemForCrossDomainIdentityManagement. Service, deklaruje interfejs, Microsoft. SystemForCrossDomainIdentityManagement. IProvider, pokazano na poniższej ilustracji. Deweloper korzystający z bibliotek implementuje ten interfejs z klasą, która może odwoływać się ogólnie, jako dostawcę. Biblioteki umożliwiają deweloperowi wdrożenie usługi sieci Web, która jest zgodna ze specyfikacją Standard scim. Usługa sieci Web może być hostowana w ramach Internet Information Services lub dowolnego wykonywalnego zestawu interfejsu wiersza polecenia. Żądanie jest tłumaczone na wywołania metod dostawcy, które będą zaprogramowane przez dewelopera do działania w przypadku niektórych magazynów tożsamości.
  
   ![Podział: żądanie przetłumaczone na wywołania metod dostawcy][3]
  
* [Procedury obsługi tras ekspresowych](https://expressjs.com/guide/routing.html) są dostępne do analizowania obiektów żądań Node. js reprezentujących wywołania (zgodnie z definicją Standard scim), które zostały wprowadzone do usługi sieci Web Node. js.

### <a name="building-a-custom-scim-endpoint"></a>Kompilowanie niestandardowego punktu końcowego Standard scim

Deweloperzy korzystający z bibliotek interfejsu wiersza polecenia mogą hostować swoje usługi w ramach dowolnego wykonywalnego zestawu interfejsu wiersza polecenia lub w ramach Internet Information Services. Poniżej znajduje się przykładowy kod służący do hostowania usługi w ramach zestawu wykonywalnego, pod adresem http://localhost:9000: 

   ```csharp
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
   ```

Ta usługa musi mieć adres HTTP i certyfikat uwierzytelniania serwera, którego główny urząd certyfikacji jest jedną z następujących nazw: 

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Przejdź do Daddy
* Firmy
* WoSign

Certyfikat uwierzytelniania serwera można powiązać z portem na hoście z systemem Windows za pomocą narzędzia powłoki sieciowej: 

    netsh http add sslcert ipport=0.0.0.0:443 certhash=0000000000003ed9cd0c315bbb6dc1c08da5e6 appid={00112233-4455-6677-8899-AABBCCDDEEFF}  

W tym miejscu wartość podana dla argumentu certhash jest odciskiem palca certyfikatu, podczas gdy wartość podana dla argumentu AppID jest dowolnym globalnie unikatowym identyfikatorem.  

Aby hostować usługę w Internet Information Services, programista utworzy zestaw biblioteki kodu interfejsu wiersza polecenia z klasą o nazwie Startup w domyślnej przestrzeni nazw zestawu.  Oto przykład takiej klasy: 

   ```csharp
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
   ```

### <a name="handling-endpoint-authentication"></a>Obsługa uwierzytelniania punktu końcowego

Żądania od Azure Active Directory zawierają token elementu nośnego OAuth 2,0.   Każda usługa otrzymująca żądanie powinna uwierzytelniać wystawcę jako Azure Active Directory dla oczekiwanego Azure Active Directory dzierżawy, aby uzyskać dostęp do usługi sieci Web programu Azure Active Directory Graph.  W tokenie wystawca jest identyfikowany przez zgłoszenie ISS, takie jak "ISS": "https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/".  W tym przykładzie adres podstawowy wartości "https://sts.windows.net" identyfikuje Azure Active Directory jako wystawcę, natomiast segment adresu względnego cbb1a5ac-f33b-45fa-9bf5-f37db0fed422 jest unikatowym identyfikatorem dzierżawy Azure Active Directory, dla której token został wystawiony. Odbiorcy tokenu będą IDENTYFIKATORem szablonu aplikacji dla aplikacji w galerii. Identyfikator szablonu aplikacji dla wszystkich aplikacji niestandardowych to 8adf8e6e-67b2-4cf2-A259-e3dc5476c621. Identyfikator szablonu aplikacji dla każdej aplikacji w galerii jest różny. Skontaktuj się z ProvisioningFeedback@microsoft.com w przypadku pytań dotyczących identyfikatora szablonu aplikacji dla aplikacji galerii. Każda aplikacja zarejestrowana w jednej dzierżawie może otrzymać to samo żądanie `iss` z żądaniami Standard scim.

Deweloperzy korzystający z bibliotek interfejsu wiersza polecenia dostarczonych przez firmę Microsoft do tworzenia usługi Standard scim mogą uwierzytelniać żądania z Azure Active Directory przy użyciu pakietu Microsoft. Owin. Security. ActiveDirectory, wykonując następujące czynności: 

1. W ramach dostawcy Zaimplementuj Właściwość Microsoft. SystemForCrossDomainIdentityManagement. IProvider. StartupBehavior, zwracając metodę do wywołania przy każdym uruchomieniu usługi: 

   ```csharp
     public override Action<Owin.IAppBuilder, System.Web.Http.HttpConfiguration.HttpConfiguration> StartupBehavior
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

1. Dodaj następujący kod do tej metody, aby uzyskać wszelkie żądania do dowolnego punktu końcowego usługi uwierzytelniane jako mające token wystawiony przez Azure Active Directory dla określonej dzierżawy, aby uzyskać dostęp do usługi sieci Web grafu Azure AD: 

   ```csharp
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
           ValidAudience = "8adf8e6e-67b2-4cf2-a259-e3dc5476c621"
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

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>Obsługa obsługi administracyjnej i cofanie aprowizacji użytkowników

1. Azure Active Directory wysyła zapytanie do usługi dla użytkownika z wartością atrybutu externalId zgodną z wartością atrybutu mailNickname użytkownika w usłudze Azure AD. Zapytanie jest wyrażone jako żądanie protokołu HTTP (Hypertext Transfer Protocol), takie jak ten przykład, w którym jyoung jest próbka mailNickname użytkownika w Azure Active Directory.

    >[!NOTE]
    > Jest to tylko przykład. Nie wszyscy użytkownicy będą mieć atrybut mailNickname, a wartość użytkownika nie może być unikatowa w katalogu. Ponadto atrybut używany do dopasowywania (w tym przypadku jest externalId) można skonfigurować w [mapowaniu atrybutów usługi Azure AD](customize-application-attributes.md).

   ```
    GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
    Authorization: Bearer ...
   ```
   Jeśli usługa została skompilowana przy użyciu bibliotek interfejsu wiersza polecenia dostarczonych przez firmę Microsoft do implementowania usług Standard scim, żądanie jest tłumaczone na wywołanie metody zapytania dostawcy usługi.  Oto podpis tej metody: 
   ```csharp
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]> Query(
      Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
      string correlationIdentifier);
   ```
   Oto definicja interfejsu Microsoft. SystemForCrossDomainIdentityManagement. IQueryParameters: 
   ```csharp
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

   ```
     GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
     Authorization: Bearer ...
   ```

   Jeśli usługa została skompilowana przy użyciu bibliotek Common Language Infrastructure dostarczonych przez firmę Microsoft do implementowania usług Standard scim, żądanie jest tłumaczone na wywołanie metody zapytania dostawcy usługi.  Oto podpis tej metody: 

   ```csharp
     // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
     // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
     // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  
 
     System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]>  Query(
       Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
       string correlationIdentifier);
   ```

   Oto definicja interfejsu Microsoft. SystemForCrossDomainIdentityManagement. IQueryParameters: 

   ```csharp
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

   W poniższym przykładzie zapytania dla użytkownika o podanej wartości atrybutu externalId wartości argumentów przekazanych do metody zapytania są następujące: 
   * wejściowe. AlternateFilters. Count: 1
   * wejściowe. AlternateFilters. ElementAt (0). AttributePath: "externalId"
   * wejściowe. AlternateFilters. ElementAt (0). Operatorporównania: operatorporównania. Equals
   * wejściowe. AlternateFilter. ElementAt (0). ComparisonValue: "jyoung"
   * correlationIdentifier: System .NET. http. HttpRequestMessage. GetOwinEnvironment ["Owin. Identyfikator żądania "] 

1. Jeśli odpowiedź na zapytanie do usługi sieci Web dla użytkownika z wartością atrybutu externalId, która pasuje do wartości atrybutu mailNickname użytkownika, nie zwróci żadnych użytkowników, a następnie Azure Active Directory żądania zainicjowania usługi przez użytkownika odpowiadającego temu w Azure Active Directory.  Oto przykład tego żądania: 

   ```
    POST https://.../scim/Users HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/scim+json
    {
      "schemas":
      [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0User"],
      "externalId":"jyoung",
      "userName":"jyoung",
      "active":true,
      "addresses":null,
      "displayName":"Joy Young",
      "emails": [
        {
          "type":"work",
          "value":"jyoung@Contoso.com",
          "primary":true}],
      "meta": {
        "resourceType":"User"},
       "name":{
        "familyName":"Young",
        "givenName":"Joy"},
      "phoneNumbers":null,
      "preferredLanguage":null,
      "title":null,
      "department":null,
      "manager":null}
   ```
   Biblioteki interfejsu wiersza polecenia udostępniane przez firmę Microsoft do implementowania usług Standard scim spowodują przetłumaczenie tego żądania na wywołanie metody Create dostawcy usługi.  Metoda Create ma następujący podpis: 
   ```csharp
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> Create(
      Microsoft.SystemForCrossDomainIdentityManagement.Resource resource, 
      string correlationIdentifier);
   ```
   W żądaniu do aprowizacji użytkownika wartość argumentu zasobu jest wystąpieniem Microsoft. SystemForCrossDomainIdentityManagement. Klasa Core2EnterpriseUser zdefiniowana w bibliotece Microsoft. SystemForCrossDomainIdentityManagement. Schematys.  Jeśli żądanie zainicjowania obsługi użytkownika zakończyło się pomyślnie, implementacja metody będzie zwracać wystąpienie elementu Microsoft. SystemForCrossDomainIdentityManagement. Klasa Core2EnterpriseUser, z wartością właściwości identyfikatora ustawioną na unikatowy identyfikator nowo zainicjowanego użytkownika.  

1. Aby zaktualizować użytkownika znanego w magazynie tożsamości, z którego korzysta Standard scim, Azure Active Directory kontynuować, żądając bieżącego stanu tego użytkownika od usługi przy użyciu żądania takiego jak: 
   ```
    GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
   ```
   W przypadku usługi skompilowanej przy użyciu bibliotek interfejsu wiersza polecenia dostarczonych przez firmę Microsoft do implementowania usług Standard scim żądanie jest tłumaczone na wywołanie metody pobierającej dostawcy usługi.  Oto podpis metody pobierania: 
   ```csharp
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource and 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
    // are defined in Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> 
       Retrieve(
         Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
           parameters, 
           string correlationIdentifier);

    public interface 
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters:   
        IRetrievalParameters
        {
          Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
            ResourceIdentifier 
              { get; }
    }
    public interface Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier
    {
        string Identifier 
          { get; set; }
        string Microsoft.SystemForCrossDomainIdentityManagement.SchemaIdentifier 
          { get; set; }
    }
   ```
   W przykładzie żądania pozyskania bieżącego stanu użytkownika wartości właściwości obiektu dostarczonego jako wartość argumentu Parameters są następujące w następujących przypadkach: 
  
   * Identyfikator: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * SchemaIdentifier: "urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User"

1. Jeśli atrybut odwołania ma zostać zaktualizowany, Azure Active Directory wysyła zapytanie do usługi w celu ustalenia, czy bieżąca wartość atrybutu odwołania w magazynie tożsamości, z którego pochodzi usługa, jest już zgodna z wartością tego atrybutu na platformie Azure. Katalogi. W przypadku użytkowników jedynym atrybutem, którego bieżącą wartością jest zapytanie w ten sposób, jest atrybut Manager. Oto przykład żądania, aby określić, czy atrybut Menedżera określonego obiektu użytkownika ma obecnie określoną wartość: 

   Jeśli usługa została skompilowana przy użyciu bibliotek interfejsu wiersza polecenia dostarczonych przez firmę Microsoft do implementowania usług Standard scim, żądanie jest tłumaczone na wywołanie metody zapytania dostawcy usługi. Wartość właściwości obiektu dostarczonego jako wartość argumentu Parameters są następujące: 
  
   * wejściowe. AlternateFilters. Count: 2
   * wejściowe. AlternateFilters. ElementAt (x). AttributePath: "ID"
   * wejściowe. AlternateFilters. ElementAt (x). Operatorporównania: operatorporównania. Equals
   * wejściowe. AlternateFilter. ElementAt (x). ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * wejściowe. AlternateFilters. ElementAt (y). AttributePath: "Manager"
   * wejściowe. AlternateFilters. ElementAt (y). Operatorporównania: operatorporównania. Equals
   * wejściowe. AlternateFilter. ElementAt (y). ComparisonValue: "2819c223-7f76-453A-919d-413861904646"
   * wejściowe. RequestedAttributePaths. ElementAt (0): "ID"
   * wejściowe. SchemaIdentifier: "urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User"

   W tym miejscu wartość indeksu x może być równa 0, a wartość indeksu y może wynosić 1 lub wartość x może wynosić 1, a wartość y może być równa 0, w zależności od kolejności wyrażeń parametru kwerendy filtru.   

1. Oto przykład żądania od Azure Active Directory do usługi Standard scim w celu zaktualizowania użytkownika: 
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
   Biblioteki interfejsu wiersza polecenia firmy Microsoft służące do implementowania usług Standard scim spowodują przetłumaczenie żądania na wywołanie metody Update dostawcy usługi. Oto podpis metody Update: 
   ```csharp
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
   ```

   Jeśli usługa została skompilowana przy użyciu bibliotek Common Language Infrastructure dostarczonych przez firmę Microsoft do implementowania usług Standard scim, żądanie jest tłumaczone na wywołanie metody zapytania dostawcy usługi. Wartość właściwości obiektu dostarczonego jako wartość argumentu Parameters są następujące: 
  
* wejściowe. AlternateFilters. Count: 2
* wejściowe. AlternateFilters. ElementAt (x). AttributePath: "ID"
* wejściowe. AlternateFilters. ElementAt (x). Operatorporównania: operatorporównania. Equals
* wejściowe. AlternateFilter. ElementAt (x). ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
* wejściowe. AlternateFilters. ElementAt (y). AttributePath: "Manager"
* wejściowe. AlternateFilters. ElementAt (y). Operatorporównania: operatorporównania. Equals
* wejściowe. AlternateFilter. ElementAt (y). ComparisonValue: "2819c223-7f76-453A-919d-413861904646"
* wejściowe. RequestedAttributePaths. ElementAt (0): "ID"
* wejściowe. SchemaIdentifier: "urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User"

  W tym miejscu wartość indeksu x może być równa 0, a wartość indeksu y może wynosić 1 lub wartość x może wynosić 1, a wartość y może być równa 0, w zależności od kolejności wyrażeń parametru kwerendy filtru.   

1. Oto przykład żądania od Azure Active Directory do usługi Standard scim w celu zaktualizowania użytkownika: 

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

   Biblioteki Common Language Infrastructure firmy Microsoft służące do implementowania usług Standard scim spowodują przetłumaczenie żądania na wywołanie metody Update dostawcy usługi. Oto podpis metody Update: 

   ```csharp
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

    W przykładzie żądania zaktualizowania użytkownika obiekt dostarczony jako wartość argumentu patch ma następujące wartości właściwości: 
  
   * ResourceIdentifier. Identyfikator: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * ResourceIdentifier. SchemaIdentifier: "urn: IETF: params: Standard scim: schematy: Extension: Enterprise: 2.0: User"
   * (PatchRequest jako PatchRequest2). Operations. Count: 1
   * (PatchRequest jako PatchRequest2). Operations. ElementAt (0). OperationName: OperationName. Add
   * (PatchRequest jako PatchRequest2). Operations. ElementAt (0). Path. AttributePath: "Manager"
   * (PatchRequest jako PatchRequest2). Operations. ElementAt (0). Wartość. Count: 1
   * (PatchRequest jako PatchRequest2). Operations. ElementAt (0). Value. ElementAt (0). Odwołanie: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
   * (PatchRequest jako PatchRequest2). Operations. ElementAt (0). Value. ElementAt (0). Value: 2819c223-7f76-453A-919d-413861904646

1. Aby anulować obsługę administracyjną użytkownika z magazynu tożsamości przedniego przez usługę Standard scim, usługa Azure AD wysyła żądanie, takie jak: 

   ```
     DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
     Authorization: Bearer ...
   ```

   Jeśli usługa została skompilowana przy użyciu bibliotek Common Language Infrastructure dostarczonych przez firmę Microsoft do implementowania usług Standard scim, żądanie jest tłumaczone na wywołanie metody Delete dostawcy usługi.   Ta metoda ma następującą sygnaturę: 

   ```csharp
     // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
     // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
     System.Threading.Tasks.Task Delete(
       Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
         resourceIdentifier, 
       string correlationIdentifier);
   ```

   Obiekt dostarczony jako wartość argumentu resourceIdentifier ma te wartości właściwości w przykładzie żądania cofnięcia aprowizacji użytkownikowi: 

1. Aby anulować obsługę administracyjną użytkownika z magazynu tożsamości przedniego przez usługę Standard scim, usługa Azure AD wysyła żądanie, takie jak: 
   ````
    DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
   ````
   Jeśli usługa została skompilowana przy użyciu bibliotek interfejsu wiersza polecenia dostarczonych przez firmę Microsoft do implementowania usług Standard scim, żądanie jest tłumaczone na wywołanie metody Delete dostawcy usługi.   Ta metoda ma następującą sygnaturę: 
   ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
    System.Threading.Tasks.Task Delete(
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
        resourceIdentifier, 
      string correlationIdentifier);
   ````
   Obiekt dostarczony jako wartość argumentu resourceIdentifier ma te wartości właściwości w przykładzie żądania cofnięcia aprowizacji użytkownikowi: 
  
   * ResourceIdentifier. Identyfikator: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * ResourceIdentifier. SchemaIdentifier: "urn: IETF: params: Standard scim: schematy: Extension: Enterprise: 2.0: User"

## <a name="user-and-group-schema-reference"></a>Odwołanie do schematu użytkowników i grup

Azure Active Directory można udostępnić dwa typy zasobów Standard scim usługom sieci Web.  Te typy zasobów to użytkownicy i grupy.  

Zasoby użytkownika są identyfikowane przez identyfikator schematu, `urn:ietf:params:scim:schemas:extension:enterprise:2.0:User`, który jest zawarty w specyfikacji protokołu: https://tools.ietf.org/html/rfc7643.  Domyślne mapowanie atrybutów użytkowników w Azure Active Directory do atrybutów zasobów użytkowników znajduje się w tabeli 1.  

Zasoby grupy są identyfikowane przez identyfikator schematu, `urn:ietf:params:scim:schemas:core:2.0:Group`. Tabela 2 zawiera domyślne mapowanie atrybutów grup w Azure Active Directory do atrybutów zasobów grupy.  

### <a name="table-1-default-user-attribute-mapping"></a>Tabela 1: domyślne mapowanie atrybutów użytkownika

| Azure Active Directory użytkownika | "urn: IETF: params: Standard scim: schematy: Extension: Enterprise: 2.0: User" |
| --- | --- |
| IsSoftDeleted |aktywne |
| displayName |displayName |
| Faks-numer telefonu |numer telefonu [typ EQ "Fax"]. wartość |
| GivenName |Nazwa. imię |
| Stanowiska |title |
| mail (poczta) |wiadomości e-mail [Type EQ "Work"]. Value |
| MailNickname |externalId |
| Menedżera |Menedżera |
| urządzenie |numer telefonu [typ EQ "Mobile"]. Value |
| Obiektu |ID |
| Pocztowy |addresss [Type EQ "Work"]. KodPocztowy |
| Adresy serwera proxy |wiadomości e-mail [Type EQ "Other"]. Wartościami |
| Physical-Delivery-OfficeName |adresy [Type EQ "Other"]. Poprawić |
| streetAddress |adresy [typ EQ "Work"]. streetAddress |
| surname |Nazwa. rodzina |
| Numer telefonu |numer telefonu [typ EQ "Work"]. wartość |
| User-PrincipalName |Uż |

### <a name="table-2-default-group-attribute-mapping"></a>Tabela 2: Mapowanie atrybutów grupy domyślnej

| Grupa Azure Active Directory | urn: IETF: params: Standard scim: schematy: rdzeń: 2.0: Grupa |
| --- | --- |
| displayName |externalId |
| mail (poczta) |wiadomości e-mail [Type EQ "Work"]. Value |
| MailNickname |displayName |
| Skład |Skład |
| Obiektu |ID |
| proxyAddresses |wiadomości e-mail [Type EQ "Other"]. Wartościami |

## <a name="allow-ip-addresses-used-by-the-azure-ad-provisioning-service-to-make-scim-requests"></a>Zezwalaj na adresy IP używane przez usługę Azure AD Provisioning do podejmowania żądań Standard scim

Niektóre aplikacje zezwalają na ruch przychodzący do swojej aplikacji. Aby usługa Azure AD Provisioning działała zgodnie z oczekiwaniami, używane adresy IP muszą być dozwolone. Aby uzyskać listę adresów IP dla każdego tagu usługi/regionu, zobacz plik JSON — [zakresy adresów IP platformy Azure i Tagi usług — chmura publiczna](https://www.microsoft.com/download/details.aspx?id=56519). W razie konieczności można pobrać i obsłużyć te adresy IP w zaporze. Zakresy zarezerwowanych adresów IP dla aprowizacji usługi Azure AD można znaleźć w obszarze "AzureActiveDirectoryDomainServices".

## <a name="related-articles"></a>Pokrewne artykuły:

* [Automatyzacja aprowizacji użytkowników/anulowania obsługi administracyjnej w aplikacjach SaaS](user-provisioning.md)
* [Dostosowywanie mapowań atrybutów na potrzeby aprowizacji użytkowników](customize-application-attributes.md)
* [Pisanie wyrażeń do mapowania atrybutów](functions-for-customizing-application-data.md)
* [Filtry zakresu dla aprowizacji użytkowników](define-conditional-rules-for-provisioning-user-accounts.md)
* [Powiadomienia o aprowizacji konta](user-provisioning.md)
* [Lista samouczków dotyczących integrowania aplikacji SaaS](../saas-apps/tutorial-list.md)

<!--Image references-->
[0]: ./media/use-scim-to-provision-users-and-groups/scim-figure-1.png
[1]: ./media/use-scim-to-provision-users-and-groups/scim-figure-2a.png
[2]: ./media/use-scim-to-provision-users-and-groups/scim-figure-2b.png
[3]: ./media/use-scim-to-provision-users-and-groups/scim-figure-3.png
[4]: ./media/use-scim-to-provision-users-and-groups/scim-figure-4.png
[5]: ./media/use-scim-to-provision-users-and-groups/scim-figure-5.png
