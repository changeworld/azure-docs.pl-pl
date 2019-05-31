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
ms.date: 05/24/2019
ms.author: aljo
ms.openlocfilehash: 606b14fba093b6ec8039c646a49bc3bf7d24eb51
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2019
ms.locfileid: "66296783"
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

## <a name="supported-version-names"></a>Obsługiwana wersja nazwy

W poniższej tabeli wymieniono nazwy wersji usługi Service Fabric oraz ich odpowiednich numerów wersji.

| Nazwa wersji | Numer wersji Windows | Numer wersji systemu Linux |
| --- | --- | --- |
| 5.3 CEL CZASU ODZYSKIWANIA | 5.3.121.9494 | Nie dotyczy |
| 5.3 PAKIETU CU1 | 5.3.204.9494 | Nie dotyczy |
| 5.3 PAKIETU CU2 | 5.3.301.9590 | Nie dotyczy |
| 5.3 CU3 | 5.3.311.9590 | Nie dotyczy |
| 5.4 PAKIETU CU2 | 5.4.164.9494 | Nie dotyczy |
| 5.5 PAKIETU CU1 | 5.5.216.0    | Nie dotyczy |
| 5.5 PAKIETU CU2 | 5.5.219.0    | Nie dotyczy |
| WERSJI 5.5 CU3 W | 5.5.227.0    | Nie dotyczy |
| 5.5 CU4 | 5.5.232.0    | Nie dotyczy |
| 5.6 CEL CZASU ODZYSKIWANIA | 5.6.204.9494 | Nie dotyczy |
| 5.6 PAKIETU CU2 | 5.6.210.9494 | Nie dotyczy |
| 5.6 CU3 | 5.6.220.9494 | Nie dotyczy |
| W WERSJI 5.7 CEL CZASU ODZYSKIWANIA | 5.7.198.9494 | Nie dotyczy |
| W WERSJI 5.7 CU4 | 5.7.221.9494 | Nie dotyczy |
| 6.0 CEL CZASU ODZYSKIWANIA | 6.0.211.9494 | 6.0.120.1 |
| 6.0 PAKIETU CU1 | 6.0.219.9494 | 6.0.127.1 |
| 6.0 PAKIETU CU2 | 6.0.232.9494 | 6.0.133.1 |
| 6.1 PAKIETU CU1 | 6.1.456.9494 | 6.1.183.1 |
| 6.1 PAKIETU CU2 | 6.1.467.9494 | 6.1.185.1 |
| 6.1 CU3 | 6.1.472.9494 | Nie dotyczy |
| 6.1 CU4 | 6.1.480.9494 | 6.1.187.1 |
| 6.2 CEL CZASU ODZYSKIWANIA | 6.2.269.9494 | 6.2.184.1 | 
| 6.2 PAKIETU CU1 | 6.2.274.9494 | 6.2.191.1 |
| 6.2 PAKIETU CU2 | 6.2.283.9494 | 6.2.194.1 |
| 6.2 CU3 | 6.2.301.9494 | 6.2.199.1 |
| 6.3 CEL CZASU ODZYSKIWANIA | 6.3.162.9494 | 6.3.119.1 |
| 6.3 PAKIETU CU1 | 6.3.176.9494 | 6.3.124.1 |
| 6.3 PAKIETU CU1 | 6.3.187.9494 | 6.3.129.1 |
| 6.4 CEL CZASU ODZYSKIWANIA | 6.4.617.9590 | 6.4.625.1 |
| 6.4 PAKIETU CU2 | 6.4.622.9590 | Nie dotyczy |
| 6.4 CU3 | 6.4.637.9590 | 6.4.634.1 |
| 6.4 CU4 | 6.4.644.9590 | 6.4.639.1 |
| 6.4 CU5 | 6.4.654.9590 | 6.4.649.1 |
| 6.4 CU6 | 6.4.658.9590 | Nie dotyczy |
| 6.4 CU7 | 6.4.664.9590 | 6.4.661.1 |