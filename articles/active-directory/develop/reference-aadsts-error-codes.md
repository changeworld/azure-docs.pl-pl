---
title: Azure Active Directory uwierzytelnianie i autoryzacja kodów do błędów | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o kodach błędów AADSTS, które zostaną zwrócone z usługi tokenu zabezpieczającego (STS) usługi Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 02/13/2019
ms.author: celested
ms.reviewer: hirsin, justhu
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 69440fb99439231cdc046ef48bddfa852c17924c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59271802"
---
# <a name="authentication-and-authorization-error-codes"></a>Kody błędów uwierzytelniania i autoryzacji

Szukasz informacji na temat AADSTS kody błędów, które są zwracane z Usługa tokenu zabezpieczającego (STS) usługi Azure Active Directory (Azure AD)? Czytać ten dokument, aby znaleźć AADSTS opisów błędów, poprawki i niektóre sugerowane rozwiązania problemu.

> [!NOTE]
> Informacja ta jest wstępna i może ulec zmianie. Masz pytania lub nie można znaleźć, czego szukasz? Utwórz problem usługi GitHub lub zobacz [opcje pomocy technicznej i pomocy dla deweloperów](active-directory-develop-help-support.md) informacje o innych sposobach możesz uzyskać pomoc i obsługa techniczna.
>
> Ta dokumentacja dla deweloperów i wskazówki dotyczące administratora, ale nigdy nie powinna być używana przez klienta. Kody błędów są może ulec zmianie w dowolnym momencie w celu zapewnienia bardziej szczegółowe komunikaty o błędach, które mają na celu pomoc dla deweloperów podczas kompilowania aplikacji. Aplikacje, które zależni tekst lub błąd numery kodu zostanie przerwane wraz z upływem czasu.  

## <a name="aadsts-error-codes"></a>Kody błędów AADSTS

