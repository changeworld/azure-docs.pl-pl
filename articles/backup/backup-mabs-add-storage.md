---
title: Nowoczesne magazynu kopii zapasowej za pomocą serwera kopii zapasowej Azure w wersji 2
description: Więcej informacji na temat nowych funkcji w wersji v2 serwer kopii zapasowej Azure. W tym artykule opisano sposób uaktualnienia instalacji serwera kopii zapasowej.
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: markgal
ms.openlocfilehash: 7c583ea048ed1837c662869c62039165aaa3c024
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606758"
---
# <a name="add-storage-to-azure-backup-server-v2"></a>Dodawanie magazynu do serwera usługi Kopia zapasowa Azure w wersji 2

Azure v2 Utwórz kopię zapasową serwera jest dostarczany z System Center 2016 ochrony Menedżera Modern kopii zapasowej pamięci masowej. Nowoczesne magazynu kopii zapasowej oferuje oszczędności pojemności magazynu 50 procent kopii zapasowych, które są trzy razy szybszych i bardziej wydajnych magazynu. Zapewnia także magazynu obsługującej obciążenie. 

> [!NOTE]
> Aby używać nowoczesnych magazynu kopii zapasowej, należy uruchomić v2 Utwórz kopię zapasową serwera w systemie Windows Server 2016. Po uruchomieniu v2 Utwórz kopię zapasową serwera we wcześniejszej wersji systemu Windows Server, serwer kopii zapasowej Azure nie może korzystać z nowoczesnych magazynu kopii zapasowej. Zamiast tego chroni ona obciążenia, jak w przypadku v1 Utwórz kopię zapasową serwera. Aby uzyskać więcej informacji, zobacz wersji kopii zapasowej serwera [macierzy ochrony](backup-mabs-protection-matrix.md).

## <a name="volumes-in-backup-server-v2"></a>Woluminy w kopii zapasowej serwera v2

Kopia zapasowa v2 Server akceptuje woluminów magazynu. Podczas dodawania woluminu, Utwórz kopię zapasową serwera formatuje wolumin Resilient File System (system plików ReFS), co wymaga nowoczesnej magazynu kopii zapasowej. Aby dodać wolumin i rozwiń go później, jeśli chcesz, zaleca się, że używasz tego przepływu pracy:

1.  Skonfiguruj serwer zapasowy v2 na maszynie Wirtualnej.
2.  Utwórz wolumin na dysku wirtualnego w puli magazynów:
    1.  Dodaj dysk do puli magazynu i Utwórz dysk wirtualny z układ prosty.
    2.  Dodaj dodatkowe dyski, a rozszerzenie dysku wirtualnego.
    3.  Tworzenie woluminów na dysku wirtualnym.
3.  Dodane do kopii zapasowej serwera.
4.  Konfigurowanie magazynu obsługującej obciążenie.

## <a name="create-a-volume-for-modern-backup-storage"></a>Tworzenie woluminu dla nowoczesnych magazynu kopii zapasowej

Użycie v2 Utwórz kopię zapasową serwera z woluminami jako magazynu danych na dysku może pomóc zachować kontrolę nad magazynu. Wolumin może być także jeden dysk. Jednak jeśli chcesz rozszerzyć magazynu w przyszłości, należy utworzyć wolumin poza dysk utworzony przy użyciu funkcji miejsca do magazynowania. Może to ułatwić, jeśli chcesz rozszerzyć woluminu dla magazynu kopii zapasowej. Ta sekcja zawiera najlepsze rozwiązania dotyczące tworzenia woluminu z tej instalacji.

1. W Menedżerze serwera wybierz **usług plików i magazynowania** > **woluminów** > **pule magazynu**. W obszarze **dysków fizycznych**, wybierz pozycję **nowa pula magazynu**. 

    ![Tworzenie nowej puli magazynu](./media/backup-mabs-add-storage/mabs-add-storage-1.png)

2. W **zadania** listy rozwijanej wybierz pozycję **nowego dysku wirtualnego**.

    ![Dodaj dysk wirtualny](./media/backup-mabs-add-storage/mabs-add-storage-2.png)

