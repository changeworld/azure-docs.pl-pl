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
ms.openlocfilehash: 24b54a3645fe97903219841dd148c0942dfcda76
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67112384"
---
# <a name="baseline-policy-require-mfa-for-service-management-preview"></a>Zasady punktu odniesienia: Wymagać uwierzytelniania Wieloskładnikowego do zarządzania usługami (wersja zapoznawcza)

Być może używasz różnych usług platformy Azure w Twojej organizacji. Te usługi można zarządzać za pomocą interfejsu API usługi Azure Resource Manager:

* Azure Portal
* Azure PowerShell
* Interfejs wiersza polecenia platformy Azure

Za pomocą usługi Azure Resource Manager do zarządzania usługami jest wysoce uprzywilejowanych akcji. Usługa Azure Resource Manager można zmienić konfiguracji obowiązujące w dzierżawie, takich jak ustawienia usługi i rozliczeń subskrypcji. Uwierzytelnianie pojedynczy czynnik jest narażony na różne atakami, takimi jak osłony techniki wyłudzania informacji oraz hasło. W związku z tym jest ważne, aby sprawdzić tożsamość użytkowników, którzy chcą mieć dostęp do usługi Azure Resource Manager i aktualizowanie konfiguracji, wymagając uwierzytelniania wieloskładnikowego przed zezwoleniem na dostęp.

**Wymagać uwierzytelniania Wieloskładnikowego do zarządzania usługami** jest [bazowymi zasadami](concept-baseline-protection.md) mogą one wymagać uwierzytelniania Wieloskładnikowego dla każdego użytkownika, dostęp do witryny Azure portal, programu Azure PowerShell lub wiersza polecenia platformy Azure. Ta zasada ma zastosowanie do wszystkich użytkowników uzyskujących dostęp do usługi Azure Resource Manager, bez względu na to, gdy są one administrator.

Po włączeniu tych zasad w dzierżawie wszystkich użytkowników logujących się do zasobów zarządzania platformy Azure zostaną zakwestionowane usługi Multi-Factor authentication. Jeśli użytkownik nie jest zarejestrowany do uwierzytelniania Wieloskładnikowego, użytkownik będą musieli zarejestrować się przy użyciu aplikacji Microsoft Authenticator, aby kontynuować.

![Wymagać uwierzytelniania Wieloskładnikowego dla usługi Azure Resource Manager](./media/howto-baseline-protect-azure/baseline-policy-require-mfa-for-service-management.png)

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

### <a name="user-exclusions"></a>Wykluczenia użytkownika

Ta zasada linii bazowej zapewnia opcji, aby wykluczyć użytkowników. Przed włączeniem zasad dla swojej dzierżawy, zaleca się, z wyłączeniem następujących kont:

* **Dostęp awaryjny** lub **break szkła** konta, aby uniknąć zablokowania konta obowiązujące w dzierżawie. W mało prawdopodobnym scenariuszu, który wszyscy administratorzy z zablokowanym dostępem do Twojej dzierżawy Twoje konto administracyjne dostępu awaryjnego może służyć do logowania się do dzierżawy podejmij kroki, aby odzyskać dostęp.
   * Więcej informacji można znaleźć w artykule [zarządzania kont dostępu awaryjnego w usłudze Azure AD](../users-groups-roles/directory-emergency-access.md).
* **Konta usług** i **usługi zasady**, takich jak konto Azure AD Connect Sync. Konta usług są nieinteraktywnych kont, które nie są powiązane z żadnym określonym użytkownikiem. One są zwykle używane przez usługi zaplecza i zezwolić na dostęp programistyczny do aplikacji. Konta usług powinny być wyłączone, ponieważ usługi MFA nie można wykonać programowo.
   * Jeśli Twoja organizacja ma konta te używane w skryptach lub kod, należy wziąć pod uwagę zastępowała je za pomocą [zarządzanych tożsamości](../managed-identities-azure-resources/overview.md). Jako rozwiązanie tymczasowe możesz wykluczyć te określonych kont z bazowymi zasadami.
* Użytkownicy, którzy nie ma lub nie będzie mógł używać smartfonie.
   * Ta zasada wymaga od użytkowników rejestracji usługi MFA za pomocą aplikacji Microsoft Authenticator.

## <a name="enable-the-baseline-policy"></a>Włącz zasady linii bazowej

Zasady **bazowymi zasadami: Wymagać uwierzytelniania Wieloskładnikowego do zarządzania usługami (wersja zapoznawcza)** ma wstępnie skonfigurowany i pojawi się u góry po przejściu do bloku dostępu warunkowego w witrynie Azure portal.

Aby włączyć te zasady i chronić administratorów:

1. Zaloguj się do **witryny Azure portal** jako administratora globalnego, administratora zabezpieczeń lub administrator dostępu warunkowego.
1. Przejdź do **usługi Azure Active Directory** > **dostępu warunkowego**.
1. Na liście zasad wybierz **bazowymi zasadami: Wymagać uwierzytelniania Wieloskładnikowego do zarządzania usługami (wersja zapoznawcza)** .
1. Ustaw **Włącz zasady** do **Użyj zasad natychmiast**.
1. Dodaj wykluczenia użytkownika, klikając **użytkowników** > **wybierz wykluczonych użytkowników** i wybierając pozycję Użytkownicy, którzy muszą być wyłączone. Kliknij przycisk **wybierz** następnie **gotowe**.
1. Kliknij przycisk **Zapisz**.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji, zobacz:

* [Zasady ochrony linii bazowej dostępu warunkowego](concept-baseline-protection.md)
* [Pięć kroków do zabezpieczania infrastruktury tożsamości](../../security/azure-ad-secure-steps.md)
* [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](overview.md)