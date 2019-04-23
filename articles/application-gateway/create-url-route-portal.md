---
title: Samouczek — Tworzenie bramy aplikacji za pomocą opartego na ścieżkach reguł routingu adresów URL — witryna Azure portal
description: W tym samouczku dowiesz się, jak utworzyć oparty na ścieżkach reguł routingu adresów URL dla bramy aplikacji i maszyn wirtualnych zestawu skalowania przy użyciu witryny Azure portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 4/18/2019
ms.author: victorh
ms.openlocfilehash: 5307f7674635fd33241e1faba9bb0b7c0432d10b
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60001035"
---
# <a name="tutorial-create-an-application-gateway-with-path-based-routing-rules-using-the-azure-portal"></a>Samouczek: Tworzenie bramy aplikacji przy użyciu opartego na ścieżkach reguł routingu, za pomocą witryny Azure portal

Witryna Azure portal służy do konfigurowania [reguł routingu opartego na ścieżkach URL](url-route-overview.md) po utworzeniu [bramy application gateway](overview.md). W tym samouczku utworzysz pule zaplecza przy użyciu maszyn wirtualnych. Następnie można utworzyć reguły routingu, które upewnij się, że ruch w sieci web dociera do odpowiednich serwerów w puli.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie bramy aplikacji
> * Tworzenie maszyn wirtualnych dla serwerów wewnętrznej bazy danych
> * Utwórz pule zaplecza z serwerami wewnętrznej bazy danych
> * Utwórz odbiornik wewnętrznej bazy danych
> * Utwórz regułę routingu opartego na ścieżkach

![Przykład routingu adresów URL](./media/create-url-route-portal/scenario.png)

