---
title: Jak zarządzać administratorami lokalnymi na urządzeniach przyłączonych do usługi Azure AD
description: Dowiedz się, jak przypisać role platformy Azure do lokalnej grupy administratorów urządzenia z systemem Windows.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc1812d955590ec0c7372e1311c9d69f93b9957c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128877"
---
# <a name="how-to-manage-the-local-administrators-group-on-azure-ad-joined-devices"></a>Jak zarządzać grupą administratorów lokalnych na urządzeniach przyłączonych do usługi Azure AD

Aby zarządzać urządzeniem z systemem Windows, musisz być członkiem lokalnej grupy administratorów. W ramach procesu dołączania usługi Azure Active Directory (Azure AD) usługa Azure AD aktualizuje członkostwo tej grupy na urządzeniu. Aktualizację członkostwa można dostosować do wymagań firmy. Aktualizacja członkostwa jest na przykład przydatna, jeśli chcesz włączyć pracowników działu pomocy technicznej wykonywanie zadań wymagających uprawnień administratora na urządzeniu.

W tym artykule wyjaśniono, jak działa aktualizacja członkostwa i jak można ją dostosować podczas dołączania do usługi Azure AD. Zawartość tego artykułu nie ma zastosowania do **hybrydowego** sprzężenia usługi Azure AD.

## <a name="how-it-works"></a>Jak to działa

Po podłączeniu urządzenia z systemem Windows z usługą Azure AD przy użyciu usługi Azure AD usługi Azure AD dodaje następujące zasady zabezpieczeń do lokalnej grupy administratorów na urządzeniu:

- Rola administratora globalnego usługi Azure AD
- Rola administratora urządzenia usługi Azure AD 
- Użytkownik wykonujący sprzężenie usługi Azure AD   

Dodając role usługi Azure AD do grupy administratorów lokalnych, można zaktualizować użytkowników, którzy mogą zarządzać urządzeniem w dowolnym momencie w usłudze Azure AD bez modyfikowania niczego na urządzeniu. Obecnie nie można przypisać grup do roli administratora.
Usługa Azure AD dodaje również rolę administratora urządzenia usługi Azure AD do grupy administratorów lokalnych, aby obsługiwać zasadę najmniejszych uprawnień (PoLP). Oprócz administratorów globalnych można również włączyć użytkownikom, których przypisano *tylko* rolę administratora urządzenia, zarządzanie urządzeniem. 

## <a name="manage-the-global-administrators-role"></a>Zarządzanie rolą administratorów globalnych

Aby wyświetlić i zaktualizować członkostwo w roli administratora globalnego, zobacz:

- [Wyświetlanie wszystkich członków roli administratora w usłudze Azure Active Directory](../users-groups-roles/directory-manage-roles-portal.md)
- [Przypisywanie użytkownika do ról administratorów w usłudze Azure Active Directory](../fundamentals/active-directory-users-assign-role-azure-portal.md)


## <a name="manage-the-device-administrator-role"></a>Zarządzanie rolą administratora urządzenia 

W witrynie Azure portal można zarządzać rolę administratora urządzenia na **urządzeniach** strony. Aby otworzyć stronę **Urządzenia:**

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako administrator globalny.
1. Wyszukaj i wybierz pozycję *Azure Active Directory*.
1. W sekcji **Zarządzanie** kliknij pozycję **Urządzenia**.
1. Na stronie **Urządzenia** kliknij pozycję **Ustawienia urządzenia**.

Aby zmodyfikować rolę administratora urządzenia, **skonfiguruj dodatkowych administratorów lokalnych na urządzeniach przyłączonych do usługi Azure AD**.  

![Dodatkowi administratorzy lokalni](./media/assign-local-admin/10.png)

>[!NOTE]
> Ta opcja wymaga dzierżawy usługi Azure AD Premium. 

