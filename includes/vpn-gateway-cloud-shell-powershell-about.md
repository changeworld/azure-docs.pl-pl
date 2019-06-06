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
ms.openlocfilehash: f6af5e5e42a16548b1997845ea5076ddd4dd3be5
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735920"
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

Można również zainstalować i uruchomić polecenia cmdlet programu PowerShell platformy Azure lokalnie na komputerze. Polecenia cmdlet programu PowerShell są często aktualizowane. Jeśli nie używasz najnowszej wersji, wartości określonych w instrukcji może zakończyć się niepowodzeniem. Aby znaleźć wersję programu PowerShell, które uruchamiasz lokalnie, należy użyć `Get-Module -ListAvailable Az` polecenia cmdlet. Aby zainstalować lub zaktualizować, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps).
