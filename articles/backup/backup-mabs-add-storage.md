---
title: Korzystanie z nowoczesnego magazynu kopii zapasowych z serwerem kopii zapasowych platformy Azure
description: Dowiedz się więcej o nowych funkcjach usługi Azure Backup Server. W tym artykule opisano sposób uaktualniania instalacji serwera kopii zapasowych.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: c6346d7b0275a00271c1787b378a63b8365edf2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74172378"
---
# <a name="add-storage-to-azure-backup-server"></a>Dodawanie magazynu do usługi Azure Backup Server

Usługa Azure Backup Server w wersji 2 i nowszych obsługuje nowoczesny magazyn kopii zapasowych, który oferuje oszczędność magazynu o 50 procent, kopie zapasowe, które są trzy razy szybsze i bardziej wydajne magazynu. Oferuje również magazyn z uwzględnieniem obciążenia.

> [!NOTE]
> Aby korzystać z nowoczesnego magazynu kopii zapasowych, należy uruchomić serwer kopii zapasowych v2 lub v3 w systemie Windows Server 2016 lub V3 w systemie Windows Server 2019.
> Jeśli uruchomisz serwer kopii zapasowej w wersji 2 we wcześniejszej wersji systemu Windows Server, serwer kopii zapasowej platformy Azure nie będzie mógł korzystać z nowoczesnego magazynu kopii zapasowych. Zamiast tego chroni obciążenia, tak jak w przypadku serwera kopii zapasowej v1. Aby uzyskać więcej informacji, zobacz [macierz Ochrony wersji](backup-mabs-protection-matrix.md)serwera kopii zapasowych .
>
> Aby uzyskać lepszą wydajność tworzenia kopii zapasowych, zaleca się wdrożenie mas w wersji 3 z warstwowym magazynem w systemie Windows Server 2019. Aby zapoznać się z artykułem programu DPM "[Konfigurowanie mbs z magazynem warstwowym](https://docs.microsoft.com/system-center/dpm/add-storage?view=sc-dpm-2019#set-up-mbs-with-tiered-storage)", aby uzyskać instrukcje konfigurowania magazynu warstwowego.

## <a name="volumes-in-backup-server"></a>Woluminy na serwerze kopii zapasowych

Serwer kopii zapasowej v2 lub nowszy akceptuje woluminy magazynu. Po dodaniu woluminu serwer kopii zapasowej formatuje wolumin do systemu plików Resilient File System (ReFS), którego wymaga nowoczesny magazyn kopii zapasowych. Aby dodać wolumin i rozwinąć go później, jeśli zajdzie taka potrzeba, zalecamy użycie tego przepływu pracy:

1. Konfigurowanie serwera kopii zapasowych na maszynie wirtualnej.
2. Tworzenie woluminu na dysku wirtualnym w puli magazynu:
    1. Dodaj dysk do puli magazynu i utwórz dysk wirtualny z prostym układem.
    2. Dodaj dodatkowe dyski i rozszerz dysk wirtualny.
    3. Tworzenie woluminów na dysku wirtualnym.
3. Dodaj woluminy do serwera kopii zapasowych.
4. Konfigurowanie magazynu obsługującego obciążenie pracą.

## <a name="create-a-volume-for-modern-backup-storage"></a>Tworzenie woluminu dla nowoczesnego magazynu kopii zapasowych

Używanie serwera kopii zapasowej w wersji 2 lub nowszej z woluminami jako magazynem dysku może pomóc w utrzymaniu kontroli nad magazynem. Wolumin może być pojedynczym dyskiem. Jeśli jednak chcesz rozszerzyć magazyn w przyszłości, utwórz wolumin z dysku utworzonego przy użyciu miejsca do magazynowania. Może to pomóc, jeśli chcesz rozszerzyć wolumin do przechowywania kopii zapasowych. W tej sekcji znajdują się najlepsze rozwiązania dotyczące tworzenia woluminu za pomocą tej konfiguracji.

