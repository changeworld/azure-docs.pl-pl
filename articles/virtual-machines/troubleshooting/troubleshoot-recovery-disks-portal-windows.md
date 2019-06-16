---
title: Użyj Windows z maszyny Wirtualnej rozwiązywania problemów w witrynie Azure portal | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy z maszyny wirtualnej Windows na platformie Azure, łącząc dysku systemu operacyjnego do odzyskiwania maszyny Wirtualnej przy użyciu witryny Azure portal
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: jeconnoc
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/13/2018
ms.author: genli
ms.openlocfilehash: ec2da7d9f659f32c40f7a2685ab08be4eec27ed5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60320933"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Rozwiązywanie problemów z maszyny Wirtualnej z systemem Windows, dołączając dysk systemu operacyjnego do odzyskiwania maszyny Wirtualnej przy użyciu witryny Azure portal
Windows maszyny wirtualnej (VM) na platformie Azure napotka błąd rozruchu lub dysk, może być konieczne wykonanie kroków rozwiązywania problemów na samym wirtualnym dysku twardym. Typowym przykładem może być Niepowodzenie aktualizacji aplikacji, która uniemożliwia maszynie Wirtualnej możliwość wykonania rozruchu pomyślnie. Ten artykuł szczegółowo opisuje sposób użycia witryny Azure portal połączyć wirtualny dysk twardy do innej Windows maszyny Wirtualnej, aby naprawić wszystkie błędy, a następnie ponownie utworzyć oryginalną maszynę Wirtualną.

## <a name="recovery-process-overview"></a>Omówienie procesu odzyskiwania
Proces rozwiązywania problemów jest następujący:

1. Usuń maszynę Wirtualną napotyka problemy, utrzymując wirtualnych dysków twardych.
2. Dołącz i zainstaluj wirtualny dysk twardy do innej maszyny Wirtualnej Windows na potrzeby rozwiązywania problemów.
3. Nawiąż połączenie z maszyną wirtualną rozwiązywania problemów. Edytuj pliki lub uruchom dowolnych narzędzi w celu rozwiązywania problemów na oryginalnym wirtualnym dysku twardym.
4. Odłącz wirtualny dysk twardy od maszyny wirtualnej rozwiązywania problemów.
5. Utwórz Maszynę wirtualną za pomocą oryginalnego wirtualnego dysku twardego.

Dla maszyn wirtualnych dysku zarządzanego używa firma Microsoft teraz używać programu Azure PowerShell do zmiany dysku systemu operacyjnego dla maszyny Wirtualnej. Firma Microsoft nie będzie konieczne usunięcie i ponowne utworzenie maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z maszyny Wirtualnej z systemem Windows, dołączając dysk systemu operacyjnego do odzyskiwania maszyny Wirtualnej przy użyciu programu Azure PowerShell](troubleshoot-recovery-disks-windows.md).

## <a name="determine-boot-issues"></a>Określenia problemów rozruchu
Aby określić, dlaczego nie jest poprawnie rozruch maszyny Wirtualnej, sprawdź diagnostyki rozruchu zrzut ekranu maszyny Wirtualnej. Typowym przykładem może być Niepowodzenie aktualizacji aplikacji lub podstawowy wirtualny dysk twardy jest usunięty lub przeniesiony.

Wybierz maszynę Wirtualną w portalu, a następnie przewiń w dół do **pomoc techniczna i rozwiązywanie problemów** sekcji. Kliknij przycisk **diagnostykę rozruchu** do wyświetlania na zrzucie ekranu. Należy pamiętać, wszystkie określone komunikaty o błędach lub kody błędów, aby określić, dlaczego maszyny Wirtualnej napotkała problem. 

![Wyświetlanie maszyny Wirtualnej diagnostyki rozruchu dzienniki konsoli](./media/troubleshoot-recovery-disks-portal-windows/screenshot-error.png)

Możesz również kliknąć **Pobierz zrzut ekranu** można pobrać Przechwyć zrzut ekranu maszyny Wirtualnej.

