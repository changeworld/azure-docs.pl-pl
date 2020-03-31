---
title: Rozwiązywanie problemów z błędami podczas usuwania klasycznych kont magazynu platformy Azure, kontenerów lub dysków VHD | Dokumenty firmy Microsoft
description: Jak rozwiązywać problemy podczas usuwania zasobów magazynu zawierających dołączone dyski wirtualne.
services: storage
author: AngshumanNayakMSFT
tags: top-support-issue,azure-service-management
ms.service: storage
ms.topic: troubleshooting
ms.date: 01/11/2019
ms.author: annayak
ms.openlocfilehash: 95c85309058911d6767eb44efd7b37ddac7a9119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77915041"
---
# <a name="troubleshoot-classic-storage-resource-deletion-errors"></a>Rozwiązywanie problemów z błędami usuwania zasobów magazynu klasycznego
Ten artykuł zawiera wskazówki dotyczące rozwiązywania problemów, gdy wystąpi jeden z następujących błędów podczas próby usunięcia konta klasycznego magazynu platformy Azure, kontenera lub pliku obiektu blob strony *.vhd. 


W tym artykule opisano tylko problemy z klasycznymi zasobami magazynu. Jeśli użytkownik usunie klasyczną maszynę wirtualną przy użyciu portalu Azure Portal, Programu PowerShell lub interfejsu wiersza polecenia, dyski nie są automatycznie usuwane. Użytkownik otrzymuje opcję usunięcia zasobu "Dysk". W przypadku, gdy opcja nie jest zaznaczona, zasób "Dysk" uniemożliwi usunięcie konta magazynu, kontenera i rzeczywistego pliku obiektu blob strony *.vhd.

Więcej informacji na temat dysków platformy Azure można znaleźć [tutaj](../../virtual-machines/windows/managed-disks-overview.md). Platforma Azure zapobiega usunięciu dysku dołączonego do maszyny Wirtualnej, aby zapobiec uszkodzeniu. Zapobiega również usuwanie kontenerów i kont magazynu, które mają obiekt blob strony, który jest dołączony do maszyny Wirtualnej. 

## <a name="what-is-a-disk"></a>Co to jest "Dysk"?
Zasób "Dysk" służy do instalowania pliku obiektów blob strony *.vhd na maszynie wirtualnej jako dysk systemu operacyjnego lub dysk danych. Dysk systemu operacyjnego lub zasób dysku danych, dopóki nie zostanie usunięty, będzie nadal utrzymywać dzierżawę pliku *.vhd. Nie można usunąć żadnego zasobu magazynu w ścieżce pokazanej na poniższym obrazie, jeśli wskazuje na niego zasób "Dysk".

![Zrzut ekranu przedstawiający portal z otwartym okienkiem "Właściwość" (klasyczny)](./media/storage-classic-cannot-delete-storage-account-container-vhd/Disk_Lease_Illustration.jpg) 


## <a name="steps-while-deleting-a-classic-virtual-machine"></a>Kroki podczas usuwania klasycznej maszyny wirtualnej 

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


1. Usuń klasyczną maszynę wirtualną.
2. Jeśli zaznaczone jest pole wyboru "Dyski", **dzierżawa dysku** (pokazana na obrazku powyżej) skojarzona ze stroną blob *.vhd jest przerwana. Rzeczywisty plik obiektu blob *.vhd strony nadal będzie istnieć na koncie magazynu.
![Zrzut ekranu przedstawiający portal z otwartym okienkiem błędu "Usuń" maszyny wirtualnej (klasycznej) "Usuń"](./media/storage-classic-cannot-delete-storage-account-container-vhd/steps_while_deleting_classic_vm.jpg) 

3. Po przerwaniu dzierżawy dysku można usunąć same obiekty blob strony. Konto magazynu lub kontener można usunąć po usunięciu wszystkich zasobów "Dysk" znajdujących się w nich.

