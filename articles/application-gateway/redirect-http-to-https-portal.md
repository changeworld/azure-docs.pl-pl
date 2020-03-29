---
title: Przekierowanie http do HTTPS w portalu — brama aplikacji azure
description: Dowiedz się, jak utworzyć bramę aplikacji z przekierowanym ruchem z HTTP do HTTPS za pomocą witryny Azure portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: 51c191a7815bb64243e2324e150c00c2dcb7ec4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76705330"
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-the-azure-portal"></a>Tworzenie bramy aplikacji z przekierowaniem HTTP do HTTPS przy użyciu portalu Azure

Za pomocą portalu Azure można utworzyć [bramę aplikacji](overview.md) z certyfikatem zakończenia SSL. Reguła routingu służy do przekierowywania ruchu HTTP do portu HTTPS w bramie aplikacji. W tym przykładzie można również utworzyć [zestaw skalowania maszyny wirtualnej](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) dla puli wewnętrznej bazy danych bramy aplikacji, która zawiera dwa wystąpienia maszyny wirtualnej.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Tworzenie certyfikatu z podpisem własnym
> * Konfigurowanie sieci
> * Tworzenie bramy aplikacji z certyfikatem
> * Dodawanie odbiornika i reguły przekierowania
> * Tworzenie zestawu skalowania maszyn wirtualnych przy użyciu domyślnej puli zaplecza

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ten samouczek wymaga modułu programu Azure PowerShell w wersji 1.0.0 lub nowszej do utworzenia certyfikatu i zainstalowania usług IIS. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Aby uruchomić polecenia w tym samouczku, `Login-AzAccount` należy również uruchomić, aby utworzyć połączenie z platformą Azure.

## <a name="create-a-self-signed-certificate"></a>Tworzenie certyfikatu z podpisem własnym

Do użytku w produkcji należy zaimportować prawidłowy certyfikat podpisany przez zaufanego dostawcę. W tym samouczku utworzysz certyfikat z podpisem własnym przy użyciu polecenia [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate). Korzystając z polecenia [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate) i zwróconego odcisku palca, możesz wyeksportować plik pfx z certyfikatu.

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

Wynik powinien wyglądać podobnie do następującego:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

Użyj odcisku palca w celu utworzenia pliku pfx:

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456!" -Force -AsPlainText
Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

Sieć wirtualna jest potrzebna do komunikacji między utworzonymi zasobami. W tym przykładzie są tworzone dwie podsieci: jedna dla bramy aplikacji i druga dla serwerów zaplecza. Sieć wirtualną można utworzyć podczas tworzenia bramy aplikacji.

