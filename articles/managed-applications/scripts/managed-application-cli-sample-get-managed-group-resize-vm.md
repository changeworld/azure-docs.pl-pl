---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — pobieranie zarządzanej grupy zasobów i zmienianie rozmiaru maszyn wirtualnych | Microsoft Docs
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — pobieranie zarządzanej grupy zasobów i zmienianie rozmiaru maszyn wirtualnych
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
ms.openlocfilehash: 9c4032191738703b71319c05bce4f6bbbc61b44b
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2018
ms.locfileid: "29847203"
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-azure-cli"></a>Pobieranie zasobów z zarządzanej grupy zasobów i zmienianie rozmiaru maszyn wirtualnych za pomocą interfejsu wiersza polecenia platformy Azure

Ten skrypt pobiera zasoby z zarządzanej grupy zasobów i zmienia rozmiar maszyn wirtualnych w tej grupie zasobów.


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli[main](../../../cli_scripts/managed-applications/get-application/get-application.sh "Get application")]


## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następujących poleceń do wdrożenia aplikacji zarządzanej. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az managedapp list](https://docs.microsoft.com/cli/azure/managedapp#az_managedapp_list) | Wyświetla listę aplikacji zarządzanych. Należy podać wartości zapytania, aby zawęzić wyniki. |
| [az resource list](https://docs.microsoft.com/cli/azure/resource#az_resource_list) | Wyświetla listę zasobów. Należy podać grupę zasobów i wartości zapytania, aby zawęzić wyniki. |
| [az vm resize](https://docs.microsoft.com/cli/azure/vm#az_vm_resize) | Aktualizuje rozmiar maszyny wirtualnej. |


## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do aplikacji zarządzanych, zobacz [Azure Managed Application overview](../overview.md) (Omówienie aplikacji zarządzanych platformy Azure).
* Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).
