---
title: Tworzenie bramy aplikacji — witryna Azure Portal
description: Dowiedz się, jak utworzyć bramę aplikacji przy użyciu witryny Azure portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2018
ms.author: victorh
ms.openlocfilehash: ba4ab7e488de4a8d3c9fa702c6b33f9ea31b2c2a
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2018
ms.locfileid: "51615963"
---
# <a name="create-an-application-gateway-using-the-azure-portal"></a>Tworzenie bramy aplikacji przy użyciu witryny Azure portal

Za pomocą witryny Azure portal do utworzenia lub zarządzania bramy application Gateway. Ten przewodnik Szybki Start dowiesz się, jak utworzyć zasoby sieciowe, serwerów wewnętrznej bazy danych i bramy aplikacji.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [http://portal.azure.com](http://portal.azure.com)

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

Sieć wirtualna jest wymagane do komunikacji między zasobami, które tworzysz. W tym przykładzie są tworzone dwie podsieci: jedna dla bramy aplikacji i druga dla serwerów zaplecza. Sieć wirtualną można utworzyć podczas tworzenia bramy aplikacji.

1. Kliknij przycisk **New** w lewym górnym rogu witryny Azure portal.
2. Wybierz pozycję **Sieć**, a następnie z listy Polecane wybierz pozycję **Application Gateway**.
3. Wprowadź następujące wartości dla bramy aplikacji:

    - *myAppGateway* — jako nazwę bramy aplikacji.
    - *myResourceGroupAG* — jako nową grupę zasobów.

    ![Tworzenie nowej bramy aplikacji](./media/application-gateway-create-gateway-portal/application-gateway-create.png)

4. Zaakceptuj wartości domyślne dla innych ustawień, a następnie kliknij przycisk **OK**.
5. Kliknij przycisk **wybierz sieć wirtualną**, kliknij przycisk **Utwórz nową**, a następnie wprowadź następujące wartości dla sieci wirtualnej:

    - *myVNet* — jako nazwę sieci wirtualnej.
    - *10.0.0.0/16* — jako przestrzeń adresową sieci wirtualnej.
    - *myAGSubnet* — jako nazwę podsieci.
    - *10.0.0.0/24* — jako przestrzeń adresową podsieci.

    ![Tworzenie sieci wirtualnej](./media/application-gateway-create-gateway-portal/application-gateway-vnet.png)

6. Kliknij przycisk **OK**, aby utworzyć sieć wirtualną i podsieć.
6. Kliknij przycisk **wybierz publiczny adres IP**, kliknij przycisk **Utwórz nową**, a następnie wprowadź nazwę publicznego adresu IP. W tym przykładzie publiczny adres IP nosi nazwę *myAGPublicIPAddress*. Zaakceptuj wartości domyślne dla innych ustawień, a następnie kliknij przycisk **OK**.
8. Zaakceptuj wartości domyślne dla konfiguracji odbiornika, pozostaw zapory aplikacji sieci Web, które są wyłączone, a następnie kliknij przycisk **OK**.
9. Przejrzyj ustawienia na stronie podsumowania, a następnie kliknij przycisk **OK**, aby utworzyć sieć wirtualną, publiczny adres IP i bramę aplikacji. Może upłynąć kilka minut w usłudze application gateway można utworzyć, poczekaj na wdrożenie zakończy się pomyślnie przed przejściem do następnej sekcji.

### <a name="add-a-subnet"></a>Dodawanie podsieci

1. W menu po lewej stronie kliknij pozycję **Wszystkie zasoby**, a następnie na liście zasobów kliknij pozycję **myVNet**.
2. Kliknij przycisk **podsieci**, a następnie kliknij przycisk **podsieci**.

    ![Tworzenie podsieci](./media/application-gateway-create-gateway-portal/application-gateway-subnet.png)

3. Wprowadź *myBackendSubnet* jako nazwę podsieci, a następnie kliknij przycisk **OK**.

## <a name="create-backend-servers"></a>Tworzenie serwerów zaplecza

W tym przykładzie utworzysz dwie maszyny wirtualne, które są używane jako serwery zaplecza w usłudze application gateway. Zainstaluj również usługi IIS na maszynach wirtualnych, aby sprawdzić, czy brama aplikacji została pomyślnie utworzona.

### <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. Kliknij przycisk **Nowy**.
2. Kliknij przycisk **obliczenia** , a następnie wybierz **systemu Windows Server 2016 Datacenter** na liście proponowany.
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

1. Otwórz interaktywnej powłoki i upewnij się, że jest ustawiona na **PowerShell**.

    ![Instalowanie rozszerzenia niestandardowego](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

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

3. Kliknij przycisk **wszystkie zasoby**, a następnie kliknij przycisk **myAppGateway**.
4. Kliknij pozycję **Pule zaplecza**. Domyślna pula została utworzona automatycznie podczas tworzenia bramy aplikacji. Kliknij pozycję **appGatewayBackendPool**.
5. Kliknij przycisk **Add target** dodawania każdej maszyny wirtualnej, który został utworzony do puli zaplecza.

    ![Dodawanie serwerów zaplecza](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Kliknij pozycję **Zapisz**.

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

1. Na ekranie Przegląd znajdź publiczny adres IP bramy aplikacji. Kliknij przycisk **wszystkie zasoby** a następnie kliknij przycisk **myAGPublicIPAddress**.

    ![Rejestrowanie publicznego adresu IP bramy aplikacji](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png)

2. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki.

    ![Testowanie bramy aplikacji](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, Usuń grupę zasobów, usługa application gateway i wszystkie powiązane zasoby. Aby to zrobić, wybierz grupę zasobów zawierającą bramę aplikacji, a następnie kliknij przycisk **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki start utworzono grupę zasobów, zasoby sieciowe i serwery zaplecza. Te zasoby są następnie używane do utworzenia bramy aplikacji. Aby dowiedzieć się więcej na temat bramy aplikacji i skojarzonych z nimi zasobów, przejdź do artykuły z poradami.
