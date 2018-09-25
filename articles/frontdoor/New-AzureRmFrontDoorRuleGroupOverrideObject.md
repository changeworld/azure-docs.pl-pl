---
title: Usługa Azure drzwiami frontowymi — dokumentacja programu PowerShell | Dokumentacja firmy Microsoft
description: Ta dokumentacja szczegóły obsługiwane dla usługi Azure Service drzwiami frontowymi różnych poleceń cmdlet programu PowerShell
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2018
ms.author: sharadag
Module Name: AzureRM.FrontDoor
online version: https://docs.microsoft.com/powershell/module/azurerm.frontdoor/new-azurermfrontdoorrulegroupoverrideobject
schema: 2.0.0
ms.openlocfilehash: ce786d80c3dc1b66252772cea1916a5333e4a7b5
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47047962"
---
# Nowe AzureRmFrontDoorRuleGroupOverrideObject
Utwórz obiekt RuleGroupOverride do tworzenia zasad zapory aplikacji sieci Web

## SKŁADNIA

```
New-AzureRmFrontDoorRuleGroupOverrideObject -Override <PSRuleGroupOverride> -Action <PSAction>
 [-DefaultProfile <IAzureContextContainer>] [<CommonParameters>]
```

## OPIS
Utwórz obiekt RuleGroupOverride do tworzenia zasad zapory aplikacji sieci Web

## PRZYKŁADY

### Przykład 1
```powershell
PS C:\>  New-AzureRmFrontDoorRuleGroupOverrideObject -Override SqlInjection -Action Block

Action RuleGroupOverride
------ -----------------
 Block      SqlInjection
```

Utwórz obiekt RuleGroupOverride

## PARAMETRY

### -Action
Typ akcji.
Możliwe wartości to: "Zezwalaj", "Zablokuj", "Log"

```yaml
Type: Microsoft.Azure.Commands.FrontDoor.Models.PSAction
Parameter Sets: (All)
Aliases:
Accepted values: Allow, Block, Log

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -DefaultProfile
Poświadczenia, konto, dzierżawcy i subskrypcję używane do komunikacji z platformą Azure.

```yaml
Type: Microsoft.Azure.Commands.Common.Authentication.Abstractions.IAzureContextContainer
Parameter Sets: (All)
Aliases: AzureRmContext, AzureCredential

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -Zastąpienia
W tym artykule opisano overrideruleGroup.
Możliwe wartości to: "SqlInjection", "XSS"

```yaml
Type: Microsoft.Azure.Commands.FrontDoor.Models.PSRuleGroupOverride
Parameter Sets: (All)
Aliases:
Accepted values: SqlInjection, XSS

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### Typowe parametry
To polecenie cmdlet obsługuje typowe parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction i -WarningVariable. Aby uzyskać więcej informacji, zobacz informacje o parametrach wspólnych (http://go.microsoft.com/fwlink/?LinkID=113216).
