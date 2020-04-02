---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/11/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6077db0a09b09f7e4bfb859902da53b173845e55
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520757"
---
### <a name="portal"></a>Portal

Konfigurowanie kluczy zarządzanych przez klienta dla dysków będzie wymagało utworzenia zasobów w określonej kolejności, jeśli robisz to po raz pierwszy. Najpierw należy utworzyć i skonfigurować usługę Azure Key Vault.

#### <a name="setting-up-your-azure-key-vault"></a>Konfigurowanie usługi Azure Key Vault

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) i wyszukaj usługę Key Vault
1. Wyszukaj i wybierz **przechowalnię kluczy**.

    [![sse-key-vault-portal-search.png](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search.png)](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search-expanded.png#lightbox)

    > [!IMPORTANT]
    > Magazyn kluczy platformy Azure, zestaw szyfrowania dysku, maszyna wirtualna, dyski i migawki muszą znajdować się w tym samym regionie i subskrypcji, aby wdrożenie zakończyło się pomyślnie.

1. Wybierz **+Dodaj,** aby utworzyć nową przechowalnię kluczy.
1. Tworzenie nowej grupy zasobów
1. Wprowadź nazwę magazynu kluczy, wybierz region i wybierz warstwę cenową.
1. Wybierz **pozycję Recenzja + Utwórz**, sprawdź swoje wybory, a następnie wybierz pozycję **Utwórz**.

    ![Zrzut ekranu przedstawiający środowisko tworzenia usługi Azure Key Vault. Wyświetlanie konkretnych wartości, które tworzysz](media/virtual-machines-disk-encryption-portal/sse-create-a-key-vault.png)

1. Po zakończeniu wdrażania magazynu kluczy wybierz go.
1. Wybierz **pozycję Klawisze** w obszarze **Ustawienia**.
1. Wybierz **pozycję Generuj/Importuj**

    ![Zrzut ekranu przedstawiający okienko ustawień zasobu magazynu kluczy. Pokazuje przycisk generowania/importowania wewnątrz ustawień.](media/virtual-machines-disk-encryption-portal/sse-key-vault-generate-settings.png)

1. Pozostaw zarówno **typ klucza** ustawiony na **RSA,** jak i **RSA Key Size** ustawiono na **2048**.
1. Wypełnij pozostałe zaznaczenia, jak chcesz, a następnie wybierz pozycję **Utwórz**.

    ![Zrzut ekranu przedstawiający blok bloku klucza, który pojawia się po wybraniu przycisku generowania/importowania](media/virtual-machines-disk-encryption-portal/sse-create-a-key-generate.png)

#### <a name="setting-up-your-disk-encryption-set"></a>Konfigurowanie zestawu szyfrowania dysku

Aby utworzyć i skonfigurować zestawy szyfrowania dysków, należy użyć następującego łącza: https://aka.ms/diskencryptionsets. Jeśli korzystasz z regionów instytucji rządowych platformy Microsoft Azure, [https://aka.ms/diskencryptionsetsff](https://aka.ms/diskencryptionsetsff)musisz użyć tego łącza: . Tworzenie zestawu szyfrowania dysku nie jest jeszcze dostępne w globalnej witrynie Azure portal.

1. Otwórz łącze zestawów szyfrowania dysku odpowiednie dla danego regionu:

    Regiony publiczne:[https://aka.ms/diskencryptionsets](https://aka.ms/diskencryptionsets)

    Regiony platformy Azure dla instytucji rządowych:[https://aka.ms/diskencryptionsetsff](https://aka.ms/diskencryptionsetsff)
    
1. Wybierz **+Dodaj**.

    ![Zrzut ekranu przedstawiający główny ekran portalu szyfrowania dysku. Wyróżnianie przycisku Dodaj](media/virtual-machines-disk-encryption-portal/sse-create-disk-encryption-set.png)

1. Wybierz grupę zasobów, nazwij zestaw szyfrowania i wybierz ten sam region co magazyn kluczy.
1. Wybierz **przechowalnię kluczy i klawisz**.
1. Wybierz utworzony wcześniej przechowalnię kluczy i klucz, a także wersję.
1. Naciśnij **klawisz Select**.
1. Wybierz **pozycję Recenzja + Utwórz,** a następnie **utwórz**.

    ![Zrzut ekranu przedstawiający blok tworzenia szyfrowania dysku. Wyświetlanie subskrypcji, grupy zasobów, nazwy zestawu szyfrowania dysku, regionu i selektora kluczy + kluczy.](media/virtual-machines-disk-encryption-portal/sse-disk-enc-set-blade-key.png)

1. Otwórz zestaw szyfrowania dysku po zakończeniu tworzenia i wybierz alert, który się pojawi.

    ![Zrzut ekranu przedstawiający okno podręczne alertu: "Aby skojarzyć dysk, obraz lub migawkę z zestawem szyfrowania dysku, należy przyznać uprawnienia do magazynu kluczy". Wybierz ten alert, aby kontynuować](media/virtual-machines-disk-encryption-portal/sse-disk-enc-alert-fix.png)

Dwa powiadomienia powinny pojawić się i zakończyć się powodzeniem. W ten sposób można użyć zestawu szyfrowania dysku z magazynem kluczy.

![Zrzut ekranu przedstawiający pomyślne uprawnienia i przypisanie roli do magazynu kluczy.](media/virtual-machines-disk-encryption-portal/disk-enc-notification-success.png)

#### <a name="deploy-a-vm"></a>Wdrażanie maszyny wirtualnej

Po utworzeniu i skonfigurowaniu magazynu kluczy i zestawu szyfrowania dysku można wdrożyć maszynę wirtualną przy użyciu szyfrowania.
Proces wdrażania maszyny Wirtualnej jest podobny do standardowego procesu wdrażania, jedynymi różnicami są to, że trzeba wdrożyć maszynę wirtualną w tym samym regionie, co inne zasoby i zdecydujesz się użyć klucza zarządzanego przez klienta.

1. Otwórz łącze zestawów szyfrowania dysku odpowiednie dla danego regionu:

    Regiony publiczne:[https://aka.ms/diskencryptionsets](https://aka.ms/diskencryptionsets)

    Regiony platformy Azure dla instytucji rządowych:[https://aka.ms/diskencryptionsetsff](https://aka.ms/diskencryptionsetsff)

1. Wyszukaj **maszyny wirtualne** i wybierz **+ Dodaj,** aby utworzyć maszynę wirtualną.
1. Na karcie **Podstawowe** wybierz ten sam region, co zestaw szyfrowania dysku i usługę Azure Key Vault.
1. Wypełnij inne wartości na karcie **Podstawowe,** jak chcesz.

    ![Zrzut ekranu przedstawiający środowisko tworzenia maszyny Wirtualnej z wyróżnioną wartością regionu.](media/virtual-machines-disk-encryption-portal/sse-create-a-vm-region.png)

1. Na karcie **Dyski** wybierz pozycję **Szyfrowanie w spoczynku przy za pomocą klucza zarządzanego przez klienta**.
1. Wybierz zestaw szyfrowania dysku w **zestawie szyfrowania dysku.**
1. Dokonaj pozostałych wyborów, jak chcesz.

    ![Zrzut ekranu przedstawiający środowisko tworzenia maszyny Wirtualnej, blok dysków. Z wyróżnionym zestawem szyfrowania dysku.](media/virtual-machines-disk-encryption-portal/sse-create-vm-select-cmk-encryption-set.png)

#### <a name="enable-on-an-existing-disk"></a>Włączanie na istniejącym dysku

Aby zarządzać szyfrowaniem dysku i konfigurować je na istniejących https://aka.ms/diskencryptionsetsdyskach, należy użyć następującego łącza: . Włączanie kluczy zarządzanych przez klienta na istniejących dyskach nie jest jeszcze dostępne w globalnej witrynie Azure portal.

> [!CAUTION]
> Włączenie szyfrowania dysku na wszystkich dyskach podłączonych do maszyny Wirtualnej będzie wymagało zatrzymania maszyny Wirtualnej.

1. Otwórz łącze zestawów szyfrowania dysku odpowiednie dla danego regionu:

    Regiony publiczne:[https://aka.ms/diskencryptionsets](https://aka.ms/diskencryptionsets)

    Regiony platformy Azure dla instytucji rządowych:[https://aka.ms/diskencryptionsetsff](https://aka.ms/diskencryptionsetsff)
    
1. Przejdź do maszyny Wirtualnej, która znajduje się w tym samym regionie co jeden z zestawów szyfrowania dysku.
1. Otwórz maszynę wirtualną i wybierz **pozycję Zatrzymaj**.

    ![Zrzut ekranu przedstawiający główną nakładkę dla przykładowej maszyny Wirtualnej. Z podświetleniem przycisku Stop](media/virtual-machines-disk-encryption-portal/sse-stop-VM-to-encrypt-disk.png)

1. Po zakończeniu zatrzymywania maszyny Wirtualnej wybierz pozycję **Dyski,** a następnie wybierz dysk, który chcesz zaszyfrować.

    ![Zrzut ekranu przykładowej maszyny Wirtualnej z otwartym blokem Dyski. Dysk systemu operacyjnego jest wyróżniony jako przykładowy dysk do wyboru.](media/virtual-machines-disk-encryption-portal/sse-existing-disk-select.png)

1. Wybierz **opcję Szyfrowanie** i wybierz **pozycję Szyfrowanie w spoczynku za pomocą klucza zarządzanego przez klienta,** a następnie wybierz szyfrowanie dysku ustawione na liście rozwijanej.
1. Wybierz **pozycję Zapisz**.

    ![Zrzut ekranu przykładowego dysku systemu operacyjnego. Blok szyfrowania jest otwarty, wybrano szyfrowanie za pomocą klucza zarządzanego przez klienta, a także przykładową usługę Azure Key Vault. Po dokonaniu tych wyborów zostanie wybrany przycisk zapisz.](media/virtual-machines-disk-encryption-portal/sse-encrypt-existing-disk-customer-managed-key.png)

1. Powtórz ten proces dla innych dysków dołączonych do maszyny Wirtualnej, którą chcesz zaszyfrować.
1. Po zakończeniu przełączania dysków do kluczy zarządzanych przez klienta, jeśli nie ma żadnych innych podłączonych dysków, które chcesz zaszyfrować, możesz uruchomić maszynę wirtualną.
