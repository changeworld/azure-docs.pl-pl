---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 00d292b3ba2d1b6c7c425d4c9f89188e660ac80d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73182259"
---
W tej procedurze:

1. [Przygotuj się do uruchomienia pliku wykonywalnego opiekuna](#to-prepare-to-run-the-maintainer) .
2. [Przygotuj bazę danych zawartości i Kosz do natychmiastowego usunięcia osieroconych bloków BLOB.](#to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs)
3. [Uruchom plik Maintainer.exe](#to-run-the-maintainer).
4. [Przywróć ustawienia bazy danych zawartości i Kosza](#to-revert-the-content-database-and-recycle-bin-settings).

#### <a name="to-prepare-to-run-the-maintainer"></a>Aby przygotować się do uruchomienia opiekuna
1. Na serwerze frontu sieci Web otwórz powłokę zarządzania programu SharePoint 2013 jako administrator.
2. Przejdź do *dysku rozruchowego*folderu :\Program Files\Microsoft SQL Remote Blob Storage 10.50\Maintainer\.
3. Zmień nazwę nazwy **pliku Microsoft.Data.SqlRemoteBlobs.Maintainer.exe.config** na **web.config**.
4. Służy `aspnet_regiis -pdf connectionStrings` do odszyfrowywania pliku web.config.
5. W odszyfrowanym pliku web.config w `connectionStrings` obszarze węzła dodaj parametry połączenia dla wystąpienia serwera SQL i nazwę bazy danych zawartości. Zobacz poniższy przykład.
   
    `<add name="RBSMaintainerConnectionWSSContent" connectionString="Data Source=SHRPT13-SQL12\SHRPT13;Initial Catalog=WSS_Content;Integrated Security=True;Application Name=&quot;Remote Blob Storage Maintainer for WSS_Content&quot;" providerName="System.Data.SqlClient" />`
6. Służy `aspnet_regiis –pef connectionStrings` do ponownego szyfrowania pliku web.config. 
7. Zmień nazwę pliku web.config na witrynę Microsoft.Data.SqlRemoteBlobs.Maintainer.exe.config. 

#### <a name="to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs"></a>Aby przygotować bazę danych zawartości i Kosz do natychmiastowego usunięcia osieroconych bloków BLOB
1. Na serwerze SQL Server w programie SQL Management Studio uruchom następujące kwerendy aktualizujące dla docelowej bazy danych zawartości: 
   
       `use WSS_Content`
   
       `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ’time 00:00:00’`
   
       `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’time 00:00:00’`
2. Na serwerze front-endu sieci Web w obszarze **Administracja centralna**edytuj **ogólne ustawienia aplikacji sieci Web** dla żądanej bazy danych zawartości, aby tymczasowo wyłączyć Kosz. Ta akcja spowoduje również opróżnienie Kosza dla wszystkich powiązanych zbiorów witryn. Aby to zrobić, kliknij pozycję Centralne zarządzanie -> **aplikacjami** ->  **administracyjnymi****w sieci Web (Zarządzanie aplikacjami internetowymi)** -> **SharePoint — 80** -> **ogólnych ustawień aplikacji**. Ustaw **stan Kosza** na **OFF**.
   
    ![Ustawienia ogólne aplikacji sieci Web](./media/storsimple-sharepoint-adapter-garbage-collection/HCS_WebApplicationGeneralSettings-include.png)

#### <a name="to-run-the-maintainer"></a>Aby uruchomić opiekuna
* Na serwerze front-endu sieci Web w shellu zarządzania programu SharePoint 2013 uruchom opiekuna w następujący sposób:
  
      `Microsoft.Data.SqlRemoteBlobs.Maintainer.exe -ConnectionStringName RBSMaintainerConnectionWSSContent -Operation GarbageCollection -GarbageCollectionPhases rdo`
  
  > [!NOTE]
  > Tylko `GarbageCollection` operacja jest obsługiwana dla StorSimple w tej chwili. Należy również zauważyć, że parametry wydane dla microsoft.Data.SqlRemoteBlobs.Maintainer.exe są rozróżniane wielkość liter. 
  > 
  > 

#### <a name="to-revert-the-content-database-and-recycle-bin-settings"></a>Aby przywrócić ustawienia bazy danych zawartości i Kosza
1. Na serwerze SQL Server w programie SQL Management Studio uruchom następujące kwerendy aktualizujące dla docelowej bazy danych zawartości:
   
      `use WSS_Content`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ‘days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘orphan_scan_period’ , ’days 30’`
2. Na serwerze front-endu sieci Web w **administracji centralnej**edytuj **ogólne ustawienia aplikacji sieci Web** dla żądanej bazy danych zawartości, aby ponownie włączyć Kosz. Aby to zrobić, kliknij pozycję Centralne zarządzanie -> **aplikacjami** ->  **administracyjnymi****w sieci Web (Zarządzanie aplikacjami internetowymi)** -> **SharePoint — 80** -> **ogólnych ustawień aplikacji**. Ustaw stan Kosza **na ON**.

