---
title: Użyj maszyny wirtualnej rozwiązywania problemów z systemem Windows w Azure Portal | Microsoft Docs
description: Dowiedz się, jak rozwiązywać problemy z maszynami wirtualnymi systemu Windows na platformie Azure, łącząc dysk systemu operacyjnego z maszyną wirtualną odzyskiwania przy użyciu Azure Portal
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: gwallace
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/13/2018
ms.author: genli
ms.openlocfilehash: 8ab6fc75475cd99e3d803450476880175f12d2b6
ms.sourcegitcommit: 1b7b0e1c915f586a906c33d7315a5dc7050a2f34
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "67881147"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Rozwiązywanie problemów z maszyną wirtualną z systemem Windows przez dołączenie dysku systemu operacyjnego do maszyny wirtualnej odzyskiwania przy użyciu Azure Portal
Jeśli maszyna wirtualna z systemem Windows na platformie Azure napotyka błąd rozruchu lub dysku, może być konieczne wykonanie kroków rozwiązywania problemów dotyczących wirtualnego dysku twardego. Typowym przykładem może być niepowodzenie aktualizacji aplikacji, która uniemożliwia pomyślne uruchomienie maszyny wirtualnej. W tym artykule szczegółowo opisano sposób używania Azure Portal do łączenia wirtualnego dysku twardego z inną maszyną wirtualną z systemem Windows w celu usunięcia błędów, a następnie ponownego utworzenia oryginalnej maszyny wirtualnej. 

## <a name="recovery-process-overview"></a>Omówienie procesu odzyskiwania
Proces rozwiązywania problemów jest następujący:

1. Usuń problemy z MASZYNą wirtualną, zachowując wirtualne dyski twarde.
2. Dołącz i Zainstaluj wirtualny dysk twardy na innej maszynie wirtualnej z systemem Windows w celu rozwiązywania problemów.
3. Nawiąż połączenie z maszyną wirtualną rozwiązywania problemów. Edytuj pliki lub Uruchom dowolne narzędzia, aby rozwiązać problemy dotyczące oryginalnego wirtualnego dysku twardego.
4. Odłącz wirtualny dysk twardy od maszyny wirtualnej rozwiązywania problemów.
5. Utwórz maszynę wirtualną przy użyciu oryginalnego wirtualnego dysku twardego.

W przypadku maszyny wirtualnej korzystającej z dysku zarządzanego można teraz użyć Azure PowerShell, aby zmienić dysk systemu operacyjnego dla maszyny wirtualnej. Nie jest już konieczne usuwanie i Tworzenie maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z maszyną wirtualną z systemem Windows przez dołączenie dysku systemu operacyjnego do maszyny wirtualnej odzyskiwania przy użyciu Azure PowerShell](troubleshoot-recovery-disks-windows.md).

> [!NOTE]
> Ten artykuł nie ma zastosowania do maszyny wirtualnej z dyskiem niezarządzanym.

## <a name="determine-boot-issues"></a>Określanie problemów z rozruchem
Aby określić, dlaczego nie można prawidłowo uruchomić maszyny wirtualnej, sprawdź zrzut ekranu maszyny wirtualnej diagnostyki rozruchu. Typowym przykładem może być niepowodzenie aktualizacji aplikacji lub usunięcie lub przeniesienie bazowego wirtualnego dysku twardego.

Wybierz maszynę wirtualną w portalu, a następnie przewiń w dół do sekcji **Pomoc techniczna i rozwiązywanie problemów** . Kliknij pozycję **Diagnostyka rozruchu** , aby wyświetlić zrzut ekranu. Zwróć uwagę na wszystkie określone komunikaty o błędach lub kody błędów, aby pomóc w ustaleniu przyczyny wystąpienia problemu z maszyną wirtualną.

![Wyświetlanie dzienników konsoli diagnostyki rozruchu maszyny wirtualnej](./media/troubleshoot-recovery-disks-portal-windows/screenshot-error.png)

Możesz również kliknąć pozycję **Pobierz zrzut ekranu** , aby pobrać przechwytywanie zrzutu ekranu maszyny wirtualnej.

## <a name="view-existing-virtual-hard-disk-details"></a>Wyświetl szczegóły istniejącego wirtualnego dysku twardego
Przed dołączeniem wirtualnego dysku twardego do innej maszyny wirtualnej należy określić nazwę wirtualnego dysku twardego (VHD).

Wybierz maszynę wirtualną, na której występuje problem, a następnie wybierz pozycję **dyski**. Zapisz nazwę dysku systemu operacyjnego, tak jak w poniższym przykładzie:

![Wybieranie obiektów blob magazynu](./media/troubleshoot-recovery-disks-portal-windows/view-disk.png)

