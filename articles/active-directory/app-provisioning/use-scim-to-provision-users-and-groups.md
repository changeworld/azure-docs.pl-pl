---
title: Opracowywanie punktu końcowego Standard scim na potrzeby aprowizacji użytkowników w aplikacjach z usługi Azure AD
description: System do zarządzania tożsamościami między domenami (standard scim) zapewnia standaryzację automatycznej aprowizacji użytkowników. Dowiedz się, jak opracowywać punkt końcowy Standard scim, zintegrować interfejs API Standard scim z usługą Azure Active Directory i rozpocząć automatyzację użytkowników i grup aprowizacji w aplikacjach w chmurze.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: mimart
ms.reviewer: arvinh
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9ebeb0db14a42f090a629e379d88e00867bda65
ms.sourcegitcommit: 163be411e7cd9c79da3a3b38ac3e0af48d551182
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2020
ms.locfileid: "77538179"
---
# <a name="build-a-scim-endpoint-and-configure-user-provisioning-with-azure-active-directory-azure-ad"></a>Tworzenie punktu końcowego Standard scim i Konfigurowanie aprowizacji użytkowników przy użyciu Azure Active Directory (Azure AD)

Jako deweloper aplikacji możesz użyć interfejsu API zarządzania użytkownikami (standard scim) między domenami, aby włączyć automatyczną obsługę administracyjną użytkowników i grup między aplikacją a usługą Azure AD. W tym artykule opisano sposób tworzenia punktu końcowego Standard scim i integracji z usługą Azure AD Provisioning. Specyfikacja Standard scim zawiera wspólny schemat użytkownika na potrzeby aprowizacji. W połączeniu z standardami Federacji, takimi jak SAML lub OpenID Connect Connect, standard scim zapewnia administratorom kompleksowe, oparte na standardach rozwiązanie do zarządzania dostępem.

Standard scim jest standardową definicją dwóch punktów końcowych:/Users punkt końcowy i punkt końcowy/groups. Używa typowych czasowników REST do tworzenia, aktualizowania i usuwania obiektów oraz wstępnie zdefiniowanego schematu dla wspólnych atrybutów, takich jak nazwa grupy, username, imię, nazwisko i adres e-mail. Aplikacje oferujące interfejs API REST w systemie Standard scim 2,0 mogą zmniejszyć lub wyeliminować możliwości pracy z własnym interfejsem API zarządzania użytkownikami. Na przykład każdy zgodny klient Standard scim wie, jak wykonać wpis HTTP obiektu JSON do punktu końcowego/users, aby utworzyć nowy wpis użytkownika. Zamiast niepotrzebnego nieco innego interfejsu API dla tych samych podstawowych akcji, aplikacje zgodne z standardem Standard scim mogą natychmiast korzystać z istniejących klientów, narzędzi i kodu. 

![Inicjowanie obsługi administracyjnej z usługi Azure AD do aplikacji za pomocą Standard scim](media/use-scim-to-provision-users-and-groups/scim-provisioning-overview.png)

