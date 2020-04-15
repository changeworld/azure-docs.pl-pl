---
title: Szybki start — tworzenie zasobu serwera usług Azure Analysis Services przy użyciu szablonu usługi Azure Resource Manager
description: Szybki start przedstawiający sposób zasób serwera usług Azure Analysis Services przy użyciu szablonu Usługi Azure Resource Manager.
author: minewiskan
ms.author: owend
tags: azure-resource-manager
ms.service: analysis-services
ms.topic: quickstart
ms.date: 04/14/2020
ms.custom: subject-armqs
ms.openlocfilehash: d292500c5e26d3c07ff2402964166b3928cc7e44
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384257"
---
# <a name="quickstart-create-a-server---azure-resource-manager-template"></a>Szybki start: tworzenie serwera — szablon usługi Azure Resource Manager

W tym przewodniku Szybki start opisano sposób tworzenia zasobu serwera usług Analysis Services w ramach subskrypcji platformy Azure przy użyciu szablonu Menedżera zasobów.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**: odwiedź stronę [bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), aby utworzyć konto.
* **Azure Active Directory**: subskrypcja musi być skojarzona z dzierżawą usługi Azure Active Directory. Ponadto musisz zalogować się na platformie Azure przy użyciu konta należącego do tej dzierżawy usługi Azure Active Directory. Aby dowiedzieć się więcej, zobacz [Authentication and user permissions (Uwierzytelnianie i uprawnienia użytkownika)](analysis-services-manage-users.md).

## <a name="create-a-server"></a>Tworzenie serwera

### <a name="review-the-template"></a>Przejrzyj szablon

Szablon używany w tym przewodniku Szybki start pochodzi z [szablonów szybki start platformy Azure](https://azure.microsoft.com/resources/templates/101-analysis-services-create/).

:::code language="json" source="~/quickstart-templates/101-analysis-services-create/azuredeploy.json":::

W szablonie zdefiniowano pojedynczy [zasób Microsoft.AnalysisServices/servers](https://docs.microsoft.com/azure/templates/microsoft.analysisservices/2017-08-01/servers) z regułą zapory. 

### <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz następujące łącze Wdrażanie na platformie Azure, aby zalogować się na platformę Azure i otworzyć szablon. Szablon jest używany do tworzenia zasobu serwera usług Analysis Services i określania wymaganych i opcjonalnych właściwości.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-analysis-services-create%2Fazuredeploy.json"><img src="./media/analysis-services-create-template/deploy-azure.png" alt="deploy to azure"/></a>

2. Wybierz lub wprowadź następujące wartości.

    O ile nie określono inaczej, należy użyć wartości domyślnych.

    * **Subskrypcja**: Wybierz subskrypcję platformy Azure.
    * **Grupa zasobów**: Kliknij pozycję **Utwórz nowy**, a następnie wprowadź unikatową nazwę dla nowej grupy zasobów.
    * **Lokalizacja**: Wybierz domyślną lokalizację dla zasobów utworzonych w grupie zasobów.
    * **Nazwa serwera**: Wprowadź nazwę zasobu serwera. 
    * **Lokalizacja:** Ignoruj usługi Analysis Services. Lokalizacja jest określona w lokalizacji serwera.
    * **Lokalizacja serwera:** Wprowadź lokalizację serwera usług Analysis Services. Jest to często ten sam region co domyślna lokalizacja określona dla grupy zasobów, ale nie jest wymagana. Na przykład **północno-środkowe stany USA**. Aby uzyskać obsługiwane regiony, zobacz [Analysis Services dostępność według regionu](analysis-services-overview.md#availability-by-region).
    * **Nazwa Sku**: Wprowadź nazwę sku dla serwera Analysis Services do utworzenia. Do wyboru: B1, B2, D1, S0, S1, S2, S3, S4, S8v2, S9v2. Dostępność Sku zależy od regionu. S0 lub D1 jest zalecane do oceny i testowania.
    * **Pojemność:** Wprowadź całkowitą liczbę wystąpień skalowanych replik kwerend. Skalowanie w poziomie więcej niż jednego wystąpienia jest obsługiwane tylko w wybranych regionach.
    * **Ustawienia zapory**: Wprowadź reguły zapory przychodzącej do zdefiniowania dla serwera. Jeśli nie zostanie określona, zapora jest wyłączona.
    * **Uri kontenera obiektów blob kopii zapasowej:** Wprowadź identyfikator URI sygnatury dostępu Współdzielonego do prywatnego kontenera usługi Azure Blob Storage z uprawnieniami do odczytu, zapisu i listy. Wymagane tylko wtedy, gdy zamierzasz użyć [kopii zapasowej/przywracania](analysis-services-backup.md).
    * **Wyrażam zgodę na powyższe warunki i postanowienia**: zaznacz.

3. Wybierz pozycję **Kup**. Po pomyślnym wdrożeniu serwera otrzymasz powiadomienie:

   ![Szablon Menedżera zasobów, wdrażanie powiadomienia portalu](./media/analysis-services-create-template/notification.png)

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

Użyj witryny Azure portal lub programu Azure PowerShell, aby zweryfikować, że grupa zasobów i zasób serwera zostały utworzone.

#### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
(Get-AzResource -ResourceType "Microsoft.AnalysisServices/servers" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, użyj witryny Azure Portal, interfejsu wiersza polecenia platformy Azure lub programu Azure PowerShell, aby usunąć grupę zasobów i zasób serwera.

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start użyto szablonu usługi Azure Resource Manager do utworzenia nowej grupy zasobów i zasobu serwera usług Azure Analysis Services. Po utworzeniu zasobu serwera przy użyciu szablonu należy wziąć pod uwagę następujące kwestie:
- [Szybki start: tworzenie serwera — PowerShell](analysis-services-create-powershell.md)
- [Dodawanie przykładowego modelu z portalu](analysis-services-create-sample-model.md)
- [Konfigurowanie ról administratora i użytkowników serwera](tutorials/analysis-services-tutorial-roles.md)