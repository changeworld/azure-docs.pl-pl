---
title: Zarządzanie pojemnością magazynu w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Monitorowanie i zarządzanie Azure Stack wydajności i dostępności magazynu miejsca do magazynowania dla usługi Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: conceptual
ms.date: 03/29/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 03/19/2019
ms.openlocfilehash: e5188a7f7a1ce889c8f4340f100cfe767ff2dff8
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58629395"
---
# <a name="manage-storage-capacity-for-azure-stack"></a>Zarządzanie pojemnością magazynu dla usługi Azure Stack 

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Informacje przedstawione w tym artykule pomaga monitor — operator chmury Azure Stack i zarządzanie wydajnością magazynu, ich wdrożenia usługi Azure Stack. Infrastruktura magazynu usługi Azure Stack przydziela podzbiór całkowitej pojemności wdrożenia usługi Azure Stack służący do **usług magazynu**. Usługi magazynu przechowywanie danych dzierżaw, w udziałach na woluminach, które odnoszą się do węzłów wdrożenia.

Jako operator chmury masz ograniczoną ilość miejsca do pracy z. Ilość miejsca w magazynie jest definiowany przez rozwiązanie, które można zaimplementować. Rozwiązania dostarczane przez dostawcę OEM, korzystając z rozwiązania wielowęzłowymi lub przez sprzęt, na którym jest instalowany zestaw Azure Stack Development Kit.

