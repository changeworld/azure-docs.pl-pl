---
title: Wdrażanie zapory platformy Azure przy użyciu szablonu
description: Wdrażanie zapory platformy Azure przy użyciu szablonu
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 7/9/2018
ms.author: victorh
ms.openlocfilehash: b39174152e427e408e7dfbbc353baf5f96ec7c01
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657088"
---
# <a name="deploy-azure-firewall-using-a-template"></a>Wdrażanie zapory platformy Azure przy użyciu szablonu

[Szablonu konfiguracji piaskownicy tworzenie AzureFirewall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox) tworzy środowisko testowe sieci za pomocą zapory. Sieci ma jednej sieci wirtualnej (VNet) z trzema podsieciami: *AzureFirewallSubnet*, *ServersSubnet*, i *JumpboxSubnet*. *ServersSubnet* i *JumpboxSubnet* każda podsieć ma jednej, dwóch podstawowej maszyny wirtualnej systemu Windows Server.

Zapora jest w *AzureFirewallSubnet* podsieci, a kolekcja reguł aplikacji przy użyciu jednej reguły, które zezwalają na dostęp do *www.microsoft.com*.

Zdefiniowana przez użytkownika trasa wskazuje ruch sieciowy z *ServersSubnet* podsieci za pośrednictwem zapory, w których stosowane są reguły zapory.

Aby uzyskać więcej informacji na temat zapory usługi Azure, zobacz [Wdróż i Skonfiguruj zaporę platformy Azure przy użyciu witryny Azure portal](tutorial-firewall-deploy-portal.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-the-template-to-deploy-azure-firewall"></a>Szablon służy do wdrażania zapory platformy Azure

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

**Aby zainstalować i wdrożyć zapory platformy Azure przy użyciu szablonu:**

1. Uzyskiwać dostęp do szablonu w [ https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox).
   
1. Przeczytaj wprowadzenie, a gdy wszystko będzie gotowe do wdrożenia, wybierz **Wdróż na platformie Azure**.
   
1. Zaloguj się do witryny Azure portal w razie potrzeby. 

1. W portalu na **Tworzenie ustawień piaskownicy AzureFirewall** strony, wpisz lub wybierz następujące wartości:
   
   - **Grupa zasobów**: Wybierz **Utwórz nową**, wpisz nazwę grupy zasobów, a wybierz **OK**. 
   - **Nazwa sieci wirtualnej**: Wpisz nazwę nowej sieci wirtualnej. 
   - **Nazwa użytkownika administratora**: Wpisz nazwę użytkownika dla konta administratora.
   - **Hasło administratora**: Wpisz hasło administratora. 
   
1. Przeczytaj warunki i postanowienia, a następnie wybierz **zgodę na warunki i postanowienia, o których wspomniano**.
   
1. Wybierz pozycję **Kup**.
   
   Potrwa kilka minut, aby utworzyć zasoby. 
   
1. Zapoznaj się z zasobami, które zostały utworzone za pomocą zapory. 

Aby poznać składnię JSON i właściwości dla zapory w szablonie, zobacz temat [Microsoft.Network/azureFirewalls](/azure/templates/microsoft.network/azurefirewalls).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, możesz usunąć grupy zasobów, zapory i wszystkie pokrewne zasoby, uruchamiając [AzResourceGroup Usuń](/powershell/module/az.resources/remove-azresourcegroup) polecenia programu PowerShell. Aby usunąć grupę zasobów o nazwie *MyResourceGroup*Uruchom: 

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```
Nie usuwaj grupy zasobów i zapory, jeśli planowane jest przejście do zapory samouczek monitorowania. 

## <a name="next-steps"></a>Następne kroki

Następnie można monitorować dzienniki zapory platformy Azure:

> [!div class="nextstepaction"]
> [Samouczek: Monitorowanie dzienników usługi Azure Firewall](./tutorial-diagnostics.md)
