---
title: Przygotowywanie maszyn wirtualnych VMware z systemem Linux dla migracji na platformę Azure za pomocą usługi Azure Migration migracji serwera | Dokumentacja firmy Microsoft
description: Opisuje, jak przygotować Maszynę wirtualną systemu Linux do migracji na platformę Azure za pomocą usługi Azure Migration migracji serwera
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/14/2018
ms.author: raynew
ms.openlocfilehash: efc410699ef6f4722857c812b38473c8f54b911b
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811793"
---
# <a name="prepare-linux-vmware-vms-for-migration-to-azure"></a>Przygotowywanie maszyn wirtualnych VMware z systemem Linux do migracji na platformę Azure 

W tym artykule opisano jak przygotować maszyny wirtualne programu VMware z systemem Linux, jeśli chcesz przeprowadzić ich migrację do platformy Azure za pomocą [usługi Azure Migrate](migrate-overview.md). 

> [!NOTE]
> Migrowanie serwera migracji na platformę Azure jest obecnie w publicznej wersji zapoznawczej. Istniejąca wersja GA usługi Azure Migrate umożliwia odnajdywanie i ocenianie maszyn wirtualnych do migracji, ale rzeczywistej migracji nie jest obsługiwane w istniejącej wersji Ogólnodostępnej.

Przygotowywanie maszyn z systemem Linux w następujący sposób:

1. Zainstaluj usługi integracji funkcji Hyper-V w systemie Linux. Nowsze wersje są dystrybucje systemu Linux może być to instalowany domyślnie).
2. Tak, aby zawierała niezbędne sterowniki funkcji Hyper-V, a więc, że uruchomieniu maszyny Wirtualnej na platformie Azure (wymagane dla niektórych dystrybucji), należy ponownie skompilować obraz init systemu Linux.
3. Włącz rejestrowanie konsoli szeregowej do rozwiązywania problemów. [Dowiedz się więcej](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console).
4. Zaktualizuj plik mapy urządzenia przy użyciu nazwy urządzenia i skojarzenia woluminów, używać identyfikatorów urządzeń trwałe.
5. Zaktualizuj wpisy fstab, aby używać identyfikatorów trwały wolumin.
6. Usuń wszystkie reguły udev, które zarezerwowania nazwy interfejsu, na podstawie adresu MAC itp.
7. Aktualizacja interfejsy sieciowe do odbierania adresy IP protokołu DHCP.
8. Upewnij się, ssh jest włączony. Upewnij się, że sshd usługa ma ustawione do automatycznego uruchamiania podczas ponownego uruchamiania komputera.
9. Upewnij się, czy przychodzące ssh żądań połączenia nie są zablokowane przez zaporę systemu operacyjnego lub reguły tabeli adresów IP.

[Dowiedz się więcej](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console) o wprowadzenie tych zmian na najbardziej popularnych dystrybucji systemu Linux.

## <a name="sample-script"></a>Przykładowy skrypt

Ten skrypt (przygotowanie for-azure.sh) zawiera przykładowy do przygotowania maszyn z systemem Linux. Skrypt może nie działać dla wszystkich dystrybucjach i środowiska, ale to dobry punkt wyjścia.

Pokazuje skrypt jak: 

- Ponowne generowanie obrazu init systemu Linux przy użyciu niezbędne sterowniki w razie potrzeby.
- Zaktualizuj wpisy fstab, aby używać identyfikatorów trwały wolumin.
- Przekierowywanie dzienników konsoli do portu szeregowego.
- Włącz konsoli szeregowej dostępu na platformie Azure
- Usuń reguły netto usługi udev
- Wstrzyknąć wykonywania skryptu rozruchowy, który jest uruchamiany, gdy maszyna wirtualna zostanie uruchomiona. Sprawdza, czy komputer działa na platformie Azure. Jeśli tak jest, zaktualizuje konfigurację sieci na maszynie Wirtualnej i ustawia pierwszy interfejs sieci ethernet do używania protokołu DHCP w celu uzyskania adresu IP.

### <a name="before-you-start"></a>Przed rozpoczęciem

