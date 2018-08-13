---
title: Aplikacje renderowania usługi Batch
description: Wstępnie zainstalowane aplikacje renderowania usługi Batch
services: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 28acd1b7275694d38a52f14d2b2c32b79cc8183e
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2018
ms.locfileid: "40034831"
---
# <a name="pre-installed-applications-on-rendering-vm-images"></a>Wstępnie zainstalowane aplikacje na renderowanie obrazów maszyn wirtualnych

Istnieje możliwość aplikacjami do renderowania za pomocą usługi Azure Batch. Obrazy maszyny Wirtualnej portalu Azure Marketplace są jednak dostępne z wstępnie zainstalowanymi aplikacjami wspólnej.

Gdy to stosowne, płatność za użycie licencji jest dostępna dla aplikacji wstępnie zainstalowanych renderowania. Podczas tworzenia puli usługi Batch można określić wymagane aplikacje i koszt maszyn wirtualnych i aplikacji, będą rozliczane za minutę. Ceny aplikacji są wyświetlane na [usługi Azure Batch stronę z cennikiem](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

Niektóre aplikacje obsługują tylko Windows, ale większość są obsługiwane w systemach Windows i Linux.

## <a name="applications-on-centos-7-rendering-nodes"></a>Aplikacje w węzłach renderowania CentOS 7

* Autodesk Maya I/O 2017 Update 5 (wersja 201708032230)
* Autodesk Maya operacji We/Wy 2018 Update 2 (Wytnij 201711281015)
* Autodesk Arnold for Maya 2017 (wersja Arnold 5.0.1.1) MtoA-2.0.1.1-2017
* Autodesk Arnold for Maya 2018 (wersja Arnold 5.0.1.4) MtoA-2.1.0.3-2018
* Chaos Group V-Ray for Maya 2017 (wersja 3.60.04)
* Chaos Group V-Ray for Maya 2018 (wersja 3.60.04)
* Blender (2.68)

## <a name="applications-on-windows-server-2016-rendering-nodes"></a>Aplikacje w węzłach renderowania w systemie Windows Server 2016

* Autodesk Maya I/O 2017 Update 5 (wersja 17.4.5459)
* Autodesk Maya operacji We/Wy 2018 Update 3 (wersja 18.3.0.7040)  
* Autodesk 3ds Max operacji We/Wy 2019 Update 1 (wersja 21.10.1314)
* Autodesk 3ds Max I/O 2018 Update 4 (wersja 20.4.0.4254)
* Autodesk Arnold for Maya (wersja Arnold 5.0.1.1) MtoA-2.0.1.1-2017
* Autodesk Arnold for Maya (wersja Arnold 5.0.1.4) MtoA-2.0.2.3-2018
* Autodesk Arnold for 3ds Max (5.0.2.4)(version wersji Arnold 1.2.926)
* Chaos Group V-Ray for Maya (wersja 3.52.03)
* Chaos Group V-Ray for 3ds Max (wersja 3.60.02)
* Blender (2.79)

## <a name="next-steps"></a>Kolejne kroki

Aby użyć renderowanie obrazów maszyn wirtualnych, muszą być określona w konfiguracji puli, gdy tworzona jest pula; zobacz [Batch możliwości puli w celu renderowania](https://docs.microsoft.com/azure/batch/batch-rendering-functionality#batch-pools).