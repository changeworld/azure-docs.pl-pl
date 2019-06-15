---
title: Łączenie z usługi Azure Kubernetes Service (AKS) z usługą Azure Database for MySQL
description: Dowiedz się więcej o łączeniu z usługi Azure Kubernetes Service z usługą Azure Database for MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 11/28/2018
ms.openlocfilehash: d9f2e26a2bc89329ca9038c666c0d960289e2670
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60790525"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-mysql"></a>Łączenie usługi Azure Kubernetes Service i Azure Database for MySQL

Usługa Azure Kubernetes Service (AKS) umożliwia zarządzanych klastrów Kubernetes, których używasz na platformie Azure. Poniżej przedstawiono niektóre opcje, które należy wziąć pod uwagę podczas korzystania z usługi AKS i Azure Database for MySQL ze sobą do tworzenia aplikacji.


## <a name="accelerated-networking"></a>Wydajniejsze sieci
Użyj jej jako przyspieszonej z obsługą sieci podstawowych maszyn wirtualnych w klastrze AKS. Po włączeniu przyspieszonej łączności sieciowej na maszynie Wirtualnej istnieje mniejsze opóźnienia, jednoczesnym zmniejszeniu zakłóceń i obniżenie wykorzystanie procesora CPU na maszynie Wirtualnej. Dowiedz się więcej na temat przyspieszonej sieci działa, obsługiwanej wersji systemu operacyjnego i obsługiwane wystąpienia maszyn wirtualnych na [Linux](../virtual-network/create-vm-accelerated-networking-cli.md).

Od listopada 2018 r. AKS obsługuje przyspieszonej łączności sieciowej na tych obsługiwanych wystąpień maszyn wirtualnych. Przyspieszona sieć jest włączona domyślnie na nowych klastrów usługi AKS, korzystających z tych maszyn wirtualnych.

Można sprawdzić, czy klaster AKS jest przyspieszoną sieć:
1. Przejdź do witryny Azure portal i wybierz klaster usługi AKS.
2. Wybierz kartę właściwości.
3. Kopiuj nazwę **grupa zasobów infrastruktury**.
4. Użyj paska wyszukiwania portalu, aby znaleźć i otworzyć grupa zasobów infrastruktury.
5. Wybierz maszynę Wirtualną w tej grupie zasobów.
6. Przejdź do maszyny Wirtualnej **sieć** kartę.
7. Upewnij się, czy **funkcji przyspieszonej łączności sieciowej** ma wartość "włączona."

Lub za pośrednictwem wiersza polecenia platformy Azure przy użyciu dwóch następujących poleceń:
```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query "nodeResourceGroup"
```
Dane wyjściowe będą grupy zasobów wygenerowanego, że AKS tworzy zawierający interfejs sieciowy. Przyjmuje nazwę "nodeResourceGroup" i używać go w następnego polecenia. **EnableAcceleratedNetworking** będzie mieć wartość true lub false:
```azurecli
az network nic list --resource-group nodeResourceGroup -o table
```

## <a name="open-service-broker-for-azure"></a>Open Service Broker for Azure 
[Otwórz Service Broker for Azure](https://github.com/Azure/open-service-broker-azure/blob/master/README.md) (OSBA) umożliwia udostępniania usług platformy Azure bezpośrednio z klastrem Kubernetes lub Cloud Foundry. Jest [interfejsu Open Service Broker API](https://www.openservicebrokerapi.org/) wdrożenia na platformie Azure.

Z usługą OSBA można tworzyć serwera Azure Database for MySQL i powiązać klastra usługi AKS przy użyciu natywnego języka Kubernetes. Dowiedz się więcej o sposobie używania OSBA i Azure Database for MySQL razem na [strony OSBA GitHub](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/mysql.md). 



## <a name="next-steps"></a>Kolejne kroki
- [Utwórz klaster Azure Kubernetes Service](../aks/kubernetes-walkthrough.md)
- Dowiedz się, jak [Zainstaluj platformę WordPress z planu narzędzia Helm, przy użyciu OSBA i Azure Database for MySQL](../aks/integrate-azure.md)
