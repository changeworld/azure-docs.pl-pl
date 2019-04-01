---
title: Zasoby szablonu usługi Azure API Management | Dokumentacja firmy Microsoft
description: Dowiedz się więcej na temat typów zasobów, które są dostępne do użycia w szablonów portalu dla deweloperów w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 51a1b4c6-a9fd-4524-9e0e-03a9800c3e94
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: 673dcbeb630899eebc328cd4fae16f7fe8f47a55
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58757596"
---
# <a name="azure-api-management-template-resources"></a>Zasoby szablonu usługi Azure API Management
Usługa Azure API Management zawiera następujące typy zasobów, do użytku w Deweloper szablonów portalu.  
  
-   [Zasoby ciągów](#strings)  
  
-   [Zasoby glifów](#glyphs)  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="strings"></a> Zasoby ciągów  
 Usługa API Management oferuje kompleksowy zestaw zasobów ciągów do użycia w portalu dla deweloperów. Te zasoby są zlokalizowane we wszystkich językach obsługiwanych przez usługę API Management. Domyślny zestaw szablonów używa tych zasobów, nagłówki stron, etykiety i stałych ciągów, które są wyświetlane w portalu dla deweloperów. Aby użyć zasobu ciągu w szablonach, należy podać prefiks ciągu zasobu, następuje nazwa ciągu, jak pokazano w poniższym przykładzie.  
  
```  
{% localized "Prefix|Name" %}  
  
```  
  
 Poniższy przykład pochodzi z szablonu listy produktów i wyświetla **produktów** w górnej części strony.  
  
```  
<h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>  
  
```  
  
Obsługiwane są następujące opcje lokalizacji:

| Ustawienia regionalne    | Język               |
|-----------|------------------------|
| „pl”      | "Angielski"              |
| "cs"      | "Čeština"              |
| "de"      | "Deutsch"              |
| "es"      | "Hiszpański"              |
| "fr"      | "Français"             |
| "hu"      | "Magyar"               |
| "on"      | "(Wersja włoska)"             |
| "ja-JP"   | "日本語"                |
| "ko"      | "한국어"                |
| "nl"      | "Nederlands"           |
| "pl"      | "Polski"               |
| "pt-br"   | "Português (Brazylia)"   |
| "pt-pt"   | "Português (Portugalia)" |
| "ru"      | "Русский"              |
| "sv"      | "Svenska"              |
| "tr"      | "Türkçe"               |
| "nazwy zh-hans" | "中文(简体)"           |
| "zh-hant" | "中文(繁體)"           |

 Zapoznaj się z następującymi tabelami zasobów ciągu dostępne do użycia w Twojej szablonów portalu dla deweloperów. Użyto nazwy tabeli jako prefiks dla zasobów ciągów w tej tabeli.  
  
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
  
###  <a name="ApisStrings"></a> ApisStrings  
  
|Name (Nazwa)|Tekst|  
|----------|----------|  
|PageTitleApis|Interfejsy API|  
  
###  <a name="AppDetailsStrings"></a> AppDetailsStrings  
  
|Name (Nazwa)|Tekst|  
|----------|----------|  
|WebApplicationsDetailsTitle|Podgląd aplikacji|  
|WebApplicationsRequirementsHeader|Wymagania|  
|WebApplicationsScreenshotAlt|Zrzut ekranu|  
|WebApplicationsScreenshotsHeader|Zrzuty ekranu|  
  
###  <a name="ApplicationListStrings"></a> ApplicationListStrings  
  
|Name (Nazwa)|Tekst|  
|----------|----------|  
|WebDevelopersAppDeleteConfirmation|Czy na pewno chcesz usunąć aplikację?|  
|WebDevelopersAppNotPublished|Nie opublikowano|  
|WebDevelopersAppNotSubmitted|Nie przesłano|  
|WebDevelopersAppTableCategoryHeader|Kategoria|  
|WebDevelopersAppTableNameHeader|Name (Nazwa)|  
|WebDevelopersAppTableStateHeader|Stan|  
|WebDevelopersEditLink|Edytuj|  
|WebDevelopersRegisterAppLink|Zarejestruj aplikację|  
|WebDevelopersRemoveLink|Remove|  
|WebDevelopersSubmitLink|Submit|  
|WebDevelopersYourApplicationsHeader|Twoje aplikacje|  
  
###  <a name="AppStrings"></a> AppStrings  
  
|Name (Nazwa)|Tekst|  
|----------|----------|  
|WebApplicationsHeader|Aplikacje|  
  
###  <a name="CommonResources"></a> CommonResources  
  
|Name (Nazwa)|Tekst|  
|----------|----------|  
|NoItemsToDisplay|Brak wyników.|  
|GeneralExceptionMessage|Coś jest nie prawo. Może to być tymczasowy błąd lub usterka. Spróbuj ponownie.|  
|GeneralJsonExceptionMessage|Coś jest nie prawo. Może to być tymczasowy błąd lub usterka. Wykonaj ponownie załaduj stronę i spróbuj ponownie.|  
|ConfirmationMessageUnsavedChanges|Istnieją pewne niezapisane zmiany. Czy na pewno chcesz anulować i odrzucić zmiany?|  
|AzureActiveDirectory|Usługa Azure Active Directory|  
|HttpLargeRequestMessage|Treść żądania HTTP jest za duża.|  
  
###  <a name="CommonStrings"></a> CommonStrings  
  
|Name (Nazwa)|Tekst|  
|----------|----------|  
|ButtonLabelCancel|Cancel|  
|ButtonLabelSave|Zapisz|  
|GeneralExceptionMessage|Coś jest nie prawo. Może to być tymczasowy błąd lub usterka. Spróbuj ponownie.|  
|NoItemsToDisplay|Brak elementów do wyświetlenia.|  
|PagerButtonLabelFirst|Pierwsze|  
|PagerButtonLabelLast|Ostatnia|  
|PagerButtonLabelNext|Następne kroki|  
|PagerButtonLabelPrevious|Wstecz|  
|PagerLabelPageNOfM|Strona {0} programu {1}|  
|PasswordTooShort|Hasło jest za krótkie|  
|EmailAsPassword|Nie używaj swojego adresu e-mail jako hasła|  
|PasswordSameAsUserName|Hasło nie może zawierać Twojej nazwy użytkownika|  
|PasswordTwoCharacterClasses|Użyj innych klas znaków|  
|PasswordTooManyRepetitions|Za dużo powtórzeń|  
|PasswordSequenceFound|Twoje hasło zawiera sekwencje|  
|PagerLabelPageSize|Rozmiar strony|  
|CurtainLabelLoading|Ładowanie...|  
|TablePlaceholderNothingToDisplay|Brak danych dla wybranego okresu i zakresu|  
|ButtonLabelClose|Zamykanie|  
  
###  <a name="Documentation"></a> Dokumentacja  
  
|Name (Nazwa)|Tekst|  
|----------|----------|  
|WebDocumentationInvalidHeaderErrorMessage|Nieprawidłowy nagłówek "{0}"|  
|WebDocumentationInvalidRequestErrorMessage|Nieprawidłowy adres URL żądania|  
|TextboxLabelAccessToken|Access token *|  
|DropdownOptionPrimaryKeyFormat|Podstawowe-{0}|  
|DropdownOptionSecondaryKeyFormat|Secondary-{0}|  
|WebDocumentationSubscriptionKeyText|Twój klucz subskrypcji|  
|WebDocumentationTemplatesAddHeaders|Dodaj wymagane nagłówki HTTP|  
|WebDocumentationTemplatesBasicAuthSample|Przykład uwierzytelniania Basic Authorization|  
|WebDocumentationTemplatesCurlForBasicAuth|dla uwierzytelniania Basic Authorization użyj: --user {username}:{password}|  
|WebDocumentationTemplatesCurlValuesForPath|Określ wartości dla parametrów ścieżki (pokazane jako {...}), swój klucz subskrypcji i wartości parametrów zapytania|  
|WebDocumentationTemplatesDeveloperKey|Określ swój klucz subskrypcji|  
|WebDocumentationTemplatesJavaApache|Ten przykład używa klienta Apache HTTP z projektu HTTP Components)http://hc.apache.org/httpcomponents-client-ga/)|  
|WebDocumentationTemplatesOptionalParams|W razie potrzeby określ wartości dla parametrów opcjonalnych|  
|WebDocumentationTemplatesPhpPackage|Ten przykład używa pakietu HTTP_Request2. (Aby uzyskać więcej informacji: https://pear.php.net/package/HTTP_Request2)|  
|WebDocumentationTemplatesPythonValuesForPath|W razie potrzeby określ wartości parametrów ścieżki (pokazanych jako {...}) i treść żądania|  
|WebDocumentationTemplatesRequestBody|Określ treść żądania|  
|WebDocumentationTemplatesRequiredParams|Określ wartości dla następujących wymaganych parametrów|  
|WebDocumentationTemplatesValuesForPath|Określ wartości dla parametrów ścieżki (pokazanych jako {...})|  
|OAuth2AuthorizationEndpointDescription|Punkt końcowy autoryzacji służy do interakcji z właścicielem zasobu i uzyskania przydziału autoryzacji.|  
|OAuth2AuthorizationEndpointName|Punkt końcowy autoryzacji|  
|OAuth2TokenEndpointDescription|Punkt końcowy tokenu jest używany przez klienta do uzyskania tokenu dostępu poprzez przedstawienie przyznania autoryzacji lub tokenu odświeżenia.|  
|OAuth2TokenEndpointName|Punkt końcowy tokenu|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Description|< p\> klienta inicjuje przepływ, kierując właściciel zasobu agenta użytkownika do punktu końcowego autoryzacji.  Klient dołącza identyfikator klienta, żądanym zakresie, stan lokalnego i identyfikator URI przekierowania, do którego serwer autoryzacji wyśle do agenta użytkownika, ponownie, gdy dostęp jest udzielany (lub odmowa).     < /p\> < p\> serwer autoryzacji uwierzytelnia właściciel zasobu (za pośrednictwem agenta użytkownika) i określa, czy właściciel zasobu lub go przydziela odrzuca żądanie dostępu klienta.     < /p\> < p\> zakładając, że właściciel zasobu przyzna dostęp, serwer autoryzacji przekieruje agenta użytkownika do klienta przy użyciu podanego identyfikatora URI przekierowania wcześniej (w żądaniu lub podczas registrati klienta Funkcja włączona).  Identyfikator URI przekierowania zawiera kod autoryzacji i dowolny stan lokalny podany przez klienta wcześniej.     </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ErrorDescription|< p\> Jeśli użytkownik odrzuca żądanie dostępu lub, jeśli żądanie jest nieprawidłowe, klient zostanie poinformowany, korzystając z następujących parametrów dodanych do przekierowania: < /p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Name|Żądanie autoryzacji|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_RequestDescription|< p\> aplikacja kliencka musi wysłać użytkownika do punktu końcowego autoryzacji w celu zainicjowania procesu uwierzytelniania OAuth.          W punkcie końcowym autoryzacji użytkownik uwierzytelnia i następnie przyznaje lub nie zezwala na dostęp do aplikacji.     </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ResponseDescription|< p\> zakładając, że właściciel zasobu przyzna dostęp, serwer autoryzacji przekieruje agenta użytkownika do klienta przy użyciu podanego identyfikatora URI przekierowania wcześniej (w żądaniu lub podczas rejestracji klienta).  Identyfikator URI przekierowania zawiera kod autoryzacji i dowolny stan lokalny podany przez klienta wcześniej. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_Description|< p\> klient żąda tokenu dostępu z serwera autoryzacji "s punktu końcowego tokenu, dołączając kod autoryzacji odebrany w poprzednim kroku.  Podczas wykonywania żądania, klient uwierzytelnia się za pomocą serwera autoryzacji.  Klient dołącza przekierowania URI używany do uzyskania kodu autoryzacji do weryfikacji. < /p\> < p\> serwer autoryzacji uwierzytelnia klienta, weryfikuje kod autoryzacji i zapewnia zgodność Odebrano identyfikatora URI przekierowania URI używany do przekierowania klienta w kroku (C).  Jeśli jest on prawidłowy, serwer autoryzacji odpowiada z powrotem token dostępu i, opcjonalnie, token odświeżania. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ErrorDescription|< p\> Jeśli żądanie uwierzytelnienia klienta nie powiodło się lub jest nieprawidłowe, serwer autoryzacji odpowiada kodem stanu HTTP 400 (złe żądanie) (chyba że określono inaczej) i obejmuje następujące parametry z odpowiedzią. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_RequestDescription|< p\> klient wysyła żądanie do punktu końcowego tokenu, wysyłając następujące parametry przy użyciu formatu "application/x--www-form-urlencoded" z kodowaniem znaków UTF-8 w HTTP żądania treści jednostki. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ResponseDescription|< p\> serwer autoryzacji wystawia token dostępu i opcjonalny token odświeżania oraz buduje odpowiedź, dodając następujące parametry do treści jednostki odpowiedzi HTTP z kodem stanu 200 (OK). </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_Description|< p\> klient uwierzytelnia się za pomocą serwera autoryzacji i żąda tokenu dostępu z punktu końcowego tokenu. < /p\> < p\> serwer autoryzacji uwierzytelnia klienta, a jeśli są one prawidłowe, wystawia token dostępu. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ErrorDescription|< p\> Jeśli żądanie nie powiodło się uwierzytelnienie klienta lub jest nieprawidłowy serwer autoryzacji odpowiada kodem stanu HTTP 400 (złe żądanie) (chyba że określono inaczej) i obejmuje następujące parametry z odpowiedzią. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_RequestDescription|< p\> klient wysyła żądanie do punktu końcowego tokenu, dodając następujące parametry przy użyciu formatu "application/x--www-form-urlencoded" z kodowaniem znaków UTF-8 w HTTP żądania treści jednostki. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ResponseDescription|< p\> Jeśli żądanie tokenu dostępu jest prawidłowe i autoryzowane, serwer autoryzacji wystawia token dostępu i opcjonalny token odświeżania oraz buduje odpowiedź, dodając następujące parametry do treści jednostki odpowiedzi HTTP z 200  Kod stanu (OK). </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_Description|< p\> klienta inicjuje przepływ, kierując właściciel zasobu "s agenta użytkownika do punktu końcowego autoryzacji.  Klient dołącza identyfikator klienta, żądanym zakresie, stan lokalnego i identyfikator URI przekierowania, do którego serwer autoryzacji wyśle do agenta użytkownika, ponownie, gdy dostęp jest udzielany (lub odmowa). < /p\> < p\> serwer autoryzacji uwierzytelnia właściciel zasobu (za pośrednictwem agenta użytkownika) i określa, czy właściciel zasobu lub go przydziela nie zezwala na kliencie "żądanie dostępu. < /p\> < p\> zakładając, że właściciel zasobu przyzna dostęp, serwer autoryzacji przekieruje agenta użytkownika do klienta przy użyciu przekierowania podanego wcześniej identyfikatora URI.  Identyfikator URI przekierowania zawiera token dostępu w składnik fragment identyfikatora URI. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ErrorDescription|< p\> Jeśli właściciel zasobu odrzuca żądanie dostępu lub jeśli żądanie nie powiedzie się z powodów innych niż Brak lub nieprawidłowy identyfikator URI przekierowania, serwer autoryzacji informuje klienta, dodając następujące parametry do fragmentu o składnika f przy użyciu formatu "application/x--www-form-urlencoded" identyfikatora URI przekierowania. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_RequestDescription|< p\> aplikacja kliencka musi wysłać użytkownika do punktu końcowego autoryzacji w celu zainicjowania procesu uwierzytelniania OAuth.      W punkcie końcowym autoryzacji użytkownik uwierzytelnia i następnie przyznaje lub nie zezwala na dostęp do aplikacji. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ResponseDescription|< p\> Jeśli właściciel zasobu zaakceptuje żądanie dostępu, serwer autoryzacji wystawi token dostępu i dostarczy go do klienta, dodając następujące parametry do fragmentu składnika identyfikatora URI za pomocą "application/x-www przekierowania format — tworzą - urlencoded". </p\>|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Description|Przepływ kodu autoryzacji jest zoptymalizowany dla klientów zdolnych do zachowania poufności ich poświadczeń (np. aplikacji serwera internetowego zaimplementowanych przy użyciu języka PHP, Java, Python, Ruby, ASP.NET itd.).|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Name|Przyznano kod autoryzacji|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Description|Przepływ poświadczeń klienta jest odpowiedni w przypadkach, gdy klient (Twoja aplikacja) żąda dostępu do chronionych zasobów pod jego kontrolą. Klient jest uważany za właściciela zasobu, więc żadna interakcja użytkownika końcowego jest wymagane.|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Name|Przyznanie poświadczenia klienta|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Description|Niejawny przepływ jest optymalizowany dla klientów niezdolnych do zachowania poufności ich poświadczeń znane działanie określonego identyfikatora URI przekierowania. Ci klienci są zazwyczaj implementowani w przeglądarce za pomocą języka skryptów, takich jak JavaScript.|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Name|Niejawne przyznanie|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Description|Przepływ poświadczeń hasła właściciela zasobu jest odpowiedni w przypadkach, gdy właściciel zasobu ma relację zaufania z klientem (Twoją aplikacją), takich jak system operacyjny urządzenia lub aplikacji o wysokim poziomie uprawnień. Ten przepływ jest odpowiedni dla klientów zdolnych do uzyskania poświadczeń właściciela (nazwa użytkownika i hasło, zwykle za pomocą formularza interaktywnego) zasobu.|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Name|Przyznanie poświadczeń hasła właściciela zasobu|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_Description|< p\> właściciel zasobu przekazuje temu klientowi swoją nazwę użytkownika i hasło. < /p\> < p\> klient żąda tokenu dostępu z serwera autoryzacji "punktu końcowego tokenu s, dołączając poświadczenia otrzymane od właściciela zasobu.  Podczas wykonywania żądania, klient uwierzytelnia się za pomocą serwera autoryzacji. < /p\> < p\> serwer autoryzacji uwierzytelnia klienta i weryfikuje poświadczenia właściciela zasobu, a jeśli są one prawidłowe, wystawia token dostępu. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ErrorDescription|< p\> Jeśli żądanie nie powiodło się uwierzytelnienie klienta lub jest nieprawidłowy serwer autoryzacji odpowiada kodem stanu HTTP 400 (złe żądanie) (chyba że określono inaczej) i obejmuje następujące parametry z odpowiedzią. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_RequestDescription|< p\> klient wysyła żądanie do punktu końcowego tokenu, dodając następujące parametry przy użyciu formatu "application/x--www-form-urlencoded" z kodowaniem znaków UTF-8 w HTTP żądania treści jednostki. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ResponseDescription|< p\> Jeśli żądanie tokenu dostępu jest prawidłowe i autoryzowane, serwer autoryzacji wystawia token dostępu i opcjonalny token odświeżania oraz buduje odpowiedź, dodając następujące parametry do treści jednostki odpowiedzi HTTP z 20 Kod stanu (OK) 0. </p\>|  
|OAuth2Step_AccessTokenRequest_Name|Żądanie tokenu dostępu|  
|OAuth2Step_AuthorizationRequest_Name|Żądanie autoryzacji|  
|OAuth2AccessToken_AuthorizationCodeGrant_TokenResponse|WYMAGANE. Token dostępu wystawiony przez serwer autoryzacji.|  
|OAuth2AccessToken_ClientCredentialsGrant_TokenResponse|WYMAGANE. Token dostępu wystawiony przez serwer autoryzacji.|  
|OAuth2AccessToken_ImplicitGrant_AuthorizationResponse|WYMAGANE. Token dostępu wystawiony przez serwer autoryzacji.|  
|OAuth2AccessToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|WYMAGANE. Token dostępu wystawiony przez serwer autoryzacji.|  
|OAuth2ClientId_AuthorizationCodeGrant_AuthorizationRequest|WYMAGANE. Identyfikator klienta.|  
|OAuth2ClientId_AuthorizationCodeGrant_TokenRequest|WYMAGANE, jeśli klient nie uwierzytelnia się za pomocą serwera autoryzacji.|  
|OAuth2ClientId_ImplicitGrant_AuthorizationRequest|WYMAGANE. Identyfikator klienta.|  
|OAuth2Code_AuthorizationCodeGrant_AuthorizationResponse|WYMAGANE. Kod autoryzacji wygenerowany przez serwer autoryzacji.|  
|OAuth2Code_AuthorizationCodeGrant_TokenRequest|WYMAGANE. Kod autoryzacji otrzymany z serwera autoryzacji.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_AuthorizationErrorResponse|OPCJONALNIE. Tekst ASCII czytelny dla człowieka dodatkowymi informacjami.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_TokenErrorResponse|OPCJONALNIE. Tekst ASCII czytelny dla człowieka dodatkowymi informacjami.|  
|OAuth2ErrorDescription_ClientCredentialsGrant_TokenErrorResponse|OPCJONALNIE. Tekst ASCII czytelny dla człowieka dodatkowymi informacjami.|  
|OAuth2ErrorDescription_ImplicitGrant_AuthorizationErrorResponse|OPCJONALNIE. Tekst ASCII czytelny dla człowieka dodatkowymi informacjami.|  
|OAuth2ErrorDescription_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|OPCJONALNIE. Tekst ASCII czytelny dla człowieka dodatkowymi informacjami.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_AuthorizationErrorResponse|OPCJONALNIE. Identyfikator URI identyfikujący czytelny dla człowieka strony sieci web z informacjami o błędzie.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_TokenErrorResponse|OPCJONALNIE. Identyfikator URI identyfikujący czytelny dla człowieka strony sieci web z informacjami o błędzie.|  
|OAuth2ErrorUri_ClientCredentialsGrant_TokenErrorResponse|OPCJONALNIE. Identyfikator URI identyfikujący czytelny dla człowieka strony sieci web z informacjami o błędzie.|  
|OAuth2ErrorUri_ImplicitGrant_AuthorizationErrorResponse|OPCJONALNIE. Identyfikator URI identyfikujący czytelny dla człowieka strony sieci web z informacjami o błędzie.|  
|OAuth2ErrorUri_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|OPCJONALNIE. Identyfikator URI identyfikujący czytelny dla człowieka strony sieci web z informacjami o błędzie.|  
|OAuth2Error_AuthorizationCodeGrant_AuthorizationErrorResponse|WYMAGANE. Pojedynczy kod błędu ASCII spośród następujących: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable.|  
|OAuth2Error_AuthorizationCodeGrant_TokenErrorResponse|WYMAGANE. Pojedynczy kod błędu ASCII spośród następujących: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2Error_ClientCredentialsGrant_TokenErrorResponse|WYMAGANE. Pojedynczy kod błędu ASCII spośród następujących: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2Error_ImplicitGrant_AuthorizationErrorResponse|WYMAGANE. Pojedynczy kod błędu ASCII spośród następujących: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable.|  
|OAuth2Error_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|WYMAGANE. Pojedynczy kod błędu ASCII spośród następujących: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2ExpiresIn_AuthorizationCodeGrant_TokenResponse|ZALECANE. Okres istnienia w ciągu kilku sekund tokenu dostępu.|  
|OAuth2ExpiresIn_ClientCredentialsGrant_TokenResponse|ZALECANE. Okres istnienia w ciągu kilku sekund tokenu dostępu.|  
|OAuth2ExpiresIn_ImplicitGrant_AuthorizationResponse|ZALECANE. Okres istnienia w ciągu kilku sekund tokenu dostępu.|  
|OAuth2ExpiresIn_ResourceOwnerPasswordCredentialsGrant_TokenResponse|ZALECANE. Okres istnienia w ciągu kilku sekund tokenu dostępu.|  
|OAuth2GrantType_AuthorizationCodeGrant_TokenRequest|WYMAGANE. Wartość musi być ustawiona na "authorization_code".|  
|OAuth2GrantType_ClientCredentialsGrant_TokenRequest|WYMAGANE. Wartość musi być ustawiona na "client_credentials".|  
|OAuth2GrantType_ResourceOwnerPasswordCredentialsGrant_TokenRequest|WYMAGANE. Wartość musi być ustawiona na "password".|  
|OAuth2Password_ResourceOwnerPasswordCredentialsGrant_TokenRequest|WYMAGANE. Hasło właściciela zasobu.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_AuthorizationRequest|OPCJONALNIE. Identyfikator URI punktu końcowego przekierowania musi być bezwzględnym identyfikatorem URI.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_TokenRequest|WYMAGANE, jeśli parametr „redirect_uri” został uwzględniony w żądaniu autoryzacji i ich wartości MUSZĄ być identyczne.|  
|OAuth2RedirectUri_ImplicitGrant_AuthorizationRequest|OPCJONALNIE. Identyfikator URI punktu końcowego przekierowania musi być bezwzględnym identyfikatorem URI.|  
|OAuth2RefreshToken_AuthorizationCodeGrant_TokenResponse|OPCJONALNIE. Token odświeżania, który może służyć do uzyskania nowych tokenów dostępu.|  
|OAuth2RefreshToken_ClientCredentialsGrant_TokenResponse|OPCJONALNIE. Token odświeżania, który może służyć do uzyskania nowych tokenów dostępu.|  
|OAuth2RefreshToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|OPCJONALNIE. Token odświeżania, który może służyć do uzyskania nowych tokenów dostępu.|  
|OAuth2ResponseType_AuthorizationCodeGrant_AuthorizationRequest|WYMAGANE. Wartość musi być ustawiona na "code".|  
|OAuth2ResponseType_ImplicitGrant_AuthorizationRequest|WYMAGANE. Wartość musi być ustawiona na "token".|  
|OAuth2Scope_AuthorizationCodeGrant_AuthorizationRequest|OPCJONALNIE. Zakres żądania dostępu.|  
|OAuth2Scope_AuthorizationCodeGrant_TokenResponse|OPCJONALNY, jeśli identyczny jak zakres żądany przez klienta; w przeciwnym razie WYMAGANY.|  
|OAuth2Scope_ClientCredentialsGrant_TokenRequest|OPCJONALNIE. Zakres żądania dostępu.|  
|OAuth2Scope_ClientCredentialsGrant_TokenResponse|OPCJONALNY, jeśli identyczny jak zakres żądany przez klienta; w przeciwnym przypadku WYMAGANY.|  
|OAuth2Scope_ImplicitGrant_AuthorizationRequest|OPCJONALNIE. Zakres żądania dostępu.|  
|OAuth2Scope_ImplicitGrant_AuthorizationResponse|OPCJONALNY, jeśli identyczny jak zakres żądany przez klienta; w przeciwnym razie WYMAGANY.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenRequest|OPCJONALNIE. Zakres żądania dostępu.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenResponse|OPCJONALNY, jeśli identyczny jak zakres żądany przez klienta; w przeciwnym przypadku WYMAGANY.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationErrorResponse|WYMAGANE, jeśli parametr "state" znajdował się w żądaniu autoryzacji klienta.  Dokładna wartość odebrana od klienta.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationRequest|ZALECANE. Nieprzezroczysta wartość używana przez klienta w celu zachowania stanu między żądaniem i wywołania zwrotnego.  Serwer autoryzacji dołącza tę wartość podczas przekierowywania agent użytkownika klienta.  Parametr stosuje się w celu zapobiegania fałszerstwo żądania międzywitrynowego.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationResponse|WYMAGANE, jeśli parametr "state" znajdował się w żądaniu autoryzacji klienta.  Dokładna wartość odebrana od klienta.|  
|OAuth2State_ImplicitGrant_AuthorizationErrorResponse|WYMAGANE, jeśli parametr "state" znajdował się w żądaniu autoryzacji klienta.  Dokładna wartość odebrana od klienta.|  
|OAuth2State_ImplicitGrant_AuthorizationRequest|ZALECANE. Nieprzezroczysta wartość używana przez klienta w celu zachowania stanu między żądaniem i wywołania zwrotnego.  Serwer autoryzacji dołącza tę wartość podczas przekierowywania agent użytkownika klienta.  Parametr stosuje się w celu zapobiegania fałszerstwo żądania międzywitrynowego.|  
|OAuth2State_ImplicitGrant_AuthorizationResponse|WYMAGANE, jeśli parametr "state" znajdował się w żądaniu autoryzacji klienta.  Dokładna wartość odebrana od klienta.|  
|OAuth2TokenType_AuthorizationCodeGrant_TokenResponse|WYMAGANE. Typ wystawionego tokenu.|  
|OAuth2TokenType_ClientCredentialsGrant_TokenResponse|WYMAGANE. Typ wystawionego tokenu.|  
|OAuth2TokenType_ImplicitGrant_AuthorizationResponse|WYMAGANE. Typ wystawionego tokenu.|  
|OAuth2TokenType_ResourceOwnerPasswordCredentialsGrant_TokenResponse|WYMAGANE. Typ wystawionego tokenu.|  
|OAuth2UserName_ResourceOwnerPasswordCredentialsGrant_TokenRequest|WYMAGANE. Nazwa użytkownika właściciela zasobu.|  
|OAuth2UnsupportedTokenType|Typ tokenu "{0}" nie jest obsługiwane.|  
|OAuth2InvalidState|Nieprawidłowa odpowiedź z serwera autoryzacji|  
|OAuth2GrantType_AuthorizationCode|Kod autoryzacji|  
|OAuth2GrantType_Implicit|Niejawny|  
|OAuth2GrantType_ClientCredentials|Poświadczenia klienta|  
|OAuth2GrantType_ResourceOwnerPassword|Hasło właściciela zasobu|  
|WebDocumentation302Code|302 — Znaleziono|  
|WebDocumentation400Code|400 (Złe żądanie)|  
|OAuth2SendingMethod_AuthHeader|Nagłówek uwierzytelnienia|  
|OAuth2SendingMethod_QueryParam|Parametr zapytania|  
|OAuth2AuthorizationServerGeneralException|Wystąpił błąd podczas autoryzacji dostępu za pośrednictwem {0}|  
|OAuth2AuthorizationServerCommunicationException|Nie można nawiązać połączenia HTTP z serwerem autoryzacji lub zostało ono nieoczekiwanie zamknięte.|  
|WebDocumentationOAuth2GeneralErrorMessage|Wystąpił nieoczekiwany błąd.|  
|AuthorizationServerCommunicationException|Wyjątek komunikacji z serwerem autoryzacji się stało. Skontaktuj się z administratorem.|  
|TextblockSubscriptionKeyHeaderDescription|Klucz subskrypcji, która zapewnia dostęp do tego interfejsu API. W Twojej < href = "/ dla deweloperów"\>profilu < /a\>.|  
|TextblockOAuthHeaderDescription|Token dostępu OAuth 2.0 uzyskany z < mogę\>{0}< /i\>. Obsługiwane typów przydziałów: < i\>{1}< /i\>.|  
|TextblockContentTypeHeaderDescription|Typ nośnika treści wysłanej do interfejsu API.|  
|ErrorMessageApiNotAccessible|Interfejs API, który próbujesz wywołać jest w tej chwili niedostępny. Skontaktuj się z wydawcą interfejsu API < href = "/ issues"\>tutaj < /a\>.|  
|ErrorMessageApiTimedout|Interfejs API, który próbujesz wywołać trwa dłużej niż zwykle, aby wrócić odpowiedzi. Skontaktuj się z wydawcą interfejsu API < href = "/ issues"\>tutaj < /a\>.|  
|BadRequestParameterExpected|""{0}"oczekuje parametru"|  
|TooltipTextDoubleClickToSelectAll|Kliknij dwukrotnie, aby wybrać wszystko.|  
|TooltipTextHideRevealSecret|Pokaż/ukryj|  
|ButtonLinkOpenConsole|Wypróbuj|  
|SectionHeadingRequestBody|Treść żądania|  
|SectionHeadingRequestParameters|Parametry żądania|  
|SectionHeadingRequestUrl|Adres URL żądania|  
|SectionHeadingResponse|Odpowiedź|  
|SectionHeadingRequestHeaders|Nagłówki żądań|  
|FormLabelSubtextOptional|opcjonalne|  
|SectionHeadingCodeSamples|Przykłady kodu|  
|TextblockOpenidConnectHeaderDescription|Token identyfikatora uwierzytelniania OpenID Connect uzyskany z < mogę\>{0}< /i\>. Obsługiwane typów przydziałów: < i\>{1}< /i\>.|  
  
###  <a name="ErrorPageStrings"></a> ErrorPageStrings  
  
|Name (Nazwa)|Tekst|  
|----------|----------|  
|LinkLabelBack|wstecz|  
|LinkLabelHomePage|stronę główną|  
|LinkLabelSendUsEmail|Wyślij nam wiadomość e-mail|  
|PageTitleError|Niestety, wystąpił problem z wyświetleniem żądanej strony|  
|TextblockPotentialCauseIntermittentIssue|Może to być problem z dostępem do danych tymczasowy, który został już usunięty.|  
|TextblockPotentialCauseOldLink|Kliknięty link może być nieaktualny i nie wskazywać już na poprawną lokalizację.|  
|TextblockPotentialCauseTechnicalProblem|Po naszej stronie mogą być problemy techniczne.|  
|TextblockPotentialSolutionRefresh|Spróbuj odświeżyć stronę.|  
|TextblockPotentialSolutionStartOver|Rozpocznij ponownie od naszego {0}.|  
|TextblockPotentialSolutionTryAgain|Przejdź {0} i spróbuj wykonać akcję ponownie wykonać.|  
|TextReportProblem|{0} opisujący, co poszło źle i omówimy je tak szybko jak to możliwe.|  
|TitlePotentialCause|Prawdopodobna przyczyna|  
|TitlePotentialSolution|Prawdopodobnie jest to tylko tymczasowy problem i wystarczy wykonać kilka czynności|  
  
###  <a name="IssuesStrings"></a> IssuesStrings  
  
|Name (Nazwa)|Tekst|  
|----------|----------|  
|WebIssuesIndexTitle|Problemy|  
|WebIssuesNoActiveSubscriptions|Masz aktywne subskrypcje. Należy zasubskrybować produkt, aby zgłosić problem.|  
|WebIssuesNotSignin|Nie zalogowano. Proszę {0} Aby zgłosić problem lub opublikować komentarz.|  
|WebIssuesReportIssueButton|Zgłoś problem|  
|WebIssuesSignIn|logowanie|  
|WebIssuesStatusReportedBy|Stan: {0} &#124; zgłoszone przez {1}|  
  
###  <a name="NotFoundStrings"></a> NotFoundStrings  
  
|Name (Nazwa)|Tekst|  
|----------|----------|  
|LinkLabelHomePage|stronę główną|  
|LinkLabelSendUsEmail|wyślij do nas wiadomość e-mail|  
|PageTitleNotFound|Niestety nie można odnaleźć strony, której szukasz|  
|TextblockPotentialCauseMisspelledUrl|Błędnie adres URL Jeśli został wpisany.|  
|TextblockPotentialCauseOldLink|Kliknięty link może być nieaktualny i nie wskazywać już na poprawną lokalizację.|  
|TextblockPotentialSolutionRetype|Spróbuj ponownie wpisać adres URL.|  
|TextblockPotentialSolutionStartOver|Rozpocznij ponownie od naszego {0}.|  
|TextReportProblem|{0} opisujący, co poszło źle i omówimy je tak szybko jak to możliwe.|  
|TitlePotentialCause|Prawdopodobna przyczyna|  
|TitlePotentialSolution|Potencjalne rozwiązanie|  
  
###  <a name="ProductDetailsStrings"></a> ProductDetailsStrings  
  
|Name (Nazwa)|Tekst|  
|----------|----------|  
|WebProductsAgreement|Jeśli zasubskrybujesz {0} produktu, zgadzam się na `<a data-toggle='modal' href='#legal-terms'\>Terms of Use</a\>`.|  
|WebProductsLegalTermsLink|Warunki użytkowania|  
|WebProductsSubscribeButton|Subskrybuj|  
|WebProductsUsageLimitsHeader|Limity użycia|  
|WebProductsYouAreNotSubscribed|Subskrybujesz ten produkt.|  
|WebProductsYouRequestedSubscription|Żądasz zasubskrybowania tego produktu.|  
|ErrorYouNeedToAgreeWithLegalTerms|Musisz zgodzić się na Warunki użytkowania, zanim będzie można kontynuować.|  
|ButtonLabelAddSubscription|Dodawanie subskrypcji|  
|LinkLabelChangeSubscriptionName|zmień|  
|ButtonLabelConfirm|Potwierdź|  
|TextblockMultipleSubscriptionsCount|Masz {0} subskrypcji do tego produktu:|  
|TextblockSingleSubscriptionsCount|Masz {0} subskrypcję tego produktu:|  
|TextblockSingleApisCount|Ten produkt zawiera {0} interfejsu API:|  
|TextblockMultipleApisCount|Ten produkt zawiera {0} interfejsów API:|  
|TextblockHeaderSubscribe|Zasubskrybuj produkt|  
|TextblockSubscriptionDescription|Nowa subskrypcja zostanie utworzona w następujący sposób:|  
|TextblockSubscriptionLimitReached|Osiągnięto limit subskrypcji.|  
  
###  <a name="ProductsStrings"></a> ProductsStrings  
  
|Name (Nazwa)|Tekst|  
|----------|----------|  
|PageTitleProducts|Produkty|  
  
###  <a name="ProviderInfoStrings"></a> ProviderInfoStrings  
  
|Name (Nazwa)|Tekst|  
|----------|----------|  
|TextboxExternalIdentitiesDisabled|Logowanie się jest w tej chwili wyłączone przez administratorów.|  
|TextboxExternalIdentitiesSigninInvitation|Możesz też zalogować się przy użyciu|  
|TextboxExternalIdentitiesSigninInvitationPrimary|Zaloguj się przy użyciu|  
  
###  <a name="SigninResources"></a> SigninResources  
  
|Name (Nazwa)|Tekst|  
|----------|----------|  
|PrincipalNotFound|Nie można odnaleźć podmiotu zabezpieczeń lub podpis jest nieprawidłowy|  
|ErrorSsoAuthenticationFailed|Uwierzytelnianie logowania jednokrotnego nie powiodło się|  
|ErrorSsoAuthenticationFailedDetailed|Podano nieprawidłowy token lub nie można zweryfikować podpisu.|  
|ErrorSsoTokenInvalid|Token logowania jednokrotnego jest nieprawidłowy|  
|ValidationErrorSpecificEmailAlreadyExists|Wiadomość e-mail "{0}" już zarejestrowany|  
|ValidationErrorSpecificEmailInvalid|Wiadomość e-mail "{0}" jest nieprawidłowy|  
|ValidationErrorPasswordInvalid|Hasło jest nieprawidłowe. Popraw błędy i spróbuj ponownie.|  
|PropertyTooShort|{0} jest zbyt krótki|  
|WebAuthenticationAddresserEmailInvalidErrorMessage|Nieprawidłowy adres e-mail.|  
|ValidationMessageNewPasswordConfirmationRequired|Potwierdź nowe hasło|  
|ValidationErrorPasswordConfirmationRequired|Potwierdzenie hasła jest puste|  
|WebAuthenticationEmailChangeNotice|Wiadomość e-mail z potwierdzeniem zmiany została wysłana do {0}. Wykonaj poniższe instrukcje znajdujące się w nim, aby potwierdzić nowy adres e-mail. Jeśli wiadomość e-mail nie dotrze do Twojej skrzynki odbiorczej w ciągu najbliższych kilku minut, sprawdź folder wiadomości-śmieci.|  
|WebAuthenticationEmailChangeNoticeHeader|Żądanie zmiany adresu e-mail zostało pomyślnie przetworzone|  
|WebAuthenticationEmailChangeNoticeTitle|Zażądano zmiany adresu e-mail|  
|WebAuthenticationEmailHasBeenRevertedNotice|Twój adres e-mail już istnieje. Żądanie zostało wycofane|  
|ValidationErrorEmailAlreadyExists|Adres e-mail już istnieje|  
|ValidationErrorEmailInvalid|Nieprawidłowy adres e-mail|  
|TextboxLabelEmail|Email|  
|ValidationErrorEmailRequired|Adres e-mail jest wymagany.|  
|WebAuthenticationErrorNoticeHeader|Błąd|  
|WebAuthenticationFieldLengthErrorMessage|{0} musi mieć maksymalną długość {1}|  
|TextboxLabelEmailFirstName|Imię|  
|ValidationErrorFirstNameRequired|Imię jest wymagane.|  
|ValidationErrorFirstNameInvalid|Nieprawidłowe imię|  
|NoticeInvalidInvitationToken|Należy pamiętać, że linki do potwierdzenia są ważne tylko przez 48 godzin. Jeśli jesteś nadal w tym przedziale czasowym, upewnij się, że link jest poprawny. Jeśli link wygasł, powtórz akcję, którą próbujesz potwierdzić.|  
|NoticeHeaderInvalidInvitationToken|Nieprawidłowy token zaproszenia|  
|NoticeTitleInvalidInvitationToken|Błąd potwierdzenia|  
|WebAuthenticationLastNameInvalidErrorMessage|Nieprawidłowe nazwisko|  
|TextboxLabelEmailLastName|Nazwisko|  
|ValidationErrorLastNameRequired|Nazwisko jest wymagane.|  
|WebAuthenticationLinkExpiredNotice|Link do potwierdzenia wysłana do Ciebie utracił ważność. `<a href={0}?token={1}>Resend confirmation email.</a\>`|  
|NoticePasswordResetLinkInvalidOrExpired|Link do zresetowania hasła jest nieprawidłowy lub wygasł.|  
|WebAuthenticationLinkExpiredNoticeTitle|Link został wysłany|  
|WebAuthenticationNewPasswordLabel|Nowe hasło|  
|ValidationMessageNewPasswordRequired|Nowe hasło jest wymagane.|  
|TextboxLabelNotificationsSenderEmail|Adres e-mail nadawcy dla powiadomień|  
|TextboxLabelOrganizationName|Nazwa organizacji|  
|WebAuthenticationOrganizationRequiredErrorMessage|Nazwa organizacji jest pusta|  
|WebAuthenticationPasswordChangedNotice|Twoje hasło zostało pomyślnie zaktualizowane|  
|WebAuthenticationPasswordChangedNoticeTitle|Hasło zostało zaktualizowane|  
|WebAuthenticationPasswordCompareErrorMessage|Hasła są niezgodne|  
|WebAuthenticationPasswordConfirmLabel|Potwierdź hasło|  
|ValidationErrorPasswordInvalidDetailed|Hasło jest za słabe.|  
|WebAuthenticationPasswordLabel|Hasło|  
|ValidationErrorPasswordRequired|Hasło jest wymagane.|  
|WebAuthenticationPasswordResetSendNotice|Zmiana wiadomość e-mail z potwierdzeniem hasła znajduje się w sposób {0}. Postępuj zgodnie z instrukcjami w wiadomości e-mail, aby kontynuować proces zmiany hasła.|  
|WebAuthenticationPasswordResetSendNoticeHeader|Żądanie zresetowania hasła zostało pomyślnie przetworzone|  
|WebAuthenticationPasswordResetSendNoticeTitle|Zażądano zresetowania hasła|  
|WebAuthenticationRequestNotFoundNotice|Nie odnaleziono żądania|  
|WebAuthenticationSenderEmailRequiredErrorMessage|Adres e-mail nadawcy powiadomień jest pusty|  
|WebAuthenticationSigninPasswordLabel|Potwierdź zmianę, wprowadzając hasło|  
|WebAuthenticationSignupConfirmNotice|Wiadomość e-mail z potwierdzeniem rejestracji została wysłana do {0}. < br /\> , postępuj zgodnie z instrukcjami w wiadomości e-mail, aby aktywować swoje konto. < br /\> Jeśli wiadomość e-mail nie dotrze do Twojej skrzynki odbiorczej w ciągu najbliższych kilku minut, Sprawdź folder wiadomości-śmieci.|  
|WebAuthenticationSignupConfirmNoticeHeader|Twoje konto zostało pomyślnie utworzone|  
|WebAuthenticationSignupConfirmNoticeRepeatHeader|Wiadomość e-mail z potwierdzeniem rejestracji została ponownie wysłana|  
|WebAuthenticationSignupConfirmNoticeTitle|Utworzono konto|  
|WebAuthenticationTokenRequiredErrorMessage|Token jest pusty|  
|WebAuthenticationUserAlreadyRegisteredNotice|Wydaje się, że użytkownik mający ten adres e-mail jest już zarejestrowany w systemie. Jeśli nie pamiętasz hasła, spróbuj je przywrócić lub skontaktuj się z naszym zespołem pomocy technicznej.|  
|WebAuthenticationUserAlreadyRegisteredNoticeHeader|Użytkownik jest już zarejestrowany|  
|WebAuthenticationUserAlreadyRegisteredNoticeTitle|Już zarejestrowany|  
|ButtonLabelChangePassword|Zmień hasło|  
|ButtonLabelChangeAccountInfo|Zmień informacje konta|  
|ButtonLabelCloseAccount|Zamknij konto|  
|WebAuthenticationInvalidCaptchaErrorMessage|Wprowadzony tekst nie odpowiada tekstowi na obrazie. Spróbuj ponownie.|  
|ValidationErrorCredentialsInvalid|Adres e-mail lub hasło jest nieprawidłowe. Popraw błędy i spróbuj ponownie.|  
|WebAuthenticationRequestIsNotValid|Żądanie jest nieprawidłowe|  
|WebAuthenticationUserIsNotConfirm|Potwierdź rejestrację, zanim spróbujesz się zalogować.|  
|WebAuthenticationInvalidEmailFormated|Adres e-mail jest nieprawidłowy: {0}|  
|WebAuthenticationUserNotFound|Nie znaleziono użytkownika|  
|WebAuthenticationTenantNotRegistered|Twoje konto należy do dzierżawy usługi Azure Active Directory, która nie ma autoryzacji do dostępu do tego portalu.|  
|WebAuthenticationAuthenticationFailed|Uwierzytelnianie nie powiodło się.|  
|WebAuthenticationGooglePlusNotEnabled|Uwierzytelnianie nie powiodło się. Jeśli uprawnienia aplikacji następnie skontaktuj się z administratorem, aby upewnić się, że uwierzytelnianie serwisu Google jest poprawnie skonfigurowany.|  
|ValidationErrorAllowedTenantIsRequired|Dozwolona dzierżawa jest wymagana|  
|ValidationErrorTenantIsNotValid|Dzierżawy usługi Azure Active Directory "{0}" jest nieprawidłowa.|  
|WebAuthenticationActiveDirectoryTitle|Usługa Azure Active Directory|  
|WebAuthenticationLoginUsingYourProvider|Zaloguj się przy użyciu usługi {0} konta|  
|WebAuthenticationUserLimitNotice|Ta usługa osiągnęła maksymalną liczbę dozwolonych użytkowników. Proszę `<a href="mailto:{0}"\>contact the administrator</a\>` uaktualnić ich usługę i ponownie włączyć rejestrację użytkowników.|  
|WebAuthenticationUserLimitNoticeHeader|Rejestracja użytkowników jest wyłączona|  
|WebAuthenticationUserLimitNoticeTitle|Rejestracja użytkowników jest wyłączona|  
|WebAuthenticationUserRegistrationDisabledNotice|Rejestracja użytkowników została wyłączona przez administratora. Zaloguj się przy użyciu zewnętrznego dostawcy tożsamości.|  
|WebAuthenticationUserRegistrationDisabledNoticeHeader|Rejestracja użytkowników jest wyłączona|  
|WebAuthenticationUserRegistrationDisabledNoticeTitle|Rejestracja użytkowników jest wyłączona|  
|WebAuthenticationSignupPendingConfirmationNotice|Zanim będziemy mogli zakończyć tworzenie Twojego konta, musimy zweryfikować Twój adres e-mail. Wysłaliśmy wiadomość e-mail na adres {0}. Postępuj zgodnie z instrukcjami wewnątrz tej wiadomości, aby aktywować konto. Jeśli wiadomość e-mail nie dotrze w ciągu najbliższych kilku minut, sprawdź folder wiadomości-śmieci.|  
|WebAuthenticationSignupPendingConfirmationAccountFoundNotice|Znaleźliśmy niepotwierdzone konto dla adresu e-mail {0}. Aby zakończyć tworzenie Twojego konta, musimy zweryfikować Twój adres e-mail. Wysłaliśmy wiadomość e-mail na adres {0}. Postępuj zgodnie z instrukcjami wewnątrz tej wiadomości, aby aktywować konto. Jeśli wiadomość e-mail nie dotrze w ciągu najbliższych kilku minut, sprawdź folder wiadomości-śmieci|  
|WebAuthenticationSignupConfirmationAlmostDone|Prawie gotowe|  
|WebAuthenticationSignupConfirmationEmailSent|Wysłaliśmy wiadomość e-mail na adres {0}. Postępuj zgodnie z instrukcjami wewnątrz tej wiadomości, aby aktywować konto. Jeśli wiadomość e-mail nie dotrze w ciągu najbliższych kilku minut, sprawdź folder wiadomości-śmieci.|  
|WebAuthenticationEmailSentNotificationMessage|Wiadomość e-mail została wysłana do {0}|  
|WebAuthenticationNoAadTenantConfigured|Brak skonfigurowanej dzierżawy usługi Azure Active Directory dla usługi.|  
|CheckboxLabelUserRegistrationTermsConsentRequired|Zgadzam się na `<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use</a\>`.|  
|TextblockUserRegistrationTermsProvided|Przejrzyj `<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use.</a\>`|  
|DialogHeadingTermsOfUse|Warunki użytkowania|  
|ValidationMessageConsentNotAccepted|Musisz zgodzić się na Warunki użytkowania, zanim będzie można kontynuować.|  
  
###  <a name="SigninStrings"></a> SigninStrings  
  
|Name (Nazwa)|Tekst|  
|----------|----------|  
|WebAuthenticationForgotPassword|Nie pamiętasz hasła?|  
|WebAuthenticationIfAdministrator|Jeśli jesteś administratorem, musisz zalogować się `<a href="{0}"\>here</a\>`.|  
|WebAuthenticationNotAMember|Nie jesteś jeszcze członkiem? `<a href="/signup"\>Sign up now</a\>`|  
|WebAuthenticationRemember|Pamiętaj mnie na tym komputerze|  
|WebAuthenticationSigininWithPassword|Zaloguj się za pomocą nazwy użytkownika i hasła|  
|WebAuthenticationSigninTitle|Logowanie|  
|WebAuthenticationSignUpNow|Utwórz konto teraz|  
  
###  <a name="SignupStrings"></a> SignupStrings  
  
|Name (Nazwa)|Tekst|  
|----------|----------|  
|PageTitleSignup|Rejestrowanie|  
|WebAuthenticationAlreadyAMember|Jesteś już członkiem?|  
|WebAuthenticationCreateNewAccount|Utwórz nowe konto usługi API Management|  
|WebAuthenticationSigninNow|Logowanie|  
|ButtonLabelSignup|Rejestrowanie|  
  
###  <a name="SubscriptionListStrings"></a> SubscriptionListStrings  
  
|Name (Nazwa)|Tekst|  
|----------|----------|  
|SubscriptionCancelConfirmation|Czy na pewno chcesz anulować tę subskrypcję?|  
|SubscriptionRenewConfirmation|Czy na pewno chcesz odnowić tę subskrypcję?|  
|WebDevelopersManageSubscriptions|Zarządzanie subskrypcjami|  
|WebDevelopersPrimaryKey|Klucz podstawowy|  
|WebDevelopersRegenerateLink|Regenerate|  
|WebDevelopersSecondaryKey|Klucz pomocniczy|  
|ButtonLabelShowKey|Show|  
|ButtonLabelRenewSubscription|Renew|  
|WebDevelopersSubscriptionRequested|Żądana {0}|  
|WebDevelopersSubscriptionRequestedState|Żądane|  
|WebDevelopersSubscriptionTableNameHeader|Name (Nazwa)|  
|WebDevelopersSubscriptionTableStateHeader|Stan|  
|WebDevelopersUsageStatisticsLink|Raporty analizy|  
|WebDevelopersYourSubscriptions|Twoje subskrypcje|  
|SubscriptionPropertyLabelRequestedDate|Żądanie zgłoszone w dniu|  
|SubscriptionPropertyLabelStartedDate|Rozpoczęto|  
|PageTitleRenameSubscription|Zmień nazwę subskrypcji|  
|SubscriptionPropertyLabelName|Nazwa subskrypcji|  
  
###  <a name="SubscriptionStrings"></a> SubscriptionStrings  
  
|Name (Nazwa)|Tekst|  
|----------|----------|  
|SectionHeadingCloseAccount|Szukasz możliwości zamknięcia konta?|  
|PageTitleDeveloperProfile|Profil|  
|ButtonLabelHideKey|Ukryj|  
|ButtonLabelRegenerateKey|Regenerate|  
|InformationMessageKeyWasRegenerated|Czy na pewno chcesz ponownie wygenerować ten klucz?|  
|ButtonLabelShowKey|Show|  
  
###  <a name="UpdateProfileStrings"></a> UpdateProfileStrings  
  
|Name (Nazwa)|Tekst|  
|----------|----------|  
|ButtonLabelUpdateProfile|Aktualizowanie profilu|  
|PageTitleUpdateProfile|Aktualizowanie informacji o koncie|  
  
###  <a name="UserProfile"></a> UserProfile  
  
|Name (Nazwa)|Tekst|  
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
|SubscriptionStateCancelled|Anulowana|  
|SubscriptionStateExpired|Wygaśnięcie|  
|SubscriptionStateRejected|Odrzucone|  
|SubscriptionStateRequested|Żądane|  
|SubscriptionStateSuspended|Wstrzymane|  
|DefaultSubscriptionNameTemplate|{0}  (ustawienie domyślne)|  
|SubscriptionNameTemplate|Deweloper dostępu #{0}|  
|TextboxLabelSubscriptionName|Nazwa subskrypcji|  
|ValidationMessageSubscriptionNameRequired|Nazwa subskrypcji nie może być pusta.|  
|ApiManagementUserLimitReached|Ta usługa osiągnęła maksymalną liczbę dozwolonych użytkowników. Przeprowadź uaktualnienie do wyższej warstwy cenowej.|  
  
##  <a name="glyphs"></a> Zasoby glifów  
 Szablonów portalu dla deweloperów usługi API Management służy symbole z [Glyphicons z Bootstrap](https://getbootstrap.com/components/#glyphicons). Ten zestaw symbole obejmuje ponad 250 symbole w formacie czcionki na podstawie [Glyphicon](https://glyphicons.com/) Halflings zestawu. Aby użyć symbolu z tego zestawu, należy użyć następującej składni.  
  
```html  
<span class="glyphicon glyphicon-user">  
```  
  
 Aby uzyskać pełną listę symbole, zobacz [Glyphicons z Bootstrap](https://getbootstrap.com/components/#glyphicons).

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [Dostosowywanie portalu dla deweloperów usługi API Management przy użyciu szablonów](api-management-developer-portal-templates.md).
