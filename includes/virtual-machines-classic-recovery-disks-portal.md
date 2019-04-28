---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 5490bdd3934b438a683ce4271fbec20b3d13735d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60770858"
---
Jeśli maszyna wirtualna na platformie Azure napotkała błąd podczas rozruchu lub błąd dysku, konieczne może wykonanie kroków rozwiązywania problemów na samym wirtualnym dysku twardym. Typowym przykładem może być niepowodzenie aktualizacji aplikacji, które uniemożliwia pomyślny rozruch maszyny wirtualnej. W tym artykule opisano, jak za pomocą witryny Azure Portal połączyć wirtualny dysk twardy z inną maszyną wirtualną w celu naprawienia błędów, a następnie ponownie utworzyć oryginalną maszynę wirtualną.


## <a name="recovery-process-overview"></a>Omówienie procesu odzyskiwania
Proces rozwiązywania problemów jest następujący:

1. Usuń maszynę wirtualną, która napotyka problemy, ale zachowaj wirtualne dyski twarde.
2. Dołącz i zainstaluj wirtualny dysk twardy do innej maszyny wirtualnej na potrzeby rozwiązywania problemów.
3. Nawiąż połączenie z maszyną wirtualną rozwiązywania problemów. Edytuj pliki lub uruchom narzędzia, aby naprawić błędy na oryginalnym wirtualnym dysku twardym.
4. Odłącz wirtualny dysk twardy od maszyny wirtualnej rozwiązywania problemów.
5. Utwórz maszynę wirtualną za pomocą oryginalnego wirtualnego dysku twardego.

## <a name="delete-the-original-vm"></a>Usuwanie oryginalnej maszyny wirtualnej
Wirtualne dyski twarde i maszyny wirtualne to dwa odrębne zasoby na platformie Azure. Wirtualny dysk twardy to miejsce, w którym przechowywany jest system operacyjny, aplikacje i konfiguracje. Maszyna wirtualna jest po prostu metadanymi, które definiują rozmiar lub lokalizację i które odwołują się do zasobów, takich jak wirtualny dysk twardy lub wirtualna karta sieciowa. Każdy wirtualny dysk twardy otrzymuje przypisaną dzierżawę, kiedy jest dołączany do maszyny wirtualnej. Dyski danych można dołączać i odłączać nawet wtedy, gdy maszyna wirtualna jest uruchomiona, ale dysku systemu operacyjnego nie można odłączyć, chyba że zasób maszyny wirtualnej zostanie usunięty. Dzierżawa w dalszym ciągu będzie kojarzyła dysk systemu operacyjnego z maszyną wirtualną, nawet gdy ta maszyna wirtualna jest w stanie zatrzymania i ma cofnięty przydział.

Pierwszym krokiem do odzyskania maszyny wirtualnej jest usunięcie samego zasobu maszyny wirtualnej. Usunięcie maszyny wirtualnej pozostawia wirtualne dyski twarde na koncie magazynu. Po usunięciu maszyny wirtualnej możesz dołączyć wirtualny dysk twardy do innej maszyny wirtualnej, aby przeprowadzić rozwiązywanie problemów i usunąć błędy. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 
2. W menu po lewej stronie kliknij pozycję **Maszyny wirtualne (klasyczne)**.
3. Wybierz maszynę wirtualną, której dotyczy problem, kliknij pozycję **Dyski**, a następnie zidentyfikuj nazwę wirtualnego dysku twardego. 
4. Wybierz wirtualny dysk twardy systemu operacyjnego i sprawdź atrybut **Lokalizacja**, aby zidentyfikować konto magazynu zawierające ten wirtualny dysk twardy. W poniższym przykładzie ciąg bezpośrednio przed „.blob.core.windows.net” jest nazwą konta magazynu.

    ```
    https://portalvhds73fmhrw5xkp43.blob.core.windows.net/vhds/SCCM2012-2015-08-28.vhd
    ```

    ![Obraz dotyczący lokalizacji maszyny wirtualnej](./media/virtual-machines-classic-recovery-disks-portal/vm-location.png)

