---
title: Kody błędów w raportach działań związanych z logowaniem w portalu usługi Azure Active Directory | Microsoft Docs
description: Dokumentacja dotycząca kodów błędów w raportach działań związanych z logowaniem.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 05/31/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: aab03c92bee0d3b69062cdcb179eebbb5c0fc8f8
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2018
ms.locfileid: "39160250"
---
# <a name="sign-in-activity-report-error-codes-in-the-azure-active-directory-portal"></a>Kody błędów w raportach działań związanych z logowaniem w portalu usługi Azure Active Directory

Dzięki informacjom zawartym w raporcie operacji logowania użytkowników można uzyskać odpowiedzi na pytania, takie jak:

- Kto logował się za pomocą usługi Azure Active Directory?
- Do których aplikacji logowali się użytkownicy?
- Które operacje logowania zakończyły się błędem i dlaczego?

Ten artykuł zawiera listę kodów błędów i powiązane opisy. 

## <a name="how-can-i-display-failed-sign-ins"></a>Jak mogę wyświetlić nieudane operacje logowania? 

Pierwszym punktem wejścia do wszystkich danych działań związanych z logowaniem jest pozycja **[Logowania](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)** w sekcji **Działania** usługi **Azure Active**.


![Działania związane z logowaniem](./media/active-directory-reporting-activity-sign-ins-errors/61.png "Działania związane z logowaniem")

W raporcie logowań można wyświetlić wszystkie nieudane logowania, wybierając wartość **Niepowodzenie** w obszarze **Stan logowania**.

![Działania związane z logowaniem](./media/active-directory-reporting-activity-sign-ins-errors/06.png "Działania związane z logowaniem")

Kliknięcie elementu na wyświetlonej liście powoduje otwarcie bloku **Szczegóły działań: logowania**. Ten widok zawiera wszystkie szczegóły operacji logowania śledzone przez usługę Azure Active Directory, takie jak na przykład **kod błędu logowania** i **przyczyna niepowodzenia**.

![Działania związane z logowaniem](./media/active-directory-reporting-activity-sign-ins-errors/05.png "Działania związane z logowaniem")


Aby uzyskać dostęp do danych logowania, zamiast witryny Azure Portal można również użyć [interfejsu API raportowania](active-directory-reporting-api-getting-started-azure-portal.md).


