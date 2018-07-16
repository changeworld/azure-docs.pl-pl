---
title: Pomoc dotycząca aplikacji Microsoft Authenticator — usługa Azure AD | Dokumentacja firmy Microsoft
description: Zawiera listę często zadawanych pytań i odpowiedzi dotyczące aplikacji firmy Microsoft Authentication i usługi Azure Multi-Factor Authentication.
services: multi-factor-authentication
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.assetid: f04d5bce-e99e-4f75-82d1-ef6369be3402
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/08/2018
ms.author: lizross
ms.reviewer: librown
ms.custom: end-user
ms.openlocfilehash: d86bc84653e38a9b64a336b8ce9ed7e657129e8c
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39059830"
---
# <a name="microsoft-authenticator-app-faq"></a>Aplikacja Microsoft Authenticator — często zadawane pytania

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące aplikacji Microsoft Authenticator. Jeśli nie widzisz odpowiedź na Twoje pytanie, przejdź do strony [forum aplikacji Microsoft Authenticator](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp). Ponadto można przejrzeć inną często zadawane pytania dotyczące określonych funkcji w ramach aplikacji [Zaloguj się przy użyciu telefonu — często zadawane pytania](microsoft-authenticator-app-phone-signin-faq.md).

Aplikacja Microsoft Authenticator zastępowane aplikacji Azure Authenticator, a jest zalecaną aplikację, korzystając z usługi Azure Multi-Factor Authentication. Aplikacja Microsoft Authenticator jest dostępna dla systemów [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) i [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071).

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="what-data-does-the-authenticator-store-on-my-behalf-and-how-can-i-delete-it"></a>Jakie dane wystawcy uwierzytelnienia przechowuje w moim imieniu i jak go usunąć?

Microsoft Authenticator przechowuje informacje o koncie, utworzone po dodaniu konta. Korzystając z uwierzytelniania dziennik diagnostyczny jest tworzony na potrzeby debugowania i przydatne dane są przechowywane w ułatwienia firmie Microsoft diagnozowanie nieprzewidzianych problemów. Uzyskujesz dostęp do danych dziennika, otwierając **pomocy** > **Wyślij dzienniki** > **wyświetlanie dzienników**.

Dane można usunąć przez usunięcie Kafelek konta. Usunięcie kafelka konta spowoduje również usunięcie wszystkich informacje o koncie używany przez aplikację, w tym dzienniki. 

Aby uzyskać więcej informacji o używaniu danych firmy Microsoft odwiedź stronę: https://servicetrust.microsoft.com/ViewPage/PrivacyGettingStarted

### <a name="what-are-the-codes-in-the-app-for-why-does-the-number-keep-counting-down"></a>Co to są kody w aplikacji na potrzeby? Dlaczego liczba zachowania liczenia w dół?

Po otwarciu aplikacji Microsoft Authenticator, zobaczysz kont, które zostały dodane i 6 - lub 8 cyfrowy numer przez każdy z nich. Możesz zobaczyć czasomierz 30 sekund licząc w dół.

Kody te są używane podczas logowania się do swojego konta. Po wprowadzeniu nazwy użytkownika i hasła, może zostać poproszony o podanie kodu weryfikacyjnego. Otwórz aplikację Microsoft Authenticator, a następnie skopiuj kod, który jest obecnie wyświetlane. Wprowadź ten kod na stronie logowania, aby zakończyć.

Przyczyna, który kodów zmienić co 30 sekund jest tak, aby użytkownik nigdy nie używaj tego samego kodu dwa razy. Nie ma takich jak hasła, które one powinien pamiętać. Chodzi o to, że tylko osoba z dostępem na Twój telefon zna Twój kod weryfikacyjny.

Kody nie wymagają internet lub danych, więc nie trzeba się martwić o smartfona, aby zalogować się. Po zamknięciu aplikacji nie pozostanie uruchomione w tle, a nie wyczerpuje, poziom naładowania baterii. Można zamknąć aplikację i go zignorować czasu następnego logowania.  

