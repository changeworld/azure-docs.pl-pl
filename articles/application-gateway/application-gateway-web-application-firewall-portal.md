---
title: Utwórz bramę aplikacji z zapory aplikacji sieci web - portalu Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć bramę aplikacji za pomocą zapory aplikacji sieci web przy użyciu portalu Azure.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/26/2018
ms.author: victorh
ms.openlocfilehash: 9967813b193159b68aa0f008dae4440aa6e533dc
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
ms.locfileid: "33207395"
---
# <a name="create-an-application-gateway-with-a-web-application-firewall-using-the-azure-portal"></a>Utwórz bramę aplikacji za pomocą zapory aplikacji sieci web przy użyciu portalu Azure

Azure portal umożliwia tworzenie [brama aplikacji w](application-gateway-introduction.md) z [zapory aplikacji sieci web](application-gateway-web-application-firewall-overview.md) (WAF). Używa zapory aplikacji sieci Web [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) zasady ochrony aplikacji. Reguły obejmują ochronę przed ataki, takie jak iniekcja kodu SQL, atakami skryptów między witrynami i hijacks sesji.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Utwórz bramę aplikacji z zapory aplikacji sieci Web jest włączona
> * Tworzenie maszyn wirtualnych, używane jako serwery wewnętrznej bazy danych
> * Utwórz konto magazynu i skonfigurować diagnostykę

