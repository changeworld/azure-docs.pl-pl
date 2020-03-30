---
title: 'Skrypt programu PowerShell: lista zaproszeń do udziału danych platformy Azure wysłanych do konsumenta | Dokumenty firmy Microsoft'
description: Ten skrypt programu PowerShell akceptuje zaproszenia z istniejącego udziału danych.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 157b844afd9d59b6c03c7ae8585bc2182370d3a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70307149"
---
# <a name="use-powershell-to-get-a-data-share-invitation"></a>Aby otrzymać zaproszenie do udostępniania danych za pomocą programu PowerShell

Ten skrypt programu PowerShell pobiera zaproszenia wysyłane do konsumenta.

## <a name="sample-script"></a>Przykładowy skrypt
```powershell
# Set variables with your own values
$invitationId = "<invitationId>"
$location = "<location>"

#List invitations sent to a consumer
Get-AzDataShareInvitation

#Get a specific invitation sent to a consumer
Get-AzDataShareInvitation -location -invitationId 

```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń: 

| Polecenie | Uwagi |
|---|---|
| [Get-AzDataShareInvitation](/powershell/module/az.datashare/get-azdatashareinvitation?view=azps-2.6.0) | Pobierz i wystaw zaproszenia do udostępniania danych wysłanych. |
|||

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/).

Dodatkowe przykłady skryptów programu PowerShell udostępniania danych platformy Azure można znaleźć w [przykładach programu PowerShell udostępniania danych platformy Azure.](../../samples-powershell.md)
