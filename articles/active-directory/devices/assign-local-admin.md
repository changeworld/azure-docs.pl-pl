---
title: Urządzenia przyłączone do sposobu zarządzania do lokalnej grupy administratorów w usłudze Azure AD | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przypisać role usługi Azure do lokalnej grupy administratorów urządzenia Windows.
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
ms.openlocfilehash: 11b71b4656181da328cf630cefa4d25cb4f4efda
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67482111"
---
# <a name="how-to-manage-the-local-administrators-group-on-azure-ad-joined-devices"></a>Urządzenia przyłączone do sposobu zarządzania do lokalnej grupy administratorów w usłudze Azure AD

Aby zarządzać urządzeniami Windows, musisz być członkiem lokalnej grupy administratorów. Jako część procesu dołączania usługi Azure Active Directory (Azure AD) usługi Azure AD zaktualizuje członkostwo tej grupy na urządzeniu. Można dostosować aktualizację członkostwa do zaspokojenia potrzeb biznesowych. Aktualizacja członkostwa jest, na przykład przydatne, jeśli chcesz umożliwić pracownikom działu pomocy technicznej do wykonywania zadań wymagających prawa administratora na urządzeniu.

W tym artykule wyjaśniono, jak działa aktualizacja członkostwa i jak można dostosować go w usłudze Azure AD Join. Zawartość tego artykułu nie ma zastosowania do **hybrydowego** dołączania do usługi Azure AD.

## <a name="how-it-works"></a>Jak to działa

Po podłączeniu urządzenia Windows z usługą Azure AD przy użyciu usługi Azure AD join, usługi Azure AD dodaje następujące zasady zabezpieczeń do lokalnej grupy administratorów na urządzeniu:

- Rola administratora globalnego usługi Azure AD
- Rola administratora usługi Azure AD urządzenia 
- Użytkownik wykonujący dołączania do usługi Azure AD   

Przez Dodawanie ról usługi Azure AD do lokalnej grupy administratorów, należy zaktualizować użytkowników, którymi może zarządzać urządzeniem dowolnym czasie w usłudze Azure AD bez modyfikowania niczego na urządzeniu. Obecnie nie można przypisać grup do roli administratora.
Usługa Azure AD dodaje także roli administratora usługi Azure AD do lokalnej grupy administratorów do obsługi zasadę najmniejszych uprawnień (PoLP). Oprócz administratorów globalnych, można również włączyć użytkowników, które zostały *tylko* z przypisaną rolą administratora urządzenia do zarządzania urządzeniem. 

## <a name="manage-the-global-administrators-role"></a>Zarządzanie rolą administratora globalnego

Aby przeglądać i aktualizować członkostwo w roli administratora globalnego, zobacz:

- [Wyświetlanie wszystkich elementów członkowskich w roli administratora w usłudze Azure Active Directory](../users-groups-roles/directory-manage-roles-portal.md)
- [Przypisz użytkownika do ról administratora w usłudze Azure Active Directory](../fundamentals/active-directory-users-assign-role-azure-portal.md)


## <a name="manage-the-device-administrator-role"></a>Zarządzanie rolą administratora urządzenia 

W witrynie Azure portal można zarządzać rolą administratora urządzenia na **urządzeń** strony. Aby otworzyć **urządzeń** strony:

