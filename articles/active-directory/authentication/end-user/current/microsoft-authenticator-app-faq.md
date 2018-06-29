---
title: Pomoc dotycząca aplikacji uwierzytelniania firmy Microsoft — usługi Azure AD | Dokumentacja firmy Microsoft
description: Zawiera listę często zadawanych pytań i odpowiedzi dotyczące aplikacji Microsoft Authentication i Azure Multi-Factor Authentication.
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
ms.openlocfilehash: 9aea8060d9b3a5314d4c6f42197bb78586d5ad00
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102489"
---
# <a name="microsoft-authenticator-app-faq"></a>Aplikacja uwierzytelniania firmy Microsoft — często zadawane pytania

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące aplikacji Authenticator firmy Microsoft. Jeśli nie widzisz odpowiedź na swoje pytanie, przejdź do [forum aplikacji Microsoft Authenticator](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp). Ponadto można przejrzeć innego często zadawane pytania dotyczące określonych funkcji w aplikacji, [Zaloguj się przy użyciu telefonu — często zadawane pytania](microsoft-authenticator-app-phone-signin-faq.md).

Aplikacja Microsoft Authenticator zastępowane aplikacji Azure Authenticator, a jest zalecaną aplikację, korzystając z usługi Azure Multi-Factor Authentication. Jest dostępna dla aplikacji Microsoft Authenticator [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594), i [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071).

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="what-data-does-the-authenticator-store-on-my-behalf-and-how-can-i-delete-it"></a>Jakie dane wystawcy uwierzytelnienia przechowuje w imieniu mojej i jak go usunąć?

Microsoft Authenticator przechowuje informacje o koncie, utworzone po dodaniu konta. Gdy używasz uwierzytelniania dzienników diagnostycznych jest tworzony na potrzeby debugowania i przechowuje dane przydatne w ułatwienia firmie Microsoft diagnozowanie problemów nieprzewidzianego. Ma dostęp do danych dziennika, otwierając **pomocy** > **Wyślij dzienniki** > **Sprawdź dzienniki**.

Można usunąć dane przez usunięcie Kafelek konta. Usunięcie Kafelek konta spowoduje również usunięcie wszystkich informacji konto używane przez aplikację, w tym dzienniki. 

Aby uzyskać więcej informacji, w jaki sposób firma Microsoft korzysta z danych należy odwiedzić witrynę: https://servicetrust.microsoft.com/ViewPage/PrivacyGettingStarted

### <a name="what-are-the-codes-in-the-app-for-why-does-the-number-keep-counting-down"></a>Co to są kody w aplikacji w? Dlaczego numer keep, licząc w?

Po otwarciu aplikacji Authenticator firmy Microsoft, zobacz konta, które zostały dodane i numer lub ośmiu sześciocyfrowy przez każde z nich. Może pojawić się czasomierza 30 sekund, licząc w.

Te kody są używane podczas logowania się do swojego konta. Po wprowadzeniu nazwy użytkownika i hasła, może zostać poproszona o wprowadź kod weryfikacyjny. Otwórz aplikację Microsoft Authenticator i skopiuj kod, który jest aktualnie wyświetlany. Wprowadź ten kod na stronie logowania, aby zakończyć.

Powód kody zmienić co 30 sekund jest, aby nie używać tego samego kodu dwukrotnie. Nie ma takich jak hasła, które są powinien do zapamiętania. Pomysł jest, że tylko osoba z dostępem do telefonu zna kod weryfikacyjny.

Kody nie wymagają internet lub dane, tak aby nie trzeba się martwić o telefoniczna do logowania. Podczas zamykania aplikacji nie zachować uruchomione w tle, a nie opróżnienia baterii. Możesz zamknąć aplikacji i Ignoruj, dopóki przy następnym logowaniu.  

### <a name="i-only-get-notifications-when-i-have-the-app-open-if-the-app-isnt-open-i-dont-get-any-notifications"></a>Można tylko otrzymywać powiadomienia, gdy aplikacja na komputerze otwórz. Jeśli aplikacja nie jest otwarty, nie pojawia się powiadomienia.

Jeśli otrzymywać powiadomień, ale nie należy szumu lub Włącz wibrację pomimo Twojej dzwonka w, sprawdź najpierw ustawień aplikacji. Włącz aplikację, aby użyć dźwięku, ani też z jego powiadomienia.

Jeśli w ogóle nie otrzymywać powiadomień, sprawdź następujących przypadkach:

- Twój telefon znajduje się w trybie nie przeszkadzać lub cichego? Ten tryb może zapobiec wysyłanie powiadomień aplikacji.
- Możesz otrzymywać powiadomienia z innych aplikacji? Jeśli nie, może być problem z połączeń sieciowych na telefonie lub kanału powiadomień z systemu Android i Apple. Pierwsza opcja można rozwiązać w ustawienia telefonu, ale może wymagać komunikował się z dostawcą usług, aby uzyskać pomoc dotyczącą drugiej opcji.
- Możesz otrzymywać powiadomienia dla niektórych kont w aplikacji, ale niekoniecznie? Jeśli tak, Usuń konto powodować problemy z aplikacji, a następnie dodaj go ponownie w celu włączenia powiadomień wypychanych.

