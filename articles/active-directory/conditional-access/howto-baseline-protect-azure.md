---
title: Bazowe zasady wymagają usługi MFA dla usługi zarządzania (wersja zapoznawcza) — usługi Azure Active Directory
description: Zasady dostępu warunkowego, aby wymagać uwierzytelniania Wieloskładnikowego dla usługi Azure Resource Manager
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
ms.openlocfilehash: 203b752f9da67ebf60e373fe7ce0893b4fd7fcb5
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/04/2019
ms.locfileid: "67560959"
---
# <a name="baseline-policy-require-mfa-for-service-management-preview"></a>Zasady punktu odniesienia: Wymagać uwierzytelniania Wieloskładnikowego do zarządzania usługami (wersja zapoznawcza)

Być może używasz różnych usług platformy Azure w Twojej organizacji. Te usługi można zarządzać za pomocą interfejsu API usługi Azure Resource Manager:

* Azure Portal
* Azure PowerShell
* Interfejs wiersza polecenia platformy Azure

Za pomocą usługi Azure Resource Manager do zarządzania usługami jest wysoce uprzywilejowanych akcji. Usługa Azure Resource Manager można zmienić konfiguracji obowiązujące w dzierżawie, takich jak ustawienia usługi i rozliczeń subskrypcji. Uwierzytelnianie pojedynczy czynnik jest narażony na różne atakami, takimi jak osłony techniki wyłudzania informacji oraz hasło. W związku z tym jest ważne, aby sprawdzić tożsamość użytkowników, którzy chcą mieć dostęp do usługi Azure Resource Manager i aktualizowanie konfiguracji, wymagając uwierzytelniania wieloskładnikowego przed zezwoleniem na dostęp.

**Wymagać uwierzytelniania Wieloskładnikowego do zarządzania usługami** jest [bazowymi zasadami](concept-baseline-protection.md) mogą one wymagać uwierzytelniania Wieloskładnikowego dla każdego użytkownika, dostęp do witryny Azure portal, programu Azure PowerShell lub wiersza polecenia platformy Azure. Ta zasada ma zastosowanie do wszystkich użytkowników uzyskujących dostęp do usługi Azure Resource Manager, bez względu na to, gdy są one administrator.

Po włączeniu tych zasad w dzierżawie wszystkich użytkowników logujących się do zasobów zarządzania platformy Azure zostaną zakwestionowane usługi Multi-Factor authentication. Jeśli użytkownik nie jest zarejestrowany do uwierzytelniania Wieloskładnikowego, użytkownik będą musieli zarejestrować się przy użyciu aplikacji Microsoft Authenticator, aby kontynuować.

Do wykonania, interaktywne logowanie przy użyciu [programu Azure Powershell](https://docs.microsoft.com/powershell/azure/authenticate-azureps), użyj [Connect AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) polecenia cmdlet.

```PowerShell
Connect-AzAccount
```

Po uruchomieniu to polecenie cmdlet spowoduje wyświetlenie ciągu tokenu. Aby się zarejestrować, skopiuj następujący ciąg i wklej go w [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin)  w przeglądarce. Sesja programu PowerShell zostanie uwierzytelniona na potrzeby połączenia z platformą Azure.

Do wykonania, interaktywne logowanie przy użyciu [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)Uruchom [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) polecenia.

```azurecli
az login
```

Jeśli interfejs wiersza polecenia może otworzyć Twoją domyślną przeglądarkę, zrobi to i załaduje stronę logowania. W przeciwnym razie trzeba otworzyć stronę przeglądarki i postępuj zgodnie z instrukcjami w wierszu polecenia, aby wprowadzić kod autoryzacji po przejściu do [ https://aka.ms/devicelogin ](https://aka.ms/devicelogin) w przeglądarce. Następnie zaloguj się przy użyciu poświadczeń konta w przeglądarce.

## <a name="deployment-considerations"></a>Zagadnienia dotyczące wdrażania

Ponieważ **wymagają usługi MFA do zarządzania usługami** zasady mają zastosowanie do wszystkich użytkowników usługi Azure Resource Manager, kilka zagadnień, które należy podjąć, aby zapewnić bezproblemowe wdrożenie. Te zagadnienia obejmują identyfikowanie użytkowników i zasad usługi w usłudze Azure AD, która nie może lub nie należy wykonywać uwierzytelnianie wieloskładnikowe, a także aplikacji i używanych przez Twoją organizację klientów, które nie obsługują nowoczesnego uwierzytelniania.

## <a name="enable-the-baseline-policy"></a>Włącz zasady linii bazowej

Zasady **bazowymi zasadami: Wymagać uwierzytelniania Wieloskładnikowego do zarządzania usługami (wersja zapoznawcza)** ma wstępnie skonfigurowany i pojawi się u góry po przejściu do bloku dostępu warunkowego w witrynie Azure portal.

Aby włączyć te zasady i chronić administratorów:

1. Zaloguj się do **witryny Azure portal** jako administratora globalnego, administratora zabezpieczeń lub administrator dostępu warunkowego.
1. Przejdź do **usługi Azure Active Directory** > **dostępu warunkowego**.
1. Na liście zasad wybierz **bazowymi zasadami: Wymagać uwierzytelniania Wieloskładnikowego do zarządzania usługami (wersja zapoznawcza)** .
1. Ustaw **Włącz zasady** do **Użyj zasad natychmiast**.
1. Kliknij przycisk **Zapisz**.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji, zobacz:

* [Zasady ochrony linii bazowej dostępu warunkowego](concept-baseline-protection.md)
* [Pięć kroków do zabezpieczania infrastruktury tożsamości](../../security/azure-ad-secure-steps.md)
* [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](overview.md)