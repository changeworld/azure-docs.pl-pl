---
title: Dowiedz się więcej o wersjach klastra usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: Obsługiwane wersje klastra usługi Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 9/24/2018
ms.author: aljo
ms.openlocfilehash: d99000e1682b662f4bceb28096395243c894282f
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2019
ms.locfileid: "59681611"
---
# <a name="supported-service-fabric-versions"></a>Obsługiwane wersje usługi Service Fabric

Upewnij się, że klaster zawsze działa obsługiwana wersja usługi Service Fabric. Gdy firma Microsoft ogłaszamy wydanie nowej wersji usługi Service Fabric, poprzednia wersja jest oznaczony do zakończenia wsparcia po co najmniej 60 dni od tego dnia. Nowe wersje są anonsowane [na blogu zespołu usługi Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/).

Można znaleźć w następujących dokumentach na szczegółowe informacje na temat sposobów zabezpieczania klastra z obsługiwaną wersją usługi Service Fabric.

- [Uaktualnij wersję usługi Service Fabric w klastrze platformy Azure](service-fabric-cluster-upgrade.md)
- [Uaktualnij wersję usługi Service Fabric w klastrze serwerów autonomicznych systemu windows](service-fabric-cluster-upgrade-windows-server.md)

## <a name="supported-versions"></a>Obsługiwane wersje

W poniższej tabeli wymieniono wersje usługi Service Fabric, które są obsługiwane i ich daty zakończenia wsparcia.

| **Środowisko uruchomieniowe usługi Service Fabric w klastrze** | **Można uaktualnić bezpośrednio z wersji klastra** |**Zgodne SDK / wersji pakietu NuGet** | **Obsługa Data zakończenia** |
| --- | --- |--- | --- |
| Wszystkie wersje klastra przed 5.3.121 | 5.1.158* |Mniejsze niż wersja 2.3 |20 stycznia 2017 r. |
| 5.3.* | 5.1.158.* |Mniejsze niż wersja 2.3 |24 lutego 2017 r. |
| 5.4.* | 5.1.158.* |Mniejsza lub równa wersji 2.4 |Może być 10,2017       |
| 5.5.* | 5.4.164.* |Mniejsze niż wersja 2.5 |Sierpień 10,2017    |
| 5.6.* | 5.4.164.* |Mniejsze niż w wersji 2.6 |Październik 13,2017   |
| 5.7.* | 5.4.164.* |Mniejsze niż w wersji 2.7 |Grudzień 15,2017  |
| 6.0.* | 5.6.205.* |Mniejsze niż w wersji 2.8 |Marzec 30,2018     |
| 6.1.* | 5.7.221.* |Mniejsze niż w wersji 3.0 |Lipiec 15,2018      |
| 6.2.* | 6.0.232.* |Mniejsze niż wersja 3.1 |Październik 26,2018   |
| 6.3.* | 6.1.480.* |Mniejsze niż w wersji 3.2 |Marzec 31,2019  |
| 6.4.* | 6.2.301.* |Mniejsze niż w wersji 3.3 |Bieżąca wersja i dlatego bez daty zakończenia |

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

 W poniższej tabeli wymieniono obsługiwane systemy operacyjne obsługiwane wersje usługi Service Fabric.

| **System operacyjny** | **Najstarszą wersję programu obsługiwanej usługi Service Fabric** |
| --- | --- |
| Windows Server 2012 R2 | Wszystkie wersje |
| Windows Server 2016 | Wszystkie wersje |
| Windows Server 1709 | 6.0 |
| System Windows Server w wersji 1803 | 6.4 |
| Windows Server 1809 | 6.4.654.9590 |
| Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16.04 | 6.0 |

