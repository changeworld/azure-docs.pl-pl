---
title: Zasady linii bazowej wymagają uwierzytelniania wieloskładnikowego dla zarządzania usługami (wersja zapoznawcza) — Azure Active Directory
description: Zasady dostępu warunkowego, które wymagają uwierzytelniania wieloskładnikowego dla Azure Resource Manager
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: e8095b4fa6e52b7c34cedaea35b129ab68dddc65
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532959"
---
# <a name="baseline-policy-require-mfa-for-service-management-preview"></a>Zasady linii bazowej: Wymagaj uwierzytelniania wieloskładnikowego dla zarządzania usługami (wersja zapoznawcza)

W Twojej organizacji mogą być używane różne usługi platformy Azure. Tymi usługami można zarządzać za poorednictwem Azure Resource Manager interfejsu API:

* Azure Portal
* Azure PowerShell
* Interfejs wiersza polecenia platformy Azure

Używanie Azure Resource Manager do zarządzania usługami to wysoce uprzywilejowana akcja. Azure Resource Manager mogą zmieniać konfiguracje dla całej dzierżawy, takie jak ustawienia usługi i rozliczenia subskrypcji. Uwierzytelnianie wieloskładnikowe jest podatne na różne ataki, takie jak phishing i rozpylanie haseł. W związku z tym ważne jest, aby zweryfikować tożsamość użytkowników, którzy chcą uzyskać dostęp do konfiguracji Azure Resource Manager i aktualizacji, przez wymaganie uwierzytelniania wieloskładnikowego przed zezwoleniem na dostęp.

**Wymagaj uwierzytelniania wieloskładnikowego dla zarządzania usługami** jest [zasadami odniesienia](concept-baseline-protection.md) , które będą wymagały uwierzytelniania wieloskładnikowego dla każdego użytkownika uzyskującego dostęp do Azure Portal, Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. Te zasady mają zastosowanie do wszystkich użytkowników uzyskujących dostęp do Azure Resource Manager niezależnie od tego, czy są one administratorami.

Po włączeniu tych zasad w dzierżawie wszyscy użytkownicy logujący się do zasobów usługi Azure Management będą zarejestrowani przy użyciu uwierzytelniania wieloskładnikowego. Jeśli użytkownik nie jest zarejestrowany na potrzeby uwierzytelniania wieloskładnikowego, użytkownik będzie musiał zarejestrować się przy użyciu aplikacji Microsoft Authenticator, aby można było wykonać tę operację.

Aby przeprowadzić logowanie interaktywne przy użyciu [programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps), użyj polecenia cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) .

```PowerShell
Connect-AzAccount
```

Po uruchomieniu to polecenie cmdlet spowoduje wyświetlenie ciągu tokenu. Aby się zalogować, Skopiuj ten ciąg i wklej go [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin) do programu w przeglądarce. Sesja programu PowerShell zostanie uwierzytelniona na potrzeby połączenia z platformą Azure.

Aby przeprowadzić logowanie interaktywne przy użyciu [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest), uruchom polecenie [AZ login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) .

```azurecli
az login
```

Jeśli interfejs wiersza polecenia może otworzyć Twoją domyślną przeglądarkę, zrobi to i załaduje stronę logowania. W przeciwnym razie musisz otworzyć stronę przeglądarki i postępować zgodnie z instrukcjami w wierszu polecenia, aby wprowadzić kod autoryzacji po przejściu do [https://aka.ms/devicelogin](https://aka.ms/devicelogin) przeglądarki w przeglądarce. Następnie zaloguj się przy użyciu poświadczeń konta w przeglądarce.

## <a name="deployment-considerations"></a>Zagadnienia dotyczące wdrażania

Ze względu na to, że zasady **Wymagaj usługi MFA na potrzeby zarządzania usługami** mają zastosowanie do wszystkich Azure Resource Manager użytkowników, należy wprowadzić kilka kwestii w celu zapewnienia bezproblemowego wdrożenia. Te zagadnienia obejmują Identyfikowanie użytkowników i zasad usługi w usłudze Azure AD, które nie mogą ani nie powinny wykonywać uwierzytelniania MFA, a także aplikacje i klientów używane przez organizację, które nie obsługują nowoczesnego uwierzytelniania.

## <a name="enable-the-baseline-policy"></a>Włączanie zasad linii bazowej

Zasady linii **bazowej zasad: Wymagaj, aby usługa MFA dla zarządzania usługami** (wersja zapoznawcza) została wstępnie skonfigurowana i będzie wyświetlana u góry po przejściu do bloku dostęp warunkowy w Azure Portal.

Aby włączyć te zasady i chronić administratorów:

1. Zaloguj się do **Azure Portal** jako Administrator globalny, administrator zabezpieczeń lub administrator dostępu warunkowego.
1. Przejdź do **Azure Active Directory** > **dostęp warunkowy**.
1. Na liście zasad wybierz pozycję **zasady linii bazowej: Wymagaj uwierzytelniania wieloskładnikowego dla zarządzania usługami**(wersja zapoznawcza).
1. Ustaw opcję **Włącz zasady** , aby od **razu używać zasad**.
1. Kliknij polecenie **Zapisz**.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz:

* [Zasady ochrony punktu odniesienia dostępu warunkowego](concept-baseline-protection.md)
* [Pięć kroków związanych z zabezpieczaniem infrastruktury tożsamości](../../security/fundamentals/steps-secure-identity.md)
* [Co to jest dostęp warunkowy w Azure Active Directory?](overview.md)