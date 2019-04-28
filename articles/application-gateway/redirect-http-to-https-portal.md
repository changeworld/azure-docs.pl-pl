---
title: Tworzenie bramy aplikacji za pośrednictwem protokołu HTTP do przekierowania protokołu HTTPS przy użyciu witryny Azure portal
description: Dowiedz się, jak utworzyć bramę aplikacji przy użyciu przekierowanego ruchu z protokołu HTTP do HTTPS przy użyciu witryny Azure portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 12/7/2018
ms.author: victorh
ms.openlocfilehash: 17eef2fc2608ca4ccbabff8179cd63798d275582
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62101465"
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-the-azure-portal"></a>Tworzenie bramy aplikacji za pośrednictwem protokołu HTTP do przekierowania protokołu HTTPS przy użyciu witryny Azure portal

Można użyć witryny Azure portal, aby utworzyć [bramy application gateway](overview.md) przy użyciu certyfikatu dla kończenia żądań SSL. Reguła routingu jest używany do przekierowywania ruchu HTTP do portu HTTPS w Twojej bramy application gateway. W tym przykładzie tworzymy [zestawu skalowania maszyn wirtualnych](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) dla puli zaplecza usługi application gateway, która zawiera dwa wystąpienia maszyn wirtualnych.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Tworzenie certyfikatu z podpisem własnym
> * Konfigurowanie sieci
> * Tworzenie bramy aplikacji z certyfikatem
> * Dodaj regułę odbiornik i Przekierowanie
> * Tworzenie zestawu skalowania maszyn wirtualnych przy użyciu domyślnej puli zaplecza

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ten samouczek wymaga modułu Wersja 1.0.0 programu Azure PowerShell lub nowszej, aby utworzyć certyfikat i zainstalować usługi IIS. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Aby uruchamiać polecenia w tym samouczku, trzeba będzie również uruchomić `Login-AzAccount` do utworzenia połączenia z platformą Azure.

## <a name="create-a-self-signed-certificate"></a>Tworzenie certyfikatu z podpisem własnym

Do użycia w środowisku produkcyjnym należy zaimportować prawidłowy certyfikat podpisany przez zaufanego dostawcę. W tym samouczku utworzysz certyfikat z podpisem własnym przy użyciu polecenia [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate). Korzystając z polecenia [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate) i zwróconego odcisku palca, możesz wyeksportować plik pfx z certyfikatu.

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

1. Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).
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
   - *10.0.1.0/24* — jako przestrzeni adresowej podsieci.

     ![Tworzenie sieci wirtualnej](./media/create-url-route-portal/application-gateway-vnet.png)

7. Kliknij przycisk **OK**, aby utworzyć sieć wirtualną i podsieć.
8. W obszarze **konfiguracja adresu IP frontonu**, upewnij się, **typ adresu IP** jest **publicznych**, i **Utwórz nową** jest zaznaczone. Wprowadź *myAGPublicIPAddress* dla nazwy. Zaakceptuj wartości domyślne dla innych ustawień, a następnie kliknij przycisk **OK**.
9. W obszarze **konfiguracji odbiornika**, wybierz opcję **HTTPS**, a następnie wybierz **wybierz plik** i przejdź do *c:\appgwcert.pfx* pliku i Wybierz **Otwórz**.
10. Typ *appgwcert* dla nazwy certyfikatu i *Azure123456!* jako hasło.
11. Pozostaw zapory aplikacji sieci Web, które są wyłączone, a następnie wybierz pozycję **OK**.
12. Przejrzyj ustawienia na stronie podsumowania, a następnie wybierz **OK** do tworzenia zasobów sieciowych i bramy aplikacji. Może upłynąć kilka minut w usłudze application gateway można utworzyć, poczekaj na wdrożenie zakończy się pomyślnie przed przejściem do następnej sekcji.

### <a name="add-a-subnet"></a>Dodawanie podsieci

