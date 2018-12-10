---
title: Samoobsługowe resetowanie hasła usługi Azure AD z ekranu logowania systemu Windows 10
description: W tym samouczku włączysz resetowanie hasła z ekranu logowania systemu Windows 10, aby zmniejszyć liczbę zgłoszeń do działu pomocy technicznej.
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 0c5554ca929cbd5231c99e568e987e6e0b7cf6eb
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52844842"
---
# <a name="tutorial-azure-ad-password-reset-from-the-login-screen"></a>Samouczek: resetowanie hasła usługi Azure AD z ekranu logowania

W tym samouczku umożliwisz użytkownikom resetowanie swoich haseł z ekranu logowania systemu Windows 10. Dzięki nowej aktualizacji systemu Windows 10 z kwietnia 2018 r. użytkownicy z urządzeniami **dołączonymi do usługi Azure AD** lub urządzeniami **hybrydowymi dołączonymi do usługi Azure AD** mogą skorzystać z linku „Resetuj hasło” znajdującego się na ich ekranach logowania. Kliknięcie tego linku powoduje przejście do znanego użytkownikom środowiska samoobsługowego resetowania hasła.

> [!div class="checklist"]
> * Konfigurowanie linku resetowania hasła przy użyciu usługi Intune
> * Opcjonalne konfigurowanie przy użyciu rejestru Windows
> * Interpretacja tego, co będą widzieli użytkownicy

## <a name="prerequisites"></a>Wymagania wstępne

* Klient systemu Windows 10 z aktualizacją z kwietnia 2018 lub nowszą będący urządzeniem:
   * [dołączonym do usługi Azure AD](../device-management-azure-portal.md) lub  
   * [hybrydowym dołączonym do usługi Azure AD](../device-management-hybrid-azuread-joined-devices-setup.md).
* Włączona funkcja samoobsługowego resetowania haseł w usłudze Azure AD.

## <a name="configure-reset-password-link-using-intune"></a>Konfigurowanie linku resetowania hasła przy użyciu usługi Intune

Wdrażanie zmiany konfiguracji w celu włączenia możliwości resetowania hasła z poziomu ekranu logowania przy użyciu usługi Intune jest metodą najbardziej elastyczną. Usługa Intune umożliwia wdrażanie zmiany konfiguracji dla określonej zdefiniowanej grupy maszyn. Ta metoda wymaga rejestracji urządzenia w usłudze Intune.

### <a name="create-a-device-configuration-policy-in-intune"></a>Tworzenie zasad konfiguracji urządzenia w usłudze Intune

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i kliknij pozycję **Intune**.
2. Utwórz nowy profil konfiguracji urządzenia, przechodząc kolejno do pozycji **Konfiguracja urządzenia** > **Profil** > **Utwórz profil**
   * Podaj znaczącą nazwę profilu
   * Opcjonalnie podaj znaczący opis profilu
   * Platforma: **Windows 10 lub nowsza**
   * Typ profilu: **Niestandardowy**

3. Skonfiguruj obszar **Ustawienia**
   * **Dodaj** następujące ustawienie OMA-URI, aby włączyć link resetowania hasła
      * Podaj znaczącą nazwę, aby ułatwić zrozumienie działania ustawienia
      * Opcjonalnie podaj znaczący opis ustawienia
      * Ustaw pozycję **OMA-URI** na wartość `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset`
      * Ustaw pozycję **Typ danych** na **Liczba całkowita**
      * Ustaw pozycję **Wartość**  na **1**
      * Kliknij przycisk **OK**.
   * Kliknij przycisk **OK**.
4. Kliknij przycisk **Utwórz**

### <a name="assign-a-device-configuration-policy-in-intune"></a>Przypisywanie zasad konfiguracji urządzenia w usłudze Intune

#### <a name="create-a-group-to-apply-device-configuration-policy-to"></a>Tworzenie grupy do zastosowania zasad konfiguracji urządzeń

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i kliknij pozycję **Azure Active Directory**.
2. Przejdź kolejno do pozycji **Użytkownicy i grupy** > **Wszystkie grupy** > **Nowa grupa**
3. Podaj nazwę grupy i w obszarze **Typ członkostwa** wybierz pozycję **Przypisane**
   * W obszarze **Członkowie** wybierz urządzenia z systemem Windows 10 dołączone do usługi Azure AD, do których chcesz zastosować zasady.
   * Kliknij pozycję **Wybierz**
4. Kliknij przycisk **Utwórz**

Więcej informacji na temat tworzenia grup można znaleźć w artykule [Zarządzanie dostępem do zasobów za pomocą grup usługi Azure Active Directory](../fundamentals/active-directory-manage-groups.md).

