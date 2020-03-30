---
title: Przypisywanie etykiet czułości do grup — Usługa Azure AD | Dokumenty firmy Microsoft
description: Jak utworzyć reguły członkostwa, aby automatycznie wypełniać grupy i odwołanie do reguły.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 02/24/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 667fb39aabfec14cff01221b82a45ba8ad1d68d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78329736"
---
# <a name="assign-sensitivity-labels-to-office-365-groups-in-azure-active-directory-preview"></a>Przypisywanie etykiet czułości do grup usługi Office 365 w usłudze Azure Active Directory (wersja zapoznawcza)

Usługa Azure Active Directory (Azure AD) obsługuje stosowanie etykiet wrażliwości opublikowanych przez [centrum zgodności usługi Microsoft 365](https://sip.protection.office.com/homepage) do grup usługi Office 365. Etykiety czułości mają zastosowanie do grup między usługami, takimi jak Outlook, Microsoft Teams i SharePoint. Ta funkcja jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat obsługi aplikacji usługi Office 365, zobacz [Obsługa etykiet wrażliwości usługi Office 365](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites#support-for-the-sensitivity-labels).

> [!IMPORTANT]
> Aby skonfigurować tę funkcję, musi istnieć co najmniej jedna aktywna licencja usługi Azure Active Directory Premium P1 w organizacji usługi Azure AD.

## <a name="enable-sensitivity-label-support-in-powershell"></a>Włącz obsługę etykiet czułości w programie PowerShell

Aby zastosować opublikowane etykiety do grup, należy najpierw włączyć operację. Te kroki umożliwiają włączenie funkcji w usłudze Azure AD.

1. Otwórz okno programu Windows PowerShell na komputerze. Można go otworzyć bez podwyższonych uprawnień.
1. Uruchom następujące polecenia, aby przygotować się do uruchomienia poleceń cmdlet.

    ```PowerShell
    Import-Module AzureADPreview
    Connect-AzureAD
    ```

    Na stronie **Zaloguj się na konto** wprowadź konto administratora i hasło, aby połączyć cię z usługą, a następnie wybierz pozycję Zaloguj **się**.
1. Pobierz bieżące ustawienia grupy dla organizacji usługi Azure AD.

    ```PowerShell
    $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
    ```

    > [!NOTE]
    > Jeśli nie utworzono żadnych ustawień grupy dla tej organizacji usługi Azure AD, należy najpierw utworzyć ustawienia. Wykonaj kroki opisane w [poleceniach cmdlet usługi Azure Active Directory, aby skonfigurować ustawienia grupy](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-settings-cmdlets) w celu utworzenia ustawień grupy dla tej organizacji usługi Azure AD.

1. Następnie wyświetl bieżące ustawienia grupy.

    ```PowerShell
    $Setting.Values
    ```

1. Następnie włącz funkcję:

    ```PowerShell
    $Setting["EnableMIPLabels"] = "True"
    ```

1. Następnie zapisz zmiany i zastosuj ustawienia:

    ```PowerShell
    Set-AzureADDirectorySetting -Id $Setting.Id -DirectorySetting $Setting
    ```

Gotowe. Włączono tę funkcję i można stosować opublikowane etykiety do grup.

## <a name="assign-a-label-to-a-new-group-in-azure-portal"></a>Przypisywanie etykiety do nowej grupy w witrynie Azure Portal

1. Zaloguj się do [centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com).
1. Wybierz pozycję **Grupy**, a następnie wybierz pozycję **Nowa grupa**.
1. Na stronie **Nowa grupa** wybierz pozycję **Office 365**, a następnie wypełnij wymagane informacje dla nowej grupy i wybierz etykietę czułości z listy.

   ![Przypisywanie etykiety czułości na stronie Nowe grupy](./media/groups-assign-sensitivity-labels/new-group-page.png)

1. Zapisz zmiany i wybierz pozycję **Utwórz**.

Grupa zostanie utworzona, a ustawienia witryny i grupy skojarzone z wybraną etykietą są następnie automatycznie wymuszane.

## <a name="assign-a-label-to-an-existing-group-in-azure-portal"></a>Przypisywanie etykiety do istniejącej grupy w witrynie Azure Portal

1. Zaloguj się do [centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy za pomocą konta administratora grup lub jako właściciel grupy.
1. Wybierz **opcję Grupy**.
1. Na stronie **Wszystkie grupy** wybierz grupę, którą chcesz oznaczyć etykietą.
1. Na stronie wybranej grupy wybierz pozycję **Właściwości** i wybierz etykietę czułości z listy.

   ![Przypisywanie etykiety czułości na stronie przeglądu grupy](./media/groups-assign-sensitivity-labels/assign-to-existing.png)

1. Wybierz przycisk **Zapisz**, aby zapisać zmiany.

## <a name="remove-a-label-from-an-existing-group-in-azure-portal"></a>Usuwanie etykiety z istniejącej grupy w witrynie Azure Portal

1. Zaloguj się do [centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy za pomocą konta administratora lub grupy globalnej lub jako właściciel grupy.
1. Wybierz **opcję Grupy**.
1. Na stronie **Wszystkie grupy** wybierz grupę, z której chcesz usunąć etykietę.
1. Na stronie **Grupa** wybierz pozycję **Właściwości**.
1. Wybierz pozycję **Usuń**.
1. Aby zastosować zmiany, wybierz pozycję **Zapisz**.

## <a name="using-classic-azure-ad-classifications"></a>Korzystanie z klasycznych klasyfikacji usługi Azure AD

Po włączeniu tej funkcji klasyfikacje "klasyczne" dla grup będą wyświetlane tylko w istniejących grupach i witrynach i należy ich używać w nowych grupach tylko wtedy, gdy tworzysz grupy w aplikacjach, które nie obsługują etykiet czułości. Administrator może później przekonwertować je na etykiety czułości. Klasyfikacje klasyczne to stare klasyfikacje skonfigurowane przez `ClassificationList` zdefiniowanie wartości dla ustawienia w programie Azure AD PowerShell. Gdy ta funkcja jest włączona, te klasyfikacje nie będą stosowane do grup.

## <a name="troubleshooting-issues"></a>Rozwiązywanie problemów

### <a name="sensitivity-labels-are-not-available-for-assignment-on-a-group"></a>Etykiety czułości nie są dostępne dla przypisania w grupie

Opcja etykiety czułości jest wyświetlana tylko dla grup, gdy spełnione są wszystkie następujące warunki:

1. Etykiety są publikowane w Centrum zgodności usługi Microsoft 365 dla tej dzierżawy.
1. Funkcja jest włączona, EnableMIPLabels jest ustawiona na True w programie PowerShell.
1. Grupa jest grupą usługi Office 365.
1. Dzierżawca ma aktywną licencję Premium P1 usługi Azure Active Directory.
1. Bieżący zalogowany użytkownik ma wystarczające uprawnienia do przypisywania etykiet. Użytkownik musi być administratorem globalnym, administratorem grupy lub właścicielem grupy.

Upewnij się, że wszystkie warunki są spełnione, aby przypisać etykiety do grupy.

### <a name="the-label-i-want-to-assign-is-not-in-the-list"></a>Etykieta, która chcę przypisać, nie znajduje się na liście

Jeśli szukana etykieta nie znajduje się na liście, może to oznaczać z jednego z następujących powodów:

- Etykieta może nie zostać opublikowana w Centrum zgodności usługi Microsoft 365. Może to również dotyczyć etykiet, które nie są już publikowane. Aby uzyskać więcej informacji, skontaktuj się z administratorem.
- Etykieta może zostać opublikowana, jednak nie jest dostępna dla zalogowanych użytkowników. Aby uzyskać więcej informacji na temat uzyskiwania dostępu do etykiety, skontaktuj się z administratorem.

### <a name="how-to-change-the-label-on-a-group"></a>Jak zmienić etykietę w grupie

Etykiety można wymieniać w dowolnym momencie, wykonując te same kroki, co przypisywanie etykiety do istniejącej grupy w następujący sposób:

1. Zaloguj się do [centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy za pomocą konta administratora globalnego lub grupowego lub jako właściciel grupy.
1. Wybierz **opcję Grupy**.
1. Na stronie **Wszystkie grupy** wybierz grupę, którą chcesz oznaczyć etykietą.
1. Na stronie wybranej grupy wybierz pozycję **Właściwości** i wybierz nową etykietę czułości z listy.
1. Wybierz **pozycję Zapisz**.

### <a name="group-setting-changes-to-published-labels-are-not-updated-on-the-groups"></a>Zmiany ustawień grupowych w opublikowanych etykietach nie są aktualizowane w grupach

Najlepszym rozwiązaniem nie zaleca się zmieniania ustawień grupy etykiety po zastosowaniu etykiety do grup. Po wprowadzeniu zmian w ustawieniach grupy skojarzonych z opublikowanymi etykietami w [Centrum zgodności usługi Microsoft 365](https://sip.protection.office.com/homepage)te zmiany zasad nie są automatycznie stosowane w grupach, których dotyczy problem.

Jeśli musisz wprowadzić zmiany, użyj [skryptu programu Azure AD PowerShell,](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1) aby ręcznie zastosować aktualizacje do grup, których dotyczy problem. Ta metoda zapewnia, że wszystkie istniejące grupy wymuszają nowe ustawienie.

## <a name="next-steps"></a>Następne kroki

- [Używanie etykiet czułości w usłudze Microsoft Teams, grupach usługi Office 365 i witrynach programu SharePoint](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)
- [Ręczne aktualizowanie grup po zmianie zasad etykiet za pomocą skryptu programu Azure AD PowerShell](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1)
- [Edytowanie ustawień grupy](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-settings-azure-portal)
- [Zarządzanie grupami przy użyciu poleceń programu PowerShell](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-settings-v2-cmdlets)
