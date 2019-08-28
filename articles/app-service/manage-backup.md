---
title: Tworzenie kopii zapasowej aplikacji — Azure App Service
description: Dowiedz się, jak tworzyć kopie zapasowe aplikacji w Azure App Service.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: 6223b6bd-84ec-48df-943f-461d84605694
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: d153238c58d72874ddbf7979e68423e21ea7c020
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073157"
---
# <a name="back-up-your-app-in-azure"></a>Tworzenie kopii zapasowej aplikacji na platformie Azure
Funkcja tworzenia kopii zapasowych i przywracania w [Azure App Service](overview.md) umożliwia łatwe tworzenie kopii zapasowych aplikacji ręcznie lub zgodnie z harmonogramem.  Kopie zapasowe można skonfigurować tak, aby były przechowywane przez czas nieokreślony. Możesz przywrócić aplikację do migawki poprzedniego stanu, zastępując istniejącą aplikację lub przywracając ją do innej aplikacji.

Aby uzyskać informacje na temat przywracania aplikacji z kopii zapasowej, zobacz [Przywracanie aplikacji na platformie Azure](web-sites-restore.md).

<a name="whatsbackedup"></a>

## <a name="what-gets-backed-up"></a>Tworzenie kopii zapasowej
App Service można utworzyć kopię zapasową następujących informacji na koncie usługi Azure Storage i kontenerze, dla którego skonfigurowano aplikację do użycia. 

* Konfiguracja aplikacji
* Zawartość pliku
* Baza danych połączona z aplikacją

