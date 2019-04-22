---
title: Samouczek — Konfigurowanie bramy aplikacji przy użyciu kończenia żądań SSL — witryna Azure portal
description: W tym samouczku dowiesz się, jak skonfigurować bramę aplikacji i dodawanie certyfikatu do kończenia żądań SSL przy użyciu witryny Azure portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 4/17/2019
ms.author: victorh
ms.openlocfilehash: f3ba3eb12dc85a72c4e49c374e62209b83400d33
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2019
ms.locfileid: "59677854"
---
# <a name="tutorial-configure-an-application-gateway-with-ssl-termination-using-the-azure-portal"></a>Samouczek: Konfigurowanie bramy aplikacji przy użyciu kończenia żądań SSL przy użyciu witryny Azure portal

Witryna Azure portal służy do konfigurowania [bramy application gateway](overview.md) przy użyciu certyfikatu dla kończenia żądań SSL, który używa maszyn wirtualnych dla serwerów wewnętrznej bazy danych.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie certyfikatu z podpisem własnym
> * Tworzenie bramy aplikacji z certyfikatem
> * Tworzenie maszyn wirtualnych, używane jako serwery zaplecza
> * Testowanie bramy aplikacji

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com)

## <a name="create-a-self-signed-certificate"></a>Tworzenie certyfikatu z podpisem własnym

W tej sekcji użyjesz [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) do utworzenia certyfikatu z podpisem własnym. Możesz przekazać certyfikat do witryny Azure portal, podczas tworzenia odbiornika dla usługi application gateway.

Na komputerze lokalnym Otwórz okno programu Windows PowerShell jako administrator. Uruchom następujące polecenie, aby utworzyć certyfikat:

```powershell
New-SelfSignedCertificate \
  -certstorelocation cert:\localmachine\my \
  -dnsname www.contoso.com
```

Powinien zostać wyświetlony podobny do tej odpowiedzi:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com

