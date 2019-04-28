---
title: Renderowanie aplikacji — Azure Batch
description: Wstępnie zainstalowane aplikacje renderowania usługi Batch
services: batch
ms.service: batch
author: laurenhughes
ms.author: lahugh
ms.date: 03/26/2018
ms.topic: conceptual
ms.openlocfilehash: 84b2ca30f1ccd49e18e2f9d42133f8672d3f8ad6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776173"
---
# <a name="pre-installed-applications-on-rendering-vm-images"></a>Wstępnie zainstalowane aplikacje na renderowanie obrazów maszyn wirtualnych

Istnieje możliwość aplikacjami do renderowania za pomocą usługi Azure Batch. Obrazy maszyny Wirtualnej portalu Azure Marketplace są jednak dostępne z wstępnie zainstalowanymi aplikacjami wspólnej.

Gdy to stosowne, płatność za użycie licencji jest dostępna dla aplikacji wstępnie zainstalowanych renderowania. Podczas tworzenia puli usługi Batch można określić wymagane aplikacje i koszt maszyn wirtualnych i aplikacji, będą rozliczane za minutę. Ceny aplikacji są wyświetlane na [usługi Azure Batch stronę z cennikiem](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

Niektóre aplikacje obsługują tylko Windows, ale większość są obsługiwane w systemach Windows i Linux.

## <a name="applications-on-centos-7-rendering-images"></a>Aplikacje na CentOS 7 renderowanie obrazów

* Autodesk Maya I/O 2017 Update 5 (wersja 201708032230)
* Autodesk Maya operacji We/Wy 2018 Update 2 (Wytnij 201711281015)
* Autodesk Arnold for Maya 2017 (wersja Arnold 5.0.1.1) MtoA-2.0.1.1-2017
* Autodesk Arnold for Maya 2018 (wersja Arnold 5.0.1.4) MtoA-2.1.0.3-2018
* Chaos Group V-Ray for Maya 2017 (wersja 3.60.04)
* Chaos Group V-Ray for Maya 2018 (wersja 3.60.04)
* Blender (2.68)

## <a name="applications-on-latest-windows-server-2016-rendering-images"></a>Aplikacji na najnowszych systemu Windows Server 2016 renderowanie obrazów

Poniższa lista dotyczy systemu Windows Server 2016 w wersji 1.3.4 renderowanie obrazów.

* Autodesk Maya I/O 2017 Update 5 (wersja 17.4.5459)
* Autodesk Maya operacji We/Wy 2018 Update 4 (wersja 18.4.0.7622)
* Autodesk 3ds Max operacji We/Wy 2019 Update 1 (wersja 21.2.0.2219)
* Autodesk 3ds Max I/O 2018 Update 4 (wersja 20.4.0.4254)
* Autodesk Arnold for Maya 2017 (wersja 5.2.0.1 programu Arnold) MtoA-3.1.0.1-2017
* Autodesk Arnold for Maya 2018 (wersja 5.2.0.1 programu Arnold) MtoA-3.1.0.1-2018 r.
* Autodesk Arnold for 3ds Max (5.0.2.4)(version wersji Arnold 1.2.926)
* Chaos Group V-Ray for Maya 2018 (wersja 3.52.03)
* Chaos Group V-Ray dla aplikacji 3ds Max 2018 r. (wersja 3.60.02)
* Chaos Group V-Ray for Maya 2019 r (wersja 3.52.03)
* Chaos Group V-Ray dla aplikacji 3ds Max 2019 (wersja 4.10.01)
* Blender (2.79)

> [!NOTE]
> Chaos Group V-Ray dla aplikacji 3ds Max 2019 (wersja 4.10.01) wprowadza zmiany powodujące niezgodność do V-ray. Aby użyć poprzedniej wersji (wersja 3.60.02), należy użyć systemu Windows Server 2016 w wersji 1.3.2 renderowanie węzłów.

## <a name="applications-on-previous-windows-server-2016-rendering-images"></a>Aplikacje w poprzednim systemie Windows Server 2016 renderowanie obrazów

Poniższa lista dotyczy systemu Windows Server 2016 w wersji 1.3.2 renderowanie obrazów.

* Autodesk Maya I/O 2017 Update 5 (wersja 17.4.5459)
* Autodesk Maya operacji We/Wy 2018 Update 4 (wersja 18.4.0.7622)  
* Autodesk 3ds Max operacji We/Wy 2019 Update 1 (wersja 21.2.0.2219)
* Autodesk 3ds Max I/O 2018 Update 4 (wersja 20.4.0.4254)
* Autodesk Arnold for Maya 2017 (wersja 5.2.0.1 programu Arnold) MtoA-3.1.0.1-2017
* Autodesk Arnold for Maya 2018 (wersja 5.2.0.1 programu Arnold) MtoA-3.1.0.1-2018 r.
* Autodesk Arnold for 3ds Max (5.0.2.4)(version wersji Arnold 1.2.926)
* Chaos Group V-Ray for Maya 2019 r (wersja 3.52.03)
* Chaos Group V-Ray dla aplikacji 3ds Max 2018 r. (wersja 3.60.02)
* Blender (2.79)

## <a name="next-steps"></a>Kolejne kroki

Aby użyć renderowanie obrazów maszyn wirtualnych, muszą być określona w konfiguracji puli, gdy tworzona jest pula; zobacz [Batch możliwości puli w celu renderowania](https://docs.microsoft.com/azure/batch/batch-rendering-functionality#batch-pools).