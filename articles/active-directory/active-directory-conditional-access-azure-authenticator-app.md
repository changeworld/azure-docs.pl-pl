---
title: Aplikacja Azure Authenticator dla systemu Android | Dokumentacja firmy Microsoft
description: Aplikacja Microsoft Azure Authenticator może służyć do logowania na dostęp do zasobów roboczych. Aplikacji Azure Authenticator powiadomi użytkownika żądania oczekujące weryfikacji dwuskładnikowego poprzez wyświetlenie alert do urządzenia przenośnego.
services: active-directory
documentationcenter: ''
author: femila
manager: swadhwa
editor: ''
ms.assetid: b7ceca0d-5c9d-45c4-942c-b3a9b6bad36c
ms.service: active-directory
ms.component: protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: femila
ROBOTS: NOINDEX, NOFOLLOW
ms.openlocfilehash: 5c34219d01c8670102df40d9157cb0deff74b8e4
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34724289"
---
# <a name="azure-authenticator-for-android"></a>Aplikacja Azure Authenticator dla systemu Android
IT administrator może zalecić przy użyciu programu Microsoft Azure Authenticator logowanie na dostęp do zasobów roboczych. Ta aplikacja zawiera te dwie opcje logowania:

* Uwierzytelnianie wieloskładnikowe umożliwia zabezpieczenie konta służbowego w trakcie weryfikacji dwuetapowej. Możesz Zaloguj się przy użyciu znać (na przykład hasła) i chronić konto jeszcze bardziej dzięki czemuś, co masz (klucza zabezpieczeń z tej aplikacji). Aplikacji Azure Authenticator powiadomi użytkownika żądania oczekujące weryfikacji dwuskładnikowego poprzez wyświetlenie alert do urządzenia przenośnego. Należy po prostu Wyświetl żądanie w aplikacji i wybierz weryfikację lub ją anulować. Alternatywnie może pojawić się prośba o wprowadzenie kodu dostępu wyświetlany w aplikacji.
* Konto służbowe umożliwia przekształcić zaufanego urządzenia z systemem Android telefon lub tablet i zapewnić pojedynczego logowania jednokrotnego (SSO) do aplikacji firmowych. IT administrator może wymagać dodania konta służbowego, aby uzyskać dostęp do zasobów firmy. Logowania jednokrotnego pozwala zalogować się raz i móc automatycznie korzystać ze we wszystkich aplikacjach, które firma ma udostępnionych użytkownikowi.

## <a name="installing-the-azure-authenticator-app"></a>Instalowanie aplikacji Azure Authenticator
Aplikacja Azure Authenticator można zainstalować ze sklepu Google Play.
Z instrukcjami, aby dodać konta służbowego z urządzenia z systemem innym niż Samsung Android - vs urządzenia Samsung Android są nieco inne. Poniżej przedstawiono instrukcje dla obu.

## <a name="adding-the-work-account-from-samsung-android-device"></a>Dodawanie konta służbowego z urządzenia Samsung Android
### <a name="adding-the-work-account-through-the-app-home-screen"></a>Dodawanie konta służbowego do ekranu głównego aplikacji
Poniższe instrukcje dotyczą Samsung GS3 i telefony lub Uwaga2 i wyższych tablety.

1. Ekranu głównego aplikacji należy zaakceptować umowę licencyjną użytkownika (oprogramowania EULA).
2. Na ekranie aktywować konto, kliknij z prawej strony i wybierz menu kontekstowe **konta służbowego**.
3. Na dodawanie konta ekranu, wybierz ** konta Work.
4. Na ekranie administratora urządzenia Aktywuj kliknij **Aktywuj**.
5. Na ekranie zasady zachowania poufności informacji, zaznacz pole wyboru, a następnie kliknij przycisk **Potwierdź**.
6. Na ekranie dołączanie do miejsca pracy, wprowadź userID podane w organizacji, a następnie kliknij przycisk **Join**.
7. Aby zalogować się do aplikacji Azure Authenticator, wprowadź swoje konto organizacyjne i hasło, a następnie kliknij przycisk **Zaloguj**.
8. Dalej ekranu, który zawiera informacje na temat uwierzytelniania wieloskładnikowego (MFA) na dodaniu zabezpieczeń i jest opcjonalne. Zostanie wyświetlony następujący ekran, jeśli konto służbowe wymaga uwierzytelniania dwuskładnikowego do tworzenia konta służbowego. Instrukcje dodatkową weryfikację konta.
9. Dołączanie ekranu wyświetli komunikat "**dołączenie do miejsca pracy**". Aplikacja Azure authenticator próbuje dołączyć urządzenie do miejsca pracy.
10. Na następnym ekranie powinna zostać wyświetlona dołączone do wiadomości.

