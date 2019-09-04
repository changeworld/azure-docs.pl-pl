---
title: 'Skrypt programu PowerShell: Utwórz nowe konto udziału danych platformy Azure | Microsoft Docs'
description: Ten skrypt programu PowerShell tworzy nowe konto udostępniania danych.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: cc4e157856a05290da82aca686e2268a5ed2b3ce
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70243024"
---
# <a name="use-powershell-to-create-a-data-share-account-in-azure"></a>Tworzenie konta udziału danych na platformie Azure przy użyciu programu PowerShell

Ten skrypt programu PowerShell tworzy nowe konto udostępniania danych. 

## <a name="sample-script"></a>Przykładowy skrypt

```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name"
$location = "<Location>"
$dataShareAccountName = "<Data share account name>"

# Create a new Azure Data Share account
New-AzDataShareAccount -ResourceGroupName $resourceGroupName -Name $dataShareAccountName -Location $location
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń: 

| Polecenie | Uwagi |
|---|---|
| [New-AzDataShareAccount](/powershell/module/az.resources/new-azdatashareaccount) | Tworzy konto udostępniania danych. |
|||

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/).

Dodatkowe przykłady skryptów programu PowerShell dotyczące udziałów danych platformy Azure można znaleźć w [przykładach programu PowerShell w udziale danych platformy Azure](../../samples-powershell.md).