## <a name="view-existing-virtual-hard-disk-details"></a>Szczegółowe informacje są istniejącego wirtualnego dysku twardego
Zanim będzie możliwe dołączenie wirtualny dysk twardy do innej maszyny Wirtualnej, należy określić nazwę wirtualnego dysku twardego (VHD). 

Wybierz grupę zasobów z poziomu portalu, a następnie wybierz swoje konto magazynu. Kliknij przycisk **obiektów blob**, jak w poniższym przykładzie:

![Wybierz magazyn obiektów blob](./media/troubleshoot-recovery-disks-portal-windows/storage-account-overview.png)

Zazwyczaj mają kontener o nazwie **wirtualne dyski twarde** który przechowuje Twoje wirtualne dyski twarde. Wybierz kontener, aby wyświetlić listę wirtualnych dysków twardych. Zanotuj nazwę wirtualnego dysku twardego (prefiks jest zazwyczaj Nazwa maszyny Wirtualnej):

![Identyfikowanie dysku VHD w kontenerze magazynu](./media/troubleshoot-recovery-disks-portal-windows/storage-container.png)

Wybierz istniejący wirtualny dysk twardy z listy i skopiuj adres URL do użycia w kolejnych krokach:

![Skopiuj istniejący adres URL wirtualnego dysku twardego](./media/troubleshoot-recovery-disks-portal-windows/copy-vhd-url.png)


## <a name="delete-existing-vm"></a>Usuń istniejącą maszynę Wirtualną
Wirtualne dyski twarde i maszyny wirtualne to dwa odrębne zasoby na platformie Azure. Wirtualny dysk twardy jest, gdzie są przechowywane sam system operacyjny, aplikacje i konfiguracje. Samej maszyny Wirtualnej jest po prostu metadanymi, definiują rozmiar lub lokalizację, która odwołuje się do zasobów, takich jak wirtualny dysk twardy lub karty interfejsu sieci wirtualnej (NIC). Każdy wirtualny dysk twardy ma przypisaną dzierżawę, kiedy dołączone do maszyny Wirtualnej. Dyski danych można dołączać i odłączać nawet wtedy, gdy maszyna wirtualna jest uruchomiona, ale dysku systemu operacyjnego nie można odłączyć, chyba że zasób maszyny wirtualnej zostanie usunięty. Dalszym ciągu będzie kojarzyła dysk systemu operacyjnego z maszyną Wirtualną, nawet wtedy, gdy ta maszyna wirtualna jest w stanie zatrzymania i ma cofnięty przydział dzierżawy.

Pierwszy krok, aby odzyskać maszynę Wirtualną jest nieusuwanie samego zasobu maszyny Wirtualnej. Usunięcie maszyny wirtualnej pozostawia wirtualne dyski twarde na koncie magazynu. Po usunięciu maszyny Wirtualnej możesz dołączyć wirtualny dysk twardy do innej maszyny Wirtualnej do rozwiązywania oraz usuwania błędów.

Wybierz maszynę Wirtualną w portalu, a następnie kliknij przycisk **Usuń**:

![Maszyna wirtualna rozruchu zrzut ekranu przedstawiający diagnostykę rozruchu wystąpił błąd](./media/troubleshoot-recovery-disks-portal-windows/stop-delete-vm.png)

Poczekaj, aż maszyny Wirtualnej zostało zakończone, usuwanie, przed dołączeniem wirtualny dysk twardy do innej maszyny Wirtualnej. Dzierżawy dla wirtualnego dysku twardego, który kojarzy ją z maszyny Wirtualnej musi zostać zwolnione, zanim będzie możliwe dołączenie wirtualny dysk twardy do innej maszyny Wirtualnej.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Dołącz istniejący wirtualny dysk twardy do innej maszyny Wirtualnej
W następnych kilku krokach należy użyć innej maszyny Wirtualnej na potrzeby rozwiązywania problemów. Istniejący wirtualny dysk twardy możesz dołączyć do tej maszyny Wirtualnej rozwiązywania problemów, aby można było do przeglądania i edytowania zawartości na dysku. Ten proces umożliwia Popraw wszelkie błędy konfiguracji lub przejrzyj dodatkową aplikację i plikach dziennika systemowego, na przykład. Wybierz lub Utwórz inną maszynę Wirtualną do użycia na potrzeby rozwiązywania problemów.

