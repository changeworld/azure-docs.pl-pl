---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 1f094cd78e6708fec4bda1e5510379b11df14dcd
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299193"
---
Migawki przyrostowe są kopiami zapasowymi punktu w czasie dla dysków zarządzanych, które w razie potrzeby składają się tylko ze wszystkich zmian od ostatniej migawki. Podczas próby pobrania lub użycia migawki przyrostowej jest używany pełny dysk VHD. Ta nowa możliwość dla migawek dysków zarządzanych potencjalnie pozwala im zwiększyć koszty, ponieważ, o ile nie zostanie wybrana opcja, nie trzeba przechowywać całego dysku przy każdej pojedynczej migawce. Podobnie jak w przypadku zwykłych migawek, przyrostowe migawki mogą być używane do tworzenia pełnego dysku zarządzanego lub wykonywania regularnej migawki.

Istnieje kilka różnic między migawką przyrostową i regularną migawką. Migawki przyrostowe zawsze korzystają ze standardowego magazynu HDD, niezależnie od typu magazynu dysku, a regularne migawki mogą korzystać z dysków SSD w warstwie Premium. Jeśli używasz zwykłych migawek na Premium Storage do skalowania wdrożeń maszyn wirtualnych, zalecamy używanie obrazów niestandardowych w ramach magazynu w warstwie Standardowa w [galerii obrazów udostępnionych](../articles/virtual-machines/linux/shared-image-galleries.md). Pomożemy Ci w osiągnięciu bardziej ogromnej skali z niższym kosztem. Ponadto migawki przyrostowe potencjalnie oferują lepszą niezawodność za pomocą [magazynu Strefowo nadmiarowego](../articles/storage/common/storage-redundancy-zrs.md) (ZRS). Jeśli ZRS jest dostępny w wybranym regionie, migawka przyrostowa będzie używać ZRS automatycznie. Jeśli ZRS nie jest dostępny w regionie, migawka domyślnie będzie [magazynem lokalnie nadmiarowy](../articles/storage/common/storage-redundancy-lrs.md) (LRS). Można zastąpić to zachowanie i wybrać jeden z nich ręcznie, ale nie jest to zalecane.

Migawki przyrostowe oferują również funkcję różnicową, która jest dostępna tylko w przypadku dysków zarządzanych. Umożliwiają one uzyskanie zmian między dwiema przyrostowymi migawkami tych samych dysków zarządzanych, w dół do poziomu bloku. Można użyć tej funkcji, aby zmniejszyć rozmiary danych podczas kopiowania migawek między regionami.
