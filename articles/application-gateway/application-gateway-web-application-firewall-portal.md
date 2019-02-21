---
title: Tworzenie bramy aplikacji przy użyciu zapory aplikacji sieci web — witryna Azure portal | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć bramę aplikacji z zaporą aplikacji internetowych przy użyciu witryny Azure portal.
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
ms.openlocfilehash: 7a090a068984a71c917cf5c33761dd78ac1ad2c8
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2019
ms.locfileid: "56453260"
---
# <a name="create-an-application-gateway-with-a-web-application-firewall-using-the-azure-portal"></a>Tworzenie bramy aplikacji z zaporą aplikacji sieci web przy użyciu witryny Azure portal

Można użyć witryny Azure portal, aby utworzyć [bramy application gateway](application-gateway-introduction.md) z [zapory aplikacji sieci web](application-gateway-web-application-firewall-overview.md) (WAF). Zapora aplikacji internetowych chroni Twoje aplikacje, używając reguł [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project). Reguły te obejmują ochronę przed atakami, takimi jak iniekcja kodu SQL, działanie skryptów między witrynami i porywanie sesji.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Tworzenie bramy aplikacji z włączoną zaporą aplikacji internetowych
> * Tworzenie maszyn wirtualnych, używane jako serwery zaplecza
> * Tworzenie konta magazynu i konfigurowanie diagnostyki

![Przykład zapory aplikacji internetowych](./media/application-gateway-web-application-firewall-portal/scenario-waf.png)

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com)

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

Sieć wirtualna jest potrzebna do komunikacji między utworzonymi zasobami. W tym przykładzie są tworzone dwie podsieci: jedna dla bramy aplikacji i druga dla serwerów zaplecza. Sieć wirtualną można utworzyć podczas tworzenia bramy aplikacji.

1. Kliknij przycisk **New** w lewym górnym rogu witryny Azure portal.
2. Wybierz pozycję **Sieć**, a następnie z listy Polecane wybierz pozycję **Application Gateway**.
3. Wprowadź następujące wartości dla bramy aplikacji:

    - *myAppGateway* — jako nazwę bramy aplikacji.
    - *myResourceGroupAG* — jako nową grupę zasobów.
    - Wybierz *zapory aplikacji sieci Web* dla warstwy usługi application gateway.

    ![Tworzenie nowej bramy aplikacji](./media/application-gateway-web-application-firewall-portal/application-gateway-create.png)

4. Zaakceptuj wartości domyślne dla innych ustawień, a następnie kliknij przycisk **OK**.
5. Kliknij kolejno pozycje **Wybierz sieć wirtualną**, **Utwórz nową**, a następnie wprowadź następujące wartości dla sieci wirtualnej:

    - *myVNet* — jako nazwę sieci wirtualnej.
    - *10.0.0.0/16* — jako przestrzeń adresową sieci wirtualnej.
    - *myAGSubnet* — jako nazwę podsieci.
    - *10.0.0.0/24* — jako przestrzeń adresową podsieci.

    ![Tworzenie sieci wirtualnej](./media/application-gateway-web-application-firewall-portal/application-gateway-vnet.png)

6. Kliknij przycisk **OK**, aby utworzyć sieć wirtualną i podsieć.
7. Kliknij przycisk **wybierz publiczny adres IP**, kliknij przycisk **Utwórz nową**, a następnie wprowadź nazwę publicznego adresu IP. W tym przykładzie publiczny adres IP nosi nazwę *myAGPublicIPAddress*. Zaakceptuj wartości domyślne dla innych ustawień, a następnie kliknij przycisk **OK**.
8. Zaakceptuj wartości domyślne dla konfiguracji odbiornika, pozostaw zapory aplikacji sieci Web, które są wyłączone, a następnie kliknij przycisk **OK**.
9. Przejrzyj ustawienia na stronie podsumowania, a następnie kliknij przycisk **OK** do utworzenia bramy aplikacji i zasobów sieciowych. Może upłynąć kilka minut w usłudze application gateway można utworzyć, poczekaj na wdrożenie zakończy się pomyślnie przed przejściem do następnej sekcji.

### <a name="add-a-subnet"></a>Dodawanie podsieci

