---
title: Odwołanie do modelu danych szablonu usługi Azure API Management | Dokumentacja firmy Microsoft
description: Więcej informacji na temat jednostki i typu oświadczenia dla najczęściej używane w modelach danych szablonów portalu deweloperów w usłudze Azure API Management.
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
ms.openlocfilehash: 0f27b6b529c2591e37d48e3386190077fc8efc32
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2018
ms.locfileid: "29378160"
---
# <a name="azure-api-management-template-data-model-reference"></a>Odwołanie modelu danych Azure API Management szablonu
W tym temacie opisano jednostki i typu oświadczenia dla najczęściej używane w modelach danych szablonów portalu deweloperów w usłudze Azure API Management.  
  
 Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [dostosowywaniu portalu dla deweloperów interfejsu API zarządzania za pomocą szablonów](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  
  
-   [Interfejs API](#API)  
-   [Podsumowanie interfejsu API](#APISummary)  
-   [Aplikacji](#Application)  
-   [Załącznika](#Attachment)  
-   [Przykładowy kod](#Sample)  
-   [Komentarz](#Comment)  
-   [Filtrowanie](#Filtering)  
-   [Nagłówek](#Header)  
-   [Żądania HTTP](#HTTPRequest)  
-   [Odpowiedź HTTP](#HTTPResponse)  
-   [Problem](#Issue)  
-   [Operacja](#Operation)  
-   [Operacja menu](#Menu)  
-   [Operacja elementu menu](#MenuItem)  
-   [Stronicowania](#Paging)  
-   [Parametr](#Parameter)  
-   [Produktu](#Product)  
-   [Dostawca](#Provider)  
-   [Reprezentacja wartości](#Representation)  
-   [Subskrypcja](#Subscription)  
-   [Podsumowanie subskrypcji](#SubscriptionSummary)  
-   [Informacje o koncie użytkownika](#UserAccountInfo)  
-   [Logowanie użytkownika](#UseSignIn)  
-   [Tworzenia konta użytkownika](#UserSignUp)  
  
##  <a name="API"></a> INTERFEJS API  
 `API` Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|id|ciąg|Identyfikator zasobu. Identyfikuje interfejsu API w ramach bieżącego wystąpienia usługi Zarządzanie interfejsami API. Wartość jest nieprawidłowa względny adres URL w formacie `apis/{id}` gdzie `{id}` jest identyfikatorem interfejsu API. Ta właściwość jest tylko do odczytu.|  
|name|ciąg|Nazwa interfejsu API. Nie może być pusta. Maksymalna długość wynosi 100 znaków.|  
|description|ciąg|Opis interfejsu API. Nie może być pusta. Może obejmować formatowanie tagów HTML. Maksymalna długość wynosi 1000 znaków.|  
|serviceUrl|ciąg|Bezwzględny adres URL usługi wewnętrznej bazy danych, wdrażanie tego interfejsu API.|  
|ścieżka|ciąg|Względny adres URL, który unikatowo identyfikuje ten interfejs API i wszystkie jego ścieżki zasobu w wystąpieniu usługi Zarządzanie interfejsami API. Jest on dołączany do interfejsu API punktu końcowego podstawowy adres URL określony podczas tworzenia wystąpienia usługi do utworzenia publiczny adres URL dla tego interfejsu API.|  
|protokoły|Tablica numer|Opisuje, na które protokoły można wywołać operacji w tym interfejsie API. Dozwolone wartości to `1 - http` i `2 - https`, lub obie.|  
|authenticationSettings|[Ustawienia uwierzytelniania serwera autoryzacji](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#AuthenticationSettings)|Kolekcja ustawień uwierzytelniania zawarte w tym interfejsie API.|  
|subscriptionKeyParameterNames|obiekt|Opcjonalna właściwość, który może służyć do określenia nazwy niestandardowych parametrów zapytania lub nagłówek zawierający klucz subskrypcji. Gdy ta właściwość jest obecny, musi zawierać co najmniej jeden z dwóch następujących właściwości.<br /><br /> `{   "subscriptionKeyParameterNames":   {     "query": “customQueryParameterName",     "header": “customHeaderParameterName"   } }`|  
  
##  <a name="APISummary"></a> Podsumowanie interfejsu API  
 `API summary` Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|id|ciąg|Identyfikator zasobu. Identyfikuje interfejsu API w ramach bieżącego wystąpienia usługi Zarządzanie interfejsami API. Wartość jest nieprawidłowa względny adres URL w formacie `apis/{id}` gdzie `{id}` jest identyfikatorem interfejsu API. Ta właściwość jest tylko do odczytu.|  
|name|ciąg|Nazwa interfejsu API. Nie może być pusta. Maksymalna długość wynosi 100 znaków.|  
|description|ciąg|Opis interfejsu API. Nie może być pusta. Może obejmować formatowanie tagów HTML. Maksymalna długość wynosi 1000 znaków.|  
  
##  <a name="Application"></a> Aplikacji  
 `application` Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|Identyfikator|ciąg|Unikatowy identyfikator aplikacji.|  
|Stanowisko|ciąg|Tytuł aplikacji.|  
|Opis|ciąg|Opis aplikacji.|  
|Url|Identyfikator URI|Identyfikator URI dla aplikacji.|  
|Wersja|ciąg|Informacje o wersji dla aplikacji.|  
|Wymagania|ciąg|Opis wymagań dotyczących aplikacji.|  
|Stan|numer|Bieżący stan aplikacji.<br /><br /> -0 - zarejestrowany<br /><br /> -1 - przesłane<br /><br /> -2 - opublikowane<br /><br /> -3 - odrzucone<br /><br /> -4 - nieopublikowane|  
|RegistrationDate|DateTime|Data i godzina, aplikacja została zarejestrowana.|  
|CategoryId|numer|Do kategorii aplikacji (Finance, rozrywka itp.)|  
|DeveloperId|ciąg|Unikatowy identyfikator dewelopera przesłania aplikacji.|  
|Załączniki|Kolekcja [załącznika](#Attachment) jednostek.|Wszystkie załączniki dla aplikacji, takie jak zrzuty ekranu lub ikony.|  
|Ikona|[Załącznika](#Attachment)|Ikona dla aplikacji.|  
  
##  <a name="Attachment"></a> Załącznika  
 `attachment` Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|UniqueId|ciąg|Unikatowy identyfikator dla załącznika.|  
|Url|ciąg|Adres URL zasobu.|  
|Typ|ciąg|Typ załącznika.|  
|Typ zawartości|ciąg|Typ nośnika załącznika.|  
  
##  <a name="Sample"></a> Przykładowy kod  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|tytuł|ciąg|Nazwa operacji.|  
|fragment kodu|ciąg|Ta właściwość jest przestarzała i nie powinna być używana.|  
|Pędzel|ciąg|Składnia kodu kolorowania szablon ma być używany podczas wyświetlania przykładowy kod. Dozwolone wartości to `plain`, `php`, `java`, `xml`, `objc`, `python`, `ruby`, i `csharp`.|  
|szablon|ciąg|Nazwa tego kod przykładowy szablon.|  
|treść|ciąg|Symbolu zastępczego dla części próbki kodu fragmentu.|  
|method|ciąg|Metoda HTTP operacji.|  
|Schemat|ciąg|Protokół do użycia dla żądania operacji.|  
|ścieżka|ciąg|Ścieżka operacji.|  
|query|ciąg|Przykład zdefiniowanych parametrów ciągu zapytania.|  
|host|ciąg|Adres URL bramy usługi Zarządzanie interfejsami API dla interfejsu API, który zawiera tę operację.|  
|nagłówki|Kolekcja [nagłówka](#Header) jednostek.|Nagłówki dla tej operacji.|  
|parameters|Kolekcja [parametru](#Parameter) jednostek.|Parametry, które są zdefiniowane dla tej operacji.|  
  
##  <a name="Comment"></a> Komentarz  
 `API` Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|Identyfikator|numer|Identyfikator komentarza.|  
|Commenttext —|ciąg|Treść komentarza. Może obejmować HTML.|  
|DeveloperCompany|ciąg|Nazwa firmy dewelopera.|  
|PostedOn|DateTime|Data i godzina komentarz został opublikowany.|  
  
##  <a name="Issue"></a> Problem  
 `issue` Jednostka ma następujące właściwości.  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|Identyfikator|ciąg|Unikatowy identyfikator problemu.|  
|ApiID|ciąg|Identyfikator dla interfejsu API, dla którego ten problem został zgłoszony.|  
|Stanowisko|ciąg|Tytuł problemu.|  
|Opis|ciąg|Opis problemu.|  
|SubscriptionDeveloperName|ciąg|Imię projektanta, który zgłosił problem.|  
|IssueState|ciąg|Bieżący stan problemu. Możliwe wartości to proponowany, Opened, zamknięte.|  
|ReportedOn|DateTime|Data i godzina problem został zgłoszony.|  
|Komentarze|Kolekcja [komentarz](#Comment) jednostek.|Komentarze dotyczące tego problemu.|  
|Załączniki|Kolekcja [załącznika](api-management-template-data-model-reference.md#Attachment) jednostek.|Załączniki do problemu.|  
|Usługi|Kolekcja [interfejsu API](#API) jednostek.|Interfejsy API subskrybowana przez użytkownika, który zgłosić problem.|  
  
##  <a name="Filtering"></a> Filtrowanie  
 `filtering` Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|Wzorce|ciąg|Bieżący termin wyszukiwania; lub `null` Jeśli brak szukanego terminu.|  
|Symbol zastępczy|ciąg|Tekst wyświetlany w polu wyszukiwania, gdy jest brak szukanego terminu określony.|  
  
##  <a name="Header"></a> Nagłówek  
 W tej sekcji opisano `parameter` reprezentacji.  
  
|Właściwość|Opis|Typ|  
|--------------|-----------------|----------|  
|name|ciąg|Nazwa parametru.|  
|description|ciąg|Opis parametru.|  
|wartość|ciąg|Wartość nagłówka.|  
|Właściwość TypeName|ciąg|Typ danych wartości nagłówka.|  
|opcje|ciąg|Opcje.|  
|Wymagane|wartość logiczna|Określa, czy nagłówek jest wymagana.|  
|Tylko do odczytu|wartość logiczna|Określa, czy nagłówek jest tylko do odczytu.|  
  
##  <a name="HTTPRequest"></a> Żądania HTTP  
 W tej sekcji opisano `request` reprezentacji.  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|description|ciąg|Opis żądania operacji.|  
|nagłówki|Tablica [nagłówka](#Header) jednostek.|Nagłówki żądań.|  
|parameters|Tablica [parametru](#Parameter)|Kolekcja parametrów żądania operacji.|  
|oświadczenia|Tablica [reprezentacja](#Representation)|Kolekcja reprezentacje żądania operacji.|  
  
##  <a name="HTTPResponse"></a> Odpowiedź HTTP  
 W tej sekcji opisano `response` reprezentacji.  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|statusCode|Dodatnia liczba całkowita|Kod stanu odpowiedzi operacji.|  
|description|ciąg|Opis odpowiedzi operacji.|  
|oświadczenia|Tablica [reprezentacja](#Representation)|Kolekcja reprezentacje odpowiedzi operacji.|  
  
##  <a name="Operation"></a> Operacja  
 `operation` Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|id|ciąg|Identyfikator zasobu. Identyfikuje operacji w ramach bieżącego wystąpienia usługi Zarządzanie interfejsami API. Wartość jest nieprawidłowa względny adres URL w formacie `apis/{aid}/operations/{id}` gdzie `{aid}` jest identyfikatorem interfejsu API i `{id}` jest identyfikator operacji. Ta właściwość jest tylko do odczytu.|  
|name|ciąg|Nazwa operacji. Nie może być pusta. Maksymalna długość wynosi 100 znaków.|  
|description|ciąg|Opis operacji. Nie może być pusta. Może obejmować formatowanie tagów HTML. Maksymalna długość wynosi 1000 znaków.|  
|Schemat|ciąg|Opisuje, na które protokoły można wywołać operacji w tym interfejsie API. Dozwolone wartości to `http`, `https`, lub obie `http` i `https`.|  
|Obiekt uriTemplate|ciąg|Szablon względny adres URL identyfikujący zasób docelowy dla tej operacji. Mogą zawierać parametrów. Przykład: `customers/{cid}/orders/{oid}/?date={date}`|  
|host|ciąg|Brama adres URL interfejsu API zarządzania obsługującego interfejsu API.|  
|HttpMethod|ciąg|Metoda operacji HTTP.|  
|Żądanie|[Żądania HTTP](#HTTPRequest)|Obiekt zawierający informacje dotyczące żądania.|  
|odpowiedzi|Tablica [odpowiedzi HTTP](#HTTPResponse)|Tablica operacji [odpowiedzi HTTP](#HTTPResponse) jednostek.|  
  
##  <a name="Menu"></a> Operacja menu  
 `operation menu` Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|ApiId|ciąg|Identyfikator bieżącego interfejsu API.|  
|CurrentOperationId|ciąg|Identyfikator bieżącej operacji.|  
|Akcja|ciąg|Typ menu.|  
|Elementy MenuItem|Kolekcja [element menu operacji](#MenuItem) jednostek.|Operacje dla bieżącego interfejsu API.|  
  
##  <a name="MenuItem"></a> Operacja elementu menu  
 `operation menu item` Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|Identyfikator|ciąg|Identyfikator operacji.|  
|Stanowisko|ciąg|Opis operacji.|  
|HttpMethod|ciąg|Metoda Http operacji.|  
  
##  <a name="Paging"></a> Stronicowania  
 `paging` Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|Strona|numer|Numer bieżącej strony.|  
|PageSize|numer|Maksymalna wyników do wyświetlenia na jednej stronie.|  
|TotalItemCount|numer|Liczba elementów do wyświetlenia.|  
|ShowAll|wartość logiczna|Określa, czy Pokaż wszystko powoduje na jednej stronie.|  
|PageCount|numer|Liczba stron wyników.|  
  
##  <a name="Parameter"></a> Parametr  
 W tej sekcji opisano `parameter` reprezentacji.  
  
|Właściwość|Opis|Typ|  
|--------------|-----------------|----------|  
|name|ciąg|Nazwa parametru.|  
|description|ciąg|Opis parametru.|  
|wartość|ciąg|Wartość parametru.|  
|opcje|Tablica ciągów|Wartości zdefiniowane dla wartości parametrów zapytania.|  
|Wymagane|wartość logiczna|Określa, czy parametr jest wymagany.|  
|rodzaj|numer|Określa, czy ten parametr jest parametr path (1) lub parametr querystring (2).|  
|Właściwość TypeName|ciąg|Typ parametru.|  
  
##  <a name="Product"></a> Produktu  
 `product` Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|Identyfikator|ciąg|Identyfikator zasobu. Unikatowy identyfikator produktu w ramach bieżącego wystąpienia usługi Zarządzanie interfejsami API. Wartość jest nieprawidłowa względny adres URL w formacie `products/{pid}` gdzie `{pid}` to identyfikator produktu. Ta właściwość jest tylko do odczytu.|  
|Stanowisko|ciąg|Nazwa produktu. Nie może być pusta. Maksymalna długość wynosi 100 znaków.|  
|Opis|ciąg|Opis produktu. Nie może być pusta. Może obejmować formatowanie tagów HTML. Maksymalna długość wynosi 1000 znaków.|  
|Warunki|ciąg|Warunki użytkowania produktów. Deweloperzy próbuje subskrybować produktu zostanie przedstawiony i musi je zaakceptować przed zakończeniem procesu subskrypcji.|  
|ProductState|numer|Określa, czy produkt jest opublikowana lub nie. Opublikowane produkty są wykrywalny przez deweloperów w portalu dla deweloperów. Produkty opublikowane nie są widoczne tylko dla administratorów.<br /><br /> Dopuszczalne wartości stanu produktu są:<br /><br /> - `0 - Not Published`<br /><br /> - `1 - Published`<br /><br /> - `2 - Deleted`|  
|AllowMultipleSubscriptions|wartość logiczna|Określa, czy użytkownik może mieć wiele subskrypcji do tego produktu w tym samym czasie.|  
|MultipleSubscriptionsCount|numer|Maksymalna liczba subskrypcji do tego produktu użytkownik może mieć jednocześnie.|  
  
##  <a name="Provider"></a> Dostawcy  
 `provider` Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|Właściwości|Słownik ciągu|Właściwości dla tego dostawcy uwierzytelniania.|  
|Typ authenticationType|ciąg|Typ dostawcy. (Azure Active Directory, Facebook logowania, konta Google, Microsoft Account, Twitter).|  
|Podpis|ciąg|Nazwa wyświetlana dostawcy.|  
  
##  <a name="Representation"></a> Reprezentacja wartości  
 W tej sekcji opisano `representation`.  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|Typ zawartości|ciąg|Na przykład określa zarejestrowane lub niestandardowy typ zawartości dla tego reprezentacja `application/xml`.|  
|próbki|ciąg|Przykład reprezentacji.|  
  
##  <a name="Subscription"></a> Subskrypcji  
 `subscription` Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|Identyfikator|ciąg|Identyfikator zasobu. Unikatowy identyfikator subskrypcji w ramach bieżącego wystąpienia usługi Zarządzanie interfejsami API. Wartość jest nieprawidłowa względny adres URL w formacie `subscriptions/{sid}` gdzie `{sid}` jest identyfikator subskrypcji. Ta właściwość jest tylko do odczytu.|  
|Identyfikator produktu|ciąg|Identyfikator zasobu produktu subskrybowanego produktu. Wartość jest nieprawidłowa względny adres URL w formacie `products/{pid}` gdzie `{pid}` to identyfikator produktu.|  
|ProductTitle|ciąg|Nazwa produktu. Nie może być pusta. Maksymalna długość wynosi 100 znaków.|  
|ProductDescription|ciąg|Opis produktu. Nie może być pusta. Może obejmować formatowanie tagów HTML. Maksymalna długość wynosi 1000 znaków.|  
|ProductDetailsUrl|ciąg|Względny adres URL do szczegółów produktu.|  
|state|ciąg|Stan subskrypcji. Dostępne są następujące stany:<br /><br /> - `0 - suspended` — subskrypcji jest zablokowany i subskrybenta nie można wywołać wszystkie interfejsy API produktu.<br /><br /> - `1 - active` — Subskrypcja jest aktywna.<br /><br /> - `2 - expired` — Subskrypcja osiągnęła daty jego wygaśnięcia i została zdezaktywowana.<br /><br /> - `3 - submitted` — Żądanie subskrypcji przez deweloperów, ale ma nie została jeszcze zatwierdzeniu lub odrzuceniu.<br /><br /> - `4 - rejected` — Żądanie subskrypcji zostało odrzucone przez administratora.<br /><br /> - `5 - cancelled` — Subskrypcja została anulowana przez dewelopera lub administratora.|  
|Nazwa wyświetlana|ciąg|Nazwa wyświetlana subskrypcji.|  
|CreatedDate|Data i godzina|Data subskrypcji został utworzony, w formacie ISO 8601: `2014-06-24T16:25:00Z`.|  
|CanBeCancelled|wartość logiczna|Określa, czy subskrypcja mogą zostać anulowane przez bieżącego użytkownika.|  
|IsAwaitingApproval|wartość logiczna|Określa, czy subskrypcja oczekuje na zatwierdzenie.|  
|datą rozpoczęcia|Data i godzina|Data rozpoczęcia subskrypcji, w formacie ISO 8601: `2014-06-24T16:25:00Z`.|  
|ExpirationDate|Data i godzina|Data wygaśnięcia subskrypcji, w formacie ISO 8601: `2014-06-24T16:25:00Z`.|  
|NotificationDate|Data i godzina|Data powiadomienia dla subskrypcji, w formacie ISO 8601: `2014-06-24T16:25:00Z`.|  
|primaryKey|ciąg|Klucz podstawowy subskrypcji. Maksymalna długość wynosi 256 znaków.|  
|secondaryKey|ciąg|Klucz pomocniczy subskrypcji. Maksymalna długość wynosi 256 znaków.|  
|CanBeRenewed|wartość logiczna|Określa, czy subskrypcja mogą być odnawiane przez bieżącego użytkownika.|  
|HasExpired|wartość logiczna|Określa, czy subskrypcja wygasła.|  
|IsRejected|wartość logiczna|Określa, czy żądanie subskrypcji zostało odrzucone.|  
|CancelUrl|ciąg|Względny adres Url do anulowania subskrypcji.|  
|RenewUrl|ciąg|Względny adres Url odnowienia subskrypcji.|  
  
##  <a name="SubscriptionSummary"></a> Podsumowanie subskrypcji  
 `subscription summary` Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|Identyfikator|ciąg|Identyfikator zasobu. Unikatowy identyfikator subskrypcji w ramach bieżącego wystąpienia usługi Zarządzanie interfejsami API. Wartość jest nieprawidłowa względny adres URL w formacie `subscriptions/{sid}` gdzie `{sid}` jest identyfikator subskrypcji. Ta właściwość jest tylko do odczytu.|  
|Nazwa wyświetlana|ciąg|Nazwa wyświetlana subskrypcji|  
  
##  <a name="UserAccountInfo"></a> Informacje o koncie użytkownika  
 `user account info` Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|Imię|ciąg|Imię. Nie może być pusta. Maksymalna długość wynosi 100 znaków.|  
|Nazwisko|ciąg|Nazwisko. Nie może być pusta. Maksymalna długość wynosi 100 znaków.|  
|Email|ciąg|Adres e-mail. Nie może być pusta i musi być unikatowa w ramach wystąpienia usługi. Maksymalna długość to 254 znaków.|  
|Hasło|ciąg|Hasło do konta użytkownika.|  
|NameIdentifier|ciąg|Identyfikator konta, taki sam jak adres e-mail użytkownika.|  
|ProviderName|ciąg|Nazwa dostawcy uwierzytelniania.|  
|IsBasicAccount|wartość logiczna|Wartość true, jeśli konto zostało zarejestrowane za pomocą poczty e-mail i hasło; wartość false, jeśli konto zostało zarejestrowane za pomocą dostawcy.|  
  
##  <a name="UseSignIn"></a> Logowanie użytkownika  
 `user sign in` Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|Email|ciąg|Adres e-mail. Nie może być pusta i musi być unikatowa w ramach wystąpienia usługi. Maksymalna długość to 254 znaków.|  
|Hasło|ciąg|Hasło do konta użytkownika.|  
|ReturnUrl|ciąg|Adres URL strony, gdy użytkownik kliknął Zaloguj się.|  
|RememberMe|wartość logiczna|Określa, czy zapisać informacji o bieżącym użytkowniku.|  
|RegistrationEnabled|wartość logiczna|Określa, czy rejestracja jest włączona.|  
|DelegationEnabled|wartość logiczna|Określa, czy delegowany logowania jest włączona.|  
|DelegationUrl|ciąg|Delegowane znaku w adresie url, jeśli włączone.|  
|SsoSignUpUrl|ciąg|Rejestracja jednokrotna adres URL dla użytkownika, jeśli jest obecny.|  
|AuxServiceUrl|ciąg|Jeśli bieżący użytkownik jest administratorem, to łącze do wystąpienia usługi w portalu Azure.|  
|Dostawcy|Kolekcja [dostawcy](#Provider) jednostek|Dostawców uwierzytelniania dla tego użytkownika.|  
|UserRegistrationTerms|ciąg|Warunki, które użytkownik musi wyrazić zgodę na przed zarejestrowaniem się.|  
|UserRegistrationTermsEnabled|wartość logiczna|Czy warunki są włączone.|  
  
##  <a name="UserSignUp"></a> Tworzenia konta użytkownika  
 `user sign up` Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|PasswordConfirm|wartość logiczna|Wartość używana przez [rejestracji](api-management-page-controls.md#sign-up)kontroli rejestracji.|  
|Hasło|ciąg|Hasło do konta użytkownika.|  
|PasswordVerdictLevel|numer|Wartość używana przez [rejestracji](api-management-page-controls.md#sign-up)kontroli rejestracji.|  
|UserRegistrationTerms|ciąg|Warunki, które użytkownik musi wyrazić zgodę na przed zarejestrowaniem się.|  
|UserRegistrationTermsOptions|numer|Wartość używana przez [rejestracji](api-management-page-controls.md#sign-up)kontroli rejestracji.|  
|ConsentAccepted|wartość logiczna|Wartość używana przez [rejestracji](api-management-page-controls.md#sign-up)kontroli rejestracji.|  
|Email|ciąg|Adres e-mail. Nie może być pusta i musi być unikatowa w ramach wystąpienia usługi. Maksymalna długość to 254 znaków.|  
|Imię|ciąg|Imię. Nie może być pusta. Maksymalna długość wynosi 100 znaków.|  
|Nazwisko|ciąg|Nazwisko. Nie może być pusta. Maksymalna długość wynosi 100 znaków.|  
|Danych użytkownika|ciąg|Wartość używana przez [rejestracji](api-management-page-controls.md#sign-up) formantu.|  
|NameIdentifier|ciąg|Wartość używana przez [rejestracji](api-management-page-controls.md#sign-up)kontroli rejestracji.|  
|ProviderName|ciąg|Nazwa dostawcy uwierzytelniania.|

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [dostosowywaniu portalu dla deweloperów interfejsu API zarządzania za pomocą szablonów](api-management-developer-portal-templates.md).
