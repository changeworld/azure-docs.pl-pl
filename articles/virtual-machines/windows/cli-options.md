---
title: W systemie Windows przy użyciu interfejsu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: W systemie Windows przy użyciu interfejsu wiersza polecenia platformy Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/14/2017
ms.author: cynthn
ms.openlocfilehash: e1d611617b72a1fd5134b83bd3a2de59ba668b11
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="using-the-azure-cli-on-windows"></a>W systemie Windows przy użyciu interfejsu wiersza polecenia platformy Azure

Azure interfejsu wiersza polecenia (CLI) oferuje wiersza polecenia i skryptów środowiska do tworzenia i zarządzania zasobami platformy Azure. Interfejsu wiersza polecenia Azure jest dostępna dla macOS, Linux i systemów operacyjnych Windows. W tych systemach operacyjnych polecenia interfejsu wiersza polecenia są identyczne, jednak może się różnić składni skryptów systemu operacyjnego.

Ten dokument zawiera szczegóły dotyczące sposobów, że można instalować i uruchamiać na zagadnienia syntaktycznych systemu Windows i szczegóły dla każdego wiersza polecenia platformy Azure. Aby uzyskać szczegółowe interfejsu wiersza polecenia Azure dokumentacji, zobacz [dokumentacji interfejsu wiersza polecenia Azure]( https://docs.microsoft.com/cli/azure).

## <a name="windows-subsystem-for-linux"></a>Podsystem systemu Windows dla systemu Linux

Podsystem systemu Windows dla systemu Linux (WSL) udostępnia środowisko Ubuntu Linux na system Windows 10 Anniversary i późniejszych wersjach. Po włączeniu WSL zapewnia natywnym środowiskiem Bash, która może służyć do tworzenia i uruchamiania skryptów wiersza polecenia platformy Azure. Ponieważ WSL zapewnia natywnym środowiskiem Bash, skryptów wiersza polecenia platformy Azure może być współużytkowana macOS, Linux i Windows bez żadnych modyfikacji.

Aby użyć wiersza polecenia platformy Azure w WSL, wykonaj poniższe kroki.

|Zadanie | Instrukcje |
|---|---|
| Włącz WSL | [Zainstaluj WSL dokumentacji ](https://msdn.microsoft.com/commandline/wsl/install_guide) |
| Zainstaluj interfejs wiersza polecenia platformy Azure |[Instalowanie interfejsu wiersza polecenia na WSL/Ubuntu 14.04](https://docs.microsoft.com/cli/azure/install-az-cli2#ubuntu)|

## <a name="powershell"></a>PowerShell

Interfejsu wiersza polecenia Azure może działać natywnie w systemie Windows. W tej konfiguracji pakietu wiersza polecenia platformy Azure jest zainstalowany w systemie operacyjnym Windows, a polecenia można uruchomić z programu PowerShell. W tej konfiguracji interfejsu wiersza polecenia Azure poleceń i skryptów może działać w dowolnej obsługiwanej wersji systemu Windows, jednak platforma składni skryptów jest wymagane. W związku z tym skrypty nie zawsze mogą współużytkować system macOS, Linux i Windows bez żadnych modyfikacji.

Aby użyć wiersza polecenia platformy Azure w systemie Windows, należy zainstalować pakiet, korzystając z tych instrukcji [instalowanie interfejsu wiersza polecenia w systemie Windows](https://docs.microsoft.com/cli/azure/install-az-cli2#windows).

## <a name="docker-image"></a>Obraz docker

Korzystając z Docker dla systemu Windows, obrazu Docker można uruchomić zawierającą wiersza polecenia platformy Azure. Ten obraz jest oparte na systemie Linux i zawiera natywnym środowiskiem Bash.  Podczas używania Docker dla systemu Windows i interfejsu wiersza polecenia Azure obrazu, skrypty, które mogą być współużytkowane przez system macOS, Linux i Windows. 

Aby używać interfejsu wiersza polecenia Azure Docker dla systemu Windows, upewnij się, że Docker dla systemu Windows jest uruchomiona i uruchom następujące polecenie.

```bash
docker run -it azuresdk/azure-cli-python:latest bash
```

Po ukończeniu Bash, czyli rozpoczęcia sesji wstępnie ładowane przy użyciu narzędzi wiersza polecenia platformy Azure.

## <a name="next-steps"></a>Następne kroki

[Przykładowy interfejs wiersza polecenia dla maszyn wirtualnych platformy Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Przykłady interfejsu wiersza polecenia dla aplikacji sieci Web Azure](../../app-service/app-service-cli-samples.md)

[Przykłady interfejsu wiersza polecenia dla bazy danych SQL Azure](../../sql-database/sql-database-cli-samples.md)
