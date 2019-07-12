---
title: Ponowne wdrażanie maszyn wirtualnych Windows na platformie Azure | Dokumentacja firmy Microsoft
description: Jak ponownie wdrożyć maszyny wirtualne Windows na platformie Azure, aby rozwiązać problemy z połączeniem RDP.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: genlin
manager: gwallace
tags: azure-resource-manager,top-support-issue
ms.assetid: 0ee456ee-4595-4a14-8916-72c9110fc8bd
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 48b10b6fd854cc6da1f2ea2d48852d162e995946
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67710251"
---
# <a name="redeploy-windows-virtual-machine-to-new-azure-node"></a>Wdróż ponownie maszynę wirtualną Windows nowym węźle platformy Azure
Jeśli został skierowany trudności Rozwiązywanie problemów z pulpitu zdalnego (RDP) połączenia lub aplikacji dostęp do opartych na Windows Azure maszynę wirtualną (VM), ponowne wdrożenie maszyny Wirtualnej może pomóc. Podczas ponownego wdrażania maszyny Wirtualnej z systemem Azure zamknie maszynę Wirtualną, Przenieś maszynę Wirtualną do nowego węzła w ramach infrastruktury platformy Azure, a następnie włącz go ponownie na zachowaniu wszystkich opcji konfiguracji i skojarzonych zasobów. W tym artykule pokazano, jak przeprowadzić ponowne wdrożenie maszyny Wirtualnej przy użyciu programu Azure PowerShell lub witryny Azure portal.

> [!NOTE]
> Po przeprowadzić ponowne wdrożenie maszyny Wirtualnej, dysk tymczasowy to dysk utracone i dynamiczne adresy IP skojarzoną z interfejsem sieci wirtualnej zostały zaktualizowane. 


## <a name="using-azure-powershell"></a>Korzystanie z programu Azure PowerShell
Upewnij się, że masz najnowszą wersję programu Azure PowerShell 1.x zainstalowana na tym komputerze. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).

Poniższy przykład służy do wdrażania maszyny Wirtualnej o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup`:

```powershell
Set-AzVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Następne kroki
Jeśli występują problemy z nawiązywaniem połączenia z maszyną wirtualną, możesz znaleźć pomoc na [Rozwiązywanie problemów z połączeniami RDP](troubleshoot-rdp-connection.md) lub [szczegółowe kroki rozwiązywania problemów RDP](detailed-troubleshoot-rdp.md). Jeśli nie masz dostępu aplikacji uruchomionej na maszynie Wirtualnej, można również przeczytać [aplikacji Rozwiązywanie problemów z](../windows/troubleshoot-app-connection.md).

