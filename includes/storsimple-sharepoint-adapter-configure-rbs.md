---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: f84fe995e65d2b67aaaf4ff9acc4a6a44ce607dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183271"
---
> [!NOTE]
> Podczas wprowadzania zmian w konfiguracji StorSimple Adapter for SharePoint SPZ musisz być zalogowany przy za pomocą konta użytkownika należącego do grupy Administratorzy domeny. Ponadto należy uzyskać dostęp do strony konfiguracji z przeglądarki uruchomionej na tym samym hoście co administracja centralna.
> 
> 

#### <a name="to-configure-rbs"></a>Aby skonfigurować SPZ
1. Otwórz stronę Administracja centralna programu SharePoint i przejdź do **pozycji Ustawienia systemowe**. 
2. W sekcji **Azure StorSimple** kliknij pozycję **Konfiguruj kartę StorSimple**.
   
    ![Konfigurowanie karty StorSimple](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS1-include.png) 
3. Na stronie **Konfigurowanie karty StorSimple:**
   
   1. Upewnij się, że pole wyboru **Włącz ścieżkę edycji** jest zaznaczone.
   2. W polu tekstowym wpisz ścieżkę universal naming convention (UNC) magazynu obiektów BLOB.
      
      > [!NOTE]
      > Wolumin magazynu obiektów BLOB musi być obsługiwany na woluminie iSCSI skonfigurowanym na urządzeniu StorSimple.

   3. Kliknij przycisk **Włącz** poniżej każdej z baz danych zawartości, które chcesz skonfigurować do magazynu zdalnego.
      
      > [!NOTE]
      > Magazyn obiektów BLOB musi być współużytkowany i osiągalny przez wszystkie serwery frontonu sieci Web (WFE), a konto użytkownika skonfigurowane dla farmy serwerów programu SharePoint musi mieć dostęp do udziału.
      
      ![Włączanie dostawcy SPZ](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS2-include.png)
      
      Po włączeniu lub wyłączeniu SPZ zostanie wyświetlony następujący komunikat.
      
      ![Skonfiguruj włącz kartę StorSimple Disable](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_ConfigureStorSimpleAdapterEnableDisableMessage-include.png)

   4. Kliknij przycisk **Aktualizuj,** aby zastosować konfigurację. Po kliknięciu przycisku **Aktualizuj** stan konfiguracji SPZ zostanie zaktualizowany na wszystkich serwerach WFE, a cała farma będzie włączona rbs. Zostanie wyświetlony następujący komunikat.
      
      ![Komunikat o konfiguracji karty](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS3-include.png)
      
      > [!NOTE]
      > Jeśli konfigurujesz SPZ dla farmy programu SharePoint z bardzo dużą liczbą baz danych (większą niż 200), strona sieci Web administracji centralnej programu SharePoint może się prze uiścić limit czasu. W takim przypadku odśwież stronę. Nie ma to wpływu na proces konfiguracji.

4. Sprawdź konfigurację:
   
   1. Zaloguj się w witrynie sieci Web administracji centralnej programu SharePoint i przejdź do strony **Konfigurowanie karty StorSimple.**
   2. Sprawdź szczegóły konfiguracji, aby upewnić się, że są one zgodne z wprowadzonymi ustawieniami. 
5. Sprawdź, czy SPZ działa poprawnie:
   
   1. Przekazywanie dokumentu do programu SharePoint. 
   2. Przejdź do skonfigurowaną ścieżkę UNC. Upewnij się, że struktura katalogu SPZ została utworzona i że zawiera przekazany obiekt.
6. (Opcjonalnie) Za pomocą polecenia cmdlet Programu Microsoft RBS `Migrate()` PowerShell dołączonego do programu SharePoint można przeprowadzić migrację istniejącej zawartości obiektu BLOB na urządzenie StorSimple. Aby uzyskać więcej informacji, zobacz [Migrowanie zawartości do lub z SPZ w programie SharePoint 2013][6] lub [Migrowanie zawartości do lub z SPZ (SharePoint Foundation 2010)][7]lub z nich .
7. (Opcjonalnie) W instalacjach testowych można sprawdzić, czy obiekty BLOB zostały przeniesione z bazy danych zawartości w następujący sposób: 
   
   1. Uruchom program SQL Management Studio.
   2. Uruchom kwerendę ListBlobsInDB_2010.sql lub ListBlobsInDB_2013.sql w następujący sposób.
      
      ```
      **ListBlobsInDB_2013.sql**
      
        USE WSS_Content
        GO
      
        SELECT DocStreams.DocId,
      
               LeafName AS Name,
               Content,
               AllDocs.Size AS OrigSizeOfContent,
               LEN(CAST(Content AS VARBINARY(MAX))) AS SizeOfContentInDB,
               DocStreams.RbsId,
               TimeLastModified
      
        FROM DocStreams
      
             INNER JOIN AllDocs ON DocStreams.DocId = AllDocs.Id
        ORDER BY TimeLastModified DESC
        GO
      
      **ListBlobsInDB_2010.sql**
      
        USE WSS_Content
        GO
      
        SELECT AllDocStreams.Id,
      
               LeafName AS Name,
               Content,
               AllDocs.Size AS OrigSizeOfContent,
               LEN(CAST(Content AS VARBINARY(MAX))) AS SizeOfContentInDB,
               RbsId,
               TimeLastModified
        FROM AllDocStreams
      
             INNER JOIN AllDocs ON AllDocStreams.Id = AllDocs.Id
        ORDER BY TimeLastModified DESC
        GO
      ```
      
      Jeśli SPZ został poprawnie skonfigurowany, wartość NULL powinna pojawić się w kolumnie SizeOfContentInDB dla każdego obiektu, który został przekazany i pomyślnie zewnętrzny z SPZ.
8. (Opcjonalnie) Po skonfigurowaniu SPZ i przeniesienie całej zawartości obiektu BLOB do urządzenia StorSimple można przenieść bazę danych zawartości na urządzenie. Jeśli zdecydujesz się przenieść bazę danych zawartości, zaleca się skonfigurowanie magazynu bazy danych zawartości na urządzeniu jako woluminu podstawowego. Następnie należy użyć ustalonych najlepszych rozwiązań programu SQL Server, aby przeprowadzić migrację bazy danych zawartości do urządzenia StorSimple. 
   
   > [!NOTE]
   > Przenoszenie bazy danych zawartości na urządzenie jest obsługiwane tylko dla serii StorSimple 8000 (nie jest obsługiwana dla serii 5000 lub 7000).
   
   Jeśli dyski BLOB i bazy danych zawartości są przechowywane w oddzielnych woluminach na urządzeniu StorSimple, zaleca się skonfigurowanie ich w tym samym kontenerze woluminów. Gwarantuje to, że będą one wspierane razem.
   
   > [!WARNING]
   > Jeśli nie włączono SPZ, nie zaleca się przenoszenia bazy danych zawartości do urządzenia StorSimple. Jest to konfiguracja nieprzetestowana.
   
9. Przejdź do następnego kroku: [Konfigurowanie wyrzucania elementów bezużytecznych](#configure-garbage-collection).

[6]: https://technet.microsoft.com/library/ff628254(v=office.15).aspx
[7]: https://technet.microsoft.com/library/ff628255(v=office.14).aspx