1. Wybierz **wszystkie zasoby** w menu po lewej stronie, a następnie wybierz **myVNet** na liście zasobów.
2. Wybierz **podsieci**, a następnie kliknij przycisk **podsieci**.

    ![Tworzenie podsieci](./media/create-url-route-portal/application-gateway-subnet.png)

3. Typ *myBackendSubnet* nazwy podsieci.
4. Typ *10.0.2.0/24* dla zakresu adresów, a następnie wybierz **OK**.

## <a name="add-a-listener-and-redirection-rule"></a>Dodaj regułę odbiornik i Przekierowanie

### <a name="add-the-listener"></a>Dodawanie odbiornika

Najpierw dodaj odbiornik o nazwie *myListener* dla portu 80.

1. Otwórz **myResourceGroupAG** zasobu, grupy i wybierz **myAppGateway**.
2. Wybierz **odbiorników** , a następnie wybierz **+ podstawowa**.
3. Typ *MyListener* dla nazwy.
4. Typ *httpPort* dla nowej nazwy portu frontonu i *80* za pośrednictwem portu usługi.
5. Upewnij się, protokołu jest równa **HTTP**, a następnie wybierz pozycję **OK**.

### <a name="add-a-routing-rule-with-a-redirection-configuration"></a>Dodaj regułę routingu za pomocą konfiguracji przekierowania

1. Na **myAppGateway**, wybierz opcję **reguły** , a następnie wybierz **+ podstawowa**.
2. Aby uzyskać **nazwa**, typ *reguły Rule2*.
3. Upewnij się, **MyListener** został wybrany do odbiornika.
4. Wybierz **Konfigurowanie przekierowania** pole wyboru.
5. Aby uzyskać **typ przekierowania**, wybierz opcję **stałe**.
6. Aby uzyskać **miejsce docelowe przekierowania**, wybierz opcję **odbiornika**.
7. Upewnij się, **odbiornik docelowy** ustawiono **appGatewayHttpListener**.
8. Wybierz **ciąg zapytania dołączania** i **ścieżki dołączania** pola wyboru.
9. Kliknij przycisk **OK**.

## <a name="create-a-virtual-machine-scale-set"></a>Tworzenie zestawu skalowania maszyn wirtualnych

W tym przykładzie utworzysz zestaw skalowania maszyn wirtualnych, aby zapewnić serwery dla puli zaplecza w bramie aplikacji.

1. W portalu lewym górnym rogu, wybierz **+ Utwórz zasób**.
2. Wybierz **obliczenia**.
3. W polu wyszukiwania wpisz *zestawu skalowania* i naciśnij klawisz Enter.
4. Wybierz **zestawu skalowania maszyn wirtualnych**, a następnie wybierz pozycję **Utwórz**.
5. Aby uzyskać **nazwy zestawu skalowania maszyn wirtualnych**, typ *myvmss*.
6. Obraz dysku systemu operacyjnego ** upewnij się, **systemu Windows Server 2016 Datacenter** jest zaznaczone.
7. Aby uzyskać **grupy zasobów**, wybierz opcję **myResourceGroupAG**.
8. Aby uzyskać **nazwa_użytkownika**, typ *azureuser*.
9. Aby uzyskać **hasło**, typ *Azure123456!* i Potwierdź hasło.
10. Aby uzyskać **liczba wystąpień**, upewnij się, wartość jest **2**.
11. Aby uzyskać **rozmiaru wystąpienia**, wybierz opcję **D2s_v3**.
12. W obszarze **sieć**, upewnij się, **wybierz opcje równoważenia obciążenia** ustawiono **Application Gateway**.
13. Upewnij się, **bramy Application gateway** ustawiono **myAppGateway**.
14. Upewnij się, **podsieci** ustawiono **myBackendSubnet**.
15. Wybierz pozycję **Utwórz**.

### <a name="associate-the-scale-set-with-the-proper-backend-pool"></a>Skojarzenie zestawu z pulą zaplecza odpowiedniego skalowania