1. Zaloguj się do Twojej [witryny Azure portal](https://portal.azure.com) jako administrator globalny lub administrator urządzenia.
1. Na lewym pasku nawigacyjnym kliknij **usługi Azure Active Directory**. 
1. W **Zarządzaj** kliknij **urządzeń**.
1. Na **urządzeń** kliknij **ustawienia urządzenia**.

Aby zmodyfikować rolę administrator urządzenia, należy skonfigurować **Dodatkowi administratorzy lokalni w usłudze Azure AD urządzenia przyłączone do**.  

![Dodatkowi administratorzy lokalni](./media/assign-local-admin/10.png)

>[!NOTE]
> Ta opcja wymaga dzierżawy usługi Azure AD Premium. 

Administratorzy urządzenia są przypisane do urządzeń przyłączonych do wszystkich Azure AD. Nie można określić zakres Administratorzy urządzenia do określonego zestawu urządzeń. Aktualizacja roli administratora urządzenia nie musi koniecznie mieć bezpośredni wpływ na użytkowników, których to dotyczy. W przypadku urządzeń użytkownik jest zalogowany w, odbywa się aktualizacja uprawnień:

- Gdy użytkownik rejestruje.
- Po 4 godzinach gdy nowy głównej odświeżanie tokenu jest wystawiony. 

## <a name="manage-regular-users"></a>Zarządzanie użytkownikami regularne

Domyślnie program Azure AD dodaje użytkownika wykonującego sprzężenia Azure AD do grupy administratorów na urządzeniu. Jeśli chcesz zapobiec zwykli użytkownicy lokalni administratorzy, masz następujące opcje:

- [Rozwiązania Windows Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) — rozwiązania Windows Autopilot udostępnia opcję, aby uniemożliwić użytkownika podstawowego, wykonując sprzężenie staje się kontem lokalnego administratora. Można to osiągnąć [tworzenia profilu rozwiązania Autopilot](https://docs.microsoft.com/intune/enrollment-autopilot#create-an-autopilot-deployment-profile).
- [Rejestracja zbiorcza](https://docs.microsoft.com/intune/windows-bulk-enroll) — sprzężenie usługi Azure AD, która jest wykonywana w kontekście rejestracji zbiorczej odbywa się w kontekście użytkownika utworzony automatycznie. Użytkownicy logowania po przyłączone do urządzenia nie są dodawane do grupy Administratorzy.   

## <a name="manually-elevate-a-user-on-a-device"></a>Ręcznie podwyższenia poziomu uprawnień użytkownika na urządzeniu 

Oprócz używania proces dołączania usługi Azure AD, możesz również ręcznie podnieść poziom konta zwykłego użytkownika, aby stać się administratorem lokalnym na jednego konkretnego urządzenia. Ten krok wymaga już być członkiem lokalnej grupy administratorów. 

Począwszy od **systemu Windows 10 1709** wersji, można wykonać tego zadania od **Ustawienia -> konta -> innym użytkownikom**. Wybierz **Dodaj użytkownika firmowego lub szkolnego**, wprowadź nazwę UPN użytkownika, w obszarze **konta użytkownika** i wybierz *administratora* w obszarze **typ konta**  
 
Ponadto można również dodać użytkowników przy użyciu wiersza polecenia:

- Jeśli użytkownicy dzierżawy są synchronizowane z usługi Active Directory w środowisku lokalnym, użyj `net localgroup administrators /add "Contoso\username"`.
- Jeśli użytkownicy dzierżawy są tworzone w usłudze Azure AD, należy użyć `net localgroup administrators /add "AzureAD\UserUpn"`

## <a name="considerations"></a>Zagadnienia do rozważenia 

Nie można przypisać grup do roli administratora urządzenia, tylko pojedynczy użytkownicy są dozwolone.

Administratorzy urządzenia są przypisane do wszystkich Azure AD urządzeń w miejscu pracy. One nie może należeć do zakresu określonej grupy urządzeń.

Jeśli usuniesz użytkowników z roli administratora urządzenia nadal ma on uprawnienia administratora lokalnego na urządzeniu tak długo, jak długo po zalogowaniu się do niego. Uprawnienie jest odwoływane, podczas następnego logowania lub po 4 godzinach, po wygenerowaniu nowego tokena odświeżania podstawowego.

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać omówienie sposobu zarządzania urządzeniami w witrynie Azure Portal, zobacz [zarządzanie urządzeniami przy użyciu witryny Azure Portal](device-management-azure-portal.md)
- Aby dowiedzieć się więcej na temat dostępu warunkowego opartego na urządzeniach, zobacz [skonfigurować zasady dostępu warunkowego opartego na urządzeniach w usłudze Azure Active Directory](../conditional-access/require-managed-devices.md).
