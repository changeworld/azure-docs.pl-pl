---
title: Utwórz bramę aplikacji - portalu Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć bramę aplikacji przy użyciu portalu Azure.
services: application-gateway
author: vhorne
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/25/2018
ms.author: victorh
ms.openlocfilehash: 0df71c445d2c5fc6827b69f708203a3b3e6e2b53
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
ms.locfileid: "33202392"
---
# <a name="create-an-application-gateway-using-the-azure-portal"></a>Utwórz bramę aplikacji przy użyciu portalu Azure

Azure portal umożliwia tworzenie lub zarządzanie bramy aplikacji. Ta opcja szybkiego startu przedstawia tworzenie zasobów sieciowych, serwerów wewnętrznej bazy danych oraz bramy aplikacji.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do portalu Azure pod adresem [http://portal.azure.com](http://portal.azure.com)

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

Sieć wirtualna jest wymagany dla komunikacji między zasobami, które można utworzyć. Dwie podsieci są tworzone w tym przykładzie: jeden dla bramy aplikacji, a drugi dla serwerów zaplecza. W tym samym czasie utworzonego bramy aplikacji może utworzyć sieć wirtualną.

1. Kliknij przycisk **nowy** znaleziono w lewym górnym rogu portalu Azure.
2. Wybierz **sieci** , a następnie wybierz **brama aplikacji w** na liście duży.
3. Wprowadź wartości dla bramy aplikacji:

    - *myAppGateway* — nazwa bramy aplikacji.
    - *myResourceGroupAG* — dla nowej grupy zasobów.

    ![Utwórz nową bramę aplikacji](./media/application-gateway-create-gateway-portal/application-gateway-create.png)

4. Zaakceptuj wartości domyślne dla innych ustawień, a następnie kliknij przycisk **OK**.
5. Kliknij przycisk **wybierz sieć wirtualną**, kliknij przycisk **Utwórz nowy**, a następnie wprowadź wartości dla sieci wirtualnej:

    - *myVNet* — dla nazwy sieci wirtualnej.
    - *10.0.0.0/16* — do przestrzeni adresowej sieci wirtualnej.
    - *myAGSubnet* — dla nazwy podsieci.
    - *10.0.0.0/24* — do przestrzeni adresowej podsieci.

    ![Tworzenie sieci wirtualnej](./media/application-gateway-create-gateway-portal/application-gateway-vnet.png)

6. Kliknij przycisk **OK** do tworzenia sieci wirtualnej i podsieci.
6. Kliknij przycisk **wybierz publiczny adres IP**, kliknij przycisk **Utwórz nowy**, a następnie wprowadź nazwę publicznego adresu IP. W tym przykładzie publiczny adres IP o nazwie *myAGPublicIPAddress*. Zaakceptuj wartości domyślne dla innych ustawień, a następnie kliknij przycisk **OK**.
8. Zaakceptuj wartości domyślne w konfiguracji odbiornika, pozostaw zapory aplikacji sieci Web, które są wyłączone, a następnie kliknij **OK**.
9. Przejrzyj ustawienia na stronie Podsumowanie, a następnie kliknij przycisk **OK** Aby utworzyć sieć wirtualną, publiczny adres IP i bramy aplikacji. Może upłynąć kilka minut dla bramy aplikacji można utworzyć, poczekaj na wdrożenie zakończy się pomyślnie przed przejściem do następnej sekcji.

### <a name="add-a-subnet"></a>Dodaj podsieć

1. Kliknij przycisk **wszystkie zasoby** w menu po lewej stronie, a następnie kliknij przycisk **myVNet** na liście zasobów.
2. Kliknij przycisk **podsieci**, a następnie kliknij przycisk **podsieci**.

    ![Tworzenie podsieci](./media/application-gateway-create-gateway-portal/application-gateway-subnet.png)

3. Wprowadź *myBackendSubnet* dla nazwy podsieci, a następnie kliknij przycisk **OK**.

## <a name="create-backend-servers"></a>Tworzenie serwerów zaplecza

W tym przykładzie utworzysz dwie maszyny wirtualne do użycia jako serwery zaplecza bramy aplikacji. Należy również zainstalować usług IIS na maszynach wirtualnych, aby sprawdzić, czy brama aplikacji została pomyślnie utworzona.

### <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. Kliknij przycisk **Nowy**.
2. Kliknij przycisk **obliczeniowe** , a następnie wybierz **systemu Windows Server 2016 Datacenter** na liście duży.
3. Wprowadź wartości dla maszyny wirtualnej:

    - *myVM* — Nazwa maszyny wirtualnej.
    - *azureuser* — jako nazwę użytkownika administratora.
    - *Azure123456!* hasła.
    - Wybierz **Użyj istniejącego**, a następnie wybierz *myResourceGroupAG*.

4. Kliknij przycisk **OK**.
5. Wybierz **DS1_V2** jako rozmiar maszyny wirtualnej, a następnie kliknij pozycję **Wybierz**.
6. Upewnij się, że **myVNet** został wybrany do sieci wirtualnej i podsieci jest **myBackendSubnet**. 
7. Kliknij pozycję **Wyłączone**, aby wyłączyć diagnostykę rozruchu.
8. Kliknij przycisk **OK**, przejrzyj ustawienia na stronie podsumowania, a następnie kliknij przycisk **Utwórz**.

### <a name="install-iis"></a>Instalowanie usług IIS

1. Otwórz powłokę interakcyjne i upewnij się, że jest ustawiona na **PowerShell**.

    ![Zainstaluj rozszerzenia niestandardowego](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

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

3. Utwórz maszynę wirtualną drugiej i zainstaluj usługi IIS, wykonując kroki, które właśnie zostało zakończone. Wprowadź *myVM2* dla nazwy i VMName w AzureRmVMExtension zestawu.

### <a name="add-backend-servers"></a>Dodawanie serwerów wewnętrznej bazy danych

3. Kliknij przycisk **wszystkie zasoby**, a następnie kliknij przycisk **myAppGateway**.
4. Kliknij przycisk **pul zaplecza**. Domyślna pula został utworzony automatycznie z bramy aplikacji. Kliknij przycisk **appGatewayBackendPool**.
5. Kliknij przycisk **docelowy Dodaj** dodawania każdej maszyny wirtualnej, który został utworzony do puli wewnętrznej bazy danych.

    ![Dodawanie serwerów wewnętrznej bazy danych](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Kliknij pozycję **Zapisz**.

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

1. Znajdź publicznego adresu IP dla bramy aplikacji na ekran Przegląd. Kliknij przycisk **wszystkie zasoby** , a następnie kliknij przycisk **myAGPublicIPAddress**.

    ![Zarejestruj publiczny adres IP bramy aplikacji](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png)

2. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki.

    ![Brama aplikacji w testu](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, Usuń grupę zasobów, bramę aplikacji i wszystkie powiązane zasoby. Aby to zrobić, wybierz grupę zasobów, która zawiera bramy aplikacji i kliknij przycisk **usunąć**.

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki start utworzono grupę zasobów, zasoby sieciowe i serwery zaplecza. Te zasoby są następnie używane do tworzenia bramy aplikacji. Aby dowiedzieć się więcej na temat bram aplikacji i ich skojarzonych zasobów, nadal artykuły.
