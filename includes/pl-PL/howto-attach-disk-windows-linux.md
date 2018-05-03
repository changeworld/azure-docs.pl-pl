


## <a name="attach-an-empty-disk"></a>Dołączanie pustego dysku
Dołączanie pusty dysk jest prosty sposób, aby dodać dysk danych, ponieważ Azure utworzy plik VHD i zapisuje go na koncie magazynu.

1. Kliknij przycisk **maszyn wirtualnych (klasyczne)**, a następnie wybierz odpowiednią maszynę Wirtualną.

2. W menu Ustawienia kliknij **dysków**.

   ![Dołącz nowy pusty dysk](./media/howto-attach-disk-windows-linux/menudisksattachnew.png)

3. Na pasku poleceń, kliknij przycisk **Dołącz nowy**.  
    **Dołączanie nowego dysku** zostanie wyświetlone okno dialogowe.

    ![Dołączanie nowego dysku](./media/howto-attach-disk-windows-linux/newdiskdetail.png)

    Wypełnij następujące informacje:
    - W **nazwę pliku**, zaakceptuj nazwę domyślną lub wpisz inną pliku VHD. Dysk z danymi korzysta z użyciem nazwy wygenerowanej automatycznie, nawet wtedy, gdy wpisz inną nazwę dla pliku VHD.
    - Wybierz **typu** dysku danych. Wszystkie maszyny wirtualne obsługuje dyski standardowe. Wiele maszyn wirtualnych również obsługuje dysków premium.
    - Wybierz **rozmiar (GB)** dysku danych.
    - Aby uzyskać **buforowanie hosta**, wybierz Brak lub tylko do odczytu.
    - Kliknij przycisk OK, aby zakończyć.

4. Po utworzeniu i dołączyć dysku danych, znajduje się w sekcji dysków maszyny wirtualnej.

   ![Pomyślnie dołączono dysk danych nowy i puste](./media/howto-attach-disk-windows-linux/newdiskemptysuccessful.png)

> [!NOTE]
> Po dodaniu dysku danych, należy zalogować się do maszyny Wirtualnej i Zainicjuj dysk, dzięki czemu można go używać.

## <a name="how-to-attach-an-existing-disk"></a>Porady: dołączanie istniejącego dysku
Dołączanie istniejącego dysku wymaga pliku vhd dostępnego na koncie magazynu. Użyj [Add-AzureVhd](https://msdn.microsoft.com/library/azure/dn495173.aspx) polecenia cmdlet można przekazać pliku VHD na koncie magazynu. Po utworzeniu i przekazać plik VHD, aby podłączyć go do maszyny Wirtualnej.

1. Kliknij przycisk **maszyn wirtualnych (klasyczne)**, a następnie wybierz odpowiednią maszynę wirtualną.

2. W menu Ustawienia kliknij **dysków**.

3. Na pasku poleceń, kliknij przycisk **Attach istniejących**.

    ![Dołączanie dysku danych](./media/howto-attach-disk-windows-linux/menudisksattachexisting.png)

4. Kliknij przycisk **lokalizacji**. Wyświetlanie kont magazynu dostępne. Następnie wybierz konto magazynu odpowiednie spośród wymienionych.

    ![Podaj konto magazynu dysku](./media/howto-attach-disk-windows-linux/existdiskstorageaccounts.png)

5. A **konta magazynu** zawiera jeden lub więcej kontenerów, które zawierają dyski (VHD). Wybierz odpowiedniego kontenera z wymienionych.

    ![Podaj kontenera systemu windows wirtualnych maszyn](./media/howto-attach-disk-windows-linux/existdiskcontainers.png)

6. **Wirtualne dyski twarde** panelu zawiera listę dysków w kontenerze. Kliknij jeden z dysków, a następnie kliknij przycisk Wybierz.

    ![Przewidują obrazu dysku wirtualnego windows maszyny](./media/howto-attach-disk-windows-linux/existdiskvhds.png)

7. **Dołączyć istniejącego dysku** panelu wyświetla się ponownie z lokalizacji konta magazynu, kontenera i wybrany dysk twardy (vhd) do dodania do maszyny wirtualnej.

  Ustaw **buforowanie hosta** None lub odczytu, kliknij przycisk OK.

    ![Pomyślnie dołączono dysk danych](./media/howto-attach-disk-windows-linux/exisitingdisksuccessful.png)