1. Zaloguj się do witryny Azure portal w [https://portal.azure.com](https://portal.azure.com).
2. W lewym górnym rogu witryny Azure Portal kliknij przycisk **Utwórz zasób**.
3. Wybierz pozycję **Sieć**, a następnie z listy Polecane wybierz pozycję **Application Gateway**.
4. Wprowadź następujące wartości dla bramy aplikacji:

   - *myAppGateway* — jako nazwę bramy aplikacji.
   - *myResourceGroupAG* — jako nową grupę zasobów.

     ![Tworzenie nowej bramy aplikacji](./media/create-url-route-portal/application-gateway-create.png)

5. Zaakceptuj wartości domyślne dla innych ustawień, a następnie kliknij przycisk **OK**.
6. Kliknij kolejno pozycje **Wybierz sieć wirtualną**, **Utwórz nową**, a następnie wprowadź następujące wartości dla sieci wirtualnej:

   - *myVNet* — jako nazwę sieci wirtualnej.
   - *10.0.0.0/16* — jako przestrzeń adresową sieci wirtualnej.
   - *myAGSubnet* — jako nazwę podsieci.
   - *10.0.0.0/24* — jako przestrzeń adresową podsieci.

     ![Tworzenie sieci wirtualnej](./media/create-url-route-portal/application-gateway-vnet.png)

7. Kliknij przycisk **OK**, aby utworzyć sieć wirtualną i podsieć.
8. W obszarze **Konfiguracja ip frontu**upewnij się, że **typ adresu IP** jest **publiczny,** a **opcja Utwórz nowy** jest zaznaczona. Wprowadź *myAGPublicIPAddress* dla nazwy. Zaakceptuj wartości domyślne dla innych ustawień, a następnie kliknij przycisk **OK**.
9. W obszarze **Konfiguracja odbiornika**wybierz **https**, a następnie wybierz pozycję **Wybierz plik** i przejdź do pliku *c:\appgwcert.pfx* i wybierz pozycję **Otwórz**.
10. Wpisz *appgwcert* dla nazwy certyfikatu i *Azure123456!* jako hasło.
11. Pozostaw zaporę aplikacji sieci Web wyłączona, a następnie wybierz przycisk **OK**.
12. Przejrzyj ustawienia na stronie podsumowania, a następnie wybierz przycisk **OK,** aby utworzyć zasoby sieciowe i bramę aplikacji. Utworzenie bramy aplikacji może potrwać kilka minut, poczekaj, aż wdrożenie zakończy się pomyślnie, zanim przejdzie do następnej sekcji.

### <a name="add-a-subnet"></a>Dodawanie podsieci

1. Wybierz **pozycję Wszystkie zasoby** w menu po lewej stronie, a następnie wybierz **myVNet** z listy zasobów.
2. Wybierz **pozycję Podsieci**, a następnie kliknij pozycję **Podsieć**.

    ![Tworzenie podsieci](./media/create-url-route-portal/application-gateway-subnet.png)

3. Wpisz *myBackendSubnet* dla nazwy podsieci.
4. Wpisz *10.0.2.0/24* dla zakresu adresów, a następnie wybierz **PRZYCISK OK**.

## <a name="add-a-listener-and-redirection-rule"></a>Dodawanie odbiornika i reguły przekierowania

### <a name="add-the-listener"></a>Dodawanie odbiornika

Najpierw dodaj odbiornik o nazwie *myListener* dla portu 80.

1. Otwórz grupę zasobów **myResourceGroupAG** i wybierz **myAppGateway**.
2. Wybierz **pozycję Detektory,** a następnie wybierz pozycję **+ Podstawowe**.
3. Wpisz *MyListener* dla nazwy.
4. Wpisz *httpPort* dla nowej nazwy portu frontowego i *80* dla portu.
5. Upewnij się, że protokół jest ustawiony na **HTTP**, a następnie wybierz **przycisk OK**.

### <a name="add-a-routing-rule-with-a-redirection-configuration"></a>Dodawanie reguły routingu z konfiguracją przekierowania

1. W **aplikacji myAppGateway**wybierz pozycję **Reguły,** a następnie wybierz pozycję **+Żądaj reguły routingu**.
2. W przypadku **nazwy reguły**wpisz *Rule2*.
3. Upewnij się, że **mylistener** jest wybrany dla odbiornika.
4. Kliknij kartę **Cele wewnętrznej bazy danych** i wybierz pozycję Typ **celu** jako *przekierowanie*.
5. W polu **Typ przekierowania**wybierz opcję **Stałe**.
6. W polu **Cel przekierowania**wybierz pozycję **Odbiornik**.
7. Upewnij się, że **odbiornik docelowy** jest ustawiony na **appGatewayHttpListener**.
8. W przypadku **ciągu zapytania Dołącz** i **uwzględnij ścieżkę** wybierz pozycję *Tak*.
9. Wybierz pozycję **Dodaj**.

## <a name="create-a-virtual-machine-scale-set"></a>Tworzenie zestawu skalowania maszyn wirtualnych

W tym przykładzie utworzysz zestaw skalowania maszyn wirtualnych, aby zapewnić serwery dla puli zaplecza w bramie aplikacji.

1. W lewym górnym rogu portalu wybierz pozycję **+Utwórz zasób**.
2. Wybierz pozycję **Compute**.
3. W polu wyszukiwania wpisz *zestaw skalowania* i naciśnij klawisz Enter.
4. Wybierz **pozycję Zestaw skalowania maszyny wirtualnej**, a następnie wybierz pozycję **Utwórz**.
5. W przypadku **nazwy zestawu skalowania maszyny wirtualnej**wpisz *myvmss*.
6. W przypadku obrazu dysku systemu operacyjnego** upewnij się, że wybrano **centrum danych systemu Windows Server 2016.**
7. W obszarze **Grupa zasobów**wybierz **myResourceGroupAG**.
8. W przypadku **nazwy użytkownika**wpisz *azureuser*.
9. W przypadku **hasła**wpisz *Azure123456!* i potwierdź hasło.
10. Dla **liczby wystąpień,** upewnij się, że wartość wynosi **2**.
11. W **przypadku opcji Rozmiar instancji**wybierz **D2s_v3**.
12. W obszarze **Sieć**upewnij się, że **opcja Wybierz równoważenie obciążenia** jest ustawiona na **Brama aplikacji**.
13. Upewnij się, że **brama aplikacji** jest ustawiona na **myAppGateway**.
14. Upewnij się, że **podsieć** jest ustawiona na **myBackendSubnet**.
15. Wybierz **pozycję Utwórz**.

### <a name="associate-the-scale-set-with-the-proper-backend-pool"></a>Skojarz zestaw skalowania z odpowiednią pulą wewnętrznej bazy danych

Interfejs użytkownika portalu zestawu skalowania maszyny wirtualnej tworzy nową pulę wewnętrznej bazy danych dla zestawu skalowania, ale chcesz skojarzyć ją z istniejącą aplikacjąGatewayBackendPool.

1. Otwórz grupę zasobów **myResourceGroupAg.**
2. Wybierz **myAppGateway**.
3. Wybierz **pulę wewnętrznej bazy danych**.
4. Wybierz **myAppGatewaymyvmss**.
5. Wybierz **pozycję Usuń wszystkie obiekty docelowe z puli wewnętrznej bazy danych**.
6. Wybierz **pozycję Zapisz**.
7. Po zakończeniu tego procesu wybierz pulę zaplecza **myAppGatewaymyvmss,** wybierz pozycję **Usuń,** a następnie **przycisk OK,** aby potwierdzić.
8. Wybierz pozycję **appGatewayBackendPool**.
9. W obszarze **Obiekty docelowe**wybierz pozycję **VMSS**.
10. W obszarze **VMSS**wybierz **myvmss**.
11. W obszarze **Konfiguracje interfejsu sieciowego**wybierz **myvmssNic**.
12. Wybierz **pozycję Zapisz**.

### <a name="upgrade-the-scale-set"></a>Uaktualnianie zestawu skalowania

Na koniec należy uaktualnić zestaw skalowania z tymi zmianami.

1. Wybierz zestaw skalowania **myvmss.**
2. W obszarze **Ustawienia** wybierz pozycję **Wystąpienia**.
3. Zaznacz oba wystąpienia, a następnie wybierz pozycję **Uaktualnij**.
4. Wybierz pozycję **Tak**, aby potwierdzić.
5. Po zakończeniu, wróć do **myAppGateway** i wybierz **backend baseny**. Teraz powinieneś zobaczyć, że **appGatewayBackendPool** ma dwa cele, a **myAppGatewaymyvmss** ma zero celów.
6. Wybierz **myAppGatewaymyvmss**, a następnie wybierz pozycję **Usuń**.
7. Wybierz przycisk **OK**, aby potwierdzić.

### <a name="install-iis"></a>Instalowanie usług IIS

Łatwym sposobem zainstalowania usługi IIS w zestawie skalowania jest użycie programu PowerShell. W portalu kliknij ikonę powłoki chmury i upewnij się, że program **PowerShell** jest zaznaczony.

Wklej następujący kod do okna programu PowerShell i naciśnij klawisz Enter.

```azurepowershell
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
$vmss = Get-AzVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss
Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings
Update-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmss
```

### <a name="upgrade-the-scale-set"></a>Uaktualnianie zestawu skalowania

Po zmianie wystąpień za pomocą programów IIS należy ponownie uaktualnić zestaw skalowania za pomocą tej zmiany.

1. Wybierz zestaw skalowania **myvmss.**
2. W obszarze **Ustawienia** wybierz pozycję **Wystąpienia**.
3. Zaznacz oba wystąpienia, a następnie wybierz pozycję **Uaktualnij**.
4. Wybierz pozycję **Tak**, aby potwierdzić.

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

Publiczny adres IP aplikacji można uzyskać ze strony Przegląd bramy aplikacji.

1. Wybierz **myAppGateway**.
2. Na stronie **Przegląd** zanotuj adres IP w obszarze **Publiczny adres IP frontu**.

3. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki. Na przykład: http://52.170.203.149

   ![Ostrzeżenie o zabezpieczeniach](./media/redirect-http-to-https-powershell/application-gateway-secure.png)

4. Aby zaakceptować ostrzeżenie o zabezpieczeniach, jeśli używasz certyfikatu z podpisem własnym, wybierz pozycję **Szczegóły**, a następnie pozycję **Przejdź do strony internetowej**. Zostanie wyświetlona zabezpieczona witryna internetowa usług IIS, tak jak w poniższym przykładzie:

   ![Testowanie podstawowego adresu URL w bramie aplikacji](./media/redirect-http-to-https-powershell/application-gateway-iistest.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [utworzyć bramę aplikacji z wewnętrznym przekierowaniem](redirect-internal-site-powershell.md).
