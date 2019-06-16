---
title: Tworzenie bramy aplikacji za pomocą opartego na ścieżkach reguł routingu adresów URL — witryna Azure portal
description: Dowiedz się, jak utworzyć oparty na ścieżkach reguł routingu adresów URL dla bramy aplikacji i maszyn wirtualnych zestawu skalowania przy użyciu witryny Azure portal.
services: application-gateway
author: vhorne
manager: jpconnock
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 3/26/2018
ms.author: victorh
ms.openlocfilehash: 10bc4e4c440e5495afd820f588270b7990108b68
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66135311"
---
# <a name="create-an-application-gateway-with-path-based-routing-rules-using-the-azure-portal"></a>Tworzenie bramy aplikacji przy użyciu opartego na ścieżkach reguł routingu, za pomocą witryny Azure portal

Witryna Azure portal służy do konfigurowania [reguł routingu opartego na ścieżkach URL](application-gateway-url-route-overview.md) po utworzeniu [bramy application gateway](application-gateway-introduction.md). W tym samouczku utworzysz pule zaplecza przy użyciu maszyn wirtualnych. Następnie można utworzyć reguły routingu, które upewnij się, że ruch w sieci web dociera do odpowiednich serwerów w puli.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Tworzenie bramy aplikacji
> * Tworzenie maszyn wirtualnych dla serwerów wewnętrznej bazy danych
> * Utwórz pule zaplecza z serwerami wewnętrznej bazy danych
> * Utwórz odbiornik wewnętrznej bazy danych
> * Utwórz regułę routingu opartego na ścieżkach

![Przykład routingu adresów URL](./media/application-gateway-create-url-route-portal/scenario.png)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com)

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

Sieć wirtualna jest potrzebna do komunikacji między utworzonymi zasobami. W tym przykładzie są tworzone dwie podsieci: jedna dla bramy aplikacji i druga dla serwerów zaplecza. Sieć wirtualną można utworzyć podczas tworzenia bramy aplikacji.

1. Kliknij przycisk **New** w lewym górnym rogu witryny Azure portal.
2. Wybierz pozycję **Sieć**, a następnie z listy Polecane wybierz pozycję **Application Gateway**.
3. Wprowadź następujące wartości dla bramy aplikacji:

   - *myAppGateway* — jako nazwę bramy aplikacji.
   - *myResourceGroupAG* — jako nową grupę zasobów.

     ![Tworzenie nowej bramy aplikacji](./media/application-gateway-create-url-route-portal/application-gateway-create.png)

4. Zaakceptuj wartości domyślne dla innych ustawień, a następnie kliknij przycisk **OK**.
5. Kliknij kolejno pozycje **Wybierz sieć wirtualną**, **Utwórz nową**, a następnie wprowadź następujące wartości dla sieci wirtualnej:

   - *myVNet* — jako nazwę sieci wirtualnej.
   - *10.0.0.0/16* — jako przestrzeń adresową sieci wirtualnej.
   - *myAGSubnet* — jako nazwę podsieci.
   - *10.0.0.0/24* — jako przestrzeń adresową podsieci.

     ![Tworzenie sieci wirtualnej](./media/application-gateway-create-url-route-portal/application-gateway-vnet.png)

6. Kliknij przycisk **OK**, aby utworzyć sieć wirtualną i podsieć.
7. Kliknij przycisk **wybierz publiczny adres IP**, kliknij przycisk **Utwórz nową**, a następnie wprowadź nazwę publicznego adresu IP. W tym przykładzie publiczny adres IP nosi nazwę *myAGPublicIPAddress*. Zaakceptuj wartości domyślne dla innych ustawień, a następnie kliknij przycisk **OK**.
8. Zaakceptuj wartości domyślne dla konfiguracji odbiornika, pozostaw zapory aplikacji sieci Web, które są wyłączone, a następnie kliknij przycisk **OK**.
9. Przejrzyj ustawienia na stronie podsumowania, a następnie kliknij przycisk **OK** do tworzenia zasobów sieciowych i bramy aplikacji. Może upłynąć kilka minut w usłudze application gateway można utworzyć, poczekaj na wdrożenie zakończy się pomyślnie przed przejściem do następnej sekcji.

### <a name="add-a-subnet"></a>Dodawanie podsieci

1. W menu po lewej stronie kliknij pozycję **Wszystkie zasoby**, a następnie na liście zasobów kliknij pozycję **myVNet**.
2. Kliknij przycisk **podsieci**, a następnie kliknij przycisk **podsieci**.

    ![Tworzenie podsieci](./media/application-gateway-create-url-route-portal/application-gateway-subnet.png)

3. Wprowadź *myBackendSubnet* jako nazwę podsieci, a następnie kliknij przycisk **OK**.

## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

W tym przykładzie utworzysz trzy maszyny wirtualne do użycia jako serwery zaplecza w usłudze application gateway. Zainstaluj również usługi IIS na maszynach wirtualnych, aby sprawdzić, czy brama aplikacji została pomyślnie utworzona.

