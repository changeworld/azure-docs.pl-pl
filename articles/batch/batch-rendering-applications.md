---
title: Aplikacje renderowania — usługa Azure Batch
description: Istnieje możliwość użycia dowolnej aplikacji renderowania za pomocą usługi Azure Batch. Jednak obrazy maszyn wirtualnych w portalu Azure Marketplace są dostępne z fabrycznie zainstalowanymi typowymi aplikacjami.
services: batch
ms.service: batch
author: LauraBrenner
ms.author: labrenne
ms.date: 09/19/2019
ms.topic: conceptual
ms.openlocfilehash: 77672534b2aad993a44e9b637fbed58df8610e97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022991"
---
# <a name="pre-installed-applications-on-rendering-vm-images"></a>Wstępnie zainstalowane aplikacje na renderowaniu obrazów maszyn wirtualnych

Istnieje możliwość użycia dowolnej aplikacji renderowania za pomocą usługi Azure Batch. Jednak obrazy maszyn wirtualnych w portalu Azure Marketplace są dostępne z fabrycznie zainstalowanymi typowymi aplikacjami.

W stosownych przypadkach licencja pay-per-use jest dostępna dla wstępnie zainstalowanych aplikacji renderowania. Podczas tworzenia puli partii można określić wymagane aplikacje i zarówno koszt maszyny wirtualnej, jak i aplikacji będą rozliczane za minutę. Ceny aplikacji są podane na [stronie cennik azure batch](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

Niektóre aplikacje obsługują tylko system Windows, ale większość z nich jest obsługiwana zarówno w systemie Windows, jak i Linux.

## <a name="applications-on-centos-7-rendering-images"></a>Aplikacje na obrazach renderowania CentOS 7

Poniższa lista dotyczy obrazów renderowania CentOS 7.6 w wersji 1.1.6.

* Autodesk Maya I/O 2017 Update 5 (wersja 201708032230)
* Aktualizacja 2 I/O programu Autodesk Maya 2018 (wytład 201711281015)
* Aktualizacja Autodesk Maya I/O 2019 1
* Autodesk Arnold dla Maya 2017 (Arnold wersja 5.3.1.1) MtoA-3.2.1.1-2017
* Autodesk Arnold dla Maya 2018 (Arnold wersja 5.3.1.1) MtoA-3.2.1.1-2018
* Autodesk Arnold na Majów 2019 (Arnold wersja 5.3.1.1) MtoA-3.2.1.1-2019
* Chaos Group V-Ray for Maya 2017 (wersja 3.60.04)
* Chaos Group V-Ray for Maya 2018 (wersja 3.60.04)
* Blender (2.68)
* Blender (2.8)

## <a name="applications-on-latest-windows-server-2016-rendering-images"></a>Aplikacje dotyczące najnowszych obrazów renderowania systemu Windows Server 2016

Poniższa lista dotyczy obrazów renderowania systemu Windows Server 2016 w wersji 1.3.8.

* Autodesk Maya I/O 2017 Update 5 (wersja 17.4.5459)
* Autodesk Maya I/O 2018 Aktualizacja 6 (wersja 18.4.0.7622)
* Autodesk Maya I/O 2019
* Autodesk 3ds Max I/O 2018 Update 4 (wersja 20.4.0.4254)
* Aktualizacja 1 programu Autodesk 3ds Max I/O (wersja 21.2.0.2219)
* Aktualizacja Autodesk 3ds Max We/Wy 2020
* Autodesk Arnold dla Maya 2017 (Arnold wersja 5.3.0.2) MtoA-3.2.0.2-2017
* Autodesk Arnold dla Maya 2018 (Arnold wersja 5.3.0.2) MtoA-3.2.0.2-2018
* Autodesk Arnold na Majów 2019 (Arnold wersja 5.3.0.2) MtoA-3.2.0.2-2019
* Autodesk Arnold dla 3ds Max 2018 (Arnold w wersji 5.3.0.2) (wersja 1.2.926)
* Autodesk Arnold dla 3ds Max 2019 (Arnold w wersji 5.3.0.2) (wersja 1.2.926)
* Autodesk Arnold dla 3ds Max 2020 (Arnold w wersji 5.3.0.2) (wersja 1.2.926)
* Chaos Group V-Ray dla Maya 2017 (wersja 4.12.01)
* Chaos Group V-Ray dla Maya 2018 (wersja 4.12.01)
* Chaos Group V-Ray na Majów 2019 (wersja 4.04.03)
* Chaos Group V-Ray dla 3ds Max 2018 (wersja 4.20.01)
* Chaos Group V-Ray na 3ds Max 2019 (wersja 4.20.01)
* Chaos Group V-Ray na 3ds Max 2020 (wersja 4.20.01)
* Blender (2.79)
* Blender (2,80)
* AZ 10

> [!IMPORTANT]
> Aby uruchomić v-ray z Maya poza [szablonami rozszerzenia usługi Azure Batch](https://github.com/Azure/batch-extension-templates), uruchom `vrayses.exe` przed uruchomieniem renderowania. Aby uruchomić plik vrayses.exe poza szablonami, można `%MAYA_2017%\vray\bin\vrayses.exe"`użyć następującego polecenia .
>
> Na przykład zobacz zadanie [początkowe szablonu Maya i V-Ray](https://github.com/Azure/batch-extension-templates/blob/master/templates/maya/render-vray-windows/pool.template.json) w usłudze GitHub.

## <a name="applications-on-previous-windows-server-2016-rendering-images"></a>Aplikacje na poprzednich obrazach renderowania systemu Windows Server 2016

Poniższa lista dotyczy obrazów renderowania systemu Windows Server 2016 w wersji 1.3.7.

* Autodesk Maya I/O 2017 Update 5 (wersja 17.4.5459)
* Autodesk Maya I/O 2018 Aktualizacja 4 (wersja 18.4.0.7622)
* Aktualizacja 1 programu Autodesk 3ds Max I/O (wersja 21.2.0.2219)
* Autodesk 3ds Max I/O 2018 Update 4 (wersja 20.4.0.4254)
* Autodesk Arnold dla Maya 2017 (Arnold wersja 5.2.0.1) MtoA-3.1.0.1-2017
* Autodesk Arnold dla Maya 2018 (Arnold wersja 5.2.0.1) MtoA-3.1.0.1-2018
* Autodesk Arnold dla 3ds Max 2018 (Arnold w wersji 5.0.2.4) (wersja 1.2.926)
* Autodesk Arnold dla 3ds Max 2019 (Arnold w wersji 5.0.2.4) (wersja 1.2.926)
* Chaos Group V-Ray dla Maya 2018 (wersja 3.52.03)
* Chaos Group V-Ray dla 3ds Max 2018 (wersja 3.60.02)
* Chaos Group V-Ray na Majów 2019 (wersja 3.52.03)
* Chaos Group V-Ray na 3ds Max 2019 (wersja 4.10.01)
* Blender (2.79)

> [!NOTE]
> Chaos Group V-Ray dla 3ds Max 2019 (wersja 4.10.01) wprowadza przełomowe zmiany w V-ray. Aby użyć poprzedniej wersji (wersja 3.60.02), należy użyć systemu Windows Server 2016, węzłów renderowania w wersji 1.3.2.

## <a name="next-steps"></a>Następne kroki

Aby użyć obrazów maszyn wirtualnych renderowania, należy je określić w konfiguracji puli podczas tworzenia puli; zobacz [Możliwości puli partii do renderowania](https://docs.microsoft.com/azure/batch/batch-rendering-functionality#batch-pools).