> [!NOTE]
> Konto pojedynczego pracy są dozwolone na urządzeniu.
> 
> 

### <a name="adding-the-work-account-from-the-settings-menu"></a>Dodawanie konta służbowego z menu ustawień
Po zainstalowano aplikację Azure Authenticator, można również utworzyć konto służbowe z menedżerem systemu Android.

1. W menu Ustawienia, przejdź do **kont** i kliknij przycisk **Dodaj konto**.
2. Wykonaj kroki 3 – 10 w ramach procedury dodawania konta służbowego do ekranu głównego aplikacji, aby dodać konta służbowego.

## <a name="adding-the-work-account-from-a-non-samsung-android-device"></a>Dodawanie konta służbowego z urządzenia z systemem innym niż Samsung Android
### <a name="adding-the-work-account-through-the-app-home-screen"></a>Dodawanie konta służbowego do ekranu głównego aplikacji
1. Ekranu głównego aplikacji należy zaakceptować umowę licencyjną użytkownika (oprogramowania EULA).
2. Na ekranie aktywować konto, kliknij z prawej strony i wybierz menu kontekstowe **konta służbowego**.
3. Na ekranie konta kliknij **Dodaj konto**.
4. Jeśli zostanie wyświetlony ekran konta, kliknij przycisk **Dodaj konto**. Jeśli konto służbowe jest już utworzony wcześniej, zostanie wyświetlony synchronizacji ekran pokazujący istniejącego konta służbowego. Po prostu wybierając strzałkę wstecz do ekranu głównego, można zachować konta służbowego. Alternatywnie można wybrać konto, aby usunąć i ponownie utwórz nowy pracy konta na dołączanie ekran, wprowadź identyfikator userID udostępniane przez organizację i kliknij sprzężenia.
5. Aby zalogować się do aplikacji Azure Authenticator, wprowadź swoje konto organizacyjne i hasło, a następnie kliknij przycisk **Zaloguj**.
6. Dalej ekranu, który zawiera informacje na temat uwierzytelniania wieloskładnikowego (MFA) na dodaniu zabezpieczeń i jest opcjonalne. Zostanie wyświetlony następujący ekran, jeśli konto służbowe wymaga uwierzytelniania dwuskładnikowego do tworzenia konta służbowego. Instrukcje dodatkową weryfikację konta.
7. Kliknij przycisk **OK** na następnym ekranie. Nie należy zmieniać nazwę certyfikatu.
   komunikat "Dołączenia w miejscu pracy". Aplikacja Azure authenticator próbuje dołączyć urządzenie do miejsca pracy.
   Na następnym ekranie powinna zostać wyświetlona dołączone do wiadomości.

> [!NOTE]
> Konto pojedynczego pracy są dozwolone na urządzeniu.
> 
> 

Po zainstalowano aplikację Azure Authenticator, można również utworzyć konto służbowe z menedżerem systemu Android.

1. Z **ustawienia** menu, przejdź do konta i kliknij **Dodaj konto**.
2. Wykonaj kroki od 2 do 7 w procedurze, Dodawanie konta służbowego za pośrednictwem aplikacji macierzystego ekranu **, aby dodać konta służbowego.

