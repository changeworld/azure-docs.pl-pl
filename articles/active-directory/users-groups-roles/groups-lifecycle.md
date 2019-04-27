---
title: Ustawienia wygasania grup usługi Office 365 — usługi Azure Active Directory | Dokumentacja firmy Microsoft
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
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c19ee3bdd14ee6a2c5b59294f475f6c18b570fa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60471960"
---
# <a name="configure-the-expiration-policy-for-office-365-groups"></a>Konfigurowanie zasad wygasania grup usługi Office 365

Można teraz zarządzać cyklem życia grup usługi Office 365, ustawiając zasady wygasania dla nich. Zasady wygasania możesz ustawić tylko grup usługi Office 365 w usłudze Azure Active Directory (Azure AD). 

Po ustawieniu grupy wygaśnie:

- Właściciele grupy powiadomi o konieczności odnowienia grupy, zgodnie z zbliżą się do czasu wygaśnięcia
- Wszystkie grupy, która nie zostanie odnowiona, zostanie usunięty
- Można przywrócić żadnej grupy usługi Office 365, które zostało usunięte w ciągu 30 dni, właściciele grupy lub przez administratora

Aktualnie można skonfigurować tylko jedną zasadę wygasania grup usługi Office 365 w dzierżawie.

> [!NOTE]
> Konfigurowanie i używanie zasady wygasania grup usługi Office 365 wymaga licencje na usługi Azure AD Premium dla wszystkich członków grupy, do których zastosowano zasady wygasania.

Aby uzyskać informacje na temat sposobu pobierania i instalowania poleceń cmdlet programu PowerShell usługi Azure AD, zobacz [Azure Active Directory PowerShell wykres 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137).

## <a name="roles-and-permissions"></a>Role i uprawnienia

Dostępne są następujące role, które można skonfigurować na użytek wygasania grup usługi Office 365 w usłudze Azure AD.

Rola | Uprawnienia
-------- | --------
Administrator globalny lub administrator użytkowników | Można utworzyć, odczytu, aktualizacji lub usunięcia ustawień zasad wygasania grup usługi Office 365<br>Można odnowić żadnej grupy usługi Office 365
Użytkownik | Można odnowić grupy usługi Office 365, do której jest właścicielem<br>Można przywrócić grupy usługi Office 365, do której jest właścicielem<br>Czas wygaśnięcia może odczytać ustawienia zasad

Aby uzyskać więcej informacji na temat uprawnień, aby przywrócić usunięte grupy, zobacz [Przywracanie usuniętej grupy usługi Office 365 w usłudze Azure Active Directory](groups-restore-deleted.md).

## <a name="set-group-expiration"></a>Ustawianie czasu wygaśnięcia grup

