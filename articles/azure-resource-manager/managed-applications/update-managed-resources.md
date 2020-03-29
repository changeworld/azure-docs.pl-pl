---
title: Aktualizowanie zarządzanych zasobów
description: W tym artykule opisano sposób pracy nad zasobami w grupie zasobów zarządzanych dla aplikacji zarządzanej platformy Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/26/2017
ms.author: tomfitz
ms.openlocfilehash: 3dbb88d53a961e277e102d7eec580309951c73b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651217"
---
# <a name="work-with-resources-in-the-managed-resource-group-for-azure-managed-application"></a>Praca z zasobami w zarządzanej grupie zasobów dla aplikacji zarządzanej platformy Azure

W tym artykule opisano sposób aktualizowania zasobów, które są wdrażane jako część aplikacji zarządzanej. Jako wydawca aplikacji zarządzanej masz dostęp do zasobów w grupie zarządzanych zasobów. Aby zaktualizować te zasoby, należy znaleźć zarządzaną grupę zasobów skojarzoną z aplikacją zarządzaną i uzyskać dostęp do zasobu w tej grupie zasobów.

W tym artykule przyjęto założenie, że wdrożono aplikację zarządzaną w [zarządzanej aplikacji sieci Web (IaaS) przy](https://github.com/Azure/azure-managedapp-samples/tree/master/Managed%20Application%20Sample%20Packages/201-managed-web-app) przykładowym projekcie usług zarządzania platformy Azure. Ta aplikacja zarządzana zawiera **Standard_D1_v2** maszynę wirtualną. Jeśli nie wdrożono tej zarządzanej aplikacji, nadal można użyć tego artykułu, aby zapoznać się z instrukcjami aktualizowania zarządzanej grupy zasobów.

Na poniższej ilustracji przedstawiono wdrożoną aplikację zarządzana.

![Wdrożona aplikacja zarządzana](./media/update-managed-resources/deployed.png)

W tym artykule używasz interfejsu wiersza polecenia platformy Azure do:

* Identyfikowanie aplikacji zarządzanej
* Identyfikowanie zarządzanej grupy zasobów
* Identyfikowanie zasobów maszyn wirtualnych w grupie zasobów zarządzanych
* Zmień rozmiar maszyny Wirtualnej (mniejszy rozmiar, jeśli nie jest używany, lub większy, aby obsługiwać większe obciążenie)
* Przypisywanie zasad do zarządzanej grupy zasobów określającej dozwolone lokalizacje

## <a name="get-managed-application-and-managed-resource-group"></a>Pobierz aplikację zarządzana i zarządzana grupa zasobów

Aby uzyskać aplikacje zarządzane w grupie zasobów, należy użyć:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')]"
```

Aby uzyskać identyfikator zarządzanej grupy zasobów, należy użyć:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')].{ managedResourceGroup:managedResourceGroupId }"
```

## <a name="resize-vms-in-managed-resource-group"></a>Ponowne rozmiary maszyn wirtualnych w zarządzanej grupie zasobów

Aby wyświetlić maszyny wirtualne w zarządzanej grupie zasobów, podaj nazwę zarządzanej grupy zasobów.

```azurecli-interactive
az vm list -g DemoApp6zkevchqk7sfq --query "[].{VMName:name,OSType:storageProfile.osDisk.osType,VMSize:hardwareProfile.vmSize}"
```

Aby zaktualizować rozmiar maszyn wirtualnych, należy użyć:

```azurecli-interactive
az vm resize --size Standard_D2_v2 --ids $(az vm list -g DemoApp6zkevchqk7sfq --query "[].id" -o tsv)
```

Po zakończeniu operacji sprawdź, czy aplikacja jest uruchomiona w standardzie D2 v2.

![Zarządzana aplikacja używająca standardu D2 v2](./media/update-managed-resources/upgraded.png)

## <a name="apply-policy-to-managed-resource-group"></a>Stosowanie zasad do zarządzanej grupy zasobów

Pobierz zarządzaną grupę zasobów i przypisz zasadę w tym zakresie. Zasada **e56962a6-4747-49cd-b67b-bf8b01975c4c** to wbudowana zasada określania dozwolonych lokalizacji.

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

Aby wyświetlić dozwolone lokalizacje, użyj:

```azurecli-interactive
az policy assignment show --name locationAssignment --scope $managedGroup --query parameters.listofallowedLocations.value
```

Przypisanie zasad pojawi się w portalu.

![Wyświetlanie przypisania zasad](./media/update-managed-resources/assignment.png)

## <a name="next-steps"></a>Następne kroki

* Zobacz artykuł [Omówienie aplikacji zarządzanych](overview.md) zawierający wprowadzenie do aplikacji zarządzanych.
* W przypadku przykładowych projektów zobacz [Przykładowe projekty dla aplikacji zarządzanych platformy Azure](sample-projects.md).
