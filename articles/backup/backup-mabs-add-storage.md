---
title: Modern Backup Storage za pomocą usługi Azure Backup Server
description: Dowiedz się więcej o nowych funkcjach w usłudze Azure Backup Server. W tym artykule opisano sposób uaktualniania instalację serwera usługi Backup.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: adigan
ms.openlocfilehash: 621d071f98701ff3a949f4172fef1d13819d7192
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60813121"
---
# <a name="add-storage-to-azure-backup-server"></a>Dodawanie magazynu do usługi Azure Backup Server

Usługa Azure Backup Server V2 i jego nowsze wersje obsługują Modern Backup Storage, oferująca oszczędności pojemności magazynu 50 procent kopie zapasowe są trzy razy szybsze i wydajniejsze magazynu. Zapewnia ona również wydajniejszego magazynu obsługującego obciążenie.

> [!NOTE]
> Aby użyć Modern Backup Storage, należy uruchomić kopii zapasowej serwera w wersji 2 lub 3 w systemie Windows Server 2016 lub 2019 serwera systemu Windows w wersji 3.
> Jeśli uruchamiasz kopii zapasowej serwera w wersji 2 w starszej wersji systemu Windows Server, serwer usługi Azure Backup nie mogą korzystać z Modern Backup Storage. Zamiast tego należy je chroni obciążenia, jak w przypadku kopii zapasowej serwera w wersji 1. Aby uzyskać więcej informacji, zobacz wersji kopii zapasowej serwera [macierz ochrony](backup-mabs-protection-matrix.md).

## <a name="volumes-in-backup-server"></a>Woluminy w kopii zapasowej serwera

Utworzenie kopii zapasowej serwera w wersji 2 lub nowszym akceptuje woluminy magazynu. Podczas dodawania woluminu, Utwórz kopię zapasową serwera formatuje wolumin systemu plików (ReFS), co wymaga Modern Backup Storage. Aby dodać wolumin i rozwiń go później, jeśli trzeba, zalecamy użycie tego przepływu pracy:

1.  Konfigurowanie serwera usługi Backup na maszynie Wirtualnej.
2.  Utwórz wolumin na dysku wirtualnego w puli magazynów:
    1.  Dodaj dysk do puli magazynów i Utwórz dysk wirtualny przy użyciu układu prostego.
    2.  Dodaj dodatkowe dyski i Rozszerz dysk wirtualny.
    3.  Tworzenie woluminów na dysku wirtualnym.
3.  Dodaj woluminy do serwera kopii zapasowych.
4.  Konfigurowanie magazynu obsługującego obciążenie.

## <a name="create-a-volume-for-modern-backup-storage"></a>Tworzenie woluminu dla funkcji Modern Backup Storage

Przy użyciu kopii zapasowej serwera w wersji 2 lub nowszego z woluminów, ilość miejsca do magazynowania może pomóc zachować kontrolę nad przechowywaniem. Wolumin może być jednym dysku. Jednak jeśli chcesz rozszerzyć magazynu w przyszłości, należy utworzyć wolumin z dysku, który został utworzony przy użyciu funkcji miejsca do magazynowania. Może to ułatwić, jeśli chcesz rozszerzyć woluminu dla magazynu kopii zapasowych. Ta sekcja zawiera najlepsze rozwiązania dotyczące tworzenia woluminu za pomocą tego Instalatora.

1. W Menedżerze serwera wybierz **usług plików i magazynowania** > **woluminów** > **pule magazynów**. W obszarze **dysków fizycznych**, wybierz opcję **nowa pula magazynu**.

    ![Tworzenie nowej puli magazynu](./media/backup-mabs-add-storage/mabs-add-storage-1.png)

2. W **zadania** listy rozwijanej wybierz pozycję **nowego dysku wirtualnego**.

    ![Dodaj dysk wirtualny](./media/backup-mabs-add-storage/mabs-add-storage-2.png)

3. Wybierz pulę magazynu, a następnie wybierz **Dodaj dysk fizyczny**.

    ![Dodawanie dysku fizycznego](./media/backup-mabs-add-storage/mabs-add-storage-3.png)

4. Wybierz dysk fizyczny, a następnie wybierz **Rozszerz dysk wirtualny**.

    ![Rozszerz dysk wirtualny](./media/backup-mabs-add-storage/mabs-add-storage-4.png)

5. Wybierz wirtualny dysk, a następnie wybierz **nowy wolumin**.

    ![Utwórz nowy wolumin](./media/backup-mabs-add-storage/mabs-add-storage-5.png)

6. W **wybierz serwer i dysk** okno dialogowe, wybierz serwer i nowego dysku. Następnie wybierz opcję **Dalej**.

    ![Wybierz serwer i dysk](./media/backup-mabs-add-storage/mabs-add-storage-6.png)

## <a name="add-volumes-to-backup-server-disk-storage"></a>Dodaj woluminy do serwer zapasowy magazyn dyskowy

Aby dodać wolumin do serwera kopii zapasowych w **zarządzania** okienku ponownego skanowania magazynu, a następnie wybierz **Dodaj**. Zostanie wyświetlona lista wszystkich woluminów dostępnych do dodania do magazynu serwera kopii zapasowych. Po dostępne woluminy są dodawane do listy wybranych woluminów, można przekazać przyjazną nazwę, aby ułatwić zarządzanie nimi. Aby sformatowanie tych woluminów do systemu plików ReFS, więc Utwórz kopię zapasową serwera można korzystać z zalet funkcji Modern Backup Storage, wybierz pozycję **OK**.