### <a name="how-to-find-out-which-version-is-installed"></a>Jak sprawdzić, która wersja jest zainstalowana
1. Można sprawdzić, która wersja aplikacji Azure Authenticator i skojarzona usługa wersje są zainstalowane na urządzeniu.
2. W wyświetlonym menu kliknij **o**.
3. Na ekranie informacje są wyświetlane z usług aplikacji i wersji zainstalowanej na urządzeniu.

### <a name="sending-log-files-to-report-issues"></a>Wysyłanie plików dziennika, aby zgłosić problemy
1. Postępuj zgodnie ze wskazówkami w Microsoft Support zgłosić zdarzenie z aplikacji Azure Authenticator, uzyskać numer zdarzenia i Wyślij pliki dzienników przed przypisany numer zdarzenia w następujący sposób:
2. W wyświetlonym menu kliknij **rejestrowanie**.
3. Jeśli masz otwartego zdarzenia z Microsoft Support, Zanotuj liczbę zdarzeń (będzie on potrzebny do kolejnych kroków). Jeśli nie utworzono jeszcze zdarzenia pomocy technicznej i pomoc, wykonaj wskazówki na [Microsoft Support](https://support.microsoft.com/en-us/contactus) można otworzyć nowego incydentu.
4. Na ekranie rejestrowanie kliknij **Wyślij teraz**.
5. Wybierz dostawcę poczty e-mail, którego chcesz użyć.
6. Jeśli masz już otwartego zdarzenia Support firmy Microsoft, skontaktuj się z pracownikiem pomocy technicznej, przypisane do tego problemu, aby dowiedzieć się, jak wysyłać dane dziennika i mieć skojarzone ze zdarzeniem użytkownika. Z pracownikiem pomocy technicznej będzie dostarczać informacji o wierszu adresu i temat wiadomości e-mail. Jeśli nie masz już zdarzeniem pomocy technicznej, postępuj zgodnie ze wskazówkami w Support firmy Microsoft, aby otworzyć nowe zdarzenie.
7. Edytuj **do** wiersza i **podmiotu** wiersz z informacjami o rozwiązaniach otrzymanych od firmy Microsoft Support.
8. Aplikacja Azure Authenticator dołącza plik dziennika do wysyłania wiadomości e-mail. Opisz problem, który pojawia się zaktualizować listy adresatów (opcjonalnie) i wysyłania wiadomości e-mail.

### <a name="deleting-the-work-account-and-leaving-your-workplace"></a>Usunięcie konta służbowego i pozostawienie miejsca pracy
Można usunąć konta służbowego, utworzony w dowolnym momencie w następujący sposób:

**Aby usunąć konto służbowe z menu ustawień**

1. Wybierz z Menedżera kont **konta służbowego**.
2. Na ekranie konto służbowe w **ustawienia ogólne**, wybierz pozycję **sieci w miejscu pracy pozostaw ustawienia konta —**.
3. Wybierz **pozostaw** na **dołączania** ekranu.
4. Kliknij przycisk **OK** gdy zostanie wyświetlony komunikat "Czy na pewno chcesz opuścić miejsce pracy".
5. Dzięki temu, że usunięto konto służbowe z miejsca pracy.

> [!NOTE]
> Zaleca się, że nie należy używać opcji Usuń konto można usunąć konta służbowego, ponieważ ta opcja może nie działać w niektórych wcześniejszych wersjach systemu android.
> 
> 

## <a name="uninstalling-the-app"></a>Odinstalowywanie aplikacji
Na urządzeniu Samsung Android uprawnień administratora urządzenia przed należy usunąć w następujący sposób odinstalowywania 

1. Z **ustawienia**w obszarze **systemu**, wybierz pozycję **zabezpieczeń**.
2. D**evice administracji**, kliknij przycisk **Administratorzy urządzenia**. Upewnij się, że pole wyboru obok pozycji **Azure Authenticator** jest wyczyszczone.

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Jeśli widzisz **błąd magazynu kluczy**, może to być spowodowane brakiem blokady ekranu zestaw się przy użyciu numeru PIN. Aby obejść ten problem, Odinstaluj aplikację Azure Authenticator Konfigurowanie numeru PIN na potrzeby ekranu blokady i zainstaluj ponownie aplikację.

