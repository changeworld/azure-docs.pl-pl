---
title: Typowe problemy z uwierzytelnianiem dwuskładnikowym konta — Usługa Azure AD
description: Rozwiązania niektórych z najczęstszych problemów z weryfikacją dwuskładnikową oraz konta służbowego.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: curtand
ms.reviewer: kexia
ms.openlocfilehash: 1703853f42b52dccc80fdfadaa09b67e18a19db8
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632893"
---
# <a name="common-problems-with-two-factor-verification-and-your-work-or-school-account"></a>Typowe problemy z weryfikacją dwuskładnikową i kontem służbowym

Gdy organizacja usługi Azure Active Directory (Azure AD) włącza weryfikację dwuskładnikową, logowanie do konta służbowego lub szkolnego wymaga kombinacji nazwy użytkownika, hasła oraz urządzenia przenośnego lub telefonu. Jest bezpieczniejsza niż tylko hasło, opierając się na dwóch formach uwierzytelniania: czymś, co wiesz i czymś, co masz przy sobie. Weryfikacja dwuskładniowa może pomóc powstrzymać złośliwych hakerów przed podszywaniem się pod Ciebie, ponieważ nawet jeśli mają twoje hasło, szanse są takie, że nie mają twojego urządzenia.

<center>

![Obraz metod uwierzytelniania koncepcyjnego](../authentication/media/concept-mfa-howitworks/methods.png)</center>

Istnieją pewne typowe problemy z weryfikacją dwuskładnikową, które wydają się zdarzać częściej niż ktokolwiek z nas by chciał. Zebraliśmy ten artykuł, aby rozwiązać najczęstsze problemy i niektóre możliwe poprawki.

