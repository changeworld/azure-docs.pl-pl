---
title: Wdrażanie zapory platformy Azure przy użyciu szablonu
description: Wdrażanie zapory platformy Azure przy użyciu szablonu
services: firewall
author: vhorne
manager: jpconnock
ms.service: firewall
ms.topic: article
ms.date: 7/11/2018
ms.author: victorh
ms.openlocfilehash: d32e6e29c287d140c28206743e36dc025b26158b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991338"
---
# <a name="deploy-azure-firewall-using-a-template"></a>Wdrażanie zapory platformy Azure przy użyciu szablonu

Ten szablon umożliwia utworzenie zapory i sieci środowiska testowego. Sieć ma jednej sieci wirtualnej, z trzema podsieciami: *AzureFirewallSubnet*, *ServersSubnet*, a *JumpboxSubnet*. Podsieci ServersSubnet i JumpboxSubnet obejmują po jednej 2-rdzeniowej maszynie z systemem Windows Server.

Zapora znajduje się w podsieci AzureFirewallSubnet i jest w niej skonfigurowana kolekcja reguł aplikacji z pojedynczą regułą zezwalającą na dostęp do witryny www.microsoft.com.

Tworzona jest trasa zdefiniowana przez użytkownika, która kieruje ruch sieciowy z podsieci ServersSubnet przez zaporę, gdzie są stosowane reguły zapory.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="template-location"></a>Lokalizacja szablonu

Szablon znajduje się w:

[https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-sandbox](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-sandbox)

Przeczytaj wprowadzenie, a gdy wszystko będzie gotowe do wdrożenia, kliknij przycisk **Wdróż na platformie Azure**.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Najpierw zapoznaj się z zasobami, które zostały utworzone za pomocą zapory, a następnie w trakcie nie będą już potrzebne, można użyć [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) polecenie, aby usunąć grupę zasobów, zapory i wszystkie pokrewne zasoby.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```
## <a name="next-steps"></a>Kolejne kroki

Następnie można monitorować dzienniki zapory platformy Azure:

- [Samouczek: monitorowanie dzienników usługi Azure Firewall](./tutorial-diagnostics.md)

