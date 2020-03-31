---
title: Łączenie się z usługą Azure Kubernetes — usługa Azure Database dla mysql
description: Dowiedz się więcej o łączeniu usługi Azure Kubernetes z usługą Azure Database for MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 9e020d34b6cfb8117ccff1114cc938c966126b77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537282"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-mysql"></a>Łączenie usługi Azure Kubernetes i bazy danych platformy Azure dla mysql

Usługa Azure Kubernetes Service (AKS) udostępnia zarządzany klaster Kubernetes, którego można używać na platformie Azure. Poniżej znajduje się kilka opcji, które należy wziąć pod uwagę podczas korzystania z usługi AKS i azure database dla MySQL razem, aby utworzyć aplikację.


## <a name="accelerated-networking"></a>Wydajniejsze sieci
Użyj przyspieszonych podstawowych maszyn wirtualnych z obsługą sieci w klastrze AKS. Gdy przyspieszona sieć jest włączona na maszynie Wirtualnej, występuje mniejsze opóźnienie, zmniejszone jitter i zmniejszenie wykorzystania procesora CPU na maszynie Wirtualnej. Dowiedz się więcej o tym, jak działa przyspieszona sieć, obsługiwane wersje systemu operacyjnego i obsługiwane wystąpienia maszyn wirtualnych dla [systemu Linux.](../virtual-network/create-vm-accelerated-networking-cli.md)

Od listopada 2018 r. usługa AKS obsługuje przyspieszoną sieć w obsługiwanych wystąpieniach maszyn wirtualnych. Przyspieszona sieć jest domyślnie włączona w nowych klastrach AKS, które używają tych maszyn wirtualnych.

Można sprawdzić, czy klaster AKS przyspieszył tworzenie sieci:
1. Przejdź do witryny Azure portal i wybierz klaster AKS.
2. Wybierz kartę Properties (Właściwości).
3. Skopiuj nazwę **grupy zasobów infrastruktury**.
4. Użyj paska wyszukiwania portalu, aby zlokalizować i otworzyć grupę zasobów infrastruktury.
5. Wybierz maszynę wirtualną w tej grupie zasobów.
6. Przejdź do karty Sieci maszyny **Wirtualnej.**
7. Sprawdź, czy **przyspieszona sieć** jest "Włączona".

Lub za pośrednictwem interfejsu wiersza polecenia platformy Azure przy użyciu następujących dwóch poleceń:
```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query "nodeResourceGroup"
```
Dane wyjściowe będą wygenerowaną grupą zasobów, którą tworzy usługa AKS zawierająca interfejs sieciowy. Weź nazwę "nodeResourceGroup" i użyj jej w następnym poleceniu. **EnableAcceleratedNetworking** będzie true lub false:
```azurecli
az network nic list --resource-group nodeResourceGroup -o table
```

## <a name="open-service-broker-for-azure"></a>Open Service Broker for Azure 
[Open Service Broker for Azure](https://github.com/Azure/open-service-broker-azure/blob/master/README.md) (OSBA) umożliwia aprowizycja usług platformy Azure bezpośrednio z usługi Kubernetes lub Cloud Foundry. Jest to implementacja [interfejsu API open service broker](https://www.openservicebrokerapi.org/) dla platformy Azure.

Za pomocą OSBA można utworzyć usługę Azure Database dla serwera MySQL i powiązać go z klastrem AKS przy użyciu języka macierzystego usługi Kubernetes. Dowiedz się, jak używać OSBA i Azure Database dla MySQL razem na [stronie OSBA GitHub](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/mysql.md). 



## <a name="next-steps"></a>Następne kroki
- [Tworzenie klastra usługi Kubernetes platformy Azure](../aks/kubernetes-walkthrough.md)
- Dowiedz się, jak [zainstalować program WordPress z wykresu Helm przy użyciu OSBA i bazy danych platformy Azure dla mysql](../aks/integrate-azure.md)
