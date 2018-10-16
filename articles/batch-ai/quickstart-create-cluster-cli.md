---
title: Przewodnik Szybki start platformy Azure — tworzenie klastra usługi Batch AI — interfejs wiersza polecenia platformy Azure | Microsoft Docs
description: Przewodnik Szybki start — tworzenie klastra usługi Batch AI do szkolenia modeli uczenia maszynowego i sztucznej inteligencji — interfejs wiersza polecenia platformy Azure
services: batch-ai
documentationcenter: na
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: quickstart
ms.date: 09/03/2018
ms.author: danlep
ms.openlocfilehash: 0d4ba7edfb22a6710222c854ceb2bf86284d2d77
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44057802"
---
# <a name="quickstart-create-a-cluster-for-batch-ai-training-jobs-using-the-azure-cli"></a>Przewodnik Szybki start: tworzenie klastra do zadań szkoleniowych usługi Batch AI przy użyciu interfejsu wiersza polecenia platformy Azure

W tym przewodniku Szybki start przedstawiono, jak użyć interfejsu wiersza polecenia platformy Azure do utworzenia klastra usługi Batch AI, który można wykorzystać do szkolenia modeli sztucznej inteligencji i uczenia maszynowego. Batch AI to zarządzana usługa, która umożliwia naukowcom zajmującym się danymi oraz badaczom sztucznej inteligencji szkolenie modeli sztucznej inteligencji i uczenia maszynowego na dużą skalę w klastrach z maszynami wirtualnymi platformy Azure.

Początkowo klaster zawiera jeden węzeł procesora GPU. Po ukończeniu tego przewodnika Szybki Start będziesz mieć klaster, który będzie można skalować w górę oraz wykorzystać do szkolenia swoich modeli. Prześlij zadania szkoleniowe do klastra przy użyciu usługi Batch AI, narzędzi [Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md) lub rozszerzenia [Visual Studio Tools for AI](https://github.com/Microsoft/vs-tools-for-ai).

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się na instalację i używanie interfejsu wiersza polecenia lokalnie, ten przewodnik Szybki start wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.38 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). 

W tym przewodniku Szybki start założono, że uruchamiasz polecenia w powłoce Bash w usłudze Cloud Shell lub na komputerze lokalnym.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia `az group create`. Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. 

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus2*. Pamiętaj, aby wybrać lokalizację, w której usługa Batch AI będzie dostępna (na przykład Wschodnie stany USA 2).

```azurecli-interactive 
az group create \
    --name myResourceGroup \
    --location eastus2
```

## <a name="create-a-batch-ai-cluster"></a>Tworzenie klastra usługi Batch AI

Najpierw użyj polecenia `az batchai workspace create`, aby utworzyć *obszar roboczy* usługi Batch AI. Obszar roboczy jest niezbędny do zorganizowania klastrów usługi Batch AI oraz innych zasobów.

```azurecli-interactive
az batchai workspace create \
    --workspace myworkspace \
    --resource-group myResourceGroup 
```

Aby utworzyć klaster usługi Batch AI, użyj polecenia `az batchai cluster create`. W poniższym przykładzie utworzono klaster o następujących właściwościach:

* Zawiera jeden węzeł o rozmiarze maszyny wirtualnej NC6, z jednym procesorem GPU NVIDIA Tesla K80. 
* Uruchamia domyślny obraz serwera Ubuntu przeznaczony do hostowania aplikacji opartych na kontenerach, którego można użyć dla większości obciążeń szkoleniowych. 
* Dodaje konto użytkownika o nazwie *myusername* i generuje klucze SSH, jeśli jeszcze nie znajdują się one w domyślnej lokalizacji kluczy (*~/.ssh*) w środowisku lokalnym. 

```azurecli-interactive
az batchai cluster create \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --vm-size Standard_NC6 \
    --target 1 \
    --user-name myusername \
    --generate-ssh-keys
```

