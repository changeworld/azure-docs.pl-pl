---
title: Obsługiwane wersje klastrów w usłudze Azure Service Fabric | Dokumentacja firmy Microsoft
description: Więcej informacji na temat wersje klastrów w usłudze Azure Service Fabric.
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
ms.openlocfilehash: 75e95737eecb9407a80103d1cad00d4987fe7091
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60716085"
---
# <a name="supported-service-fabric-versions"></a>Obsługiwane wersje usługi Service Fabric

Upewnij się, że klaster jest zawsze uruchomiona obsługiwana wersja usługi Azure Service Fabric. Kończy się co najmniej 60 dni, po możemy ogłaszamy wydanie nowej wersji usługi Service Fabric, pomocy technicznej dla wcześniejszych wersji. Znajdziesz anonsów o nowych wersjach na [blog zespołu usługi Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/).

Można znaleźć w następujących dokumentach, aby uzyskać szczegółowe informacje na temat sposobów zabezpieczania klastra z obsługiwaną wersją usługi Service Fabric:

- [Uaktualnianie klastra usługi Azure Service Fabric](service-fabric-cluster-upgrade.md)
- [Uaktualnij wersję usługi Service Fabric, która działa w klastrze systemu Windows Server autonomiczny](service-fabric-cluster-upgrade-windows-server.md)

## <a name="supported-versions"></a>Obsługiwane wersje

W poniższej tabeli wymieniono wersje usługi Service Fabric i ich daty zakończenia wsparcia.

| Środowisko uruchomieniowe usługi Service Fabric w klastrze | Można uaktualnić bezpośrednio z wersji klastra |Zgodnej wersji pakietu SDK lub NuGet | Zakończenie obsługi |
| --- | --- |--- | --- |
| Wszystkie wersje klastra przed 5.3.121 | 5.1.158.* |Mniejsze niż wersja 2.3 |20 stycznia 2017 r. |
| 5.3.* | 5.1.158.* |Mniejsze niż wersja 2.3 |24 lutego 2017 r. |
| 5.4.* | 5.1.158.* |Mniejsza lub równa wersji 2.4 |10 maja 2017       |
| 5.5.* | 5.4.164.* |Mniejsze niż wersja 2.5 |Sierpień 10,2017    |
| 5.6.* | 5.4.164.* |Mniejsze niż w wersji 2.6 |Październik 13,2017   |
| 5.7.* | 5.4.164.* |Mniejsze niż w wersji 2.7 |15 grudnia 2017 r.  |
| 6.0.* | 5.6.205.* |Mniejsze niż w wersji 2.8 |30 marca 2018 r.     |
| 6.1.* | 5.7.221.* |Mniejsze niż w wersji 3.0 |15 lipca 2018 r.      |
| 6.2.* | 6.0.232.* |Mniejsze niż wersja 3.1 |26 października 2018 r.   |
| 6.3.* | 6.1.480.* |Mniejsze niż w wersji 3.2 |Do 31 marca 2019 r.  |
| 6.4.* | 6.2.301.* |Mniejsze niż w wersji 3.3 |Bieżąca wersja, dlatego bez daty zakończenia |

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

W poniższej tabeli wymieniono obsługiwane systemy operacyjne obsługiwane wersje usługi Service Fabric.

| System operacyjny | Najwcześniejsza obsługiwana wersja usługi Service Fabric |
| --- | --- |
| Windows Server 2012 R2 | Wszystkie wersje |
| Windows Server 2016 | Wszystkie wersje |
| Windows Server 1709 | 6.0 |
| System Windows Server w wersji 1803 | 6.4 |
| Windows Server 1809 | 6.4.654.9590 |
| Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16.04 | 6.0 |

