---
title: Konfigurowanie bramy aplikacji przy użyciu kończenia żądań SSL — witryna Azure portal | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować bramę aplikacji i dodawanie certyfikatu do kończenia żądań SSL przy użyciu witryny Azure portal.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.date: 5/15/2018
ms.author: victorh
ms.openlocfilehash: 814c3ebec326ab1c17f4fea7f11b2bacaa6b42d9
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52997608"
---
# <a name="configure-an-application-gateway-with-ssl-termination-using-the-azure-portal"></a>Konfigurowanie bramy aplikacji przy użyciu kończenia żądań SSL przy użyciu witryny Azure portal

Witryna Azure portal służy do konfigurowania [bramy application gateway](overview.md) przy użyciu certyfikatu dla kończenia żądań SSL, który używa maszyn wirtualnych dla serwerów wewnętrznej bazy danych.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Tworzenie certyfikatu z podpisem własnym
> * Tworzenie bramy aplikacji z certyfikatem
> * Tworzenie maszyn wirtualnych, używane jako serwery zaplecza

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com)

## <a name="create-a-self-signed-certificate"></a>Tworzenie certyfikatu z podpisem własnym

W tej sekcji użyjesz [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) do utworzenia certyfikatu z podpisem własnym, które są przesyłane do witryny Azure portal podczas tworzenia odbiornika dla usługi application gateway.

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

1. Kliknij przycisk **New** w lewym górnym rogu witryny Azure portal.
2. Wybierz pozycję **Sieć**, a następnie z listy Polecane wybierz pozycję **Application Gateway**.
3. Wprowadź *myAppGateway* dla nazwy usługi application gateway i *myResourceGroupAG* dla nowej grupy zasobów.
4. Zaakceptuj wartości domyślne dla innych ustawień, a następnie kliknij przycisk **OK**.
5. Kliknij kolejno pozycje **Wybierz sieć wirtualną**, **Utwórz nową**, a następnie wprowadź następujące wartości dla sieci wirtualnej:

    - *myVNet* — jako nazwę sieci wirtualnej.
    - *10.0.0.0/16* — jako przestrzeń adresową sieci wirtualnej.
    - *myAGSubnet* — jako nazwę podsieci.
    - *10.0.0.0/24* — jako przestrzeń adresową podsieci.

    ![Tworzenie sieci wirtualnej](./media/create-ssl-portal/application-gateway-vnet.png)

6. Kliknij przycisk **OK**, aby utworzyć sieć wirtualną i podsieć.
7. Kliknij przycisk **wybierz publiczny adres IP**, kliknij przycisk **Utwórz nową**, a następnie wprowadź nazwę publicznego adresu IP. W tym przykładzie publiczny adres IP nosi nazwę *myAGPublicIPAddress*. Zaakceptuj wartości domyślne dla innych ustawień, a następnie kliknij przycisk **OK**.
8. Kliknij przycisk **HTTPS** dla protokołu odbiornika i upewnij się, że port jest zdefiniowany jako **443**.
9. Kliknij ikonę folderu i przejdź do *appgwcert.pfx* certyfikat, który został wcześniej utworzony do przekazania go.
10. Wprowadź *mycert1* dla nazwy certyfikatu i *Azure123456!* hasło, a następnie kliknij przycisk **OK**.

    ![Tworzenie nowej bramy aplikacji](./media/create-ssl-portal/application-gateway-create.png)

11. Przejrzyj ustawienia na stronie podsumowania, a następnie kliknij przycisk **OK** do tworzenia zasobów sieciowych i bramy aplikacji. Może upłynąć kilka minut w usłudze application gateway można utworzyć, poczekaj na wdrożenie zakończy się pomyślnie przed przejściem do następnej sekcji.

### <a name="add-a-subnet"></a>Dodawanie podsieci

1. W menu po lewej stronie kliknij pozycję **Wszystkie zasoby**, a następnie na liście zasobów kliknij pozycję **myVNet**.
2. Kliknij przycisk **podsieci**, a następnie kliknij przycisk **podsieci**.

    ![Tworzenie podsieci](./media/create-ssl-portal/application-gateway-subnet.png)

