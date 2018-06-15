---
title: Usuwanie klastra platformy Azure i jej zasobach | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Aby całkowicie usunąć klastra usługi sieć szkieletowa, usunięcie grupy zasobów, zawierającą klaster lub selektywne usuwanie zasobów.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: de422950-2d22-4ddb-ac47-dd663a946a7e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/24/2017
ms.author: aljo
ms.openlocfilehash: 55840dc4b9f25c7e2676cff936390a0542eb239d
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34211273"
---
# <a name="delete-a-service-fabric-cluster-on-azure-and-the-resources-it-uses"></a>Usuwanie klastra sieci szkieletowej usług na platformie Azure i zasobów, które są używane
Klastra usługi sieć szkieletowa składa się z wielu innych zasobów platformy Azure oprócz samego zasobu klastra. Dlatego też, aby całkowicie usunąć klaster usługi Service Fabric, musisz również usunąć wszystkie zasoby, z których się składa.
Dostępne są dwie opcje: albo Usuń grupę zasobów, który znajduje się w klastrze (który usuwa zasób klastra i innych zasobów w grupie zasobów) lub usunięcie zasobu klastra, w szczególności i związany z zasobów (, ale nie innych zasobów w grupy zasobów).

> [!NOTE]
> Usuwanie zasobu klastra **nie** usunąć wszystkie zasoby, które klastra usługi sieć szkieletowa składa się z.
> 
> 

## <a name="delete-the-entire-resource-group-rg-that-the-service-fabric-cluster-is-in"></a>Usuń grupę zasobów całej (grupy zasobów), należącego do klastra sieci szkieletowej usług
To jest najprostszym sposobem, aby upewnić się, że należy usunąć wszystkie zasoby, które są skojarzone z klastrem, w tym grupy zasobów. Można usunąć grupy zasobów, przy użyciu programu PowerShell lub za pośrednictwem portalu Azure. Jeśli grupa zasobów zawiera zasoby, które nie są związane z klastrem usługi sieć szkieletowa, można usunąć określonych zasobów.

### <a name="delete-the-resource-group-using-azure-powershell"></a>Usuń grupę zasobów, przy użyciu programu Azure PowerShell
Możesz także usunąć grupy zasobów, uruchamiając następujące polecenia cmdlet programu Azure PowerShell. Upewnij się, że Azure PowerShell 1.0 lub jest zainstalowany na tym komputerze. Jeśli nie zostało zrobione to przed, wykonaj czynności opisane w temacie [sposób instalowania i konfigurowania programu Azure PowerShell.](/powershell/azure/overview)

Otwórz okno programu PowerShell i uruchom następujące polecenia cmdlet PS:

```powershell
Connect-AzureRmAccount

Remove-AzureRmResourceGroup -Name <name of ResouceGroup> -Force
```

Zostanie wyświetlony monit o potwierdzenie usunięcia, jeśli nie używasz *-Force* opcji. Na potwierdzenie grupy zasobów i wszystkie zasoby, które zawiera zostaną usunięte.

### <a name="delete-a-resource-group-in-the-azure-portal"></a>Usuń grupę zasobów, w portalu Azure
1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
2. Przejdź do klastra usługi Service Fabric, który chcesz usunąć.
3. Kliknij nazwę grupy zasobów na stronie essentials klastra.
4. Spowoduje to wyświetlenie **Essentials grupy zasobów** strony.
5. Kliknij polecenie **Usuń**.
6. Postępuj zgodnie z instrukcjami na stronie Kończenie procesu usuwania grupy zasobów.

![Usuń grupę zasobów][ResourceGroupDelete]

## <a name="delete-the-cluster-resource-and-the-resources-it-uses-but-not-other-resources-in-the-resource-group"></a>Usuń zasób klastra i zasoby, które są używane, ale nie innych zasobów w grupie zasobów
Jeśli grupa zasobów zawiera tylko tych zasobów, które są powiązane z klastra sieci szkieletowej usług, które chcesz usunąć, następnie łatwiej można usunąć grupy zasobów całej. Jeśli chcesz selektywnie usunąć jeden po drugim zasoby w grupie zasobów, należy wykonać następujące kroki.

Jeśli wdrożono klastra za pomocą portalu lub przy użyciu jednego z szablonów usługi sieć szkieletowa Resource Manager z galerii szablonów z następujących dwóch tagów są oznakowane wszystkie zasoby używane przez klaster. Można używać ich, aby zdecydować, które zasoby, które chcesz usunąć.

***Tag #1:*** klucz = Nazwa_klastra, wartość = "Nazwa klastra"

***Tag #2:*** klucz = resourceName, wartość = ServiceFabric

### <a name="delete-specific-resources-in-the-azure-portal"></a>Usuwanie określonych zasobów w portalu Azure
1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
2. Przejdź do klastra usługi Service Fabric, który chcesz usunąć.
3. Przejdź do **wszystkie ustawienia** w bloku essentials.
4. Polecenie **tagi** w obszarze **zarządzanie zasobami** w bloku ustawienia.
5. Kliknij jeden z **tagi** w bloku tagów, aby uzyskać listę wszystkich zasobów z tym znacznikiem.
   
    ![Tagi zasobów][ResourceTags]
6. Po utworzeniu listy zasobów oznakowany, kliknij każdy z zasobów i usuń je.
   
    ![Oznakowane zasobów][TaggedResources]

### <a name="delete-the-resources-using-azure-powershell"></a>Usuwanie zasobów przy użyciu programu Azure PowerShell
Możesz usunąć zasoby po kolei, uruchamiając następujące polecenia cmdlet programu Azure PowerShell. Upewnij się, że Azure PowerShell 1.0 lub jest zainstalowany na tym komputerze. Jeśli nie zostało zrobione to przed, wykonaj czynności opisane w temacie [sposób instalowania i konfigurowania programu Azure PowerShell.](/powershell/azure/overview)

Otwórz okno programu PowerShell i uruchom następujące polecenia cmdlet PS:

```powershell
Connect-AzureRmAccount
```
Dla każdego z zasobów, które chcesz usunąć Uruchom następujący skrypt:

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "<Resource Type>" -ResourceGroupName "<name of the resource group>" -Force
```

Aby usunąć zasobu klastra, uruchom następujący skrypt:

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "Microsoft.ServiceFabric/clusters" -ResourceGroupName "<name of the resource group>" -Force
```

## <a name="next-steps"></a>Kolejne kroki
Przeczytaj następujące również informacje na temat uaktualniania klastra i partycjonowanie usług:

* [Więcej informacji na temat uaktualnienia klastra](service-fabric-cluster-upgrade.md)
* [Więcej informacji na temat partycjonowania usługi stanowej maksymalną skalę](service-fabric-concepts-partitioning.md)

<!--Image references-->
[ResourceGroupDelete]: ./media/service-fabric-cluster-delete/ResourceGroupDelete.PNG

[ResourceTags]: ./media/service-fabric-cluster-delete/ResourceTags.png

[TaggedResources]: ./media/service-fabric-cluster-delete/TaggedResources.PNG
