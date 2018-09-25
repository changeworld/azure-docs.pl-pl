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
online version: https://docs.microsoft.com/powershell/module/azurerm.frontdoor/new-azurermfrontdoorhealthprobesettingobject
schema: 2.0.0
ms.openlocfilehash: 23a5b3a56ecdacbf923db9131a89942bba7de276
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47048053"
---
# Nowe AzureRmFrontDoorHealthProbeSettingObject

Utwórz obiekt PSHealthProbeSetting drzwiami frontowymi tworzenia

## SKŁADNIA

```
New-AzureRmFrontDoorHealthProbeSettingObject -Name <String> [-Path <String>] [-Protocol <PSProtocol>]
 [-IntervalInSeconds <Int32>] [-DefaultProfile <IAzureContextContainer>] [<CommonParameters>]
```

## OPIS
Utwórz obiekt PSHealthProbeSetting drzwiami frontowymi tworzenia

## PRZYKŁADY

### Przykład 1
```powershell
PS C:\>  New-AzureRmFrontDoorHealthProbeSettingObject -Name "healthProbeSetting1"


Path              : /
Protocol          : Http
IntervalInSeconds : 30
ResourceState     :
Id                :
Name              : healthProbeSetting1
Type              :
```

Utwórz obiekt PSHealthProbeSetting drzwiami frontowymi tworzenia

## PARAMETRY

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

### -IntervalInSeconds
Liczba sekund między kondycji sondy.
Wartość domyślna to 30

```yaml
Type: System.Int32
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### — Nazwa
Nazwa ustawienia sondy kondycji.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -Path
Ścieżka do użycia na potrzeby sondy kondycji.
Wartością domyślną jest /

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -Protocol
Schemat protokołu na potrzeby tej sondy wartość domyślna to HTTP

```yaml
Type: Microsoft.Azure.Commands.FrontDoor.Models.PSProtocol
Parameter Sets: (All)
Aliases:
Accepted values: Http, Https

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### Typowe parametry
To polecenie cmdlet obsługuje typowe parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction i -WarningVariable. Aby uzyskać więcej informacji, zobacz informacje o parametrach wspólnych (http://go.microsoft.com/fwlink/?LinkID=113216).
