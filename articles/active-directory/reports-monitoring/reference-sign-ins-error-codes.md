---
title: Kody błędów w portalu Azure Active Directory | Microsoft Docs
description: Dokumentacja dotycząca kodów błędów w raportach działań związanych z logowaniem.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 08/08/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84e9d484da1b59c456b30cf0b465ec6de5af77d1
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014293"
---
# <a name="sign-in-activity-report-error-codes"></a>Kody błędów raportów działań związanych z logowaniem 

Korzystając z informacji podanych w [raporcie logowania użytkownika](concept-sign-ins.md), znajdziesz odpowiedzi na pytania, takie jak:

- Kto jest zalogowany do mojej aplikacji?
- Które aplikacje zostały zalogowane?
- Które logowania nie powiodły się i dlaczego?

Gdy logowanie nie powiedzie się, zostanie wyświetlony kod błędu odpowiadający awarii. W tym artykule wymieniono kody błędów wraz z ich opisami oraz sugerowany przebieg akcji, o ile ma to zastosowanie. 

## <a name="how-can-i-display-failed-sign-ins"></a>Jak mogę wyświetlić nieudane operacje logowania? 

Przejdź do [raportu logowania](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns) w [Azure Portal](https://portal.azure.com).

![Aktywność logowania](./media/reference-sign-ins-error-codes/61.png "Aktywność związana z logowaniem")

Przefiltruj raport, aby wyświetlić wszystkie nieudane logowania, wybierając pozycję **Niepowodzenie** w polu listy rozwijanej **Stan logowania** .

![Aktywność logowania](./media/reference-sign-ins-error-codes/06.png "Aktywność związana z logowaniem")

Wybranie elementu z listy filtrowanej powoduje otwarcie bloku **szczegóły działania: logowania** . Ten widok zawiera dodatkowe informacje dotyczące nieudanego zdarzenia logowania, **w tym kod błędu logowania** i **przyczynę niepowodzenia**.

![Aktywność logowania](./media/reference-sign-ins-error-codes/05.png "Aktywność związana z logowaniem")

Możesz również programowo uzyskać dostęp do danych logowania przy użyciu [interfejsu API raportowania](concept-reporting-api.md).

## <a name="error-codes"></a>Kody błędów


|Błąd|Opis|
|---|---|
|16000|Jest to wewnętrzna szczegóły implementacji, a nie warunek błędu. Można bezpiecznie zignorować to odwołanie.|
|20001|Wystąpił problem z Twoim dostawcą tożsamości federacyjnej. Skontaktuj się ze swoim dostawcą tożsamości, aby rozwiązać ten problem.|
|20012|Wystąpił problem z Twoim dostawcą tożsamości federacyjnej. Skontaktuj się ze swoim dostawcą tożsamości, aby rozwiązać ten problem.|
|20033|Wystąpił problem z Twoim dostawcą tożsamości federacyjnej. Skontaktuj się ze swoim dostawcą tożsamości, aby rozwiązać ten problem.|
|40008|Wystąpił problem z Twoim dostawcą tożsamości federacyjnej. Skontaktuj się ze swoim dostawcą tożsamości, aby rozwiązać ten problem.|
|40009|Wystąpił problem z Twoim dostawcą tożsamości federacyjnej. Skontaktuj się ze swoim dostawcą tożsamości, aby rozwiązać ten problem.|
|40014|Wystąpił problem z Twoim dostawcą tożsamości federacyjnej. Skontaktuj się ze swoim dostawcą tożsamości, aby rozwiązać ten problem.|
|50000|Wystąpił problem z naszą usługą logowania. [Otwórz bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md), aby rozwiązać ten problem.|
|50001|Nie odnaleziono głównej nazwy usługi w tej dzierżawie. Taka sytuacja może wystąpić, jeśli aplikacja nie została zainstalowana przez administratora dzierżawy lub jeśli podmiot zabezpieczeń nie został znaleziony w katalogu lub jest nieprawidłowy.|
|50002|Logowanie nie powiodło się z powodu ograniczonego dostępu serwera proxy w dzierżawie. Jeśli jesteś własnymi zasadami dzierżawy, możesz zmienić ustawienia dzierżawy z ograniczeniami, aby rozwiązać ten problem.|
|50003|Logowanie nie powiodło się z powodu braku klucza lub certyfikatu podpisywania. Przyczyną może być to, że w aplikacji nie skonfigurowano żadnego klucza podpisywania. Sprawdź rozwiązania opisane tutaj: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured). Jeśli problem będzie się powtarzać, skontaktuj się z właścicielem aplikacji lub administratorem aplikacji.|
|50005|Użytkownik próbował zalogować się na urządzeniu z platformy, która nie jest obecnie obsługiwana przez zasady dostępu warunkowego.|
|50006| Weryfikacja podpisu nie powiodła się z powodu nieprawidłowego podpisu. Sprawdź rozwiązanie opisane tutaj: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery). Jeśli problem będzie się powtarzać, skontaktuj się z właścicielem aplikacji lub administratorem aplikacji.|
|50007|Nie znaleziono certyfikatu szyfrowania partnera dla tej aplikacji. Aby uzyskać ten problem [, Otwórz bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md) w firmie Microsoft.|
|50008|Brak potwierdzenia SAML lub zostało ono nieprawidłowo skonfigurowane w tokenie. Skontaktuj się z dostawcą federacji.|
|50010|Weryfikacja identyfikatora URI odbiorców dla aplikacji nie powiodła się, ponieważ nie skonfigurowano żadnych odbiorców z tokenem. Skontaktuj się z właścicielem aplikacji w celu rozwiązania problemu.|
|50011|Brak adresu zwrotnego, został nieprawidłowo skonfigurowany lub jest inny niż adresy zwrotne skonfigurowane dla aplikacji. Wypróbuj rozwiązanie wymienione w [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application). Jeśli problem będzie się powtarzać, skontaktuj się z właścicielem aplikacji lub administratorem aplikacji.|
|50012| Jest to ogólny komunikat o błędzie wskazujący, że uwierzytelnianie nie powiodło się. Może się to zdarzyć z przyczyn, takich jak brakujące lub nieprawidłowe poświadczenia lub oświadczenia w żądaniu. Upewnij się, że żądanie jest wysyłane z prawidłowymi poświadczeniami i oświadczeniami. |
|50013|Potwierdzenie jest nieprawidłowe z różnych powodów. Na przykład Wystawca tokenu nie jest zgodny z wersją interfejsu API w prawidłowym zakresie czasu, token wygasł lub źle sformułowany lub token odświeżania w potwierdzeniu nie jest podstawowym tokenem odświeżania.|
|50017|Weryfikacja certyfikacji nie powiodła się z następujących powodów:<ul><li>Nie można odnaleźć certyfikatu wystawcy na liście zaufanych certyfikatów</li><li>Nie można odnaleźć oczekiwanego elementu CrlSegment</li><li>Nie można odnaleźć certyfikatu wystawcy na liście zaufanych certyfikatów</li><li>Punkt dystrybucji różnicowej listy CRL jest skonfigurowany bez odpowiedniego punktu dystrybucji listy CRL</li><li>Nie można pobrać prawidłowych segmentów listy CRL z powodu przekroczenia limitu czasu</li><li>Nie można pobrać listy CRL</li></ul>Skontaktuj się z administratorem dzierżawy.|
|50020|Użytkownik nie ma autoryzacji z jednej z następujących przyczyn.<ul><li>Użytkownik próbuje zalogować się przy użyciu konta MSA z punktem końcowym w wersji 1.</li><li>Użytkownik nie istnieje w dzierżawie.</li></ul> Skontaktuj się z właścicielem aplikacji.|
|50027|Token JWT jest nieprawidłowy z następujących przyczyn:<ul><li>nie zawiera oświadczenia nonce, oświadczenia sub</li><li>niezgodność identyfikatora podmiotu</li><li>zduplikowane oświadczenie w oświadczeniach idToken</li><li>nieoczekiwany wystawca</li><li>nieoczekiwani odbiorcy</li><li>nie mieści się w prawidłowym zakresie czasu </li><li>format tokenu nie jest prawidłowy</li><li>weryfikacja tokenu zewnętrznego identyfikatora od wystawcy nie powiodła się.</li></ul>Skontaktuj się z właścicielem aplikacji|
|50029|Nieprawidłowy identyfikator URI — nazwa domeny zawiera nieprawidłowe znaki. Skontaktuj się z administratorem dzierżawy.|
|50034|Użytkownik nie istnieje w katalogu. Skontaktuj się z administratorem dzierżawy.|
|50042|Brak ciągu wymaganego do wygenerowania identyfikatora parowania w zasadzie. Skontaktuj się z administratorem dzierżawy.|
|50048|Podmiot jest niezgodny z oświadczeniem wystawcy w potwierdzeniu klienta. Skontaktuj się z administratorem dzierżawy.|
|50050|Żądanie jest źle sformułowane. Skontaktuj się z właścicielem aplikacji.|
|50053|Konto jest zablokowane, ponieważ użytkownik próbował zalogować się zbyt wiele razy przy użyciu niepoprawnego identyfikatora użytkownika lub hasła.|
|50055|Nieprawidłowe hasło — wprowadzono nieważne hasło.|
|50056|Nieprawidłowe lub puste hasło — hasło nie istnieje w sklepie dla tego użytkownika.|
|50057|Konto użytkownika zostało wyłączone. Konto zostało wyłączone przez administratora.|
|50058|Aplikacja próbowała wykonać logowanie dyskretne, ale użytkownika nie można zalogować dyskretnie. Aplikacja musi uruchomić interaktywny przepływ, który umożliwi użytkownikom zalogowanie się. Skontaktuj się z właścicielem aplikacji.|
|50059|Użytkownik nie istnieje w katalogu. Skontaktuj się z administratorem dzierżawy.|
|50061|Żądanie wylogowania jest nieprawidłowe. Skontaktuj się z właścicielem aplikacji.|
|50072|Użytkownik musi zarejestrować się na potrzeby uwierzytelniania dwuskładnikowego (Interactive).|
|50074|Użytkownik nie przeszedł żądania uwierzytelniania MFA.|
|50076|Użytkownik nie przeszedł testu MFA (nieinterakcyjny).|
|50079|Użytkownik musi zarejestrować się na potrzeby uwierzytelniania dwuskładnikowego (logowania nieinterakcyjnego).|
|50085|Token odświeżania wymaga danych logowania dostawy tożsamości z serwisów społecznościowych. Użytkownik spróbuje ponownie zalogować się przy użyciu nazwy użytkownika i hasła.|
|50089|Token przepływu wygasł — uwierzytelnianie nie powiodło się. Użytkownik spróbuje ponownie zalogować się przy użyciu nazwy użytkownika i hasła|
|50097|Wymagane jest uwierzytelnianie urządzenia. Przyczyną może być fakt, że element DeviceId lub oświadczenia DeviceAltSecId mają **wartość null**lub jeśli nie istnieje urządzenie odpowiadające identyfikatorowi urządzenia.|
|50099|Podpis tokenu JWT jest nieprawidłowy. Skontaktuj się z właścicielem aplikacji.|
|50105|Zalogowany użytkownik nie jest przypisany do roli dla zalogowanej aplikacji. Przypisz użytkownika do aplikacji. Więcej informacji: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role)|
|50107|Żądany obiekt obszaru federacji nie istnieje. Skontaktuj się z administratorem dzierżawy.|
|50120|Problem z nagłówkiem JWT. Skontaktuj się z administratorem dzierżawy.|
|50124|Przekształcenie oświadczeń zawiera nieprawidłowy parametr wejściowy. Aby zaktualizować zasady, skontaktuj się z administratorem dzierżawy.|
|50125|Logowanie zostało przerwane z powodu resetowania hasła lub wpisu rejestracji hasła.|
|50126|Nieprawidłowa nazwa użytkownika lub hasło albo nieprawidłowa lokalna nazwa użytkownika lub hasło.|
|50127|Użytkownik musi zainstalować aplikację brokera, aby uzyskać dostęp do tej zawartości.|
|50128|Nieprawidłowa nazwa domeny — nie znaleziono informacji identyfikacyjnych dzierżawy w żądaniu lub IMPLIKOWANYCH przez żadne podane poświadczenia.|
|50129|Urządzenie nie jest dołączone do miejsca pracy — do zarejestrowania urządzenia wymagane jest **dołączenie do miejsca pracy** .|
|50130|Nie można zinterpretować wartości Claim jako znanej metody uwierzytelniania.|
|50131|Używane w różnych błędach dostępu warunkowego. Na przykład Zły stan urządzenia z systemem Windows albo żądanie zablokowane z powodu podejrzanych decyzji związanych z działaniami, zasadami dostępu i zasadami zabezpieczeń.|
|50132|Poświadczenia zostały odwołane z następujących powodów:<ul><li>Artefakt logowania jednokrotnego jest nieprawidłowy lub wygasł</li><li>Sesja nie jest wystarczająco świeża dla aplikacji</li><li>Wysłano żądanie logowania dyskretnego, ale sesja użytkownika z usługą Azure AD jest nieprawidłowy lub wygasła.</li></ul>|
|50133|Sesja jest nieprawidłowa z powodu wygaśnięcia lub niedawnej zmiany hasła.|
|50135|Zmiana hasła jest wymagana ze względu na ryzyko związane z kontem.|
|50136|Przekieruj sesję MSA do aplikacji — wykryto jedną sesję MSA. |
|50140|Ten błąd wystąpił z powodu przerwania „Nie wylogowuj mnie” w trakcie logowania użytkownika. [Otwórz bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md) z identyfikatorem korelacji, identyfikatorem żądania i kodem błędu, aby uzyskać więcej informacji. |
|50143|Niezgodność sesji — sesja jest nieprawidłowa, ponieważ dzierżawca użytkownika nie jest zgodny z wskazówką domeny z powodu innego zasobu. Aby uzyskać więcej informacji,  [Otwórz bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md) o identyfikatorze korelacji, identyfikatorze żądania i kodzie błędu.|
|50144|Ważność hasła użytkownika usługi Active Directory wygasła. Wygeneruj nowe hasło dla użytkownika lub użyj narzędzia do resetowania samoobsługowego.|
|50146|Ta aplikacja musi być skonfigurowana przy użyciu klucza podpisywania specyficznego dla aplikacji. Albo nie została skonfigurowana przy jego użyciu, albo klucz wygasł lub nie jest jeszcze ważny. Skontaktuj się z właścicielem aplikacji.|
|50148|Wartość parametru code_verifier jest niezgodna z elementem code_challenge podanym w żądaniu autoryzacji dla PKCE. Skontaktuj się z deweloperem aplikacji. |
|50155|Uwierzytelnianie urządzenia dla tego użytkownika nie powiodło się.|
|50158|Zewnętrzne wyzwanie zabezpieczeń nie zostało spełnione.|
|50161|Oświadczenia wysyłane przez dostawcę zewnętrznego są niewystarczające lub brakujące oświadczenie zażądało dostawcy zewnętrznemu.|
|50166|Nie można wysłać żądania do dostawcy oświadczeń.|
|50169|Obszar nie jest skonfigurowanym obszarem bieżącej przestrzeni nazw usługi.|
|50172|Zewnętrzny dostawca oświadczeń nie jest zatwierdzony. Skontaktuj się z administratorem dzierżawy|
|50173|Wymagany jest świeży token uwierzytelniania. Zaloguj się ponownie przy użyciu nowych poświadczeń.|
|50177|Test zewnętrzny nie jest obsługiwany w przypadku użytkowników przekazujących.|
|50178|Kontrolka sesji nie jest obsługiwana w przypadku użytkowników przekazujących.|
|50180|Wymagane jest zintegrowane uwierzytelnianie systemu Windows. Włącz dzierżawę dla bezproblemowego logowania jednokrotnego.|
|50181|Błąd związany z uwierzytelnianiem OTP podczas logowania. |
|50201|Ten komunikat przerwania monitu zostanie wyświetlony użytkownikowi podczas logowania, jeśli do użytkownika mają zostać dostarczone dodatkowe informacje.|
|51001|Nie ma wskazówki domeny z lokalnym identyfikatorem zabezpieczeń lokalnej nazwy użytkownika.|
|51004|Konto użytkownika nie istnieje w katalogu.|
|51006|Wymagane jest zintegrowane uwierzytelnianie systemu Windows. Użytkownik zalogował się przy użyciu tokenu sesji, którego brakuje za pośrednictwem żądania. Zażądaj od użytkownika, aby zalogował się ponownie.|
|52004|Użytkownik nie wyraził zgody na dostęp do zasobów usługi LinkedIn. |
|53000|Zasady dostępu warunkowego wymagają zgodnego urządzenia, ale urządzenie nie jest zgodne. Użytkownik musi zarejestrować urządzenie przy użyciu zatwierdzonego dostawcy MDM, takiego jak usługa Intune.|
|53001|Zasady dostępu warunkowego wymagają urządzenia przyłączonego do domeny, ale urządzenie nie jest przyłączone do domeny. Użytkownik musi korzystać z urządzenia przyłączonego do domeny.|
|53002|Używana aplikacja nie jest zatwierdzoną aplikacją dla dostępu warunkowego. Użytkownik musi używać jednej z aplikacji z listy zatwierdzonych aplikacji w celu uzyskania dostępu.|
|53003|Dostęp został zablokowany z powodu zasad dostępu warunkowego.|
|53004|Użytkownik musi ukończyć proces rejestracji w celu używania uwierzytelniania wieloskładnikowego przed uzyskaniem dostępu do tej zawartości. Użytkownik powinien zarejestrować się w celu uwierzytelniania wieloskładnikowego.|
|65001|Aplikacja X nie ma uprawnienia dostępu do aplikacji Y lub uprawnienie zostało odwołany. Lub użytkownik albo administrator nie wyraził zgody na używanie aplikacji z identyfikatorem X. Wyślij interaktywne żądanie autoryzacji dla tego użytkownika i zasobu. Lub użytkownik albo administrator nie wyraził zgody na używanie aplikacji z identyfikatorem X. Wyślij do administratora dzierżawy żądanie autoryzacji dotyczące działania w imieniu aplikacji Y dla zasobu Z.|
|65004|Użytkownik odmówił wyrażenia zgody na dostęp do aplikacji. Użytkownik musi ponowić próbę zalogowania się i wyrazić zgodę na aplikację|
|65005|Lista dostępu do wymaganych zasobów aplikacji nie zawiera aplikacji możliwych do wykrycia przez zasób lub aplikacja kliencka zażądała dostępu do zasobu, który nie został określony na liście dostępu do wymaganych zasobów, lub usługa programu Graph zwróciła nieprawidłowe żądanie lub nie można odnaleźć zasobu. Jeśli aplikacja obsługuje język SAML, być może aplikację skonfigurowano z nieprawidłowym identyfikatorem (jednostką). Wypróbuj rozwiązania dla języka SAML, korzystając z linku poniżej: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav#no-resource-in-requiredresourceaccess-list](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav)|
|70000|Udzielenie dostępu jest nieprawidłowe z następujących przyczyn:<ul><li>Żądane potwierdzenie SAML 2.0 ma nieprawidłową metodę potwierdzenia podmiotu</li><li>Przepływ OnBehalfOf aplikacji nie jest obsługiwany w wersji 2</li><li>Podstawowy token odświeżania nie jest podpisany przy użyciu klucza sesji</li><li>Nieprawidłowy zewnętrzny token odświeżania</li><li>Udzielenie dostępu uzyskano dla innej dzierżawy.</li></ul>|
|70001|Nie można odnaleźć aplikacji o nazwie X w dzierżawie o nazwie Y. Taka sytuacja może wystąpić, jeśli aplikacja o identyfikatorze X nie została zainstalowana przez administratora dzierżawy lub żaden użytkownik w dzierżawie nie wyraził odpowiedniej zgody. Być może nieprawidłowo skonfigurowano wartość identyfikatora dla aplikacji lub wysłano żądanie uwierzytelnienia do niewłaściwej dzierżawy.|
|70002|Aplikacja zwróciła nieprawidłowe poświadczenia klienta. Skontaktuj się z właścicielem aplikacji.|
|70003|Aplikacja zwróciła typ nieobsługiwany typ udzielania. Skontaktuj się z właścicielem aplikacji.|
|70004|Aplikacja zwróciła nieprawidłowy identyfikator URI przekierowania. Adres przekierowania określony przez klienta nie pasuje do żadnych skonfigurowanych adresów ani żadnych adresów na liście zatwierdzonych dostawców OIDC. Skontaktuj się z właścicielem aplikacji.|
|70005|Aplikacja zwróciła nieobsługiwany typ odpowiedzi z następujących powodów:<ul><li>typ odpowiedzi „token” nie jest włączony dla aplikacji</li><li>typ odpowiedzi „id_token” wymaga zakresu OpenID — zawiera nieobsługiwaną wartość parametru OAuth w zakodowanym kanale wctx</li></ul>Skontaktuj się z właścicielem aplikacji.|
|70007|Aplikacja zwróciła nieobsługiwaną wartość parametru „response_mode” podczas żądania tokenu. Skontaktuj się z właścicielem aplikacji.|
|70008|Podany kod autoryzacji lub token odświeżania wygasł lub został odwołany. Użytkownik ponowi próbę zalogowania się.|
|70011|Zakres żądany przez aplikację jest nieprawidłowy. Skontaktuj się z właścicielem aplikacji.|
|70012|Wystąpił błąd serwera podczas uwierzytelniania użytkownika zarządzanego konta usługi (użytkownika). Ponów próbę logowania, a jeśli problem będzie nadal występować, [Otwórz bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md) |
|70018|Nieprawidłowy kod weryfikacyjny — użytkownik wpisał niewłaściwy kod użytkownika dla przepływu kodu urządzenia. Autoryzacja nie jest zatwierdzona.|
|70019|Kod weryfikacyjny wygasł. Ponowienie próby logowania przez użytkownika.|
|70037|Podano nieprawidłową odpowiedź na wezwanie. Odrzucono sesję uwierzytelniania zdalnego.|
|70043|Zarządzanie sesjami dostępu warunkowego platformy Azure wymusza wygaśnięcie sesji|
|70044|Zarządzanie sesjami dostępu warunkowego platformy Azure wymusza wygaśnięcie sesji|
|75001|Wystąpił błąd podczas wiązania komunikatu SAML.|
|75003|Aplikacja zwróciła błąd związany z nieobsługiwanym powiązaniem (odpowiedzi protokołu SAML nie można wysłać za pomocą powiązań innych niż żądania HTTP POST). Skontaktuj się z właścicielem aplikacji.|
|75005|Usługa Azure AD nie obsługuje żądania SAML wysłanego przez aplikację na potrzeby logowania jednokrotnego. Skontaktuj się z właścicielem aplikacji.|
|75008|Żądanie z aplikacji zostało odrzucone, ponieważ żądanie SAML miało nieoczekiwany obiekt docelowy. Skontaktuj się z właścicielem aplikacji.|
|75011|Metoda uwierzytelniania użyta do uwierzytelnienia użytkownika w usłudze jest niezgodna z żądaną metodą uwierzytelniania. Skontaktuj się z właścicielem aplikacji.|
|75016|Żądanie uwierzytelniania SAML2 ma nieprawidłowy element NameIdPolicy. Skontaktuj się z właścicielem aplikacji.|
|80001|Agent uwierzytelniania nie może nawiązać połączenia z usługą Active Directory. Upewnij się, że agent uwierzytelniania jest zainstalowany na komputerze przyłączonym do domeny, który ma kontakt z kontrolerem domeny, który może obsługiwać żądania logowania użytkownika.|
|80002|Błąd wewnętrzny. Upłynął limit czasu żądania weryfikacji hasła. Nie można wysłać żądania uwierzytelnienia do wewnętrznej usługi tożsamości hybrydowej. [Otwórz bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md), aby uzyskać więcej informacji na temat błędu.|
|80003|Agent uwierzytelniania odebrał nieprawidłową odpowiedź. Wystąpił nieznany błąd podczas próby uwierzytelniania za pomocą lokalnej usługi Active Directory. [Otwórz bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md), aby uzyskać więcej informacji na temat błędu.|
|80005|Agent uwierzytelniania: wystąpił nieznany błąd podczas przetwarzania odpowiedzi od agenta uwierzytelniania. [Otwórz bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md), aby uzyskać więcej informacji na temat błędu.|
|80007|Agent uwierzytelniania nie może zweryfikować hasła użytkownika.|
|80010|Agent uwierzytelniania nie może odszyfrować hasła. |
|80011|Agent uwierzytelniania nie może pobrać klucza szyfrowania.|
|80012|Użytkownicy próbujący się zalogować poza dozwolonymi godzinami (określono w usłudze AD).|
|80013|Nie można ukończyć próby uwierzytelnienia z powodu niesymetryczności czasu między maszyną, na której działa agent uwierzytelniania, a usługą AD. Rozwiąż problemy z synchronizacją czasu|
|80014|Upłynął limit czasu agenta uwierzytelniania. Aby uzyskać więcej informacji na temat tego błędu, [Otwórz bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md) z kodem błędu, identyfikatorem korelacji i elementem DateTime.|
|81001|Bilet Kerberos użytkownika jest zbyt duży. Może to się zdarzyć, jeśli użytkownik jest w zbyt wielu grupach i w związku z tym bilet Kerberos zawiera zbyt wiele członkostw w grupach. Zmniejsz liczbę członkostw użytkownika w grupach i spróbuj ponownie.|
|81005|Pakiet uwierzytelniania nie jest obsługiwany.|
|81007|Dzierżawca nie obsługuje bezproblemowego logowania jednokrotnego.|
|81012|To nie jest warunek błędu. Oznacza to, że użytkownik próbujący zalogować się do usługi Azure AD różni się od użytkownika zalogowanego na urządzeniu. Możesz bezpiecznie zignorować ten kod w dziennikach.|
|90010|Żądanie nie jest obsługiwane z różnych powodów. Na przykład żądanie jest wykonywane za pomocą nieobsługiwanej metody żądania (obsługiwana jest tylko Metoda POST) lub algorytm podpisywania tokenu, którego zażądano, nie jest obsługiwany. Skontaktuj się z deweloperem aplikacji.|
|90014| Brak wymaganego pola dla komunikatu protokołu, skontaktuj się z właścicielem aplikacji. Jeśli jesteś właścicielem aplikacji, upewnij się, że masz wszystkie wymagane parametry dla żądania logowania. |
|90051| Nieprawidłowy token delegowania. Określono nieprawidłowy identyfikator chmury krajowej ({cloudId}).|
|90072| Konto musi zostać najpierw dodane jako użytkownik zewnętrzny w dzierżawie. Wyloguj się i zaloguj ponownie przy użyciu innego konta usługi Azure AD.|
|90094| Przydzielenie wymaga uprawnień administratora. Poproszenie administratora dzierżawy o zgodę na tę aplikację.|
|500021|Dzierżawca jest ograniczony przez firmowy serwer proxy. Odmawianie dostępu do zasobów.|
|500121| Uwierzytelnianie w trakcie silnego żądania uwierzytelniania nie powiodło się.|
|500133| Potwierdzenie nie znajduje się w prawidłowym zakresie czasu. Upewnij się, że token dostępu nie wygasł przed użyciem go do potwierdzenia przez użytkownika lub zażądaj nowego tokenu.|
|530021|Aplikacja nie spełnia wymagań dotyczących zatwierdzonej aplikacji dostępu warunkowego.|
|530032|Zablokowane przez zasady zabezpieczeń.| 
|700016|Nie znaleziono aplikacji o identyfikatorze "{appIdentifier}" w katalogu "{dzierżawcname}". Taka sytuacja może wystąpić, jeśli aplikacja nie została zainstalowana przez administratora dzierżawy lub nie została wysłana przez żadnego użytkownika w dzierżawie. Być może wysłano żądanie uwierzytelnienia do niewłaściwej dzierżawy.|
|900432|Klient poufny nie jest obsługiwany w żądaniu między chmurą.|
|7000218|Treść żądania musi zawierać następujący parametr: "client_assertion" lub "client_secret".|


## <a name="next-steps"></a>Następne kroki

* [Raporty logowania — przegląd](concept-sign-ins.md)
* [Programistyczny dostęp do raportów usługi Azure AD](concept-reporting-api.md)
