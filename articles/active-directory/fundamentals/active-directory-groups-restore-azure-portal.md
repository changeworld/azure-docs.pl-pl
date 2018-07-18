---
title: Przywracanie usuniętej grupy usługi Office 365 w usłudze Azure AD | Microsoft Docs
description: Jak przywrócić usuniętą grupę, wyświetlić grupy możliwe do przywrócenia i trwale usunąć grupę w usłudze Azure Active Directory
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: quickstart
ms.date: 08/28/2017
ms.author: lizross
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 621260f0af781799c72fbfaed0900a68383044a1
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2018
ms.locfileid: "37860468"
---
# <a name="restore-a-deleted-office-365-group-in-azure-active-directory"></a>Przywracanie usuniętej grupy usługi Office 365 w usłudze Azure Active Directory

Po usunięciu grupy usługi Office 365 w usłudze Azure Active Directory (Azure AD) usunięta grupa jest zachowywana, ale nie jest widoczna przez 30 dni od daty usunięcia. To dlatego, że grupy i ich zawartość można przywrócić w razie potrzeby. Ta funkcja jest ograniczona wyłącznie do grup usługi Office 365 w usłudze Azure AD. Nie jest dostępna dla grup zabezpieczeń i grup dystrybucji.

> [!NOTE] 
> Nie używaj opcji `Remove-MsolGroup`, ponieważ powoduje trwale wyczyszczenie grup. W celu usunięcia grupy usługi Office 365 zawsze używaj opcji `Remove-AzureADMSGroup`. 

Uprawnienia wymagane do przywrócenia grupy mogą być dowolnymi z następujących:

Rola | Uprawnienia 
--------- | ---------
Administrator firmy, Pomoc techniczna dla partnerów (warstwa 2) i Administratorzy usługi Intune | Może przywrócić dowolne usunięte grupy usługi Office 365 
Administrator kont użytkowników i Pomoc techniczna dla partnerów (warstwa 1) | Może przywrócić wszystkie usunięte grupy usługi Office 365, z wyjątkiem tych przypisanych do roli Administrator firmy 
Użytkownik | Może przywrócić wszystkie usunięte grupy usługi Office 365, które są jego własnością 


## <a name="view-the-deleted-office-365-groups-that-are-available-to-restore"></a>Wyświetl usunięte grupy usługi Office 365, które są dostępne do przywrócenia
Prz użyciu poniższych poleceń cmdlet możesz wyświetlić usunięte grupy, aby sprawdzić, czy te, które Cię interesują, nie zostały jeszcze trwale usunięte. Te polecenia cmdlet są częścią [modułu usługi Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/). Więcej informacji na temat tego modułu można znaleźć w artykule [Azure Active Directory PowerShell w wersji 2](/powershell/azure/install-adv2?view=azureadps-2.0).

1.  Uruchom następujące polecenie cmdlet, aby wyświetlić wszystkie usunięte grupy usługi Office 365 w Twojej dzierżawie, które są nadal dostępne do przywrócenia.
 ```
 Get-AzureADMSDeletedGroup
 ```

2.  Ewentualnie, jeśli znasz identyfikator obiektu określonej grupy (i możesz go pobrać, korzystając z polecenia cmdlet w kroku 1), uruchom następujące polecenie cmdlet, aby sprawdzić, czy określona usunięta grupa nie została jeszcze trwale usunięta.
 ```
 Get-AzureADMSDeletedGroup –Id <objectId>
 ```



## <a name="how-to-restore-your-deleted-office-365-group"></a>Jak przywrócić usuniętą grupę usługi Office 365
Po upewnieniu się, że grupa jest nadal dostępna do przywrócenia, możesz przywrócić usuniętą grupę za pomocą jednego z następujących kroków. Jeśli grupa zawiera dokumenty, witryny usługi SharePoint lub inne trwałe obiekty, całkowite przywrócenie grupy i jej zawartości może zająć do 24 godzin.

1.  Uruchom następujące polecenie cmdlet, aby przywrócić grupę i jej zawartość.
 
 ```
 Restore-AzureADMSDeletedDirectoryObject –Id <objectId>
 ``` 

Możesz również uruchomić następujące polecenie cmdlet, aby trwale usunąć usuniętą grupę.
 ```
 Remove-AzureADMSDeletedDirectoryObject –Id <objectId>
 ```

## <a name="how-do-you-know-this-worked"></a>Skąd wiadomo, że to zadziałało?
Aby sprawdzić, czy grupa usługi Office 365 została pomyślnie przywrócona, uruchom polecenie cmdlet `Get-AzureADGroup –ObjectId <objectId>` w celu wyświetlenia informacji o grupie. Po ukończeniu żądania przywrócenia:
- Grupy będą wyświetlane na lewym pasku nawigacyjnym w programie Exchange
- Plan dla grupy zostanie wyświetlony w programie Planner
- Wszystkie witryny programu SharePoint i cała ich zawartość będą dostępne
- Dostęp do grup można będzie uzyskać z dowolnych punktów końcowych programu Exchange i innych obciążeń usługi Office 365, które obsługują grupy usługi Office 365


## <a name="next-steps"></a>Następne kroki
Te artykuły zawierają dodatkowe informacje o grupach usługi Azure Active Directory.

* [Wyświetlanie istniejących grup](active-directory-groups-view-azure-portal.md)
* [Zarządzanie ustawieniami grupy](active-directory-groups-settings-azure-portal.md)
* [Zarządzanie członkami grupy](active-directory-groups-members-azure-portal.md)
* [Zarządzanie członkostwem w grupie](active-directory-groups-membership-azure-portal.md)
* [Zarządzanie regułami dynamicznymi dla użytkowników w grupie](../users-groups-roles/groups-dynamic-membership.md)
