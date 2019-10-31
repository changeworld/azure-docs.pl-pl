---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 00d292b3ba2d1b6c7c425d4c9f89188e660ac80d
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73182259"
---
Ta procedura obejmuje następujące czynności:

1. [Przygotuj się, aby uruchomić plik wykonywalny utrzymujący](#to-prepare-to-run-the-maintainer) .
2. [Przygotuj bazę danych zawartości i Kosz, aby natychmiast usunąć oddzielone obiekty blob](#to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs).
3. [Uruchom element utrzymujący. exe](#to-run-the-maintainer).
4. [Przywróć bazę danych zawartości i ustawienia kosza](#to-revert-the-content-database-and-recycle-bin-settings).

#### <a name="to-prepare-to-run-the-maintainer"></a>Aby przygotować się do uruchomienia przez element utrzymujący
1. Na serwerze frontonu sieci Web Otwórz powłokę zarządzania programu SharePoint 2013 jako administrator.
2. Przejdź do folderu *dysk rozruchowy*: \Program Files\Microsoft SQL Remote BLOB Storage 10.50 \ utrzymujący\.
3. Zmień nazwę **Microsoft. Data. SqlRemoteBlobs. utrzymująer. exe. config** na **Web. config**.
4. Użyj `aspnet_regiis -pdf connectionStrings`, aby odszyfrować plik Web. config.
5. W odszyfrowanym pliku Web. config w węźle `connectionStrings` Dodaj parametry połączenia dla wystąpienia programu SQL Server i nazwę bazy danych zawartości. Zobacz poniższy przykład.
   
    `<add name="RBSMaintainerConnectionWSSContent" connectionString="Data Source=SHRPT13-SQL12\SHRPT13;Initial Catalog=WSS_Content;Integrated Security=True;Application Name=&quot;Remote Blob Storage Maintainer for WSS_Content&quot;" providerName="System.Data.SqlClient" />`
6. Użyj `aspnet_regiis –pef connectionStrings` do ponownego zaszyfrowania pliku Web. config. 
7. Zmień nazwę pliku Web. config na Microsoft. Data. SqlRemoteBlobs. utrzymujący. exe. config. 

#### <a name="to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs"></a>Aby przygotować bazę danych zawartości i kosz do natychmiastowego usunięcia oddzielonych obiektów BLOB
1. Na SQL Server w Management Studio SQL Uruchom następujące zapytania dotyczące aktualizacji dla docelowej bazy danych zawartości: 
   
       `use WSS_Content`
   
       `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ’time 00:00:00’`
   
       `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’time 00:00:00’`
2. Na serwerze frontonu sieci Web w obszarze **Administracja centralna**Edytuj **Ogólne ustawienia aplikacji sieci Web** dla żądanej bazy danych zawartości, aby tymczasowo wyłączyć kosz. Ta akcja spowoduje również opróżnienie kosza dla wszystkich powiązanych kolekcji witryn. W tym celu kliknij pozycję **Administracja centralna** -> **Zarządzanie aplikacjami** -> **aplikacje sieci Web (zarządzanie aplikacjami sieci web)**  -> **SharePoint-80** -> **Ogólne ustawienia aplikacji**. Ustaw **stan kosza** na **wyłączony**.
   
    ![Ustawienia ogólne aplikacji sieci Web](./media/storsimple-sharepoint-adapter-garbage-collection/HCS_WebApplicationGeneralSettings-include.png)

#### <a name="to-run-the-maintainer"></a>Aby uruchomić element utrzymujący
* Na serwerze frontonu sieci Web w powłoce zarządzania programu SharePoint 2013 Uruchom element utrzymujący w następujący sposób:
  
      `Microsoft.Data.SqlRemoteBlobs.Maintainer.exe -ConnectionStringName RBSMaintainerConnectionWSSContent -Operation GarbageCollection -GarbageCollectionPhases rdo`
  
  > [!NOTE]
  > W tej chwili w StorSimple jest obsługiwana tylko operacja `GarbageCollection`. Należy również zauważyć, że parametry wystawione dla elementu Microsoft. Data. SqlRemoteBlobs. exe uwzględniają wielkość liter. 
  > 
  > 

#### <a name="to-revert-the-content-database-and-recycle-bin-settings"></a>Aby przywrócić ustawienia bazy danych zawartości i kosza
1. Na SQL Server w Management Studio SQL Uruchom następujące zapytania dotyczące aktualizacji dla docelowej bazy danych zawartości:
   
      `use WSS_Content`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ‘days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘orphan_scan_period’ , ’days 30’`
2. Na serwerze frontonu sieci Web w witrynie **Administracja centralna**Edytuj **Ustawienia ogólne aplikacji sieci Web** dla żądanej bazy danych zawartości, aby ponownie włączyć Kosz. W tym celu kliknij pozycję **Administracja centralna** -> **Zarządzanie aplikacjami** -> **aplikacje sieci Web (zarządzanie aplikacjami sieci web)**  -> **SharePoint-80** -> **Ogólne ustawienia aplikacji**. Ustaw stan kosza na **włączone**.