1. Kliknij przycisk **Nowy**.
2. Kliknij pozycję **Compute** (Wystąpienia obliczeniowe), a następnie z listy Polecane wybierz pozycję **Windows Server 2016 Datacenter**.
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

    ![Instalowanie rozszerzenia niestandardowego](./media/application-gateway-create-url-route-portal/application-gateway-extension.png)

2. Uruchom następujące polecenie, aby zainstalować usługi IIS na maszynie wirtualnej: 

    ```azurepowershell-interactive
    $publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1");  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -Location eastus `
      -ExtensionName IIS `
      -VMName myVM1 `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -Settings $publicSettings
    ```

3. Utwórz dwie maszyny wirtualne z więcej i zainstalować usługi IIS, wykonując kroki, które właśnie zostało zakończone. Wprowadź nazwy *myVM2* i *myVM3* dla nazwy i wartości VMName w AzVMExtension zestawu.

## <a name="create-backend-pools-with-the-virtual-machines"></a>Tworzenie puli wewnętrznej bazy danych z maszynami wirtualnymi

1. Kliknij przycisk **wszystkie zasoby** a następnie kliknij przycisk **myAppGateway**.
2. Kliknij pozycję **Pule zaplecza**. Domyślna pula została utworzona automatycznie podczas tworzenia bramy aplikacji. Kliknij pozycję **appGatewayBackendPool**.
3. Kliknij przycisk **Add target** dodać *myVM1* do appGatewayBackendPool.

    ![Dodawanie serwerów zaplecza](./media/application-gateway-create-url-route-portal/application-gateway-backend.png)

4. Kliknij pozycję **Zapisz**.
5. Kliknij przycisk **pule zaplecza** a następnie kliknij przycisk **Dodaj**.
6. Wprowadź nazwę *imagesBackendPool* i Dodaj *myVM2* przy użyciu **Add target**.
7. Kliknij przycisk **OK**.
8. Kliknij przycisk **Dodaj** ponownie, aby dodać innej puli zaplecza o nazwie *videoBackendPool* i Dodaj *myVM3* do niego.

## <a name="create-a-backend-listener"></a>Utwórz odbiornik wewnętrznej bazy danych

1. Kliknij przycisk **odbiorników** i kliknięcie **podstawowe**.
2. Wprowadź *myBackendListener* dla nazwy *myFrontendPort* nazwy port frontonu, a następnie *8080* jako port odbiornika.
3. Kliknij przycisk **OK**.

## <a name="create-a-path-based-routing-rule"></a>Utwórz regułę routingu opartego na ścieżkach

1. Kliknij przycisk **reguły** a następnie kliknij przycisk **opartego na ścieżkach**.
2. Wprowadź *reguły rule2* dla nazwy.
3. Wprowadź *obrazów* nazwy pierwszej ścieżki. Wprowadź */images/* \* dla ścieżki. Wybierz **imagesBackendPool** dla puli zaplecza.
4. Wprowadź *wideo* jako nazwę drugiej ścieżki. Wprowadź */video/* \* dla ścieżki. Wybierz **videoBackendPool** dla puli zaplecza.

    ![Utwórz regułę bazującą na ścieżce](./media/application-gateway-create-url-route-portal/application-gateway-route-rule.png)

5. Kliknij przycisk **OK**.

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

1. Kliknij przycisk **wszystkie zasoby**, a następnie kliknij przycisk **myAGPublicIPAddress**.

    ![Rejestrowanie publicznego adresu IP bramy aplikacji](./media/application-gateway-create-url-route-portal/application-gateway-record-ag-address.png)

2. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki. Takich jak http:\//40.121.222.19.

    ![Testowanie podstawowego adresu URL w bramie aplikacji](./media/application-gateway-create-url-route-portal/application-gateway-iistest.png)

3. Zmień adres URL na http://&lt;adresu ip&gt;: 8080/images/test.htm, zastępując &lt;adresu ip&gt; za pomocą Twojego adresu IP adres i powinny zostać wyświetlone informacje jak w poniższym przykładzie:

    ![Testowanie adresu URL obrazów w bramie aplikacji](./media/application-gateway-create-url-route-portal/application-gateway-iistest-images.png)

4. Zmień adres URL na http://&lt;adresu ip&gt;: 8080/video/test.htm, zastępując &lt;adresu ip&gt; za pomocą Twojego adresu IP adres i powinny zostać wyświetlone informacje jak w poniższym przykładzie:

    ![Testowanie adresu URL wideo w bramie aplikacji](./media/application-gateway-create-url-route-portal/application-gateway-iistest-video.png)

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono sposób

> [!div class="checklist"]
> * Tworzenie bramy aplikacji
> * Tworzenie maszyn wirtualnych dla serwerów wewnętrznej bazy danych
> * Utwórz pule zaplecza z serwerami wewnętrznej bazy danych
> * Utwórz odbiornik wewnętrznej bazy danych
> * Utwórz regułę routingu opartego na ścieżkach

Aby dowiedzieć się więcej na temat bramy aplikacji i skojarzonych z nimi zasobów, przejdź do artykuły z poradami.
