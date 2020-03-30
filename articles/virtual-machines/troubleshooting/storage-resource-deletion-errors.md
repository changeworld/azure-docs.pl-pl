---
title: Rozwiązywanie problemów z błędami usuwania zasobów magazynu na maszynach wirtualnych z systemem Linux na platformie Azure | Dokumenty firmy Microsoft
description: Jak rozwiązywać problemy podczas usuwania zasobów magazynu zawierających dołączone dyski wirtualne.
keywords: ''
services: virtual-machines
author: genlin
manager: dcscontentpm
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 50ab4b0f1e676ffcba0ce69ab6aa957e4c77ab88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71058155"
---
# <a name="troubleshoot-storage-resource-deletion-errors"></a>Rozwiązywanie problemów z błędami usuwania zasobów magazynu

W niektórych scenariuszach może wystąpić jeden z następujących błędów podczas próby usunięcia konta magazynu platformy Azure, kontenera lub obiektu blob we wdrożeniu usługi Azure Resource Manager:

> **Nie można usunąć konta magazynu "StorageAccountName". Błąd: Nie można usunąć konta magazynu z powodu jego artefaktów, które są używane.**
> 
> **Nie można usunąć # z #<br>container(s): vhds: Obecnie istnieje dzierżawa kontenera i nie identyfikator dzierżawy został określony w żądaniu.**
> 
> **Nie można usunąć # z #<br>obiektów blobs: BlobName.vhd: Obecnie istnieje dzierżawa obiektu blob i nie określono identyfikator dzierżawy w żądaniu.**

Dyski VHD używane w maszynach wirtualnych platformy Azure to pliki vhd przechowywane jako obiekty blob stron na koncie magazynu standardowego lub w wersji premium na platformie Azure. Aby uzyskać więcej informacji na temat dysków platformy Azure, zobacz nasze [wprowadzenie do dysków zarządzanych](../linux/managed-disks-overview.md).

Platforma Azure zapobiega usunięciu dysku dołączonego do maszyny Wirtualnej, aby zapobiec uszkodzeniu. Zapobiega również usuwanie kontenerów i kont magazynu, które mają obiekt blob strony, który jest dołączony do maszyny Wirtualnej. 

