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
ms.openlocfilehash: 05736495d0d4a0c3a5072d29ad27801b6d4a7241
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2018
ms.locfileid: "37967873"
---
## <a name="create-a-kubernetes-cluster-enabled-for-azure-dev-spaces"></a>Tworzenie klastra Kubernetes obsługującego usługę Azure Dev Spaces

1. Zaloguj się do witryny Azure Portal pod adresem http://portal.azure.com.
1. Wybierz pozycję **Utwórz zasób** > wyszukaj pozycję **Kubernetes** > wybierz pozycję **Kubernetes Service** > **Utwórz**.

   Wykonaj poniższe kroki pod każdym nagłówkiem formularza tworzenia klastra AKS.

    - **SZCZEGÓŁY PROJEKTU**: wybierz subskrypcję platformy Azure oraz nową lub istniejącą grupę zasobów platformy Azure.
    - **SZCZEGÓŁY KLASTRA**: wprowadź nazwę, region (obecnie musisz wybrać region EastUS, CentralUS, WestEurope, WestUS2, CanadaCentral lub CanadaEast), wersję oraz prefiks nazwy DNS dla klastra usługi AKS.
    - **SKALA**: wybierz rozmiar maszyny wirtualnej dla węzłów agenta AKS i liczbę węzłów. Jeśli rozpoczynasz pracę z usługą Azure Dev Spaces, jeden węzeł jest wystarczający, aby zapoznać się z wszystkimi funkcjami. Liczbę węzłów można łatwo dostosować w dowolnym momencie po wdrożeniu klastra. Pamiętaj, że rozmiaru maszyny wirtualnej nie można zmienić po utworzeniu klastra usługi AKS. Jednak w razie potrzeby skalowania w górę po wdrożeniu klastra usługi AKS możesz łatwo utworzyć nowy klaster usługi AKS z większymi maszynami wirtualnymi i przeprowadzić ponowne wdrożenie na tym większym klastrze za pomocą usługi Dev Spaces.

   Upewnij się, że wybrano platformę Kubernetes w wersji 1.10.3 lub nowszej.

   ![Ustawienia konfiguracji platformy Kubernetes](../media/common/Kubernetes-Create-Cluster-2.PNG)

   Po zakończeniu wybierz pozycję **Dalej: sieć**.

1. Upewnij się, że włączono routing aplikacji protokołu HTTP.

   ![Włączanie routingu aplikacji protokołu HTTP](../media/common/Kubernetes-Create-Cluster-3.PNG)

    > [!IMPORTANT]
    > Pamiętaj o włączeniu routingu aplikacji protokołu HTTP podczas tworzenia klastra usługi AKS. Zmiana tego ustawienia nie jest później możliwa.

1. Wybierz żądane ustawienie kontroli dostępu na podstawie ról (RBAC, role-based access control). Usługa Azure Dev Spaces obsługuje klastry z włączoną lub wyłączoną kontrolą RBAC.

    ![Ustawienie kontroli RBAC](../media/common/k8s-RBAC.PNG)

1. Wybierz pozycje **Przegląd + utwórz**, a następnie po zakończeniu wybierz pozycję **Utwórz**.
