---
title: Dokumentacja modelu danych szablonu API Management platformy Azure | Microsoft Docs
description: Dowiedz się więcej o jednostkach i typach reprezentacji dla typowych elementów używanych w modelach danych dla szablonów portalu dla deweloperów w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: b0ad7e15-9519-4517-bb73-32e593ed6380
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 5625ff7e4fc51b9b6b894698719247902a480f44
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176539"
---
# <a name="azure-api-management-template-data-model-reference"></a>Dokumentacja modelu danych szablonu API Management platformy Azure
W tym temacie opisano reprezentacje jednostek i typów dla typowych elementów używanych w modelach danych dla szablonów portalu dla deweloperów w usłudze Azure API Management.  
  
 Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [How to dostosowywanie portalu deweloperów API Management przy użyciu szablonów](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="reference"></a>Informacje ogólne

-   [Interfejs API](#API)  
-   [Podsumowanie interfejsu API](#APISummary)  
-   [Aplikacja](#Application)  
-   [Umieszczenie](#Attachment)  
-   [Przykładowy kod](#Sample)  
-   [Komentować](#Comment)  
-   [Identyfikatorów](#Filtering)  
-   [Nagłówek](#Header)  
-   [Żądanie HTTP](#HTTPRequest)  
-   [Odpowiedź HTTP](#HTTPResponse)  
-   [Wykonaj](#Issue)  
-   [Operacja](#Operation)  
-   [Menu operacje](#Menu)  
-   [Element menu operacji](#MenuItem)  
-   [Stronicowania](#Paging)  
-   [Konstruktora](#Parameter)  
-   [Iloczyn](#Product)  
-   [Dostawca](#Provider)  
-   [Reprezentowana](#Representation)  
-   [Subskrypcja](#Subscription)  
-   [Podsumowanie subskrypcji](#SubscriptionSummary)  
-   [Informacje o koncie użytkownika](#UserAccountInfo)  
-   [Logowanie użytkownika](#UseSignIn)  
-   [Rejestracja użytkownika](#UserSignUp)  
  
##  <a name="API"></a>INTERFEJSU API  
 Jednostka `API` ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`id`|string|Identyfikator zasobu. Jednoznacznie identyfikuje interfejs API w bieżącym wystąpieniu usługi API Management. Wartość jest prawidłowym względnym adresem URL w formacie `apis/{id}`, gdzie `{id}` jest identyfikatorem interfejsu API. Ta właściwość jest tylko do odczytu.|  
|`name`|string|Nazwa interfejsu API. Nie może być pusty. Maksymalna długość to 100 znaków.|  
|`description`|string|Opis interfejsu API. Nie może być pusty. Może zawierać Tagi formatowania HTML. Maksymalna długość to 1000 znaków.|  
|`serviceUrl`|string|Bezwzględny adres URL usługi wewnętrznej bazy danych implementującej ten interfejs API.|  
|`path`|string|Względny adres URL, który jednoznacznie identyfikuje ten interfejs API i wszystkie jego ścieżki zasobów w ramach wystąpienia usługi API Management. Jest dołączany do podstawowego adresu URL punktu końcowego interfejsu API określonego podczas tworzenia wystąpienia usługi, aby utworzyć publiczny adres URL dla tego interfejsu API.|  
|`protocols`|tablica liczb|Opisuje protokoły, w których można wywołać operacje w tym interfejsie API. Dozwolone wartości to `1 - http` i `2 - https`.|  
|`authenticationSettings`|[Ustawienia uwierzytelniania serwera autoryzacji](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#AuthenticationSettings)|Kolekcja ustawień uwierzytelniania uwzględnionych w tym interfejsie API.|  
|`subscriptionKeyParameterNames`|obiekt|Opcjonalna właściwość, która może służyć do określania nazw niestandardowych dla parametrów zapytania i/lub nagłówka zawierających klucz subskrypcji. Gdy ta właściwość jest obecna, musi zawierać co najmniej jedną z dwóch następujących właściwości.<br /><br /> `{   "subscriptionKeyParameterNames":   {     "query": “customQueryParameterName",     "header": “customHeaderParameterName"   } }`|  
  
##  <a name="APISummary"></a>Podsumowanie interfejsu API  
 Jednostka `API summary` ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`id`|string|Identyfikator zasobu. Jednoznacznie identyfikuje interfejs API w bieżącym wystąpieniu usługi API Management. Wartość jest prawidłowym względnym adresem URL w formacie `apis/{id}`, gdzie `{id}` jest identyfikatorem interfejsu API. Ta właściwość jest tylko do odczytu.|  
|`name`|string|Nazwa interfejsu API. Nie może być pusty. Maksymalna długość to 100 znaków.|  
|`description`|string|Opis interfejsu API. Nie może być pusty. Może zawierać Tagi formatowania HTML. Maksymalna długość to 1000 znaków.|  
  
##  <a name="Application"></a>Aplikacja  
 Jednostka `application` ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`Id`|string|Unikatowy identyfikator aplikacji.|  
|`Title`|string|Tytuł aplikacji.|  
|`Description`|string|Opis aplikacji.|  
|`Url`|ADRESU|Identyfikator URI aplikacji.|  
|`Version`|string|Informacje o wersji aplikacji.|  
|`Requirements`|string|Opis wymagań aplikacji.|  
|`State`|numer|Bieżący stan aplikacji.<br /><br /> -0-zarejestrowano<br /><br /> -1-przesłane<br /><br /> -2 — Opublikowano<br /><br /> -3 — odrzucono<br /><br /> -4 — nieopublikowany|  
|`RegistrationDate`|Data i godzina|Data i godzina zarejestrowania aplikacji.|  
|`CategoryId`|numer|Kategoria aplikacji (Finanse, rozrywka itp.)|  
|`DeveloperId`|string|Unikatowy identyfikator dewelopera, który przesłał aplikację.|  
|`Attachments`|Kolekcja jednostek [załączników](#Attachment) .|Wszystkie załączniki dla aplikacji, takie jak zrzuty ekranu lub ikony.|  
|`Icon`|[Umieszczenie](#Attachment)|Ikona dla aplikacji.|  
  
##  <a name="Attachment"></a>Umieszczenie  
 Jednostka `attachment` ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`UniqueId`|string|Unikatowy identyfikator załącznika.|  
|`Url`|string|Adres URL zasobu.|  
|`Type`|string|Typ załącznika.|  
|`ContentType`|string|Typ nośnika załącznika.|  
  
##  <a name="Sample"></a>Przykładowy kod  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`title`|string|Nazwa operacji.|  
|`snippet`|string|Ta właściwość jest przestarzała i nie powinna być używana.|  
|`brush`|string|Który szablon kolorowania składni kodu ma być używany podczas wyświetlania przykładu kodu. Dozwolone wartości to `plain`, `php`, `java`, `xml`, `objc`, `python`, `ruby`i `csharp`.|  
|`template`|string|Nazwa tego szablonu przykładowego kodu.|  
|`body`|string|Symbol zastępczy fragmentu kodu.|  
|`method`|string|Metoda HTTP operacji.|  
|`scheme`|string|Protokół, który ma być używany dla żądania operacji.|  
|`path`|string|Ścieżka operacji.|  
|`query`|string|Przykład ciągu zapytania ze zdefiniowanymi parametrami.|  
|`host`|string|Adres URL bramy usługi API Management dla interfejsu API, który zawiera tę operację.|  
|`headers`|Kolekcja jednostek [nagłówka](#Header) .|Nagłówki dla tej operacji.|  
|`parameters`|Kolekcja jednostek [parametrów](#Parameter) .|Parametry, które są zdefiniowane dla tej operacji.|  
  
##  <a name="Comment"></a>Komentować  
 Jednostka `API` ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`Id`|numer|Identyfikator komentarza.|  
|`CommentText`|string|Treść komentarza. Może zawierać kod HTML.|  
|`DeveloperCompany`|string|Nazwa firmy dewelopera.|  
|`PostedOn`|Data i godzina|Data i godzina opublikowania komentarza.|  
  
##  <a name="Issue"></a>Wykonaj  
 Jednostka `issue` ma następujące właściwości.  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`Id`|string|Unikatowy identyfikator problemu.|  
|`ApiID`|string|Identyfikator interfejsu API, dla którego zgłoszono ten problem.|  
|`Title`|string|Tytuł problemu.|  
|`Description`|string|Opis problemu.|  
|`SubscriptionDeveloperName`|string|Imię i nazwisko dewelopera, który zgłosił problem.|  
|`IssueState`|string|Bieżący stan problemu. Możliwe wartości są proponowane, otwarte i zamknięte.|  
|`ReportedOn`|Data i godzina|Data i godzina zgłoszenia problemu.|  
|`Comments`|Kolekcja jednostek [komentarzy](#Comment) .|Komentarze dotyczące tego problemu.|  
|`Attachments`|Kolekcja jednostek [załączników](api-management-template-data-model-reference.md#Attachment) .|Dowolnych załączników do problemu.|  
|`Services`|Kolekcja jednostek [interfejsu API](#API) .|Interfejsy API subskrybowane przez użytkownika, który zgłosił problem.|  
  
##  <a name="Filtering"></a>Identyfikatorów  
 Jednostka `filtering` ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`Pattern`|string|Bieżący termin wyszukiwania; lub `null`, jeśli nie ma wyszukiwanego terminu.|  
|`Placeholder`|string|Tekst, który ma być wyświetlany w polu wyszukiwania, gdy nie określono terminu wyszukiwania.|  
  
##  <a name="Header"></a>Nagłówki  
 W tej sekcji opisano reprezentację `parameter`.  
  
|Właściwość|Typ|Opis|  
|--------------|-----------------|----------|  
|`name`|string|Nazwa parametru.|  
|`description`|string|Opis parametru.|  
|`value`|string|Wartość nagłówka.|  
|`typeName`|string|Typ danych wartości nagłówka.|  
|`options`|string|Opcje.|  
|`required`|wartość logiczna|Określa, czy nagłówek jest wymagany.|  
|`readOnly`|wartość logiczna|Czy nagłówek jest tylko do odczytu.|  
  
##  <a name="HTTPRequest"></a>Żądanie HTTP  
 W tej sekcji opisano reprezentację `request`.  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`description`|string|Opis żądania operacji.|  
|`headers`|Tablica jednostek [nagłówka](#Header) .|Nagłówki żądania.|  
|`parameters`|Tablica [parametrów](#Parameter)|Kolekcja parametrów żądania operacji.|  
|`representations`|Tablica [reprezentacji](#Representation)|Kolekcja reprezentacji żądań operacji.|  
  
##  <a name="HTTPResponse"></a>Odpowiedź HTTP  
 W tej sekcji opisano reprezentację `response`.  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`statusCode`|Dodatnia liczba całkowita|Kod stanu odpowiedzi operacji.|  
|`description`|string|Opis odpowiedzi operacji.|  
|`representations`|Tablica [reprezentacji](#Representation)|Kolekcja reprezentacji odpowiedzi operacji.|  
  
##  <a name="Operation"></a>Operacje  
 Jednostka `operation` ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`id`|string|Identyfikator zasobu. Unikatowa identyfikacja operacji w ramach bieżącego wystąpienia usługi API Management. Wartość jest prawidłowym względnym adresem URL w formacie `apis/{aid}/operations/{id}`, gdzie `{aid}` jest identyfikatorem interfejsu API, a `{id}` jest identyfikatorem operacji. Ta właściwość jest tylko do odczytu.|  
|`name`|string|Nazwa operacji. Nie może być pusty. Maksymalna długość to 100 znaków.|  
|`description`|string|Opis operacji. Nie może być pusty. Może zawierać Tagi formatowania HTML. Maksymalna długość to 1000 znaków.|  
|`scheme`|string|Opisuje protokoły, w których można wywołać operacje w tym interfejsie API. Dozwolone wartości to `http`, `https`lub obu `http` i `https`.|  
|`uriTemplate`|string|Szablon względnego adresu URL identyfikujący zasób docelowy dla tej operacji. Może zawierać parametry. Przykład: `customers/{cid}/orders/{oid}/?date={date}`|  
|`host`|string|Adres URL bramy API Management, który hostuje interfejs API.|  
|`httpMethod`|string|Metoda HTTP operacji.|  
|`request`|[Żądanie HTTP](#HTTPRequest)|Jednostka zawierająca szczegóły żądania.|  
|`responses`|Tablica [odpowiedzi HTTP](#HTTPResponse)|Tablica obiektów [odpowiedzi HTTP](#HTTPResponse) operacji.|  
  
##  <a name="Menu"></a>Menu operacje  
 Jednostka `operation menu` ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`ApiId`|string|Identyfikator bieżącego interfejsu API.|  
|`CurrentOperationId`|string|Identyfikator bieżącej operacji.|  
|`Action`|string|Typ menu.|  
|`MenuItems`|Kolekcja jednostek [elementów menu operacji](#MenuItem) .|Operacje dla bieżącego interfejsu API.|  
  
##  <a name="MenuItem"></a>Element menu operacji  
 Jednostka `operation menu item` ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`Id`|string|Identyfikator operacji.|  
|`Title`|string|Opis operacji.|  
|`HttpMethod`|string|Metoda http operacji.|  
  
##  <a name="Paging"></a>Stronicowania  
 Jednostka `paging` ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`Page`|numer|Numer bieżącej strony.|  
|`PageSize`|numer|Maksymalna liczba wyników do wyświetlenia na jednej stronie.|  
|`TotalItemCount`|numer|Liczba elementów do wyświetlenia.|  
|`ShowAll`|wartość logiczna|Czy wszystkie wyniki mają być pożądane na jednej stronie.|  
|`PageCount`|numer|Liczba stron wyników.|  
  
##  <a name="Parameter"></a>Konstruktora  
 W tej sekcji opisano reprezentację `parameter`.  
  
|Właściwość|Typ|Opis|  
|--------------|-----------------|----------|  
|`name`|string|Nazwa parametru.|  
|`description`|string|Opis parametru.|  
|`value`|string|Wartość parametru.|  
|`options`|tablica ciągów|Wartości zdefiniowane dla wartości parametrów zapytania.|  
|`required`|wartość logiczna|Określa, czy parametr jest wymagany, czy nie.|  
|`kind`|numer|Określa, czy ten parametr jest parametrem ścieżki (1), czy parametrem QueryString (2).|  
|`typeName`|string|Typ parametru.|  
  
##  <a name="Product"></a>Iloczyn  
 Jednostka `product` ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`Id`|string|Identyfikator zasobu. Unikatowy identyfikator produktu w ramach bieżącego wystąpienia usługi API Management. Wartość jest prawidłowym względnym adresem URL w formacie `products/{pid}`, gdzie `{pid}` jest identyfikatorem produktu. Ta właściwość jest tylko do odczytu.|  
|`Title`|string|Nazwa produktu. Nie może być pusty. Maksymalna długość to 100 znaków.|  
|`Description`|string|Opis produktu. Nie może być pusty. Może zawierać Tagi formatowania HTML. Maksymalna długość to 1000 znaków.|  
|`Terms`|string|Warunki użytkowania produktu. Deweloperzy próbujący subskrybować produkt będą przedstawiani i zobowiązani do zaakceptowania tych warunków przed ukończeniem procesu subskrypcji.|  
|`ProductState`|numer|Określa, czy produkt jest publikowany, czy nie. Opublikowane produkty są odnajdywane przez deweloperów w portalu dla deweloperów. Nieopublikowane produkty są widoczne tylko dla administratorów.<br /><br /> Dopuszczalne wartości dla stanu produktu to:<br /><br /> - `0 - Not Published`<br /><br /> - `1 - Published`<br /><br /> - `2 - Deleted`|  
|`AllowMultipleSubscriptions`|wartość logiczna|Określa, czy użytkownik może jednocześnie korzystać z wielu subskrypcji tego produktu.|  
|`MultipleSubscriptionsCount`|numer|Maksymalna liczba subskrypcji tego produktu, które może mieć użytkownik, w tym samym czasie.|  
  
##  <a name="Provider"></a>Dostawcy  
 Jednostka `provider` ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`Properties`|słownik ciągów|Właściwości tego dostawcy uwierzytelniania.|  
|`AuthenticationType`|string|Typ dostawcy. (Azure Active Directory, logowanie do serwisu Facebook, konto Google, konto Microsoft, Twitter).|  
|`Caption`|string|Nazwa wyświetlana dostawcy.|  
  
##  <a name="Representation"></a>Reprezentowana  
 W tej sekcji opisano `representation`.  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`contentType`|string|Określa zarejestrowany lub niestandardowy typ zawartości dla tej reprezentacji, na przykład `application/xml`.|  
|`sample`|string|Przykład reprezentacji.|  
  
##  <a name="Subscription"></a>Ramach  
 Jednostka `subscription` ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`Id`|string|Identyfikator zasobu. Jednoznacznie identyfikuje subskrypcję w ramach bieżącego wystąpienia usługi API Management. Wartość jest prawidłowym względnym adresem URL w formacie `subscriptions/{sid}`, gdzie `{sid}` jest identyfikatorem subskrypcji. Ta właściwość jest tylko do odczytu.|  
|`ProductId`|string|Identyfikator zasobu produktu subskrybowanego produktu. Wartość jest prawidłowym względnym adresem URL w formacie `products/{pid}`, gdzie `{pid}` jest identyfikatorem produktu.|  
|`ProductTitle`|string|Nazwa produktu. Nie może być pusty. Maksymalna długość to 100 znaków.|  
|`ProductDescription`|string|Opis produktu. Nie może być pusty. Może zawierać Tagi formatowania HTML. Maksymalna długość to 1000 znaków.|  
|`ProductDetailsUrl`|string|Względny adres URL do szczegółowych informacji o produkcie.|  
|`state`|string|Stan subskrypcji. Możliwe stany to:<br /><br /> - `0 - suspended` — subskrypcja została zablokowana i subskrybent nie może wywołać żadnych interfejsów API produktu.<br /><br /> - `1 - active` — subskrypcja jest aktywna.<br /><br /> - `2 - expired` — subskrypcja osiągnęła swoją datę wygaśnięcia i została zdezaktywowana.<br /><br /> - `3 - submitted` — żądanie subskrypcji zostało wykonane przez dewelopera, ale jeszcze nie zostało zatwierdzone lub odrzucone.<br /><br /> - `4 - rejected` — administrator odrzucił żądanie subskrypcji.<br /><br /> - `5 - cancelled` — subskrypcja została anulowana przez dewelopera lub administratora.|  
|`DisplayName`|string|Nazwa wyświetlana subskrypcji.|  
|`CreatedDate`|Datę|Data utworzenia subskrypcji w formacie ISO 8601: `2014-06-24T16:25:00Z`.|  
|`CanBeCancelled`|wartość logiczna|Czy subskrypcja może zostać anulowana przez bieżącego użytkownika.|  
|`IsAwaitingApproval`|wartość logiczna|Czy subskrypcja oczekuje na zatwierdzenie.|  
|`StartDate`|Datę|Data rozpoczęcia subskrypcji w formacie ISO 8601: `2014-06-24T16:25:00Z`.|  
|`ExpirationDate`|Datę|Data wygaśnięcia subskrypcji w formacie ISO 8601: `2014-06-24T16:25:00Z`.|  
|`NotificationDate`|Datę|Data powiadomienia dla subskrypcji w formacie ISO 8601: `2014-06-24T16:25:00Z`.|  
|`primaryKey`|string|Klucz subskrypcji podstawowej. Maksymalna długość to 256 znaków.|  
|`secondaryKey`|string|Pomocniczy klucz subskrypcji. Maksymalna długość to 256 znaków.|  
|`CanBeRenewed`|wartość logiczna|Czy subskrypcja może zostać odnowiona przez bieżącego użytkownika.|  
|`HasExpired`|wartość logiczna|Czy subskrypcja wygasła.|  
|`IsRejected`|wartość logiczna|Czy żądanie subskrypcji zostało odrzucone.|  
|`CancelUrl`|string|Względny adres URL, aby anulować subskrypcję.|  
|`RenewUrl`|string|Względny adres URL do odnowienia subskrypcji.|  
  
##  <a name="SubscriptionSummary"></a>Podsumowanie subskrypcji  
 Jednostka `subscription summary` ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`Id`|string|Identyfikator zasobu. Jednoznacznie identyfikuje subskrypcję w ramach bieżącego wystąpienia usługi API Management. Wartość jest prawidłowym względnym adresem URL w formacie `subscriptions/{sid}`, gdzie `{sid}` jest identyfikatorem subskrypcji. Ta właściwość jest tylko do odczytu.|  
|`DisplayName`|string|Nazwa wyświetlana subskrypcji|  
  
##  <a name="UserAccountInfo"></a>Informacje o koncie użytkownika  
 Jednostka `user account info` ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`FirstName`|string|Imię. Nie może być pusty. Maksymalna długość to 100 znaków.|  
|`LastName`|string|Nazwisko. Nie może być pusty. Maksymalna długość to 100 znaków.|  
|`Email`|string|Adres e-mail. Nie może być pusta i musi być unikatowa w obrębie wystąpienia usługi. Maksymalna długość to 254 znaków.|  
|`Password`|string|Hasło konta użytkownika.|  
|`NameIdentifier`|string|Identyfikator konta, taki sam jak adres e-mail użytkownika.|  
|`ProviderName`|string|Nazwa dostawcy uwierzytelniania.|  
|`IsBasicAccount`|wartość logiczna|Prawda, jeśli to konto zostało zarejestrowane przy użyciu poczty e-mail i hasła; wartość false, jeśli konto zostało zarejestrowane przy użyciu dostawcy.|  
  
##  <a name="UseSignIn"></a>Logowanie użytkownika  
 Jednostka `user sign in` ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`Email`|string|Adres e-mail. Nie może być pusta i musi być unikatowa w obrębie wystąpienia usługi. Maksymalna długość to 254 znaków.|  
|`Password`|string|Hasło konta użytkownika.|  
|`ReturnUrl`|string|Adres URL strony, na której użytkownik kliknął przycisk Zaloguj.|  
|`RememberMe`|wartość logiczna|Czy zapisać informacje o bieżącym użytkowniku.|  
|`RegistrationEnabled`|wartość logiczna|Czy rejestracja jest włączona.|  
|`DelegationEnabled`|wartość logiczna|Czy delegowane logowanie jest włączone.|  
|`DelegationUrl`|string|Delegowany adres URL logowania, jeśli jest włączony.|  
|`SsoSignUpUrl`|string|Adres URL logowania jednokrotnego dla użytkownika, jeśli jest obecny.|  
|`AuxServiceUrl`|string|Jeśli bieżący użytkownik jest administratorem, jest to link do wystąpienia usługi w Azure Portal.|  
|`Providers`|Kolekcja jednostek [dostawcy](#Provider)|Dostawcy uwierzytelniania dla tego użytkownika.|  
|`UserRegistrationTerms`|string|Warunki, które użytkownik musi wyrazić zgodę przed zalogowaniem się.|  
|`UserRegistrationTermsEnabled`|wartość logiczna|Czy warunki są włączone.|  
  
##  <a name="UserSignUp"></a>Rejestracja użytkownika  
 Jednostka `user sign up` ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`PasswordConfirm`|wartość logiczna|Wartość używana przez formant rejestracji w celu zarejestrowania [się](api-management-page-controls.md#sign-up).|  
|`Password`|string|Hasło konta użytkownika.|  
|`PasswordVerdictLevel`|numer|Wartość używana przez formant rejestracji w celu zarejestrowania [się](api-management-page-controls.md#sign-up).|  
|`UserRegistrationTerms`|string|Warunki, które użytkownik musi wyrazić zgodę przed zalogowaniem się.|  
|`UserRegistrationTermsOptions`|numer|Wartość używana przez formant rejestracji w celu zarejestrowania [się](api-management-page-controls.md#sign-up).|  
|`ConsentAccepted`|wartość logiczna|Wartość używana przez formant rejestracji w celu zarejestrowania [się](api-management-page-controls.md#sign-up).|  
|`Email`|string|Adres e-mail. Nie może być pusta i musi być unikatowa w obrębie wystąpienia usługi. Maksymalna długość to 254 znaków.|  
|`FirstName`|string|Imię. Nie może być pusty. Maksymalna długość to 100 znaków.|  
|`LastName`|string|Nazwisko. Nie może być pusty. Maksymalna długość to 100 znaków.|  
|`UserData`|string|Wartość używana przez formant [rejestracji](api-management-page-controls.md#sign-up) .|  
|`NameIdentifier`|string|Wartość używana przez formant rejestracji w celu zarejestrowania [się](api-management-page-controls.md#sign-up).|  
|`ProviderName`|string|Nazwa dostawcy uwierzytelniania.|

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [How to dostosowywanie portalu deweloperów API Management przy użyciu szablonów](api-management-developer-portal-templates.md).