1. Otwórz [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta, które jest administratorem globalnym w swojej dzierżawie usługi Azure AD.

2. Wybierz **grup**, a następnie wybierz **wygaśnięcia** aby otworzyć ustawienia wygaśnięcia.
  
   ![Ustawienia wygasania grup](./media/groups-lifecycle/expiration-settings.png)

3. Na **wygaśnięcia** bloku, możesz:

  * Ustaw okres istnienia grupy w dniach. Można wybrać jeden z wstępnie zdefiniowanych wartości lub niestandardową wartość (powinien mieć co najmniej 31 dni). 
  * Określ adres e-mail, w którym mają być wysyłane powiadomienia odnowienia i wygaśnięcia gdy grupa nie ma właściciela. 
  * Wybierz grupy usługi Office 365, które wygasną. Można włączyć wygaśnięcie dla **wszystkich** grup usługi Office 365, możesz włączyć tylko **wybrane** grup usługi Office 365, lub wybierz **Brak** wyłączyć wygaśnięcia dla wszystkich grup .
  * Zapisz ustawienia w przypadku, gdy wszystko będzie gotowe, wybierając **Zapisz**.

> [!NOTE]
> * Jeśli najpierw skonfigurować wygaśnięcie żadnych grup, które są starsze niż interwał wygasania są ustawione na 30 dni do wygaśnięcia. Pierwszy odnowienia powiadomienie e-mail zostanie wysłane w ciągu dnia. Na przykład A grupa została utworzona 400 dni temu, a interwał wygasania jest ustawiona na 180 dni. Po zastosowaniu ustawień wygasania grupy A ma 30 dni przed usunięciem, chyba że właściciel odnawia je.
> * Grupa dynamiczna, która została usunięta i przywrócona, jest widoczna jako nowa grupa. Jest ona ponownie wypełniana zgodnie z regułą. Ten proces może potrwać do 24 godzin.

## <a name="email-notifications"></a>Powiadomienia e-mail

Taką jak ta są wysyłane powiadomienia e-mail właścicielom grup usługi Office 365 30 dni, 15 dni i 1 dzień przed wygaśnięciem grupy. Język wiadomości e-mail jest określana przez właściciela grupy preferowanego języka lub języku dzierżawy. Jeśli właściciel grupy zdefiniował preferowanego języka lub wielu właścicielom mają ten sam język preferowany, używany jest ten język. W pozostałych przypadkach jest używany język dzierżawy.

![Powiadomienia e-mail wygaśnięcia](./media/groups-lifecycle/expiration-notification.png)

Z **Odnów grupę** wiadomość e-mail z powiadomieniem i grupy Właściciele mogą bezpośrednio dostęp do strony szczegółów grupy w panelu dostępu. Użytkownicy mogą uzyskać więcej informacji na temat grupy, takiej jak jej opis jej ostatniego Odnowiono, datę wygaśnięcia, a także możliwość Odnów grupę. Strona szczegółów grupy teraz zawiera także łącza do zasobów grupy usługi Office 365, tak aby właściciel grupy wygodnie wyświetlić zawartość i działania w swojej grupie.

Po wygaśnięciu grupy Grupa została usunięta jeden dzień po dacie wygaśnięcia. Powiadomienie e-mail, taką jak ta są wysyłane do właścicieli grupy usługi Office 365 z informacją o wygaśnięciu i późniejsze usunięcie ich z grupy usługi Office 365.

![Powiadomienia e-mail usunięcie grupy](./media/groups-lifecycle/deletion-notification.png)

Grupy można przywrócić w ciągu 30 dni od jego usunięcia, wybierając **Przywracanie grupy** lub za pomocą poleceń cmdlet programu PowerShell, zgodnie z opisem w [Przywracanie usuniętej grupy usługi Office 365 w usłudze Azure Active Directory](groups-restore-deleted.md). Należy pamiętać, że grupy 30-dniowego okresu przywracania nie jest możliwe do dostosowania.
    
Jeśli grupy, do której są przywracane zawiera dokumentów, witryn programu SharePoint lub inne obiekty trwałe, może upłynąć do 24 godzin potrzeba pełnego przywrócenia grupy i jego zawartość.

## <a name="how-to-retrieve-office-365-group-expiration-date"></a>Jak pobrać datę wygaśnięcia grupy usługi Office 365
Oprócz panelu dostępu, w którym użytkownicy mogą wyświetlać szczegóły grupy, w tym datę wygaśnięcia i Data ostatniego odnowienia datę wygaśnięcia grupy usługi Office 365 mogą być pobierane z programu Microsoft Graph REST API w wersji Beta. expirationDateTime jako właściwość grupy została włączona w wersji Beta programu Microsoft Graph. Mogą być pobierane przy użyciu żądania GET. Aby uzyskać więcej informacji, zapoznaj się [w tym przykładzie](https://docs.microsoft.com/en-us/graph/api/group-get?view=graph-rest-beta#example).

> [!NOTE]
> Aby zarządzać członkostwem w grupach na panelu dostępu, "Ogranicz dostęp do grup w panelu dostępu" musi być równa "No", w ustawieniu ogólne usługi Azure Active Directory grupy.


## <a name="how-office-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>Jak działa wygaśnięcie grup usługi Office 365 ze skrzynką pocztową z przyczyn prawnych
Gdy grupa wygaśnie i zostanie usunięty, następnie w ciągu 30 dni po usunięciu grupy danych z aplikacji, takich jak narzędzia planista witryn, lub zespoły są trwale usuwane, ale skrzynki pocztowej grupy, która jest z przyczyn prawnych są przechowywane i nie zostanie trwale usunięta. Administrator może użyć poleceń cmdlet programu Exchange do Przywróć skrzynkę pocztową, aby pobrać dane. 

## <a name="how-office-365-group-expiration-works-with-retention-policy"></a>Jak działa wygaśnięcie grup usługi Office 365 za pomocą zasad przechowywania
Zasady przechowywania zostały skonfigurowane za pomocą Centrum zabezpieczeń i zgodności. Jeśli po skonfigurowaniu zasad przechowywania dla grup usługi Office 365, gdy grupa wygaśnie i zostanie usunięty, konwersacje grupy w skrzynce pocztowej grupy i pliki w witrynie grupy są przechowywane w kontenerze przechowywania przez określoną liczbę dni określoną w przechowywania zasady. Użytkownicy nie będą widoczne po wygaśnięciu grupy lub jego zawartość, ale można odzyskać dane lokacji i skrzynek pocztowych za pośrednictwem zbieranie elektronicznych materiałów dowodowych.

## <a name="powershell-examples"></a>Przykłady programu PowerShell
Poniżej przedstawiono przykłady jak można użyć poleceń cmdlet programu PowerShell, aby skonfigurować ustawienia wygasania grup usługi Office 365 w Twojej dzierżawie:

1. Zainstaluj moduł programu PowerShell w wersji 2.0 (wersja zapoznawcza) (2.0.0.137), a następnie zaloguj się w wierszu polecenia programu PowerShell:
   ```powershell
   Install-Module -Name AzureADPreview
   connect-azuread 
   ```
2. Skonfiguruj ustawienia wygaśnięcia New-AzureADMSGroupLifecyclePolicy:  To polecenie cmdlet Ustawia okres istnienia dla wszystkich grup usługi Office 365 w ramach dzierżawy do 365 dni. Powiadomienia o odnowieniu dla usługi Office 365 grup bez właścicieli zostanie wysłany do "emailaddress@contoso.com"
  
   ```powershell
   New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
   ```
3. Pobierz istniejące zasady Get-AzureADMSGroupLifecyclePolicy: To polecenie cmdlet pobiera bieżące, które zostały skonfigurowane ustawienia wygaśnięcia grupy usługi Office 365. W tym przykładzie widać:
   * Identyfikator zasad 
   * Okres istnienia dla wszystkich grup usługi Office 365 w ramach dzierżawy jest ustawiony do 365 dni
   * Powiadomienia o odnowieniu dla usługi Office 365 grup bez właścicieli zostanie wysłany do "emailaddress@contoso.com."
  
   ```powershell
   Get-AzureADMSGroupLifecyclePolicy
  
   ID                                    GroupLifetimeInDays ManagedGroupTypes AlternateNotificationEmails
   --                                    ------------------- ----------------- ---------------------------
   26fcc232-d1c3-4375-b68d-15c296f1f077  365                 All               emailaddress@contoso.com
   ``` 
   
4. Zaktualizuj istniejące zasady zestaw-AzureADMSGroupLifecyclePolicy: To polecenie cmdlet używane do aktualizowania istniejącej zasady. W poniższym przykładzie okres istnienia grupy w istniejących zasad została zmieniona z 365 dni do 180 dni. 
  
   ```powershell
   Set-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -GroupLifetimeInDays 180 -AlternateNotificationEmails "emailaddress@contoso.com"
   ```
  
5. Dodaj określonych grup do zasad Add-AzureADMSLifecyclePolicyGroup: To polecenie cmdlet służy do dodawania grupy zasad cyklu życia. Na przykład:
  
   ```powershell
   Add-AzureADMSLifecyclePolicyGroup -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -groupId "cffd97bd-6b91-4c4e-b553-6918a320211c"
   ```
  
6. Usuń istniejące zasady Remove-AzureADMSGroupLifecyclePolicy: To polecenie cmdlet usuwa ustawienia wygasania grup usługi Office 365, ale wymaga identyfikatora zasad. Spowoduje to wyłączenie wygasania grup usługi Office 365. 
  
   ```powershell
   Remove-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077"
   ```
  
Następujące polecenia cmdlet, można skonfigurować zasady bardziej szczegółowo. Aby uzyskać więcej informacji, zobacz [dokumentacji programu PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&branch=master#groups).

* Get-AzureADMSGroupLifecyclePolicy
* New-AzureADMSGroupLifecyclePolicy
* Get-AzureADMSGroupLifecyclePolicy
* Set-AzureADMSGroupLifecyclePolicy
* Remove-AzureADMSGroupLifecyclePolicy
* Add-AzureADMSLifecyclePolicyGroup
* Remove-AzureADMSLifecyclePolicyGroup
* Reset-AzureADMSLifeCycleGroup   
* Get-AzureADMSLifecyclePolicyGroup

## <a name="next-steps"></a>Kolejne kroki
Te artykuły zawierają dodatkowe informacje na temat grup usługi Azure AD.

* [Wyświetlanie istniejących grup](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Zarządzanie ustawieniami grupy](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Zarządzanie członkami grupy](../fundamentals/active-directory-groups-members-azure-portal.md)
* [Zarządzanie członkostwem w grupie](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Zarządzanie regułami dynamicznymi dla użytkowników w grupie](groups-dynamic-membership.md)