1. W Menedżerze serwera wybierz pozycję Pule**woluminów woluminów** >  **plików i usług** > **magazynowych**. W obszarze **DYSKI FIZYCZNE**wybierz pozycję **Nowa pula magazynów**.

    ![Tworzenie nowej puli magazynu](./media/backup-mabs-add-storage/mabs-add-storage-1.png)

2. Z listy rozwijanej **ZADANIA** wybierz pozycję **Nowy dysk wirtualny**.

    ![Dodawanie dysku wirtualnego](./media/backup-mabs-add-storage/mabs-add-storage-2.png)

3. Wybierz pulę magazynu, a następnie wybierz pozycję **Dodaj dysk fizyczny**.

    ![Dodawanie dysku fizycznego](./media/backup-mabs-add-storage/mabs-add-storage-3.png)

4. Wybierz dysk fizyczny, a następnie wybierz pozycję **Wydłuż dysk wirtualny**.

    ![Rozszerzanie dysku wirtualnego](./media/backup-mabs-add-storage/mabs-add-storage-4.png)

5. Wybierz dysk wirtualny, a następnie wybierz pozycję **Nowy wolumin**.

    ![Tworzenie nowego woluminu](./media/backup-mabs-add-storage/mabs-add-storage-5.png)

6. W oknie dialogowym **Wybierz serwer i dysk** wybierz serwer i nowy dysk. Następnie wybierz przycisk **Dalej**.

    ![Wybierz serwer i dysk](./media/backup-mabs-add-storage/mabs-add-storage-6.png)

## <a name="add-volumes-to-backup-server-disk-storage"></a>Dodawanie woluminów do magazynu dysku serwera kopii zapasowych

> [!NOTE]
>
> - Dodaj tylko jeden dysk do puli, aby zachować liczbę kolumn do 1. Następnie można dodać dyski w razie potrzeby później.
> - Jeśli dodasz wiele dysków do puli magazynu w podróży, liczba dysków jest przechowywana jako liczba kolumn. Po dodaniu większej liczby dysków mogą one być tylko wielokrotnością liczby kolumn.

Aby dodać wolumin do serwera kopii zapasowych, w okienku **Zarządzanie** ponownie przeskanuj magazyn, a następnie wybierz pozycję **Dodaj**. Zostanie wyświetlona lista wszystkich woluminów dostępnych do dodania do magazynu serwera kopii zapasowych. Po dodaniu dostępnych woluminów do listy wybranych woluminów można nadać im przyjazną nazwę ułatwiające zarządzanie nimi. Aby sformatować te woluminy na system plików ReFS, aby serwer kopii zapasowej mógł korzystać z zalet nowoczesnego magazynu kopii zapasowych, wybierz przycisk **OK**.

![Dodawanie dostępnych woluminów](./media/backup-mabs-add-storage/mabs-add-storage-7.png)

## <a name="set-up-workload-aware-storage"></a>Konfigurowanie magazynu obsługującego obciążenie pracą

Za pomocą magazynu obsługującego obciążenie pracą można wybrać woluminy, które preferencyjnie przechowują określone rodzaje obciążeń. Na przykład można ustawić drogie woluminy, które obsługują dużą liczbę operacji wejścia/wyjścia na sekundę (IOPS) do przechowywania tylko obciążeń, które wymagają częstych kopii zapasowych dużej ilości. Przykładem jest SQL Server z dziennikami transakcji. Inne obciążenia, które są archiwizowane rzadziej, takich jak maszyny wirtualne, można kopii zapasowej do woluminów o niskich kosztach.

### <a name="update-dpmdiskstorage"></a>Update-DPMDiskStorage

Magazyn obsługujący obciążenie pracą można skonfigurować przy użyciu polecenia cmdlet Update-DPMDiskStorage programu PowerShell, który aktualizuje właściwości woluminu w puli magazynu na serwerze kopii zapasowej platformy Azure.

Składnia:

`Parameter Set: Volume`

```powershell
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [ <CommonParameters>]
```

