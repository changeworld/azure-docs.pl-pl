---
title: 'Odwołanie: przestarzałe obraz DSVM'
description: Szczegóły dotyczące zaniechania wpływu na Data Science Virtual Machine platformy Azure (DSVM)
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 10/14/2019
ms.topic: reference
ms.openlocfilehash: 252f9c66034dbadaf7a2e9e6f78665c26d414deb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456285"
---
# <a name="reference-deprecation-of-dsvm-images"></a>Odwołanie: zaniechanie obrazów DSVM

Poniżej omówiono sugestie dotyczące sposobu postępowania z nadchodzącymi zastosowaniami w usłudze Azure Data Science Virtual Machine.

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012: Migrowanie dysków danych

Nie będziemy obsługiwać obrazu DSVM systemu Windows 2012 w dniu 31 grudnia 2019. Aby przeprowadzić migrację dysku danych z istniejącego systemu Windows 2012 DSVM do systemu Windows 2016 DSVM, wykonaj następujące czynności:

1. Utwórz nowy system Windows 2016 DSVM, postępując zgodnie z instrukcjami przedstawionymi [tutaj](./provision-vm.md#create-your-dsvm).
1. Odłącz istniejące dyski danych z obrazu systemu Windows 2012, korzystając z [tych instrukcji](../../virtual-machines/windows/detach-disk.md).
1. Dołącz dysk z poprzedniego kroku do obrazu systemu Windows 2016, korzystając z [tych instrukcji](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm).
