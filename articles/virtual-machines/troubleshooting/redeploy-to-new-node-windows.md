---
title: Ponowne wdrożenie maszyn wirtualnych systemu Windows na platformie Azure | Dokumenty firmy Microsoft
description: Jak ponownie wdrożyć maszyny wirtualne systemu Windows na platformie Azure, aby ograniczyć problemy z połączeniem RDP.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: genlin
manager: dcscontentpm
tags: azure-resource-manager,top-support-issue
ms.assetid: 0ee456ee-4595-4a14-8916-72c9110fc8bd
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 36af0eeb43fb209ed65f950576f2dc9e97ec3633
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71058641"
---
# <a name="redeploy-windows-virtual-machine-to-new-azure-node"></a>Ponowne wdrażanie maszyny wirtualnej z systemem Windows w nowym węźle platformy Azure
Jeśli masz trudności z rozwiązywaniem problemów z połączeniem pulpitu zdalnego (RDP) lub dostępem aplikacji do maszyny wirtualnej platformy Azure (Windows) — ponowne wdrożenie maszyny wirtualnej może pomóc. Po ponowne wdrożenie maszyny Wirtualnej, Platforma Azure zamknie maszynę wirtualną, przenieść maszynę wirtualną do nowego węzła w infrastrukturze platformy Azure, a następnie włączyć ją ponownie, zachowując wszystkie opcje konfiguracji i skojarzone zasoby. W tym artykule pokazano, jak ponownie wdrożyć maszynę wirtualną przy użyciu programu Azure PowerShell lub witryny Azure portal.

> [!NOTE]
> Po ponownej wydechu maszyny Wirtualnej dysk tymczasowy zostanie utracony i zostaną zaktualizowane dynamiczne adresy IP skojarzone z interfejsem sieci wirtualnej. 


## <a name="using-azure-powershell"></a>Korzystanie z programu Azure PowerShell
Upewnij się, że masz na komputerze zainstalowano najnowszą usługę Azure PowerShell 1.x. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).

Poniższy przykład wdraża maszynę `myVM` wirtualną o `myResourceGroup`nazwie w grupie zasobów o nazwie:

```powershell
Set-AzVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Następne kroki
Jeśli masz problemy z połączeniem się z maszyną wirtualną, możesz znaleźć konkretną pomoc dotyczącą [rozwiązywania problemów z połączeniami RDP](troubleshoot-rdp-connection.md) lub [szczegółowe kroki rozwiązywania problemów z prok.](detailed-troubleshoot-rdp.md) Jeśli nie można uzyskać dostępu do aplikacji uruchomionej na maszynie wirtualnej, można również przeczytać [problemy z rozwiązywaniem problemów z aplikacjami](../windows/troubleshoot-app-connection.md).

