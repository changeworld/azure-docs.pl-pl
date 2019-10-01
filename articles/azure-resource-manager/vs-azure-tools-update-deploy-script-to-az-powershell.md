---
title: Zaktualizuj skrypt wdrażania szablonów programu Visual Studio, aby użyć polecenia AZ PowerShell
description: Aktualizuj skrypt wdrożenia szablonu programu Visual Studio z AzureRM do AZ PowerShell
author: cweining
ms.service: azure-resource-manager
ms.topic: quickstart
ms.date: 09/27/2019
ms.author: cweining
ms.openlocfilehash: 483b20f0003994f88ec35cc9f31a77cf571804f3
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695848"
---
# <a name="updating-the-visual-studio-template-deployment-script-to-use-az-powershell"></a>Aktualizowanie skryptu wdrażania szablonu programu Visual Studio w celu użycia polecenia AZ PowerShell

Program Visual Studio 16,4 obsługuje używanie polecenia AZ PowerShell w skrypcie wdrażania szablonu. Program Visual Studio nie instaluje AZ PowerShell ani automatycznie aktualizuje skrypt wdrożenia w projekcie grupy zasobów. Aby można było użyć nowszego AZ PowerShell, należy wykonać następujące cztery czynności:
1. Odinstalowywanie programu AzureRM PowerShell
1. Zainstaluj AZ PowerShell
1. Aktualizacja do programu Visual Studio 16,4
1. Zaktualizuj skrypt wdrożenia w projekcie.

## <a name="uninstall-azurerm-powershell"></a>Odinstalowywanie programu AzureRM PowerShell
Postępuj zgodnie z tymi [instrukcjami](https://docs.microsoft.com/powershell/azure/uninstall-az-ps?view=azps-2.7.0#uninstall-the-azurerm-module) , aby odinstalować program AzureRM PowerShell.

## <a name="install-az-powershell"></a>Zainstaluj AZ PowerShell
Postępuj zgodnie z tymi [instrukcjami](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.7.0) , aby zainstalować AZ PowerShell.

## <a name="update-visual-studio-to-164"></a>Aktualizowanie programu Visual Studio do 16,4
Zaktualizuj do programu Visual Studio 16,4, gdy jest on dostępny. Podczas uaktualniania upewnij się, że składnik programu Azure PowerShell nie jest zaznaczony. Ponieważ zainstalowano AZ PowerShell za pomocą galerii, nie ma potrzeby AzureRM wersji programu PowerShell, która jest instalowana z programem Visual Studio.

Jeśli uaktualniono już do 16,4, a składnik programu Azure PowerShell został sprawdzony, można go odinstalować, uruchamiając Instalator programu Visual Studio i usuwając zaznaczenie składnika Azure PowerShell w obciążeniu platformy Azure lub na stronie poszczególne składniki.

## <a name="update-the-deployment-script-in-your-project"></a>Aktualizowanie skryptu wdrożenia w projekcie
Zamień wszystkie wystąpienia ciągu "AzureRm" na "AZ" w skrypcie wdrożenia. Możesz odwołać się do poprawek w tym [rejestrze](https://gist.github.com/cweining/d2da2479418ea403499c4306dcf4f619) , aby zobaczyć zmiany. Zapoznaj się z tą [dokumentacją](https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az?view=azps-2.5.0) , aby uzyskać więcej informacji na temat uaktualniania skryptów do programu AZ PowerShell.