3. Wprowadź *myBackendSubnet* jako nazwę podsieci, a następnie kliknij przycisk **OK**.

## <a name="create-backend-servers"></a>Tworzenie serwerów zaplecza

W tym przykładzie utworzysz dwie maszyny wirtualne, które będą używane jako serwery zaplecza dla bramy aplikacji. Zainstaluj również usługi IIS na maszynach wirtualnych, aby sprawdzić, czy brama aplikacji została pomyślnie utworzona.

### <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. Kliknij przycisk **Nowy**.
2. Kliknij pozycję **Compute** (Wystąpienia obliczeniowe), a następnie z listy Polecane wybierz pozycję **Windows Server 2016 Datacenter**.
3. Wprowadź poniższe wartości dla maszyny wirtualnej:

    - *myVM* — jako nazwę maszyny wirtualnej.
    - *azureuser* — jako nazwę użytkownika administratora.
    - *Azure123456!* jako hasło.
    - Wybierz pozycję **Użyj istniejącej** i wybierz grupę *myResourceGroupAG*.

4. Kliknij przycisk **OK**.
5. Wybierz **DS1_V2** jako rozmiar maszyny wirtualnej, a następnie kliknij pozycję **Wybierz**.
6. Upewnij się, że wybrano sieć wirtualną **myVNet** i podsieć **myBackendSubnet**. 
7. Kliknij pozycję **Wyłączone**, aby wyłączyć diagnostykę rozruchu.
8. Kliknij przycisk **OK**, przejrzyj ustawienia na stronie podsumowania, a następnie kliknij przycisk **Utwórz**.

### <a name="install-iis"></a>Instalowanie usług IIS

1. Otwórz powłokę interaktywną i upewnij się, że jest ustawiona na program **PowerShell**.

    ![Instalowanie rozszerzenia niestandardowego](./media/create-ssl-portal/application-gateway-extension.png)

2. Uruchom następujące polecenie, aby zainstalować usługi IIS na maszynie wirtualnej: 

    ```azurepowershell-interactive
    Set-AzureRmVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName myVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location EastUS
    ```

3. Utwórz drugą maszynę wirtualną i zainstaluj usługi IIS, wykonując kroki, które właśnie ukończono. Wprowadź wartość *myVM2* dla jej nazwy i dla parametru VMName polecenia Set-AzureRmVMExtension.

### <a name="add-backend-servers"></a>Dodawanie serwerów zaplecza

3. Kliknij pozycję **Wszystkie zasoby**, a następnie kliknij pozycję **myAppGateway**.
4. Kliknij pozycję **Pule zaplecza**. Domyślna pula została utworzona automatycznie podczas tworzenia bramy aplikacji. Kliknij przycisk **appGateayBackendPool**.
5. Kliknij przycisk **Add target** dodawania każdej maszyny wirtualnej, który został utworzony do puli zaplecza.

    ![Dodawanie serwerów zaplecza](./media/create-ssl-portal/application-gateway-backend.png)

6. Kliknij pozycję **Zapisz**.

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

1. Kliknij przycisk **wszystkie zasoby**, a następnie kliknij przycisk **myAGPublicIPAddress**.

    ![Rejestrowanie publicznego adresu IP bramy aplikacji](./media/create-ssl-portal/application-gateway-ag-address.png)

2. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki. Aby zaakceptować ostrzeżenie o zabezpieczeniach, jeśli używasz certyfikatu z podpisem własnym, wybierz szczegóły, a następnie przejdź do strony sieci Web:

    ![Ostrzeżenie o zabezpieczeniach](./media/create-ssl-portal/application-gateway-secure.png)

    Zostanie wyświetlona zabezpieczona witryna internetowa usług IIS, tak jak w poniższym przykładzie:

    ![Testowanie podstawowego adresu URL w bramie aplikacji](./media/create-ssl-portal/application-gateway-iistest.png)

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie certyfikatu z podpisem własnym
> * Tworzenie bramy aplikacji z certyfikatem
> * Tworzenie maszyn wirtualnych, używane jako serwery zaplecza

Aby dowiedzieć się więcej na temat bramy aplikacji i skojarzonych z nimi zasobów, przejdź do artykuły z poradami.