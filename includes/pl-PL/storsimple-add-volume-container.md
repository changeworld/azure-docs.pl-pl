<!--author=SharS last changed: 1/7/2016-->

#### <a name="to-add-a-volume-container"></a>Aby dodać kontener woluminów
1. Na **urządzeń** , wybierz urządzenie, kliknij go dwukrotnie, a następnie kliknij przycisk **kontenery woluminów** kartę.
2. Kliknij przycisk **Dodaj** w dolnej części strony. W **Tworzenie kontenera woluminów** okna dialogowego pole, wykonaj następujące czynności:
   
   1. Podaj unikatową **nazwa** kontenera woluminów. Ta nazwa może zawierać maksymalnie 32 znaki.
   2. Wybierz **konta magazynu** ma zostać skojarzony z tym kontenerem woluminów. Możesz wybrać istniejące konto magazynu w tej samej subskrypcji lub wybierz **dodać więcej** wybierz konto magazynu z innej subskrypcji. Możesz również konta magazynu, który najpierw został wygenerowany, gdy usługa została utworzona.
   3. Określ przepustowość jako **bez ograniczeń** Jeśli chcesz używać wszystkich dostępnych przepustowości lub **niestandardowy** aby stosować kontrolę przepustowości. Niestandardowe przepustowości Podaj wartość z zakresu od 1 do 1000 MB/s. Aby przydzielić przepustowość zgodnie z harmonogramem, możesz **wybierz szablon przepustowości**.
   4. Firma Microsoft zaleca zachowywanie **Włącz szyfrowanie magazynu w chmurze** wybrane do szyfrowania danych, która ma wejść w chmurze. Wyłącz szyfrowanie tylko wtedy, gdy są stosowania innych środków do szyfrowania danych. Nie można modyfikować ustawień szyfrowania po utworzeniu kontenera woluminów.
   5. Podaj **klucz szyfrowania magazynu w chmurze** zawierający od 8 do 32 znaków. Urządzenie używa tego klucza dostępu do zaszyfrowanych danych. W **Potwierdź klucz szyfrowania magazynu w chmurze** wprowadź klucz szyfrowania magazynu w chmurze ponownie w celu potwierdzenia. 
   6. Kliknij strzałkę, aby przejść do następnej strony.
      
      ![Tworzenie kontenera woluminów z szablonem o przepustowości 1](./media/storsimple-add-volume-container/HCS_CreateVCBT1-include.png) 
3. Jeśli określono **wybierz szablon przepustowości**, wybierz z listy rozwijanej istniejących szablonów przepustowości. Przejrzyj ustawienia harmonogramu, a następnie kliknij ikonę znacznika wyboru ![Ikona znacznika wyboru](./media/storsimple-configure-new-storage-account/HCS_CheckIcon-include.png).
   
    ![Tworzenie kontenera woluminów z szablonem przepustowość 2](./media/storsimple-add-volume-container/HCS_CreateVCBT2-include.png) 

Kontener woluminów zostanie zapisany i nowo utworzony wolumin kontenerów zostanie wymieniony na **kontenera woluminów** strony.

