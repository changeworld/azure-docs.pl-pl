---
title: Operacje ochrony hasłem i raporty — Azure Active Directory
description: Operacje wdrażania i raportowania w usłudze Azure AD Password Protection
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: d8602a9c5b69c47c2f663dab461394ef5e1a9bed
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75762862"
---
# <a name="azure-ad-password-protection-operational-procedures"></a>Procedury operacyjne ochrony hasłem usługi Azure AD

Po zakończeniu [instalacji ochrony hasłem usługi Azure AD](howto-password-ban-bad-on-premises-deploy.md) lokalnie istnieje kilka elementów, które muszą zostać skonfigurowane w Azure Portal.

## <a name="configure-the-custom-banned-password-list"></a>Skonfiguruj niestandardową listę wykluczonych haseł

Postępuj zgodnie ze wskazówkami zawartymi w artykule [Konfigurowanie listy niestandardowo zakazanych haseł](howto-password-ban-bad-configure.md) , aby dostosowywać listę wykluczonych haseł dla organizacji.

## <a name="enable-password-protection"></a>Włącz ochronę hasłem

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do **Azure Active Directory**, **zabezpieczenia**,**metody uwierzytelniania**, a następnie **Ochrona hasłem**.
1. Ustaw **opcję Włącz ochronę hasłem w systemie Windows Server Active Directory** na **wartość tak**
1. Jak wspomniano w [Podręczniku wdrażania](howto-password-ban-bad-on-premises-deploy.md#deployment-strategy)zaleca się wstępne ustawienie **trybu** **inspekcji**
   * Po dodaniu tej funkcji możesz przełączyć **tryb** na **wymuszony**
1. Kliknij pozycję **Zapisz**

![Włączanie składników ochrony hasłem usługi Azure AD w Azure Portal](./media/howto-password-ban-bad-on-premises-operations/authentication-methods-password-protection-on-prem.png)

## <a name="audit-mode"></a>Tryb inspekcji

Tryb inspekcji jest przeznaczony do uruchamiania oprogramowania w trybie "co jeśli". Każda usługa agenta DC szacuje hasło przychodzące zgodnie z aktualnie aktywnymi zasadami. Jeśli bieżące zasady zostały skonfigurowane do obsługi trybu inspekcji, hasła "złe" powodują, że komunikaty dziennika zdarzeń są akceptowane. Jest to jedyna różnica między trybami inspekcji i wymuszania. wszystkie inne operacje działają tak samo.

> [!NOTE]
> Firma Microsoft zaleca, aby początkowe wdrożenie i testy były zawsze uruchamiane w trybie inspekcji. Zdarzenia w dzienniku zdarzeń powinny być następnie monitorowane w celu zaplanowania, czy jakiekolwiek istniejące procesy operacyjne byłyby zakłócone po włączeniu trybu wymuszania.

## <a name="enforce-mode"></a>Wymuszaj tryb

Tryb wymuszania jest zamierzony jako Konfiguracja końcowa. Podobnie jak w przypadku trybu inspekcji powyżej, każda usługa agenta kontrolera domeny szacuje hasła przychodzące zgodnie z aktualnie aktywnymi zasadami. Jeśli Tryb wymuszania jest włączony, hasło, które jest uznawane za niezabezpieczone zgodnie z zasadami, jest odrzucane.

Jeśli hasło zostanie odrzucone w trybie wymuszania przez agenta usługi Azure AD Password Protection, widoczny wpływ widoczny dla użytkownika końcowego jest identyczny jak w przypadku odrzucenia hasła przez tradycyjne wymuszanie złożoności hasła. Na przykład użytkownik może zobaczyć następujący tradycyjny komunikat o błędzie na ekranie hasła logon\change systemu Windows:

`Unable to update the password. The value provided for the new password does not meet the length, complexity, or history requirements of the domain.`

Ten komunikat to tylko jeden przykład z kilku możliwych wyników. Konkretny komunikat o błędzie może się różnić w zależności od rzeczywistego oprogramowania lub scenariusza, który podejmuje próbę ustawienia niezabezpieczonego hasła.

Użytkownicy końcowi, którzy mają problemy, mogą potrzebować współpracy z pracownikami IT w celu zrozumienia nowych wymagań i zapewnienia dodatkowych haseł.

> [!NOTE]
> Ochrona hasłem w usłudze Azure AD nie kontroluje określonego komunikatu o błędzie wyświetlanego przez komputer kliencki w przypadku odrzucenia słabego hasła.

## <a name="enable-mode"></a>Tryb włączania

To ustawienie powinno pozostać w domyślnym stanie włączony (tak). Skonfigurowanie tego ustawienia na wyłączone (nie) spowoduje, że wszystkie wdrożone Agenty usługi Azure AD Password Protection będą działać w trybie quiescent, w którym wszystkie hasła są akceptowane jako-is, a żadne działania weryfikacyjne nie będą wykonywane w żadnym przypadku (na przykład nierówne zdarzenia inspekcji zostanie wyemitowany).

## <a name="next-steps"></a>Następne kroki

[Monitorowanie ochrony hasłem usługi Azure AD](howto-password-ban-bad-on-premises-monitor.md)
