---
title: Włączanie zarządzania subskrypcjami w dzierżawie — Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak włączyć zarządzanie subskrypcjami w Twojej dzierżawie, korzystając z usługi Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 03/27/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: bcaea64484010672a53050cb806c4256a17b19a8
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2018
ms.locfileid: "48901179"
---
# <a name="enable-subscription-management-in-your-tenant"></a>Włączanie zarządzania subskrypcjami w Twojej dzierżawie

Jako administrator globalny katalogu możesz nie mieć domyślnego dostępu do wszystkich zasobów w Twojej dzierżawie. W tym artykule opisano kroki, aby zapewnić sobie dostęp do wszystkich subskrypcji w ramach dzierżawy. Umożliwia także zalecane podejście do pozostałych zgodne z dowolnej opcji zabezpieczeń, które Twoja organizacja wymaga, aby po otrzymaniu dostępu.

## <a name="who-can-enable-management-of-subscriptions-in-my-directory"></a>Kto może włączyć zarządzanie subskrypcjami w moim katalogu?

Każdy użytkownik przypisany do roli administratora globalnego, należy wykonać poniższe kroki, aby włączyć zarządzanie subskrypcjami. Po włączeniu zarządzania subskrypcjami dla siebie, można dodać innych administratorów globalnych, którzy mogą potrzebować zasobów także dostępu. Nie ma żadnego ustawienia katalogu, który umożliwia uzyskiwanie dostępu do wszystkich elementów członkowskich w roli administratora globalnego.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do witryny Azure portal przy użyciu konta, które jest elementem członkowskim roli administratora globalnego kwalifikujących się lub aktywny. Jeśli konto jest uprawniona administrator globalny, należy aktywować rolę przed przejściem do następnego kroku.

## <a name="access-the-azure-active-directory-admin-center"></a>Dostęp do Centrum administracyjnego usługi Azure Active Directory

Teraz, gdy użytkownik jest zalogowany do witryny Azure portal jako administrator globalny, można edytować ustawienia, które zapewniają dostęp do subskrypcji platformy Azure. Przejdź do Centrum administracyjnego usługi Azure Active Directory (Azure AD), a następnie wybierz pozycję **właściwości**.

![Zrzut ekranu programu Azure AD Centrum administracyjnego, z właściwościami wyróżniony](media/azure-pim-resource-rbac/aad_properties.png)

Na liście właściwości w obszarze **Administrator globalny może zarządzać subskrypcjami platformy Azure**, wybierz opcję **tak**.

![Zrzut ekranu właściwości zawierającej Przełącz wartość tak](media/azure-pim-resource-rbac/aad_properties_save.png)

Twoje konto jest automatycznie dodany do roli administrator dostępu użytkowników dla każdego zasobu subskrypcji w ramach dzierżawy.

## <a name="browse-to-azure-ad-pim"></a>Przejdź do aplikacji Azure AD PIM

 W tym miejscu przejdź do usługi Azure AD Privileged Identity Management (PIM). W obszarze **Zarządzaj**, wybierz opcję **zasobów platformy Azure**.

![Zrzut ekranu z usługi PIM, z wyróżnioną pozycją zasobami platformy Azure](media/azure-pim-resource-rbac/aadpim_manage_azure_resources.png)

## <a name="manage-and-discover-resources"></a>Zarządzanie i odnajdywanie zasobów

Jeśli Twoja organizacja używa już usługi Azure AD PIM do ochrony administratorów w usłudze Azure AD, możesz wyświetlić listę subskrypcji, podczas ładowania bloku.

![Zrzut ekranu z usługi PIM, z listy wyświetlane w bloku subskrypcji](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

> [!NOTE]
> Jeśli nie widzisz żadnych zasobów, upewnij się, że:
>- Twoja rola administratora globalnego nie wygasł. 
>- Twoja organizacja ma subskrypcję platformy Azure.

![Zrzut ekranu z usługi PIM, z listy zasobów pusty](media/azure-pim-resource-rbac/aadpim_rbac_empty_resource_list.png)

## <a name="configure-assignments"></a>Konfigurowanie przypisań

Wybierz subskrypcję z listy i przypisz siebie (lub grupy, które są członkami) jako uprawnionych właściciela zasobu. 
[Przeczytaj więcej na temat przypisywania ról](pim-resource-roles-assign-roles.md).

Powtórz tę procedurę dla każdego zasobu przed przejściem do następnego kroku.

## <a name="clean-up-standing-access"></a>Czyszczenie stałego dostępu

Teraz, gdy kwalifikującymi się przypisaniami ważne subskrypcje w Twojej organizacji, możesz wyczyścić stałego dostępu przez wyłączenie opcji w oknie właściwości katalogu.

![Zrzut ekranu właściwości zawierającej Przełącz ustawiona na nie](media/azure-pim-resource-rbac/aad_properties_no.png)

## <a name="next-steps"></a>Kolejne kroki

- [Odnajdź zasoby platformy Azure do zarządzania w usłudze PIM](pim-resource-roles-discover-resources.md)
- [Konfigurowanie ustawień roli zasobów platformy Azure w usłudze PIM](pim-resource-roles-configure-role-settings.md)