5. Kliknij prawym przyciskiem myszy maszynę wirtualną, a następnie wybierz pozycję **Usuń**. Upewnij się, że dyski nie są wybrane podczas usuwania maszyny wirtualnej.
6. Utwórz nową maszynę wirtualną odzyskiwania. Ta maszyna wirtualna musi znajdować w tym samym regionie i grupie zasobów (usłudze w chmurze) co maszyna wirtualna, której dotyczy problem.
7. Wybierz maszynę wirtualną odzyskiwania, a następnie wybierz pozycje **Dyski** > **Dołącz istniejący**.
8. Aby wybrać istniejący wirtualny dysk twardy, kliknij pozycję **Plik VHD**:

    ![Przeglądnie w poszukiwaniu istniejącego dysku VHD](./media/virtual-machines-classic-recovery-disks-portal/select-vhd-location.png)

9. Wybierz konto magazynu > kontener wirtualnego dysku twardego > wirtualny dysk twardy i kliknij przycisk **Wybierz**, aby potwierdzić wybór.

    ![Wybieranie istniejącego wirtualnego dysku twardego](./media/virtual-machines-classic-recovery-disks-portal/select-vhd.png)

10. Po wybraniu wirtualnego dysku twardego wybierz polecenie **OK**, aby dołączyć istniejący wirtualny dysk twardy.
11. Po kilku sekundach w okienku **Dyski** dla maszyny wirtualnej zostanie wyświetlony istniejący wirtualny dysk twardy podłączony jako dysk danych:

    ![Istniejący wirtualny dysk twardy dołączony jako dysk danych](./media/virtual-machines-classic-recovery-disks-portal/attached-disk.png)

## <a name="fix-issues-on-the-original-virtual-hard-disk"></a>Rozwiązywanie problemów na oryginalnym wirtualnym dysku twardym
Kiedy istniejący wirtualny dysk twardy jest zainstalowany, można wykonać dowolne kroki obsługi i rozwiązywania problemów w zależności od potrzeb. Po usunięciu problemów wykonaj następujące kroki.

## <a name="unmount-and-detach-the-original-virtual-hard-disk"></a>Odinstalowywanie i odłączanie oryginalnego wirtualnego dysku twardego
Po rozwiązaniu wszystkich problemów i usunięciu błędów odinstaluj i odłącz istniejący wirtualny dysk twardy z rozwiązywania problemów od maszyny wirtualnej rozwiązywania problemów. Wirtualnego dysku twardego nie można używać razem z innymi maszynami wirtualnymi, dopóki dzierżawa, która dołącza wirtualny dysk twardy do maszyny wirtualnej rozwiązywania problemów, nie zostanie zwolniona.  

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 
2. W menu po lewej stronie wybierz pozycję **Maszyny wirtualne (klasyczne)**.
3. Zlokalizuj maszynę wirtualną odzyskiwania. Wybierz pozycję Dyski, kliknij prawym przyciskiem myszy dysk, a następnie wybierz polecenie **Odłącz**.

## <a name="create-a-vm-from-the-original-hard-disk"></a>Tworzenie maszyny wirtualnej na podstawie oryginalnego dysku twardego

Aby utworzyć Maszynę wirtualną z oryginalnego wirtualnego dysku twardego, użyj [witryny Azure portal](https://portal.azure.com).

1. Zaloguj się w [portalu Azure](https://portal.azure.com).
2. W lewym górnym rogu portalu, wybierz opcję **Utwórz zasób** > **obliczenia** > **maszyny wirtualnej** > **z Galeria**.
3. W sekcji **Wybieranie obrazu** wybierz pozycję **Moje dyski**, a następnie wybierz oryginalny wirtualny dysk twardy. Sprawdź informacje o lokalizacji. Jest to region, w którym maszyna wirtualna musi zostać wdrożona. Kliknij przycisk Dalej.
4. W sekcji **Konfiguracja maszyny wirtualnej** wpisz nazwę maszyny wirtualnej i wybierz rozmiar maszyny wirtualnej.
