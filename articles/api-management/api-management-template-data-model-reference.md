---
title: Danych szablonu usługi Azure API Management modelu odniesienia | Dokumentacja firmy Microsoft
description: Więcej informacji na temat reprezentacji jednostek i typów dla wspólne elementy używane w ramach modeli danych dla szablonów portalu dla deweloperów w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: b0ad7e15-9519-4517-bb73-32e593ed6380
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: apimpm
ms.openlocfilehash: 3c2384b536235554fed7c1cf1a08b7c665f513a8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61094526"
---
# <a name="azure-api-management-template-data-model-reference"></a>Dokumentacja modelu danych szablonu usługi Azure API Management
W tym temacie opisano reprezentacji jednostek i typów dla wspólne elementy używane w ramach modeli danych dla szablonów portalu dla deweloperów w usłudze Azure API Management.  
  
 Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [Dostosowywanie portalu dla deweloperów usługi API Management przy użyciu szablonów](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

Portalu dla deweloperów, nie jest dostępne w warstwie zużycia.

## <a name="reference"></a>Informacje ogólne

-   [Interfejs API](#API)  
-   [Podsumowanie interfejsu API](#APISummary)  
-   [Aplikacja](#Application)  
-   [Załącznik](#Attachment)  
-   [Przykładowy kod](#Sample)  
-   [Komentarz](#Comment)  
-   [Filtrowanie](#Filtering)  
-   [Nagłówek](#Header)  
-   [Żądanie HTTP](#HTTPRequest)  
-   [Odpowiedź HTTP](#HTTPResponse)  
-   [Problem](#Issue)  
-   [Operacja](#Operation)  
-   [Operacja menu](#Menu)  
-   [Operacja elementu menu.](#MenuItem)  
-   [Stronicowania](#Paging)  
-   [Parametr](#Parameter)  
-   [Produkt](#Product)  
-   [Dostawca](#Provider)  
-   [Reprezentacja](#Representation)  
-   [Subskrypcja](#Subscription)  
-   [Podsumowanie subskrypcji](#SubscriptionSummary)  
-   [Informacje o koncie użytkownika](#UserAccountInfo)  
-   [Logowanie użytkownika](#UseSignIn)  
-   [Utwórz konto użytkownika](#UserSignUp)  
  
##  <a name="API"></a> INTERFEJS API  
 `API` Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`id`|string|Identyfikator zasobu. Jednoznacznie identyfikuje interfejs API w ramach bieżącego wystąpienia usługi API Management. Wartość jest prawidłowym względnym adresem URL w formacie `apis/{id}` gdzie `{id}` jest to identyfikator interfejsu API. Ta właściwość jest tylko do odczytu.|  
|`name`|string|Nazwa interfejsu API. Nie może być pusta. Maksymalna długość wynosi 100 znaków.|  
|`description`|string|Opis interfejsu API. Nie może być pusta. Może obejmować formatowanie tagów HTML. Maksymalna długość wynosi 1000 znaków.|  
|`serviceUrl`|string|Bezwzględny adres URL usługi zaplecza, implementacja tego interfejsu API.|  
|`path`|string|Względny adres URL, który unikatowo identyfikuje ten interfejs API i wszystkie jego ścieżki zasobów w ramach wystąpienia usługi API Management. Jest ona dołączana do interfejsu API punktu końcowego podstawowego adresu URL określona podczas tworzenia wystąpienia usługi w celu utworzenia publicznego adresu URL dla tego interfejsu API.|  
|`protocols`|tablica liczb|W tym artykule opisano, na których protokołów można wywołać operacji w tym interfejsie API. Dozwolone wartości to `1 - http` i `2 - https`, lub obu.|  
|`authenticationSettings`|[Ustawienia uwierzytelniania serwera autoryzacji](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#AuthenticationSettings)|Kolekcja ustawień uwierzytelniania zawarte w tym interfejsie API.|  
|`subscriptionKeyParameterNames`|obiekt|Opcjonalna właściwość, która może służyć do określania niestandardowych nazw parametrów zapytania i/lub nagłówka zawierający klucz subskrypcji. Gdy ta właściwość jest obecny, musi zawierać co najmniej jeden z dwóch następujących właściwości.<br /><br /> `{   "subscriptionKeyParameterNames":   {     "query": “customQueryParameterName",     "header": “customHeaderParameterName"   } }`|  
  
##  <a name="APISummary"></a> Podsumowanie interfejsu API  
 `API summary` Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`id`|string|Identyfikator zasobu. Jednoznacznie identyfikuje interfejs API w ramach bieżącego wystąpienia usługi API Management. Wartość jest prawidłowym względnym adresem URL w formacie `apis/{id}` gdzie `{id}` jest to identyfikator interfejsu API. Ta właściwość jest tylko do odczytu.|  
|`name`|string|Nazwa interfejsu API. Nie może być pusta. Maksymalna długość wynosi 100 znaków.|  
|`description`|string|Opis interfejsu API. Nie może być pusta. Może obejmować formatowanie tagów HTML. Maksymalna długość wynosi 1000 znaków.|  
  
##  <a name="Application"></a> Aplikacja  
 `application` Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`Id`|string|Unikatowy identyfikator aplikacji.|  
|`Title`|string|Tytuł aplikacji.|  
|`Description`|string|Opis aplikacji.|  
|`Url`|URI|Identyfikator URI dla aplikacji.|  
|`Version`|string|Informacje o wersji dla aplikacji.|  
|`Requirements`|string|Opis wymagań aplikacji.|  
|`State`|numer|Bieżący stan aplikacji.<br /><br /> -0 - zarejestrowany<br /><br /> -1 - przesłane<br /><br /> -2 - opublikowane<br /><br /> -3 - odrzucone<br /><br /> -4 - nieopublikowane|  
|`RegistrationDate`|DateTime|Data i godzina, których aplikacja została zarejestrowana.|  
|`CategoryId`|numer|Do kategorii aplikacji (finanse, rozrywka itp.)|  
|`DeveloperId`|string|Unikatowy identyfikator dla deweloperów, który przesłany aplikacji.|  
|`Attachments`|Kolekcja [załącznika](#Attachment) jednostek.|Wszystkie załączniki dla aplikacji, takie jak zrzuty ekranu lub ikony.|  
|`Icon`|[Załącznik](#Attachment)|Ikona dla aplikacji.|  
  
##  <a name="Attachment"></a> Załącznik  
 `attachment` Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`UniqueId`|string|Unikatowy identyfikator dla załącznika.|  
|`Url`|string|Adres URL zasobu.|  
|`Type`|string|Typ załącznika.|  
|`ContentType`|string|Typ nośnika załącznika.|  
  
##  <a name="Sample"></a> Przykładowy kod  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`title`|string|Nazwa operacji.|  
|`snippet`|string|Ta właściwość jest przestarzała i nie powinna być używana.|  
|`brush`|string|Składni kodu kolorowania szablon, który będzie używany podczas wyświetlania przykładowy kod. Dozwolone wartości to `plain`, `php`, `java`, `xml`, `objc`, `python`, `ruby`, i `csharp`.|  
|`template`|string|Nazwa tego kodu przykładowego szablonu.|  
|`body`|string|Symbol zastępczy dla określonej części przykładowy kod fragmentu kodu.|  
|`method`|string|Metoda HTTP operacji.|  
|`scheme`|string|Protokół do użycia dla żądania operacji.|  
|`path`|string|Ścieżka operacji.|  
|`query`|string|Przykład zdefiniowanych parametrów ciągu zapytania.|  
|`host`|string|Adres URL bramy usługi API Management dla interfejsu API, który zawiera tę operację.|  
|`headers`|Kolekcja [nagłówka](#Header) jednostek.|Nagłówki dla tej operacji.|  
|`parameters`|Kolekcja [parametru](#Parameter) jednostek.|Parametry, które są zdefiniowane dla tej operacji.|  
  
##  <a name="Comment"></a> Komentarz  
 `API` Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`Id`|numer|Identyfikator komentarza.|  
|`CommentText`|string|Treść komentarza. Może obejmować HTML.|  
|`DeveloperCompany`|string|Nazwa firmy dewelopera.|  
|`PostedOn`|DateTime|Data i godzina, którego ten komentarz został opublikowany.|  
  
##  <a name="Issue"></a> Problem  
 `issue` Jednostka ma następujące właściwości.  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`Id`|string|Unikatowy identyfikator problemu.|  
|`ApiID`|string|Identyfikator interfejsu API, dla którego ten problem został zgłoszony.|  
|`Title`|string|Tytuł problemu.|  
|`Description`|string|Opis problemu.|  
|`SubscriptionDeveloperName`|string|Imię dewelopera, który zgłosił problem.|  
|`IssueState`|string|Bieżący stan problemu. Możliwe wartości to proponowane, Opened, zamknięte.|  
|`ReportedOn`|DateTime|Data i godzina, który został zgłoszony problem.|  
|`Comments`|Kolekcja [komentarz](#Comment) jednostek.|Komentarze dotyczące tego problemu.|  
|`Attachments`|Kolekcja [załącznika](api-management-template-data-model-reference.md#Attachment) jednostek.|Załączniki do tego problemu.|  
|`Services`|Kolekcja [API](#API) jednostek.|Interfejsy API subskrybowana przez użytkownika, który zgłosić problem.|  
  
##  <a name="Filtering"></a> Filtrowanie  
 `filtering` Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`Pattern`|string|Bieżący wyszukiwany termin; lub `null` Jeśli brak wyszukiwanego terminu.|  
|`Placeholder`|string|Tekst do wyświetlenia w polu wyszukiwania, gdy jest brak wyszukiwanego terminu określony.|  
  
##  <a name="Header"></a> Nagłówek  
 W tej sekcji opisano `parameter` reprezentacji.  
  
|Właściwość|Typ|Opis|  
|--------------|-----------------|----------|  
|`name`|string|Nazwa parametru.|  
|`description`|string|Opis parametru.|  
|`value`|string|Wartość nagłówka.|  
|`typeName`|string|Typ danych wartości nagłówka.|  
|`options`|string|Opcje.|  
|`required`|wartość logiczna|Czy nagłówka jest wymagana.|  
|`readOnly`|wartość logiczna|Czy nagłówek jest tylko do odczytu.|  
  
##  <a name="HTTPRequest"></a> Żądanie HTTP  
 W tej sekcji opisano `request` reprezentacji.  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`description`|string|Opis żądania operacji.|  
|`headers`|Tablica [nagłówka](#Header) jednostek.|Nagłówki żądania.|  
|`parameters`|Tablica [parametru](#Parameter)|Kolekcja parametrów żądania operacji.|  
|`representations`|Tablica [reprezentacji](#Representation)|Kolekcja reprezentacje żądania operacji.|  
  
##  <a name="HTTPResponse"></a> Odpowiedź HTTP  
 W tej sekcji opisano `response` reprezentacji.  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`statusCode`|dodatnia liczba całkowita|Kod statusu odpowiedzi operacji.|  
|`description`|string|Opis odpowiedzi operacji.|  
|`representations`|Tablica [reprezentacji](#Representation)|Kolekcja reprezentacje odpowiedzi operacji.|  
  
##  <a name="Operation"></a> Operacja  
 `operation` Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`id`|string|Identyfikator zasobu. Jednoznacznie identyfikuje operacji w ciągu bieżącego wystąpienia usługi API Management. Wartość jest prawidłowym względnym adresem URL w formacie `apis/{aid}/operations/{id}` gdzie `{aid}` jest to identyfikator interfejsu API i `{id}` jest identyfikatorem operacji. Ta właściwość jest tylko do odczytu.|  
|`name`|string|Nazwa operacji. Nie może być pusta. Maksymalna długość wynosi 100 znaków.|  
|`description`|string|Opis operacji. Nie może być pusta. Może obejmować formatowanie tagów HTML. Maksymalna długość wynosi 1000 znaków.|  
|`scheme`|string|W tym artykule opisano, na których protokołów można wywołać operacji w tym interfejsie API. Dozwolone wartości to `http`, `https`, i / lub `http` i `https`.|  
|`uriTemplate`|string|Względna szablon adres URL identyfikujący zasób docelowy dla tej operacji. Może zawierać parametrów. Przykład: `customers/{cid}/orders/{oid}/?date={date}`|  
|`host`|string|URL bramy usługi API Management udostępnia interfejs API.|  
|`httpMethod`|string|Metoda operacji HTTP.|  
|`request`|[Żądanie HTTP](#HTTPRequest)|Jednostki zawierające szczegóły żądania.|  
|`responses`|Tablica [odpowiedzi HTTP](#HTTPResponse)|Szereg operacji [odpowiedź HTTP](#HTTPResponse) jednostek.|  
  
##  <a name="Menu"></a> Operacja menu  
 `operation menu` Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`ApiId`|string|Identyfikator bieżącego interfejsu API.|  
|`CurrentOperationId`|string|Identyfikator bieżącej operacji.|  
|`Action`|string|Typ menu.|  
|`MenuItems`|Kolekcja [element menu operacji](#MenuItem) jednostek.|Operacje dla bieżącego interfejsu API.|  
  
##  <a name="MenuItem"></a> Operacja elementu menu.  
 `operation menu item` Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`Id`|string|Identyfikator operacji.|  
|`Title`|string|Opis operacji.|  
|`HttpMethod`|string|Metoda Http operacji.|  
  
##  <a name="Paging"></a> Stronicowania  
 `paging` Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`Page`|numer|Numer bieżącej strony.|  
|`PageSize`|numer|Maksymalna liczba wyników do wyświetlenia na jednej stronie.|  
|`TotalItemCount`|numer|Liczba elementów do wyświetlenia.|  
|`ShowAll`|wartość logiczna|Określa, czy Pokaż wszystkie wyniki na jednej stronie.|  
|`PageCount`|numer|Liczba stron wyników.|  
  
##  <a name="Parameter"></a> Parametr  
 W tej sekcji opisano `parameter` reprezentacji.  
  
|Właściwość|Typ|Opis|  
|--------------|-----------------|----------|  
|`name`|string|Nazwa parametru.|  
|`description`|string|Opis parametru.|  
|`value`|string|Wartość parametru.|  
|`options`|tablica ciągów|Wartości zdefiniowane dla wartości parametrów zapytania.|  
|`required`|wartość logiczna|Określa, czy parametr jest wymagany.|  
|`kind`|numer|Czy ten parametr jest to parametr ścieżki (1) lub parametr querystring (2).|  
|`typeName`|string|Typ parametru.|  
  
##  <a name="Product"></a> Produkt  
 `product` Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`Id`|string|Identyfikator zasobu. Jednoznacznie identyfikuje produkt w ciągu bieżącego wystąpienia usługi API Management. Wartość jest prawidłowym względnym adresem URL w formacie `products/{pid}` gdzie `{pid}` to identyfikator produktu. Ta właściwość jest tylko do odczytu.|  
|`Title`|string|Nazwa produktu. Nie może być pusta. Maksymalna długość wynosi 100 znaków.|  
|`Description`|string|Opis produktu. Nie może być pusta. Może obejmować formatowanie tagów HTML. Maksymalna długość wynosi 1000 znaków.|  
|`Terms`|string|Produkt z warunkami użytkowania. Deweloperzy próbuje subskrybować produkt będą prezentowane i trzeba je zaakceptować przed ukończeniem procesu subskrypcji.|  
|`ProductState`|numer|Określa, czy produkt jest publikowany czy nie. Opublikowane produkty są wykrywane przez deweloperów w portalu dla deweloperów. Opublikowane przez inne niż produkty są widoczne tylko dla administratorów.<br /><br /> Dopuszczalne wartości dla stanu produktu są:<br /><br /> - `0 - Not Published`<br /><br /> - `1 - Published`<br /><br /> - `2 - Deleted`|  
|`AllowMultipleSubscriptions`|wartość logiczna|Określa, czy użytkownik może mieć wiele subskrypcji tego produktu, w tym samym czasie.|  
|`MultipleSubscriptionsCount`|numer|Maksymalna liczba subskrypcji dla tego produktu użytkownik może mieć w tym samym czasie.|  
  
##  <a name="Provider"></a> Dostawcy  
 `provider` Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`Properties`|Słownik ciągu|Właściwości dla tego dostawcy uwierzytelniania.|  
|`AuthenticationType`|string|Typ dostawcy. (Usługa azure Active Directory, logowania do usługi Facebook, konta Google, Microsoft Account, Twitter).|  
|`Caption`|string|Nazwa wyświetlana dostawcy.|  
  
##  <a name="Representation"></a> Reprezentacja  
 W tej sekcji opisano `representation`.  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`contentType`|string|Na przykład określa zarejestrowane lub niestandardowy typ zawartości to reprezentacja `application/xml`.|  
|`sample`|string|Przykład reprezentacji.|  
  
##  <a name="Subscription"></a> Subskrypcja  
 `subscription` Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`Id`|string|Identyfikator zasobu. Jednoznacznie identyfikuje subskrypcji w ramach bieżącego wystąpienia usługi API Management. Wartość jest prawidłowym względnym adresem URL w formacie `subscriptions/{sid}` gdzie `{sid}` jest identyfikatorem subskrypcji. Ta właściwość jest tylko do odczytu.|  
|`ProductId`|string|Identyfikator zasobu produktu subskrybowanego produktu. Wartość jest prawidłowym względnym adresem URL w formacie `products/{pid}` gdzie `{pid}` to identyfikator produktu.|  
|`ProductTitle`|string|Nazwa produktu. Nie może być pusta. Maksymalna długość wynosi 100 znaków.|  
|`ProductDescription`|string|Opis produktu. Nie może być pusta. Może obejmować formatowanie tagów HTML. Maksymalna długość wynosi 1000 znaków.|  
|`ProductDetailsUrl`|string|Względny adres URL do szczegółów produktu.|  
|`state`|string|Stan subskrypcji. Możliwe stany to:<br /><br /> - `0 - suspended` — subskrypcji jest zablokowane i subskrybenta nie można wywołać dowolnych interfejsów API produktu.<br /><br /> - `1 - active` — Subskrypcja jest aktywna.<br /><br /> - `2 - expired` — Subskrypcja osiągnęła daty wygaśnięcia i zostało zdezaktywowane.<br /><br /> - `3 - submitted` — Żądanie subskrypcji przez deweloperów, ale ma nie została jeszcze zatwierdzeniu lub odrzuceniu.<br /><br /> - `4 - rejected` — Żądanie subskrypcji zostało odrzucone przez administratora.<br /><br /> - `5 - cancelled` — Subskrypcja została anulowana przez dewelopera lub administratora.|  
|`DisplayName`|string|Nazwa wyświetlana subskrypcji.|  
|`CreatedDate`|Data i godzina|Data subskrypcja została utworzona, w formacie ISO 8601: `2014-06-24T16:25:00Z`.|  
|`CanBeCancelled`|wartość logiczna|Czy można anulować subskrypcji przez bieżącego użytkownika.|  
|`IsAwaitingApproval`|wartość logiczna|Czy subskrypcji oczekuje na zatwierdzenie.|  
|`StartDate`|Data i godzina|Data rozpoczęcia subskrypcji, w formacie ISO 8601: `2014-06-24T16:25:00Z`.|  
|`ExpirationDate`|Data i godzina|Data wygaśnięcia subskrypcji, w formacie ISO 8601: `2014-06-24T16:25:00Z`.|  
|`NotificationDate`|Data i godzina|Data powiadomień dla subskrypcji, w formacie ISO 8601: `2014-06-24T16:25:00Z`.|  
|`primaryKey`|string|Klucz podstawowy subskrypcji. Maksymalna długość wynosi 256 znaków.|  
|`secondaryKey`|string|Klucz pomocniczy subskrypcji. Maksymalna długość wynosi 256 znaków.|  
|`CanBeRenewed`|wartość logiczna|Czy subskrypcję można odnowić przez bieżącego użytkownika.|  
|`HasExpired`|wartość logiczna|Czy subskrypcja wygasła.|  
|`IsRejected`|wartość logiczna|Czy żądanie subskrypcji zostało odrzucone.|  
|`CancelUrl`|string|Względny adres Url do anulowania subskrypcji.|  
|`RenewUrl`|string|Względny adres Url do odnowienia subskrypcji.|  
  
##  <a name="SubscriptionSummary"></a> Podsumowanie subskrypcji  
 `subscription summary` Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`Id`|string|Identyfikator zasobu. Jednoznacznie identyfikuje subskrypcji w ramach bieżącego wystąpienia usługi API Management. Wartość jest prawidłowym względnym adresem URL w formacie `subscriptions/{sid}` gdzie `{sid}` jest identyfikatorem subskrypcji. Ta właściwość jest tylko do odczytu.|  
|`DisplayName`|string|Nazwa wyświetlana subskrypcji|  
  
##  <a name="UserAccountInfo"></a> Informacje o koncie użytkownika  
 `user account info` Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`FirstName`|string|Imię. Nie może być pusta. Maksymalna długość wynosi 100 znaków.|  
|`LastName`|string|Nazwisko. Nie może być pusta. Maksymalna długość wynosi 100 znaków.|  
|`Email`|string|Adres e-mail. Nie może być pusta i musi być unikatowa w obrębie wystąpienia usługi. Maksymalna długość to 254 znaków.|  
|`Password`|string|Hasło konta użytkownika.|  
|`NameIdentifier`|string|Identyfikator konta, taki sam jak adres e-mail użytkownika.|  
|`ProviderName`|string|Nazwa dostawcy uwierzytelniania.|  
|`IsBasicAccount`|wartość logiczna|Wartość true, jeśli to konto został zarejestrowany za pomocą poczty e-mail i hasło; wartość false, jeśli konto zostało zarejestrowane przy użyciu dostawcy.|  
  
##  <a name="UseSignIn"></a> Logowanie użytkownika  
 `user sign in` Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`Email`|string|Adres e-mail. Nie może być pusta i musi być unikatowa w obrębie wystąpienia usługi. Maksymalna długość to 254 znaków.|  
|`Password`|string|Hasło konta użytkownika.|  
|`ReturnUrl`|string|Adres URL strony, gdy użytkownik kliknął element Zaloguj się.|  
|`RememberMe`|wartość logiczna|Określa, czy można zapisać informacji o bieżącym użytkowniku.|  
|`RegistrationEnabled`|wartość logiczna|Włączenie rejestracji.|  
|`DelegationEnabled`|wartość logiczna|Czy delegowanego logowania jest włączona.|  
|`DelegationUrl`|string|Delegowane znak w adresie url, jeśli włączona.|  
|`SsoSignUpUrl`|string|Pojedynczy adres URL logowania dla użytkownika, jeśli jest obecny.|  
|`AuxServiceUrl`|string|Jeśli bieżący użytkownik jest administratorem, to łącze do wystąpienia usługi w witrynie Azure portal.|  
|`Providers`|Kolekcja [dostawcy](#Provider) jednostek|Dostawcy uwierzytelniania dla tego użytkownika.|  
|`UserRegistrationTerms`|string|Warunki, które użytkownik musi zaakceptować przed zalogowaniem się.|  
|`UserRegistrationTermsEnabled`|wartość logiczna|Czy warunki są włączone.|  
  
##  <a name="UserSignUp"></a> Rejestracja użytkownika  
 `user sign up` Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`PasswordConfirm`|wartość logiczna|Wartość używana przez [rejestracji](api-management-page-controls.md#sign-up)kontroli rejestracji.|  
|`Password`|string|Hasło konta użytkownika.|  
|`PasswordVerdictLevel`|numer|Wartość używana przez [rejestracji](api-management-page-controls.md#sign-up)kontroli rejestracji.|  
|`UserRegistrationTerms`|string|Warunki, które użytkownik musi zaakceptować przed zalogowaniem się.|  
|`UserRegistrationTermsOptions`|numer|Wartość używana przez [rejestracji](api-management-page-controls.md#sign-up)kontroli rejestracji.|  
|`ConsentAccepted`|wartość logiczna|Wartość używana przez [rejestracji](api-management-page-controls.md#sign-up)kontroli rejestracji.|  
|`Email`|string|Adres e-mail. Nie może być pusta i musi być unikatowa w obrębie wystąpienia usługi. Maksymalna długość to 254 znaków.|  
|`FirstName`|string|Imię. Nie może być pusta. Maksymalna długość wynosi 100 znaków.|  
|`LastName`|string|Nazwisko. Nie może być pusta. Maksymalna długość wynosi 100 znaków.|  
|`UserData`|string|Wartość używana przez [rejestracji](api-management-page-controls.md#sign-up) kontroli.|  
|`NameIdentifier`|string|Wartość używana przez [rejestracji](api-management-page-controls.md#sign-up)kontroli rejestracji.|  
|`ProviderName`|string|Nazwa dostawcy uwierzytelniania.|

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [Dostosowywanie portalu dla deweloperów usługi API Management przy użyciu szablonów](api-management-developer-portal-templates.md).