![Dodaj dostępne woluminy](./media/backup-mabs-add-storage/mabs-add-storage-7.png)

## <a name="set-up-workload-aware-storage"></a>Konfigurowanie magazynu obsługującego obciążenie

Dzięki magazynowi obsługującemu obciążenie można wybrać woluminów, na których preferencyjne niektórych rodzajów obciążeń. Na przykład można ustawić kosztowne woluminy, które obsługują dużą liczbę operacji wejścia/wyjścia na sekundę (IOPS), aby przechowywały tylko obciążenia wymagające częste, duże kopie zapasowe. Przykładem jest program SQL Server przy użyciu dzienników transakcji. Kopię zapasową innych obciążeń, które są rzadziej kopie, podobnie jak w przypadku maszyn wirtualnych można tworzyć na tańszych woluminach.

### <a name="update-dpmdiskstorage"></a>Update-DPMDiskStorage

Wydajniejszego magazynu obsługującego obciążenie można skonfigurować za pomocą polecenia cmdlet programu PowerShell Update-DPMDiskStorage. umożliwia zaktualizowanie właściwości woluminu w puli magazynów na serwerze usługi Azure Backup. 

Składnia:

`Parameter Set: Volume`

```
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [ <CommonParameters>]
```
Poniższy zrzut ekranu przedstawia polecenia cmdlet Update-DPMDiskStorage w oknie programu PowerShell.

![Polecenie Update-DPMDiskStorage w oknie programu PowerShell](./media/backup-mabs-add-storage/mabs-add-storage-8.png)

Zmiany wprowadzone przy użyciu programu PowerShell są odzwierciedlane w konsoli administratora serwera kopii zapasowych.

![Dyski i woluminy w konsoli administratora programu](./media/backup-mabs-add-storage/mabs-add-storage-9.png)


## <a name="migrate-legacy-storage-to-modern-backup-storage"></a>Migrowanie starszej wersji magazynu do funkcji Modern Backup Storage
Po uaktualnieniu lub zainstalować kopii zapasowej serwera w wersji 2 i Uaktualnij system operacyjny do systemu Windows Server 2016 można zaktualizować grup ochrony do użycia Modern Backup Storage. Domyślnie grupy ochrony nie są zmieniane. One nadal działać zgodnie z początkowo zostały ustawione.

Aktualizowanie grup ochrony w celu użycia Modern Backup Storage jest opcjonalne. Aby zaktualizować grupę ochrony, Zatrzymaj ochronę wszystkich źródeł danych przy użyciu opcji Zachowaj dane. Następnie dodaj źródła danych do nowej grupy ochrony.

1. W konsoli administratora wybierz **ochrony** funkcji. W **elementu członkowskiego grupy ochrony** listy, kliknij prawym przyciskiem myszy element członkowski, a następnie wybierz **Zatrzymaj ochronę członka**.

   ![Zatrzymaj ochronę członka](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-stop-protection1.png)

2. W **Usuń z grupy** okna dialogowego przejrzyj używane miejsce na dysku i dostępne wolne miejsce w puli magazynów. Wartość domyślna to pozostawienie punktów odzyskiwania na dysku i umożliwić im wygaśnie za skojarzonych zasad przechowywania. Kliknij przycisk **OK**.

   Aby natychmiast przywrócić zajęte miejsce na dysku do wolnej puli magazynów, należy zaznaczyć **Usuń replikę z dysku** pole wyboru, aby usunąć dane kopii zapasowej (i punktów odzyskiwania) skojarzonych z tym elementem członkowskim.

   ![Usuń z grupy, okno dialogowe](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-retain-data.png)

3. Utwórz grupę ochrony, która używa funkcji Modern Backup Storage. Dołącz źródła danych niechronione.

## <a name="add-disks-to-increase-legacy-storage"></a>Dodawanie dysków w celu zwiększenia magazynu w starszej wersji

Jeśli chcesz używać starszej wersji magazynu z kopii zapasowej serwera, może być konieczne dodanie dysków w celu zwiększenia magazynu w starszej wersji.

Aby dodać magazyn dyskowy:

1. W konsoli administratora wybierz **zarządzania** > **Magazyn dyskowy** > **Dodaj**.

    ![Dodaj okno dialogowe Magazyn dyskowy](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-add-disk-storage.png)

4. W **Dodawanie magazynu dyskowego** okno dialogowe, wybierz opcję **dodawać dyski**.

5. Na liście dostępnych dysków, wybierz dyski, o których chcesz dodać, wybierz **Dodaj**, a następnie wybierz pozycję **OK**.

## <a name="next-steps"></a>Kolejne kroki
Po zainstalowaniu serwera usługi Backup, Dowiedz się, jak przygotować serwer lub rozpoczęciu ochrony obciążeń.

- [Przygotowywanie obciążeń serwera usługi Backup](backup-azure-microsoft-azure-backup.md)
- [Tworzenie kopii zapasowej serwera VMware przy użyciu kopii zapasowej serwera](backup-azure-backup-server-vmware.md)
- [Tworzenie kopii zapasowych programu SQL Server przy użyciu kopii zapasowej serwera](backup-azure-sql-mabs.md)
