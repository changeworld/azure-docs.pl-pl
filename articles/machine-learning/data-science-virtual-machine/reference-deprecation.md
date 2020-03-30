---
title: 'Odwołanie: Wycofanie obrazu maszyny wirtualnej do nauki o danych'
titleSuffix: Azure Data Science Virtual Machine
description: Szczegółowe informacje na temat deprecjonowania wpływających na maszynę wirtualną do nauki o danych platformy Azure
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 10/14/2019
ms.topic: reference
ms.openlocfilehash: 71ce8519dca6fa71340b1c34fe3a6891ccf5753f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77525791"
---
# <a name="reference-deprecation-of-dsvm-images"></a>Odwołanie: Wycofanie obrazów DSVM

Poniżej omówimy sugestie dotyczące radzenia sobie z nadchodzącymi deprecations na maszynie wirtualnej nauki o danych platformy Azure.

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012: Migrowanie dysków z danymi

31 grudnia 2019 r. przestaniemy obsługiwać obraz DSVM systemu Windows 2012. Aby przeprowadzić migrację dysku danych z istniejącego systemu Windows 2012 DSVM do systemu Windows 2016 DSVM, należy wykonać następujące kroki:

1. Utwórz nowy system Windows 2016 DSVM, postępując zgodnie z instrukcjami podanymi [tutaj](./provision-vm.md#create-your-dsvm).
1. Odłącz istniejące dyski danych z obrazu systemu Windows 2012, korzystając z [tych instrukcji](../../virtual-machines/windows/detach-disk.md).
1. Dołącz dysk z poprzedniego kroku do obrazu systemu Windows 2016, korzystając z [tych instrukcji](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm).