1. Wybierz grupę zasobów z poziomu portalu, a następnie wybierz maszynę Wirtualną rozwiązywania problemów. Wybierz **dysków** a następnie kliknij przycisk **Dołącz istniejące**:

    ![Dołączanie istniejącego dysku w portalu](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. Aby wybrać istniejący wirtualny dysk twardy, kliknij pozycję **Plik VHD**:

    ![Przeglądnie w poszukiwaniu istniejącego dysku VHD](./media/troubleshoot-recovery-disks-portal-windows/select-vhd-location.png)

3. Wybierz swoje konto magazynu i kontener, a następnie kliknij przycisk istniejącego wirtualnego dysku twardego. Kliknij przycisk **wybierz** przycisk, aby potwierdzić wybór:

    ![Wybieranie istniejącego wirtualnego dysku twardego](./media/troubleshoot-recovery-disks-portal-windows/select-vhd.png)

4. Za pomocą wirtualnego dysku twardego teraz zaznaczone, kliknij przycisk **OK** można dołączyć istniejącego wirtualnego dysku twardego:

    ![Potwierdź dołączenie istniejącego wirtualnego dysku twardego](./media/troubleshoot-recovery-disks-portal-windows/attach-disk-confirm.png)

5. Po kilku sekundach **dysków** okienko dla maszyny Wirtualnej zawiera istniejący wirtualny dysk twardy podłączony jako dysk z danymi:

    ![Istniejący wirtualny dysk twardy dołączony jako dysk danych](./media/troubleshoot-recovery-disks-portal-windows/attached-disk.png)


## <a name="mount-the-attached-data-disk"></a>Zainstaluj dysk danych dołączony

1. Otwórz połączenie pulpitu zdalnego z maszyną wirtualną. Wybierz maszynę Wirtualną w portalu i kliknij przycisk **Connect**. Pobierz i Otwórz plik połączenia RDP. Wprowadź swoje poświadczenia, aby zalogować się do maszyny Wirtualnej w następujący sposób:

    ![Zaloguj się do maszyny Wirtualnej przy użyciu pulpitu zdalnego](./media/troubleshoot-recovery-disks-portal-windows/open-remote-desktop.png)

2. Otwórz **Menedżera serwera**, a następnie wybierz **usług plików i magazynowania**. 

    ![Wybierz usługi plików i magazynowania w Menedżerze serwera](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

3. Dysk danych jest automatycznie wykryte i dołączony. Aby wyświetlić listę podłączonych dysków, wybierz **dysków**. Można wybrać dysk danych do wyświetlania informacji o woluminie, łącznie z literą dysku. W poniższym przykładzie pokazano dysk danych dołączony i przy użyciu **F:** :

    ![Dysk dołączony i informacji o woluminie w Menedżerze serwera](./media/troubleshoot-recovery-disks-portal-windows/server-manager-disk-attached.png)


## <a name="fix-issues-on-original-virtual-hard-disk"></a>Rozwiązywanie problemów na oryginalnym wirtualnym dysku twardym
Istniejący wirtualny dysk twardy zainstalowany można teraz wykonywać żadnej konserwacji i kroki rozwiązywania problemów, zgodnie z potrzebami. Po usunięciu problemów wykonaj następujące kroki.


## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Odinstalowywanie i odłączanie oryginalnego wirtualnego dysku twardego
Gdy błędy są rozwiązywane, odłącz istniejący wirtualny dysk twardy z maszyny Wirtualnej rozwiązywania problemów. Nie można użyć wirtualnego dysku twardego z innej maszyny Wirtualnej, dopóki dzierżawa dołączanie wirtualnego dysku twardego do maszyny Wirtualnej rozwiązywania problemów zostanie zwolniona.

1. W sesji protokołu RDP z maszyną wirtualną, otwórz **Menedżera serwera**, a następnie wybierz **usług plików i magazynowania**:

    ![Wybierz usługi plików i magazynowania w Menedżerze serwera](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

2. Wybierz **dysków** , a następnie wybierz dysk danych. Kliknij prawym przyciskiem myszy na dysku danych, a następnie wybierz pozycję **Przełącz do trybu Offline**:

    ![Wynoszą dysk z danymi w Menedżerze serwera w trybie offline](./media/troubleshoot-recovery-disks-portal-windows/server-manager-set-disk-offline.png)

3. Teraz Odłącz wirtualny dysk twardy z maszyny Wirtualnej. Wybierz maszynę Wirtualną w witrynie Azure portal, a następnie kliknij przycisk **dysków**. Wybierz istniejący wirtualny dysk twardy, a następnie kliknij przycisk **Odłącz**:

    ![Odłącz istniejący wirtualny dysk twardy](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    Poczekaj, aż maszyny Wirtualnej ma pomyślne odłączenie dysku danych przed kontynuowaniem.

## <a name="create-vm-from-original-hard-disk"></a>Tworzenie maszyny Wirtualnej na podstawie oryginalnego dysku twardego
Aby utworzyć Maszynę wirtualną z oryginalnego wirtualnego dysku twardego, użyj [tego szablonu usługi Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-new-or-existing-vnet). Szablon umożliwia wdrożenie maszyny Wirtualnej w istniejącej lub nowej sieci wirtualnej, przy użyciu adresu URL wirtualnego dysku twardego z wcześniej polecenia. Kliknij przycisk **Wdróż na platformie Azure** przycisk w następujący sposób:

![Wdrażanie maszyny Wirtualnej z szablonu z serwisu GitHub](./media/troubleshoot-recovery-disks-portal-windows/deploy-template-from-github.png)

Szablon jest ładowany do witryny Azure portal dla wdrożenia. Wprowadź nazwy dla nowej maszyny Wirtualnej i istniejące zasoby platformy Azure, a następnie wklej adres URL do istniejącego wirtualnego dysku twardego. Aby rozpocząć wdrożenie, kliknij przycisk **zakupu**:

![Wdrażanie maszyny Wirtualnej z szablonu](./media/troubleshoot-recovery-disks-portal-windows/deploy-from-image.png)


## <a name="re-enable-boot-diagnostics"></a>Włączyć ponownie diagnostykę rozruchu
Po utworzeniu maszyny Wirtualnej z istniejącego wirtualnego dysku twardego Diagnostyka rozruchu może nie być włączane automatycznie. Aby sprawdzić stan diagnostykę rozruchu i włączyć, jeśli to konieczne, wybierz maszynę Wirtualną w portalu. W obszarze **monitorowanie**, kliknij przycisk **ustawień diagnostycznych**. Upewnij się, stan **na**i znacznik wyboru obok pozycji **diagnostykę rozruchu** jest zaznaczone. Jeśli wprowadzisz jakiekolwiek zmiany, kliknij przycisk **Zapisz**:

![Aktualizowanie ustawień diagnostyki rozruchu](./media/troubleshoot-recovery-disks-portal-windows/reenable-boot-diagnostics.png)

## <a name="next-steps"></a>Kolejne kroki
Jeśli występują problemy z nawiązywaniem połączenia z maszyną wirtualną, zobacz [połączenia RDP Rozwiązywanie problemów z Maszyną wirtualną platformy Azure](troubleshoot-rdp-connection.md). W przypadku problemów z dostępem do aplikacji działających na maszynie Wirtualnej, zobacz [aplikacji Rozwiązywanie problemów z łącznością na maszynie Wirtualnej Windows](troubleshoot-app-connection.md).

Aby uzyskać więcej informacji na temat przy użyciu usługi Resource Manager, zobacz [Omówienie usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

