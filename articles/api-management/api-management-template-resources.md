---
title: Zasobów szablonu usługi Azure API Management | Dokumentacja firmy Microsoft
description: Więcej informacji na temat typów zasobów, które są dostępne do użycia w szablonach portalu deweloperów w usłudze Azure API Management.
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
ms.openlocfilehash: 212e7ea7bb2ffea63c7ba210195df0da38aa8f0a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23835398"
---
# <a name="azure-api-management-template-resources"></a>Zasobów szablonu usługi Azure API Management
Zarządzanie interfejsami API Azure zapewnia następujące typy zasobów do użycia w dewelopera szablony portalu.  
  
-   [Zasoby ciągów](#strings)  
  
-   [Zasoby symbolu](#glyphs)  
  
##  <a name="strings"></a>Zasoby ciągów  
 Zarządzanie interfejsami API zapewnia rozbudowany zestaw zasobów ciągu do użycia w portalu dla deweloperów. Te zasoby są zlokalizowane w wszystkie języki obsługiwane przez interfejs API Management. Domyślny zestaw szablonów używa tych zasobów, nagłówki stron, etykiety i wszelkie stałe ciągów, które są wyświetlane w portalu dla deweloperów. Aby użyć zasobu ciągu w szablonach, podaj prefiks ciągu zasobu następuje nazwa ciągu, jak pokazano w poniższym przykładzie.  
  
```  
{% localized "Prefix|Name" %}  
  
```  
  
 Poniższy przykład pochodzi z szablonu listy produktów i wyświetla **produkty** w górnej części strony.  
  
```  
<h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>  
  
```  
  
 Zapoznaj się z następujących tabel dla zasobów ciągu dostępne do użycia w szablonach portalu deweloperów. Użyj nazwy tabeli jako prefiks dla zasobów ciągu w tej tabeli.  
  
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
  
|Nazwa|Tekst|  
|----------|----------|  
|PageTitleApis|Interfejsy API|  
  
###  <a name="AppDetailsStrings"></a>AppDetailsStrings  
  
|Nazwa|Tekst|  
|----------|----------|  
|WebApplicationsDetailsTitle|Podgląd aplikacji|  
|WebApplicationsRequirementsHeader|Wymagania|  
|WebApplicationsScreenshotAlt|Zrzut ekranu|  
|WebApplicationsScreenshotsHeader|Zrzuty ekranu|  
  
###  <a name="ApplicationListStrings"></a>ApplicationListStrings  
  
|Nazwa|Tekst|  
|----------|----------|  
|WebDevelopersAppDeleteConfirmation|Czy na pewno chcesz usunąć aplikację?|  
|WebDevelopersAppNotPublished|Nie opublikowano|  
|WebDevelopersAppNotSubminted|Nie przesłano|  
|WebDevelopersAppTableCategoryHeader|Kategoria|  
|WebDevelopersAppTableNameHeader|Nazwa|  
|WebDevelopersAppTableStateHeader|Stan|  
|WebDevelopersEditLink|Edytuj|  
|WebDevelopersRegisterAppLink|Zarejestrować aplikację|  
|WebDevelopersRemoveLink|Remove|  
|WebDevelopersSubmitLink|Submit|  
|WebDevelopersYourApplicationsHeader|Aplikacje|  
  
###  <a name="AppStrings"></a>AppStrings  
  
|Nazwa|Tekst|  
|----------|----------|  
|WebApplicationsHeader|Aplikacje|  
  
###  <a name="CommonResources"></a>CommonResources  
  
|Nazwa|Tekst|  
|----------|----------|  
|NoItemsToDisplay|Brak wyników.|  
|GeneralExceptionMessage|Coś są nieprawidłowe. Może to być tymczasowy błąd lub usterki. Spróbuj ponownie.|  
|GeneralJsonExceptionMessage|Coś są nieprawidłowe. Może to być tymczasowy błąd lub usterki. Wykonaj ponownie załaduj stronę i spróbuj ponownie.|  
|ConfirmationMessageUnsavedChanges|Istnieją pewne niezapisane zmiany. Czy na pewno chcesz anulować i odrzucić zmiany?|  
|Usługi AzureActiveDirectory|Usługa Azure Active Directory|  
|HttpLargeRequestMessage|Http treść żądania zbyt duży.|  
  
###  <a name="CommonStrings"></a>CommonStrings  
  
|Nazwa|Tekst|  
|----------|----------|  
|ButtonLabelCancel|Cancel|  
|ButtonLabelSave|Zapisz|  
|GeneralExceptionMessage|Coś są nieprawidłowe. Może to być tymczasowy błąd lub usterki. Spróbuj ponownie.|  
|NoItemsToDisplay|Brak elementów do wyświetlenia.|  
|PagerButtonLabelFirst|Pierwsze|  
|PagerButtonLabelLast|ostatni|  
|PagerButtonLabelNext|Następne kroki|  
|PagerButtonLabelPrevious|Poprzedni|  
|PagerLabelPageNOfM|Strony {0} {1}|  
|PasswordTooShort|Hasło jest za krótkie|  
|EmailAsPassword|Nie należy używać poczty e-mail jako hasło|  
|PasswordSameAsUserName|Hasło nie może zawierać nazwy użytkownika|  
|PasswordTwoCharacterClasses|Użyj innego znaku klas|  
|PasswordTooManyRepetitions|Zbyt wiele powtórzeń|  
|PasswordSequenceFound|Hasło zawiera sekwencji|  
|PagerLabelPageSize|Rozmiar strony|  
|CurtainLabelLoading|Trwa ładowanie...|  
|TablePlaceholderNothingToDisplay|Nie ma żadnych danych dla wybranego okresu i zakres|  
|ButtonLabelClose|Zamknij|  
  
###  <a name="Documentation"></a>Dokumentacja  
  
|Nazwa|Tekst|  
|----------|----------|  
|WebDocumentationInvalidHeaderErrorMessage|Nieprawidłowy nagłówek "{0}"|  
|WebDocumentationInvalidRequestErrorMessage|Nieprawidłowe żądanie adresu URL|  
|TextboxLabelAccessToken|Token dostępu *|  
|DropdownOptionPrimaryKeyFormat|Podstawowy — {0}|  
|DropdownOptionSecondaryKeyFormat|Pomocniczy-{0}|  
|WebDocumentationSubscriptionKeyText|Klucz subskrypcji|  
|WebDocumentationTemplatesAddHeaders|Dodaj wymagane nagłówki HTTP|  
|WebDocumentationTemplatesBasicAuthSample|Przykładowe podstawowe autoryzacji|  
|WebDocumentationTemplatesCurlForBasicAuth|do użytku podstawowe autoryzacji:--użytkownika {username}: {hasło}|  
|WebDocumentationTemplatesCurlValuesForPath|Określ wartości dla parametrów ścieżki (wyświetlane jako {...}), subskrypcji klucza i wartości parametrów zapytania|  
|WebDocumentationTemplatesDeveloperKey|Określ klucz subskrypcji|  
|WebDocumentationTemplatesJavaApache|W przykładzie użyto klienta Apache HTTP ze składników HTTP (http://hc.apache.org/httpcomponents-client-ga/)|  
|WebDocumentationTemplatesOptionalParams|Określ wartości dla parametrów, zgodnie z potrzebami|  
|WebDocumentationTemplatesPhpPackage|W tym przykładzie korzysta z pakietu HTTP_Request2. (Aby uzyskać więcej informacji: http://pear.php.net/package/HTTP_Request2)|  
|WebDocumentationTemplatesPythonValuesForPath|Określ wartości dla parametrów ścieżki (wyświetlane jako {...}) i treści żądania, w razie potrzeby|  
|WebDocumentationTemplatesRequestBody|Określ treść żądania|  
|WebDocumentationTemplatesRequiredParams|Określ wartości następujących wymaganych parametrów|  
|WebDocumentationTemplatesValuesForPath|Określ wartości dla parametrów ścieżki (wyświetlane jako {...})|  
|OAuth2AuthorizationEndpointDescription|Punkt końcowy autoryzacji służy do interakcji z właściciela zasobów i uzyskać udzielania autoryzacji.|  
|OAuth2AuthorizationEndpointName|Punkt końcowy autoryzacji|  
|OAuth2TokenEndpointDescription|Punktu końcowego tokenu jest używany przez klienta uzyskać token dostępu z uwzględnieniem jego udzielania autoryzacji lub odświeżać token.|  
|OAuth2TokenEndpointName|Token punktu końcowego|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Description|< p\> klienta inicjuje przepływ kierując agenta użytkownika właściciel zasobu do punktu końcowego autoryzacji.  Klient dołącza identyfikatora klienta, żądany zakres stanu lokalnego i identyfikatora URI przekierowania, do której serwer autoryzacji wyśle agenta użytkownika Wstecz, gdy dostęp jest udzielono (lub odmówiono).     < /p\> < p\> serwera autoryzacji uwierzytelnia właściciela zasobów (za pomocą agenta użytkownika) i ustala, czy właściciel zasobu lub go przydziela nie zezwala na żądanie dostępu klienta.     < /p\> < p\> zakładając, że właściciel zasobu nieograniczony dostęp, serwer autoryzacji przekierowuje użytkownika agenta do klienta przy użyciu dostarczonego identyfikatora URI przekierowania wcześniejszych (w żądaniu lub podczas registrati klienta na).  Identyfikator URI przekierowania zawiera kod autoryzacji i każdy stan lokalnego wydanego wcześniej przez klienta.     < /p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ErrorDescription|< p\> , gdy użytkownik odmówi żądania dostępu, jeśli żądanie jest nieprawidłowe, klient zostanie poinformowany, korzystając z następujących parametrów dodaje przekierowania: < /p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Name|Żądania autoryzacji|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_RequestDescription|< p\> aplikację klienta należy wysłać do użytkownika do punktu końcowego autoryzacji w celu rozpoczęcia procesu OAuth.          W punkcie końcowym autoryzacji użytkownik uwierzytelnia i następnie przyznaje lub nie zezwala na dostęp do aplikacji.     < /p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ResponseDescription|< p\> zakładając, że właściciel zasobu nieograniczony dostęp, serwer autoryzacji przekierowuje użytkownika agenta do klienta przy użyciu dostarczonego identyfikatora URI przekierowania wcześniejszych (w żądaniu lub podczas rejestracji klienta).  Identyfikator URI przekierowania zawiera kod autoryzacji i każdy stan lokalnego wydanego wcześniej przez klienta. < /p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_Description|< p\> klient żąda token dostępu z serwera autoryzacji "punktu końcowego tokena s, umieszczając w niej kod autoryzacji, odebrany w poprzednim kroku.  W żądaniu skierowanym, klient jest uwierzytelniany w usłudze serwera autoryzacji.  Klient dołącza przekierowania, który identyfikator URI używany do uzyskania kodu autoryzacji do weryfikacji. < /p\> < p\> serwera autoryzacji uwierzytelnia klientów, sprawdza poprawność kodu autoryzacji i zapewni spójność przekierowania URI Odebrano identyfikator URI używany do przekierowywania klientów w kroku (C).  Jeśli jest prawidłowa, serwera autoryzacji odpowiada z powrotem token dostępu i, opcjonalnie, token odświeżania. < /p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ErrorDescription|< p\> żądania uwierzytelniania klienta nie powiodło się lub jest nieprawidłowy, serwera autoryzacji odpowiada z kodem stanu HTTP 400 (nieprawidłowe żądanie) (chyba że określono inaczej) i zawiera następujące parametry z odpowiedzią. < /p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_RequestDescription|< p\> klient wysyła żądanie do punktu końcowego tokenu, wysyłając treści jednostki żądania następujących parametrów przy użyciu formatu "application/x--www-form-urlencoded" z kodowania znaków UTF-8 w HTTP. < /p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ResponseDescription|< p\> serwera autoryzacji wystawia token dostępu i token odświeżania opcjonalne i tworzy odpowiedzi, dodając następujące parametry do treści jednostki odpowiedzi HTTP o kodzie 200 stanu (OK). < /p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_Description|< p\> klient jest uwierzytelniany w usłudze serwera autoryzacji i żądania tokenu dostępu z punktu końcowego tokena. < /p\> < p\> serwera autoryzacji uwierzytelnia klientów, a jeśli jest prawidłowa, generuje token dostępu. < /p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ErrorDescription|< p\> Jeśli żądanie nie powiodło się uwierzytelnienie klienta lub jest nieprawidłowa serwera autoryzacji odpowiada z kodem stanu HTTP 400 (nieprawidłowe żądanie) (chyba że określono inaczej) i zawiera następujące parametry z odpowiedzią. < /p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_RequestDescription|< p\> klient wysyła żądanie do punktu końcowego tokenu, dodając następujące parametry w formacie "application/x--www-form-urlencoded" z kodowania znaków UTF-8 w HTTP żądania treści jednostki. < /p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ResponseDescription|< p\> w przypadku żądania tokenu dostępu jest prawidłowa i autoryzowanych, serwer autoryzacji wystawia token dostępu i token odświeżania opcjonalne i tworzy odpowiedzi, dodając następujące parametry do treści odpowiedzi HTTP z 200 jednostki  Kod stanu (OK). < /p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_Description|< p\> klienta inicjuje przepływ kierując właściciel zasobu "s agenta użytkownika do punktu końcowego autoryzacji.  Klient dołącza identyfikatora klienta, żądany zakres stanu lokalnego i identyfikatora URI przekierowania, do której serwer autoryzacji wyśle agenta użytkownika Wstecz, gdy dostęp jest udzielono (lub odmówiono). < /p\> < p\> serwera autoryzacji uwierzytelnia właściciela zasobów (za pomocą agenta użytkownika) i ustala, czy właściciel zasobu lub go przydziela nie zezwala na kliencie "s żądanie dostępu. < /p\> < p\> zakładając, że właściciel zasobu nieograniczony dostęp, serwer autoryzacji przekierowuje użytkownika agenta do klienta przy użyciu przekierowania URI wydanego wcześniej.  Identyfikator URI przekierowania zawiera token dostępu w fragmentu identyfikatora URI. < /p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ErrorDescription|< p\> Jeśli właściciel zasobu nie zezwala na żądanie dostępu lub w przypadku niepowodzenia żądania z powodów innych niż brakujący lub nieprawidłowy identyfikator URI przekierowania serwera autoryzacji informuje klienta, dodając następujące parametry do fragmentu o składnika f przekierowania URI przy użyciu formatu "application/x--www-form-urlencoded". < /p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_RequestDescription|< p\> aplikację klienta należy wysłać do użytkownika do punktu końcowego autoryzacji w celu rozpoczęcia procesu OAuth.      W punkcie końcowym autoryzacji użytkownik uwierzytelnia i następnie przyznaje lub nie zezwala na dostęp do aplikacji. < /p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ResponseDescription|< p\> Jeśli właściciel zasobu przyznaje żądanie dostępu, serwer autoryzacji wystawia token dostępu i dostarcza go do klienta przez dodanie następujących parametrów do składnika fragmentu przekierowania URI przy użyciu "application/x-www format — tworzą - urlencoded". < /p\>|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Description|Przepływu kodu autoryzacji jest zoptymalizowana pod kątem klienci z możliwością obsługi poufności swoich poświadczeń (np. aplikacji sieci web serwera implementowane za pomocą języka PHP, Java, Python, Ruby, ASP.NET, itp.).|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Name|Udzielania kodu autoryzacji|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Description|Przepływ poświadczeń klienta jest odpowiednia w przypadku, gdy żąda dostępu do chronionych zasobów pod jej kontrolą klienta (aplikacji). Klient jest traktowana jako właściciela zasobu, więc interakcja użytkownika końcowego nie jest wymagana.|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Name|Udziel poświadczeń klienta|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Description|Przepływu niejawnego jest zoptymalizowana pod kątem klienci bez możliwości o zachowanie poufności swoich poświadczeń znane działanie określonego identyfikator URI przekierowania. Ci klienci są zwykle implementowany w przeglądarce, za pomocą języka skryptów, takich jak JavaScript.|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Name|Niejawne Przyznaj|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Description|Przepływ poświadczenia hasła właściciela zasobu jest odpowiednia w przypadku, gdy właściciel zasobu ma relację zaufania z klienta (aplikacji), takich jak system operacyjny urządzenia lub aplikacji wysoko uprzywilejowane. Ten przepływ jest odpowiednia dla klientów w stanie uzyskać zasób poświadczenia właściciela (nazwa użytkownika i hasło, zwykle za pomocą formularza interaktywnego).|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Name|Przyznaj poświadczenia hasła właściciela zasobu|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_Description|< p\> właściciel zasobu przekazuje temu klientowi swoją nazwę użytkownika i hasło. < /p\> < p\> klient żąda token dostępu z serwera autoryzacji "punktu końcowego tokena s, umieszczając w niej poświadczeń otrzymanych od właściciela zasobów.  W żądaniu skierowanym, klient jest uwierzytelniany w usłudze serwera autoryzacji. < /p\> < p\> serwera autoryzacji uwierzytelnia klientów i sprawdza poprawność poświadczeń właściciela zasobu, a jeśli jest prawidłowa, wystawia token dostępu. < /p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ErrorDescription|< p\> Jeśli żądanie nie powiodło się uwierzytelnienie klienta lub jest nieprawidłowa serwera autoryzacji odpowiada z kodem stanu HTTP 400 (nieprawidłowe żądanie) (chyba że określono inaczej) i zawiera następujące parametry z odpowiedzią. < /p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_RequestDescription|< p\> klient wysyła żądanie do punktu końcowego tokenu, dodając następujące parametry w formacie "application/x--www-form-urlencoded" z kodowania znaków UTF-8 w HTTP żądania treści jednostki. < /p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ResponseDescription|< p\> w przypadku żądania tokenu dostępu jest prawidłowa i autoryzowanych, serwer autoryzacji wystawia token dostępu i token odświeżania opcjonalne i tworzy odpowiedzi, dodając następujące parametry do treści jednostki odpowiedzi HTTP z 20 Kod stanu (OK) 0. < /p\>|  
|OAuth2Step_AccessTokenRequest_Name|Żądanie tokenu dostępu|  
|OAuth2Step_AuthorizationRequest_Name|Żądania autoryzacji|  
|OAuth2AccessToken_AuthorizationCodeGrant_TokenResponse|WYMAGANE. Token dostępu wystawione przez serwer autoryzacji.|  
|OAuth2AccessToken_ClientCredentialsGrant_TokenResponse|WYMAGANE. Token dostępu wystawione przez serwer autoryzacji.|  
|OAuth2AccessToken_ImplicitGrant_AuthorizationResponse|WYMAGANE. Token dostępu wystawione przez serwer autoryzacji.|  
|OAuth2AccessToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|WYMAGANE. Token dostępu wystawione przez serwer autoryzacji.|  
|OAuth2ClientId_AuthorizationCodeGrant_AuthorizationRequest|WYMAGANE. Identyfikator klienta.|  
|OAuth2ClientId_AuthorizationCodeGrant_TokenRequest|WYMAGANE, jeśli klient nie uwierzytelnia serwer autoryzacji.|  
|OAuth2ClientId_ImplicitGrant_AuthorizationRequest|WYMAGANE. Identyfikator klienta.|  
|OAuth2Code_AuthorizationCodeGrant_AuthorizationResponse|WYMAGANE. Kod autoryzacji generowane przez serwer autoryzacji.|  
|OAuth2Code_AuthorizationCodeGrant_TokenRequest|WYMAGANE. Kod autoryzacji otrzymany z serwera autoryzacji.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_AuthorizationErrorResponse|OPCJONALNE. Czytelny dla człowieka tekst w formacie ASCII podanie dodatkowych informacji.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_TokenErrorResponse|OPCJONALNE. Czytelny dla człowieka tekst w formacie ASCII podanie dodatkowych informacji.|  
|OAuth2ErrorDescription_ClientCredentialsGrant_TokenErrorResponse|OPCJONALNE. Czytelny dla człowieka tekst w formacie ASCII podanie dodatkowych informacji.|  
|OAuth2ErrorDescription_ImplicitGrant_AuthorizationErrorResponse|OPCJONALNE. Czytelny dla człowieka tekst w formacie ASCII podanie dodatkowych informacji.|  
|OAuth2ErrorDescription_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|OPCJONALNE. Czytelny dla człowieka tekst w formacie ASCII podanie dodatkowych informacji.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_AuthorizationErrorResponse|OPCJONALNE. Identyfikator URI identyfikujący czytelny dla człowieka strony sieci web z informacjami o tym błędzie.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_TokenErrorResponse|OPCJONALNE. Identyfikator URI identyfikujący czytelny dla człowieka strony sieci web z informacjami o tym błędzie.|  
|OAuth2ErrorUri_ClientCredentialsGrant_TokenErrorResponse|OPCJONALNE. Identyfikator URI identyfikujący czytelny dla człowieka strony sieci web z informacjami o tym błędzie.|  
|OAuth2ErrorUri_ImplicitGrant_AuthorizationErrorResponse|OPCJONALNE. Identyfikator URI identyfikujący czytelny dla człowieka strony sieci web z informacjami o tym błędzie.|  
|OAuth2ErrorUri_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|OPCJONALNE. Identyfikator URI identyfikujący czytelny dla człowieka strony sieci web z informacjami o tym błędzie.|  
|OAuth2Error_AuthorizationCodeGrant_AuthorizationErrorResponse|WYMAGANE. Jeden kod błędu ASCII spośród następujących: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable.|  
|OAuth2Error_AuthorizationCodeGrant_TokenErrorResponse|WYMAGANE. Jeden kod błędu ASCII spośród następujących: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2Error_ClientCredentialsGrant_TokenErrorResponse|WYMAGANE. Jeden kod błędu ASCII spośród następujących: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2Error_ImplicitGrant_AuthorizationErrorResponse|WYMAGANE. Jeden kod błędu ASCII spośród następujących: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable.|  
|OAuth2Error_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|WYMAGANE. Jeden kod błędu ASCII spośród następujących: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2ExpiresIn_AuthorizationCodeGrant_TokenResponse|ZALECANE. Okres istnienia w sekundach tokenu dostępu.|  
|OAuth2ExpiresIn_ClientCredentialsGrant_TokenResponse|ZALECANE. Okres istnienia w sekundach tokenu dostępu.|  
|OAuth2ExpiresIn_ImplicitGrant_AuthorizationResponse|ZALECANE. Okres istnienia w sekundach tokenu dostępu.|  
|OAuth2ExpiresIn_ResourceOwnerPasswordCredentialsGrant_TokenResponse|ZALECANE. Okres istnienia w sekundach tokenu dostępu.|  
|OAuth2GrantType_AuthorizationCodeGrant_TokenRequest|WYMAGANE. Wartość musi mieć ustawioną wartość "authorization_code".|  
|OAuth2GrantType_ClientCredentialsGrant_TokenRequest|WYMAGANE. MUSI mieć ustawioną wartość 'client_credentials'.|  
|OAuth2GrantType_ResourceOwnerPasswordCredentialsGrant_TokenRequest|WYMAGANE. Wartość musi być równa "password".|  
|OAuth2Password_ResourceOwnerPasswordCredentialsGrant_TokenRequest|WYMAGANE. Hasło właściciela zasobów.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_AuthorizationRequest|OPCJONALNE. Identyfikator URI punktu końcowego przekierowania musi być bezwzględnym identyfikatorem URI.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_TokenRequest|WYMAGANE, jeśli parametr "redirect_uri" została uwzględniona w żądaniu autoryzacji i ich wartości muszą być takie same.|  
|OAuth2RedirectUri_ImplicitGrant_AuthorizationRequest|OPCJONALNE. Identyfikator URI punktu końcowego przekierowania musi być bezwzględnym identyfikatorem URI.|  
|OAuth2RefreshToken_AuthorizationCodeGrant_TokenResponse|OPCJONALNE. Token odświeżania może zostać użyty do uzyskania nowe tokeny dostępu.|  
|OAuth2RefreshToken_ClientCredentialsGrant_TokenResponse|OPCJONALNE. Token odświeżania może zostać użyty do uzyskania nowe tokeny dostępu.|  
|OAuth2RefreshToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|OPCJONALNE. Token odświeżania może zostać użyty do uzyskania nowe tokeny dostępu.|  
|OAuth2ResponseType_AuthorizationCodeGrant_AuthorizationRequest|WYMAGANE. Wartość musi być równa 'code'.|  
|OAuth2ResponseType_ImplicitGrant_AuthorizationRequest|WYMAGANE. Wartość musi być ustawiona na "token".|  
|OAuth2Scope_AuthorizationCodeGrant_AuthorizationRequest|OPCJONALNE. Zakres żądania dostępu.|  
|OAuth2Scope_AuthorizationCodeGrant_TokenResponse|OPCJONALNIE, jeśli takie same jak zakres żądanego przez klienta; w przeciwnym razie jest to wymagane.|  
|OAuth2Scope_ClientCredentialsGrant_TokenRequest|OPCJONALNE. Zakres żądania dostępu.|  
|OAuth2Scope_ClientCredentialsGrant_TokenResponse|OPCJONALNE, jeśli takie same jak zakres żądanego przez klienta; w przeciwnym razie jest to wymagane.|  
|OAuth2Scope_ImplicitGrant_AuthorizationRequest|OPCJONALNE. Zakres żądania dostępu.|  
|OAuth2Scope_ImplicitGrant_AuthorizationResponse|OPCJONALNIE, jeśli takie same jak zakres żądanego przez klienta; w przeciwnym razie jest to wymagane.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenRequest|OPCJONALNE. Zakres żądania dostępu.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenResponse|OPCJONALNE, jeśli takie same jak zakres żądanego przez klienta; w przeciwnym razie jest to wymagane.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationErrorResponse|WYMAGANE, jeśli z parametrem "stanu" było obecne w żądaniu autoryzacji klienta.  Dokładną wartość otrzymanych od klienta.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationRequest|ZALECANE. Wartości nieprzezroczystej używany przez klienta do zarządzania stanem między żądaniem i wywołania zwrotnego.  Serwer autoryzacji zawiera tę wartość, podczas przekierowywania agent użytkownika klienta.  Parametr należy używać zapobiegania fałszowaniu żądania międzywitrynowego.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationResponse|WYMAGANE, jeśli z parametrem "stanu" było obecne w żądaniu autoryzacji klienta.  Dokładną wartość otrzymanych od klienta.|  
|OAuth2State_ImplicitGrant_AuthorizationErrorResponse|WYMAGANE, jeśli z parametrem "stanu" było obecne w żądaniu autoryzacji klienta.  Dokładną wartość otrzymanych od klienta.|  
|OAuth2State_ImplicitGrant_AuthorizationRequest|ZALECANE. Wartości nieprzezroczystej używany przez klienta do zarządzania stanem między żądaniem i wywołania zwrotnego.  Serwer autoryzacji zawiera tę wartość, podczas przekierowywania agent użytkownika klienta.  Parametr należy używać zapobiegania fałszowaniu żądania międzywitrynowego.|  
|OAuth2State_ImplicitGrant_AuthorizationResponse|WYMAGANE, jeśli z parametrem "stanu" było obecne w żądaniu autoryzacji klienta.  Dokładną wartość otrzymanych od klienta.|  
|OAuth2TokenType_AuthorizationCodeGrant_TokenResponse|WYMAGANE. Typem wystawianych tokenów.|  
|OAuth2TokenType_ClientCredentialsGrant_TokenResponse|WYMAGANE. Typem wystawianych tokenów.|  
|OAuth2TokenType_ImplicitGrant_AuthorizationResponse|WYMAGANE. Typem wystawianych tokenów.|  
|OAuth2TokenType_ResourceOwnerPasswordCredentialsGrant_TokenResponse|WYMAGANE. Typem wystawianych tokenów.|  
|OAuth2UserName_ResourceOwnerPasswordCredentialsGrant_TokenRequest|WYMAGANE. Nazwa właściciela zasobów.|  
|OAuth2UnsupportedTokenType|Typ tokenu "{0}" nie jest supporetd.|  
|OAuth2InvalidState|Nieprawidłowa odpowiedź z serwera autoryzacji|  
|OAuth2GrantType_AuthorizationCode|Kod autoryzacji|  
|OAuth2GrantType_Implicit|Niejawne|  
|OAuth2GrantType_ClientCredentials|Poświadczenia klienta|  
|OAuth2GrantType_ResourceOwnerPassword|Hasło właściciela zasobów|  
|WebDocumentation302Code|Znaleziono 302|  
|WebDocumentation400Code|400 (nieprawidłowe żądanie)|  
|OAuth2SendingMethod_AuthHeader|Nagłówek autoryzacji|  
|OAuth2SendingMethod_QueryParam|Parametr zapytania|  
|OAuth2AuthorizationServerGeneralException|Wystąpił błąd podczas autoryzowania dostępu za pośrednictwem {0}|  
|OAuth2AuthorizationServerCommunicationException|Nie można nawiązać połączenia HTTP serwera autoryzacji lub został nieoczekiwanie zamknięty.|  
|WebDocumentationOAuth2GeneralErrorMessage|Wystąpił nieoczekiwany błąd.|  
|AuthorizationServerCommunicationException|Wystąpił wyjątek łączności serwera autoryzacji. Skontaktuj się z administratorem.|  
|TextblockSubscriptionKeyHeaderDescription|Klucz subskrypcji, która zapewnia dostęp do tego interfejsu API. W Twojej < href = "/ developer"\>profilu < /a\>.|  
|TextblockOAuthHeaderDescription|Token dostępu protokołu OAuth 2.0 uzyskane z < i\>{0} < /i\>. Obsługiwane typy grant: < i\>{1} < /i\>.|  
|TextblockContentTypeHeaderDescription|Typ nośnika treści wysyłane do interfejsu API.|  
|ErrorMessageApiNotAccessible|Interfejs API, którą chcesz wywołać nie jest dostępny w tej chwili. Skontaktuj się z wydawcą interfejsu API < href = "/ wystawia"\>tutaj < /a\>.|  
|ErrorMessageApiTimedout|Interfejsu API, którą chcesz wywołać trwa dłużej niż zwykle, aby wrócić odpowiedzi. Skontaktuj się z wydawcą interfejsu API < href = "/ wystawia"\>tutaj < /a\>.|  
|BadRequestParameterExpected|"oczekuje parametru '{0}'"|  
|TooltipTextDoubleClickToSelectAll|Kliknij dwukrotnie, aby wybrać wszystkie.|  
|TooltipTextHideRevealSecret|Pokaż/Ukryj|  
|ButtonLinkOpenConsole|Wypróbuj|  
|SectionHeadingRequestBody|Treść żądania|  
|SectionHeadingRequestParameters|Parametry żądania|  
|SectionHeadingRequestUrl|Adres URL żądania|  
|SectionHeadingResponse|Odpowiedź|  
|SectionHeadingRequestHeaders|Nagłówki żądania|  
|FormLabelSubtextOptional|Opcjonalne|  
|SectionHeadingCodeSamples|Przykłady kodu|  
|TextblockOpenidConnectHeaderDescription|Token identyfikatora OpenID Connect uzyskane z < i\>{0} < /i\>. Obsługiwane typy grant: < i\>{1} < /i\>.|  
  
###  <a name="ErrorPageStrings"></a>ErrorPageStrings  
  
|Nazwa|Tekst|  
|----------|----------|  
|LinkLabelBack|Wstecz|  
|LinkLabelHomePage|Strona główna|  
|LinkLabelSendUsEmail|Wyślij do nas wiadomość e-mail|  
|PageTitleError|Niestety, wystąpił problem podczas obsługująca żądanej strony|  
|TextblockPotentialCauseIntermittentIssue|Może to być problem dostępu sporadyczne danych, który został już usunięty.|  
|TextblockPotentialCauseOldLink|Kliknięte na łącze mogą być stara i nie wskazuje już poprawnej lokalizacji.|  
|TextblockPotentialCauseTechnicalProblem|Może istnieć problem techniczny po naszej stronie.|  
|TextblockPotentialSolutionRefresh|Spróbuj odświeżyć stronę.|  
|TextblockPotentialSolutionStartOver|Rozpoczęcie od {0}.|  
|TextblockPotentialSolutionTryAgain|Przejdź do {0} i spróbuj wykonać akcję, należy wykonać ponownie.|  
|TextReportProblem|{0} opisujące co poszło źle i firma Microsoft będzie wyglądać jego jak możemy.|  
|TitlePotentialCause|Potencjalną przyczynę|  
|TitlePotentialSolution|Prawdopodobnie jest po prostu problem tymczasowy, kilka możliwości do wypróbowania|  
  
###  <a name="IssuesStrings"></a>IssuesStrings  
  
|Nazwa|Tekst|  
|----------|----------|  
|WebIssuesIndexTitle|Problemy|  
|WebIssuesNoActiveSubscriptions|Nie masz żadnych aktywnych subskrypcji. Musisz mieć subskrypcję produktu zgłosić problem.|  
|WebIssuesNotSignin|Nie zalogowano Cię. Skontaktuj się z {0}, aby zgłosić problem lub post komentarz.|  
|WebIssuesReportIssueButton|Zgłoś problem|  
|WebIssuesSignIn|logowanie|  
|WebIssuesStatusReportedBy|Stan: {0} &#124; Zgłoszone przez element {1}|  
  
###  <a name="NotFoundStrings"></a>NotFoundStrings  
  
|Nazwa|Tekst|  
|----------|----------|  
|LinkLabelHomePage|Strona główna|  
|LinkLabelSendUsEmail|Wyślij do nas wiadomość e-mail|  
|PageTitleNotFound|Niestety nie możemy znaleźć strona, której szukasz|  
|TextblockPotentialCauseMisspelledUrl|Możesz błędna adres URL, jeśli został wpisany.|  
|TextblockPotentialCauseOldLink|Kliknięte na łącze mogą być stara i nie wskazuje już poprawnej lokalizacji.|  
|TextblockPotentialSolutionRetype|Spróbuj przepisywania adres URL.|  
|TextblockPotentialSolutionStartOver|Rozpoczęcie od {0}.|  
|TextReportProblem|{0} opisujące co poszło źle i firma Microsoft będzie wyglądać jego jak możemy.|  
|TitlePotentialCause|Potencjalną przyczynę|  
|TitlePotentialSolution|Potencjalne rozwiązania|  
  
###  <a name="ProductDetailsStrings"></a>ProductDetailsStrings  
  
|Nazwa|Tekst|  
|----------|----------|  
|WebProductsAgreement|Subskrypcja do {0} produktu, zgadzam się na `<a data-toggle='modal' href='#legal-terms'\>Terms of Use</a\>`.|  
|WebProductsLegalTermsLink|Warunki użytkowania|  
|WebProductsSubscribeButton|Subskrybuj|  
|WebProductsUsageLimitsHeader|Limity użycia|  
|WebProductsYouAreNotSubscribed|Subskrybujesz tego produktu.|  
|WebProductsYouRequestedSubscription|Żądana Subskrypcja do tego produktu.|  
|ErrorYouNeedtoAgreeWithLegalTerms|Aby kontynuować, musisz zaakceptować warunki użytkowania.|  
|ButtonLabelAddSubscription|Dodawanie subskrypcji|  
|LinkLabelChangeSubscriptionName|zmiany|  
|ButtonLabelConfirm|Upewnij się|  
|TextblockMultipleSubscriptionsCount|Posiadania subskrypcji {0} do tego produktu:|  
|TextblockSingleSubscriptionsCount|Masz subskrypcję {0} do tego produktu:|  
|TextblockSingleApisCount|Ten produkt zawiera API {0}:|  
|TextblockMultipleApisCount|Ten produkt zawiera {0} interfejsów API:|  
|TextblockHeaderSubscribe|Subskrypcja do produktu|  
|TextblockSubscriptionDescription|Nowa subskrypcja zostanie utworzona w następujący sposób:|  
|TextblockSubscriptionLimitReached|Osiągnięto limit subskrypcji.|  
  
###  <a name="ProductsStrings"></a>ProductsStrings  
  
|Nazwa|Tekst|  
|----------|----------|  
|PageTitleProducts|Produkty|  
  
###  <a name="ProviderInfoStrings"></a>ProviderInfoStrings  
  
|Nazwa|Tekst|  
|----------|----------|  
|TextboxExternalIdentitiesDisabled|Logowanie zostało wyłączone przez administratorów w tej chwili.|  
|TextboxExternalIdentitiesSigninInvitation|Alternatywnie Zaloguj się przy użyciu|  
|TextboxExternalIdentitiesSigninInvitationPrimary|Zaloguj się przy użyciu:|  
  
###  <a name="SigninResources"></a>SigninResources  
  
|Nazwa|Tekst|  
|----------|----------|  
|PrincipalNotFound|Nie można odnaleźć podmiotu zabezpieczeń lub podpis jest nieprawidłowy|  
|ErrorSsoAuthenticationFailed|Usługa rejestracji Jednokrotnej uwierzytelnianie nie powiodło się|  
|ErrorSsoAuthenticationFailedDetailed|Nie można zweryfikować nieprawidłowy token dostarczony lub podpisu.|  
|ErrorSsoTokenInvalid|Token rejestracji Jednokrotnej jest nieprawidłowy|  
|ValidationErrorSpecificEmailAlreadyExists|Wiadomości e-mail "{0}" jest już zarejestrowany|  
|ValidationErrorSpecificEmailInvalid|Poczta e-mail "{0}" jest nieprawidłowy|  
|ValidationErrorPasswordInvalid|Hasło jest nieprawidłowe. Popraw błędy i spróbuj ponownie.|  
|PropertyTooShort|trwa krócej niż {0}|  
|WebAuthenticationAddresserEmailInvalidErrorMessage|Nieprawidłowy adres e-mail.|  
|ValidationMessageNewPasswordConfirmationRequired|Potwierdź nowe hasło|  
|ValidationErrorPasswordConfirmationRequired|Upewnij się, że hasło jest puste|  
|WebAuthenticationEmailChangeNotice|Wiadomość e-mail z potwierdzeniem zmian jest w drodze do {0}. Wykonaj instrukcje w nim, aby potwierdzić swój nowy adres e-mail. Jeśli adres e-mail został odebrany w ciągu następnych kilku minut do skrzynki odbiorczej, sprawdź folder wiadomości-śmieci.|  
|WebAuthenticationEmailChangeNoticeHeader|Pomyślnie przetworzono żądanie zmiany poczty e-mail|  
|WebAuthenticationEmailChangeNoticeTitle|Żądana zmiana poczty e-mail|  
|WebAuthenticationEmailHasBeenRevertedNotice|Wiadomości e-mail możesz już istnieje. Żądania została wycofana.|  
|ValidationErrorEmailAlreadyExists|Wiadomości e-mail już istnieje.|  
|ValidationErrorEmailInvalid|Nieprawidłowy adres e-mail|  
|TextboxLabelEmail|Adres e-mail|  
|ValidationErrorEmailRequired|Poczta e-mail jest wymagane.|  
|WebAuthenticationErrorNoticeHeader|Błąd|  
|WebAuthenticationFieldLengthErrorMessage|{0} musi być maksymalnej długości {1}|  
|TextboxLabelEmailFirstName|Imię|  
|ValidationErrorFirstNameRequired|Wymagana jest nazwa pierwszego.|  
|ValidationErrorFirstNameInvalid|Nieprawidłowa nazwa pierwszego|  
|NoticeInvalidInvitationToken|Należy pamiętać, że potwierdzenie łączy są prawidłowe tylko 48 godzin. Jeśli jesteś nadal w tym przedziale czasu, upewnij się, czy łącze jest prawidłowa. Po wygaśnięciu ważności link do należy powtórzyć akcję, którą próbujesz Potwierdź.|  
|NoticeHeaderInvalidInvitationToken|Zaproszenie nieprawidłowy token|  
|NoticeTitleInvalidInvitationToken|Błąd potwierdzenia|  
|WebAuthenticationLastNameInvalidErrorMessage|Nieprawidłowa nazwa ostatniego|  
|TextboxLabelEmailLastName|Nazwisko|  
|ValidationErrorLastNameRequired|Ostatnia nazwa jest wymagana.|  
|WebAuthenticationLinkExpiredNotice|Łącze potwierdzenia wysłanego utracił ważność. `<a href={0}?token={1}>Resend confirmation email.</a\>`|  
|NoticePasswordResetLinkInvalidOrExpired|Link do resetowania hasła jest nieprawidłowa lub wygasła.|  
|WebAuthenticationLinkExpiredNoticeTitle|Łącze|  
|WebAuthenticationNewPasswordLabel|Nowe hasło|  
|ValidationMessageNewPasswordRequired|Nowe hasło jest wymagane.|  
|TextboxLabelNotificationsSenderEmail|Powiadomienia e-mail nadawcy|  
|TextboxLabelOrganizationName|Nazwa organizacji|  
|WebAuthenticationOrganizationRequiredErrorMessage|Nazwa organizacji jest pusty|  
|WebAuthenticationPasswordChangedNotice|Twoje hasło zostało pomyślnie zaktualizowane.|  
|WebAuthenticationPasswordChangedNoticeTitle|Hasło|  
|WebAuthenticationPasswordCompareErrorMessage|Hasła nie są zgodne|  
|WebAuthenticationPasswordConfirmLabel|Potwierdź hasło|  
|ValidationErrorPasswordInvalidDetailed|Hasło jest zbyt słabe.|  
|WebAuthenticationPasswordLabel|Hasło|  
|ValidationErrorPasswordRequired|Wymagane jest hasło.|  
|WebAuthenticationPasswordResetSendNotice|Zmień wiadomość e-mail z potwierdzeniem hasła jest w drodze do {0}. Postępuj zgodnie z instrukcjami w wiadomości e-mail, aby kontynuować przetwarzanie zmiany hasła.|  
|WebAuthenticationPasswordResetSendNoticeHeader|Pomyślnie przetworzono żądanie resetowania hasła|  
|WebAuthenticationPasswordResetSendNoticeTitle|Zażądano zresetowania hasła|  
|WebAuthenticationRequestNotFoundNotice|Nie znaleziono żądania|  
|WebAuthenticationSenderEmailRequiredErrorMessage|Powiadomienia e-mail nadawcy jest pusty|  
|WebAuthenticationSigninPasswordLabel|Potwierdź zmianę poprzez podanie hasła|  
|WebAuthenticationSignupConfirmNotice|Wiadomość e-mail z potwierdzeniem rejestracji jest w drodze do {0}. < br /\> wykonaj instrukcje w wiadomości e-mail, aby aktywować konto. < br /\> Jeśli adres e-mail został odebrany w ciągu następnych kilku minut w Twojej skrzynce odbiorczej, sprawdź, czy Twoje wiadomości-śmieci folder poczty e-mail.|  
|WebAuthenticationSignupConfirmNoticeHeader|Pomyślnie utworzono konto|  
|WebAuthenticationSignupConfirmNoticeRepeatHeader|Wysłano wiadomość e-mail z potwierdzeniem rejestracji ponownie|  
|WebAuthenticationSignupConfirmNoticeTitle|Utworzono konto|  
|WebAuthenticationTokenRequiredErrorMessage|Token jest pusty|  
|WebAuthenticationUserAlreadyRegisteredNotice|Wydaje się, że użytkownik z tej wiadomości e-mail jest już zarejestrowany w systemie. Jeśli nie pamiętasz hasła, spróbuj je przywrócić lub skontaktuj się z naszym zespołem pomocy technicznej.|  
|WebAuthenticationUserAlreadyRegisteredNoticeHeader|Użytkownik już zarejestrowany|  
|WebAuthenticationUserAlreadyRegisteredNoticeTitle|Już zarejestrowany|  
|ButtonLabelChangePassword|Zmień hasło|  
|ButtonLabelChangeAccountInfo|Zmień informacje o koncie|  
|ButtonLabelCloseAccount|Zamknij konto|  
|WebAuthenticationInvalidCaptchaErrorMessage|Wprowadzony tekst nie jest zgodny tekst na obrazie. Spróbuj ponownie.|  
|ValidationErrorCredentialsInvalid|Adres e-mail lub hasło jest nieprawidłowe. Popraw błędy i spróbuj ponownie.|  
|WebAuthenticationRequestIsNotValid|Żądanie jest nieprawidłowe|  
|WebAuthenticationUserIsNotConfirm|Potwierdź rejestrację, przed podjęciem próby wykonania do logowania.|  
|WebAuthenticationInvalidEmailFormated|Poczta e-mail jest nieprawidłowy: {0}|  
|WebAuthenticationUserNotFound|Nie znaleziono użytkownika|  
|WebAuthenticationTenantNotRegistered|Używane konto należy do dzierżawy usługi Azure Active Directory, która nie ma uprawnień dostępu do tego portalu.|  
|WebAuthenticationAuthenticationFailed|Uwierzytelnianie nie powiodło się.|  
|WebAuthenticationGooglePlusNotEnabled|Uwierzytelnianie nie powiodło się. Jeśli autoryzowany aplikacji następnie skontaktuj skontaktuj się z pomocą administratora, aby upewnić się, że uwierzytelnianie Google jest poprawnie skonfigurowany.|  
|ValidationErrorAllowedTenantIsRequired|Dozwolone dzierżawy jest wymagana|  
|ValidationErrorTenantIsNotValid|Dzierżawy usługi Azure Active Directory "{0}" jest nieprawidłowy.|  
|WebAuthenticationActiveDirectoryTitle|Usługa Azure Active Directory|  
|WebAuthenticationLoginUsingYourProvider|Zaloguj się za pomocą konta {0}|  
|WebAuthenticationUserLimitNotice|Ta usługa została osiągnięta maksymalna liczba dozwolonych użytkowników. Sprawdź `<a href="mailto:{0}"\>contact the administrator</a\>` do uaktualnienia usługi i ponownie Włącz rejestrację użytkownika.|  
|WebAuthenticationUserLimitNoticeHeader|Rejestracja użytkownika wyłączone|  
|WebAuthenticationUserLimitNoticeTitle|Rejestracja użytkownika wyłączone|  
|WebAuthenticationUserRegistrationDisabledNotice|Rejestracja użytkowników została wyłączona przez administratora. Zaloguj się z dostawcą tożsamości zewnętrznej.|  
|WebAuthenticationUserRegistrationDisabledNoticeHeader|Rejestracja użytkownika wyłączone|  
|WebAuthenticationUserRegistrationDisabledNoticeTitle|Rejestracja użytkownika wyłączone|  
|WebAuthenticationSignupPendingConfirmationNotice|Przed możemy ukończyć tworzenia Twoje konto, musimy zweryfikować adres e-mail. Wysłaliśmy wiadomość e-mail do {0}. Postępuj zgodnie z instrukcjami w wiadomości e-mail, aby aktywować konto. Jeśli adres e-mail nie dostarczona w ciągu następnych kilku minut, sprawdź folder wiadomości-śmieci.|  
|WebAuthenticationSignupPendingConfirmationAccountFoundNotice|Znaleziono niepotwierdzone konta dla adresu e-mail {0}. Aby zakończyć tworzenie Twoje konto, musimy zweryfikować adres e-mail. Wysłaliśmy wiadomość e-mail do {0}. Postępuj zgodnie z instrukcjami w wiadomości e-mail, aby aktywować konto. Jeśli adres e-mail nie dostarczona w ciągu następnych kilku minut, sprawdź folder wiadomości-śmieci|  
|WebAuthenticationSignupConfirmationAlmostDone|Prawie gotowe|  
|WebAuthenticationSignupConfirmationEmailSent|Wysłaliśmy wiadomość e-mail do {0}. Postępuj zgodnie z instrukcjami w wiadomości e-mail, aby aktywować konto. Jeśli adres e-mail nie dostarczona w ciągu następnych kilku minut, sprawdź folder wiadomości-śmieci.|  
|WebAuthenticationEmailSentNotificationMessage|Wiadomość e-mail została wysłana do {0}|  
|WebAuthenticationNoAadTenantConfigured|Nie skonfigurowane dla usługi dzierżawy usługi Azure Active Directory.|  
|CheckboxLabelUserRegistrationTermsConsentRequired|Zgadzam się na `<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use</a\>`.|  
|TextblockUserRegistrationTermsProvided|Zapoznaj się z tematem`<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use.</a\>`|  
|DialogHeadingTermsOfUse|Warunki użytkowania|  
|ValidationMessageConsentNotAccepted|Aby kontynuować, musisz zaakceptować warunki użytkowania.|  
  
###  <a name="SigninStrings"></a>SigninStrings  
  
|Nazwa|Tekst|  
|----------|----------|  
|WebAuthenticationForgotPassword|Nie pamiętasz hasła?|  
|WebAuthenticationIfAdministrator|Jeśli jesteś administratorem należy zalogować się `<a href="{0}"\>here</a\>`.|  
|WebAuthenticationNotAMember|Nie w elemencie członkowskim jeszcze? `<a href="/signup"\>Sign up now</a\>`|  
|WebAuthenticationRemember|Zapamiętaj mnie na tym komputerze|  
|WebAuthenticationSigininWithPassword|Zaloguj się przy użyciu nazwy użytkownika i hasła|  
|WebAuthenticationSigninTitle|Logowanie|  
|WebAuthenticationSignUpNow|Utwórz konto teraz|  
  
###  <a name="SignupStrings"></a>SignupStrings  
  
|Nazwa|Tekst|  
|----------|----------|  
|PageTitleSignup|Rejestrowanie|  
|WebAuthenticationAlreadyAMember|Jesteś już członkiem?|  
|WebAuthenticationCreateNewAccount|Utwórz nowe konto usługi API Management|  
|WebAuthenticationSigninNow|Zaloguj się teraz|  
|ButtonLabelSignup|Rejestrowanie|  
  
###  <a name="SubscriptionListStrings"></a>SubscriptionListStrings  
  
|Nazwa|Tekst|  
|----------|----------|  
|SubscriptionCancelConfirmation|Czy na pewno chcesz anulować tę subskrypcję?|  
|SubscriptionRenewConfirmation|Czy na pewno chcesz odnawiać tę subskrypcję?|  
|WebDevelopersManageSubscriptions|Zarządzanie subskrypcjami|  
|WebDevelopersPrimaryKey|Klucz podstawowy|  
|WebDevelopersRegenerateLink|Regenerate|  
|WebDevelopersSecondaryKey|Klucz pomocniczy|  
|ButtonLabelShowKey|Show|  
|ButtonLabelRenewSubscription|Renew|  
|WebDevelopersSubscriptionReqested|Żądana {0}|  
|WebDevelopersSubscriptionRequestedState|Żądanie|  
|WebDevelopersSubscriptionTableNameHeader|Nazwa|  
|WebDevelopersSubscriptionTableStateHeader|Stan|  
|WebDevelopersUsageStatisticsLink|Raporty analizy|  
|WebDevelopersYourSubscriptions|Subskrypcji|  
|SubscriptionPropertyLabelRequestedDate|Żądana|  
|SubscriptionPropertyLabelStartedDate|Uruchomiona na|  
|PageTitleRenameSubscription|Zmień nazwę subskrypcji|  
|SubscriptionPropertyLabelName|Nazwa subskrypcji|  
  
###  <a name="SubscriptionStrings"></a>SubscriptionStrings  
  
|Nazwa|Tekst|  
|----------|----------|  
|SectionHeadingCloseAccount|Wyszukiwanie, aby zamknąć konto?|  
|PageTitleDeveloperProfile|Profil|  
|ButtonLabelHideKey|Ukryj|  
|ButtonLabelRegenerateKey|Regenerate|  
|InformationMessageKeyWasRegenerated|Czy na pewno chcesz ponownie wygenerować klucz ten?|  
|ButtonLabelShowKey|Show|  
  
###  <a name="UpdateProfileStrings"></a>UpdateProfileStrings  
  
|Nazwa|Tekst|  
|----------|----------|  
|ButtonLabelUpdateProfile|Aktualizuj profil|  
|PageTitleUpdateProfile|Aktualizowanie informacji o koncie|  
  
###  <a name="UserProfile"></a>UserProfile  
  
|Nazwa|Tekst|  
|----------|----------|  
|ButtonLabelChangeAccountInfo|Zmień informacje o koncie|  
|ButtonLabelChangePassword|Zmień hasło|  
|ButtonLabelCloseAccount|Zamknij konto|  
|TextboxLabelEmail|Adres e-mail|  
|TextboxLabelEmailFirstName|Imię|  
|TextboxLabelEmailLastName|Nazwisko|  
|TextboxLabelNotificationsSenderEmail|Powiadomienia e-mail nadawcy|  
|TextboxLabelOrganizationName|Nazwa organizacji|  
|SubscriptionStateActive|Aktywne|  
|SubscriptionStateCancelled|Anulowane|  
|SubscriptionStateExpired|Ważność|  
|SubscriptionStateRejected|Odrzucone|  
|SubscriptionStateRequested|Żądanie|  
|SubscriptionStateSuspended|Zawieszone|  
|DefaultSubscriptionNameTemplate|{0} (ustawienie domyślne)|  
|SubscriptionNameTemplate|Dostęp Developer #{0}|  
|TextboxLabelSubscriptionName|Nazwa subskrypcji|  
|ValidationMessageSubscriptionNameRequired|Nazwa subskrypcji nie może być pusta.|  
|ApiManagementUserLimitReached|Ta usługa została osiągnięta maksymalna liczba dozwolonych użytkowników. Przeprowadź uaktualnienie do wyższej warstwy cenowej.|  
  
##  <a name="glyphs"></a>Zasoby symbolu  
 Zarządzanie interfejsami API developer portal szablonów można użyć symboli z [Glyphicons od początkowego](http://getbootstrap.com/components/#glyphicons). Ten zestaw symboli zawiera ponad 250 symboli w formacie czcionki z [Glyphicon](http://glyphicons.com/) Halflings ustawiony. Aby użyć symbolu z tego zestawu, użyj następującej składni.  
  
```html  
<span class="glyphicon glyphicon-user">  
```  
  
 Aby uzyskać pełną listę symboli, zobacz [Glyphicons od początkowego](http://getbootstrap.com/components/#glyphicons).

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [dostosowywaniu portalu dla deweloperów interfejsu API zarządzania za pomocą szablonów](api-management-developer-portal-templates.md).
