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
ms.openlocfilehash: 01358d13d30358a9950cbe35622df065fc5a6de5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133786"
---
W tym artykule używa poleceń cmdlet programu PowerShell. Aby uruchomić polecenia cmdlet, można użyć usługi Azure Cloud Shell, środowisko interaktywnej powłoki hostowanych na platformie Azure i używać za pośrednictwem przeglądarki. Usługa Azure Cloud Shell jest powiązana z poleceń cmdlet programu PowerShell platformy Azure wstępnie zainstalowane.

Aby uruchomić dowolny kod zawarty w tym artykule w usłudze Azure Cloud Shell, otwórz sesję usługi Cloud Shell, należy użyć **kopiowania** przycisku w bloku kodu, aby skopiować kod i wklej go do sesji usługi Cloud Shell za pomocą __Ctrl + Shift + V__ na Windows i Linux, lub __Cmd + Shift + V__ w systemie macOS. Wklejony tekst nie zostanie wykonany automatycznie, więc naciśnij **Enter** do uruchomienia kodu.

Można uruchomić usługi Azure Cloud Shell przy użyciu:

|  |   |
|-----------------------------------------------|---|
| Wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. To __nie__ automatycznie skopiować tekst do usługi Cloud Shell. | ![Przykład Wypróbuj usługę Azure Cloud Shell](./media/cloud-shell-try-it/cli-try-it.png) |
| Otwórz [shell.azure.com](https://shell.azure.com) w przeglądarce. | [![Uruchom usługę Azure Cloud Shell, przycisk](./media/cloud-shell-try-it/launchcloudshell.png)](https://shell.azure.com) |
| Wybierz przycisk **Cloud Shell** w menu w prawym górnym rogu witryny [Azure Portal](https://portal.azure.com). | ![Przycisk Cloud Shell w witrynie Azure Portal](./media/cloud-shell-try-it/cloud-shell-menu.png) |

**Uruchamianie programu PowerShell lokalnie**

Można również zainstalować i uruchomić polecenia cmdlet programu PowerShell platformy Azure lokalnie na komputerze. Polecenia cmdlet programu PowerShell są często aktualizowane. Jeśli nie używasz najnowszej wersji, wartości określonych w instrukcji może zakończyć się niepowodzeniem. Aby znaleźć wersji programu Azure PowerShell na komputerze zainstalowany, użyj `Get-Module -ListAvailable Az` polecenia cmdlet. Aby zainstalować lub zaktualizować, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps).
