---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/25/2018
ms.author: cynthn
ms.openlocfilehash: 343ffb8daf19711d6c2a61b819ece92448146446
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2018
ms.locfileid: "52331310"
---
Gdy już nie potrzebujesz dysku danych dołączonego do maszyny wirtualnej, możesz go łatwo odłączyć. Odłączenie dysku powoduje jego usunięcie z maszyny wirtualnej, ale nie usuwa go z konta magazynu platformy Azure.

Jeśli chcesz użyć danych znajdujących się na tym dysku, możesz dołączyć go ponownie do tej samej lub innej maszyny wirtualnej.  

> [!NOTE]
> Aby odłączyć dysk systemu operacyjnego, najpierw musisz usunąć maszynę wirtualną.
>

## <a name="find-the-disk"></a>Wyszukiwanie dysku
Jeśli nie znasz nazwy dysku lub chcesz ją sprawdzić przed odłączeniem dysku, wykonaj następujące kroki.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Kliknij pozycję **Maszyny wirtualne**, a następnie wybierz odpowiednią maszynę wirtualną.

3. Kliknij pozycję **Dyski** z lewej strony pulpitu nawigacyjnego maszyny wirtualnej, w obszarze **Ustawienia**.

 Pulpit nawigacyjny maszyny wirtualnej zawiera listę nazw i typów wszystkich dołączonych dysków. Na przykład na tym ekranie przedstawiono maszynę wirtualną z jednym dyskiem systemu operacyjnego (OS) i jednym dyskiem danych:

    ![Znajdowanie dysku danych](./media/howto-detach-disk-windows-linux/vmwithdisklist.png)

## <a name="detach-the-disk"></a>Odłączanie dysku
1. W witrynie Azure Portal kliknij pozycję **Maszyny wirtualne**, a następnie kliknij nazwę maszyny wirtualnej zawierającej dysk danych, który chcesz odłączyć.

2. Kliknij pozycję **Dyski** z lewej strony pulpitu nawigacyjnego maszyny wirtualnej, w obszarze **Ustawienia**.

3. Kliknij dysk, który chcesz odłączyć.

  ![Identyfikowanie dysku do odłączenia](./media/howto-detach-disk-windows-linux/disklist.png)

4. Na pasku poleceń kliknij pozycję **Odłącz**.

  ![Znajdowanie polecenia Odłącz](./media/howto-detach-disk-windows-linux/diskdetachcommand.png)

5. W oknie potwierdzenia kliknij przycisk **Tak**, aby odłączyć dysk.

  ![Potwierdzanie odłączania dysku](./media/howto-detach-disk-windows-linux/confirmdetach.png)

Odłączony dysk pozostaje w magazynie, lecz nie jest już dołączony do maszyny wirtualnej.
