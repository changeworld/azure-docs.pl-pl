---
title: Zarządzanie pojemność magazynu w stosie Azure | Dokumentacja firmy Microsoft
description: Monitorowanie i zarządzanie nimi dostępnego miejsca na stosie Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: b0e694e4-3575-424c-afda-7d48c2025a62
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: get-started-article
ms.date: 05/10/2018
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.openlocfilehash: da6bb00d7538c1a26e1ed4be29d3c882aa378e9e
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2018
---
# <a name="manage-storage-capacity-for-azure-stack"></a>Zarządzanie wydajnością magazynu Azure stosu

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Informacje przedstawione w tym artykule pomaga monitor operator chmury Azure stosu i zarządzaj nimi pojemności ich wdrożenia stosu Azure. Infrastruktura magazynu Azure stosu przydziela podzbiór całkowitej pojemności wdrożenia stosu Azure używanego do **usług magazynu**. Usługi magazynu przechowywania danych dzierżawcy w udziałach na woluminach, które odpowiadają węzłów wdrożenia.

Jako operatora chmury masz ograniczoną ilość miejsca w magazynie do pracy z. Ilość miejsca w magazynie jest zdefiniowany przez rozwiązanie, które można zaimplementować. Rozwiązania jest dostarczany przez dostawcę OEM, korzystając z wieloma węzłami rozwiązania lub sprzęt, na którym jest instalowany Azure stosu Development Kit.