3. Wybierz pulę magazynu, a następnie wybierz **Dodaj dysk fizyczny**.

    ![Dodaj dysk fizyczny](./media/backup-mabs-add-storage/mabs-add-storage-3.png)

4. Wybierz dysk fizyczny, a następnie wybierz **Rozszerz dysk wirtualny**.

    ![Rozszerzanie dysku wirtualnego](./media/backup-mabs-add-storage/mabs-add-storage-4.png)

5. Wybierz dysk wirtualny, a następnie wybierz **nowy wolumin**.

    ![Utwórz nowy wolumin](./media/backup-mabs-add-storage/mabs-add-storage-5.png)

6. W **wybierz serwer i dysk** okno dialogowe, wybierz serwer i nowy dysk. Następnie wybierz opcję **dalej**.

    ![Wybierz serwer i dysk](./media/backup-mabs-add-storage/mabs-add-storage-6.png)

## <a name="add-volumes-to-backup-server-disk-storage"></a>Dodaj woluminów do magazynu danych na dysku serwera kopii zapasowej

Aby dodać wolumin do serwera kopii zapasowej w **zarządzania** okienku ponownego skanowania magazynu, a następnie wybierz **Dodaj**. Zostanie wyświetlona lista wszystkich woluminów dostępnych do dodania do magazynu kopii zapasowej serwera. Po dodaniu woluminów dostępnych do listy wybranych woluminów, możesz je nadaj przyjazną nazwę w celu zarządzania nimi. Aby sformatować woluminy te do systemu plików ReFS, Utwórz kopię zapasową serwera można używać z zalet nowoczesnych magazynu kopii zapasowej, zaznacz **OK**.

![Dodaj dostępne woluminy](./media/backup-mabs-add-storage/mabs-add-storage-7.png)

## <a name="set-up-workload-aware-storage"></a>Konfigurowanie magazynu obsługującej obciążenie

Z magazynu obsługujących obciążenie można wybrać woluminów, które przechowują preferencyjnie niektóre rodzaje obciążeń. Na przykład można ustawić kosztowne woluminów, które obsługują dużej liczby operacji wejścia/wyjścia na sekundę (IOPS) do przechowywania obciążeń, które wymagają częstego, dużej liczby kopii zapasowych. Przykładem jest program SQL Server z dzienników transakcji. Innych obciążeń, których kopii zapasowej rzadziej, takich jak maszyny wirtualne, można tworzenie kopii zapasowych woluminów niskich kosztach.

### <a name="update-dpmdiskstorage"></a>Update-DPMDiskStorage

Magazyn obsługujący obciążenia można skonfigurować przy użyciu polecenia cmdlet programu PowerShell Update-DPMDiskStorage, która aktualizuje właściwości woluminu w puli magazynu na serwerze programu Data Protection Manager.

Składnia:

`Parameter Set: Volume`

```
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [ <CommonParameters>]
```
Poniższy zrzut ekranu przedstawia polecenie cmdlet Update-DPMDiskStorage w oknie programu PowerShell.

![Polecenie Update-DPMDiskStorage w oknie programu PowerShell](./media/backup-mabs-add-storage/mabs-add-storage-8.png)

Zmiany wprowadzone przy użyciu programu PowerShell są uwzględniane w konsoli administratora serwera kopii zapasowej.

![Dyski i woluminy w konsoli administratora](./media/backup-mabs-add-storage/mabs-add-storage-9.png)

## <a name="next-steps"></a>Kolejne kroki
Po zainstalowaniu serwera kopii zapasowej, Dowiedz się, jak przygotować serwer lub Włącz ochronę obciążeń.

- [Przygotowanie serwera kopii zapasowej obciążeń](backup-azure-microsoft-azure-backup.md)
- [Użyj kopii zapasowej serwera, aby utworzyć kopię zapasową serwera VMware](backup-azure-backup-server-vmware.md)
- [Utwórz kopię zapasową serwera umożliwia tworzenie kopii zapasowych programu SQL Server](backup-azure-sql-mabs.md)

