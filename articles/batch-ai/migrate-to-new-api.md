---
title: Migrowanie do interfejs API zaktualizowane Azure Batch AI | Dokumentacja firmy Microsoft
description: Jak zaktualizować kodu usługi Azure Batch AI i skryptów, Użyj obszaru roboczego i eksperymentowania zasobów
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
ms.devlang: multiple
ms.topic: article
ms.date: 06/08/2018
ms.author: danlep
ROBOTS: NOINDEX
ms.openlocfilehash: 75a9a5e9bafd62b320397c00ef6574b7536d9e09
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2018
ms.locfileid: "53407784"
---
# <a name="migrate-to-the-updated-batch-ai-api"></a>Migrowanie do zaktualizowanego interfejsu API sztucznej Inteligencji usługi Batch

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

W API REST usługi Batch AI w wersji 2018-05-01 i powiązanych zestawów SDK sztucznej Inteligencji usługi Batch oraz narzędzia znaczące zmiany i nowe funkcje zostały wprowadzone.

Jeśli używano poprzedniej wersji interfejsu API sztucznej Inteligencji usługi Batch, w tym artykule opisano sposób modyfikowania kodu oraz skrypty do pracy z nowego interfejsu API. 

## <a name="whats-changing"></a>Co ulega zmianie?

W odpowiedzi na opinie klientów możemy usunąć limity liczby zadań i ułatwia zarządzanie zasobami usługi Batch AI. Nowy interfejs API wprowadzono dwa nowe zasoby, *obszaru roboczego* i *eksperymentować*. Zbieranie szkolenie powiązane zadania w obszarze eksperymentu, a organizować wszystkie pokrewne zasoby usługi Batch AI (klastry, serwery plików, eksperymentów, zadania) w obszarze roboczym.  

* **Obszar roboczy** — kolekcja najwyższego poziomu wszystkich typów zasobów usługi Batch AI. Tworzy klastry i serwery plików znajdują się w obszarze roboczym. Obszary robocze należące do różnych grup lub projekty zazwyczaj oddzielne pracy. Na przykład Niewykluczone, że deweloper i obszar roboczy testu. Prawdopodobnie konieczne będzie jedynie ograniczonej liczby obszarów roboczych na subskrypcję. 

* **Eksperyment** — zbierania powiązane zadania, które można tworzyć zapytania i zarządza się razem. Na przykład użyć eksperymentu do grupy wszystkich zadań, które są wykonywane w ramach funkcji hyper-dostosowywania parametrów. 

Na poniższej ilustracji przedstawiono przykład zasobów hierarchii. 

![](./media/migrate-to-new-api/batch-ai-resource-hierarchy.png)

## <a name="monitor-and-manage-existing-resources"></a>Monitorowanie i zarządzanie nimi istniejących zasobów
W każdej grupie zasobów, której już utworzono klastry usługi Batch AI, zadania lub serwerów plików, usługa Batch AI utworzy obszar roboczy o nazwie `migrated-<region>` (na przykład `migrated-eastus`) i eksperymentowania o nazwie `migrated`. Aby uzyskać dostęp do utworzonego wcześniej zadania, klastrów lub serwerów plików, należy użyć zmigrowanego obszaru roboczego i eksperymentowania. 

### <a name="portal"></a>Portal 
Aby uzyskać dostęp do utworzonego wcześniej zadania, klastrów lub serwerów plików przy użyciu portalu, najpierw wybierz `migrated-<region>` obszaru roboczego. Po wybraniu obszaru roboczego, wykonywać operacje takie jak zmiana rozmiaru lub usuwania klastra i wyświetlanie stanu zadania i dane wyjściowe. 

### <a name="sdks"></a>Zestawy SDK 
Aby uzyskać dostęp do zadań, klastrów lub serwerów plików, które wcześniej zostały utworzone za pomocą zestawów SDK usługi Batch AI, podaj nazwę obszaru roboczego i eksperymentować nazwy parametrów. 

Jeśli używasz zestawu SDK języka Python, istotne zmiany są wyświetlane w poniższych przykładach. Zmiany są podobne w innych zestawów SDK sztucznej Inteligencji usługi Batch. 


#### <a name="get-old-cluster"></a>Pobierz stary klaster 

```python
cluster = client.clusters.get(resource_group_name, 'migrated-<region>', cluster_name)
```

#### <a name="delete-old-cluster"></a>Usuń stary klaster 

```python
client.clusters.delete(resource_group_name, 'migrated-<region>', cluster_name)
```

#### <a name="get-old-file-server"></a>Pobierz starego serwera plików 

```python
cluster = client.fileservers.get(resource_group_name, 'migrated-<region>', fileserver_name)
```

#### <a name="delete-old-file-server"></a>Usuń stary serwer plików  

```python
client.fileservers.delete(resource_group_name, 'migrated-<region>', fileserver_name)
``` 