| Błąd | Opis |
|---|---|
| AADSTS16000 | SelectUserAccount — jest to przerwania zgłoszony przez usługę Azure AD, co skutkuje interfejs użytkownika, który umożliwia użytkownikowi wybrać spośród wielu sesji logowania jednokrotnego prawidłowe. Ten błąd jest dość typowy i mogą być zwrócone do aplikacji, jeśli `prompt=none` jest określony. |
| AADSTS16001 | UserAccountSelectionInvalid — zostanie wyświetlony ten błąd, jeśli użytkownik kliknie na kafelku, która odrzuciła logiki wybierz sesji. Po wyzwoleniu tego błędu umożliwia użytkownikowi odzyskiwanie przez pobrania ze zaktualizowanej listy kafelków/sesji lub wybierając inne konto. Ten błąd może wystąpić z powodu warunku kodu wady lub sytuacji wyścigu. |
| AADSTS16002 | AppSessionSelectionInvalid — wymaganie SID określona aplikacja nie została osiągnięta.  |
| AADSTS16003 | SsoUserAccountNotFoundInResourceTenant — wskazuje, że użytkownik nie została jawnie dodana do dzierżawy. |
| AADSTS17003 | CredentialKeyProvisioningFailed — usługa Azure AD nie może aprowizować klucza użytkownika. |
| AADSTS20001 | WsFedSignInResponseError — istnieje problem z dostawcą tożsamości federacyjnych. Skontaktuj się ze swoim dostawcą tożsamości, aby rozwiązać ten problem. |
| AADSTS20012 | WsFedMessageInvalid — istnieje problem z dostawcą tożsamości federacyjnych. Skontaktuj się ze swoim dostawcą tożsamości, aby rozwiązać ten problem. |
| AADSTS20033 | FedMetadataInvalidTenantName — istnieje problem z dostawcą tożsamości federacyjnych. Skontaktuj się ze swoim dostawcą tożsamości, aby rozwiązać ten problem. |
| AADSTS40008 | OAuth2IdPUnretryableServerError — istnieje problem z dostawcą tożsamości federacyjnych. Skontaktuj się ze swoim dostawcą tożsamości, aby rozwiązać ten problem. |
| AADSTS40009 | OAuth2IdPRefreshTokenRedemptionUserError — istnieje problem z dostawcą tożsamości federacyjnych. Skontaktuj się ze swoim dostawcą tożsamości, aby rozwiązać ten problem. |
| AADSTS40010 | OAuth2IdPRetryableServerError — istnieje problem z dostawcą tożsamości federacyjnych. Skontaktuj się ze swoim dostawcą tożsamości, aby rozwiązać ten problem. |
| AADSTS40015 | OAuth2IdPAuthCodeRedemptionUserError — istnieje problem z dostawcą tożsamości federacyjnych. Skontaktuj się ze swoim dostawcą tożsamości, aby rozwiązać ten problem. |
| AADSTS50000 | TokenIssuanceError — istnieje problem z usługą logowania. [Otwórz bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md), aby rozwiązać ten problem. |
| AADSTS50001 | InvalidResource - zasobu jest wyłączona lub nie istnieje. Sprawdź kod aplikacji, aby upewnić się, że został określony adres URL zasobu dokładnie dla zasobu, do którego próbujesz uzyskać dostęp.  |
| AADSTS50002 | NotAllowedTenant - Sign in nie powiodło się z powodu dostęp ograniczony serwera proxy, w ramach dzierżawy. Jeśli zasady dzierżawy, możesz zmienić ustawienia dzierżawy ograniczony, aby rozwiązać ten problem. |
| AADSTS50003 | MissingSigningKey - Sign in nie powiodło się ze względu na Brak klucza podpisywania lub certyfikatów. Może to być, ponieważ nie było żadnych klucza podpisywania, które zostały skonfigurowane w aplikacji. Sprawdź rozwiązania opisane tutaj: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured). Jeśli nadal widzisz problemy, skontaktuj się z właścicielem aplikacji lub administratorem aplikacji. |
| AADSTS50005 | DevicePolicyError — użytkownik próbował zalogować się do urządzenia z platformą, która nie jest obecnie obsługiwana za pomocą zasad dostępu warunkowego. |
| AADSTS50006 | InvalidSignature — Weryfikacja podpisu nie powiodła się ze względu na nieprawidłowy podpis. |
| AADSTS50007 | PartnerEncryptionCertificateMissing — dla tej aplikacji nie znaleziono certyfikatu szyfrowania partnera. [Otwórz bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md) z firmą Microsoft, aby uzyskać dostęp do tej stałej. |
| AADSTS50008 | InvalidSamlToken — potwierdzenie SAML lub jest niepoprawnie skonfigurowany w tokenie. Skontaktuj się z dostawcą federacji. |
| AADSTS50010 | AudienceUriValidationFailed — identyfikator URI odbiorców Weryfikacja aplikacji nie powiodła się, ponieważ zostały skonfigurowane nie odbiorców tokenu. |
| AADSTS50011 | InvalidReplyTo - adres zwrotny brakuje, nieprawidłowo skonfigurowane, lub jest niezgodna z adresy zwrotne skonfigurowane dla aplikacji.  Rozwiązanie tego problemu upewnij się, aby dodać ten brak Adres Odpowiedz do aplikacji usługi Azure Active Directory lub ktoś z uprawnieniami do zarządzania aplikacją w usłudze Active Directory to dla Ciebie.|
| AADSTS50012 | Nie można uwierzytelnić AuthenticationFailed — jednej z następujących przyczyn:<ul><li>Nazwa podmiotu certyfikatu podpisywania nie jest autoryzowany.</li><li>Nie można odnaleźć odpowiednich zasad zaufany urząd dla nazwy podmiotu autoryzowane</li><li>Łańcuch certyfikatów jest nieprawidłowy</li><li>Certyfikat podpisywania jest nieprawidłowy</li><li>Nie skonfigurowano zasad w ramach dzierżawy</li><li>Odcisk palca certyfikatu podpisywania nie jest autoryzowany.</li><li>Potwierdzenia klienta zawiera nieprawidłowy podpis</li></ul> |
| AADSTS50013 | InvalidAssertion - potwierdzenia jest nieprawidłowy z różnych powodów — wystawcy tokenów nie jest zgodna api zakres wersji w ramach jego prawidłową godzinę — wygasły token odświeżania — źle sformułowane — w potwierdzenie nie jest token odświeżania podstawowego. |
| AADSTS50014 | GuestUserInPendingState - realizacji przez użytkownika jest w stanie oczekiwania. Konta gościa nie pełni powstaje jeszcze. |
| AADSTS50015 | ViralUserLegalAgeConsentRequiredState — użytkownik wymaga programu pełnoletności grupy zgody. |
| AADSTS50017 | CertificateValidationFailed — weryfikacja certyfikatu nie powiodło się, ze względu na z następujących powodów:<ul><li>Nie można odnaleźć certyfikatu wystawcy na liście zaufanych certyfikatów</li><li>Nie można odnaleźć oczekiwanego elementu CrlSegment</li><li>Nie można odnaleźć certyfikatu wystawcy na liście zaufanych certyfikatów</li><li>Punkt dystrybucji różnicowej listy CRL jest skonfigurowany bez odpowiedniego punktu dystrybucji listy CRL</li><li>Nie można pobrać prawidłową listę CRL segmentów z powodu błędu w przypadku przekroczenia limitu czasu</li><li>Nie można pobrać listy CRL</li></ul>Skontaktuj się z administratorem dzierżawy. |
| AADSTS50020 | UserUnauthorized — użytkownicy mają autoryzacji do wywołania tego punktu końcowego. |
| AADSTS50027 | InvalidJwtToken - token JWT nieprawidłowy z następujących powodów:<ul><li>nie zawiera oświadczenia nonce, oświadczenia sub</li><li>niezgodność identyfikatora podmiotu</li><li>zduplikowane oświadczenie w oświadczeniach idToken</li><li>nieoczekiwany wystawca</li><li>nieoczekiwani odbiorcy</li><li>nie mieści się w prawidłowym zakresie czasu </li><li>format tokenu nie jest prawidłowy</li><li>weryfikacja tokenu zewnętrznego identyfikatora od wystawcy nie powiodła się.</li></ul> |
| AADSTS50029 | Nieprawidłowy identyfikator URI — nazwa domeny zawiera nieprawidłowe znaki. Skontaktuj się z administratorem dzierżawy. |
| AADSTS50032 | WeakRsaKey — wskazuje użytkownik błędnie próbę użycia słabe klucza RSA. |
| AADSTS50033 | RetryableError — wskazuje błąd przejściowy, nie są związane z operacji w bazie danych. |
| AADSTS50034 | UserAccountNotFound — logowanie się do tej aplikacji, musisz dodać to konto do katalogu. |
| AADSTS50042 | UnableToGeneratePairwiseIdentifierWithMissingSalt — ziarna wymagane do wygenerowania parowania identyfikator nie istnieje w zasadzie. Skontaktuj się z administratorem dzierżawy. |
| AADSTS50043 | UnableToGeneratePairwiseIdentifierWithMultipleSalts |
| AADSTS50048 | SubjectMismatchesIssuer - podmiotu niezgodność wystawcy oświadczenia w potwierdzenia klienta. Skontaktuj się z administratorem dzierżawy. |
| AADSTS50049 | NoSuchInstanceForDiscovery — nieznany lub nieprawidłowy wystąpienia. |
| AADSTS50050 | MalformedDiscoveryRequest — żądanie jest nieprawidłowo sformułowany. |
| AADSTS50053 | IdsLocked — to konto jest zablokowane, ponieważ użytkownik podjął zbyt wiele prób zalogowania przy użyciu niepoprawnego Identyfikatora użytkownika lub hasło. |
| AADSTS50055 | InvalidPasswordExpiredPassword - hasło wygasło. |
| AADSTS50056 | Nieprawidłowy lub równy null haseł — hasła nie istnieje w magazynie dla tego użytkownika. |
| AADSTS50057 | UserDisabled — konto użytkownika jest wyłączona. Konto zostało wyłączone przez administratora. |
| AADSTS50058 | UserInformationNotProvided — oznacza to, że nie jest zalogowany użytkownik. Jest to typowy błąd, który oczekuje się, gdy użytkownik nie została uwierzytelniona i nie jest jeszcze zalogowany.</br>Jeśli ten błąd, zaleca się w kontekście logowania jednokrotnego, gdzie użytkownik wcześniej logował się, oznacza to, że sesji logowania jednokrotnego było nie znaleziono lub nieprawidłowy.</br>Tego błędu mogą być zwrócone do aplikacji, jeśli jest to szybkie = nie jest określona. |
| AADSTS50059 | MissingTenantRealmAndNoUserInformationProvided — informacje identyfikacyjne dzierżawy nie została znaleziona w jednym żądaniu lub też dorozumianych przez dowolnego podanych poświadczeń. Użytkownik może skontaktować się z administratorem dzierżawy, aby pomóc rozwiązać ten problem. |
| AADSTS50061 | SignoutInvalidRequest - wyrejestrowywania żądanie jest nieprawidłowe. |
| AADSTS50064 | CredentialAuthenticationError — sprawdzanie poprawności poświadczeń na nazwy użytkownika i hasła nie powiodło się. |
| AADSTS50068 | SignoutInitiatorNotParticipant - wylogowanie nie powiodło się. Aplikacja, która zainicjowała Wyloguj się nie jest uczestnikiem w bieżącej sesji. |
| AADSTS50070 | SignoutUnknownSessionIdentifier - wylogowanie nie powiodło się. Żądanie wylogowanie określony identyfikator nazwy, który nie pasował istniejące sesje. |
| AADSTS50071 | SignoutMessageExpired - żądaniu wylogowania utracił ważność. |
| AADSTS50072 | UserStrongAuthEnrollmentRequiredInterrupt — użytkownik musi zarejestrować na potrzeby uwierzytelniania dwuskładnikowego (Logowanie interakcyjne). |
| AADSTS50074 | UserStrongAuthClientAuthNRequiredInterrupt — wymagane jest silne uwierzytelnianie, a użytkownik nie przeszedł żądania uwierzytelniania MFA. |
| AADSTS50076 | UserStrongAuthClientAuthNRequired — z powodu zmiany konfiguracji wprowadzone przez administratora, lub ponieważ został on przeniesiony do nowej lokalizacji, użytkownik musi używać uwierzytelniania wieloskładnikowego, dostępu do zasobu. Spróbuj ponownie za pomocą nowego żądania autoryzacji dla zasobu. |
| AADSTS50079 | UserStrongAuthEnrollmentRequired — z powodu zmiany konfiguracji wprowadzone przez administratora, lub ponieważ użytkownik przeniesione do nowej lokalizacji, użytkownik musi używać uwierzytelniania wieloskładnikowego. |
| AADSTS50085 | Token odświeżania wymaga danych logowania dostawy tożsamości z serwisów społecznościowych. Użytkownik musi zalogować się ponownie przy użyciu nazwy użytkownika i hasła |
| AADSTS50086 | SasNonRetryableError |
| AADSTS50087 | SasRetryableError — usługa jest tymczasowo niedostępna. Spróbuj ponownie. |
| AADSTS50089 | Token uwierzytelniania wygasł — uwierzytelnianie nie powiodło się. Użytkownik, wypróbuj logowanie się ponownie przy użyciu nazwy użytkownika-hasło. |
| AADSTS50097 | DeviceAuthenticationRequired - uwierzytelniania urządzenia jest wymagana. |
| AADSTS50099 | PKeyAuthInvalidJwtUnauthorized — podpis JWT jest nieprawidłowy. |
| AADSTS50105 | EntitlementGrantsNotFound - zalogowany użytkownik nie jest przypisany do roli zalogowania w aplikacji. Przypisz użytkownika do aplikacji. Aby uzyskać więcej informacji:[https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role). |
| AADSTS50107 | InvalidRealmUri — obiekt obszaru Federacji żądanego nie istnieje. Skontaktuj się z administratorem dzierżawy. |
| AADSTS50120 | ThresholdJwtInvalidJwtFormat - problem z nagłówkiem JWT. Skontaktuj się z administratorem dzierżawy. |
| AADSTS50124 | ClaimsTransformationInvalidInputParameter — Przekształcanie oświadczeń zawiera nieprawidłowy parametr wejściowy. Skontaktuj się z administratorem dzierżawy, aby zaktualizować zasady. |
| AADSTS50125 | PasswordResetRegistrationRequiredInterrupt - Sign in zostało przerwane z powodu Resetowanie hasła lub wpis rejestracji hasła. |
| AADSTS50126 | InvalidUserNameOrPassword — błąd walidacji poświadczeń z powodu nieprawidłowej nazwy użytkownika lub hasło. |
| AADSTS50127 | BrokerAppNotInstalled — użytkownik musi instalacji aplikacji brokera, aby uzyskać dostęp do tej zawartości. |
| AADSTS50128 | Nieprawidłowa nazwa domeny — żadnych informacji do identyfikowania dzierżawy odnaleziony ani w żądaniu, lub też dorozumianych przez dowolnego podanych poświadczeń. |
| AADSTS50129 | DeviceIsNotWorkplaceJoined — dołączenie do miejsca pracy jest wymagany do zarejestrowania urządzenia. |
| AADSTS50131 | ConditionalAccessFailed — wskazuje różnych błędów dostępu warunkowego, takich jak zły stan urządzenia Windows, żądanie zablokowane z powodu podejrzanych działań, zasady dostępu lub decyzji dotyczących zasad zabezpieczeń. |
| AADSTS50132 | SsoArtifactInvalidOrExpired - sesja jest nieprawidłowa z powodu wygaśnięcia hasła lub niedawnej zmiany hasła. |
| AADSTS50133 | SsoArtifactRevoked - sesja jest nieprawidłowa z powodu wygaśnięcia hasła lub niedawnej zmiany hasła. |
| AADSTS50134 | DeviceFlowAuthorizeWrongDatacenter — centrum danych problem. Aby autoryzować żądania, które zostało zainicjowane przez aplikację w usłudze flow urządzenia protokołu OAuth 2.0, strony autoryzacji muszą być w tym samym centrum danych, w którym znajduje się oryginalne żądanie. |
| AADSTS50135 | PasswordChangeCompromisedPassword — zmiana hasła jest wymagana ze względu na ryzyko konta. |
| AADSTS50136 | RedirectMsaSessionToApp - sesji pojedynczego MSA wykryte. |
| AADSTS50139 | SessionMissingMsaOAuth2RefreshToken - sesja jest nieprawidłowa. ze względu na brak tokenu odświeżania zewnętrznych. |
| AADSTS50140 | KmsiInterrupt — ten błąd wystąpił z powodu przerwania "Nie wylogowuj mnie" podczas logowania użytkownika. [Otwórz bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md) z identyfikatorem korelacji, identyfikatorem żądania i kodem błędu, aby uzyskać więcej informacji. |
| AADSTS50143 | Niezgodność sesji - Sesja jest nieprawidłowa, ponieważ dzierżawy użytkownika jest niezgodna z wskazówkę dotyczącą domeny ze względu na inny zasób.  [Otwórz bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md) kodem Identyfikator korelacji, identyfikator żądania i błąd, aby uzyskać więcej szczegółów. |
| AADSTS50144 | InvalidPasswordExpiredOnPremPassword - użytkownika usługi Active Directory hasło wygasło. Generuj nowe hasło dla użytkownika lub masz użytkowników, resetowania swojego hasła za pomocą narzędzia samoobsługowego resetowania. |
| AADSTS50146 | MissingCustomSigningKey — ta aplikacja jest wymagane do skonfigurowania za pomocą klucza podpisywania specyficznego dla aplikacji. Albo nie została skonfigurowana przy jego użyciu, albo klucz wygasł lub nie jest jeszcze ważny. |
| AADSTS50147 | MissingCodeChallenge — rozmiar parametru żądania kodu nie jest prawidłowy. |
| AADSTS50155 | DeviceAuthenticationFailed — uwierzytelnianie urządzenia dla tego użytkownika nie powiodło się. |
| AADSTS50158 | ExternalSecurityChallenge - test zabezpieczeń zewnętrznych nie był spełniony. |
| AADSTS50161 | InvalidExternalSecurityChallengeConfiguration - oświadczenia wysyłane przez dostawcę zewnętrznego nie jest wystarczająco dużo, lub brak oświadczenia wymagane do zewnętrznego dostawcy. |
| AADSTS50166 | ExternalClaimsProviderThrottled — nie można wysłać żądanie do dostawcy oświadczeń. |
| AADSTS50168 | ChromeBrowserSsoInterruptRequired — klient jest zdolnych do uzyskania tokenu logowania jednokrotnego za pośrednictwem rozszerzenia systemu Windows 10 kont, ale token nie został odnaleziony w żądaniu lub podany token wygasł. |
| AADSTS50169 | InvalidRequestBadRealm — obszar nie jest skonfigurowany obszar w bieżącej przestrzeni nazw usługi. |
| AADSTS50170 | MissingExternalClaimsProviderMapping — kontrolki zewnętrznej mapowanie nie istnieje. |
| AADSTS50177 | ExternalChallengeNotSupportedForPassthroughUsers - challenge zewnętrznych nie jest obsługiwane dla użytkowników, przekazywanie. |
| AADSTS50178 | SessionControlNotSupportedForPassthroughUsers - kontroli sesji nie jest obsługiwane dla użytkowników, przekazywanie. |
| AADSTS50180 | WindowsIntegratedAuthMissing — uwierzytelnianie zintegrowane Windows jest wymagany. Włącz dzierżawę dla bezproblemowego logowania jednokrotnego. |
| AADSTS50187 | DeviceInformationNotProvided - usługa nie może wykonać uwierzytelnianie urządzenia. |
| AADSTS51000 | RequiredFeatureNotEnabled — ta funkcja jest wyłączona. |
| AADSTS51001 | DomainHintMustbePresent - wskazówkę dotyczącą domeny musi być obecny przy użyciu identyfikatora zabezpieczeń w środowisku lokalnym lub nazwę UPN lokalnie. |
| AADSTS51004 | UserAccountNotInDirectory — konto użytkownika nie istnieje w katalogu. |
| AADSTS51005 | TemporaryRedirect - równoważna stan HTTP 307, co oznacza, że żądane informacje znajduje się na określony w nagłówku location identyfikator URI. Po otrzymaniu tego stanu wykonaj nagłówek lokalizacji skojarzonej z odpowiedzią. Pierwotną metodą żądania podczas WPIS przekierowane żądanie będzie również użyć metody POST. |
| AADSTS51006 | ForceReauthDueToInsufficientAuth — uwierzytelnianie zintegrowane Windows jest wymagany. Użytkownik zalogowany przy użyciu tokenu sesji, w którym brakuje oświadczenia uwierzytelniania zintegrowanego Windows. Żądanie użytkownika, aby zalogować się ponownie. |
| AADSTS52004 | DelegationDoesNotExistForLinkedIn — użytkownik nie dostarczył wyrażania zgody dla dostępu do zasobów usługi LinkedIn. |
| AADSTS53000 | DeviceNotCompliant — zasady dostępu warunkowego wymagają zgodnego urządzenia, a urządzenie nie jest zgodne. Użytkownik muszą zarejestrować urządzenie z zatwierdzonych dostawcy zarządzania urządzeniami Przenośnymi, takiego jak usługi Intune. |
| AADSTS53001 | DeviceNotDomainJoined — zasady dostępu warunkowego wymaga urządzenia połączonego z domeną, a urządzenie nie jest przyłączony do domeny. Użyj użytkownika domeny dołączają do urządzenia. |
| AADSTS53002 | ApplicationUsedIsNotAnApprovedApp — aplikacja używana jest zatwierdzonych aplikacji dla dostępu warunkowego. Użytkownik musi użyć jednej z aplikacji z listy zatwierdzonych aplikacji do użycia w celu uzyskania dostępu. |
| AADSTS53003 | BlockedByConditionalAccess - dostęp został zablokowany przez zasady dostępu warunkowego. Zasady dostępu nie zezwala na wystawiania tokenu. |
| AADSTS53004 | ProofUpBlockedDueToRisk — użytkownik musi przejść proces rejestracji uwierzytelniania wieloskładnikowego przed uzyskaniem dostępu do tej zawartości. Użytkownik powinien zarejestrować się w celu uwierzytelniania wieloskładnikowego. |
| AADSTS54000 | MinorUserBlockedLegalAgeGroupRule |
| AADSTS65001 | DelegationDoesNotExist — użytkownik albo administrator nie wyraził zgody na używanie aplikacji z Identyfikatorem X. Wyślij interaktywne żądanie autoryzacji dla tego użytkownika i zasobu. |
| AADSTS65004 | UserDeclinedConsent — użytkownik odmówił wyrażenia zgody na dostęp do aplikacji. Użytkownik musi ponowić próbę zalogowania się i wyrazić zgodę na aplikację|
| AADSTS65005 | MisconfiguredApplication — aplikacja wymagana listy dostępu do zasobów nie zawiera aplikacji wykrywalny przez zasób lub aplikacja kliencka zażądała dostępu do zasobu, który nie został określony w jej listy dostępu do wymaganych zasobów lub usługi Graph zwrócił nieprawidłowy żądanie lub nie można odnaleźć zasobu. Jeśli aplikacja obsługuje SAML, może skonfigurowano aplikację z identyfikatorem niewłaściwego (jednostki). Wypróbuj rozwiązania dla języka SAML, korzystając z linku poniżej: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#no-resource-in-requiredresourceaccess-list](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav) |
| AADSTS67003 | ActorNotValidServiceIdentity |
| AADSTS70000 | InvalidGrant — uwierzytelnianie nie powiodło się. Token odświeżania jest nieprawidłowy. Błąd może być z następujących przyczyn:<ul><li>Nagłówek tworzenia powiązań tokenu jest puste</li><li>Skrót tworzenia powiązań tokenu nie jest zgodny</li></ul> |
| AADSTS70001 | UnauthorizedClient — aplikacja jest wyłączona. |
| AADSTS70002 | InvalidClient — błąd podczas sprawdzania poprawności poświadczeń. Określona wartość client_secret nie jest zgodna z oczekiwaną wartością dla tego klienta. Popraw wartość client_secret i spróbuj ponownie. Aby uzyskać więcej informacji, zobacz [żądania tokenu dostępu za pomocą kodu autoryzacji](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token). |
| AADSTS70003 | UnsupportedGrantType — aplikacja zwrócił typ nieobsługiwany przydział. |
| AADSTS70004 | InvalidRedirectUri — aplikacja zwróciła nieprawidłowy identyfikator URI przekierowania. Adres przekierowania określony przez klienta nie pasuje do żadnych skonfigurowanych adresów ani żadnych adresów na liście zatwierdzonych dostawców OIDC. |
| AADSTS70005 | UnsupportedResponseType - aplikacji zwracany typ nieobsługiwany odpowiedzi z następujących przyczyn:<ul><li>Typ odpowiedzi "token" nie został włączony dla aplikacji</li><li>typ odpowiedzi „id_token” wymaga zakresu OpenID — zawiera nieobsługiwaną wartość parametru OAuth w zakodowanym kanale wctx</li></ul> |
| AADSTS70007 | UnsupportedResponseMode — aplikacja zwrócił nieobsługiwana wartość `response_mode` podczas żądania tokenu.  |
| AADSTS70008 | ExpiredOrRevokedGrant - token odświeżania wygasł z powodu braku aktywności. Token został wystawiony na XXX i był nieaktywny przez pewien czas. |
| AADSTS70011 | InvalidScope — zakres żądany przez aplikację jest nieprawidłowy. |
| AADSTS70012 | MsaServerError — błąd serwera podczas uwierzytelniania użytkownika konta Microsoft (użytkownik). Spróbuj ponownie. Jeśli nadal nie będzie działać, [Otwórz bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md) |
| AADSTS70016 | AuthorizationPending — błąd przepływu urządzenia protokołu OAuth 2.0. Autoryzacja jest w stanie oczekiwania. Urządzenie ponowi próbę żądania sondowania. |
| AADSTS70018 | BadVerificationCode — kod weryfikacyjny nieprawidłowy ze względu na użytkownika, wpisując w kodzie użytkownika problem dla przepływu kodu urządzenia. Autoryzacja nie została zatwierdzona. |
| AADSTS70019 | CodeExpired — kod weryfikacyjny wygasł. Należy mieć użytkownika, ponów próbę logowania. |
| AADSTS75001 | BindingSerializationError — wystąpił błąd podczas tworzenia powiązania z komunikatu protokołu SAML. |
| AADSTS75003 | UnsupportedBindingError — aplikacji wystąpił błąd powiązany nieobsługiwane powiązania (za pośrednictwem powiązania innego niż POST protokołu HTTP nie można wysłać odpowiedź protokołu SAML). |
| AADSTS75005 | Saml2MessageInvalid — usługi Azure AD nie obsługuje żądania języka SAML wysyłane przez aplikację dla logowania jednokrotnego. |
| AADSTS75008 | RequestDeniedError — żądanie z aplikacji zostało odrzucone, ponieważ żądanie języka SAML miała nieoczekiwany docelowego. |
| AADSTS75011 | NoMatchedAuthnContextInOutputClaims — metodę uwierzytelniania za pomocą którego użytkownik uwierzytelniony w usłudze nie odpowiada Żądana metoda uwierzytelniania. |
| AADSTS75016 | Saml2AuthenticationRequestInvalidNameIDPolicy — żądanie uwierzytelniania SAML2 ma nieprawidłowy NameIdPolicy. |
| AADSTS80001 | OnPremiseStoreIsNotAvailable — Agent uwierzytelniania nie może nawiązać połączenia z usługi Active Directory. Upewnij się, że agent serwery należą do tego samego lasu usługi AD jako użytkownicy, których hasła muszą być weryfikowane i będą mogły nawiązać połączenia z usługi Active Directory. |
| AADSTS80002 | OnPremisePasswordValidatorRequestTimedout - żądania weryfikacji hasła upłynął limit czasu. Upewnij się, że usługi Active Directory jest dostępna i działa prawidłowo żądań z agentów. |
| AADSTS80005 | OnPremisePasswordValidatorUnpredictableWebException — wystąpił nieznany błąd podczas przetwarzania odpowiedzi od agenta uwierzytelniania. Ponów żądanie. Jeśli nadal nie będzie działać, [Otwórz bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md) Aby uzyskać więcej informacji na temat błędu. |
| AADSTS80007 | OnPremisePasswordValidatorErrorOccurredOnPrem — Agent uwierzytelniania nie może zweryfikować hasła użytkownika. Sprawdź dzienniki agenta, aby uzyskać więcej informacji i upewnij się, że usługi Active Directory działa zgodnie z oczekiwaniami. |
| AADSTS80010 | OnPremisePasswordValidationEncryptionException — Agent uwierzytelniania nie może odszyfrować hasła. |
| AADSTS80012 | OnPremisePasswordValidationAccountLogonInvalidHours — użytkownicy próbował zalogować poza dozwolonych godzin (określony w AD). |
| AADSTS80013 | OnPremisePasswordValidationTimeSkew — próba uwierzytelnienia nie można ukończyć ze względu na godzinę niesymetryczność między komputer z programem, agenta uwierzytelniania i usługi AD. Napraw czas synchronizacji problemów. |
| AADSTS81004 | DesktopSsoIdentityInTicketIsNotAuthenticated — próba uwierzytelniania Kerberos nie powiodło się. |
| AADSTS81005 | DesktopSsoAuthenticationPackageNotSupported - pakietu uwierzytelniania nie jest obsługiwana. |
| AADSTS81006 | DesktopSsoNoAuthorizationHeader — znaleziono bez nagłówka autoryzacji. |
| AADSTS81007 | Bezproblemowe logowanie Jednokrotne nie włączono DesktopSsoTenantIsNotOptIn - dzierżawy. |
| AADSTS81009 | DesktopSsoAuthorizationHeaderValueWithBadFormat — nie można zweryfikować biletu Kerberos użytkownika. |
| AADSTS81010 | DesktopSsoAuthTokenInvalid - bezproblemowe logowanie Jednokrotne nie powiodło się, ponieważ biletu Kerberos użytkownika wygasła lub jest nieprawidłowa. |
| AADSTS81011 | DesktopSsoLookupUserBySidFailed — nie można znaleźć obiektu użytkownika w oparciu o informacje z biletu Kerberos użytkownika. |
| AADSTS81012 | DesktopSsoMismatchBetweenTokenUpnAndChosenUpn — użytkownik próbujący zalogować się do usługi Azure AD jest inny niż użytkownik zalogowany do urządzenia. |
| AADSTS90002 | InvalidTenantName — nazwa dzierżawy nie można znaleźć w magazynie danych. Upewnij się, czy masz identyfikator dzierżawy poprawne. |
| AADSTS90004 | InvalidRequestFormat - żądanie nie jest poprawnie sformatowany. |
| AADSTS90005 | InvalidRequestWithMultipleRequirements — nie można wykonać żądania. Żądania jest nieprawidłowa, ponieważ identyfikator i wskazówki logowania nie może być używane razem.  |
| AADSTS90006 | ExternalServerRetryableError — usługa jest tymczasowo niedostępna.|
| AADSTS90007 | InvalidSessionId — nieprawidłowe żądanie. Nie można przeanalizować Identyfikatora przekazanej sesji. |
| AADSTS90008 | TokenForItselfRequiresGraphPermission — użytkownik albo administrator nie wyraził zgodę na używanie aplikacji. Jako minimum aplikacja wymaga dostępu do usługi Azure AD, określając identyfikator logowania i użytkownika profilu uprawnienie do odczytu. |
| AADSTS90009 | TokenForItselfMissingIdenticalAppIdentifier — aplikacja żąda tokenu dla siebie. Ten scenariusz jest obsługiwany tylko wtedy, gdy używa zasobu, który jest określony identyfikator aplikacji na podstawie identyfikatora GUID. |
| AADSTS90010 | NotSupported — nie można utworzyć algorytmu. |
| AADSTS90012 | RequestTimeout - żądania został przekroczony. |
| AADSTS90013 | InvalidUserInput — dane wejściowe od użytkownika jest nieprawidłowa. |
| AADSTS90014 | MissingRequiredField - tego kodu błędu mogą występować w różnych przypadkach, gdy oczekiwanego pola nie jest obecny w poświadczeniu. |
| AADSTS90015 | QueryStringTooLong — ciąg zapytania jest zbyt długa. |
| AADSTS90016 | MissingRequiredClaim - token dostępu jest nieprawidłowy. Brak wymaganego oświadczenia. |
| AADSTS90019 | MissingTenantRealm — usługi Azure AD nie mógł określić identyfikator dzierżawy z żądania. |
| AADSTS90022 | AuthenticatedInvalidPrincipalNameFormat — format nazwy podmiotu zabezpieczeń jest nieprawidłowy lub nie odpowiada oczekiwanej `name[/host][@realm]` formatu. Nazwa jednostki jest wymagana, hosta i obszaru są opcjonalne i może być ustawiona na wartość null. |
| AADSTS90023 | InvalidRequest — żądanie uwierzytelnienia usługi jest nieprawidłowa. |
| AADSTS90024 | RequestBudgetExceededError — wystąpił błąd przejściowy. Spróbuj ponownie. |
| AADSTS90033 | MsodsServiceUnavailable — Microsoft Online Directory usługa MSODS () nie jest dostępna. |
| AADSTS90036 | MsodsServiceUnretryableFailure — wystąpił nieoczekiwany, wywołały błąd usługi WCF hostowanej przez MSODS. [Otwórz bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md), aby uzyskać więcej informacji na temat błędu. |
| AADSTS90038 | NationalCloudTenantRedirection - określonego dzierżawcę "Y" należy do chmury krajowe "X". Bieżące wystąpienie 'Z' nie Federację z X w chmurze. Zwracany jest błąd przekierowania chmury. |
| AADSTS90043 | NationalCloudAuthCodeRedirection — ta funkcja jest wyłączona. |
| AADSTS90051 | InvalidNationalCloudId — identyfikator chmury krajowe zawiera identyfikator nieprawidłowy chmury. |
| AADSTS90055 | TenantThrottlingError — ma zbyt wiele żądań przychodzących. Ten wyjątek jest generowany w przypadku dzierżaw zablokowane. |
| AADSTS90056 | BadResourceRequest — Aby zrealizować kodu dla tokenu dostępu do aplikacji należy wysłać żądanie POST `/token` punktu końcowego. Ponadto przed tym należy podać kod autoryzacji i wysłać go w żądaniu POST do `/token` punktu końcowego. Zapoznaj się z tym artykułem, aby uzyskać przegląd przepływ kodu autoryzacji OAuth 2.0: [ https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code ](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code). Bezpośrednie użytkownikowi `/authorize` punktu końcowego, który zwróci authorization_code. Publikując żądanie `/token` punktu końcowego, użytkownik uzyskuje token dostępu. Zaloguj się w witrynie Azure portal i sprawdź **rejestracji aplikacji > punkty końcowe** aby upewnić się, że dwa punkty końcowe zostały prawidłowo skonfigurowane. |
| AADSTS90072 | PassThroughUserMfaError — konta zewnętrzne, które użytkownik zaloguje się za pomocą nie istnieje w dzierżawie, na które one podpisane w; Dlatego użytkownik nie może spełnić wymagania uwierzytelniania MFA dla dzierżawy. Konto musi najpierw dodane jako użytkownik zewnętrzny w dzierżawie. Wyloguj się i zaloguj się przy użyciu innej usługi Azure AD konta użytkownika. |
| AADSTS90081 | OrgIdWsFederationMessageInvalid — wystąpił błąd podczas próby przetworzenia komunikatu protokołu WS-Federation usługi. Komunikat jest nieprawidłowy. |
| AADSTS90082 | OrgIdWsFederationNotSupported — zasady uwierzytelniania wybranej dla żądania nie jest obecnie obsługiwane. |
| AADSTS90084 | OrgIdWsFederationGuestNotAllowed — konta gościa nie jest dozwolone dla tej lokacji. |
| AADSTS90085 | OrgIdWsFederationSltRedemptionFailed — usługa jest nie można wystawić tokenu, ponieważ obiekt firmy nie zostało jeszcze aprowizowane. |
| AADSTS90086 | OrgIdWsTrustDaTokenExpired - token użytkownika DA wygasł. |
| AADSTS90087 | OrgIdWsFederationMessageCreationFromUriFailed — wystąpił błąd podczas tworzenia wiadomości WS-Federation z identyfikatora URI. |
| AADSTS90090 | GraphRetryableError — usługa jest tymczasowo niedostępna. |
| AADSTS90091 | GraphServiceUnreachable |
| AADSTS90092 | GraphNonRetryableError |
| AADSTS90093 | GraphUserUnauthorized - Graph zwrócił niedozwolony kod błędu dla żądania. |
| AADSTS90094 | AdminConsentRequired — wymagana jest zgoda administratora. |
| AADSTS90100 | InvalidRequestParameter — parametr jest pusty lub nieprawidłowy. |
| AADSTS90101 | InvalidEmailAddress - dostarczonych danych nie jest prawidłowym adresem e-mail. Adres e-mail musi być w formacie `someone@example.com`. |
| AADSTS90102 | InvalidUriParameter — wartość musi być prawidłowym bezwzględnym identyfikatorem URI. |
| AADSTS90107 | InvalidXml - żądania jest nieprawidłowa. Upewnij się, że dane nie mają nieprawidłowe znaki.|
| AADSTS90114 | InvalidExpiryDate — znacznik czasu wygaśnięcia tokenu zbiorczego spowoduje, że wygasły token został wystawiony. |
| AADSTS90117 | InvalidRequestInput |
| AADSTS90119 | InvalidUserCode — kod użytkownika jest wartość null lub jest pusty.|
| AADSTS90120 | InvalidDeviceFlowRequest — żądanie zostało już autoryzowany lub odrzucone. |
| AADSTS90121 | InvalidEmptyRequest — nieprawidłowe żądanie puste.|
| AADSTS90123 | IdentityProviderAccessDenied — nie można wystawić tokenu, ponieważ dostawca tożsamości lub oświadczenia wystawiania odrzucił żądanie. |
| AADSTS90124 | V1ResourceV2GlobalEndpointNotSupported — zasób nie jest obsługiwany za pośrednictwem `/common` lub `/consumers` punktów końcowych. Użyj `/organizations` lub punktem końcowym specyficznym dla dzierżawy zamiast tego. |
| AADSTS90125 | DebugModeEnrollTenantNotFound — użytkownik nie istnieje w systemie. Upewnij się, że poprawnie wprowadzono nazwę użytkownika. |
| AADSTS90126 | DebugModeEnrollTenantNotInferred — typ użytkownika nie jest obsługiwana dla tego punktu końcowego. System nie można wywnioskować dzierżawy użytkownika na podstawie nazwy użytkownika. |
| AADSTS90130 | NonConvergedAppV2GlobalEndpointNotSupported — aplikacja nie jest obsługiwany za pośrednictwem `/common` lub `/consumers` punktów końcowych. Użyj `/organizations` lub punktem końcowym specyficznym dla dzierżawy zamiast tego. |
| AADSTS120000 | PasswordChangeIncorrectCurrentPassword |
| AADSTS120002 | PasswordChangeInvalidNewPasswordWeak |
| AADSTS120003 | PasswordChangeInvalidNewPasswordContainsMemberName |
| AADSTS120004 | PasswordChangeOnPremComplexity |
| AADSTS120005 | PasswordChangeOnPremSuccessCloudFail |
| AADSTS120008 | PasswordChangeAsyncJobStateTerminated — błąd niepowtarzający wystąpił.|
| AADSTS120011 | PasswordChangeAsyncUpnInferenceFailed |
| AADSTS120012 | PasswordChangeNeedsToHappenOnPrem |
| AADSTS120013 | PasswordChangeOnPremisesConnectivityFailure |
| AADSTS120014 | PasswordChangeOnPremUserAccountLockedOutOrDisabled |
| AADSTS120015 | PasswordChangeADAdminActionRequired |
| AADSTS120016 | PasswordChangeUserNotFoundBySspr |
| AADSTS120018 | PasswordChangePasswordDoesnotComplyFuzzyPolicy |
| AADSTS120020 | PasswordChangeFailure |
| AADSTS120021 | PartnerServiceSsprInternalServiceError |
| AADSTS130004 | NgcKeyNotFound — nazwy głównej użytkownika nie ma klucza Identyfikatora NGC, skonfigurowane. |
| AADSTS130005 | NgcInvalidSignature - NGC podpis klucza weryfikacji nie powiodło się.|
| AADSTS130006 | NgcTransportKeyNotFound - NGC klucz transportu nie jest skonfigurowane na urządzeniu. |
| AADSTS130007 | NgcDeviceIsDisabled — urządzenie jest wyłączone. |
| AADSTS130008 | NgcDeviceIsNotFound — nie można znaleźć urządzenia przywoływany przez klucz NGC. |
| AADSTS135010 | KeyNotFound |
| AADSTS140000 | InvalidRequestNonce — nie podano identyfikatora jednorazowego żądania. |
| AADSTS140001 | InvalidSessionKey - klucz sesji jest nieprawidłowy.|
| AADSTS165900 | InvalidApiRequest — nieprawidłowe żądanie. |
| AADSTS220450 | UnsupportedAndroidWebViewVersion — wersji dla programu Chrome WebView nie jest obsługiwane. |
| AADSTS220501 | InvalidCrlDownload |
| AADSTS221000 | Nie skonfigurowano DeviceOnlyTokensNotSupportedByResource - zasobu mogą zaakceptować tokeny tylko do urządzeń. |
| AADSTS240001 | BulkAADJTokenUnauthorized — użytkownik nie ma uprawnień do zarejestrowania urządzenia w usłudze Azure AD. |
| AADSTS240002 | RequiredClaimIsMissing - id_token nie może pełnić `urn:ietf:params:oauth:grant-type:jwt-bearer` udzielić.|
| AADSTS530032 | BlockedByConditionalAccessOnSecurityPolicy — Administrator dzierżawy skonfigurował zasady zabezpieczeń, która blokuje tego żądania. Sprawdzenie zasad zabezpieczeń, które są zdefiniowane na poziomie dzierżawy, aby ustalić, czy żądanie spełnia wymagania zasad. |
| AADSTS700016 | UnauthorizedClient_DoesNotMatchRequest — aplikacja nie został znaleziony w katalogu/dzierżawców. Może to nastąpić, jeśli nie została zainstalowana przez administratora dzierżawy lub wyraża zgodę na żaden użytkownik w dzierżawie usługi aplikacji. Możesz może być nieprawidłowo skonfigurowana wartość identyfikatora dla aplikacji, lub wysłać żądanie uwierzytelnienia do nieprawidłowej dzierżawy. |
| AADSTS700020 | InteractionRequired — udzielanie dostępu wymaga interakcji. |
| AADSTS700022 | InvalidMultipleResourcesScope — podana wartość zakresu parametr wejściowy jest nieprawidłowy, ponieważ zawiera on więcej niż jeden zasób. |
| AADSTS700023 | InvalidResourcelessScope — podana jest wartość dla zakresu parametr wejściowy jest nieprawidłowy podczas żądania tokenu dostępu. |
| AADSTS1000000 | UserNotBoundError - Bind interfejs API wymaga od użytkownika usługi Azure AD można również uwierzytelnianie przy użyciu zewnętrznego dostawcy tożsamości, które nie zostało jeszcze przeprowadzone. |
| AADSTS1000002 | Należy poinformować BindCompleteInterruptError - bind została ukończona pomyślnie, ale użytkownik. |

## <a name="next-steps"></a>Kolejne kroki

* Masz pytania lub nie można znaleźć, czego szukasz? Utwórz problem usługi GitHub lub zobacz [opcje pomocy technicznej i pomocy dla deweloperów](active-directory-develop-help-support.md) informacje o innych sposobach możesz uzyskać pomoc i obsługa techniczna.
