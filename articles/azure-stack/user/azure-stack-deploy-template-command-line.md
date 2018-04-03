---
title: Wdrażanie szablonów przy użyciu wiersza polecenia Azure stosu | Dokumentacja firmy Microsoft
description: Dowiedz się, jak użyć interfejsu wiersza polecenia (CLI) i platform do wdrażania szablonów Azure stosu.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 9584177f-4af3-4834-864d-930b09ae0995
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 761e09889a230642c42697b6bc4f96dc32fe03a0
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="deploy-templates-in-azure-stack-using-the-command-line"></a>Wdrażanie szablonów w stosie Azure przy użyciu wiersza polecenia

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Użyj wiersza polecenia do wdrażania usługi Azure Resource Manager szablonów w zestawie Azure stosu. Szablony usługi Azure Resource Manager wdrażania i obsługi administracyjnej wszystkie zasoby aplikacji w jednej, skoordynowanej operacji.

## <a name="before-you-begin"></a>Przed rozpoczęciem
 - [Zainstaluj i połącz](azure-stack-version-profiles-azurecli2.md) stos Azure z wiersza polecenia platformy Azure
 - Pobierz pliki *azuredeploy.json* i *azuredeploy.parameters.json* z [Utwórz szablon przykład konta magazynu](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-create-storage-account).
 
## <a name="deploy-template"></a>Wdrażanie szablonu
Przejdź do folderu, w którym te pliki zostały pobrane i uruchom następujące polecenie, aby wdrożyć szablon:

    azure group create "cliRG" "local" –f azuredeploy.json –d "testDeploy" –e azuredeploy.parameters.json

To polecenie wdraża szablonu w grupie zasobów **cliRG** w lokalizacji domyślnej POC stosu Azure.

## <a name="validate-template-deployment"></a>Weryfikowanie szablonu wdrożenia
Aby wyświetlić ten zasób grupy i konto magazynu, użyj następujących poleceń:

    azure group list

    azure storage account list




