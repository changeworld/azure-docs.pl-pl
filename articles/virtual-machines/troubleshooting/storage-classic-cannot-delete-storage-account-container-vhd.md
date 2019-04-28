---
title: Rozwiązywanie problemów z błędami podczas usuwania Azure klasycznych kont magazynu, kontenerów lub wirtualnych dysków twardych | Dokumentacja firmy Microsoft
description: Jak rozwiązywać problemy podczas usuwania zasobów magazynu zawierające dołączonymi dyskami VHD.
services: storage
author: AngshumanNayakMSFT
tags: top-support-issue,azure-service-management
ms.service: storage
ms.topic: troubleshooting
ms.date: 01/11/2019
ms.author: annayak
ms.openlocfilehash: 673101ad7f55969c216adf7e970402a2109f8254
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60864345"
---
# <a name="troubleshoot-classic-storage-resource-deletion-errors"></a>Rozwiązywanie problemów z błędami usuwania zasobów magazynu klasycznego
Ten artykuł zawiera wskazówki dotyczące rozwiązywania problemów, gdy wystąpi jedno z następujących błędów, w trakcie usuwania Azure klasycznego konta magazynu, kontenera lub pliku obiektów blob strony *.vhd. 


W tym artykule opisano tylko problemy związane z klasycznych zasobów magazynu. Jeśli użytkownik usunie klasycznej maszyny wirtualnej przy użyciu witryny Azure portal, programu PowerShell lub interfejsu wiersza polecenia, a następnie dyski nie są automatycznie usuwane. Użytkownik pobiera opcję można usunąć zasobu "Dysk". W przypadku, gdy opcja nie jest zaznaczona, zasób "Dysk" uniemożliwi usunięcie konta magazynu, kontenerów i *.vhd rzeczywistego pliku obiektu blob strony.

Można znaleźć więcej informacji na temat usługi Azure disks [tutaj](../../virtual-machines/windows/managed-disks-overview.md). Azure uniemożliwia usunięcie dysku, który jest dołączony do maszyny Wirtualnej w celu uniknięcia uszkodzenia. Uniemożliwia ona usunięcie kontenerów i kont magazynu, które mają stronicowych obiektów blob, który jest dołączony do maszyny Wirtualnej. 

## <a name="what-is-a-disk"></a>Co to jest "Dysk"?
Zasób "Dysk" jest używany do instalacji pliku obiektu blob strony *.vhd na maszynę wirtualną jako dysk systemu operacyjnego lub dysku z danymi. Dysk systemu operacyjnego lub zasób dysku danych, aż do usunięcia, będą nadal posiada dzierżawy w pliku *.vhd. Nie można usunąć dowolny zasób magazynu w ścieżce poniżej obrazu, jeśli wskazuje zasobu "Dysk".

![Otwórz zrzut ekranu przedstawiający portal, za pomocą okienka "Property" dysk (klasyczny)](./media/storage-classic-cannot-delete-storage-account-container-vhd/Disk_Lease_Illustration.jpg) 


## <a name="steps-while-deleting-a-classic-virtual-machine"></a>Kroki podczas usuwania klasycznej maszyny wirtualnej 
1. Usuń klasycznej maszyny wirtualnej.
2. Zaznaczenie pola wyboru "Dysków" **dzierżawy dysku** (pokazane na ilustracji powyżej) skojarzonego z stronicowych obiektów blob *.vhd został przerwany. Strona rzeczywista pliku *.vhd obiektu blob będą nadal istnieć w koncie magazynu.
![Otwórz zrzut ekranu przedstawiający portal, za pomocą okienka błąd "Delete" maszyna wirtualna (klasyczna)](./media/storage-classic-cannot-delete-storage-account-container-vhd/steps_while_deleting_classic_vm.jpg) 

3. Po dzierżawy dysków jest uszkodzona, można usunąć obiektów blob strony, sam. Usunięcie wszystkich zasobów "Dysk" jest obecny w nich można usunąć konta magazynu lub kontenera.

