---
title: 'Skrypt programu PowerShell: Wyświetlanie listy istniejących udziałów w udziale danych platformy Azure | Microsoft Docs'
description: Ten skrypt programu PowerShell zawiera listę i wyświetla szczegóły udziałów.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 53b44441397be6aaa690c75bf362fd16d5a34019
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70242894"
---
# <a name="use-powershell-to-view-the-details-of-a-sent-data-share"></a>Wyświetlanie szczegółów wysłanego udziału danych przy użyciu programu PowerShell

Ten skrypt programu PowerShell zawiera listę udziałów danych z istniejącego konta i pobiera szczegóły określonego udziału.


## <a name="sample-script"></a>Przykładowy skrypt

```powershell

# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"

#Lists all data shares within an account
Get-AzDataShare -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName

#Gets details of a specific data share
Get-AzDataShare -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -Name $dataShareName

```


## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń: 

| Polecenie | Uwagi |
|---|---|
| [Get-AzDataShare](/powershell/module/az.resources/get-az) | Pobiera i wyświetla listę udziałów w ramach konta. |
|||

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/).

Dodatkowe przykłady skryptów programu PowerShell dotyczące udziałów danych platformy Azure można znaleźć w [przykładach programu PowerShell w udziale danych platformy Azure](../../samples-powershell.md).
