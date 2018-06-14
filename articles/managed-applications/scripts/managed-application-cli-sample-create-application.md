---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — wdrażanie aplikacji zarządzanej | Microsoft Docs
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — wdrażanie aplikacji zarządzanej
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2017
ms.author: tomfitz
ms.openlocfilehash: 3090284a89cfeb1fe8b6446e55e2731e1155167f
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2018
ms.locfileid: "29847220"
---
# <a name="deploy-a-managed-application-for-service-catalog-with-azure-cli"></a>Wdrażanie aplikacji zarządzanej do obsługi katalogu usług za pomocą interfejsu wiersza polecenia platformy Azure

Ten skrypt wdraża definicję aplikacji zarządzanej z katalogu usług. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli[main](../../../cli_scripts/managed-applications/create-application/create-application.sh "Create application")]


## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następującego polecenia do wdrożenia aplikacji zarządzanej. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az managedapp create](https://docs.microsoft.com/cli/azure/managedapp#az_managedapp_create) | Tworzy aplikację zarządzaną. Należy podać identyfikator definicji i parametry szablonu. |


## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do aplikacji zarządzanych, zobacz [Azure Managed Application overview](../overview.md) (Omówienie aplikacji zarządzanych platformy Azure).
* Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).
