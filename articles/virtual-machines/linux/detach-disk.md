---
title: Odłączyć dysku danych maszyny wirtualnej systemu Linux - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się odłączyć dysku danych z maszyny wirtualnej na platformie Azure przy użyciu interfejsu wiersza polecenia w wersji 2.0 lub portalu Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.topic: article
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: 572fe5bd4d6d79bb9dd94353732e273282e2a0af
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
ms.locfileid: "30903687"
---
# <a name="how-to-detach-a-data-disk-from-a-linux-virtual-machine"></a>Jak można odłączyć dysku danych od maszyny wirtualnej systemu Linux

Gdy już nie potrzebujesz dysku danych dołączonego do maszyny wirtualnej, możesz go łatwo odłączyć. Usuwa dysk od maszyny wirtualnej, ale nie powoduje usunięcia go z magazynu. 

> [!WARNING]
> Jeśli możesz odłączyć dysk, który nie jest automatycznie usuwana. Jeśli masz subskrypcję do magazyn w warstwie Premium, będą nadal naliczane opłaty za magazyn dla dysku. Więcej informacji można znaleźć w [cennik i rozliczenia w przypadku korzystania z magazyn w warstwie Premium](../windows/premium-storage.md#pricing-and-billing). 
> 
> 

Jeśli chcesz użyć danych znajdujących się na tym dysku, możesz dołączyć go ponownie do tej samej lub innej maszyny wirtualnej.  

## <a name="detach-a-data-disk-using-cli-20"></a>Odłączyć dysku danych przy użyciu interfejsu wiersza polecenia 2.0

```azurecli
az vm disk detach \
    -g myResourceGroup \
    --vm-name myVm \
    -n myDataDisk
```

Odłączony dysk pozostaje w magazynie, lecz nie jest już dołączony do maszyny wirtualnej.


## <a name="detach-a-data-disk-using-the-portal"></a>Odłączanie dysku danych przy użyciu portalu
1. W menu po lewej stronie wybierz **maszyn wirtualnych**.
2. Wybierz maszynę wirtualną, która ma dysk danych, aby odłączyć, a następnie kliknij przycisk **zatrzymać** można cofnąć alokacji maszyny Wirtualnej.
3. W okienku maszyny wirtualnej, wybierz **dysków**.
4. W górnej części **dysków** okienku wybierz **Edytuj**.
5. W **dysków** okienku po prawej dysku danych, który chcesz odłączyć, kliknij przycisk ![obraz przycisku Detach](./media/detach-disk/detach.png) odłączyć przycisku.
5. Po usunięciu dysk, kliknij przycisk Zapisz w górnej części okienka.
6. W okienku maszyny wirtualnej, kliknij **omówienie** , a następnie kliknij przycisk **Start** przycisk w górnej części okienka, aby ponownie uruchomić maszyny Wirtualnej.

Odłączony dysk pozostaje w magazynie, lecz nie jest już dołączony do maszyny wirtualnej.


## <a name="next-steps"></a>Kolejne kroki
Jeśli chcesz ponownie użyć dysku danych, możesz po prostu [dołączenie go do innej maszyny Wirtualnej](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

