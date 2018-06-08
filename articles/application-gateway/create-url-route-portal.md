---
title: Utwórz bramę aplikacji z adresu URL na podstawie ścieżki reguły routingu - portalu Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć adres URL na podstawie ścieżki reguły routingu dla bramy aplikacji i skalowania maszyny wirtualnej ustawić za pomocą portalu Azure.
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
ms.openlocfilehash: 6b45b00de53822224afbfb3a15dbc6790deb11ce
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "34356345"
---
# <a name="create-an-application-gateway-with-path-based-routing-rules-using-the-azure-portal"></a>Utwórz bramę aplikacji przy użyciu ścieżki na podstawie reguł routingu przy użyciu portalu Azure

Azure portal umożliwiają skonfigurowanie [reguł routingu na podstawie ścieżki adresu URL](url-route-overview.md) podczas tworzenia [brama aplikacji w](overview.md). W tym samouczku utworzysz pul zaplecza przy użyciu maszyn wirtualnych. Następnie można utworzyć reguły routingu, które upewnij się, że ruch w sieci web dociera do odpowiednich serwerów w pulach.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Tworzenie bramy aplikacji
> * Tworzenie maszyn wirtualnych dla serwerów wewnętrznej bazy danych
> * Tworzenie puli wewnętrznej bazy danych z serwerami wewnętrznej bazy danych
> * Utwórz odbiornik wewnętrznej bazy danych
> * Utwórz regułę routingu na podstawie ścieżki