Poniższy zrzut ekranu przedstawia polecenie cmdlet Update-DPMDiskStorage w oknie programu PowerShell.

![Polecenie Update-DPMDiskStorage w oknie programu PowerShell](./media/backup-mabs-add-storage/mabs-add-storage-8.png)

Zmiany wprowadzone za pomocą programu PowerShell są odzwierciedlane w Konsoli administratora serwera kopii zapasowych.

![Dyski i woluminy w Konsoli administratora](./media/backup-mabs-add-storage/mabs-add-storage-9.png)

## <a name="migrate-legacy-storage-to-modern-backup-storage"></a>Migrowanie starszego magazynu do nowoczesnego magazynu kopii zapasowych

Po uaktualnieniu do lub zainstalowaniu serwera kopii zapasowej w wersji 2 i uaktualnieniu systemu operacyjnego do systemu Windows Server 2016 zaktualizuj grupy ochrony, aby korzystały z nowoczesnego magazynu kopii zapasowych. Domyślnie grupy ochrony nie są zmieniane. Nadal funkcjonują tak, jak były początkowo zakładane.

Aktualizowanie grup ochrony w celu korzystania z funkcji Modern Backup Storage jest opcjonalne. Aby zaktualizować grupę ochrony, zatrzymaj ochronę wszystkich źródeł danych przy użyciu opcji zachowaj dane. Następnie dodaj źródła danych do nowej grupy ochrony.

1. W Konsoli administratora wybierz funkcję **Ochrona.** Na liście **Członek grupy ochrony** kliknij prawym przyciskiem myszy element członkowski, a następnie wybierz pozycję **Zatrzymaj ochronę członka**.

   ![Zatrzymaj ochronę członka](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-stop-protection1.png)

2. W oknie dialogowym **Usuń z grupy** przejrzyj używane miejsce na dysku i dostępne wolne miejsce dla puli magazynu. Domyślnie punkty odzyskiwania pozostają na dysku i wygasają zgodnie ze skojarzonymi z nimi zasadami przechowywania. Kliknij przycisk **OK**.

   Jeśli chcesz natychmiast zwrócić używane miejsce na dysku do puli wolnego magazynu, zaznacz pole wyboru **Usuń replikę na dysku,** aby usunąć dane kopii zapasowej (i punkty odzyskiwania) skojarzone z tym elementem członkowskim.

   ![Okno dialogowe Usuwanie z grupy](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-retain-data.png)

3. Utwórz grupę ochrony, która używa nowoczesnego magazynu kopii zapasowych. Uwzględnij niechronione źródła danych.

## <a name="add-disks-to-increase-legacy-storage"></a>Dodawanie dysków w celu zwiększenia starszej pamięci masowej

Jeśli chcesz użyć starszego magazynu z serwerem kopii zapasowych, może być konieczne dodanie dysków w celu zwiększenia starszego magazynu.

Aby dodać magazyn dyskowy:

1. W Konsoli administratora wybierz pozycję Dodawanie**magazynu dysków** >  **zarządzania** > **.**

    ![Okno dialogowe Dodawanie magazynu dysku](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-add-disk-storage.png)

2. W oknie dialogowym **Dodawanie magazynu dysków** wybierz pozycję **Dodaj dyski**.

3. Na liście dostępnych dysków wybierz dyski, które chcesz dodać, wybierz pozycję **Dodaj**, a następnie wybierz przycisk **OK**.

## <a name="next-steps"></a>Następne kroki

Po zainstalowaniu serwera kopii zapasowej dowiedz się, jak przygotować serwer lub rozpocząć ochronę obciążenia.

- [Przygotowywanie obciążeń serwera kopii zapasowych](backup-azure-microsoft-azure-backup.md)
- [Tworzenie kopii zapasowej serwera VMware za pomocą serwera kopii zapasowych](backup-azure-backup-server-vmware.md)
- [Tworzenie kopii zapasowej programu SQL Server za pomocą serwera kopii zapasowych](backup-azure-sql-mabs.md)