- Przykładowy skrypt zawiera przykładowe kroki. Nie można uruchomić w systemach produkcyjnych. Można go uszkodzić lub uszkodzenie maszyny Wirtualnej, na którym jest uruchomiony.
- Zaleca się, że uruchomieniu na testowej maszynie Wirtualnej. Przed rozpoczęciem należy wykonać kopii zapasowej maszyny Wirtualnej lub migawki, aby w razie potrzeby można przywrócić maszynę Wirtualną. 
- Działania skryptu, gdy maszyna wirtualna jest uruchomiona jedna z tych dystrybucje systemu Linux:
    - Red Hat Enterprise Linux 6.5+, 7.1+
    - System operacyjny centów 6.5 + 7.1 +
    - SUSE Linux Enterprise Server 12 SP1,SP2, SP3
    - Ubuntu 14.04, 16.04, 18.04
    - Debian 7, 8

### <a name="run-the-script"></a>Uruchamianie skryptu

1. Kopiuj skrypt do systemu Linux testowania maszyny Wirtualnej przy użyciu klienta punktu połączenia usługi, takie jak FileZilla lub WinScp lub sftp.
2. Nawiąż połączenie maszyny z systemem Linux przy użyciu konta administratora.
3. Przejdź do katalogu skryptu.
4. Aby utworzyć skrypt do pliku wykonywalnego, wpisz **"sudo" chmod 777 przygotowanie for-azure.sh**.
5. Uruchom skrypt przy użyciu **./prepare-for-azure.sh**.

Poniżej przedstawiono sposób uruchamiania skryptu:

![Skrypt Linux](./media/how-to-prepare-linux-for-migration/script1.png)
![skryptu systemu Linux](./media/how-to-prepare-linux-for-migration/script2.png)
![skryptu systemu Linux](./media/how-to-prepare-linux-for-migration/script3.png)
![skryptu systemu Linux](./media/how-to-prepare-linux-for-migration/script4.png)
![skryptu systemu Linux](./media/how-to-prepare-linux-for-migration/script5.png)
![skryptu systemu Linux ](./media/how-to-prepare-linux-for-migration/script6.png)
 ![Skryptu systemu Linux](./media/how-to-prepare-linux-for-migration/script7.png)
![skryptu systemu Linux](./media/how-to-prepare-linux-for-migration/script8.png)
![skryptu systemu Linux](./media/how-to-prepare-linux-for-migration/script9.png)
![skryptu systemu Linux](./media/how-to-prepare-linux-for-migration/script10.png)
![skryptu systemu Linux ](./media/how-to-prepare-linux-for-migration/script11.png)
 ![Skryptu systemu Linux](./media/how-to-prepare-linux-for-migration/script12.png)
![skryptu systemu Linux](./media/how-to-prepare-linux-for-migration/script13.png)
![skryptu systemu Linux](./media/how-to-prepare-linux-for-migration/script14.png)
![skryptu systemu Linux](./media/how-to-prepare-linux-for-migration/script15.png)
![skryptu systemu Linux ](./media/how-to-prepare-linux-for-migration/script16.png)
 ![Skryptu systemu Linux](./media/how-to-prepare-linux-for-migration/script17.png)
![skryptu systemu Linux](./media/how-to-prepare-linux-for-migration/script18.png)
![skryptu systemu Linux](./media/how-to-prepare-linux-for-migration/script19.png)
![skryptu systemu Linux](./media/how-to-prepare-linux-for-migration/script20.png)
![skryptu systemu Linux ](./media/how-to-prepare-linux-for-migration/script21.png)
 ![Skryptu systemu Linux](./media/how-to-prepare-linux-for-migration/script22.png)
![skryptu systemu Linux](./media/how-to-prepare-linux-for-migration/script23.png)
![skryptu systemu Linux](./media/how-to-prepare-linux-for-migration/script24.png)
![skryptu systemu Linux](./media/how-to-prepare-linux-for-migration/script25.png)



## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak używać [mapowania zależności maszyn](how-to-create-group-machine-dependencies.md) do tworzenia grup o dużej pewności.
- [Dowiedz się więcej](concepts-assessment-calculation.md) na temat sposobu obliczania ocen.
