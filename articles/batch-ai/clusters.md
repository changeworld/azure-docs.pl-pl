---
title: Praca z klastrami usługi Azure Batch AI | Dokumentacja firmy Microsoft
description: Jak wybrać konfiguracji klastra usługi Batch AI oraz tworzenie i Zarządzanie klastrem sztucznej Inteligencji
services: batch-ai
documentationcenter: ''
author: johnwu10
manager: jeconnoc
editor: ''
ms.service: batch-ai
ms.topic: article
ms.date: 08/14/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 61294d8b6b84b03b1e0c8d79b4d2855452c7f0e6
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44057233"
---
# <a name="work-with-batch-ai-clusters"></a>Praca z klastrami usługi Batch AI 

W tym artykule opisano sposób pracy z klastrami w usłudze Azure Batch AI. Wprowadza pojęcia klastrów, typy konfiguracji, które są możliwe i przykłady. Większość przykładów, aby tworzyć i zarządzać nimi w klastrze, w tym artykule Użyj wiersza polecenia platformy Azure. Jednak można użyć innych narzędzi, takich jak witryny Azure portal i zestawów SDK usługi Azure Batch AI do pracy z klastrami.

Klaster usługi Batch AI to jeden z kilku zasobów w usłudze. Zobacz [Omówienie zasobów usługi Batch AI](resource-concepts.md) Aby zrozumieć zakres klastrów w usłudze.

## <a name="introduction-to-clusters"></a>Wprowadzenie do klastrów

W klastrze na platformie usługi Batch AI zawiera zasoby obliczeniowe usługi machine learning uruchomionych zadań szkoleniowych AI. Wszystkie węzły w klastrze mają ten sam rozmiar maszyny Wirtualnej i obrazu systemu operacyjnego. Usługa Batch AI oferują wiele opcji tworzenia klastrów, które są dostosowywane do różnych potrzeb. Zazwyczaj należy skonfigurować innego klastra dla każdej kategorii mocy obliczeniowej, potrzebne do ukończenia projektu. Możesz skalować liczbę węzłów w klastrze w górę i w dół zależności od potrzeb i budżetu. Klastry można ustanowić i współużytkowane przez zespół lub każdego fizycznych można udostępnić własne klastra. 

