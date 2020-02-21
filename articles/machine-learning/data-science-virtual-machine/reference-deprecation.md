---
title: 'Odwołanie: zaniechanie obrazu Data Science Virtual Machine'
titleSuffix: Azure Data Science Virtual Machine
description: Szczegóły dotyczące zaniechania wpływu na Data Science Virtual Machine platformy Azure
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 10/14/2019
ms.topic: reference
ms.openlocfilehash: 71ce8519dca6fa71340b1c34fe3a6891ccf5753f
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2020
ms.locfileid: "77525791"
---
# <a name="reference-deprecation-of-dsvm-images"></a>Odwołanie: zaniechanie obrazów DSVM

Poniżej omówiono sugestie dotyczące sposobu postępowania z nadchodzącymi zastosowaniami w usłudze Azure Data Science Virtual Machine.

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012: Migrowanie dysków danych

Nie będziemy obsługiwać obrazu DSVM systemu Windows 2012 w dniu 31 grudnia 2019. Aby przeprowadzić migrację dysku danych z istniejącego systemu Windows 2012 DSVM do systemu Windows 2016 DSVM, wykonaj następujące czynności:

1. Utwórz nowy system Windows 2016 DSVM, postępując zgodnie z instrukcjami przedstawionymi [tutaj](./provision-vm.md#create-your-dsvm).
1. Odłącz istniejące dyski danych z obrazu systemu Windows 2012, korzystając z [tych instrukcji](../../virtual-machines/windows/detach-disk.md).
1. Dołącz dysk z poprzedniego kroku do obrazu systemu Windows 2016, korzystając z [tych instrukcji](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm).
