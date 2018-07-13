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
ms.openlocfilehash: 1a732e22d72c36afe11030e42bae529baa35df1a
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2018
ms.locfileid: "38991379"
---
# <a name="deploy-azure-firewall-using-a-template"></a>Wdrażanie zapory platformy Azure przy użyciu szablonu

[!INCLUDE [firewall-preview-notice](../../includes/firewall-preview-notice.md)]

Przykłady w artykułach zapory usługi Azure założono włączonego zapory usługi Azure w wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [Włącz zaporę usługi Azure w wersji zapoznawczej](public-preview.md).

Ten szablon umożliwia utworzenie zapory i sieci środowiska testowego. Sieć ma jednej sieci wirtualnej, z trzema podsieciami: *AzureFirewallSubnet*, *ServersSubnet*, a *JumpboxSubnet*. ServersSubnet, jak i JumpboxSubnet ma jeden 2-rdzeniowej systemu Windows Server w nich.

Zapora jest AzureFirewallSubnet i skonfigurowano kolekcji reguł aplikacji przy użyciu jednej reguły, które zezwalają na dostęp do www.microsoft.com.

Tworzony jest trasy zdefiniowanej przez użytkownika, który wskazuje ruch sieciowy z ServersSubnet przez zaporę, w których stosowane są reguły zapory.

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

- [Samouczek: Monitorowanie Azure dzienniki zapory](./tutorial-diagnostics.md)

