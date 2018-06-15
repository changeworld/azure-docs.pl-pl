---
title: Samoobsługowe resetowanie hasła usługi Azure AD z ekranu logowania systemu Windows 10 | Microsoft Docs
description: Konfigurowanie resetowania hasła usługi Azure AD z ekranu logowania systemu Windows 10 w przypadku zapomnienia numeru PIN
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: get-started-article
ms.date: 04/27/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 701ab041eb603a1a06025d5bb43964880353ada9
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
ms.locfileid: "33867497"
---
# <a name="azure-ad-password-reset-from-the-login-screen"></a>Resetowanie hasła usługi Azure AD z ekranu logowania

Funkcja samoobsługowego resetowania hasła (SSPR, self-service password reset) usługi Azure AD została już wdrożona, ale użytkownicy nadal dzwonią do działu obsługi klienta, jeśli nie pamiętają hasła. Dzieje się tak, ponieważ nie mogą oni przejść do samoobsługowego resetowania hasła w przeglądarce internetowej.

Dzięki nowej aktualizacji systemu Windows 10 z kwietnia 2018 r. użytkownicy z urządzeniami **dołączonymi do usługi Azure AD** lub urządzeniami **hybrydowymi dołączonymi do usługi Azure AD** widzą na swoich ekranach logowania link „Resetuj hasło”, którego mogą użyć. Kliknięcie tego linku powoduje przejście do znanego użytkownikom środowiska samoobsługowego resetowania hasła.

Aby użytkownicy mogli resetować hasło usługi Azure AD z ekranu logowania systemu Windows 10, muszą zostać spełnione następujące wymagania:

