---
title: Azure CLI próbki — usługi App Service | Dokumentacja firmy Microsoft
description: Przykłady Azure CLI - usługi aplikacji
services: app-service
documentationcenter: app-service
author: syntaxc4
manager: erikre
editor: ggailey777
tags: azure-service-management
ms.assetid: 53e6a15a-370a-48df-8618-c6737e26acec
ms.service: app-service
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 12/12/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: fdc5e03350783fb8c3e30b6c9a40af45a5925ba8
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2017
ms.locfileid: "26658367"
---
# <a name="azure-cli-samples"></a>Przykłady Azure CLI

Poniższa tabela zawiera linki do bash skrypty utworzone przy użyciu wiersza polecenia platformy Azure.

| | |
|-|-|
|**Tworzenie aplikacji**||
| [Tworzenie aplikacji sieci web i wdrażanie plików przy użyciu FTP](./scripts/app-service-cli-deploy-ftp.md?toc=%2fcli%2fazure%2ftoc.json)| Tworzenie aplikacji sieci web platformy Azure, a następnie wdraża pliku za pomocą protokołu FTP. |
| [Tworzenie aplikacji sieci Web i wdrażanie kodu z usługi GitHub](./scripts/app-service-cli-deploy-github.md?toc=%2fcli%2fazure%2ftoc.json)| Tworzenie aplikacji sieci web platformy Azure i wdraża kod z publicznego repozytorium GitHub. |
| [Tworzenie aplikacji sieci Web z ciągłym wdrażania z usługi GitHub](./scripts/app-service-cli-continuous-deployment-github.md?toc=%2fcli%2fazure%2ftoc.json)| Tworzy aplikację sieci web platformy Azure z publikowaniem ciągłego z repozytorium GitHub użytkownika. |
| [Tworzenie aplikacji sieci Web i wdrażanie kodu z lokalnego repozytorium Git](./scripts/app-service-cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json) | Tworzenie aplikacji sieci web platformy Azure i konfiguruje wypychania kodu z lokalnego repozytorium Git. |
| [Tworzenie aplikacji sieci Web i wdrażanie kodu w środowisku przejściowym](./scripts/app-service-cli-deploy-staging-environment.md?toc=%2fcli%2fazure%2ftoc.json) | Tworzy aplikację sieci web platformy Azure z miejscem wdrożenia, do przemieszczania zmian w kodzie. |
| [Tworzenie aplikacji sieci Web ASP.NET Core w kontenerze platformy Docker](./scripts/app-service-cli-linux-docker-aspnetcore.md?toc=%2fcli%2fazure%2ftoc.json)| Tworzenie aplikacji sieci web platformy Azure w systemie Linux i ładuje obraz Docker z Centrum Docker. |
|**Konfigurowanie aplikacji**||
| [Mapowanie domeny niestandardowej na aplikację sieci Web](./scripts/app-service-cli-configure-custom-domain.md?toc=%2fcli%2fazure%2ftoc.json)| Tworzenie aplikacji sieci web platformy Azure i mapuje niestandardowej nazwy domeny. |
| [Wiązania niestandardowego certyfikatu SSL w aplikacji sieci web](./scripts/app-service-cli-configure-ssl-certificate.md?toc=%2fcli%2fazure%2ftoc.json)| Tworzenie aplikacji sieci web platformy Azure i wiąże certyfikatu SSL z niestandardowej nazwy domeny. |
|**Skalowanie aplikacji**||
| [Ręczne skalowanie aplikacji sieci Web](./scripts/app-service-cli-scale-manual.md?toc=%2fcli%2fazure%2ftoc.json) | Tworzenie aplikacji sieci web platformy Azure i skaluje go w wystąpieniach 2. |
| [Skalowanie aplikacji sieci Web na całym świecie przy użyciu architektury wysokiej dostępności](./scripts/app-service-cli-scale-high-availability.md?toc=%2fcli%2fazure%2ftoc.json) | Tworzy dwie aplikacje sieci web platformy Azure w dwóch różnych regionach geograficznych i udostępnia je poprzez jeden punkt końcowy za pomocą usługi Azure Traffic Manager. |
|**Łączenie aplikacji z zasobów**||
| [Łączenie aplikacji sieci web z bazy danych SQL](./scripts/app-service-cli-app-service-sql.md?toc=%2fcli%2fazure%2ftoc.json)| Tworzy aplikację sieci web platformy Azure i bazy danych SQL, a następnie dodaje parametry połączenia bazy danych do ustawień aplikacji. |
| [Łączenie aplikacji sieci Web z kontem magazynu](./scripts/app-service-cli-app-service-storage.md?toc=%2fcli%2fazure%2ftoc.json)| Tworzy aplikację sieci web platformy Azure i konto magazynu, a następnie dodaje parametry połączenia magazynu do ustawień aplikacji. |
| [Łączenie aplikacji sieci web z pamięci podręcznej redis](./scripts/app-service-cli-app-service-redis.md?toc=%2fcli%2fazure%2ftoc.json) | Tworzy aplikację sieci web platformy Azure i pamięci podręcznej redis, a następnie dodaje szczegóły połączenia redis do ustawień aplikacji.) |
| [Łączenie aplikacji sieci web do bazy danych rozwiązania Cosmos](./scripts/app-service-cli-app-service-documentdb.md?toc=%2fcli%2fazure%2ftoc.json) | Tworzy aplikację sieci web platformy Azure i DB rozwiązania Cosmos, a następnie dodaje informacje dotyczące połączenia rozwiązania Cosmos DB ustawień aplikacji. |
|**Kopie zapasowe i przywracanie aplikacji**||
| [Tworzenie kopii zapasowej aplikacji sieci web](./scripts/app-service-cli-backup-onetime.md?toc=%2fcli%2fazure%2ftoc.json) | Tworzy aplikację sieci web platformy Azure oraz jednorazowej kopii zapasowej dla niego. |
| [Tworzenie zaplanowanego tworzenia kopii zapasowej dla aplikacji sieci web](./scripts/app-service-cli-backup-scheduled.md?toc=%2fcli%2fazure%2ftoc.json) | Tworzy aplikację sieci web platformy Azure oraz zaplanowanego tworzenia kopii zapasowej dla niego. |
| [Przywrócenie z kopii zapasowej aplikacji sieci web](./scripts/app-service-cli-backup-restore.md?toc=%2fcli%2fazure%2ftoc.json) | Przywraca aplikację sieci web platformy Azure z kopii zapasowej. |
|**Monitoruj aplikację**||
| [Monitorowanie aplikacji sieci Web za pomocą dzienników serwera sieci Web](./scripts/app-service-cli-monitor.md?toc=%2fcli%2fazure%2ftoc.json) | Tworzenie aplikacji sieci web platformy Azure, włączenie rejestrowania dla niego i pobiera dzienniki na komputerze lokalnym. |
| | |