>[!NOTE] 
>Jeśli użytkownik usunie maszynę Wirtualną, ale nie wirtualnego dysku twardego, opłaty za magazyn będą nadal naliczane pliku *.vhd blob strony. Opłaty zostaną naliczone tworzone są typ konta magazynu, sprawdź [stronę z cennikiem](https://azure.microsoft.com/en-us/pricing/details/storage/) Aby uzyskać więcej informacji. Jeśli użytkownik nie chce już używać wirtualnymi dyskami twardymi, Usuń it/je aby uniknąć naliczania opłat w przyszłości. 

## <a name="unable-to-delete-storage-account"></a>Można usunąć konta magazynu 

Podczas próby usunięcia konta klasycznego magazynu, które nie są już potrzebne, użytkownik może zostać wyświetlony następujące zachowanie.

#### <a name="azure-portal"></a>Azure Portal 
Użytkownik przechodzi do konta magazynu klasycznego na [witryny Azure portal](https://portal.azure.com) i klika pozycję **Usuń**, użytkownik zobaczy następujący komunikat: 

Za pomocą dysków "dołączone" do maszyny wirtualnej

![Otwórz zrzut ekranu przedstawiający portal, za pomocą okienka błąd "Delete" maszyna wirtualna (klasyczna)](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_storage_account_disks_attached_portal.jpg) 


Za pomocą dysków "odłączeniu" do maszyny wirtualnej

![Otwórz zrzut ekranu przedstawiający portal, za pomocą okienka — błąd "Delete" maszyna wirtualna (klasyczna)](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_storage_account_disks_unattached_portal.jpg)


#### <a name="azure-powershell"></a>Azure PowerShell
Użytkownik próbuje usunąć konto magazynu, który jest już używany, za pomocą poleceń cmdlet programu PowerShell klasycznego. Użytkownik zobaczy następujący komunikat:

> <span style="color:cyan">**Remove-AzureStorageAccount -StorageAccountName myclassicaccount**</span>
> 
> <span style="color:red">Remove-AzureStorageAccount : BadRequest: Myclassicaccount konto magazynu ma pewne aktywne obrazy i/lub dyski, np.  
> myclassicaccount. Upewnij się, te obrazy i/lub dyski muszą zostać usunięte przed usunięciem tego konta magazynu.</span>

## <a name="unable-to-delete-storage-container"></a>Nie można usunąć kontenera magazynu

Gdy użytkownik próbuje usunąć klasycznego magazynu kontenera obiektów blob, który nie jest już potrzebny, użytkownik może zostać wyświetlony następujące zachowanie.

#### <a name="azure-portal"></a>Azure Portal 
Witryna Azure portal nie Zezwalaj użytkownikowi na usunąć kontener, jeśli istnieje dzierżawa "Dysków", wskazującego na plik *.vhd strony obiektów blob w kontenerze. Jest zgodnie z projektem, aby zapobiec przypadkowemu usunięciu pliku wirtualnymi dyskami twardymi za pomocą dzierżawy dyski na nich. 

![Otwórz zrzut ekranu przedstawiający portal, za pomocą okienka "list" kontener magazynu](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_container_portal.jpg)


#### <a name="azure-powershell"></a>Azure PowerShell
Jeśli użytkownik zdecyduje się usunąć przy użyciu programu PowerShell, spowodują następujący błąd. 

> <span style="color:cyan">**Remove-AzureStorageContainer -Context $context -Name vhds**</span>
> 
> <span style="color:red">Remove-AzureStorageContainer : Serwer zdalny zwrócił błąd: Miejsca (412) jest obecnie dostępna dzierżawa na kontenerze, a nie Identyfikatora dzierżawy została określona w żądaniu... Kod stanu HTTP: 412 — komunikat o błędzie HTTP: Obecnie jest dzierżawy w kontenerze i identyfikator dzierżawy nie został określony w żądaniu.</span>

## <a name="unable-to-delete-a-vhd"></a>Nie można usunąć wirtualnego dysku twardego 

Po usunięciu maszyny wirtualnej platformy Azure, użytkownik próbuje usunąć plik wirtualnego dysku twardego (stronicowych obiektów blob) i wyświetlony następujący komunikat:

#### <a name="azure-portal"></a>Azure Portal 
W portalu może być dwa środowiska w zależności od listy obiektów blob wybranym do usunięcia.

1. Tylko obiekty BLOB "Wydzierżawiony" są następnie przycisk usuwania nie jest wyświetlane.
![Otwórz zrzut ekranu przedstawiający portal, za pomocą okienka "list" kontenera obiektów blob](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_portal.jpg)


2. W przypadku różnych obiektów blob "Wydzierżawiony" i "Dostępne" przycisk "Usuń" pojawia się. Ale operacji "Delete" spowoduje, że za stronicowe obiekty BLOB, których dzierżawa dysku. 
![Zrzut ekranu przedstawiający portal, z otwartym okienkiem kontenera obiektów blob "list"](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_and_unleased_portal_1.jpg)
![zrzut ekranu przedstawiający portal, za pomocą wybranego obiektu blob "delete" Otwórz okienko](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_and_unleased_portal_2.jpg)

#### <a name="azure-powershell"></a>Azure PowerShell 
Jeśli użytkownik zdecyduje się usunąć przy użyciu programu PowerShell, spowodują następujący błąd. 

> <span style="color:cyan">**Remove-AzureStorageBlob -Context $context -Container vhds -Blob "classicvm-os-8698.vhd"**</span>
> 
> <span style="color:red">Remove-AzureStorageBlob : Serwer zdalny zwrócił błąd: Miejsca (412) jest obecnie dostępna dzierżawa obiektu blob, a nie Identyfikatora dzierżawy została określona w żądaniu... Kod stanu HTTP: 412 — komunikat o błędzie HTTP: Obecnie jest dzierżawy w obiekcie blob i identyfikator dzierżawy nie został określony w żądaniu.</span>


## <a name="resolution-steps"></a>Kroki rozwiązywania problemów

### <a name="to-remove-classic-disks"></a>Aby usunąć klasycznych dysków
W witrynie Azure portal, wykonaj następujące kroki:
1.  Przejdź do witryny [Azure Portal](https://portal.azure.com).
2.  Przejdź do Disks(classic). 
3.  Kliknij kartę Dyski. ![Otwórz zrzut ekranu przedstawiający portal, za pomocą okienka "list" kontenera obiektów blob](./media/storage-classic-cannot-delete-storage-account-container-vhd/resolution_click_disks_tab.jpg)
 
4.  Wybierz dysk danych, a następnie kliknij polecenie Usuń dysk.
 ![Otwórz zrzut ekranu przedstawiający portal, za pomocą okienka "list" kontenera obiektów blob](./media/storage-classic-cannot-delete-storage-account-container-vhd/resolution_click_delete_disk.jpg)
 
5.  Spróbuj ponownie wykonać operację usuwania, która poprzednio zakończyła się niepowodzeniem.
6.  Nie można usunąć konta magazynu lub kontenera, tak długo, jak długo ma jeden dysk.

### <a name="to-remove-classic-images"></a>Aby usunąć obrazów klasycznych   
W witrynie Azure portal, wykonaj następujące kroki:
1.  Przejdź do witryny [Azure Portal](https://portal.azure.com).
2.  Przejdź do obrazów systemu operacyjnego (klasyczny).
3.  Usuń obraz.
4.  Spróbuj ponownie wykonać operację usuwania, która poprzednio zakończyła się niepowodzeniem.
5.  Nie można usunąć konta magazynu lub kontenera, tak długo, jak przedstawiono w nim pojedynczego obrazu.