## <a name="delete-existing-vm"></a>Usuń istniejącą maszynę wirtualną
Wirtualne dyski twarde i maszyny wirtualne to dwa odrębne zasoby na platformie Azure. Wirtualny dysk twardy to miejsce, w którym są przechowywane system operacyjny, aplikacje i konfiguracje. Sama maszyna wirtualna to tylko metadane, które definiują rozmiar lub lokalizację oraz odwołują się do zasobów, takich jak wirtualny dysk twardy lub karta interfejsu sieci wirtualnej (karta sieciowa). Każdy wirtualny dysk twardy ma przypisaną dzierżawę po dołączeniu do maszyny wirtualnej. Dyski danych można dołączać i odłączać nawet wtedy, gdy maszyna wirtualna jest uruchomiona, ale dysku systemu operacyjnego nie można odłączyć, chyba że zasób maszyny wirtualnej zostanie usunięty. Dzierżawa nadal kojarzy dysk systemu operacyjnego z maszyną wirtualną nawet wtedy, gdy maszyna wirtualna jest w stanie zatrzymania i cofania przydziału.

Pierwszym krokiem do odzyskania maszyny wirtualnej jest usunięcie samego zasobu maszyny wirtualnej. Usunięcie maszyny wirtualnej pozostawia wirtualne dyski twarde na koncie magazynu. Po usunięciu maszyny wirtualnej można dołączyć wirtualny dysk twardy do innej maszyny wirtualnej, aby rozwiązać problemy i rozwiązać te błędy.

Wybierz maszynę wirtualną w portalu, a następnie kliknij pozycję **Usuń**:

![Zrzut ekranu diagnostyki rozruchu maszyny wirtualnej przedstawia błąd rozruchu](./media/troubleshoot-recovery-disks-portal-windows/stop-delete-vm.png)

Przed dołączeniem wirtualnego dysku twardego do innej maszyny wirtualnej poczekaj na zakończenie usuwania maszyny wirtualnej. Przed dołączeniem wirtualnego dysku twardego do innej maszyny wirtualnej należy zwolnić dzierżawę na wirtualnym dysku twardym, która kojarzy ją z MASZYNą wirtualną.

## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Dołącz istniejący wirtualny dysk twardy do innej maszyny wirtualnej
W następnych kilku krokach do rozwiązywania problemów służy inna maszyna wirtualna. Aby móc przeglądać i edytować zawartość dysku, należy dołączyć istniejący wirtualny dysk twardy do tej maszyny wirtualnej rozwiązywania problemów. Ten proces umożliwia poprawienie wszystkich błędów konfiguracji lub przejrzenie dodatkowych plików dziennika aplikacji lub systemu, na przykład. Wybierz lub Utwórz inną maszynę wirtualną do użycia na potrzeby rozwiązywania problemów.

