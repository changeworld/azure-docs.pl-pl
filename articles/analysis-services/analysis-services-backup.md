---
title: Azure Analysis Services bazy danych w kopii zapasowej i przywracania | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak utworzyć kopię zapasową i przywrócić bazę danych usług Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 31e8e65b382a3a6bcad2998a0babdf9605dc4968
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61023911"
---
# <a name="backup-and-restore"></a>Tworzenie kopii zapasowej i przywracanie

Wykonywanie kopii zapasowych baz danych modelu tabelarycznego w usługach Azure Analysis Services jest prawie tak samo jak w przypadku usług Analysis Services w środowisku lokalnym. Podstawowa różnica polega na tym, gdzie przechowujesz pliki kopii zapasowych. Pliki kopii zapasowej muszą zostać zapisane w kontenerze [konta usługi Azure storage](../storage/common/storage-create-storage-account.md). Można użyć konta magazynu i kontener, którą już posiadasz, lub mogą być tworzone podczas konfigurowania ustawień magazynu dla serwera.

> [!NOTE]
> Tworzenie konta magazynu może skutkować powstaniem nowej usługi płatnej. Aby dowiedzieć się więcej, zobacz [cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).
> 
> 

Kopie zapasowe są zapisywane z rozszerzeniem abf. Modele tabelaryczne w pamięci zarówno w modelu danych, jak i metadane są przechowywane. Dla zapytania bezpośredniego modeli tabelarycznych są przechowywane tylko metadanych modelu. Kopie zapasowe można kompresowane i szyfrowane, w zależności od wybranych opcji.


## <a name="configure-storage-settings"></a>Skonfiguruj ustawienia magazynu
Przed utworzeniem kopii zapasowej, należy skonfigurować ustawienia magazynu dla serwera.


### <a name="to-configure-storage-settings"></a>Aby skonfigurować ustawienia magazynu
1.  W witrynie Azure portal > **ustawienia**, kliknij przycisk **kopii zapasowej**.

    ![Tworzenie kopii zapasowych w ustawieniach](./media/analysis-services-backup/aas-backup-backups.png)

2.  Kliknij przycisk **włączone**, następnie kliknij przycisk **ustawienia magazynu**.

    ![Włączenie](./media/analysis-services-backup/aas-backup-enable.png)

3. Wybierz konto magazynu lub Utwórz nową.

4. Wybierz kontener lub Utwórz nową.

    ![Wybieranie kontenera](./media/analysis-services-backup/aas-backup-container.png)

5. Zapisz ustawienia kopii zapasowej.

    ![Zapisanie ustawień kopii zapasowej](./media/analysis-services-backup/aas-backup-save.png)

## <a name="backup"></a>Backup

### <a name="to-backup-by-using-ssms"></a>Do wykonywania kopii zapasowych przy użyciu programu SSMS

1. W programie SSMS kliknij prawym przyciskiem myszy bazę danych > **Utwórz kopię zapasową**.

2. W **Backup Database** > **pliku kopii zapasowej**, kliknij przycisk **Przeglądaj**.

3. W **Zapisz plik jako** okno dialogowe, sprawdź ścieżkę do folderu, a następnie wpisz nazwę pliku kopii zapasowej. 

4. W **Backup Database** okno dialogowe, wybierz opcje.

    **Zezwalaj na plik zastąpić** — wybierz tę opcję, aby zastąpić pliki kopii zapasowych o takiej samej nazwie. Jeśli ta opcja nie jest zaznaczona, plik, który jest zapisywany nie może mieć taką samą nazwę jako plik, który już istnieje w tej samej lokalizacji.

    **Stosowanie kompresji** — wybierz tę opcję, aby kompresować pliku kopii zapasowej. Skompresowane pliki kopii zapasowej zaoszczędzić miejsce na dysku, ale wymaga nieco większe użycie procesora CPU. 

    **Szyfrowanie pliku kopii zapasowej** — wybierz tę opcję, aby zaszyfrować plik kopii zapasowej. Ta opcja wymaga hasło dostarczone przez użytkownika, aby zabezpieczyć plik kopii zapasowej. Hasło zapobiega odczytywanie danych kopii zapasowej jakikolwiek inny sposób niż operacji przywracania. Jeśli chcesz szyfrować kopie zapasowe przechowywania hasła w bezpiecznym miejscu.

5. Kliknij przycisk **OK** Aby utworzyć i zapisać plik kopii zapasowej.


### <a name="powershell"></a>PowerShell
Użyj [ASDatabase kopii zapasowej](https://docs.microsoft.com/sql/analysis-services/powershell/backup-asdatabase-cmdlet) polecenia cmdlet.

## <a name="restore"></a>Przywracanie
Podczas przywracania, plik kopii zapasowej musi być w ramach konta magazynu, skonfigurowanych dla Twojego serwera. Jeśli musisz przenieść plik kopii zapasowej z lokalizacji lokalnych do swojego konta magazynu, użyj [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) lub [AzCopy](../storage/common/storage-use-azcopy.md) narzędzie wiersza polecenia. 



> [!NOTE]
> Jeśli jest przywracana z serwera lokalnego, należy usunąć wszystkich użytkowników domeny z modelu ról i dodaj je z powrotem do ról jako użytkownicy usługi Azure Active Directory.
> 
> 

### <a name="to-restore-by-using-ssms"></a>Aby przywrócić za pomocą programu SSMS

1. W programie SSMS kliknij prawym przyciskiem myszy bazę danych > **przywrócić**.

2. W **Backup Database** okna dialogowego w **pliku kopii zapasowej**, kliknij przycisk **Przeglądaj**.

3. W **Zlokalizuj pliki bazy danych** okno dialogowe, wybierz plik, który chcesz przywrócić.

4. W **Przywróć bazę danych**, wybierz bazę danych.

5. Określ opcje. Opcje zabezpieczeń musi odpowiadać opcje tworzenia kopii zapasowej, którego użyto podczas tworzenia kopii zapasowej.


### <a name="powershell"></a>PowerShell

Użyj [ASDatabase przywracania](https://docs.microsoft.com/sql/analysis-services/powershell/restore-asdatabase-cmdlet) polecenia cmdlet.


## <a name="related-information"></a>Informacje pokrewne

[Konta usługi Azure storage](../storage/common/storage-create-storage-account.md)  
[Wysoka dostępność](analysis-services-bcdr.md)     
[Zarządzanie usług Azure Analysis Services](analysis-services-manage.md)
