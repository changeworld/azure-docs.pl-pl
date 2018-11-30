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
ms.openlocfilehash: 8c21ed737cab98c9136e1c1991997ff3931a4c9d
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/28/2018
ms.locfileid: "52447201"
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
|id|ciąg|Identyfikator zasobu. Jednoznacznie identyfikuje interfejs API w ramach bieżącego wystąpienia usługi API Management. Wartość jest prawidłowym względnym adresem URL w formacie `apis/{id}` gdzie `{id}` jest to identyfikator interfejsu API. Ta właściwość jest tylko do odczytu.|  
|name|ciąg|Nazwa interfejsu API. Nie może być pusta. Maksymalna długość wynosi 100 znaków.|  
|description|ciąg|Opis interfejsu API. Nie może być pusta. Może obejmować formatowanie tagów HTML. Maksymalna długość wynosi 1000 znaków.|  
|serviceUrl|ciąg|Bezwzględny adres URL usługi zaplecza, implementacja tego interfejsu API.|  
|ścieżka|ciąg|Względny adres URL, który unikatowo identyfikuje ten interfejs API i wszystkie jego ścieżki zasobów w ramach wystąpienia usługi API Management. Jest ona dołączana do interfejsu API punktu końcowego podstawowego adresu URL określona podczas tworzenia wystąpienia usługi w celu utworzenia publicznego adresu URL dla tego interfejsu API.|  
|Protokoły|tablica liczb|W tym artykule opisano, na których protokołów można wywołać operacji w tym interfejsie API. Dozwolone wartości to `1 - http` i `2 - https`, lub obu.|  
|authenticationSettings|[Ustawienia uwierzytelniania serwera autoryzacji](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#AuthenticationSettings)|Kolekcja ustawień uwierzytelniania zawarte w tym interfejsie API.|  
|subscriptionKeyParameterNames|obiekt|Opcjonalna właściwość, która może służyć do określania niestandardowych nazw parametrów zapytania i/lub nagłówka zawierający klucz subskrypcji. Gdy ta właściwość jest obecny, musi zawierać co najmniej jeden z dwóch następujących właściwości.<br /><br /> `{   "subscriptionKeyParameterNames":   {     "query": “customQueryParameterName",     "header": “customHeaderParameterName"   } }`|  
  
##  <a name="APISummary"></a> Podsumowanie interfejsu API  
 `API summary` Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|id|ciąg|Identyfikator zasobu. Jednoznacznie identyfikuje interfejs API w ramach bieżącego wystąpienia usługi API Management. Wartość jest prawidłowym względnym adresem URL w formacie `apis/{id}` gdzie `{id}` jest to identyfikator interfejsu API. Ta właściwość jest tylko do odczytu.|  
|name|ciąg|Nazwa interfejsu API. Nie może być pusta. Maksymalna długość wynosi 100 znaków.|  
|description|ciąg|Opis interfejsu API. Nie może być pusta. Może obejmować formatowanie tagów HTML. Maksymalna długość wynosi 1000 znaków.|  
  
##  <a name="Application"></a> Aplikacja  
 `application` Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|Identyfikator|ciąg|Unikatowy identyfikator aplikacji.|  
|Tytuł|ciąg|Tytuł aplikacji.|  
|Opis|ciąg|Opis aplikacji.|  
|Url|Identyfikator URI|Identyfikator URI dla aplikacji.|  
|Wersja|ciąg|Informacje o wersji dla aplikacji.|  
|Wymagania|ciąg|Opis wymagań aplikacji.|  
|Stan|numer|Bieżący stan aplikacji.<br /><br /> -0 - zarejestrowany<br /><br /> -1 - przesłane<br /><br /> -2 - opublikowane<br /><br /> -3 - odrzucone<br /><br /> -4 - nieopublikowane|  
|RegistrationDate|DateTime|Data i godzina, których aplikacja została zarejestrowana.|  
|CategoryId|numer|Do kategorii aplikacji (finanse, rozrywka itp.)|  
|DeveloperId|ciąg|Unikatowy identyfikator dla deweloperów, który przesłany aplikacji.|  
|Załączniki|Kolekcja [załącznika](#Attachment) jednostek.|Wszystkie załączniki dla aplikacji, takie jak zrzuty ekranu lub ikony.|  
|Ikona|[Załącznik](#Attachment)|Ikona dla aplikacji.|  
  
##  <a name="Attachment"></a> Załącznik  
 `attachment` Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|UniqueId|ciąg|Unikatowy identyfikator dla załącznika.|  
|Url|ciąg|Adres URL zasobu.|  
|Typ|ciąg|Typ załącznika.|  
|ContentType|ciąg|Typ nośnika załącznika.|  
  
##  <a name="Sample"></a> Przykładowy kod  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|tytuł|ciąg|Nazwa operacji.|  
|Fragment kodu|ciąg|Ta właściwość jest przestarzała i nie powinna być używana.|  
|Pędzel|ciąg|Składni kodu kolorowania szablon, który będzie używany podczas wyświetlania przykładowy kod. Dozwolone wartości to `plain`, `php`, `java`, `xml`, `objc`, `python`, `ruby`, i `csharp`.|  
|szablon|ciąg|Nazwa tego kodu przykładowego szablonu.|  
|treść|ciąg|Symbol zastępczy dla określonej części przykładowy kod fragmentu kodu.|  
|method|ciąg|Metoda HTTP operacji.|  
|Schemat|ciąg|Protokół do użycia dla żądania operacji.|  
|ścieżka|ciąg|Ścieżka operacji.|  
|query|ciąg|Przykład zdefiniowanych parametrów ciągu zapytania.|  
|host|ciąg|Adres URL bramy usługi API Management dla interfejsu API, który zawiera tę operację.|  
|Nagłówki|Kolekcja [nagłówka](#Header) jednostek.|Nagłówki dla tej operacji.|  
|parameters|Kolekcja [parametru](#Parameter) jednostek.|Parametry, które są zdefiniowane dla tej operacji.|  
  
##  <a name="Comment"></a> Komentarz  
 `API` Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|Identyfikator|numer|Identyfikator komentarza.|  
|Commenttext —|ciąg|Treść komentarza. Może obejmować HTML.|  
|DeveloperCompany|ciąg|Nazwa firmy dewelopera.|  
|PostedOn|DateTime|Data i godzina, którego ten komentarz został opublikowany.|  
  
##  <a name="Issue"></a> Problem  
 `issue` Jednostka ma następujące właściwości.  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|Identyfikator|ciąg|Unikatowy identyfikator problemu.|  
|ApiID|ciąg|Identyfikator interfejsu API, dla którego ten problem został zgłoszony.|  
|Tytuł|ciąg|Tytuł problemu.|  
|Opis|ciąg|Opis problemu.|  
|SubscriptionDeveloperName|ciąg|Imię dewelopera, który zgłosił problem.|  
|IssueState|ciąg|Bieżący stan problemu. Możliwe wartości to proponowane, Opened, zamknięte.|  
|ReportedOn|DateTime|Data i godzina, który został zgłoszony problem.|  
|Komentarze|Kolekcja [komentarz](#Comment) jednostek.|Komentarze dotyczące tego problemu.|  
|Załączniki|Kolekcja [załącznika](api-management-template-data-model-reference.md#Attachment) jednostek.|Załączniki do tego problemu.|  
|Usługi|Kolekcja [API](#API) jednostek.|Interfejsy API subskrybowana przez użytkownika, który zgłosić problem.|  
  
##  <a name="Filtering"></a> Filtrowanie  
 `filtering` Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|Wzorce|ciąg|Bieżący wyszukiwany termin; lub `null` Jeśli brak wyszukiwanego terminu.|  
|Symbol zastępczy|ciąg|Tekst do wyświetlenia w polu wyszukiwania, gdy jest brak wyszukiwanego terminu określony.|  
  
##  <a name="Header"></a> Nagłówek  
 W tej sekcji opisano `parameter` reprezentacji.  
  
|Właściwość|Opis|Typ|  
|--------------|-----------------|----------|  
|name|ciąg|Nazwa parametru.|  
|description|ciąg|Opis parametru.|  
|wartość|ciąg|Wartość nagłówka.|  
|Nazwa typu|ciąg|Typ danych wartości nagłówka.|  
|opcje|ciąg|Opcje.|  
|wymagane|wartość logiczna|Czy nagłówka jest wymagana.|  
|Tylko do odczytu|wartość logiczna|Czy nagłówek jest tylko do odczytu.|  
  
##  <a name="HTTPRequest"></a> Żądanie HTTP  
 W tej sekcji opisano `request` reprezentacji.  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|description|ciąg|Opis żądania operacji.|  
|Nagłówki|Tablica [nagłówka](#Header) jednostek.|Nagłówki żądania.|  
|parameters|Tablica [parametru](#Parameter)|Kolekcja parametrów żądania operacji.|  
|Oświadczenia|Tablica [reprezentacji](#Representation)|Kolekcja reprezentacje żądania operacji.|  
  
##  <a name="HTTPResponse"></a> Odpowiedź HTTP  
 W tej sekcji opisano `response` reprezentacji.  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|statusCode|dodatnia liczba całkowita|Kod statusu odpowiedzi operacji.|  
|description|ciąg|Opis odpowiedzi operacji.|  
|Oświadczenia|Tablica [reprezentacji](#Representation)|Kolekcja reprezentacje odpowiedzi operacji.|  
  
##  <a name="Operation"></a> Operacja  
 `operation` Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|id|ciąg|Identyfikator zasobu. Jednoznacznie identyfikuje operacji w ciągu bieżącego wystąpienia usługi API Management. Wartość jest prawidłowym względnym adresem URL w formacie `apis/{aid}/operations/{id}` gdzie `{aid}` jest to identyfikator interfejsu API i `{id}` jest identyfikatorem operacji. Ta właściwość jest tylko do odczytu.|  
|name|ciąg|Nazwa operacji. Nie może być pusta. Maksymalna długość wynosi 100 znaków.|  
|description|ciąg|Opis operacji. Nie może być pusta. Może obejmować formatowanie tagów HTML. Maksymalna długość wynosi 1000 znaków.|  
|Schemat|ciąg|W tym artykule opisano, na których protokołów można wywołać operacji w tym interfejsie API. Dozwolone wartości to `http`, `https`, i / lub `http` i `https`.|  
|UriTemplate|ciąg|Względna szablon adres URL identyfikujący zasób docelowy dla tej operacji. Może zawierać parametrów. Przykład: `customers/{cid}/orders/{oid}/?date={date}`|  
|host|ciąg|URL bramy usługi API Management udostępnia interfejs API.|  
|HttpMethod|ciąg|Metoda operacji HTTP.|  
|żądanie|[Żądanie HTTP](#HTTPRequest)|Jednostki zawierające szczegóły żądania.|  
|Odpowiedzi|Tablica [odpowiedzi HTTP](#HTTPResponse)|Szereg operacji [odpowiedź HTTP](#HTTPResponse) jednostek.|  
  
##  <a name="Menu"></a> Operacja menu  
 `operation menu` Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|ApiId|ciąg|Identyfikator bieżącego interfejsu API.|  
|CurrentOperationId|ciąg|Identyfikator bieżącej operacji.|  
|Akcja|ciąg|Typ menu.|  
|Vlastnost MenuItems|Kolekcja [element menu operacji](#MenuItem) jednostek.|Operacje dla bieżącego interfejsu API.|  
  
##  <a name="MenuItem"></a> Operacja elementu menu.  
 `operation menu item` Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|Identyfikator|ciąg|Identyfikator operacji.|  
|Tytuł|ciąg|Opis operacji.|  
|HttpMethod|ciąg|Metoda Http operacji.|  
  
##  <a name="Paging"></a> Stronicowania  
 `paging` Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|Strona|numer|Numer bieżącej strony.|  
|PageSize|numer|Maksymalna liczba wyników do wyświetlenia na jednej stronie.|  
|TotalItemCount|numer|Liczba elementów do wyświetlenia.|  
|ShowAll|wartość logiczna|Określa, czy Pokaż wszystkie wyniki na jednej stronie.|  
|PageCount|numer|Liczba stron wyników.|  
  
##  <a name="Parameter"></a> Parametr  
 W tej sekcji opisano `parameter` reprezentacji.  
  
|Właściwość|Opis|Typ|  
|--------------|-----------------|----------|  
|name|ciąg|Nazwa parametru.|  
|description|ciąg|Opis parametru.|  
|wartość|ciąg|Wartość parametru.|  
|opcje|tablica ciągów|Wartości zdefiniowane dla wartości parametrów zapytania.|  
|wymagane|wartość logiczna|Określa, czy parametr jest wymagany.|  
|rodzaj|numer|Czy ten parametr jest to parametr ścieżki (1) lub parametr querystring (2).|  
|Nazwa typu|ciąg|Typ parametru.|  
  
##  <a name="Product"></a> Produkt  
 `product` Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|Identyfikator|ciąg|Identyfikator zasobu. Jednoznacznie identyfikuje produkt w ciągu bieżącego wystąpienia usługi API Management. Wartość jest prawidłowym względnym adresem URL w formacie `products/{pid}` gdzie `{pid}` to identyfikator produktu. Ta właściwość jest tylko do odczytu.|  
|Tytuł|ciąg|Nazwa produktu. Nie może być pusta. Maksymalna długość wynosi 100 znaków.|  
|Opis|ciąg|Opis produktu. Nie może być pusta. Może obejmować formatowanie tagów HTML. Maksymalna długość wynosi 1000 znaków.|  
|Warunki|ciąg|Produkt z warunkami użytkowania. Deweloperzy próbuje subskrybować produkt będą prezentowane i trzeba je zaakceptować przed ukończeniem procesu subskrypcji.|  
|ProductState|numer|Określa, czy produkt jest publikowany czy nie. Opublikowane produkty są wykrywane przez deweloperów w portalu dla deweloperów. Opublikowane przez inne niż produkty są widoczne tylko dla administratorów.<br /><br /> Dopuszczalne wartości dla stanu produktu są:<br /><br /> - `0 - Not Published`<br /><br /> - `1 - Published`<br /><br /> - `2 - Deleted`|  
|AllowMultipleSubscriptions|wartość logiczna|Określa, czy użytkownik może mieć wiele subskrypcji tego produktu, w tym samym czasie.|  
|MultipleSubscriptionsCount|numer|Maksymalna liczba subskrypcji dla tego produktu użytkownik może mieć w tym samym czasie.|  
  
##  <a name="Provider"></a> Dostawcy  
 `provider` Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|Właściwości|Słownik ciągu|Właściwości dla tego dostawcy uwierzytelniania.|  
|AuthenticationType|ciąg|Typ dostawcy. (Usługa azure Active Directory, logowania do usługi Facebook, konta Google, Microsoft Account, Twitter).|  
|Podpis|ciąg|Nazwa wyświetlana dostawcy.|  
  
##  <a name="Representation"></a> Reprezentacja  
 W tej sekcji opisano `representation`.  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|contentType|ciąg|Na przykład określa zarejestrowane lub niestandardowy typ zawartości to reprezentacja `application/xml`.|  
|Próbki|ciąg|Przykład reprezentacji.|  
  
##  <a name="Subscription"></a> Subskrypcja  
 `subscription` Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|Identyfikator|ciąg|Identyfikator zasobu. Jednoznacznie identyfikuje subskrypcji w ramach bieżącego wystąpienia usługi API Management. Wartość jest prawidłowym względnym adresem URL w formacie `subscriptions/{sid}` gdzie `{sid}` jest identyfikatorem subskrypcji. Ta właściwość jest tylko do odczytu.|  
|Identyfikator produktu|ciąg|Identyfikator zasobu produktu subskrybowanego produktu. Wartość jest prawidłowym względnym adresem URL w formacie `products/{pid}` gdzie `{pid}` to identyfikator produktu.|  
|ProductTitle|ciąg|Nazwa produktu. Nie może być pusta. Maksymalna długość wynosi 100 znaków.|  
|ProductDescription|ciąg|Opis produktu. Nie może być pusta. Może obejmować formatowanie tagów HTML. Maksymalna długość wynosi 1000 znaków.|  
|ProductDetailsUrl|ciąg|Względny adres URL do szczegółów produktu.|  
|state|ciąg|Stan subskrypcji. Możliwe stany to:<br /><br /> - `0 - suspended` — subskrypcji jest zablokowane i subskrybenta nie można wywołać dowolnych interfejsów API produktu.<br /><br /> - `1 - active` — Subskrypcja jest aktywna.<br /><br /> - `2 - expired` — Subskrypcja osiągnęła daty wygaśnięcia i zostało zdezaktywowane.<br /><br /> - `3 - submitted` — Żądanie subskrypcji przez deweloperów, ale ma nie została jeszcze zatwierdzeniu lub odrzuceniu.<br /><br /> - `4 - rejected` — Żądanie subskrypcji zostało odrzucone przez administratora.<br /><br /> - `5 - cancelled` — Subskrypcja została anulowana przez dewelopera lub administratora.|  
|Nazwa wyświetlana|ciąg|Nazwa wyświetlana subskrypcji.|  
|CreatedDate|Data i godzina|Data subskrypcja została utworzona, w formacie ISO 8601: `2014-06-24T16:25:00Z`.|  
|CanBeCancelled|wartość logiczna|Czy można anulować subskrypcji przez bieżącego użytkownika.|  
|IsAwaitingApproval|wartość logiczna|Czy subskrypcji oczekuje na zatwierdzenie.|  
|Oprócz parametru startDate|Data i godzina|Data rozpoczęcia subskrypcji, w formacie ISO 8601: `2014-06-24T16:25:00Z`.|  
|ExpirationDate|Data i godzina|Data wygaśnięcia subskrypcji, w formacie ISO 8601: `2014-06-24T16:25:00Z`.|  
|NotificationDate|Data i godzina|Data powiadomień dla subskrypcji, w formacie ISO 8601: `2014-06-24T16:25:00Z`.|  
|primaryKey|ciąg|Klucz podstawowy subskrypcji. Maksymalna długość wynosi 256 znaków.|  
|secondaryKey|ciąg|Klucz pomocniczy subskrypcji. Maksymalna długość wynosi 256 znaków.|  
|CanBeRenewed|wartość logiczna|Czy subskrypcję można odnowić przez bieżącego użytkownika.|  
|HasExpired|wartość logiczna|Czy subskrypcja wygasła.|  
|IsRejected|wartość logiczna|Czy żądanie subskrypcji zostało odrzucone.|  
|CancelUrl|ciąg|Względny adres Url do anulowania subskrypcji.|  
|RenewUrl|ciąg|Względny adres Url do odnowienia subskrypcji.|  
  
##  <a name="SubscriptionSummary"></a> Podsumowanie subskrypcji  
 `subscription summary` Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|Identyfikator|ciąg|Identyfikator zasobu. Jednoznacznie identyfikuje subskrypcji w ramach bieżącego wystąpienia usługi API Management. Wartość jest prawidłowym względnym adresem URL w formacie `subscriptions/{sid}` gdzie `{sid}` jest identyfikatorem subskrypcji. Ta właściwość jest tylko do odczytu.|  
|Nazwa wyświetlana|ciąg|Nazwa wyświetlana subskrypcji|  
  
##  <a name="UserAccountInfo"></a> Informacje o koncie użytkownika  
 `user account info` Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|FirstName|ciąg|Imię. Nie może być pusta. Maksymalna długość wynosi 100 znaków.|  
|LastName|ciąg|Nazwisko. Nie może być pusta. Maksymalna długość wynosi 100 znaków.|  
|Email|ciąg|Adres e-mail. Nie może być pusta i musi być unikatowa w obrębie wystąpienia usługi. Maksymalna długość to 254 znaków.|  
|Hasło|ciąg|Hasło konta użytkownika.|  
|NameIdentifier|ciąg|Identyfikator konta, taki sam jak adres e-mail użytkownika.|  
|ProviderName|ciąg|Nazwa dostawcy uwierzytelniania.|  
|IsBasicAccount|wartość logiczna|Wartość true, jeśli to konto został zarejestrowany za pomocą poczty e-mail i hasło; wartość false, jeśli konto zostało zarejestrowane przy użyciu dostawcy.|  
  
##  <a name="UseSignIn"></a> Logowanie użytkownika  
 `user sign in` Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|Email|ciąg|Adres e-mail. Nie może być pusta i musi być unikatowa w obrębie wystąpienia usługi. Maksymalna długość to 254 znaków.|  
|Hasło|ciąg|Hasło konta użytkownika.|  
|ReturnUrl|ciąg|Adres URL strony, gdy użytkownik kliknął element Zaloguj się.|  
|RememberMe|wartość logiczna|Określa, czy można zapisać informacji o bieżącym użytkowniku.|  
|RegistrationEnabled|wartość logiczna|Włączenie rejestracji.|  
|DelegationEnabled|wartość logiczna|Czy delegowanego logowania jest włączona.|  
|DelegationUrl|ciąg|Delegowane znak w adresie url, jeśli włączona.|  
|SsoSignUpUrl|ciąg|Pojedynczy adres URL logowania dla użytkownika, jeśli jest obecny.|  
|AuxServiceUrl|ciąg|Jeśli bieżący użytkownik jest administratorem, to łącze do wystąpienia usługi w witrynie Azure portal.|  
|Dostawcy|Kolekcja [dostawcy](#Provider) jednostek|Dostawcy uwierzytelniania dla tego użytkownika.|  
|UserRegistrationTerms|ciąg|Warunki, które użytkownik musi zaakceptować przed zalogowaniem się.|  
|UserRegistrationTermsEnabled|wartość logiczna|Czy warunki są włączone.|  
  
##  <a name="UserSignUp"></a> Rejestracja użytkownika  
 `user sign up` Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|PasswordConfirm|wartość logiczna|Wartość używana przez [rejestracji](api-management-page-controls.md#sign-up)kontroli rejestracji.|  
|Hasło|ciąg|Hasło konta użytkownika.|  
|PasswordVerdictLevel|numer|Wartość używana przez [rejestracji](api-management-page-controls.md#sign-up)kontroli rejestracji.|  
|UserRegistrationTerms|ciąg|Warunki, które użytkownik musi zaakceptować przed zalogowaniem się.|  
|UserRegistrationTermsOptions|numer|Wartość używana przez [rejestracji](api-management-page-controls.md#sign-up)kontroli rejestracji.|  
|ConsentAccepted|wartość logiczna|Wartość używana przez [rejestracji](api-management-page-controls.md#sign-up)kontroli rejestracji.|  
|Email|ciąg|Adres e-mail. Nie może być pusta i musi być unikatowa w obrębie wystąpienia usługi. Maksymalna długość to 254 znaków.|  
|FirstName|ciąg|Imię. Nie może być pusta. Maksymalna długość wynosi 100 znaków.|  
|LastName|ciąg|Nazwisko. Nie może być pusta. Maksymalna długość wynosi 100 znaków.|  
|danych użytkownika|ciąg|Wartość używana przez [rejestracji](api-management-page-controls.md#sign-up) kontroli.|  
|NameIdentifier|ciąg|Wartość używana przez [rejestracji](api-management-page-controls.md#sign-up)kontroli rejestracji.|  
|ProviderName|ciąg|Nazwa dostawcy uwierzytelniania.|

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [Dostosowywanie portalu dla deweloperów usługi API Management przy użyciu szablonów](api-management-developer-portal-templates.md).
