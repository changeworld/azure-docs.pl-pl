---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 684b212ca771af6c336cf6239e18ea367f2da5ce
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045096"
---
W tym artykule są stosowane polecenia cmdlet programu PowerShell. Aby uruchomić polecenia cmdlet, można użyć Azure Cloud Shell, interaktywnego środowiska powłoki hostowanego na platformie Azure i używanego przez przeglądarkę. Azure Cloud Shell jest dostarczany ze wstępnie zainstalowanymi poleceniami cmdlet Azure PowerShell.

Aby uruchomić dowolny kod zawarty w tym artykule na Azure Cloud Shell, Otwórz sesję Cloud Shell, użyj przycisku **Kopiuj** w bloku kodu, aby skopiować kod, i wklej go do sesji Cloud Shell za pomocą __kombinacji klawiszy Ctrl + Shift + v__ w systemach Windows i Linux lub __cmd + Shift + v__ w macOS. Wklejony tekst nie jest automatycznie wykonywany, dlatego naciśnij klawisz **Enter** , aby uruchomić kod.

Azure Cloud Shell można uruchomić z:

|  |   |
|-----------------------------------------------|---|
| Wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. __Nie powoduje__ to automatycznego kopiowania tekstu do Cloud Shell. | ![Przykład try dla Azure Cloud Shell](./media/cloud-shell-try-it/hdi-azure-cli-try-it.png) |
| Otwórz [Shell.Azure.com](https://shell.azure.com) w przeglądarce. | [przycisk ![uruchamiania Azure Cloud Shell](./media/cloud-shell-try-it/hdi-launch-cloud-shell.png)](https://shell.azure.com) |
| Wybierz przycisk **Cloud Shell** w menu w prawym górnym rogu witryny [Azure Portal](https://portal.azure.com). | ![Przycisk Cloud Shell w witrynie Azure Portal](./media/cloud-shell-try-it/hdi-cloud-shell-menu.png) |

**Uruchamianie programu PowerShell lokalnie**

Polecenia cmdlet Azure PowerShell można również zainstalować i uruchomić lokalnie na komputerze. Polecenia cmdlet programu PowerShell są często aktualizowane. Jeśli nie korzystasz z najnowszej wersji, wartości określone w instrukcjach mogą zakończyć się niepowodzeniem. Aby znaleźć wersje Azure PowerShell zainstalowanych na komputerze, użyj polecenia cmdlet `Get-Module -ListAvailable Az`. Aby zainstalować lub zaktualizować, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps).

Jeśli używasz programu PowerShell lokalnie, pamiętaj, aby uruchomić polecenie "Connect-AzAccount", aby utworzyć połączenie z platformą Azure.