---
title: Zasoby szablonów API Management platformy Azure | Microsoft Docs
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
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: b634f871670d252aadaf2d2a672c4d5f43a6e5d9
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073331"
---
# <a name="azure-api-management-template-resources"></a>Zasoby szablonów API Management platformy Azure
Usługa Azure API Management udostępnia następujące typy zasobów do użycia w szablonach portalu dla deweloperów.  
  
-   [Zasoby ciągów](#strings)  
  
-   [Zasoby symboli](#glyphs)  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="strings"></a>Zasoby ciągów  
 API Management zapewnia kompleksowy zestaw zasobów ciągów do użycia w portalu dla deweloperów. Te zasoby są zlokalizowane we wszystkich językach obsługiwanych przez API Management. Domyślny zestaw szablonów używa tych zasobów dla nagłówków stron, etykiet i wszystkich stałych ciągów, które są wyświetlane w portalu dla deweloperów. Aby użyć zasobu ciągu w szablonach, podaj prefiks ciągu zasobu, a po nim nazwę ciągu, jak pokazano w poniższym przykładzie.  
  
```  
{% localized "Prefix|Name" %}  
  
```  
  
 Poniższy przykład pochodzi z szablonu Lista produktów i zawiera **produkty** w górnej części strony.  
  
```  
<h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>  
  
```  
  
Obsługiwane są następujące opcje lokalizacji:

| Ustawienia regionalne    | Język               |
|-----------|------------------------|
| „pl”      | Anglia              |
| Rejestr      | "Čeština"              |
| Ukryj      | Deutsch              |
| AK      | "Español"              |
| fr      | "Français"             |
| Węgry      | Magyar               |
| go      | Italiano             |
| "ja-JP"   | "日本語"                |
| Ko      | "한국어"                |
| NL      | Nederlands           |
| zysków      | "Polski"               |
| "pt-br"   | "Português (Brazylia)"   |
| "pt-pt"   | "Português (Portugalia)" |
| ru      | "Русский"              |
| OHR      | Svenska              |
| zdawczy      | "Türkçe"               |
| "zh-Hans" | "中文(简体)"           |
| "zh-Hant" | "中文(繁體)"           |

 W poniższych tabelach zawarto informacje dotyczące zasobów ciągów dostępnych do użycia w szablonach portalu dla deweloperów. Użyj nazwy tabeli jako prefiksu dla zasobów ciągów w tej tabeli.  
  
-   [ApisStrings](#ApisStrings)  
  
-   [ApplicationListStrings](#ApplicationListStrings)  
  
-   [AppDetailsStrings](#AppDetailsStrings)  
  
-   [AppStrings](#AppStrings)  
  
-   [CommonResources](#CommonResources)  
  
-   [CommonStrings](#CommonStrings)  
  
-   [Dokumentacja](#Documentation)  
  
-   [ErrorPageStrings](#ErrorPageStrings)  
  
-   [IssuesStrings](#IssuesStrings)  
  
-   [NotFoundStrings](#NotFoundStrings)  
  
-   [ProductDetailsStrings](#ProductDetailsStrings)  
  
-   [ProductsStrings](#ProductsStrings)  
  
-   [ProviderInfoStrings](#ProviderInfoStrings)  
  
-   [SigninResources](#SigninResources)  
  
-   [SigninStrings](#SigninStrings)  
  
-   [SignupStrings](#SignupStrings)  
  
-   [SubscriptionListStrings](#SubscriptionListStrings)  
  
-   [SubscriptionStrings](#SubscriptionStrings)  
  
-   [UpdateProfileStrings](#UpdateProfileStrings)  
  
-   [UserProfile](#UserProfile)  
  
###  <a name="ApisStrings"></a>ApisStrings  
  
|Name|Text|  
|----------|----------|  
|PageTitleApis|interfejsy API|  
  
###  <a name="AppDetailsStrings"></a>AppDetailsStrings  
  
|Name|Text|  
|----------|----------|  
|WebApplicationsDetailsTitle|Podgląd aplikacji|  
|WebApplicationsRequirementsHeader|Wymagania|  
|WebApplicationsScreenshotAlt|Zrzut ekranu|  
|WebApplicationsScreenshotsHeader|Zrzuty ekranu|  
  
###  <a name="ApplicationListStrings"></a>ApplicationListStrings  
  
|Name|Text|  
|----------|----------|  
|WebDevelopersAppDeleteConfirmation|Czy na pewno chcesz usunąć aplikację?|  
|WebDevelopersAppNotPublished|Nie opublikowano|  
|WebDevelopersAppNotSubmitted|Nie przesłano|  
|WebDevelopersAppTableCategoryHeader|Kategoria|  
|WebDevelopersAppTableNameHeader|Name|  
|WebDevelopersAppTableStateHeader|State|  
|WebDevelopersEditLink|Edytuj|  
|WebDevelopersRegisterAppLink|Zarejestruj aplikację|  
|WebDevelopersRemoveLink|Usuń|  
|WebDevelopersSubmitLink|Prześlij|  
|WebDevelopersYourApplicationsHeader|Twoje aplikacje|  
  
###  <a name="AppStrings"></a>AppStrings  
  
|Name|Text|  
|----------|----------|  
|WebApplicationsHeader|Aplikacje|  
  
###  <a name="CommonResources"></a>CommonResources  
  
|Name|Text|  
|----------|----------|  
|NoItemsToDisplay|Brak wyników.|  
|GeneralExceptionMessage|Coś nie jest prawidłowe. Może to być błąd tymczasowy lub usterka. Spróbuj ponownie.|  
|GeneralJsonExceptionMessage|Coś nie jest prawidłowe. Może to być błąd tymczasowy lub usterka. Załaduj ponownie stronę i spróbuj ponownie.|  
|ConfirmationMessageUnsavedChanges|Istnieją niezapisane zmiany. Czy na pewno chcesz anulować i odrzucić zmiany?|  
|AzureActiveDirectory|Usługa Azure Active Directory|  
|HttpLargeRequestMessage|Treść żądania HTTP jest za duża.|  
  
###  <a name="CommonStrings"></a>CommonStrings  
  
|Name|Text|  
|----------|----------|  
|ButtonLabelCancel|Cancel|  
|ButtonLabelSave|Zapisanie|  
|GeneralExceptionMessage|Coś nie jest prawidłowe. Może to być błąd tymczasowy lub usterka. Spróbuj ponownie.|  
|NoItemsToDisplay|Brak elementów do wyświetlenia.|  
|PagerButtonLabelFirst|Pierwsza|  
|PagerButtonLabelLast|Ostatnia|  
|PagerButtonLabelNext|Następne kroki|  
|PagerButtonLabelPrevious|Wstecz|  
|PagerLabelPageNOfM|Na {0} stronie{1}|  
|PasswordTooShort|Hasło jest za krótkie|  
|EmailAsPassword|Nie używaj swojego adresu e-mail jako hasła|  
|PasswordSameAsUserName|Hasło nie może zawierać Twojej nazwy użytkownika|  
|PasswordTwoCharacterClasses|Użyj innych klas znaków|  
|PasswordTooManyRepetitions|Za dużo powtórzeń|  
|PasswordSequenceFound|Twoje hasło zawiera sekwencje|  
|PagerLabelPageSize|Rozmiar strony|  
|CurtainLabelLoading|Trwa ładowanie...|  
|TablePlaceholderNothingToDisplay|Brak danych dla wybranego okresu i zakresu|  
|ButtonLabelClose|Zamykanie|  
  
###  <a name="Documentation"></a>Łączoną  
  
|Name|Text|  
|----------|----------|  
|WebDocumentationInvalidHeaderErrorMessage|Nieprawidłowy nagłówek "{0}"|  
|WebDocumentationInvalidRequestErrorMessage|Nieprawidłowy adres URL żądania|  
|TextboxLabelAccessToken|Token dostępu *|  
|DropdownOptionPrimaryKeyFormat|Głównym{0}|  
|DropdownOptionSecondaryKeyFormat|Dodatkowych{0}|  
|WebDocumentationSubscriptionKeyText|Twój klucz subskrypcji|  
|WebDocumentationTemplatesAddHeaders|Dodaj wymagane nagłówki HTTP|  
|WebDocumentationTemplatesBasicAuthSample|Przykład uwierzytelniania Basic Authorization|  
|WebDocumentationTemplatesCurlForBasicAuth|w przypadku uwierzytelniania podstawowego:--User {username}: {Password}|  
|WebDocumentationTemplatesCurlValuesForPath|Określ wartości dla parametrów ścieżki (pokazanych jako {...}), klucz subskrypcji i wartości parametrów zapytania|  
|WebDocumentationTemplatesDeveloperKey|Określ swój klucz subskrypcji|  
|WebDocumentationTemplatesJavaApache|Ten przykład używa klienta Apache HTTP z projektu HTTP Components) (http://hc.apache.org/httpcomponents-client-ga/)|  
|WebDocumentationTemplatesOptionalParams|W razie potrzeby określ wartości dla parametrów opcjonalnych|  
|WebDocumentationTemplatesPhpPackage|Ten przykład używa pakietu HTTP_Request2. (Aby uzyskać więcej informacji: https://pear.php.net/package/HTTP_Request2)|  
|WebDocumentationTemplatesPythonValuesForPath|W razie potrzeby określ wartości dla parametrów ścieżki (pokazane jako {...}) i treść żądania|  
|WebDocumentationTemplatesRequestBody|Określ treść żądania|  
|WebDocumentationTemplatesRequiredParams|Określ wartości dla następujących wymaganych parametrów|  
|WebDocumentationTemplatesValuesForPath|Określ wartości dla parametrów ścieżki (pokazanych jako {...})|  
|OAuth2AuthorizationEndpointDescription|Punkt końcowy autoryzacji jest używany do współdziałania z właścicielem zasobu i uzyskiwania uprawnień autoryzacji.|  
|OAuth2AuthorizationEndpointName|Punkt końcowy autoryzacji|  
|OAuth2TokenEndpointDescription|Punkt końcowy tokenu jest używany przez klienta do uzyskiwania tokenu dostępu przez przedprezentowanie jego autoryzacji lub tokenu odświeżenia.|  
|OAuth2TokenEndpointName|Punkt końcowy tokenu|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Description|< p\> klient inicjuje przepływ, kierując do punktu końcowego autoryzacji właściciela zasobu.  Klient zawiera identyfikator klienta, żądany zakres, stan lokalny i identyfikator URI przekierowania, do którego serwer autoryzacji wyśle agenta użytkownika po udzieleniu dostępu (lub odmowie).     </p\> < p\> serwer autoryzacji uwierzytelnia właściciela zasobu (za pośrednictwem agenta użytkownika) i ustali, czy właściciel zasobu przyznaje lub nie odmówi żądania dostępu klienta.     </p\> < p\> założenie, że właściciel zasobu przyznaje dostęp, serwer autoryzacji przekierowuje agenta użytkownika z powrotem do klienta przy użyciu podanego wcześniej identyfikatora URI przekierowania (w żądaniu lub podczas registrati klienta w systemie).  Identyfikator URI przekierowania zawiera kod autoryzacji i wszystkie stany lokalne dostarczone przez klienta wcześniej.     </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ErrorDescription|< p\> , jeśli użytkownik odrzuca żądanie dostępu, jeśli żądanie jest nieprawidłowe, klient zostanie poinformowany za pomocą następujących parametrów dodanych do przekierowania: </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Name|Żądanie autoryzacji|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_RequestDescription|< p\> aplikacja kliencka musi wysłać użytkownika do punktu końcowego autoryzacji w celu zainicjowania procesu uwierzytelniania OAuth.          W punkcie końcowym autoryzacji użytkownik jest uwierzytelniany, a następnie udziela lub nie odmawia dostępu do aplikacji.     </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ResponseDescription|< p\> założenie, że właściciel zasobu przyznaje dostęp, serwer autoryzacji przekierowuje agenta użytkownika z powrotem do klienta przy użyciu podanego wcześniej identyfikatora URI przekierowania (w żądaniu lub podczas rejestracji klienta).  Identyfikator URI przekierowania zawiera kod autoryzacji i wszystkie stany lokalne dostarczone przez klienta wcześniej. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_Description|< p\> klient żąda tokenu dostępu od punktu końcowego tokenu serwera autoryzacji, dołączając kod autoryzacji otrzymany w poprzednim kroku.  Podczas wykonywania żądania klient jest uwierzytelniany przy użyciu serwera autoryzacji.  Klient zawiera identyfikator URI przekierowania używany do uzyskania kodu autoryzacji w celu weryfikacji. </p\> < p\> serwer autoryzacji uwierzytelnia klienta, weryfikuje kod autoryzacji i zapewnia, że otrzymany identyfikator URI przekierowania jest zgodny z identyfikatorem URI używanym do przekierowania klienta w kroku (C).  Jeśli jest prawidłowy, serwer autoryzacji reaguje ponownie z tokenem dostępu i, opcjonalnie, tokenem odświeżania. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ErrorDescription|< p\> Jeśli uwierzytelnienie klienta nie powiodło się lub jest nieprawidłowe, serwer autoryzacji reaguje na kod stanu HTTP 400 (zły żądanie) (chyba że określono inaczej) i zawiera następujące parametry z odpowiedzią. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_RequestDescription|< p\> klient wysyła żądanie do punktu końcowego tokenu przez wysłanie następujących parametrów przy użyciu formatu "application/x-www-form-urlencoded" z kodowaniem znaków UTF-8 w treści żądania HTTP. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ResponseDescription|< p\> serwer autoryzacji wystawia token dostępu i opcjonalny token odświeżania i konstruuje odpowiedź poprzez dodanie następujących parametrów do treści jednostki odpowiedzi HTTP z kodem stanu 200 (ok). </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_Description|< p\> klient uwierzytelnia się przy użyciu serwera autoryzacji i żąda tokenu dostępu od punktu końcowego tokenu. </p\> < p\> serwer autoryzacji uwierzytelnia klienta, a jeśli jest prawidłowy, wystawia token dostępu. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ErrorDescription|< p\> , jeśli żądanie uwierzytelnienia klienta nie powiodło się lub jest nieprawidłowy, serwer autoryzacji reaguje na kod stanu HTTP 400 (zły żądanie) (chyba że określono inaczej) i zawiera następujące parametry z odpowiedzią. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_RequestDescription|< p\> klient wysyła żądanie do punktu końcowego tokenu przez dodanie następujących parametrów przy użyciu formatu "application/x-www-form-urlencoded" z kodowaniem znaków UTF-8 w treści żądania HTTP. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ResponseDescription|< p\> Jeśli żądanie tokenu dostępu jest prawidłowe i autoryzowane, serwer autoryzacji wystawia token dostępu i opcjonalny token odświeżania i konstruuje odpowiedź, dodając następujące parametry do treści jednostki odpowiedzi HTTP z 200  (OK) kod stanu. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_Description|< p\> klient inicjuje przepływ, kierując do punktu końcowego autoryzacji właściciela zasobu "User-Agent".  Klient zawiera identyfikator klienta, żądany zakres, stan lokalny i identyfikator URI przekierowania, do którego serwer autoryzacji wyśle agenta użytkownika po udzieleniu dostępu (lub odmowie). </p\> < p\> serwer autoryzacji uwierzytelnia właściciela zasobu (za pośrednictwem agenta użytkownika) i ustali, czy właściciel zasobu przyznaje lub nie odmówi żądania dostępu klienta. </p\> < p\> założenie, że właściciel zasobu przyznaje dostęp, serwer autoryzacji przekierowuje agenta użytkownika z powrotem do klienta przy użyciu podanego wcześniej identyfikatora URI przekierowania.  Identyfikator URI przekierowania zawiera token dostępu do fragmentu identyfikatora URI. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ErrorDescription|< p\> , jeśli właściciel zasobu odmówił żądania dostępu lub jeśli żądanie nie powiedzie się z powodu braku lub nieprawidłowego identyfikatora URI przekierowania, serwer autoryzacji informuje klienta, dodając następujące parametry do składnika fragmentu o f identyfikator URI przekierowania przy użyciu formatu "application/x-www-form-urlencoded". </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_RequestDescription|< p\> aplikacja kliencka musi wysłać użytkownika do punktu końcowego autoryzacji w celu zainicjowania procesu uwierzytelniania OAuth.      W punkcie końcowym autoryzacji użytkownik jest uwierzytelniany, a następnie udziela lub nie odmawia dostępu do aplikacji. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ResponseDescription|< p\> , jeśli właściciel zasobu przyznaje żądanie dostępu, serwer autoryzacji wystawia token dostępu i dostarcza go do klienta, dodając następujące parametry do składnika fragmentu identyfikatora URI przekierowania przy użyciu "application/x-www Format "form-urlencoded". </p\>|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Description|Przepływ kodu autoryzacji jest zoptymalizowany pod kątem klientów, którzy mogą utrzymywać poufność poświadczeń (np. aplikacje serwera sieci Web zaimplementowane przy użyciu języka PHP, Java, Python, Ruby, ASP.NET itp.).|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Name|Przyznano kod autoryzacji|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Description|Przepływ poświadczeń klienta jest odpowiedni w przypadkach, gdy klient (aplikacja) żąda dostępu do chronionych zasobów w ramach jego kontroli. Klient jest traktowany jako właściciel zasobu, więc nie jest wymagane interakcja z użytkownikiem końcowym.|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Name|Przyznanie poświadczenia klienta|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Description|Niejawny przepływ jest zoptymalizowany pod kątem klientów, którzy nie mogą utrzymywać poufności poświadczeń znanych do obsługi określonego identyfikatora URI przekierowania. Ci klienci są zwykle zaimplementowani w przeglądarce przy użyciu języka skryptowego, takiego jak JavaScript.|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Name|Niejawne przyznanie|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Description|Przepływ poświadczeń hasła właściciela zasobu jest odpowiedni w przypadkach, gdy właściciel zasobu ma relację zaufania z klientem (aplikacją), taką jak system operacyjny urządzenia lub wysoce uprzywilejowana aplikacja. Ten przepływ jest odpowiedni dla klientów z możliwością uzyskania poświadczeń właściciela zasobu (nazwy użytkownika i hasła, zazwyczaj przy użyciu formularza interaktywnego).|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Name|Przyznanie poświadczeń hasła właściciela zasobu|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_Description|< p\> Właściciel zasobu dostarcza klientowi nazwę użytkownika i hasło. </p\> < p\> klient żąda tokenu dostępu od punktu końcowego tokenu serwera autoryzacji, dołączając poświadczenia otrzymane od właściciela zasobu.  Podczas wykonywania żądania klient jest uwierzytelniany przy użyciu serwera autoryzacji. </p\> < p\> serwer autoryzacji uwierzytelnia klienta i sprawdza poprawność poświadczeń właściciela zasobu, a jeśli jest prawidłowy, wystawia token dostępu. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ErrorDescription|< p\> , jeśli żądanie uwierzytelnienia klienta nie powiodło się lub jest nieprawidłowy, serwer autoryzacji reaguje na kod stanu HTTP 400 (zły żądanie) (chyba że określono inaczej) i zawiera następujące parametry z odpowiedzią. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_RequestDescription|< p\> klient wysyła żądanie do punktu końcowego tokenu przez dodanie następujących parametrów przy użyciu formatu "application/x-www-form-urlencoded" z kodowaniem znaków UTF-8 w treści żądania HTTP. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ResponseDescription|< p\> Jeśli żądanie tokenu dostępu jest prawidłowe i autoryzowane, serwer autoryzacji wystawia token dostępu i opcjonalny token odświeżania i konstruuje odpowiedź, dodając następujące parametry do treści jednostki odpowiedzi HTTP z 20 0 (OK) kod stanu. </p\>|  
|OAuth2Step_AccessTokenRequest_Name|Żądanie tokenu dostępu|  
|OAuth2Step_AuthorizationRequest_Name|Żądanie autoryzacji|  
|OAuth2AccessToken_AuthorizationCodeGrant_TokenResponse|WYMAGANE. Token dostępu wystawiony przez serwer autoryzacji.|  
|OAuth2AccessToken_ClientCredentialsGrant_TokenResponse|WYMAGANE. Token dostępu wystawiony przez serwer autoryzacji.|  
|OAuth2AccessToken_ImplicitGrant_AuthorizationResponse|WYMAGANE. Token dostępu wystawiony przez serwer autoryzacji.|  
|OAuth2AccessToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|WYMAGANE. Token dostępu wystawiony przez serwer autoryzacji.|  
|OAuth2ClientId_AuthorizationCodeGrant_AuthorizationRequest|WYMAGANE. Identyfikator klienta.|  
|OAuth2ClientId_AuthorizationCodeGrant_TokenRequest|WYMAGANE, jeśli klient nie jest uwierzytelniany na serwerze autoryzacji.|  
|OAuth2ClientId_ImplicitGrant_AuthorizationRequest|WYMAGANE. Identyfikator klienta.|  
|OAuth2Code_AuthorizationCodeGrant_AuthorizationResponse|WYMAGANE. Kod autoryzacji generowany przez serwer autoryzacji.|  
|OAuth2Code_AuthorizationCodeGrant_TokenRequest|WYMAGANE. Kod autoryzacji otrzymany z serwera autoryzacji.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_AuthorizationErrorResponse|OBOWIĄZKOWE. Czytelny dla człowieka tekst ASCII zawierający dodatkowe informacje.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_TokenErrorResponse|OBOWIĄZKOWE. Czytelny dla człowieka tekst ASCII zawierający dodatkowe informacje.|  
|OAuth2ErrorDescription_ClientCredentialsGrant_TokenErrorResponse|OBOWIĄZKOWE. Czytelny dla człowieka tekst ASCII zawierający dodatkowe informacje.|  
|OAuth2ErrorDescription_ImplicitGrant_AuthorizationErrorResponse|OBOWIĄZKOWE. Czytelny dla człowieka tekst ASCII zawierający dodatkowe informacje.|  
|OAuth2ErrorDescription_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|OBOWIĄZKOWE. Czytelny dla człowieka tekst ASCII zawierający dodatkowe informacje.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_AuthorizationErrorResponse|OBOWIĄZKOWE. Identyfikator URI identyfikujący stronę sieci Web czytelną dla człowieka z informacjami o błędzie.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_TokenErrorResponse|OBOWIĄZKOWE. Identyfikator URI identyfikujący stronę sieci Web czytelną dla człowieka z informacjami o błędzie.|  
|OAuth2ErrorUri_ClientCredentialsGrant_TokenErrorResponse|OBOWIĄZKOWE. Identyfikator URI identyfikujący stronę sieci Web czytelną dla człowieka z informacjami o błędzie.|  
|OAuth2ErrorUri_ImplicitGrant_AuthorizationErrorResponse|OBOWIĄZKOWE. Identyfikator URI identyfikujący stronę sieci Web czytelną dla człowieka z informacjami o błędzie.|  
|OAuth2ErrorUri_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|OBOWIĄZKOWE. Identyfikator URI identyfikujący stronę sieci Web czytelną dla człowieka z informacjami o błędzie.|  
|OAuth2Error_AuthorizationCodeGrant_AuthorizationErrorResponse|WYMAGANE. Pojedynczy kod błędu ASCII z następujących: invalid_request, unauthorized_client, ACCESS_DENIED, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable.|  
|OAuth2Error_AuthorizationCodeGrant_TokenErrorResponse|WYMAGANE. Pojedynczy kod błędu ASCII z następujących: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2Error_ClientCredentialsGrant_TokenErrorResponse|WYMAGANE. Pojedynczy kod błędu ASCII z następujących: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2Error_ImplicitGrant_AuthorizationErrorResponse|WYMAGANE. Pojedynczy kod błędu ASCII z następujących: invalid_request, unauthorized_client, ACCESS_DENIED, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable.|  
|OAuth2Error_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|WYMAGANE. Pojedynczy kod błędu ASCII z następujących: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2ExpiresIn_AuthorizationCodeGrant_TokenResponse|REKOMENDOWANE. Okres istnienia w sekundach tokenu dostępu.|  
|OAuth2ExpiresIn_ClientCredentialsGrant_TokenResponse|REKOMENDOWANE. Okres istnienia w sekundach tokenu dostępu.|  
|OAuth2ExpiresIn_ImplicitGrant_AuthorizationResponse|REKOMENDOWANE. Okres istnienia w sekundach tokenu dostępu.|  
|OAuth2ExpiresIn_ResourceOwnerPasswordCredentialsGrant_TokenResponse|REKOMENDOWANE. Okres istnienia w sekundach tokenu dostępu.|  
|OAuth2GrantType_AuthorizationCodeGrant_TokenRequest|WYMAGANE. Wartość musi być równa "authorization_code".|  
|OAuth2GrantType_ClientCredentialsGrant_TokenRequest|WYMAGANE. Wartość musi być równa "client_credentials".|  
|OAuth2GrantType_ResourceOwnerPasswordCredentialsGrant_TokenRequest|WYMAGANE. Wartość musi być równa "Password" (hasło).|  
|OAuth2Password_ResourceOwnerPasswordCredentialsGrant_TokenRequest|WYMAGANE. Hasło właściciela zasobu.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_AuthorizationRequest|OBOWIĄZKOWE. Identyfikator URI punktu końcowego przekierowania musi być bezwzględnym identyfikatorem URI.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_TokenRequest|WYMAGANE, jeśli parametr "redirect_uri" został uwzględniony w żądaniu autoryzacji i ich wartości muszą być identyczne.|  
|OAuth2RedirectUri_ImplicitGrant_AuthorizationRequest|OBOWIĄZKOWE. Identyfikator URI punktu końcowego przekierowania musi być bezwzględnym identyfikatorem URI.|  
|OAuth2RefreshToken_AuthorizationCodeGrant_TokenResponse|OBOWIĄZKOWE. Token odświeżania, który może służyć do uzyskiwania nowych tokenów dostępu.|  
|OAuth2RefreshToken_ClientCredentialsGrant_TokenResponse|OBOWIĄZKOWE. Token odświeżania, który może służyć do uzyskiwania nowych tokenów dostępu.|  
|OAuth2RefreshToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|OBOWIĄZKOWE. Token odświeżania, który może służyć do uzyskiwania nowych tokenów dostępu.|  
|OAuth2ResponseType_AuthorizationCodeGrant_AuthorizationRequest|WYMAGANE. Wartość musi być równa "Code".|  
|OAuth2ResponseType_ImplicitGrant_AuthorizationRequest|WYMAGANE. Wartość musi być równa "token".|  
|OAuth2Scope_AuthorizationCodeGrant_AuthorizationRequest|OBOWIĄZKOWE. Zakres żądania dostępu.|  
|OAuth2Scope_AuthorizationCodeGrant_TokenResponse|OPCJONALNE, jeśli są identyczne z zakresem żądanym przez klienta; w przeciwnym razie jest to wymagane.|  
|OAuth2Scope_ClientCredentialsGrant_TokenRequest|OBOWIĄZKOWE. Zakres żądania dostępu.|  
|OAuth2Scope_ClientCredentialsGrant_TokenResponse|OPCJONALNE, jeśli taka sama jak zakres żądany przez klienta; w przeciwnym razie jest to wymagane.|  
|OAuth2Scope_ImplicitGrant_AuthorizationRequest|OBOWIĄZKOWE. Zakres żądania dostępu.|  
|OAuth2Scope_ImplicitGrant_AuthorizationResponse|OPCJONALNE, jeśli są identyczne z zakresem żądanym przez klienta; w przeciwnym razie jest to wymagane.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenRequest|OBOWIĄZKOWE. Zakres żądania dostępu.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenResponse|OPCJONALNE, jeśli taka sama jak zakres żądany przez klienta; w przeciwnym razie jest to wymagane.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationErrorResponse|WYMAGANE, jeśli parametr "State" był obecny w żądaniu autoryzacji klienta.  Dokładna wartość odebrana od klienta.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationRequest|REKOMENDOWANE. Wartość nieprzezroczysta używana przez klienta do utrzymania stanu między żądaniem a wywołaniem zwrotnym.  Serwer autoryzacji zawiera tę wartość podczas przekierowywania agenta użytkownika do klienta programu.  Parametr powinien być używany do zapobiegania fałszerstwu żądań między lokacjami.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationResponse|WYMAGANE, jeśli parametr "State" był obecny w żądaniu autoryzacji klienta.  Dokładna wartość odebrana od klienta.|  
|OAuth2State_ImplicitGrant_AuthorizationErrorResponse|WYMAGANE, jeśli parametr "State" był obecny w żądaniu autoryzacji klienta.  Dokładna wartość odebrana od klienta.|  
|OAuth2State_ImplicitGrant_AuthorizationRequest|REKOMENDOWANE. Wartość nieprzezroczysta używana przez klienta do utrzymania stanu między żądaniem a wywołaniem zwrotnym.  Serwer autoryzacji zawiera tę wartość podczas przekierowywania agenta użytkownika do klienta programu.  Parametr powinien być używany do zapobiegania fałszerstwu żądań między lokacjami.|  
|OAuth2State_ImplicitGrant_AuthorizationResponse|WYMAGANE, jeśli parametr "State" był obecny w żądaniu autoryzacji klienta.  Dokładna wartość odebrana od klienta.|  
|OAuth2TokenType_AuthorizationCodeGrant_TokenResponse|WYMAGANE. Typ wystawionego tokenu.|  
|OAuth2TokenType_ClientCredentialsGrant_TokenResponse|WYMAGANE. Typ wystawionego tokenu.|  
|OAuth2TokenType_ImplicitGrant_AuthorizationResponse|WYMAGANE. Typ wystawionego tokenu.|  
|OAuth2TokenType_ResourceOwnerPasswordCredentialsGrant_TokenResponse|WYMAGANE. Typ wystawionego tokenu.|  
|OAuth2UserName_ResourceOwnerPasswordCredentialsGrant_TokenRequest|WYMAGANE. Nazwa użytkownika właściciela zasobu.|  
|OAuth2UnsupportedTokenType|Typ tokenu "{0}" nie jest obsługiwany.|  
|OAuth2InvalidState|Nieprawidłowa odpowiedź z serwera autoryzacji|  
|OAuth2GrantType_AuthorizationCode|Kod autoryzacji|  
|OAuth2GrantType_Implicit|Niejawne|  
|OAuth2GrantType_ClientCredentials|Poświadczenia klienta|  
|OAuth2GrantType_ResourceOwnerPassword|Hasło właściciela zasobu|  
|WebDocumentation302Code|302 — Znaleziono|  
|WebDocumentation400Code|400 (Nieprawidłowe żądanie)|  
|OAuth2SendingMethod_AuthHeader|Nagłówek uwierzytelnienia|  
|OAuth2SendingMethod_QueryParam|Parametr zapytania|  
|OAuth2AuthorizationServerGeneralException|Wystąpił błąd podczas autoryzowania dostępu za pośrednictwem{0}|  
|OAuth2AuthorizationServerCommunicationException|Nie można nawiązać połączenia HTTP z serwerem autoryzacji lub został on nieoczekiwanie zamknięty.|  
|WebDocumentationOAuth2GeneralErrorMessage|Wystąpił nieoczekiwany błąd.|  
|AuthorizationServerCommunicationException|Wystąpił wyjątek komunikacji z serwerem autoryzacji. Skontaktuj się z administratorem.|  
|TextblockSubscriptionKeyHeaderDescription|Klucz subskrypcji, który zapewnia dostęp do tego interfejsu API. Znaleziono w < profilu href = "/Developer"\></a.\>|  
|TextblockOAuthHeaderDescription|Token dostępu OAuth 2,0 otrzymany z < i\>{0}</i\>. Obsługiwane typy dotacji: < i\>{1}</i\>.|  
|TextblockContentTypeHeaderDescription|Typ nośnika treści wysyłanej do interfejsu API.|  
|ErrorMessageApiNotAccessible|Interfejs API, który próbujesz wywołać, jest w tej chwili niedostępny. Skontaktuj się z wydawcą interfejsu API < a href =\>"/Issues"\>tutaj </a.|  
|ErrorMessageApiTimedout|Interfejs API, który próbujesz wywołać, trwa dłużej niż zwykle, aby można było odzyskać odpowiedź. Skontaktuj się z wydawcą interfejsu API < a href =\>"/Issues"\>tutaj </a.|  
|BadRequestParameterExpected|Oczekiwano{0}parametru "" "|  
|TooltipTextDoubleClickToSelectAll|Kliknij dwukrotnie, aby zaznaczyć wszystkie.|  
|TooltipTextHideRevealSecret|Pokaż/Ukryj|  
|ButtonLinkOpenConsole|Wypróbuj|  
|SectionHeadingRequestBody|Treść żądania|  
|SectionHeadingRequestParameters|Parametry żądania|  
|SectionHeadingRequestUrl|Adres URL żądania|  
|SectionHeadingResponse|Odpowiedź|  
|SectionHeadingRequestHeaders|Nagłówki żądań|  
|FormLabelSubtextOptional|opcjonalne|  
|SectionHeadingCodeSamples|Przykłady kodu|  
|TextblockOpenidConnectHeaderDescription|Token identyfikatora połączenia OpenID Connect uzyskany z <\>i{0}<\>/i. Obsługiwane typy dotacji: < i\>{1}</i\>.|  
  
###  <a name="ErrorPageStrings"></a>ErrorPageStrings  
  
|Name|Text|  
|----------|----------|  
|LinkLabelBack|wstecz|  
|LinkLabelHomePage|stronę główną|  
|LinkLabelSendUsEmail|Wyślij do nas wiadomość e-mail|  
|PageTitleError|Niestety, wystąpił problem z zażądaną stroną|  
|TextblockPotentialCauseIntermittentIssue|Może to być sporadyczny problem z dostępem do danych, który już nie został usunięty.|  
|TextblockPotentialCauseOldLink|Kliknięty link może być już stary i nie wskazuje na poprawną lokalizację.|  
|TextblockPotentialCauseTechnicalProblem|Na naszym końcu może wystąpić problem techniczny.|  
|TextblockPotentialSolutionRefresh|Spróbuj odświeżyć stronę.|  
|TextblockPotentialSolutionStartOver|Zacznij od firmy Microsoft {0}.|  
|TextblockPotentialSolutionTryAgain|Przejdź {0} i spróbuj ponownie wykonać akcję.|  
|TextReportProblem|{0}Zapoznaj się z błędami i poinformujemy o tym, jak tylko będzie to możliwe.|  
|TitlePotentialCause|Prawdopodobna przyczyna|  
|TitlePotentialSolution|Prawdopodobnie tylko tymczasowy problem — kilka rzeczy do wypróbowania|  
  
###  <a name="IssuesStrings"></a>IssuesStrings  
  
|Name|Text|  
|----------|----------|  
|WebIssuesIndexTitle|Problemy|  
|WebIssuesNoActiveSubscriptions|Nie masz aktywnych subskrypcji. Musisz subskrybować produkt, aby zgłosić problem.|  
|WebIssuesNotSignin|Użytkownik nie jest zalogowany. {0} Aby zgłosić problem lub opublikować komentarz.|  
|WebIssuesReportIssueButton|Zgłoś problem|  
|WebIssuesSignIn|zaloguj się|  
|WebIssuesStatusReportedBy|Stan: {0} &#124; zgłoszone przez{1}|  
  
###  <a name="NotFoundStrings"></a>NotFoundStrings  
  
|Name|Text|  
|----------|----------|  
|LinkLabelHomePage|stronę główną|  
|LinkLabelSendUsEmail|Wyślij do nas wiadomość e-mail|  
|PageTitleNotFound|Niestety, nie można odnaleźć strony, której szukasz|  
|TextblockPotentialCauseMisspelledUrl|Jeśli wpisano ten adres URL, być może został on wpisany nieprawidłowo.|  
|TextblockPotentialCauseOldLink|Kliknięty link może być już stary i nie wskazuje na poprawną lokalizację.|  
|TextblockPotentialSolutionRetype|Spróbuj ponownie wpisać adres URL.|  
|TextblockPotentialSolutionStartOver|Zacznij od firmy Microsoft {0}.|  
|TextReportProblem|{0}Zapoznaj się z błędami i poinformujemy o tym, jak tylko będzie to możliwe.|  
|TitlePotentialCause|Prawdopodobna przyczyna|  
|TitlePotentialSolution|Potencjalne rozwiązanie|  
  
###  <a name="ProductDetailsStrings"></a>ProductDetailsStrings  
  
|Name|Text|  
|----------|----------|  
|WebProductsAgreement|Subskrybując {0} produkt, zgadzam `<a data-toggle='modal' href='#legal-terms'\>Terms of Use</a\>`się na.|  
|WebProductsLegalTermsLink|Warunki użytkowania|  
|WebProductsSubscribeButton|Subskrybuj|  
|WebProductsUsageLimitsHeader|Limity użycia|  
|WebProductsYouAreNotSubscribed|Subskrybujesz ten produkt.|  
|WebProductsYouRequestedSubscription|Zażądano subskrypcji tego produktu.|  
|ErrorYouNeedToAgreeWithLegalTerms|Przed kontynuowaniem należy wyrazić zgodę na warunki użytkowania.|  
|ButtonLabelAddSubscription|Dodaj subskrypcję|  
|LinkLabelChangeSubscriptionName|zmień|  
|ButtonLabelConfirm|Potwierdź|  
|TextblockMultipleSubscriptionsCount|Masz {0} subskrypcje na ten produkt:|  
|TextblockSingleSubscriptionsCount|Masz {0} subskrypcję tego produktu:|  
|TextblockSingleApisCount|Ten produkt zawiera {0} interfejs API:|  
|TextblockMultipleApisCount|Ten produkt zawiera {0} interfejsy API:|  
|TextblockHeaderSubscribe|Zasubskrybuj produkt|  
|TextblockSubscriptionDescription|Zostanie utworzona nowa subskrypcja:|  
|TextblockSubscriptionLimitReached|Osiągnięto limit subskrypcji.|  
  
###  <a name="ProductsStrings"></a>ProductsStrings  
  
|Name|Text|  
|----------|----------|  
|PageTitleProducts|Produkty|  
  
###  <a name="ProviderInfoStrings"></a>ProviderInfoStrings  
  
|Name|Text|  
|----------|----------|  
|TextboxExternalIdentitiesDisabled|Logowanie jest w tej chwili wyłączone przez administratorów.|  
|TextboxExternalIdentitiesSigninInvitation|Alternatywnie Zaloguj się przy użyciu|  
|TextboxExternalIdentitiesSigninInvitationPrimary|Zaloguj się przy użyciu:|  
  
###  <a name="SigninResources"></a>SigninResources  
  
|Name|Text|  
|----------|----------|  
|PrincipalNotFound|Nie znaleziono podmiotu zabezpieczeń lub podpis jest nieprawidłowy|  
|ErrorSsoAuthenticationFailed|Uwierzytelnianie logowania jednokrotnego nie powiodło się|  
|ErrorSsoAuthenticationFailedDetailed|Podano nieprawidłowy token lub nie można zweryfikować podpisu.|  
|ErrorSsoTokenInvalid|Token logowania jednokrotnego jest nieprawidłowy|  
|ValidationErrorSpecificEmailAlreadyExists|Wiadomość e-mail{0}"" została już zarejestrowana|  
|ValidationErrorSpecificEmailInvalid|Adres e-mail{0}"" jest nieprawidłowy|  
|ValidationErrorPasswordInvalid|Hasło jest nieprawidłowe. Popraw błędy i spróbuj ponownie.|  
|PropertyTooShort|{0}jest za krótki|  
|WebAuthenticationAddresserEmailInvalidErrorMessage|Nieprawidłowy adres e-mail.|  
|ValidationMessageNewPasswordConfirmationRequired|Potwierdź nowe hasło|  
|ValidationErrorPasswordConfirmationRequired|Potwierdzenie hasła jest puste|  
|WebAuthenticationEmailChangeNotice|Wiadomość e-mail z potwierdzeniem zmiany jest w {0}sposób do. Aby potwierdzić swój nowy adres e-mail, postępuj zgodnie z instrukcjami w tym obszarze. Jeśli wiadomość e-mail nie dotarła do skrzynki odbiorczej w ciągu najbliższych kilku minut, sprawdź folder wiadomości-śmieci.|  
|WebAuthenticationEmailChangeNoticeHeader|Żądanie zmiany adresu e-mail zostało pomyślnie przetworzone|  
|WebAuthenticationEmailChangeNoticeTitle|Zażądano zmiany adresu e-mail|  
|WebAuthenticationEmailHasBeenRevertedNotice|Adres e-mail już istnieje. Żądanie zostało cofnięte|  
|ValidationErrorEmailAlreadyExists|Adres e-mail już istnieje|  
|ValidationErrorEmailInvalid|Nieprawidłowy adres e-mail|  
|TextboxLabelEmail|Email|  
|ValidationErrorEmailRequired|Adres e-mail jest wymagany.|  
|WebAuthenticationErrorNoticeHeader|Błąd|  
|WebAuthenticationFieldLengthErrorMessage|{0}musi mieć maksymalną długość równą{1}|  
|TextboxLabelEmailFirstName|Imię|  
|ValidationErrorFirstNameRequired|Imię jest wymagane.|  
|ValidationErrorFirstNameInvalid|Nieprawidłowe imię|  
|NoticeInvalidInvitationToken|Należy pamiętać, że linki potwierdzające są prawidłowe tylko przez 48 godzin. Jeśli nadal znajduje się w tym przedziale czasowym, upewnij się, że link jest prawidłowy. Jeśli link wygasł, powtórz akcję, którą próbujesz potwierdzić.|  
|NoticeHeaderInvalidInvitationToken|Nieprawidłowy token zaproszenia|  
|NoticeTitleInvalidInvitationToken|Błąd potwierdzenia|  
|WebAuthenticationLastNameInvalidErrorMessage|Nieprawidłowe nazwisko|  
|TextboxLabelEmailLastName|Nazwisko|  
|ValidationErrorLastNameRequired|Nazwisko jest wymagane.|  
|WebAuthenticationLinkExpiredNotice|Link do potwierdzenia został wysłany do Ciebie. `<a href={0}?token={1}>Resend confirmation email.</a\>`|  
|NoticePasswordResetLinkInvalidOrExpired|Łącze do resetowania hasła jest nieprawidłowe lub wygasło.|  
|WebAuthenticationLinkExpiredNoticeTitle|Link został wysłany|  
|WebAuthenticationNewPasswordLabel|Nowe hasło|  
|ValidationMessageNewPasswordRequired|Nowe hasło jest wymagane.|  
|TextboxLabelNotificationsSenderEmail|Adres e-mail nadawcy dla powiadomień|  
|TextboxLabelOrganizationName|Nazwa organizacji|  
|WebAuthenticationOrganizationRequiredErrorMessage|Nazwa organizacji jest pusta|  
|WebAuthenticationPasswordChangedNotice|Twoje hasło zostało pomyślnie zaktualizowane|  
|WebAuthenticationPasswordChangedNoticeTitle|Hasło zostało zaktualizowane|  
|WebAuthenticationPasswordCompareErrorMessage|Hasła nie są zgodne|  
|WebAuthenticationPasswordConfirmLabel|Potwierdź hasło|  
|ValidationErrorPasswordInvalidDetailed|Hasło jest zbyt słabe.|  
|WebAuthenticationPasswordLabel|Hasło|  
|ValidationErrorPasswordRequired|Hasło jest wymagane.|  
|WebAuthenticationPasswordResetSendNotice|Zmiana hasła wiadomość e-mail z potwierdzeniem jest w {0}sposób do. Postępuj zgodnie z instrukcjami w wiadomości e-mail, aby kontynuować proces zmiany hasła.|  
|WebAuthenticationPasswordResetSendNoticeHeader|Żądanie zresetowania hasła zostało pomyślnie przetworzone|  
|WebAuthenticationPasswordResetSendNoticeTitle|Zażądano zresetowania hasła|  
|WebAuthenticationRequestNotFoundNotice|Nie odnaleziono żądania|  
|WebAuthenticationSenderEmailRequiredErrorMessage|Adres e-mail nadawcy powiadomień jest pusty|  
|WebAuthenticationSigninPasswordLabel|Potwierdź zmianę, wprowadzając hasło|  
|WebAuthenticationSignupConfirmNotice|Wiadomość e-mail z potwierdzeniem rejestracji jest w {0}drodze do. <\> br/postępuj zgodnie z instrukcjami w wiadomości e-mail, aby aktywować konto\> . < br/Jeśli wiadomość e-mail nie dotarła do skrzynki odbiorczej w ciągu najbliższych kilku minut, należy Sprawdź folder wiadomości-śmieci.|  
|WebAuthenticationSignupConfirmNoticeHeader|Twoje konto zostało pomyślnie utworzone|  
|WebAuthenticationSignupConfirmNoticeRepeatHeader|Wiadomość e-mail z potwierdzeniem rejestracji została wysłana ponownie|  
|WebAuthenticationSignupConfirmNoticeTitle|Utworzono konto|  
|WebAuthenticationTokenRequiredErrorMessage|Token jest pusty|  
|WebAuthenticationUserAlreadyRegisteredNotice|Wydaje się, że użytkownik z tą wiadomością e-mail jest już zarejestrowany w systemie. Jeśli nie pamiętasz hasła, spróbuj je przywrócić lub skontaktuj się z naszym zespołem pomocy technicznej.|  
|WebAuthenticationUserAlreadyRegisteredNoticeHeader|Użytkownik jest już zarejestrowany|  
|WebAuthenticationUserAlreadyRegisteredNoticeTitle|Już zarejestrowany|  
|ButtonLabelChangePassword|Zmień hasło|  
|ButtonLabelChangeAccountInfo|Zmień informacje konta|  
|ButtonLabelCloseAccount|Zamknij konto|  
|WebAuthenticationInvalidCaptchaErrorMessage|Wprowadzony tekst nie jest zgodny z tekstem na obrazku. Spróbuj ponownie.|  
|ValidationErrorCredentialsInvalid|Adres e-mail lub hasło są nieprawidłowe. Popraw błędy i spróbuj ponownie.|  
|WebAuthenticationRequestIsNotValid|Żądanie jest nieprawidłowe|  
|WebAuthenticationUserIsNotConfirm|Potwierdź rejestrację przed podjęciem próby zalogowania się.|  
|WebAuthenticationInvalidEmailFormated|Adres e-mail jest nieprawidłowy:{0}|  
|WebAuthenticationUserNotFound|Nie znaleziono użytkownika|  
|WebAuthenticationTenantNotRegistered|Twoje konto należy do dzierżawy Azure Active Directory, która nie ma autoryzacji do uzyskiwania dostępu do tego portalu.|  
|WebAuthenticationAuthenticationFailed|Uwierzytelnianie nie powiodło się.|  
|WebAuthenticationGooglePlusNotEnabled|Uwierzytelnianie nie powiodło się. Jeśli aplikacja została autoryzowana, skontaktuj się z administratorem, aby upewnić się, że uwierzytelnianie Google zostało prawidłowo skonfigurowane.|  
|ValidationErrorAllowedTenantIsRequired|Dozwolona dzierżawa jest wymagana|  
|ValidationErrorTenantIsNotValid|Dzierżawca Azure Active Directory "{0}" jest nieprawidłowy.|  
|WebAuthenticationActiveDirectoryTitle|Usługa Azure Active Directory|  
|WebAuthenticationLoginUsingYourProvider|Zaloguj się przy użyciu {0} swojego konta|  
|WebAuthenticationUserLimitNotice|Ta usługa osiągnęła maksymalną liczbę dozwolonych użytkowników. Wykonaj `<a href="mailto:{0}"\>contact the administrator</a\>` uaktualnienie swojej usługi i ponownie Włącz rejestrację użytkownika.|  
|WebAuthenticationUserLimitNoticeHeader|Rejestracja użytkowników jest wyłączona|  
|WebAuthenticationUserLimitNoticeTitle|Rejestracja użytkowników jest wyłączona|  
|WebAuthenticationUserRegistrationDisabledNotice|Rejestracja użytkowników została wyłączona przez administratora. Zaloguj się przy użyciu zewnętrznego dostawcy tożsamości.|  
|WebAuthenticationUserRegistrationDisabledNoticeHeader|Rejestracja użytkowników jest wyłączona|  
|WebAuthenticationUserRegistrationDisabledNoticeTitle|Rejestracja użytkowników jest wyłączona|  
|WebAuthenticationSignupPendingConfirmationNotice|Zanim będziemy mogli zakończyć tworzenie konta, musimy zweryfikować swój adres e-mail. Wysłaliśmy wiadomość e-mail na {0}adres. Aby aktywować konto, postępuj zgodnie z instrukcjami w wiadomości e-mail. Jeśli wiadomość e-mail nie dotarła w ciągu kilku następnych minut, sprawdź folder wiadomości-śmieci.|  
|WebAuthenticationSignupPendingConfirmationAccountFoundNotice|Znaleźliśmy niepotwierdzone konto dla adresu {0}e-mail. Aby ukończyć tworzenie konta, musimy zweryfikować swój adres e-mail. Wysłaliśmy wiadomość e-mail na {0}adres. Aby aktywować konto, postępuj zgodnie z instrukcjami w wiadomości e-mail. Jeśli wiadomość e-mail nie dotarła w ciągu kilku następnych minut, sprawdź folder wiadomości-śmieci.|  
|WebAuthenticationSignupConfirmationAlmostDone|Prawie gotowe|  
|WebAuthenticationSignupConfirmationEmailSent|Wysłaliśmy wiadomość e-mail na {0}adres. Aby aktywować konto, postępuj zgodnie z instrukcjami w wiadomości e-mail. Jeśli wiadomość e-mail nie dotarła w ciągu kilku następnych minut, sprawdź folder wiadomości-śmieci.|  
|WebAuthenticationEmailSentNotificationMessage|Wiadomość e-mail została pomyślnie wysłana do{0}|  
|WebAuthenticationNoAadTenantConfigured|Brak skonfigurowanej dzierżawy Azure Active Directory dla usługi.|  
|CheckboxLabelUserRegistrationTermsConsentRequired|Zgadzam się na `<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use</a\>`.|  
|TextblockUserRegistrationTermsProvided|Przejrzyj`<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use.</a\>`|  
|DialogHeadingTermsOfUse|Warunki użytkowania|  
|ValidationMessageConsentNotAccepted|Przed kontynuowaniem należy wyrazić zgodę na warunki użytkowania.|  
  
###  <a name="SigninStrings"></a>SigninStrings  
  
|Name|Text|  
|----------|----------|  
|WebAuthenticationForgotPassword|Nie pamiętasz hasła?|  
|WebAuthenticationIfAdministrator|Jeśli jesteś administratorem, musisz się zalogować `<a href="{0}"\>here</a\>`.|  
|WebAuthenticationNotAMember|Nie jesteś jeszcze członkiem? `<a href="/signup"\>Sign up now</a\>`|  
|WebAuthenticationRemember|Pamiętaj mnie na tym komputerze|  
|WebAuthenticationSigininWithPassword|Zaloguj się przy użyciu nazwy użytkownika i hasła|  
|WebAuthenticationSigninTitle|Logowanie|  
|WebAuthenticationSignUpNow|Zarejestruj się teraz|  
  
###  <a name="SignupStrings"></a>SignupStrings  
  
|Name|Text|  
|----------|----------|  
|PageTitleSignup|Zarejestruj się|  
|WebAuthenticationAlreadyAMember|Jesteś już członkiem?|  
|WebAuthenticationCreateNewAccount|Utwórz nowe konto API Management|  
|WebAuthenticationSigninNow|Logowanie|  
|ButtonLabelSignup|Zarejestruj się|  
  
###  <a name="SubscriptionListStrings"></a>SubscriptionListStrings  
  
|Name|Text|  
|----------|----------|  
|SubscriptionCancelConfirmation|Czy na pewno chcesz anulować tę subskrypcję?|  
|SubscriptionRenewConfirmation|Czy na pewno chcesz odnowić tę subskrypcję?|  
|WebDevelopersManageSubscriptions|Zarządzaj subskrypcjami|  
|WebDevelopersPrimaryKey|Klucz podstawowy|  
|WebDevelopersRegenerateLink|Generuj ponownie|  
|WebDevelopersSecondaryKey|Klucz pomocniczy|  
|ButtonLabelShowKey|Pokaż|  
|ButtonLabelRenewSubscription|Odnów|  
|WebDevelopersSubscriptionRequested|Zażądano{0}|  
|WebDevelopersSubscriptionRequestedState|Żądane|  
|WebDevelopersSubscriptionTableNameHeader|Name|  
|WebDevelopersSubscriptionTableStateHeader|State|  
|WebDevelopersUsageStatisticsLink|Raporty analizy|  
|WebDevelopersYourSubscriptions|Twoje subskrypcje|  
|SubscriptionPropertyLabelRequestedDate|Żądanie zgłoszone w dniu|  
|SubscriptionPropertyLabelStartedDate|Rozpoczęto|  
|PageTitleRenameSubscription|Zmień nazwę subskrypcji|  
|SubscriptionPropertyLabelName|Nazwa subskrypcji|  
  
###  <a name="SubscriptionStrings"></a>SubscriptionStrings  
  
|Name|Text|  
|----------|----------|  
|SectionHeadingCloseAccount|Chcesz zamknąć konto?|  
|PageTitleDeveloperProfile|Profil|  
|ButtonLabelHideKey|Ukryj|  
|ButtonLabelRegenerateKey|Generuj ponownie|  
|InformationMessageKeyWasRegenerated|Czy na pewno chcesz ponownie wygenerować ten klucz?|  
|ButtonLabelShowKey|Pokaż|  
  
###  <a name="UpdateProfileStrings"></a>UpdateProfileStrings  
  
|Name|Text|  
|----------|----------|  
|ButtonLabelUpdateProfile|Aktualizowanie profilu|  
|PageTitleUpdateProfile|Aktualizowanie informacji o koncie|  
  
###  <a name="UserProfile"></a>UserProfile  
  
|Name|Text|  
|----------|----------|  
|ButtonLabelChangeAccountInfo|Zmień informacje konta|  
|ButtonLabelChangePassword|Zmień hasło|  
|ButtonLabelCloseAccount|Zamknij konto|  
|TextboxLabelEmail|Email|  
|TextboxLabelEmailFirstName|Imię|  
|TextboxLabelEmailLastName|Nazwisko|  
|TextboxLabelNotificationsSenderEmail|Adres e-mail nadawcy dla powiadomień|  
|TextboxLabelOrganizationName|Nazwa organizacji|  
|SubscriptionStateActive|Aktywne|  
|SubscriptionStateCancelled|Anulowane|  
|SubscriptionStateExpired|Wygaśnięcie|  
|SubscriptionStateRejected|Odrzucone|  
|SubscriptionStateRequested|Żądane|  
|SubscriptionStateSuspended|Suspended|  
|DefaultSubscriptionNameTemplate|{0}wartooć|  
|SubscriptionNameTemplate|Dostęp dla deweloperów #{0}|  
|TextboxLabelSubscriptionName|Nazwa subskrypcji|  
|ValidationMessageSubscriptionNameRequired|Nazwa subskrypcji nie może być pusta.|  
|ApiManagementUserLimitReached|Ta usługa osiągnęła maksymalną liczbę dozwolonych użytkowników. Przeprowadź uaktualnienie do wyższej warstwy cenowej.|  
  
##  <a name="glyphs"></a>Zasoby symboli  
 Szablony portalu dla deweloperów API Management mogą używać glifów z [Glyphicons z Bootstrap](https://getbootstrap.com/components/#glyphicons). Ten zestaw symboli zawiera ponad 250 symboli w formacie czcionki z zestawu [Glyphicon](https://glyphicons.com/) Halflings. Aby użyć symbolu z tego zestawu, użyj następującej składni.  
  
```html  
<span class="glyphicon glyphicon-user">  
```  
  
 Aby uzyskać pełną listę symboli, zobacz [Glyphicons from Bootstrap](https://getbootstrap.com/components/#glyphicons).

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [How to dostosowywanie portalu deweloperów API Management przy użyciu szablonów](api-management-developer-portal-templates.md).