Administratorzy urządzeń są przypisywani do wszystkich urządzeń przyłączonych do usługi Azure AD. Nie można określić zakresu administratorów urządzeń do określonego zestawu urządzeń. Aktualizowanie roli administratora urządzenia nie musi mieć natychmiastowego wpływu na użytkowników, których dotyczy problem. Na urządzeniach, na których użytkownik jest już zalogowany, aktualizacja uprawnień ma miejsce, gdy *obie* poniższe akcje mają miejsce:

- 4 godziny zostały przekazane dla usługi Azure AD do wydania nowego tokenu odświeżania podstawowego z odpowiednimi uprawnieniami. 
- Użytkownik wylogowuje się i loguje się z powrotem, a nie zablokuj /odbloku, aby odświeżyć swój profil.

## <a name="manage-regular-users"></a>Zarządzanie zwykłymi użytkownikami

Domyślnie usługa Azure AD dodaje użytkownika wykonującego sprzężenie usługi Azure AD do grupy administratorów na urządzeniu. Jeśli chcesz uniemożliwić zwykłym użytkownikom stanie się administratorami lokalnymi, masz następujące opcje:

- [Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot) — windows autopilot udostępnia opcję, aby uniemożliwić użytkownikowi głównemu wykonującemu sprzężenie stanie się administratorem lokalnym. Można to osiągnąć, [tworząc profil autopilota.](/intune/enrollment-autopilot#create-an-autopilot-deployment-profile)
- [Rejestracja zbiorcza](/intune/windows-bulk-enroll) — sprzężenie usługi Azure AD, które jest wykonywane w kontekście rejestracji zbiorczej, odbywa się w kontekście automatycznie utworzonego użytkownika. Użytkownicy logujący się po dołączeniu urządzenia nie są dodawani do grupy administratorów.   

## <a name="manually-elevate-a-user-on-a-device"></a>Ręczne podnoszenie poziomu użytkownika na urządzeniu 

Oprócz korzystania z procesu sprzężenia usługi Azure AD, można również ręcznie podnieść poziom zwykłego użytkownika, aby stać się administratorem lokalnym na jednym określonym urządzeniu. Ten krok wymaga już członkostwa w grupie administratorów lokalnych. 

Począwszy od wersji **systemu Windows 10 1709,** można wykonać to zadanie z **Ustawienia -> Konta -> Inni użytkownicy**. Wybierz **pozycję Dodaj użytkownika służbowego,** wprowadź numer UPN użytkownika w obszarze Konto **użytkownika** i wybierz *pozycję Administrator* w obszarze Typ **konta**  
 
Ponadto można również dodawać użytkowników za pomocą wiersza polecenia:

- Jeśli użytkownicy dzierżawcy są synchronizowani z lokalnej `net localgroup administrators /add "Contoso\username"`usługi Active Directory, użyj programu .
- Jeśli użytkownicy dzierżawy są tworzone w usłudze Azure AD, użyj`net localgroup administrators /add "AzureAD\UserUpn"`

## <a name="considerations"></a>Zagadnienia do rozważenia 

Nie można przypisać grup do roli administratora urządzenia, tylko poszczególni użytkownicy są dozwolone.

Administratorzy urządzeń są przypisywani do wszystkich urządzeń przyłączonych do usługi Azure AD. Nie można ich mieć zakresu do określonego zestawu urządzeń.

Po usunięciu użytkowników z roli administratora urządzenia nadal mają uprawnienia administratora lokalnego na urządzeniu, o ile są do niego zalogowane. Uprawnienie jest odwoływane podczas następnego logowania lub po 4 godzinach po wystawieniu nowego podstawowego tokenu odświeżania.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać omówienie sposobu zarządzania urządzeniami w witrynie Azure Portal, zobacz [zarządzanie urządzeniami przy użyciu witryny Azure Portal](device-management-azure-portal.md)
- Aby dowiedzieć się więcej o dostępie warunkowym opartym na [urządzeniach, zobacz konfigurowanie zasad dostępu warunkowego opartego na urządzeniach usługi Azure Active Directory](../conditional-access/require-managed-devices.md).
