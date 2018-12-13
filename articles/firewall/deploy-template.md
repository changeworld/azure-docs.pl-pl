---
title: Wdrażanie zapory platformy Azure przy użyciu szablonu
description: Wdrażanie zapory platformy Azure przy użyciu szablonu
services: firewall
author: vhorne
manager: jpconnock
ms.service: firewall
ms.topic: article
ms.date: 12/01/2018
ms.author: victorh
ms.openlocfilehash: 86fdbbacf3e8064afe0aaaaebea1d6ef6c25f9d4
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52865837"
---
# <a name="deploy-azure-firewall-using-a-template"></a>Wdrażanie zapory platformy Azure przy użyciu szablonu

[Szablonu konfiguracji piaskownicy tworzenie AzureFirewall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-sandbox) tworzy środowisko testowe sieci za pomocą zapory. Sieć ma jednej sieci wirtualnej (VNet) z trzema podsieciami: *AzureFirewallSubnet*, *ServersSubnet*, i *JumpboxSubnet*. *ServersSubnet* i *JumpboxSubnet* każda podsieć ma jednej, dwóch podstawowej maszyny wirtualnej systemu Windows Server.

Zapora jest w *AzureFirewallSubnet* podsieci, a kolekcja reguł aplikacji przy użyciu jednej reguły, które zezwalają na dostęp do *www.microsoft.com*.

Zdefiniowana przez użytkownika trasa wskazuje ruch sieciowy z *ServersSubnet* podsieci za pośrednictwem zapory, w których stosowane są reguły zapory.

Aby uzyskać więcej informacji na temat zapory usługi Azure, zobacz [Wdróż i Skonfiguruj zaporę platformy Azure przy użyciu witryny Azure portal](tutorial-firewall-deploy-portal.md).

## <a name="use-the-template-to-deploy-azure-firewall"></a>Szablon służy do wdrażania zapory platformy Azure

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

**Aby zainstalować i wdrożyć zapory platformy Azure przy użyciu szablonu:**

1. Uzyskiwać dostęp do szablonu w [ https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-sandbox ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-sandbox).
   
1. Przeczytaj wprowadzenie, a gdy wszystko będzie gotowe do wdrożenia, wybierz **Wdróż na platformie Azure**.
   
1. Zaloguj się do witryny Azure portal w razie potrzeby. 

1. W portalu na **Tworzenie ustawień piaskownicy AzureFirewall** strony, wpisz lub wybierz następujące wartości:
   
   - **Grupa zasobów**: Wybierz **Utwórz nową**, wpisz nazwę grupy zasobów, a wybierz **OK**. 
   - **Nazwa sieci wirtualnej**: wpisz nazwę nowej sieci wirtualnej. 
   - **Nazwa użytkownika administratora**: wpisz nazwę użytkownika dla konta administratora.
   - **Hasło administratora**: wpisz hasło administratora. 
   
1. Przeczytaj warunki i postanowienia, a następnie wybierz **zgodę na warunki i postanowienia, o których wspomniano**.
   
1. Wybierz pozycję **Kup**.
   
   Potrwa kilka minut, aby utworzyć zasoby. 
   
1. Zapoznaj się z zasobami, które zostały utworzone za pomocą zapory. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, możesz usunąć grupy zasobów, zapory i wszystkie pokrewne zasoby, uruchamiając [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) polecenia programu PowerShell. Aby usunąć grupę zasobów o nazwie *MyResourceGroup*Uruchom: 

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name MyResourceGroup
```
Nie usuwaj grupy zasobów i zapory, jeśli planowane jest przejście do zapory samouczek monitorowania. 

## <a name="next-steps"></a>Kolejne kroki

Następnie można monitorować dzienniki zapory platformy Azure:

> [!div class="nextstepaction"]
> [Samouczek: monitorowanie dzienników usługi Azure Firewall](./tutorial-diagnostics.md)
