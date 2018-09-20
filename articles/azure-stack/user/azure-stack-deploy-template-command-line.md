---
title: Wdrażanie szablonów za pomocą wiersza polecenia w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać interfejsu wiersza polecenia (CLI) i platform do wdrażania szablonów usługi Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 9584177f-4af3-4834-864d-930b09ae0995
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: d40f9e8bf319f327879a71b23f9d9b82da924c2f
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2018
ms.locfileid: "46365438"
---
# <a name="deploy-templates-in-azure-stack-using-the-command-line"></a>Wdrażanie szablonów w usłudze Azure Stack przy użyciu wiersza polecenia

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

Aby wdrożyć szablony usługi Azure Resource Manager w środowisku Azure Stack Development Kit, należy użyć wiersza polecenia. Szablony usługi Azure Resource Manager umożliwia wdrażanie i aprowizację wszystkich zasobów dla aplikacji w jednej, skoordynowanej operacji.

## <a name="before-you-begin"></a>Przed rozpoczęciem

 - [Instalowanie i połączyć](azure-stack-version-profiles-azurecli2.md) do usługi Azure Stack przy użyciu wiersza polecenia platformy Azure.
 - Pobierz pliki *azuredeploy.json* i *azuredeploy.parameters.json* z [tworzenia magazynu konta przykładowy szablon](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-create-storage-account).
 
## <a name="deploy-template"></a>Wdrażanie szablonu

Przejdź do folderu, do którego te pliki zostały pobrane i uruchom następujące polecenie, aby wdrożyć szablon:

```azurecli
az group create "cliRG" "local" –f azuredeploy.json –d "testDeploy" –e azuredeploy.parameters.json
```

To polecenie służy do wdrażania szablonu w grupie zasobów **cliRG** w lokalizacji domyślnej usługi Azure Stack POC.

## <a name="validate-template-deployment"></a>Weryfikuj wdrożenie szablonu

Aby wyświetlić ten zasób grupy i konta magazynu, użyj następujących poleceń:

```azurecli
az group list

az storage account list
```

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat wdrażania szablonów, zobacz:

[Wdrażanie szablonów za pomocą programu PowerShell](azure-stack-deploy-template-powershell.md)