1. W menu po lewej stronie kliknij pozycję **Wszystkie zasoby**, a następnie na liście zasobów kliknij pozycję **myVNet**.
2. Kliknij przycisk **podsieci**, a następnie kliknij przycisk **podsieci**.

    ![Tworzenie podsieci](./media/application-gateway-web-application-firewall-portal/application-gateway-subnet.png)

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

    ![Instalowanie rozszerzenia niestandardowego](./media/application-gateway-web-application-firewall-portal/application-gateway-extension.png)

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

1. Kliknij pozycję **Wszystkie zasoby**, a następnie kliknij pozycję **myAppGateway**.
2. Kliknij pozycję **Pule zaplecza**. Domyślna pula została utworzona automatycznie podczas tworzenia bramy aplikacji. Kliknij pozycję **appGatewayBackendPool**.
3. Kliknij przycisk **Add target** dodawania każdej maszyny wirtualnej, który został utworzony do puli zaplecza.

    ![Dodawanie serwerów zaplecza](./media/application-gateway-web-application-firewall-portal/application-gateway-backend.png)

4. Kliknij pozycję **Zapisz**.

## <a name="create-a-storage-account-and-configure-diagnostics"></a>Tworzenie konta magazynu i konfigurowanie diagnostyki

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

W tym samouczku brama aplikacji używa konta magazynu do przechowywania danych na potrzeby wykrywania i zapobiegania. Dzienniki usługi Azure Monitor lub Centrum zdarzeń można również użyć do rejestrowania danych.

1. Kliknij przycisk **New** w lewym górnym rogu witryny Azure portal.
2. Wybierz pozycję **Magazyn**, a następnie wybierz pozycję **Konto magazynu — obiekt blob, plik, tabela, kolejka**.
3. Wprowadź nazwę konta magazynu, wybierz **Użyj istniejącej** dla grupy zasobów, a następnie wybierz **myResourceGroupAG**. W tym przykładzie nazwa konta magazynu jest *myagstore1*. Zaakceptuj wartości domyślne dla pozostałych ustawień, a następnie kliknij przycisk **Utwórz**.

## <a name="configure-diagnostics"></a>Konfigurowanie diagnostyki

Skonfiguruj diagnostykę do rejestrowania danych w dziennikach ApplicationGatewayAccessLog, ApplicationGatewayPerformanceLog i ApplicationGatewayFirewallLog.

1. W menu po lewej stronie kliknij **wszystkie zasoby**, a następnie wybierz pozycję *myAppGateway*.
2. W obszarze monitorowanie, kliknij przycisk **dzienniki diagnostyczne**.
3. Kliknij przycisk **Dodaj ustawienia diagnostyki**.
4. Wprowadź *myDiagnosticsSettings* jako nazwę ustawień diagnostyki.
5. Wybierz **Zarchiwizuj na koncie magazynu**, a następnie kliknij przycisk **Konfiguruj** wybrać *myagstore1* konta magazynu, która została wcześniej utworzona.
6. Wybierz dzienniki bramy aplikacji, aby zbierać i przechowywać.
7. Kliknij pozycję **Zapisz**.

    ![Konfigurowanie diagnostyki](./media/application-gateway-web-application-firewall-portal/application-gateway-diagnostics.png)

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

1. Na ekranie Przegląd znajdź publiczny adres IP bramy aplikacji. Kliknij pozycję **Wszystkie zasoby**, a następnie kliknij pozycję **myAGPublicIPAddress**.

    ![Rejestrowanie publicznego adresu IP bramy aplikacji](./media/application-gateway-web-application-firewall-portal/application-gateway-record-ag-address.png)

2. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki.

    ![Testowanie bramy aplikacji](./media/application-gateway-web-application-firewall-portal/application-gateway-iistest.png)

## <a name="next-steps"></a>Kolejne kroki

W tym artykule zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie bramy aplikacji z włączoną zaporą aplikacji internetowych
> * Tworzenie maszyn wirtualnych, używane jako serwery zaplecza
> * Tworzenie konta magazynu i konfigurowanie diagnostyki

Aby dowiedzieć się więcej na temat bramy aplikacji i skojarzonych z nimi zasobów, przejdź do artykuły z poradami.