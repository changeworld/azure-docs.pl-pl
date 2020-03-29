---
title: Metody uwierzytelniania — usługa Azure Active Directory
description: Metody uwierzytelniania dostępne w usłudze Azure AD dla usługi MFA i sspr
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry, michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5a82c69575e82a7cf397955f08c3f114e449ba6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968770"
---
# <a name="what-are-authentication-methods"></a>Co to są metody uwierzytelniania?

Jako administrator, wybierając metody uwierzytelniania azure wieloskładnikowego i samoobsługowego resetowania hasła (SSPR) zaleca się, aby wymagać od użytkowników, aby zarejestrować wiele metod uwierzytelniania. Jeśli metoda uwierzytelniania nie jest dostępna dla użytkownika, mogą wybrać uwierzytelnienie przy użyciu innej metody.

Administratorzy mogą definiować w zasadach, które metody uwierzytelniania są dostępne dla użytkowników sspr i usługi MFA. Niektóre metody uwierzytelniania mogą nie być dostępne dla wszystkich funkcji. Aby uzyskać więcej informacji na temat konfigurowania zasad, zobacz artykuły [Jak pomyślnie wdrożyć samoobsługowe resetowanie haseł](howto-sspr-deployment.md) i planowanie [uwierzytelniania wieloskładnikowego platformy Azure w chmurze](howto-mfa-getstarted.md)

Firma Microsoft zdecydowanie zaleca administratorom umożliwienie użytkownikom wybrania więcej niż minimalna wymagana liczba metod uwierzytelniania w przypadku, gdy nie mają dostępu do nich.

|Metoda uwierzytelniania|Sposób użycia|
| --- | --- |
| Hasło | Mfa i SSPR |
| Pytania zabezpieczające | Tylko SSPR |
| Adres e-mail | Tylko SSPR |
| Aplikacja Microsoft Authenticator | Mfa i SSPR |
| TOKEN SPRZĘTOWY OATH | Publiczna wersja zapoznawcza usługi MFA i sspr |
| SMS | Mfa i SSPR |
| Połączenie głosowe | Mfa i SSPR |
| Hasła aplikacji | Pomoc makrofinansową tylko w niektórych przypadkach |

![Metody uwierzytelniania używane na ekranie logowania](media/concept-authentication-methods/overview-login.png)

