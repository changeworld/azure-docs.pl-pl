---
title: Przykłady dla programu Azure PowerShell — App Service | Microsoft Docs
description: Przykłady dla programu Azure PowerShell — App Service
services: app-service
documentationcenter: app-service
author: syntaxc4
manager: erikre
editor: ggailey777
tags: azure-service-management
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.service: app-service
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 03/08/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 211284f4eff112b9aebec7fa1a031f292a4a92f4
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2018
ms.locfileid: "52290743"
---
# <a name="azure-powershell-samples"></a>Przykłady dla programu Azure PowerShell

Poniższa tabela zawiera linki do skryptów programu PowerShell skompilowanych za pomocą programu Azure PowerShell.

| | |
|-|-|
|**Tworzenie aplikacji**||
| [Tworzenie aplikacji wraz wdrożeniem z usługi GitHub](./scripts/app-service-powershell-deploy-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tworzy aplikację internetową platformy Azure, która ściąga kod z usługi GitHub. |
| [Tworzenie aplikacji z ciągłym wdrażaniem z usługi GitHub](./scripts/app-service-powershell-continuous-deployment-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tworzy aplikację internetową platformy Azure, która w sposób ciągły wdraża kod z usługi GitHub. |
| [Tworzenie aplikacji i wdrażanie kodu za pomocą protokołu FTP](./scripts/app-service-powershell-deploy-ftp.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tworzy aplikację internetową platformy Azure i przekazuje pliki z katalogu lokalnego przy użyciu protokołu FTP. |
| [Tworzenie aplikacji i wdrażanie kodu z lokalnego repozytorium Git](./scripts/app-service-powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tworzy aplikację internetową platformy Azure i konfiguruje wypychanie kodu z lokalnego repozytorium Git. |
| [Tworzenie aplikacji i wdrażanie kodu w środowisku przejściowym](./scripts/app-service-powershell-deploy-staging-environment.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tworzy aplikację internetową platformy Azure z miejscem wdrożenia zmian kodu w środowisku przejściowym. |
|**Konfigurowanie aplikacji**||
| [Mapowanie domeny niestandardowej na aplikację](./scripts/app-service-powershell-configure-custom-domain.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tworzy aplikację internetową platformy Azure i mapuje na nią nazwę domeny niestandardowej. |
| [Tworzenie powiązania niestandardowego certyfikatu SSL z aplikacją](./scripts/app-service-powershell-configure-ssl-certificate.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tworzy aplikację internetową platformy Azure, następnie tworzy powiązanie certyfikatu SSL nazwy domeny niestandardowej z tą aplikacją. |
|**Skalowanie aplikacji**||
| [Ręczne skalowanie aplikacji](./scripts/app-service-powershell-scale-manual.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tworzy aplikację internetową platformy Azure i skaluje ją na 2 wystąpienia. |
| [Skalowanie aplikacji na całym świecie przy użyciu architektury wysokiej dostępności](./scripts/app-service-powershell-scale-high-availability.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tworzy dwie aplikacje internetowe platformy Azure w dwóch różnych regionach geograficznych i udostępnia je za pośrednictwem pojedynczego punktu końcowego przy użyciu usługi Azure Traffic Manager. |
|**Łączenie aplikacji z zasobami**||
| [Łączenie aplikacji z programem SQL Database](./scripts/app-service-powershell-connect-to-sql.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tworzy aplikację internetową platformy Azure i bazę danych SQL, a następnie dodaje parametry połączenia bazy danych do ustawień aplikacji. |
| [Łączenie aplikacji z kontem magazynu](./scripts/app-service-powershell-connect-to-storage.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tworzy aplikację internetową platformy Azure i konto magazynu, a następnie dodaje parametry połączenia magazynu do ustawień aplikacji. |
|**Tworzenie i przywracanie kopii zapasowej aplikacji**||
| [Tworzenie kopii zapasowej aplikacji](./scripts/app-service-powershell-backup-onetime.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tworzy aplikację internetową platformy Azure oraz jej jednorazową kopię zapasową. |
| [Tworzenie zaplanowanej kopii zapasowej aplikacji](./scripts/app-service-powershell-backup-scheduled.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tworzy aplikację internetową platformy Azure oraz jej zaplanowaną kopię zapasową. |
| [Usuwanie kopii zapasowej aplikacji](./scripts/app-service-powershell-backup-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Usuwa istniejącą kopię zapasową aplikacji. |
| [Przywracanie aplikacji z kopii zapasowej](./scripts/app-service-powershell-backup-restore.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Przywraca aplikację z utworzonej wcześniej kopii zapasowej. |
| [Przywracanie kopii zapasowej między subskrypcjami](./scripts/app-service-powershell-backup-restore-diff-sub.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Przywraca aplikację internetową z kopii zapasowej w innej subskrypcji. |
|**Monitorowanie aplikacji**||
| [Monitorowanie aplikacji za pomocą dzienników serwera internetowego](./scripts/app-service-powershell-monitor.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tworzy aplikację internetową platformy Azure, włącza dla niej rejestrowanie i pobiera dzienniki na komputer lokalny. |
| | |
