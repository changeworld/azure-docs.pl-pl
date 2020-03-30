---
title: Zmienianie członkostwa w grupach statycznych na dynamiczne — Usługa Azure AD | Dokumenty firmy Microsoft
description: Jak utworzyć reguły członkostwa, aby automatycznie wypełniać grupy i odwołanie do reguły.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34451fcc4597f77464e5e9566613e21e9fecdbc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74027310"
---
# <a name="change-static-group-membership-to-dynamic-in-azure-active-directory"></a>Zmienianie członkostwa w grupach statycznych na dynamiczne w usłudze Azure Active Directory

Można zmienić członkostwo grupy ze statycznego na dynamiczny (lub odwrotnie) w usłudze Azure Active Directory (Azure AD). Usługa Azure AD zachowuje tę samą nazwę grupy i identyfikator w systemie, więc wszystkie istniejące odwołania do grupy są nadal prawidłowe. Jeśli zamiast tego utworzysz nową grupę, należy zaktualizować te odwołania. Członkostwo w grupach dynamicznych eliminuje obciążenie związane z dodawaniem i usuwaniem użytkowników. W tym artykule opisano sposób konwertowania istniejących grup z członkostwa statycznego na dynamiczne przy użyciu centrum administracyjnego usługi Azure AD lub poleceń cmdlet programu PowerShell.

> [!WARNING]
> Podczas zmiany istniejącej grupy statycznej na grupę dynamiczną wszyscy istniejący członkowie są usuwani z grupy, a następnie reguła członkostwa jest przetwarzana w celu dodania nowych członków. Jeśli grupa jest używana do kontrolowania dostępu do aplikacji lub zasobów, należy pamiętać, że oryginalni członkowie mogą utracić dostęp, dopóki reguła członkostwa nie zostanie w pełni przetworzona.
>
> Zaleca się przetestowanie nowej reguły członkostwa wcześniej, aby upewnić się, że nowe członkostwo w grupie jest zgodne z oczekiwaniami.

## <a name="change-the-membership-type-for-a-group"></a>Zmienianie typu członkostwa w grupie

1. Zaloguj się do [centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy za pomocą konta, które jest administratorem globalnym lub administratorem użytkownika w dzierżawie.
2. Wybierz **opcję Grupy**.
3. Na liście **Wszystkie grupy** otwórz grupę, którą chcesz zmienić.
4. Wybierz **pozycję Właściwości**.
5. Na stronie **Właściwości** grupy wybierz **typ członkostwa** przypisanego (statycznego), dynamicznego użytkownika lub urządzenia dynamicznego, w zależności od żądanego typu członkostwa. W przypadku członkostwa dynamicznego można użyć konstruktora reguł, aby wybrać opcje prostej reguły lub napisać regułę członkostwa samodzielnie. 

Poniższe kroki są przykładem zmiany grupy z członkostwa statycznego na dynamiczne dla grupy użytkowników.

1. Na stronie **Właściwości** dla wybranej grupy wybierz **typ członkostwa** **użytkownika dynamicznego**, a następnie wybierz pozycję Tak w oknie dialogowym wyjaśniającym zmiany w członkostwie grupy, aby kontynuować. 
  
   ![wybieranie typu członkostwa użytkownika dynamicznego](./media/groups-change-type/select-group-to-convert.png)
  
2. Wybierz **pozycję Dodaj kwerendę dynamiczną**, a następnie podaj regułę.
  
   ![wprowadzanie reguły dla grupy dynamicznej](./media/groups-change-type/enter-rule.png)
  
3. Po utworzeniu reguły wybierz pozycję **Dodaj kwerendę** u dołu strony.
4. Wybierz **pozycję Zapisz** na stronie **Właściwości** dla grupy, aby zapisać zmiany. **Typ członkostwa** grupy jest natychmiast aktualizowany na liście grup.

> [!TIP]
> Konwersja grupy może zakończyć się niepowodzeniem, jeśli wprowadzona reguła członkostwa jest nieprawidłowa. Powiadomienie jest wyświetlane w prawym górnym rogu portalu, że zawiera wyjaśnienie, dlaczego reguła nie może być zaakceptowana przez system. Przeczytaj ją uważnie, aby zrozumieć, jak można dostosować regułę, aby była prawidłowa. Aby zapoznać się z przykładami składni reguły i pełną listą obsługiwanych właściwości, operatorów i wartości reguły członkostwa, zobacz [Dynamiczne reguły członkostwa dla grup w usłudze Azure Active Directory](groups-dynamic-membership.md).

## <a name="change-membership-type-for-a-group-powershell"></a>Zmienianie typu członkostwa dla grupy (Program PowerShell)

> [!NOTE]
> Aby zmienić właściwości grupy dynamicznej, należy użyć poleceń cmdlet z **wersji zapoznawczej** [programu Azure AD PowerShell w wersji 2](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0). Podgląd można zainstalować w [galerii programu PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).

Oto przykład funkcji, które przełączają zarządzanie członkostwem w istniejącej grupie. W tym przykładzie należy zadbać o poprawne manipulowanie właściwości GroupTypes i zachowanie wszystkich wartości, które nie są związane z członkostwem dynamicznym.

```powershell
#The moniker for dynamic groups as used in the GroupTypes property of a group object
$dynamicGroupTypeString = "DynamicMembership"

function ConvertDynamicGroupToStatic
{
    Param([string]$groupId)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -eq $null -or !$groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a static group. Aborting conversion.";
    }


    #remove the type for dynamic groups, but keep the other type values
    $groupTypes.Remove($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to remove the dynamic type, ii) pause execution of the current rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "Paused"
}

function ConvertStaticGroupToDynamic
{
    Param([string]$groupId, [string]$dynamicMembershipRule)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -ne $null -and $groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a dynamic group. Aborting conversion.";
    }
    #add the dynamic group type to existing types
    $groupTypes.Add($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to add the dynamic type, ii) start execution of the rule, iii) set the rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "On" -MembershipRule $dynamicMembershipRule
}
```
Aby grupa była statyczna:

```powershell
ConvertDynamicGroupToStatic "a58913b2-eee4-44f9-beb2-e381c375058f"
```

Aby dynamicznie grupy:

```powershell
ConvertStaticGroupToDynamic "a58913b2-eee4-44f9-beb2-e381c375058f" "user.displayName -startsWith ""Peter"""
```

## <a name="next-steps"></a>Następne kroki

Te artykuły zawierają dodatkowe informacje na temat grup w usłudze Azure Active Directory.

* [Wyświetlanie istniejących grup](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Tworzenie nowej grupy i dodawanie członków](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Zarządzanie ustawieniami grupy](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Zarządzanie członkostwem w grupie](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Zarządzanie regułami dynamicznymi dla użytkowników w grupie](groups-dynamic-membership.md)
