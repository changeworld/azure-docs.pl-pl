---
title: Tworzenie kopii zapasowej aplikacji — usłudze Azure App Service
description: Dowiedz się, jak tworzyć kopie zapasowe swoich aplikacji w usłudze Azure App Service.
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
ms.custom: seodec18
ms.openlocfilehash: 7e697329e83b530157e490b04f5155d28d243bb6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61271803"
---
# <a name="back-up-your-app-in-azure"></a>Tworzenie kopii zapasowej aplikacji na platformie Azure
Funkcja tworzenia kopii zapasowych i przywracania w [usługi Azure App Service](overview.md) umożliwia łatwe tworzenie kopii zapasowych aplikacji ręcznie lub zgodnie z harmonogramem. Aplikację można przywrócić do migawki poprzedniego stanu przez zastąpienie istniejącej aplikacji lub przywrócenie do innej aplikacji. 

Aby uzyskać informacji o przywracaniu aplikacji z kopii zapasowej, zobacz [Przywracanie aplikacji na platformie Azure](web-sites-restore.md).

<a name="whatsbackedup"></a>

## <a name="what-gets-backed-up"></a>Kopiami zapasowymi
Usługa App Service można wykonać kopię zapasową następujące informacje konta magazynu platformy Azure i kontener, który skonfigurowano aplikację do używania. 

* Konfiguracja aplikacji
* Zawartość pliku
* Baza danych połączoną z aplikacją