Dane wyjściowe polecenia zawierają właściwości klastra. Utworzenie i uruchomienie węzła może potrwać kilka minut. Aby sprawdzić stan klastra, uruchom polecenie `az batchai cluster show`. 

```azurecli-interactive
az batchai cluster show \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --output table
```

Na wczesnym etapie tworzenia klastra dane wyjściowe będą przypominać dane w przykładzie poniżej, gdzie stanem klastra jest `resizing`:

```bash
Name       Resource Group    Workspace    VM Size       State      Idle    Running    Preparing    Leaving    Unusable
---------  ----------------  -----------  ------------  -------  ------  ---------  -----------  ---------  ----------
mycluster  myResourceGroup   myworkspace  STANDARD_NC6  resizing      0          0            0          0           0

```
Klaster jest gotowy do użycia, gdy jego stanem jest `steady`, a jeden węzeł jest w stanie `Idle`.

## <a name="list-cluster-nodes"></a>Wyświetlenie listy węzłów klastra 

Jeśli musisz połączyć się z węzłami klastra (w tym przypadku jednym węzłem), aby zainstalować aplikacje lub przeprowadzić konserwację, uzyskaj informacje o połączeniu, uruchamiając polecenie `az batchai cluster node list`:


```azurecli-interactive
az batchai cluster node list \
    --cluster mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup 
 ```

Dane wyjściowe języka JSON są podobne do następujących:

```JSON
[
  {
    "ipAddress": "40.68.254.143",
    "nodeId": "tvm-1816144089_1-20180626t233430z",
    "port": 50000.0
  }
]
```
Użyj tych informacji, aby nawiązać połączenie SSH z węzłem. Na przykład podstaw poprawny adres IP Twojego węzła w następującym poleceniu:

```bash
ssh myusername@40.68.254.143 -p 50000
``` 
Zamknij sesję SSH, aby kontynuować.

## <a name="resize-the-cluster"></a>Zmiana rozmiaru klastra

Gdy chcesz użyć klastra do uruchomienia zadania szkoleniowego, możesz potrzebować więcej zasobów obliczeniowych. Na przykład, aby zwiększyć rozmiar do 2 węzłów na potrzeby rozproszonego zadania szkoleniowego, uruchom polecenie [batch ai cluster resize](/cli/azure/batchai/cluster#az-batchai-cluster-resize):

```azurecli-interactive
az batchai cluster resize \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --target 2
```

Zmiana rozmiaru klastra potrwa kilka minut.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz kontynuować naukę z samouczkami i przykładami usługi Batch AI, skorzystaj z obszaru roboczego usługi Batch AI, który został utworzony w tym przewodniku Szybki start. 

Opłaty za korzystanie z klastra usługi Batch AI są naliczane, dopóki działają węzły. Jeśli chcesz zachować konfigurację klastra, gdy nie ma zadań do uruchomienia, zmień rozmiar klastra na 0 węzłów. 

```azurecli-interactive
az batchai cluster resize \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --target 0
```

Później, aby uruchomić swoje zadania, zmień go na 1 lub więcej węzłów. Gdy klaster nie jest już potrzebny, usuń go przy użyciu polecenia `az batchai cluster delete`:

```azurecli-interactive
az batchai cluster delete \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
```

Gdy grupa zasobów usługi Batch AI nie jest już potrzebna, można ją usunąć za pomocą polecenia `az group delete`. 

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start przedstawiono tworzenie klastra usługi Batch AI przy użyciu interfejsu wiersza polecenia platformy Azure. Aby uzyskać informacje na temat używania klastra usługi Batch AI do szkolenia modelu, przejdź do przewodnika Szybki start dotyczącego szkolenia modelu uczenia głębokiego.

> [!div class="nextstepaction"]
> [Szkolenie modelu uczenia głębokiego](./quickstart-tensorflow-training-cli.md)
