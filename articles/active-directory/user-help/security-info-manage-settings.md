---
title: Zarządzanie zabezpieczających — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzać informacjom zabezpieczającym, w tym sposobu pracy z ustawieniami weryfikacji dwuetapowej.
services: active-directory
author: eross-msft
manager: mtillman
ms.reviewer: sahenry
ms.service: active-directory
ms.component: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.openlocfilehash: 4cd88490118343a9a00dbd919d7820f0334df92e
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2018
ms.locfileid: "53016150"
---
# <a name="manage-your-security-info-preview"></a>Zarządzanie zabezpieczających (wersja zapoznawcza)

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

Można użyć swoje informacje zabezpieczające, zaloguj się do swojego konta firmowego lub szkolnego lub zresetowania hasła.

Podczas logowania, w zależności od ustawień organizacji, można napotkać pole wyboru, które mówi **nie pytaj ponownie przez X dni**. Pozwala to pole wyboru użytkownik pozostanie zalogowany do Twojego urządzenia przez liczbę dni, administrator umożliwia, bez konieczności reverification.

## <a name="change-your-info"></a>Zmiana informacji zabezpieczających
Można zaktualizować lub Dodaj informacje zabezpieczające lub zmienić domyślne, w oparciu o dozwolona przez administratora i Twojej organizacji.

### <a name="to-change-your-info"></a>Aby zmienić informacji zabezpieczających

1. Zaloguj się do swojego konta firmowego lub szkolnego.

2. Przejdź do myapps.microsoft.com, wybierz nazwę w prawym górnym rogu strony, a następnie wybierz **profilu**.

3. W **Zarządzanie kontem** wybierz opcję **Edytuj informacje zabezpieczające**.

    ![Ekran profil z wyróżnionym linkiem informacje zabezpieczeń edycji](media/security-info/security-info-profile.png)

4. Użyj metodą domyślną zatwierdzać dostęp i aby wyświetlić bieżące szczegóły informacje zabezpieczeń, jeśli administrator skonfigurował to środowisko w Twojej organizacji.

5. Na **zabezpieczyć swoje konto** strony, możesz:

    - Wybierz **Dodaj informacje zabezpieczające** można dodać dodatkowe metody.

    - Wybierz **zmienić ustawienie domyślne** Aby zmienić metodę domyślną.

    - Wybierz **ołówka** ikon umieszczonych obok nazw istniejącą metodę, aby zaktualizować swoje informacje.

    ![Ekran informacje zabezpieczeń z istniejących, można edytować informacje](media/security-info/security-info-edit.png)

6. Po dokonaniu zmian, możesz opuścić tę stronę, a zmiany zostaną zapisane.

Jeśli nie widzisz te opcje, lub nie uda się uzyskać dostęp do strony myapps.microsoft.com, jest to możliwe, że Twoja organizacja korzysta z niestandardowych opcji lub niestandardową stronę. Należy skontaktować się z administratorem, aby uzyskać dalszą pomoc.

## <a name="manage-your-security-info-for-a-lost-or-potentially-compromised-device"></a>Zarządzanie swoje informacje zabezpieczające. w przypadku utraty lub mogą mieć złamane zabezpieczenia urządzenia

W przypadku utraty urządzenia lub urządzenie zostanie naruszone bezpieczeństwo, należy ponownie przeprowadzić proces weryfikacji dla wszystkich wcześniej zaufanych urządzeniach.

### <a name="to-manage-your-security-info-for-lost-or-potentially-compromised-devices"></a>Aby zarządzać zabezpieczających w przypadku utraty lub mogą mieć złamane zabezpieczenia urządzeń

1. Zaloguj się do swojego konta firmowego lub szkolnego.

2. Przejdź do myapps.microsoft.com, wybierz nazwę w prawym górnym rogu strony, a następnie wybierz **profilu**.

3. W **Zarządzanie kontem** wybierz opcję **zapomnij MFA na zapamiętanych urządzeniach**.
    
    Wybór oznacza to opcja, że musisz przejść przez proces usługi Multi-Factor Authentication (MFA) stanie się ponownie, po zalogowaniu.

    ![Ekran profil z wyróżnionym linkiem zapomnij](media/security-info/security-info-forget.png)

## <a name="common-problems-and-solutions-with-your-security-info"></a>Typowe problemy i rozwiązania za pomocą swoje informacje zabezpieczające

Ten artykuł pomaga rozwiązywać problemy z tym problemy związane z weryfikacji dwuetapowej zabezpieczających.

