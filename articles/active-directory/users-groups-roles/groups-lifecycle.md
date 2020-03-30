---
title: Ustawianie wygaśnięcia dla grup usługi Office 365 — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Jak skonfigurować wygaśnięcie dla grup usługi Office 365 w usłudze Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/13/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 562b551bc8a46a45135bf6a9a8e328b4b0e74f98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80048245"
---
# <a name="configure-the-expiration-policy-for-office-365-groups"></a>Konfigurowanie zasad wygasania dla grup usługi Office 365

W tym artykule opisano, jak zarządzać cyklem życia grup usługi Office 365, ustawiając dla nich zasady wygasania. Zasady wygasania można ustawić tylko dla grup usługi Office 365 w usłudze Azure Active Directory (Azure AD).

Po ustawieniu grupy, aby wygasnąć:

- Grupy z działaniami użytkowników są automatycznie odnawiane w miarę zbliżania się wygaśnięcia.
- Właściciele grupy są powiadamiani o odnowieniu grupy, jeśli grupa nie jest odnawiana automatycznie.
- Każda grupa, która nie jest odnawiana, zostanie usunięta.
- Każda usuwana grupa usługi Office 365 może zostać przywrócona w ciągu 30 dni przez właścicieli grupy lub administratora.

Obecnie tylko jedna zasada wygasania może być skonfigurowana dla wszystkich grup usługi Office 365 w organizacji usługi Azure AD.

> [!NOTE]
> Konfigurowanie i używanie zasad wygasania dla grup usługi Office 365 wymaga posiadania, ale niekoniecznie przypisywania licencji usługi Azure AD Premium dla członków wszystkich grup, do których są stosowane zasady wygasania.

Aby uzyskać informacje dotyczące pobierania i instalowania poleceń cmdlet programu Azure AD PowerShell, zobacz [Program PowerShell usługi Azure Active Directory dla programu Graph 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137).

## <a name="activity-based-automatic-renewal"></a>Automatyczne odnawianie oparte na działaniach

Dzięki usłudze Azure AD intelligence grupy są teraz automatycznie odnawiane na podstawie tego, czy zostały ostatnio użyte. Ta funkcja eliminuje konieczność ręcznego działania właścicieli grup, ponieważ jest ona oparta na aktywności użytkowników w grupach w usługach Office 365, takich jak Outlook, SharePoint lub Teams. Jeśli na przykład właściciel lub członek grupy przekaże dokument w programie SharePoint, odwiedzi kanał Usługi lub wyśle wiadomość e-mail do grupy w programie Outlook, grupa zostanie automatycznie odnowiona, a właściciel nie otrzyma żadnych powiadomień o odnowieniu.

### <a name="activities-that-automatically-renew-group-expiration"></a>Działania, które automatycznie odnawiają wygaśnięcie grupy

Następujące akcje użytkownika powodują automatyczne odnawianie grup:

- Program SharePoint: wyświetlanie, edytowanie, pobieranie, przenoszenie, udostępnianie lub przekazywanie plików
- Outlook: Dołączanie do grupy, odczytywanie/pisanie wiadomości grupy z obszaru grupy, Jak wiadomość (w programie Outlook Web Access)
- Drużyny: Odwiedź kanał drużynowy

### <a name="auditing-and-reporting"></a>Inspekcja i sprawozdawczość

Administratorzy mogą uzyskać listę automatycznie odnowionych grup z dzienników inspekcji działania w usłudze Azure AD.

![Automatyczne odnawianie grup na podstawie aktywności](./media/groups-lifecycle/audit-logs-autorenew-group.png)

## <a name="roles-and-permissions"></a>Role i uprawnienia

Poniżej przedstawiono role, które mogą konfigurować i używać wygasania dla grup usługi Office 365 w usłudze Azure AD.

