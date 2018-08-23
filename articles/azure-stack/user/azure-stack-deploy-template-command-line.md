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
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 7814552256f17c5265bbeb4ce8c069dd8dca1bb2
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/18/2018
ms.locfileid: "42058227"
---
# <a name="deploy-templates-in-azure-stack-using-the-command-line"></a>Wdrażanie szablonów w usłudze Azure Stack przy użyciu wiersza polecenia

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

Aby wdrożyć szablony usługi Azure Resource Manager do usługi Azure Stack Development Kit, należy użyć wiersza polecenia. Szablony usługi Azure Resource Manager umożliwia wdrażanie i aprowizację wszystkich zasobów dla aplikacji w jednej, skoordynowanej operacji.

## <a name="before-you-begin"></a>Przed rozpoczęciem
 - [Instalowanie i połączyć](azure-stack-version-profiles-azurecli2.md) do usługi Azure Stack przy użyciu wiersza polecenia platformy Azure
 - Pobierz pliki *azuredeploy.json* i *azuredeploy.parameters.json* z [tworzenia magazynu konta przykładowy szablon](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-create-storage-account).
 
## <a name="deploy-template"></a>Wdrażanie szablonu
Przejdź do folderu, w którym te pliki zostały pobrane i uruchom następujące polecenie, aby wdrożyć szablon:

    azure group create "cliRG" "local" –f azuredeploy.json –d "testDeploy" –e azuredeploy.parameters.json

To polecenie służy do wdrażania szablonu w grupie zasobów **cliRG** w lokalizacji domyślnej usługi Azure Stack POC.

## <a name="validate-template-deployment"></a>Weryfikuj wdrożenie szablonu
Aby wyświetlić ten zasób grupy i konta magazynu, użyj następujących poleceń:

    azure group list

    azure storage account list




