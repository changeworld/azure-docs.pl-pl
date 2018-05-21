---
title: Aktualizowanie zasobów na platformie Azure zarządzane aplikacje | Dokumentacja firmy Microsoft
description: Opisuje sposób pracy do zasobów w zarządzanej aplikacji zarządzane przez grupę zasobów platformy Azure.
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/26/2017
ms.author: tomfitz
ms.openlocfilehash: 7c2b38055771dae458e4a3a56c2c98231335ae03
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="work-with-resources-in-the-managed-resource-group-for-azure-managed-application"></a>Praca z zasobami w zarządzanej aplikacji zarządzane przez grupę zasobów platformy Azure

W tym artykule opisano sposób aktualizacji zasobów, które są wdrażane w ramach zarządzanych aplikacji. Jako wydawca zarządzaną aplikację masz dostęp do zasobów w grupie zasobów zarządzanych. Aby zaktualizować tych zasobów, należy odnaleźć grupy zasobów zarządzanych skojarzonych z zarządzaną aplikację, a dostęp do zasobu w tej grupie zasobów.

W tym artykule przyjęto założenie, wdrożono aplikację w [zarządzanych aplikacji sieci Web (IaaS) z usługami Azure zarządzania](https://github.com/Azure/azure-managedapp-samples/tree/master/samples/201-managed-web-app) przykładowy projekt. Czy zarządzanej aplikacji obejmuje **Standard_D1_v2** maszyny wirtualnej. Jeśli w tym zarządzanych aplikacji nie zostały wdrożone, można nadal używać w tym artykule zapoznać się z kroki aktualizowanie grupy zasobów zarządzanych.

Na poniższej ilustracji przedstawiono wdrożonej aplikacji zarządzanych.

![Wdrożonej aplikacji zarządzanych](./media/update-managed-resources/deployed.png)

W tym artykule można użyć interfejsu wiersza polecenia Azure, aby:

* Zidentyfikuj zarządzanej aplikacji
* Identyfikator grupy zasobów zarządzanych
* Zidentyfikuj zasoby maszyny wirtualnej w grupie zasobów zarządzanych
* Zmień rozmiar maszyny Wirtualnej (albo mniejszy rozmiar, jeśli nie zostały użyte lub większych do obsługi obciążenia więcej)
* Przypisać zasady do grupy zasobów zarządzanych, która określa dozwolonych lokalizacji

## <a name="get-managed-application-and-managed-resource-group"></a>Pobierz aplikację i grupy zasobów zarządzanych

Aby uzyskać aplikacje zarządzane w grupie zasobów, należy użyć:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')]"
```

Aby uzyskać identyfikator grupy zasobów zarządzanych, należy użyć:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')].{ managedResourceGroup:managedResourceGroupId }"
```

## <a name="resize-vms-in-managed-resource-group"></a>Zmień rozmiar maszyny wirtualne w grupie zasobów zarządzanych

Aby wyświetlić maszyny wirtualne w grupie zasobów zarządzanych, podaj nazwę grupy zasobów zarządzanych.

```azurecli-interactive
az vm list -g DemoApp6zkevchqk7sfq --query "[].{VMName:name,OSType:storageProfile.osDisk.osType,VMSize:hardwareProfile.vmSize}"
```

Aby zaktualizować rozmiar maszyn wirtualnych, należy użyć:

```azurecli-interactive
az vm resize --size Standard_D2_v2 --ids $(az vm list -g DemoApp6zkevchqk7sfq --query "[].id" -o tsv)
```

Po zakończeniu operacji, sprawdź, czy aplikacja działa na standardowe D2 v2.

![Zarządzanej aplikacji przy użyciu standardowych D2 v2](./media/update-managed-resources/upgraded.png)

## <a name="apply-policy-to-managed-resource-group"></a>Zastosuj zasady grupy zasobów zarządzanych

Pobieranie grupy zasobów zarządzanych i przypisanie zasad w tym zakresie. Zasady **e56962a6-4747-49cd-b67b-bf8b01975c4c** wbudowane zasady dla określanie dozwolonych lokalizacji.

```azurecli-interactive
managedGroup=$(az managedapp show --name <app-name> --resource-group DemoApp --query managedResourceGroupId --output tsv)

az policy assignment create --name locationAssignment --policy e56962a6-4747-49cd-b67b-bf8b01975c4c --scope $managedGroup --params '{
                            "listofallowedLocations": {
                                "value": [
                                    "northeurope",
                                    "westeurope"
                                ]
                            }
                        }'
```

Aby wyświetlić dozwolonych lokalizacji, należy użyć:

```azurecli-interactive
az policy assignment show --name locationAssignment --scope $managedGroup --query parameters.listofallowedLocations.value
```

Przypisania zasad jest wyświetlana w portalu.

![Widok przypisania zasad](./media/update-managed-resources/assignment.png)

## <a name="next-steps"></a>Kolejne kroki

* Zobacz artykuł [Omówienie aplikacji zarządzanych](overview.md) zawierający wprowadzenie do aplikacji zarządzanych.
* Dla przykładowych projektach [przykładowe projekty dla platformy Azure zarządzanych aplikacji](sample-projects.md).
