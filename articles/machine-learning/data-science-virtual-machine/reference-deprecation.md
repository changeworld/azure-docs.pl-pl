---
title: 'Odwołanie: przestarzałe obraz DSVM'
description: Szczegóły dotyczące zaniechania wpływu na Data Science Virtual Machine platformy Azure (DSVM)
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 10/14/2019
ms.topic: reference
ms.openlocfilehash: 4968ac08b75141ed84994ca287215a34728232c4
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333341"
---
# <a name="reference-deprecation-of-dsvm-images"></a>Odwołanie: zaniechanie obrazów DSVM

Poniżej omówiono sugestie dotyczące sposobu postępowania z nadchodzącymi zastosowaniami w usłudze Azure Data Science Virtual Machine.

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012: Migrowanie dysków danych

Zatrzymamy obraz DSVM systemu Windows 2012 w dniu 5 listopada 2019. Aby przeprowadzić migrację dysku danych z istniejącego systemu Windows 2012 DSVM do systemu Windows 2016 DSVM, wykonaj następujące czynności:

1. Utwórz nowy system Windows 2016 DSVM, postępując zgodnie z instrukcjami przedstawionymi [tutaj](./provision-vm.md#create-your-dsvm).
1. Odłącz istniejące dyski danych z obrazu systemu Windows 2012, korzystając z [tych instrukcji](../../virtual-machines/windows/detach-disk.md).
1. Dołącz dysk z poprzedniego kroku do obrazu systemu Windows 2016, korzystając z [tych instrukcji](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm).
