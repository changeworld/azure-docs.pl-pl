---
title: Konfigurowanie uwierzytelniania wieloskładnikowego platformy Azure dla pulpitu wirtualnego systemu Windows — Platforma Azure
description: Jak skonfigurować uwierzytelnianie wieloskładnikowe platformy Azure w celu zwiększenia bezpieczeństwa na pulpicie wirtualnym systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0b3c47e1bbe5efdc5ee303305e52a785a49d0c00
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586875"
---
# <a name="set-up-azure-multi-factor-authentication"></a>Konfigurowanie uwierzytelniania wieloskładnikowego platformy Azure

Klient systemu Windows dla pulpitu wirtualnego systemu Windows jest doskonałą opcją integracji pulpitu wirtualnego systemu Windows z komputerem lokalnym. Jednak podczas konfigurowania konta pulpitu wirtualnego systemu Windows do klienta systemu Windows istnieją pewne środki, które należy podjąć, aby zapewnić bezpieczeństwo sobie i użytkownikom.

Podczas pierwszego logowania klient pyta o nazwę użytkownika, hasło i usługi Azure MFA. Po tym następnym zalogowaniu klient zapamięta token z aplikacji enterprise usługi Azure Active Directory (AD). Po wybraniu opcji **Zapamiętaj mnie**użytkownicy mogą zalogować się po ponownym uruchomieniu klienta bez konieczności ponownego wniesienia poświadczeń.

Zapamiętywanie poświadczeń jest wygodne, ale może również zmniejszyć bezpieczeństwo wdrożeń w scenariuszach przedsiębiorstwa lub urządzeniach osobistych. Aby chronić użytkowników, musisz upewnić się, że klient ciągle prosi o poświadczenia usługi Azure Multi-Factor Authentication (MFA). W tym artykule pokazano, jak skonfigurować zasady dostępu warunkowego dla pulpitu wirtualnego systemu Windows, aby włączyć to ustawienie.

## <a name="prerequisites"></a>Wymagania wstępne

Oto, czego potrzebujesz, aby rozpocząć:

- Przypisz wszystkim użytkownikom jedną z następujących licencji:
  - Microsoft 365 E3 lub E5
  - Usługa Azure Active Directory Premium P1 lub P2
  - Mobilność w przedsiębiorstwie + bezpieczeństwo E3 lub E5
- Grupa usługi Azure Active Directory z użytkownikami przypisanymi jako członkowie grupy.
- Włącz usługę Azure MFA dla wszystkich użytkowników. Aby uzyskać więcej informacji na temat tego, jak to zrobić, zobacz [Jak wymagać weryfikacji dwuetapowej dla użytkownika](/active-directory/authentication/howto-mfa-userstates).

>[!NOTE]
>Następujące ustawienie dotyczy również [klienta sieci Web pulpitu wirtualnego systemu Windows](https://rdweb.wvd.microsoft.com/webclient/index.html).

## <a name="opt-in-to-the-conditional-access-policy"></a>Zapisz się do zasad dostępu warunkowego

1. Otwórz **usługę Azure Active Directory**.

2. Przejdź do karty **Wszystkie aplikacje.** W menu rozwijanym "Typ aplikacji" wybierz pozycję **Aplikacje przedsiębiorstwa**, a następnie wyszukaj **klienta pulpitu wirtualnego systemu Windows**.

    ![Zrzut ekranu przedstawiający kartę Wszystkie aplikacje. Użytkownik wprowadził "windows virtual desktop client" do paska wyszukiwania, a aplikacja pojawi się w wynikach wyszukiwania.](media/all-applications-search.png)

3. Wybierz **pozycję Dostęp warunkowy**.

    ![Zrzut ekranu przedstawiający użytkownika najeżdżającego kursor myszy na kartę Dostęp warunkowy.](media/conditional-access-location.png)

4. Wybierz **+ Nowe zasady**.

   ![Zrzut ekranu strony Dostęp warunkowy. Użytkownik najeżdża kursor myszy na nowy przycisk zasad.](media/new-policy-button.png)

5. Wprowadź **nazwę** **reguły**, a następnie **wybierz** nazwę ***grupy** utworzonej w wymaganiach wstępnych.

6. Wybierz **pozycję Wybierz**, a następnie wybierz pozycję **Gotowe**.

7. Następnie otwórz **aplikacje w chmurze lub akcje**.

8. W panelu **Wybierz** wybierz aplikację **Windows Virtual Desktop** Enterprise.

    ![Zrzut ekranu strony Aplikacje lub akcje w chmurze. Użytkownik wybrał aplikację Pulpit wirtualny systemu Windows, zaznaczając znacznik wyboru obok niej. Wybrana aplikacja zostanie podświetlona na czerwono.](media/cloud-apps-select.png)
    
    >[!NOTE]
    >Powinna również zostać wyświetlona aplikacja Klienta pulpitu wirtualnego systemu Windows wybrana po lewej stronie ekranu, jak pokazano na poniższej ilustracji. Aby zasady działały, potrzebne są zarówno aplikacje Windows Virtual Desktop Desktop, jak i Windows Virtual Desktop Client Enterprise.
    >
    > ![Zrzut ekranu strony Aplikacje lub akcje w chmurze. Aplikacje klienta pulpitu wirtualnego systemu Windows i pulpitu wirtualnego systemu Windows są wyróżnione na czerwono.](media/cloud-apps-enterprise-selected.png)

9. Wybierz **pozycję Wybierz**

10. Następnie otwórz **Grant** 

11. Wybierz **pozycję Wymagaj uwierzytelniania wieloskładnikowego**, a następnie wybierz pozycję **Wymagaj jednego z wybranych formantów**.
   
    ![Zrzut ekranu strony Grant. Wybrano opcję "Wymagaj uwierzytelniania wieloskładnikowego".](media/grant-page.png)

    >[!NOTE]
    >Jeśli w organizacji są urządzenia zarejestrowane w usłudze MDM i nie chcesz, aby były wyświetlane monity usługi MFA, możesz również wybrać **opcję Wymagaj, aby urządzenie było oznaczone jako zgodne**.

12. Wybierz **pozycję Sesja**.

13. Ustaw **częstotliwość logowania** na **Aktywny,** a następnie zmień jej wartość na **1 godziny**.

    ![Zrzut ekranu strony Sesja. Menu sesji pokazuje menu rozwijane częstotliwości logowania zostały zmienione na "1" i "Godziny".](media/sign-in-frequency.png)
   
    >[!NOTE]
    >Aktywne sesje w środowisku pulpitu wirtualnego systemu Windows będą nadal działać po zmianie zasad. Jeśli jednak rozłączysz się lub wylogujesz się, po 60 minutach musisz ponownie podać swoje poświadczenia. Po zmianie ustawień można przedłużyć limit czasu tak długo, jak chcesz (o ile jest on zgodny z zasadami zabezpieczeń organizacji).
    >
    >Ustawieniem domyślnym jest okno stopniowe 90 dni, co oznacza, że klient poprosi użytkowników o ponowne zalogowanie się podczas próby uzyskania dostępu do zasobu po nieaktywności na komputerze przez 90 dni lub dłużej.

14. Włącz zasady.

15. Wybierz **pozycję Utwórz,** aby potwierdzić zasadę.

Wszystko gotowe! Możesz przetestować zasady, aby upewnić się, że lista dozwolonych działa zgodnie z przeznaczeniem.