![Przykład zapory aplikacji sieci Web](./media/application-gateway-web-application-firewall-portal/scenario-waf.png)

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do portalu Azure pod adresem [http://portal.azure.com](http://portal.azure.com)

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

Sieć wirtualna jest wymagany dla komunikacji między zasobami, które można utworzyć. Dwie podsieci są tworzone w tym przykładzie: jeden dla bramy aplikacji, a drugi dla serwerów zaplecza. W tym samym czasie utworzonego bramy aplikacji może utworzyć sieć wirtualną.

1. Kliknij przycisk **nowy** znaleziono w lewym górnym rogu portalu Azure.
2. Wybierz **sieci** , a następnie wybierz **brama aplikacji w** na liście duży.
3. Wprowadź wartości dla bramy aplikacji:

    - *myAppGateway* — nazwa bramy aplikacji.
    - *myResourceGroupAG* — dla nowej grupy zasobów.
    - Wybierz *WAF* dla warstwy bramy aplikacji.

    ![Utwórz nową bramę aplikacji](./media/application-gateway-web-application-firewall-portal/application-gateway-create.png)

4. Zaakceptuj wartości domyślne dla innych ustawień, a następnie kliknij przycisk **OK**.
5. Kliknij przycisk **wybierz sieć wirtualną**, kliknij przycisk **Utwórz nowy**, a następnie wprowadź wartości dla sieci wirtualnej:

    - *myVNet* — dla nazwy sieci wirtualnej.
    - *10.0.0.0/16* — do przestrzeni adresowej sieci wirtualnej.
    - *myAGSubnet* — dla nazwy podsieci.
    - *10.0.0.0/24* — do przestrzeni adresowej podsieci.

    ![Tworzenie sieci wirtualnej](./media/application-gateway-web-application-firewall-portal/application-gateway-vnet.png)

6. Kliknij przycisk **OK** do tworzenia sieci wirtualnej i podsieci.
7. Kliknij przycisk **wybierz publiczny adres IP**, kliknij przycisk **Utwórz nowy**, a następnie wprowadź nazwę publicznego adresu IP. W tym przykładzie publiczny adres IP o nazwie *myAGPublicIPAddress*. Zaakceptuj wartości domyślne dla innych ustawień, a następnie kliknij przycisk **OK**.
8. Zaakceptuj wartości domyślne w konfiguracji odbiornika, pozostaw zapory aplikacji sieci Web, które są wyłączone, a następnie kliknij **OK**.
9. Przejrzyj ustawienia na stronie Podsumowanie, a następnie kliknij przycisk **OK** tworzyć zasoby sieciowe i bramy aplikacji. Może upłynąć kilka minut dla bramy aplikacji można utworzyć, poczekaj na wdrożenie zakończy się pomyślnie przed przejściem do następnej sekcji.

### <a name="add-a-subnet"></a>Dodaj podsieć

1. Kliknij przycisk **wszystkie zasoby** w menu po lewej stronie, a następnie kliknij przycisk **myVNet** na liście zasobów.
2. Kliknij przycisk **podsieci**, a następnie kliknij przycisk **podsieci**.

    ![Tworzenie podsieci](./media/application-gateway-web-application-firewall-portal/application-gateway-subnet.png)

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

    ![Zainstaluj rozszerzenia niestandardowego](./media/application-gateway-web-application-firewall-portal/application-gateway-extension.png)

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

1. Kliknij przycisk **wszystkie zasoby**, a następnie kliknij przycisk **myAppGateway**.
2. Kliknij przycisk **pul zaplecza**. Domyślna pula został utworzony automatycznie z bramy aplikacji. Kliknij przycisk **appGateayBackendPool**.
3. Kliknij przycisk **docelowy Dodaj** dodawania każdej maszyny wirtualnej, który został utworzony do puli wewnętrznej bazy danych.

    ![Dodawanie serwerów wewnętrznej bazy danych](./media/application-gateway-web-application-firewall-portal/application-gateway-backend.png)

4. Kliknij pozycję **Zapisz**.

## <a name="create-a-storage-account-and-configure-diagnostics"></a>Utwórz konto magazynu i skonfigurować diagnostykę

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

W tym samouczku aplikacji przy użyciu brama konta magazynu do przechowywania danych na potrzeby wykrywania i zapobiegania. Analiza dzienników lub Centrum zdarzeń można użyć również do rejestrowania danych.

1. Kliknij przycisk **nowy** znaleziono w lewym górnym rogu portalu Azure.
2. Wybierz pozycję **Magazyn**, a następnie wybierz pozycję **Konto magazynu — obiekt blob, plik, tabela, kolejka**.
3. Wprowadź nazwę konta magazynu, wybierz opcję **Użyj istniejącego** dla grupy zasobów, a następnie wybierz **myResourceGroupAG**. W tym przykładzie nazwa konta magazynu jest *myagstore1*. Zaakceptuj wartości domyślne dla innych ustawień, a następnie kliknij przycisk **Utwórz**.

## <a name="configure-diagnostics"></a>Skonfiguruj diagnostyki

Skonfiguruj diagnostykę, aby zapisać dane w dzienniku ApplicationGatewayAccessLog, ApplicationGatewayPerformanceLog i ApplicationGatewayFirewallLog.

1. W menu po lewej stronie kliknij **wszystkie zasoby**, a następnie wybierz *myAppGateway*.
2. W obszarze monitorowania, kliknij przycisk **dzienników diagnostycznych**.
3. Kliknij przycisk **Dodaj ustawienie diagnostyczne**.
4. Wprowadź *myDiagnosticsSettings* jako nazwy ustawień diagnostycznych.
5. Wybierz **archiwum na konto magazynu**, a następnie kliknij przycisk **Konfiguruj** wybierz *myagstore1* konta magazynu, która została wcześniej utworzona.
6. Wybierz dzienniki bramy aplikacji do zbierania i zachowania.
7. Kliknij pozycję **Zapisz**.

    ![Skonfiguruj diagnostyki](./media/application-gateway-web-application-firewall-portal/application-gateway-diagnostics.png)

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

1. Znajdź publicznego adresu IP dla bramy aplikacji na ekran Przegląd. Kliknij przycisk **wszystkie zasoby** , a następnie kliknij przycisk **myAGPublicIPAddress**.

    ![Zarejestruj publiczny adres IP bramy aplikacji](./media/application-gateway-web-application-firewall-portal/application-gateway-record-ag-address.png)

2. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki.

    ![Brama aplikacji w testu](./media/application-gateway-web-application-firewall-portal/application-gateway-iistest.png)

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono sposób:

> [!div class="checklist"]
> * Utwórz bramę aplikacji z zapory aplikacji sieci Web jest włączona
> * Tworzenie maszyn wirtualnych, używane jako serwery wewnętrznej bazy danych
> * Utwórz konto magazynu i skonfigurować diagnostykę

Aby dowiedzieć się więcej na temat bram aplikacji i ich skojarzonych zasobów, nadal artykuły.