* Aktualizacja systemu Windows 10 z kwietnia 2018 r. lub nowszy klient [dołączony do usługi Azure AD](../device-management-azure-portal.md) lub [hybrydowy dołączony do usługi Azure AD](../device-management-hybrid-azuread-joined-devices-setup.md).
* Włączona funkcja samoobsługowego resetowania haseł w usłudze Azure AD.
* Skonfiguruj i wdróż ustawienie, aby włączyć link resetowania hasła przy użyciu jednej z następujących metod:
   * [Profil konfiguracji urządzenia usługi Intune](tutorial-sspr-windows.md#configure-reset-password-link-using-intune)
   * [Klucz rejestru](tutorial-sspr-windows.md#configure-reset-password-link-using-the-registry)

## <a name="configure-reset-password-link-using-intune"></a>Konfigurowanie linku resetowania hasła przy użyciu usługi Intune

### <a name="create-a-device-configuration-policy-in-intune"></a>Tworzenie zasad konfiguracji urządzenia w usłudze Intune

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i wybierz pozycję **Intune**.
2. Utwórz nowy profil konfiguracji urządzenia, przechodząc kolejno do pozycji **Konfiguracja urządzenia** > **Profil** > **Utwórz profil**
   * Podaj znaczącą nazwę profilu
   * Opcjonalnie podaj znaczący opis profilu
   * Platforma: **Windows 10 lub nowsza**
   * Typ profilu: **Niestandardowy**

   ![CreateProfile][CreateProfile]

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

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i wybierz pozycję **Azure Active Directory**.
2. Przejdź kolejno do pozycji **Użytkownicy i grupy** > **Wszystkie grupy** > **Nowa grupa**
3. Podaj nazwę grupy i w obszarze **Typ członkostwa** wybierz pozycję **Przypisane**
   * W obszarze **Członkowie** wybierz urządzenia z systemem Windows 10 dołączone do usługi Azure AD, do których chcesz zastosować zasady.
   * Kliknij pozycję **Wybierz**
4. Kliknij przycisk **Utwórz**

Więcej informacji na temat tworzenia grup można znaleźć w artykule [Zarządzanie dostępem do zasobów za pomocą grup usługi Azure Active Directory](../active-directory-manage-groups.md).

#### <a name="assign-device-configuration-policy-to-device-group"></a>Przypisywanie zasad konfiguracji urządzeń do grupy urządzeń

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i wybierz pozycję **Intune**.
2. Znajdź utworzony wcześniej profil konfiguracji urządzenia, przechodząc kolejno do pozycji **Konfiguracja urządzenia** > **Profile** > kliknij utworzony wcześniej profil
3. Przypisz profil do grupy urządzeń 
   * Kliknij kolejno pozycje **Przypisania** > w obszarze **Uwzględnianie** > **Wybierz grupy do uwzględnienia**
   * Wybierz utworzoną wcześniej grupę, a następnie kliknij pozycję **Wybierz**
   * Kliknij pozycję **Zapisz**

   ![Przypisanie][Assignment]

Zasady konfiguracji urządzenia zostały teraz utworzone i przypisane w celu włączenia linku resetowania hasła na ekranie logowania przy użyciu usługi Intune.

## <a name="configure-reset-password-link-using-the-registry"></a>Konfigurowanie linku resetowania hasła przy użyciu rejestru

Zalecamy używanie tej metody tylko w celu testowania zmiany ustawienia.

1. Zaloguj się do urządzenia połączonego z usługą Azure AD przy użyciu poświadczeń administracyjnych
2. Uruchom **regedit** jako administrator
3. Ustaw poniższy klucz rejestru
   * `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      * `"AllowPasswordReset"=dword:00000001`

## <a name="what-do-users-see"></a>Co widzą użytkownicy

Teraz, gdy zasady zostały skonfigurowane i przypisane, co zmienia się dla użytkownika? Skąd użytkownicy będą wiedzieć, że mogą zresetować swoje hasło na ekranie logowania?

![LoginScreen][LoginScreen]

Gdy użytkownicy próbują się zalogować, widzą teraz link resetowania, który otwiera środowisko samoobsługowego resetowania hasła na ekranie logowania. Ta funkcja umożliwia użytkownikom zresetowanie hasła bez konieczności uzyskiwania dostępu do przeglądarki internetowej przy użyciu innego urządzenia.

Wskazówki dotyczące używania tej funkcji będzie można znaleźć w artykule [Reset your work or school password (Resetowanie hasła służbowego)](../active-directory-passwords-update-your-own-password.md#reset-password-at-sign-in)

## <a name="common-issues"></a>Typowe problemy

Podczas testowania tej funkcjonalności za pomocą funkcji Hyper-V link „Resetuj hasło” nie jest wyświetlany.

* Przejdź do maszyny wirtualnej używanej do testowania, kliknij pozycję **Widok**, a następnie usuń zaznaczenie pola wyboru **Sesja rozszerzona**.

Podczas testowania tej funkcjonalności za pomocą pulpitu zdanego link „Resetuj hasło” nie jest wyświetlany

* Resetowanie hasła nie jest obecnie obsługiwane z poziomu pulpitu zdalnego.

## <a name="next-steps"></a>Następne kroki

Poniższe linki dają dostęp do dodatkowych informacji dotyczących resetowania haseł za pomocą usługi Azure AD

* [How do I deploy SSPR? (Jak mogę wdrożyć samoobsługowe resetowanie hasła?)](howto-sspr-deployment.md)
* [Jak mogę włączyć resetowanie numeru PIN na ekranie logowania?](https://docs.microsoft.com/intune/device-windows-pin-reset)
* [More information about MDM authentication policies (Więcej informacji na temat zasad uwierzytelniania funkcji MDM)](https://docs.microsoft.com/windows/client-management/mdm/policy-csp-authentication)

[CreateProfile]: ./media/tutorial-sspr-windows/create-profile.png "Tworzenie profilu konfiguracji urządzenia usługi Intune w celu włączenia linku resetowania hasła na ekranie logowania systemu Windows 10"
[Assignment]: ./media/tutorial-sspr-windows/profile-assignment.png "Przypisywanie zasad konfiguracji urządzenia usługi Intune do grupy urządzeń z systemem Windows 10"
[LoginScreen]: ./media/tutorial-sspr-windows/logon-reset-password.png "Link resetowania hasła na ekranie logowania systemu Windows 10"