Ponieważ stos Azure nie obsługuje rozszerzania pojemności, ważne jest, aby [monitor](#monitor-shares) dostępny magazyn w celu zapewnienia skutecznego operacje, które są obsługiwane.  

Gdy pozostałe wolna pojemność udziału staje się ograniczone, Zaplanuj [zarządzania przestrzenią](#manage-available-space) aby zapobiec akcji wyczerpaniu pojemności.

Opcje zarządzania pojemności obejmują:
- Odzyskiwania pojemności
- Migrowanie kontenera

Jeśli udział jest 100% wykorzystania magazynu usługi nie jest już funkcje dla tego udziału. Aby uzyskać pomoc w przywracaniu operacje dla udziału, skontaktuj się z pomocą techniczną firmy Microsoft.

## <a name="understand-volumes-and-shares-containers-and-disks"></a>Woluminy i udziały, kontenery i dysków
### <a name="volumes-and-shares"></a>Woluminów i udziałów
*Usługi magazynu* partycje dostępny magazyn do oddzielnych i równy woluminów przydzielonych do przechowywania danych dzierżawy. Z liczbą woluminów jest równa liczbie węzłów wdrożenia stosu Azure:

- W ramach wdrożenia z czterema węzłami istnieją cztery woluminy. Każdy wolumin ma jednego udziału. W ramach wdrożenia z wieloma węzłami liczbę akcji nie ograniczono Jeśli węzeł jest usunięty lub nieprawidłowo.
- Jeśli używasz zestaw dewelopera stosu Azure istnieje pojedynczy wolumin zawierający pojedynczy udział.

Ponieważ udziałów usługi magazynu do wyłącznego użytku usług magazynu, możesz bezpośrednio zmodyfikować, dodać lub usunąć wszystkie pliki w udziałach. Tylko usługi magazynowania powinny działać na plików przechowywanych w tych woluminów.

Udziały na woluminach przechowywania danych dzierżawy. Dane dzierżawy zawiera stronicowe obiekty BLOB, blokowe obiekty BLOB, Dołącz obiektów blob, tabel, kolejek, baz danych i powiązane magazyny metadanych. Ponieważ obiektów magazynu (obiekty BLOB itp.) są indywidualnie zawarty w pojedynczy udział, maksymalny rozmiar każdego obiektu nie może przekraczać rozmiaru udziału. Maksymalna liczba nowych obiektów zależy od możliwości, które pozostało w udziale jako nieużywane miejsce podczas tworzenia nowego obiektu.

Jeśli udział kończy się wolne miejsce oraz akcji do [odzyskać](#reclaim-capacity) miejsca nie są pomyślnie lub dostępne, można operatorowi chmury Azure stosu [migracji](#migrate-a-container-between) kontenerów obiektów blob z jednego udziału na inny.

- Aby uzyskać więcej informacji na temat kontenerów i obiektów blob, zobacz [magazynu obiektów Blob](azure-stack-key-features.md#blob-storage) w funkcji klucza i pojęcia dotyczące stosu Azure.
- Aby uzyskać informacje dotyczące działania użytkowników dzierżawy z magazynem obiektów blob Azure stosu, zobacz [usług Azure stosu magazynu](/azure/azure-stack/user/azure-stack-storage-overview#azure-stack-storage-services).


### <a name="containers"></a>Containers
Użytkownicy dzierżawy tworzyć kontenerów, które są następnie używane do przechowywania danych obiektów blob. Gdy użytkownik zdecyduje się w kontenerze, który można umieścić obiekty BLOB, Usługa magazynu używa nieobsługiwanego algorytmu ustalenie, na którym woluminie, aby umieścić kontenera. Algorytm zazwyczaj wybiera kreski najwięcej miejsca.  

Po umieszczeniu w kontenerze obiektu blob, tego obiektu blob może zwiększyć więcej miejsca. Jak dodać nowe obiekty BLOB i istniejące obiekty BLOB rosnąć, dostępne miejsce na woluminie, która przetrzymuje zmniejsza tego kontenera.  

Kontenery nie są ograniczone do jednego udziału. Gdy danych połączonych obiektów blob w kontenerze rozwoju Użyj 80% lub więcej dostępnego miejsca, wprowadza kontenera *przepełnienie* tryb. W trybie przepełnienia, nowe obiekty BLOB, które są tworzone w tym kontenerze są przydzielane na inny wolumin, który ma wystarczającą ilość miejsca. Wraz z upływem czasu kontenera w trybie przepełnienie mogą mieć obiektów blob, które są rozproszone na wielu woluminach.

W przypadku 80%, a następnie 90% miejsca dostępnego na woluminie systemu zgłasza alerty w portalu administratora platformy Azure stosu. Operatorzy chmury powinien przejrzeć dostępnej pojemności i zaplanować rebalance zawartości. Usługa magazynu zatrzymuje, praca, gdy dysk jest używany w 100% i żadne dodatkowe alerty są zgłaszane.

### <a name="disks"></a>Dyski
Dyski maszyny Wirtualnej są dodawane do kontenerów przez dzierżawców i dołączyć dysku systemu operacyjnego. Maszyny wirtualne mogą mieć także co najmniej jeden dysk danych. Oba typy dysków są przechowywane jako stronicowych obiektów blob. Wskazówki dotyczące dzierżawcy jest umieszczenie każdego dysku w oddzielnych kontenera, aby poprawić wydajność maszyny wirtualnej.
- Każdy kontener, który zawiera dysku (stronicowych obiektów blob) z maszyny Wirtualnej jest traktowany jako kontener dołączonych do maszyny Wirtualnej, który jest właścicielem dysku.
- Kontener, który nie zawiera żadnych dysku od maszyny Wirtualnej jest traktowany jako wolne kontenera.

Opcje celu zwolnienia miejsca w kontenerze dołączonych [są ograniczone](#move-vm-disks).
> [!TIP]  
> Operatorzy chmury nie bezpośrednio zarządzać dyski, które są dołączone do maszyn wirtualnych (VM), które dzierżawcy może dodać do kontenera. Jednak podczas planowania zarządzania miejscem na udziałów magazynu, może być użytkowania, aby zrozumieć, jak dyski odnoszą się do kontenerów i udziałów.

## <a name="monitor-shares"></a>Udziały monitora
Użyj programu PowerShell lub portalu administracyjnego monitorowania udziałów, więc można zrozumieć, kiedy ilość wolnego miejsca jest ograniczona. Korzystając z portalu, możesz otrzymywać alerty o akcji, które jest mało miejsca.    

### <a name="use-powershell"></a>Korzystanie z programu PowerShell
Jako operatora chmury, można monitorować pojemność magazynu przy użyciu programu PowerShell udziału **Get-AzsStorageShare** polecenia cmdlet. Polecenie cmdlet Get-AzsStorageShare zwraca całkowitą, przydzielone i wolnego miejsca w bajtach na poszczególnych udziałów.   
![Przykład: Zwraca ilość wolnego miejsca dla akcji](media/azure-stack-manage-storage-shares/free-space.png)

- **Łączna pojemność** jest całkowita ilość miejsca w bajtach, które są dostępne w udziale. Ten obszar służy do danych i metadanych, które są obsługiwane przez usługi magazynu.
- **Używane pojemności** ilość danych w bajtach, który jest używany przez wszystkie zakresy z plików, których są przechowywane dane dzierżawy i skojarzone metadane.

### <a name="use-the-administrator-portal"></a>Korzystanie z portalu administratora
Jako operatora chmury portalu administracyjnego służy do wyświetlania pojemności wszystkich udziałów. **Przejdź do magazynu** > **udziałów plików** aby otworzyć listę udziału pliku, której można wyświetlić informacji o użyciu.
![Przykład: Udziałów plików magazynu](media/azure-stack-manage-storage-shares/storage-file-shares.png)
- **Całkowita liczba** jest całkowita ilość miejsca w bajtach, które są dostępne w udziale. Ten obszar służy do danych i metadanych, które są obsługiwane przez usługi magazynu.
- **UŻYWANE** ilość danych w bajtach, który jest używany przez wszystkie zakresy z plików, których są przechowywane dane dzierżawy i skojarzone metadane.

### <a name="storage-space-alerts"></a>Alerty miejsca magazynu
Korzystając z portalu administratora, możesz otrzymywać alerty o akcji, które jest mało miejsca.

> [!IMPORTANT]
> Jako operatora chmury zapobiec osiągnięciu pełnej użycie udziałów. Jeśli udział jest 100% wykorzystania magazynu usługi nie jest już funkcje dla tego udziału. Aby odzyskać wolnego miejsca i przywrócić operacje na udziale, który jest wykorzystywany w 100%, należy skontaktować się pomocy technicznej firmy Microsoft.

**Ostrzeżenie**: udział plików jest ponad 80% wykorzystania, pojawi się *ostrzeżenie* alertów w portalu administracyjnym: ![przykład: ostrzeżenie](media/azure-stack-manage-storage-shares/alert-warning.png)


**Krytyczne**: udział plików jest ponad 90% wykorzystania, pojawi się *krytyczne* alertów w portalu administracyjnym: ![przykład: alert krytyczny](media/azure-stack-manage-storage-shares/alert-critical.png)

**Wyświetl szczegóły**: W portalu administracyjnym można otworzyć szczegółów alertu wyświetlić opcje środki zaradcze: ![przykład: wyświetlanie szczegółów alertów](media/azure-stack-manage-storage-shares/alert-details.png)


## <a name="manage-available-space"></a>Zarządzanie wolnego miejsca
Gdy jest to niezbędne w celu zwolnienia miejsca w udziale, najpierw użyj metody najmniej inwazyjne. Na przykład próbować odzyskać miejsce, zanim wybierzesz opcję wykonania migracji kontenera.  

### <a name="reclaim-capacity"></a>Odzyskiwania pojemności
*Ta opcja ma zastosowanie do wdrożeń z wieloma węzłami i Azure stosu Development Kit.*

Można odzyskać pojemności używanych przez konta dzierżawy, które zostały usunięte. Zdolność ta jest automatycznie odzyskać, gdy dane [okres przechowywania](azure-stack-manage-storage-accounts.md#set-the-retention-period) osiągnięciu lub może działać na natychmiast go odzyskać.

Aby uzyskać więcej informacji, zobacz [odzyskiwania pojemności](azure-stack-manage-storage-accounts.md#reclaim) w zarządzanie zasobami magazynu.

### <a name="migrate-a-container-between-volumes"></a>Migrowanie między woluminami kontenera
*Ta opcja ma zastosowanie tylko do wdrożeń z wieloma węzłami.*

Z powodu wzorców użycia dzierżawy udziały dzierżawy używać więcej miejsca niż inne. Może to spowodować powstanie udziale, który zaczyna brakować miejsca przed innymi udziałów, które są stosunkowo nieużywane.

Możesz spróbować wolnego miejsca na udział nadmiernego obciążenia za pomocą ręcznie migracji niektórych kontenerów obiektów blob do innego udziału. Pojedynczy udział, który ma pojemność do przechowywania wszystkich można migrować kilka mniejszych kontenerów. Migracji można użyć do przeniesienia *wolnego* kontenerów. Kontenery wolnego są kontenerów, które nie zawierają dysku dla maszyny Wirtualnej.   

Migracja konsoliduje obiektu blob kontenery nowego udziału.

- Jeśli kontener został przełączony w tryb przepełnienie i umieścił obiekty BLOB na dodatkowych woluminów, nowy udział musi mieć wystarczającą pojemność do przechowywania wszystkich obiektów blob do kontenera, które można migrować. W tym obiektów blob, które znajdują się na dodatkowe udziały.

- Polecenia cmdlet programu PowerShell *Get AzsStorageContainer* identyfikuje tylko zajętego miejsca na na woluminie początkowej kontenera. Polecenie cmdlet nie będzie rozpoznawał miejsca, który jest używany przez obiekty BLOB, zaznacz pole obok dodatkowych woluminów. W związku z tym pełnego rozmiaru kontenera nie może być widoczne. Istnieje możliwość, że konsolidacji kontenera na nowy udział przesyłania tego nowego udziału na nastąpiło przepełnienie gdzie umieszczane dane na dodatkowe udziały. W związku z tym może być konieczne ponowne zrównoważenie udziałów ponownie.

- Jeśli brakuje uprawnień do grupy zasobów i nie można użyć programu PowerShell do badania dodatkowych woluminów danych przepełnienia, współpracować z właścicielem tych grup zasobów i kontenery zrozumieć całkowity rozmiar danych do migracji przed przeprowadzeniem migracji danych.  

> [!IMPORTANT]
> Migracja obiektów blob kontenera jest operację w trybie offline, która wymaga użycia programu PowerShell. Dopiero po zakończeniu migracji, wszystkie obiekty BLOB do kontenera, który jest przeprowadzana migracja pozostaną w trybie offline i nie można użyć. Należy również unikać uaktualniania stosu Azure do momentu ukończenia wszystkich bieżących migracji.

#### <a name="to-migrate-containers-using-powershell"></a>Aby przeprowadzić migrację kontenerów przy użyciu programu PowerShell
1. Upewnij się, że masz [programu Azure PowerShell zainstalowany i skonfigurowany](http://azure.microsoft.com/documentation/articles/powershell-install-configure/). Aby uzyskać więcej informacji, zobacz temat [Using Azure PowerShell with Azure Resource Manager](http://go.microsoft.com/fwlink/?LinkId=394767) (Używanie programu Azure PowerShell z usługą Azure Resource Manager).
2.  Sprawdź, czy kontener, aby zrozumieć, jakie dane znajdują się w udziale, które mają zostać poddane migracji. Aby zidentyfikować najlepsze kontenery candidate dla migracji w woluminie, użyj **Get-AzsStorageContainer** polecenia cmdlet:

    ````PowerShell  
    $farm_name = (Get-AzsStorageFarm)[0].name
    $shares = Get-AzsStorageShare -FarmName $farm_name
    $containers = Get-AzsStorageContainer -ShareName $shares[0].ShareName -FarmName $farm_name
    ````
    Następnie sprawdź $containers:

    ````PowerShell
    $containers
    ````

    ![Przykład: $Containers](media/azure-stack-manage-storage-shares/containers.png)

3.  Zidentyfikuj najlepsze udziałów docelowego do przechowywania kontenera, które można migrować:

    ````PowerShell
    $destinationshares = Get-AzsStorageShare -SourceShareName
    $shares[0].ShareName -Intent ContainerMigration
    ````

    Następnie sprawdź $destinationshares:

    "" $Destinationshares środowiska PowerShell
    ````

    ![Example: $destination shares](media/azure-stack-manage-storage-shares/examine-destinationshares.png)

4. Start migration for a container. Migration is asynchronous. If you start migration of additional containers before the first migration completes, use the job id to track the status of each.

  ````PowerShell
  $job_id = Start-AzsStorageContainerMigration -StorageAccountName $containers[0].Accountname -ContainerName $containers[0].Containername -ShareName $containers[0].Sharename -DestinationShareUncPath $destinationshares[0].UncPath -FarmName $farm_name
  ````

  Obejrzyj $jobId. W poniższym przykładzie Zastąp *d62f8f7a-8b46-4f59-a8aa-5db96db4ebb0* z identyfikatorem zadania do sprawdzenia:

  ````PowerShell
  $jobId
  d62f8f7a-8b46-4f59-a8aa-5db96db4ebb0
  ````

5. Identyfikator zadania umożliwia sprawdzenie stanu zadania migracji. Po zakończeniu migracji kontenera, **MigrationStatus** ustawiono **Complete**.

  ````PowerShell 
  Get-AzsStorageContainerMigrationStatus -JobId $job_id -FarmName $farm_name
  ````

  ![Przykład: Migracji stanu](media/azure-stack-manage-storage-shares/migration-status1.png)

6.  Możesz anulować zadanie migracji w toku. Anulowane migracji, które zadania są wykonywane asynchronicznie. Anulowanie można śledzić za pomocą $jobid:

  ````PowerShell
  Stop-AzsStorageContainerMigration -JobId $job_id -FarmName $farm_name
  ````

  ![Przykład: Stan wycofywania](media/azure-stack-manage-storage-shares/rollback.png)

7. Można uruchomić polecenie z kroku 6 ponownie, dopóki stan potwierdza zadania migracji jest **anulowane**:  

    ![Przykład: Stan anulowania](media/azure-stack-manage-storage-shares/cancelled.png)

### <a name="move-vm-disks"></a>Przenoszenie dysków maszyny Wirtualnej
*Ta opcja ma zastosowanie tylko do wdrożeń z wieloma węzłami.*

Najbardziej skrajne metody do zarządzania przestrzenią obejmuje przenoszenie dysków maszyny wirtualnej. Ponieważ przenoszenie dołączone kontenera (taki, który zawiera dysk maszyny Wirtualnej) jest złożony, skontaktuj się z Microsoft Support do wykonania tej akcji.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [oferty maszyn wirtualnych dla użytkowników](azure-stack-tutorial-tenant-vm.md).
