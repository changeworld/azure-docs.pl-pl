---
title: Plik dyrektywy include
description: Plik dyrektywy include
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: 4c4a5b66fe35da01a3661715e17a9fda20bc2411
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2018
ms.locfileid: "43184790"
---
## <a name="create-a-kubernetes-cluster-enabled-for-azure-dev-spaces"></a>Tworzenie klastra Kubernetes obsługującego usługę Azure Dev Spaces

1. Zaloguj się do witryny Azure Portal pod adresem http://portal.azure.com.
1. Wybierz pozycję **Utwórz zasób** > wyszukaj pozycję **Kubernetes** > wybierz pozycję **Kubernetes Service** > **Utwórz**.

   Wykonaj poniższe kroki pod każdym nagłówkiem formularza tworzenia klastra AKS.

    - **SZCZEGÓŁY PROJEKTU**: wybierz subskrypcję platformy Azure oraz nową lub istniejącą grupę zasobów platformy Azure.
    - **SZCZEGÓŁY KLASTRA**: wprowadź nazwę, region (obecnie musisz wybrać region EastUS, CentralUS, WestEurope, WestUS2, CanadaCentral lub CanadaEast), wersję oraz prefiks nazwy DNS dla klastra usługi AKS.
    - **SKALA**: wybierz rozmiar maszyny wirtualnej dla węzłów agenta AKS i liczbę węzłów. Jeśli rozpoczynasz pracę z usługą Azure Dev Spaces, jeden węzeł jest wystarczający, aby zapoznać się z wszystkimi funkcjami. Liczbę węzłów można łatwo dostosować w dowolnym momencie po wdrożeniu klastra. Pamiętaj, że rozmiaru maszyny wirtualnej nie można zmienić po utworzeniu klastra usługi AKS. Jednak w razie potrzeby skalowania w górę po wdrożeniu klastra usługi AKS możesz łatwo utworzyć nowy klaster usługi AKS z większymi maszynami wirtualnymi i przeprowadzić ponowne wdrożenie na tym większym klastrze za pomocą usługi Dev Spaces.

   Upewnij się, że wybrano platformę Kubernetes w wersji 1.9.6 lub nowszej.

   ![Ustawienia konfiguracji platformy Kubernetes](../media/common/Kubernetes-Create-Cluster-2.PNG)

   Wybierz pozycję **Dalej: uwierzytelnianie** po zakończeniu.

1. Wybierz żądane ustawienie kontroli dostępu na podstawie ról (RBAC, role-based access control). Usługa Azure Dev Spaces obsługuje klastry z włączoną lub wyłączoną kontrolą RBAC.

    ![Ustawienie kontroli RBAC](../media/common/k8s-RBAC.PNG)

1. Upewnij się, że włączono routing aplikacji protokołu HTTP.

   ![Włączanie routingu aplikacji protokołu HTTP](../media/common/Kubernetes-Create-Cluster-3.PNG)

    > [!Note]
    > Aby włączyć [routing aplikacji protokołu HTTP](/azure/aks/http-application-routing) w istniejącym klastrze, użyj polecenia: `az aks enable-addons --resource-group myResourceGroup --name myAKSCluster --addons http_application_routing`

1. Wybierz pozycje **Przegląd + utwórz**, a następnie po zakończeniu wybierz pozycję **Utwórz**.
