---
title: Azure kopii zapasowej bazy danych usług Analysis Services i przywracania | Dokumentacja firmy Microsoft
description: Opisuje sposób wykonywania kopii zapasowej i przywracanie bazy danych usług Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: ec213d5c223180825ea0eabe95881002432b92e9
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="backup-and-restore"></a>Tworzenie kopii zapasowej i przywracanie

Tworzenie kopii zapasowej bazy danych modelu tabelarycznego w usług Azure Analysis Services jest znacznie takie same, jak w przypadku lokalnego Analysis Services. Podstawowa różnica polega na którym są przechowywane pliki kopii zapasowych. Pliki kopii zapasowej należy zapisać do kontenera w [konto magazynu Azure](../storage/common/storage-create-storage-account.md). Można było utworzyć, konfigurując ustawienia magazynu dla serwera lub służy konto magazynu i kontener, który już istnieje.

> [!NOTE]
> Tworzenie konta magazynu może skutkować nową usługą płatną. Aby dowiedzieć się więcej, zobacz [cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).
> 
> 

Kopie zapasowe są zapisywane z rozszerzeniem abf. Dla modeli tabelarycznych w pamięci zarówno w modelu danych, jak i metadane są przechowywane. Dla modeli tabelarycznych zapytania bezpośredniego są przechowywane tylko metadane modelu. Kopie zapasowe można skompresować i szyfrowane, w zależności od wybranych opcji. 



## <a name="configure-storage-settings"></a>Konfigurowanie ustawień magazynu
Przed utworzeniem kopii zapasowej, należy skonfigurować ustawienia magazynu dla serwera.


### <a name="to-configure-storage-settings"></a>Aby skonfigurować ustawienia magazynu
1.  W portalu Azure > **ustawienia**, kliknij przycisk **kopii zapasowej**.

    ![Tworzenie kopii zapasowych w ustawieniach](./media/analysis-services-backup/aas-backup-backups.png)

2.  Kliknij przycisk **włączone**, następnie kliknij przycisk **ustawienia magazynu**.

    ![Włączanie](./media/analysis-services-backup/aas-backup-enable.png)

3. Wybierz konto magazynu lub Utwórz nową.

4. Wybierz kontener lub Utwórz nową.

    ![Wybierz kontener](./media/analysis-services-backup/aas-backup-container.png)

5. Zapisz ustawienia kopii zapasowej.

    ![Zapisanie ustawień kopii zapasowej](./media/analysis-services-backup/aas-backup-save.png)

## <a name="backup"></a>Backup

### <a name="to-backup-by-using-ssms"></a>Do wykonania kopii zapasowej przy użyciu narzędzia SSMS

1. W programie SSMS, kliknij prawym przyciskiem myszy bazę danych > **kopię zapasową**.

2. W **instrukcji Backup Database** > **plik kopii zapasowej**, kliknij przycisk **Przeglądaj**.

3. W **Zapisz plik jako** okna dialogowego, sprawdź, czy ścieżka folderu, a następnie wpisz nazwę pliku kopii zapasowej. 

4. W **instrukcji Backup Database** okno dialogowe, wybierz pozycję Opcje.

    **Zezwalaj na plik zastąpić** — wybierz tę opcję, aby zastąpić tworzenia kopii zapasowej plików o takiej samej nazwie. Jeśli ta opcja nie jest zaznaczona, zapisywanego pliku nie może mieć takiej samej nazwie jako plik już istnieje w tej samej lokalizacji.

    **Zastosowanie kompresji** — wybierz tę opcję, aby kompresować pliku kopii zapasowej. Skompresowane pliki kopii zapasowej zaoszczędzić miejsce na dysku, ale wymaga nieco większe użycie procesora CPU. 

    **Szyfrowanie pliku kopii zapasowej** — wybierz tę opcję, aby zaszyfrować plik kopii zapasowej. Ta opcja wymaga hasło dostarczone przez użytkownika, aby zabezpieczyć plik kopii zapasowej. Hasło zapobiega odczytywanie danych kopii zapasowej inny sposób niż operacji przywracania. Jeśli wybierzesz do szyfrowania kopii zapasowych, przechowywania hasła w bezpiecznym miejscu.

5. Kliknij przycisk **OK** Aby utworzyć i zapisać plik kopii zapasowej.


### <a name="powershell"></a>PowerShell
Użyj [ASDatabase kopii zapasowej](https://docs.microsoft.com/sql/analysis-services/powershell/backup-asdatabase-cmdlet) polecenia cmdlet.

## <a name="restore"></a>Przywracanie
Podczas przywracania, pliku kopii zapasowej musi być na koncie magazynu, skonfigurowanego dla serwera. Jeśli musisz przenieść plik kopii zapasowej z lokalizacji lokalnej do konta magazynu, należy użyć [Eksploratora usługi Microsoft Azure Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) lub [AzCopy](../storage/common/storage-use-azcopy.md) narzędzia wiersza polecenia. 



> [!NOTE]
> Jeśli jest przywracana z lokalnego serwera, należy usunąć wszystkich użytkowników domeny z modelu ról i dodaj je do ról jako użytkowników usługi Azure Active Directory.
> 
> 

### <a name="to-restore-by-using-ssms"></a>Aby przywrócić za pomocą narzędzia SSMS

1. W programie SSMS, kliknij prawym przyciskiem myszy bazę danych > **przywrócić**.

2. W **instrukcji Backup Database** okna dialogowego, w **plik kopii zapasowej**, kliknij przycisk **Przeglądaj**.

3. W **zlokalizować pliki bazy danych** okno dialogowe, wybierz plik, który chcesz przywrócić.

4. W **Przywróć bazę danych**, wybierz bazę danych.

5. Określ opcje. Opcje zabezpieczeń muszą być zgodne opcje tworzenia kopii zapasowej, używane podczas wykonywania kopii zapasowej.


### <a name="powershell"></a>PowerShell

Użyj [ASDatabase przywracania](https://docs.microsoft.com/sql/analysis-services/powershell/restore-asdatabase-cmdlet) polecenia cmdlet.


## <a name="related-information"></a>Informacje pokrewne

[Konta magazynu platformy Azure](../storage/common/storage-create-storage-account.md)  
[Wysoka dostępność](analysis-services-bcdr.md)     
[Zarządzanie usług Azure Analysis Services](analysis-services-manage.md)
