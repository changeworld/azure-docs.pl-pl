---
title: Migrowanie do interfejsu API zaktualizowane Azure partii AI | Dokumentacja firmy Microsoft
description: Jak zaktualizować AI usługi partia zadań Azure kod i skrypty, Użyj obszaru roboczego i wypróbować zasobów
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
ms.openlocfilehash: 94db54f35b7871407368b174536dc6454775779c
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35249340"
---
# <a name="migrate-to-the-updated-batch-ai-api"></a>Migrowanie zaktualizowane API partii AI

W nadchodzących interfejsu API REST AI partii wersji 2018-05-01 i powiązane partii AI zestawy SDK i narzędzia są trwa wprowadzono znaczące zmiany i nowe funkcje.

Jeśli poprzednia wersja interfejsu API partii AI była używana, w tym artykule opisano sposób modyfikowania kod i skrypty do pracy z nowym interfejsem API. Należy wprowadzić te zmiany dopiero po nowy interfejs API jest dostępna.

## <a name="whats-changing"></a>Co to jest zmiana?

W odpowiedzi na opinie klientów możemy usunąć ograniczenia dotyczące liczby zadań i ułatwiając zarządzanie zasobami AI partii. Nowy interfejs API wprowadza dwa nowe zasoby, *obszaru roboczego* i *eksperymentu*. Zbieraj zadania pokrewne szkolenia w eksperymencie i organizowanie wszystkie powiązane zasoby AI partii (klastrów serwerów plików, eksperymentów, zadania) w obszarze roboczym.  

* **Obszar roboczy** — kolekcja najwyższego poziomu wszystkich typów zasobów AI partii. Klastry i serwery plików znajdują się w obszarze roboczym. Obszary robocze pracy zwykle oddzielne należących do różnych grup lub projektów. Na przykład może być deweloperów i obszaru roboczego testu. Prawdopodobnie należy ograniczoną liczbę obszarów roboczych na subskrypcję. 

* **Eksperymentu** — Kolekcja powiązanych zadań, które można zbadać i zarządza się razem. Aby pogrupować wszystkie zadania, które są wykonywane jako część odchylenia dostrajanie parametrów funkcji hyper, na przykład użyć eksperymentu. 

Na poniższej ilustracji przedstawiono przykład hierarchii zasobów. 

![](./media/migrate-to-new-api/batch-ai-resource-hierarchy.png)

## <a name="monitor-and-manage-existing-resources"></a>Monitorowanie i zarządzanie istniejącymi zasobami
W każdej grupie zasobów, gdy utworzone klastrów AI partii, zadania lub serwerów plików, usługa partii AI utworzy obszaru roboczego o nazwie `migrated-<region>` (na przykład `migrated-eastus`) i eksperymentu o nazwie `migrated`. Aby uzyskać dostęp do utworzonego wcześniej zadania, klastrów lub serwerów plików, należy użyć zmigrowanego obszaru roboczego i eksperymentu. 

### <a name="portal"></a>Portal 
Aby uzyskać dostęp do utworzonego wcześniej zadania, klastrów lub serwerów plików przy użyciu portalu, należy najpierw zaznaczyć `migrated-<region>` obszaru roboczego. Wybierz obszar roboczy i wykonywanie operacji takich jak zmienianie rozmiaru lub usuwania klastra i wyświetlanie stanu zadania i dane wyjściowe. 

### <a name="sdks"></a>Zestawy SDK 
Aby uzyskać dostęp do zadania, klastrów lub serwerów plików, które wcześniej utworzony za pomocą zestawów SDK AI partii, podaj nazwę obszaru roboczego i eksperymentu nazwy parametrów. 

Jeśli używasz zestawu SDK Python, odpowiednich zmian zostaną wyświetlone w poniższych przykładach. Zmiany są podobne w innych zestawów SDK AI partii. 


#### <a name="get-old-cluster"></a>Pobierz stary klaster 

```python
cluster = client.clusters.get(resource_group_name, 'migrated-<region>', cluster_name)
```

#### <a name="delete-old-cluster"></a>Usuwanie starego klastra 

```python
client.clusters.delete(resource_group_name, 'migrated-<region>', cluster_name)
```

#### <a name="get-old-file-server"></a>Pobierz starego serwera plików 

```python
cluster = client.fileservers.get(resource_group_name, 'migrated-<region>', fileserver_name)
```

