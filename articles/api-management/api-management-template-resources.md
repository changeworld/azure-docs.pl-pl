---
title: Zasoby szablonów usługi Azure API Management | Dokumenty firmy Microsoft
description: Dowiedz się więcej o typach zasobów dostępnych do użycia w szablonach portalu dla deweloperów w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 51a1b4c6-a9fd-4524-9e0e-03a9800c3e94
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: c3b6123c63bf530463379a175745ef86baf2c5a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249505"
---
# <a name="azure-api-management-template-resources"></a>Zasoby szablonów usługi Azure API Management
Usługa Azure API Management udostępnia następujące typy zasobów do użycia w szablonach portalu dla deweloperów.  
  
-   [Zasoby ciągów](#strings)  
  
-   [Zasoby glifów](#glyphs)  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="string-resources"></a><a name="strings"></a>Zasoby ciągów  
 Usługa API Management zapewnia kompleksowy zestaw zasobów ciągów do użycia w portalu dewelopera. Te zasoby są zlokalizowane we wszystkich językach obsługiwanych przez zarządzanie interfejsem API. Domyślny zestaw szablonów używa tych zasobów dla nagłówków stron, etykiet i wszelkich stałych ciągów, które są wyświetlane w portalu dewelopera. Aby użyć zasobu ciągu w szablonach, podaj prefiks ciągu zasobu, po którym następuje nazwa ciągu, jak pokazano w poniższym przykładzie.  
  
```  
{% localized "Prefix|Name" %}  
  
```  
  
 Poniższy przykład pochodzi z szablonu listy produktów i wyświetla **produkty** u góry strony.  
  
```  
<h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>  
  
```  
  
Obsługiwane są następujące opcje lokalizacji:

| Ustawienia regionalne    | Język               |
|-----------|------------------------|
| „pl”      | "Bardzo się podaj.              |
| "cs"      | "Čeština"              |
| "de"      | "Deutsch"              |
| "es"      | "Español"              |
| "fr"      | "Français"             |
| "hu"      | "Magyar"               |
| "To"      | "Italiano"             |
| "ja-JP"   | "Japonki"                |
| "ko"      | "Bardzo"                |
| "nl"      | "Nederlands"           |
| "pl"      | "Polski"               |
| "pt-br"   | "Português (Brazylia)"   |
| "pt-pt"   | "Português (Portugalia)" |
| "ru"      | "Русский"              |
| "sv"      | "Svenska"              |
| "tr"      | "Türkçe"               |
| "zh-hans" | "中田田)"           |
| "zh-hant" | "中田田)"           |

 Zapoznaj się z poniższymi tabelami dla zasobów ciągów dostępnych do użycia w szablonach portalu dewelopera. Użyj nazwy tabeli jako prefiksu dla zasobów ciągu w tej tabeli.  
  
-   [ApisSatry](#ApisStrings)  
  
-   [ApplicationListStrings (Listy aplikacji)](#ApplicationListStrings)  
  
-   [AppDetailsStrings (Szczegóły aplikacji)](#AppDetailsStrings)  
  
-   [AppStringi](#AppStrings)  
  
-   [Wspólne Źródła](#CommonResources)  
  
-   [Znaki wspólne](#CommonStrings)  
  
-   [Dokumentacja](#Documentation)  
  
-   [Strony błędówSieci](#ErrorPageStrings)  
  
-   [IssuesStrings (Numery problemów)](#IssuesStrings)  
  
-   [NotFoundStrings (NiefoundStrings)](#NotFoundStrings)  
  
-   [Szczegóły produktuStrzanie](#ProductDetailsStrings)  
  
-   [ProduktySci](#ProductsStrings)  
  
-   [ProviderInfoStrings](#ProviderInfoStrings)  
  
-   [Źródła signin](#SigninResources)  
  
-   [SigninStrueds (Znaki wskazujące)](#SigninStrings)  
  
-   [Rejestracja Szyldy](#SignupStrings)  
  
-   [SubscriptionListStrings](#SubscriptionListStrings)  
  
-   [Subscriptionstrings](#SubscriptionStrings)  
  
-   [AktualizacjaProfileStruje](#UpdateProfileStrings)  
  
-   [UserProfile](#UserProfile)  
  
###  <a name="apisstrings"></a><a name="ApisStrings"></a>ApisSatry  
  
|Nazwa|Tekst|  
|----------|----------|  
|StronaTitleApis|Interfejsy API|  
  
###  <a name="appdetailsstrings"></a><a name="AppDetailsStrings"></a>AppDetailsStrings (Szczegóły aplikacji)  
  
|Nazwa|Tekst|  
|----------|----------|  
|WebApplicationsDetailsTitle|Podgląd aplikacji|  
|WebApplicationsRequirementsHeader|Wymagania|  
|Aplikacja internetowaScreenshotAlt|Zrzut ekranu|  
|WebApplicationsScreenshotsHeader|Zrzuty ekranu|  
  
###  <a name="applicationliststrings"></a><a name="ApplicationListStrings"></a>ApplicationListStrings (Listy aplikacji)  
  
|Nazwa|Tekst|  
|----------|----------|  
|WebDevelopersAppDeleteConfirmation|Czy na pewno chcesz usunąć aplikację?|  
|WebDevelopersAppNotPublished|Nie opublikowano|  
|WebDevelopersAppNotSubmitted|Nie przesłane|  
|WebDevelopersAppTableCategoryHeader (WebDevelopersAppTableCategoryHeader)|Kategoria|  
|WebDevelopersAppNameHeader|Nazwa|  
|WebDevelopersAppTableStateHeader|Stan|  
|WebDevelopersEditLink|Edytuj|  
|WebDevelopersRegisterAppLink|Rejestrowanie aplikacji|  
|WebDevelopersRemoveLink|Remove|  
|WebDevelopersSubmitLink|Submit|  
|WebDevelopersYourApplicationsHeader|Twoje aplikacje|  
  
###  <a name="appstrings"></a><a name="AppStrings"></a>AppStringi  
  
|Nazwa|Tekst|  
|----------|----------|  
|Strona internetowaApplicationsHeader|Aplikacje|  
  
###  <a name="commonresources"></a><a name="CommonResources"></a>Wspólne Źródła  
  
|Nazwa|Tekst|  
|----------|----------|  
|NoItemsToWyświetlać|Nie znaleziono wyników.|  
|Ogólna ekspceptacjaSszewanie|Coś jest nie tak. Może to być tymczasowy błąd lub błąd. Spróbuj ponownie.|  
|GeneralJsonExceptionMessage|Coś jest nie tak. Może to być tymczasowy błąd lub błąd. Załaduj ponownie stronę i spróbuj ponownie.|  
|ConfirmationMessageUnsavedZmienia w kształcie|Istnieją pewne niezapisane zmiany. Czy na pewno chcesz anulować i odrzucić zmiany?|  
|Usługa AzureActiveDirectory|Usługa Azure Active Directory|  
|HttpLargeRequestMessage|Obiekt żądania http jest zbyt duży.|  
  
###  <a name="commonstrings"></a><a name="CommonStrings"></a>Znaki wspólne  
  
|Nazwa|Tekst|  
|----------|----------|  
|PrzyciskLabelCancel|Cancel|  
|PrzyciskLabelSave|Zapisz|  
|Ogólna ekspceptacjaSszewanie|Coś jest nie tak. Może to być tymczasowy błąd lub błąd. Spróbuj ponownie.|  
|NoItemsToWyświetlać|Nie ma żadnych elementów do wyświetlenia.|  
|PagerButtonLabelFirst|First|  
|PagerButtonLabelLast|Last|  
|PagerButtonLabelNastępny|Następne kroki|  
|PagerButtonLabelPoprzednie|Poprzedni|  
|PagerLabelPageNOfM|Strona {0} z {1}|  
|PasswordTooShort (Hasło)|Hasło jest za krótkie|  
|Poczta elektroniczna Hasło|Nie używaj adresu e-mail jako hasła|  
|Nazwa użytkownika PasswordSameAsUserName|Hasło nie może zawierać nazwy użytkownika|  
|PasswordTwoCharacterClasses|Używanie różnych klas znaków|  
|PasswordTooManyRepetitions (HasłaTooManyRepetitions)|Zbyt wiele powtórzeń|  
|Funkcja PasswordSequenceFound|Twoje hasło zawiera sekwencje|  
|Rozmiar strony PagerLabelPageSize|Rozmiar strony|  
|CurtainLabelLoading (KurtynaLabelLoading)|Trwa ładowanie...|  
|TablePlaceholderNothingToDisplay|Brak danych dla wybranego okresu i zakresu|  
|ButtonLabelKrówkowanie|Zamykanie|  
  
###  <a name="documentation"></a><a name="Documentation"></a>Dokumentacji  
  
|Nazwa|Tekst|  
|----------|----------|  
|WebDocumentationInvalidHeaderErrorMessage|Nieprawidłowy{0}nagłówek '|  
|WebDocumentationInvalidRequestErrorMessage|Nieprawidłowy adres URL żądania|  
|TextboxLabelAccessToken|Token dostępu *|  
|Rozwijana pozycjowaPrimaryKeyFormat|Podstawowa-{0}|  
|Rozwijana oczekszeszesondaryKeyFormat|Drugorzędne{0}|  
|WebDocumentationSubscriptionKeyText|Klucz subskrypcji|  
|WebDocumentationTemplatesAddHeaders|Dodawanie wymaganych nagłówków HTTP|  
|WebDocumentationTemplatesBasicAuthSample|Próbka autoryzacji podstawowej|  
|WebDocumentationTemplatesCurlForBasicAuth|do podstawowego użycia autoryzacji: --user {username}:{password}|  
|WebDocumentationTemplatesCurlValuesForPath|Określanie wartości parametrów ścieżki (wyświetlanych jako {...}), klucza subskrypcji i wartości parametrów kwerendy|  
|WebDocumentationTemplatesDeveloperKey|Określanie klucza subskrypcji|  
|WebDocumentationTemplatesJavaApache|W tym przykładzie użyto klienta APACHE HTTP ze składników HTTP (http://hc.apache.org/httpcomponents-client-ga/)|  
|WebDocumentationTemplatesOptionalParams|W razie potrzeby określanie wartości parametrów opcjonalnych|  
|WebDocumentationTemplatesPhpPackage|W tym przykładzie użyto pakietu HTTP_Request2. (Aby uzyskać więcej informacji:https://pear.php.net/package/HTTP_Request2)|  
|WebDocumentationTemplatesPythonValuesForPath|Określ wartości parametrów ścieżki (pokazane jako {...}) i w razie potrzeby treść żądania|  
|WebDocumentationTemplatesRequestBody|Określ treść żądania|  
|WebDocumentationTemplatesRequiredParams|Określ wartości dla następujących wymaganych parametrów|  
|WebDocumentationTemplatesValuesForPath|Określ wartości parametrów ścieżki (pokazane jako {...})|  
|OAuth2AuthorizationEndpointDescription|Punkt końcowy autoryzacji jest używany do interakcji z właścicielem zasobu i uzyskania dotacji autoryzacji.|  
|OAuth2AuthorizationEndpointName|Punkt końcowy autoryzacji|  
|OAuth2TokenEndpointDescription|Punkt końcowy tokenu jest używany przez klienta w celu uzyskania tokenu dostępu przez przedstawienie jego autoryzacji dotacji lub odświeżania tokenu.|  
|Nazwa punktu OAuth2TokenEndpoint|Punkt końcowy tokenu|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Description|<p\> Klient inicjuje przepływ, kierując agenta użytkownika właściciela zasobu do punktu końcowego autoryzacji.  Klient zawiera identyfikator klienta, żądany zakres, stan lokalny i identyfikator URI przekierowania, do którego serwer autoryzacji wyśle agenta użytkownika z powrotem po udzieleniu dostępu (lub odmowie).     </p\> <p\> Serwer autoryzacji uwierzytelnia właściciela zasobu (za pośrednictwem agenta użytkownika) i ustala, czy właściciel zasobu udziela lub odrzuca żądanie dostępu klienta.     </p\> <p\> Przy założeniu, że właściciel zasobu udziela dostępu, serwer autoryzacji przekierowuje agenta użytkownika z powrotem do klienta przy użyciu identyfikatora URI przekierowania podanego wcześniej (w żądaniu lub podczas rejestracji klienta).  Identyfikator URI przekierowania zawiera kod autoryzacji i każdy stan lokalny dostarczony przez klienta wcześniej.     </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ErrorDescription|<p\> Jeśli użytkownik odrzuci żądanie dostępu, jeśli żądanie jest nieprawidłowe, klient zostanie poinformowany o tym, używając następujących parametrów dodanych do przekierowania: </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Name|Wniosek o autoryzację|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_RequestDescription|<p\> Aplikacja kliencka musi wysłać użytkownika do punktu końcowego autoryzacji w celu zainicjowania procesu OAuth.          W punkcie końcowym autoryzacji użytkownik uwierzytelnia, a następnie udziela lub odmawia dostępu do aplikacji.     </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ResponseDescription|<p\> Przy założeniu, że właściciel zasobu udziela dostępu, serwer autoryzacji przekierowuje agenta użytkownika z powrotem do klienta przy użyciu identyfikatora URI przekierowania dostarczonego wcześniej (w żądaniu lub podczas rejestracji klienta).  Identyfikator URI przekierowania zawiera kod autoryzacji i każdy stan lokalny dostarczony przez klienta wcześniej. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_Description|<p\> Klient żąda tokenu dostępu z punktu końcowego tokenu serwera autoryzacji, dołączając kod autoryzacji odebrany w poprzednim kroku.  Podczas składania żądania klient uwierzytelnia się za pomocą serwera autoryzacji.  Klient zawiera identyfikator URI przekierowania używany do uzyskania kodu autoryzacji do weryfikacji. </p\> <p\> Serwer autoryzacji uwierzytelnia klienta, sprawdza poprawność kodu autoryzacji i zapewnia, że odebrany identyfikator URI przekierowania jest zgodny z identyfikatorem URI używanym do przekierowywania klienta w kroku (C).  Jeśli jest prawidłowy, serwer autoryzacji odpowiada z powrotem tokenem dostępu i, opcjonalnie, token odświeżania. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ErrorDescription|<p\> Jeśli uwierzytelnianie klienta żądania nie powiodło się lub jest nieprawidłowe, serwer autoryzacji odpowiada kodem stanu HTTP 400 (nieprawidłowe żądanie) (chyba że określono inaczej) i zawiera następujące parametry z odpowiedzią. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_RequestDescription|<p\> Klient wysyła żądanie do punktu końcowego tokenu, wysyłając następujące parametry przy użyciu formatu "application/x-form-urlencoded" z kodowaniem znaków UTF-8 w treści jednostki żądania HTTP. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ResponseDescription|<p\> Serwer autoryzacji wystawia token dostępu i opcjonalny token odświeżania i konstruuje odpowiedź, dodając następujące parametry do treści jednostki odpowiedzi HTTP za pomocą kodu stanu 200 (OK). </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_Description|<p\> Klient uwierzytelnia się za pomocą serwera autoryzacji i żąda tokenu dostępu z punktu końcowego tokenu. </p\> <p\> Serwer autoryzacji uwierzytelnia klienta, a jeśli jest prawidłowy, wystawia token dostępu. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ErrorDescription|<p\> Jeśli żądanie nie powiodło się uwierzytelnienie klienta lub jest nieprawidłowe, serwer autoryzacji odpowiada kodem stanu HTTP 400 (bad request) (chyba że określono inaczej) i zawiera następujące parametry z odpowiedzią. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_RequestDescription|<p\> Klient sprawia, że żądanie do punktu końcowego tokenu, dodając następujące parametry przy użyciu formatu "application/x-form-urlencoded" z kodowaniem znaków UTF-8 w treści jednostki żądania HTTP. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ResponseDescription|<p\> Jeśli żądanie tokenu dostępu jest prawidłowe i autoryzowane, serwer autoryzacji wystawia token dostępu i opcjonalny token odświeżania i konstruuje odpowiedź, dodając następujące parametry do treści jednostki odpowiedzi HTTP za pomocą kodu stanu 200 (OK). </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_Description|<p\> Klient inicjuje przepływ, kierując agenta użytkownika właściciela zasobu do punktu końcowego autoryzacji.  Klient zawiera identyfikator klienta, żądany zakres, stan lokalny i identyfikator URI przekierowania, do którego serwer autoryzacji wyśle agenta użytkownika z powrotem po udzieleniu dostępu (lub odmowie). </p\> <p\> Serwer autoryzacji uwierzytelnia właściciela zasobu (za pośrednictwem agenta użytkownika) i ustala, czy właściciel zasobu udziela lub odrzuca żądanie dostępu klienta. </p\> <p\> Przy założeniu, że właściciel zasobu udziela dostępu, serwer autoryzacji przekierowuje agenta użytkownika z powrotem do klienta przy użyciu identyfikatora URI przekierowania dostarczonego wcześniej.  Identyfikator URI przekierowania zawiera token dostępu w fragmencie identyfikatora URI. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ErrorDescription|<p\> Jeśli właściciel zasobu odrzuca żądanie dostępu lub jeśli żądanie nie powiedzie się z przyczyn innych niż brakujący lub nieprawidłowy identyfikator URI przekierowania, serwer autoryzacji informuje klienta, dodając następujące parametry do składnika fragmentu identyfikatora URI przekierowania przy użyciu formatu "application/x-www-form-urlencoded". </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_RequestDescription|<p\> Aplikacja kliencka musi wysłać użytkownika do punktu końcowego autoryzacji w celu zainicjowania procesu OAuth.      W punkcie końcowym autoryzacji użytkownik uwierzytelnia, a następnie udziela lub odmawia dostępu do aplikacji. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ResponseDescription|<p\> Jeśli właściciel zasobu udzieli żądania dostępu, serwer autoryzacji wystawia token dostępu i dostarcza go do klienta, dodając następujące parametry do składnika fragmentu identyfikatora URI przekierowania przy użyciu formatu "application/x-www-form-urlencoded". </p\>|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Description|Przepływ kodu autoryzacji jest zoptymalizowany dla klientów zdolnych do zachowania poufności swoich poświadczeń (np. aplikacji serwera www zaimplementowanych przy użyciu PHP, Java, Python, Ruby, ASP.NET itp.).|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Name|Udzielenie kodu autoryzacyjnego|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Description|Przepływ poświadczeń klienta jest odpowiedni w przypadkach, gdy klient (aplikacja) żąda dostępu do chronionych zasobów pod jego kontrolą. Klient jest uważany za właściciela zasobu, więc nie jest wymagana interakcja z użytkownikiem końcowym.|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Name|Dotacja poświadczeń klienta|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Description|Przepływ niejawny jest zoptymalizowany dla klientów niezdolnych do zachowania poufności ich poświadczeń, o których wiadomo, że obsługuje określony identyfikator URI przekierowania. Te klienci są zazwyczaj implementowane w przeglądarce przy użyciu języka skryptów, takich jak JavaScript.|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Name|Dotacja niejawna|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Description|Przepływ poświadczeń hasła właściciela zasobu jest odpowiedni w przypadkach, gdy właściciel zasobu ma relację zaufania z klientem (aplikacją), taką jak system operacyjny urządzenia lub aplikacja o wysokim stopniu uprzywilejowania. Ten przepływ jest odpowiedni dla klientów, którzy mogą uzyskać poświadczenia właściciela zasobu (nazwa użytkownika i hasło, zazwyczaj przy użyciu formularza interaktywnego).|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Name|Przyznanie poświadczeń hasła właściciela zasobu|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_Description|<p\> Właściciel zasobu udostępnia klientowi swoją nazwę użytkownika i hasło. </p\> <p\> Klient żąda tokenu dostępu z punktu końcowego tokenu serwera autoryzacji, dołączając poświadczenia odebrane od właściciela zasobu.  Podczas składania żądania klient uwierzytelnia się za pomocą serwera autoryzacji. </p\> <p\> Serwer autoryzacji uwierzytelnia klienta i sprawdza poprawność poświadczeń właściciela zasobu, a jeśli jest prawidłowy, wystawia token dostępu. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ErrorDescription|<p\> Jeśli żądanie nie powiodło się uwierzytelnienie klienta lub jest nieprawidłowe, serwer autoryzacji odpowiada kodem stanu HTTP 400 (bad request) (chyba że określono inaczej) i zawiera następujące parametry z odpowiedzią. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_RequestDescription|<p\> Klient sprawia, że żądanie do punktu końcowego tokenu, dodając następujące parametry przy użyciu formatu "application/x-form-urlencoded" z kodowaniem znaków UTF-8 w treści jednostki żądania HTTP. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ResponseDescription|<p\> Jeśli żądanie tokenu dostępu jest prawidłowe i autoryzowane, serwer autoryzacji wystawia token dostępu i opcjonalny token odświeżania i konstruuje odpowiedź, dodając następujące parametry do treści jednostki odpowiedzi HTTP za pomocą kodu stanu 200 (OK). </p\>|  
|OAuth2Step_AccessTokenRequest_Name|Żądanie tokenu dostępu|  
|OAuth2Step_AuthorizationRequest_Name|Wniosek o autoryzację|  
|OAuth2AccessToken_AuthorizationCodeGrant_TokenResponse|Wymagane. Token dostępu wystawiony przez serwer autoryzacji.|  
|OAuth2AccessToken_ClientCredentialsGrant_TokenResponse|Wymagane. Token dostępu wystawiony przez serwer autoryzacji.|  
|OAuth2AccessToken_ImplicitGrant_AuthorizationResponse|Wymagane. Token dostępu wystawiony przez serwer autoryzacji.|  
|OAuth2AccessToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Wymagane. Token dostępu wystawiony przez serwer autoryzacji.|  
|OAuth2ClientId_AuthorizationCodeGrant_AuthorizationRequest|Wymagane. Identyfikator klienta.|  
|OAuth2ClientId_AuthorizationCodeGrant_TokenRequest|WYMAGANE, jeśli klient nie uwierzytelnia się za pomocą serwera autoryzacji.|  
|OAuth2ClientId_ImplicitGrant_AuthorizationRequest|Wymagane. Identyfikator klienta.|  
|OAuth2Code_AuthorizationCodeGrant_AuthorizationResponse|Wymagane. Kod autoryzacji wygenerowany przez serwer autoryzacji.|  
|OAuth2Code_AuthorizationCodeGrant_TokenRequest|Wymagane. Kod autoryzacji otrzymany z serwera autoryzacji.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_AuthorizationErrorResponse|Opcjonalne. Tekst ASCII czytelny dla człowieka zawierający dodatkowe informacje.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_TokenErrorResponse|Opcjonalne. Tekst ASCII czytelny dla człowieka zawierający dodatkowe informacje.|  
|OAuth2ErrorDescription_ClientCredentialsGrant_TokenErrorResponse|Opcjonalne. Tekst ASCII czytelny dla człowieka zawierający dodatkowe informacje.|  
|OAuth2ErrorDescription_ImplicitGrant_AuthorizationErrorResponse|Opcjonalne. Tekst ASCII czytelny dla człowieka zawierający dodatkowe informacje.|  
|OAuth2ErrorDescription_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|Opcjonalne. Tekst ASCII czytelny dla człowieka zawierający dodatkowe informacje.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_AuthorizationErrorResponse|Opcjonalne. Identyfikator URI identyfikujący stronę internetową czytelną dla człowieka z informacjami o błędzie.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_TokenErrorResponse|Opcjonalne. Identyfikator URI identyfikujący stronę internetową czytelną dla człowieka z informacjami o błędzie.|  
|OAuth2ErrorUri_ClientCredentialsGrant_TokenErrorResponse|Opcjonalne. Identyfikator URI identyfikujący stronę internetową czytelną dla człowieka z informacjami o błędzie.|  
|OAuth2ErrorUri_ImplicitGrant_AuthorizationErrorResponse|Opcjonalne. Identyfikator URI identyfikujący stronę internetową czytelną dla człowieka z informacjami o błędzie.|  
|OAuth2ErrorUri_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|Opcjonalne. Identyfikator URI identyfikujący stronę internetową czytelną dla człowieka z informacjami o błędzie.|  
|OAuth2Error_AuthorizationCodeGrant_AuthorizationErrorResponse|Wymagane. Pojedynczy kod błędu ASCII z następujących elementów: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable.|  
|OAuth2Error_AuthorizationCodeGrant_TokenErrorResponse|Wymagane. Pojedynczy kod błędu ASCII z następujących elementów: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2Error_ClientCredentialsGrant_TokenErrorResponse|Wymagane. Pojedynczy kod błędu ASCII z następujących elementów: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2Error_ImplicitGrant_AuthorizationErrorResponse|Wymagane. Pojedynczy kod błędu ASCII z następujących elementów: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable.|  
|OAuth2Error_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|Wymagane. Pojedynczy kod błędu ASCII z następujących elementów: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2ExpiresIn_AuthorizationCodeGrant_TokenResponse|Zalecane. Okres istnienia w sekundach tokenu dostępu.|  
|OAuth2ExpiresIn_ClientCredentialsGrant_TokenResponse|Zalecane. Okres istnienia w sekundach tokenu dostępu.|  
|OAuth2ExpiresIn_ImplicitGrant_AuthorizationResponse|Zalecane. Okres istnienia w sekundach tokenu dostępu.|  
|OAuth2ExpiresIn_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Zalecane. Okres istnienia w sekundach tokenu dostępu.|  
|OAuth2GrantType_AuthorizationCodeGrant_TokenRequest|Wymagane. Wartość MUSI być ustawiona na "authorization_code".|  
|OAuth2GrantType_ClientCredentialsGrant_TokenRequest|Wymagane. Wartość MUSI być ustawiona na "client_credentials".|  
|OAuth2GrantType_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Wymagane. Wartość MUSI być ustawiona na "hasło".|  
|OAuth2Password_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Wymagane. Hasło właściciela zasobu.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_AuthorizationRequest|Opcjonalne. Identyfikator URI punktu końcowego przekierowania musi być bezwzględnym identyfikatorem URI.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_TokenRequest|WYMAGANE, jeśli parametr "redirect_uri" został uwzględniony w żądaniu autoryzacji, a ich wartości MUSZĄ być identyczne.|  
|OAuth2RedirectUri_ImplicitGrant_AuthorizationRequest|Opcjonalne. Identyfikator URI punktu końcowego przekierowania musi być bezwzględnym identyfikatorem URI.|  
|OAuth2RefreshToken_AuthorizationCodeGrant_TokenResponse|Opcjonalne. Token odświeżania, który może służyć do uzyskiwania nowych tokenów dostępu.|  
|OAuth2RefreshToken_ClientCredentialsGrant_TokenResponse|Opcjonalne. Token odświeżania, który może służyć do uzyskiwania nowych tokenów dostępu.|  
|OAuth2RefreshToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Opcjonalne. Token odświeżania, który może służyć do uzyskiwania nowych tokenów dostępu.|  
|OAuth2ResponseType_AuthorizationCodeGrant_AuthorizationRequest|Wymagane. Wartość MUSI być ustawiona na "kod".|  
|OAuth2ResponseType_ImplicitGrant_AuthorizationRequest|Wymagane. Wartość MUSI być ustawiona na "token".|  
|OAuth2Scope_AuthorizationCodeGrant_AuthorizationRequest|Opcjonalne. Zakres żądania dostępu.|  
|OAuth2Scope_AuthorizationCodeGrant_TokenResponse|OPCJONALNIE, jeśli jest identyczny z zakresem wymaganym przez klienta; w przeciwnym razie WYMAGANE.|  
|OAuth2Scope_ClientCredentialsGrant_TokenRequest|Opcjonalne. Zakres żądania dostępu.|  
|OAuth2Scope_ClientCredentialsGrant_TokenResponse|OPCJONALNIE, jeśli jest identyczny z zakresem wymaganym przez klienta; w przeciwnym razie WYMAGANE.|  
|OAuth2Scope_ImplicitGrant_AuthorizationRequest|Opcjonalne. Zakres żądania dostępu.|  
|OAuth2Scope_ImplicitGrant_AuthorizationResponse|OPCJONALNIE, jeśli jest identyczny z zakresem wymaganym przez klienta; w przeciwnym razie WYMAGANE.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Opcjonalne. Zakres żądania dostępu.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenResponse|OPCJONALNIE, jeśli jest identyczny z zakresem wymaganym przez klienta; w przeciwnym razie WYMAGANE.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationErrorResponse|WYMAGANE, jeśli parametr "state" był obecny w żądaniu autoryzacji klienta.  Dokładna wartość otrzymana od klienta.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationRequest|Zalecane. Nieprzezroczysta wartość używana przez klienta do utrzymania stanu między żądaniem a wywołaniem zwrotnym.  Serwer autoryzacji zawiera tę wartość podczas przekierowywania agenta użytkownika z powrotem do klienta.  Parametr powinien być używany do zapobiegania fałszerskości żądań między siedzibą firmy.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationResponse|WYMAGANE, jeśli parametr "state" był obecny w żądaniu autoryzacji klienta.  Dokładna wartość otrzymana od klienta.|  
|OAuth2State_ImplicitGrant_AuthorizationErrorResponse|WYMAGANE, jeśli parametr "state" był obecny w żądaniu autoryzacji klienta.  Dokładna wartość otrzymana od klienta.|  
|OAuth2State_ImplicitGrant_AuthorizationRequest|Zalecane. Nieprzezroczysta wartość używana przez klienta do utrzymania stanu między żądaniem a wywołaniem zwrotnym.  Serwer autoryzacji zawiera tę wartość podczas przekierowywania agenta użytkownika z powrotem do klienta.  Parametr powinien być używany do zapobiegania fałszerskości żądań między siedzibą firmy.|  
|OAuth2State_ImplicitGrant_AuthorizationResponse|WYMAGANE, jeśli parametr "state" był obecny w żądaniu autoryzacji klienta.  Dokładna wartość otrzymana od klienta.|  
|OAuth2TokenType_AuthorizationCodeGrant_TokenResponse|Wymagane. Typ wystawionego tokenu.|  
|OAuth2TokenType_ClientCredentialsGrant_TokenResponse|Wymagane. Typ wystawionego tokenu.|  
|OAuth2TokenType_ImplicitGrant_AuthorizationResponse|Wymagane. Typ wystawionego tokenu.|  
|OAuth2TokenType_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Wymagane. Typ wystawionego tokenu.|  
|OAuth2UserName_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Wymagane. Nazwa użytkownika właściciela zasobu.|  
|OAuth2NiepochwytowaneZtytyty zuszeń|Typ tokenu '{0}' nie jest obsługiwany.|  
|Stan OAuth2Invalid|Nieprawidłowa odpowiedź z serwera autoryzacji|  
|OAuth2GrantType_AuthorizationCode|Kod autoryzacji|  
|OAuth2GrantType_Implicit|Niejawne|  
|OAuth2GrantType_ClientCredentials|Poświadczenia klienta|  
|OAuth2GrantType_ResourceOwnerPassword|Hasło właściciela zasobu|  
|Kod Doznania Sieci Web302|302 Znaleziono|  
|Kod Dodocumentation400|400 (Złe żądanie)|  
|OAuth2SendingMethod_AuthHeader|Nagłówek autoryzacji|  
|OAuth2SendingMethod_QueryParam|Parametr kwerendy|  
|OAuth2AuthorizationServerGeneralException|Wystąpił błąd podczas autoryzowania dostępu za pośrednictwem{0}|  
|OAuth2AuthorizationServerCommunicationException|Nie można ustanowić połączenia HTTP z serwerem autoryzacji lub zostało ono nieoczekiwanie zamknięte.|  
|WebDocumentationOAuth2GeneralErrorMessage|Wystąpił nieoczekiwany błąd.|  
|AuthorizationServerCommunicationException (Nierozsądne zachowanie)|Wystąpił wyjątek komunikacji serwera autoryzacji. Skontaktuj się z administratorem.|  
|TextblockSubscriptionKeyHeaderDescription (Opis kodu zawierającego klucze tekstowe)|Klucz subskrypcji, który zapewnia dostęp do tego interfejsu API. Znaleziono w <href ='/developer'\>Profil</a\>.|  
|TextblockOAuthHeaderDescription (TekstblockOAuthHeaderDescription)|Token dostępu OAuth 2.0 uzyskany\> {0} z <i\></i . Obsługiwane rodzaje dotacji: <i\> {1}</i\>.|  
|TextblockContentTypeHeaderDescription (TekstblockContentTypeTypeerDescription)|Typ nośnika treści wysyłane do interfejsu API.|  
|ErrorMessageApiNotAccessible|Interfejs API, który próbujesz wywołać, nie jest obecnie dostępny. Skontaktuj się z wydawcą API <href="/issues"\>tutaj</a\>.|  
|BłądMessageApiTimedout|Interfejs API, który próbujesz wywołać trwa dłużej niż zwykle, aby uzyskać odpowiedź z powrotem. Skontaktuj się z wydawcą API <href="/issues"\>tutaj</a\>.|  
|BadRequestParameterPrzedażyło|"'{0}' oczekuje się parametru"|  
|Etykietka narzędziaTextDoubleClickToSelectAll|Kliknij dwukrotnie, aby zaznaczyć wszystkie.|  
|Etykietka narzędziaTextHideRevealSecret|Pokaż/Ukryj|  
|ButtonLinkOpenConsole|Wypróbuj|  
|SekcjaHeadingRequestBody|Treść żądania|  
|SekcjaPozycjeRequestParametry|Parametry żądania|  
|SekcjaHeadingRequestUrl|Adres URL żądania|  
|SekcjaPozycjaPozycja|Odpowiedź|  
|SectionHeadingRequestHeaders (Głowice nagłówków)|Nagłówki żądań|  
|FormLabelSubtextOkoncesticja|optional|  
|SectionHeadingCodePróby|Przykłady kodu|  
|TextblockOpenidConnectHeaderDescription (TekstblockOpenidConnectHeaderDescription)|Token OpenID Connect ID uzyskany\> {0} z <i\></i . Obsługiwane rodzaje dotacji: <i\> {1}</i\>.|  
  
###  <a name="errorpagestrings"></a><a name="ErrorPageStrings"></a>Strony błędówSieci  
  
|Nazwa|Tekst|  
|----------|----------|  
|LinkLabelBack (LinkLabelBack)|wstecz|  
|LinkLabelHomePage (LinkLabelHomePage)|strona główna|  
|LinkLabelsendUsemail|Napisz do nas|  
|PageTitleError|Niestety, wystąpił problem z obsługą żądanej strony|  
|TextblockPotentialCauseIntermittentIssue|Może to być sporadyczny problem z dostępem do danych, który już zniknął.|  
|TextblockPotentialCauseOldLink (Blok tekstu)|Link, który kliknąłeś, może być stary i nie wskazywał już prawidłowej lokalizacji.|  
|TextblockPotentialCauseTechnicalProblem|Po naszej stronie może być problem techniczny.|  
|TextblockPotentialSolutionRefresh TextblockPotentialSolutionRefresh TextblockPotentialSolutionRefresh Textblock|Spróbuj odświeżyć stronę.|  
|TextblockPotentialSolutionStartOver|Zacznij od {0}nowa od naszego .|  
|TextblockPotentialSolutionTryAgain|Idź {0} i spróbuj wykonać akcję ponownie.|  
|Problem z raportem tekstowym|{0}opisując, co poszło nie tak i przyjrzymy się temu tak szybko, jak to możliwe.|  
|TitlePotentialCause|Potencjalna przyczyna|  
|Rozwiązanie titlepotencjalne|To prawdopodobnie tylko tymczasowy problem, kilka rzeczy do wypróbowania|  
  
###  <a name="issuesstrings"></a><a name="IssuesStrings"></a>IssuesStrings (Numery problemów)  
  
|Nazwa|Tekst|  
|----------|----------|  
|WebIssuesIndexTitle|Problemy|  
|WebIssuesNoActiveSubscriptions|Nie masz żadnych aktywnych subskrypcji. Musisz subskrybować produkt, aby zgłosić problem.|  
|WebIssuesNotSignin|Nie jesteś zalogowany. Zgłoś {0} problem lub opublikuj komentarz.|  
|WebIssuesReportIssueButton|Problem z raportem|  
|WebIssuesSignIn|logowanie|  
|WebIssuesStatusReportedBy|Status: {0} &#124; Zgłoszone przez{1}|  
  
###  <a name="notfoundstrings"></a><a name="NotFoundStrings"></a>NotFoundStrings (NiefoundStrings)  
  
|Nazwa|Tekst|  
|----------|----------|  
|LinkLabelHomePage (LinkLabelHomePage)|strona główna|  
|LinkLabelsendUsemail|napisz do nas e-mail|  
|PageTitleNotFound|Niestety, nie możemy znaleźć strony, której szukasz|  
|TextblockPotentialCauseMisspelledUrl|Być może adres URL został wpisany błędnie, jeśli został wpisany.|  
|TextblockPotentialCauseOldLink (Blok tekstu)|Link, który kliknąłeś, może być stary i nie wskazywał już prawidłowej lokalizacji.|  
|TextblockPotentialSolutionRetype (Typ)|Spróbuj ponownie przymknieć adres URL.|  
|TextblockPotentialSolutionStartOver|Zacznij od {0}nowa od naszego .|  
|Problem z raportem tekstowym|{0}opisując, co poszło nie tak i przyjrzymy się temu tak szybko, jak to możliwe.|  
|TitlePotentialCause|Potencjalna przyczyna|  
|Rozwiązanie titlepotencjalne|Potencjalne rozwiązanie|  
  
###  <a name="productdetailsstrings"></a><a name="ProductDetailsStrings"></a>Szczegóły produktuStrzanie  
  
|Nazwa|Tekst|  
|----------|----------|  
|WebProductsZrozadnia|Subskrybując {0} Produkt, wyrażam `<a data-toggle='modal' href='#legal-terms'\>Terms of Use</a\>`zgodę na .|  
|WebProductsLegalTermsLink|Warunki użytkowania|  
|WebProductsSubscribeButton|Subskrybuj|  
|WebProductsUsageLimitsHeader|Limity użycia|  
|WebProductsYouAreNotSubscribed|Jesteś subskrybentem tego produktu.|  
|WebProductsYouRequestedSubscription|Zażądałeś subskrypcji tego produktu.|  
|BłądYouNeedToAgreeWithLegalTerms|Musisz zaakceptować Warunki użytkowania, zanim będzie można kontynuować.|  
|ButtonLabelAddSubscription|Dodawanie subskrypcji|  
|LinkLabelChangeName|zmienianie|  
|PrzyciskLabelConfirma|Confirm|  
|TextblockMultipleSubscriptionsCount|Masz {0} subskrypcje tego produktu:|  
|TextblockSinglesubscriptionsCount|Masz {0} subskrypcję tego produktu:|  
|TextblockSingleapisCount|Ten produkt {0} zawiera API:|  
|TextblockMultipleApisCount|Ten produkt {0} zawiera interfejsy API:|  
|TextblockHeaderSubscribe|Subskrybuj produkt|  
|TextblockSubscriptionDescription (Opis blokady tekstu)|Nowa subskrypcja zostanie utworzona w następujący sposób:|  
|TextblockSubscriptionLimitReached|Osiągnięto limit subskrypcji.|  
  
###  <a name="productsstrings"></a><a name="ProductsStrings"></a>ProduktySci  
  
|Nazwa|Tekst|  
|----------|----------|  
|PageTitleProdukty|Produkty|  
  
###  <a name="providerinfostrings"></a><a name="ProviderInfoStrings"></a>ProviderInfoStrings  
  
|Nazwa|Tekst|  
|----------|----------|  
|TextboxExternalIdentitiesDisabled (Nie podlegać reklamom)|Logowanie jest obecnie wyłączone przez administratorów.|  
|TextboxExternalIdentitiesSigninInvitation (Skrzynka tekstowaExternalIdentitiesSigninVitation)|Możesz też zalogować się za pomocą|  
|TextboxExternalIdentitiesSigninInvitationPrimary|Zaloguj się za pomocą:|  
  
###  <a name="signinresources"></a><a name="SigninResources"></a>Źródła signin  
  
|Nazwa|Tekst|  
|----------|----------|  
|GłównyNieznajdny|Nie znaleziono głównego zobowiązanego lub podpis jest nieprawidłowy|  
|ErrorSsoAuthenticationFailed|Uwierzytelnianie SSO nie powiodło się|  
|ErrorSsoAuthenticationFailedDetailed|Nie można zweryfikować nieprawidłowego tokenu lub podpisu.|  
|BłądSsoTokenInvalid|Token SSO jest nieprawidłowy|  
|ValidationErrorSpecificEmailAlreadyExists|E-mail '{0}już zarejestrowany|  
|ValidationErrorSpecificEmailInvalid|Wiadomość{0}e-mail ' ' jest nieprawidłowa|  
|ValidationErrorPasswordInvalid|Hasło jest nieprawidłowe. Popraw błędy i spróbuj ponownie.|  
|PropertyTooShort (WłaściwośćTooKrótki)|{0}jest zbyt krótki|  
|WebAuthenticationAddresserEmailInvalidErrorMessage|Nieprawidłowy adres e-mail.|  
|ValidationMessageNewPasswordConfirmationRequired ValidationMessageNewPasswordConfirmationRequired ValidationMessageNewPasswordConfirmationRequired ValidationM|Potwierdź nowe hasło|  
|ValidationErrorPasswordConfirmationRequired ValidationErrorPasswordConfirmationRequired ValidationErrorPasswordConfirmationRequired ValidationEr|Potwierdź, że hasło jest puste|  
|WebAuthenticationEmailChangeNotice|Zmień wiadomość e-mail {0}z potwierdzeniem jest w drodze do . Postępuj zgodnie z instrukcjami, aby potwierdzić swój nowy adres e-mail. Jeśli wiadomość e-mail nie dotrze do Twojej skrzynki odbiorczej w ciągu najbliższych kilku minut, sprawdź folder wiadomości-śmieci.|  
|WebAuthenticationEmailChangeNoticeHeader|Żądanie zmiany adresu e-mail zostało pomyślnie przetworzone|  
|WebAuthenticationEmailChangeNoticeTitle|Żądana zmiana adresu e-mail|  
|WebAuthenticationEmailHasBeenRevertedNotice|Wiadomości e-mail już istnieją. Żądanie zostało wycofane|  
|ValidationErrorEmailAlreadyExists (Sprawdzanie poprawnościEmailatatatawyjścia)|Poczta e-mail już istnieje|  
|ValidationErrorEmailInvalid|Nieprawidłowy adres e-mail|  
|TextboxLabelEmail (skrzynka tekstowa)|Adres e-mail|  
|Wymagane sprawdzanie poprawnościEmail|Adres e-mail jest wymagany.|  
|WebAuthenticationErrorNoticeHeader|Błąd|  
|WebAuthenticationFieldLengthErrorMessage|{0}musi wynosić maksymalną długość{1}|  
|TextboxLabelEmailFirstName|Imię|  
|Wymagana nazwa validationerorfirst|Imię jest wymagane.|  
|ValidationErrorFirstNameInvalid|Nieprawidłowe imię|  
|NoticeInvalidInvitationToken|Należy pamiętać, że linki potwierdzające są ważne tylko przez 48 godzin. Jeśli nadal jesteś w tym przedziale czasowym, upewnij się, że link jest poprawny. Jeśli link wygasł, powtórz czynność, którą próbujesz potwierdzić.|  
|NoticeHeaderInvalidInvitationToken|Nieprawidłowy token zaproszenia|  
|UwagaTitleInvalidInvitationToken|Błąd potwierdzenia|  
|WebAuthenticationLastNameInvalidErrorMessage|Nieprawidłowe nazwisko|  
|TextboxLabelEmailLastName|Nazwisko|  
|Wymagana nazwa pliku ValidationErorLastName|Wymagane jest nazwisko.|  
|WebAuthenticationLinkExpiredNotice|Wysłany do Ciebie link potwierdzający wygasł. `<a href={0}?token={1}>Resend confirmation email.</a\>`|  
|UwagaPasswordResetLinkInvalidOrExpired|Link do resetowania hasła jest nieprawidłowy lub wygasł.|  
|WebAuthenticationLinkExpiredNoticeTitle|Wysłany link|  
|WebAuthenticationNewPasswordLabel|Nowe hasło|  
|ValidationMessageNewPasswordRequired ValidationMessageNewPasswordRequired ValidationMessageNewPasswordRequired ValidationM|Wymagane jest nowe hasło.|  
|TextboxLabelNotificationsSenderEmail|Wiadomość e-mail nadawcy powiadomień|  
|TextboxLabelOrganizationName|Nazwa organizacji|  
|WebAuthenticationOrganizationRequiredErrorMessage|Nazwa organizacji jest pusta|  
|WebAuthenticationPasswordChangedNotice|Twoje hasło zostało pomyślnie zaktualizowane|  
|WebAuthenticationPasswordChangedNoticeTitle|Zaktualizowane hasło|  
|WebAuthenticationPasswordCompareErrorMessage|Hasła nie są zgodne|  
|WebAuthenticationPasswordConfirmLabel|Potwierdź hasło|  
|ValidationErrorPasswordInvalidSzczegóły|Hasło jest zbyt słabe.|  
|WebAuthenticationPasswordLabel|Hasło|  
|ValidationErrorPasswordRequired (Wymagane jest walidacjaErrorPasswordRequired)|Hasło jest wymagane.|  
|WebAuthenticationPasswordResetSendNotice|Zmień wiadomość e-mail z {0}potwierdzeniem hasła jest w drodze do . Postępuj zgodnie z instrukcjami zawartymi w wiadomości e-mail, aby kontynuować proces zmiany hasła.|  
|WebAuthenticationPasswordResetSendNoticeHeader|Żądanie zresetowania hasła zostało pomyślnie przetworzone|  
|WebAuthenticationPasswordResetSendNoticeTitle|Żądane zresetowanie hasła|  
|WebAuthenticationRequestNotNotice|Nie znaleziono żądania|  
|WebAuthenticationSenderEmailRequiredErrorMessage|Wiadomość e-mail nadawcy powiadomień jest pusta|  
|WebAuthenticationSigninPasswordLabel|Potwierdź zmianę, wprowadzając hasło|  
|WebAuthenticationSignupConfirmNotice|E-mail z potwierdzeniem {0}rejestracji jest w\> drodze do .<br / Postępuj zgodnie z\> instrukcjami w wiadomości e-mail, aby aktywować swój account.<br / Jeśli e-mail nie dotrze do skrzynki odbiorczej w ciągu najbliższych kilku minut, sprawdź folder wiadomości-śmieci.|  
|WebAuthenticationSignupConfirmNoticeHeader|Twoje konto zostało pomyślnie utworzone|  
|WebAuthenticationSignupConfirmNoticeRepeatHeader|Wiadomość e-mail z potwierdzeniem rejestracji została wysłana ponownie|  
|WebAuthenticationSignupConfirmNoticeTitle|Konto utworzone|  
|WebAuthenticationTokenRequiredErrorMessage|Token jest pusty|  
|WebAuthenticationUserAlreadyRegisteredNotice|Wydaje się, że użytkownik z tej wiadomości e-mail jest już zarejestrowany w systemie. Jeśli nie pamiętasz hasła, spróbuj je przywrócić lub skontaktuj się z naszym zespołem pomocy technicznej.|  
|WebAuthenticationUserAlreadyRegisteredNoticeHeader|Użytkownik już zarejestrowany|  
|WebAuthenticationUserAlreadyRegisteredNoticeTitle|Już zarejestrowana|  
|ButtonLabelChangePassword|Zmień hasło|  
|ButtonLabelChangeAccountInfo|Zmienianie informacji o koncie|  
|Konto ButtonLabelCloseAccount|Zamknij konto|  
|WebAuthenticationInvalidCaptchaErrorMessage|Wprowadzony tekst nie jest zgodny z tekstem na obrazie. Spróbuj ponownie.|  
|ValidationErrorCredentialsInvalid ValidationErrorCredentialsInvalid ValidationErrorCredentialsInvalid Validation|Adres e-mail lub hasło są nieprawidłowe. Popraw błędy i spróbuj ponownie.|  
|WebAuthenticationRequestIsNotValid|Żądanie jest nieprawidłowe|  
|WebAuthenticationUserIsNotConfirm|Przed próbą zalogowania się należy potwierdzić rejestrację.|  
|WebAuthenticationInvalidEmailFormowany|Wiadomość e-mail jest nieprawidłowa:{0}|  
|WebAuthenticationUserNotfound|Nie znaleziono użytkownika|  
|WebAuthenticationTenantNotRegistered|Twoje konto należy do dzierżawy usługi Azure Active Directory, która nie jest autoryzowana do uzyskiwania dostępu do tego portalu.|  
|WebAuthenticationAuthenticationNieważone|Uwierzytelnianie nie powiodło się.|  
|WebAuthenticationGooglePlusNotEnabled|Uwierzytelnianie nie powiodło się. Jeśli autoryzowałeś aplikację, skontaktuj się z administratorem, aby upewnić się, że uwierzytelnianie Google jest poprawnie skonfigurowane.|  
|Wymagane jest 200 000 000 000 000 000 000 0|Dozwolona dzierżawa jest wymagana|  
|ValidationErrorTenantIsNotValid ValidationErrorTenantIsNotValid ValidationErrorTenantIsNotValid ValidationEr|Dzierżawa usługi Azure{0}Active Directory ' jest nieprawidłowa.|  
|WebAuthenticationActiveDirectoryTitle|Usługa Azure Active Directory|  
|WebAuthenticationLoginUsingYourProvider|Zaloguj się {0} przy użyciu swojego konta|  
|WebAuthenticationUserLimitNotice|Ta usługa osiągnęła maksymalną liczbę dozwolonych użytkowników. Prosimy `<a href="mailto:{0}"\>contact the administrator</a\>` o uaktualnienie usługi i ponowne włączenie rejestracji użytkownika.|  
|WebAuthenticationUserLimitNoticeHeader|Rejestracja użytkownika wyłączona|  
|WebAuthenticationUserLimitNoticeTitle|Rejestracja użytkownika wyłączona|  
|WebAuthenticationUserRegistrationDisabledNotice|Rejestracja użytkowników została wyłączona przez administratora. Zaloguj się za pomocą zewnętrznego dostawcy tożsamości.|  
|WebAuthenticationUserRegistrationDisabledNoticeHeader|Rejestracja użytkownika wyłączona|  
|WebAuthenticationUserRegistrationDisabledNoticeTitle|Rejestracja użytkownika wyłączona|  
|WebAuthenticationSignupPendingConfirmationNotice|Zanim będziemy mogli zakończyć tworzenie Twojego konta, musimy zweryfikować Twój adres e-mail. Wysłaliśmy wiadomość e-mail {0}na adres . Postępuj zgodnie z instrukcjami zawartymi w wiadomości e-mail, aby aktywować swoje konto. Jeśli wiadomość e-mail nie dotrze w ciągu najbliższych kilku minut, sprawdź folder wiadomości-śmieci.|  
|WebAuthenticationSignupPendingConfirmationAccountFoundNotice|Znaleźliśmy niepotwierdzone konto dla adresu {0}e-mail . Aby zakończyć tworzenie konta, musimy zweryfikować Twój adres e-mail. Wysłaliśmy wiadomość e-mail {0}na adres . Postępuj zgodnie z instrukcjami zawartymi w wiadomości e-mail, aby aktywować swoje konto. Jeśli wiadomość e-mail nie dotrze w ciągu najbliższych kilku minut, sprawdź folder wiadomości-śmieci|  
|WebAuthenticationSignupConfirmationAlmostDone|Prawie gotowe|  
|WebAuthenticationSignupConfirmationEmailSent|Wysłaliśmy wiadomość e-mail {0}na adres . Postępuj zgodnie z instrukcjami zawartymi w wiadomości e-mail, aby aktywować swoje konto. Jeśli wiadomość e-mail nie dotrze w ciągu najbliższych kilku minut, sprawdź folder wiadomości-śmieci.|  
|WebAuthenticationEmailSentNotificationMessage|Wiadomość e-mail wysłana pomyślnie do{0}|  
|WebAuthenticationNoAadTenantConfigured|Żadna dzierżawa usługi Azure Active Directory nie skonfigurowana dla usługi.|  
|CheckboxLabelUserRegistrationTermsConsentRequired|Zgadzam się `<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use</a\>`na .|  
|TextblockUserRegistrationTermsProvided TextblockUserRegistrationTermsProvided TextblockUserRegistrationTermsProvided Textblock|Proszę przejrzeć`<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use.</a\>`|  
|Okno dialogoweTermsOfUse|Warunki użytkowania|  
|ValidationMessageConsentNotAccepted|Musisz zaakceptować Warunki użytkowania, zanim będzie można kontynuować.|  
  
###  <a name="signinstrings"></a><a name="SigninStrings"></a>SigninStrueds (Znaki wskazujące)  
  
|Nazwa|Tekst|  
|----------|----------|  
|WebAuthenticationForgotPassword|Forgot your password?|  
|WebAuthenticationIfAdministrator|Jeśli jesteś administratorem, musisz `<a href="{0}"\>here</a\>`się zalogować .|  
|WebAuthenticationNotAMember|Nie jesteś jeszcze członkiem? `<a href="/signup"\>Sign up now</a\>`|  
|WebAuthenticationRemember|Zapamiętaj mnie na tym komputerze|  
|WebAuthenticationSigininWithPassword|Zaloguj się przy użyciu nazwy użytkownika i hasła|  
|WebAuthenticationSigninTitle|Logowanie|  
|WebAuthenticationSignUpNow|Utwórz konto teraz|  
  
###  <a name="signupstrings"></a><a name="SignupStrings"></a>Rejestracja Szyldy  
  
|Nazwa|Tekst|  
|----------|----------|  
|Podpis pagetitle|Rejestrowanie|  
|WebAuthenticationAlreadyAMember|Jesteś już członkiem?|  
|WebAuthenticationCreateNewAccount|Tworzenie nowego konta usługi API Management|  
|WebAuthenticationSigninNow|Zaloguj się teraz|  
|ButtonLabelSignup|Rejestrowanie|  
  
###  <a name="subscriptionliststrings"></a><a name="SubscriptionListStrings"></a>SubscriptionListStrings  
  
|Nazwa|Tekst|  
|----------|----------|  
|SubskrypcjaCancelConfirmation|Czy na pewno chcesz anulować tę subskrypcję?|  
|SubskrypcjaRenewConfirmacja|Czy na pewno chcesz odnowić tę subskrypcję?|  
|WebDevelopersManageSubscriptions|Zarządzanie subskrypcjami|  
|WebDevelopersPrimaryKey|Klucz podstawowy|  
|WebDevelopersRegenerateLink|Regenerate|  
|WebDevelopersSecondaryKey (WebDevelopersSecondaryKey)|Klucz pomocniczy|  
|PrzyciskLabelShowKey|Show|  
|ButtonLabelOdpisy|Renew|  
|WebDevelopersSubscriptionZaspozachany|Wniosek na{0}|  
|WebDevelopersSubscriptionRequestedState|Zażądano|  
|WebDevelopersSubscriptionTableNameHeader|Nazwa|  
|WebDevelopersSubscriptionTableStateHeader|Stan|  
|WebDevelopersUsageStatisticsLink|Raporty analityczne|  
|WebDevelopersYourSubscriptions|Twoje subskrypcje|  
|SubskrypcjaPropertyLabelRequestedDate|Wniosek na|  
|SubskrypcjaPropertyLabelStartedDate|Rozpoczęto pracę|  
|Subskrypcja pageTitleRenameSubscription|Zmienianie nazwy subskrypcji|  
|Nazwa subskrypcjiPropertyLabelName|Nazwa subskrypcji|  
  
###  <a name="subscriptionstrings"></a><a name="SubscriptionStrings"></a>Subscriptionstrings  
  
|Nazwa|Tekst|  
|----------|----------|  
|SekcjaUchczenieCloseKontowanie|Chcesz zamknąć konto?|  
|PageTitleDeveloperProfile|Profil|  
|PrzyciskLabelHideKey|Ukryj|  
|PrzyciskLabelRegenerateKey|Regenerate|  
|InformationMessageKeyWasRegenerowane|Czy na pewno chcesz ponownie wygenerować ten klucz?|  
|PrzyciskLabelShowKey|Show|  
  
###  <a name="updateprofilestrings"></a><a name="UpdateProfileStrings"></a>AktualizacjaProfileStruje  
  
|Nazwa|Tekst|  
|----------|----------|  
|ButtonLabelUpdateProfile|Aktualizuj profil|  
|PageTitleUpdateProfile|Aktualizowanie informacji o koncie|  
  
###  <a name="userprofile"></a><a name="UserProfile"></a>Userprofile  
  
|Nazwa|Tekst|  
|----------|----------|  
|ButtonLabelChangeAccountInfo|Zmienianie informacji o koncie|  
|ButtonLabelChangePassword|Zmień hasło|  
|Konto ButtonLabelCloseAccount|Zamknij konto|  
|TextboxLabelEmail (skrzynka tekstowa)|Adres e-mail|  
|TextboxLabelEmailFirstName|Imię|  
|TextboxLabelEmailLastName|Nazwisko|  
|TextboxLabelNotificationsSenderEmail|Wiadomość e-mail nadawcy powiadomień|  
|TextboxLabelOrganizationName|Nazwa organizacji|  
|SubskrypcjaStateActive|Aktywne|  
|SubskrypcjaConCancelled|Anulowano|  
|SubskrypcjaStateExpired|Wygasłe|  
|SubskrypcjaStanRej wyrzucony|Odrzucone|  
|SubskrypcjaConrequested|Zażądano|  
|SubskrypcjaStateSuspended|Suspended|  
|Domyślnanamnampłamnamowa|{0}(domyślnie)|  
|SubscriptionNameTemplate|Dostęp dla deweloperów #{0}|  
|TextboxLabelSubscriptionName|Nazwa subskrypcji|  
|ValidationMessageSubscriptionName Wymagane|Nazwa subskrypcji nie może być pusta.|  
|ApiManagementUserLimitReached|Ta usługa osiągnęła maksymalną liczbę dozwolonych użytkowników. Przejdź do wyższej warstwy cenowej.|  
  
##  <a name="glyph-resources"></a><a name="glyphs"></a>Zasoby glifów  
 Szablony portali deweloperów zarządzania interfejsami API mogą używać glifów [firmy Glyphicons firmy Bootstrap.](https://getbootstrap.com/components/#glyphicons) Ten zestaw glifów zawiera ponad 250 glifów w formacie czcionki z zestawu [Glyphicon](https://glyphicons.com/) Halflings. Aby użyć glifów z tego zestawu, należy użyć następującej składni.  
  
```html  
<span class="glyphicon glyphicon-user">  
```  
  
 Aby uzyskać pełną listę glifów, zobacz [Glyphicons z Bootstrap](https://getbootstrap.com/components/#glyphicons).

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [Jak dostosować portal dewelopera zarządzania interfejsami API przy użyciu szablonów](api-management-developer-portal-templates.md).
