---
title: Tworzenie kopii zapasowej aplikacji na platformie Azure
description: Dowiedz się, jak tworzyć kopie zapasowe aplikacji w usłudze Azure App Service.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: 6223b6bd-84ec-48df-943f-461d84605694
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
ms.openlocfilehash: 66423fecce92cd933cc8680810aa2abe5e25a90d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32153586"
---
# <a name="back-up-your-app-in-azure"></a>Tworzenie kopii zapasowej aplikacji na platformie Azure
Funkcja Kopia zapasowa i przywracanie w [usłudze Azure App Service](app-service-web-overview.md) umożliwia łatwe tworzenie kopii zapasowych aplikacji, ręcznie lub zgodnie z harmonogramem. Zastępowanie istniejących aplikacji lub przywracania do innej aplikacji, można przywrócić aplikację do migawki poprzedniego stanu. 

Aby uzyskać informacje na przywracanie z kopii zapasowej aplikacji, zobacz [Przywracanie aplikacji na platformie Azure](web-sites-restore.md).

<a name="whatsbackedup"></a>

## <a name="what-gets-backed-up"></a>Kopiami zapasowymi
Usługi aplikacji — kopię zapasową można wykonać poniższe informacje konto magazynu Azure i kontener, który skonfigurowano do używania aplikacji. 

* Konfiguracja aplikacji
* Zawartość pliku
* Bazy danych jest podłączony do aplikacji

