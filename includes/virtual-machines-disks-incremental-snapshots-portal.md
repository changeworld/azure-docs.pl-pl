---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/02/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: de0cf260207747f4acb02a377819a13de8b8ba22
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80628415"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="regional-availability"></a>Dostępność regionalna
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

### <a name="restrictions"></a>Ograniczenia

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="portal"></a>Portal


1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) i przejdź do dysku, który chcesz migać.
1. Na dysku wybierz pozycję **Utwórz migawkę**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-snapshot-button-incremental.png" alt-text="Zrzut ekranu. Blok dysku z podświetleną **+Create snapshot** , ponieważ należy to wybrać.":::

1. Wybierz grupę zasobów, której chcesz użyć, i wprowadź nazwę.
1. Wybierz **przyrostowe** i wybierz **pozycję Recenzja + Utwórz**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/incremental-snapshot-create-snapshot-blade.png" alt-text="Zrzut ekranu. Utwórz blok migawki, wypełnij nazwę i wybierz przyrostowe, a następnie utwórz migawkę.":::

1. Wybierz **pozycję Utwórz**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-incremental-snapshot-validation.png" alt-text="Zrzut ekranu. Strona sprawdzania poprawności migawki, potwierdź swoje wybory, a następnie utwórz migawkę.":::

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz zobaczyć przykładowy kod demonstrujący różnicowe możliwości migawek przyrostowych, za pomocą platformy .NET, zobacz [Kopiowanie kopii zapasowych dysków zarządzanych platformy Azure do innego regionu z możliwością różnicowania migawek przyrostowych](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots).