Interfejs użytkownika portalu zestaw skali maszyny wirtualnej jest tworzona nowa pula wewnętrznej bazy danych dla zestawu skalowania, ale chcesz skojarzyć go z Twojego istniejącego appGatewayBackendPool.

1. Otwórz **myResourceGroupAg** grupy zasobów.
2. Wybierz **myAppGateway**.
3. Wybierz **pule zaplecza**.
4. Wybierz **myAppGatewaymyvmss**.
5. Wybierz **Usuń wszystkie elementy docelowe z puli zaplecza**.
6. Wybierz pozycję **Zapisz**.
7. Po zakończeniu tego procesu, wybierz **myAppGatewaymyvmss** puli zaplecza, wybierz opcję **Usuń** i następnie **OK** o potwierdzenie.
8. Wybierz **appGatewayBackendPool**.
9. W obszarze **cele**, wybierz opcję **VMSS**.
10. W obszarze **VMSS**, wybierz opcję **myvmss**.
11. W obszarze **konfiguracji interfejsu sieciowego**, wybierz opcję **myvmssNic**.
12. Wybierz pozycję **Zapisz**.

### <a name="upgrade-the-scale-set"></a>Uaktualnij zestaw skalowania

Na koniec należy uaktualnić zestawu skalowania przy użyciu tych zmian.

1. Wybierz **myvmss** zestawu skalowania.
2. W obszarze **ustawienia**, wybierz opcję **wystąpień**.
3. Wybierz oba wystąpienia, a następnie wybierz **uaktualnienia**.
4. Wybierz pozycję **Tak**, aby potwierdzić.
5. Po zakończeniu tego procesu, wróć do **myAppGateway** i wybierz **pule zaplecza**. Powinny teraz widzimy, że **appGatewayBackendPool** ma dwa obiekty docelowe, a **myAppGatewaymyvmss** ma zero elementów docelowych.
6. Wybierz **myAppGatewaymyvmss**, a następnie wybierz pozycję **Usuń**.
7. Wybierz **OK** o potwierdzenie.

### <a name="install-iis"></a>Instalowanie usług IIS

To prosty sposób, aby zainstalować usługi IIS w zestawie skalowania przy użyciu programu PowerShell. Z poziomu portalu, kliknij ikonę usługi Cloud Shell, a następnie upewnij się, że **PowerShell** jest zaznaczone.

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

### <a name="upgrade-the-scale-set"></a>Uaktualnij zestaw skalowania

Po zmianie wystąpień z usługami IIS, należy uaktualnić ponownie zestawu skalowania przy użyciu tej zmiany.

1. Wybierz **myvmss** zestawu skalowania.
2. W obszarze **ustawienia**, wybierz opcję **wystąpień**.
3. Wybierz oba wystąpienia, a następnie wybierz **uaktualnienia**.
4. Wybierz pozycję **Tak**, aby potwierdzić.

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

Na stronie Przegląd bramy aplikacji, można uzyskać publicznego adresu IP aplikacji.

1. Wybierz **myAppGateway**.
2. Na **Przegląd** strony, adres IP w ramach **publiczny adres IP frontonu**.

3. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki. Na przykład: http://52.170.203.149

   ![Ostrzeżenie o zabezpieczeniach](./media/redirect-http-to-https-powershell/application-gateway-secure.png)

4. Aby zaakceptować ostrzeżenie o zabezpieczeniach, jeśli używasz certyfikatu z podpisem własnym, wybierz pozycję **Szczegóły**, a następnie pozycję **Przejdź do strony internetowej**. Zostanie wyświetlona zabezpieczona witryna internetowa usług IIS, tak jak w poniższym przykładzie:

   ![Testowanie podstawowego adresu URL w bramie aplikacji](./media/redirect-http-to-https-powershell/application-gateway-iistest.png)

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak [Tworzenie bramy aplikacji za pomocą wewnętrznego przekierowania](redirect-internal-site-powershell.md).
