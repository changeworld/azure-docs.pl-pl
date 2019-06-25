---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: f84fe995e65d2b67aaaf4ff9acc4a6a44ce607dc
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183271"
---
> [!NOTE]
> Podczas wprowadzania zmian do adaptera StorSimple dla konfiguracji SPZ programu SharePoint, użytkownik musi być zalogowany przy użyciu konta użytkownika, który należy do grupy Administratorzy domeny. Ponadto należy uzyskać dostęp do strony konfiguracji z poziomu przeglądarki uruchomionych na tym samym hoście jako witryny Administracja centralna.
> 
> 

#### <a name="to-configure-rbs"></a>Aby skonfigurować SPZ
1. Otwórz stronę administracji centralnej programu SharePoint, a następnie przejdź do **ustawień systemowych**. 
2. W **Azure StorSimple** kliknij **skonfigurować Adapter usługi StorSimple**.
   
    ![Skonfiguruj Adapter usługi StorSimple](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS1-include.png) 
3. Na **skonfigurować Adapter usługi StorSimple** strony:
   
   1. Upewnij się, że **umożliwić ścieżkę edycji** pole wyboru jest zaznaczone.
   2. W polu tekstowym wpisz ścieżkę Universal Naming Convention (UNC) z magazynu obiektów BLOB.
      
      > [!NOTE]
      > Wolumin magazynu obiektów BLOB musi być hostowany na woluminie iSCSI skonfigurowane na urządzeniu StorSimple.

   3. Kliknij przycisk **Włącz** znajdujący się poniżej każdej z baz danych zawartości, które chcesz skonfigurować Magazyn zdalny.
      
      > [!NOTE]
      > Magazyn obiektów BLOB musi być udostępniony i osiągalny przez wszystkie frontowych serwerach sieci web (WFE), a konto użytkownika, który jest skonfigurowany dla farmy serwerów programu SharePoint musi mieć dostęp do udziału.
      
      ![Włącz dostawcę SPZ](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS2-include.png)
      
      Po włączeniu lub wyłączeniu SPZ, zostanie również wyświetlony następujący komunikat.
      
      ![Konfigurowanie Wyłącz Włącz Adapter usługi StorSimple](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_ConfigureStorSimpleAdapterEnableDisableMessage-include.png)

   4. Kliknij przycisk **aktualizacji** przycisk, aby zastosować konfigurację. Po kliknięciu **aktualizacji** przycisk, będzie można zaktualizować stanu konfiguracji SPZ na wszystkich serwerach frontonu internetowego i całej farmy będzie włączona SPZ. Zostanie wyświetlony następujący komunikat.
      
      ![Komunikat o konfiguracji karty](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS3-include.png)
      
      > [!NOTE]
      > Jeśli konfigurujesz SPZ farmy programu SharePoint z bardzo dużą liczbą baz danych (większe niż 200), strony sieci web Administracja centralna programu SharePoint może upłynąć limit czasu. Jeśli ma to miejsce, należy odświeżyć stronę. Nie wpływa to na proces konfiguracji.

4. Sprawdź konfigurację:
   
   1. Zaloguj się do witryny sieci Web Administracja centralna programu SharePoint i przejdź do **skonfigurować Adapter usługi StorSimple** strony.
   2. Sprawdź szczegóły konfiguracji, aby upewnić się, że są one zgodne ustawienia, które zostały wprowadzone. 
5. Sprawdź, czy SPZ działa prawidłowo:
   
   1. Przekaż dokument w programie SharePoint. 
   2. Przejdź do ścieżki UNC, który został skonfigurowany. Upewnij się, że struktura katalogów SPZ został utworzony i czy zawiera przekazany obiekt.
6. (Opcjonalnie) Możesz użyć RBS Microsoft `Migrate()` polecenia cmdlet programu PowerShell, dołączone do programu SharePoint, aby przeprowadzić migrację istniejącej zawartości obiektu BLOB z urządzeniem StorSimple. Aby uzyskać więcej informacji, zobacz [migracji zawartości do lub z SPZ w programie SharePoint 2013] [ 6] lub [migracji zawartości do lub z SPZ (SharePoint Foundation 2010)] [7].
7. (Opcjonalnie) W instalacjach testu można sprawdzić, czy obiekty BLOB zostały przeniesione z zawartością bazy danych w następujący sposób: 
   
   1. Uruchom program SQL Management Studio.
   2. Uruchom zapytanie ListBlobsInDB_2010.sql lub ListBlobsInDB_2013.sql w następujący sposób.
      
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
      
      Jeśli SPZ została skonfigurowana prawidłowo, wartości NULL powinna pojawić się w kolumnie SizeOfContentInDB dla dowolnego obiektu, który został przekazany i pomyślnie zewnętrznych z SPZ.
8. (Opcjonalnie) Po skonfigurowaniu SPZ i przenieść całą zawartość obiektu BLOB z urządzeniem StorSimple, możesz przenieść bazę danych zawartości na urządzeniu. Jeśli wybierzesz przenieść bazę danych zawartości, zaleca się skonfigurować Magazyn bazy danych zawartości na urządzenia jako wolumin podstawowy. Następnie korzystaj ze sprawdzonych najlepszych rozwiązań programu SQL Server do migracji bazy danych zawartości z urządzeniem StorSimple. 
   
   > [!NOTE]
   > Przenoszenie bazy danych zawartości na urządzeniu jest obsługiwana tylko w przypadku serii StorSimple 8000 (jest nieobsługiwany w przypadku serii 5000 i 7000).
   
   Jeśli obiekty BLOB i bazy danych zawartości są przechowywane w oddzielnych woluminów na urządzeniu StorSimple, zaleca się skonfigurować je w ten sam kontener woluminów. Daje to gwarancję, że ich zostanie skopiowana ze sobą.
   
   > [!WARNING]
   > Jeśli nie włączono SPZ, zaleca się przeniesienie bazy danych zawartości z urządzeniem StorSimple. To jest Konfiguracja nieprzetestowanych.
   
9. Przejdź do następnego kroku: [Konfigurowanie wyrzucania elementów bezużytecznych](#configure-garbage-collection).

[6]: https://technet.microsoft.com/library/ff628254(v=office.15).aspx
[7]: https://technet.microsoft.com/library/ff628255(v=office.14).aspx