Jeśli nastąpiła te sugestie dotyczące rozwiązywania problemów, ale nadal występują problemy, możesz wysłać dzienniki dla diagnostyki. Przejdź do ustawień aplikacji, a następnie wybierz **— Pomoc i opinie** i **wysyłanie dzienników**. Następnie przejdź do [forum aplikacji Microsoft Authenticator](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp) i Daj nam znać, co problem został wyświetlony i jakie kroki próbowałeś wykonanej do tej pory.

### <a name="im-already-using-the-microsoft-authenticator-application-for-verification-codes-how-do-i-switch-to-one-click-push-notifications"></a>Aplikacja Microsoft Authenticator już jest używany do używania kodów weryfikacyjnych. Jak zmienić powiadomień wypychanych jednym kliknięciem?
Zatwierdzanie logowanie za pomocą powiadomień wypychanych jest dostępna tylko dla osobistego konta Microsoft lub pracy oraz szkolnego konta Microsoft, nie dla kont innych firm, takich jak Google lub usługi Facebook. Jeśli masz służbowe konto Microsoft organizacji można wyłączyć tę opcję.

Korzystanie z konta osobistego konta Microsoft, aby przełączyć się do powiadomień wypychanych należy ponownie dodać konto. Ponowne zarejestrowanie urządzenia przy użyciu konta i Konfigurowanie powiadomień wypychanych.  

Jeżeli używasz Authenticator firmy Microsoft do pracy lub konta służbowego, następnie organizacji decyduje o tym, czy zezwolić na powiadomienia o jednym kliknięciem.

### <a name="do-one-click-push-notifications-work-for-non-microsoft-accounts"></a>Powiadomienia wypychane jednym kliknięciem działają dla kont innych niż firmy Microsoft?
Nie, powiadomienia wypychane działa tylko z kontami Microsoft i kontami usługi Azure Active Directory. Konto służbowe używa konta usługi Azure AD, może wyłączyć tę funkcję.  

### <a name="i-got-a-new-device-or-restored-my-device-from-a-backup-how-do-i-set-up-my-accounts-in-the-microsoft-authenticator-app-again"></a>I uzyskano nowego urządzenia lub przywrócony z kopii zapasowej urządzenia. Jak skonfigurować kont w aplikacji Microsoft Authenticator ponownie?
Jeśli korzystasz z urządzenia z systemem iOS, włączono **automatycznego tworzenia kopii zapasowej**i utworzeniu kopii zapasowej kont na urządzeniu starego; tej kopii zapasowej można użyć do odzyskania na urządzeniu nowych poświadczeń konta. Aby uzyskać więcej informacji, zobacz [kopii zapasowej i odzyskiwanie poświadczenia konta z aplikacji Microsoft Authenticator](../../../../multi-factor-authentication/end-user/microsoft-authenticator-app-backup-and-recovery.md) artykułu. 