Ponieważ usługi Azure Stack nie obsługuje rozszerzania pojemność magazynu, ważne jest [monitor](#monitor-shares) dostępny magazyn, aby zapewnić wysoką wydajność operacji są obsługiwane.  

Gdy pozostałe wolna pojemność udziału zostaje ograniczone, Zaplanuj [zarządzania przestrzenią](#manage-available-space) aby zapobiec udziałów wyczerpaniem pojemności.

Opcje do zarządzania pojemność:
- Odzyskaj pojemności
- Migrowanie kontenera

Gdy udział jest 100% wykorzystania magazynu usługi nie jest już funkcji dla tego udziału. Aby uzyskać pomoc w przywracania operacje dla udziału, skontaktuj się z działem pomocy technicznej firmy Microsoft.

## <a name="understand-volumes-and-shares-containers-and-disks"></a>Zrozumienie woluminy i udziały, kontenery i dyski
### <a name="volumes-and-shares"></a>Woluminów i udziałów
*Usługi storage* partycje dostępnego magazynu na oddzielnych i równe woluminów, które są przydzielane do przechowywania danych dzierżawy. Liczba woluminów jest równa liczbie węzłów w ramach wdrożenia usługi Azure Stack:

- We wdrożeniu z czterema węzłami istnieją cztery woluminy. Każdy wolumin ma pojedynczy udział. We wdrożeniu wieloma węzłami liczba akcji nie ulegnie obniżeniu, jeśli węzeł jest usunięty lub nieprawidłowo.
- Jeśli używasz usługi Azure Stack Developer Kit, istnieje pojedynczy wolumin zawierający pojedynczy udział.

Ponieważ udziały usługi storage do wyłącznego użytku usług magazynu, możesz bezpośrednio modyfikować, dodać lub usunąć wszystkie pliki w udziałach. Tylko usługi magazynu powinny działać na temat plików przechowywanych w tych woluminach.

Udziały w woluminach przechowywania danych dzierżawy. Danych dzierżawy obejmuje stronicowe obiekty BLOB, blokowe obiekty BLOB, uzupełnialnych obiektów blob, tabel, kolejek, baz danych i powiązane magazynów metadanych. Ponieważ obiektów magazynu (obiekty BLOB itp.) są indywidualnie zawarte w obrębie jednego udziału, maksymalny rozmiar każdego obiektu nie może przekraczać wielkości udziału. Maksymalny rozmiar nowych obiektów zależy od pojemności, który pozostaje w udziale jako nieużywane miejsce, gdy zostanie utworzony nowy obiekt.

Gdy udział kończy się wolne miejsce i akcje [odzyskać](#reclaim-capacity) miejsca nie są pomyślnie lub będą dostępne, operator chmury Azure Stack można migrować kontenery obiektów blob z jednego udziału na inny.

- Aby uzyskać informacje o współdziałaniu użytkownikom dzierżawy z magazynu obiektów blob w usłudze Azure Stack, zobacz [usług Azure Storage stosu](/azure/azure-stack/user/azure-stack-storage-overview#azure-stack-storage-services).


### <a name="containers"></a>Containers
Użytkownicy dzierżawy tworzyć kontenery, które są następnie używane do przechowywania danych obiektów blob. Gdy użytkownik zdecyduje się w kontenerze, który można umieścić obiekty BLOB, Usługa magazynu używa algorytmu, aby określić, na który wolumin, aby umieścić kontenera. Algorytm zazwyczaj wybiera wolumin z najwięcej miejsca.  

Po umieszczeniu obiektu blob w kontenerze, tego obiektu blob można powiększać więcej miejsca. W miarę dodawania nowych obiektów blob oraz istniejące obiekty BLOB rośnie, dostępne miejsce w woluminie, który przechowuje zmniejsza tego kontenera.  

Kontenery nie są ograniczone do jednego udziału. W przypadku danych połączonych obiektów blob w kontenerze wzrostu użycia 80% lub więcej dostępnego miejsca, kontener wprowadza *przepełnienie* trybu. W trybie przepełnienie nowe obiekty BLOB, które są tworzone w ramach tego kontenera są przydzielane na inny wolumin, który ma wystarczająco dużo miejsca. Wraz z upływem czasu kontenera w trybie przepełnienie może zawierać obiekty BLOB, które są rozmieszczone na wielu woluminach.

Gdy używany jest 80% i 90 procent dostępnego miejsca w woluminie, system zgłasza alerty w portalu administratora usługi Azure Stack. Operatorom chmury należy przejrzeć dostępnej pojemności i planujesz ponownie zrównoważyć zawartości. Usługa magazynu po przestaje działać dysk jest używany w 100%, a żadne dodatkowe alerty są zgłaszane.

### <a name="disks"></a>Dyski
Dyski maszyn wirtualnych dodawanych do kontenerów przez dzierżawców i zawierać dysk systemu operacyjnego. Maszyny wirtualne można również mieć co najmniej jeden dysk danych. Oba typy dysków przechowywane jako stronicowe obiekty BLOB. Wskazówki dotyczące dzierżawy jest umieścić każdy dysk oddzielny kontener, aby zwiększyć wydajność maszyny wirtualnej.
- Każdy kontener, który zawiera dysk (stronicowych obiektów blob) z maszyny Wirtualnej jest uważany za kontener dołączonych do maszyny Wirtualnej, która jest właścicielem dysku.
- Kontener, który nie zawiera żadnych dysków z maszyny Wirtualnej jest uważany za kontener bezpłatne.

Opcje, aby zwolnić miejsce na pojemniku dołączonych [są ograniczone](#move-vm-disks).
> [!TIP]  
> Operatorzy chmury nie bezpośrednio zarządzać dyski, które są dołączone do maszyn wirtualnych (VM), które dzierżaw może dodać do kontenera. Jednak podczas planowania zarządzania miejscem na udziałów magazynu, może być użytkowania, aby zrozumieć, jak dyski odnoszą się do kontenerów i udziałów.

## <a name="monitor-shares"></a>Monitorowanie udziałów
Użyj programu PowerShell lub portalu administracyjnego do monitorowania akcji, aby zrozumieć, kiedy ilość wolnego miejsca jest ograniczona. Korzystając z portalu, możesz otrzymywać alerty o udziałów, które jest mało miejsca.    

### <a name="use-powershell"></a>Korzystanie z programu PowerShell
Operator chmury, można monitorować pojemności udziału, przy użyciu programu PowerShell **Get AzsStorageShare** polecenia cmdlet. Polecenie cmdlet Get-AzsStorageShare zwraca łączna liczba przydzielonych i wolnego miejsca w bajtach na poszczególnych udziałów.   
![Przykład: Zwraca ilość wolnego miejsca dla akcji](media/azure-stack-manage-storage-shares/free-space.png)

- **Całkowita pojemność** jest całkowita ilość miejsca w bajtach, które są dostępne w udziale. To miejsce jest używane dla danych i metadanych, które są obsługiwane przez usługi magazynu.
- **Używana pojemność** ilość danych w bajtach, który jest używany przez wszystkie zakresy z plików, które przechowują dane dzierżawy i skojarzone metadane.

### <a name="use-the-administrator-portal"></a>Korzystanie z portalu administratora
Jako operator chmury z portalu administracyjnego służy również do wyświetlania pojemności wszystkich udziałów.

1. Zaloguj się do [portalu administracyjnego](https://adminportal.local.azurestack.external).
2. Wybierz **wszystkich usług** > **magazynu** > **udziałów plików** Aby otworzyć listę udziału pliku, gdzie można wyświetlić informacje o użyciu. 

    ![Przykład: Udziałów plików magazynu](media/azure-stack-manage-storage-shares/storage-file-shares.png)

   - **Łączna liczba** jest całkowita ilość miejsca w bajtach, które są dostępne w udziale. To miejsce jest używane dla danych i metadanych, które są obsługiwane przez usługi magazynu.
   - **UŻYWANE** ilość danych w bajtach, który jest używany przez wszystkie zakresy z plików, które przechowują dane dzierżawy i skojarzone metadane.

### <a name="storage-space-alerts"></a>Alerty miejsce magazynowania
Korzystając z portalu administratora, możesz otrzymywać alerty o udziałów, które jest mało miejsca.

> [!IMPORTANT]
> Jako operator chmury zapobiec osiągnięciu pełnej użycia udziałów. Gdy udział jest 100% wykorzystania magazynu usługi nie jest już funkcji dla tego udziału. Odzyskać wolnego miejsca i operacji w udziale, który jest w 100% wykorzystania przywracania, możesz skontaktować się pomocą techniczną firmy Microsoft.

**Ostrzeżenie**: Jeśli udział plików jest ponad 80% wykorzystania, pojawi się *ostrzeżenie* alertów w portalu administracyjnym: ![Przykład: Alert ostrzegawczy](media/azure-stack-manage-storage-shares/alert-warning.png)


**Krytyczne**: Gdy udział plików jest ponad 90% wykorzystywane, otrzymasz *krytyczny* alertów w portalu administracyjnym: ![Przykład: Alert krytyczny](media/azure-stack-manage-storage-shares/alert-critical.png)

**Wyświetl szczegóły**: W portalu administracyjnym, można otworzyć szczegóły alertu wyświetlić opcje środki zaradcze: ![Przykład: Wyświetl szczegóły alertu](media/azure-stack-manage-storage-shares/alert-details.png)


## <a name="manage-available-space"></a>Zarządzanie dostępnego miejsca
Gdy jest to konieczne w celu zwolnienia miejsca w udziale, należy najpierw użyć najmniej inwazyjne metod. Na przykład próbować odzyskać miejsce, zanim zdecydujesz się kontener.  

### <a name="reclaim-capacity"></a>Odzyskaj pojemności
*Ta opcja ma zastosowanie zarówno w przypadku wdrożeń z wieloma węzłami, jak i usługi Azure Stack Development Kit.*

Można odzyskać pojemności używane przez konta dzierżawy, które zostały usunięte. Ta pojemność to automatycznie odzyskana, gdy dane [okres przechowywania](azure-stack-manage-storage-accounts.md#set-the-retention-period) osiągnięciu lub mogą działać, aby natychmiast go odzyskać.

Aby uzyskać więcej informacji, zobacz [odzyskać pojemności](azure-stack-manage-storage-accounts.md#reclaim) w zarządzanie zasobami magazynu.

### <a name="migrate-a-container-between-volumes"></a>Migrowanie kontenera między woluminami
*Ta opcja ma zastosowanie tylko do wdrożeń z wieloma węzłami.*

Ze względu na wzorce użycia dzierżawy niektóre udziały dzierżawy za pomocą więcej miejsca niż inne. Może to spowodować udostępnioną za mało miejsca przed innych akcji, które są stosunkowo nieużywane.

Możesz spróbować wolnego miejsca na udział nadmiernego obciążenia dzięki ręcznie migracji niektórych kontenery obiektów blob do innego udziału. Kilka mniejszych kontenerów można migrować do jednego udziału, który ma pojemność do przechowywania ich wszystkich. Można użyć migracji można przenieść *bezpłatne* kontenerów. Bezpłatne kontenery są kontenery, które nie zawierają dysk dla maszyny Wirtualnej.   

Migracja konsoliduje obiektu blob kontenerów nowego udziału.

- Jeśli kontener przeszła w tryb przepełnienie i umieścił obiektów blob na dodatkowych woluminów, nowy udział musi mieć wystarczającą pojemność do przechowywania wszystkich obiektów blob w kontenerze, które można migrować. W tym obiektów blob, które znajdują się na dodatkowe udziały.

- Polecenia cmdlet programu PowerShell *Get AzsStorageContainer* identyfikuje tylko obszar używane na woluminie początkowej dla kontenera. Polecenie cmdlet nie identyfikuje miejsca używanego przez obiekty BLOB, umieść w dodatkowych woluminów. W związku z tym pełny rozmiar kontenera może nie być widoczne. Istnieje możliwość, że konsolidacji kontenerze na nowy udział może wysyłać tego nowego udziału warunek przepełnienia gdzie umieszcza dane na dodatkowe udziały. W rezultacie może być konieczne ponowne zrównoważenie udziałów ponownie.

- Jeśli nie mają uprawnień do grupy zasobów i nie można użyć programu PowerShell do wykonywania zapytań dodatkowych woluminów danych przepełnienia, pracować z właścicielem tych grup zasobów i kontenery Aby zrozumieć całkowity rozmiar danych do migracji przed przeprowadzeniem migracji tych danych.  

> [!IMPORTANT]
> Migracja obiektów blob dla kontenera jest operację w trybie offline, która wymaga użycia programu PowerShell. Zakończenie migracji wszystkich obiektów blob w kontenerze, który w przypadku migracji pozostawać w trybie offline i nie można używać. Należy również unikać uaktualnianie usługi Azure Stack, aż do zakończenia wszystkich bieżących migracji.

#### <a name="to-migrate-containers-using-powershell"></a>Aby przeprowadzić migrację kontenerów przy użyciu programu PowerShell
1. Upewnij się, że masz [programu Azure PowerShell zainstalowany i skonfigurowany](https://azure.microsoft.com/documentation/articles/powershell-install-configure/). Aby uzyskać więcej informacji, zobacz temat [Using Azure PowerShell with Azure Resource Manager](https://go.microsoft.com/fwlink/?LinkId=394767) (Używanie programu Azure PowerShell z usługą Azure Resource Manager).
2. Sprawdź kontener, aby zrozumieć, jakie dane są w udziale, który mają być poddane migracji. Aby zidentyfikować najlepsze kontenery Release candidate dla migracji w woluminie, należy użyć **Get AzsStorageContainer** polecenia cmdlet:

   ```powershell  
   $farm_name = (Get-AzsStorageFarm)[0].name
   $shares = Get-AzsStorageShare -FarmName $farm_name
   $containers = Get-AzsStorageContainer -ShareName $shares[0].ShareName -FarmName $farm_name
   ```
   Następnie sprawdź $containers:

   ```powershell
   $containers
   ```

   ![Przykład: $Containers](media/azure-stack-manage-storage-shares/containers.png)

3. Zidentyfikuj najlepsze udziałów docelowego na potrzeby przechowywania kontenerów, które można migrować:

   ```powershell
   $destinationshares = Get-AzsStorageShare -SourceShareName
   $shares[0].ShareName -Intent ContainerMigration
   ```

   Następnie sprawdź $destinationshares:

   ```powershell 
   $destinationshares
   ```

   ![Przykład: $destination udziałów](media/azure-stack-manage-storage-shares/examine-destinationshares.png)

4. Rozpoczynanie migracji dla kontenera. Migracja jest asynchroniczna. W przypadku uruchomienia migracji dodatkowe kontenery przed zakończeniem pierwszej migracji, należy użyć zadania o identyfikatorze śledzić stan każdego z nich.

   ```powershell
   $job_id = Start-AzsStorageContainerMigration -StorageAccountName $containers[0].Accountname -ContainerName $containers[0].Containername -ShareName $containers[0].Sharename -DestinationShareUncPath $destinationshares[0].UncPath -FarmName $farm_name
   ```

   Następnie zbadaj $jobId. W poniższym przykładzie Zastąp *d62f8f7a-8b46-4f59-a8aa-5db96db4ebb0* o identyfikatorze zadania, które chcesz zbadać:

   ```powershell
   $jobId
   d62f8f7a-8b46-4f59-a8aa-5db96db4ebb0
   ```

5. Aby sprawdzić stan zadania migracji, należy użyć Identyfikatora zadania. Po zakończeniu migracji kontenera **MigrationStatus** ustawiono **Complete**.

   ```powershell 
   Get-AzsStorageContainerMigrationStatus -JobId $job_id -FarmName $farm_name
   ```

   ![Przykład: Stan migracji](media/azure-stack-manage-storage-shares/migration-status1.png)

6. Możesz anulować zadanie migracji w toku. Anulowano migracji, które zadania są wykonywane asynchronicznie. Unieważnieniu można śledzić za pomocą $jobid:

   ```powershell
   Stop-AzsStorageContainerMigration -JobId $job_id -FarmName $farm_name
   ```

   ![Przykład: Stan wycofania](media/azure-stack-manage-storage-shares/rollback.png)

7. Można uruchomić polecenie z kroku 6 ponownie, dopóki stan potwierdza to zadanie migracji **anulowane**:  

    ![Przykład: Stan anulowania](media/azure-stack-manage-storage-shares/cancelled.png)

### <a name="move-vm-disks"></a>Przenieś dyski maszyny Wirtualnej
*Ta opcja ma zastosowanie tylko do wdrożeń z wieloma węzłami.*

Najbardziej skrajne metody do zarządzania przestrzenią wymaga przenoszenia dysków maszyny wirtualnej. Przenoszenie kontenera dołączone, (jeden, który zawiera dysk maszyny Wirtualnej) jest złożone, skontaktuj się z Microsoft Support do wykonania tej akcji.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [oferty maszyny wirtualne do użytkowników](azure-stack-tutorial-tenant-vm.md).
