---
title: Zmień typ członkostwa grupy statyczne dynamiczny — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Jak utworzyć reguły członkostwa, aby automatycznie wypełnić grupy i odwołanie do reguły.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: dd753ca4994975302a0bc6fede61964f80196d7c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60472075"
---
# <a name="change-static-group-membership-to-dynamic-in-azure-active-directory"></a>Zmiany członkostwa w grupie statycznej na dynamiczną w usłudze Azure Active Directory

Można zmienić członkostwa w grupie ze statycznego na dynamiczny (lub odwrotnie) w usłudze Azure Active Directory (Azure AD). Usługi Azure AD przechowuje taką samą nazwę grupy i identyfikator w systemie, dzięki czemu wszystkie istniejące odwołania do tej grupy są nadal ważne. Zamiast tego możesz utworzyć nową grupę, należy zaktualizować te odwołania. Dynamiczne członkostwo w grupie eliminuje Zarządzanie obciążenie Dodawanie i usuwanie użytkowników. W tym artykule wyjaśniono sposób konwertowania istniejących grup ze statycznej do członkostwa dynamicznego przy użyciu Centrum administracyjnego usługi Azure AD lub poleceń cmdlet programu PowerShell.

> [!WARNING]
> Po zmianie istniejącej statycznych grupy do grupy dynamicznej, wszystkie istniejące elementy członkowskie są usuwane z grupy, a następnie przetwarzania reguły członkostwa można dodawać nowych członków. Jeśli grupa jest używana do kontrolowania dostępu do aplikacji lub zasobów, należy pamiętać, elementy członkowskie oryginalnego utracić dostęp do momentu reguły członkostwa jest w pełni przetwarzany.
>
> Firma Microsoft zaleca test nową regułę członkostwa wcześniej, aby upewnić się, że nowego członkostwa w grupie zgodnie z oczekiwaniami.

## <a name="change-the-membership-type-for-a-group"></a>Zmień typ członkostwa dla grupy

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta administratora globalnego lub administratora użytkowników w dzierżawie.
2. Wybierz **grup**.
3. Z **wszystkich grup** listy, otwórz grupę, którą chcesz zmienić.
4. Wybierz **właściwości**.
5. Na **właściwości** stronie dla grupy, wybierz opcję **Typ członkostwa** przypisane (statyczny), dynamiczne użytkownika lub urządzenie dynamiczne, w zależności od typu żądanego członkostwa. Członkostwo dynamiczne można użyć konstruktora reguły wybierz opcje dla prostej reguły lub samodzielnie zapisujesz reguły członkostwa. 

Poniższe kroki są przykładem Zmiana grupy ze statycznego na dynamiczne zarządzanie członkostwem w grupie użytkowników.

1. Na **właściwości** strony dla wybranej grupy, wybierz opcję **Typ członkostwa** z **użytkownik dynamiczny**, następnie wybierz pozycję Tak, w oknie dialogowym wyjaśniających zmiany do grupy członkostwo, aby kontynuować. 
  
   ![Wybierz typ członkostwa dynamicznego użytkownika](./media/groups-change-type/select-group-to-convert.png)
  
2. Wybierz **Dodaj zapytanie dynamiczne**, a następnie podaj reguły.
  
   ![Wprowadź reguły dla grupy dynamicznej](./media/groups-change-type/enter-rule.png)
  
3. Po utworzeniu reguły, wybierz **Dodaj zapytanie** w dolnej części strony.
4. Wybierz **Zapisz** na **właściwości** stronie dla grupy zapisać zmiany. **Typ członkostwa** grupy natychmiast zaktualizować na liście grup.

> [!TIP]
> Konwersja grup może zakończyć się niepowodzeniem, jeśli reguły członkostwa, który wprowadzono była nieprawidłowa. Zostanie wyświetlone powiadomienie w prawym górnym rogu portalu który zawiera wyjaśnienie, dlaczego nie można zaakceptować zasady przez system. Przeczytaj uważnie, aby zrozumieć, jak można dostosować reguły, aby stał się nieprawidłowy. Przykłady składni reguł i pełną listę obsługiwanych właściwości, operatory i wartości dla reguły członkostwa, zobacz [reguły członkostwa dynamicznego dla grup w usłudze Azure Active Directory](groups-dynamic-membership.md).

## <a name="change-membership-type-for-a-group-powershell"></a>Zmień typ członkostwa w grupie (PowerShell)

> [!NOTE]
> Aby zmienić właściwości grupy dynamicznej, musisz użyć polecenia cmdlet z **wersję zapoznawczą** [usługi Azure AD PowerShell w wersji 2](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0). Możesz zainstalować wersję zapoznawczą z [galerii programu PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).

Oto przykład funkcji, które Przełącz zarządzanie członkostwem w istniejącej grupy. W tym przykładzie jest uważać, aby poprawnie manipulowania właściwość GroupTypes przy zachowaniu dowolnych wartości, które są powiązane z członkostwa dynamicznego.

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
Aby umożliwić grupy statyczne:

```powershell
ConvertDynamicGroupToStatic "a58913b2-eee4-44f9-beb2-e381c375058f"
```

Aby utworzyć grupę dynamiczną:

```powershell
ConvertStaticGroupToDynamic "a58913b2-eee4-44f9-beb2-e381c375058f" "user.displayName -startsWith ""Peter"""
```

## <a name="next-steps"></a>Kolejne kroki

Te artykuły zawierają dodatkowe informacje na temat grup w usłudze Azure Active Directory.

* [Wyświetlanie istniejących grup](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Tworzenie nowej grupy i dodawanie członków](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Zarządzanie ustawieniami grupy](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Zarządzanie członkostwem w grupie](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Zarządzanie regułami dynamicznymi dla użytkowników w grupie](groups-dynamic-membership.md)
