---
title: 'Odwołanie: Wycofanie obrazu maszyny wirtualnej do nauki o danych'
titleSuffix: Azure Data Science Virtual Machine
description: Szczegółowe informacje na temat deprecjonowania wpływających na maszynę wirtualną do nauki o danych platformy Azure
author: lobrien
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: laobri
ms.date: 04/03/2020
ms.topic: reference
ms.openlocfilehash: 6004ae0c1fd2fa25a07ab84776ab74789f6e7da9
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754772"
---
# <a name="reference-deprecation-of-dsvm-images"></a>Odwołanie: Wycofanie obrazów DSVM

Poniżej omówimy sugestie dotyczące radzenia sobie z nadchodzącymi deprecations na maszynie wirtualnej nauki o danych platformy Azure.

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012: Migrowanie dysków z danymi

31 grudnia 2019 r. przestaniemy obsługiwać obraz DSVM systemu Windows 2012. Aby przeprowadzić migrację dysku danych z istniejącego systemu Windows 2012 DSVM do systemu Windows 2016 DSVM, należy wykonać następujące kroki:

1. Utwórz nowy system Windows 2016 DSVM, postępując zgodnie z instrukcjami podanymi [tutaj](./provision-vm.md#create-your-dsvm).
1. Odłącz istniejące dyski danych z obrazu systemu Windows 2012, korzystając z [tych instrukcji](../../virtual-machines/windows/detach-disk.md).
1. Dołącz dysk z poprzedniego kroku do obrazu systemu Windows 2016, korzystając z [tych instrukcji](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm).

## <a name="centos"></a>CentOS

Nowi użytkownicy powinni korzystać z najnowszych obrazów Ubuntu lub Windows. CentOS pozostanie dostępny do użycia z istniejącymi szablonami rozwiązań.