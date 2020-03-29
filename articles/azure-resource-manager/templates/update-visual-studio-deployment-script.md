---
title: Aktualizowanie skryptu wdrażania szablonu programu Visual Studio w celu użycia programu Az PowerShell
description: Aktualizowanie skryptu wdrażania szablonu programu Visual Studio z usługi AzureRM do programu Az PowerShell
author: cweining
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: cweining
ms.openlocfilehash: 357e0289f3237ed32b0801280316225ba5530282
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76963873"
---
# <a name="update-visual-studio-template-deployment-script-to-use-az-powershell-module"></a>Aktualizowanie skryptu wdrażania szablonu programu Visual Studio w celu użycia modułu programu Az PowerShell

Visual Studio 16.4 obsługuje przy użyciu modułu Programu Az PowerShell w skrypcie wdrażania szablonu. Jednak visual studio nie automatycznie zainstalować tego modułu. Aby korzystać z modułu Az, należy wykonać cztery kroki:

1. [Odinstalowywanie modułu AzureRM](/powershell/azure/uninstall-az-ps#uninstall-the-azurerm-module)
1. [Instalowanie modułu Az](/powershell/azure/install-az-ps)
1. Aktualizowanie programu Visual Studio do wersji 16.4
1. Zaktualizuj skrypt wdrażania w projekcie.

## <a name="update-visual-studio-to-164"></a>Aktualizowanie programu Visual Studio do wersji 16.4

Zaktualizuj instalację programu Visual Studio do wersji 16.4 lub nowszej. Podczas uaktualniania upewnij się, że składnik programu Azure PowerShell nie jest zaznaczony. Ponieważ zainstalowano moduł Az za pośrednictwem galerii, nie chcesz ponownie zainstalować modułu AzureRM.

Jeśli został już uaktualniony do wersji 16.4 i sterownik azure powershell został sprawdzony, można go odinstalować, uruchamiając Instalator programu Visual Studio. Nie wybieraj składnika programu Azure PowerShell w obciążeniu platformy Azure ani na stronie poszczególnych składników.

## <a name="update-the-deployment-script-in-your-project"></a>Aktualizowanie skryptu wdrażania w projekcie

Zamień wszystkie wystąpienia ciągu "AzureRm" na "Az" w skrypcie wdrażania. Można odwołać się do poprawek w tym [sednie,](https://gist.github.com/cweining/d2da2479418ea403499c4306dcf4f619) aby zobaczyć zmiany. Aby uzyskać więcej informacji na temat uaktualniania skryptów do modułu Az, zobacz [Migrowanie programu Azure PowerShell z usługi AzureRM do Az](/powershell/azure/migrate-from-azurerm-to-az).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o korzystaniu z projektu programu Visual Studio, zobacz [Tworzenie i wdrażanie projektów grup zasobów platformy Azure za pośrednictwem programu Visual Studio.](create-visual-studio-deployment-project.md)
