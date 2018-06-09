---
title: Zarządzanie tożsamościami uprzywilejowanymi zasobów Azure - Włącz Zarządzanie subskrypcją | Dokumentacja firmy Microsoft
description: Dowiedz się, jak globalne Administratorzy mogą zarządzać subskrypcjami w dzierżawie.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: protection
ms.date: 03/27/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 628ee70f7eb59673d4229441e3c4242e1ef8e0d3
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234276"
---
# <a name="enable-subscription-management-in-your-tenant"></a>Włącz Zarządzanie subskrypcją, w Twojej dzierżawie

Jako administrator globalny katalogu może nie ma domyślnego dostępu do wszystkich zasobów subskrypcji w dzierżawie. W tym artykule przedstawiono kroki, aby zapewnić sobie dostęp do wszystkich subskrypcji w dzierżawie. Umożliwia także zalecane podejście do pozostałych zgodne z żadnych opcji zabezpieczeń, które organizacja wymaga po otrzymaniu dostępu.

## <a name="who-can-enable-management-of-subscriptions-in-my-directory"></a>Kto może włączyć zarządzanie subskrypcji w katalogu Moje?

Każdy użytkownik przypisany do roli administratora globalnego, wykonaj poniższe kroki, aby włączyć Zarządzanie subskrypcją. Po włączeniu subskrypcji zarządzania dla siebie innych administratorów globalnych, którzy mogą wymagać zasobów można dodać także dostępu. Brak ustawienie bez katalogu, który umożliwia dostęp do wszystkich członków z roli administrator globalny.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do portalu Azure przy użyciu konta z jest uprawniona lub active należącego do roli administratora globalnego. Jeśli konto jest uprawniona administratora globalnego, musisz aktywować rolę przed przejściem do następnego kroku.

## <a name="access-the-azure-active-directory-admin-center"></a>Dostęp do Centrum administracyjnego usługi Azure Active Directory

Teraz, gdy użytkownik jest zalogowany do portalu Azure jako administrator globalny, można edytować ustawienia, które zapewniają dostęp do subskrypcji platformy Azure. Przejdź do Centrum administracyjnego usługi Azure Active Directory (Azure AD), a następnie wybierz **właściwości**.

![Zrzut ekranu Azure AD Centrum administracyjnego, z wyróżnioną pozycją właściwości](media/azure-pim-resource-rbac/aad_properties.png)

Na liście właściwości w obszarze **Administrator globalny może zarządzać subskrypcjami platformy Azure**, wybierz pozycję **tak**.

![Zrzut ekranu właściwości strony, z ustawioną wartość Yes przełączania](media/azure-pim-resource-rbac/aad_properties_save.png)

Teraz Twoje konto jest automatycznie dodawane do roli administratora dostępu dla każdego zasobu subskrypcji w dzierżawie.

## <a name="browse-to-azure-ad-pim"></a>Przejdź do usługi Azure AD PIM

 W tym miejscu przejdź do Azure AD Privileged Identity zarządzania (PIM). W obszarze **Zarządzaj**, wybierz pozycję **zasobów Azure**.

![Zrzut ekranu PIM, z wyróżnionym zasobów platformy Azure](media/azure-pim-resource-rbac/aadpim_manage_azure_resources.png)

## <a name="manage-and-discover-resources"></a>Zarządzanie i odnajdywania zasobów

Jeśli Twoja organizacja już używa usługi Azure AD PIM do ochrony administratorów w usłudze Azure AD, można wyświetlić listę subskrypcji, podczas ładowania bloku.

![Zrzut ekranu PIM, z listy subskrypcji wyświetlane w bloku](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

> [!NOTE]
> Jeśli nie ma żadnych zasobów, upewnij się, że:
>- Rolę administratora globalnego nie wygasł. 
>- Twoja organizacja ma subskrypcję platformy Azure.

![Zrzut ekranu PIM, z listy zasobów pusty](media/azure-pim-resource-rbac/aadpim_rbac_empty_resource_list.png)

## <a name="configure-assignments"></a>Konfigurowanie przypisania

Wybierz subskrypcję z listy i przypisywania samodzielnie (lub grupy, które są członkami) jako kwalifikujących się właścicielem zasobu. 
[Dowiedz się więcej o przypisywanie ról](pim-resource-roles-assign-roles.md).

Powtórz ten proces dla każdego zasobu przed przejściem do następnego kroku.

## <a name="clean-up-standing-access"></a>Wyczyść stałego dostępu

Teraz, gdy masz kwalifikujących się przydziałów ważne subskrypcje w Twojej organizacji, możesz wyczyścić stałego dostępu przez wyłączenie opcji we właściwościach katalogu.

![Zrzut ekranu właściwości strony, z Przełącz ustawiona na nie](media/azure-pim-resource-rbac/aad_properties_no.png)

## <a name="next-steps"></a>Kolejne kroki

[Odnajdywanie zasobów](pim-resource-roles-discover-resources.md)

[Konfigurowanie ustawień roli](pim-resource-roles-configure-role-settings.md)








