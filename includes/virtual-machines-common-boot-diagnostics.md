Obsługa dwie funkcje debugowania jest teraz dostępna w systemie Azure: dane wyjściowe konsoli i tworzenia zrzutów ekranu obsługę modelu wdrażania maszyn wirtualnych platformy Azure Resource Manager. 

Podczas przełączania własny obraz na platformie Azure albo jeden z obrazów platformy nawet rozruch, może istnieć wiele przyczyn, dlaczego pobiera maszynę wirtualną do stanu rozruchowego. Te funkcje umożliwiają łatwe diagnozowanie i odzyskiwanie po błędach rozruchu maszyn wirtualnych.

Dla maszyn wirtualnych systemu Linux można łatwo przeglądać dane wyjściowe konsoli dziennika z portalu:

![Azure Portal](./media/virtual-machines-common-boot-diagnostics/screenshot1.png)
 
Jednak w przypadku systemów Windows i Linux maszyn wirtualnych Azure umożliwia również Zobacz zrzut ekranu maszyny wirtualnej z funkcji hypervisor:

![Błąd](./media/virtual-machines-common-boot-diagnostics/screenshot2.png)

Obie te funkcje są obsługiwane dla maszyn wirtualnych platformy Azure we wszystkich regionach. Należy pamiętać, że może minąć do 10 minut, zanim zrzuty ekranu i dane wyjściowe pojawią się na koncie magazynu.

## <a name="common-boot-errors"></a>Typowe błędy rozruchu

- [0xC000000E](https://support.microsoft.com/help/4010129)
- [0xC000000F](https://support.microsoft.com/help/4010130)
- [0xC0000011](https://support.microsoft.com/help/4010134)
- [0xC0000034](https://support.microsoft.com/help/4010140)
- [0xC0000098](https://support.microsoft.com/help/4010137)
- [0xC00000BA](https://support.microsoft.com/help/4010136)
- [0xC000014C](https://support.microsoft.com/help/4010141)
- [0xC0000221](https://support.microsoft.com/help/4010132)
- [0xC0000225](https://support.microsoft.com/help/4010138)
- [0xC0000359](https://support.microsoft.com/help/4010135)
- [0xC0000605](https://support.microsoft.com/help/4010131)
- [Nie znaleziono systemu operacyjnego](https://support.microsoft.com/help/4010142)
- [Niepowodzenie rozruchu lub błąd INACCESSIBLE_BOOT_DEVICE](https://support.microsoft.com/help/4010143)

## <a name="enable-diagnostics-on-a-new-virtual-machine"></a>Włączanie diagnostyki na nowej maszynie wirtualnej
1. Podczas tworzenia nowej maszyny wirtualnej z portalu Azure, wybierz **usługi Azure Resource Manager** z listy rozwijanej modelu wdrażania:
 
    ![Resource Manager](./media/virtual-machines-common-boot-diagnostics/screenshot3.jpg)

2. W **ustawienia**, Włącz **diagnostyki rozruchu**, a następnie wybierz konto magazynu, które chcesz umieścić te pliki diagnostyczne.
 
    ![Tworzenie maszyny wirtualnej](./media/virtual-machines-common-boot-diagnostics/create-storage-account.png)

    > [!NOTE]
    > Funkcja diagnostyki rozruchu obsługuje konto magazynu w warstwie premium. Jeśli używasz konta magazynu w warstwie premium dla diagnostyki rozruchu po ponownym uruchomieniu maszyny Wirtualnej zostanie zgłoszony błąd StorageAccountTypeNotSupported.
    >
    > 

3. Jeśli wdrażasz za pomocą szablonu usługi Azure Resource Manager, przejdź do zasobu maszyny wirtualnej i Dołącz sekcji profilu diagnostyki. Pamiętaj, aby użyć nagłówka wersji interfejsu API „2015-06-15”.

    ```json
    {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Compute/virtualMachines",
          … 
    ```

4. Profil diagnostyki umożliwia wybranie konta magazynu, na którym chcesz umieścić te dzienniki.

    ```json
            "diagnosticsProfile": {
                "bootDiagnostics": {
                "enabled": true,
                "storageUri": "[concat('http://', parameters('newStorageAccountName'), '.blob.core.windows.net')]"
                }
            }
            }
        }
    ```

Aby wdrożyć przykładowe maszyny wirtualnej z włączoną diagnostykę rozruchu, zapoznaj się z naszym repozytorium, w tym miejscu.

## <a name="enable-boot-diagnostics-on-existing-virtual-machine"></a>Włącz diagnostykę rozruchu w istniejącej maszyny wirtualnej 

Aby włączyć diagnostyki rozruchu na już istniejącej maszynie wirtualnej, wykonaj następujące kroki:

1. Zaloguj się do [portalu Azure](https://portal.azure.com), a następnie wybierz maszynę wirtualną.
2. W **pomocy technicznej i rozwiązywania problemów**, wybierz pozycję **diagnostyki rozruchu** > **ustawienia**, Zmień stan na **na**, a następnie Wybierz konto magazynu. 
4. Upewnij się, że jest zaznaczona opcja diagnostyki rozruchu, a następnie zapisz zmiany.

    ![Aktualizowanie istniejącej maszyny wirtualnej](./media/virtual-machines-common-boot-diagnostics/enable-for-existing-vm.png)

3. Uruchom ponownie maszynę wirtualną, aby zmiany zaczęły obowiązywać.