Schemat obiektów użytkownika standardowego i interfejsy API REST do zarządzania zdefiniowane w Standard scim 2,0 (RFC [7642](https://tools.ietf.org/html/rfc7642), [7643](https://tools.ietf.org/html/rfc7643), [7644](https://tools.ietf.org/html/rfc7644)) umożliwiają dostawcom tożsamości i aplikacjom łatwą integrację ze sobą. Deweloperzy aplikacji tworzący punkt końcowy Standard scim można zintegrować z dowolnym klientem zgodnym z standard scim bez konieczności wykonywania niestandardowych zadań.

Automatyzacja aprowizacji do aplikacji wymaga skompilowania i integracji punktu końcowego Standard scim z usługą Azure AD Standard scim. Wykonaj następujące kroki, aby rozpocząć Inicjowanie obsługi administracyjnej użytkowników i grup w aplikacji. 
    
  * **[Krok 1. Projektowanie schematu użytkownika i grupy.](#step-1-design-your-user-and-group-schema)** Zidentyfikuj obiekty i atrybuty, których potrzebuje aplikacja, i określ, w jaki sposób są one mapowane na schemat użytkownika i grupy obsługiwane przez implementację usługi Azure AD Standard scim.

  * **[Krok 2: Omówienie implementacji usługi Azure AD Standard scim.](#step-2-understand-the-azure-ad-scim-implementation)** Dowiedz się, w jaki sposób jest zaimplementowany klient usługi Azure AD Standard scim i modeluje obsługę żądań i odpowiedzi protokołu Standard scim.

  * **[Krok 3: Tworzenie punktu końcowego Standard scim.](#step-3-build-a-scim-endpoint)** Aby można było zintegrować usługę Azure AD provisioner, punkt końcowy musi mieć Standard scim 2,0 — zgodny. Jako opcję można użyć bibliotek Microsoft Common Language Infrastructure (CLI) i przykładów kodu do kompilowania punktu końcowego. Te przykłady są przeznaczone wyłącznie do celów referencyjnych i testowych. Zalecamy kodowanie aplikacji produkcyjnej w celu podjęcia na nich zależności.

  * **[Krok 4. Integracja punktu końcowego Standard scim z klientem usługi Azure AD Standard scim.](#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client)** Jeśli Twoja organizacja korzysta z aplikacji innej firmy, która implementuje profil Standard scim 2,0 obsługiwanego przez usługę Azure AD, możesz od razu rozpocząć automatyzację i cofanie aprowizacji użytkowników i grup.

  * **[Krok 5. publikowanie aplikacji w galerii aplikacji usługi Azure AD.](#step-5-publish-your-application-to-the-azure-ad-application-gallery)** Ułatwiaj klientom odnajdywanie aplikacji i łatwe konfigurowanie aprowizacji. 

![Kroki związane z integracją punktu końcowego Standard scim z usługą Azure AD](media/use-scim-to-provision-users-and-groups/process.png)

## <a name="step-1-design-your-user-and-group-schema"></a>Krok 1. Projektowanie schematu użytkownika i grupy

Każda aplikacja wymaga innych atrybutów do utworzenia użytkownika lub grupy. Rozpocznij integrację, identyfikując obiekty (użytkowników, grupy) i atrybuty (nazwę, Menedżera, stanowisko, itp.) wymagane przez aplikację. Standard Standard scim definiuje schemat do zarządzania użytkownikami i grupami. Podstawowy schemat użytkownika wymaga tylko trzech atrybutów: **ID** (identyfikator zdefiniowany przez dostawcę usługi), **externalid** (identyfikator zdefiniowany przez klienta) i **meta** (metadane tylko do odczytu utrzymywane przez dostawcę usługi). Wszystkie inne atrybuty są opcjonalne. Oprócz podstawowego schematu użytkownika Standard Standard scim definiuje rozszerzenie użytkownika przedsiębiorstwa oraz model rozszerzający schemat użytkownika w celu spełnienia wymagań aplikacji. Jeśli na przykład aplikacja wymaga Menedżera użytkownika, można użyć schematu użytkownika przedsiębiorstwa do zebrania Menedżera użytkownika i podstawowego schematu, aby zebrać wiadomości e-mail użytkownika. Aby zaprojektować schemat, wykonaj następujące czynności:
  1. Lista atrybutów wymaganych przez aplikację. Pomocne może być podzielenie wymagań na atrybuty wymagane do uwierzytelniania (np. loginName i poczta e-mail), atrybuty potrzebne do zarządzania cyklem życia użytkownika (np. stan/aktywny) i inne atrybuty potrzebne do działania określonej aplikacji (np. Menedżer, tag).
  2. Sprawdź, czy te atrybuty zostały już zdefiniowane w podstawowym schemacie użytkownika lub w schemacie użytkownika przedsiębiorstwa. Jeśli wszystkie atrybuty, które są potrzebne, nie są uwzględnione w schematach użytkownika Core lub Enterprise, należy zdefiniować rozszerzenie dla schematu użytkownika, które obejmuje potrzebne atrybuty. W poniższym przykładzie dodaliśmy rozszerzenie użytkownika, aby umożliwić obsługę "tagu" na użytkowniku. Najlepiej zacząć od tylko podstawowych i korporacyjnych schematów użytkownika i rozszerzać je do dodatkowych schematów niestandardowych później.  
  3. Mapowanie atrybutów Standard scim do atrybutów użytkownika w usłudze Azure AD. Jeśli jeden z atrybutów zdefiniowanych w punkcie końcowym Standard scim nie ma wyraźnego odpowiednika w schemacie użytkownika usługi Azure AD, istnieje dobry szansa, że dane nie są przechowywane w obiekcie użytkownika we wszystkich dzierżawach. Należy rozważyć, czy ten atrybut może być opcjonalny w przypadku tworzenia użytkownika. Jeśli atrybut ma krytyczne znaczenie dla aplikacji, należy poprowadzić administratora dzierżawy, aby rozszerzał swój schemat lub użyć atrybutu rozszerzenia, jak pokazano poniżej dla właściwości "Tags".

### <a name="table-1-outline-the-attributes-that-you-need"></a>Tabela 1: Tworzenie konspektu wymaganych atrybutów 
| Krok 1. określanie atrybutów wymaganych przez aplikację| Krok 2. mapowanie wymagań aplikacji na Standard scim Standard| Krok 3. Mapowanie atrybutów Standard scim do atrybutów usługi Azure AD|
|--|--|--|
|loginName|userName|userPrincipalName|
|firstName|name.givenName|givenName|
|lastName|Nazwa. lastName|lastName|
|workMail|Wiadomości e-mail [Type EQ "Work"]. Value|Poczta|
|Menedżer|Menedżer|Menedżer|
|seryjn|urn: IETF: params: Standard scim: schematy: rozszerzenie: 2.0: CustomExtension: tag|extensionAttribute1|
|status|aktywne|isSoftDeleted (obliczona wartość nie jest przechowywana na użytkowniku)|

Schemat zdefiniowany powyżej zostałby przedstawiony przy użyciu ładunku JSON poniżej. Należy pamiętać, że oprócz atrybutów wymaganych dla aplikacji, reprezentacja JSON zawiera wymagane atrybuty "ID", "externalId" i "meta".

```json
{
     "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User",
      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
      "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User"],
     "userName":"bjensen",
     "externalId":"bjensen",
     "name":{
       "familyName":"Jensen",
       "givenName":"Barbara"
     },
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User": {
     "Manager": "123456"
   },
     "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:CustomAttribute:User": {
     "tag": "701984",
   },
   "meta": {
     "resourceType": "User",
     "created": "2010-01-23T04:56:22Z",
     "lastModified": "2011-05-13T04:42:34Z",
     "version": "W\/\"3694e05e9dff591\"",
     "location":
 "https://example.com/v2/Users/2819c223-7f76-453a-919d-413861904646"
   }
 ```

### <a name="table-2-default-user-attribute-mapping"></a>Tabela 2: domyślne mapowanie atrybutów użytkownika
Następnie można użyć poniższej tabeli, aby zrozumieć, w jaki sposób atrybuty wymagane przez aplikację mogą być mapowane na atrybut w usłudze Azure AD i w Standard scim RFC. Możesz [dostosować](customize-application-attributes.md) sposób, w jaki atrybuty są mapowane między usługą Azure AD a punktem końcowym Standard scim. Należy pamiętać, że nie musisz obsługiwać użytkowników i grup ani wszystkich atrybutów przedstawionych poniżej. Są one odwołaniem do sposobu, w jaki atrybuty w usłudze Azure AD często są zamapowane na właściwości w protokole Standard scim. 

| Użytkownika usługi Azure Active Directory | "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User" |
| --- | --- |
| IsSoftDeleted |aktywne |
|department|urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: Department|
| displayName |displayName |
|employeeId|urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: employeeNumber|
| Facsimile-TelephoneNumber |wartość phoneNumbers [typ eq "faksu"] |
| givenName |name.givenName |
| Stanowisko |title |
| mail (poczta) |wiadomości e-mail [typ eq "Praca"] .value |
| mailNickname |externalId |
| Menedżer |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: Manager |
| Telefon komórkowy |wartość phoneNumbers [eq wpisz "wyraz mobile"] |
| postalCode |.postalCode adresy [typ eq "Praca"] |
| proxy-Addresses |wiadomości e-mail [Wpisz eq "other"]. Wartość |
| physical-Delivery-OfficeName |adresy [Wpisz eq "other"]. Sformatowany |
| Adres |.streetAddress adresy [typ eq "Praca"] |
| surname |name.familyName |
| Numer telefonu |wartość phoneNumbers [typ eq "Praca"] |
| user-PrincipalName |userName |


### <a name="table-3-default-group-attribute-mapping"></a>Tabela 3: Mapowanie atrybutów grupy domyślnej

| Grupa usługi Azure Active Directory | urn:ietf:params:scim:schemas:core:2.0:Group |
| --- | --- |
| displayName |displayName |
| mail (poczta) |wiadomości e-mail [typ eq "Praca"] .value |
| mailNickname |displayName |
| elementy członkowskie |elementy członkowskie |
| Identyfikator obiektu |externalId |
| proxyAddresses |wiadomości e-mail [Wpisz eq "other"]. Wartość |

Istnieje kilka punktów końcowych zdefiniowanych w Standard scim RFC. Możesz rozpocząć pracę z punktem końcowym/User, a następnie rozwinąć je stamtąd. Punkt końcowy/schemas jest przydatny podczas korzystania z atrybutów niestandardowych lub jeśli schemat zmienia się często. Umożliwia klientowi automatyczne pobranie najbardziej aktualnego schematu. Punkt końcowy/Bulk jest szczególnie przydatny podczas obsługi grup. W poniższej tabeli opisano różne punkty końcowe zdefiniowane w standardzie Standard scim. Punkt końcowy/schemas jest przydatny podczas korzystania z atrybutów niestandardowych lub jeśli schemat zmienia się często. Umożliwia klientowi automatyczne pobieranie najbardziej aktualnych schematów. Punkt końcowy/Bulk jest szczególnie przydatny podczas obsługi grup. W poniższej tabeli opisano różne punkty końcowe zdefiniowane w standardzie Standard scim. 
 
### <a name="table-4-determine-the-endpoints-that-you-would-like-to-develop"></a>Tabela 4: Określanie punktów końcowych, które chcesz opracować
|PUNKTU końcowego|OPIS|
|--|--|
|/User|Wykonywanie operacji CRUD na obiekcie użytkownika.|
|/Group|Wykonywanie operacji CRUD na obiekcie grupy.|
|/ServiceProviderConfig|Zawiera szczegółowe informacje na temat funkcji obsługiwanego standardu Standard scim, na przykład obsługiwanych zasobów i metody uwierzytelniania.|
|/ResourceTypes|Określa metadane dotyczące poszczególnych zasobów|
|/Schemas|Zestaw atrybutów obsługiwanych przez każdego klienta i dostawcę usług może się różnić. Chociaż jeden dostawca usług może zawierać "name", "title" i "e-mail", podczas gdy inny dostawca usług używa "name", "title" i "numerówek". Punkt końcowy schematów umożliwia odnajdywanie obsługiwanych atrybutów.|
|/Bulk|Operacje zbiorcze umożliwiają wykonywanie operacji na dużej kolekcji obiektów zasobów w ramach jednej operacji (np. aktualizacji członkostwa w dużej grupie).|


## <a name="step-2-understand-the-azure-ad-scim-implementation"></a>Krok 2. Omówienie implementacji usługi Azure AD Standard scim
> [!IMPORTANT]
> Ostatnio Zaktualizowano zachowanie implementacji usługi Azure AD Standard scim w dniu 18 grudnia 2018. Aby uzyskać informacje na temat zmian, zobacz [zgodność protokołów standard scim 2,0 usługi Azure AD User Provisioning](application-provisioning-config-problem-scim-compatibility.md).

Jeśli tworzysz aplikację, która obsługuje interfejs API zarządzania użytkownikami w systemie Standard scim 2,0, w tej sekcji szczegółowo opisano sposób implementacji klienta usługi Azure AD Standard scim. Przedstawiono w nim również sposób modelowania obsługi żądań i odpowiedzi protokołu Standard scim. Po wdrożeniu punktu końcowego Standard scim można go przetestować, wykonując procedurę opisaną w poprzedniej sekcji.

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
* Odpowiedź na żądanie zapytania/filtru zawsze powinna być `ListResponse`.
* Grupy są opcjonalne, ale są obsługiwane tylko wtedy, gdy implementacja Standard scim obsługuje żądania PATCH.
* Nie trzeba uwzględniać całego zasobu w odpowiedzi na POPRAWKĘ.
* Microsoft Azure AD stosuje tylko następujące operatory:  
    - `eq`
    - `and`
* Nie wymagaj dopasowania uwzględniającego wielkość liter w elementach konstrukcyjnych w Standard scim, w szczególności wartości operacji `op` poprawki, zgodnie z definicją w https://tools.ietf.org/html/rfc7644#section-3.5.2. Usługa Azure AD emituje wartości elementu "op" jako `Add`, `Replace`i `Remove`.
* Microsoft Azure AD wykonuje żądania pobrania losowego użytkownika i grupy, aby upewnić się, że punkt końcowy i poświadczenia są prawidłowe. Jest również wykonywane w ramach przepływu **połączenia testowego** w [Azure Portal](https://portal.azure.com). 
* Atrybut, w którym można wykonywać zapytania o zasoby, powinien być ustawiony jako pasujący atrybut w aplikacji w [Azure Portal](https://portal.azure.com). Aby uzyskać więcej informacji, zobacz [Dostosowywanie mapowań atrybutów aprowizacji użytkowników](customize-application-attributes.md)

### <a name="user-provisioning-and-deprovisioning"></a>Inicjowanie obsługi i cofanie aprowizacji użytkowników

Na poniższej ilustracji przedstawiono komunikaty, które Azure Active Directory wysyła do usługi Standard scim w celu zarządzania cyklem życia użytkownika w magazynie tożsamości aplikacji.  

![przedstawia sekwencję aprowizacji i anulowania obsługi użytkowników](media/use-scim-to-provision-users-and-groups/scim-figure-4.png)<br/>
*Sekwencja inicjowania obsługi i anulowania aprowizacji użytkowników*

### <a name="group-provisioning-and-deprovisioning"></a>Inicjowanie obsługi i cofanie aprowizacji grupy

Inicjowanie obsługi i cofanie aprowizacji grupy jest opcjonalne. Po zaimplementowaniu i włączeniu na poniższej ilustracji przedstawiono komunikaty wysyłane przez usługę Azure AD do usługi Standard scim w celu zarządzania cyklem życia grupy w magazynie tożsamości aplikacji.  Komunikaty te różnią się od komunikatów o użytkownikach na dwa sposoby:

* Żądania pobrania grup określają, że atrybut Members ma być wykluczony z dowolnego zasobu dostarczonego w odpowiedzi na żądanie.  
* Żądania, aby ustalić, czy jest atrybut odwołania, ma określoną wartość są żądania o atrybucie elementów członkowskich.  

![przedstawia sekwencję aprowizacji i anulowania obsługi grup](media/use-scim-to-provision-users-and-groups/scim-figure-5.png)<br/>
*Sekwencjonowanie grup i cofanie aprowizacji*

### <a name="scim-protocol-requests-and-responses"></a>Żądania i odpowiedzi protokołu Standard scim
Ta sekcja zawiera przykładowe żądania Standard scim emitowane przez klienta usługi Azure AD Standard scim i przykładowe oczekiwane odpowiedzi. Aby uzyskać najlepsze wyniki, należy zakodować aplikację do obsługi tych żądań w tym formacie i emitować oczekiwane odpowiedzi.

> [!IMPORTANT]
> Aby dowiedzieć się, jak i kiedy usługa aprowizacji użytkowników w usłudze Azure AD emituje opisane poniżej operacje, zapoznaj się z sekcją [cykle aprowizacji: początkowe i przyrostowe](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) w [sposobie działania aprowizacji](how-provisioning-works.md).

[Operacje użytkownika](#user-operations)
  - [Utwórz użytkownika](#create-user) ([żądanie](#request) / [odpowiedzi](#response))
  - [Pobierz użytkownika](#get-user) ([żądanie](#request-1) / [odpowiedzi](#response-1))
  - [Pobierz użytkownika według zapytania](#get-user-by-query) ([żądanie](#request-2) / [odpowiedzi](#response-2))
  - [Pobierz użytkownika według zapytania — wyniki zerowe](#get-user-by-query---zero-results) ([żądanie](#request-3)
/ [odpowiedzi](#response-3))
  - [Aktualizowanie użytkownika [właściwości wielowartościowe]](#update-user-multi-valued-properties) ([żądanie](#request-4) /  [odpowiedzi](#response-4))
  - [Aktualizowanie użytkownika [właściwości pojedynczej wartości]](#update-user-single-valued-properties) ([żądanie](#request-5)
/ [odpowiedzi](#response-5)) 
  - [Wyłącz użytkownika](#disable-user) ([żądanie](#request-14) / 
[odpowiedzi](#response-14))
  - [Usuń użytkownika](#delete-user) ([żądanie](#request-6) / 
[odpowiedzi](#response-6))


[Operacje grupy](#group-operations)
  - [Utwórz grupę](#create-group) ( [żądanie](#request-7) / [odpowiedzi](#response-7))
  - [Pobierz grupę](#get-group) ( [żądanie](#request-8) / [odpowiedzi](#response-8))
  - [Pobierz grupę według DisplayName](#get-group-by-displayname) ([żądanie](#request-9) / [odpowiedzi](#response-9))
  - [Grupa aktualizacji [atrybuty inne niż Członkowskie]](#update-group-non-member-attributes) ([żądanie](#request-10) /
 [odpowiedzi](#response-10))
  - [Grupa aktualizacji [Dodaj członków]](#update-group-add-members) ( [żądanie](#request-11) /
[odpowiedzi](#response-11))
  - [Grupa aktualizacji [usuwanie członków]](#update-group-remove-members) ( [żądanie](#request-12) /
[odpowiedzi](#response-12))
  - [Usuń grupę](#delete-group) ([żądanie](#request-13) /
[odpowiedzi](#response-13))

### <a name="user-operations"></a>Operacje użytkownika

* Użytkownicy mogą wykonywać zapytania o atrybuty `userName` lub `email[type eq "work"]`.  

#### <a name="create-user"></a>Tworzenie użytkownika

###### <a name="request"></a>Żądanie

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

#### <a name="get-user"></a>Pobieranie użytkownika

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

###### <a name="request"></a>Żądanie
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

### <a name="disable-user"></a>Wyłączanie użytkownika

##### <a name="request-14"></a>Żądając

*Poprawka/users/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "Operations": [
        {
            "op": "Replace",
            "path": "active",
            "value": false
        }
    ],
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ]
}
```

##### <a name="response-14"></a>Reakcji

```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User"
    ],
    "id": "CEC50F275D83C4530A495FCF@834d0e1e5d8235f90a495fda",
    "userName": "deanruiz@testuser.com",
    "name": {
        "familyName": "Harris",
        "givenName": "Larry"
    },
    "active": false,
    "emails": [
        {
            "value": "gloversuzanne@testuser.com",
            "type": "work",
            "primary": true
        }
    ],
    "addresses": [
        {
            "country": "ML",
            "type": "work",
            "primary": true
        }
    ],
    "meta": {
        "resourceType": "Users",
        "location": "/scim/5171a35d82074e068ce2/Users/CEC50F265D83B4530B495FCF@5171a35d82074e068ce2"
    }
}
```
#### <a name="delete-user"></a>Usuń użytkownika

##### <a name="request-6"></a>Żądając

*Usuń/users/5171a35d82074e068ce2 HTTP/1.1*

##### <a name="response-6"></a>Reakcji

*HTTP/1.1 204 Brak zawartości*

### <a name="group-operations"></a>Operacje grupy

* Grupy zawsze są tworzone z pustą listą członków.
* Grupy mogą być badane przez atrybut `displayName`.
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

### <a name="security-requirements"></a>Wymagania dotyczące zabezpieczeń
**Wersje protokołu TLS**

Jedyne akceptowalne wersje protokołu TLS to TLS 1,2 i TLS 1,3. Żadna inna wersja protokołu TLS nie jest dozwolona. Nie jest dozwolona żadna wersja protokołu SSL. 
- Klucze RSA muszą mieć co najmniej 2 048 bitów.
- Klucze ECC muszą mieć co najmniej 256 bitów, generowane przy użyciu zatwierdzonej krzywej eliptycznej


**Długości kluczy**

Wszystkie usługi muszą używać certyfikatów X. 509 wygenerowanych przy użyciu kluczy kryptograficznych o wystarczającej długości, co oznacza:

**Mechanizmy szyfrowania**

Wszystkie usługi muszą być skonfigurowane tak, aby korzystały z następujących mechanizmów szyfrowania w kolejności określonej poniżej. Należy pamiętać, że jeśli masz tylko certyfikat RSA, instalacja mechanizmów szyfrowania ECDSA nie ma żadnego skutku. </br>

Minimalny pasek mechanizmów szyfrowania TLS 1,2:

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384


## <a name="step-3-build-a-scim-endpoint"></a>Krok 3. Tworzenie punktu końcowego Standard scim

Tworząc usługę sieci Web Standard scim, która jest obsługiwana przy użyciu Azure Active Directory, można włączyć automatyczne Inicjowanie obsługi użytkowników dla praktycznie dowolnej aplikacji lub magazynu tożsamości.

Poniżej przedstawiono, jak to działa:

1. Usługa Azure AD udostępnia bibliotekę Common Language Infrastructure (CLI) o nazwie Microsoft. SystemForCrossDomainIdentityManagement, dołączoną do przykładów kodu opisującą poniżej. Integratory systemów i deweloperzy mogą używać tej biblioteki do tworzenia i wdrażania punktu końcowego usługi sieci Web opartej na Standard scim, który może połączyć usługę Azure AD z magazynem tożsamości dowolnej aplikacji.
2. Mapowania są implementowane w usłudze sieci web, aby zamapować schemat standardowy użytkownika na schemat użytkownika oraz Protokół wymagany przez aplikację. 
3. Adres URL punktu końcowego jest zarejestrowany w usłudze Azure AD jako część niestandardową aplikację w galerii aplikacji.
4. Użytkownicy i grupy są przypisane do tej aplikacji w usłudze Azure AD. Po przypisaniu są one umieszczane w kolejce w celu synchronizacji z aplikacją docelową. Proces synchronizacji, obsługa kolejki jest uruchamiane co 40 minut.

### <a name="code-samples"></a>Przykłady kodu

Aby ułatwić ten proces, należy podać [przykłady kodu](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) , które tworzą punkt końcowy usługi sieci Web Standard scim i przedstawiają automatyczną obsługę administracyjną. Przykładem jest dostawca, który przechowuje plik z wierszami wartości rozdzielonych przecinkami reprezentującymi użytkowników i grupy.

**Wymagania wstępne**

* Visual Studio 2013 lub nowszy
* [Zestaw Azure SDK dla platformy .NET](https://azure.microsoft.com/downloads/)
* Windows maszyny, która obsługuje struktury programu ASP.NET 4.5 ma być używany jako punkt końcowy Standard SCIM. Ta maszyna musi być dostępna w chmurze.
* [Subskrypcja platformy Azure z wersją próbną lub licencjonowaną Azure AD — wersja Premium](https://azure.microsoft.com/services/active-directory/)

### <a name="getting-started"></a>Wprowadzenie

Najłatwiejszym sposobem realizowania Standard SCIM punktu końcowego, który może akceptować żądania alokacji z usługi Azure AD jest, aby skompilować i wdrożyć przykładowy kod, który wyprowadza aprowizowanych użytkowników do pliku wartości rozdzielanych przecinkami (CSV).

#### <a name="to-create-a-sample-scim-endpoint"></a>Aby utworzyć przykładowy punkt końcowy Standard scim

1. Pobierz pakiet przykładowego kodu w [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)
1. Rozpakuj pakiet i umieść go na komputerze Windows w lokalizacji takiej jak C:\AzureAD-BYOA-Provisioning-Samples\.
1. W tym folderze Uruchom projekt FileProvisioning\Host\FileProvisioningService.csproj w programie Visual Studio.
1. Wybierz kolejno pozycje **narzędzia** > **menedżer pakietów NuGet** > **konsola Menedżera pakietów**i wykonaj następujące polecenia dla projektu FileProvisioningService, aby rozpoznać odwołania do rozwiązania:

   ```powershell
    Update-Package -Reinstall
   ```

1. Skompiluj projekt FileProvisioningService.
1. Uruchom aplikację wiersza polecenia w systemie Windows (jako administrator), a następnie użyj polecenia **CD** , aby zmienić katalog na folder **\AzureAD-BYOA-Provisioning-Samples\FileProvisioning\Host\bin\Debug** .
1. Uruchom następujące polecenie, zastępując `<ip-address>` adresem IP lub nazwą domeny komputera z systemem Windows:

   ```
    FileSvc.exe http://<ip-address>:9000 TargetFile.csv
   ```

1. W systemie Windows w obszarze **Ustawienia systemu windows** > **sieci & Ustawienia internetowe**, wybierz opcję **Zapora systemu Windows** > **Ustawienia zaawansowane**i Utwórz **regułę ruchu przychodzącego** , która zezwala na dostęp przychodzący do portu 9000.
1. Jeśli komputer z systemem Windows znajduje się za routerem, router musi być skonfigurowany do uruchamiania translacji dostępu do sieci między portem 9000, który jest dostępny dla Internetu, a port 9000 na komputerze z systemem Windows. Ta konfiguracja jest wymagana, aby usługa Azure AD mogła uzyskiwać dostęp do tego punktu końcowego w chmurze.

#### <a name="to-register-the-sample-scim-endpoint-in-azure-ad"></a>Aby zarejestrować punkt końcowy Standard scim z przykładem w usłudze Azure AD

1. Zaloguj się do [portalu Azure Active Directory](https://aad.portal.azure.com). 
1. W okienku po lewej stronie wybierz pozycję **aplikacje dla przedsiębiorstw** . Zostanie wyświetlona lista wszystkich skonfigurowanych aplikacji, w tym aplikacji, które zostały dodane z galerii.
1. Wybierz pozycję **+ Nowa aplikacja** > **wszystkie** > **aplikacji spoza galerii**.
1. Wprowadź nazwę aplikacji, a następnie wybierz pozycję **Dodaj** , aby utworzyć obiekt aplikacji. Utworzony obiekt aplikacji jest przeznaczony do reprezentowania aplikacji docelowej, można będzie aprowizacji i implementowanie logowania jednokrotnego dla i nie tylko punkt końcowy Standard SCIM.
1. Na ekranie Zarządzanie aplikacjami wybierz opcję **Inicjowanie obsługi** w lewym panelu.
1. W menu **tryb aprowizacji** wybierz pozycję **automatycznie**.    
1. W polu **adres URL dzierżawy** wprowadź adres URL punktu końcowego Standard scim aplikacji. Przykład: https://api.contoso.com/scim/

1. Jeśli punkt końcowy Standard scim wymaga tokenu okaziciela OAuth od wystawcy innego niż usługa Azure AD, a następnie skopiuj wymagany token okaziciela OAuth do pola opcjonalnego **tokenu tajnego** . Jeśli to pole pozostanie puste, usługa Azure AD zawiera token okaziciela OAuth wystawiony przez usługę Azure AD za pomocą każdego żądania. Aplikacje korzystające z usługi Azure AD jako dostawca tożsamości mogą sprawdzić poprawność tego tokenu wystawionego przez usługę Azure AD.
1. Wybierz pozycję **Testuj połączenie** , aby uzyskać Azure Active Directory próbę nawiązania połączenia z punktem końcowym Standard scim. Jeśli próba nie powiedzie się, zostanie wyświetlony komunikat o błędzie.  

    > [!NOTE]
    > **Test connection** wysyła zapytanie do punktu końcowego Standard scim dla użytkownika, który nie istnieje, przy użyciu losowego identyfikatora GUID jako pasującej właściwości wybranej w konfiguracji usługi Azure AD. Oczekiwana prawidłowa odpowiedź to HTTP 200 OK z pustym komunikatem Standard scim ListResponse

1. Jeśli próba nawiązania połączenia z aplikacją zakończyła się pomyślnie, wybierz pozycję **Zapisz** , aby zapisać poświadczenia administratora.
1. W sekcji **mapowania** istnieją dwa możliwe do wyboru zestawy mapowań atrybutów: jeden dla obiektów użytkownika i jeden dla obiektów grupy. Wybierz każdą z nich Przejrzyj atrybuty, które są synchronizowane z usługi Azure Active Directory do swojej aplikacji. Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowywania do użytkowników i grup w aplikacji w celu wykonywania operacji aktualizacji. Wybierz pozycję **Zapisz** , aby zatwierdzić wszelkie zmiany.
1. W obszarze **Ustawienia**pole **zakres** określa, którzy użytkownicy i grupy są synchronizowane. Wybierz opcję **"Synchronizuj tylko przypisanych użytkowników i grupy** (zalecane) tylko do synchronizacji użytkowników i grup przypisanych na karcie **Użytkownicy i grupy** .
1. Po zakończeniu konfiguracji Ustaw **stan aprowizacji** na **włączone**.
1. Wybierz pozycję **Zapisz** , aby uruchomić usługę Azure AD Provisioning.
1. W przypadku synchronizacji tylko przypisanych użytkowników i grup (zalecane) należy wybrać kartę **Użytkownicy i grupy** i przypisać użytkowników lub grupy, które mają zostać zsynchronizowane.

Po rozpoczęciu pierwszego cyklu możesz wybrać opcję **dzienniki inspekcji** w lewym panelu, aby monitorować postęp, który pokazuje wszystkie akcje wykonywane przez usługę aprowizacji w aplikacji. Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](check-status-user-account-provisioning.md).

W ostatnim kroku weryfikacji próbki jest można otworzyć pliku TargetFile.csv w folderze \AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug na komputerze Windows. Po jego uruchomieniu procesu aprowizacji ten plik zawiera szczegółowe informacje o wszystkich przypisane i aprowizacji użytkowników i grup.

### <a name="development-libraries"></a>Biblioteki programistyczne

Aby opracować własne usługi sieci web, który jest zgodny ze specyfikacją Standard SCIM, najpierw zapoznać się z następującymi bibliotekami, które są obsługiwane przez firmę Microsoft, aby przyspieszyć proces programowania:

* Wspólne biblioteki Language Infrastructure (CLI) są oferowane do użytku z językami oparte na tej infrastruktury, takich jak C#. Jedna z tych bibliotek, Microsoft. SystemForCrossDomainIdentityManagement. Service, deklaruje interfejs, Microsoft. SystemForCrossDomainIdentityManagement. IProvider, pokazano na poniższej ilustracji. Deweloper korzystający z bibliotek implementuje ten interfejs z klasą, która może odwoływać się ogólnie, jako dostawcę. Biblioteki umożliwiają deweloperowi wdrożenie usługi sieci Web, która jest zgodna ze specyfikacją Standard scim. Usługa sieci Web może być hostowana w ramach Internet Information Services lub dowolnego wykonywalnego zestawu interfejsu wiersza polecenia. Żądanie jest tłumaczony na wywołania metody dostawcy, które będzie zaprogramowane przez projektanta do pracy na niektórych magazynu tożsamości.
  
   ![Podział: żądanie przetłumaczone na wywołania metod dostawcy](media/use-scim-to-provision-users-and-groups/scim-figure-3.png)
  
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

```
netsh http add sslcert ipport=0.0.0.0:443 certhash=0000000000003ed9cd0c315bbb6dc1c08da5e6 appid={00112233-4455-6677-8899-AABBCCDDEEFF}
```

W tym miejscu wartość podana dla argumentu certhash jest odcisk palca certyfikatu, gdy wartość podana dla appid argument jest umownym identyfikatorem globalnie unikatowa.  

Aby hostować usługę w Internet Information Services, programista utworzy zestaw biblioteki kodu interfejsu wiersza polecenia z klasą o nazwie Startup w domyślnej przestrzeni nazw zestawu.  Poniżej przedstawiono przykład takiego klasy: 

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

### <a name="handling-endpoint-authentication"></a>Obsługa punktu końcowego uwierzytelniania

Żądania z usługi Azure Active Directory zawierać tokenu elementu nośnego OAuth 2.0.   Każda usługa otrzymująca żądanie powinna uwierzytelniać wystawcę jako Azure Active Directory dla oczekiwanego Azure Active Directory dzierżawy, aby uzyskać dostęp do usługi interfejsu API Microsoft Graph.  W tokenie wystawca jest identyfikowany przez zgłoszenie ISS, takie jak "ISS": "https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/".  W tym przykładzie adres podstawowy wartości "https://sts.windows.net" identyfikuje Azure Active Directory jako wystawcę, natomiast segment adresu względnego cbb1a5ac-f33b-45fa-9bf5-f37db0fed422 jest unikatowym identyfikatorem dzierżawy Azure Active Directory, dla której token został wystawiony. Odbiorcy tokenu będą IDENTYFIKATORem szablonu aplikacji dla aplikacji w galerii. Identyfikator szablonu aplikacji dla wszystkich aplikacji niestandardowych to 8adf8e6e-67b2-4cf2-A259-e3dc5476c621. Identyfikator szablonu aplikacji dla każdej aplikacji w galerii jest różny. Skontaktuj się z firmą ProvisioningFeedback@microsoft.com, aby uzyskać pytania dotyczące identyfikatora szablonu aplikacji dla aplikacji galerii. Każda aplikacja zarejestrowana w jednej dzierżawie może otrzymać takie samo `iss`e żądanie z żądaniami Standard scim.

Deweloperzy korzystający z bibliotek interfejsu wiersza polecenia dostarczonych przez firmę Microsoft do tworzenia usługi Standard scim mogą uwierzytelniać żądania z Azure Active Directory przy użyciu pakietu Microsoft. Owin. Security. ActiveDirectory, wykonując następujące czynności: 

Najpierw w dostawcy Zaimplementuj Właściwość Microsoft. SystemForCrossDomainIdentityManagement. IProvider. StartupBehavior, zwracając metodę, która ma być wywoływana za każdym razem, gdy usługa jest uruchomiona: 

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

Następnie Dodaj następujący kod do tej metody, aby żądanie do dowolnego punktu końcowego usługi były uwierzytelniane jako mające token wystawiony przez Azure Active Directory dla określonej dzierżawy, w celu uzyskania dostępu do usługi interfejsu API Microsoft Graph: 

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

***Przykład 1. Wysyłanie zapytań do usługi pod kątem pasującego użytkownika***

Usługa Azure Active Directory kwerendę usługi dla użytkownika z wartością atrybutu externalId dopasowania wartość atrybutu mailNickname użytkownika w usłudze Azure AD. Zapytanie jest wyrażone jako żądanie protokołu HTTP (Hypertext Transfer Protocol), takie jak ten przykład, w którym jyoung jest próbka mailNickname użytkownika w Azure Active Directory.

>[!NOTE]
> Jest to tylko przykład. Nie wszyscy użytkownicy będą mieć atrybut mailNickname, a wartość użytkownika nie może być unikatowa w katalogu. Ponadto atrybut używany do dopasowywania (w tym przypadku jest externalId) można skonfigurować w [mapowaniu atrybutów usługi Azure AD](customize-application-attributes.md).

```
GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
 Authorization: Bearer ...
```

Jeśli usługa została skompilowana przy użyciu bibliotek interfejsu wiersza polecenia dostarczonych przez firmę Microsoft do implementowania usług Standard scim, żądanie jest tłumaczone na wywołanie metody zapytania dostawcy usługi.  Podpis tej metody jest następujący: 

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

Poniżej przedstawiono definicję interfejsu Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters: 

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

Jeśli usługa została skompilowana przy użyciu bibliotek Common Language Infrastructure obsługiwane przez firmę Microsoft do implementowania usługi Standard SCIM, żądanie jest tłumaczony na wywołanie do metody zapytania dostawcy usług.  Podpis tej metody jest następujący: 

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

Poniżej przedstawiono definicję interfejsu Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters: 

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

W poniższym przykładzie zapytanie dla użytkownika z danej wartości dla atrybutu externalId wartości Argumenty przekazane do metody zapytania są: 
* parameters.AlternateFilters.Count: 1
* Parametry. AlternateFilters.ElementAt(0). AttributePath: "externalId"
* parameters.AlternateFilters.ElementAt(0).ComparisonOperator: ComparisonOperator.Equals
* Parametry. AlternateFilter.ElementAt(0). ComparisonValue: "jyoung"
* correlationIdentifier: System.Net.Http.HttpRequestMessage.GetOwinEnvironment["owin.RequestId"] 

***Przykład 2. Inicjowanie obsługi administracyjnej użytkownika***

Jeśli odpowiedź na zapytanie do usługi sieci Web dla użytkownika z wartością atrybutu externalId, która pasuje do wartości atrybutu mailNickname użytkownika, nie zwróci żadnych użytkowników, a następnie Azure Active Directory żądania zainicjowania usługi przez użytkownika odpowiadającego temu w Azure Active Directory.  Poniżej przedstawiono przykład takiego żądania: 

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

Biblioteki interfejsu wiersza polecenia udostępniane przez firmę Microsoft do implementowania usług Standard scim spowodują przetłumaczenie tego żądania na wywołanie metody Create dostawcy usługi.  Metoda tworzenia ma podpis:

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
 // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  

 System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> Create(
   Microsoft.SystemForCrossDomainIdentityManagement.Resource resource, 
   string correlationIdentifier);
```

W żądaniu na aprowizację użytkownika wartość argumentu zasobów jest wystąpieniem Microsoft.SystemForCrossDomainIdentityManagement. Klasa Core2EnterpriseUser zdefiniowane w bibliotece Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  Jeśli żądanie obsługi administracyjnej użytkownika zakończy się powodzeniem, następnie implementacji metody powinien zwrócić wystąpienia Microsoft.SystemForCrossDomainIdentityManagement. Klasa Core2EnterpriseUser z wartością identyfikatora ustawioną na unikatowy identyfikator użytkownika, które użytkownicy nowo aprowizowanych.  

***Przykład 3. Zbadaj bieżący stan użytkownika*** 

Do zaktualizowania użytkownika istnieje w magazynie tożsamości przez standard SCIM, usługi Azure Active Directory przechodzi przez zażądanie bieżący stan tego użytkownika z usługi z żądaniem, takie jak: 

```
 GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
 Authorization: Bearer ...
```

W przypadku usługi skompilowanej przy użyciu bibliotek interfejsu wiersza polecenia dostarczonych przez firmę Microsoft do implementowania usług Standard scim żądanie jest tłumaczone na wywołanie metody pobierającej dostawcy usługi.  Poniżej znajduje się sygnatura metody pobierania:

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

W przykładzie żądanie, aby pobrać bieżący stan użytkownika wartości właściwości obiektu, podana jako wartość argumentu parametry są następujące: 
  
* Identyfikator: "54D382A4-2050-4C03-94D1-E769F1D15682"
* SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"

***Przykład 4. Zbadaj wartość atrybutu odwołania do zaktualizowania*** 

Jeśli atrybut odwołania ma zostać zaktualizowany, Azure Active Directory wysyła zapytanie do usługi w celu ustalenia, czy bieżąca wartość atrybutu odwołania w magazynie tożsamości, z którego pochodzi usługa, jest już zgodna z wartością tego atrybutu na platformie Azure. Katalogi. Dla użytkowników to jedyny atrybut, którego bieżącą wartość jest wysyłane zapytanie w ten sposób jest to atrybut menedżera. Oto przykładowe żądanie, aby określić, czy atrybut menedżera obiektu dany użytkownik ma obecnie określonej wartości: 

Jeśli usługa została skompilowana przy użyciu bibliotek interfejsu wiersza polecenia dostarczonych przez firmę Microsoft do implementowania usług Standard scim, żądanie jest tłumaczone na wywołanie metody zapytania dostawcy usługi. Wartość właściwości obiektu, podana jako wartość argumentu parametry są następujące: 
  
* parameters.AlternateFilters.Count: 2
* Parametry. AlternateFilters.ElementAt(x). AttributePath: "ID"
* parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
* wejściowe. AlternateFilter. ElementAt (x). ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
* parameters.AlternateFilters.ElementAt(y).AttributePath: "manager"
* parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
* wejściowe. AlternateFilter. ElementAt (y). ComparisonValue: "2819c223-7f76-453A-919d-413861904646"
* Parametry. RequestedAttributePaths.ElementAt(0): "ID"
* Parametry. SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"

W tym miejscu wartość indeksu x może być równa 0, a wartość indeksu y może wynosić 1 lub wartość x może wynosić 1, a wartość y może być równa 0, w zależności od kolejności wyrażeń parametru kwerendy filtru.   

***Przykład 5. Zażądaj od usługi Azure AD, aby zaktualizować użytkownika*** 

Oto przykład żądania z usługi Azure Active Directory z usługą Standard SCIM do zaktualizowania użytkownika: 

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

W przykładzie żądania do zaktualizowania użytkownika obiektu podana jako wartość argumentu poprawki ma wartości tych właściwości: 
  
* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"
* (PatchRequest jako PatchRequest2). Operations.Count: 1
* (PatchRequest jako PatchRequest2). Operations.ElementAt(0). OperationName: OperationName.Add
* (PatchRequest jako PatchRequest2). Operations.ElementAt(0). Path.AttributePath: "manager"
* (PatchRequest jako PatchRequest2). Operations.ElementAt(0). Value.Count: 1
* (PatchRequest jako PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Odwołanie: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
* (PatchRequest jako PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Wartość: 2819c223-7f76-453a-919d-413861904646

***Przykład 6. Anulowanie aprowizacji użytkownika***

Aby anulować obsługę administracyjną użytkownika z magazynu tożsamości przedniego przez usługę Standard scim, usługa Azure AD wysyła żądanie, takie jak:

```
  DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
  Authorization: Bearer ...
```

Jeśli usługa została skompilowana przy użyciu bibliotek Common Language Infrastructure obsługiwane przez firmę Microsoft do implementowania usługi Standard SCIM, żądanie jest tłumaczona na wywołanie metody usuwania, które dostawcy usług.   Ta metoda ma podpis: 

```csharp
  // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
  // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
  // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
  System.Threading.Tasks.Task Delete(
    Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
      resourceIdentifier, 
    string correlationIdentifier);
```

Obiekt dostarczony jako wartość argumentu resourceIdentifier ma te wartości właściwości w przykładzie żądania anulowania aprowizacji użytkownika: 

* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client"></a>Krok 4. Integracja punktu końcowego Standard scim z klientem usługi Azure AD Standard scim

Usługę Azure AD można skonfigurować tak, aby automatycznie udostępniać przypisanym użytkownikom i grupom aplikacje implementujące konkretny profil [protokołu standard scim 2,0](https://tools.ietf.org/html/rfc7644). Szczegóły profilu są udokumentowane w [kroku 2: Opis implementacji usługi Azure AD Standard scim](#step-2-understand-the-azure-ad-scim-implementation).

Skontaktuj się z dostawcą aplikacji lub dokumentacji dostawcy aplikacji dla instrukcji zgodność z tymi wymaganiami.

> [!IMPORTANT]
> Implementacja usługi Azure AD Standard scim jest oparta na usłudze Azure AD User Provisioning, która została zaprojektowana w celu ciągłego utrzymywania synchronizacji użytkowników między usługą Azure AD a aplikacją docelową i implementuje bardzo konkretny zestaw standardowych operacji. Ważne jest, aby zrozumieć te zachowania, aby zrozumieć zachowanie klienta usługi Azure AD Standard scim. Aby uzyskać więcej informacji, zapoznaj się z sekcją [cykle aprowizacji: początkowe i przyrostowe](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) w [sposobie działania aprowizacji](how-provisioning-works.md).

### <a name="getting-started"></a>Wprowadzenie

Aplikacje, które obsługują profile Standard SCIM opisane w tym artykule może być połączona z usługą Azure Active Directory przy użyciu funkcji "aplikacji spoza galerii" w galerii aplikacji usługi Azure AD. Po nawiązaniu połączenia usługi Azure AD działa proces synchronizacji co 40 minut, gdzie go zapytania aplikacji Standard SCIM z punktu końcowego dla przypisanych użytkowników i grup i tworzy lub modyfikuje je zgodnie ze szczegółami przypisania.

**Aby połączyć aplikację, która obsługuje standard scim:**

1. Zaloguj się do [portalu Azure Active Directory](https://aad.portal.azure.com). Należy pamiętać, że możesz uzyskać dostęp do bezpłatnej wersji próbnej Azure Active Directory z licencjami P2, rejestrując się w [programie dla deweloperów](https://developer.microsoft.com/office/dev-program)
2. W okienku po lewej stronie wybierz pozycję **aplikacje dla przedsiębiorstw** . Zostanie wyświetlona lista wszystkich skonfigurowanych aplikacji, w tym aplikacji, które zostały dodane z galerii.
3. Wybierz pozycję **+ Nowa aplikacja** > **wszystkie** > **aplikacji spoza galerii**.
4. Wprowadź nazwę aplikacji, a następnie wybierz pozycję **Dodaj** , aby utworzyć obiekt aplikacji. Nowa aplikacja zostanie dodana do listy aplikacji dla przedsiębiorstw i zostanie otwarta na swoim ekranie zarządzania aplikacjami.

   Zrzut ekranu ![przedstawia galerię aplikacji usługi Azure AD](media/use-scim-to-provision-users-and-groups/scim-figure-2a.png)<br/>
   *Galeria aplikacji usługi Azure AD*

5. Na ekranie Zarządzanie aplikacjami wybierz opcję **Inicjowanie obsługi** w lewym panelu.
6. W menu **tryb aprowizacji** wybierz pozycję **automatycznie**.

   ![przykład: Strona aprowizacji aplikacji w Azure Portal](media/use-scim-to-provision-users-and-groups/scim-figure-2b.png)<br/>
   *Konfigurowanie aprowizacji w Azure Portal*

7. W polu **adres URL dzierżawy** wprowadź adres URL punktu końcowego Standard scim aplikacji. Przykład: https://api.contoso.com/scim/
8. Jeśli punkt końcowy Standard scim wymaga tokenu okaziciela OAuth od wystawcy innego niż usługa Azure AD, a następnie skopiuj wymagany token okaziciela OAuth do pola opcjonalnego **tokenu tajnego** . Jeśli to pole pozostanie puste, usługa Azure AD zawiera token okaziciela OAuth wystawiony przez usługę Azure AD za pomocą każdego żądania. Aplikacje korzystające z usługi Azure AD jako dostawca tożsamości mogą sprawdzić poprawność tego tokenu wystawionego przez usługę Azure AD. 
   > [!NOTE]
   > ***Nie*** zaleca się pozostawienia tego pola pustego i polegania na tokenie wygenerowanym przez usługę Azure AD. Ta opcja jest dostępna głównie do celów testowych.
9. Wybierz pozycję **Testuj połączenie** , aby uzyskać Azure Active Directory próbę nawiązania połączenia z punktem końcowym Standard scim. Jeśli próba nie powiedzie się, zostanie wyświetlony komunikat o błędzie.  

    > [!NOTE]
    > **Test connection** wysyła zapytanie do punktu końcowego Standard scim dla użytkownika, który nie istnieje, przy użyciu losowego identyfikatora GUID jako pasującej właściwości wybranej w konfiguracji usługi Azure AD. Oczekiwana prawidłowa odpowiedź to HTTP 200 OK z pustym komunikatem Standard scim ListResponse.

10. Jeśli próba nawiązania połączenia z aplikacją zakończyła się pomyślnie, wybierz pozycję **Zapisz** , aby zapisać poświadczenia administratora.
11. W sekcji **mapowania** istnieją dwa możliwe do wyboru zestawy [mapowań atrybutów](customize-application-attributes.md): jeden dla obiektów użytkownika i jeden dla obiektów grupy. Wybierz każdą z nich Przejrzyj atrybuty, które są synchronizowane z usługi Azure Active Directory do swojej aplikacji. Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowywania do użytkowników i grup w aplikacji w celu wykonywania operacji aktualizacji. Wybierz pozycję **Zapisz** , aby zatwierdzić wszelkie zmiany.

    > [!NOTE]
    > Opcjonalnie można wyłączyć synchronizację grupy obiektów, wyłączając mapowania "groups".

12. W obszarze **Ustawienia**pole **zakres** określa, którzy użytkownicy i grupy są synchronizowane. Wybierz opcję **Synchronizuj tylko przypisani Użytkownicy i grupy** (zalecane) tylko do synchronizacji użytkowników i grup przypisanych na karcie **Użytkownicy i grupy** .
13. Po zakończeniu konfiguracji Ustaw **stan aprowizacji** na **włączone**.
14. Wybierz pozycję **Zapisz** , aby uruchomić usługę Azure AD Provisioning.
15. W przypadku synchronizacji tylko przypisanych użytkowników i grup (zalecane) należy wybrać kartę **Użytkownicy i grupy** i przypisać użytkowników lub grupy, które mają zostać zsynchronizowane.

Po rozpoczęciu pierwszego cyklu możesz wybrać opcję **dzienniki aprowizacji** w lewym panelu, aby monitorować postęp, który pokazuje wszystkie akcje wykonywane przez usługę aprowizacji w aplikacji. Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](check-status-user-account-provisioning.md).

> [!NOTE]
> Cykl początkowy trwa dłużej niż w przypadku późniejszej synchronizacji, który występuje około co 40 minut, o ile usługa jest uruchomiona.

## <a name="step-5-publish-your-application-to-the-azure-ad-application-gallery"></a>Krok 5. publikowanie aplikacji w galerii aplikacji usługi Azure AD

Jeśli tworzysz aplikację, która będzie używana przez więcej niż jedną dzierżawę, możesz ją udostępnić w galerii aplikacji usługi Azure AD. Dzięki temu organizacje mogą łatwo odnaleźć aplikację i skonfigurować obsługę administracyjną. Publikowanie aplikacji w galerii usługi Azure AD i udostępnianie jej innym osobom jest proste. Zapoznaj się z opisanymi [tutaj](../develop/howto-app-gallery-listing.md)instrukcjami. Firma Microsoft będzie współpracować z klientem w celu zintegrowania aplikacji z naszą galerią, przetestowania punktu końcowego i udostępnienia [dokumentacji](../saas-apps/tutorial-list.md) do dołączania do klientów. 

### <a name="gallery-onboarding-checklist"></a>Lista kontrolna dołączania galerii
Postępuj zgodnie z poniższą listą kontrolną, aby upewnić się, że aplikacja została podłączona, a klienci mają płynne środowisko wdrażania. Informacje zostaną zebrane z użytkownika podczas dołączania do galerii. 
> [!div class="checklist"]
> * Obsługa punktu końcowego użytkownika i grupy [standard scim 2,0](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#step-2-understand-the-azure-ad-scim-implementation) (tylko jeden jest wymagany, ale oba są zalecane)
> * Obsługa co najmniej 25 żądań na sekundę na dzierżawcę (wymagane)
> * Obsługa przydzielenia kodu autoryzacji OAuth lub tokenu długotrwałego, zgodnie z poniższym opisem (wymagane)
> * Ustanów inżynierów inżynieryjnych i pomocy technicznej w celu obsługi klientów po dołączeniu do galerii (wymagane)
> * Obsługa aktualizowania wielu członkostw w grupie za pomocą pojedynczej poprawki (zalecane) 
> * Dokumentowanie punktu końcowego Standard scim publicznie (zalecane) 
> * [Obsługa odnajdywania schematu](https://tools.ietf.org/html/rfc7643#section-6) (zalecane)


### <a name="authorization-for-provisioning-connectors-in-the-application-gallery"></a>Autoryzacja łączników aprowizacji w galerii aplikacji
Specyfikacja Standard scim nie definiuje schematu specyficznego dla Standard scim na potrzeby uwierzytelniania i autoryzacji. Opiera się na korzystaniu z istniejących standardów branżowych. Klient aprowizacji usługi Azure AD obsługuje dwie metody autoryzacji dla aplikacji w galerii. 

|Metoda autoryzacji|Specjaliści|Wady|Pomoc techniczna|
|--|--|--|--|
|Nazwa użytkownika i hasło (niezalecane lub obsługiwane przez usługę Azure AD)|Łatwa implementacja|Niezabezpieczone — [Twoje PA $ $Word nie ma znaczenia](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)|Obsługiwane w przypadku aplikacji galerii w przypadku wielkości liter. Nieobsługiwane w przypadku aplikacji innych niż Galeria.|
|Token okaziciela o długim czasie trwania (obsługiwany przez usługę Azure AD)|Tokeny długotrwałe nie wymagają obecności użytkownika. Administratorzy mogą łatwo używać podczas konfigurowania aprowizacji.|Tokeny długotrwałe mogą być trudne do udostępnienia administratorowi bez użycia niezabezpieczonych metod, takich jak poczta e-mail. |Obsługiwane w przypadku aplikacji Galeria i innych niż Galeria. |
|Przyznawanie kodu autoryzacji OAuth (obsługiwane przez usługę Azure AD)|Tokeny dostępu są znacznie krótsze niż hasła i mają mechanizm zautomatyzowanego odświeżania, który nie ma tokenów okaziciela o długim czasie trwania.  Rzeczywisty użytkownik musi być obecny podczas wstępnej autoryzacji, co umożliwia dodanie poziomu odpowiedzialności. |Wymaga, aby użytkownik był obecny. Jeśli użytkownik opuści organizację, token jest nieprawidłowy, a autoryzacja będzie musiała zostać ukończona ponownie.|Obsługiwane w przypadku aplikacji w galerii. Obsługa aplikacji innych niż Galeria jest w toku.|
|Przyznawanie poświadczeń klienta OAuth (nieobsługiwane w naszym planie)|Tokeny dostępu są znacznie krótsze niż hasła i mają mechanizm zautomatyzowanego odświeżania, który nie ma tokenów okaziciela o długim czasie trwania. Zarówno kod autoryzacji przydzielenia, jak i poświadczenia klienta umożliwiają tworzenie tego samego typu tokenu dostępu, więc przechodzenie między tymi metodami jest niewidoczne dla interfejsu API.  Inicjowanie obsługi może być całkowicie zautomatyzowane i nowe tokeny mogą być wymagane w trybie dyskretnym bez interakcji z użytkownikiem. ||Nieobsługiwane w przypadku aplikacji Galeria i innych niż Galeria. Pomoc techniczna znajduje się w naszej zaległości.|

**Przepływ przyznania kodu autoryzacji OAuth:** Usługa aprowizacji obsługuje [przyznawanie kodu autoryzacji](https://tools.ietf.org/html/rfc6749#page-24). Po przesłaniu żądania opublikowania aplikacji w galerii nasz zespół będzie mógł zebrać następujące informacje:
*  Adres URL autoryzacji: adres URL klienta, aby uzyskać autoryzację od właściciela zasobu za pośrednictwem przekierowania agenta użytkownika. Użytkownik zostanie przekierowany do tego adresu URL, aby autoryzować dostęp. 
*  Adres URL wymiany tokenów: adres URL klienta programu, który umożliwia wymianę autoryzacji dla tokenu dostępu, zazwyczaj z uwierzytelnianiem klienta.
*  Identyfikator klienta: serwer autoryzacji wystawia zarejestrowany klientowi identyfikator klienta, który jest unikatowym ciągiem reprezentującym Informacje rejestracyjne dostarczone przez klienta.  Identyfikator klienta nie jest wpisem tajnym; jest on narażony na właściciela zasobu i **nie może** być używany do uwierzytelniania klientów.  
*  Wpis tajny klienta: klucz tajny klienta jest kluczem tajnym wygenerowanym przez serwer autoryzacji. Powinna to być unikatowa wartość znana tylko serwerowi autoryzacji. 

Należy zauważyć, że uwierzytelnianie OAuth V1 nie jest obsługiwane ze względu na narażenie klucza tajnego klienta. Uwierzytelnianie OAuth v2 jest obsługiwane.  

Najlepsze rozwiązania (zalecane, ale nie wymagane):
* Obsługa wielu adresów URL przekierowań. Administratorzy mogą skonfigurować obsługę administracyjną zarówno z "portal.azure.com", jak i "aad.portal.azure.com". Obsługa wielu adresów URL przekierowania gwarantuje, że użytkownicy będą mogli autoryzować dostęp z dowolnego portalu.
* Obsługa wielu wpisów tajnych w celu zapewnienia bezproblemowego odnawiania tajnego. 

**Tokeny okaziciela OAuth o długim czasie trwania:** Jeśli aplikacja nie obsługuje przepływu przydzielenia kodu autoryzacji OAuth, można również wygenerować token okaziciela OAuth o długim czasie trwania, który może być używany przez administratora do skonfigurowania integracji aprowizacji. Token powinien być tymczasowy lub w przeciwnym razie zadanie aprowizacji zostanie poddane [kwarantannie](application-provisioning-quarantine-status.md) po wygaśnięciu tokenu. Token musi być poniżej rozmiarze 1 KB w rozmiarze.  

Aby uzyskać dodatkowe metody uwierzytelniania i autoryzacji, daj nam znać w witrynie [UserVoice](https://aka.ms/appprovisioningfeaturerequest).

### <a name="gallery-go-to-market-launch-check-list"></a>Lista kontrolna uruchamiania z galerii przejdź do rynku
Aby zwiększyć świadomość i zapotrzebowanie naszej integracji, Zalecamy zaktualizowanie istniejącej dokumentacji i wzmocnienie integracji w kanałach marketingowych.  Poniżej znajduje się zestaw działań list kontrolnych, zalecamy zakończenie obsługi uruchamiania

* **Gotowość do obsługi sprzedaży i klienta.** Upewnij się, że zespoły sprzedaży i pomocy technicznej są świadome i mogą mówić do możliwości integracji. W skrócie zespół ds. sprzedaży i pomocy technicznej, Zapewnij im często zadawane pytania i Włącz integrację z materiałami sprzedaży. 
* **Wpis w blogu i/lub naciśnięcie klawisza.** Umieść wpis w blogu lub wersję próbną opisującą integrację, zalety i sposób rozpoczynania pracy. [Przykład: Niewprowadzenie i Azure Active Directory Press](https://www.imprivata.com/company/press/imprivata-introduces-iam-cloud-platform-healthcare-supported-microsoft) 
* **Media społecznościowe.** Skorzystaj z mediów społecznościowych, takich jak Twitter, Facebook lub LinkedIn, aby promować integrację z klientami. Upewnij się, że dołączasz @AzureAD, aby umożliwić nam retweet Twojego wpisu. [Przykład: Niemniej wpis w serwisie Twitter](https://twitter.com/azuread/status/1123964502909779968)
* **Witryna sieci Web Marketing.** Utwórz lub zaktualizuj strony marketingowe (np. Strona integracji, Strona partnera, Cennik itp.) w celu uwzględnienia dostępności wspólnej integracji. [Przykład: Strona integracji Pingboard](https://pingboard.com/org-chart-for), [Strona integracji narzędzia Smartsheet](https://www.smartsheet.com/marketplace/apps/microsoft-azure-ad), [Strona z cennikiem Monday.com](https://monday.com/pricing/) 
* **Dokumentacja techniczna.** Utwórz artykuł centrum pomocy lub dokumentację techniczną dotyczącą sposobu, w jaki klienci mogą rozpocząć pracę. [Przykład: wysłannika + Microsoft Azure Active Directory integrację.](https://envoy.help/en/articles/3453335-microsoft-azure-active-directory-integration/
) 
* **Komunikacja z klientem.** Powiadamiaj klientów o nowej integracji za pośrednictwem komunikacji z klientem (Biuletyny Miesięczne, kampanie e-mail i informacje o wersji produktu). 

### <a name="allow-ip-addresses-used-by-the-azure-ad-provisioning-service-to-make-scim-requests"></a>Zezwalaj na adresy IP używane przez usługę Azure AD Provisioning do podejmowania żądań Standard scim

Niektóre aplikacje zezwalają na ruch przychodzący do swojej aplikacji. Aby usługa Azure AD Provisioning działała zgodnie z oczekiwaniami, używane adresy IP muszą być dozwolone. Aby uzyskać listę adresów IP dla każdego tagu usługi/regionu, zobacz plik JSON — [zakresy adresów IP platformy Azure i Tagi usług — chmura publiczna](https://www.microsoft.com/download/details.aspx?id=56519). W razie konieczności można pobrać i obsłużyć te adresy IP w zaporze. Zakresy zarezerwowanych adresów IP dla aprowizacji usługi Azure AD można znaleźć w obszarze "AzureActiveDirectoryDomainServices".

## <a name="related-articles"></a>Pokrewne artykuły

* [Automatyzowanie aprowizacji użytkowników i anulowanie obsługi aplikacji SaaS](user-provisioning.md)
* [Dostosuj mapowania atrybutów na potrzeby aprowizacji użytkowników](customize-application-attributes.md)
* [Pisanie wyrażeń do mapowania atrybutów](functions-for-customizing-application-data.md)
* [Filtry zakresu dla aprowizacji użytkowników](define-conditional-rules-for-provisioning-user-accounts.md)
* [Powiadomienia o aprowizacji konta](user-provisioning.md)
* [Lista samouczków dotyczących integrowania aplikacji SaaS](../saas-apps/tutorial-list.md)