W poniższej sekcji przedstawiono kompletne omówienie wszystkich możliwych błędów i powiązane opisy. 

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
|50000|Wystąpił problem z naszą usługą logowania. [Otwórz bilet pomocy technicznej](fundamentals/active-directory-troubleshooting-support-howto.md), aby rozwiązać ten problem.|
|50001|Nie odnaleziono głównej nazwy usługi w tej dzierżawie. Może to się zdarzyć, jeśli aplikacja nie została zainstalowana przez administratora dzierżawy. Lub w katalogu nie znaleziono jednostki Zasób albo jest ona nieprawidłowa.|
|50002|Logowanie nie powiodło się z powodu ograniczonego dostępu serwera proxy w dzierżawie. Jeśli są to zasady Twojej własnej dzierżawy, możesz zmienić ustawienia ograniczonej dzierżawy, aby rozwiązać ten problem|
|50003|Logowanie nie powiodło się z powodu braku klucza lub certyfikatu podpisywania. Przyczyną może być to, że w aplikacji nie skonfigurowano żadnego klucza podpisywania. Sprawdź rozwiązania opisane tutaj: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured). Jeśli nadal występują problemy, skontaktuj się z właścicielem aplikacji lub administratorem aplikacji|
|50005|Użytkownik próbował zalogować się do urządzenia z platformy, która nie jest obecnie obsługiwana za pomocą zasad dostępu warunkowego|
|50006| Weryfikacja podpisu nie powiodła się z powodu nieprawidłowego podpisu. Sprawdź rozwiązanie opisane tutaj: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery). Jeśli nadal występują problemy, skontaktuj się z właścicielem aplikacji lub administratorem aplikacji|
|50007|Nie znaleziono certyfikatu szyfrowania partnera dla tej aplikacji. [Otwórz bilet pomocy technicznej](fundamentals/active-directory-troubleshooting-support-howto.md) dla działu pomocy technicznej firmy Microsoft, aby rozwiązać ten problem|
|50008|Brak potwierdzenia SAML lub zostało ono nieprawidłowo skonfigurowane w tokenie. Skontaktuj się z dostawcą federacji.|
|50010|Weryfikacja identyfikatora URI odbiorców dla aplikacji nie powiodła się, ponieważ nie skonfigurowano żadnych odbiorców z tokenem. Skontaktuj się z właścicielem aplikacji|
|50011|Brak adresu zwrotnego, został nieprawidłowo skonfigurowany lub jest inny niż adresy zwrotne skonfigurowane dla aplikacji. Wypróbuj rozwiązanie opisane tutaj: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application). Jeśli nadal występują problemy, skontaktuj się z właścicielem aplikacji lub administratorem aplikacji|
|50012| Jest ogólny komunikat, który wskazuje, że uwierzytelnianie nie powiodło się. Może to nastąpić powodów, takich jak brakujące lub nieprawidłowe poświadczenia lub oświadczenia w żądaniu. Upewnij się, że żądanie jest wysyłane z prawidłowymi poświadczeniami i oświadczeń. |
|50013|Potwierdzenie jest nieprawidłowe z różnych przyczyn: — wystawca tokenu nie odpowiada wersji interfejsu API w jej prawidłowym zakresie czasu, — wygasło, — jest źle sformułowane, — token odświeżania w potwierdzeniu nie jest głównym tokenem odświeżania.|
|50017|Weryfikacja certyfikacji nie powiodła się z następujących powodów:<ul><li>Nie można odnaleźć certyfikatu wystawcy na liście zaufanych certyfikatów</li><li>Nie można odnaleźć oczekiwanego elementu CrlSegment</li><li>Nie można odnaleźć certyfikatu wystawcy na liście zaufanych certyfikatów</li><li>Punkt dystrybucji różnicowej listy CRL jest skonfigurowany bez odpowiedniego punktu dystrybucji listy CRL</li><li>Nie można pobrać prawidłowych segmentów listy CRL z powodu przekroczenia limitu czasu</li><li>Nie można pobrać listy CRL</li></ul>Skontaktuj się z administratorem dzierżawy.|
|50020|Użytkownik nie ma autoryzacji dla jednego z następujących przyczyn.<ul><li>Użytkownik próbuje zalogować się za pomocą konta MSA z punktem końcowym v1</li><li>Użytkownik nie istnieje w dzierżawie.</li></ul> Skontaktuj się z właścicielem aplikacji.|
|50027|Token JWT jest nieprawidłowy z następujących przyczyn:<ul><li>nie zawiera oświadczenia nonce, oświadczenia sub</li><li>niezgodność identyfikatora podmiotu</li><li>zduplikowane oświadczenie w oświadczeniach idToken</li><li>nieoczekiwany wystawca</li><li>nieoczekiwani odbiorcy</li><li>nie mieści się w prawidłowym zakresie czasu </li><li>format tokenu nie jest prawidłowy</li><li>weryfikacja tokenu zewnętrznego identyfikatora od wystawcy nie powiodła się.</li></ul>Skontaktuj się z właścicielem aplikacji|
|50029|Nieprawidłowy identyfikator URI — nazwa domeny zawiera nieprawidłowe znaki. Skontaktuj się z administratorem dzierżawy.|
|50034|Użytkownik nie istnieje w katalogu. Skontaktuj się z administratorem dzierżawy.|
|50042|Podmiot zabezpieczeń nie ma ciągu inicjującego wymaganego do wygenerowania identyfikatora parowania. Skontaktuj się z administratorem dzierżawy.|
|50048|Podmiot jest niezgodny z oświadczeniem wystawcy w potwierdzeniu klienta. Skontaktuj się z administratorem dzierżawy.|
|50050|Żądanie jest źle sformułowane. Skontaktuj się z właścicielem aplikacji.|
|50053|Konto zostało zablokowane, ponieważ użytkownik podjął zbyt wiele prób zalogowania przy użyciu niepoprawnego identyfikatora użytkownika lub hasła.|
|50055|Nieprawidłowe hasło — wprowadzono nieważne hasło.|
|50056|Hasło jest nieprawidłowe lub ma wartość null — hasło nie istnieje w magazynie dla tego użytkownika|
|50057|Konto użytkownika zostało wyłączone. Konto zostało wyłączone przez administratora.|
|50058|Aplikacja próbowała wykonać logowanie dyskretne, ale użytkownika nie można zalogować dyskretnie. Aplikacja musi uruchomić przepływ interaktywny dający użytkownikom możliwość zalogowania się. Skontaktuj się z właścicielem aplikacji.|
|50059|Użytkownik nie istnieje w katalogu. Skontaktuj się z administratorem dzierżawy|
|50061|Żądanie wylogowania jest nieprawidłowe. Skontaktuj się z właścicielem aplikacji|
|50072|Użytkownik musi zarejestrować się na potrzeby uwierzytelniania dwuskładnikowego (interaktywnie)|
|50074|Użytkownik nie przeszedł żądania uwierzytelniania MFA.|
|50076|Użytkownik nie przeszedł żądania uwierzytelniania MFA (nieinteraktywnie)|
|50079|Użytkownik musi zarejestrować się na potrzeby uwierzytelniania dwuskładnikowego (logowania nieinterakcyjne)|
|50085|Token odświeżania wymaga danych logowania dostawy tożsamości z serwisów społecznościowych. Użytkownik musi zalogować się ponownie przy użyciu nazwy użytkownika i hasła|
|50089|Token uwierzytelniania wygasł — uwierzytelnianie nie powiodło się. Użytkownik musi zalogować się ponownie przy użyciu nazwy użytkownika i hasła|
|50097|Wymagane uwierzytelnienie urządzenia — oświadczenia DeviceId i DeviceAltSecId mają wartość null LUB nie istnieje żadne urządzenie odpowiadające identyfikatorowi urządzenia|
|50099|Podpis tokenu JWT jest nieprawidłowy. Skontaktuj się z właścicielem aplikacji.|
|50105|Zalogowany użytkownik nie jest przypisany do roli dla zalogowanej aplikacji. Przypisz użytkownika do aplikacji. Więcej informacji: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role)|
|50107|Żądany obiekt obszaru federacji nie istnieje. Skontaktuj się z administratorem dzierżawy.|
|50120|Problem z nagłówkiem JWT. Skontaktuj się z administratorem dzierżawy.|
|50124|Przekształcenie oświadczeń zawiera nieprawidłowy parametr wejściowy. Skontaktuj się z administratorem dzierżawy, aby zaktualizować zasady.|
|50125|Logowanie zostało przerwane z powodu wpisu dotyczącego resetowania lub rejestracji hasła|
|50126|Nieprawidłowa nazwa użytkownika lub hasło lub nieprawidłowy lokalnej nazwy użytkownika i hasła.|
|50127|Użytkownik musi zainstalować aplikację brokera, aby uzyskać dostęp do tej zawartości.|
|50128|Nieprawidłowa nazwa domeny — żadne informacje identyfikujące dzierżawę nie zostały znalezione w żądaniu ani nie są implikowane przez jakiekolwiek podane poświadczenia|
|50129|Urządzenie nie jest dołączone w miejscu pracy — dołączanie w miejscu pracy jest wymagane do zarejestrowania urządzenia.|
|50130|Wartości oświadczenia nie można zinterpretować jako znanej metody uwierzytelniania|
|50131|Kod używany w przypadku różnych błędów dostępu warunkowego, Na przykład Zły stan urządzenia z systemem Windows albo żądanie zablokowane z powodu podejrzanych decyzji związanych z działaniami, zasadami dostępu i zasadami zabezpieczeń.|
|50132|Poświadczenia zostały odwołane z następujących powodów:<ul><li>Artefakt logowania jednokrotnego jest nieprawidłowy lub wygasł</li><li>Sesja nie jest wystarczająco świeża dla aplikacji</li><li>Wysłano żądanie logowania dyskretnego, ale sesja użytkownika z usługą Azure AD jest nieprawidłowy lub wygasła.</li></ul>|
|50133|Sesja jest nieprawidłowa z powodu wygaśnięcia lub niedawnej zmiany hasła.|
|50135|Wymagana jest zmiana hasła ze względu na ryzyko dotyczące konta|
|50136|Przekierowanie sesji zarządzanego konta usługi do aplikacji — wykryto jedną sesję konta MSA |
|50140|Ten błąd wystąpił z powodu przerwania „Nie wylogowuj mnie” w trakcie logowania użytkownika. [Otwórz bilet pomocy technicznej](fundamentals/active-directory-troubleshooting-support-howto.md) z identyfikatorem korelacji, identyfikatorem żądania i kodem błędu, aby uzyskać więcej informacji. |
|50143|Niezgodność sesji — sesja jest nieprawidłowa, ponieważ dzierżawa użytkownika nie odpowiada wskazówce dotyczącej domeny z powodu innego zasobu. [Otwórz bilet pomocy technicznej](fundamentals/active-directory-troubleshooting-support-howto.md) z identyfikatorem korelacji, identyfikatorem żądania i kodem błędu, aby uzyskać więcej informacji.|
|50144|Ważność hasła użytkownika usługi Active Directory wygasła. Wygeneruj nowe hasło dla użytkownika lub poproś użytkownika końcowego o skorzystanie z narzędzia samoobsługowego resetowania hasła|
|50146|Ta aplikacja musi być skonfigurowana przy użyciu klucza podpisywania specyficznego dla aplikacji. Albo nie została skonfigurowana przy jego użyciu, albo klucz wygasł lub nie jest jeszcze ważny. Skontaktuj się z właścicielem aplikacji|
|50148|Wartość parametru code_verifier jest niezgodna z elementem code_challenge podanym w żądaniu autoryzacji dla PKCE. Skontaktuj się z deweloperem aplikacji. |
|50155|Uwierzytelnianie urządzenia nie powiodło się dla tego użytkownika|
|50158|Zewnętrzny test zabezpieczeń nie powiódł się|
|50161|Oświadczenia wysyłane przez zewnętrznego dostawcę nie są wystarczające lub brak oświadczenia żądanego od zewnętrznego dostawcy|
|50166|Nie można wysłać żądania do dostawcy oświadczeń|
|50169|Obszar nie jest skonfigurowanym obszarem bieżącej przestrzeni nazw usługi.|
|50172|Zewnętrzny dostawca oświadczeń nie jest zatwierdzony. Skontaktuj się z administratorem dzierżawy|
|50173|Wymagany jest świeży token uwierzytelniania. Użytkownik musi zalogować się ponownie przy użyciu świeżych poświadczeń|
|50177|Zewnętrzny test nie jest obsługiwany dla użytkowników przekazujących|
|50178|Kontrola sesji nie jest obsługiwana dla użytkowników przekazujących|
|50180|Wymagane jest zintegrowane uwierzytelnianie systemu Windows. Włącz dzierżawę dla bezproblemowego logowania jednokrotnego.|
|51001|Wskazówka dotycząca domeny nie jest obecna w lokalnym identyfikatorze zabezpieczeń — lokalna nazwa UPN|
|51004|Konto użytkownika nie istnieje w katalogu.|
|51006|Wymagane jest zintegrowane uwierzytelnianie systemu Windows. Użytkownik zalogowany przy użyciu tokenu sesji, w którym brakuje oświadczenia wia. Zażądaj od użytkownika, aby zalogował się ponownie.|
|52004|Użytkownik nie wyraził zgody na dostęp do zasobów usługi LinkedIn. |
|53000|Zasady dostępu warunkowego wymagają zgodnego urządzenia, ale urządzenie nie jest zgodne. Użytkownik musi zarejestrować swoje urządzenie przy użyciu zatwierdzonego dostawcy rozwiązania do zarządzania urządzeniami przenośnymi, na przykład usługi Intune|
|53001|Zasady dostępu warunkowego wymagają urządzenia przyłączonego do domeny, ale urządzenie nie jest przyłączone do domeny. Użytkownik musi korzystać z urządzenia przyłączonego do domeny|
|53002|Używana aplikacja nie jest zatwierdzoną aplikacją dla dostępu warunkowego. Użytkownik musi używać jednej z aplikacji z listy zatwierdzonych aplikacji w celu uzyskania dostępu.|
|53003|Dostęp został zablokowany ze względu na zasady dostępu warunkowego.|
|53004|Użytkownik musi ukończyć proces rejestracji w celu używania uwierzytelniania wieloskładnikowego przed uzyskaniem dostępu do tej zawartości. Użytkownik powinien zarejestrować się w celu uwierzytelniania wieloskładnikowego.|
|65001|Aplikacja X nie ma uprawnienia dostępu do aplikacji Y lub uprawnienie zostało odwołany. Lub użytkownik albo administrator nie wyraził zgody na używanie aplikacji z identyfikatorem X. Wyślij interaktywne żądanie autoryzacji dla tego użytkownika i zasobu. Lub użytkownik albo administrator nie wyraził zgody na używanie aplikacji z identyfikatorem X. Wyślij do administratora dzierżawy żądanie autoryzacji dotyczące działania w imieniu aplikacji Y dla zasobu Z.|
|65004|Użytkownik odmówił wyrażenia zgody na dostęp do aplikacji. Użytkownik musi ponowić próbę zalogowania się i wyrazić zgodę na aplikację|
|65005|Lista dostępu do wymaganych zasobów aplikacji nie zawiera aplikacji możliwych do wykrycia przez zasób lub aplikacja kliencka zażądała dostępu do zasobu, który nie został określony na liście dostępu do wymaganych zasobów, lub usługa programu Graph zwróciła nieprawidłowe żądanie lub nie można odnaleźć zasobu. Jeśli aplikacja obsługuje język SAML, być może aplikację skonfigurowano z nieprawidłowym identyfikatorem (jednostką). Wypróbuj rozwiązania dla języka SAML, korzystając z linku poniżej: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav#no-resource-in-requiredresourceaccess-list](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav#no-resource-in-requiredresourceaccess-list)|
|70000|Udzielenie dostępu jest nieprawidłowe z następujących przyczyn:<ul><li>Żądane potwierdzenie SAML 2.0 ma nieprawidłową metodę potwierdzenia podmiotu</li><li>Przepływ OnBehalfOf aplikacji nie jest obsługiwany w wersji 2</li><li>Podstawowy token odświeżania nie jest podpisany przy użyciu klucza sesji</li><li>Nieprawidłowy zewnętrzny token odświeżania</li><li>Udzielenie dostępu uzyskano dla innej dzierżawy.</li></ul>|
|70001|Nie można odnaleźć aplikacji o nazwie X w dzierżawie o nazwie Y. Taka sytuacja może wystąpić, jeśli aplikacja o identyfikatorze X nie została zainstalowana przez administratora dzierżawy lub żaden użytkownik w dzierżawie nie wyraził odpowiedniej zgody. Być może nieprawidłowo skonfigurowano wartość identyfikatora aplikacji lub wysyłano żądanie uwierzytelniania do niewłaściwej dzierżawy|
|70002|Aplikacja zwróciła nieprawidłowe poświadczenia klienta. Skontaktuj się z właścicielem aplikacji|
|70003|Aplikacja zwróciła typ nieobsługiwany typ udzielania. Skontaktuj się z właścicielem aplikacji|
|70004|Aplikacja zwróciła nieprawidłowy identyfikator URI przekierowania. Adres przekierowania określony przez klienta nie pasuje do żadnych skonfigurowanych adresów ani żadnych adresów na liście zatwierdzonych dostawców OIDC. Skontaktuj się z właścicielem aplikacji|
|70005|Aplikacja zwróciła nieobsługiwany typ odpowiedzi z następujących powodów:<ul><li>typ odpowiedzi „token” nie jest włączony dla aplikacji</li><li>typ odpowiedzi „id_token” wymaga zakresu OpenID — zawiera nieobsługiwaną wartość parametru OAuth w zakodowanym kanale wctx</li></ul>Skontaktuj się z właścicielem aplikacji|
|70007|Aplikacja zwróciła nieobsługiwaną wartość parametru „response_mode” podczas żądania tokenu. Skontaktuj się z właścicielem aplikacji|
|70008|Podany kod autoryzacji lub token odświeżania wygasł i został odwołany. Użytkownik powinien spróbować ponownie się zalogować|
|70011|Zakres żądany przez aplikację jest nieprawidłowy. Skontaktuj się z właścicielem aplikacji|
|70012|Wystąpił błąd serwera podczas uwierzytelniania użytkownika zarządzanego konta usługi (użytkownika). Spróbuj ponownie. Jeśli próba ponownie nie powiedzie się, [otwórz bilet pomocy technicznej](fundamentals/active-directory-troubleshooting-support-howto.md) |
|70018|Nieprawidłowy kod weryfikacyjny — użytkownik wpisał niewłaściwy kod użytkownika dla przepływu kodu urządzenia. Autoryzacja nie została zatwierdzona|
|70019|Kod weryfikacyjny wygasł. Użytkownik powinien ponowić próbę logowania|
|70037|Podano nieprawidłową odpowiedź na wezwanie. Odrzucono sesję uwierzytelniania zdalnego.|
|75001|Wystąpił błąd podczas wiązania komunikatu SAML.|
|75003|Aplikacja zwróciła błąd związany z nieobsługiwanym powiązaniem (odpowiedzi protokołu SAML nie można wysłać za pomocą powiązań innych niż żądania HTTP POST). Skontaktuj się z właścicielem aplikacji|
|75005|Usługa Azure AD nie obsługuje żądania SAML wysłanego przez aplikację na potrzeby logowania jednokrotnego. Skontaktuj się z właścicielem aplikacji|
|75008|Żądanie z aplikacji zostało odrzucone, ponieważ żądanie SAML miało nieoczekiwany obiekt docelowy. Skontaktuj się z właścicielem aplikacji|
|75011|Metoda uwierzytelniania użyta do uwierzytelnienia użytkownika w usłudze jest niezgodna z żądaną metodą uwierzytelniania. Skontaktuj się z właścicielem aplikacji|
|75016|Żądanie uwierzytelniania SAML2 ma nieprawidłowy element NameIdPolicy. Skontaktuj się z właścicielem aplikacji|
|80001|Agent uwierzytelniania nie może nawiązać połączenia z usługą Active Directory. Upewnij się, że agent uwierzytelniania jest zainstalowany na komputerze przyłączonym do domeny, który ma kontakt z kontrolerem domeny, który może obsługiwać żądania logowania użytkownika.|
|80002|Błąd wewnętrzny. Upłynął limit czasu żądania weryfikacji hasła. Nie możemy wysłać żądania uwierzytelnienia do wewnętrznej usługi tożsamości hybrydowej. [Otwórz bilet pomocy technicznej](fundamentals/active-directory-troubleshooting-support-howto.md), aby uzyskać więcej informacji na temat błędu|
|80003|Agent uwierzytelniania odebrał nieprawidłową odpowiedź. Wystąpił nieznany błąd podczas próby uwierzytelniania za pomocą lokalnej usługi Active Directory. [Otwórz bilet pomocy technicznej](fundamentals/active-directory-troubleshooting-support-howto.md), aby uzyskać więcej informacji na temat błędu.|
|80005|Agent uwierzytelniania: wystąpił nieznany błąd podczas przetwarzania odpowiedzi od agenta uwierzytelniania. [Otwórz bilet pomocy technicznej](fundamentals/active-directory-troubleshooting-support-howto.md), aby uzyskać więcej informacji na temat błędu.|
|80007|Agent uwierzytelniania nie może zweryfikować hasła użytkownika.|
|80010|Agent uwierzytelniania nie może odszyfrować hasła. |
|80011|Agent uwierzytelniania nie może pobrać klucza szyfrowania.|
|80012|Użytkownicy próbowali zalogować się poza dozwolonymi godzinami (określonymi w usłudze AD)|
|80013|Nie można ukończyć próby uwierzytelnienia z powodu niesymetryczności czasu między maszyną, na której działa agent uwierzytelniania, a usługą AD. Rozwiąż problemy z synchronizacją czasu|
|80014|Upłynął limit czasu agenta uwierzytelniania. [Otwórz bilet pomocy technicznej](fundamentals/active-directory-troubleshooting-support-howto.md) z kodem błędu, identyfikatorem korelacji oraz datą/godziną, aby uzyskać więcej informacji na temat tego błędu|
|81001|Bilet Kerberos użytkownika jest zbyt duży. Może to się zdarzyć, jeśli użytkownik jest w zbyt wielu grupach i w związku z tym bilet Kerberos zawiera zbyt wiele członkostw w grupach. Zmniejsz liczbę członkostw użytkownika w grupach i spróbuj ponownie.|
|81005|Pakiet uwierzytelniania nie jest obsługiwany|
|81007|Dzierżawa nie jest włączona dla bezproblemowego logowania jednokrotnego|
|81012|To nie jest w stanie błędu. Wskazuje ona, że tego użytkownika, próba zalogowania się do usługi Azure AD jest inny niż użytkownik zalogowany do urządzenia. Można bezpiecznie zignorować ten kod w dziennikach.|
|90010|Żądanie nie jest obsługiwane z różnych powodów. Na przykład żądanie jest wysyłane za pomocą metody nieobsługiwane żądanie (obsługiwane tylko metody POST) lub algorytm podpisywania tokenu, którego zażądano nie jest obsługiwana. Skontaktuj się z deweloperem aplikacji.|
|90014| Brak wymaganego pola komunikatu protokołu, skontaktuj się z właścicielem aplikacji. Jeśli jesteś właścicielem aplikacji, upewnij się, że wszystkie niezbędne parametry dla żądania logowania. |
|90072| Konto musi najpierw należy dodać jako użytkownik zewnętrzny w dzierżawie. Wylogować i zalogować ponownie, używając innej usługi Azure AD konta.|
|90094| Przydziel musi mieć uprawnienia administratora. Skontaktuj się z administratorem dzierżawy, zapewnienie wyrażania zgody dla tej aplikacji.|

## <a name="next-steps"></a>Kolejne kroki

Więcej informacji można znaleźć w temacie [Raporty dotyczące logowań w portalu usługi Azure Active Directory](active-directory-reporting-activity-sign-ins.md).
