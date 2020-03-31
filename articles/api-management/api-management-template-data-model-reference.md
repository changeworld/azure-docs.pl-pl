---
title: Odwołanie do modelu danych szablonu usługi Azure API Management | Dokumenty firmy Microsoft
description: Dowiedz się więcej o reprezentacji encji i typów dla typowych elementów używanych w modelach danych dla szablonów portalu deweloperów w usłudze Azure API Management.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243941"
---
# <a name="azure-api-management-template-data-model-reference"></a>Odwołanie do modelu danych szablonu usługi Azure API Management
W tym temacie opisano reprezentacje jednostki i typów dla typowych elementów używanych w modelach danych dla szablonów portalu deweloperów w usłudze Azure API Management.  
  
 Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [Jak dostosować portal dewelopera zarządzania interfejsami API przy użyciu szablonów](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="reference"></a>Tematy pomocy

-   [interfejs API](#API)  
-   [Podsumowanie interfejsu API](#APISummary)  
-   [Aplikacja](#Application)  
-   [Załącznik](#Attachment)  
-   [Przykład kodu](#Sample)  
-   [Komentarz](#Comment)  
-   [Filtrowanie](#Filtering)  
-   [Nagłówka](#Header)  
-   [Żądanie HTTP](#HTTPRequest)  
-   [Odpowiedź HTTP](#HTTPResponse)  
-   [Problem](#Issue)  
-   [Operacji](#Operation)  
-   [Menu operacji](#Menu)  
-   [Pozycja menu operacji](#MenuItem)  
-   [Stronicowanie](#Paging)  
-   [Parametr](#Parameter)  
-   [Product (Produkt)](#Product)  
-   [Dostawca](#Provider)  
-   [Reprezentacja](#Representation)  
-   [Subskrypcja](#Subscription)  
-   [Podsumowanie subskrypcji](#SubscriptionSummary)  
-   [Informacje o koncie użytkownika](#UserAccountInfo)  
-   [Logowanie użytkowników](#UseSignIn)  
-   [Rejestracja użytkownika](#UserSignUp)  
  
##  <a name="api"></a><a name="API"></a>Api  
 Jednostka `API` ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`id`|ciąg|Identyfikator zasobu. Jednoznacznie identyfikuje interfejs API w bieżącym wystąpieniu usługi zarządzania interfejsem API. Wartość jest prawidłowym względnym adresem `apis/{id}` `{id}` URL w formacie where is an API identifier. Ta właściwość jest tylko do odczytu.|  
|`name`|ciąg|Nazwa interfejsu API. Nie może być pusty. Maksymalna długość to 100 znaków.|  
|`description`|ciąg|Opis interfejsu API. Nie może być pusty. Może zawierać znaczniki formatowania HTML. Maksymalna długość to 1000 znaków.|  
|`serviceUrl`|ciąg|Bezwzględny adres URL usługi wewnętrznej bazy danych implementującej ten interfejs API.|  
|`path`|ciąg|Względny adres URL jednoznacznie identyfikujący ten interfejs API i wszystkie jego ścieżki zasobów w wystąpieniu usługi zarządzania interfejsami API. Jest on dołączany do podstawowego adresu URL punktu końcowego interfejsu API określonego podczas tworzenia wystąpienia usługi w celu utworzenia publicznego adresu URL dla tego interfejsu API.|  
|`protocols`|tablica numerów|W tym artykule opisano, na których protokołów można wywołać operacje w tym interfejsie API. Dozwolone wartości `1 - http` są `2 - https`i , lub oba.|  
|`authenticationSettings`|[Ustawienia uwierzytelniania serwera autoryzacji](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#AuthenticationSettings)|Kolekcja ustawień uwierzytelniania zawartych w tym interfejsie API.|  
|`subscriptionKeyParameterNames`|obiekt|Opcjonalna właściwość, która może służyć do określania niestandardowych nazw dla parametrów kwerendy i/lub nagłówka zawierającego klucz subskrypcji. Gdy ta właściwość jest obecny, musi zawierać co najmniej jedną z dwóch następujących właściwości.<br /><br /> `{   "subscriptionKeyParameterNames":   {     "query": “customQueryParameterName",     "header": “customHeaderParameterName"   } }`|  
  
##  <a name="api-summary"></a><a name="APISummary"></a>Podsumowanie interfejsu API  
 Jednostka `API summary` ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`id`|ciąg|Identyfikator zasobu. Jednoznacznie identyfikuje interfejs API w bieżącym wystąpieniu usługi zarządzania interfejsem API. Wartość jest prawidłowym względnym adresem `apis/{id}` `{id}` URL w formacie where is an API identifier. Ta właściwość jest tylko do odczytu.|  
|`name`|ciąg|Nazwa interfejsu API. Nie może być pusty. Maksymalna długość to 100 znaków.|  
|`description`|ciąg|Opis interfejsu API. Nie może być pusty. Może zawierać znaczniki formatowania HTML. Maksymalna długość to 1000 znaków.|  
  
##  <a name="application"></a><a name="Application"></a>Aplikacji  
 Jednostka `application` ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`Id`|ciąg|Unikatowy identyfikator aplikacji.|  
|`Title`|ciąg|Tytuł aplikacji.|  
|`Description`|ciąg|Opis aplikacji.|  
|`Url`|Identyfikator URI|Identyfikator URI dla aplikacji.|  
|`Version`|ciąg|Informacje o wersji aplikacji.|  
|`Requirements`|ciąg|Opis wymagań dla aplikacji.|  
|`State`|numer|Bieżący stan aplikacji.<br /><br /> - 0 - Zarejestrowany<br /><br /> - 1 - Przesłane<br /><br /> - 2 - Opublikowano<br /><br /> - 3 - Odrzucone<br /><br /> - 4 - Niepublikowane|  
|`RegistrationDate`|DateTime|Data i godzina rejestracji wniosku.|  
|`CategoryId`|numer|Kategoria aplikacji (Finanse, rozrywka itp.)|  
|`DeveloperId`|ciąg|Unikatowy identyfikator dewelopera, który przesłał aplikację.|  
|`Attachments`|Kolekcja jednostek [załącznika.](#Attachment)|Wszelkie załączniki do aplikacji, takie jak zrzuty ekranu lub ikony.|  
|`Icon`|[Załącznik](#Attachment)|Ikona dla aplikacji.|  
  
##  <a name="attachment"></a><a name="Attachment"></a>Załącznik  
 Jednostka `attachment` ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`UniqueId`|ciąg|Unikatowy identyfikator załącznika.|  
|`Url`|ciąg|Adres URL zasobu.|  
|`Type`|ciąg|Typ załącznika.|  
|`ContentType`|ciąg|Typ nośnika załącznika.|  
  
##  <a name="code-sample"></a><a name="Sample"></a>Przykład kodu  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`title`|ciąg|Nazwa operacji.|  
|`snippet`|ciąg|Ta właściwość jest przestarzała i nie powinny być używane.|  
|`brush`|ciąg|Który szablon składni kodu ma być używany podczas wyświetlania przykładu kodu. Dozwolone wartości `plain`to `php` `java`, `xml` `objc`, `python` `ruby`, `csharp`, , , i .|  
|`template`|ciąg|Nazwa tego przykładowego szablonu kodu.|  
|`body`|ciąg|Symbol zastępczy przykładowej części kodu fragmentu kodu.|  
|`method`|ciąg|Metoda HTTP operacji.|  
|`scheme`|ciąg|Protokół do użycia dla żądania operacji.|  
|`path`|ciąg|Ścieżka operacji.|  
|`query`|ciąg|Przykład ciągu zapytania ze zdefiniowanymi parametrami.|  
|`host`|ciąg|Adres URL bramy usługi api Management dla interfejsu API, który zawiera tę operację.|  
|`headers`|Kolekcja jednostek [nagłówka.](#Header)|Nagłówki dla tej operacji.|  
|`parameters`|Kolekcja jednostek [parametrów.](#Parameter)|Parametry, które są zdefiniowane dla tej operacji.|  
  
##  <a name="comment"></a><a name="Comment"></a>Komentarz  
 Jednostka `API` ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`Id`|numer|Identyfikator komentarza.|  
|`CommentText`|ciąg|Treść komentarza. Może zawierać HTML.|  
|`DeveloperCompany`|ciąg|Nazwa firmy dewelopera.|  
|`PostedOn`|DateTime|Data i godzina opublikowania komentarza.|  
  
##  <a name="issue"></a><a name="Issue"></a>Problem  
 Jednostka `issue` ma następujące właściwości.  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`Id`|ciąg|Unikatowy identyfikator problemu.|  
|`ApiID`|ciąg|Identyfikator interfejsu API, dla którego ten problem został zgłoszony.|  
|`Title`|ciąg|Tytuł wydania.|  
|`Description`|ciąg|Opis problemu.|  
|`SubscriptionDeveloperName`|ciąg|Imię dewelopera, który zgłosił problem.|  
|`IssueState`|ciąg|Bieżący stan problemu. Możliwe wartości to Proponowane, Otwarte, Zamknięte.|  
|`ReportedOn`|DateTime|Data i godzina zgłoszenia problemu.|  
|`Comments`|Kolekcja [encji Komentarz.](#Comment)|Komentarze w tej sprawie.|  
|`Attachments`|Kolekcja jednostek [załącznika.](api-management-template-data-model-reference.md#Attachment)|Wszelkie załączniki do problemu.|  
|`Services`|Kolekcja jednostek [interfejsu API.](#API)|Interfejsy API subskrybowane przez użytkownika, który zgłosił problem.|  
  
##  <a name="filtering"></a><a name="Filtering"></a>Filtrowanie  
 Jednostka `filtering` ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`Pattern`|ciąg|Bieżący termin wyszukiwania; lub `null` jeśli nie ma wyszukiwałego terminu.|  
|`Placeholder`|ciąg|Tekst do wyświetlenia w polu wyszukiwania, gdy nie określono wyszukiwałego hasła.|  
  
##  <a name="header"></a><a name="Header"></a>Nagłówka  
 W tej `parameter` sekcji opisano reprezentację.  
  
|Właściwość|Typ|Opis|  
|--------------|-----------------|----------|  
|`name`|ciąg|Nazwa parametru.|  
|`description`|ciąg|Opis parametru.|  
|`value`|ciąg|Wartość nagłówka.|  
|`typeName`|ciąg|Typ danych wartości nagłówka.|  
|`options`|ciąg|Opcje.|  
|`required`|wartość logiczna|Czy nagłówek jest wymagany.|  
|`readOnly`|wartość logiczna|Czy nagłówek jest tylko do odczytu.|  
  
##  <a name="http-request"></a><a name="HTTPRequest"></a>Żądanie HTTP  
 W tej `request` sekcji opisano reprezentację.  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`description`|ciąg|Opis żądania operacji.|  
|`headers`|tablica jednostek [nagłówka.](#Header)|Nagłówki żądań.|  
|`parameters`|tablica [parametrów](#Parameter)|Zbieranie parametrów żądania operacji.|  
|`representations`|tablica [reprezentacji](#Representation)|Zbieranie oświadczeń żądań operacji.|  
  
##  <a name="http-response"></a><a name="HTTPResponse"></a>Odpowiedź HTTP  
 W tej `response` sekcji opisano reprezentację.  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`statusCode`|dodatnia wartość całkowita|Kod stanu odpowiedzi operacji.|  
|`description`|ciąg|Opis odpowiedzi operacji.|  
|`representations`|tablica [reprezentacji](#Representation)|Kolekcja reprezentacji odpowiedzi operacji.|  
  
##  <a name="operation"></a><a name="Operation"></a>Operacji  
 Jednostka `operation` ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`id`|ciąg|Identyfikator zasobu. Jednoznacznie identyfikuje operację w bieżącym wystąpieniu usługi zarządzania interfejsem API. Wartość jest prawidłowym względnym adresem `apis/{aid}/operations/{id}` `{aid}` URL w formacie `{id}` where is an API identifier i jest identyfikatorem operacji. Ta właściwość jest tylko do odczytu.|  
|`name`|ciąg|Nazwa operacji. Nie może być pusty. Maksymalna długość to 100 znaków.|  
|`description`|ciąg|Opis operacji. Nie może być pusty. Może zawierać znaczniki formatowania HTML. Maksymalna długość to 1000 znaków.|  
|`scheme`|ciąg|W tym artykule opisano, na których protokołów można wywołać operacje w tym interfejsie API. Dozwolone wartości `http`to `https`, `http` lub `https`oba i .|  
|`uriTemplate`|ciąg|Szablon względnego adresu URL identyfikujący zasób docelowy dla tej operacji. Może zawierać parametry. Przykład: `customers/{cid}/orders/{oid}/?date={date}`|  
|`host`|ciąg|Adres URL bramy zarządzania interfejsami API, który obsługuje interfejs API.|  
|`httpMethod`|ciąg|Operacja HTTP metoda.|  
|`request`|[Żądanie HTTP](#HTTPRequest)|Jednostka zawierająca szczegóły żądania.|  
|`responses`|tablica [odpowiedzi HTTP](#HTTPResponse)|Tablica jednostek [odpowiedzi HTTP](#HTTPResponse) operacji.|  
  
##  <a name="operation-menu"></a><a name="Menu"></a>Menu operacji  
 Jednostka `operation menu` ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`ApiId`|ciąg|Identyfikator bieżącego interfejsu API.|  
|`CurrentOperationId`|ciąg|Identyfikator bieżącej operacji.|  
|`Action`|ciąg|Typ menu.|  
|`MenuItems`|Kolekcja jednostek [elementu menu operacji.](#MenuItem)|Operacje dla bieżącego interfejsu API.|  
  
##  <a name="operation-menu-item"></a><a name="MenuItem"></a>Pozycja menu operacji  
 Jednostka `operation menu item` ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`Id`|ciąg|Identyfikator operacji.|  
|`Title`|ciąg|Opis operacji.|  
|`HttpMethod`|ciąg|Http metoda operacji.|  
  
##  <a name="paging"></a><a name="Paging"></a>Stronicowania  
 Jednostka `paging` ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`Page`|numer|Bieżący numer strony.|  
|`PageSize`|numer|Maksymalne wyniki wyświetlane na jednej stronie.|  
|`TotalItemCount`|numer|Liczba elementów do wyświetlenia.|  
|`ShowAll`|wartość logiczna|Czy sho wszystkie wyniki na jednej stronie.|  
|`PageCount`|numer|Liczba stron wyników.|  
  
##  <a name="parameter"></a><a name="Parameter"></a>Parametr  
 W tej `parameter` sekcji opisano reprezentację.  
  
|Właściwość|Typ|Opis|  
|--------------|-----------------|----------|  
|`name`|ciąg|Nazwa parametru.|  
|`description`|ciąg|Opis parametru.|  
|`value`|ciąg|Wartość parametru.|  
|`options`|tablica ciągów|Wartości zdefiniowane dla wartości parametrów kwerendy.|  
|`required`|wartość logiczna|Określa, czy parametr jest wymagany, czy nie.|  
|`kind`|numer|Czy ten parametr jest parametrem ścieżki (1), czy parametrem querystring (2).|  
|`typeName`|ciąg|Typ parametru.|  
  
##  <a name="product"></a><a name="Product"></a>Produktu  
 Jednostka `product` ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`Id`|ciąg|Identyfikator zasobu. Jednoznacznie identyfikuje produkt w bieżącym wystąpieniu usługi zarządzania interfejsem API. Wartość jest prawidłowym względnym adresem `products/{pid}` `{pid}` URL w formacie, w którym jest identyfikator produktu. Ta właściwość jest tylko do odczytu.|  
|`Title`|ciąg|Nazwa produktu. Nie może być pusty. Maksymalna długość to 100 znaków.|  
|`Description`|ciąg|Opis produktu. Nie może być pusty. Może zawierać znaczniki formatowania HTML. Maksymalna długość to 1000 znaków.|  
|`Terms`|ciąg|Warunki użytkowania produktu. Deweloperzy próbujący subskrybować produkt zostaną przedstawieni i zobowiązani do zaakceptowania tych warunków, zanim będą mogli ukończyć proces subskrypcji.|  
|`ProductState`|numer|Określa, czy produkt jest publikowany, czy nie. Opublikowane produkty są wykrywalne przez deweloperów w portalu dla deweloperów. Nieopublikowane produkty są widoczne tylko dla administratorów.<br /><br /> Dopuszczalne wartości dla stanu produktu to:<br /><br /> - `0 - Not Published`<br /><br /> - `1 - Published`<br /><br /> - `2 - Deleted`|  
|`AllowMultipleSubscriptions`|wartość logiczna|Określa, czy użytkownik może mieć wiele subskrypcji tego produktu w tym samym czasie.|  
|`MultipleSubscriptionsCount`|numer|Maksymalna liczba subskrypcji tego produktu, które użytkownik może mieć w tym samym czasie.|  
  
##  <a name="provider"></a><a name="Provider"></a>Dostawca  
 Jednostka `provider` ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`Properties`|słownik ciągów|Właściwości dla tego dostawcy uwierzytelniania.|  
|`AuthenticationType`|ciąg|Typ dostawcy. (Usługa Azure Active Directory, logowanie do Facebooka, konto Google, konto Microsoft, Twitter).|  
|`Caption`|ciąg|Wyświetlana nazwa dostawcy.|  
  
##  <a name="representation"></a><a name="Representation"></a>Reprezentacja  
 W tej `representation`sekcji opisano plik .  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`contentType`|ciąg|Określa zarejestrowany lub niestandardowy typ zawartości dla `application/xml`tej reprezentacji, na przykład .|  
|`sample`|ciąg|Przykład reprezentacji.|  
  
##  <a name="subscription"></a><a name="Subscription"></a>Subskrypcji  
 Jednostka `subscription` ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`Id`|ciąg|Identyfikator zasobu. Jednoznacznie identyfikuje subskrypcję w bieżącym wystąpieniu usługi zarządzania interfejsem API. Wartość jest prawidłowym względnym adresem `subscriptions/{sid}` `{sid}` URL w formacie, w którym jest identyfikator subskrypcji. Ta właściwość jest tylko do odczytu.|  
|`ProductId`|ciąg|Identyfikator zasobu produktu subskrybowanego produktu. Wartość jest prawidłowym względnym adresem `products/{pid}` `{pid}` URL w formacie, w którym jest identyfikator produktu.|  
|`ProductTitle`|ciąg|Nazwa produktu. Nie może być pusty. Maksymalna długość to 100 znaków.|  
|`ProductDescription`|ciąg|Opis produktu. Nie może być pusty. Może zawierać znaczniki formatowania HTML. Maksymalna długość to 1000 znaków.|  
|`ProductDetailsUrl`|ciąg|Względny adres URL do szczegółów produktu.|  
|`state`|ciąg|Stan subskrypcji. Możliwe stany to:<br /><br /> - `0 - suspended`— subskrypcja jest zablokowana, a subskrybent nie może wywołać żadnych interfejsów API produktu.<br /><br /> - `1 - active`– subskrypcja jest aktywna.<br /><br /> - `2 - expired`– subskrypcja osiągnęła datę wygaśnięcia i została dezaktywowana.<br /><br /> - `3 - submitted`– wniosek o subskrypcję został złożony przez dewelopera, ale nie został jeszcze zatwierdzony lub odrzucony.<br /><br /> - `4 - rejected`– żądanie subskrypcji zostało odrzucone przez administratora.<br /><br /> - `5 - cancelled`– subskrypcja została anulowana przez dewelopera lub administratora.|  
|`DisplayName`|ciąg|Wyświetlana nazwa subskrypcji.|  
|`CreatedDate`|Data i godzina|Data utworzenia subskrypcji w formacie ISO 8601: `2014-06-24T16:25:00Z`.|  
|`CanBeCancelled`|wartość logiczna|Czy subskrypcja może zostać anulowana przez bieżącego użytkownika.|  
|`IsAwaitingApproval`|wartość logiczna|Czy subskrypcja oczekuje na zatwierdzenie.|  
|`StartDate`|Data i godzina|Data rozpoczęcia subskrypcji w formacie ISO 8601: `2014-06-24T16:25:00Z`.|  
|`ExpirationDate`|Data i godzina|Data wygaśnięcia subskrypcji w formacie ISO 8601: `2014-06-24T16:25:00Z`.|  
|`NotificationDate`|Data i godzina|Data powiadomienia o subskrypcji w formacie ISO 8601: `2014-06-24T16:25:00Z`.|  
|`primaryKey`|ciąg|Podstawowy klucz subskrypcji. Maksymalna długość to 256 znaków.|  
|`secondaryKey`|ciąg|Pomocniczy klucz subskrypcji. Maksymalna długość to 256 znaków.|  
|`CanBeRenewed`|wartość logiczna|Czy subskrypcja może być odnawiana przez bieżącego użytkownika.|  
|`HasExpired`|wartość logiczna|Czy subskrypcja wygasła.|  
|`IsRejected`|wartość logiczna|Czy żądanie subskrypcji zostało odrzucone.|  
|`CancelUrl`|ciąg|Względny adres URL, aby anulować subskrypcję.|  
|`RenewUrl`|ciąg|Względny adres URL do odnowienia subskrypcji.|  
  
##  <a name="subscription-summary"></a><a name="SubscriptionSummary"></a>Podsumowanie subskrypcji  
 Jednostka `subscription summary` ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`Id`|ciąg|Identyfikator zasobu. Jednoznacznie identyfikuje subskrypcję w bieżącym wystąpieniu usługi zarządzania interfejsem API. Wartość jest prawidłowym względnym adresem `subscriptions/{sid}` `{sid}` URL w formacie, w którym jest identyfikator subskrypcji. Ta właściwość jest tylko do odczytu.|  
|`DisplayName`|ciąg|Wyświetlana nazwa subskrypcji|  
  
##  <a name="user-account-info"></a><a name="UserAccountInfo"></a>Informacje o koncie użytkownika  
 Jednostka `user account info` ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`FirstName`|ciąg|Imię. Nie może być pusty. Maksymalna długość to 100 znaków.|  
|`LastName`|ciąg|Nazwisko. Nie może być pusty. Maksymalna długość to 100 znaków.|  
|`Email`|ciąg|Adres e-mail. Nie może być pusty i musi być unikatowy w wystąpieniu usługi. Maksymalna długość to 254 znaki.|  
|`Password`|ciąg|Hasło do konta użytkownika.|  
|`NameIdentifier`|ciąg|Identyfikator konta, taki sam jak adres e-mail użytkownika.|  
|`ProviderName`|ciąg|Nazwa dostawcy uwierzytelniania.|  
|`IsBasicAccount`|wartość logiczna|Prawda, jeśli to konto zostało zarejestrowane przy użyciu adresu e-mail i hasła; false, jeśli konto zostało zarejestrowane za pomocą dostawcy.|  
  
##  <a name="user-sign-in"></a><a name="UseSignIn"></a>Logowanie użytkownika  
 Jednostka `user sign in` ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`Email`|ciąg|Adres e-mail. Nie może być pusty i musi być unikatowy w wystąpieniu usługi. Maksymalna długość to 254 znaki.|  
|`Password`|ciąg|Hasło do konta użytkownika.|  
|`ReturnUrl`|ciąg|Adres URL strony, na której użytkownik kliknął logowanie.|  
|`RememberMe`|wartość logiczna|Czy zapisać informacje o bieżącym użytkowniku.|  
|`RegistrationEnabled`|wartość logiczna|Czy rejestracja jest włączona.|  
|`DelegationEnabled`|wartość logiczna|Czy logowanie delegowane jest włączone.|  
|`DelegationUrl`|ciąg|Delegowany adres URL logowania, jeśli jest włączony.|  
|`SsoSignUpUrl`|ciąg|Pojedynczy znak na adres URL dla użytkownika, jeśli jest obecny.|  
|`AuxServiceUrl`|ciąg|Jeśli bieżący użytkownik jest administratorem, jest to łącze do wystąpienia usługi w witrynie Azure portal.|  
|`Providers`|Zbieranie jednostek [dostawcy](#Provider)|Dostawców uwierzytelniania dla tego użytkownika.|  
|`UserRegistrationTerms`|ciąg|Warunki, na które użytkownik musi wyrazić zgodę przed zalogowaniem się.|  
|`UserRegistrationTermsEnabled`|wartość logiczna|Czy terminy są włączone.|  
  
##  <a name="user-sign-up"></a><a name="UserSignUp"></a>Rejestracja użytkownika  
 Jednostka `user sign up` ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`PasswordConfirm`|wartość logiczna|Wartość używana przez [formant](api-management-page-controls.md#sign-up)rejestracji.|  
|`Password`|ciąg|Hasło do konta użytkownika.|  
|`PasswordVerdictLevel`|numer|Wartość używana przez [formant](api-management-page-controls.md#sign-up)rejestracji.|  
|`UserRegistrationTerms`|ciąg|Warunki, na które użytkownik musi wyrazić zgodę przed zalogowaniem się.|  
|`UserRegistrationTermsOptions`|numer|Wartość używana przez [formant](api-management-page-controls.md#sign-up)rejestracji.|  
|`ConsentAccepted`|wartość logiczna|Wartość używana przez [formant](api-management-page-controls.md#sign-up)rejestracji.|  
|`Email`|ciąg|Adres e-mail. Nie może być pusty i musi być unikatowy w wystąpieniu usługi. Maksymalna długość to 254 znaki.|  
|`FirstName`|ciąg|Imię. Nie może być pusty. Maksymalna długość to 100 znaków.|  
|`LastName`|ciąg|Nazwisko. Nie może być pusty. Maksymalna długość to 100 znaków.|  
|`UserData`|ciąg|Wartość używana przez [formant rejestracji.](api-management-page-controls.md#sign-up)|  
|`NameIdentifier`|ciąg|Wartość używana przez [formant](api-management-page-controls.md#sign-up)rejestracji.|  
|`ProviderName`|ciąg|Nazwa dostawcy uwierzytelniania.|

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [Jak dostosować portal dewelopera zarządzania interfejsami API przy użyciu szablonów](api-management-developer-portal-templates.md).