![Przykład routingu adresów URL](./media/create-url-route-portal/scenario.png)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do witryny Azure Portal pod adresem [http://portal.azure.com](http://portal.azure.com)

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

Sieć wirtualna jest wymagany dla komunikacji między zasobami, które można utworzyć. W tym przykładzie są tworzone dwie podsieci: jedna dla bramy aplikacji i druga dla serwerów zaplecza. Sieć wirtualną można utworzyć podczas tworzenia bramy aplikacji.

1. Kliknij przycisk **nowy** znaleziono w lewym górnym rogu portalu Azure.
2. Wybierz pozycję **Sieć**, a następnie z listy Polecane wybierz pozycję **Application Gateway**.
3. Wprowadź następujące wartości dla bramy aplikacji:

    - *myAppGateway* — jako nazwę bramy aplikacji.
    - *myResourceGroupAG* — jako nową grupę zasobów.

    ![Tworzenie nowej bramy aplikacji](./media/create-url-route-portal/application-gateway-create.png)

4. Zaakceptuj wartości domyślne dla innych ustawień, a następnie kliknij przycisk **OK**.
5. Kliknij przycisk **wybierz sieć wirtualną**, kliknij przycisk **Utwórz nowy**, a następnie wprowadź wartości dla sieci wirtualnej:

    - *myVNet* — jako nazwę sieci wirtualnej.
    - *10.0.0.0/16* — jako przestrzeń adresową sieci wirtualnej.
    - *myAGSubnet* — jako nazwę podsieci.
    - *10.0.0.0/24* — jako przestrzeń adresową podsieci.

    ![Tworzenie sieci wirtualnej](./media/create-url-route-portal/application-gateway-vnet.png)

6. Kliknij przycisk **OK**, aby utworzyć sieć wirtualną i podsieć.
7. Kliknij przycisk **wybierz publiczny adres IP**, kliknij przycisk **Utwórz nowy**, a następnie wprowadź nazwę publicznego adresu IP. W tym przykładzie publiczny adres IP nosi nazwę *myAGPublicIPAddress*. Zaakceptuj wartości domyślne dla innych ustawień, a następnie kliknij przycisk **OK**.
8. Zaakceptuj wartości domyślne w konfiguracji odbiornika, pozostaw zapory aplikacji sieci Web, które są wyłączone, a następnie kliknij **OK**.
9. Przejrzyj ustawienia na stronie Podsumowanie, a następnie kliknij przycisk **OK** tworzyć zasoby sieciowe i bramy aplikacji. Może upłynąć kilka minut dla bramy aplikacji można utworzyć, poczekaj na wdrożenie zakończy się pomyślnie przed przejściem do następnej sekcji.

### <a name="add-a-subnet"></a>Dodawanie podsieci

1. W menu po lewej stronie kliknij pozycję **Wszystkie zasoby**, a następnie na liście zasobów kliknij pozycję **myVNet**.
2. Kliknij przycisk **podsieci**, a następnie kliknij przycisk **podsieci**.

    ![Tworzenie podsieci](./media/create-url-route-portal/application-gateway-subnet.png)

3. Wprowadź *myBackendSubnet* jako nazwę podsieci, a następnie kliknij przycisk **OK**.

## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

W tym przykładzie należy utworzyć trzy maszyny wirtualne do użycia jako serwery zaplecza bramy aplikacji. Zainstaluj również usługi IIS na maszynach wirtualnych, aby sprawdzić, czy brama aplikacji została pomyślnie utworzona.

1. Kliknij przycisk **Nowy**.
2. Kliknij przycisk **obliczeniowe** , a następnie wybierz **systemu Windows Server 2016 Datacenter** na liście duży.
3. Wprowadź poniższe wartości dla maszyny wirtualnej:

    - *myVM1* — jako nazwę maszyny wirtualnej.
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

    ![Instalowanie rozszerzenia niestandardowego](./media/create-url-route-portal/application-gateway-extension.png)

2. Uruchom następujące polecenie, aby zainstalować usługi IIS na maszynie wirtualnej: 

    ```azurepowershell-interactive
    $publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/davidmu1/samplescripts/master/appgatewayurl.ps1");  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
    Set-AzureRmVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -Location eastus `
      -ExtensionName IIS `
      -VMName myVM1 `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -Settings $publicSettings
    ```

3. Utwórz dwie maszyny wirtualne i zainstaluj usługi IIS przy użyciu kroków, które właśnie zostało zakończone. Wprowadź nazwy *myVM2* i *myVM3* dla nazwy i wartości VMName w AzureRmVMExtension zestawu.

## <a name="create-backend-pools-with-the-virtual-machines"></a>Tworzenie puli wewnętrznej bazy danych z maszynami wirtualnymi

1. Kliknij przycisk **wszystkie zasoby** , a następnie kliknij przycisk **myAppGateway**.
2. Kliknij pozycję **Pule zaplecza**. Domyślna pula została utworzona automatycznie podczas tworzenia bramy aplikacji. Kliknij przycisk **appGateayBackendPool**.
3. Kliknij przycisk **docelowy Dodaj** można dodać *myVM1* do appGatewayBackendPool.

    ![Dodawanie serwerów zaplecza](./media/create-url-route-portal/application-gateway-backend.png)

4. Kliknij pozycję **Zapisz**.
5. Kliknij przycisk **pul zaplecza** , a następnie kliknij przycisk **Dodaj**.
6. Wprowadź nazwę *imagesBackendPool* i Dodaj *myVM2* przy użyciu **docelowy Dodaj**.
7. Kliknij przycisk **OK**.
8. Kliknij przycisk **Dodaj** ponownie, aby dodać innej puli wewnętrznej bazy danych o nazwie *videoBackendPool* i Dodaj *myVM3* do niego.

## <a name="create-a-backend-listener"></a>Utwórz odbiornik wewnętrznej bazy danych

1. Kliknij przycisk **odbiorników** , a następnie kliknij pozycję **podstawowe**.
2. Wprowadź *myBackendListener* nazwę *myFrontendPort* dla nazwy portu frontonu, a następnie *8080* jako numer portu odbiornika.
3. Kliknij przycisk **OK**.

## <a name="create-a-path-based-routing-rule"></a>Utwórz regułę routingu na podstawie ścieżki

1. Kliknij przycisk **reguły** , a następnie kliknij przycisk **na podstawie ścieżki**.
2. Wprowadź *rule2* dla nazwy.
3. Wprowadź *obrazów* dla nazwy pierwszej ścieżki. Wprowadź */images/** dla ścieżki. Wybierz **imagesBackendPool** puli wewnętrznej bazy danych.
4. Wprowadź *wideo* nazwę drugiego ścieżki. Wprowadź */video/** dla ścieżki. Wybierz **videoBackendPool** puli wewnętrznej bazy danych.

    ![Tworzenie reguły na podstawie ścieżki](./media/create-url-route-portal/application-gateway-route-rule.png)

5. Kliknij przycisk **OK**.

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

1. Kliknij przycisk **wszystkie zasoby**, a następnie kliknij przycisk **myAGPublicIPAddress**.

    ![Rejestrowanie publicznego adresu IP bramy aplikacji](./media/create-url-route-portal/application-gateway-record-ag-address.png)

2. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki. Na przykład http://http://40.121.222.19.

    ![Testowanie podstawowego adresu URL w bramie aplikacji](./media/create-url-route-portal/application-gateway-iistest.png)

3. Zmień adres URL do http://&lt;adres ip&gt;: 8080/video/test.htm, zastępując &lt;adres ip&gt; z IP adresów, a powinien zostać wyświetlony ekran podobny do następującego:

    ![Testowanie adresu URL obrazów w bramie aplikacji](./media/create-url-route-portal/application-gateway-iistest-images.png)

4. Zmień adres URL do http://&lt;adres ip&gt;: 8080/video/test.htm, zastępując &lt;adres ip&gt; z IP adresów, a powinien zostać wyświetlony ekran podobny do następującego:

    ![Testowanie adresu URL wideo w bramie aplikacji](./media/create-url-route-portal/application-gateway-iistest-video.png)

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono sposób

> [!div class="checklist"]
> * Tworzenie bramy aplikacji
> * Tworzenie maszyn wirtualnych dla serwerów wewnętrznej bazy danych
> * Tworzenie puli wewnętrznej bazy danych z serwerami wewnętrznej bazy danych
> * Utwórz odbiornik wewnętrznej bazy danych
> * Utwórz regułę routingu na podstawie ścieżki

Aby dowiedzieć się więcej na temat bram aplikacji i ich skojarzonych zasobów, nadal artykuły.