#### <a name="get-old-job"></a>Pobrać stare zadania 

```python
cluster = client.jobs.get(resource_group_name, 'migrated-<region>', 'migrated', job_name)
```

#### <a name="delete-old-job"></a>Usuń stare zadania

```python
client.jobs.delete(resource_group_name, 'migrated-<region>', 'migrated', job_name)
```
 
### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure 
 
Korzystając z wiersza polecenia platformy Azure, aby zadania utworzone wcześniej dostęp, klastrów lub serwerów plików, użyj `-w` i `-e` parametry, aby podać obszar roboczy i eksperymentowania nazwy. 


#### <a name="get-old-cluster"></a>Pobierz stary klaster

```azurecli
az batchai cluster show -g resource-group-name -w migrated-<region> -n cluster-name
```


#### <a name="delete-old-cluster"></a>Usuń stary klaster 

```azurecli
az batchai cluster delete -g resource-group-name -w migrated-<region> -n cluster-name
```

#### <a name="get-old-file-server"></a>Pobierz starego serwera plików

```azurecli
az batchai file-server show -g resource-group-name -w migrated-<region> -n fileserver-name
```


#### <a name="delete-old-file-server"></a>Usuń stary serwer plików 

```azurecli
az batchai file-server delete -g resource-group-name -w migrated-<region> -n fileserver-name
``` 


#### <a name="get-old-job"></a>Pobrać stare zadania

```azurecli
az batchai job show -g resource-group-name -w migrated-<region> -e migrated -n job-name
```


#### <a name="delete-old-job"></a>Usuń stare zadania 

```azurecli
az batchai job delete -g resource-group-name -w migrated-<region> -e migrated -n job-name
``` 

## <a name="create-batch-ai-resources"></a>Tworzenie zasobów usługi Batch AI 
 
Jeśli używasz jednego z istniejących zestawów SDK sztucznej Inteligencji usługi Batch można nadal z niej korzystać, aby utworzyć zasoby usługi Batch AI (zadań, klastrów lub serwerów plików) bez wprowadzania zmian w kodzie. Jednak po uaktualnieniu do nowego zestawu SDK, należy wprowadzić następujące zmiany.
 
### <a name="create-workspace"></a>Tworzenie obszaru roboczego 
Zależnie od scenariusza można utworzyć obszar roboczy ręcznie jednorazowego za pośrednictwem portalu lub interfejsu wiersza polecenia. Jeśli używasz interfejsu wiersza polecenia, należy utworzyć obszar roboczy za pomocą następującego polecenia: 

```azurecli
az batchai workspace create -g resource-group-name -n workspace-name
```

### <a name="create-experiment"></a>Tworzenie eksperymentu 


W zależności od scenariusza możesz utworzyć eksperyment ręcznie jednorazowego za pośrednictwem portalu lub interfejsu wiersza polecenia. Jeśli używasz interfejsu wiersza polecenia, należy utworzyć eksperyment, korzystając z następującego polecenia: 

```azurecli
az batchai experiment create -g resource-group-name -w workspace-name -n experiment-name

```

### <a name="create-clusters-file-servers-and-jobs"></a>Tworzenie klastrów serwerów plików i zadania
 
Korzystając z portalu do utworzenia zadania, klastrów lub serwerów plików, portalu informacje pomocne podczas początkowego tworzenia, podaj nazwę obszaru roboczego i eksperymentowania nazwy parametrów.

Tworzenie zadań, klastrami lub serwerami plików za pomocą zaktualizowanego zestawu SDK, należy podać parametr Nazwa obszaru roboczego. Jeśli używasz zestawu SDK języka Python, istotne zmiany są wyświetlane w poniższych przykładach. Zastąp *workspace_name* i *experiment_name* z obszaru roboczego i eksperyment, który został utworzony wcześniej. Zmiany są podobne w innych zestawów SDK sztucznej Inteligencji usługi Batch. 


#### <a name="create-cluster"></a>Tworzenie klastra 

```python
_ = client.clusters.create(resource_group_name, workspace_name, cluster_name, cluster_create_parameters).result()
```

#### <a name="create-file-server"></a>Tworzenie serwera plików 

```python
_ = client.fileservers.create(resource_group_name, workspace_name, fileserver_name, fileserver_create_parameters).result()
```

#### <a name="create-job"></a>Tworzenie zadania 

```python
_ = client.jobs.create(resource_group_name, workspace_name, experiment_name, job_name job_create_parameters).result()
```


## <a name="next-steps"></a>Kolejne kroki

* Zobacz dokumentacja interfejsu API usługi Batch AI: [Interfejs wiersza polecenia](/cli/azure/batchai), [.NET](/dotnet/api/overview/azure/batchai), [Java](/java/api/overview/azure/batchai), [Node.js](/javascript/api/overview/azure/batchai), [Python](/python/api/overview/azure/batchai), i [REST](/rest/api/batchai)