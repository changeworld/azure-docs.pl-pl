---
title: Włączanie lokalnej ochrony hasłem usługi Azure AD
description: Dowiedz się, jak włączyć ochronę hasłem usługi Azure AD w lokalnym środowisku usług domenowych Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 03/05/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: d00a8b84477226b68913f95c5121bbbdfc2eb09d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263818"
---
# <a name="enable-on-premises-azure-active-directory-password-protection"></a>Włączanie lokalnej ochrony hasłem usługi Azure Active Directory

Użytkownicy często tworzą hasła, które używają typowych lokalnych słów, takich jak szkoła, drużyna sportowa lub znana osoba. Te hasła są łatwe do odgadnięcia i słabe przed atakami opartymi na słowniku. Aby wymusić silne hasła w organizacji, ochrona hasłem usługi Azure Active Directory (Azure AD) zapewnia globalną i niestandardową listę haseł zbanowanych. Żądanie zmiany hasła kończy się niepowodzeniem, jeśli na tej liście zablokowanych haseł znajduje się dopasowanie.

Aby chronić lokalne środowisko usług domenowych Active Directory (AD DS), można zainstalować i skonfigurować usługę Azure AD Password Protection do pracy z kontrolerem domeny on-prem. W tym artykule pokazano, jak włączyć ochronę hasłem usługi Azure AD dla środowiska lokalnego.

Aby uzyskać więcej informacji na temat działania ochrony hasłem usługi Azure AD w środowisku lokalnym, zobacz [Jak wymusić ochronę hasłem usługi Azure AD dla usługi Windows Server Active Directory](concept-password-ban-bad-on-premises.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule pokazano, jak włączyć ochronę hasłem usługi Azure AD dla środowiska lokalnego. Przed ukończeniem tego artykułu [należy zainstalować i zarejestrować usługę proxy usługi Azure AD Protection i agentów kontrolera domeny](howto-password-ban-bad-on-premises-deploy.md) w lokalnym środowisku usług AD DS.

## <a name="enable-on-premises-password-protection"></a>Włącz lokalną ochronę hasłem

1. Zaloguj się do [portalu Azure](https://portal.azure.com) i przejdź do**metod** > uwierzytelniania**zabezpieczeń** >  **usługi Azure Active Directory** > **Ochrona hasłem**.
1. Ustaw opcję **Włącz ochronę hasłem w usłudze Active Directory systemu Windows Server na** *Tak*.

    Gdy to ustawienie jest ustawione na *Nie,* wszyscy wdrożoni agenci kontrolera kontrolera Azure AD Password Protection przechodzą w tryb spoczynkowy, w którym wszystkie hasła są akceptowane w stanie takim, w jakim są. Nie są wykonywane żadne działania sprawdzania poprawności, a zdarzenia inspekcji nie są generowane.

1. Zaleca się, aby początkowo ustawić **tryb** *inspekcji*. Po wygodnej funkcji i wpływie na użytkowników w organizacji można przełączyć **tryb** na *Wymuszony*. Aby uzyskać więcej informacji, zobacz następującą sekcję dotyczącą [trybów pracy](#modes-of-operation).
1. Gdy będzie gotowy, wybierz pozycję **Zapisz**.

    [![](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords-cropped.png "Enable on-premises password protection under Authentication Methods in the Azure portal")](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords.png#lightbox)

## <a name="modes-of-operation"></a>Tryby operacyjne

Po włączeniu lokalnej usługi Azure AD Password Protection, można użyć trybu *inspekcji* lub *trybu wymuszania.* Zaleca się, że początkowe wdrożenie i testowanie zawsze rozpoczyna się w trybie inspekcji. Wpisy w dzienniku zdarzeń powinny być następnie monitorowane, aby przewidzieć, czy istniejące procesy operacyjne zostaną zakłócone po włączeniu trybu *wymuszania.*

### <a name="audit-mode"></a>Tryb inspekcji

*Tryb inspekcji* jest przeznaczony jako sposób uruchamiania oprogramowania w trybie "co jeśli". Każda usługa agenta kontrolera domeny ochrony haseł usługi Azure AD ocenia hasło przychodzące zgodnie z aktualnie aktywną zasadą.

Jeśli bieżąca zasada jest skonfigurowana do trybu inspekcji, "złe" hasła powodują komunikaty dziennika zdarzeń, ale są przetwarzane i aktualizowane. To zachowanie jest jedyną różnicą między trybem inspekcji i wymuszania. Wszystkie inne operacje działają tak samo.

### <a name="enforced-mode"></a>Tryb wymuszony

*Wymuszony* tryb jest przeznaczony jako ostateczna konfiguracja. Podobnie jak w trybie inspekcji, każda usługa agenta kontrolera domeny ochrony haseł usługi Azure AD ocenia hasła przychodzące zgodnie z aktualnie aktywną zasadą. Gdy tryb wymuszony jest włączony, hasło, które jest uważane za niebezpieczne zgodnie z zasadami, jest odrzucane.

Gdy hasło zostanie odrzucone w trybie wymuszonym przez agenta kontrolera domeny ochrony haseł usługi Azure AD, użytkownik końcowy widzi podobny błąd, tak jak zobaczy, jeśli ich hasło zostało odrzucone przez tradycyjne lokalne wymuszanie złożoności haseł. Na przykład użytkownik może zobaczyć następujący tradycyjny komunikat o błędzie na ekranie logowania systemu Windows lub zmienić hasło:

*"Nie można zaktualizować hasła. Wartość podana dla nowego hasła nie spełnia wymagań dotyczących długości, złożoności ani historii domeny."*

Ten komunikat jest tylko jednym z przykładów kilku możliwych wyników. Określony komunikat o błędzie może się różnić w zależności od rzeczywistego oprogramowania lub scenariusza, który próbuje ustawić niezabezpieczone hasło.

Użytkownicy końcowi, których to dotyczy, mogą być zmuszeni do współpracy ze swoim personelem IT, aby zrozumieć nowe wymagania i wybrać bezpieczne hasła.

> [!NOTE]
> Usługa Azure AD Password Protection nie ma kontroli nad określonym komunikatem o błędzie wyświetlanym przez komputer kliencki, gdy słabe hasło zostanie odrzucone.

## <a name="next-steps"></a>Następne kroki

Aby dostosować listę zablokowanych haseł dla organizacji, zobacz [Konfigurowanie niestandardowej listy haseł zakazanych w usłudze Azure AD](tutorial-configure-custom-password-protection.md).

Aby monitorować zdarzenia przedwzmowe, zobacz [Monitorowanie ochrony hasłem usługi Azure AD na przedm.](howto-password-ban-bad-on-premises-monitor.md)
