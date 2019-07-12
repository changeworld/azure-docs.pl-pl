---
title: Business ciągłości działania i odzyskiwania po awarii w Azure Dev miejsca do magazynowania
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: lisaguthrie
ms.author: lcozzens
ms.date: 01/28/2019
ms.topic: conceptual
description: Szybkie tworzenie w środowisku Kubernetes za pomocą kontenerów i mikrousług na platformie Azure
keywords: 'Docker, Kubernetes, Azure, usługi AKS, usłudze Azure Kubernetes Service, kontenerów, narzędzia Helm, usługa siatki, routing siatki usługi, narzędzia kubectl, k8s '
manager: gwallace
ms.openlocfilehash: 2da92b4fcd98024ada8d852d65e08fe8c70e3884
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704051"
---
# <a name="business-continuity-and-disaster-recovery-in-azure-dev-spaces"></a>Business ciągłości działania i odzyskiwania po awarii w Azure Dev miejsca do magazynowania

## <a name="review-disaster-recovery-guidance-for-azure-kubernetes-service-aks"></a>Przejrzyj wskazówki dotyczące odzyskiwania po awarii dla usługi Azure Kubernetes Service (AKS)

Usługa Azure Dev spacji jest funkcją Azure Kubernetes Service (AKS). Należy mieć świadomość wskazówki dotyczące odzyskiwania po awarii w usłudze AKS i należy wziąć pod uwagę, czy odnoszą się do klastrów usługi AKS, używanych do tworzenia miejsca do magazynowania. Aby uzyskać więcej informacji, zobacz [najlepszych rozwiązań biznesowych ciągłość działalności biznesowej i odzyskiwanie po awarii w usłudze Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region)

## <a name="enable-dev-spaces-on-aks-clusters-in-different-regions"></a>Włącz Dev miejsca do magazynowania w klastrach usługi AKS w różnych regionach

Włączanie tworzenia miejsca do magazynowania w klastrach usługi AKS w różnych regionach pozwala wznowić działanie przy użyciu standardowego miejsca do magazynowania, natychmiast po niepowodzeniu regionu świadczenia usługi Azure.