#### <a name="delete-old-file-server"></a>Usuwanie starego serwera plików  

```python
client.fileservers.delete(resource_group_name, 'migrated-<region>', fileserver_name)
``` 


#### <a name="get-old-job"></a>Pobierz zadanie starego 

```python
cluster = client.jobs.get(resource_group_name, 'migrated-<region>', 'migrated', job_name)
```

#### <a name="delete-old-job"></a>Usuń stare zadanie

```python
client.jobs.delete(resource_group_name, 'migrated-<region>', 'migrated', job_name)
```
 
### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure 
 
Korzystając z wiersza polecenia platformy Azure do zadań programu access utworzoną wcześniej, klastrów lub serwerów plików, użyj `-w` i `-e` parametry, aby podać obszaru roboczego i wypróbować nazwy. 


#### <a name="get-old-cluster"></a>Pobierz stary klaster

```azurecli
az batchai cluster show -g resource-group-name -w migrated-<region> -n cluster-name
```


#### <a name="delete-old-cluster"></a>Usuwanie starego klastra 

```azurecli
az batchai cluster delete -g resource-group-name -w migrated-<region> -n cluster-name
```

#### <a name="get-old-file-server"></a>Pobierz starego serwera plików

```azurecli
az batchai fileserver show -g resource-group-name -w migrated-<region> -n fileserver-name
```


#### <a name="delete-old-file-server"></a>Usuwanie starego serwera plików 

```azurecli
az batchai fileserver delete -g resource-group-name -w migrated-<region> -n fileserver-name
``` 


#### <a name="get-old-job"></a>Pobierz zadanie starego

```azurecli
az batchai fileserver show -g resource-group-name -w migrated-<region> -e migrated -n job-name
```


#### <a name="delete-old-job"></a>Usuń stare zadanie 

```azurecli
az batchai fileserver delete -g resource-group-name -w migrated-<region> -e migrated -n job-name
``` 

## <a name="create-batch-ai-resources"></a>Utwórz zasoby partii AI 
 
Jeśli używasz jednego z istniejących zestawów SDK AI partii, możesz go użyć do utworzenia AI partii zasobów (zadań, klastrów lub serwerów plików) bez wprowadzania zmian w kodzie. Jednak po uaktualnieniu do nowej wersji zestawu SDK, należy wprowadzić następujące zmiany.
 
### <a name="create-workspace"></a>Utwórz obszar roboczy 
W zależności od scenariusza można utworzyć obszaru roboczego ręcznie jednorazowego za pośrednictwem portalu lub interfejsu wiersza polecenia. Jeśli używasz interfejsu wiersza polecenia, należy utworzyć obszaru roboczego przy użyciu następującego polecenia: 

```azurecli
az batchai workspace create -g resource-group-name -n workspace-name
```

### <a name="create-experiment"></a>Tworzenie eksperymentu 


W zależności od scenariusza można utworzyć eksperyment ręcznie jednorazowego za pośrednictwem portalu lub interfejsu wiersza polecenia. Jeśli używasz interfejsu wiersza polecenia utworzyć eksperyment za pomocą następującego polecenia: 

```azurecli
az batchai experiment create -g resource-group-name -w workspace-name -n experiment-name

```

### <a name="create-clusters-file-servers-and-jobs"></a>Tworzenie klastrów serwerów plików i zadań
 
Jeśli używasz portalu do tworzenia zadań, klastrów lub serwerów plików, portalu zawiera informacje pomocne podczas początkowego tworzenia podać nazwę obszaru roboczego i wypróbować nazwy parametrów.

Tworzenie zadań, klastrów lub serwerów plików za pomocą zaktualizowanego zestawu SDK, podaj parametr Nazwa obszaru roboczego. Jeśli używasz zestawu SDK Python, odpowiednich zmian zostaną wyświetlone w poniższych przykładach. Zastąp *workspace_name* i *experiment_name* z obszaru roboczego i doświadczenia, utworzony wcześniej. Zmiany są podobne w innych zestawów SDK AI partii. 


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

* Zobacz odwołanie do API AI partii: [CLI](/cli/azure/batchai), [.NET](/dotnet/api/overview/azure/batchai), [Java](/java/api/overview/azure/batchai), [Node.js](/javascript/api/overview/azure/batchai), [Python](/python/api/overview/azure/batchai)i [REST](/rest/api/batchai)