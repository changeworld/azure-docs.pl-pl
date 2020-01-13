---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/10/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a799339f2780c2bc372c39120a6e20b34d907326
ms.sourcegitcommit: e9776e6574c0819296f28b43c9647aa749d1f5a6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/13/2020
ms.locfileid: "75912746"
---
### <a name="portal"></a>Portal

Skonfigurowanie kluczy zarządzanych przez klienta dla dysków będzie wymagało utworzenia zasobów w określonej kolejności, jeśli jest to wykonywane po raz pierwszy. Najpierw należy utworzyć i skonfigurować Azure Key Vault.

#### <a name="setting-up-your-azure-key-vault"></a>Konfigurowanie Azure Key Vault

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) i wyszukaj ciąg Key Vault
1. Wyszukaj i wybierz pozycję **magazyny kluczy**.

    [![SSE-Key-Vault-Portal-Search. png](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search.png)](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search-expanded.png#lightbox)

    > [!IMPORTANT]
    > Aby wdrożenie zakończyło się pomyślnie, w tym samym regionie i w ramach subskrypcji musi znajdować się Twój Magazyn kluczy platformy Azure, zestaw szyfrowania dysków, maszyna wirtualna, dyski i migawki.

1. Wybierz pozycję **+ Dodaj** , aby utworzyć nowy Key Vault.
1. Tworzenie nowej grupy zasobów
1. Wprowadź nazwę magazynu kluczy, wybierz region, a następnie wybierz warstwę cenową.
1. Wybierz pozycję **Recenzja + Utwórz**, sprawdź wybrane opcje, a następnie wybierz pozycję **Utwórz**.

    ![SSE-Create-a-Key-Vault. png](media/virtual-machines-disk-encryption-portal/sse-create-a-key-vault.png)

1. Po zakończeniu wdrażania magazynu kluczy wybierz go.
1. Wybierz pozycję **klucze** w obszarze **Ustawienia**.
1. Wybierz pozycję **Generuj/Importuj**

    ![SSE-Key-Vault-Generate-Settings. png](media/virtual-machines-disk-encryption-portal/sse-key-vault-generate-settings.png)

1. Pozostaw **Typ klucza** ustawiony na wartość **RSA** i **rozmiar klucza RSA** ustawiony na **2080**.
1. Wypełnij pozostałe wybrane opcje, a następnie wybierz pozycję **Utwórz**.

    ![SSE-Create-a-Key-Generate. png](media/virtual-machines-disk-encryption-portal/sse-create-a-key-generate.png)

#### <a name="setting-up-your-disk-encryption-set"></a>Konfigurowanie zestawu szyfrowania dysków

Aby utworzyć i skonfigurować zestawy szyfrowania dysków, należy użyć następującego linku: https://aka.ms/diskencryptionsets. Tworzenie zestawu szyfrowania dysków nie jest jeszcze dostępne w globalnym Azure Portal.

1. Otwórz [Link zestawy szyfrowania dysków](https://aka.ms/diskencryptionsets).
1. Wybierz pozycję **+ Dodaj**.

    ![SSE-Create-Disk-Encryption-Set. png](media/virtual-machines-disk-encryption-portal/sse-create-disk-encryption-set.png)

1. Wybierz grupę zasobów, Nazwij zestaw szyfrowania i wybierz ten sam region, w którym znajduje się Twój Magazyn kluczy.
1. Wybierz pozycję **Magazyn kluczy i klucz**.
1. Wybierz utworzony wcześniej Magazyn kluczy i klucz, a także wersję.
1. Naciśnij **pozycję Wybierz**.
1. Wybierz kolejno pozycje **Przegląd + Utwórz** i **Utwórz**.

    ![SSE-Disk-ENC-Set-Blade-Key. png](media/virtual-machines-disk-encryption-portal/sse-disk-enc-set-blade-key.png)

1. Otwórz zestaw szyfrowanie dysków po zakończeniu tworzenia i wybierz alert, który zostanie wystawiony.

    ![SSE-Disk-ENC-alert-Fix. png](media/virtual-machines-disk-encryption-portal/sse-disk-enc-alert-fix.png)

Dwie powiadomienia powinny wyskakujące i zakończyć się pomyślnie. Pozwoli to na użycie zestawu szyfrowanie dysków w magazynie kluczy.

![Disk-ENC-Notification-Success. png](media/virtual-machines-disk-encryption-portal/disk-enc-notification-success.png)

#### <a name="deploy-a-vm"></a>Wdrażanie maszyny wirtualnej

Po utworzeniu i skonfigurowaniu magazynu kluczy oraz zestawu szyfrowania dysków można wdrożyć maszynę wirtualną przy użyciu szyfrowania.
Proces wdrażania maszyn wirtualnych jest podobny do standardowego procesu wdrażania, ale jedyną różnicą jest to, że należy wdrożyć maszynę wirtualną w tym samym regionie, co inne zasoby, i wybrać klucz zarządzany przez klienta.

1. Wyszukaj **Virtual Machines** a następnie wybierz pozycję **+ Dodaj** , aby utworzyć maszynę wirtualną.
1. Na karcie **podstawowa** wybierz ten sam region, w którym ustawiono szyfrowanie dysków i Azure Key Vault.
1. Wypełnij pozostałe wartości na karcie **podstawowa** .

    ![SSE-Create-a-VM-region. png](media/virtual-machines-disk-encryption-portal/sse-create-a-vm-region.png)

1. Na karcie **dyski** wybierz pozycję **szyfrowanie w spoczynku z kluczem zarządzanym przez klienta**.
1. Wybierz pozycję Szyfrowanie dysków na liście rozwijanej **zestaw szyfrowania dysków** .
1. Wprowadź pozostałe wybrane opcje.

    ![SSE-Create-VM-SELECT-CMK-Encryption-Set. png](media/virtual-machines-disk-encryption-portal/sse-create-vm-select-cmk-encryption-set.png)