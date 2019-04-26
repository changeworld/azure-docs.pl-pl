---
title: Kody błędów w raportach działań związanych z logowaniem w portalu usługi Azure Active Directory | Microsoft Docs
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
ms.date: 11/18/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8fefc2eb2e642b5c7ac93b8a1cfc25a54c3b646
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60437183"
---
# <a name="sign-in-activity-report-error-codes"></a>Kody błędów raport aktywności logowania 

Dzięki informacjom zawartym [raporcie logowań użytkownika](concept-sign-ins.md), takich jak znaleźć odpowiedzi na pytania:

- Kto zalogowanie się do mojej aplikacji?
- Aplikacji, które zostały podpisane w celu?
- Które operacje logowania nie powiodła się dlaczego?

Logowanie nie powiedzie się, zobaczysz kod błędu odpowiadający awarii. Ten artykuł zawiera listę kodów błędów i ich opisy, oraz zalecane działanie, jeśli ma to zastosowanie. 

## <a name="how-can-i-display-failed-sign-ins"></a>Jak mogę wyświetlić nieudane operacje logowania? 

Przejdź do [raporcie logowań](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns) w [witryny Azure portal](https://portal.azure.com).

![Działania związane z logowaniem](./media/reference-sign-ins-error-codes/61.png "Działania związane z logowaniem")

Filtruj raport, aby wyświetlić wszystkie nieudane operacje logowania, wybierając **błąd** z **stan logowania** pole listy rozwijanej.

![Działania związane z logowaniem](./media/reference-sign-ins-error-codes/06.png "Działania związane z logowaniem")

Wybranie elementu z listy filtrowanej otwiera **szczegóły działań: Operacje logowania** bloku. Ten widok zawiera dodatkowe informacje na temat nieudanych zdarzenia logowania, w tym **kod błędu logowania** i **Przyczyna niepowodzenia**.

![Działania związane z logowaniem](./media/reference-sign-ins-error-codes/05.png "Działania związane z logowaniem")

Można także programowo uzyskiwać dostęp za pomocą danych logowania [interfejsu API raportowania](concept-reporting-api.md).

## <a name="error-codes"></a>Kody błędów


|Błąd|Opis|
|---|---|
|16000|Jest to szczegółowo opisuje implementacja wewnętrznego i nie jest w stanie błędu. Można bezpiecznie zignorować to odwołanie.|
|20001|Wystąpił problem z Twoim dostawcą tożsamości federacyjnej. Skontaktuj się ze swoim dostawcą tożsamości, aby rozwiązać ten problem.|
|20012|Wystąpił problem z Twoim dostawcą tożsamości federacyjnej. Skontaktuj się ze swoim dostawcą tożsamości, aby rozwiązać ten problem.|
|20033|Wystąpił problem z Twoim dostawcą tożsamości federacyjnej. Skontaktuj się ze swoim dostawcą tożsamości, aby rozwiązać ten problem.|
|40008|Wystąpił problem z Twoim dostawcą tożsamości federacyjnej. Skontaktuj się ze swoim dostawcą tożsamości, aby rozwiązać ten problem.|
|40009|Wystąpił problem z Twoim dostawcą tożsamości federacyjnej. Skontaktuj się ze swoim dostawcą tożsamości, aby rozwiązać ten problem.|
|40014|Wystąpił problem z Twoim dostawcą tożsamości federacyjnej. Skontaktuj się ze swoim dostawcą tożsamości, aby rozwiązać ten problem.|
|50000|Wystąpił problem z naszą usługą logowania. [Otwórz bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md), aby rozwiązać ten problem.|
|50001|Nie odnaleziono głównej nazwy usługi w tej dzierżawie. Może to nastąpić, jeśli aplikacja nie została zainstalowana przez administratora dzierżawy lub zasób podmiotu zabezpieczeń nie został znaleziony w katalogu lub jest nieprawidłowy.|
|50002|Logowanie nie powiodło się z powodu ograniczonego dostępu serwera proxy w dzierżawie. Jeśli zasady dzierżawy, możesz zmienić ustawienia dzierżawy ograniczony, aby rozwiązać ten problem.|
|50003|Logowanie nie powiodło się z powodu braku klucza lub certyfikatu podpisywania. Przyczyną może być to, że w aplikacji nie skonfigurowano żadnego klucza podpisywania. Sprawdź rozwiązania opisane tutaj: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured). Jeśli problem będzie się powtarzać, skontaktuj się z właścicielem aplikacji lub administratorem aplikacji.|
|50005|Użytkownik próbował zalogować się do urządzenia z platformą, która nie jest obecnie obsługiwana za pomocą zasad dostępu warunkowego.|
|50006| Weryfikacja podpisu nie powiodła się z powodu nieprawidłowego podpisu. Sprawdź rozwiązanie opisane tutaj: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery). Jeśli problem będzie się powtarzać, skontaktuj się z właścicielem aplikacji lub administrator aplikacji.|
|50007|Nie znaleziono certyfikatu szyfrowania partnera dla tej aplikacji. [Otwórz bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md) z firmą Microsoft, aby uzyskać dostęp do tej stałej.|
|50008|Brak potwierdzenia SAML lub zostało ono nieprawidłowo skonfigurowane w tokenie. Skontaktuj się z dostawcą federacji.|
|50010|Weryfikacja identyfikatora URI odbiorców dla aplikacji nie powiodła się, ponieważ nie skonfigurowano żadnych odbiorców z tokenem. Rozwiązania, skontaktuj się z właścicielem aplikacji.|
|50011|Brak adresu zwrotnego, został nieprawidłowo skonfigurowany lub jest inny niż adresy zwrotne skonfigurowane dla aplikacji. Wypróbuj rozwiązania opisane w temacie [ https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application ](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application). Jeśli problem będzie się powtarzać, skontaktuj się z właścicielem aplikacji lub administrator aplikacji.|
|50012| Jest ogólny komunikat, który wskazuje, że uwierzytelnianie nie powiodło się. Może to nastąpić powodów, takich jak brakujące lub nieprawidłowe poświadczenia lub oświadczenia w żądaniu. Upewnij się, że żądanie jest wysyłane z prawidłowymi poświadczeniami i oświadczeń. |
|50013|Potwierdzenia jest nieprawidłowy z różnych powodów. Na przykład wystawcy tokenów nie odpowiada wersji interfejsu api w jego zakresie prawidłową godzinę, token jest wygasła lub jest nieprawidłowo sformułowany lub token odświeżania w potwierdzenie nie jest token odświeżania podstawowego.|
|50017|Weryfikacja certyfikacji nie powiodła się z następujących powodów:<ul><li>Nie można odnaleźć certyfikatu wystawcy na liście zaufanych certyfikatów</li><li>Nie można odnaleźć oczekiwanego elementu CrlSegment</li><li>Nie można odnaleźć certyfikatu wystawcy na liście zaufanych certyfikatów</li><li>Punkt dystrybucji różnicowej listy CRL jest skonfigurowany bez odpowiedniego punktu dystrybucji listy CRL</li><li>Nie można pobrać prawidłowych segmentów listy CRL z powodu przekroczenia limitu czasu</li><li>Nie można pobrać listy CRL</li></ul>Skontaktuj się z administratorem dzierżawy.|
|50020|Użytkownik nie ma autoryzacji dla jednego z następujących przyczyn.<ul><li>Użytkownik próbuje zalogować się za pomocą konta MSA z punktem końcowym v1</li><li>Użytkownik nie istnieje w dzierżawie.</li></ul> Skontaktuj się z właścicielem aplikacji.|
|50027|Token JWT jest nieprawidłowy z następujących przyczyn:<ul><li>nie zawiera oświadczenia nonce, oświadczenia sub</li><li>niezgodność identyfikatora podmiotu</li><li>zduplikowane oświadczenie w oświadczeniach idToken</li><li>nieoczekiwany wystawca</li><li>nieoczekiwani odbiorcy</li><li>nie mieści się w prawidłowym zakresie czasu </li><li>format tokenu nie jest prawidłowy</li><li>weryfikacja tokenu zewnętrznego identyfikatora od wystawcy nie powiodła się.</li></ul>Skontaktuj się z właścicielem aplikacji|
|50029|Nieprawidłowy identyfikator URI — nazwa domeny zawiera nieprawidłowe znaki. Skontaktuj się z administratorem dzierżawy.|
|50034|Użytkownik nie istnieje w katalogu. Skontaktuj się z administratorem dzierżawy.|
|50042|Ziarna wymagane do wygenerowania parowania identyfikator nie istnieje w zasadzie. Skontaktuj się z administratorem dzierżawy.|
|50048|Podmiot jest niezgodny z oświadczeniem wystawcy w potwierdzeniu klienta. Skontaktuj się z administratorem dzierżawy.|
|50050|Żądanie jest źle sformułowane. Skontaktuj się z właścicielem aplikacji.|
|50053|Konto zostało zablokowane, ponieważ użytkownik podjął zbyt wiele prób zalogowania przy użyciu niepoprawnego Identyfikatora użytkownika lub hasło.|
|50055|Nieprawidłowe hasło — wprowadzono nieważne hasło.|
|50056|Nieprawidłowy lub równy null hasło — hasła nie istnieje w magazynie dla tego użytkownika.|
|50057|Konto użytkownika zostało wyłączone. Konto zostało wyłączone przez administratora.|
|50058|Aplikacja próbowała wykonać logowanie dyskretne, ale użytkownika nie można zalogować dyskretnie. Aplikacja musi uruchomić interakcyjne przepływu, zapewniając użytkownikom możliwość logowania. Skontaktuj się z właścicielem aplikacji.|
|50059|Użytkownik nie istnieje w katalogu. Skontaktuj się z administratorem dzierżawy.|
|50061|Żądanie wylogowania jest nieprawidłowe. Skontaktuj się z właścicielem aplikacji.|
|50072|Użytkownik musi zarejestrować się na potrzeby uwierzytelniania dwuskładnikowego (Logowanie interakcyjne).|
|50074|Użytkownik nie przeszedł żądania uwierzytelniania MFA.|
|50076|Użytkownik nie przeszedł żądania uwierzytelniania MFA (inne niż logowanie interakcyjne).|
|50079|Użytkownik musi zarejestrować się na potrzeby uwierzytelniania dwuskładnikowego (logowania nieinterakcyjnego).|
|50085|Token odświeżania wymaga danych logowania dostawy tożsamości z serwisów społecznościowych. Ma spróbować logowanie się ponownie przy użyciu nazwy użytkownika i hasła użytkownika.|
|50089|Przepływ token wygasł — uwierzytelnianie nie powiodło się. Wypróbuj logowanie się ponownie przy użyciu nazwy użytkownika i hasła użytkownika|
|50097|Wymagane jest uwierzytelnianie urządzenia. Mogło się tak zdarzyć, ponieważ są oświadczenia DeviceId lub DeviceAltSecId **null**, lub czy istnieje żadnego urządzenia odpowiadający identyfikator urządzenia.|
|50099|Podpis tokenu JWT jest nieprawidłowy. Skontaktuj się z właścicielem aplikacji.|
|50105|Zalogowany użytkownik nie jest przypisany do roli dla zalogowanej aplikacji. Przypisz użytkownika do aplikacji. Więcej informacji: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role)|
|50107|Żądany obiekt obszaru federacji nie istnieje. Skontaktuj się z administratorem dzierżawy.|
|50120|Problem z nagłówkiem JWT. Skontaktuj się z administratorem dzierżawy.|
|50124|Przekształcenie oświadczeń zawiera nieprawidłowy parametr wejściowy. Skontaktuj się z administratorem dzierżawy, aby zaktualizować zasady.|
|50125|Logowanie zostało przerwane z powodu Resetowanie hasła lub wpis rejestracji hasła.|
|50126|Nieprawidłowa nazwa użytkownika lub hasło, lub lokalnego Nieprawidłowa nazwa użytkownika lub hasło.|
|50127|Użytkownik musi zainstalować aplikację brokera w celu uzyskania dostępu do tej zawartości.|
|50128|Nieprawidłowa nazwa domeny — żadnych informacji do identyfikowania dzierżawy odnaleziony ani w żądaniu, lub też dorozumianych przez dowolnego podanych poświadczeń.|
|50129|Urządzenie nie jest dołączone - **dołączanie** jest wymagany do zarejestrowania urządzenia.|
|50130|Wartość oświadczenia nie może być interpretowane jako metoda uwierzytelniania znane.|
|50131|Kod używany w przypadku różnych błędów dostępu warunkowego, Na przykład Zły stan urządzenia z systemem Windows albo żądanie zablokowane z powodu podejrzanych decyzji związanych z działaniami, zasadami dostępu i zasadami zabezpieczeń.|
|50132|Poświadczenia zostały odwołane z następujących powodów:<ul><li>Artefakt logowania jednokrotnego jest nieprawidłowy lub wygasł</li><li>Sesja nie jest wystarczająco świeża dla aplikacji</li><li>Wysłano żądanie logowania dyskretnego, ale sesja użytkownika z usługą Azure AD jest nieprawidłowy lub wygasła.</li></ul>|
|50133|Sesja jest nieprawidłowa z powodu wygaśnięcia lub niedawnej zmiany hasła.|
|50135|Zmiana hasła jest wymagana ze względu na ryzyko konta.|
|50136|Przekierowanie MSA sesji do aplikacji — wykryto zarządzanych kont usług w jednej sesji. |
|50140|Ten błąd wystąpił z powodu przerwania „Nie wylogowuj mnie” w trakcie logowania użytkownika. [Otwórz bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md) z identyfikatorem korelacji, identyfikatorem żądania i kodem błędu, aby uzyskać więcej informacji. |
|50143|Niezgodność sesji - Sesja jest nieprawidłowa, ponieważ dzierżawy użytkownika jest niezgodna z wskazówkę dotyczącą domeny ze względu na inny zasób.  [Otwórz bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md) kodem Identyfikator korelacji, identyfikator żądania i błąd, aby uzyskać więcej szczegółów.|
|50144|Ważność hasła użytkownika usługi Active Directory wygasła. Generuj nowe hasło dla użytkownika lub użytkownika końcowego za pomocą narzędzia samoobsługowego resetowania.|
|50146|Ta aplikacja musi być skonfigurowana przy użyciu klucza podpisywania specyficznego dla aplikacji. Albo nie została skonfigurowana przy jego użyciu, albo klucz wygasł lub nie jest jeszcze ważny. Skontaktuj się z właścicielem aplikacji.|
|50148|Wartość parametru code_verifier jest niezgodna z elementem code_challenge podanym w żądaniu autoryzacji dla PKCE. Skontaktuj się z deweloperem aplikacji. |
|50155|Uwierzytelnianie urządzeń tego użytkownika nie powiodło się.|
|50158|Test zabezpieczeń zewnętrznych nie był spełniony.|
|50161|Oświadczenia wysyłane przez dostawcę zewnętrznego nie jest wystarczająca lub brak oświadczenia wymagane do zewnętrznego dostawcy.|
|50166|Nie można wysłać żądania do dostawcy oświadczeń.|
|50169|Obszar nie jest skonfigurowanym obszarem bieżącej przestrzeni nazw usługi.|
|50172|Zewnętrzny dostawca oświadczeń nie jest zatwierdzony. Skontaktuj się z administratorem dzierżawy|
|50173|Wymagany jest świeży token uwierzytelniania. Mieć logowania użytkownika ponownie, używając świeżych poświadczeń.|
|50177|Zewnętrzne żądanie nie jest obsługiwane dla użytkowników, przekazywanie.|
|50178|Kontrola sesji nie jest obsługiwana dla użytkowników, przekazywanie.|
|50180|Wymagane jest zintegrowane uwierzytelnianie systemu Windows. Włącz dzierżawę dla bezproblemowego logowania jednokrotnego.|
|51001|Wskazówka dotycząca domeny nie jest dostępny z identyfikatorem zabezpieczeń w środowisku lokalnym — nazwę UPN lokalnie.|
|51004|Konto użytkownika nie istnieje w katalogu.|
|51006|Wymagane jest zintegrowane uwierzytelnianie systemu Windows. Użytkownik zalogowany przy użyciu tokenu sesji, który jest brak za pośrednictwem oświadczeń. Zażądaj od użytkownika, aby zalogował się ponownie.|
|52004|Użytkownik nie wyraził zgody na dostęp do zasobów usługi LinkedIn. |
|53000|Zasady dostępu warunkowego wymagają zgodnego urządzenia, ale urządzenie nie jest zgodne. Należy mieć użytkownika zarejestrować swoje urządzenie przy użyciu zatwierdzonych dostawcy zarządzania urządzeniami Przenośnymi, takiej jak Intune.|
|53001|Zasady dostępu warunkowego wymagają urządzenia przyłączonego do domeny, ale urządzenie nie jest przyłączone do domeny. Użyj użytkownika domeny dołączają do urządzenia.|
|53002|Używana aplikacja nie jest zatwierdzoną aplikacją dla dostępu warunkowego. Użytkownik musi używać jednej z aplikacji z listy zatwierdzonych aplikacji w celu uzyskania dostępu.|
|53003|Dostęp został zablokowany ze względu na zasady dostępu warunkowego.|
|53004|Użytkownik musi ukończyć proces rejestracji w celu używania uwierzytelniania wieloskładnikowego przed uzyskaniem dostępu do tej zawartości. Użytkownik powinien zarejestrować się w celu uwierzytelniania wieloskładnikowego.|
|65001|Aplikacja X nie ma uprawnienia dostępu do aplikacji Y lub uprawnienie zostało odwołany. Lub użytkownik albo administrator nie wyraził zgody na używanie aplikacji z identyfikatorem X. Wyślij interaktywne żądanie autoryzacji dla tego użytkownika i zasobu. Lub użytkownik albo administrator nie wyraził zgody na używanie aplikacji z Identyfikatorem X. Wyślij żądanie autoryzacji do administratora dzierżawy na działanie w imieniu aplikacji: Y dla zasobu: Z.|
|65004|Użytkownik odmówił wyrażenia zgody na dostęp do aplikacji. Użytkownik musi ponowić próbę zalogowania się i wyrazić zgodę na aplikację|
|65005|Lista dostępu do wymaganych zasobów aplikacji nie zawiera aplikacji możliwych do wykrycia przez zasób lub aplikacja kliencka zażądała dostępu do zasobu, który nie został określony na liście dostępu do wymaganych zasobów, lub usługa programu Graph zwróciła nieprawidłowe żądanie lub nie można odnaleźć zasobu. Jeśli aplikacja obsługuje język SAML, być może aplikację skonfigurowano z nieprawidłowym identyfikatorem (jednostką). Wypróbuj rozwiązania dla języka SAML, korzystając z linku poniżej: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav#no-resource-in-requiredresourceaccess-list](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav)|
|70000|Udzielenie dostępu jest nieprawidłowe z następujących przyczyn:<ul><li>Żądane potwierdzenie SAML 2.0 ma nieprawidłową metodę potwierdzenia podmiotu</li><li>Przepływ OnBehalfOf aplikacji nie jest obsługiwany w wersji 2</li><li>Podstawowy token odświeżania nie jest podpisany przy użyciu klucza sesji</li><li>Nieprawidłowy zewnętrzny token odświeżania</li><li>Udzielenie dostępu uzyskano dla innej dzierżawy.</li></ul>|
|70001|Nie można odnaleźć aplikacji o nazwie X w dzierżawie o nazwie Y. Taka sytuacja może wystąpić, jeśli aplikacja o identyfikatorze X nie została zainstalowana przez administratora dzierżawy lub żaden użytkownik w dzierżawie nie wyraził odpowiedniej zgody. Możesz może być nieprawidłowo skonfigurowana wartość identyfikatora dla aplikacji, lub wysłać żądanie uwierzytelnienia do nieprawidłowej dzierżawy.|
|70002|Aplikacja zwróciła nieprawidłowe poświadczenia klienta. Skontaktuj się z właścicielem aplikacji.|
|70003|Aplikacja zwróciła typ nieobsługiwany typ udzielania. Skontaktuj się z właścicielem aplikacji.|
|70004|Aplikacja zwróciła nieprawidłowy identyfikator URI przekierowania. Adres przekierowania określony przez klienta nie pasuje do żadnych skonfigurowanych adresów ani żadnych adresów na liście zatwierdzonych dostawców OIDC. Skontaktuj się z właścicielem aplikacji.|
|70005|Aplikacja zwróciła nieobsługiwany typ odpowiedzi z następujących powodów:<ul><li>typ odpowiedzi „token” nie jest włączony dla aplikacji</li><li>typ odpowiedzi „id_token” wymaga zakresu OpenID — zawiera nieobsługiwaną wartość parametru OAuth w zakodowanym kanale wctx</li></ul>Skontaktuj się z właścicielem aplikacji.|
|70007|Aplikacja zwróciła nieobsługiwaną wartość parametru „response_mode” podczas żądania tokenu. Skontaktuj się z właścicielem aplikacji.|
|70008|Podany kod autoryzacji lub Odśwież token wygasł lub został odwołany. Należy mieć użytkownika ponownych prób logowania.|
|70011|Zakres żądany przez aplikację jest nieprawidłowy. Skontaktuj się z właścicielem aplikacji.|
|70012|Wystąpił błąd serwera podczas uwierzytelniania użytkownika zarządzanego konta usługi (użytkownika). Ponów próbę logowania, a jeśli problem będzie się powtarzać, [Otwórz bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md) |
|70018|Nieprawidłowy kod weryfikacyjny — użytkownik wpisał niewłaściwy kod użytkownika dla przepływu kodu urządzenia. Autoryzacja nie została zatwierdzona.|
|70019|Kod weryfikacyjny wygasł. Należy mieć użytkownika, ponów próbę logowania.|
|70037|Podano nieprawidłową odpowiedź na wezwanie. Odrzucono sesję uwierzytelniania zdalnego.|
|75001|Wystąpił błąd podczas wiązania komunikatu SAML.|
|75003|Aplikacja zwróciła błąd związany z nieobsługiwanym powiązaniem (odpowiedzi protokołu SAML nie można wysłać za pomocą powiązań innych niż żądania HTTP POST). Skontaktuj się z właścicielem aplikacji.|
|75005|Usługa Azure AD nie obsługuje żądania SAML wysłanego przez aplikację na potrzeby logowania jednokrotnego. Skontaktuj się z właścicielem aplikacji.|
|75008|Żądanie z aplikacji zostało odrzucone, ponieważ żądanie SAML miało nieoczekiwany obiekt docelowy. Skontaktuj się z właścicielem aplikacji.|
|75011|Metoda uwierzytelniania użyta do uwierzytelnienia użytkownika w usłudze jest niezgodna z żądaną metodą uwierzytelniania. Skontaktuj się z właścicielem aplikacji.|
|75016|Żądanie uwierzytelniania SAML2 ma nieprawidłowy element NameIdPolicy. Skontaktuj się z właścicielem aplikacji.|
|80001|Agent uwierzytelniania nie może nawiązać połączenia z usługą Active Directory. Upewnij się, że agent uwierzytelniania jest zainstalowany na komputerze przyłączonym do domeny, który ma kontakt z kontrolerem domeny, który może obsługiwać żądania logowania użytkownika.|
|80002|Błąd wewnętrzny. Upłynął limit czasu żądania weryfikacji hasła. Nie możemy wysłać żądania uwierzytelnienia do wewnętrznej usługi tożsamości hybrydowej. [Otwórz bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md), aby uzyskać więcej informacji na temat błędu.|
|80003|Agent uwierzytelniania odebrał nieprawidłową odpowiedź. Wystąpił nieznany błąd podczas próby uwierzytelniania za pomocą lokalnej usługi Active Directory. [Otwórz bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md), aby uzyskać więcej informacji na temat błędu.|
|80005|Agent uwierzytelniania: Wystąpił nieznany błąd podczas przetwarzania odpowiedzi od agenta uwierzytelniania. [Otwórz bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md), aby uzyskać więcej informacji na temat błędu.|
|80007|Agent uwierzytelniania nie może zweryfikować hasła użytkownika.|
|80010|Agent uwierzytelniania nie może odszyfrować hasła. |
|80011|Agent uwierzytelniania nie może pobrać klucza szyfrowania.|
|80012|Użytkownicy próbował zalogować się poza dozwolonych godzin (określony w AD).|
|80013|Nie można ukończyć próby uwierzytelnienia z powodu niesymetryczności czasu między maszyną, na której działa agent uwierzytelniania, a usługą AD. Rozwiąż problemy z synchronizacją czasu|
|80014|Upłynął limit czasu agenta uwierzytelniania. [Otwórz bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md) z kodu błędu, identyfikator korelacji i daty/godziny, aby uzyskać więcej informacji na temat tego błędu.|
|81001|Bilet Kerberos użytkownika jest zbyt duży. Może to się zdarzyć, jeśli użytkownik jest w zbyt wielu grupach i w związku z tym bilet Kerberos zawiera zbyt wiele członkostw w grupach. Zmniejsz liczbę członkostw użytkownika w grupach i spróbuj ponownie.|
|81005|Pakiet uwierzytelniania nie jest obsługiwane.|
|81007|Dzierżawa nie jest włączona dla bezproblemowe logowanie Jednokrotne.|
|81012|To nie jest w stanie błędu. Wskazuje ona, że tego użytkownika, próba zalogowania się do usługi Azure AD jest inny niż użytkownik zalogowany do urządzenia. Można bezpiecznie zignorować ten kod w dziennikach.|
|90010|Żądanie nie jest obsługiwane z różnych powodów. Na przykład żądanie jest wysyłane za pomocą metody nieobsługiwane żądanie (obsługiwane tylko metody POST) lub algorytm podpisywania tokenu, którego zażądano nie jest obsługiwana. Skontaktuj się z deweloperem aplikacji.|
|90014| Brak wymaganego pola komunikatu protokołu, skontaktuj się z właścicielem aplikacji. Jeśli jesteś właścicielem aplikacji, upewnij się, że wszystkie niezbędne parametry dla żądania logowania. |
|90051| Delegowanie nieprawidłowy Token. Określono nieprawidłowy identyfikator chmury krajowe ({cloudId}).|
|90072| Konto musi najpierw należy dodać jako użytkownik zewnętrzny w dzierżawie. Wylogować i zalogować ponownie, używając innej usługi Azure AD konta.|
|90094| Przydziel musi mieć uprawnienia administratora. Skontaktuj się z administratorem dzierżawy, zapewnienie wyrażania zgody dla tej aplikacji.|
|500133| Potwierdzenie nie jest w zasięgu prawidłową godzinę. Upewnij się, nie wygasł token dostępu przed jego użyciem na potwierdzenie użytkownika lub uzyskać nowy token.|
|530021|Aplikacja nie spełnia wymagań dotyczących dostępu warunkowego zatwierdzonych aplikacji.|

## <a name="next-steps"></a>Kolejne kroki

* [Omówienie raportów dotyczących logowań](concept-sign-ins.md)
* [Programowy dostęp do raportów usługi Azure AD](concept-reporting-api.md)
