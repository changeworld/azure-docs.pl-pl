<!--author=alkohli last changed:01/14/2016-->


#### <a name="to-create-a-new-service"></a>Aby utworzyć nową usługę
1. Za pomocą poświadczeń konta Microsoft zaloguj się do klasycznego portalu Azure pod tym adresem URL: [https://manage.windowsazure.com/](https://manage.windowsazure.com/).
2. W klasycznej witrynie Azure Portal kliknij kolejno opcje **Nowe** > **Data Services** > **StorSimple Manager** > **Szybkie tworzenie**.
3. W wyświetlonym formularzu wykonaj następujące czynności:
   
   1. Podaj unikatową nazwę usługi w polu **Nazwa**. To jest przyjazna nazwa, która może służyć do identyfikowania usługi. Nazwa może zawierać od 2 do 50 znaków, które mogą być literami, cyframi i łącznikami. Nazwa musi zaczynać i kończyć się literą lub cyfrą.
   2. Wypełnij pole **Lokalizacja** dla usługi. Ogólnie rzecz biorąc, wybierz lokalizację najbliżej regionu geograficznego, w którym chcesz wdrożyć urządzenie. Możesz także użyć następujących składników: 
      
      * Jeśli masz istniejące obciążenia na platformie Azure, które również zamierzasz wdrożyć na urządzeniu StorSimple, użyj tego centrum danych.
      * Usługi StorSimple Manager i Azure Storage mogą być w dwóch różnych lokalizacjach. W takim przypadku należy utworzyć konta usługi StorSimple Manager i usługi Azure Storage oddzielnie. Aby utworzyć konto usługi Azure Storage, przejdź do usługi Azure Storage w klasycznej witrynie Azure Portal i postępuj zgodnie z instrukcjami w temacie [Tworzenie konta usługi Azure Storage](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account). Po utworzeniu konta dodaj je do usługi StorSimple Manager, wykonując czynności w temacie [Configure a new storage account for the service](../articles/storsimple/storsimple-deployment-walkthrough.md#configure-a-new-storage-account-for-the-service) (Konfigurowanie nowego konta magazynu dla usługi).
   3. Wybierz opcję **Subskrypcja** z listy rozwijanej. Subskrypcja jest połączona z kontem rozliczeniowym. To pole nie jest widoczne, jeśli istnieje tylko jedna subskrypcja.
   4. Wybierz opcję **Utwórz nowe konto magazynu**, aby automatycznie utworzyć konto magazynu w usłudze. To konto magazynu będzie miało specjalną nazwę, np. „storsimplebwv8c6dcnf”. Jeśli potrzebujesz danych w innej lokalizacji, usuń zaznaczenie tego pola. 
   5. Kliknij opcję **Utwórz usługę StorSimple Manager** i utwórz usługę.
   
   ![Utwórz usługę StorSimple Manager](./media/storsimple-create-new-service/HCS_CreateAService-include.png)
   
   Nastąpi przekierowanie do strony docelowej **Usługa**. Tworzenie usługi może potrwać kilka minut. Po pomyślnym utworzeniu usługi użytkownik zostanie odpowiednio powiadomiony i stan usługi zmieni się na **Aktywny**.
   
   ![Tworzenie usług](./media/storsimple-create-new-service/HCS_StorSimpleManagerServicePage-include.png)

![Zobacz film](./media/storsimple-create-new-service/Video_icon.png) **Zobacz film**

Aby obejrzeć film wideo, na którym przestawiono tworzenie nowej usługi StorSimple Manager, kliknij [tutaj](https://azure.microsoft.com/documentation/videos/create-a-storsimple-manager-service/).

