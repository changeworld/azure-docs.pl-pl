---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/13/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c8f45e4bb16c05c9f322dd04d2c80f6144744e64
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60320247"
---
W tym artykule używa poleceń cmdlet programu PowerShell. Aby uruchomić polecenia cmdlet, można użyć usługi Azure Cloud Shell. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, mającej typowych narzędzi platformy Azure, które są wstępnie zainstalowane i skonfigurowane do korzystania z Twoim kontem. Po prostu kliknij **kopiowania** będzie skopiować kod, wklej go w usłudze Cloud Shell, a następnie naciśnij klawisz enter, aby go uruchomić. Istnieje kilka sposobów uruchomienia usługi Cloud Shell:

|  |   |
|-----------------------------------------------|---|
| Kliknij przycisk **Wypróbuj** w prawym górnym rogu bloku kodu. | ![Usługa Cloud Shell w tym artykule](./media/vpn-gateway-cloud-shell-powershell/cloud-shell-powershell-try-it.png) |
| Otwórz usługę Cloud Shell w swojej przeglądarce. | [![https://shell.azure.com/powershell](./media/vpn-gateway-cloud-shell-powershell/launchcloudshell.png)](https://shell.azure.com/powershell) |
| Kliknij przycisk **Cloud Shell** w menu w prawym górnym rogu witryny Azure Portal. | [![Usługa Cloud Shell w portalu](./media/vpn-gateway-cloud-shell-powershell/cloud-shell-menu.png)](https://portal.azure.com) |
|  |  |

**Uruchamianie programu PowerShell lokalnie**

Można również zainstalować i uruchomić polecenia cmdlet programu PowerShell platformy Azure lokalnie na komputerze. Polecenia cmdlet programu PowerShell są często aktualizowane. Jeśli nie używasz najnowszej wersji, wartości określonych w instrukcji może zakończyć się niepowodzeniem. Aby znaleźć wersję programu PowerShell, które uruchamiasz lokalnie, należy użyć `Get-Module -ListAvailable Az` polecenia cmdlet. Aby zainstalować lub zaktualizować, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps).