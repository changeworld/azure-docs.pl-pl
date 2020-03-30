---
title: Przywracanie aplikacji z kopii zapasowej
description: Dowiedz się, jak przywrócić aplikację z kopii zapasowej. Niektóre połączone bazy danych można przywrócić wraz z aplikacją w jednej operacji.
ms.assetid: 4444dbf7-363c-47e2-b24a-dbd45cb08491
ms.topic: article
ms.date: 07/06/2016
ms.custom: seodec18
ms.openlocfilehash: 1295080d0eec7a4e88029cdadd85863f5f40d034
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74689246"
---
# <a name="restore-an-app-in-azure"></a>Przywracanie aplikacji na platformie Azure
W tym artykule pokazano, jak przywrócić aplikację w [usłudze Azure App Service,](../app-service/overview.md) której wcześniej wykonałeś kopię zapasową (zobacz [Utwórz kopię zapasową aplikacji na platformie Azure).](manage-backup.md) Możesz przywrócić aplikację z połączonymi bazami danych na żądanie do poprzedniego stanu lub utworzyć nową aplikację na podstawie jednej z kopii zapasowych oryginalnej aplikacji. Usługa Azure App Service obsługuje następujące bazy danych do tworzenia kopii zapasowych i przywracania:
- [Baza danych SQL](https://azure.microsoft.com/services/sql-database/)
- [Azure Database for MySQL](https://azure.microsoft.com/services/mysql)
- [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql)
- [MySQL w aplikacji](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/06/announcing-general-availability-for-mysql-in-app)

Przywracanie z kopii zapasowych jest dostępne dla aplikacji działających w warstwie **Standardowa** i **Premium.** Aby uzyskać informacje dotyczące skalowania aplikacji, zobacz [Skalowanie w górę aplikacji na platformie Azure](manage-scale-up.md). **Warstwa Premium** umożliwia wykonywanie większej liczby codziennych kopii zapasowych niż **warstwa standardowa.**

<a name="PreviousBackup"></a>

## <a name="restore-an-app-from-an-existing-backup"></a>Przywracanie aplikacji z istniejącej kopii zapasowej
1. Na stronie **Ustawienia** aplikacji w witrynie Azure portal kliknij pozycję **Kopie zapasowe,** aby wyświetlić stronę **Kopie zapasowe.** Następnie kliknij przycisk **Przywróć**.
   
    ![Wybierz przywracanie teraz][ChooseRestoreNow]
2. Na stronie **Przywracanie** najpierw wybierz źródło kopii zapasowej.
   
    ![](./media/web-sites-restore/021ChooseSource1.png)
   
    Opcja **tworzenia kopii zapasowej aplikacji** pokazuje wszystkie istniejące kopie zapasowe bieżącej aplikacji i można łatwo wybrać jedną.
    Opcja **Magazyn** umożliwia wybranie dowolnego zapasowego pliku ZIP z dowolnego istniejącego konta i kontenera usługi Azure Storage w ramach subskrypcji.
    Jeśli próbujesz przywrócić kopię zapasową innej aplikacji, użyj opcji **Magazyn.**
3. Następnie określ miejsce docelowe przywracania aplikacji w **obszarze Przywróć miejsce docelowe**.
   
    ![](./media/web-sites-restore/022ChooseDestination1.png)
   
   > [!WARNING]
   > Jeśli wybierzesz **opcję Zastąpienie,** wszystkie istniejące dane w bieżącej aplikacji zostaną wymazane i zastąpione. Przed kliknięciem **przycisku OK**upewnij się, że jest to dokładnie to, co chcesz zrobić.
   > 
   > 
   
   > [!WARNING]
   > Jeśli usługa App Service zapisuje dane do bazy danych podczas przywracania, może to spowodować objawy, takie jak naruszenie klucza podstawowego i utraty danych. Zaleca się, aby najpierw zatrzymać usługę App Service przed rozpoczęciem przywracania bazy danych.
   > 
   > 
   
    Możesz wybrać **istniejącą aplikację,** aby przywrócić kopię zapasową aplikacji do innej aplikacji w tej samej grupie zasobów. Przed użyciem tej opcji należy już utworzyć inną aplikację w grupie zasobów z dublowanie konfiguracji bazy danych do tej zdefiniowanej w kopii zapasowej aplikacji. Możesz też utworzyć **nową** aplikację, aby przywrócić zawartość.

4. Kliknij przycisk **OK**.

<a name="StorageAccount"></a>

## <a name="download-or-delete-a-backup-from-a-storage-account"></a>Pobieranie lub usuwanie kopii zapasowej z konta magazynu
1. Na głównej stronie **przeglądania** witryny Azure portal wybierz pozycję **Konta magazynu**. Zostanie wyświetlona lista istniejących kont magazynu.
2. Wybierz konto magazynu zawierające kopię zapasową, którą chcesz pobrać lub usunąć. Zostanie wyświetlona strona dla konta magazynu.
3. Na stronie konta magazynu wybierz odpowiedni kontener
   
    ![Wyświetl kontenery][ViewContainers]
4. Wybierz plik kopii zapasowej, który chcesz pobrać lub usunąć.
   
    ![Wyświetlenia](./media/web-sites-restore/03ViewFiles.png)
5. Kliknij **pozycję Pobierz** lub **Usuń** w zależności od tego, co chcesz zrobić.  

<a name="OperationLogs"></a>

## <a name="monitor-a-restore-operation"></a>Monitorowanie operacji przywracania
Aby wyświetlić szczegółowe informacje o powodzenie lub niepowodzenie operacji przywracania aplikacji, przejdź do **dziennika aktywności** strony w witrynie Azure portal.  
 

Przewiń w dół, aby znaleźć żądaną operację przywracania, a następnie kliknij, aby ją zaznaczyć.

Na stronie szczegółów są wyświetlane dostępne informacje związane z operacją przywracania.

## <a name="automate-with-scripts"></a>Automatyzowanie przy użyciu skryptów

Zarządzanie kopiami zapasowymi można zautomatyzować za pomocą skryptów przy użyciu [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lub programu Azure [PowerShell.](/powershell/azure/overview)

Próbki można znaleźć w:

- [Przykłady interfejsu wiersza polecenia platformy Azure](samples-cli.md)
- [Przykłady programu Azure PowerShell](samples-powershell.md)

<!-- ## Next Steps
You can backup and restore App Service apps using REST API. -->


<!-- IMAGES -->
[ChooseRestoreNow]: ./media/web-sites-restore/02ChooseRestoreNow1.png
[ViewContainers]: ./media/web-sites-restore/03ViewContainers.png