|     |
| --- |
| Tokeny sprzętowe OATH dla usługi MFA i SSPR są publicznymi funkcjami podglądu usługi Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawców, zobacz [Dodatkowe warunki użytkowania w wersji Zapoznawczej platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="password"></a>Hasło

Hasło usługi Azure AD jest uważane za metodę uwierzytelniania. Jest to jedna metoda, która **nie może być wyłączona**.

## <a name="security-questions"></a>Pytania zabezpieczające

Pytania zabezpieczające są dostępne **tylko w samoobsługowym resetowanie hasła usługi Azure AD** do kont niebędących administratorami.

Jeśli używasz pytań zabezpieczających, zalecamy używanie ich w połączeniu z inną metodą. Pytania zabezpieczające mogą być mniej bezpieczne niż inne metody, ponieważ niektóre osoby mogą znać odpowiedzi na pytania innego użytkownika.

> [!NOTE]
> Pytania zabezpieczające są przechowywane prywatnie i bezpiecznie na obiekcie użytkownika w katalogu i mogą być odbierane tylko przez użytkowników podczas rejestracji. Administrator nie może czytać ani modyfikować pytań lub odpowiedzi użytkownika.
>

### <a name="predefined-questions"></a>Wstępnie zdefiniowane pytania

* W jakim mieście spotkałeś swojego pierwszego małżonka / partnera?
* W jakim mieście spotkali się twoi rodzice?
* W jakim mieście mieszka twoje najbliższe rodzeństwo?
* W jakim mieście urodził się twój ojciec?
* W jakim mieście była Twoja pierwsza praca?
* W jakim mieście urodziła się twoja matka?
* W jakim mieście byłeś w Nowy Rok 2000?
* Jakie jest nazwisko twojego ulubionego nauczyciela w liceum?
* Jak nazywa się uczelnia, do której się ubiegałeś, ale nie uczestniczyłeś?
* Jak nazywa się miejsce, w którym odbyło się pierwsze przyjęcie weselne?
* Jakie jest drugie imię twojego ojca?
* Jakie jest Twoje ulubione jedzenie?
* Jakie jest imię i nazwisko twojej babci matki?
* Jakie jest drugie imię twojej matki?
* Jaki jest miesiąc i rok urodzin twojego najstarszego rodzeństwa? (np. listopad 1985 r.)
* Jakie jest drugie imię twojego najstarszego rodzeństwa?
* Jakie jest imię i nazwisko twojego dziadka?
* Jakie jest drugie imię twojego najmłodszego rodzeństwa?
* Do jakiej szkoły uczęszczałeś w szóstej klasie?
* Jakie było imię i nazwisko twojego najlepszego przyjaciela z dzieciństwa?
* Jakie było imię i nazwisko twojego pierwszego znaczącego innego?
* Jakie było nazwisko twojego ulubionego nauczyciela w szkole średniej?
* Jaka była tasza i model twojego pierwszego samochodu lub motocykla?
* Jak nazywała się pierwsza szkoła, do której uczęszczałeś?
* Jak nazywał się szpital, w którym się urodziłeś?
* Jak nazywała się ulica twojego pierwszego domu z dzieciństwa?
* Jak nazywał się twój bohater z dzieciństwa?
* Jak nazywał się Twój ulubiony wypchane zwierzę?
* Jak nazywał się Twój pierwszy zwierzak?
* Jaki był Twój pseudonim z dzieciństwa?
* Jaki był Twój ulubiony sport w szkole średniej?
* Jaka była Twoja pierwsza praca?
* Jakie były ostatnie cztery cyfry twojego numeru telefonu z dzieciństwa?
* Kiedy byłeś młody, czym chciałeś być, kiedy dorastałeś?
* Kto jest najbardziej znaną osobą, jaką kiedykolwiek spotkałeś?

Wszystkie wstępnie zdefiniowane pytania zabezpieczające są tłumaczone i zlokalizowane na pełny zestaw języków usługi Office 365 na podstawie ustawień regionalnych przeglądarki użytkownika.

### <a name="custom-security-questions"></a>Niestandardowe pytania zabezpieczające

Niestandardowe pytania zabezpieczające nie są zlokalizowane. Wszystkie pytania niestandardowe są wyświetlane w tym samym języku, w jakim są wprowadzane w interfejsie użytkownika administracyjnego, nawet jeśli ustawienia regionalne przeglądarki użytkownika są różne. Jeśli potrzebujesz zlokalizowanych pytań, należy użyć wstępnie zdefiniowanych pytań.

Maksymalna długość niestandardowego pytania zabezpieczającego wynosi 200 znaków.

### <a name="security-question-requirements"></a>Wymagania dotyczące pytań bezpieczeństwa

* Minimalny limit znaków odpowiedzi to trzy znaki.
* Maksymalny limit znaków odpowiedzi wynosi 40 znaków.
* Użytkownicy nie mogą odpowiedzieć na to samo pytanie więcej niż jeden raz.
* Użytkownicy nie mogą udzielić tej samej odpowiedzi na więcej niż jedno pytanie.
* Do definiowania pytań i odpowiedzi, w tym znaków Unicode, można użyć dowolnego zestawu znaków.
* Zdefiniowana liczba pytań musi być większa lub równa liczbie pytań, które były wymagane do rejestracji.

## <a name="email-address"></a>Adres e-mail

Adres e-mail jest dostępny **tylko w samoobsługowym resetowaniem hasła usługi Azure AD.**

Firma Microsoft zaleca korzystanie z konta e-mail, które nie wymagałoby dostępu do hasła usługi Azure AD użytkownika.

## <a name="microsoft-authenticator-app"></a>Aplikacja Microsoft Authenticator

Aplikacja Microsoft Authenticator zapewnia dodatkowy poziom zabezpieczeń konta służbowego usługi Azure AD lub konta Microsoft.

Aplikacja Microsoft Authenticator jest dostępna dla systemów [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) i [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6).

> [!NOTE]
> Użytkownicy nie będą mieli możliwości zarejestrowania swojej aplikacji mobilnej podczas rejestracji w celu samodzielnego resetowania hasła. Zamiast tego użytkownicy mogą zarejestrować swoją aplikację mobilną w [https://aka.ms/mfasetup](https://aka.ms/mfasetup) podglądzie rejestracji informacji zabezpieczających w [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo)programie .
>

### <a name="notification-through-mobile-app"></a>Powiadomienie za pośrednictwem aplikacji mobilnej

Aplikacja Microsoft Authenticator może zapobiegać nieautoryzowanemu dostępowi do kont i zatrzymywać oszukańcze transakcje, przesuwając powiadomienie na smartfon lub tablet. Użytkownicy wyświetlą powiadomienie, a jeśli jest ono zgodne z prawem, wybierz pozycję Sprawdź. W przeciwnym razie mogą wybrać Odmów.

> [!WARNING]
> W przypadku samoobsługowego resetowania hasła, gdy do zresetowania wymagana jest tylko jedna metoda, kod weryfikacyjny jest jedyną opcją dostępną dla użytkowników **w celu zapewnienia najwyższego poziomu bezpieczeństwa.**
>
> Gdy dwie metody są wymagane użytkownicy będą mogli zresetować **za** pomocą powiadomienia **lub** kodu weryfikacyjnego oprócz innych włączonych metod.
>

Jeśli włączysz korzystanie z powiadomień za pośrednictwem aplikacji mobilnej i kodu weryfikacyjnego z aplikacji mobilnej, użytkownicy, którzy zarejestrują aplikację Microsoft Authenticator za pomocą powiadomienia, będą mogli używać zarówno powiadomień, jak i kodu do weryfikacji swojej tożsamości.

> [!NOTE]
> Jeśli twoja organizacja ma pracowników pracujących w Chinach lub podróżujących do Chin, metoda **powiadamiania za pośrednictwem aplikacji mobilnej** na urządzeniach z **Androidem** nie działa w tym kraju. Alternatywne metody powinny być dostępne dla tych użytkowników.

### <a name="verification-code-from-mobile-app"></a>Kod weryfikacyjny z aplikacji mobilnej

Aplikacja Microsoft Authenticator lub inne aplikacje innych firm mogą być używane jako token oprogramowania do generowania kodu weryfikacyjnego OATH. Po wprowadzeniu nazwy użytkownika i hasła wprowadź kod podany przez aplikację na ekranie logowania. Kod weryfikacyjny stanowi drugą formę uwierzytelniania.

> [!WARNING]
> W przypadku samoobsługowego resetowania hasła, gdy tylko jedna metoda jest wymagana do resetowania kodu weryfikacyjnego, jest jedyną opcją dostępną dla użytkowników **w celu zapewnienia najwyższego poziomu bezpieczeństwa.**
>

Użytkownicy mogą mieć kombinację maksymalnie pięciu tokenów sprzętowych OATH lub aplikacji uwierzytelniających, takich jak aplikacja Microsoft Authenticator skonfigurowana do użycia w dowolnym momencie.

## <a name="oath-hardware-tokens-public-preview"></a>Tokeny sprzętowe OATH (publiczna wersja zapoznawcza)

OATH to otwarty standard określający sposób generowania kodów haseł jednorazowych (OTP). Usługa Azure AD będzie obsługiwać użycie tokenów OATH-TOTP SHA-1 odmiany 30-sekundowej lub 60-sekundowej. Klienci mogą zaopatrywać się w te tokeny od wybranego dostawcy. Klucze tajne są ograniczone do 128 znaków, które mogą nie być zgodne ze wszystkimi tokenami. Klucz tajny może zawierać tylko znaki *a-z* lub *A-Z* i cyfry *1-7*i muszą być zakodowane w base32.

![Przesyłanie tokenów OATH do bloku tokenów MFA OATH](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

Tokeny sprzętowe OATH są obsługiwane w ramach publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat wersji zapoznawców, zobacz [Dodatkowe warunki użytkowania w wersji Zapoznawczej platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

Po zdobyciu tokenów muszą zostać przesłane w formacie pliku wartości oddzielonych przecinkami (CSV), w tym nazwy UPN, numeru seryjnego, klucza tajnego, przedziału czasu, producenta i modelu, jak pokazano w poniższym przykładzie:

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567abcdef1234567abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> Upewnij się, że wiersz nagłówka jest dołączany do pliku CSV.

Po prawidłowym sformatowaniu jako plik CSV administrator może zalogować się do witryny Azure portal, przejść do**tokenów OATH**usługi **Azure Active Directory** > **Security** > **SECURITY MFA** > i przekazać wynikowy plik CSV.

W zależności od rozmiaru pliku CSV przetworzenie może potrwać kilka minut. Kliknij przycisk **Odśwież,** aby uzyskać bieżący stan. Jeśli w pliku występują błędy, będziesz mieć możliwość pobrania pliku CSV zawierającego listę błędów do rozwiązania. Nazwy pól w pobranym pliku CSV różnią się od przekazanych wersji.

Po usunięciu błędów administrator może aktywować każdy klawisz, klikając **przycisk Aktywuj,** aby token został aktywowany i wprowadzając otp wyświetlany na tokenie.

Użytkownicy mogą mieć kombinację maksymalnie pięciu tokenów sprzętowych OATH lub aplikacji uwierzytelniających, takich jak aplikacja Microsoft Authenticator skonfigurowana do użycia w dowolnym momencie.

## <a name="phone-options"></a>Opcje telefonu

### <a name="mobile-phone"></a>Telefon komórkowy

Dwie opcje są dostępne dla użytkowników telefonów komórkowych.

Jeśli użytkownicy nie chcą, aby ich numer telefonu komórkowego był widoczny w katalogu, ale nadal chcą go używać do resetowania hasła, administratorzy nie powinni wypełniać go w katalogu. Użytkownicy powinni wypełnić swój atrybut **telefon uwierzytelniania** za pośrednictwem [portalu rejestracji resetowania hasła](https://aka.ms/ssprsetup). Administratorzy mogą zobaczyć te informacje w profilu użytkownika, ale nie są publikowane w innym miejscu.

Aby działać poprawnie, numery telefonów muszą być w formacie *+CountryCode PhoneNumber*, na przykład +1 4255551234.

> [!NOTE]
> Między kodem kraju a numerem telefonu musi być miejsce.
>
> Resetowanie hasła nie obsługuje rozszerzeń telefonu. Nawet w formacie +1 4255551234X12345 rozszerzenia są usuwane przed najechaniem połączenia.

Firma Microsoft nie gwarantuje spójnego dostarczania przez program SMS lub voice-based Multi-Factor Authentication za pomocą tego samego numeru. W interesie naszych użytkowników firma Microsoft może w dowolnym momencie dodawać lub usuwać krótkie kody, gdy dokonujemy zmian tras w celu poprawy dostarczania wiadomości SMS. Firma Microsoft nie obsługuje krótkich kodów dla krajów/regionów poza Stanami Zjednoczonymi i Kanadą.

#### <a name="text-message"></a>Wiadomość SMS

Na numer telefonu komórkowego wysyłany jest sms zawierający kod weryfikacyjny. Wprowadź kod weryfikacyjny podany w interfejsie logowania, aby kontynuować.

#### <a name="phone-call"></a>Połączenie telefoniczne

Na numer telefonu, który podajesz, nawiąkczysz automatyczny telefon głosowy. Odbieranie połączenia i naciśnięcie klawisza # na klawiaturze telefonu w celu uwierzytelnienia

> [!IMPORTANT]
> Począwszy od marca 2019 r. opcje połączeń telefonicznych nie będą dostępne dla użytkowników usługi MFA i SSPR w bezpłatnych/próbnych dzierżawach usługi Azure AD. Zmiana ta nie ma wpływu na wiadomości SMS. Połączenie telefoniczne będzie nadal dostępne dla użytkowników płatnych dzierżaw usługi Azure AD. Ta zmiana ma wpływ tylko na bezpłatnych/testowych dzierżaw usług Azure AD.

### <a name="office-phone"></a>Telefon służbowy

Na numer telefonu, który podajesz, nawiąkczysz automatyczny telefon głosowy. Odbierz połączenie i naciśnij # na klawiaturze telefonu, aby uwierzytelnić.

Aby działać poprawnie, numery telefonów muszą być w formacie *+CountryCode PhoneNumber*, na przykład +1 4255551234.

Atrybutem telefonu biurowego zarządza administrator.

> [!IMPORTANT]
> Począwszy od marca 2019 r. opcje połączeń telefonicznych nie będą dostępne dla użytkowników usługi MFA i SSPR w bezpłatnych/próbnych dzierżawach usługi Azure AD. Zmiana ta nie ma wpływu na wiadomości SMS. Połączenie telefoniczne będzie nadal dostępne dla użytkowników płatnych dzierżaw usługi Azure AD. Ta zmiana ma wpływ tylko na bezpłatnych/testowych dzierżaw usług Azure AD.

> [!NOTE]
> Między kodem kraju a numerem telefonu musi być miejsce.
>
> Resetowanie hasła nie obsługuje rozszerzeń telefonu. Nawet w formacie +1 4255551234X12345 rozszerzenia są usuwane przed najechaniem połączenia.

### <a name="troubleshooting-phone-options"></a>Rozwiązywanie problemów z opcjami telefonu

Typowe problemy związane z metodami uwierzytelniania przy użyciu numeru telefonu:

* Zablokowany identyfikator dzwoniącego na jednym urządzeniu
   * Rozwiązywanie problemów z urządzeniem
* Nieprawidłowy numer telefonu, nieprawidłowy kod kraju, numer telefonu domowego w porównaniu z służbowym numerem telefonu
   * Rozwiązywanie problemów z obiektem użytkownika i skonfigurowane metody uwierzytelniania. Upewnij się, że zarejestrowane są prawidłowe numery telefonów.
* Wprowadzony nieprawidłowy kod PIN
   * Potwierdź, że użytkownik użył poprawnego kodu PIN zarejestrowanego na serwerze usługi Azure MFA Server.
* Połączenie przekazane na pocztę głosową
   * Upewnij się, że użytkownik ma włączony telefon i że usługa jest dostępna w ich obszarze lub użyć alternatywnej metody.
* Użytkownik jest zablokowany
   * Niech administrator odblokuje użytkownika w witrynie Azure portal.
* Sms nie jest subskrybowany na urządzeniu
   * Niech użytkownik zmieni metody lub aktywuj SMS na urządzeniu.
* Wadliwi dostawcy usług telekomunikacyjnych (nie wykryto wejścia telefonicznego, brakujące problemy z dźwiękami DTMF, zablokowany identyfikator dzwoniącego na wielu urządzeniach lub zablokowany SMS na wielu urządzeniach)
   * Firma Microsoft używa wielu dostawców usług telekomunikacyjnych do kierowania połączeń telefonicznych i wiadomości SMS do uwierzytelniania. Jeśli widzisz którykolwiek z powyższych problemów użytkownik próbuje użyć tej metody co najmniej 5 razy w ciągu 5 minut i mieć informacje tego użytkownika dostępne podczas kontaktowania się z pomocą techniczną firmy Microsoft.

## <a name="app-passwords"></a>Hasła aplikacji

Niektóre aplikacje inne niż przeglądarki nie obsługują uwierzytelniania wieloskładnikowego, jeśli użytkownik został włączony do uwierzytelniania wieloskładnikowego i próbuje użyć aplikacji innych niż przeglądarka, nie może uwierzytelnić. Hasło aplikacji umożliwia użytkownikom dalsze uwierzytelnianie

Jeśli wymusisz uwierzytelnianie wieloskładnikowe za pomocą zasad dostępu warunkowego, a nie za pośrednictwem usługi MFA dla użytkowników, nie można tworzyć haseł aplikacji. Aplikacje, które używają zasad dostępu warunkowego do kontrolowania dostępu, nie potrzebują haseł aplikacji.

Jeśli twoja organizacja jest sfederowana dla logowania typu SSO z usługą Azure AD i zamierzasz używać usługi Azure MFA, należy pamiętać o następujących szczegółach:

* Hasło aplikacji jest weryfikowane przez usługę Azure AD i w związku z tym omija federację. Federacja jest używana tylko podczas konfigurowania haseł aplikacji. W przypadku użytkowników federacyjnych (SSO) hasła są przechowywane w identyfikatorze organizacyjnym. Jeśli użytkownik opuści firmę, te informacje muszą przepływać do identyfikatora organizacji przy użyciu programu DirSync. Synchronizacja/usuwanie konta może potrwać do trzech godzin, co opóźnia wyłączenie/usunięcie haseł aplikacji w usłudze Azure AD.
* Ustawienia lokalnej kontroli dostępu klienta nie są uznawane przez hasło aplikacji.
* Dla haseł aplikacji nie jest dostępna funkcja rejestrowania/inspekcji uwierzytelniania lokalnego.
* Niektóre zaawansowane projekty architektoniczne mogą wymagać użycia kombinacji organizacyjnej nazwy użytkownika i haseł oraz haseł aplikacji podczas korzystania z weryfikacji dwuetapowej z klientami, w zależności od tego, gdzie uwierzytelniają się. W przypadku klientów uwierzytelnianych w infrastrukturze lokalnej należy użyć nazwy użytkownika i hasła organizacyjnego. Dla klientów, którzy uwierzytelniają się za pomocą usługi Azure AD, należy użyć hasła aplikacji.
* Domyślnie użytkownicy nie mogą tworzyć haseł aplikacji. Jeśli chcesz zezwolić użytkownikom na tworzenie haseł aplikacji, wybierz **opcję Zezwalaj użytkownikom na tworzenie haseł aplikacji, aby zalogować się do aplikacji innych niż przeglądarki** w ustawieniach usługi.

## <a name="next-steps"></a>Następne kroki

[Włączanie samoobsługowego resetowania hasła w organizacji](quickstart-sspr.md)

[Włączanie uwierzytelniania wieloskładnikowego platformy Azure dla swojej organizacji](howto-mfa-getstarted.md)

[Włącz rejestrację połączoną w dzierżawie](howto-registration-mfa-sspr-combined.md)

[Dokumentacja konfiguracji metody uwierzytelniania użytkownika końcowego](https://aka.ms/securityinfoguide)