Use [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate) with the Thumbprint that was returned to export a pfx file from the certificate:
```

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456!" -Force -AsPlainText
Export-PfxCertificate \
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 \
  -FilePath c:\appgwcert.pfx \
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

Sieć wirtualna jest potrzebna do komunikacji między utworzonymi zasobami. W tym przykładzie są tworzone dwie podsieci: jedna dla bramy aplikacji i druga dla serwerów zaplecza. Sieć wirtualną można utworzyć podczas tworzenia bramy aplikacji.

1. Wybierz **New** w lewym górnym rogu witryny Azure portal.
2. Wybierz pozycję **Sieć**, a następnie z listy Polecane wybierz pozycję **Application Gateway**.
3. Wprowadź *myAppGateway* dla nazwy usługi application gateway i *myResourceGroupAG* dla nowej grupy zasobów.
4. Zaakceptuj wartości domyślne innych ustawień, a następnie kliknij przycisk **OK**.
5. Wybierz **wybierz sieć wirtualną**, wybierz opcję **Utwórz nową**, a następnie wprowadź następujące wartości dla sieci wirtualnej:

   - *myVNet* — jako nazwę sieci wirtualnej.
   - *10.0.0.0/16* — jako przestrzeń adresową sieci wirtualnej.
   - *myAGSubnet* — jako nazwę podsieci.
   - *10.0.0.0/24* — jako przestrzeń adresową podsieci.

     ![Tworzenie sieci wirtualnej](./media/create-ssl-portal/application-gateway-vnet.png)

6. Wybierz **OK** utworzyć sieć wirtualną i podsieć.
7. Wybierz **wybierz publiczny adres IP**, wybierz opcję **Utwórz nową**, a następnie wprowadź nazwę publicznego adresu IP. W tym przykładzie publiczny adres IP nosi nazwę *myAGPublicIPAddress*. Zaakceptuj wartości domyślne innych ustawień, a następnie kliknij przycisk **OK**.
8. Wybierz **HTTPS** dla protokołu odbiornika i upewnij się, że port jest zdefiniowany jako **443**.
9. Wybierz ikonę folderu i przejdź do *appgwcert.pfx* certyfikat, który został wcześniej utworzony do przekazania go.
10. Wprowadź *mycert1* dla nazwy certyfikatu i *Azure123456!* jako hasło, a następnie wybierz **OK**.

    ![Tworzenie nowej bramy aplikacji](./media/create-ssl-portal/application-gateway-create.png)

11. Przejrzyj ustawienia na stronie podsumowania, a następnie wybierz **OK** do tworzenia zasobów sieciowych i bramy aplikacji. Może upłynąć kilka minut w usłudze application gateway można utworzyć, poczekaj na wdrożenie zakończy się pomyślnie przed przejściem do następnej sekcji.

### <a name="add-a-subnet"></a>Dodawanie podsieci

1. Wybierz **wszystkie zasoby** w menu po lewej stronie, a następnie wybierz **myVNet** na liście zasobów.
2. Wybierz **podsieci**, a następnie wybierz pozycję **podsieci**.

    ![Tworzenie podsieci](./media/create-ssl-portal/application-gateway-subnet.png)

3. Wprowadź *myBackendSubnet* dla nazwy podsieci, a następnie wybierz **OK**.

## <a name="create-backend-servers"></a>Tworzenie serwerów zaplecza

W tym przykładzie utworzysz dwie maszyny wirtualne, używane jako serwery zaplecza w usłudze application gateway. Możesz także zainstalować usługi IIS na maszynach wirtualnych, aby sprawdzić, czy brama aplikacji został pomyślnie utworzony.

### <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. Wybierz pozycję **Nowy**.
2. Wybierz pozycję **Compute**, a następnie z listy Polecane wybierz pozycję **Windows Server 2016 Datacenter**.
3. Wprowadź poniższe wartości dla maszyny wirtualnej:

    - *myVM* — jako nazwę maszyny wirtualnej.
    - *azureuser* — jako nazwę użytkownika administratora.
    - *Azure123456!* jako hasło.
    - Wybierz pozycję **Użyj istniejącej** i wybierz grupę *myResourceGroupAG*.

4. Kliknij przycisk **OK**.
5. Wybierz **DS1_V2** dla rozmiaru maszyny wirtualnej, a następnie wybierz pozycję **wybierz**.
6. Upewnij się, że wybrano sieć wirtualną **myVNet** i podsieć **myBackendSubnet**. 
7. Kliknij pozycję **Wyłączone**, aby wyłączyć diagnostykę rozruchu.
8. Kliknij przycisk **OK**, przejrzyj ustawienia na stronie podsumowania, a następnie wybierz pozycję **Utwórz**.

### <a name="install-iis"></a>Instalowanie usług IIS

1. Otwórz powłokę interaktywną i upewnij się, że jest ustawiona na program **PowerShell**.

    ![Instalowanie rozszerzenia niestandardowego](./media/create-ssl-portal/application-gateway-extension.png)

2. Uruchom następujące polecenie, aby zainstalować usługi IIS na maszynie wirtualnej: 

    ```azurepowershell-interactive
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName myVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location EastUS
    ```

3. Utwórz drugą maszynę wirtualną i zainstaluj usługi IIS, wykonując kroki, które właśnie ukończono. Wprowadź *myVM2* dla nazwy i VMName w AzVMExtension zestawu.

### <a name="add-backend-servers"></a>Dodawanie serwerów zaplecza

1. Wybierz pozycję **Wszystkie zasoby**, a następnie wybierz pozycję **myAppGateway**.
1. Wybierz **pule zaplecza**. Domyślna pula została utworzona automatycznie podczas tworzenia bramy aplikacji. Wybierz **appGatewayBackendPool**.
1. Wybierz **Add target** dodawania każdej maszyny wirtualnej, który został utworzony do puli zaplecza.

    ![Dodawanie serwerów zaplecza](./media/create-ssl-portal/application-gateway-backend.png)

1. Wybierz pozycję **Zapisz**.

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

1. Wybierz **wszystkie zasoby**, a następnie wybierz pozycję **myAGPublicIPAddress**.

    ![Rejestrowanie publicznego adresu IP bramy aplikacji](./media/create-ssl-portal/application-gateway-ag-address.png)

2. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki. Aby zaakceptować ostrzeżenie o zabezpieczeniach, jeśli używasz certyfikatu z podpisem własnym, wybierz szczegóły, a następnie przejdź do strony sieci Web:

    ![Ostrzeżenie o zabezpieczeniach](./media/create-ssl-portal/application-gateway-secure.png)

    Zostanie wyświetlona zabezpieczona witryna internetowa usług IIS, tak jak w poniższym przykładzie:

    ![Testowanie podstawowego adresu URL w bramie aplikacji](./media/create-ssl-portal/application-gateway-iistest.png)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o co można zrobić za pomocą usługi Azure Application Gateway](application-gateway-introduction.md)