Rola | Uprawnienia
-------- | --------
Administrator globalny, administrator grupy lub administrator użytkownika | Może tworzyć, odczytywać, aktualizować lub usuwać ustawienia zasad wygasania grup usługi Office 365<br>Można odnowić dowolną grupę usługi Office 365
Użytkownik | Może odnawiać posiadaną grupę usługi Office 365<br>Można przywrócić posiadaną grupę usługi Office 365<br>Może odczytać ustawienia zasad wygasania

Aby uzyskać więcej informacji na temat uprawnień do przywracania usuniętej grupy, zobacz [Przywracanie usuniętej grupy usługi Office 365 w usłudze Azure Active Directory](groups-restore-deleted.md).

## <a name="set-group-expiration"></a>Ustawianie czasu wygaśnięcia grup

1. Otwórz [centrum administracyjne usługi Azure AD](https://aad.portal.azure.com) za pomocą konta, które jest administratorem globalnym w organizacji usługi Azure AD.

2. Wybierz **pozycję Grupy**, a następnie wybierz pozycję **Wygasanie,** aby otworzyć ustawienia wygaśnięcia.
  
   ![Ustawienia wygaśnięcia grup](./media/groups-lifecycle/expiration-settings.png)

3. Na stronie **Wygasanie** można:

    - Ustaw okres istnienia grupy w dniach. Można wybrać jedną z wstępnie ustawionych wartości lub wartość niestandardową (powinna wynosić co najmniej 31 dni).
    - Określ adres e-mail, na który powinny być wysyłane powiadomienia o odnowieniu i wygaśnięciu, gdy grupa nie ma właściciela.
    - Wybierz, które grupy usługi Office 365 tracą ważność. Można ustawić wygaśnięcie dla:
      - **Wszystkie** Grupy usługi Office 365
      - Lista **wybranych** grup usługi Office 365
      - **Brak** ograniczenia wygaśnięcia dla wszystkich grup
    - Zapisz ustawienia po zakończeniu, wybierając pozycję **Zapisz**.

> [!NOTE]
> - Podczas pierwszej konfiguracji wygaśnięcia wszystkie grupy, które są starsze niż interwał wygaśnięcia są ustawione na 35 dni do wygaśnięcia, chyba że grupa jest automatycznie odnawiana lub właściciel odnawia ją.
> - Gdy grupa dynamiczna zostanie usunięta i przywrócona, jest postrzegana jako nowa grupa i ponownie wypełniona zgodnie z regułą. Ten proces może potrwać do 24 godzin.
> - Powiadomienia o wygaśnięciu grup używanych w aplikacjach Teams są wyświetlane w kanale Właścicieli drużyn.

## <a name="email-notifications"></a>Powiadomienia e-mail

Jeśli grupy nie są automatycznie odnawiane, powiadomienia e-mail, takie jak ta, są wysyłane do właścicieli grup usługi Office 365 30 dni, 15 dni i 1 dzień przed wygaśnięciem grupy. Język wiadomości e-mail jest określany przez preferowany język właściciela grup lub ustawienie języka usługi Azure AD. Jeśli właściciel grupy zdefiniował preferowany język lub wielu właścicieli ma ten sam preferowany język, używany jest ten język. We wszystkich innych przypadkach używane jest ustawienie języka usługi Azure AD.

![Powiadomienia e-mail o wygaśnięciu](./media/groups-lifecycle/expiration-notification.png)

W wiadomości e-mail z powiadomieniem **o renew group** właściciele grup mogą bezpośrednio uzyskać dostęp do strony szczegółów grupy w Panelu [dostępu](https://account.activedirectory.windowsazure.com/r#/applications). Tam użytkownicy mogą uzyskać więcej informacji na temat grupy, takich jak jej opis, kiedy została ostatnio odnowiona, kiedy wygaśnie, a także możliwość odnowienia grupy. Strona szczegółów grupy zawiera teraz również łącza do zasobów grupy usługi Office 365, dzięki czemu właściciel grupy może wygodnie wyświetlać zawartość i aktywność w swojej grupie.

Po wygaśnięciu grupy grupa jest usuwana jeden dzień po dacie wygaśnięcia. Powiadomienie e-mail, takie jak ten, jest wysyłane do właścicieli grup usługi Office 365 z informacją o wygaśnięciu, a następnie usunięciu ich grupy usługi Office 365.

![Grupowe powiadomienia e-mail o usuwaniu usunięcia](./media/groups-lifecycle/deletion-notification.png)

Grupę można przywrócić w ciągu 30 dni od jej usunięcia, wybierając **grupę Przywróć** lub używając poleceń cmdlet programu PowerShell, zgodnie z opisem w [obszarze Przywracanie usuniętej grupy usługi Office 365 w usłudze Azure Active Directory](groups-restore-deleted.md). 30-dniowy okres przywracania grupy nie jest dostosowywany.

Jeśli przywracana grupa zawiera dokumenty, witryny programu SharePoint lub inne obiekty trwałe, pełne przywrócenie grupy i jej zawartości może potrwać do 24 godzin.

## <a name="how-to-retrieve-office-365-group-expiration-date"></a>Jak pobrać datę wygaśnięcia grupy usługi Office 365

Oprócz Panelu dostępu, w którym użytkownicy mogą wyświetlać szczegóły grupy, w tym datę wygaśnięcia i datę ostatniego odnowienia, datę wygaśnięcia grupy usługi Office 365 można pobrać z wersji beta interfejsu API rest programu Microsoft Graph. expirationDateTime jako właściwość grupy została włączona w programie Microsoft Graph Beta. Można go pobrać za pomocą żądania GET. Aby uzyskać więcej informacji, zapoznaj się z [tym przykładem](https://docs.microsoft.com/graph/api/group-get?view=graph-rest-beta#example).

> [!NOTE]
> Aby zarządzać członkostwem w grupach w Panelu dostępu, w ustawieniu ogólnym grup usługi Azure Active Directory należy ustawić "Ogranicz dostęp do grup w panelu dostępu".

## <a name="how-office-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>Sposób działania wygasania grupy usługi Office 365 ze skrzynką pocztową wstrzymaną zgodnie z prawem

Gdy grupa wygaśnie i zostanie usunięta, 30 dni po usunięciu danych grupy z aplikacji takich jak Planner, Witryny lub Zespoły zostaną trwale usunięte, ale legalna skrzynka pocztowa grupy jest przechowywana i nie jest trwale usuwana. Administrator może użyć poleceń cmdlet programu Exchange, aby przywrócić skrzynkę pocztową w celu pobrania danych.

## <a name="how-office-365-group-expiration-works-with-retention-policy"></a>Jak wygasanie grupy usługi Office 365 działa z zasadami przechowywania

Zasady przechowywania są konfigurowane za pomocą Centrum zabezpieczeń i zgodności. Jeśli skonfigurowano zasady przechowywania dla grup usługi Office 365, gdy grupa wygaśnie i zostanie usunięta, konwersacje grupowe w skrzynce pocztowej grupy i pliki w witrynie grupy są zachowywane w kontenerze przechowywania przez określoną liczbę dni zdefiniowaną w przechowalni Zasad. Użytkownicy nie zobaczą grupy ani jej zawartości po wygaśnięciu, ale mogą odzyskać dane witryny i skrzynki pocztowej za pośrednictwem e-discovery.

## <a name="powershell-examples"></a>Przykłady programu PowerShell

Oto przykłady sposobu konfigurowania ustawień wygaśnięcia grup usługi Office 365 w organizacji usługi Azure AD za pomocą poleceń cmdlet programu PowerShell:

1. Zainstaluj moduł programu PowerShell w wersji 2.0 i zaloguj się w wierszu programu PowerShell:

   ``` PowerShell
   Install-Module -Name AzureAD
   Connect-AzureAD
   ```

1. Konfigurowanie ustawień wygaśnięcia Użyj polecenia cmdlet New-AzureADMSGroupLifecyclePolicy, aby ustawić okres istnienia dla wszystkich grup usługi Office 365 w organizacji usługi Azure AD na 365 dni. Powiadomienia o odnowieniu dla grup usługi Office 365 bez właścicieli będą wysyłane do 'emailaddress@contoso.com
  
   ``` PowerShell
   New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
   ```

1. Pobierz istniejące zasady Get-AzureADMSGroupLifecyclePolicy: To polecenie cmdlet pobiera bieżące ustawienia wygaśnięcia grupy usługi Office 365, które zostały skonfigurowane. W tym przykładzie można zobaczyć:

   - Identyfikator zasad
   - Okres istnienia wszystkich grup usługi Office 365 w organizacji usługi Azure AD wynosi 365 dni
   - Powiadomienia o odnowieniu dla grup usługi Office 365 bez właścicieli będą wysyłane do 'emailaddress@contoso.com.'
  
   ```powershell
   Get-AzureADMSGroupLifecyclePolicy
  
   ID                                    GroupLifetimeInDays ManagedGroupTypes AlternateNotificationEmails
   --                                    ------------------- ----------------- ---------------------------
   26fcc232-d1c3-4375-b68d-15c296f1f077  365                 All               emailaddress@contoso.com
   ```

1. Zaktualizuj istniejące zasady Set-AzureADMSGroupLifecyclePolicy: To polecenie cmdlet służy do aktualizowania istniejących zasad. W poniższym przykładzie okres istnienia grupy w istniejących zasadach został zmieniony z 365 dni na 180 dni.
  
   ```powershell
   Set-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -GroupLifetimeInDays 180 -AlternateNotificationEmails "emailaddress@contoso.com"
   ```
  
1. Dodaj określone grupy do zasad Add-AzureADMSLifecyclePolicyGroup: To polecenie cmdlet dodaje grupę do zasad cyklu życia. Przykład:
  
   ```powershell
   Add-AzureADMSLifecyclePolicyGroup -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -groupId "cffd97bd-6b91-4c4e-b553-6918a320211c"
   ```
  
1. Usuń istniejącą zasadę Usuń-AzureADMSGroupLifecyclePolicy: To polecenie cmdlet usuwa ustawienia wygaśnięcia grupy usługi Office 365, ale wymaga identyfikatora zasad. To polecenie cmdlet wyłącza wygaśnięcie dla grup usługi Office 365.
  
   ```powershell
   Remove-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077"
   ```
  
Następujące polecenia cmdlet mogą służyć do konfigurowania zasad bardziej szczegółowo. Aby uzyskać więcej informacji, zobacz [dokumentację programu PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&branch=master#groups).

- Get-AzureADMSGroupLifecyclePolicy
- Nowa AzureADMSGroupLifecyclePolicy
- Get-AzureADMSGroupLifecyclePolicy
- Set-AzureADMSGroupLifecyclePolicy
- Usuń-AzureADMSGroupLifecyclePolicy
- Grupa dodawania AzureADMSLifecyclePolicyGroup
- Usuń-AzureAdmSLifecyclePolicyGroup
- Grupa Reset-AzureADMSLifeCycleGroup
- Grupa polityk get-AzureAdmsLifecyclePolicyGroup

## <a name="next-steps"></a>Następne kroki

Te artykuły zawierają dodatkowe informacje na temat grup usługi Azure AD.

- [Wyświetlanie istniejących grup](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Zarządzanie ustawieniami grupy](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Zarządzanie członkami grupy](../fundamentals/active-directory-groups-members-azure-portal.md)
- [Zarządzanie członkostwem w grupie](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Zarządzanie regułami dynamicznymi dla użytkowników w grupie](groups-dynamic-membership.md)