Jeśli wolisz, możesz wykonać ten samouczek przy użyciu [wiersza polecenia platformy Azure](tutorial-url-route-cli.md) lub [programu Azure PowerShell](tutorial-url-route-powershell.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

Sieć wirtualna jest wymagane do komunikacji między zasobami, które można utworzyć. W tym przykładzie są tworzone dwie podsieci: jedna dla bramy aplikacji i druga dla serwerów zaplecza. Można utworzyć sieci wirtualnej, w tym samym czasie utworzenia bramy aplikacji.

1. Wybierz **New** w lewym górnym rogu witryny Azure portal.
2. Wybierz pozycję **Sieć**, a następnie z listy Polecane wybierz pozycję **Application Gateway**.
3. Wprowadź następujące wartości dla bramy aplikacji:

   - *myAppGateway* — jako nazwę bramy aplikacji.
   - *myResourceGroupAG* — jako nową grupę zasobów.

     ![Tworzenie nowej bramy aplikacji](./media/create-url-route-portal/application-gateway-create.png)

4. Zaakceptuj wartości domyślne innych ustawień, a następnie kliknij przycisk **OK**.
5. Wybierz **wybierz sieć wirtualną**, wybierz opcję **Utwórz nową**, a następnie wprowadź następujące wartości dla sieci wirtualnej:

   - *myVNet* — jako nazwę sieci wirtualnej.
   - *10.0.0.0/16* — jako przestrzeń adresową sieci wirtualnej.
   - *myAGSubnet* — jako nazwę podsieci.
   - *10.0.0.0/24* — jako przestrzeń adresową podsieci.

     ![Tworzenie sieci wirtualnej](./media/create-url-route-portal/application-gateway-vnet.png)

6. Wybierz **OK** utworzyć sieć wirtualną i podsieć.
7. Wybierz **wybierz publiczny adres IP**, wybierz opcję **Utwórz nową**, a następnie wprowadź nazwę publicznego adresu IP. W tym przykładzie publiczny adres IP nosi nazwę *myAGPublicIPAddress*. Zaakceptuj wartości domyślne innych ustawień, a następnie kliknij przycisk **OK**.
8. Zaakceptuj wartości domyślne dla konfiguracji odbiornika, pozostaw zapory aplikacji sieci Web, które są wyłączone, a następnie wybierz **OK**.
9. Przejrzyj ustawienia na stronie podsumowania, a następnie wybierz **OK** do tworzenia zasobów sieciowych i bramy aplikacji. Może upłynąć kilka minut w usłudze application gateway można utworzyć, poczekaj na wdrożenie zakończy się pomyślnie przed przejściem do następnej sekcji.

### <a name="add-a-subnet"></a>Dodawanie podsieci

1. Wybierz **wszystkie zasoby** w menu po lewej stronie, a następnie wybierz **myVNet** na liście zasobów.
2. Wybierz **podsieci**, a następnie wybierz pozycję **podsieci**.

    ![Tworzenie podsieci](./media/create-url-route-portal/application-gateway-subnet.png)

3. Wprowadź *myBackendSubnet* dla nazwy podsieci, a następnie wybierz **OK**.

## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

W tym przykładzie utworzysz trzy maszyny wirtualne do użycia jako serwery zaplecza w usłudze application gateway. Możesz także zainstalować usługi IIS na maszynach wirtualnych, aby sprawdzić, czy brama aplikacji został pomyślnie utworzony.

1. Wybierz pozycję **Nowy**.
2. Wybierz pozycję **Compute**, a następnie z listy Polecane wybierz pozycję **Windows Server 2016 Datacenter**.
3. Wprowadź poniższe wartości dla maszyny wirtualnej:

    - *myVM1* — jako nazwę maszyny wirtualnej.
    - *azureuser* — jako nazwę użytkownika administratora.
    - *Azure123456!* jako hasło.
    - Wybierz pozycję **Użyj istniejącej** i wybierz grupę *myResourceGroupAG*.

4. Kliknij przycisk **OK**.
5. Wybierz **DS1_V2** dla rozmiaru maszyny wirtualnej, a następnie wybierz pozycję **wybierz**.
6. Upewnij się, że wybrano sieć wirtualną **myVNet** i podsieć **myBackendSubnet**. 
7. Kliknij pozycję **Wyłączone**, aby wyłączyć diagnostykę rozruchu.
8. Kliknij przycisk **OK**, przejrzyj ustawienia na stronie podsumowania, a następnie wybierz pozycję **Utwórz**.

### <a name="install-iis"></a>Instalowanie usług IIS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Otwórz interaktywnej powłoki i upewnij się, jest równa **PowerShell**.

    ![Instalowanie rozszerzenia niestandardowego](./media/create-url-route-portal/application-gateway-extension.png)

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

1. Wybierz **wszystkie zasoby** , a następnie wybierz **myAppGateway**.
2. Wybierz **pule zaplecza**. Domyślna pula została utworzona automatycznie podczas tworzenia bramy aplikacji. Wybierz pozycję **appGatewayBackendPool**.
3. Wybierz **Add target** dodać *myVM1* do appGatewayBackendPool.

    ![Dodawanie serwerów zaplecza](./media/create-url-route-portal/application-gateway-backend.png)

4. Wybierz pozycję **Zapisz**.
5. Wybierz **pule zaplecza** , a następnie wybierz **Dodaj**.
6. Wprowadź nazwę *imagesBackendPool* i Dodaj *myVM2* przy użyciu **Add target**.
7. Kliknij przycisk **OK**.
8. Wybierz **Dodaj** ponownie, aby dodać innej puli zaplecza o nazwie *videoBackendPool* i Dodaj *myVM3* do niego.

## <a name="create-a-backend-listener"></a>Utwórz odbiornik wewnętrznej bazy danych

1. Wybierz **odbiorników** a następnie wybierz **podstawowe**.
2. Wprowadź *myBackendListener* dla nazwy *myFrontendPort* nazwy port frontonu, a następnie *8080* jako port odbiornika.
3. Kliknij przycisk **OK**.

## <a name="create-a-path-based-routing-rule"></a>Utwórz regułę routingu opartego na ścieżkach

1. Wybierz **reguły** , a następnie wybierz **opartego na ścieżkach**.
2. Wprowadź *reguły rule2* dla nazwy.
3. Wprowadź *obrazów* nazwy pierwszej ścieżki. Wprowadź */images/* \* dla ścieżki. Wybierz **imagesBackendPool** dla puli zaplecza.
4. Wprowadź *wideo* jako nazwę drugiej ścieżki. Wprowadź */video/* \* dla ścieżki. Wybierz **videoBackendPool** dla puli zaplecza.

    ![Utwórz regułę bazującą na ścieżce](./media/create-url-route-portal/application-gateway-route-rule.png)

5. Kliknij przycisk **OK**.

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

1. Wybierz **wszystkie zasoby**, a następnie wybierz pozycję **myAGPublicIPAddress**.

    ![Rejestrowanie publicznego adresu IP bramy aplikacji](./media/create-url-route-portal/application-gateway-record-ag-address.png)

2. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki. Na przykład http://40.121.222.19.

    ![Testowanie podstawowego adresu URL w bramie aplikacji](./media/create-url-route-portal/application-gateway-iistest.png)

3. Zmień adres URL na http://&lt;adresu ip&gt;: 8080/images/test.htm, zastępując &lt;adresu ip&gt; za pomocą Twojego adresu IP adres i powinny zostać wyświetlone informacje jak w poniższym przykładzie:

    ![Testowanie adresu URL obrazów w bramie aplikacji](./media/create-url-route-portal/application-gateway-iistest-images.png)

4. Zmień adres URL na http://&lt;adresu ip&gt;: 8080/video/test.htm, zastępując &lt;adresu ip&gt; za pomocą Twojego adresu IP adres i powinny zostać wyświetlone informacje jak w poniższym przykładzie:

    ![Testowanie adresu URL wideo w bramie aplikacji](./media/create-url-route-portal/application-gateway-iistest-video.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie potrzebujesz już zasobów utworzonych za pomocą bramy aplikacji, usuń grupę zasobów. Usuwając grupę zasobów, usuwasz również bramę aplikacji i wszystkie powiązane z nią zasoby. 

Aby usunąć grupę zasobów:

1. W menu po lewej stronie witryny Azure Portal wybierz pozycję **Grupy zasobów**.
2. Na stronie **Grupy zasobów** wyszukaj pozycję **myResourceGroupAG** na liście i wybierz ją.
3. Na **stronie grupy zasobów** wybierz pozycję **Usuń grupę zasobów**.
4. Wprowadź *myResourceGroupAG* dla **wpisz nazwę grupy zasobów** , a następnie wybierz **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o co można zrobić za pomocą usługi Azure Application Gateway](application-gateway-introduction.md)
