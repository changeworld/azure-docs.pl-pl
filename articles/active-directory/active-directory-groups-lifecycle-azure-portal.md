---
title: Grupuje ważności dla usługi Office 365 w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Jak skonfigurować wygaśnięcia dla grup usługi Office 365 w usłudze Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2018
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro
ms.openlocfilehash: 1756ef0a1614c69d9490899969fc78c67b9e086f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="configure-the-expiration-policy-for-office-365-groups"></a>Skonfiguruj zasady wygasania dla grup usługi Office 365

Umożliwia teraz Zarządzanie cyklem życia grup usługi Office 365, ustawiając zasady wygasania dla nich. Tylko usługi Office 365 grup w usłudze Azure Active Directory (Azure AD), można ustawić zasady wygasania. 

Po ustawieniu grupy wygaśnie:
-   Właścicieli grupy są powiadamiani o odnowić grupy, jak będzie bliski wygaśnięcia
-   Wszystkie grupy, która nie zostanie odnowiony jest usuwany.
-   Można przywrócić żadnej grupy usługi Office 365, która zostanie usunięta w ciągu 30 dni przez właścicieli grupy lub administrator

> [!NOTE]
> Konfigurowanie i używanie zasady wygasania dla grup usługi Office 365 wymaga masz dostępnych licencji Azure AD Premium dla wszystkich członków grupy, do których zastosowano zasady wygasania.

Aby uzyskać informacje na temat Pobierz i zainstaluj polecenia cmdlet programu PowerShell usługi Azure AD, zobacz [Azure Active Directory PowerShell wykres 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137).

## <a name="roles-and-permissions"></a>Role i uprawnienia
Poniżej przedstawiono role, które można skonfigurować i używać wygaśnięcia dla grup usługi Office 365 w usłudze Azure AD.

Rola | Uprawnienia
-------- | --------
Administrator globalny lub Administrator konta użytkownika | Można tworzyć, odczytywać, aktualizować lub usuwać ustawienia zasad wygasania grup usługi Office 365<br>Mogą odnowić żadnej grupy usługi Office 365
Użytkownik | Mogą odnowić grupy usługi Office 365, w której jest właścicielem<br>Można przywrócić grupy usługi Office 365, w której jest właścicielem<br>Czas wygaśnięcia można odczytać ustawień zasad

Aby uzyskać więcej informacji dotyczących uprawnień, aby przywrócić usuniętej grupie, zobacz [Przywracanie usuniętej grupy usługi Office 365 w usłudze Azure Active Directory](active-directory-groups-restore-azure-portal.md).

## <a name="set-group-expiration"></a>Ustaw grupy wygaśnięcia