### <a name="i-lost-my-device-or-moved-on-to-a-new-device-how-do-i-make-sure-notifications-dont-continue-to-go-to-my-old-device"></a>I utraty urządzenia lub przeniesione do nowego urządzenia. Jak utworzyć się, że powiadomienia nie w dalszym ciągu przejdź do starego urządzenia?  
Dodawanie aplikacji Microsoft Authenticator do nowego urządzenia z systemem iOS nie będą automatycznie usunąć aplikacji z starego urządzenia. Nawet usunięcie aplikacji z urządzenia starego jest niewystarczające. Należy zarówno usunięcie aplikacji z urządzenia stary i poinformuj firmy Microsoft lub organizacji, aby zapomnisz starym urządzeniem i wyrejestrować go z Twojego konta.
- **Aby usunąć aplikację z urządzenia za pomocą osobistego konta Microsoft.** Przejdź do obszaru weryfikacji dwuetapowej Twojej [zabezpieczenia konta](https://account.microsoft.com/security) strony i wyłączyć weryfikację starego urządzenia.  
- **Aby usunąć aplikację z urządzenia przy użyciu firmowego lub szkolnego konta Microsoft.** Przejdź do obszaru weryfikacji dwuetapowej Twojej [MyApps](https://myapps.microsoft.com/) strony lub niestandardowych portal firmy i wyłączyć weryfikację starego urządzenia. 



### <a name="how-do-i-remove-an-account-from-the-app"></a>Jak usunąć konto z aplikacji?
* iOS: na ekranie głównym, przejdź w lewo na kafelku konta. Wybierz pozycję **Usuń**.
* Windows Phone: Na ekranie głównym kliknij przycisk menu, następnie **edycji kont**. Wybierz **X** obok nazwy konta.
* System android: Na ekranie głównym kliknij przycisk menu, następnie **edycji kont**. Wybierz **X** obok nazwy konta.

Jeśli masz urządzenie jest zarejestrowane w swojej organizacji, może być konieczne wykonanie dodatkowych czynności można usunąć konta. Na tych urządzeniach aplikacji Microsoft Authenticator jest automatycznie dodawane jako administrator urządzenia. Aby całkowicie odinstalować aplikację, musisz najpierw wyrejestrować aplikacji w ustawieniach aplikacji.

### <a name="why-does-the-app-request-so-many-permissions"></a>Dlaczego aplikacji zażądać uprawnień tyle?
Poniżej przedstawiono pełną listę uprawnień, które może zostać wyświetlony monit o podanie i sposób ich użycia w aplikacji. Określone uprawnienia, dostępne są zależne od typu telefonu, do których masz.

* **Aparat fotograficzny**: używane do skanowania kodów QR po dodaniu pracy, szkole lub kont innych niż Microsoft.
* **Kontakty i phone**: używane w celu uproszczenia procesu przez wyszukiwanie istniejących kont w telefonie, gdy zalogujesz się przy użyciu swojego osobistego konta Microsoft.
* **SMS**: umożliwia upewnij się, że Twój numer telefonu jest zgodna z liczbą w rekordzie. Gdy zalogujesz się osobiste konto Microsoft po raz pierwszy.  Wyślemy wiadomość SMS z numerem telefonu którego pobrano aplikację zawierającą kod weryfikacyjny 6-8 cyfr. Pytania, możesz znaleźć tego kodu i wprowadź go w aplikacji, a nie został znaleziony w wiadomości tekstowej dla Ciebie.
* **Rysowanie w innych aplikacjach**: otrzymasz powiadomienie weryfikującym tożsamość użytkownika jest również wyświetlany na dowolną aplikację, która może być uruchomiona.
* **Odbieranie danych z Internetu**: to uprawnienie jest wymagane do wysyłania powiadomień.
* **Uniemożliwić phone uśpiony**: Jeśli zarejestrować urządzenie w Twojej organizacji, organizacja może zmienić te zasady na telefonie.
* **Kontrolowanie wibrację**: można wybrać, czy chcesz wibrację zawsze po otrzymaniu powiadomienia, aby zweryfikować swoją tożsamość.
* **Korzystanie ze sprzętu odcisk palca**: niektóre kont służbowych wymagają dodatkowych numeru PIN przy każdym zweryfikować Twoją tożsamość. Aby ułatwić proces, firma Microsoft będzie można korzystać odcisku palca zamiast wprowadzania numeru PIN.
* **Wyświetlanie połączeń sieciowych**: podczas dodawania konta Microsoft, aplikacja wymaga połączenia sieć i internet.
* **Odczytywać zawartość magazynu**: to uprawnienie jest używana tylko, gdy zgłaszasz problem techniczny za pomocą ustawień aplikacji. Niektóre informacje z magazynu są zbierane zdiagnozować problem.
* **Pełny dostęp do sieci**: to uprawnienie jest wymagane do wysyłania powiadomień do zweryfikowania Twojej tożsamości.
* **Uruchom przy uruchamianiu**: należy ponownie uruchomić telefon, to uprawnienie zadba nadal otrzymywać powiadomienia, aby zweryfikować swoją tożsamość.

### <a name="why-does-the-microsoft-authenticator-app-allow-you-to-approve-a-request-without-unlocking-the-device"></a>Dlaczego aplikację uwierzytelniania firmy Microsoft zezwala na zatwierdzenie żądania bez odblokowywania urządzenia?

Nie masz do odblokowania urządzenia w celu weryfikacji żądań zatwierdzenia, ponieważ jest potrzebne w celu potwierdzenia, czy masz telefon z Tobą. Włączono weryfikację dwuetapową wymaga potwierdzania dwie czynności — element, który znasz i operacją, której masz. Element, który znasz jest Twoje hasło. Operacją, której masz jest Twój telefon (skonfigurować przy użyciu aplikacji Microsoft Authenticator i zarejestrowany jako dowód MFA). W związku z tym o telefonu i zatwierdzania żądania spełnia kryteria dotyczące drugiego etapu uwierzytelniania.

### <a name="what-does-the-lock-icon-in-the-account-list-mean"></a>Co oznacza ikonę blokady na liście kont?

Ikona kłódki wskazuje, że urządzenie jest zarejestrowane w usłudze Azure AD i zarejestrowana na koncie. Rejestracja urządzeń dla systemu iOS odbywa się podczas rejestracji Microsoft Intune.

## <a name="next-steps"></a>Kolejne kroki

### <a name="contact-us"></a>Skontaktuj się z nami
Tutaj nie odpowiedzi na pytanie, chcemy poznać Twoją opinię. Przejdź do [forum aplikacji Microsoft Authenticator](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp) Zgłoś zapytanie i Uzyskaj pomoc od społeczności lub zostaw komentarz na tej stronie.


### <a name="related-topics"></a>Powiązane tematy
* [O weryfikacji dwuetapowej](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification) kont Microsoft
* [Wystąpił problem w trakcie weryfikacji dwuetapowej](../../../../multi-factor-authentication/end-user/multi-factor-authentication-end-user-troubleshoot.md) dla swojego konta firmowego lub szkolnego?
* [Użyj Authenticator firmy Microsoft, aby zalogować się w telefonie](microsoft-authenticator-app-phone-signin-faq.md)