Następujące rozwiązania bazy danych są obsługiwane za pomocą funkcji Kopia zapasowa: 
   - [SQL Database](https://azure.microsoft.com/services/sql-database/)
   - [Azure Database for MySQL](https://azure.microsoft.com/services/mysql)
   - [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql)
   - [MySQL w aplikacji](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/06/announcing-general-availability-for-mysql-in-app)
 

> [!NOTE]
>  Każda kopia zapasowa jest w trybie offline pełną kopię swojej aplikacji, a nie aktualizację przyrostową.
>  

<a name="requirements"></a>

## <a name="requirements-and-restrictions"></a>Wymagania i ograniczenia
* Funkcja Kopia zapasowa i przywracanie wymaga planu usługi App Service w **standardowa** warstwy lub **Premium** warstwy. Aby uzyskać więcej informacji na temat skalowania planu usługi App Service, aby korzystać z wyższej warstwy, zobacz [skalowanie aplikacji na platformie Azure](web-sites-scale.md).  
  **Premium** warstwy umożliwia większą liczbę dziennych kopii ups niż **standardowa** warstwy.
* Potrzebujesz konta usługi Azure storage i kontenera w tej samej subskrypcji co aplikacja, którą chcesz utworzyć kopię zapasową. Aby uzyskać więcej informacji na temat konta usługi Azure storage, zobacz [Przegląd konta usługi Azure storage](https://docs.microsoft.com/azure/storage/common/storage-account-overview).
* Kopie zapasowe mogą być 10 GB zawartości aplikacji i bazy danych. Jeśli rozmiar kopii zapasowej przekracza ten limit, wystąpi błąd.
* Kopie zapasowe protokołu SSL, włączone — Azure Database dla MySQL nie jest obsługiwana. Jeśli skonfigurowano kopię zapasową, zostanie wyświetlony wykonywania kopii zapasowej.
* Kopie zapasowe SSL włączone — Azure Database dla PostgreSQL nie jest obsługiwana. Jeśli skonfigurowano kopię zapasową, zostanie wyświetlony wykonywania kopii zapasowej.
* Bazy danych MySQL w aplikacji są automatycznie do kopii zapasowej bez żadnej konfiguracji. Możesz wprowadzić ręcznie ustawienia bazy danych MySQL w aplikacji, takich jak dodawanie parametrów połączenia, tworzenie kopii zapasowych może nie działać poprawnie.
* Za pomocą zapory włączone konto magazynu, jako miejsca docelowego dla kopii zapasowych nie jest obsługiwana. Jeśli skonfigurowano kopię zapasową, zostanie wyświetlony wykonywania kopii zapasowej.


<a name="manualbackup"></a>

## <a name="create-a-manual-backup"></a>Ręczne tworzenie kopii zapasowej
1. W [witryny Azure portal](https://portal.azure.com), przejdź do strony aplikacji, wybierz **kopie zapasowe**. **Kopie zapasowe** zostanie wyświetlona strona.
   
    ![Strona tworzenia kopii zapasowych][ChooseBackupsPage]
   
   > [!NOTE]
   > Jeśli zostanie wyświetlony następujący komunikat, kliknij je, aby uaktualnić swój plan usługi App Service, zanim będzie można kontynuować z kopiami zapasowymi.
   > Aby uzyskać więcej informacji, zobacz [skalowanie aplikacji na platformie Azure](web-sites-scale.md).  
   > ![Wybierz konto magazynu](./media/web-sites-backup/01UpgradePlan1.png)
   > 
   > 

2. W **kopii zapasowej** stronie, kliknij **Konfiguruj**
![kliknij przycisk Konfiguruj](./media/web-sites-backup/ClickConfigure1.png)
3. W **konfiguracji kopii zapasowej** kliknij **magazynu: Nieskonfigurowane** konfigurowania konta magazynu.
   
    ![Wybierz konto magazynu][ChooseStorageAccount]
4. Wybierz miejsce docelowe kopii zapasowej, wybierając **konta magazynu** i **kontenera**. Konto magazynu musi należeć do tej samej subskrypcji co aplikacja, który chcesz utworzyć kopię zapasową. Jeśli chcesz, można utworzyć nowe konto magazynu lub nowy kontener na odpowiednich stronach. Gdy wszystko będzie gotowe, kliknij przycisk **wybierz**.
   
    ![Wybierz konto magazynu](./media/web-sites-backup/02ChooseStorageAccount1-1.png)
5. W **konfiguracji kopii zapasowej** strony, które są pozostawione otwarte, można skonfigurować **Backup Database**, wybierz bazy danych, które chcesz uwzględnić w kopii zapasowych (SQL database ani MySQL), a następnie kliknij przycisk **OK**.  
   
    ![Wybierz konto magazynu](./media/web-sites-backup/03ConfigureDatabase1.png)
   
   > [!NOTE]
   > Aby baza danych są wyświetlane na tej liście, jego parametry połączenia muszą istnieć w **parametry połączenia** części **ustawienia aplikacji** strony aplikacji. 
   >
   > Bazy danych MySQL w aplikacji są automatycznie do kopii zapasowej bez żadnej konfiguracji. Możesz wprowadzić ręcznie ustawienia bazy danych MySQL w aplikacji, takich jak dodawanie parametrów połączenia, tworzenie kopii zapasowych może nie działać poprawnie.
   > 
   > 
6. W **konfiguracji kopii zapasowej** kliknij **Zapisz**.    
7. W **kopie zapasowe** kliknij **kopii zapasowej**.
   
    ![Przycisk BackUpNow][BackUpNow]
   
    Zostanie wyświetlony postęp podczas procesu tworzenia kopii zapasowej.

Po skonfigurowaniu konta magazynu i kontener, w dowolnym momencie można zainicjować ręcznego tworzenia kopii zapasowej.  

<a name="automatedbackups"></a>

## <a name="configure-automated-backups"></a>Konfigurowanie automatycznych kopii zapasowych
1. W **konfiguracji kopii zapasowej** ustaw **zaplanowanych kopii zapasowych** do **na**. 
   
    ![Wybierz konto magazynu](./media/web-sites-backup/05ScheduleBackup1.png)
2. Ustaw harmonogram tworzenia kopii zapasowych, opcje będą wyświetlane, **zaplanowanych kopii zapasowych** do **na**, a następnie skonfiguruj harmonogram tworzenia kopii zapasowych zgodnie z potrzebami i kliknij przycisk **OK**.
   
    ![Włącz automatyczne kopie zapasowe][SetAutomatedBackupOn]

<a name="partialbackups"></a>

## <a name="configure-partial-backups"></a>Konfigurowanie częściowych kopii zapasowych
Czasami nie chcesz utworzyć kopię zapasową wszystko, co w aplikacji. Oto kilka przykładów:

* Możesz [Konfigurowanie cotygodniowe kopie zapasowe](#configure-automated-backups) aplikacji, który zawiera zawartość statyczną, która nigdy nie zmieni się, takich jak stare wpisy w blogu lub obrazy.
* Twoja aplikacja ma ponad 10 GB zawartości (czyli maksymalna ilość kopię zapasową można wykonywać w czasie).
* Nie chcesz utworzyć kopię zapasową plików dziennika.

Częściowych kopii zapasowych pozwala wybrać dokładnie pliki, które chcesz utworzyć kopię zapasową.

> [!NOTE]
> Pojedyncze bazy danych w kopii zapasowej może być 4GB max, ale maksymalny łączny rozmiar kopii zapasowej wynosi 10GB

### <a name="exclude-files-from-your-backup"></a>Wyklucz pliki z kopii zapasowej
Załóżmy, że masz aplikację, która zawiera pliki dziennika i obrazy statyczne, które zostały kopii zapasowej raz i nie chce zmienić. W takich przypadkach można wykluczyć te foldery i pliki znajdują się w przyszłości kopii zapasowych. Aby wykluczyć pliki i foldery z kopii zapasowych, należy utworzyć `_backup.filter` w pliku `D:\home\site\wwwroot` folderu aplikacji. Określ listę plików i folderów, które chcesz wykluczyć, w tym pliku. 

Jest łatwy sposób dostępu do plików można użyć narzędzia Kudu. Kliknij przycisk **zaawansowane narzędzia -> Przejdź** ustawienia dla aplikacji sieci web dostęp Kudu.

![Aparat kudu przy użyciu portalu][kudu-portal]

Określ foldery, które mają być wykluczone z kopii zapasowych.  Na przykład chcesz odfiltrować wyróżnione folderów i plików.

![Folder obrazów][ImagesFolder]

Utwórz plik o nazwie `_backup.filter` powyższej listy należy umieścić w pliku, ale Usuń `D:\home`. Lista jednego katalogu lub pliku w każdym wierszu. Aby zawartość pliku powinna być:
 ```bash
    \site\wwwroot\Images\brand.png
    \site\wwwroot\Images\2014
    \site\wwwroot\Images\2013
```

Przekaż `_backup.filter` plik `D:\home\site\wwwroot\` katalogu przy użyciu witryny [ftp](deploy-ftp.md) lub innej metody. Jeśli chcesz, można utworzyć pliku bezpośrednio przy użyciu narzędzia Kudu `DebugConsole` i Wstaw zawartość istnieje.

Uruchamianie tworzenia kopii zapasowych tak samo będzie robisz to zazwyczaj, [ręcznie](#create-a-manual-backup) lub [automatycznie](#configure-automated-backups). Teraz, wszystkie pliki i foldery, które są określone w `_backup.filter` jest wykluczony z kolejnych kopii zapasowych, według harmonogramu lub ręcznie zainicjowane. 

> [!NOTE]
> Przywróć ten sam sposób, w jaki częściowych kopii zapasowych lokacji [przywrócić zwykłej kopii zapasowej](web-sites-restore.md). Proces przywracania jest właściwe.
> 
> Po przywróceniu pełną kopię zapasową całej zawartości w witrynie zastępuje się ze względu na w kopii zapasowej. Jeśli plik znajduje się w witrynie, ale nie w kopii zapasowej zostaje usunięty. Jednak po przywróceniu częściowej kopii zapasowej, zawartość, która znajduje się w jednym z katalogów zabronionych lub dowolnego pliku zabroniony, pozostanie jest.
> 


<a name="aboutbackups"></a>

## <a name="how-backups-are-stored"></a>Jak są przechowywane kopie zapasowe
Po wybraniu jednego lub więcej kopii zapasowych dla aplikacji, kopie zapasowe są widoczne na **kontenery** strony swojego konta magazynu i aplikacji. W ramach konta magazynu każdej kopii zapasowej składa się z`.zip` pliku, który zawiera dane kopii zapasowej i `.xml` plik zawierający manifest z `.zip` zawartość pliku. Można rozpakować i przeglądania tych plików, jeśli chcesz uzyskać dostęp do kopii zapasowych bez rzeczywistego wykonania przywracania aplikacji.

Kopia zapasowa bazy danych dla aplikacji są przechowywane w katalogu głównym pliku zip. Usługi SQL database to jest plik BACPAC (bez rozszerzenia) i można je zaimportować. Aby utworzyć oparte na eksportowanie pliku BACPAC bazy danych SQL, zobacz [Importowanie pliku BACPAC do utworzenia nowej bazy danych użytkownika](https://technet.microsoft.com/library/hh710052.aspx).

> [!WARNING]
> Zmieniając dowolne pliki znajdujące się w Twojej **websitebackups** kontener może spowodować, że kopia zapasowa stają się nieprawidłowe i w związku z tym nie-możliwością przywrócenia.
> 
> 

## <a name="automate-with-scripts"></a>Automatyzowanie przy użyciu skryptów

Można zautomatyzować zarządzania kopiami zapasowymi za pomocą skryptów, za pomocą [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lub [programu Azure PowerShell](/powershell/azure/overview).

Aby uzyskać przykłady zobacz:

- [Przykłady interfejsu wiersza polecenia platformy Azure](samples-cli.md)
- [Przykłady dla programu Azure PowerShell](samples-powershell.md)

<a name="nextsteps"></a>

## <a name="next-steps"></a>Następne kroki
Aby uzyskać informacji o przywracaniu aplikacji z kopii zapasowej, zobacz [Przywracanie aplikacji na platformie Azure](web-sites-restore.md). 


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

