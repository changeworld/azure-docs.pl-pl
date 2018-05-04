---
title: Samoobsługowego resetowania hasła jej działania — Azure Active Directory
description: Azure AD samoobsługowego resetowania hasła nowości
services: active-directory
keywords: ''
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: 618c5908-5bf6-4f0d-bf88-5168dfb28a88
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: it-pro;seohack1
ms.openlocfilehash: 602f4c9b7108c0116e83e302bd73838e0902a7f0
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="self-service-password-reset-in-azure-ad-deep-dive"></a>Samoobsługowe Resetowanie w usłudze Azure AD nowości haseł

Jak samoobsługowego resetowania hasła (SSPR) pracy? Co oznacza tej opcji w interfejsie? Kontynuuj lekturę, aby dowiedzieć się więcej na temat funkcji SSPR usługi Azure Active Directory (Azure AD).

## <a name="how-does-the-password-reset-portal-work"></a>Jak hasło zresetować portalu pracy?

Gdy użytkownik przechodzi do portalu resetowania haseł, przepływu pracy zostało rozpoczęte ustalenie:

   * Jak powinny być lokalizowany strony?
   * Konto użytkownika jest prawidłowy?
   * Jakie organizacji czy użytkownik należy do?
   * Gdy jest zarządzana hasło użytkownika
   * Użytkownik jest licencji na korzystanie z funkcji?

Strony resetowania odczytu kolejnych kroków, aby dowiedzieć się więcej o logiki hasło:

1. Użytkownik wybiera **nie może uzyskać dostępu do konta** łącze lub gdy przechodzi on bezpośrednio do [ https://aka.ms/sspr ](https://passwordreset.microsoftonline.com).
   * W oparciu o ustawienia regionalne przeglądarki, środowisko jest renderowany w odpowiednim języku. Środowisko resetowania hasła jest zlokalizowana na te same języki, które obsługuje usługi Office 365.
2. Użytkownik wprowadza nazwę użytkownika i przekazuje captcha.
3. Usługi Azure AD sprawdza, czy użytkownik jest w stanie użyć tej funkcji, wykonując następujące testy:
   * Sprawdza, czy użytkownik ma ta funkcja jest włączona i usługi Azure AD ma przypisanej licencji.
     * Jeśli użytkownik nie ma ta funkcja jest włączona lub nie ma przypisanej licencji, użytkownik jest proszony o ich administratora, aby zresetować swoje hasło.
   * Sprawdza, czy użytkownik ma prawo żądanie danych zdefiniowanych na koncie, zgodnie z zasadami administratora.
     * Jeśli zasady są wymagane tylko w jednym z wyzwań, następnie gwarantuje, że użytkownik ma odpowiednie dane zdefiniowane dla co najmniej jednym z wyzwań włączone przez zasady administratora.
       * Jeśli nie skonfigurowano żądania użytkownika, użytkownik jest zalecane jest administratora do zresetowania swojego hasła.
     * Jeśli zasady są wymagane dwa problemy, następnie gwarantuje, że użytkownik ma odpowiednie dane zdefiniowane dla co najmniej dwóch wyzwania włączone przez zasady administratora.
       * Jeśli nie skonfigurowano żądania użytkownika, użytkownik jest zalecane jest administratora do zresetowania swojego hasła.
   * Sprawdza, czy hasło danego użytkownika jest zarządzane lokalnie (federacyjnych, przekazywanego uwierzytelniania lub synchronizacji skrótów haseł).
     * Jeśli zapisywania zwrotnego jest wdrożony i hasło użytkownika jest zarządzane lokalnie, a następnie użytkownik będzie mógł przejść do uwierzytelniania i resetowania hasła.
     * Jeśli zapisywania zwrotnego nie został wdrożony i hasło użytkownika jest zarządzane lokalnie, użytkownik jest proszony o ich administratora, aby zresetować swoje hasło.
4. Jeśli okaże się, że użytkownik jest w stanie pomyślnie zresetować swoje hasło, użytkownik jest sterowana przez proces resetowania.

## <a name="authentication-methods"></a>Metody uwierzytelniania

Jeśli włączono SSPR, musisz wybrać co najmniej jeden z następujących opcji dla metod uwierzytelniania. Czasami słyszysz tych opcji, określany jako "bramy". Zdecydowanie zaleca się, wybierz co najmniej dwóch metod uwierzytelniania, dzięki czemu użytkownicy mają większą elastyczność.

* Email
* Telefon komórkowy
* Telefon biurowy
* Pytania zabezpieczające

![Uwierzytelnianie][Authentication]

### <a name="what-fields-are-used-in-the-directory-for-the-authentication-data"></a>Jakie pola są używane w katalogu danych uwierzytelniania?

* **Telefon biurowy**: odpowiada telefon biurowy.
    * Użytkownicy są nie można samodzielnie ustawić tego pola. Muszą być zdefiniowane przez administratora.
* **Telefon komórkowy**: numer telefonu uwierzytelniania (publicznie niewidoczny) lub telefonu komórkowego (publicznie widoczna).
    * Usługa szuka numer telefonu uwierzytelniania najpierw i następnie powróci do telefonu komórkowego, jeśli numer telefonu uwierzytelniania nie istnieje.
* **Alternatywny adres e-mail**: odnosi się do uwierzytelniania wiadomości e-mail (publicznie niewidoczny) lub alternatywny adres e-mail.
    * Usługa najpierw szuka uwierzytelniania wiadomości e-mail, a następnie powrót po awarii do alternatywny adres e-mail.

Domyślnie tylko telefon biurowy atrybuty chmury i telefon komórkowy są zsynchronizowane z katalogu w chmurze z katalogu lokalnego dla danych uwierzytelniania.

Użytkownicy mogą tylko zresetować swoje hasło, gdy mają danych zawartych w metod uwierzytelniania, których administrator włączył i wymaga.

Jeśli użytkownicy nie mają ich numer telefonu komórkowego, aby były widoczne w katalogu, ale nadal chcesz użyć go do resetowania hasła, wypełnij administratorów powinna nie w katalogu. Następnie należy wypełnić użytkownikom ich **numer telefonu uwierzytelniania** atrybutu za pośrednictwem [portalu rejestracji resetowania haseł](https://aka.ms/ssprsetup). Administratorzy mogą zobaczyć te informacje w profilu użytkownika, ale nie jest publikowany w innym miejscu.

### <a name="the-number-of-authentication-methods-required"></a>Liczba wymaganych metod uwierzytelniania

Ta opcja określa minimalną liczbę dostępne metody uwierzytelniania lub bramy, które użytkownik musi przejść do resetowania lub odblokowania hasła. Może należeć do jednego lub dwóch.

Użytkownicy, można podać więcej metod uwierzytelniania, jeśli administrator włącza tej metody uwierzytelniania.

Jeśli użytkownik nie ma minimalnych wymaganych metod zarejestrowany, zostanie wyświetlona stronę błędu, który kieruje je do żądania, że administrator zresetować swoje hasło.

#### <a name="change-authentication-methods"></a>Zmiana metody uwierzytelniania

Po uruchomieniu zasady z tylko jedną wymagane metodę uwierzytelniania dla resetowania lub odblokować zarejestrowane i zmiany, że do dwóch metod, co się stanie?

| Liczba metod w zarejestrowany | Wiele metod wymagane | Wynik |
| :---: | :---: | :---: |
| co najmniej 1 | 1 | **Możliwe** zresetować lub odblokowania |
| 1 | 2 | **Nie można** zresetować lub odblokowania |
| co najmniej 2 | 2 | **Możliwe** zresetować lub odblokowania |

Jeśli zmienisz typy metod uwierzytelniania, które użytkownik może używać mogą przypadkowo powstrzymanie użytkowników przed możliwości używania SSPR, jeśli nie ma minimalnej ilości dostępnych danych.

Przykład: 
1. Oryginalny zasad jest skonfigurowany z dwiema metodami uwierzytelniania, wymagany. Używa tylko numer telefonu pakietu office i pytania zabezpieczające. 
2. Administrator zmieni zasady nie są już używane pytań zabezpieczających, ale zezwala na korzystanie z telefonów komórkowych i alternatywny adres e-mail.
3. Użytkownicy bez telefon komórkowy i pól alternatywny adres e-mail nie można zresetować hasła.

### <a name="how-secure-are-my-security-questions"></a>Jak bezpieczne czy moje pytania zabezpieczające?

Użyj pytań zabezpieczających, zaleca się ich używać w połączeniu z innej metody. Pytania dotyczące bezpieczeństwa może być mniej bezpieczne od innych metod, ponieważ niektórzy użytkownicy mogą znać odpowiedzi na pytania innego użytkownika.

> [!NOTE] 
> Pytania zabezpieczające są przechowywane przez użytkowników i bezpiecznie obiektu użytkownika w katalogu i można tylko udzielane przez użytkowników podczas rejestracji. Nie istnieje sposób na odczyt lub modyfikacja pytań i odpowiedzi użytkownika przez administratora.
>

### <a name="security-question-localization"></a>Lokalizacja pytanie zabezpieczeń

Wszystkie wstępnie zdefiniowanych pytania, na które należy wykonać są zlokalizowane w pełny zestaw języków usługi Office 365 i są oparte na ustawienia regionalne przeglądarki użytkownika:

* W jakim mieście poznałeś/poznałaś swoją pierwszą współmałżonkę lub partnerkę albo swojego pierwszego współmałżonka lub partnera?
* W jakim mieście spotkali się Twoi rodzice?
* Jakie jest najbliższe miasto, w którym mieszka Twoje rodzeństwo?
* W jakim mieście urodził się Twój ojciec?
* W jakim mieście podjąłeś/podjęłaś swoją pierwszą pracę?
* W jakim mieście urodziła się Twoja matka?
* W jakim mieście byłaś/eś w dniu Nowego Roku 2000?
* Jak miał na nazwisko Twój ulubiony nauczyciel w szkole średniej?
* Na jaką uczelnię próbowałeś/próbowałaś się dostać, ale się nie udało?
* Gdzie odbyło się Twoje pierwsze wesele?
* Jak ma na drugie imię Twój ojciec?
* Jaka jest Twoja ulubiona potrawa?
* Jak ma na imię i nazwisko Twoja babcia od strony matki?
* Jakie jest drugie imię Twojej matki?
* Co to jest data urodzenia miesiąc i rok Twojego najstarszego rodzeństwa? (np. listopad 1985)
* Jak ma na drugie imię Twój najstarszy brat/siostra?
* Jak miał na imię i nazwisko Twój dziadek od strony ojca?
* Jakie jest drugie imię Twojego najmłodszego rodzeństwa?
* Jak nazywała się Twoja szkoła podstawowa?
* Jak miał na imię i nazwisko Twój najlepszy przyjaciel w dzieciństwie?
* Jak miała na imię i nazwisko Twoja pierwsza poważna sympatia?
* Jak miał na nazwisko Twój ulubiony nauczyciel w szkole podstawowej?
* Jaka była marka i model Twojego pierwszego samochodu lub motocykla?
* Jak się nazywała Twoja pierwsza szkoła?
* Jak nazywał się szpital Twoich narodzin?
* Przy jakiej ulicy znajdował się Twój pierwszy dom z dzieciństwa?
* Jak się nazywał Twój bohater z dzieciństwa?
* Jak się nazywała Twoja ulubiona maskotka?
* Jak się wabiło Twoje pierwsze zwierzę domowe?
* Jaki był Twój pseudonim w dzieciństwie?
* Jaki był Twój ulubiony sport w szkole średniej?
* Jaka była Twoja pierwsza praca?
* Jakie były cztery ostatnie cyfry Twojego numeru telefonu z dzieciństwa?
* Kim chciałeś/chciałaś zostać w dzieciństwie, gdy dorośniesz?
* Jak się nazywa najpopularniejsza poznana przez Ciebie osoba?

### <a name="custom-security-questions"></a>Niestandardowe pytania zabezpieczające

Pytania zabezpieczające niestandardowe nie są zlokalizowane dla różnych ustawień regionalnych. Wszystkie pytania niestandardowe są wyświetlane w tym samym języku wprowadzoną w interfejsie użytkownika administracyjnego, nawet jeśli ustawienia regionalne przeglądarki użytkownika jest inna. Jeśli potrzebujesz zlokalizowanych pytania należy używać wstępnie zdefiniowanych pytań.

Maksymalna długość pytanie zabezpieczające niestandardowych jest 200 znaków.

### <a name="security-question-requirements"></a>Wymagania dotyczące pytanie zabezpieczeń

* Ograniczenie liczby znaków minimalna odpowiedzi jest trzy znaki.
* Odpowiedzi maksymalny limit znaków wynosi 40 znaków.
* Użytkownicy nie może odebrać tego samego zapytania więcej niż jeden raz.
* Użytkownicy nie może sam Odpowiedz na więcej niż jedno pytanie.
* Każdy zestaw znaków może służyć do definiowania pytań i odpowiedzi, w tym znaków Unicode.
* Liczba pytań zdefiniowanych przez musi być większa lub równa liczbie pytania, które były wymagane do zarejestrowania.

## <a name="registration"></a>Rejestracja

### <a name="require-users-to-register-when-they-sign-in"></a>Wymagaj rejestracji po zalogowaniu użytkowników

Aby włączyć tę opcję, użytkownik, który jest włączony do resetowania hasła musi ukończyć rejestracji resetowania hasła, jeśli ich logowania się do aplikacji przy użyciu usługi Azure AD. Obejmuje to następujące elementy:

* Office 365
* Azure Portal
* Panel dostępu
* Aplikacji federacyjnych
* Niestandardowe aplikacje przy użyciu usługi Azure AD

Po wyłączeniu wymagają rejestracji użytkownicy mogą nadal ręcznie zarejestrować swoje informacje kontaktowe. Mogą one albo odwiedź [ https://aka.ms/ssprsetup ](https://aka.ms/ssprsetup) lub wybierz **rejestru w celu resetowania haseł** łącze w obszarze **profilu** kartę w panelu dostępu.

> [!NOTE]
> Użytkownicy mogą odrzucić portalu rejestracji resetowania haseł, wybierając **anulować** lub zamykania okna. Ale są monitowani o zarejestrować zawsze, gdy zalogują się w aż do chwili zakończenia ich rejestracji.
>
> Nie przerwać połączenie użytkownika, jeśli jest już zarejestrowany.

### <a name="set-the-number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>Ustaw liczbę dni, po którym użytkownicy są proszeni o Potwierdź swoje informacje dotyczące uwierzytelniania

Ta opcja określa okres czasu między ustawienie i reconfirming informacje dotyczące uwierzytelniania i jest dostępna tylko po włączeniu **wymagają rejestracji użytkowników podczas logowania** opcji.

Prawidłowe wartości to 0 do 730 dni, z wartością 0, co oznacza, że użytkownicy nigdy nie są proszeni o Potwierdź swoje informacje o uwierzytelnianiu.

## <a name="notifications"></a>Powiadomienia

### <a name="notify-users-on-password-resets"></a>Czy powiadamiać użytkowników o resetowaniu hasła?

Jeśli ta opcja jest ustawiona na **tak**, a następnie użytkownik, który jest zresetowania hasła otrzymuje wiadomość e-mail z informacją, że ich hasło zostało zmienione. Wiadomości e-mail są wysyłane za pośrednictwem portalu SSPR do ich adresów e-mail podstawowe i alternatywne, które są w pliku w usłudze Azure AD. Żaden inny użytkownik jest powiadamiany o resetowania zdarzeń.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Powiadamianie wszystkich administratorów innych administratorów resetowanie haseł

Jeśli ta opcja jest ustawiona na **tak**, następnie *wszystkich administratorów* otrzymasz wiadomość e-mail do ich podstawowego adresu e-mail w pliku w usłudze Azure AD. Adres e-mail powiadomienia inny administrator ma zmienić swoje hasło przy użyciu funkcji SSPR.

Przykład: Istnieją cztery Administratorzy w środowisku. Administrator A Resetuje hasła przy użyciu funkcji SSPR. Administratorzy B, C i D otrzymywać wiadomości e-mail, które alerty ich resetowania hasła.

## <a name="on-premises-integration"></a>Integracja lokalna

Jeśli musisz zainstalować, skonfigurować i włączyć Azure AD Connect, masz następujące dodatkowe opcje integracji z lokalnymi. Jeśli te opcje są wygaszone, następnie zapisywania zwrotnego nie został poprawnie skonfigurowany. Aby uzyskać więcej informacji, zobacz [Konfigurowanie funkcji zapisywania zwrotnego haseł](howto-sspr-writeback.md#configure-password-writeback).

![Zapisywanie zwrotne][Writeback]

Ta strona zawiera szybkie stan zapisywania zwrotnego lokalnej klienta jeden z następujących komunikatów o błędach są wyświetlane na podstawie bieżącej konfiguracji:

* Klient funkcji zapisywania zwrotnego lokalnej jest uruchomiona.
* Usługi Azure AD jest w trybie online i jest połączona z lokalnego klienta zapisywania zwrotnego. Jednak prawdopodobnie zainstalowana wersja programu Azure AD Connect jest nieaktualny. Należy wziąć pod uwagę [uaktualnianie programu Azure AD Connect](./../connect/active-directory-aadconnect-upgrade-previous-version.md) aby upewnić się, czy masz najnowsze funkcje łączności i ważnych poprawek.
* Niestety, firma Microsoft nie może sprawdzić stan klienta lokalnego zapisywania zwrotnego ponieważ zainstalowana wersja programu Azure AD Connect jest nieaktualny. [Uaktualnij usługi Azure AD Connect](./../connect/active-directory-aadconnect-upgrade-previous-version.md) Aby móc sprawdzić stan połączenia.
* Niestety prawdopodobnie nie nie może połączyć się z klienta lokalnego zapisywania zwrotnego w tej chwili. [Rozwiązywanie problemów z usługi Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) próby odzyskania połączenia.
* Niestety nie można nawiązać lokalnej klienta zapisywania zwrotnego, ponieważ nie został poprawnie skonfigurowany funkcji zapisywania zwrotnego haseł. [Konfigurowanie funkcji zapisywania zwrotnego haseł](howto-sspr-writeback.md#configure-password-writeback) próby odzyskania połączenia.
* Niestety prawdopodobnie nie nie może połączyć się z klienta lokalnego zapisywania zwrotnego w tej chwili. Może to być spowodowane problemami z tymczasowego po naszej stronie. Jeśli problem będzie się powtarzać, [Rozwiązywanie problemów z programu Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) próby odzyskania połączenia.

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Zapisywania zwrotnego haseł do katalogu lokalnego

Ten formant określa, czy zapisywanie zwrotne haseł jest włączony dla tego katalogu. W przypadku zapisywania zwrotnego na, wskazuje stan lokalną usługą zapisywania zwrotnego. Jest to przydatne, jeśli chcesz tymczasowo wyłączyć funkcję zapisywania zwrotnego haseł, bez konieczności ponownej konfiguracji usługi Azure AD Connect.

* Jeśli przełącznik ma ustawioną wartość **tak**, następnie zapisywania zwrotnego jest włączona, a federacyjnych, przekazywanego uwierzytelniania lub użytkowników synchronizacji skrótu hasła mogą resetować hasła.
* Jeśli przełącznik ma ustawioną wartość **nr**, następnie zapisywania zwrotnego jest wyłączona i federacyjnych, przekazywanego uwierzytelniania lub użytkowników synchronizacji skrótu hasła nie będą mogli resetować hasła.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Zezwalaj użytkownikom na odblokowanie kont bez konieczności zresetowania hasła

Ten formant określa, czy użytkownicy, którzy odwiedzają witrynę portalu resetowania haseł powinien mieć możliwość do odblokowania ich lokalnych kont usługi Active Directory bez konieczności resetowania hasła. Domyślnie program Azure AD umożliwia odblokowanie konta podczas resetowania hasła. Możesz użyć tego ustawienia do oddzielania tych dwóch operacji. 

* Jeśli ustawiono **tak**, a następnie użytkownicy otrzymują możliwość do zresetowania swojego hasła i odblokowania konta lub odblokować konto bez konieczności resetowania hasła.
* Jeśli ustawiono **nr**, następnie użytkownicy są tylko będą mogli przeprowadzić resetowanie hasła połączonych i operacji odblokowania konta.

## <a name="how-does-password-reset-work-for-b2b-users"></a>Jak hasło zresetować pracy B2B użytkowników?
Resetowanie hasła i zmiany są w pełni obsługiwane na wszystkich konfiguracji z biznesowych między firmami (B2B). Resetowanie hasła użytkownika B2B jest obsługiwane w następujących trzech przypadkach:

   * **Użytkownicy z organizacji partnerskiej z istniejącą dzierżawą usługi Azure AD**: Jeśli w organizacji, w przypadku partnerstwo z istniejącą dzierżawą usługi Azure AD, firma Microsoft *przestrzegać niezależnie od zasady resetowania hasła są włączone na tę dzierżawę*. Do resetowania hasła, aby pracować organizacji partnerskiej odpowiedzialnej za tylko musi upewnić się, czy włączono usługi Azure AD SSPR. Bezpłatne dla klientów usługi Office 365 nie istnieje i można ją włączyć, wykonując kroki opisane w naszym [wprowadzenie do zarządzania hasłami](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords) przewodnik.
   * **Użytkownicy, którzy Zarejestruj się za pośrednictwem** samoobsługowego tworzenia konta: Jeśli w organizacji jest partnerstwo z używanych [samoobsługowego tworzenia konta](../active-directory-self-service-signup.md) funkcji, aby pobrać do dzierżawcy, nie możemy udostępnić je zresetować hasła za pomocą poczty e-mail, w zarejestrowani.
   * **Użytkownicy B2B**: żadnych nowych użytkowników B2B utworzone przy użyciu nowej [możliwości B2B usługi Azure AD](../active-directory-b2b-what-is-azure-ad-b2b.md) będzie można resetować hasła z wiadomością e-mail one zarejestrowane podczas procesu zaproszenia.

Do przetestowania tego scenariusza, przejdź do http://passwordreset.microsoftonline.com jeden z tych użytkowników z firm partnerskich. Jeśli dysponują alternatywny adres e-mail lub uwierzytelniania wiadomości e-mail zdefiniowanych resetowania haseł działa zgodnie z oczekiwaniami.

> [!NOTE]
> Konta Microsoft, którym udzielono dostępu dla gości do dzierżawy usługi Azure AD, takich jak Hotmail.com, Outlook.com lub innych osobistych adresów e-mail, nie będą mogli używać usługi Azure AD SSPR. Potrzebuje do zresetowania swojego hasła, korzystając z informacji zamieszczonych w [po nie logowania się do konta Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artykułu.

## <a name="next-steps"></a>Kolejne kroki

Poniższe artykuły zawierają dodatkowe informacje dotyczące resetowania haseł za pomocą usługi Azure AD:

* [Jak wykonać pomyślne wdrożenie funkcji samoobsługowego resetowania haseł?](howto-sspr-deployment.md)
* [Resetowanie lub zmienianie hasła](../active-directory-passwords-update-your-own-password.md)
* [Rejestrowanie na potrzeby samoobsługowego resetowania haseł](../active-directory-passwords-reset-register.md)
* [Czy masz pytanie dotyczące licencjonowania?](concept-sspr-licensing.md)
* [Jakie dane są używane przez funkcję samoobsługowego resetowania haseł i jakie dane powinny zostać wypełnione dla użytkowników?](howto-sspr-authenticationdata.md)
* [Jakie metody uwierzytelniania są dostępne dla użytkowników?](concept-sspr-howitworks.md#authentication-methods)
* [Jakie są opcje zasad dla funkcji samoobsługowego resetowania haseł?](concept-sspr-policy.md)
* [Co to jest funkcja zapisywania zwrotnego haseł i dlaczego jest ona tak ważna?](howto-sspr-writeback.md)
* [Jak zgłosić działanie funkcji samoobsługowego resetowania haseł?](howto-sspr-reporting.md)
* [Jakie są dostępne opcje funkcji samoobsługowego resetowania haseł i do czego one służą?](concept-sspr-howitworks.md)
* [Myślę, że coś działa niewłaściwie. Jak rozwiązywać problemy z funkcją samoobsługowego resetowania haseł?](active-directory-passwords-troubleshoot.md)
* [Mam pytanie, na które nie mogę znaleźć odpowiedzi](active-directory-passwords-faq.md)

[Authentication]: ./media/concept-sspr-howitworks/sspr-authentication-methods.png "Dostępne metody uwierzytelniania w usłudze Azure AD i wymagane ilości"
[Writeback]: ./media/concept-sspr-howitworks/troubleshoot-writeback-running.png "Lokalne konfigurację funkcji zapisywania zwrotnego haseł integracji i informacje dotyczące rozwiązywania problemów"