#### <a name="assign-device-configuration-policy-to-device-group"></a>Przypisywanie zasad konfiguracji urządzeń do grupy urządzeń

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i kliknij pozycję **Intune**.
2. Znajdź utworzony wcześniej profil konfiguracji urządzenia, przechodząc kolejno do pozycji **Konfiguracja urządzenia** > **Profile** > kliknij utworzony wcześniej profil
3. Przypisz profil do grupy urządzeń 
   * Kliknij kolejno pozycje **Przypisania** > w obszarze **Uwzględnianie** > **Wybierz grupy do uwzględnienia**
   * Wybierz utworzoną wcześniej grupę, a następnie kliknij pozycję **Wybierz**
   * Kliknij pozycję **Zapisz**

   ![Przypisanie][Assignment]

Zasady konfiguracji urządzenia zostały teraz utworzone i przypisane w celu włączenia linku resetowania hasła na ekranie logowania przy użyciu usługi Intune.

## <a name="configure-reset-password-link-using-the-registry"></a>Konfigurowanie linku resetowania hasła przy użyciu rejestru

1. Zaloguj się do komputera PC z systemem Windows przy użyciu poświadczeń administracyjnych
2. Uruchom **regedit** jako administrator
3. Ustaw poniższy klucz rejestru
   * `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      * `"AllowPasswordReset"=dword:00000001`

## <a name="what-do-users-see"></a>Co widzą użytkownicy

Teraz, gdy zasady zostały skonfigurowane i przypisane, co zmienia się dla użytkownika? Skąd użytkownicy będą wiedzieć, że mogą zresetować swoje hasło na ekranie logowania?

![LoginScreen][LoginScreen]

Gdy użytkownicy próbują się zalogować, widzą teraz link resetowania, który otwiera środowisko samoobsługowego resetowania hasła na ekranie logowania. Ta funkcja umożliwia użytkownikom zresetowanie hasła bez konieczności uzyskiwania dostępu do przeglądarki internetowej przy użyciu innego urządzenia.

Wskazówki dotyczące używania tej funkcji będzie można znaleźć w artykule [Reset your work or school password (Resetowanie hasła służbowego)](../user-help/active-directory-passwords-update-your-own-password.md#reset-password-at-sign-in)

Dziennik inspekcji usługi Azure AD zawiera informacje dotyczące adresu IP i typu klienta, które są powiązane z żądaniem resetowania hasła.

![Przykład resetowania hasła na ekranie logowania w dzienniku inspekcji usługi Azure AD](media/tutorial-sspr-windows/windows-sspr-azure-ad-audit-log.png)

## <a name="limitations"></a>Ograniczenia

Podczas testowania tej funkcjonalności za pomocą funkcji Hyper-V link „Resetuj hasło” nie jest wyświetlany.

* Przejdź do maszyny wirtualnej używanej do testowania, kliknij pozycję **Widok**, a następnie usuń zaznaczenie pola wyboru **Sesja rozszerzona**.

Podczas testowania tej funkcjonalności za pomocą pulpitu zdalnego lub rozszerzonej sesji maszyny wirtualnej link „Resetuj hasło” nie jest wyświetlany.

* Resetowanie hasła nie jest obecnie obsługiwane z poziomu pulpitu zdalnego.

Jeśli zasady wymagają naciśnięcia klawiszy Ctrl+Alt+Del lub powiadomienia na ekranie blokady są wyłączone, pozycja **Resetuj hasło** nie będzie działać.

Wiadomo, że następujące ustawienia zasad zakłócają możliwość resetowania haseł

   * Ustawienie HideFastUserSwitching jest włączone lub ustawione na wartość 1
   * Ustawienie DontDisplayLastUserName jest włączone lub ustawione na wartość 1
   * Ustawienie NoLockScreen jest włączone lub ustawione na wartość 1
   * Ustawienie EnableLostMode jest określone na urządzeniu
   * Plik Explorer.exe został zastąpiony niestandardową powłoką

Jeśli Twoje maszyny z systemem Windows 10 znajdują się za serwerem proxy lub zaporą, ruch HTTPS (443) na adresy passwordreset.microsoftonline.com i ajax.aspnetcdn.com powinien być dozwolony.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli zdecydujesz, że nie chcesz już korzystać z funkcji, które zostały skonfigurowane w ramach tego samouczka, usuń utworzony profil konfiguracji urządzenia usługi Intune lub klucz rejestru.

## <a name="next-steps"></a>Następne kroki

W tym samouczku umożliwiono użytkownikom resetowanie swoich haseł z ekranu logowania systemu Windows 10. Przejdź do następnego samouczka, aby zobaczyć, jak można zintegrować usługę Azure Identity Protection ze środowiskami samoobsługowego resetowania haseł i uwierzytelniania wieloskładnikowego.

> [!div class="nextstepaction"]
> [Ocena ryzyka podczas logowania](tutorial-risk-based-sspr-mfa.md)

[Assignment]: ./media/tutorial-sspr-windows/profile-assignment.png "Przypisywanie zasad konfiguracji urządzenia usługi Intune do grupy urządzeń z systemem Windows 10"
[LoginScreen]: ./media/tutorial-sspr-windows/logon-reset-password.png "Link resetowania hasła na ekranie logowania systemu Windows 10"
