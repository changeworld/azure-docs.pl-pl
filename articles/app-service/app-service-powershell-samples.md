---
title: Przykłady programu Azure PowerShell - usługi aplikacji | Dokumentacja firmy Microsoft
description: Przykłady programu Azure PowerShell - usługi aplikacji
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
ms.openlocfilehash: ba2bd2b185c395e54f2f085317a424a2aa1b4421
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2018
ms.locfileid: "27713420"
---
# <a name="azure-powershell-samples"></a>Przykłady programu Azure PowerShell

Poniższa tabela zawiera linki do skryptów programu PowerShell utworzone przy użyciu programu Azure PowerShell.

| | |
|-|-|
|**Tworzenie aplikacji**||
| [Tworzenie aplikacji sieci web z wdrożeniem z usługi GitHub](./scripts/app-service-powershell-deploy-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tworzy aplikację sieci web platformy Azure, która pobiera kod z usługi GitHub. |
| [Tworzenie aplikacji sieci Web z ciągłym wdrażania z usługi GitHub](./scripts/app-service-powershell-continuous-deployment-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tworzy aplikację sieci web platformy Azure, która wdraża stale kod z usługi GitHub. |
| [Tworzenie aplikacji sieci web i wdrażanie kodu przy użyciu FTP](./scripts/app-service-powershell-deploy-ftp.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tworzy Azure web app i przekazać pliki z katalogiem lokalnym za pomocą protokołu FTP. |
| [Tworzenie aplikacji sieci Web i wdrażanie kodu z lokalnego repozytorium Git](./scripts/app-service-powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tworzenie aplikacji sieci web platformy Azure i konfiguruje wypychania kodu z lokalnego repozytorium Git. |
| [Tworzenie aplikacji sieci Web i wdrażanie kodu w środowisku przejściowym](./scripts/app-service-powershell-deploy-staging-environment.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tworzy aplikację sieci web platformy Azure z miejscem wdrożenia, do przemieszczania zmian w kodzie. |
|**Konfigurowanie aplikacji**||
| [Mapowanie domeny niestandardowej na aplikację sieci Web](./scripts/app-service-powershell-configure-custom-domain.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tworzenie aplikacji sieci web platformy Azure i mapuje niestandardowej nazwy domeny. |
| [Wiązania niestandardowego certyfikatu SSL w aplikacji sieci web](./scripts/app-service-powershell-configure-ssl-certificate.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tworzenie aplikacji sieci web platformy Azure i wiąże certyfikatu SSL z niestandardowej nazwy domeny. |
|**Skalowanie aplikacji**||
| [Ręczne skalowanie aplikacji sieci Web](./scripts/app-service-powershell-scale-manual.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tworzenie aplikacji sieci web platformy Azure i skaluje go w wystąpieniach 2. |
| [Skalowanie aplikacji sieci Web na całym świecie przy użyciu architektury wysokiej dostępności](./scripts/app-service-powershell-scale-high-availability.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tworzy dwie aplikacje sieci web platformy Azure w dwóch różnych regionach geograficznych i udostępnia je poprzez jeden punkt końcowy za pomocą usługi Azure Traffic Manager. |
|**Łączenie aplikacji z zasobów**||
| [Łączenie aplikacji sieci web z bazy danych SQL](./scripts/app-service-powershell-connect-to-sql.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tworzy aplikację sieci web platformy Azure i bazy danych SQL, a następnie dodaje parametry połączenia bazy danych do ustawień aplikacji. |
| [Łączenie aplikacji sieci Web z kontem magazynu](./scripts/app-service-powershell-connect-to-storage.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tworzy aplikację sieci web platformy Azure i konto magazynu, a następnie dodaje parametry połączenia magazynu do ustawień aplikacji. |
|**Kopie zapasowe i przywracanie aplikacji**||
| [Tworzenie kopii zapasowej aplikacji sieci web](./scripts/app-service-powershell-backup-onetime.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tworzy aplikację sieci web platformy Azure oraz jednorazowej kopii zapasowej dla niego. |
| [Tworzenie zaplanowanego tworzenia kopii zapasowej dla aplikacji sieci web](./scripts/app-service-powershell-backup-scheduled.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tworzy aplikację sieci web platformy Azure oraz zaplanowanego tworzenia kopii zapasowej dla niego. |
| [Usuwanie kopii zapasowej dla aplikacji sieci web](./scripts/app-service-powershell-backup-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Usuwa istniejącej kopii zapasowej dla aplikacji sieci web. |
|**Monitoruj aplikację**||
| [Monitorowanie aplikacji sieci Web za pomocą dzienników serwera sieci Web](./scripts/app-service-powershell-monitor.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tworzenie aplikacji sieci web platformy Azure, włączenie rejestrowania dla niego i pobiera dzienniki na komputerze lokalnym. |
| | |
