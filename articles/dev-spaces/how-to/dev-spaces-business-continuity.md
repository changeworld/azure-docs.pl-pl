---
title: Ciągłość działania i odzyskiwanie po awarii
services: azure-dev-spaces
author: lisaguthrie
ms.author: lcozzens
ms.date: 01/28/2019
ms.topic: conceptual
description: Dowiedz się, jak korzystać z usług Azure Dev Spaces i Azure Kubernetes Services, aby zapewnić ciągłość działania i przygotować się do odzyskiwania po awarii
keywords: 'Docker, Kubernetes, Azure, AKS, Usługa Azure Kubernetes, kontenery, Helm, siatka usług, routing siatki usług, kubectl, k8s '
manager: gwallace
ms.openlocfilehash: 37c0048bfa7e72b25eb56603fc027045eba25cea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78295831"
---
# <a name="business-continuity-and-disaster-recovery-in-azure-dev-spaces"></a>Ciągłość działania i odzyskiwanie po awarii w usługa Azure Dev Spaces

## <a name="review-disaster-recovery-guidance-for-azure-kubernetes-service-aks"></a>Przejrzyj wskazówki dotyczące odzyskiwania po awarii dla usługi Azure Kubernetes Service (AKS)

Usługa Azure Dev Spaces jest funkcją usługi Azure Kubernetes Service (AKS). Należy zapoznać się z wytycznymi dotyczącymi odzyskiwania po awarii w u administratora aks i rozważyć, czy mają one zastosowanie do klastrów AKS, które są używane dla przestrzeni deweloperskich. Aby uzyskać więcej informacji, zapoznaj się z [najlepszymi rozwiązaniami w zakresie ciągłości działania i odzyskiwania po awarii w usłudze Azure Kubernetes (AKS)](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region)

## <a name="enable-dev-spaces-on-aks-clusters-in-different-regions"></a>Włączanie obszarów deweloperskich w klastrach usługi AKS w różnych regionach

Włączenie obszarów deweloperskich w klastrach AKS w różnych regionach umożliwia wznowienie korzystania z obszarów deweloperskich natychmiast po awarii regionu platformy Azure.