Każdy klaster istnieje w ramach usługi Batch AI zasób o nazwie *obszaru roboczego*. Przed zainicjowaniem obsługi administracyjnej dowolnego klastra, musisz mieć skonfigurować obszar roboczy usługi Batch AI. Na przykład, jeśli używasz interfejsu wiersza polecenia platformy Azure, użyj [az batchai w obszarze roboczym tworzenie](/cli/azure/batchai/workspace?view=azure-cli-latest#az-batchai-workspace-create) polecenie, aby określić ustawienia obszaru roboczego. 

Po utworzeniu klastra, można przesłać zadania co w czasie do kolejki. Usługa Batch AI obsługuje proces alokacji zasobów do uruchamiania zadań w klastrze. 

## <a name="cluster-configuration-options"></a>Opcje konfiguracji klastra

Podczas planowania klastra należy najpierw określić wymagania dotyczące obliczeń. Usługa Batch AI oferuje elastyczne konfigurowanie opcji klaster można dostosować do swoich potrzeb. Dostępne są następujące opcje majors wziąć pod uwagę podczas aprowizowania klastra:

* **Rozmiar maszyny Wirtualnej** — wybrać dowolny [rozmiar maszyny Wirtualnej](../virtual-machines/linux/sizes.md) jest dostępny w [obsługiwany region](https://azure.microsoft.com/global-infrastructure/services/) dla węzłów klastra. Każdy klaster może zawierać tylko jeden rozmiar maszyny Wirtualnej, więc jeśli podzadania wymagają wielu typów maszyn wirtualnych, trzeba aprowizować klaster osobne dla każdego typu wymagań obliczeniowych. Do szkolenia usługi machine learning lub modele SI opracowanych za pomocą platform, które korzystają z procesorów GPU, zobacz [rozmiarów maszyn wirtualnych GPU zoptymalizowanych pod kątem](../virtual-machines/linux/sizes-gpu.md) na platformie Azure.
  
* **Priorytet maszyny Wirtualnej** — usługa Batch AI zapewnia dedykowanych lub o niskim priorytecie maszyn wirtualnych dla klastra. Maszyny wirtualne dedykowane są zarezerwowane do użycia w klastrze. Opcja o niskim priorytecie przydziela nieużywaną pojemność maszyny Wirtualnej platformy Azure na znaczne oszczędności w zamian za możliwości zadania trwa wyparte Jeśli Azure odzyskuje maszyn wirtualnych. Zadań uruchamianych przez czas dłuższy niż 24 godziny na maszynie Wirtualnej o niskim priorytecie są również automatycznie opóźnieniem. Jeśli budżet jest istotna, rozważ użycie maszyn wirtualnych o niskim priorytecie krótki eksperymentowanie w usłudze zadań. Następnie przejdź do dedykowanych maszyn wirtualnych, gdy nadejdzie czas uruchamiania zadań dłużej.

* **Liczba węzłów** — usługa Batch AI zapewnia opcje ręczne i automatyczne skalowanie liczby węzłów w klastrze. Za pomocą opcji ręcznej użytkownik decyduje skalowanie klastra w górę i w dół, co umożliwia zarządzanie kosztami obliczeń. Opcja automatycznego skalowania gwarantuje, że klaster downscales zawsze, gdy nie używasz go, aby zminimalizować koszty operacji obliczeniowych. 

  Jeśli zdecydujesz się ręczne skalowanie klastra, następnie należy zdefiniować początkowe docelowego podczas tworzenia klastra. Element docelowy jest liczba węzłów, które usługa Batch AI przydziela początkowo. Później możesz ręcznie zmienić rozmiar liczby węzłów.  

  Jeśli wybierzesz opcję skalowania automatycznego, możesz zdefiniować maksymalną i minimalną liczbę węzłów docelowych podczas tworzenia klastra. Element docelowy może wynosić od 0 maksymalnej liczby węzłów, obsługiwane przez Twoje [limit przydziału rdzeni usługi Batch AI](quota-limits.md). Celem 0 pozwala zachować konfigurację klastra bez obciążenia opłatami za wszystkie koszty operacji obliczeniowych. W przypadku żądania elementu docelowego wyższe niż usługi obsługuje limit przydziału, następnie aprowizacji zakończy się niepowodzeniem. 

* **Obraz maszyny Wirtualnej** — usługa Batch AI domyślnie aprowizuje klaster maszyn wirtualnych przy użyciu domyślnego obrazu Ubuntu Server, która obsługuje obciążeń kontenerów. Można wybrać inny wstępnie skonfigurowanego obrazu systemu Linux w witrynie Azure Marketplace, takich jak [maszyny wirtualnej do nauki o danych](../machine-learning/data-science-virtual-machine/overview.md). 

* **Magazyn** — usługa Batch AI zapewnia opcji automatycznego magazynu można wybrać podczas tworzenia klastra przy użyciu wiersza polecenia platformy Azure. Ta opcja automatycznie tworzy kontener udziału i obiektów Blob plików platformy Azure przy użyciu nowego konta magazynu. Te zasoby magazynu są instalowane na każdym z węzłów w klastrze w czasie wykonywania, co pliki były dostępne z ścieżką lokalną. Nazwy kont magazynu, nazwa udziału plików, nazwa kontenera obiektów Blob i ścieżki instalacji, wszystkie mają przypisane wartości domyślne, które również mogą być dostosowywane przy użyciu dodatkowych parametrów podczas tworzenia klastra. 

  Jeśli zdefiniowano żadnych opcji magazynu, musisz oddzielnie tworzenie zasobów magazynu oraz zdefiniuj je, podczas przesyłania zadania. Ta opcja jest przydatna, jeśli klaster jest zarządzana na poziomie organizacji, ale magazynu odbywa się na poziomie użytkownika. Aby uzyskać więcej informacji na temat przekazywania plików do usługi Azure Storage i uzyskiwać do nich dostęp w czasie wykonywania, zobacz [Store usługi Batch AI zadania wejściowe i wyjściowe przy użyciu usługi Azure Storage](use-azure-storage.md).

* **Dostęp użytkownika** — usługi Batch AI umożliwia generowanie publicznych i prywatnych SSH pliki kluczy podczas tworzenia klastra lub podaj klucze SSH, aby można było SSH do poszczególnych węzłów. Można również zdefiniować nazwę użytkownika (domyślnie ustawiona jako bieżący użytkownik) i hasło. Te poświadczenia mogą służyć do dostęp do węzłów w czasie wykonywania, aby można było wyświetlić różne metryki lub uzyskać dodatkowe szczegółowe informacje o do zadań.

* **Zadania konfigurowania** — usługa Batch AI umożliwia definiowanie argumenty wiersza polecenia do wykonania w każdym węźle po alokacji. Można również zdefiniować ścieżkę, w którym plik wyjściowy powinny być rejestrowane w taki sposób, aby. Użyj tej opcji, jeśli masz dodatkowe kroki inicjowania obsługi administracyjnej poza obrazu podstawowego.

* **Dodatkowe czynności konfiguracyjne** — istnieje kilka scenariuszy rzadziej, które mogą wymagać bardziej wyspecjalizowane konfiguracje. W tym przypadku [plik konfiguracji klastra](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#using-cluster-configuration-file) mogą być dołączane za pomocą polecenia wiersza polecenia platformy Azure, aby utworzyć klaster. 

## <a name="create-the-cluster"></a>Tworzenie klastra

Po określeniu opcji konfiguracji klastra, należy użyć wiersza polecenia platformy Azure, witryny Azure portal lub interfejsów API sztucznej Inteligencji usługi Batch do utworzenia klastra. Na przykład, aby utworzyć klaster przy użyciu wiersza polecenia platformy Azure, możesz wykonać [Tworzenie klastrów batchai az](/cli/azure/batchai/cluster?view=azure-cli-latest#az-batchai-cluster-create) dokumentację, aby utworzyć pełne polecenie zapewniająca konfiguracje, które są potrzebne. 

Najbardziej podstawowa konfiguracja, następujące postanowienia polecenia maszyna wirtualna Standard_NC6 klastra z jednym węzłem i dostęp protokołu SSH. Domyślnie ten klaster zawiera dedykowanych maszyn wirtualnych uruchomiona najnowsza domyślny obraz Ubuntu Server.

```azurecli-interactive
az batchai cluster create \
    --name mycluster \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> \
    --vm-size Standard_NC6 \
    --target 1 \
    --generate-ssh-keys
```

Poniższy przykład przepisy maszyna wirtualna Standard_NC6 klastra który skaluje się automatycznie z zakresu od 0 do 4 węzłów i węzłów o niskim priorytecie i konto usługi storage automatycznie. Ta konfiguracja jest dobrą konfiguracją, jeśli potrzebujesz klastra, ekonomiczne i łatwe w zarządzaniu. 

```azurecli-interactive
az batchai cluster create \
    --name mycluster \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> \
    --vm-size Standard_NC6 \
    --vm-priority lowpriority \
    --max 4 \
    --min 0 \
    --use-auto-storage 
```

Poniższy przykład przepisy klastra maszyna wirtualna Standard_NC6, który zawiera obraz maszyny wirtualnej do nauki o danych, magazynu niestandardowego i instalowanie opcji niestandardowych poświadczeń SSH, zadanie instalacji, które instaluje *Rozpakuj* pakietu i klastra plik konfiguracji dla dodatkowej konfiguracji. Ta konfiguracja jest przykładem klastra, który jest bardziej dostosowane do własnych potrzeb.

```azurecli-interactive
az batchai cluster create \
    --name mycluster \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> \
    --vm-size Standard_NC6 \
    --image UbuntuDSVM \ 
    --config-file cluster.json \
    --setup-task 'apt install unzip -y'
    --storage-account-name <STORAGE ACCOUNT NAME> \
    --nfs-name nfsmount \
    --afs-name afsmount \
    --bfs-name bfsmount \
    --user-name adminuser \
    --ssh-key id_rsa.pub \
    --password secretpassword 
```

## <a name="monitor-the-cluster"></a>Monitorowanie klastra

[Listy klastrów batchai az](/cli/azure/batchai/cluster?view=azure-cli-latest#az-batchai-cluster-list), [az batchai klastra show](/cli/azure/batchai/cluster?view=azure-cli-latest#az-batchai-cluster-show), i [listy węzłów klastrów batchai az](/cli/azure/batchai/cluster/node?view=azure-cli-latest#az-batchai-cluster-node-list) poleceń można monitorować różne informacje dotyczące poszczególnych klastrów.

### <a name="list-all-clusters"></a>Lista wszystkich klastrów

Następujące polecenie listy wszystkim klastrów w obszarze roboczym.

```azurecli-interactive
az batchai cluster list \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> 
```

### <a name="show-information-about-a-cluster"></a>Pokaż informacje o klastrze

Następujące polecenie wyświetla pełne informacje dotyczące określonego klastra, w formacie tabeli.

```azurecli-interactive
az batchai cluster show \
    --name <CLUSTER NAME> \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> \
    --output table
```

Klaster zainicjowano obsługę administracyjną przy użyciu opcji magazynu automatycznie, należy pobrać nazwę konta magazynu do użycia podczas przekazywania zadań szkoleniowych i skryptów. Użyj następującego polecenia:

```azurecli-interactive
az batchai cluster show \
    --name <CLUSTER NAME> \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> \
    --query "nodeSetup.mountVolumes.azureFileShares[0].{storageAccountName:accountName}"
```

Dane wyjściowe będą podobne do następujących.

```JSON
{
  "storageAccountName": "baixxxxxxxxx"
}
```

### <a name="list-cluster-nodes"></a>Lista węzłów klastra

Jeśli potrzebujesz nawiązać połączenia z węzłami klastra, następujące polecenie pobiera listę węzłów i informacje o połączeniu.  

```azurecli-interactive
az batchai cluster node list \
    --name <CLUSTER NAME> \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> 
 ```

Dane wyjściowe dla każdego węzła będzie podobny do następującego:

```JSON
[
  {
    "ipAddress": "40.68.xxx.xxx",
    "nodeId": "tvm-xxxxxxxxxx-xxxxxxxx",
    "port": 50000.0
  }
]
```
Te informacje, które służy do Utwórz połączenie SSH z węzłem, używając polecenia podobnego do następującego:.

```bash
ssh myusername@40.68.xxx.xxx -p 50000
``` 

## <a name="submit-jobs-to-the-cluster"></a>Prześlij zadania do klastra

Po zainicjowaniu obsługi administracyjnej klastra, możesz przesłać zadania do uruchomienia w węzłach. Zobacz [az batchai zadania](/cli/azure/batchai/job?view=azure-cli-latest) polecenie, aby uzyskać różne sposoby przygotowania, przesyłania i monitorowania zadań przy użyciu wiersza polecenia platformy Azure. 

## <a name="downscale-cluster-for-later-use"></a>Wartości klastra w celu późniejszego użycia

Gdy to zrobisz, uruchamianie zadań, można skalować w dół klastra. Zalecane jest aby zawsze wartości klastry, gdy nie są one używane w celu zapisania koszty operacji obliczeniowych. Downscaling klastra do 0 węzłów umożliwia zatrzymywanie rozliczeń opłat podczas nie wymagające w przyszłości ponownie udostępnić klastry, gdy trzeba ponownie zmniejszać. Jeśli automatyczne skalowanie został wybrany w procesie tworzenia klastra, klaster powinna automatycznie skalować w dół minimalną docelową. Jeśli skalowanie ręczne została wybrana skalować w dół w klastrze za pomocą następującego polecenia.

```azurecli-interactive
az batchai cluster resize \
    --name <CLUSTER NAME> \
    --resource-group <RESOURCE GROUP> \
    --workspace <WORKSPACE> \
    --target 0
```

## <a name="delete-a-cluster"></a>Usuwanie klastra

Po zakończeniu przy użyciu klastra, użyj [az batchai klastra Usuń](/cli/azure/batchai/cluster?view=azure-cli-latest#az-batchai-cluster-delete) polecenie, aby go usunąć.

```azurecli-interactive
az batchai cluster delete \
    --name <CLUSTER NAME> \
    --resource-group <RESOURCE GROUP> \
    --workspace <WORKSPACE>
```

Usuwanie grupy zasobów, również automatycznie usuwa wszystkie klastry, które zostały udostępnione w ramach tej grupy zasobów.

```azurecli-interactive
az group delete --name <RESOURCE GROUP>
```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej przykładów dotyczących tworzenia klastra usługi Batch AI, zobacz [Portal](quickstart-create-cluster-portal.md) lub [wiersza polecenia platformy Azure](quickstart-create-cluster-cli.md) przewodnika Szybki Start lub [przepisy usługi Batch AI](https://github.com/Azure/BatchAI/tree/master/recipes) w witrynie GitHub.
