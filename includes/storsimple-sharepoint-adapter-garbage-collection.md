---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 0b5d9deacdd4266da30f17c95b6e575a652d2f76
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66155865"
---
W tej procedurze wykonasz następujące czynności:

1. [Przygotowywanie do uruchomienia pliku wykonywalnego Element utrzymujący](#to-prepare-to-run-the-maintainer) .
2. [Przygotowanie bazy danych zawartości i Kosza do natychmiastowego usuwania oddzielonych obiektów blob](#to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs).
3. [Uruchom Maintainer.exe](#to-run-the-maintainer).
4. [Przywróć bazę danych zawartości i ustawień Kosza](#to-revert-the-content-database-and-recycle-bin-settings).

#### <a name="to-prepare-to-run-the-maintainer"></a>Aby przygotować się do uruchomienia Element utrzymujący
1. Na serwerze frontonu sieci Web Otwórz powłokę zarządzania programu SharePoint 2013, jako administrator.
2. Przejdź do folderu *dysk rozruchowy*: \Program Files\Microsoft 10.50\Maintainer SQL zdalnego magazynu obiektów Blob\.
3. Zmień nazwę **Microsoft.Data.SqlRemoteBlobs.Maintainer.exe.config** do **web.config**.
4. Użyj `aspnet_regiis -pdf connectionStrings` do odszyfrowania pliku web.config.
5. W pliku web.config odszyfrowane w obszarze `connectionStrings` węzła, Dodaj parametry połączenia dla wystąpienia programu SQL server i nazwę bazy danych zawartości. Zobacz poniższy przykład.
   
    `<add name=”RBSMaintainerConnectionWSSContent” connectionString="Data Source=SHRPT13-SQL12\SHRPT13;Initial Catalog=WSS_Content;Integrated Security=True;Application Name=&quot;Remote Blob Storage Maintainer for WSS_Content&quot;" providerName="System.Data.SqlClient" />`
6. Użyj `aspnet_regiis –pef connectionStrings` można ponownie zaszyfrować plik web.config. 
7. Zmień nazwę pliku web.config Microsoft.Data.SqlRemoteBlobs.Maintainer.exe.config. 

#### <a name="to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs"></a>Aby przygotować zawartość bazy danych i Kosza, aby od razu usuwa oddzielonych obiektów blob
1. Na serwerze SQL w programie SQL Management Studio, uruchom następujące zapytania aktualizacji dla docelowej bazy danych zawartości: 
   
       `use WSS_Content`
   
       `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ’time 00:00:00’`
   
       `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’time 00:00:00’`
2. Na serwerze frontonu sieci web w obszarze **witryny Administracja centralna**, Edytuj **ustawienia ogólne aplikacji sieci Web** dla żądanej zawartości bazy danych można tymczasowo wyłączyć Kosza. Ta akcja spowoduje również Opróżnij Kosz, dla wszystkich powiązanych zbiorach witryn. Aby to zrobić, kliknij przycisk **witryny Administracja centralna** -> **Zarządzanie aplikacjami** -> **aplikacji sieci Web (aplikacje sieci web Zarządzanie)**  ->  **SharePoint — 80** -> **ustawienia ogólne aplikacji**. Ustaw **stan Kosza** do **OFF**.
   
    ![Ustawienia ogólne aplikacji sieci Web](./media/storsimple-sharepoint-adapter-garbage-collection/HCS_WebApplicationGeneralSettings-include.png)

#### <a name="to-run-the-maintainer"></a>Aby uruchomić Element utrzymujący
* Na serwerze frontonu sieci web w powłoce zarządzania programu SharePoint 2013, uruchom Element utrzymujący w następujący sposób:
  
      `Microsoft.Data.SqlRemoteBlobs.Maintainer.exe -ConnectionStringName RBSMaintainerConnectionWSSContent -Operation GarbageCollection -GarbageCollectionPhases rdo`
  
  > [!NOTE]
  > Tylko `GarbageCollection` operacja jest obsługiwana dla urządzenia StorSimple w tej chwili. Należy również zauważyć, że parametry wystawiony dla Microsoft.Data.SqlRemoteBlobs.Maintainer.exe jest uwzględniana wielkość liter. 
  > 
  > 

#### <a name="to-revert-the-content-database-and-recycle-bin-settings"></a>Aby przywrócić bazę danych zawartości i ustawień Kosza
1. Na serwerze SQL w programie SQL Management Studio, uruchom następujące zapytania aktualizacji dla docelowej bazy danych zawartości:
   
      `use WSS_Content`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ‘days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘orphan_scan_period’ , ’days 30’`
2. Na serwerze frontonu sieci web w **witryny Administracja centralna**, Edytuj **ustawienia ogólne aplikacji sieci Web** dla żądanej zawartości bazy danych ponowne włączenie Kosza. Aby to zrobić, kliknij przycisk **witryny Administracja centralna** -> **Zarządzanie aplikacjami** -> **aplikacji sieci Web (aplikacje sieci web Zarządzanie)**  ->  **SharePoint — 80** -> **ustawienia ogólne aplikacji**. Ustaw stan Recycle Bin na **ON**.