Aby uzyskać ogólne informacje na temat wdrożeń wieloregionalnych usługi AKS, zobacz [Planowanie wdrażania w wielu regionach](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region#plan-for-multiregion-deployment)

### <a name="enable-dev-spaces-via-the-azure-portal"></a>Włączanie przestrzeni deweloperskich za pośrednictwem witryny Azure portal

Wybierz element menu **Miejsca deweloperów** w ustawieniach każdego klastra w witrynie Azure portal. Następnie wybierz opcję, aby włączyć miejsca dewelopera i zapisać.

![Włączanie przestrzeni deweloperskich za pośrednictwem witryny Azure portal](../media/common/enable-dev-spaces.jpg)

Powtórz ten proces dla każdego klastra.

### <a name="enable-dev-spaces-via-the-azure-cli"></a>Włączanie pomieszczeń deweloperskich za pośrednictwem interfejsu wiersza polecenia platformy Azure

W wierszu polecenia można również włączyć funkcję Dev Spaces:

```azurecli
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

## <a name="deploy-your-teams-baseline-to-each-cluster"></a>Wdrażanie planu bazowego zespołu w każdym klastrze

Podczas pracy z dev spaces zazwyczaj wdrożyć całą aplikację do nadrzędnego miejsca dewelopera w klastrze Kubernetes. Domyślnie `default` miejsce jest używane. Początkowe wdrożenie obejmuje wszystkie usługi, a także zasoby zewnętrzne, od których zależą te usługi, takie jak bazy danych lub kolejki. Jest to znane jako *punkt odniesienia*. Po skonfigurowaniu linii bazowej w nadrzędnym miejscu deweloperskim można iterować i debugować poszczególne usługi wewnątrz przestrzeni deweloperskich podrzędnych.

Należy wdrożyć najnowsze wersje zestawu usług bazowych do klastrów w wielu regionach. Aktualizowanie usług bazowych w ten sposób gwarantuje, że można nadal używać dev spaces, jeśli występuje błąd regionu platformy Azure. Na przykład jeśli wdrożysz linię bazową za pośrednictwem potoku ciągłej integracji/ciągłego wdrażania, zmodyfikuj potok tak, aby był wdrażany w wielu klastrach w różnych regionach.

## <a name="select-the-correct-aks-cluster-to-use-for-dev-spaces"></a>Wybierz odpowiedni klaster AKS do użycia w przestrzeniach deweloperskich

Po prawidłowym skonfigurowaniu klastra kopii zapasowych z uruchomieniem linii bazowej zespołu można szybko przełączyć się do klastra kopii zapasowych w dowolnym momencie. Następnie można ponownie uruchomić poszczególne usługi, nad którymi pracujesz w przestrzeniach deweloperskich podrzędnych.

Wybierz inny klaster z następującym poleceniem interfejsu wiersza polecenia:

```azurecli
az aks use-dev-spaces -g <new resource group name> -n <new cluster name>
```

Dostępne przestrzenie deweloperów w nowym klastrze można wyświetlić za pomocą następującego polecenia:

```cmd
azds space list
```

Można utworzyć nową przestrzeń deweloperów do pracy lub wybrać istniejącą przestrzeń dewelopera, za pomocą następującego polecenia:

```cmd
azds space select -n <space name>
```

Po uruchomieniu tych poleceń wybrany klaster i dev spacja będzie używana do kolejnych operacji interfejsu wiersza polecenia i do debugowania projektów przy użyciu rozszerzenia kodu programu Visual Studio dla usługi Azure Dev Spaces.

Jeśli używasz programu Visual Studio, można przełączyć klastra używanego przez istniejący projekt, wykonując następujące kroki:

1. Otwórz projekt w programie Visual Studio.
1. Kliknij prawym przyciskiem myszy nazwę projektu w Eksploratorze **rozwiązań** i kliknij polecenie Właściwości
1. W lewym okienku kliknij przycisk **Debugowanie**
1. Na stronie Właściwości debugowania kliknij listę rozwijaną **Profil** i wybierz pozycję **Azure Dev Spaces**.
1. Kliknij przycisk **Zmień.**
1. W wyświetlonym oknie dialogowym wybierz klaster AKS, którego chcesz użyć. W razie potrzeby wybierz inną przestrzeń deweloperów do pracy lub utwórz nową przestrzeń deweloperów, wybierając odpowiednią opcję z listy rozwijanej **Spacja.**

Po wybraniu odpowiedniego klastra i miejsca można nacisnąć klawisz F5, aby uruchomić usługę w przestrzeniach deweloperskich.

Powtórz te kroki dla innych projektów skonfigurowanych do używania oryginalnego klastra.

## <a name="access-a-service-on-a-backup-cluster"></a>Uzyskiwanie dostępu do usługi w klastrze kopii zapasowych

Jeśli usługa została skonfigurowana do używania publicznej nazwy DNS, usługa będzie miała inny adres URL, jeśli uruchomisz ją w klastrze kopii zapasowych. Publiczne nazwy DNS są `<space name>.s.<root space name>.<service name>.<cluster GUID>.<region>.azds.io`zawsze w formacie . Jeśli przełączysz się do innego klastra, identyfikator GUID klastra i ewentualnie region ulegnie zmianie.

Miejsca deweloperów zawsze zawiera poprawny `azds up`adres URL usługi podczas uruchamiania lub w oknie Dane wyjściowe w programie Visual Studio w obszarze **Azure Dev Spaces**.

Adres URL można również znaleźć, uruchamiając `azds list-uris` polecenie:
```
$ azds list-uris
Uri                                                     Status
------------------------------------------------------  ---------
http://default.mywebapi.d05afe7e006a4fddb73c.eus.azds.io/  Available
```

Użyj tego adresu URL podczas uzyskiwania dostępu do usługi.
