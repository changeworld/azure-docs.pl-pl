---
title: Store stan w aplikacji usługi Azure Service Fabric siatki przez zainstalowanie woluminu plików platformy Azure na podstawie wewnątrz kontenera | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przechowywać stanu w aplikacji usługi Azure Service Fabric siatki przez zainstalowanie woluminu plików platformy Azure na podstawie wewnątrz kontenera przy użyciu wiersza polecenia platformy Azure.
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/09/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: cb5b421c1bcfe888d65335f3ab7f67bed80eec34
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2018
ms.locfileid: "51614263"
---
# <a name="store-state-in-an-azure-service-fabric-mesh-application-by-mounting-an-azure-files-based-volume-inside-the-container"></a>Stan Store w aplikacji usługi Azure Service Fabric siatki, instalowania usługi Azure Files na podstawie woluminu w kontenerze

W tym artykule przedstawiono sposób przechowywania stanu w usłudze Azure Files przez zainstalowanie woluminu w kontenerze aplikacji usługi Service Fabric siatki. W tym przykładzie aplikacja licznika ma usługi platformy ASP.NET Core ze stroną sieci web, która przedstawia wartość licznika w przeglądarce. 

`counterService` Co jakiś czas odczytuje wartość licznika z pliku, zwiększa ona i zapisu je z powrotem do pliku. Plik jest przechowywany w folderze, który jest zainstalowany na woluminie, wspierane przez udział plików platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Aby zakończyć to zadanie, można użyć usługi Azure Cloud Shell lub lokalnej instalacji interfejsu wiersza polecenia platformy Azure. Aby użyć wiersza polecenia platformy Azure z tym artykułem, upewnij się, że `az --version` zwraca co najmniej `azure-cli (2.0.43)`.  Zainstalować (lub zaktualizować) modułu rozszerzenia usługi Service Fabric siatki wiersza polecenia platformy Azure, wykonując te [instrukcje](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do platformy Azure i wybierz swoją subskrypcję.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-file-share"></a>Tworzenie udziału plików

Tworzenie udziału plików platformy Azure, wykonując te [instrukcje](/azure/storage/files/storage-how-to-create-file-share). Nazwa konta magazynu i klucza konta magazynu i nazwa udziału plików, które są określone jako `<storageAccountName>`, `<storageAccountKey>`, i `<fileShareName>` w poniższych instrukcjach. Te wartości są dostępne w portalu Azure:
* <storageAccountName> -W obszarze **kont magazynu**, jest to nazwa konta magazynu używanego podczas tworzenia udziału plików.
* <storageAccountKey> -Wybierz swoje konto magazynu, w obszarze **kont magazynu** , a następnie wybierz **klucze dostępu** i użyj wartości w obszarze **klucz1**.
* <fileShareName> -Wybierz swoje konto magazynu, w obszarze **kont magazynu** , a następnie wybierz **pliki**. Nazwa do użycia jest nazwa udziału plików, który został utworzony.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów, w której zostanie wdrożona aplikacja. Następujące polecenie tworzy grupę zasobów o nazwie `myResourceGroup` w lokalizacji w wschodnich Stanach Zjednoczonych.

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Tworzenie aplikacji i powiązanych zasobów przy użyciu następującego polecenia i podaj wartości dla `storageAccountName`, `storageAccountKey` i `fileShareName` z wcześniej [Utwórz udział plików](#create-a-file-share) kroku.

`storageAccountKey` Parametr szablonu jest ciągiem bezpiecznym. Go nie będą wyświetlane w stan wdrożenia i `az mesh service show` poleceń. Upewnij się, że jest poprawnie określona w następującym poleceniu.

Następujące polecenie służy do wdrażania aplikacji systemu Linux za pomocą [szablonu counter.azurefilesvolume.linux.json](https://sfmeshsamples.blob.core.windows.net/templates/counter/counter.azurefilesvolume.linux.json). Aby wdrożyć aplikację Windows, należy użyć [szablonu counter.azurefilesvolume.windows.json](https://sfmeshsamples.blob.core.windows.net/templates/counter/counter.azurefilesvolume.windows.json). Należy pamiętać, czy większych obrazów kontenerów może potrwać dłużej wdrożenia.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/counter/counter.azurefilesvolume.linux.json  --parameters "{\"location\": {\"value\": \"eastus\"}, \"fileShareName\": {\"value\": \"<fileShareName>\"}, \"storageAccountName\": {\"value\": \"<storageAccountName>\"}, \"storageAccountKey\": {\"value\": \"<storageAccountKey>\"}}"
```

W ciągu kilku minut polecenie powinny zwracać z `counterApp has been deployed successfully on counterAppNetwork with public ip address <IP Address>`

## <a name="open-the-application"></a>Otwieranie aplikacji

Polecenie wdrożenia zwróci publiczny adres IP punktu końcowego usługi. Po pomyślnym wdrożeniu aplikacji, Uzyskaj publiczny adres IP punktu końcowego usługi, a następnie otwórz go w przeglądarce. Wyświetli stronę sieci web za pomocą wartość licznika, aktualizowane co sekundę.

Nazwa zasobu sieci dla tej aplikacji jest `counterAppNetwork`. Informacje na temat aplikacji, takie jak jej opis, lokalizację, grupę zasobów, itp. można wyświetlić za pomocą następującego polecenia:

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name counterAppNetwork
```

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>Sprawdź, czy aplikacja jest w stanie korzystać z woluminu

Aplikacja tworzy plik o nazwie `counter.txt` w pliku udostępnić wewnątrz `counter/counterService` folderu. Zawartość tego pliku jest wartość licznika są wyświetlane na stronie sieci web.

Plik może zostać pobrana przy użyciu dowolnego narzędzia, które umożliwia przeglądanie udziale usługi Azure Files, takich jak [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

## <a name="delete-the-resources"></a>Usuń zasoby

Często usunąć zasoby, których już używasz na platformie Azure. Aby usunąć zasoby związane z tym przykładzie, należy usunąć grupę zasobów, w którym zostały wdrożone (co spowoduje usunięcie wszystkich skojarzonych z tą grupą zasobów) za pomocą następującego polecenia:

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

## <a name="next-steps"></a>Kolejne kroki

- Wyświetlanie usługi Azure Files woluminu przykładowej aplikacji na [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- Aby dowiedzieć się więcej o modelu zasobów usługi Service Fabric, zobacz [Model zasobów usługi Service Fabric Mesh](service-fabric-mesh-service-fabric-resources.md).
- Aby dowiedzieć się więcej na temat usługi Service Fabric Mesh, przeczytaj [omówienie usługi Service Fabric Mesh](service-fabric-mesh-overview.md).