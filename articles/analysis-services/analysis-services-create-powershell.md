---
title: 'Szybki start: tworzenie serwera usługi Azure Analysis Services przy użyciu programu PowerShell | Microsoft Docs'
description: Dowiedz się, jak utworzyć serwer usług Azure Analysis Services przy użyciu programu PowerShell
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 4b5b00da1c920d249283625e4970510ea00bafc8
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2019
ms.locfileid: "54190748"
---
# <a name="quickstart-create-a-server---powershell"></a>Szybki start: Tworzenie serwera — PowerShell

W tym przewodniku Szybki start opisano, jak utworzyć serwer usługi Azure Analysis Services przy użyciu programu PowerShell z poziomu wiersza polecenia w ramach subskrypcji platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- **Subskrypcja platformy Azure**: odwiedź stronę [bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), aby utworzyć konto.
- **Azure Active Directory**: subskrypcja musi być skojarzona z dzierżawą usługi Azure Active Directory i musisz mieć konto w tym katalogu. Aby dowiedzieć się więcej, zobacz [Authentication and user permissions (Uwierzytelnianie i uprawnienia użytkownika)](analysis-services-manage-users.md).
- **Moduł Azure PowerShell w wersji 4.0 lub nowszej**. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie ` Get-Module -ListAvailable AzureRM`. Aby przeprowadzić instalację lub uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="import-azurermanalysisservices-module"></a>Importowanie modułu AzureRm.AnalysisServices

Aby utworzyć serwer w ramach subskrypcji, należy użyć modułu składnika [AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices). Załaduj moduł AzureRm.AnalysisServices w sesji programu PowerShell.

```powershell
Import-Module AzureRM.AnalysisServices
```

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w subskrypcji platformy Azure przy użyciu polecenia [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount). Postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

```powershell
Connect-AzureRmAccount
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

[Grupa zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md) to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi w formie grupy. Podczas tworzenia serwera musisz podać grupę zasobów w ramach subskrypcji. Jeśli nie masz jeszcze grupy zasobów, możesz ją utworzyć teraz przy użyciu polecenia [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie `myResourceGroup` w regionie Zachodnie stany USA.

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "WestUS"
```

## <a name="create-a-server"></a>Tworzenie serwera

Utwórz nowy serwer przy użyciu polecenia [New-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver). W poniższym przykładzie utworzono serwer o nazwie myServer w grupie myResourceGroup w regionie Zachodnie stany USA w warstwie D1 (bezpłatnej) i określono użytkownika philipc@adventureworks.com jako administratora serwera.

```powershell
New-AzureRmAnalysisServicesServer -ResourceGroupName "myResourceGroup" -Name "myserver" -Location WestUS -Sku D1 -Administrator "philipc@adventure-works.com"
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Możesz usunąć serwer z subskrypcji przy użyciu polecenia [Remove-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver). Jeśli będziesz korzystać z innych przewodników Szybki start i samouczków w tej kolekcji, nie usuwaj serwera. Poniższy przykład obejmuje usuwanie serwera utworzonego w poprzednim kroku.


```powershell
Remove-AzureRmAnalysisServicesServer -Name "myserver" -ResourceGroupName "myResourceGroup"
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start przedstawiono, jak utworzyć serwer w ramach subskrypcji platformy Azure przy użyciu programu PowerShell. Teraz, po utworzeniu serwera, możesz go zabezpieczyć, konfigurując (opcjonalną) zaporę serwera. Możesz też dodać do serwera podstawowy przykładowy model danych bezpośrednio w portalu. Przykładowy model służy do zdobywania wiedzy na temat konfigurowania ról modelowej bazy danych oraz testowania połączeń klienckich. Aby dowiedzieć się więcej, przejdź do samouczka dotyczącego dodawania przykładowego modelu.

> [!div class="nextstepaction"]
> [Szybki start: Konfigurowanie zapory serwera — Portal](analysis-services-qs-firewall.md)      
> [!div class="nextstepaction"]
> [Samouczek: dodawanie przykładowego modelu do serwera](analysis-services-create-sample-model.md)