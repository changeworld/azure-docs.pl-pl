---
title: Tworzenie środowisk w laboratoriach DevTest Labs za pomocą tożsamości zarządzanych platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak używać tożsamości zarządzanych na platformie Azure do wdrażania środowisk w laboratorium w laboratoriach DevTest.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: spelluru
ms.openlocfilehash: a4ba4206c01e492f2ae980c5806de1e72c7051c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73931155"
---
# <a name="use-azure-managed-identities-to-deploy-environments-in-a-lab"></a>Wdrażanie środowisk w laboratorium za pomocą tożsamości zarządzanych platformy Azure 
Jako właściciel laboratorium możesz użyć tożsamości zarządzanej do wdrażania środowisk w laboratorium. Ta funkcja jest przydatna w scenariuszach, w których środowisko zawiera lub ma odwołania do zasobów platformy Azure, takich jak magazyny kluczy, udostępnione galerie obrazów i sieci, które są zewnętrzne dla grupy zasobów środowiska. Umożliwia tworzenie środowisk piaskownicy, które nie są ograniczone do grupy zasobów tego środowiska.

> [!NOTE]
> Obecnie jedna tożsamość przypisana przez użytkownika jest obsługiwana w każdym laboratorium. 

## <a name="prerequisites"></a>Wymagania wstępne
- [Tworzenie, lista, usuwanie lub przypisywanie roli do tożsamości zarządzanej przypisanej przez użytkownika za pomocą portalu Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). 

## <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal
W tej sekcji, jako właściciel laboratorium, użyj witryny Azure Portal, aby dodać tożsamość zarządzaną przez użytkownika do laboratorium. 

1. Na stronie laboratorium wybierz **pozycję Konfiguracja i zasady**. 
1. Wybierz **pozycję Tożsamość** w sekcji **Ustawienia.**
1. Aby dodać przypisaną tożsamość użytkownika, wybierz pozycję **Dodaj** na pasku narzędzi. 
1. Wybierz **tożsamość** z wstępnie wypełnionej listy rozwijanej.
1. Kliknij przycisk **OK**.

    ![Dodawanie tożsamości zarządzanej przez użytkownika](./media/use-managed-identities-environments/add-user-managed-identity.png)
2. Na liście jest widoczna dodana tożsamość zarządzana przez użytkownika. 

    ![Tożsamość zarządzana przez użytkownika na liście](./media/use-managed-identities-environments/identity-in-list.png)

Po zapisaniu laboratorium użyje tej tożsamości podczas wdrażania wszystkich środowisk laboratoryjnych. Można również uzyskać dostęp do zasobu tożsamości na platformie Azure, wybierając tożsamość z listy. 

Właściciel laboratorium nie trzeba wykonywać żadnych specjalnych podczas wdrażania środowiska, tak długo, jak tożsamość dodana do laboratorium ma uprawnienia do zasobów zewnętrznych, które środowisko musi uzyskać dostęp. 

Aby zmienić tożsamość zarządzaną przez użytkownika przypisaną do laboratorium, najpierw usuń tożsamość dołączoną do laboratorium, a następnie dodaj kolejną do laboratorium. Aby usunąć tożsamość dołączoną do laboratorium, wybierz **... (wielokropek)** i kliknij przycisk **Usuń**. 

![Tożsamość zarządzana przez użytkownika na liście](./media/use-managed-identities-environments/replace-identity.png)  

## <a name="use-api"></a>Korzystanie z interfejsu API

1. Po utworzeniu tożsamości zanotuj identyfikator zasobu tej tożsamości. Powinien wyglądać jak następująca próbka: 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
1. Wykonaj PUT Https metody, `ServiceRunner` aby dodać nowy zasób do laboratorium, podobnie jak w poniższym przykładzie. Zasób przebiegu usługi jest zasobem serwera proxy do zarządzania i kontrolowania zarządzanych tożsamości w DevTest Labs. Nazwa obiektu programu service runner może być dowolną prawidłową nazwą, ale zaleca się użycie nazwy zasobu tożsamości zarządzanej. 
 
    ```json
    PUT https://management.azure.com/subscriptions/{subId}/resourceGroups/{rg}/providers/Microsoft.Devtestlab/labs/{yourlabname}/serviceRunners/{serviceRunnerName}

    {
        "location": "{location}",
        "identity":{
            "type": "userAssigned",
            "userAssignedIdentities":{
                "[userAssignedIdentityResourceId]":{}
            }
        }
        "properties":{
            "identityUsageType":"Environment"
                     }
          
    }
    ```
 
    Oto przykład: 

    ```json
    PUT https://management.azure.com/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/exampleRG/providers/Microsoft.Devtestlab/labs/mylab/serviceRunners/sampleuseridentity

    {
        "location": "eastus",
        "identity":{
            "type": "userAssigned",
            "userAssignedIdentities":{
                "/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/exampleRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/sampleuseridentity":{}
            }
        }
        "properties":{
            "identityUsageType":"Environment"
                     }
    }
    ```
 
Po dodaniu tożsamości przypisanej przez użytkownika do laboratorium usługa Azure DevTest Labs będzie go używać podczas wdrażania środowisk usługi Azure Resource Manager. Jeśli na przykład szablon Menedżera zasobów jest potrzebny do uzyskania dostępu do zewnętrznego obrazu galerii obrazów udostępnionych, upewnij się, że tożsamość dodana do laboratorium ma minimalne wymagane uprawnienia dla zasobu galerii obrazów udostępnionych. 
