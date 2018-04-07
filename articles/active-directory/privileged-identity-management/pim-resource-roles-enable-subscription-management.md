---
title: Zarządzanie tożsamościami dla zasobów platformy Azure — Zarządzanie subskrypcją Włącz uprzywilejowany | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Administratorzy globalni mogą zarządzać subskrypcjami w dzierżawie.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/27/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 4c6ae3da34fe5157314b8ea422591f7ecbd2a667
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="enable-subscription-management"></a>Włącz Zarządzanie subskrypcją

Jako Administrator globalny katalogu nie może mieć domyślnego dostępu do wszystkich zasobów subskrypcji w dzierżawie. W tym artykule będzie opisano kroki, aby zapewnić sobie dostęp do wszystkich subskrypcji w dzierżawie i zalecane podejście do pozostałych zgodne z żadnych opcji zabezpieczeń, które organizacja wymaga po uzyskaniu dostępu.

## <a name="who-can-enable-management-of-subscriptions-in-my-directory"></a>Kto może włączyć zarządzanie subskrypcji w katalogu Moje?

Każdy użytkownik przypisany do roli administratora globalnego, wykonaj poniższe kroki, aby włączyć Zarządzanie subskrypcją. Po włączeniu subskrypcji zarządzania dla siebie, można dodawać innych administratorów globalnych, które może być konieczne również dostęp do zasobów. Brak ustawienie bez katalogu, który umożliwia dostęp do wszystkich członków z roli administrator globalny.

## <a name="log-on-to-the-azure-portal"></a>Zaloguj się do portalu Azure

Rozpocznij, logując się do portalu Azure przy użyciu konta z jest uprawniona lub active należącego do roli administratora globalnego. Jeśli konto jest uprawniona administratora globalnego, musisz najpierw aktywować rolę przed przejściem do następnego kroku.

## <a name="access-the-azure-ad-admin-center"></a>Dostęp do Centrum administracyjnego usługi Azure AD

Teraz, gdy użytkownik jest zalogowany na portalu Azure jako administrator globalny można edytować ustawienia umożliwiające dostęp do subskrypcji platformy Azure. Przejdź do Centrum administracyjnego usługi Azure AD, Znajdź i wybierz kartę właściwości na lewym pasku nawigacyjnym.

![](media/azure-pim-resource-rbac/aad_properties.png)

Na liście właściwości Przełącz opcję o nazwie "Administrator globalny może zarządzać subskrypcjami platformy Azure", "Yes".

![](media/azure-pim-resource-rbac/aad_properties_save.png)

## <a name="navigate-to-azure-ad-pim"></a>Przejdź do usługi Azure AD PIM

Ta opcja jest włączona Twoje konto jest automatycznie dodawane do roli "Administrator dostępu użytkowników" dla każdego zasobu subskrypcji w dzierżawie. W tym miejscu przejdź do usługi Azure AD PIM i wybierz zasoby platformy Azure w sekcji Zarządzanie nawigacji po lewej stronie.

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resources.png)

## <a name="manage-and-discover-resources"></a>Zarządzanie i odnajdywania zasobów

Jeśli Twoja organizacja jest już chroniony za pomocą usługi Azure AD PIM administratorów w usłudze Azure Active Directory po załadowaniu bloku zostanie wyświetlona lista subskrypcji.

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

> [!NOTE]
> Jeśli nie ma żadnych zasobów, sprawdź:
>- Rolę administratora globalnego nie wygasł. 
>- Twoja organizacja ma subskrypcję platformy Azure

![](media/azure-pim-resource-rbac/aadpim_rbac_empty_resource_list.png)

## <a name="configure-assignments"></a>Konfigurowanie przypisania

Wybierz subskrypcję z listy i przypisz samodzielnie (lub grupy, które są członkami) jako kwalifikujących się właścicielem zasobu. 
[Dowiedz się więcej o przypisywanie ról](pim-resource-roles-assign-roles.md).

Powtórz ten proces dla każdego zasobu przed przejściem do następnego kroku.

## <a name="clean-up-standing-access"></a>Wyczyść stałego dostępu

Teraz, gdy masz kwalifikujących się przydziałów ważne subskrypcje w Twojej organizacji, możesz wyczyścić stałego dostępu przez wyłączenie opcji we właściwościach katalogu:

![](media/azure-pim-resource-rbac/aad_properties_no.png)

## <a name="next-steps"></a>Kolejne kroki

[Odnajdywanie zasobów](pim-resource-roles-discover-resources.md)

[Konfigurowanie ustawień roli](pim-resource-roles-configure-role-settings.md)








