


## <a name="attach-an-empty-disk"></a>Dołączanie pustego dysku
Dołączanie pustego dysku jest to prosty sposób dodać dysk danych, ponieważ Azure tworzy plik VHD i zapisuje go na koncie magazynu.

1. Kliknij przycisk **maszyny wirtualne (klasyczne)**, a następnie wybierz odpowiednią maszynę Wirtualną.

2. W menu Ustawienia, kliknij przycisk **dysków**.

   ![Dołączanie nowego pustego dysku](./media/howto-attach-disk-windows-linux/menudisksattachnew.png)

3. Na pasku poleceń kliknij **Dołącz nowy**.  
    **Dołącz nowy dysk** pojawi się okno dialogowe.

    ![Dołączyć nowy dysk](./media/howto-attach-disk-windows-linux/newdiskdetail.png)

    Wypełnij następujące informacje:
    - W **nazwy pliku**, zaakceptuj nazwę domyślną lub wpisać inną pliku VHD. Dysk z danymi używa nazwy wygenerowanej automatycznie, nawet jeśli możesz wpisać inną nazwę dla pliku VHD.
    - Wybierz **typu** dysku danych. Wszystkie maszyny wirtualne obsługuje dyski w warstwie standardowa. Wiele maszyn wirtualnych obsługują także dyski w warstwie premium.
    - Wybierz **rozmiar (GB)** dysku danych.
    - Aby uzyskać **hosta, buforowanie**, wybierz wyrażenie none lub tylko do odczytu.
    - Kliknij przycisk OK, aby zakończyć.

4. Po utworzeniu i dołączeniu dysku danych znajduje się w sekcji dysków maszyny wirtualnej.

   ![Dysk danych nowych i puste pomyślnie dołączony](./media/howto-attach-disk-windows-linux/newdiskemptysuccessful.png)

> [!NOTE]
> Po dodaniu dysku danych, należy zalogować się do maszyny Wirtualnej i Zainicjuj dysk, dzięki czemu mogą być używane.

## <a name="how-to-attach-an-existing-disk"></a>Porady: dołączanie istniejącego dysku
Dołączanie istniejącego dysku wymaga pliku vhd dostępnego na koncie magazynu. Użyj [Add-AzureVhd](https://msdn.microsoft.com/library/azure/dn495173.aspx) polecenia cmdlet, aby przekazać plik VHD do konta magazynu. Po utworzeniu i przekazać plik VHD, można dołączyć ją do maszyny Wirtualnej.

1. Kliknij przycisk **maszyny wirtualne (klasyczne)**, a następnie wybierz odpowiednią maszynę wirtualną.

2. W menu Ustawienia, kliknij przycisk **dysków**.

3. Na pasku poleceń kliknij **Dołącz istniejące**.

    ![Dołączanie dysku danych](./media/howto-attach-disk-windows-linux/menudisksattachexisting.png)

4. Kliknij przycisk **lokalizacji**. Wyświetl konta dostępnego magazynu. Następnie wybierz konto magazynu odpowiednie od tych na liście.

    ![Podaj konto magazynu dysku](./media/howto-attach-disk-windows-linux/existdiskstorageaccounts.png)

5. A **konta magazynu** zawiera co najmniej jeden kontener, które zawierają dyski (VHD). Wybierz odpowiedniego kontenera od tych na liście.

    ![Podaj kontenera systemu windows wirtualnych maszyn](./media/howto-attach-disk-windows-linux/existdiskcontainers.png)

6. **Wirtualne dyski twarde** panelu Wyświetla listę dysków przechowywane w kontenerze. Kliknij jeden z dysków, a następnie kliknij pozycję Wybierz.

    ![Podanie obrazu dysku wirtualnego windows maszyny](./media/howto-attach-disk-windows-linux/existdiskvhds.png)

7. **Dołączyć istniejącego dysku** panel ponownie wyświetla się za pomocą lokalizacji konta magazynu, kontenerów i wybrany dysk twardy (vhd) do dodania do maszyny wirtualnej.

  Ustaw **hosta, buforowanie** na wartość none lub odczytu, kliknij przycisk OK.

    ![Dysk danych dołączony pomyślnie](./media/howto-attach-disk-windows-linux/exisitingdisksuccessful.png)
