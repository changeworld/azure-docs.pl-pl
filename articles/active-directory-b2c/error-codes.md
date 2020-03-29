---
title: Odwołanie do kodu błędu
titleSuffix: Azure AD B2C
description: Lista kodów błędów, które mogą być zwracane przez usługę B2C usługi Azure Active Directory.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 01/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5b09b3c19ab1c5b23e56e25afc1d9631cd1caa68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188668"
---
# <a name="error-codes-azure-active-directory-b2c"></a>Kody błędów: Usługa Azure Active Directory B2C

Następujące błędy mogą być zwracane przez usługę B2C usługi Azure Active Directory.

| Kod błędu | Komunikat |
| ---------- | ------- |
| `AADB2C90002` | Zasób CORS '{0}' zwrócił 404 nie znaleziono. |
| `AADB2C90006` | Identyfikator URI{0}przekierowania ' ' podany w żądaniu{1}nie jest zarejestrowany dla identyfikatora klienta ' . |
| `AADB2C90007` | Aplikacja skojarzona z identyfikatorem{0}klienta ' ' nie ma zarejestrowanych identyfikatorów URI przekierowania. |
| `AADB2C90008` | Żądanie nie zawiera parametru identyfikatora klienta. |
| `AADB2C90010` | Żądanie nie zawiera parametru zakresu. |
| `AADB2C90011` | Identyfikator klienta '{0}' podany w żądaniu nie{1}jest zgodny z identyfikatorem klienta ' zarejestrowanym w zasadach. |
| `AADB2C90012` | Zakres '{0}" pod warunkiem, że w żądaniu nie jest obsługiwany. |
| `AADB2C90013` | Żądany typ odpowiedzi{0}' ' ' podany w żądaniu nie jest obsługiwany. |
| `AADB2C90014` | Żądany tryb odpowiedzi{0}' ' ' podany w żądaniu nie jest obsługiwany. |
| `AADB2C90016` | Żądany typ potwierdzenia{0}klienta ' ' nie{1}jest zgodny z oczekiwanym typem '. |
| `AADB2C90017` | Twierdzenie klienta podane w żądaniu jest nieprawidłowe:{0} |
| `AADB2C90018` | Identyfikator klienta '{0}' określony w żądaniu nie{1}jest zarejestrowany w dzierżawie '. |
| `AADB2C90019` | Kontener kluczy z{0}identyfikatorem '{1}' w dzierżawie ' ' nie ma prawidłowego klucza. Powód: {2}. |
| `AADB2C90021` | Profil techniczny{0}" nie istnieje w{1}polityce "{2}najemcy "". |
| `AADB2C90022` | Nie można zwrócić metadanych{0}dla zasady{1}' ' w dzierżawie '. |
| `AADB2C90023` | Profil{0}' ' nie zawiera wymaganego{1}klucza metadanych ' . |
| `AADB2C90025` | Profil{0}' '{1}w zasadach{2}' w dzierżawie ' '{3}nie zawiera wymaganego klucza kryptograficznego ' . |
| `AADB2C90027` | Podstawowe poświadczenia określone{0}dla ' ' są nieprawidłowe. Sprawdź, czy poświadczenia są poprawne i że dostęp został przyznany przez zasób. |
| `AADB2C90028` | Certyfikat klienta określony{0}dla ' jest nieprawidłowy. Sprawdź, czy certyfikat jest poprawny, zawiera klucz prywatny i że dostęp został przyznany przez zasób. |
| `AADB2C90031` | Policy{0}' ' nie określa domyślnej podróży użytkownika. Upewnij się, że zasady lub jego rodzice określają domyślną podróż użytkownika jako część sekcji jednostki uzależniona. |
| `AADB2C90035` | Usługa jest tymczasowo niedostępna. Ponów próbę po kilku minutach. |
| `AADB2C90036` | Żądanie nie zawiera identyfikatora URI, aby przekierować użytkownika do post wylogowania. Określ identyfikator URI w polu parametru post_logout_redirect_uri. |
| `AADB2C90037` | Wystąpił błąd podczas przetwarzania żądania. Skontaktuj się z administratorem witryny, do której próbujesz uzyskać dostęp. |
| `AADB2C90039` | Żądanie zawiera potwierdzenie klienta, ale pod{0}warunkiem,{1}że zasady " w dzierżawie ' ' brakuje client_secret w RelyingPartyPolicy. |
| `AADB2C90040` | Podróż użytkownika{0}' ' nie zawiera kroku oświadczeń wysyłania. |
| `AADB2C90043` | Monit zawarty w żądaniu zawiera nieprawidłowe wartości. Oczekiwano "brak", "login", "zgoda" lub "select_account". |
| `AADB2C90044` | Oświadczenie "{0}" nie jest poparte{1}przez suwnia roszczenia " ". |
| `AADB2C90046` | Mamy problemy z załadowaniem bieżącego stanu. Możesz spróbować rozpocząć sesję od początku. |
| `AADB2C90047` | Zasób{0}' ' zawiera błędy skryptu uniemożliwiające jego załadowanie. |
| `AADB2C90048` | Wystąpił nieobsługiowany wyjątek na serwerze. |
| `AADB2C90051` | Nie znaleziono odpowiednich dostawców oświadczeń. |
| `AADB2C90052` | Nieprawidłowa nazwa użytkownika lub hasło. |
| `AADB2C90053` | Nie można odnaleźć użytkownika z określonymi poświadczeniami. |
| `AADB2C90054` | Nieprawidłowa nazwa użytkownika lub hasło. |
| `AADB2C90055` | Zakres '{0}' pod warunkiem, że whttps://example.com/calendar.readżądaniu musi określić zasób, takich jak ' " . |
| `AADB2C90057` | Podana aplikacja nie jest skonfigurowana tak, aby zezwalała na przepływ niejawny OAuth. |
| `AADB2C90058` | Podana aplikacja nie jest skonfigurowana tak, aby zezwalać na klientów publicznych. |
| `AADB2C90067` | Przekierowanie URI przekierowania{0}wylogowania po wylogowaniu ma nieprawidłowy format. Określ adres URL opartyhttps://example.com/returnna https, taki jak ' lub dla klientów natywnych, użyj identyfikatora URI natywnego klienta IETF 'urn:ietf:wg:oauth:2.0:oob'. |
| `AADB2C90068` | Dostarczona aplikacja o{0}identyfikatorze ' ' nie jest prawidłowa w odniesieniu do tej usługi. Użyj aplikacji utworzonej za pośrednictwem portalu B2C i spróbuj ponownie. |
| `AADB2C90075` | Wymiana oświadczeń{0}' ' określony{1}w kroku ' '{2}zwróciła odpowiedź{3}na błąd HTTP z Code ' ' i Reason '. |
| `AADB2C90077` | Użytkownik nie ma istniejącej sesji i parametr monitu żądania ma wartość '{0}'. |
| `AADB2C90079` | Klienci muszą wysłać client_secret podczas realizacji poufnej dotacji. |
| `AADB2C90080` | Udzielona dotacja wygasła. Ponownie uwierzytelnij się i spróbuj ponownie. Aktualny czas: {0}, Grant {1}wydany czas: , Grant {2}przesuwne okno czas wygaśnięcia: . |
| `AADB2C90081` | Określony client_secret nie odpowiada oczekiwanej wartości dla tego klienta. Popraw client_secret i spróbuj ponownie. |
| `AADB2C90083` | W żądaniu brakuje wymaganego parametru: {0}. |
| `AADB2C90084` | Klienci publiczni nie powinni wysyłać client_secret przy realizacji publicznie nabytej dotacji. |
| `AADB2C90085` | Usługa napotkała błąd wewnętrzny. Proszę ponownie uwierzytelnić i spróbuj ponownie. |
| `AADB2C90086` | Dostarczone grant_type [ ]{0}nie są obsługiwane. |
| `AADB2C90087` | Pod warunkiem dotacji nie została wydana dla tej wersji punktu końcowego protokołu. |
| `AADB2C90088` | Przewidziana dotacja nie została wydana dla tego punktu końcowego. Wartość rzeczywista : {0} i wartość oczekiwana:{1} |
| `AADB2C90092` | Dostarczona aplikacja o{0}identyfikatorze ' jest{1}wyłączona dla dzierżawcy '. Włącz aplikację i spróbuj ponownie. |
| `AADB2C90107` | Aplikacja o identyfikatorze{0}' ' nie może uzyskać tokenu identyfikatora, ponieważ zakres openid nie został podany w żądaniu lub aplikacja nie jest dla niego autoryzowana. |
| `AADB2C90108` | Krok aranżacji '{0}' nie określa CpimIssuerTechnicalProfileReferenceId, gdy oczekiwano. |
| `AADB2C90110` | Parametr zakresu musi zawierać "openid" podczas żądania response_type, który zawiera "id_token". |
| `AADB2C90111` | Twoje konto zostało zablokowane. Skontaktuj się z pomocą techniczną, aby ją odblokować, a następnie spróbuj ponownie. |
| `AADB2C90114` | Twoje konto jest tymczasowo zablokowane, aby zapobiec nieautoryzowanemu użyciu. Spróbuj ponownie później. |
| `AADB2C90115` | Żądając response_type "kodu", parametr zakresu musi zawierać identyfikator zasobu lub klienta dla tokenów dostępu i "openid" dla tokenów identyfikatorów. Dodatkowo dołącz "offline_access" dla tokenów odświeżania. |
| `AADB2C90117` | Zakres '{0}' pod warunkiem, że w żądaniu nie jest obsługiwany. |
| `AADB2C90118` | Użytkownik zapomniał hasła. |
| `AADB2C90120` | Parametr max age{0}' ' określony w żądaniu jest nieprawidłowy. Maksymalny wiek musi być liczeniem całkowitym między '{1}' i '{2}włącznie. |
| `AADB2C90122` | Dane wejściowe dla '{0}' odebrane w żądaniu nie powiodło się sprawdzanie poprawności żądania HTTP. Upewnij się, że dane wejściowe nie zawierają znaków, takich jak < lub &. |
| `AADB2C90128` | Konto skojarzone z tą dotacją już nie istnieje. Proszę ponownie uwierzytelnić i spróbuj ponownie. |
| `AADB2C90129` | Udzielona dotacja została cofnięta. Proszę ponownie uwierzytelnić i spróbuj ponownie. |
| `AADB2C90145` | Nie znaleziono niezweryfikowanych numerów telefonów, a zasady nie zezwalają użytkownikowi na wprowadzenie numeru. |
| `AADB2C90146` | Zakres '{0}' pod warunkiem, że w żądaniu określa więcej niż jeden zasób dla tokenu dostępu, który nie jest obsługiwany. |
| `AADB2C90149` | Nie{0}można załadować skryptu ' '. |
| `AADB2C90151` | Użytkownik przekroczył maksymalną liczbę ponownych prób uwierzytelniania wieloskładnikowego. |
| `AADB2C90152` | Żądanie ankiety wieloskładnikowej nie może uzyskać odpowiedzi od usługi. |
| `AADB2C90154` | Żądanie weryfikacji wieloskładnikowej nie może uzyskać identyfikatora sesji z usługi. |
| `AADB2C90155` | Wniosek o weryfikację wieloskładnikową nie powiódł się z powodu "{0}". |
| `AADB2C90156` | Żądanie weryfikacji wieloczynnikowej nie powiodło się z powodu '{0}. |
| `AADB2C90157` | Użytkownik przekroczył maksymalną liczbę ponownych prób dla kroku samodzielnie potwierdzonego. |
| `AADB2C90158` | Samodzielne żądanie sprawdzania poprawności nie powiodło{0}się z powodu ' . |
| `AADB2C90159` | Samodzielnie twierdził, że wniosek o weryfikację{0}nie powiodło się z powodu ". |
| `AADB2C90161` | Samotwierdzona odpowiedź na wysłanie nie{0}powiodła się z powodu ' " . |
| `AADB2C90165` | Nie można odnaleźć{0}komunikatu inicjującego SAML o identyfikatorze " w stanie. |
| `AADB2C90168` | Żądanie przekierowania HTTP nie zawiera wymaganego{0}parametru ' ' dla podpisanego żądania. |
| `AADB2C90178` | Certyfikat podpisywania '{0}' nie ma klucza prywatnego. |
| `AADB2C90182` | Dostarczone code_verifier nie jest zgodne ze skojarzonymi code_challenge |
| `AADB2C90183` | Dostarczony code_verifier jest nieprawidłowy |
| `AADB2C90184` | Dostarczone code_challenge_method nie są obsługiwane. Obsługiwane wartości są gładkie lub S256 |
| `AADB2C90188` | Profil techniczny SAML{0}' ' określa adres URL{1}programu PartnerEntity ' , ale{2}pobieranie metadanych kończy się niepowodzeniem z powodu '. |
| `AADB2C90194` | Claim{0}' ' określony dla tokenu na okaziciela nie jest obecny w dostępnych roszczeniach. Dostępne roszczenia{1}" ". |
| `AADB2C90205` | Ta aplikacja nie ma wystarczających uprawnień do tego zasobu sieci web do wykonania operacji. |
| `AADB2C90206` | Wystąpił limit czasu inicjowania klienta. |
| `AADB2C90208` | Podany parametr id_token_hint wygasł. Podaj kolejny token i spróbuj ponownie. |
| `AADB2C90209` | Podany parametr id_token_hint nie zawiera zaakceptowanej grupy odbiorców. Prawidłowe wartości{0}odbiorców: ' '. Podaj kolejny token i spróbuj ponownie. |
| `AADB2C90210` | Nie można zweryfikować podanego parametru id_token_hint. Podaj kolejny token i spróbuj ponownie. |
| `AADB2C90211` | Żądanie zawierało niekompletny plik cookie stanu. |
| `AADB2C90212` | Żądanie zawierało nieprawidłowy plik cookie stanu. |
| `AADB2C90220` | Kontener kluczy w{0}dzierżawie '{1}' z identyfikatorem magazynu ' istnieje, ale nie zawiera prawidłowego certyfikatu. Certyfikat może wygasnąć lub certyfikat może stać się aktywny w przyszłości (nbf). |
| `AADB2C90223` | Wystąpił błąd dezynfekowania zasobu CORS. |
| `AADB2C90224` | Przepływ właściciela zasobu nie został włączony dla aplikacji. |
| `AADB2C90225` | Nazwa użytkownika lub hasło podane w żądaniu są nieprawidłowe. |
| `AADB2C90226` | Określona wymiana tokenów jest obsługiwana tylko za pośrednictwem protokołu HTTP POST. |
| `AADB2C90232` | Podany parametr id_token_hint nie zawiera akceptowanego wystawcy. Prawidłowi emitenci: '{0}". Podaj kolejny token i spróbuj ponownie. |
| `AADB2C90233` | Podany parametr id_token_hint nie powiódł sprawdzania poprawności podpisu. Podaj kolejny token i spróbuj ponownie. |
| `AADB2C90235` | Pod warunkiem, id_token wygasa. Podaj kolejny token i spróbuj ponownie. |
| `AADB2C90237` | Podane id_token nie zawiera prawidłowej grupy odbiorców. Prawidłowe wartości{0}odbiorców: ' '. Podaj kolejny token i spróbuj ponownie. |
| `AADB2C90238` | Podane id_token nie zawiera prawidłowego wystawcy. Prawidłowe wartości{0}wystawcy: ' '. Podaj kolejny token i spróbuj ponownie. |
| `AADB2C90239` | Dostarczone id_token nie powiodło się sprawdzanie poprawności podpisu. Podaj kolejny token i spróbuj ponownie. |
| `AADB2C90240` | Dostarczone id_token są zniekształcone i nie mogą być analizowane. Podaj kolejny token i spróbuj ponownie. |
| `AADB2C90242` | Profil techniczny SAML{0}' ' określa program PartnerEntity CDATA, którego nie można załadować z powodu '.{1} |
| `AADB2C90243` | Klucz/klucz tajny klienta IDP nie jest poprawnie skonfigurowany. |
| `AADB2C90244` | W tej chwili jest zbyt wiele żądań. Poczekaj chwilę i spróbuj ponownie. |
| `AADB2C90248` | Przepływ właściciela zasobów może być używany tylko przez aplikacje utworzone za pośrednictwem portalu administracyjnego B2C. |
| `AADB2C90250` | Ogólny punkt końcowy logowania nie jest obsługiwany. |
| `AADB2C90255` | Wymiana roszczeń określona w{0}profilu technicznym " nie została zakończona zgodnie z oczekiwaniami. Możesz spróbować rozpocząć sesję od początku. |
| `AADB2C90261` | Wymiana oświadczeń{0}' ' określony{1}w kroku ' ' zwróciła odpowiedź błędu HTTP, która nie może być przeanalizowana. |
| `AADB2C90272` | Parametr id_token_hint nie został określony w żądaniu. Podaj token i spróbuj ponownie. |
| `AADB2C90273` | Otrzymano nieprawidłową odpowiedź: "{0}" |
| `AADB2C90274` | Metadane dostawcy nie określają jednej usługi wylogowywania lub powiązanie punktu końcowego nie jest jednym z "urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" lub "urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST". |
| `AADB2C90276` | Żądanie nie jest zgodne z{0}ustawieniem{1}kontroli " ":{2}' "{3}w{4}technicalProfile ' dla polisy ' ' najemcy '. |
| `AADB2C90277` | Krok aranżacji '{0}z{1}journey użytkownika{2}' of policy ' ' ' nie zawiera odwołania do definicji zawartości. |
| `AADB2C90279` | Podany identyfikator klienta{0}' ' nie jest zgodny z identyfikatorem klienta, który wydał dotację. |
| `AADB2C90284` | Aplikacja o identyfikatorze{0}" nie uzyskała zgody i nie może być używana dla kont lokalnych. |
| `AADB2C90285` | Nie znaleziono aplikacji{0}o identyfikatorze ' . |
| `AADB2C90288` | UserJourney with id '{0}' referenced{1}in TechnicalProfile '{2}for refresh token{3}redemption for tenant ' ' nie istnieje w polityce ' lub żadnej z jego podstawowych zasad. |
| `AADB2C90289` | Napotkaliśmy błąd podczas łączenia się z dostawcą tożsamości. Spróbuj ponownie później. |
| `AADB2C90296` | Aplikacja nie została poprawnie skonfigurowana. Skontaktuj się z administratorem witryny, do której próbujesz uzyskać dostęp. |
| `AADB2C99005` | Żądanie zawiera nieprawidłowy parametr zakresu, który{0}zawiera niedozwolony znak ' ' . |
| `AADB2C99006` | Usługa Azure AD B2C nie może znaleźć{0}aplikacji rozszerzeń o identyfikatorze aplikacji ' . Więcej https://go.microsoft.com/fwlink/?linkid=851224 informacji można znaleźć na stronie. |
| `AADB2C99011` | Wartość metadanych{0}' ' nie została określona w TechnicalProfile '{1}' w zasadach '.{2} |
| `AADB2C99013` | Dostarczone połączenie grant_type [{0}] i token_type{1}[ ] nie jest obsługiwane. |
| `AADB2C99015` | Profile{0}' '{1}w zasadach{2}' w dzierżawie ' ' brakuje wszystkich inputClaims wymaganych do przepływu poświadczeń hasła właściciela zasobu. |