1. Otwórz [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta, które jest administratorem globalnym w dzierżawie usługi Azure AD.

2. Wybierz **grup**, a następnie wybierz pozycję **wygaśnięcia** aby otworzyć ustawienia wygaśnięcia.
  
  ![Blok wygaśnięcia](./media/active-directory-groups-lifecycle-azure-portal/expiration-settings.png)

4. Na **wygaśnięcia** bloku, można wykonywać następujące czynności:

  * Ustaw okres istnienia grupy w dniach. Możesz wybrać jedną z istniejących wartości lub wartości niestandardowych (powinien być 31 dni lub więcej). 
  * Określ adres e-mail, w którym mają być wysyłane powiadomienia odnowienia i wygaśnięcia po grupie nie ma ono właściciela. 
  * Wybierz grupy, które usługi Office 365 wygaśnie. Można włączyć wygaśnięcia dla **wszystkie** grup usługi Office 365, można włączyć tylko **wybrane** grup usługi Office 365, lub wybierz **Brak** wyłączyć wygaśnięcia dla wszystkich grup .
  * Zapisz ustawienia, gdy wszystko będzie gotowe, wybierając **zapisać**.


Powiadomienia e-mail, taką jak są wysyłane do właścicieli grupy usługi Office 365 30 dni, 15 dni i 1 dzień przed jego wygaśnięciem grupy.

![Powiadomienia e-mail o wygaśnięciu](./media/active-directory-groups-lifecycle-azure-portal/expiration-notification.png)

Z **grupy odnawiania** powiadomienia e-mail grupy Właściciele mogą bezpośrednio uzyskać dostępu do strony szczegółów grupy w panelu dostępu. Użytkownicy mogą uzyskać więcej informacji na temat grupy, takie jak jej opis, po jego został ostatnio odnowiony, datę wygaśnięcia, a także możliwość odnowić grupy. Strona szczegółów grupy teraz także linki do zasobów grupy usługi Office 365, tak aby właściciel grupy może wyświetlania zawartości i działań ich grupy.

Po wygaśnięciu grupy, grupa zostanie usunięta jeden dzień po dacie wygaśnięcia. Powiadomienie e-mail, taką jak są wysyłane do właścicieli grupy usługi Office 365 informująca o wygaśnięciu i kolejne usunięcie ich z grupy usługi Office 365.

![Powiadomienie e-mail usunięcia grupy](./media/active-directory-groups-lifecycle-azure-portal/deletion-notification.png)

Grupy można przywrócić w ciągu 30 dni od jego usunięcia, wybierając **grupy przywracania** lub za pomocą poleceń cmdlet programu PowerShell, zgodnie z opisem w [Przywracanie usuniętej grupy usługi Office 365 w usłudze Azure Active Directory](active-directory-groups-restore-azure-portal.md).
    
Jeśli grupy, które są przywracane zawiera dokumentów, witryn programu SharePoint lub inne obiekty trwałe, może potrwać do 24 godzin pełni przywrócić grupy i jego zawartość.

> [!NOTE]
> * Podczas pierwszej konfiguracji wygaśnięcia tych grup, które są starsze niż interwał ważności są ustawiane na 30 dni do wygaśnięcia. Pierwszy odnawiania powiadomienia e-mail jest wysyłane w ciągu dnia. 
>   Na przykład A grupa została utworzona 400 dni temu i interwał wygaśnięcia jest ustawiony na wartość 180 dni. Podczas stosowania ustawień wygasania grupy A ma 30 dni przed usunięciem, chyba że właściciel odnawia go.
> * Obecnie można skonfigurować tylko jedne zasady wygasania dla grup usługi Office 365 na dzierżawcy.
> * Gdy dynamiczna grupa jest usuwane i przywrócić, jest traktowany jako nową grupę i ponownie wypełniane zgodnie z zasadą. Ten proces może potrwać do 24 godzin.

## <a name="how-office-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>Jak działa wygaśnięcia grupy usługi Office 365 z skrzynkę pocztową z przyczyn prawnych
Gdy grupa wygasa i zostaje usunięta, następnie 30 dni, po usunięciu grupy danych z aplikacji, takich jak planowania, witryny, lub zespoły są trwale usuwane, ale skrzynki pocztowej grupy z przyczyn prawnych jest zachowywana i nie zostanie trwale usunięta. Administrator może być Przywróć skrzynkę pocztową, aby pobrać dane, poleceń cmdlet programu Exchange. 

## <a name="how-office-365-group-expiration-works-with-retention-policy"></a>Jak działa wygaśnięcia grupy usługi Office 365 z zasady przechowywania
Zasady przechowywania jest skonfigurowane na Centrum zgodności i zabezpieczeń. Jeśli zdefiniowano zasad przechowywania dla grup usługi Office 365, gdy grupa wygasa i zostanie usunięta, konwersacje grupy w skrzynce pocztowej grupy i pliki w witrynie grupy są zachowywane w kontenerze przechowywania określoną liczbę dni określoną w przechowywania zasady. Użytkownicy nie będą widzieć po wygaśnięciu grupy albo jej zawartość, ale można odzyskać danych lokacji i skrzynek pocztowych za pomocą zbieranie elektronicznych materiałów dowodowych.

## <a name="powershell-examples"></a>Przykłady programu PowerShell
Poniżej przedstawiono przykłady stosowania poleceń cmdlet programu PowerShell do konfigurowania ustawień wygasania dla grup usługi Office 365 w Twojej dzierżawie:

1. Zainstaluj moduł programu PowerShell w wersji 2.0 wersja zapoznawcza (2.0.0.137) i zaloguj się w wierszu polecenia programu PowerShell:
  ````
  Install-Module -Name AzureADPreview
  connect-azuread 
  ````
2. Skonfiguruj ustawienia wygasania nowy AzureADMSGroupLifecyclePolicy: to polecenie cmdlet Ustawia okres istnienia dla wszystkich grup usługi Office 365 w dzierżawie do 365 dni. Powiadomienia odnawiania dla usługi Office 365 grupy bez właścicieli będą wysyłane do "emailaddress@contoso.com"
  
  ````
  New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
  ````
3. Pobierz istniejące zasady Get-AzureADMSGroupLifecyclePolicy: to polecenie cmdlet pobiera bieżące skonfigurowane ustawienia wygaśnięcia grupy usługi Office 365. W tym przykładzie widać:
  * Identyfikator zasad 
  * Okres istnienia dla wszystkich grup usługi Office 365 w dzierżawie ustawiono 365 dni
  * Powiadomienia odnawiania dla usługi Office 365 grupy bez właścicieli będą wysyłane do "emailaddress@contoso.com."
  
  ````
  Get-AzureADMSGroupLifecyclePolicy
  
  ID                                    GroupLifetimeInDays ManagedGroupTypes AlternateNotificationEmails
  --                                    ------------------- ----------------- ---------------------------
  26fcc232-d1c3-4375-b68d-15c296f1f077  365                 All               emailaddress@contoso.com
  ```` 
   
4. Aktualizowanie istniejących zasad AzureADMSGroupLifecyclePolicy zestawu: to polecenie cmdlet jest używane do aktualizowania istniejących zasad. W poniższym przykładzie okres istnienia grupy w istniejących zasad została zmieniona z 365 dni na 180 dni. 
  
  ````
  Set-AzureADMSGroupLifecyclePolicy -Id “26fcc232-d1c3-4375-b68d-15c296f1f077”   -GroupLifetimeInDays 180 -AlternateNotificationEmails "emailaddress@contoso.com"
  ````
  
5. Dodaj określonych grup do zasad, Dodaj AzureADMSLifecyclePolicyGroup: to polecenie cmdlet dodaje grupę do zasad cyklu życia. Na przykład: 
  
  ````
  Add-AzureADMSLifecyclePolicyGroup -Id “26fcc232-d1c3-4375-b68d-15c296f1f077” -groupId "cffd97bd-6b91-4c4e-b553-6918a320211c"
  ````
  
6. Usuń istniejące zasady Remove-AzureADMSGroupLifecyclePolicy: to polecenie cmdlet usuwa ustawienia wygaśnięcia grupy usługi Office 365, ale wymaga identyfikatora zasad. Spowoduje to wyłączenie wygaśnięcia dla grup usługi Office 365. 
  
  ````
  Remove-AzureADMSGroupLifecyclePolicy -Id “26fcc232-d1c3-4375-b68d-15c296f1f077”
  ````
  
Następujące polecenia cmdlet można skonfigurować zasady bardziej szczegółowo. Aby uzyskać więcej informacji, zobacz [dokumentacji programu PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&branch=master#groups).

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
Te artykuły zawierają dodatkowe informacje o grup usługi Azure AD.

* [Zobacz istniejących grup](active-directory-groups-view-azure-portal.md)
* [Zarządzanie ustawieniami grupy](active-directory-groups-settings-azure-portal.md)
* [Elementy członkowskie grupy zarządzania](active-directory-groups-members-azure-portal.md)
* [Zarządzanie członkostwami grup](active-directory-groups-membership-azure-portal.md)
* [Dynamiczne reguły dla użytkowników w grupie zarządzania](active-directory-groups-dynamic-membership-azure-portal.md)
