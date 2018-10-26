---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/25/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b878d54f0f52768459dbfc810e47d294b9c8d996
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2018
ms.locfileid: "50097770"
---
W tym artykule używa poleceń cmdlet programu PowerShell. Aby uruchomić polecenia cmdlet, można użyć usługi Azure Cloud Shell, bezpłatna interaktywna powłoka. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Po prostu kliknij przycisk **Kopiuj**, aby skopiować kod, wklej go do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby go uruchomić. Istnieje kilka sposobów uruchomienia usługi Cloud Shell:

|  |   |
|-----------------------------------------------|---|
| Kliknij przycisk **Wypróbuj** w prawym górnym rogu bloku kodu. | ![Usługa Cloud Shell w tym artykule](./media/vpn-gateway-cloud-shell-powershell/cloud-shell-powershell-try-it.png) |
| Otwórz usługę Cloud Shell w swojej przeglądarce. | [![https://shell.azure.com/powershell](./media/vpn-gateway-cloud-shell-powershell/launchcloudshell.png)](https://shell.azure.com/powershell) |
| Kliknij przycisk **Cloud Shell** w menu w prawym górnym rogu witryny Azure Portal. | [![Usługa Cloud Shell w portalu](./media/vpn-gateway-cloud-shell-powershell/cloud-shell-menu.png)](https://portal.azure.com) |
|  |  |

Jeśli nie chcesz używać usługi Azure Cloud Shell, można zainstalować programu PowerShell lokalnie zamiast tego. Jeśli zdecydujesz się zainstalować program PowerShell i używać lokalnie, należy zainstalować najnowszą wersję poleceń cmdlet programu Azure PowerShell Resource Manager. Polecenia cmdlet programu PowerShell są często aktualizowane i zwykle konieczne zaktualizowanie poleceń cmdlet programu PowerShell można pobrać najnowszych wersji funkcji. Jeśli nie zaktualizujesz poleceń cmdlet programu PowerShell, mogą wystąpić błędy określonych wartości. Aby znaleźć wersję programu PowerShell, które uruchamiasz lokalnie, użyj polecenia cmdlet "Get-Module - ListAvailable AzureRM". Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu programu Azure PowerShell](/powershell/azure/install-azurerm-ps). Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).