---
title: Jak ponownie uaktywnić wyłączonych obszarów nazw usługi Azure Access Control Service (ACS)
description: Dowiedz się, jak znaleźć i Włącz przestrzeniami nazw usługi Azure Access Control Service (ACS) oraz wnioskiem o przedłużenie, aby były włączone do 4 lutego 2019 r.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: ryanwi
ms.reviewer: jlu
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fe1517cce8a1f9b1024085340ffb927b282f0fb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65540282"
---
# <a name="how-to-reactivate-disabled-access-control-service-namespaces"></a>Instrukcje: Ponowne uaktywnianie wyłączonych przestrzeni nazw usługi Access Control Service

Listopada 2017 r. ogłosiliśmy, że Microsoft Azure Access Control Service (ACS), usługa Azure Active Directory (Azure AD) zostanie wycofana 7 listopada 2018 r.

Ponieważ następnie wysłaliśmy wiadomości e-mail na adres e-mail administratora do subskrypcji usługi ACS o wycofaniu usługi ACS 12 miesięcy, 9 miesięcy, 6 miesięcy, 3 miesiące, 1 miesiąc, 2 tygodnie, 1, i 1 dzień tygodnia przed dniem wycofania 7 listopada 2018 r.

3 października 2018 r. ogłosiliśmy (za pośrednictwem poczty e-mail i [wpis w blogu](https://azure.microsoft.com/blog/one-month-retirement-notice-access-control-service/)) ofertę rozszerzenia, aby klienci, którzy nie może zakończyć ich migracji, zanim 7 listopada 2018 r. Ogłoszenie ma również instrukcje dotyczące o rozszerzenie.

## <a name="why-your-namespace-is-disabled"></a>Dlaczego przestrzeni nazw jest wyłączona

Jeśli jeszcze nie została wybrana w przypadku rozszerzenia, Rozpoczniemy Wyłącz przestrzeni nazw usługi ACS, począwszy od 7 listopada 2018 r. Należy żądanej rozszerzenie 4 lutego 2019 już; w przeciwnym razie nie można włączyć przestrzeni nazw za pomocą programu PowerShell.

> [!NOTE]
> Musi być administratorów usługi lub współadministratorem subskrypcji, aby uruchamiać polecenia programu PowerShell i wnioskiem o przedłużenie.

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

        ```powershell
        Get-Help [Command-Name] -Full
        ```
    
        gdzie `[Command-Name]` to nazwa polecenia ACS.
1. Nawiązywanie połączenia przy użyciu usługi ACS **Connect AcsAccount** polecenia cmdlet. 

    Może być konieczne zmiany zasad wykonywania, uruchamiając **Set-ExecutionPolicy** przed uruchomieniem polecenia.
1. Lista dostępnych subskrypcji platformy Azure przy użyciu **Get AcsSubscription** polecenia cmdlet.
1. Listy, używając przestrzeni nazw usługi ACS **Get AcsNamespace** polecenia cmdlet.
1. Upewnij się, że przestrzenie nazw są wyłączone, sprawdzając, czy `State` jest `Disabled`.

    [![Upewnij się, że przestrzenie nazw są wyłączone](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png)](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png#lightbox)

    Można również użyć `nslookup {your-namespace}.accesscontrol.windows.net` aby upewnić się, jeśli domena jest nadal aktywne.

1. Włącz usługi ACS namespace (s) przy użyciu **AcsNamespace Włącz** polecenia cmdlet.

    Po aktywowaniu usługi namespace (s), możesz poprosić rozszerzenie tak, aby namespace (s) nie będzie można wyłączyć ponownie przed 4 lutego 2019 r. Po tej dacie wszystkie żądania do usługi ACS zakończy się niepowodzeniem.

## <a name="request-an-extension"></a>Żądanie rozszerzenia

Podejmujemy nowe żądania dotyczące rozszerzeń od 21 stycznia 2019 r.

Firma Microsoft rozpocznie się wyłączenie przestrzeni nazw dla klientów, którzy poprosili rozszerzenia do 4 lutego 2019 r. Nadal można ponownie włączyć przestrzeni nazw za pomocą programu PowerShell, ale zostanie wyłączona przestrzenie nazw ponownie po upływie 48 godzin.

Po 4 marca 2019 r klienci będą mogli ponownie włączyć wszystkie przestrzenie nazw za pomocą programu PowerShell.

Dodatkowe rozszerzenia nie jest już będą zatwierdzane automatycznie. Jeśli potrzebujesz dodatkowego czasu na migrację, skontaktuj się z pomocą [pomocy technicznej platformy Azure](https://portal.azure.com/#create/Microsoft.Support) zapewnienie na osi czasu migracji szczegółowe.

### <a name="to-request-an-extension"></a>Żądanie rozszerzenia

1. Zaloguj się do witryny Azure portal i Utwórz [nowe żądanie pomocy technicznej](https://portal.azure.com/#create/Microsoft.Support).
1. Wypełnij formularz nowego żądania pomocy technicznej, jak pokazano w poniższym przykładzie.

    | Pole żądania pomocy technicznej | Wartość |
    |-----------------------|--------------------|
    | **Typ problemu** | `Technical` |
    | **Subskrypcja** | Ustaw subskrypcję |
    | **Usługa** | `All services` |
    | **Zasób** | `General question/Resource not available` |
    | **Typ problemu** | `ACS to SAS Migration` |
    | **Temat** | Opisz problem |

   ![Nowe żądanie pomocy technicznej](./media/howto-reactivate-disabled-acs-namespaces/new-technical-support-request.png)

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

- Jeśli napotkasz problemy po wykonaniu tych instrukcjach, skontaktuj się z [pomocy technicznej platformy Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
- Jeśli masz pytania lub opinie na temat wycofania usługi ACS, skontaktuj się z nami pod adresem acsfeedback@microsoft.com.

## <a name="next-steps"></a>Kolejne kroki

- Przejrzyj informacje na temat wycofania usługi ACS w [jak: Migrowanie z usługi Azure Access Control Service](active-directory-acs-migration.md).