Obsługiwane są następujące rozwiązania bazy danych przy użyciu funkcji tworzenia kopii zapasowej: 
   - [SQL Database](https://azure.microsoft.com/services/sql-database/)
   - [Bazy danych platformy Azure dla programu MySQL (wersja zapoznawcza)](https://azure.microsoft.com/services/mysql)
   - [Bazy danych platformy Azure dla PostgreSQL (wersja zapoznawcza)](https://azure.microsoft.com/services/postgres)
   - [MySQL w aplikacji](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/06/announcing-general-availability-for-mysql-in-app)
 

> [!NOTE]
>  Każda kopia zapasowa jest w trybie offline pełną kopię aplikacji, nie aktualizację przyrostową.
>  

<a name="requirements"></a>

## <a name="requirements-and-restrictions"></a>Wymagania i ograniczenia
* Funkcja tworzenia kopii zapasowej i przywracania wymaga planu usługi aplikacji w **standardowe** warstwy lub **Premium** warstwy. Aby uzyskać więcej informacji na temat skalowania swój plan usługi aplikacji, aby użyć wyższego poziomu, zobacz [skalowanie w górę aplikacji na platformie Azure](web-sites-scale.md).  
  **Premium** warstwy umożliwia większej liczbie codziennie kopii ups niż **standardowe** warstwy.
* Wymagane jest konto magazynu Azure i kontener w tej samej subskrypcji co aplikację, którą chcesz utworzyć kopię zapasową. Aby uzyskać więcej informacji o kontach magazynu Azure, zobacz [łącza](#moreaboutstorage) na końcu tego artykułu.
* Kopie zapasowe mogą być zawartości aplikacji i bazy danych do 10 GB. Jeśli rozmiar kopii zapasowej przekracza ten limit, wystąpi błąd.

<a name="manualbackup"></a>

## <a name="create-a-manual-backup"></a>Ręczne tworzenie kopii zapasowej
1. W [portalu Azure](https://portal.azure.com), przejdź do strony aplikacji, wybierz **kopii zapasowych**. **Kopii zapasowych** zostanie wyświetlona strona.
   
    ![Strona kopii zapasowych][ChooseBackupsPage]
   
   > [!NOTE]
   > Jeśli zostanie wyświetlony następujący komunikat, kliknij go, aby uaktualnić swój plan usługi aplikacji, aby móc kontynuować wykonywanie kopii zapasowych.
   > Aby uzyskać więcej informacji, zobacz [skalowanie w górę aplikacji na platformie Azure](web-sites-scale.md).  
   > ![Wybierz konto magazynu](./media/web-sites-backup/01UpgradePlan1.png)
   > 
   > 

2. W **kopii zapasowej** strony, kliknij przycisk **Konfiguruj**
![kliknij przycisk Konfiguruj.](./media/web-sites-backup/ClickConfigure1.png)
3. W **konfiguracji kopii zapasowej** kliknij przycisk **magazynu: nieskonfigurowane** konfigurowania konta magazynu.
   
    ![Wybierz konto magazynu][ChooseStorageAccount]
4. Wybierz miejsce docelowe kopii zapasowej, wybierając **konta magazynu** i **kontenera**. Konto magazynu muszą należeć do tej samej subskrypcji co aplikacja, którą chcesz utworzyć kopię zapasową. Jeśli chcesz, można utworzyć nowe konto magazynu lub nowy kontener w odpowiednich stron. Gdy wszystko będzie gotowe, kliknij przycisk **wybierz**.
   
    ![Wybierz konto magazynu](./media/web-sites-backup/02ChooseStorageAccount1-1.png)
5. W **konfiguracji kopii zapasowej** strony, którą nadal pozostanie otwarty, możesz skonfigurować **instrukcji Backup Database**, wybierz bazy danych mają zostać uwzględnione w kopii zapasowych (baza danych SQL lub MySQL), a następnie kliknij przycisk **OK**.  
   
    ![Wybierz konto magazynu](./media/web-sites-backup/03ConfigureDatabase1.png)
   
   > [!NOTE]
   > Aby baza danych ma na liście, parametrach połączenia musi istnieć w **parametry połączenia** sekcji **ustawienia aplikacji** strony dla aplikacji.
   > 
   > 
6. W **konfiguracji kopii zapasowej** kliknij przycisk **zapisać**.    
7. W **kopii zapasowych** kliknij przycisk **kopii zapasowej**.
   
    ![Przycisk BackUpNow][BackUpNow]
   
    Zostanie wyświetlony komunikat z postępu podczas procesu tworzenia kopii zapasowej.

Po skonfigurowaniu konta magazynu i kontener w dowolnym momencie można zainicjować ręcznego tworzenia kopii zapasowej.  

<a name="automatedbackups"></a>

## <a name="configure-automated-backups"></a>Skonfiguruj automatyczne kopie zapasowe
1. W **konfiguracji kopii zapasowej** ustaw **zaplanowanej kopii zapasowej** do **na**. 
   
    ![Wybierz konto magazynu](./media/web-sites-backup/05ScheduleBackup1.png)
2. Ustaw harmonogram tworzenia kopii zapasowych zostaną wyświetlone opcje, **zaplanowanych kopii zapasowych** do **na**następnie skonfiguruj harmonogram tworzenia kopii zapasowych zgodnie z potrzebami i kliknij **OK**.
   
    ![Włącz automatyczne kopie zapasowe][SetAutomatedBackupOn]

<a name="partialbackups"></a>

## <a name="configure-partial-backups"></a>Skonfiguruj częściowych kopii zapasowych
Czasami nie chcesz utworzyć kopię zapasową wszystko, co w aplikacji. Oto kilka przykładów:

* Możesz [Konfigurowanie cotygodniowe kopie zapasowe](web-sites-backup.md#configure-automated-backups) aplikacji zawierający zawartości statycznej, który nigdy nie zmienia, takich jak stare wpisy na blogu lub obrazów.
* Aplikacja ma ponad 10 GB zawartości (wartość maksymalna, którą można tworzyć kopie zapasowe w czasie).
* Nie chcesz utworzyć kopię zapasową plików dziennika.

Zezwalaj częściowych kopii zapasowych wybiera dokładnie pliki, których chcesz utworzyć kopię zapasową.

### <a name="exclude-files-from-your-backup"></a>Wyklucz pliki z kopii zapasowej
Załóżmy, że masz aplikację, która zawiera pliki dziennika i obrazów statycznych, które zostały kopii zapasowej raz i nie będzie można zmienić. W takim przypadku można wykluczyć tych plików i folderów z są przechowywane w kopii zapasowych w przyszłości. Aby wykluczyć pliki i foldery z kopii zapasowych, należy utworzyć `_backup.filter` w pliku `D:\home\site\wwwroot` folderu aplikacji. Określ listę plików i folderów, które chcesz wykluczyć w tym pliku. 

Jest łatwy sposób uzyskać dostęp do plików do użycia Kudu. Kliknij przycisk **zaawansowane narzędzia -> Przejdź** ustawienie Kudu dostępu do aplikacji sieci web.

![Program kudu przy użyciu portalu][kudu-portal]

Określ foldery, które chcesz wykluczyć z kopii zapasowych.  Na przykład chcesz filtrować wyróżnione folderów i plików.

![Folderu Obrazy][ImagesFolder]

Utwórz plik o nazwie `_backup.filter` powyższej listy należy umieścić w pliku, ale usunięcie `D:\home`. Lista jednego katalogu lub pliku w jednym wierszu. Aby zawartość pliku powinna być:
 ```bash
    \site\wwwroot\Images\brand.png
    \site\wwwroot\Images\2014
    \site\wwwroot\Images\2013
```

Przekaż `_backup.filter` pliku `D:\home\site\wwwroot\` katalog swoją witrynę przy użyciu [ftp](app-service-deploy-ftp.md) lub innej metody. Jeśli chcesz, można utworzyć pliku bezpośrednio za pomocą Kudu `DebugConsole` i Wstaw zawartość istnieje.

Uruchamianie tworzenia kopii zapasowych w taki sam sposób, w zwykły sposób, jak [ręcznie](#create-a-manual-backup) lub [automatycznie](#configure-automated-backups). Teraz, wszystkie pliki i foldery, które są określone w `_backup.filter` został wykluczony z kolejnych kopii zapasowych według harmonogramu lub ręcznie zainicjowane. 

> [!NOTE]
> Przywróć częściowych kopii zapasowych lokacji tak samo jak [przywrócić zwykłej kopii zapasowej](web-sites-restore.md). Proces przywracania nie co.
> 
> Po przywróceniu pełnej kopii zapasowej, cała zawartość w witrynie zastępuje się ze względu na w kopii zapasowej. Jeśli plik jest w lokacji, ale nie w kopii zapasowej zostaje usunięta. Jednak po przywróceniu częściowej kopii zapasowej, pozostanie zawartość, która znajduje się w jednym z katalogów zabronione lub dowolnego pliku zabronione, ponieważ jest.
> 


<a name="aboutbackups"></a>

## <a name="how-backups-are-stored"></a>Jak są przechowywane kopie zapasowe
Po wybraniu jednego lub więcej kopii zapasowych dla aplikacji, kopie zapasowe są widoczne na **kontenery** konta magazynu i aplikacji. Na koncie magazynu każdej kopii zapasowej składa się z`.zip` plik zawierający dane kopii zapasowej i `.xml` pliku, który zawiera manifest z `.zip` pliku zawartości. Można rozpakować i przeglądanie tych plików, aby uzyskać dostęp do kopii zapasowych bez rzeczywistego wykonania przywracania aplikacji.

Kopia zapasowa bazy danych dla aplikacji są przechowywane w katalogu głównym pliku zip. Bazy danych SQL jest plikiem pliku BACPAC (bez rozszerzenia pliku) i można je zaimportować. Aby utworzyć oparte na eksportowanie pliku BACPAC bazy danych SQL, zobacz [Importowanie pliku pliku BACPAC, aby utworzyć nową bazę danych użytkownika](http://technet.microsoft.com/library/hh710052.aspx).

> [!WARNING]
> Zmienianie plików w sieci **websitebackups** kontener może spowodować stała się nieprawidłowa i w związku z tym nie-umożliwiająca przywrócenie kopii zapasowej.
> 
> 

## <a name="automate-with-scripts"></a>Automatyzowanie przy użyciu skryptów

Można zautomatyzować zarządzania kopiami zapasowymi za pomocą skryptów przy użyciu [interfejsu wiersza polecenia Azure](/cli/azure/install-azure-cli) lub [programu Azure PowerShell](/powershell/azure/overview).

Aby uzyskać przykłady zobacz:

- [Przykładów dla interfejsu wiersza polecenia platformy Azure](app-service-cli-samples.md)
- [Przykładów dla platformy Azure PowerShell](app-service-powershell-samples.md)

<a name="nextsteps"></a>

## <a name="next-steps"></a>Następne kroki
Aby uzyskać informacje na przywracanie z kopii zapasowej aplikacji, zobacz [Przywracanie aplikacji na platformie Azure](web-sites-restore.md). 


<!-- IMAGES -->
[ChooseBackupsPage]: ./media/web-sites-backup/01ChooseBackupsPage1.png
[ChooseStorageAccount]: ./media/web-sites-backup/02ChooseStorageAccount-1.png
[BackUpNow]: ./media/web-sites-backup/04BackUpNow1.png
[SetAutomatedBackupOn]: ./media/web-sites-backup/06SetAutomatedBackupOn1.png
[SaveIcon]: ./media/web-sites-backup/10SaveIcon.png
[ImagesFolder]: ./media/web-sites-backup/11Images.png
[LogsFolder]: ./media/web-sites-backup/12Logs.png
[GhostUpgradeWarning]: ./media/web-sites-backup/13GhostUpgradeWarning.png
[kudu-portal]:./media/web-sites-backup/kudu-portal.PNG

