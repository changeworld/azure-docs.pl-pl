---
title: Ponowne aktywowanie wyłączonych obszarów nazw usługi Azure Access Control Service (ACS)
description: Znajdź i włącz obszary nazw usługi Azure Access Control Service (ACS) i poproś o rozszerzenie, aby utrzymać je włączone do 4 lutego 2019 r.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: ryanwi
ms.reviewer: jlu
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: ff0ce05b13fea8409475e3415c5d810d7c79769a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154869"
---
# <a name="how-to-reactivate-disabled-access-control-service-namespaces"></a>Jak: Ponowne aktywowanie wyłączonych obszarów nazw usługi kontroli dostępu

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

W listopadzie 2017 r. firma Microsoft ogłosiła, że usługa Kontroli dostępu platformy Microsoft Azure (ACS), usługa usługi Azure Active Directory (Azure AD), jest wycofywana 7 listopada 2018 r.

Od tego czasu wysłaliśmy e-maile do administratora subskrypcji ACS o przejściu na emeryturę ACS 12 miesięcy, 9 miesięcy, 6 miesięcy, 3 miesięcy, 1 miesiąc, 2 tygodnie, 1 tydzień i 1 dzień przed datą przejścia na emeryturę 7 listopada 2018.

3 października 2018 r. ogłosiliśmy (za pośrednictwem poczty e-mail i [wpisu w blogu)](https://azure.microsoft.com/blog/one-month-retirement-notice-access-control-service/)ofertę rozszerzenia dla klientów, którzy nie mogą zakończyć migracji przed 7 listopada 2018 r. Ogłoszenie miało również instrukcje dotyczące żądania rozszerzenia.

## <a name="why-your-namespace-is-disabled"></a>Dlaczego twoja przestrzeń nazw jest wyłączona

Jeśli nie wybrałeś rozszerzenia, zaczniemy wyłączać przestrzenie nazw ACS od 7 listopada 2018 r. Musisz już poprosić o przedłużenie do 4 lutego 2019 r.; w przeciwnym razie nie będzie można włączyć przestrzeni nazw za pośrednictwem programu PowerShell.

> [!NOTE]
> Aby uruchomić polecenia programu PowerShell i zażądać rozszerzenia, musisz być administratorem usługi lub współadministratorem subskrypcji.

## <a name="find-and-enable-your-acs-namespaces"></a>Znajdowanie i włączanie obszarów nazw acs

Za pomocą programu ACS PowerShell można wyświetlić listę wszystkich obszarów nazw acs i ponownie aktywować te, które zostały wyłączone.

1. Pobierz i zainstaluj program ACS PowerShell:
    1. Przejdź do Galerii programu PowerShell i pobierz [plik Acs.Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2).
    1. Zainstaluj moduł:

        ```powershell
        Install-Module -Name Acs.Namespaces
        ```

    1. Pobierz listę wszystkich możliwych poleceń:

        ```powershell
        Get-Command -Module Acs.Namespaces
        ```

        Aby uzyskać pomoc dotyczącą określonego polecenia, uruchom:

        ```powershell
        Get-Help [Command-Name] -Full
        ```
    
        gdzie `[Command-Name]` jest nazwa polecenia ACS.
1. Połącz się z acs za pomocą polecenia cmdlet **Connect-AcsAccount.** 

    Może być konieczna zmiana zasad wykonywania przez uruchomienie **Set-ExecutionPolicy** przed uruchomieniem polecenia.
1. Wyświetl listę dostępnych subskrypcji platformy Azure przy użyciu polecenia cmdlet **Get-AcsSubscription.**
1. Lista obszarów nazw usługi ACS przy użyciu polecenia cmdlet **Get-AcsNamespace.**
1. Upewnij się, że obszary nazw są `State` `Disabled`wyłączone, potwierdzając, że jest .

    [![Upewnij się, że obszary nazw są wyłączone](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png)](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png#lightbox)

    Można również `nslookup {your-namespace}.accesscontrol.windows.net` sprawdzić, czy domena jest nadal aktywna.

1. Włącz obszary nazw usługi ACS za pomocą polecenia cmdlet **Enable-AcsNamespace.**

    Po włączeniu przestrzeni nazw możesz zażądać rozszerzenia, aby nie wyłączyły się ponownie przed 4 lutego 2019 r. Po tej dacie wszystkie żądania do acs zakończy się niepowodzeniem.

## <a name="request-an-extension"></a>Poproś o rozszerzenie

Od 21 stycznia 2019 r. przyjmujemy nowe wnioski o przedłużenie.

Zaczniemy wyłączać przestrzenie nazw dla klientów, którzy zażądali rozszerzeń do 4 lutego 2019. Nadal można ponownie włączyć przestrzenie nazw za pośrednictwem programu PowerShell, ale przestrzenie nazw zostaną ponownie wyłączone po 48 godzinach.

Po 4 marca 2019 r. klienci nie będą już mogli ponownie włączyć żadnych obszarów nazw za pośrednictwem programu PowerShell.

Dalsze rozszerzenia nie będą już automatycznie zatwierdzane. Jeśli potrzebujesz dodatkowego czasu na migrację, skontaktuj się z [pomocą techniczną platformy Azure,](https://portal.azure.com/#create/Microsoft.Support) aby zapewnić szczegółową oś czasu migracji.

### <a name="to-request-an-extension"></a>Aby poprosić o rozszerzenie

1. Zaloguj się do witryny Azure portal i utwórz [nowe żądanie pomocy technicznej](https://portal.azure.com/#create/Microsoft.Support).
1. Wypełnij nowy formularz żądania pomocy technicznej, jak pokazano w poniższym przykładzie.

    | Pole żądania pomocy technicznej | Wartość |
    |-----------------------|--------------------|
    | **Typ problemu** | `Technical` |
    | **Subskrypcja** | Ustawianie subskrypcji |
    | **Usługa** | `All services` |
    | **Zasobów** | `General question/Resource not available` |
    | **Typ problemu** | `ACS to SAS Migration` |
    | **Temat** | Opisz problem |

   ![Pokazuje przykład nowego żądania pomocy technicznej](./media/howto-reactivate-disabled-acs-namespaces/new-technical-support-request.png)

<!--

1. Navigate to your ACS namespace's management portal by going to `https://{your-namespace}.accesscontrol.windows.net`.
1. Select the **Read Terms** button to read the [updated Terms of Use](https://azure.microsoft.com/support/legal/access-control/), which will direct you to a page with the updated Terms of Use.

    [![Select the Read Terms button](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png#lightbox)

1. Select **Request Extension** on the banner at the top of the page. The button will only be enabled after you read the [updated Terms of Use](https://azure.microsoft.com/support/legal/access-control/).

    [![Select the Request Extension button](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png#lightbox)

1. After the extension request is registered, the page will refresh with a new banner at the top of the page.

    [![Updated page with refreshed banner](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png#lightbox)
-->

## <a name="help-and-support"></a>Pomoc i obsługa techniczna

- Jeśli po zakończeniu tego sposobu natkniesz się na jakiekolwiek problemy, skontaktuj się z [pomocą techniczną platformy Azure.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)
- Jeśli masz pytania lub opinie na temat acsfeedback@microsoft.comemerytury ACS, skontaktuj się z nami pod adresem .

## <a name="next-steps"></a>Następne kroki

- Przejrzyj informacje dotyczące przejścia na emeryturę usługi ACS w obszarze [Jak: Migrować z usługi Kontroli dostępu platformy Azure](active-directory-acs-migration.md).