Aby uzyskać ogólne informacje dotyczące wdrażania usługi AKS w wielu regionach, zobacz [zaplanować wdrożenie w wielu regionach](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region#plan-for-multiregion-deployment)

Aby uzyskać informacji dotyczących wdrażania klastra usługi AKS, zgodny z usługi Azure Dev miejsca do magazynowania, zobacz [tworzenia klastra Kubernetes za pomocą usługi Azure Cloud Shell](https://docs.microsoft.com/azure/dev-spaces/how-to/create-cluster-cloud-shell)

### <a name="enable-dev-spaces-via-the-azure-portal"></a>Włącz Dev miejsca do magazynowania za pośrednictwem witryny Azure portal

Kliknij przycisk **Dev miejsca do magazynowania** element nawigacji w obszarze właściwości każdego klastra w witrynie Azure portal. Następnie wybierz opcję, aby włączyć Dev miejsca do magazynowania.

![Włączanie tworzenia miejsca do magazynowania za pośrednictwem witryny Azure portal](../media/common/enable-dev-spaces.jpg)

Powtórz tę procedurę dla każdego klastra.

### <a name="enable-dev-spaces-via-the-azure-cli"></a>Włącz Dev miejsca do magazynowania przy użyciu wiersza polecenia platformy Azure

Można również włączyć Dev miejsca do magazynowania, w wierszu polecenia:

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

## <a name="deploy-your-teams-baseline-to-each-cluster"></a>Wdrażanie zespołu odniesienia do każdego klastra

Podczas pracy z miejsc do magazynowania deweloperów, zazwyczaj wdrażania całej aplikacji, do obszaru dev nadrzędnego w klastrze Kubernetes. Domyślnie `default` miejsce jest używane. Początkowe wdrożenie obejmuje wszystkie usługi, a także zasobów zewnętrznych, zależne od tych usług, takich jak bazy danych lub kolejki. Jest to nazywane *linii bazowej*. Po skonfigurowaniu planu bazowego w obszarze dev nadrzędnego powtarzanie czynności w i debugować poszczególnych usług wewnątrz podrzędnych dev miejsca do magazynowania.

Najnowsze wersje linii bazowej zestawu usług należy wdrażać klastry w wielu regionach. Aktualizowanie usługi linii bazowej w ten sposób zapewnia można nadal używać spacji Dev, jeśli występuje awaria regionu świadczenia usługi Azure. Na przykład w przypadku wdrożenia punktu odniesienia i rozpoczęcie za pośrednictwem potoku ciągłej integracji/ciągłego wdrażania, zmodyfikuj potoku, tak aby wdrażania wielu klastrów w różnych regionach.

## <a name="select-the-correct-aks-cluster-to-use-for-dev-spaces"></a>Wybierz prawidłowy klaster AKS na potrzeby tworzenia miejsca do magazynowania

Po skonfigurowaniu prawidłowo kopii zapasowej klastra z systemem linii bazowej Twojego zespołu, można szybko przełączyć za pośrednictwem do tworzenia kopii zapasowej klastra w dowolnym momencie. Następnie możesz ponownie uruchomić poszczególnych usług, które użytkownik pracuje w Dev miejsca do magazynowania.

Wybierz innego klastra za pomocą następującego polecenia interfejsu wiersza polecenia:

```cmd
az aks use-dev-spaces -g <new resource group name> -n <new cluster name>
```

Lista może zawierać spacje deweloperskich dostępnych w nowym klastrze za pomocą następującego polecenia:

```cmd
azds space list
```

Możesz utworzyć nowe miejsce dev pracę w lub wybierz istniejący obszar dev, za pomocą następującego polecenia:

```cmd
azds space select -n <space name>
```

Po uruchomieniu tych poleceń, wybranego klastra i deweloperów miejsca będą używane dla kolejnych operacjach interfejsu wiersza polecenia i debugowania projektów przy użyciu rozszerzenia programu Visual Studio Code dla usługi Azure Dev miejsca do magazynowania.

Jeśli używasz programu Visual Studio, można przełączać się klastra używana przez istniejący projekt przez następujące kroki:

1. Otwórz swój projekt w programie Visual Studio.
1. Kliknij prawym przyciskiem myszy nazwę projektu w Eksploratorze rozwiązań, a następnie kliknij przycisk **właściwości**
1. W okienku po lewej stronie kliknij **debugowania**
1. Na stronie właściwości debugowania kliknij **profilu** listy rozwijanej i wybierz polecenie **miejsca do magazynowania Azure Dev**.
1. Kliknij przycisk **zmiany** przycisku.
1. W wyświetlonym oknie dialogowym wybierz klaster AKS, którego chcesz użyć. Jeśli to konieczne, wybierz miejsce na różnych deweloperów do pracy w lub utworzyć nowe miejsce dev, wybierając odpowiednią opcję z **miejsca** listy rozwijanej.

Po wybraniu klastra poprawne i miejsca naciśnięciu klawisza F5, aby uruchomić usługę w Dev miejsca do magazynowania.

Powtórz te kroki dla innych projektów skonfigurowany do używania oryginalnego klastra.

## <a name="access-a-service-on-a-backup-cluster"></a>Uzyskiwanie dostępu do usługi w klastrze kopii zapasowej

Jeśli skonfigurowano usługę do używania publicznej nazwy DNS, następnie usługi mają inny adres URL, jeśli zostanie ono uruchomione w klastrze kopii zapasowej. Publicznej nazwy DNS są zawsze w formacie `<space name>.s.<root space name>.<service name>.<cluster GUID>.<region>.azds.io`. Jeśli przełączysz się do innego klastra, zmieni się klastra identyfikator GUID i prawdopodobnie region.

Miejsca do magazynowania dev zawsze wyświetli poprawny adres URL dla usługi podczas uruchamiania `azds up`, lub w oknie danych wyjściowych w programie Visual Studio w obszarze **miejsca do magazynowania Azure Dev**.

Możesz również znaleźć adres URL, uruchamiając `azds list-uris` polecenia:
```
$ azds list-uris
Uri                                                     Status
------------------------------------------------------  ---------
http://default.mywebapi.d05afe7e006a4fddb73c.eus.azds.io/  Available
```

Podczas uzyskiwania dostępu do usługi, użyj tego adresu URL.
