---
title: Tworzenie kopii zapasowych i przywracanie bazy danych usług Azure Analysis Services | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób tworzenia kopii zapasowych i przywracania metadanych modelu i danych z bazy danych usług Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 83da2024ab74b705b45a5891f6b40251020dad31
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80408652"
---
# <a name="backup-and-restore"></a>Tworzenie kopii zapasowej i przywracanie

Tworzenie kopii zapasowych baz danych modelu tabelaryczne w usłudze Azure Analysis Services jest tak samo jak w przypadku lokalnych usług analysis services. Podstawową różnicą jest miejsce przechowywania plików kopii zapasowej. Pliki kopii zapasowej muszą być zapisane w kontenerze na [koncie magazynu platformy Azure](../storage/common/storage-create-storage-account.md). Można użyć konta magazynu i kontenera, który już masz, lub można je utworzyć podczas konfigurowania ustawień magazynu dla serwera.

> [!NOTE]
> Utworzenie konta magazynu może spowodować powstanie nowej usługi podlegającej rozliczaniu. Aby dowiedzieć się więcej, zobacz [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).
> 
> 

Kopie zapasowe są zapisywane z rozszerzeniem .abf. W przypadku modeli tabelarów w pamięci przechowywane są zarówno dane modelu, jak i metadane. W przypadku modeli tabelarykowych zapytania bezpośredniego są przechowywane tylko metadane modelu. Kopie zapasowe mogą być kompresowane i szyfrowane, w zależności od dostępnych opcji.


## <a name="configure-storage-settings"></a>Konfigurowanie ustawień magazynu
Przed utworzeniem kopii zapasowej należy skonfigurować ustawienia magazynu dla serwera.


### <a name="to-configure-storage-settings"></a>Aby skonfigurować ustawienia magazynu
1.  W witrynie Azure portal > **Ustawienia**kliknij pozycję **Kopia zapasowa**.

    ![Kopie zapasowe w ustawieniach](./media/analysis-services-backup/aas-backup-backups.png)

2.  Kliknij **pozycję Włączone**, a następnie kliknij pozycję Ustawienia **magazynu**.

    ![Włączanie](./media/analysis-services-backup/aas-backup-enable.png)

3. Wybierz swoje konto magazynu lub utwórz nowe.

4. Wybierz kontener lub utwórz nowy.

    ![Wybieranie kontenera](./media/analysis-services-backup/aas-backup-container.png)

5. Zapisz ustawienia kopii zapasowej.

    ![Zapisywanie ustawień kopii zapasowej](./media/analysis-services-backup/aas-backup-save.png)

## <a name="backup"></a>Backup

### <a name="to-backup-by-using-ssms"></a>Aby wykonać kopię zapasową przy użyciu usługi SSMS

1. W pliku SSMS kliknij prawym przyciskiem myszy bazę danych > **tworzenie kopii zapasowej**.

2. W**pliku kopii zapasowej bazy** **danych** > kliknij pozycję **Przeglądaj**.

3. W oknie dialogowym **Zapisywanie pliku jako** sprawdź ścieżkę folderu, a następnie wpisz nazwę pliku kopii zapasowej. 

4. W oknie **dialogowym Kopia zapasowa bazy danych** wybierz pozycję opcje.

    **Zezwalaj na zastępowanie pliku** — wybierz tę opcję, aby zastąpić pliki kopii zapasowych o tej samej nazwie. Jeśli ta opcja nie jest zaznaczona, zapisywany plik nie może mieć takiej samej nazwy jak plik, który już istnieje w tej samej lokalizacji.

    **Zastosuj kompresję** — wybierz tę opcję, aby skompresować plik kopii zapasowej. Skompresowane pliki kopii zapasowych oszczędzają miejsce na dysku, ale wymagają nieco większego wykorzystania procesora. 

    **Szyfruj plik kopii zapasowej** — wybierz tę opcję, aby zaszyfrować plik kopii zapasowej. Ta opcja wymaga hasła dostarczonego przez użytkownika w celu zabezpieczenia pliku kopii zapasowej. Hasło zapobiega odczytywanie danych kopii zapasowej w jakikolwiek inny sposób niż operacja przywracania. Jeśli zdecydujesz się zaszyfrować kopie zapasowe, przechowuj je w bezpiecznej lokalizacji.

5. Kliknij **przycisk OK,** aby utworzyć i zapisać plik kopii zapasowej.


### <a name="powershell"></a>PowerShell
Użyj polecenia cmdlet [Backup-ASDatabase.](https://docs.microsoft.com/powershell/module/sqlserver/backup-asdatabase)

## <a name="restore"></a>Przywracanie
Podczas przywracania plik kopii zapasowej musi znajdować się na koncie magazynu skonfigurowanym dla serwera. Jeśli chcesz przenieść plik kopii zapasowej z lokalizacji lokalnej do konta magazynu, użyj [Eksploratora usługi Microsoft Azure Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) lub narzędzia wiersza polecenia [AzCopy.](../storage/common/storage-use-azcopy.md) 



> [!NOTE]
> Jeśli przywracasz z serwera lokalnego, należy usunąć wszystkich użytkowników domeny z ról modelu i dodać je z powrotem do ról jako użytkownicy usługi Azure Active Directory.
> 
> 

### <a name="to-restore-by-using-ssms"></a>Aby przywrócić za pomocą SSMS

1. W pliku SSMS kliknij prawym przyciskiem myszy bazę danych > **przywracanie**.

2. W oknie dialogowym **Kopia zapasowa bazy danych** w pliku kopii **zapasowej**kliknij pozycję **Przeglądaj**.

3. W oknie **dialogowym Lokalizowanie plików bazy danych** wybierz plik, który chcesz przywrócić.

4. W **przywracanie bazy danych**wybierz bazę danych.

5. Określ opcje. Opcje zabezpieczeń muszą być zgodne z opcjami tworzenia kopii zapasowej używanymi podczas wykonywania kopii zapasowej.


### <a name="powershell"></a>PowerShell

Użyj polecenia cmdlet [Restore-ASDatabase.](https://docs.microsoft.com/powershell/module/sqlserver/restore-asdatabase)


## <a name="related-information"></a>Informacje pokrewne

[Konta usługi Azure Storage](../storage/common/storage-create-storage-account.md)  
[Wysoka dostępność](analysis-services-bcdr.md)     
[Zarządzanie usługami analizy platformy Azure](analysis-services-manage.md)
