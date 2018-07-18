---
title: Store stanu przez zainstalowanie usługi Azure Files na podstawie woluminu w kontenerze w aplikacji usługi Service Fabric siatki | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przechowywać stan przez zainstalowanie usługi Azure Files na podstawie woluminu w kontenerze w aplikacji usługi Service Fabric siatki, przy użyciu wiersza polecenia platformy Azure.
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/26/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: 94a4e17e6285893520a2f6482b32a69b1229e2fa
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090636"
---
# <a name="store-state-by-mounting-azure-files-based-volume-in-service-fabric-mesh-application"></a>Stan Store przez zainstalowanie usługi Azure Files na podstawie woluminu w aplikacji usługi Service Fabric siatki

W tym artykule przedstawiono sposób przechowywania stanu w usłudze Azure Files przez zainstalowanie woluminu w kontenerze aplikacji usługi Service Fabric siatki. W tym przykładzie licznik aplikacja ma usługi platformy ASP.NET Core ze stroną sieci web, która przedstawia wartość licznika w przeglądarce. 

`counterService` Perodically odczytuje wartość licznika z plikiem, zwiększa ona i zapisu je z powrotem do pliku. Plik jest przechowywany w folderze, który jest zainstalowany na woluminie, wspierane przez udział plików platformy Azure. 

## <a name="set-up-service-fabric-mesh-cli"></a>Konfigurowanie usługi Service Fabric siatki interfejsu wiersza polecenia 
Aby zakończyć to zadanie, można użyć usługi Azure Cloud Shell lub lokalnej instalacji interfejsu wiersza polecenia platformy Azure. Instalowanie modułu rozszerzenia usługi Service Fabric siatki wiersza polecenia platformy Azure, wykonując te [instrukcje](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure
Logowanie do platformy Azure i ustawić subskrypcję.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-file-share"></a>Tworzenie udziału plików 
Tworzenie udziału plików platformy Azure, wykonując te [instrukcje](/azure/storage/files/storage-how-to-create-file-share). Nazwa konta magazynu i klucza konta magazynu i nazwa udziału plików, które są określone jako `<storageAccountName>`, `<storageAccountKey>`, i `<fileShareName>` w poniższych instrukcjach.

## <a name="create-resource-group"></a>Tworzenie grupy zasobów
Utwórz grupę zasobów, aby wdrożyć aplikację. Można użyć istniejącej grupy zasobów i pominąć ten krok. 

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Tworzenie aplikacji i powiązanych zasobów przy użyciu następującego polecenia i podaj wartości dla `storageAccountName`, `storageAccountKey` i `fileShareName` z poprzedniego kroku.

`storageAccountKey` Parametr szablonu jest `securestring`. Go nie będą wyświetlane w stan wdrożenia i `az mesh service show` poleceń. Upewnij się, że jest poprawnie określona w następującym poleceniu.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.linux.json  --parameters "{\"location\": {\"value\": \"eastus\"}, \"fileShareName\": {\"value\": \"<fileShareName>\"}, \"storageAccountName\": {\"value\": \"<storageAccountName>\"}, \"storageAccountKey\": {\"value\": \"<storageAccountKey>\"}}"
```

Poprzednie polecenie służy do wdrażania aplikacji systemu Linux za pomocą [szablonu mesh_rp.linux.json](https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.linux.json). Jeśli chcesz wdrożyć aplikację Windows, użyj [szablonu mesh_rp.windows.json](https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.windows.json). Obrazy kontenerów Windows są większe niż obrazy kontenerów systemu Linux i może zająć więcej czasu, aby wdrożyć.

W ciągu kilku minut polecenia powinny zostać zwrócone przy użyciu:

`counterApp has been deployed successfully on counterAppNetwork with public ip address <IP Address>` 

## <a name="open-the-application"></a>Otwórz aplikację
Po pomyślnym wdrożeniu aplikacji, Uzyskaj publiczny adres IP punktu końcowego usługi, a następnie otwórz go w przeglądarce. Wyświetla stronę sieci web za pomocą wartość licznika, aktualizowane co sekundę.

Polecenie wdrożenia zwraca publiczny adres IP punktu końcowego usługi. Opcjonalnie można także badać zasobu sieciowego, aby znaleźć publiczny adres IP punktu końcowego usługi. 
 
Nazwa zasobu sieci dla tej aplikacji jest `counterAppNetwork`, pobierania informacji o nim przy użyciu następującego polecenia. 

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name counterAppNetwork
```

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>Sprawdź, czy aplikacja jest w stanie korzystać z woluminu
Aplikacja tworzy plik o nazwie `counter.txt` w pliku udostępnić wewnątrz `counter/counterService` folderu. Zawartość tego pliku jest wartość licznika są wyświetlane na stronie sieci web.

Plik może zostać pobrana przy użyciu dowolnego narzędzia, które umożliwia przeglądanie udział plików usługi Azure Files. [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) jest przykładem takiego narzędzia.

## <a name="delete-the-resources"></a>Usuń zasoby

Aby zaoszczędzić ograniczonych zasobów przydzielonych do programu wersji zapoznawczej, Usuń zasoby często. Aby usunąć zasoby związane z tym przykładzie, Usuń grupę zasobów, w którym zostały wdrożone.

```azurecli-interactive
az group delete --resource-group myResourceGroup 
```

## <a name="next-steps"></a>Kolejne kroki

- Wyświetlanie usługi Azure Files woluminu przykładowej aplikacji na [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- Aby dowiedzieć się więcej o modelu zasobów sieci szkieletowej usług, zobacz [siatki modelu zasobów usługi Service Fabric](service-fabric-mesh-service-fabric-resources.md).
- Aby dowiedzieć się więcej na temat usługi Service Fabric siatki, przeczytaj [Omówienie usługi Service Fabric siatki](service-fabric-mesh-overview.md).
