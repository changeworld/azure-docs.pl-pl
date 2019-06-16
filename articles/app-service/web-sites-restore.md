---
title: Przywracanie aplikacji — usługa Azure App Service
description: Dowiedz się, jak przywrócić aplikację z kopii zapasowej.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: 4444dbf7-363c-47e2-b24a-dbd45cb08491
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 1e8bebdb3f54ac59ec19ef798cc3e794473bbec0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60832513"
---
# <a name="restore-an-app-in-azure"></a>Przywracanie aplikacji na platformie Azure
W tym artykule pokazano, jak przywrócić aplikację w [usługi Azure App Service](../app-service/overview.md) , wcześniej utworzono kopię zapasową (zobacz [tworzenie kopii zapasowej aplikacji na platformie Azure](manage-backup.md)). Przywracanie aplikacji z jej połączonymi bazami danych na żądanie do poprzedniego stanu lub utworzyć nową aplikację na podstawie jednej z oryginalnej aplikacji tworzenie kopii zapasowych. Usługa Azure App Service obsługuje następujące bazy danych i przywracania kopii zapasowych:
- [SQL Database](https://azure.microsoft.com/services/sql-database/)
- [Azure Database for MySQL](https://azure.microsoft.com/services/mysql)
- [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql)
- [MySQL w aplikacji](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/06/announcing-general-availability-for-mysql-in-app)

Przywracanie z kopii zapasowej jest dostępna dla aplikacji działających w **standardowa** i **Premium** warstwy. Aby uzyskać informacji na temat skalowania aplikacji w górę, zobacz [skalowanie aplikacji na platformie Azure](web-sites-scale.md). **Premium** warstwy umożliwia większą liczbę codziennych kopii zapasowych wykonywanych niż **standardowa** warstwy.

<a name="PreviousBackup"></a>

## <a name="restore-an-app-from-an-existing-backup"></a>Przywracanie aplikacji z istniejącej kopii zapasowej
1. Na **ustawienia** strony aplikacji w witrynie Azure portal, kliknij przycisk **kopie zapasowe** do wyświetlenia **kopie zapasowe** strony. Następnie kliknij przycisk **przywrócić**.
   
    ![Wybierz opcję Przywróć teraz][ChooseRestoreNow]
2. W **przywrócić** najpierw wybierz źródłowy kopii zapasowej.
   
    ![](./media/web-sites-restore/021ChooseSource1.png)
   
    **Kopia zapasowa aplikacji** opcja powoduje wyświetlenie wszystkich istniejących kopii zapasowych bieżącej aplikacji i można łatwo wybrać.
    **Magazynu** opcja umożliwia wybranie dowolnego plik ZIP kopii zapasowej z wszelkie istniejące konto usługi Azure Storage i kontenerów w ramach subskrypcji.
    Jeśli próbujesz przywrócić z kopii zapasowej innej aplikacji, użyj **magazynu** opcji.
3. Następnie określ miejsce docelowe przywracania aplikacji w **miejsce docelowe przywracania**.
   
    ![](./media/web-sites-restore/022ChooseDestination1.png)
   
   > [!WARNING]
   > Jeśli wybierzesz **Zastąp**, wszystkie istniejące dane w bieżącej aplikacji jest usunięte i zastąpione. Przed kliknięciem przycisku **OK**, upewnij się, że jest dokładnie co chcesz zrobić.
   > 
   > 
   
   > [!WARNING]
   > Jeśli usługi App Service zapisuje dane w bazie danych, gdy są przywracane, może to spowodować objawy, takich jak naruszenie klucza podstawowego i utraty danych. Zalecane jest najpierw Zatrzymaj usługi App Service, przed przystąpieniem do przywracania bazy danych.
   > 
   > 
   
    Możesz wybrać **istniejącej aplikacji** do przywrócenia kopii zapasowej aplikacji do innej aplikacji w tej samej grupie zasobów. Przed użyciem tej opcji należy utworzono już innej aplikacji w grupie zasobów za pomocą funkcji dublowania bazy danych konfiguracji do jednego zdefiniowane w kopii zapasowej aplikacji. Można również utworzyć **New** aplikację w celu przywrócenia swoją zawartość.

4. Kliknij przycisk **OK**.

<a name="StorageAccount"></a>

## <a name="download-or-delete-a-backup-from-a-storage-account"></a>Pobierz lub usuń kopii zapasowej z konta magazynu
1. Z głównego **Przeglądaj** strony w witrynie Azure Portal, wybierz opcję **kont magazynu**. Zostanie wyświetlona lista istniejących kont magazynu.
2. Wybierz konto magazynu, który zawiera kopię zapasową, który chcesz pobrać lub usunąć. Zostanie wyświetlona strona dla konta magazynu.
3. Na stronie tworzenia konta magazynu Wybierz kontener, który chcesz
   
    ![Wyświetl kontenerów][ViewContainers]
4. Wybierz plik kopii zapasowej, który chcesz pobrać lub usunąć.
   
    ![ViewContainers](./media/web-sites-restore/03ViewFiles.png)
5. Kliknij przycisk **Pobierz** lub **Usuń** w zależności od tego, co chcesz zrobić.  

<a name="OperationLogs"></a>

## <a name="monitor-a-restore-operation"></a>Monitorowanie operacji przywracania
Aby wyświetlić szczegółowe informacje o powodzeniu lub niepowodzeniu operacji przywracania aplikacji, przejdź do **dziennika aktywności** strony w witrynie Azure portal.  
 

Przewiń w dół Aby znaleźć żądaną operację przywracania i kliknij, aby go zaznaczyć.

Na stronie szczegółów wyświetla dostępne informacje dotyczące operacji przywracania.

## <a name="automate-with-scripts"></a>Automatyzowanie przy użyciu skryptów

Można zautomatyzować zarządzania kopiami zapasowymi za pomocą skryptów, za pomocą [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lub [programu Azure PowerShell](/powershell/azure/overview).

Aby uzyskać przykłady zobacz:

- [Przykłady interfejsu wiersza polecenia platformy Azure](samples-cli.md)
- [Przykłady dla programu Azure PowerShell](samples-powershell.md)

<!-- ## Next Steps
You can backup and restore App Service apps using REST API. -->


<!-- IMAGES -->
[ChooseRestoreNow]: ./media/web-sites-restore/02ChooseRestoreNow1.png
[ViewContainers]: ./media/web-sites-restore/03ViewContainers.png
