---
title: Operacje ochrony hasłem usługi AD platformy Azure i raportowanie — usługi Azure Active Directory
description: Operacji po wdrożeniu na platformie Azure AD ochrony hasłem i raportowanie
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca85007bb016cc98d1be61ce08865945e699ad4a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60358200"
---
# <a name="azure-ad-password-protection-operational-procedures"></a>Usługa Azure AD ochrony hasłem procedury operacyjne

Po ukończeniu [instalacji ochrony haseł usługi Azure AD](howto-password-ban-bad-on-premises-deploy.md) w środowisku lokalnym, istnieje kilka elementów, które muszą być skonfigurowane w witrynie Azure portal.

## <a name="configure-the-custom-banned-password-list"></a>Skonfiguruj listę niestandardowych zakazanych haseł

Postępuj zgodnie ze wskazówkami w artykule [Konfigurowanie listy zakazanych haseł w niestandardowych](howto-password-ban-bad-configure.md) dla czynności związanych z dostosowaniem listy zakazanych haseł dla Twojej organizacji.

## <a name="enable-password-protection"></a>Włączanie ochrony hasłem

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i przejdź do **usługi Azure Active Directory**, **metod uwierzytelniania**, następnie **ochrony hasłem**.
1. Ustaw **włączenia ochrony haseł usługi Active Directory systemu Windows Server** do **tak**
1. Jak wspomniano w [przewodnik wdrażania](howto-password-ban-bad-on-premises-deploy.md#deployment-strategy), zalecane jest początkowo ustawiona **tryb** do **inspekcji**
   * Po masz doświadczenia z tej funkcji, można przełączać się **tryb** do **wymuszone**
1. Kliknij polecenie **Zapisz**.

![Włączanie składników ochrony haseł usługi Azure AD w witrynie Azure portal](./media/howto-password-ban-bad-on-premises-operations/authentication-methods-password-protection-on-prem.png)

## <a name="audit-mode"></a>Tryb inspekcji

Tryb inspekcji ma służyć jako możliwość uruchamiania oprogramowania w trybie "what if". Każda usługa agenta DC oblicza hasło do poczty przychodzącej zgodnie z zasadami aktualnie aktywne. Jeśli bieżące zasady jest skonfigurowany w trybie inspekcji, hasła "złe" wynik w komunikatach w dzienniku zdarzeń, ale są akceptowane. Jest to jedyna różnica między trybem inspekcji i wymuszania; wszystkie inne operacje, uruchom takie same.

> [!NOTE]
> Firma Microsoft zaleca się, że początkowego wdrażania i testowania zawsze rozpoczyna pracę w trybie inspekcji. Następnie należy monitorować zdarzenia w dzienniku zdarzeń w celu przewidywania, czy wszystkie istniejące procesy operacyjne będzie zakłóceń, gdy tryb wymuszania jest włączony.

## <a name="enforce-mode"></a>Tryb wymuszania

Wymusić tryb jest przeznaczony jako konfiguracji końcowej. Tak jak w trybie inspekcji powyżej każda usługa agenta DC ocenia przychodzące hasła zgodnie z zasadami aktualnie aktywne. Jeśli jednak jest włączony tryb wymuszania, hasła, który jest uważany za niebezpieczne zgodnie z zasadami jest odrzucane.

Gdy hasło zostanie odrzucona w trybie wymuszania przez agenta ochrony kontrolera domeny haseł usługi Azure AD, widoczne wpływ widoczne dla użytkownika końcowego jest identyczna co zobaczą będzie, jeśli ich hasła zostało odrzucone przez wymuszanie złożoności hasła tradycyjnych lokalnych. Na przykład użytkownik może zostać wyświetlony następujący komunikat o błędzie tradycyjnych, na ekranie hasła logon\change Windows:

`Unable to update the password. The value provided for the new password does not meet the length, complexity, or history requirements of the domain.`

Ten komunikat jest tylko jeden przykład kilku możliwych wartości. Komunikat o błędzie może się różnić w zależności od rzeczywistej oprogramowania lub scenariusz, który próbuje ustawić hasło niezabezpieczonych.

Narażeni użytkownicy końcowi może być konieczne do pracy z ich personelu IT, aby poznać nowe wymagania i bardziej umożliwia wybranie opcji bezpiecznych haseł.

## <a name="enable-mode"></a>Włącz tryb

To ustawienie, zazwyczaj powinna pozostać w stanie domyślnym włączone (tak). Skonfigurowanie tego ustawienia na wyłączone (nie) spowoduje, że wszystkie wdrożonych agentów DC ochronę haseł usługi Azure AD przejść do trybu spoczynku, gdzie wszystkie hasła będzie akceptowane jako-, a żadne działania sprawdzania poprawności zostanie wykonane jakiejkolwiek (na przykład, nawet zdarzeń inspekcji będzie obliczanie).

## <a name="next-steps"></a>Kolejne kroki

[Monitorowanie ochrony haseł usługi Azure AD](howto-password-ban-bad-on-premises-monitor.md)