>[!Important]
>Jeśli jesteś administratorem, możesz znaleźć więcej informacji na temat konfigurowania środowiska usługi Azure AD i zarządzania nim w [dokumentacji usługi Azure AD.](https://docs.microsoft.com/azure/active-directory)
>
>Ta zawartość jest również przeznaczona wyłącznie do użytku z kontem służbowym lub szkolnym, alain@contoso.comktóre jest kontem udostępnionym przez organizację (na przykład). Jeśli masz problemy z weryfikacją dwuskładnikową i osobistym kontem Microsoft, które danielle@outlook.comjest kontem skonfigurowanym dla siebie (na przykład ), zobacz [Włączanie lub wyłączanie weryfikacji dwuskładnikowej dla konta Microsoft.](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off)

## <a name="i-dont-have-my-mobile-device-with-me"></a>Nie mam przy sobie urządzenia mobilnego

Zdarza się. Urządzenie mobilne zostało pozostawione w domu, a teraz nie możesz używać telefonu do weryfikacji, kim jesteś. Jeśli wcześniej dodano inną metodę logowania się do konta, taką jak telefon biurowy, teraz możesz użyć tej metody. Jeśli nigdy nie dodano dodatkowej metody weryfikacji, skontaktuj się z działem pomocy technicznej organizacji i poproś o pomoc w powrocie do konta.

### <a name="to-sign-in-to-your-work-or-school-account-using-another-verification-method"></a>Aby zalogować się na konto służbowe przy użyciu innej metody weryfikacji

1. Zaloguj się na swoje konto, ale wybierz link **Zaloguj w inny sposób** na stronie weryfikacji **dwuskładnikowej.**

    ![Zmienianie metody weryfikacji logowania](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    Jeśli nie widzisz linku **Zaloguj w inny sposób,** oznacza to, że nie skonfigurowałeś żadnych innych metod weryfikacji. Musisz skontaktować się z administratorem, aby uzyskać pomoc w zalogowaniu się na konto.

2. Wybierz alternatywną metodę weryfikacji i kontynuuj proces weryfikacji dwuskładnikowej.

## <a name="i-lost-my-mobile-device-or-it-was-stolen"></a>Zgubiłem urządzenie mobilne lub zostało skradzione

Jeśli zgubiłeś lub skradziono urządzenie mobilne, możesz zalogować się przy użyciu innej metody lub poprosić pomoc techniczną organizacji o wyczyszczenie ustawień. Zdecydowanie zalecamy poinformowanie działu pomocy technicznej organizacji, czy telefon został zgubiony lub skradziony, aby można było wypowiedzieć odpowiednie aktualizacje na Twoim koncie. Po wyczyszczeniu ustawień zostanie wyświetlony monit o zarejestrowanie się w [celu weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-first-time.md) przy następnym loguchieniu.

## <a name="im-not-getting-the-verification-code-sent-to-my-mobile-device"></a>Nie odaję kodu weryfikacyjnego wysłanego na moje urządzenie mobilne

Nieuchwyt kodu weryfikacyjnego jest częstym problemem i jest zazwyczaj związane z urządzeniem przenośnym i jego ustawieniami. Kilka możliwych rzeczy do wypróbowania:

Spróbuj tego | Informacje o wskazówkach
--------- | ------------
Ponowne uruchamianie urządzenia mobilnego | Czasami urządzenie po prostu wymaga odświeżenia. Ponowne uruchomienie urządzenia kończy wszystkie procesy lub usługi działające w tle, które są aktualnie uruchomione i może powodować problemy, wraz z odświeżeniem podstawowych składników urządzenia, ponownym uruchomieniem ich w przypadku awarii w pewnym momencie.
Sprawdź, czy twoje informacje zabezpieczające są poprawne | Upewnij się, że informacje o metodzie weryfikacji zabezpieczeń są dokładne, zwłaszcza numery telefonów. Jeśli umieścisz niewłaściwy numer telefonu, wszystkie alerty zostaną przesunie się do tego nieprawidłowego numeru. Na szczęście ten użytkownik nie będzie mógł nic zrobić z alertami, ale również nie pomoże Ci zalogować się na swoje konto. Aby upewnić się, że informacje są poprawne, zapoznaj się z instrukcjami w artykule [Zarządzanie ustawieniami metody weryfikacji dwuskładnikowej.](multi-factor-authentication-end-user-manage-settings.md)
Sprawdź, czy powiadomienia są włączone | Upewnij się, że na urządzeniu przenośnym są włączone powiadomienia i że wybrano metodę powiadamiania, która umożliwia połączenia telefoniczne, aplikację uwierzytelniania i aplikację do obsługi wiadomości (w przypadku wiadomości tekstowych) wysyłanie widocznych powiadomień o alertach na urządzenie przenośne.
Upewnij się, że masz sygnał urządzenia i połączenie z Internetem | Upewnij się, że połączenia telefoniczne i wiadomości tekstowe docierają do urządzenia mobilnego. Zadzwoń do znajomego i wyślesz ci wiadomość tekstową, aby upewnić się, że otrzymasz oba. Jeśli tego nie zrobisz, najpierw sprawdź, czy urządzenie mobilne jest włączone. Jeśli urządzenie jest włączone, ale nadal nie otrzymujesz połączenia lub SMS-a, najprawdopodobniej jest to problem z siecią i musisz porozmawiać z dostawcą. Jeśli często występują problemy związane z sygnałem, zalecamy zainstalowanie i używanie [aplikacji Microsoft Authenticator](user-help-auth-app-download-install.md) na urządzeniu przenośnym. Aplikacja uwierzytelniająca może generować losowe kody zabezpieczeń dla logowania, bez konieczności podłączania sygnału komórkowego lub połączenia internetowego.
Wyłącz nie przeszkadzać | Upewnij się, że nie włączyłeś funkcji **Nie przeszkadzać** na urządzeniu mobilnym. Gdy ta funkcja jest włączona, powiadomienia nie mogą ostrzegać o tym na urządzeniu mobilnym. Instrukcje dotyczące wyłączania tej funkcji można znaleźć w instrukcji obsługi urządzenia mobilnego.
Odblokowywanie numerów telefonów | W Stanach Zjednoczonych połączenia głosowe firmy Microsoft pochodzą z następujących numerów: +1 (866) 539 4191, +1 (855) 330 8653 i +1 (877) 668 6536.
Sprawdzanie ustawień związanych z baterią | Ten wydaje się nieco dziwne na powierzchni, ale jeśli już skonfigurować optymalizację baterii, aby zatrzymać rzadziej używane aplikacje z pozostają aktywne w tle, system powiadomień najprawdopodobniej został naruszony. Aby spróbować rozwiązać ten problem, wyłącz optymalizację baterii aplikacji uwierzytelniania i aplikacji do obsługi wiadomości, a następnie spróbuj ponownie zalogować się na konto.

## <a name="im-not-getting-prompted-for-my-second-verification-information"></a>Nie dostajesz monitu o podanie moich drugich informacji weryfikacyjnych

Jeśli zalogujesz się na konto służbowe przy użyciu nazwy użytkownika i hasła, ale nie zostałeś poproszony o dodatkowe informacje weryfikacyjne zabezpieczeń, być może urządzenie nie zostało jeszcze skonfigurowane. Urządzenie mobilne musi być skonfigurowane do pracy z określoną dodatkową metodą weryfikacji zabezpieczeń. Aby upewnić się, że urządzenie mobilne jest włączone i jest dostępne do użycia z metodą weryfikacji, zobacz artykuł [Zarządzanie ustawieniami metody weryfikacji dwuskładnikowej.](multi-factor-authentication-end-user-manage-settings.md) Jeśli wiesz, że nie skonfigurowano urządzenia ani konta, możesz to zrobić teraz, wykonując czynności opisane w artykule [Konfigurowanie mojego konta dla weryfikacji dwuetapowej.](multi-factor-authentication-end-user-first-time.md)

## <a name="i-got-a-new-phone-number-and-i-want-to-add-it"></a>Mam nowy numer telefonu i chcę go dodać

Jeśli otrzymałeś nowy numer telefonu, musisz zaktualizować szczegóły metody weryfikacji zabezpieczeń, aby monity weryfikacyjne zostały wyświetlone we właściwej lokalizacji. Aby zaktualizować metodę weryfikacji, wykonaj czynności opisane w sekcji **Dodaj lub zmień numer telefonu** w artykule Zarządzanie [ustawieniami dwuskładnikowej metody weryfikacji.](multi-factor-authentication-end-user-manage-settings.md#add-or-change-your-phone-number)

## <a name="i-got-a-new-mobile-device-and-i-want-to-add-it"></a>Mam nowe urządzenie mobilne i chcę go dodać

Jeśli masz nowe urządzenie mobilne, musisz skonfigurować je tak, aby działało z weryfikacją dwuskładnikową. Jest to rozwiązanie wieloetapowe:

1. Skonfiguruj urządzenie do pracy z kontem służbowym, wykonując czynności opisane w artykule [Konfigurowanie konta dla weryfikacji dwuetapowej.](multi-factor-authentication-end-user-first-time.md)

1. Zaktualizuj informacje o koncie i urządzeniu na stronie **Dodatkowa weryfikacja zabezpieczeń,** usuwając stare urządzenie i dodając nowe. Aby uzyskać więcej informacji, zobacz artykuł [Zarządzanie ustawieniami metody weryfikacji dwuskładnikowej.](multi-factor-authentication-end-user-manage-settings.md)

Kroki opcjonalne:

- Pobierz, zainstaluj i skonfiguruj aplikację Microsoft Authenticator na urządzeniu przenośnym, wykonując czynności opisane w [artykule Pobieranie i instalowanie aplikacji Microsoft Authenticator.](user-help-auth-app-download-install.md)

- Włącz weryfikację dwuskładnikową dla zaufanych urządzeń, wykonując czynności opisane w sekcji **Włącz monity weryfikacji dwuskładnikowej na zaufanym urządzeniu** w artykule [Zarządzanie ustawieniami metody weryfikacji dwuskładnikowej.](multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device)

## <a name="im-having-problems-signing-in-on-my-mobile-device-while-traveling"></a>Mam problemy z logowaniem się na urządzeniu mobilnym podczas podróży

Korzystanie z metody weryfikacji związanej z urządzeniem mobilnym, takiej jak wiadomości tekstowe, podczas pobytu w lokalizacji międzynarodowej może być trudniejsze. Możliwe jest również, że urządzenie mobilne może spowodować naliżenie opłat roamingowych. W tej sytuacji zaleca się użycie aplikacji Microsoft Authenticator z opcją łączenia się z hotspotem Wi-Fi. Aby uzyskać więcej informacji na temat pobierania, instalowania i konfigurowania aplikacji Microsoft Authenticator na urządzeniu przenośnym, zobacz artykuł [Pobieranie i instalowanie aplikacji Microsoft Authenticator.](user-help-auth-app-download-install.md)

## <a name="i-cant-get-my-app-passwords-to-work"></a>Nie mogę uzyskać haseł aplikacji do działania

Hasła aplikacji zastępują zwykłe hasło dla starszych aplikacji klasycznych, które nie obsługują weryfikacji dwuskładnikowej. Najpierw upewnij się, że hasło zostało wpisane poprawnie. Jeśli to nie rozwiąże, spróbuj utworzyć nowe hasło aplikacji dla aplikacji, wykonując kroki opisane w **artykule Tworzenie i usuwanie haseł aplikacji przy użyciu** sekcji Portal Moje aplikacje w [artykule Zarządzanie hasłami aplikacji dla weryfikacji dwuetapowej.](multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page)

## <a name="i-cant-turn-two-factor-verification-off"></a>Nie mogę wyłączyć weryfikacji dwuskładnikowej

Jeśli korzystasz z weryfikacji dwuskładnikowej na koncie służbowym (na przykład), najprawdopodobniej oznacza to, że twoja organizacja zdecydowała, alain@contoso.comże musisz użyć tej dodatkowej funkcji zabezpieczeń. Ponieważ organizacja zdecydowała, że należy użyć tej funkcji, nie ma możliwości jej indywidualnego wyłączenia. Jeśli jednak korzystasz z weryfikacji dwuskładnikowej alain@outlook.comza pomocą konta osobistego, na przykład, masz możliwość włączania i wyłączania tej funkcji. Aby uzyskać instrukcje dotyczące kontrolowania weryfikacji dwuskładnikowej dla kont osobistych, zobacz [Włączanie lub wyłączanie weryfikacji dwuskładnikowej dla konta Microsoft.](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off)

Jeśli nie można wyłączyć weryfikacji dwuskładnikowej, może to być również spowodowane domyślnymi ustawieniami zabezpieczeń, które zostały zastosowane na poziomie organizacji. Aby uzyskać więcej informacji na temat wartości domyślnych zabezpieczeń, zobacz [Co to są defulta zabezpieczeń?](../fundamentals/concept-fundamentals-security-defaults.md)

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Nie znalazłem odpowiedzi na mój problem

Jeśli po wykonaniu tych czynności nadal występują problemy, skontaktuj się z działem pomocy technicznej organizacji, aby uzyskać pomoc.

## <a name="related-articles"></a>Pokrewne artykuły:

- [Zarządzanie ustawieniami metody weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-manage-settings.md)

- [Konfigurowanie konta do weryfikacji dwuetapowej](multi-factor-authentication-end-user-first-time.md)

- [Microsoft Authenticator app FAQ (Aplikacja Microsoft Authenticator — często zadawane pytania)](user-help-auth-app-faq.md)