1. Wybierz grupę zasobów z portalu, a następnie wybierz maszynę wirtualną rozwiązywania problemów. Wybierz pozycję **dyski**, wybierz pozycję **Edytuj**, a następnie kliknij pozycję **Dodaj dysk danych**:

    ![Dołącz istniejący dysk w portalu](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. Z listy **dyski danych** wybierz dysk systemu operacyjnego zidentyfikowanej maszyny wirtualnej. Jeśli nie widzisz dysku systemu operacyjnego, upewnij się, że Rozwiązywanie problemów z maszyną wirtualną i dyskiem systemu operacyjnego znajduje się w tym samym regionie (lokalizacja).
3. Wybierz pozycję **Zapisz** , aby zastosować zmiany.

## <a name="mount-the-attached-data-disk"></a>Instalowanie dołączonego dysku z danymi

1. Otwórz połączenie Pulpit zdalny z maszyną wirtualną. Wybierz maszynę wirtualną w portalu, a następnie kliknij przycisk **Połącz**. Pobierz i Otwórz plik połączenia RDP. Wprowadź swoje poświadczenia, aby zalogować się do maszyny wirtualnej w następujący sposób:

    ![Zaloguj się do maszyny wirtualnej przy użyciu Pulpit zdalny](./media/troubleshoot-recovery-disks-portal-windows/open-remote-desktop.png)

2. Otwórz **Menedżer serwera**, a następnie wybierz pozycję **usługi plików i magazynowania**. 

    ![Wybierz usługi plików i magazynowania w ramach Menedżer serwera](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

3. Dysk danych zostanie automatycznie wykryty i dołączony. Aby wyświetlić listę podłączonych dysków, wybierz pozycję **dyski**. Możesz wybrać dysk danych, aby wyświetlić informacje o woluminie, w tym literę dysku. Poniższy przykład przedstawia dołączony dysk danych i użycie **F:** :

    ![Informacje o dysku i woluminie w Menedżer serwera](./media/troubleshoot-recovery-disks-portal-windows/server-manager-disk-attached.png)


## <a name="fix-issues-on-original-virtual-hard-disk"></a>Rozwiązywanie problemów dotyczących oryginalnego wirtualnego dysku twardego
Po zainstalowaniu istniejącego wirtualnego dysku twardego można wykonać wszelkie czynności konserwacyjne i rozwiązywania problemów zgodnie z wymaganiami. Po usunięciu problemów wykonaj następujące kroki.


## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Odinstalowywanie i odłączanie oryginalnego wirtualnego dysku twardego
Po rozwiązaniu błędów Odłącz istniejący wirtualny dysk twardy z maszyny wirtualnej rozwiązywania problemów. Nie można użyć wirtualnego dysku twardego z żadną inną maszyną wirtualną, dopóki dzierżawa nie dołączy wirtualnego dysku twardego do maszyny wirtualnej rozwiązywania problemów.

1. Z sesji RDP na maszynę wirtualną Otwórz **Menedżer serwera**, a następnie wybierz pozycję **usługi plików i magazynowania**:

    ![Wybierz usługi plików i magazynowania w Menedżer serwera](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

2. Wybierz pozycję **dyski** , a następnie wybierz dysk danych. Kliknij prawym przyciskiem myszy dysk danych i wybierz polecenie **Przełącz do trybu offline**:

    ![Ustaw dysk danych jako w trybie offline w Menedżer serwera](./media/troubleshoot-recovery-disks-portal-windows/server-manager-set-disk-offline.png)

3. Teraz Odłącz wirtualny dysk twardy od maszyny wirtualnej. W Azure Portal wybierz maszynę wirtualną, a następnie kliknij pozycję **dyski**. 
4. Wybierz pozycję **Edytuj**, wybierz dołączony dysk systemu operacyjnego, a następnie kliknij przycisk **Odłącz**:

    ![Odłącz istniejący wirtualny dysk twardy](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    Poczekaj, aż maszyna wirtualna pomyślnie odłączy dysk danych przed kontynuowaniem.

## <a name="create-vm-from-original-hard-disk"></a>Tworzenie maszyny wirtualnej na podstawie oryginalnego dysku twardego

### <a name="method-1-use-azure-resource-manager-template"></a>Metoda 1 Użyj szablonu Azure Resource Manager
Aby utworzyć maszynę wirtualną na podstawie oryginalnego wirtualnego dysku twardego, użyj [tego szablonu Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-new-or-existing-vnet). Szablon wdraża MASZYNę wirtualną w istniejącej lub nowej sieci wirtualnej przy użyciu adresu URL wirtualnego dysku twardego z wcześniejszego polecenia. Kliknij przycisk **Wdróż na platformie Azure** w następujący sposób:

![Wdróż maszynę wirtualną na podstawie szablonu z usługi GitHub](./media/troubleshoot-recovery-disks-portal-windows/deploy-template-from-github.png)

Szablon jest ładowany do Azure Portal wdrożenia. Wprowadź nazwy nowej maszyny wirtualnej i istniejących zasobów platformy Azure, a następnie wklej adres URL do istniejącego wirtualnego dysku twardego. Aby rozpocząć wdrażanie, kliknij pozycję **Kup**:

![Wdróż maszynę wirtualną na podstawie szablonu](./media/troubleshoot-recovery-disks-portal-windows/deploy-from-image.png)

### <a name="method-2-create-a-vm-from-the-disk"></a>Metoda 2. Tworzenie maszyny wirtualnej na podstawie dysku

1. W Azure Portal wybierz grupę zasobów z portalu, a następnie Znajdź dysk systemu operacyjnego. Możesz również wyszukać dysk przy użyciu nazwy dysku:

    ![Wyszukaj dysk z Azure Portal](./media/troubleshoot-recovery-disks-portal-windows/search-disk.png)
1. Wybierz pozycję **Przegląd**, a następnie wybierz pozycję **Utwórz maszynę wirtualną**.
    ![Tworzenie maszyny wirtualnej na podstawie dysku z Azure Portal](./media/troubleshoot-recovery-disks-portal-windows/create-vm-from-disk.png)
1. Postępuj zgodnie z instrukcjami kreatora, aby utworzyć maszynę wirtualną.

## <a name="re-enable-boot-diagnostics"></a>Włącz ponownie diagnostykę rozruchu
Podczas tworzenia maszyny wirtualnej na podstawie istniejącego wirtualnego dysku twardego Diagnostyka rozruchu może nie być automatycznie włączona. Aby sprawdzić stan diagnostyki rozruchu i włączyć je w razie potrzeby, wybierz maszynę wirtualną w portalu. W obszarze **monitorowanie**kliknij pozycję **Ustawienia diagnostyki**. Upewnij się, że stan jest **włączony**, i zaznacz pole wyboru obok pozycji **Diagnostyka rozruchu** jest zaznaczone. Jeśli wprowadzisz jakiekolwiek zmiany, kliknij przycisk **Zapisz**:

![Aktualizowanie ustawień diagnostyki rozruchu](./media/troubleshoot-recovery-disks-portal-windows/reenable-boot-diagnostics.png)

## <a name="next-steps"></a>Kolejne kroki
Jeśli masz problemy z nawiązywaniem połączenia z maszyną wirtualną, zobacz [Rozwiązywanie problemów z połączeniami RDP z maszyną wirtualną platformy Azure](troubleshoot-rdp-connection.md). Problemy dotyczące uzyskiwania dostępu do aplikacji uruchomionych na maszynie wirtualnej można znaleźć w temacie Rozwiązywanie problemów z [łącznością aplikacji na maszynie wirtualnej z systemem Windows](troubleshoot-app-connection.md).

Aby uzyskać więcej informacji o korzystaniu z Menedżer zasobów, zobacz [Azure Resource Manager omówienie](../../azure-resource-manager/resource-group-overview.md).

