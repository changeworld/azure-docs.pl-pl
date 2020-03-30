---
title: Włączanie tożsamości zarządzanych na maszynach wirtualnych w laboratorium w laboratoriach devtest azure
description: W tym artykule pokazano, jak właściciel laboratorium może włączyć tożsamości zarządzane przypisane przez użytkownika na maszynach wirtualnych laboratorium.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2020
ms.author: spelluru
ms.openlocfilehash: 5d70f83babcf53249f581230e72326d99a0533d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75692669"
---
# <a name="enable-user-assigned-managed-identities-on-lab-virtual-machines-in-azure-devtest-labs"></a>Włączanie tożsamości zarządzanych przypisanych przez użytkownika na laboratoryjnych maszynach wirtualnych w laboratoriach devtest azure
Jako właściciel laboratorium możesz włączyć tożsamości zarządzane przypisane przez użytkownika na maszynach wirtualnych laboratorium w laboratoriach DevTest Labs.

Tożsamości zarządzanej można uwierzytelnić do dowolnej usługi obsługującej uwierzytelnianie usługi Azure Active Directory (AD), w tym usługi Key Vault, bez podawania żadnych poświadczeń w kodzie. Aby uzyskać więcej informacji na temat tożsamości zarządzanych, zobacz [Co to są tożsamości zarządzane dla zasobów platformy Azure?](../active-directory/managed-identities-azure-resources/overview.md).

Dzięki tej funkcji użytkownicy laboratorium mogą udostępniać zasoby platformy Azure, takie jak usługa Azure SQL Database w kontekście laboratorium. Uwierzytelnianie do zasobu jest załatwiona przez samą tożsamość. Po skonfigurowaniu każda istniejąca/nowo utworzona maszyna wirtualna laboratorium zostanie włączona z tą tożsamością. Użytkownicy laboratorium mogą uzyskiwać dostęp do zasobów po zalogowaniu się na swoich komputerach.

> [!NOTE]
> Można dodać wielu użytkowników przypisanych tożsamości zarządzanych, które mają być włączone na maszynach wirtualnych laboratorium.

## <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal
Aby dodać użytkownikowi przypisaną tożsamość zarządzaną dla maszyn wirtualnych w laboratorium, wykonaj następujące kroki:

1. [Tworzenie tożsamości zarządzanej przypisanej przez użytkownika w ramach subskrypcji](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)
1. Przejdź do strony **Konfiguracja i zasady** dla laboratorium.
2. Wybierz **tożsamość (podgląd)** w menu po lewej stronie.
3. Wybierz kartę **Maszyna wirtualna.**
4. Wybierz **pozycję Dodaj,** aby wybrać istniejącą tożsamość z wstępnie wypełnionej listy rozwijanej. 

    ![Przycisk Dodaj tożsamość](./media/enable-managed-identities-lab-vms/add-identity-button.png)
5. Wybierz istniejącą **tożsamość zarządzaną przez użytkownika** z listy rzuconej w dół i wybierz **przycisk OK**. 

    ![Dodawanie tożsamości](./media/enable-managed-identities-lab-vms/add-identity.png)

## <a name="use-api"></a>Korzystanie z interfejsu API

1.  Po utworzeniu tożsamości zanotuj identyfikator zasobu tożsamości. Powinien wyglądać jak następująca próbka: 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
2. Uruchom metodę PUT HTTPS, aby dodać nowy zasób **ServiceRunner** do laboratorium, jak pokazano w poniższym przykładzie. 

    Zasób przebiegu usługi jest zasobem serwera proxy do zarządzania i kontrolowania zarządzanych tożsamości w DevTest Labs. Nazwa obiektu programu service runner może być dowolną prawidłową nazwą, ale zaleca się użycie nazwy zasobu tożsamości zarządzanej.

    ```json
    {
        "identity": {
            "type": "userAssigned",
            "userAssignedIdentities": { 
                "[userAssignedIdentityResourceId]": {}
            }
            },
        "location": "southeastasia",
        "properties": {
            "identityUsageType": "VirtualMachine"
        }
    }
    ```

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o tożsamościach zarządzanych, zobacz [Co to są tożsamości zarządzane dla zasobów platformy Azure?](../active-directory/managed-identities-azure-resources/overview.md)







