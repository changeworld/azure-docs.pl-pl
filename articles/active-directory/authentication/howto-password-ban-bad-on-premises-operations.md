---
title: Usługi AD hasło ochrony (wersja zapoznawcza) operacji na platformie Azure i raportowanie
description: Usługa Azure AD hasło ochrony (wersja zapoznawcza) po wdrożeniu operacji i raportowanie
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 11/02/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.openlocfilehash: c19275f096191a2d55c939cbc9b76e7ffc6e1b8b
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2019
ms.locfileid: "54424442"
---
# <a name="preview-azure-ad-password-protection-operational-procedures"></a>Wersja zapoznawcza: Usługa Azure AD hasło ochrony procedury operacyjne

|     |
| --- |
| Ochrony hasłem w usłudze Azure AD jest funkcją publicznej wersji zapoznawczej usługi Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [dodatkowym warunkom użytkowania wersji zapoznawczych usług Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Po ukończeniu [instalacji ochrona za pomocą hasła usługi Azure AD](howto-password-ban-bad-on-premises-deploy.md) w środowisku lokalnym, istnieje kilka elementów, które muszą być skonfigurowane w witrynie Azure portal.

## <a name="configure-the-custom-banned-password-list"></a>Skonfiguruj listę niestandardowych zakazanych haseł

Postępuj zgodnie ze wskazówkami w artykule [Konfigurowanie listy zakazanych haseł w niestandardowych](howto-password-ban-bad-configure.md) dla czynności związanych z dostosowaniem listy zakazanych haseł dla Twojej organizacji.

## <a name="enable-password-protection"></a>Włączanie ochrony hasłem

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i przejdź do **usługi Azure Active Directory**, **metod uwierzytelniania**, następnie **ochrona za pomocą hasła (wersja zapoznawcza)**.
1. Ustaw **włączenia ochrony haseł usługi Active Directory systemu Windows Server** do **tak**
1. Jak wspomniano w [przewodnik wdrażania](howto-password-ban-bad-on-premises-deploy.md#deployment-strategy), zalecane jest początkowo ustawiona **tryb** do **inspekcji**
   * Po masz doświadczenia z tej funkcji, można przełączać się **tryb** do **wymuszone**
1. Kliknij pozycję **Zapisz**

![Włączanie składniki ochrony hasła usługi Azure AD w witrynie Azure portal](./media/howto-password-ban-bad-on-premises-operations/authentication-methods-password-protection-on-prem.png)

## <a name="audit-mode"></a>Tryb inspekcji

Tryb inspekcji ma służyć jako możliwość uruchamiania oprogramowania w trybie "what if". Każda usługa agenta DC oblicza hasło do poczty przychodzącej zgodnie z zasadami aktualnie aktywne. Jeśli bieżące zasady jest skonfigurowany w trybie inspekcji, hasła "złe" wynik w komunikatach w dzienniku zdarzeń, ale są akceptowane. Jest to jedyna różnica między trybem inspekcji i wymuszania; wszystkie inne operacje, uruchom takie same.

> [!NOTE]
> Firma Microsoft zaleca się, że początkowego wdrażania i testowania zawsze rozpoczyna pracę w trybie inspekcji. Następnie należy monitorować zdarzenia w dzienniku zdarzeń w celu przewidywania, czy wszystkie istniejące procesy operacyjne będzie zakłóceń, gdy tryb wymuszania jest włączony.

## <a name="enforce-mode"></a>Tryb wymuszania

Wymusić tryb jest przeznaczony jako konfiguracji końcowej. Tak jak w trybie inspekcji powyżej każda usługa agenta DC ocenia przychodzące hasła zgodnie z zasadami aktualnie aktywne. Jeśli jednak jest włączony tryb wymuszania, hasła, który jest uważany za niebezpieczne zgodnie z zasadami jest odrzucane.

Gdy hasło zostanie odrzucona w trybie wymuszania przez funkcję ochrony hasło usługi Azure AD agenta kontrolera domeny, widoczne wpływ widoczne dla użytkownika końcowego jest identyczna co zobaczą będzie, jeśli ich hasła zostało odrzucone przez wymuszanie złożoności hasła tradycyjnych lokalnych. Na przykład użytkownik może zostać wyświetlony następujący komunikat o błędzie tradycyjnych, na ekranie hasła logon\change Windows:

`Unable to update the password. The value provided for the new password does not meet the length, complexity, or history requirements of the domain.`

Ten komunikat jest tylko jeden przykład kilku możliwych wartości. Komunikat o błędzie może się różnić w zależności od rzeczywistej oprogramowania lub scenariusz, który próbuje ustawić hasło niezabezpieczonych.

Narażeni użytkownicy końcowi może być konieczne do pracy z ich personelu IT, aby poznać nowe wymagania i bardziej umożliwia wybranie opcji bezpiecznych haseł.

## <a name="enable-mode"></a>Włącz tryb

To ustawienie, zazwyczaj powinna pozostać w stanie domyślnym włączone (tak). Skonfigurowanie tego ustawienia na wyłączone (nie) spowoduje, że hasła usługi Azure AD wszystkich wdrożonych agentów ochrony kontrolera domeny przejść do trybu spoczynku, gdzie wszystkie hasła będzie akceptowane jako-, a żadne działania sprawdzania poprawności zostanie wykonane jakiejkolwiek (na przykład, nawet zdarzeń inspekcji będzie obliczanie).

## <a name="usage-reporting"></a>Raportowanie użycia

`Get-AzureADPasswordProtectionSummaryReport` Polecenia cmdlet, które może być użyta do wyprodukowania widok podsumowania działań. Przykładowe dane wyjściowe tego polecenia cmdlet jest następująca:

```PowerShell
Get-AzureADPasswordProtectionSummaryReport -DomainController bplrootdc2
DomainController                : bplrootdc2
PasswordChangesValidated        : 6677
PasswordSetsValidated           : 9
PasswordChangesRejected         : 10868
PasswordSetsRejected            : 34
PasswordChangeAuditOnlyFailures : 213
PasswordSetAuditOnlyFailures    : 3
PasswordChangeErrors            : 0
PasswordSetErrors               : 1
```

Zakres polecenia cmdlet raportowania może wpływać przy użyciu jednego z parametrów — lasu, - domeny lub kontroler domeny —. Nie został podany parametr oznacza — lasu.

> [!NOTE]
> To polecenie cmdlet działa, otwierając sesję programu PowerShell na każdym kontrolerze domeny. Aby można było pomyślnie, obsługa sesji zdalnej programu PowerShell musi być włączona na każdym kontrolerze domeny, a klient musi mieć wystarczające uprawnienia. Aby uzyskać więcej informacji na temat wymagań sesji zdalnej programu PowerShell należy uruchomić "Get-Help about_Remote_Troubleshooting" w oknie programu PowerShell.

> [!NOTE]
> To polecenie cmdlet działa zdalnie, Sondując dzienniku zdarzeń administratora każda usługa agenta kontrolera domeny. Jeśli dzienniki zdarzeń zawierają dużą liczbę zdarzeń, polecenie cmdlet może potrwać bardzo długo. Ponadto zbiorcze sieci zapytania dotyczące dużych zestawów danych może mieć wpływ na wydajność kontrolera domeny. W związku z tym tego polecenia cmdlet należy używać ostrożnie w środowiskach produkcyjnych.

## <a name="dc-agent-discovery"></a>Odnajdywanie agenta kontrolera domeny

`Get-AzureADPasswordProtectionDCAgent` Polecenia cmdlet mogą służyć do wyświetlania podstawowych informacji o różnych agentów kontrolera domeny w domenie lub lesie. Te informacje są pobierane z obiektów serviceConnectionPoint zarejestrowane przez uruchamianie usług agenta kontrolera domeny. Przykładowe dane wyjściowe tego polecenia cmdlet jest następująca:

```PowerShell
Get-AzureADPasswordProtectionDCAgent
ServerFQDN            : bplChildDC2.bplchild.bplRootDomain.com
Domain                : bplchild.bplRootDomain.com
Forest                : bplRootDomain.com
Heartbeat             : 2/16/2018 8:35:01 AM
```

Różne właściwości są aktualizowane przez poszczególne usługi agenta kontrolera domeny w systemie godzinowym przybliżone. Dane są nadal podlega procesowi opóźnienie replikacji usługi Active Directory.

Zakres kwerendy polecenie cmdlet może wpływać przy użyciu parametrów — domenie lub lesie —.

## <a name="next-steps"></a>Kolejne kroki

[Rozwiązywanie problemów i monitorowanie ochrona za pomocą hasła usługi Azure AD](howto-password-ban-bad-on-premises-troubleshoot.md)