>[!NOTE] 
>Jeśli użytkownik usunie maszynę wirtualną, ale nie vHD, opłaty za magazyn będzie nadal naliczane na stronie pliku blob *.vhd. Opłaty będą zgodne z typem konta magazynu, sprawdź [stronę cennika, aby](https://azure.microsoft.com/pricing/details/storage/) uzyskać więcej szczegółów. Jeśli użytkownik nie zamierza już korzystać z dysków WIRTUALNYCH, usuń je, aby uniknąć przyszłych opłat. 

## <a name="unable-to-delete-storage-account"></a>Nie można usunąć konta magazynu 

Gdy użytkownik próbuje usunąć klasyczne konto magazynu, które nie jest już potrzebne, użytkownik może zobaczyć następujące zachowanie.

#### <a name="azure-portal"></a>Portal Azure 
Użytkownik przechodzi do klasycznego konta magazynu w [witrynie Azure portal](https://portal.azure.com) i klika **usuń**, użytkownik zobaczy następujący komunikat: 

Z dyskami "podłączonymi" do maszyny wirtualnej

![Zrzut ekranu przedstawiający portal z otwartym okienkiem błędu "Usuń" maszyny wirtualnej (klasycznej) "Usuń"](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_storage_account_disks_attached_portal.jpg) 


Z dyskami "niepodłączonymi" do maszyny wirtualnej

![Zrzut ekranu przedstawiający portal z otwartym okienkiem "Delete" "Delete" "Delete"](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_storage_account_disks_unattached_portal.jpg)


#### <a name="azure-powershell"></a>Azure PowerShell
Użytkownik próbuje usunąć konto magazynu, które nie jest już używane, przy użyciu klasycznych poleceń cmdlet programu PowerShell. Użytkownik zobaczy następujący komunikat:

> <span style="color:cyan">**Usuń konto AzureStorageAccount -StorageAccountName myclassicaccount**</span>
> 
> <span style="color:red">Usuń AzureStorageAccount: BadRequest: Konto magazynu myclassicaccount ma aktywne obrazy i/lub dyski, np.  
> myclassicaccount. Przed usunięciem tego konta magazynu upewnij się, że te obrazy i/lub dyski zostaną usunięte.</span>

## <a name="unable-to-delete-storage-container"></a>Nie można usunąć kontenera magazynu

Gdy użytkownik próbuje usunąć klasyczny kontener obiektów blob magazynu, który nie jest już potrzebny, użytkownik może zobaczyć następujące zachowanie.

#### <a name="azure-portal"></a>Portal Azure 
Usługa Azure portal nie zezwala użytkownikowi na usunięcie kontenera, jeśli istnieje dzierżawa "Dyski) wskazujące plik obiektu blob strony *.vhd w kontenerze. To według projektu, aby zapobiec przypadkowemu usunięciu pliku vhd (s) z dysku (s) dzierżawy na nich. 

![Zrzut ekranu przedstawiający portal z otwartym okienkiem "lista" kontenera magazynu](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_container_portal.jpg)


#### <a name="azure-powershell"></a>Azure PowerShell
Jeśli użytkownik zdecyduje się usunąć za pomocą programu PowerShell, spowoduje to następujący błąd. 

> <span style="color:cyan">**Usuń-AzureStorageContainer -Context $context -Name vhds**</span>
> 
> <span style="color:red">Remove-AzureStorageContainer: Serwer zdalny zwrócił błąd: (412) Obecnie istnieje dzierżawa kontenera i nie określono identyfikatora dzierżawy w żądaniu.. Kod stanu HTTP: 412 - Komunikat o błędzie HTTP: Obecnie istnieje dzierżawa kontenera i w żądaniu nie określono identyfikatora dzierżawy.</span>

## <a name="unable-to-delete-a-vhd"></a>Nie można usunąć vhd 

Po usunięciu maszyny wirtualnej platformy Azure użytkownik próbuje usunąć plik vhd (obiekt blob strony) i otrzymać poniższy komunikat:

#### <a name="azure-portal"></a>Portal Azure 
W portalu mogą istnieć dwa środowiska w zależności od listy obiektów blob wybranych do usunięcia.

1. Jeśli zaznaczone są tylko obiekty blob "Dzierżawiony", przycisk Usuń nie jest pokazywał się.
![Zrzut ekranu przedstawiający portal z otwartym okienkiem "lista" obiektu blob kontenera](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_portal.jpg)


2. Jeśli wybrano kombinację obiektów blob "Dzierżawiony" i "Dostępny", pojawi się przycisk "Usuń". Ale operacja "Usuń" pozostawi po sobie stronicowe obiekty blob, które mają dzierżawę dysku na nich. 
![Zrzut ekranu przedstawiający portal z otwartym](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_and_unleased_portal_1.jpg)
![okienkiem "lista" obiektu blob kontenera Zrzut ekranu z portalu z otwartym wybranym okienkiem "usuń" obiektu blob](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_and_unleased_portal_2.jpg)

#### <a name="azure-powershell"></a>Azure PowerShell 
Jeśli użytkownik zdecyduje się usunąć za pomocą programu PowerShell, spowoduje to następujący błąd. 

> <span style="color:cyan">**Usuń-AzureStorageBlob -Context $context -Container vhds -Blob "classicvm-os-8698.vhd"**</span>
> 
> <span style="color:red">Remove-AzureStorageBlob: Serwer zdalny zwrócił błąd: (412) Obecnie istnieje dzierżawa obiektu blob i nie określono identyfikatora dzierżawy w żądaniu.. Kod stanu HTTP: 412 - Komunikat o błędzie HTTP: Obecnie istnieje dzierżawa obiektu blob i w żądaniu nie określono identyfikatora dzierżawy.</span>


## <a name="resolution-steps"></a>Kroki rozwiązywania problemów

### <a name="to-remove-classic-disks"></a>Aby usunąć klasyczne dyski
Wykonaj następujące kroki w witrynie Azure portal:
1.  Przejdź do [witryny Azure portal](https://portal.azure.com).
2.  Przejdź do dysków (klasycznych). 
3.  Kliknij kartę Dyski. ![Zrzut ekranu przedstawiający portal z otwartym okienkiem "lista" obiektu blob kontenera](./media/storage-classic-cannot-delete-storage-account-container-vhd/resolution_click_disks_tab.jpg)
 
4.  Wybierz dysk danych, a następnie kliknij polecenie Usuń dysk.
 ![Zrzut ekranu przedstawiający portal z otwartym okienkiem "lista" obiektu blob kontenera](./media/storage-classic-cannot-delete-storage-account-container-vhd/resolution_click_delete_disk.jpg)
 
5.  Ponów próbę operacji Delete, która wcześniej nie powiodła się.
6.  Nie można usunąć konta magazynu lub kontenera, o ile ma jeden dysk.

### <a name="to-remove-classic-images"></a>Aby usunąć klasyczne obrazy   
Wykonaj następujące kroki w witrynie Azure portal:
1.  Przejdź do [witryny Azure portal](https://portal.azure.com).
2.  Przejdź do obrazów systemu operacyjnego (klasyczny).
3.  Usuń obraz.
4.  Ponów próbę operacji Delete, która wcześniej nie powiodła się.
5.  Nie można usunąć konta magazynu lub kontenera, o ile ma on jeden obraz.
