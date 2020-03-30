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
ms.openlocfilehash: 4d2c0a02a48c0e04b501f136f66c28b3f532e2a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79485933"
---
Migawki przyrostowe są kopie zapasowe punktu w czasie dla dysków zarządzanych, które po wykonaniu składają się tylko ze wszystkich zmian od ostatniej migawki. Podczas próby pobrania lub w inny sposób użyć migawki przyrostowej, jest używany pełny dysk wirtualny. Ta nowa funkcja dla migawek dysku zarządzanego potencjalnie pozwala im być bardziej opłacalne, ponieważ, chyba że zdecydujesz się, nie trzeba przechowywać cały dysk z każdej migawki poszczególnych. Podobnie jak zwykłe migawki, przyrostowe migawki mogą służyć do tworzenia pełnego dysku zarządzanego lub do tworzenia regularnych migawek.

Istnieje kilka różnic między migawką przyrostową a zwykłą migawką. Migawki przyrostowe zawsze będą używać standardowej pamięci masowej dysków twardych, niezależnie od typu pamięci masowej dysku, podczas gdy zwykłe migawki mogą używać dysków SSD klasy premium. Jeśli do skalowania wdrożeń maszyn wirtualnych używane są zwykłe migawki w magazynie premium, zalecamy użycie obrazów niestandardowych w standardowej pamięci masowej w [Galerii obrazów udostępnionych.](../articles/virtual-machines/linux/shared-image-galleries.md) To pomoże Ci osiągnąć bardziej masową skalę przy niższych kosztach. Ponadto migawki przyrostowe potencjalnie oferują lepszą niezawodność dzięki [magazynowi nadmiarowego strefy](../articles/storage/common/storage-redundancy-zrs.md) (ZRS). Jeśli ZRS jest dostępny w wybranym regionie, migawka przyrostowa będzie automatycznie używać ZRS. Jeśli ZRS nie jest dostępny w regionie, migawka będzie [domyślnie lokalnie nadmiarowej pamięci masowej](../articles/storage/common/storage-redundancy-lrs.md) (LRS). Można zastąpić to zachowanie i wybrać jeden ręcznie, ale nie zaleca się tego.

Migawki przyrostowe oferują również różnicowe możliwości, dostępne tylko dla dysków zarządzanych. Umożliwiają one uzyskanie zmian między dwoma przyrostowymi migawkami tych samych dysków zarządzanych, aż do poziomu bloku. Za pomocą tej funkcji można zmniejszyć ilość danych podczas kopiowania migawek w różnych regionach.  Na przykład można pobrać pierwszą migawkę przyrostową jako podstawowy obiekt blob w innym regionie. Dla kolejnych migawek przyrostowych można skopiować tylko zmiany od ostatniej migawki do podstawowego obiektu blob. Po skopiowaniu zmian można wykonać migawki na podstawowym obiekcie blob, które reprezentują kopię zapasową punktu w czasie dysku w innym regionie. Dysk można przywrócić z podstawowego obiektu blob lub z migawki w podstawowym obiekcie blob w innym regionie.

:::image type="content" source="media/virtual-machines-disks-incremental-snapshots-description/incremental-snapshot-diagram.png" alt-text="Diagram przedstawiający przyrostowe migawki kopiowane między regionami. Migawki wykonać różne wywołania interfejsu API, aż ostatecznie tworząc obiekty blob strony na każdej migawki.":::