### <a name="i-only-get-notifications-when-i-have-the-app-open-if-the-app-isnt-open-i-dont-get-any-notifications"></a>Czy mogę tylko Otrzymuj powiadomienia, gdy mam aplikacji Otwórz. Jeśli aplikacja nie jest otwarty, nie pojawia się wszelkie powiadomienia.

Jeśli Otrzymuj powiadomienia, ale ich nie wydaje dźwięki, ani też pomimo swojej dzwonka zablokowany z przyczyn, najpierw należy sprawdzić ustawienia aplikacji. Włącz aplikację, aby użyć dźwięku ani też za pomocą jego powiadomień.

Jeśli w ogóle nie otrzymywać powiadomienia, sprawdź następujących przypadkach:

- Twój telefon znajduje się w trybie nie przeszkadzać lub cichego? Ten tryb może uniemożliwić wysyłania powiadomień aplikacji.
- Można otrzymywać powiadomienia z innych aplikacji? W przeciwnym razie może to być problem z połączeń sieciowych na telefonie lub kanał powiadomień od firmy Apple i Android. Pierwsza opcja można rozwiązać w ustawienia telefonu, ale może być konieczne na komunikowanie się z usługodawcą, aby uzyskać pomoc dotyczącą drugiej opcji.
- Może odbierać powiadomienia dla niektórych kont w obrębie aplikacji, ale niekoniecznie? Jeśli tak, Usuń konto problematyczne ze swojej aplikacji i dodaj go ponownie, aby włączyć powiadomienia wypychane.

Jeśli nastąpiła tych wskazówek dotyczących rozwiązywania problemów, ale nadal występują problemy, możesz wysłać dzienniki diagnostyki. Przejdź do ustawień aplikacji, a następnie wybierz **Pomoc i opinie** i **Wyślij dzienniki**. Następnie przejdź do [forum aplikacji Microsoft Authenticator](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp) i Daj nam znać, co widzisz problemu oraz jakie kroki sprawdzone do tej pory.

### <a name="im-already-using-the-microsoft-authenticator-application-for-verification-codes-how-do-i-switch-to-one-click-push-notifications"></a>Korzystam już aplikacji Microsoft Authenticator do używania kodów weryfikacyjnych. Jak przełączyć wysyłanie powiadomień wypychanych w jednym kliknięciem
Zatwierdzanie logowania za pomocą powiadomień wypychanych jest dostępna tylko dla osobistych kont Microsoft lub pracy oraz służbowego konta Microsoft, nie dla kont innych firm, takich jak Facebook lub Google. Jeśli masz służbowe konto Microsoft Twojej organizacji można wyłączyć tę opcję.

Korzystanie z konta Microsoft do konta osobistego i chcesz przełączyć się do powiadomień wypychanych, musisz dodać swoje konto ponownie. Zarejestruj ponownie urządzenie przy użyciu swojego konta i skonfigurujesz powiadomienia wypychane.  

Jeśli Microsoft Authenticator na użytek pracy lub konta służbowego, następnie organizacji decyduje, czy zezwolić na powiadomienia o jednym kliknięciem.

### <a name="do-one-click-push-notifications-work-for-non-microsoft-accounts"></a>Powiadomienia wypychane jednym kliknięciem działają dla kont innych niż Microsoft?
Nie, powiadomienia wypychane działają tylko z kontami Microsoft i kontami usługi Azure Active Directory. Służbowe korzysta z konta usługi Azure AD, mogą wyłączyć tę funkcję.  

### <a name="i-got-a-new-device-or-restored-my-device-from-a-backup-how-do-i-set-up-my-accounts-in-the-microsoft-authenticator-app-again"></a>Czy mogę stało się nowe urządzenie lub przywrócić urządzenie z kopii zapasowej. Jak skonfigurować kont w aplikacji Microsoft Authenticator ponownie?
Jeśli korzystasz z urządzenia z systemem iOS, włączono **automatycznego tworzenia kopii zapasowych**i utworzeniu kopii zapasowej kont na urządzeniu z systemem stare; tej kopii zapasowej można użyć do odzyskania poświadczenia konta na nowe urządzenie. Aby uzyskać więcej informacji, zobacz [kopia zapasowa i odzyskiwanie poświadczeń konta, za pomocą aplikacji Microsoft Authenticator](microsoft-authenticator-app-backup-and-recovery.md) artykułu. 