Proces usuwania konta magazynu, kontenera lub obiektu blob podczas odbierania jednego z tych błędów jest: 
1. Identyfikowanie obiektów blob dołączonych do maszyny Wirtualnej
2. [Usuwanie maszyn wirtualnych z dołączonym **dyskiem systemu operacyjnego**](#step-2-delete-vm-to-detach-os-disk)
3. [Odłączaj wszystkie **dyski danych** od pozostałych maszyn wirtualnych](#step-3-detach-data-disk-from-the-vm)

Po ponowienie próby usunięcia konta magazynu, kontenera lub obiektu blob po wykonaniu tych kroków.

## <a name="step-1-identify-blob-attached-to-a-vm"></a>Krok 1: Identyfikowanie obiektu blob dołączonego do maszyny Wirtualnej

### <a name="scenario-1-deleting-a-blob--identify-attached-vm"></a>Scenariusz 1: Usuwanie obiektu blob — identyfikowanie dołączonej maszyny Wirtualnej
1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. W menu Centrum wybierz polecenie **Wszystkie zasoby**. Przejdź do konta magazynu w obszarze **Usługa obiektów Blob** wybierz **kontenery**i przejdź do obiektu blob, aby usunąć.
3. Jeśli stan **dzierżawy** obiektu blob jest **dzierżawiony,** kliknij prawym przyciskiem myszy i wybierz polecenie **Edytuj metadane,** aby otworzyć okienko metadanych obiektów Blob. 

    ![Zrzut ekranu przedstawiający portal z wyróżnionymi obiektami blob konta Magazyn i prawym przyciskiem myszy > "Edytuj metadane"](./media/troubleshoot-vhds/utd-edit-metadata-sm.png)

4. W okienku metadanych obiektów Blob sprawdź i zarejestruj wartość **MicrosoftAzureCompute_VMName**. Ta wartość jest nazwą maszyny Wirtualnej, do których jest dołączony dysk wirtualny. (Zobacz **ważne,** jeśli to pole nie istnieje)
5. W okienku metadanych obiektów Blob sprawdź i zarejestruj wartość **MicrosoftAzureCompute_DiskType**. Ta wartość określa, czy dołączony dysk jest os lub dysk danych (Zobacz **ważne,** jeśli to pole nie istnieje). 

     ![Zrzut ekranu przedstawiający portal z otwartym okienkiem "Metadane obiektów blob"](./media/troubleshoot-vhds/utd-blob-metadata-sm.png)

6. Jeśli typem dysku obiektu blob jest **OSDisk** wykonaj [krok 2: Usuń maszynę wirtualną, aby odłączyć dysk systemu operacyjnego](#step-2-delete-vm-to-detach-os-disk). W przeciwnym razie, jeśli typem dysku obiektu blob jest **DataDisk** wykonaj kroki opisane w [kroku 3: Odłącz dysk danych od maszyny Wirtualnej](#step-3-detach-data-disk-from-the-vm). 

> [!IMPORTANT]
> Jeśli **MicrosoftAzureCompute_VMName** i **MicrosoftAzureCompute_DiskType** nie są wyświetlane w metadanych obiektu blob, oznacza to, że obiekt blob jest jawnie dzierżawiony i nie jest dołączony do maszyny Wirtualnej. Dzierżawione obiekty blob nie można usunąć bez wcześniejszego zerwania dzierżawy. Aby przerwać dzierżawę, kliknij prawym przyciskiem myszy obiekt blob i wybierz **opcję Przerwij dzierżawę**. Dzierżawione obiekty BLOB, które nie są dołączone do maszyny Wirtualnej, uniemożliwiają usunięcie obiektu blob, ale nie uniemożliwiają usunięcia konta kontenera lub magazynu.

### <a name="scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms"></a>Scenariusz 2: Usuwanie kontenera — identyfikowanie wszystkich obiektów blob w kontenerze dołączonych do maszyn wirtualnych
1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. W menu Centrum wybierz polecenie **Wszystkie zasoby**. Przejdź do konta magazynu w obszarze **Usługa obiektów Blob** wybierz **kontenery**i znajdź kontener do usunięcia.
3. Kliknij, aby otworzyć kontener, a pojawi się lista obiektów blob wewnątrz niego. Zidentyfikuj wszystkie obiekty blob z typem obiektu blob = **obiekt blob strony** i stan dzierżawy = **dzierżawione** z tej listy. Postępuj zgodnie ze scenariuszem 1, aby zidentyfikować maszynę wirtualną skojarzoną z każdym z tych obiektów blob.

    ![Zrzut ekranu przedstawiający portal z wyróżnionymi obiektami blob konta magazynu i "Stan dzierżawy" z wyróżnioną pozycją "Dzierżawiony".](./media/troubleshoot-vhds/utd-disks-sm.png)

4. Postępuj zgodnie z [krokami 2](#step-2-delete-vm-to-detach-os-disk) i [3,](#step-3-detach-data-disk-from-the-vm) aby usunąć maszyny wirtualne za pomocą **osdisk** i odłączyć **datadisk**. 

### <a name="scenario-3-deleting-storage-account---identify-all-blobs-within-storage-account-that-are-attached-to-vms"></a>Scenariusz 3: Usuwanie konta magazynu — identyfikowanie wszystkich obiektów blob w ramach konta magazynu, które są dołączone do maszyn wirtualnych
1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. W menu Centrum wybierz polecenie **Wszystkie zasoby**. Przejdź do konta magazynu w obszarze **Usługa obiektów Blob** wybierz pozycję **Blobs**.
3. W **okienku Kontenery** zidentyfikuj wszystkie kontenery, w których jest **dzierżawiony stan dzierżawy,** i postępuj zgodnie ze [scenariuszem 2](#scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms) dla każdego **dzierżawionego** kontenera. **Lease State**
4. Postępuj zgodnie z [krokami 2](#step-2-delete-vm-to-detach-os-disk) i [3,](#step-3-detach-data-disk-from-the-vm) aby usunąć maszyny wirtualne za pomocą **osdisk** i odłączyć **datadisk**. 

## <a name="step-2-delete-vm-to-detach-os-disk"></a>Krok 2: Usuwanie maszyny Wirtualnej w celu odłączenia dysku systemu operacyjnego
Jeśli dysk VHD jest dyskiem systemu operacyjnego, należy usunąć maszynę wirtualną, zanim można usunąć podłączony dysk VHD. Po wykonaniu tych kroków nie będą wymagane żadne dodatkowe działania dla dysków danych dołączonych do tej samej maszyny Wirtualnej:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. W menu Centrum wybierz polecenie **Maszyny wirtualne**.
3. Wybierz maszynę wirtualną, do których jest podłączony dysk wirtualny.
4. Upewnij się, że nic nie jest aktywnie przy użyciu maszyny wirtualnej i że nie jest już potrzebna maszyna wirtualna.
5. U góry okienka **szczegółów maszyny wirtualnej** wybierz pozycję **Usuń**, a następnie kliknij przycisk **Tak,** aby potwierdzić.
6. Maszyna wirtualna powinna zostać usunięta, ale dysk VHD może zostać zachowany. Jednak dysk VHD nie powinny być już dołączone do maszyny Wirtualnej lub dzierżawy na nim. Może upłynąć kilka minut, aby dzierżawa została zwolniona. Aby sprawdzić, czy dzierżawa jest zwolniona, przejdź do lokalizacji obiektu blob i w okienku **właściwości obiektu Blob,** **stan dzierżawy** powinien być **dostępny**.

## <a name="step-3-detach-data-disk-from-the-vm"></a>Krok 3: Odłącz dysk danych od maszyny Wirtualnej
Jeśli dysk VHD jest dyskiem danych, odłącz dysk VHD od maszyny Wirtualnej, aby usunąć dzierżawę:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. W menu Centrum wybierz polecenie **Maszyny wirtualne**.
3. Wybierz maszynę wirtualną, do których jest podłączony dysk wirtualny.
4. Wybierz **pozycję Dyski** w okienku **szczegółów maszyny wirtualnej.**
5. Wybierz dysk danych, do na który ma zostać usunięty dysk VHD. Obiekt blob jest dołączony do dysku, sprawdzając adres URL dysku VHD.
6. Lokalizację obiektu blob można zweryfikować, klikając na dysku, aby sprawdzić ścieżkę w polu **URI VHD.**
7. Wybierz **pozycję Edytuj** u góry okienka **Dyski.**
8. Kliknij **ikonę odłączyć** dysku danych, który ma zostać usunięty.

     ![Zrzut ekranu przedstawiający portal z otwartym okienkiem "Metadane obiektów blob"](./media/troubleshoot-vhds/utd-vm-disks-edit.png)

9. Wybierz **pozycję Zapisz**. Dysk jest teraz odłączony od maszyny Wirtualnej, a dysk VHD nie jest już dzierżawiony. Może upłynąć kilka minut, aby dzierżawa została zwolniona. Aby sprawdzić, czy dzierżawa została zwolniona, przejdź do lokalizacji obiektu blob i w okienku **właściwości obiektu Blob,** wartość **Status dzierżawy** powinna zostać **odblokowana** lub **dostępna**.

[Storage deletion errors in Resource Manager deployment]: #storage-delete-errors-in-rm