Następujące rozwiązania bazy danych są obsługiwane z funkcją tworzenia kopii zapasowych: 
   - [SQL Database](https://azure.microsoft.com/services/sql-database/)
   - [Azure Database for MySQL](https://azure.microsoft.com/services/mysql)
   - [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql)
   - [MySQL w aplikacji](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/06/announcing-general-availability-for-mysql-in-app)
 

> [!NOTE]
>  Każda kopia zapasowa jest pełną kopią w trybie offline swojej aplikacji, a nie z aktualizacją przyrostową.
>  

<a name="requirements"></a>

## <a name="requirements-and-restrictions"></a>Wymagania i ograniczenia
* Funkcja tworzenia kopii zapasowych i przywracania wymaga, aby plan App Service znajdował się w warstwie **standardowa** lub **Premium** . Aby uzyskać więcej informacji na temat skalowania planu App Service w celu korzystania z wyższej warstwy, zobacz [skalowanie w górę aplikacji na platformie Azure](manage-scale-up.md).  
  Warstwa **Premium** umożliwia większą liczbę codziennych przez siebie danych niż w warstwie **standardowa** .
* Potrzebujesz konta usługi Azure Storage i kontenera w tej samej subskrypcji co aplikacja, dla której chcesz utworzyć kopię zapasową. Aby uzyskać więcej informacji na temat kont usługi Azure Storage, zobacz [Omówienie konta usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-account-overview).
* Kopie zapasowe mogą mieć do 10 GB zawartości aplikacji i bazy danych. Jeśli rozmiar kopii zapasowej przekracza ten limit, zostanie wyświetlony komunikat o błędzie.
* Kopie zapasowe Azure Database for MySQL z włączonym protokołem SSL nie są obsługiwane. W przypadku skonfigurowania kopii zapasowej zostaną wyświetlone nieudane kopie zapasowe.
* Kopie zapasowe Azure Database for PostgreSQL z włączonym protokołem SSL nie są obsługiwane. W przypadku skonfigurowania kopii zapasowej zostaną wyświetlone nieudane kopie zapasowe.
* Kopie zapasowe baz danych MySQL są tworzone automatycznie bez żadnej konfiguracji. Jeśli ręcznie wprowadzisz ustawienia dla baz danych MySQL w aplikacji, takich jak dodawanie parametrów połączenia, kopie zapasowe mogą nie funkcjonować prawidłowo.
* Korzystanie z konta magazynu z włączoną obsługą zapory jako miejsca docelowego dla kopii zapasowych nie jest obsługiwane. W przypadku skonfigurowania kopii zapasowej zostaną wyświetlone nieudane kopie zapasowe.


<a name="manualbackup"></a>

## <a name="create-a-manual-backup"></a>Ręczne tworzenie kopii zapasowej
1. W [Azure Portal](https://portal.azure.com)przejdź do strony aplikacji, wybierz pozycję **kopie zapasowe**. Zostanie wyświetlona strona **kopie zapasowe** .
   
    ![Strona kopii zapasowych][ChooseBackupsPage]
   
   > [!NOTE]
   > Jeśli zobaczysz następujący komunikat, kliknij go, aby uaktualnić plan App Service przed kontynuowaniem tworzenia kopii zapasowych.
   > Aby uzyskać więcej informacji, zobacz [skalowanie w górę aplikacji na platformie Azure](manage-scale-up.md).  
   > ![Wybierz konto magazynu](./media/web-sites-backup/01UpgradePlan1.png)
   > 
   > 

2. Na stronie **kopia zapasowa** kliknij pozycję **Konfiguruj**
![, a następnie kliknij pozycję Konfiguruj.](./media/web-sites-backup/ClickConfigure1.png)
3. Na stronie **Konfiguracja kopii zapasowej** kliknij **pozycję Magazyn: Nie skonfigurowano** do konfigurowania konta magazynu.
   
    ![Wybierz konto magazynu][ChooseStorageAccount]
4. Wybierz miejsce docelowe kopii zapasowej, wybierając **konto magazynu** i **kontener**. Konto magazynu musi należeć do tej samej subskrypcji co aplikacja, dla której ma zostać utworzona kopia zapasowa. Jeśli chcesz, możesz utworzyć nowe konto magazynu lub nowy kontener na odpowiednich stronach. Gdy skończysz, kliknij przycisk **Wybierz**.
   
    ![Wybierz konto magazynu](./media/web-sites-backup/02ChooseStorageAccount1-1.png)
5. Na stronie **Konfiguracja kopii zapasowej** , która jest nadal otwarta, można skonfigurować **bazę danych kopii zapasowej**, a następnie wybrać bazy danych, które mają zostać uwzględnione w kopiach zapasowych (SQL Database lub MySQL), a następnie kliknąć przycisk **OK**.  
   
    ![Wybierz konto magazynu](./media/web-sites-backup/03ConfigureDatabase1.png)
   
   > [!NOTE]
   > Aby baza danych była wyświetlana na tej liście, jej parametry połączenia muszą znajdować się w sekcji **Parametry połączenia** na stronie **Ustawienia aplikacji** dla aplikacji. 
   >
   > Kopie zapasowe baz danych MySQL są tworzone automatycznie bez żadnej konfiguracji. Jeśli ręcznie wprowadzisz ustawienia dla baz danych MySQL w aplikacji, takich jak dodawanie parametrów połączenia, kopie zapasowe mogą nie funkcjonować prawidłowo.
   > 
   > 
6. Na stronie **Konfiguracja kopii zapasowej** kliknij przycisk **Zapisz**.    
7. Na stronie **kopie zapasowe** kliknij pozycję **kopia zapasowa**.
   
    ![Przycisk BackUpNow][BackUpNow]
   
    Podczas procesu tworzenia kopii zapasowej zobaczysz komunikat o postępie.

Po skonfigurowaniu konta magazynu i kontenera można w dowolnym momencie zainicjować ręczną kopię zapasową.  

<a name="automatedbackups"></a>

## <a name="configure-automated-backups"></a>Konfigurowanie zautomatyzowanych kopii zapasowych
1. Na stronie **Konfiguracja kopii zapasowej** Skonfiguruj **zaplanowaną kopię zapasową** na wartość **włączone**. 
   
    ![Wybierz konto magazynu](./media/web-sites-backup/05ScheduleBackup1.png)
2. Opcje harmonogramu tworzenia kopii zapasowych zostaną wyświetlone, ustaw **zaplanowane kopie zapasowe** na **włączone**, a następnie skonfiguruj harmonogram tworzenia kopii zapasowych zgodnie z potrzebami i kliknij przycisk **OK**.
   
    ![Włącz automatyczne kopie zapasowe][SetAutomatedBackupOn]

<a name="partialbackups"></a>

## <a name="configure-partial-backups"></a>Skonfiguruj częściowe kopie zapasowe
Czasami nie chcesz tworzyć kopii zapasowej wszystkiego w aplikacji. Oto kilka przykładów:

* Można [skonfigurować cotygodniowe wykonywanie kopii zapasowych](#configure-automated-backups) aplikacji, które zawierają zawartość statyczną, która nigdy nie zmienia się, na przykład stare wpisy w blogu lub obrazy.
* Twoja aplikacja ma ponad 10 GB zawartości (jest to maksymalna ilość, jaką można utworzyć w danym momencie).
* Nie chcesz tworzyć kopii zapasowych plików dziennika.

Częściowe kopie zapasowe umożliwiają wybranie dokładnie tych plików, dla których chcesz utworzyć kopię zapasową.

> [!NOTE]
> Pojedyncze bazy danych w kopii zapasowej mogą być 4 GB maksymalne, ale łączny maksymalny rozmiar kopii zapasowej to 10GB

### <a name="exclude-files-from-your-backup"></a>Wyklucz pliki z kopii zapasowej
Załóżmy, że masz aplikację, która zawiera pliki dziennika i obrazy statyczne, których kopie zapasowe zostały utworzone raz i nie będą zmieniane. W takich przypadkach można wykluczyć te foldery i pliki, które mają być przechowywane w przyszłych kopiach zapasowych. Aby wykluczyć pliki i foldery z kopii zapasowych, Utwórz `_backup.filter` plik `D:\home\site\wwwroot` w folderze aplikacji. Określ listę plików i folderów, które mają zostać wykluczone w tym pliku. 

Łatwym sposobem uzyskiwania dostępu do plików jest użycie kudu. Kliknij pozycję **Zaawansowane narzędzia — > go** dla aplikacji sieci Web, aby uzyskać dostęp do usługi kudu.

![Kudu przy użyciu portalu][kudu-portal]

Zidentyfikuj foldery, które mają zostać wykluczone z kopii zapasowych.  Na przykład, chcesz odfiltrować wyróżniony folder i pliki.

![Folder obrazów][ImagesFolder]

Utwórz plik o nazwie `_backup.filter` i umieść poprzednią listę w pliku, ale Usuń. `D:\home` Wyświetl listę jednego katalogu lub pliku w każdym wierszu. Dlatego zawartość pliku powinna być:
 ```bash
    \site\wwwroot\Images\brand.png
    \site\wwwroot\Images\2014
    \site\wwwroot\Images\2013
```

Przekaż `_backup.filter` plik`D:\home\site\wwwroot\` do katalogu witryny przy użyciu [protokołu FTP](deploy-ftp.md) lub innej metody. Jeśli chcesz, możesz utworzyć plik bezpośrednio przy użyciu kudu `DebugConsole` i wstawić tam zawartość.

Uruchom kopie zapasowe w taki sam sposób, jak zwykle, [ręcznie](#create-a-manual-backup) lub [automatycznie](#configure-automated-backups). Teraz wszystkie pliki i foldery określone w programie `_backup.filter` są wykluczone z przyszłych kopii zapasowych zaplanowanych lub ręcznie zainicjowanych. 

> [!NOTE]
> Przywracasz częściowe kopie zapasowe witryny w taki sam sposób, w jaki [przywracasz zwykłe](web-sites-restore.md)kopie zapasowe. Proces przywracania wykonuje odpowiednie czynności.
> 
> Po przywróceniu pełnej kopii zapasowej cała zawartość w lokacji zostanie zamieniona na kopię zapasową. Jeśli plik znajduje się w witrynie, ale nie w kopii zapasowej, zostanie usunięty. Jednak po przywróceniu częściowej kopii zapasowej każda zawartość znajdująca się w jednym z katalogów zabronionych lub dowolny plik zabroniony jest pozostawiony jako.
> 


<a name="aboutbackups"></a>

## <a name="how-backups-are-stored"></a>Jak są przechowywane kopie zapasowe
Po wykonaniu co najmniej jednej kopii zapasowej dla aplikacji kopie zapasowe są widoczne na stronie **kontenery** Twojego konta magazynu i aplikacji. Na koncie magazynu każda kopia zapasowa składa się`.zip` z pliku zawierającego dane kopii zapasowej `.xml` i pliku, który `.zip` zawiera manifest zawartości pliku. Można rozpakować i przeglądać te pliki, jeśli chcesz uzyskać dostęp do kopii zapasowych bez rzeczywistego wykonywania przywracania aplikacji.

Kopia zapasowa bazy danych aplikacji jest przechowywana w katalogu głównym pliku zip. W przypadku bazy danych SQL jest to plik BACPAC (bez rozszerzenia pliku) i można go zaimportować. Aby utworzyć bazę danych SQL na podstawie eksportu BACPAC, zobacz [Importowanie pliku BACPAC w celu utworzenia nowej bazy danych użytkownika](https://technet.microsoft.com/library/hh710052.aspx).

> [!WARNING]
> Zmiana któregokolwiek z plików w kontenerze **websitebackups** może spowodować, że kopia zapasowa stanie się nieprawidłowa i w związku z tym nie jest dostępnych.
> 
> 

## <a name="automate-with-scripts"></a>Automatyzowanie przy użyciu skryptów

Zarządzanie tworzeniem kopii zapasowych za pomocą skryptów można zautomatyzować przy użyciu [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lub [Azure PowerShell](/powershell/azure/overview).

Aby zapoznać się z przykładami, zobacz:

- [Przykłady interfejsu wiersza polecenia platformy Azure](samples-cli.md)
- [Przykłady dla programu Azure PowerShell](samples-powershell.md)

<a name="nextsteps"></a>

## <a name="next-steps"></a>Następne kroki
Aby uzyskać informacje na temat przywracania aplikacji z kopii zapasowej, zobacz [Przywracanie aplikacji na platformie Azure](web-sites-restore.md). 


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