### <a name="i-lost-my-device-or-moved-on-to-a-new-device-how-do-i-make-sure-notifications-dont-continue-to-go-to-my-old-device"></a>Czy mogę utraty urządzenia lub przeniesione do nowego urządzenia. Jak upewnić się, że powiadomienia nie w dalszym ciągu przejdź do starego urządzenia?  
Dodawanie aplikacji Microsoft Authenticator na nowe urządzenia z systemem iOS nie będą usuwane automatycznie aplikacji stare z urządzenia z systemem. Nawet usuwania aplikacji ze starego urządzenie nie jest wystarczająca. Zarówno należy usunąć aplikację z urządzenia do starego i poinformuj firmy Microsoft lub organizacji, aby nie pamięta starym urządzeniem i wyrejestrować go z Twojego konta.
- **Aby usunąć aplikację z urządzenia przy użyciu osobistego konta Microsoft.** Przejdź do obszaru weryfikacji dwuetapowej użytkownika [zabezpieczenia konta](https://account.microsoft.com/security) strony i wybrać opcję wyłączenia weryfikacji dla starego urządzenia.  
- **Aby usunąć aplikację z urządzenia przy użyciu konta firmowego lub szkolnego firmy Microsoft.** Przejdź do obszaru weryfikacji dwuetapowej użytkownika [MyApps](https://myapps.microsoft.com/) strony lub portalu niestandardowym Twojej organizacji i wybrać opcję wyłączenia weryfikacji dla starego urządzenia. 



### <a name="how-do-i-remove-an-account-from-the-app"></a>Jak usunąć konto z aplikacji?
* dla systemu iOS: na ekranie głównym, przesuń palcem po lewej na kafelku konta. Wybierz pozycję **Usuń**.
* Windows Phone: Na ekranie głównym kliknij przycisk menu, następnie **Edycja kont**. Naciśnij pozycję **X** obok nazwy konta.
* Android: Na ekranie głównym kliknij przycisk menu, następnie **Edycja kont**. Naciśnij pozycję **X** obok nazwy konta.

Jeśli masz urządzenie, który jest zarejestrowany w swojej organizacji, może być konieczne wykonanie dodatkowych czynności usunięcie konta. Na tych urządzeniach aplikacja Microsoft Authenticator jest automatycznie rejestrowane jako administrator urządzenia. Aby odinstalować aplikację, musisz najpierw Wyrejestruj aplikacji w ustawieniach aplikacji.

### <a name="why-does-the-app-request-so-many-permissions"></a>Dlaczego aplikacja może zażądać tak wiele uprawnień?
Poniżej przedstawiono pełną listę uprawnień, które może być konieczne podanie i jak są używane w aplikacji. Określone uprawnienia, które widzisz zależą od typu telefonu, do których masz.

* **Aparat fotograficzny**: używane do skanowania kodów QR, po dodaniu pracy, szkoły lub konto firmy Microsoft.
* **Kontakty i phone**: używane w celu uproszczenia procesu przez wyszukiwanie istniejących kont na telefonie, po zalogowaniu się przy użyciu osobistego konta Microsoft.
* **SMS**: używany do sprawdzenia, numer telefonu jest zgodna z liczbą w rekordzie. Po zalogowaniu się przy użyciu osobistego konta Microsoft po raz pierwszy.  Wyślemy wiadomość SMS na telefon którego pobrano aplikację, która zawiera kod weryfikacyjny cyfrę 6 – 8. Zamiast pytaniem, aby znaleźć ten kod, a następnie wprowadź go w aplikacji, został znaleziony w wiadomości tekstowej dla Ciebie.
* **Rysowanie przez inne aplikacje**: otrzymujesz samodzielną sprawdzającą tożsamości zostanie wyświetlone powiadomienie również w żadnej aplikacji, które mogą być uruchamiane.
* **Odbieranie danych z Internetu**: to uprawnienie jest wymagane do wysyłania powiadomień.
* **Uniemożliwić phone uśpiony**: Jeśli zarejestrujesz urządzenie w organizacji, organizacja może zmienić te zasady, na telefonie.
* **Kontrolowanie wibracje**: Możesz wybrać, czy chcesz wibracje zawsze wtedy, gdy otrzymasz powiadomienie, aby zweryfikować swoją tożsamość.
* **Korzystanie ze sprzętu odcisku palca**: niektóre oraz do kont służbowych wymagają dodatkowego kodu PIN przy każdym zweryfikować Twoją tożsamość. Aby ułatwić ten proces, My pozwalamy na Użyj odcisku palca zamiast wprowadzać numer PIN.
* **Wyświetlanie połączeń sieciowych**: po dodaniu konta Microsoft, aplikacja wymaga połączenia sieciowego/internet.
* **Czytaj zawartość magazynu**: to uprawnienie jest używane tylko, gdy możesz zgłaszać problemy techniczne za pomocą ustawień aplikacji. Niektóre informacje z magazynu są zbierane do zdiagnozowania problemu.
* **Pełny dostęp do sieci**: to uprawnienie jest wymagane do wysyłania powiadomień w celu zweryfikowania Twojej tożsamości.
* **Uruchamiane przy uruchamianiu**: po ponownym uruchomieniu Twój telefon zapewnia to uprawnienie, nadal otrzymywać powiadomienia, aby zweryfikować swoją tożsamość.

### <a name="why-does-the-microsoft-authenticator-app-allow-you-to-approve-a-request-without-unlocking-the-device"></a>Dlaczego warto z aplikacji Microsoft Authenticator zezwala na zatwierdzenie żądania bez odblokowywania urządzenia?

Nie masz do odblokowania urządzenia w taki sposób, aby zatwierdzić żądania weryfikacji, ponieważ wszystko, czego potrzebujesz, aby udowodnić, że musi mieć telefon przy sobie. Weryfikacja dwuetapowa wymaga potwierdzające dwie rzeczy — rzeczy, które znasz i rzeczą, jaką masz. Rzecz, którą znasz jest hasło. Rzeczą, jaką masz jest telefonu (skonfigurować przy użyciu aplikacji Microsoft Authenticator i zarejestrowana jako dowód MFA). W związku z tym o numer telefonu i zatwierdzania żądania spełnia kryteria drugi składnik uwierzytelniania.

### <a name="what-does-the-lock-icon-in-the-account-list-mean"></a>Co oznacza ikonę blokady na liście kont?

Ikona kłódki wskazuje, że urządzenie jest zarejestrowane w usłudze Azure AD i zarejestrowana na koncie. Rejestracja urządzeń dla systemu iOS ma miejsce podczas rejestracja w usłudze Microsoft Intune.

## <a name="next-steps"></a>Kolejne kroki

### <a name="contact-us"></a>Skontaktuj się z nami
Jeśli Twoje pytanie nie został tutaj odpowiedzi, chcemy poznać Twoją opinię. Przejdź do [forum aplikacji Microsoft Authenticator](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp) Opublikuj swoje pytanie i Uzyskaj pomoc od społeczności, lub pozostaw komentarz na tej stronie.


### <a name="related-topics"></a>Powiązane tematy
* [O weryfikacji dwuetapowej](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification) kont Microsoft
* [Problemy z weryfikacji dwuetapowej](multi-factor-authentication-end-user-troubleshoot.md) dla swojego konta firmowego lub szkolnego?
* [Użyj Microsoft Authenticator, aby zalogować się na telefonie](microsoft-authenticator-app-phone-signin-faq.md)
