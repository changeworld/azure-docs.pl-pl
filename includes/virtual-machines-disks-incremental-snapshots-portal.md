---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/12/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 8f2e2081ee31e2ce7b21677bd261ed7eda705334
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79486118"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="regional-availability"></a>Dostępność regionalna
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

### <a name="restrictions"></a>Ograniczenia

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="portal"></a>Portal

Aby utworzyć i skonfigurować migawki przyrostowe w globalnej witrynie [https://aka.ms/incrementalsnapshots](https://aka.ms/incrementalsnapshots)Azure portal, należy użyć następującego łącza: . Tworzenie migawki przyrostowej nie jest jeszcze dostępne w globalnej witrynie Azure portal.

1. Zaloguj się do [witryny Azure portal](https://aka.ms/incrementalsnapshots) za pomocą podanego łącza i przejdź do dysku, który chcesz migać.
1. Na dysku wybierz pozycję **Utwórz migawkę**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-snapshot-button-incremental.png" alt-text="Zrzut ekranu. Blok dysku z podświetleną **+Create snapshot** , ponieważ należy to wybrać.":::

1. Wybierz grupę zasobów, której chcesz użyć, i wprowadź nazwę.
1. Wybierz **przyrostowe** i wybierz **pozycję Recenzja + Utwórz**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/incremental-snapshot-create-snapshot-blade.png" alt-text="Zrzut ekranu. Utwórz blok migawki, wypełnij nazwę i wybierz przyrostowe, a następnie utwórz migawkę.":::

1. Wybierz **pozycję Utwórz**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-incremental-snapshot-validation.png" alt-text="Zrzut ekranu. Strona sprawdzania poprawności migawki, potwierdź swoje wybory, a następnie utwórz migawkę.":::

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz zobaczyć przykładowy kod demonstrujący różnicowe możliwości migawek przyrostowych, za pomocą platformy .NET, zobacz [Kopiowanie kopii zapasowych dysków zarządzanych platformy Azure do innego regionu z możliwością różnicowania migawek przyrostowych](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots).
