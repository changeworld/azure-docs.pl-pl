---
title: Wdrażanie usługi Azure Firewall przy użyciu szablonu
description: Wdrażanie Zapory platformy Azure przy użyciu szablonu. Utworzona sieć ma jedną sieć wirtualną z trzema podsieciami. Wdrożono dwie, dwurdzeniowe maszyny wirtualne systemu Windows Server.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: b9e82998283bf44eede88148a12d88aae521b1ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74169195"
---
# <a name="deploy-azure-firewall-using-a-template"></a>Wdrażanie usługi Azure Firewall przy użyciu szablonu

[Szablon konfiguracji piaskownicy Create AzureFirewall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox) tworzy testowe środowisko sieciowe z zaporą. Sieć ma jedną sieć wirtualną (VNet) z trzema podsieciami: *AzureFirewallSubnet*, *ServersSubnet*i *JumpboxSubnet*. *Podsieć ServersSubnet* i *JumpboxSubnet* mają jedną, dwurdzeniową maszynę wirtualną systemu Windows Server.

Zapora znajduje się w podsieci *AzureFirewallSubnet* i ma kolekcję `www.microsoft.com`reguł aplikacji z jedną regułą, która umożliwia dostęp do .

Zdefiniowana przez użytkownika trasa wskazuje ruch sieciowy z podsieci *ServersSubnet* przez zaporę, w której stosowane są reguły zapory.

Aby uzyskać więcej informacji na temat Zapory platformy Azure, zobacz [Wdrażanie i konfigurowanie Zapory platformy Azure przy użyciu witryny Azure Portal](tutorial-firewall-deploy-portal.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-the-template-to-deploy-azure-firewall"></a>Wdrażanie Zapory platformy Azure za pomocą szablonu

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

**Aby zainstalować i wdrożyć Zaporę platformy Azure przy użyciu szablonu:**

1. Dostęp do [https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox)szablonu w programie .
   
1. Przeczytaj wprowadzenie, a gdy będzie gotowy do wdrożenia, wybierz pozycję **Wdrażanie na platformie Azure**.
   
1. W razie potrzeby zaloguj się do witryny Azure Portal. 

1. W portalu na stronie **Tworzenie piaskownicy usługi AzureFirewall** wpisz lub wybierz następujące wartości:
   
   - **Grupa zasobów**: Wybierz pozycję **Utwórz nowy**, wpisz nazwę grupy zasobów i wybierz **przycisk OK**. 
   - **Nazwa sieci wirtualnej:** Wpisz nazwę nowej sieci wirtualnej. 
   - **Nazwa użytkownika administratora:** Wpisz nazwę użytkownika dla konta użytkownika administratora.
   - **Hasło administratora:** Wpisz hasło administratora. 
   
1. Zapoznaj się z warunkami, a następnie wybierz **zgadzam się z warunkami podanymi powyżej.**
   
1. Wybierz pozycję **Kup**.
   
   Utworzenie zasobów zajmie kilka minut. 
   
1. Zapoznaj się z zasobami utworzonymi za pomocą zapory. 

Aby dowiedzieć się więcej o składni JSON i właściwościach zapory w szablonie, zobacz [Microsoft.Network/azureFirewalls](/azure/templates/microsoft.network/azurefirewalls).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie są już potrzebne, można usunąć grupę zasobów, zaporę i wszystkie powiązane zasoby, uruchamiając polecenie [Usuń-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) PowerShell. Aby usunąć grupę zasobów o nazwie *MyResourceGroup*, uruchom: 

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```
Nie usuwaj jeszcze grupy zasobów i zapory, jeśli planujesz kontynuować monitorowanie zapory samouczka. 

## <a name="next-steps"></a>Następne kroki

Następnie można monitorować dzienniki zapory platformy Azure:

> [!div class="nextstepaction"]
> [Samouczek: monitorowanie dzienników usługi Azure Firewall](./tutorial-diagnostics.md)
