---
title: Jak ponownie uaktywnić wyłączonych obszarów nazw usługi Azure Access Control Service (ACS)
description: Dowiedz się, jak znaleźć i Włącz przestrzeniami nazw usługi Azure Access Control Service (ACS) oraz wnioskiem o przedłużenie, aby były włączone do 4 lutego 2019 r.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/07/2018
ms.author: celested
ms.reviewer: jlu
ms.custom: aaddev
ms.openlocfilehash: 298afda7549690a9ea0314bff63a714be50a33b9
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2018
ms.locfileid: "51019267"
---
# <a name="how-to-reactivate-disabled-access-control-service-namespaces"></a>Porady: ponowne aktywowanie wyłączonej przestrzeni nazw usługi kontroli dostępu

Listopada 2017 r. ogłosiliśmy, że Microsoft Azure Access Control Service (ACS), usługa Azure Active Directory (Azure AD) zostanie wycofana 7 listopada 2018 r.

Ponieważ następnie wysłaliśmy wiele wiadomości e-mail na adres e-mail administratora do subskrypcji usługi ACS o wycofaniu usługi ACS 12 miesięcy, 9 miesięcy, 6 miesięcy, 3 miesiące, 1 miesiąc, 2 tygodnie, 1, i 1 dzień tygodnia przed dniem wycofania 7 listopada 2018 r.

3 października 2018 r. ogłosiliśmy (za pośrednictwem poczty e-mail i [wpis w blogu](https://azure.microsoft.com/blog/one-month-retirement-notice-access-control-service/)) ofertę rozszerzenia, aby klienci, którzy nie może zakończyć ich migracji, zanim 7 listopada 2018 r. Ogłoszenie również znajdują się instrukcje dotyczące o rozszerzenie.

## <a name="why-your-namespace-is-disabled"></a>Dlaczego przestrzeni nazw jest wyłączona

Jeśli jeszcze nie została wybrana w przypadku rozszerzenia, firma Microsoft rozpocznie się wyłączyć przestrzeni nazw usługi ACS, począwszy od 7 listopada 2018 r. Jeśli brakuje komunikacji i czy nadal chcesz włączyć rozszerzenie do 4 lutego 2019 r postępuj zgodnie z instrukcjami, w poniższych sekcjach.

> [!NOTE]
> Musi być administratorem subskrypcji, aby uruchamiać polecenia programu PowerShell i wnioskiem o przedłużenie.

## <a name="find-and-enable-your-acs-namespaces"></a>Znajdowanie i włączyć przestrzeniami nazw usługi ACS

Za pomocą programu PowerShell usługi ACS do listy wszystkich przestrzeni nazw usługi ACS, a następnie ponownie aktywować te, które zostały wyłączone.

1. Pobierz i zainstaluj usługi ACS programu PowerShell:
    1. Przejdź do galerii programu PowerShell i Pobierz [Acs.Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2).
    1. Zainstaluj moduł:

        ```powershell
        Install-Module -Name Acs.Namespaces
        ```

    1. Pobierz listę wszystkie możliwe polecenia:

        ```powershell
        Get-Command -Module Acs.Namespaces
        ```

        Aby uzyskać pomoc dotyczącą określonego polecenia, uruchom polecenie:

        ```
        Get-Help [Command-Name] -Full
        ```
    
        gdzie `[Command-Name]` to nazwa polecenia ACS.
1. Lista dostępnych subskrypcji platformy Azure przy użyciu **Get AcsSubscription** polecenia cmdlet.
1. Listy, używając przestrzeni nazw usługi ACS **Get AcsNamespace** polecenia cmdlet.
1. Upewnij się, że przestrzenie nazw są wyłączone, sprawdzając, czy `State` jest `Disabled`.

    [![Upewnij się, że przestrzenie nazw są wyłączone](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png)](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png#lightbox)

    Można również użyć `nslookup {your-namespace}.accesscontrol.windows.net` aby upewnić się, jeśli domena jest nadal aktywne.

1. Włącz usługi ACS namespace (s) przy użyciu **AcsNamespace Włącz** polecenia cmdlet.

    Po aktywowaniu usługi namespace (s), możesz poprosić rozszerzenie tak, aby namespace (s) nie będzie można wyłączyć ponownie przed 4 lutego 2019 r. Po tej dacie wszystkie żądania do usługi ACS zakończy się niepowodzeniem.

## <a name="request-an-extension"></a>Żądanie rozszerzenia

1. Przejdź do portalu zarządzania usługi ACS przestrzeni nazw, przechodząc do `https://{your-namespace}.accesscontrol.windows.net`.
1. Wybierz **warunki odczytu** przycisk, aby odczytać [zaktualizowane warunki użytkowania](https://azure.microsoft.com/support/legal/access-control/), którego nastąpi bezpośrednie przekierowanie do strony zawierającej zaktualizowane warunki użytkowania.

    [![Wybierz przycisk przeczytaj warunki](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png#lightbox)

1. Wybierz **żądanie rozszerzenia** na banerze w górnej części strony. Przycisk będzie można włączyć tylko po przeczytaniu [zaktualizowane warunki użytkowania](https://azure.microsoft.com/support/legal/access-control/).

    [![Wybierz przycisk żądanie rozszerzenia](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png#lightbox)

1. Po zarejestrowaniu żądanie rozszerzenia, strona zostanie odświeżona z nowym transparentem w górnej części strony.

    [![Zaktualizowana strona z odświeżenia transparentu](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png#lightbox)

## <a name="help-and-support"></a>Pomoc i obsługa techniczna

- Jeśli napotkasz problemy po wykonaniu tych instrukcjach, skontaktuj się z [pomocy technicznej platformy Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
- Jeśli masz pytania lub opinie na temat wycofania usługi ACS, skontaktuj się z nami pod adresem acsfeedback@microsoft.com.

## <a name="next-steps"></a>Kolejne kroki

- Przejrzyj informacje na temat wycofania usługi ACS w [instrukcje: Migrowanie z usługi Azure Access Control Service](active-directory-acs-migration.md).
