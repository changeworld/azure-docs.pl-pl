---
title: Zarządzanie rolami usługi Azure AD w zarządzania tożsamościami uprzywilejowanymi (PIM) | Dokumenty firmy Microsoft
description: Jak zarządzać rolami usługi Azure AD dla zarządzania tożsamościami uprzywilejowanymi przydziałami (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5d63577cbdbdf18cb17618bdb5c9e3b5de0e44a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245982"
---
# <a name="management-capabilities-for-azure-ad-roles-in-privileged-identity-management"></a>Możliwości zarządzania rolami usługi Azure AD w zarządzania tożsamościami uprzywilejowanymi

Środowisko zarządzania rolami usługi Azure AD w zarządzania tożsamościami uprzywilejowanymi zostało zaktualizowane w celu ujednolicenia sposobu zarządzania rolami usługi Azure AD i rolami zasobów platformy Azure. Wcześniej zarządzanie tożsamościami uprzywilejowanymi dla ról zasobów platformy Azure miało kilka kluczowych funkcji, które nie były dostępne dla ról usługi Azure AD.

Po aktualizacji jest obecnie wdrażany, łączymy dwa w jednym środowiska zarządzania, a w nim można uzyskać taką samą funkcjonalność dla ról usługi Azure AD, jak dla ról zasobów platformy Azure. Ten artykuł informuje o zaktualizowanych funkcjach i wszelkich wymaganiach.


## <a name="time-bound-assignments"></a>Przydziały z terminem

Wcześniej w dziale Zarządzanie tożsamościami uprzywilejowanymi dla ról usługi Azure AD były znane z przypisaniami ról z dwoma możliwymi stanami — *kwalifikującymi się* i *stałymi.* Teraz możesz ustawić godzinę rozpoczęcia i zakończenia dla każdego typu przypisania. Ten dodatek zawiera cztery możliwe stany, w których można umieścić zadanie:

- Kwalifikują się na stałe
- Aktywny na stałe
- Kwalifikujące się, z określonymi datami rozpoczęcia/zakończenia przydziału
- Aktywne, z określonymi datami rozpoczęcia/zakończenia przydziału

W wielu przypadkach, nawet jeśli nie chcesz, aby użytkownicy mieli kwalifikujące się przypisanie i aktywować role za każdym razem, nadal możesz chronić swoją organizację usługi Azure AD, ustawiając czas wygaśnięcia przydziałów. Jeśli na przykład masz kilku użytkowników tymczasowych, którzy kwalifikują się, rozważ ustawienie wygaśnięcia, aby automatycznie usunąć je z przypisania roli po zakończeniu pracy.

## <a name="new-role-settings"></a>Nowe ustawienia roli

Dodajemy również nowe ustawienia ról usługi Azure AD. Wcześniej można było skonfigurować ustawienia aktywacji tylko dla na podstawie roli. Oznacza to, że ustawienia aktywacji, takie jak wymagania dotyczące uwierzytelniania wieloskładnikowego i wymagania dotyczące biletów incydentów/żądań, zostały zastosowane do wszystkich użytkowników kwalifikujących się do określonej roli. Teraz można skonfigurować, czy pojedynczy użytkownik musi wykonać uwierzytelnianie wieloskładnikowe, zanim będzie mógł aktywować rolę. Ponadto można mieć zaawansowaną kontrolę nad wiadomościami e-mail zarządzania tożsamościami uprzywilejowanymi związanymi z określonymi rolami.

## <a name="extend-and-renew-assignments"></a>Rozszerzanie i odnawianie przydziałów

Jak tylko dowiedzieć się przydział związany z czasem, pierwsze pytanie, które można zadać jest co się stanie, jeśli rola wygasła? W tej nowej wersji udostępniamy dwie opcje dla tego scenariusza:

- Rozszerzanie — gdy przypisanie roli zbliża się do wygaśnięcia, użytkownik może użyć zarządzania tożsamościami uprzywilejowanymi, aby zażądać rozszerzenia dla tego przypisania roli
- Odnawianie — po wygaśnięciu przypisania roli użytkownik może użyć usługi Zarządzanie tożsamościami uprzywilejowanymi, aby zażądać odnowienia dla tego przypisania roli

Obie akcje inicjowane przez użytkownika wymagają zatwierdzenia przez administratora globalnego lub administratora ról uprzywilejowanych. Administratorzy nie muszą już być w branży zarządzania tymi wygaśnięciami. Wystarczy poczekać na żądania rozszerzenia lub odnowienia i zatwierdzić je, jeśli żądanie jest prawidłowe.

## <a name="api-changes"></a>Zmiany api

Gdy klienci mają zaktualizowaną wersję wdrożoną w swojej organizacji usługi Azure AD, istniejący interfejs API wykresu przestanie działać. Aby korzystać z [interfejsu API wykresu dla ról zasobów platformy Azure,](https://docs.microsoft.com/graph/api/resources/privilegedidentitymanagement-resources?view=graph-rest-beta)należy przejść do przejścia. Aby zarządzać rolami usługi Azure `/azureResources` `/aadroles` AD przy użyciu tego interfejsu API, `resourceId`zamienić z podpisem i użyć identyfikatora katalogu dla .

Dołożyliśmy wszelkich starań, aby dotrzeć do wszystkich klientów, którzy korzystają z poprzedniego interfejsu API, aby poinformować ich o tej zmianie z wyprzedzeniem. Jeśli twoja organizacja usługi Azure AD została przeniesiona do nowej wersji i nadal pim_preview@microsoft.comzależy od starego interfejsu API, skontaktuj się z zespołem w programie .

## <a name="powershell-change"></a>Zmiana programu PowerShell

Dla klientów, którzy korzystają z modułu PowerShell zarządzania tożsamościami uprzywilejowanymi dla ról usługi Azure AD, program PowerShell przestanie pracować z aktualizacją. Zamiast poprzednich poleceń cmdlet należy użyć poleceń cmdlet zarządzania tożsamościami uprzywilejowanych wewnątrz modułu programu PowerShell usługi Azure AD Preview. Zainstaluj moduł programu Azure AD PowerShell z [Galerii programu PowerShell](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Teraz można [odczytać dokumentację i przykłady operacji pim w tym module programu PowerShell](powershell-for-azure-ad-roles.md).

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie roli niestandardowej usługi Azure AD](azure-ad-custom-roles-assign.md)
- [Usuwanie lub aktualizowanie przypisania niestandardowego zadania usługi Azure AD](azure-ad-custom-roles-update-remove.md)
- [Konfigurowanie przypisania niestandardowego zadania usługi Azure AD](azure-ad-custom-roles-configure.md)
- [Definicje ról w usłudze Azure AD](../users-groups-roles/directory-assign-admin-roles.md)
