<!--author=SharS last changed: 1/14/2016 -->

> [!NOTE]
> Podczas wprowadzania zmian do konfiguracji programu SharePoint SPZ karty StorSimple, użytkownik musi być zalogowany przy użyciu konta użytkownika, który należy do grupy Administratorzy domeny. Ponadto muszą uzyskać dostęp do strony konfiguracji w przeglądarce uruchomionej na tym samym hoście, jak Administracja centralna.
> 
> 

#### <a name="to-configure-rbs"></a>Aby skonfigurować SPZ
1. Otwórz stronę Administracja centralna programu SharePoint i przejdź do **ustawienia systemu**. 
2. W **Azure StorSimple** kliknij **skonfigurować karty StorSimple**.
   
    ![Skonfiguruj kartę StorSimple](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS1-include.png) 
3. Na **skonfigurować karty StorSimple** strony:
   
   1. Upewnij się, że **włączyć edycji ścieżki** pole wyboru jest zaznaczone.
   2. W polu tekstowym wpisz ścieżkę Universal Naming Convention (UNC) magazynu obiektów BLOB.
      
      > [!NOTE]
      > Wolumin magazynu obiektów BLOB musi być hostowany na woluminie iSCSI skonfigurowane na urządzeniu StorSimple.

   3. Kliknij przycisk **włączyć** znajdujący się poniżej każdej z baz danych zawartości, które chcesz skonfigurować Magazyn zdalny.
      
      > [!NOTE]
      > Magazyn obiektów BLOB musi być udostępniony i osiągalny przez wszystkie serwery sieci web frontonu (WFE), a konto użytkownika, który jest skonfigurowany dla farmy programu SharePoint server musi mieć dostęp do udziału.
      
      ![Włącz dostawcy SPZ](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS2-include.png)
      
      Włączenie lub wyłączenie SPZ następujący komunikat zostanie również wyświetlony.
      
      ![Skonfiguruj StorSimple karty włączenia wyłączenia](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_ConfigureStorSimpleAdapterEnableDisableMessage-include.png)

   4. Kliknij przycisk **aktualizacji** przycisk, aby zastosować konfigurację. Po kliknięciu **aktualizacji** przycisku, stan konfiguracji SPZ zostaną zaktualizowane na wszystkich serwerach WFE i całej farmy zostaną włączone SPZ. Zostanie wyświetlony następujący komunikat.
      
      ![Komunikat adaptera konfiguracji](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS3-include.png)
      
      > [!NOTE]
      > Jeśli konfigurujesz SPZ farmy programu SharePoint o bardzo dużej liczby baz danych (większe niż 200), strony sieci web Administracja centralna programu SharePoint może upłynął limit czasu. Jeśli to miejsce, Odśwież stronę. Nie dotyczy to proces konfiguracji.

4. Sprawdź konfigurację:
   
   1. Zaloguj się do witryny sieci Web Administracja centralna programu SharePoint i przejdź do **skonfigurować karty StorSimple** strony.
   2. Sprawdź szczegóły konfiguracji, aby upewnić się, że są one zgodne ustawienia, które zostały wprowadzone. 
5. Sprawdź, czy SPZ działa prawidłowo:
   
   1. Przekaż dokument w programie SharePoint. 
   2. Przejdź do ścieżki UNC, który został skonfigurowany. Upewnij się, że struktura katalogów SPZ został utworzony i czy zawiera przekazanego obiektu.
6. (Opcjonalnie) Można użyć RBS Microsoft `Migrate()` polecenia cmdlet programu PowerShell dołączony programu SharePoint, aby przeprowadzić migrację istniejącej zawartości obiektu BLOB na urządzeniu StorSimple. Aby uzyskać więcej informacji, zobacz [migrowania zawartości do lub wychodzący SPZ w SharePoint 2013] [ 6] lub [migrowania zawartości do lub wychodzący SPZ (SharePoint Foundation 2010)] [7].
7. (Opcjonalnie) W instalacjach testu można sprawdzić, czy obiekty BLOB zostały przeniesione z bazy danych zawartości w następujący sposób: 
   
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
      
      Jeśli SPZ został skonfigurowany prawidłowo, wartości NULL powinny być wyświetlane w kolumnie SizeOfContentInDB dla dowolnego obiektu, który został przekazany pomyślnie externalized z SPZ.
8. (Opcjonalnie) Po skonfigurowaniu SPZ i przenieść całej zawartości obiektu BLOB na urządzeniu StorSimple, można przenieść bazę danych zawartości na urządzeniu. Jeśli wybierzesz przenieść bazę danych zawartości, zaleca się Konfigurowanie magazynu bazy danych zawartości na urządzeniu jako podstawowy wolumin. Następnie użyj ustanowić najlepsze rozwiązania programu SQL Server, aby przeprowadzić migrację bazy danych zawartości na urządzeniu StorSimple. 
   
   > [!NOTE]
   > Przenoszenie bazy danych zawartości na urządzeniu jest obsługiwana tylko dla serii StorSimple 8000 (jest nieobsługiwany dla serii 5000 i 7000).
   
   Jeśli obiekty BLOB i bazy danych zawartości są przechowywane w oddzielnych woluminach na urządzeniu StorSimple, firma Microsoft zaleca, skonfiguruj je w tym samym kontenerze woluminów. Dzięki temu one zostanie ono utworzenia kopii zapasowej razem.
   
   > [!WARNING]
   > Jeśli nie włączono SPZ, nie zaleca się przenoszenie bazy danych zawartości na urządzeniu StorSimple. To jest Konfiguracja zastosowaniem.
   
9. Przejdź do następnego kroku: [skonfigurować wyrzucanie elementów bezużytecznych](#configure-garbage-collection).

[6]: https://technet.microsoft.com/library/ff628254(v=office.15).aspx
[7]: https://technet.microsoft.com/library/ff628255(v=office.14).aspx
