---
title: 'Skrypt programu PowerShell: lista istniejących udziałów w udziale danych platformy Azure | Dokumenty firmy Microsoft'
description: Ten skrypt programu PowerShell zawiera listę i wyświetla szczegóły udziałów.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 6314bd348c22c901001b88eda6875181a2f69df4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70307136"
---
# <a name="use-powershell-to-view-the-details-of-a-sent-data-share"></a>Wyświetlanie szczegółów wysłanego udziału danych za pomocą programu PowerShell

Ten skrypt programu PowerShell zawiera listę udziałów danych z istniejącego konta i pobiera szczegółowe informacje o określonym udziale.


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
| [Udostępnianie Get-AzDataShare](/powershell/module/az.datashare/get-azdatashare?view=azps-2.6.0) | Pobiera i wyświetla listy udziałów na koncie. |
|||

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/).

Dodatkowe przykłady skryptów programu PowerShell udostępniania danych platformy Azure można znaleźć w [przykładach programu PowerShell udostępniania danych platformy Azure.](../../samples-powershell.md)
