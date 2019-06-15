---
title: Aktualizowanie zasobów na platformie Azure managed applications | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób pracy w zasobach w zarządzanej aplikacji zarządzanej grupy zasobów platformy Azure.
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/26/2017
ms.author: tomfitz
ms.openlocfilehash: 21f4e0aa339eb0c746f9b9b06f8aaada6c4d4b71
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61043458"
---
# <a name="work-with-resources-in-the-managed-resource-group-for-azure-managed-application"></a>Praca z zasobami w zarządzanej aplikacji zarządzanej grupy zasobów platformy Azure

W tym artykule opisano, jak można zaktualizować zasobów, które są wdrażane jako część aplikacji zarządzanej. Jako wydawca aplikacji zarządzanej masz dostęp do zasobów w zarządzanej grupie zasobów. Aby zaktualizować te zasoby, należy znaleźć zarządzanej grupy zasobów skojarzonych z zarządzaną aplikację, a dostęp do zasobów w danej grupie zasobów.

W tym artykule założono, została wdrożona aplikacja zarządzana w [zarządzanych aplikacji sieci Web (IaaS) przy użyciu usługi zarządzania platformą Azure](https://github.com/Azure/azure-managedapp-samples/tree/master/samples/201-managed-web-app) przykładowy projekt. Czy aplikacja zarządzana zawiera **maszyna wirtualna Standard_D1_v2** maszyny wirtualnej. Jeśli nie wdrożono tę aplikację zarządzaną, możesz nadal używać w tym artykule zapoznać się z instrukcjami aktualizowania zarządzanej grupy zasobów.

Na poniższej ilustracji przedstawiono wdrożoną aplikację zarządzaną.

![Wdrożoną aplikację zarządzaną](./media/update-managed-resources/deployed.png)

W tym artykule, można użyć wiersza polecenia platformy Azure, aby:

* Identyfikowanie aplikacji zarządzanej
* Identyfikator grupy zasobów zarządzanych
* Zidentyfikuj zasoby maszyny wirtualnej w grupie zasobów zarządzanych
* Zmień rozmiar maszyny Wirtualnej (albo mniejszy rozmiar, jeśli nie jest wykorzystywana lub większych do obsługi obciążenia więcej)
* Przypisywać zasady do grupy zasobów zarządzanych, który określa dozwolone lokalizacje

## <a name="get-managed-application-and-managed-resource-group"></a>Pobierz aplikację zarządzaną i zarządzanej grupy zasobów

Aby uzyskać aplikacje zarządzane w grupie zasobów, użyj:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')]"
```

Aby uzyskać identyfikator zarządzanej grupy zasobów, należy użyć:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')].{ managedResourceGroup:managedResourceGroupId }"
```

## <a name="resize-vms-in-managed-resource-group"></a>Zmienianie rozmiaru maszyn wirtualnych w grupie zasobów zarządzanych

Aby wyświetlić maszynami wirtualnymi w zarządzanej grupie zasobów, należy podać nazwę zarządzanej grupy zasobów.

```azurecli-interactive
az vm list -g DemoApp6zkevchqk7sfq --query "[].{VMName:name,OSType:storageProfile.osDisk.osType,VMSize:hardwareProfile.vmSize}"
```

Aby zaktualizować rozmiaru maszyn wirtualnych, należy użyć:

```azurecli-interactive
az vm resize --size Standard_D2_v2 --ids $(az vm list -g DemoApp6zkevchqk7sfq --query "[].id" -o tsv)
```

Po zakończeniu operacji, sprawdź, czy aplikacja jest uruchomiona na standardowa D2, wersja 2.

![Zarządzanej aplikacji przy użyciu standardowa D2, wersja 2](./media/update-managed-resources/upgraded.png)

## <a name="apply-policy-to-managed-resource-group"></a>Zastosuj zasady do zarządzanej grupy zasobów

Pobieranie zarządzanej grupy zasobów i przypisywania zasad w tym zakresie. Zasady **e56962a6-4747-49cd-b67b-bf8b01975c4c** są wbudowane zasady do określania dozwolonych lokalizacji.

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

Przypisania zasad jest wyświetlana w portalu.

![Wyświetl przypisania zasad](./media/update-managed-resources/assignment.png)

## <a name="next-steps"></a>Kolejne kroki

* Zobacz artykuł [Omówienie aplikacji zarządzanych](overview.md) zawierający wprowadzenie do aplikacji zarządzanych.
* Dla przykładowych projektów, zobacz [przykładowych projektów dla platformy Azure zarządzane aplikacje](sample-projects.md).
