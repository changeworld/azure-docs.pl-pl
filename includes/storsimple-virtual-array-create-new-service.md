#### <a name="to-create-a-new-service"></a>Aby utworzyć nową usługę

1.  Przy użyciu poświadczeń konta Microsoft, zaloguj się do portalu Azure pod tym adresem URL: <https://portal.azure.com/>. Jeśli wdrażane urządzenia w portalu dla instytucji rządowych, zaloguj się na: <https://portal.azure.us/>

2.  W portalu Azure kliknij **+ Utwórz zasób** &gt; **magazynu** &gt; **serii wirtualnego StorSimple**.

    ![Tworzenie nowej usługi](./media/storsimple-virtual-array-create-new-service/createnewservice2.png) 

3.  W **Menedżera urządzeń StorSimple** otwartym bloku, wykonaj następujące czynności:

    1.  Podaj **unikatową nazwę zasobu** dla swojej usługi. Nazwa zasobu jest przyjazna nazwa, która może służyć do identyfikowania usługi. Nazwa może zawierać od 2 do 50 znaków, które mogą być literami, cyframi i łącznikami. Nazwa musi zaczynać i kończyć się literą lub cyfrą.

    2.  Wybierz opcję **Subskrypcja** z listy rozwijanej. Subskrypcja jest połączona z kontem rozliczeniowym. To pole nie jest widoczne, jeśli istnieje tylko jedna subskrypcja.

    3.  Aby uzyskać **grupy zasobów**, wybierz istniejącą lub Utwórz nową grupę. Aby uzyskać więcej informacji, zobacz [Grupy zasobów na platformie Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/).

    4.  Wypełnij pole **Lokalizacja** dla usługi. Zobacz [regiony platformy Azure](https://azure.microsoft.com/regions/#services) Aby uzyskać więcej informacji o tym, które usługi są dostępne w danym regionie. Ogólnie rzecz biorąc, wybierz **lokalizacji** najbliżej regionu geograficznego, w której chcesz wdrożyć urządzenie. Możesz także użyć następujących składników:

        -   Jeśli masz istniejące obciążenia na platformie Azure, które również zamierzasz wdrożyć na urządzeniu StorSimple, zalecane jest użycie tego centrum danych.

        -   Magazyn Menedżera urządzeń StorSimple i Azure może być w dwóch różnych lokalizacjach. W takim przypadku należy utworzyć konta usługi Menedżer urządzeń StorSimple i usługi Azure Storage oddzielnie. Aby utworzyć konto usługi Azure Storage, przejdź do usługi Azure Storage w witrynie Azure Portal i postępuj zgodnie z instrukcjami w temacie [Tworzenie konta usługi Azure Storage](https://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account). Po utworzeniu konta dodaj je do usługi Menedżer urządzeń StorSimple, wykonując czynności w temacie [Configure a new storage account for the service](https://azure.microsoft.com/documentation/articles/storsimple-deployment-walkthrough/#configure-a-new-storage-account-for-the-service) (Konfigurowanie nowego konta magazynu dla usługi).

        -   Jeśli wdrażane urządzenia wirtualnego w portalu dla instytucji rządowych, usługi Menedżer StorSimple urządzenia jest dostępny w lokalizacjach nam Iowa i Virginia NAS.

    5.  Wybierz **Utwórz nowe konto magazynu Azure** do automatycznego tworzenia konta magazynu z usługą. Określ **nazwy konta magazynu**. Jeśli potrzebujesz danych w innej lokalizacji, usuń zaznaczenie tego pola.

    6.  Zaznacz pole **Przypnij do pulpitu nawigacyjnego**, jeśli chcesz szybko łączyć się z tą usługą na pulpicie nawigacyjnym.

    7.  Kliknij pozycję **Utwórz**, aby utworzyć usługę Menedżer urządzeń StorSimple.

        ![Tworzenie nowej usługi](./media/storsimple-virtual-array-create-new-service/createnewservice4.png)  

Nastąpi przekierowanie do **usługi** strony docelowej. Tworzenie usługi potrwa kilka minut. Po pomyślnym utworzeniu usługi użytkownik zostanie odpowiednio powiadomiony i stan usługi zmieni się na **Aktywny**.