|Problem|Rozwiązanie|
|-------|--------|
|Nie mam telefonu przy użyciu elementu me|Jest to możliwe, że nie masz telefon przy sobie przez cały czas, ale że będziesz nadal chcesz zalogować się do pracy konta firmowego lub szkolnego. Aby rozwiązać ten problem, można zalogować się przy użyciu innej metody uwierzytelniania, która nie wymaga Twój telefon, takie jak numer telefonu biurowego. Aby dodać dodatkowe metody do swoich informacji zabezpieczających, możesz wykonać kroki opisane w [zmiany informacji zabezpieczających](#change-your-info) sekcji.|
|Utratą Mój telefon lub został on skradziony|Niestety może nastąpić utraty telefonu lub kradzieży. W takiej sytuacji zaleca się, że umożliwisz swoją organizację, dzięki czemu działu IT mogą resetować swoje hasła aplikacji i wyczyść dowolne zapamiętanych urządzeń z listy zaufanych urządzeń. Można również zapomnieć zaufanych urządzeniach, wykonując kroki opisane w [Zarządzanie swoje informacje zabezpieczające. w przypadku utraty lub mogą mieć złamane zabezpieczenia urządzenia](#manage-your-security-info-for-a-lost-or-potentially-compromised-device) sekcji.|
|Stało się nowy numer telefonu|Istnieją dwa sposoby, aby rozwiązać ten problem. Możesz zalogować się przy użyciu innej metody uwierzytelniania, która nie wymaga Twój numer telefonu, np. wiadomości e-mail, lub jeśli nie jest to opcja, możesz skontaktować się ze swojej organizacji przez pracowników wsparcia informatycznego i je wyczyścić ustawienia. Aby dodać dodatkowe metody do swoich informacji zabezpieczających, możesz wykonać kroki opisane w [zmiany informacji zabezpieczających](#change-your-info) sekcji.|
|Domyślną metodę jest nieprawidłowy|Należy zaktualizować metodą domyślną w opcjach zabezpieczeń. Aby uzyskać szczegółowe informacje, możesz przejść do [zmiany informacji zabezpieczających](#change-your-info) sekcji.|
|Czy mogę mnie, nie odbiera tekstu, lub zadzwoń na moim urządzeniu przenośnym|Jeśli został pomyślnie odebrany teksty lub połączeń telefonicznych do Twojego urządzenia przenośnego w przeszłości, ten problem jest najbardziej prawdopodobną z dostawcą telefonu, a nie konta. Upewnij się, że masz sygnał dobrych komórki i że możesz odbierać wiadomości SMS oraz połączeń telefonicznych. Możesz poprosić znajomego do połączenia lub wiadomości SMS jako test.<br><br>Jeśli można odbierać wiadomości tekstowych i telefon, ale nadal nie zostały uzyskane powiadomienia, możesz spróbować przy użyciu innej metody. Możesz dodać dodatkowe metody do swoich informacji zabezpieczających, wykonując kroki opisane w [zmiany informacji zabezpieczających](#change-your-info) sekcji. Jeśli nie masz innej metody, aby dodać można skontaktuj się z działem pomocy technicznej Twojej firmy i poproś go, aby wyczyścić ustawienia, dzięki czemu możesz skonfigurować swoje metody podczas następnego logowania.<br><br>Ze względu na odbiór zły komórki są często mają problemy, firma Microsoft zaleca użyj aplikacji Microsoft Authenticator na swoim urządzeniu przenośnym. Aplikacja może generować kody zabezpieczeń losowych, których używasz do logowania w i kody te nie wymagają dowolnego połączenia internetowego lub sygnału komórki. Aby uzyskać więcej informacji na temat aplikacji Microsoft Authenticator, zobacz [Rozpoczynanie pracy z aplikacją Microsoft Authenticator](https://docs.microsoft.com/azure/multi-factor-authentication/end-user/microsoft-authenticator-app-how-to) artykułu.|
|Brak opcji dostępnych w tej tabeli ma rozwiązać mojego problemu|Jeśli sprawdzone te kroki rozwiązywania problemów, ale nadal są uruchomione w problemów. Skontaktuj się z działem pomocy technicznej Twojej firmy, należy je mogą pomóc Ci.|

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o informacje zabezpieczające w [zabezpieczeniami informacji (wersja zapoznawcza) — omówienie](user-help-security-info-overview.md).

- Dowiedz się więcej o weryfikacji dwuetapowej w [Przegląd weryfikacji dwuetapowej](user-help-two-step-verification-overview.md) artykułu. 

- Wykonaj jedną z następujących porad artykułów, aby dowiedzieć się więcej o tym, jak skonfigurować urządzenie w obszarze zabezpieczeń informacji:

    - [Set up security info to use an authenticator app](security-info-setup-auth-app.md) (Konfigurowanie informacji zabezpieczających w celu korzystania z aplikacji Authenticator)

    - [Set up security info to use phone calls](security-info-setup-phone-number.md) (Konfigurowanie informacji zabezpieczających w celu korzystania z połączeń telefonicznych)

    - [Ustawiane informacje zabezpieczające, aby używać wiadomości SMS](security-info-setup-text-msg.md)

    - [Set up security info to use email](security-info-setup-email.md) (Konfigurowanie informacji zabezpieczających w celu korzystania z poczty e-mail)

    - [Skonfiguruj informacje zabezpieczające na użycie tych pytań zabezpieczających](security-info-setup-questions.md)

- Resetowanie hasła, jeśli został zgubienia lub zapomnienia, z [portal resetowania haseł](https://passwordreset.microsoftonline.com/) lub postępuj zgodnie z instrukcjami w [Resetowanie hasła służbowego lub szkolnego](user-help-reset-password.md) artykułu.

- Pobierz rozwiązania problemu, wskazówki i pomoc dotyczącą problemów z logowania w [nie zalogować się do swojego konta Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artykułu.