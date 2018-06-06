---
title: Przywracanie usuniętej grupy usługi Office 365 w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Przywracanie usuniętej grupie, wyświetlanie dostępnych grup i usuwanie permamnently grupy w usłudze Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: article
ms.date: 08/28/2017
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro
ms.openlocfilehash: f4ee875ee2fbe95c5d5081914139799e2bd4497f
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34724693"
---
# <a name="restore-a-deleted-office-365-group-in-azure-active-directory"></a>Przywracanie usuniętej grupy usługi Office 365 w usłudze Azure Active Directory

Podczas usuwania grupy usługi Office 365 w usłudze Azure Active Directory (Azure AD), usuwana grupa jest zachowane, ale nie są widoczne przez 30 dni od daty usunięcia. Jest tak, aby nazwa grupy i jego zawartość można przywrócić w razie potrzeby. Ta funkcja jest ograniczone wyłącznie do grup usługi Office 365 w usłudze Azure AD. Nie jest dostępna dla grup zabezpieczeń i grup dystrybucyjnych.

> [!NOTE] 
> Nie używaj `Remove-MsolGroup` ponieważ Przeczyszcza grupy trwałe. Zawsze używaj `Remove-AzureADMSGroup` można usunąć grupy usługi Office 365. 

Uprawnień wymaganych do przywrócenia grupy może być jedną z następujących czynności:

Rola | Uprawnienia 
--------- | ---------
Administrator firmy, pomocy partnera Tier2 i Administratorzy usługi InTune | Można przywrócić wszystkie usunięte grupy usługi Office 365 
Administrator konta użytkownika i Tier1 partnera pomocy technicznej | Można przywrócić wszystkie usunięte grupy usługi Office 365 z wyjątkiem tych, przypisane do roli administratora firmy 
Użytkownik | Można przywrócić wszystkie usuniętej grupy usługi Office 365, które są jego własnością 


## <a name="view-the-deleted-office-365-groups-that-are-available-to-restore"></a>Wyświetlanie usuniętych grup usługi Office 365, które są dostępne do przywrócenia
Następujące polecenia cmdlet umożliwia wyświetlanie usuniętych grup, aby sprawdzić, czy co najmniej te, które są zainteresowani nie jeszcze zostały trwale usunięte. Te polecenia cmdlet są częścią [modułu Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/). Więcej informacji na temat tego modułu można znaleźć w [Azure Active Directory programu PowerShell w wersji 2](/powershell/azure/install-adv2?view=azureadps-2.0) artykułu.

1.  Uruchom następujące polecenie cmdlet, aby wyświetlić wszystkich usuniętych grup usługi Office 365 w dzierżawie, które są nadal dostępne do przywrócenia.
 ```
 Get-AzureADMSDeletedGroup
 ```

2.  Jeśli znasz identyfikator obiektu określonej grupy (, możesz pobrać go z polecenia cmdlet w kroku 1) uruchom następujące polecenie cmdlet, aby sprawdzić, czy określone usuniętej grupy nie jeszcze trwale usunięto.
 ```
 Get-AzureADMSDeletedGroup –Id <objectId>
 ```



## <a name="how-to-restore-your-deleted-office-365-group"></a>Przywracanie usuniętych grupy usługi Office 365
Po upewnieniu się, że grupa jest nadal dostępne do przywrócenia, przywracanie usuniętej grupy z jedną z następujących czynności. Jeśli grupa zawiera dokumenty, SP witryn lub inne obiekty trwałe, może potrwać do 24 godzin pełni przywrócić grupy i jego zawartość.

1.  Uruchom następujące polecenie cmdlet, aby przywrócić grupy i jego zawartość.
 
 ```
 Restore-AzureADMSDeletedDirectoryObject –Id <objectId>
 ``` 

Można też uruchomić następujące polecenie cmdlet na stałe usunąć do usuniętej grupy.
 ```
 Remove-AzureADMSDeletedDirectoryObject –Id <objectId>
 ```

## <a name="how-do-you-know-this-worked"></a>Skąd wiadomo, to pracy?
Aby sprawdzić, czy został pomyślnie przywrócił grupy usługi Office 365, uruchom `Get-AzureADGroup –ObjectId <objectId>` polecenia cmdlet, aby wyświetlić informacje o grupie. Po przywróceniu jest ukończone żądania:
- Grupa pojawi się na lewym pasku nawigacyjnym na serwerze Exchange
- Plan dla tej grupy będą wyświetlane w planowania
- Witryny programu Sharepoint i wszystkie ich zawartość będzie dostępna
- Grupy jest możliwy za pomocą dowolnego punkty końcowe programu Exchange i innych obciążeń usługi Office 365, które obsługują grup usługi Office 365


## <a name="next-steps"></a>Kolejne kroki
Te artykuły zawierają dodatkowe informacje o grupach usługi Azure Active Directory.

* [Zobacz istniejących grup](active-directory-groups-view-azure-portal.md)
* [Zarządzanie ustawieniami grupy](active-directory-groups-settings-azure-portal.md)
* [Elementy członkowskie grupy zarządzania](active-directory-groups-members-azure-portal.md)
* [Zarządzanie członkostwami grup](active-directory-groups-membership-azure-portal.md)
* [Dynamiczne reguły dla użytkowników w grupie zarządzania](active-directory-groups-dynamic-membership-azure-portal.md)
