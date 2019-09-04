---
title: 'Skrypt programu PowerShell: Monitorowanie użycia udziału danych platformy Azure | Microsoft Docs'
description: Ten skrypt programu PowerShell pobiera metryki użycia wysłanego udziału danych.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 9cc7cbe8a1f523a50c91125802804e564b48a164
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70242959"
---
# <a name="use-powershell-to-monitor-the-usage-of-a-sent-data-share"></a>Monitorowanie użycia wysłanego udziału danych przy użyciu programu PowerShell

Ten skrypt programu PowerShell monitoruje użycie danych przez wystawienie synchronizacji wysłanego udziału danych i uzyskanie szczegółowych informacji o określonej synchronizacji.

## <a name="sample-script"></a>Przykładowy skrypt


```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$synchronizationId = "<Azure synchronization id>"

# List synchronizations on a share
Get-AzDataShareSynchronization -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName

#Get details of a specific synchronization
Get-AzDataShareSynchronizationDetails -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName -SynchronizationId $synchronizationId
```


## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń: 

| Polecenie | Uwagi |
|---|---|
| [Get-AzDataShareSynchronization](/powershell/module/az.resources/get-azdatasharesynchronizationdetails) | Wyświetl listę synchronizacji w udziale. |
| [Get-AzDataShareSynchronizationDetails](/powershell/module/az.resources/get-azdatasharesynchronizationdetails) | Pobiera szczegóły synchronizacji synchronizacji udziału. |
|||

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/).

Dodatkowe przykłady skryptów programu PowerShell dotyczące udziałów danych platformy Azure można znaleźć w [przykładach programu PowerShell w udziale danych platformy Azure](../../samples-powershell.md).
