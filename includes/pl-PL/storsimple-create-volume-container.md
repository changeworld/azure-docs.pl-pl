<!--author=SharS last changed: 9/17/15-->

#### <a name="to-create-a-volume-container"></a>Aby utworzyć kontener woluminów
1. Na stronie **Szybki start** urządzenia kliknij pozycję **Dodaj kontener woluminów**. Zostanie wyświetlone okno dialogowe **Tworzenie kontenera woluminów**.
   
    ![Tworzenie kontenera woluminów](./media/storsimple-create-volume-container/HCS_CreateVolumeContainerM-include.png)
2. W oknie dialogowym **Tworzenie kontenera woluminów**:
   
   1. Wprowadź wartość **Nazwa** kontenera woluminów. Nazwa musi składać się z 3–32 znaków.
   2. Wybierz wartość **Konto magazynu** do skojarzenia z tym kontenerem woluminów. Możesz wybrać domyślne konto generowane podczas tworzenia usługi. Możesz również użyć opcji **Dodaj nowe**, aby wybrać konto magazynu, które nie zostało połączone z tą subskrypcją usługi.
   3. Wybierz pozycję **Włącz szyfrowanie magazynu w chmurze**, aby włączyć szyfrowanie danych wysyłanych z urządzenia do chmury.
   4. Wprowadź i potwierdź **klucz szyfrowania magazynu w chmurze** o długości od 8 do 32 znaków. Ten klucz jest używany przez urządzenie do uzyskiwania dostępu do zaszyfrowanych danych.
   5. Wybierz pozycję **Nieograniczona** z listy rozwijanej **Określ przepustowość**, jeśli chcesz wykorzystać całą dostępną przepustowość. Możesz również ustawić tę opcję na wartość **Niestandardowa**, aby stosować kontrolę przepustowości, i wybrać wartość z przedziału od 1 do 1000 Mb/s. 
      Jeśli masz informacje o użyciu przepustowości, możesz przydzielić przepustowość zgodnie z harmonogramem, określając wartość **Wybierz szablon przepustowości**. Aby zapoznać się z procedurą krok po kroku, przejdź do tematu dotyczącego [dodawania szablonu przepustowości](../articles/storsimple/storsimple-manage-bandwidth-templates.md#add-a-bandwidth-template).
   6. Po ustawieniu filtrów ![ikona znacznika wyboru](./media/storsimple-create-volume-container/HCS_CheckIcon-include.png) aby zapisać ten kontener woluminów i zamknąć kreatora. 
   
   Nowo utworzony wolumin kontenerów zostanie wymieniony na stronie **Kontenery woluminów**.

![Zobacz film](./media/storsimple-create-volume-container/Video_icon.png) **Zobacz film**

Aby obejrzeć film wideo przedstawiający sposób tworzenia kontenera woluminów w rozwiązaniu StorSimple, kliknij [tutaj](https://azure.microsoft.com/documentation/videos/create-a-volume-container-in-your-storsimple-solution/).

