---
title: Tworzenie bramy aplikacji — witryna Azure Portal
description: Dowiedz się, jak utworzyć bramę aplikacji przy użyciu witryny Azure portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2018
ms.author: victorh
ms.openlocfilehash: d8ab49bc988060533bc5ff65d414dcba6245be48
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51631913"
---
# <a name="create-an-application-gateway-using-the-azure-portal"></a>Tworzenie bramy aplikacji przy użyciu witryny Azure portal

Za pomocą witryny Azure portal do utworzenia lub zarządzania bramy application Gateway. W tym artykule przedstawiono sposób tworzenia zasobów sieciowych, serwerów wewnętrznej bazy danych i bramy aplikacji.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [http://portal.azure.com](http://portal.azure.com)

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

Sieć wirtualna jest wymagane do komunikacji między zasobami, które tworzysz. W tym przykładzie są tworzone dwie podsieci: jedna dla bramy aplikacji i druga dla serwerów zaplecza. Sieć wirtualną można utworzyć podczas tworzenia bramy aplikacji.

1. W lewym górnym rogu witryny Azure Portal kliknij przycisk **Utwórz zasób**.
2. Kliknij przycisk **sieć** a następnie kliknij przycisk **Application Gateway** na liście proponowany.

### <a name="basics"></a>Podstawy

1. Wprowadź następujące wartości dla bramy aplikacji:

    - *myAppGateway* — jako nazwę bramy aplikacji.
    - *myResourceGroupAG* — jako nową grupę zasobów.

    ![Tworzenie nowej bramy aplikacji](./media/application-gateway-create-gateway-portal/application-gateway-create.png)

2. Zaakceptuj wartości domyślne dla innych ustawień, a następnie kliknij przycisk **OK**.

### <a name="settings"></a>Ustawienia

1. Kliknij przycisk **wybierz sieć wirtualną**, kliknij przycisk **Utwórz nową**, a następnie wprowadź następujące wartości dla sieci wirtualnej:

    - *myVNet* — jako nazwę sieci wirtualnej.
    - *10.0.0.0/16* — jako przestrzeń adresową sieci wirtualnej.
    - *myAGSubnet* — jako nazwę podsieci.
    - *10.0.0.0/24* — jako zakres adresów podsieci.

    ![Tworzenie sieci wirtualnej](./media/application-gateway-create-gateway-portal/application-gateway-vnet.png)

6. Kliknij przycisk **OK** aby wrócić do strony ustawień.
7. W obszarze **konfiguracja adresu IP frontonu** upewnij się, **typ adresu IP** jest ustawiona na **publicznych**, a następnie w obszarze **publiczny adres IP**, upewnij się, **Utwórz nową** jest zaznaczone. Typ *myAGPublicIPAddress* dla nazwa publicznego adresu IP. Zaakceptuj wartości domyślne dla innych ustawień, a następnie kliknij przycisk **OK**.

### <a name="summary"></a>Podsumowanie

Przejrzyj ustawienia na stronie podsumowania, a następnie kliknij przycisk **OK**, aby utworzyć sieć wirtualną, publiczny adres IP i bramę aplikacji. Tworzenie bramy aplikacji może potrwać kilka minut. Poczekaj na wdrożenie zakończy się pomyślnie przed przejściem do następnej sekcji.

## <a name="add-a-subnet"></a>Dodawanie podsieci

1. W menu po lewej stronie kliknij pozycję **Wszystkie zasoby**, a następnie na liście zasobów kliknij pozycję **myVNet**.
2. Kliknij przycisk **podsieci**, a następnie kliknij przycisk **+ podsieć**.

    ![Tworzenie podsieci](./media/application-gateway-create-gateway-portal/application-gateway-subnet.png)

3. Wprowadź *myBackendSubnet* jako nazwę podsieci, a następnie kliknij przycisk **OK**.

## <a name="create-backend-servers"></a>Tworzenie serwerów zaplecza

W tym przykładzie utworzysz dwie maszyny wirtualne, które są używane jako serwery zaplecza w usłudze application gateway. Zainstaluj również usługi IIS na maszynach wirtualnych, aby sprawdzić, czy brama aplikacji została pomyślnie utworzona.

### <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. W witrynie Azure Portal kliknij pozycję **Utwórz zasób**.
2. Kliknij przycisk **obliczenia** , a następnie wybierz **systemu Windows Server 2016 Datacenter** na liście proponowany.
3. Wprowadź poniższe wartości dla maszyny wirtualnej:

    - *myResourceGroupAG* dla grupy zasobów.
    - *myVM* — jako nazwę maszyny wirtualnej.
    - *azureuser* — jako nazwę użytkownika administratora.
    - *Azure123456!* jako hasło.

   Zaakceptuj pozostałe wartości domyślne, a następnie kliknij przycisk **dalej: dyski**.
4. Zaakceptuj ustawienia domyślne dysku, a następnie kliknij przycisk **dalej: sieć**.
5. Upewnij się, że wybrano sieć wirtualną **myVNet** i podsieć **myBackendSubnet**.
6. Zaakceptuj pozostałe wartości domyślne, a następnie kliknij przycisk **dalej: zarządzanie**.
7. Kliknij przycisk **poza** Aby wyłączyć diagnostykę rozruchu. Zaakceptuj pozostałe wartości domyślne, a następnie kliknij przycisk **przeglądu + Utwórz**.
8. Przejrzyj ustawienia na stronie podsumowania, a następnie kliknij przycisk **Utwórz**.
9. Poczekaj na ukończenie przed kontynuowaniem tworzenia maszyny wirtualnej.

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

1. Kliknij przycisk **wszystkie zasoby**, a następnie kliknij przycisk **myAppGateway**.
4. Kliknij pozycję **Pule zaplecza**. Domyślna pula została utworzona automatycznie podczas tworzenia bramy aplikacji. Kliknij pozycję **appGatewayBackendPool**.
5. W obszarze **cele**, kliknij przycisk **IP adres lub nazwę FQDN** wybierz **maszyny wirtualnej**.
6. W obszarze **maszyny wirtualnej**, Dodaj maszyny wirtualne myVM i myVM2 i ich skojarzone interfejsy sieciowe.

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

W tym artykule utworzono grupę zasobów, zasobów sieciowych i serwerów wewnętrznej bazy danych. Te zasoby są następnie używane do utworzenia bramy aplikacji. Aby dowiedzieć się więcej na temat bramy aplikacji i skojarzonych z nimi zasobów, zobacz [co to jest Azure Application Gateway?](overview.md)
