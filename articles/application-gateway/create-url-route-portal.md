---
title: 'Samouczek: reguły routingu oparte na ścieżce adresów URL przy użyciu portalu — Brama aplikacji platformy Azure'
description: W tym samouczku dowiesz się, jak utworzyć reguły routingu oparte na ścieżce adresów URL dla bramy aplikacji i zestawu skalowania maszyny wirtualnej przy użyciu witryny Azure portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 8d48ea133aaabbe9fd44bda545d672e68c93c08d
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312196"
---
# <a name="tutorial-create-an-application-gateway-with-path-based-routing-rules-using-the-azure-portal"></a>Samouczek: Tworzenie bramy aplikacji z regułami routingu opartymi na ścieżkach przy użyciu witryny Azure portal

Za pomocą portalu Azure można skonfigurować [reguły routingu oparte na ścieżce adresów URL](application-gateway-url-route-overview.md) podczas tworzenia [bramy aplikacji](application-gateway-introduction.md). W tym samouczku utworzysz pule wewnętrznej bazy danych przy użyciu maszyn wirtualnych. Następnie należy utworzyć reguły routingu, które upewnij się, że ruch sieci web dociera do odpowiednich serwerów w pulach.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Tworzenie bramy aplikacji
> * Tworzenie maszyn wirtualnych dla serwerów zaplecza
> * Tworzenie pul zaplecza z serwerami zaplecza
> * Tworzenie odbiornika wewnętrznej bazy danych
> * Tworzenie reguły routingu opartej na ścieżce

![Przykład routingu adresów URL](./media/application-gateway-create-url-route-portal/scenario.png)

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure portal at[https://portal.azure.com](https://portal.azure.com)

## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

W tym przykładzie utworzysz trzy maszyny wirtualne, które mają być używane jako serwery wewnętrznej bazy danych dla bramy aplikacji. Można również zainstalować usługi IIS na maszynach wirtualnych, aby sprawdzić, czy brama aplikacji działa zgodnie z oczekiwaniami.

1. W witrynie Azure Portal wybierz pozycję **Utwórz zasób**.
2. Wybierz **pozycję Centrum danych systemu Windows Server 2016** na liście Popularne.
3. Wprowadź poniższe wartości dla maszyny wirtualnej:

    - **Grupa zasobów**, wybierz **pozycję Utwórz nowy**, a następnie wpisz *myResourceGroupAG*.
    - **Nazwa maszyny wirtualnej**: *myVM1*
    - **Region**: *(USA) Wschodnie stany USA*
    - **Nazwa użytkownika**: *azureuser*
    - **Hasło**: *Azure123456!*


4. Wybierz pozycję **Dalej: Dyski**.
5. Wybierz **dalej:Sieć**
6. W polu **Sieć wirtualna**wybierz pozycję **Utwórz nowy,** a następnie wpisz te wartości dla sieci wirtualnej:

   - *myVNet* — jako nazwę sieci wirtualnej.
   - *10.0.0.0/16* — jako przestrzeń adresową sieci wirtualnej.
   - *myBackendSubnet* dla pierwszej nazwy podsieci
   - *10.0.1.0/24* - dla przestrzeni adresowej podsieci.
   - *myAGSubnet* - dla drugiej nazwy podsieci.
   - *10.0.0.0/24* — jako przestrzeń adresową podsieci.
7. Kliknij przycisk **OK**.

8. Upewnij się, że w obszarze **Interfejs sieciowy**dla podsieci wybrano **myBackendSubnet,** a następnie wybierz pozycję **Dalej: Zarządzanie**.
9. Wybierz **opcję Wyłącz,** aby wyłączyć diagnostykę rozruchu.
10. Kliknij **pozycję Recenzja + Utwórz**, przejrzyj ustawienia na stronie podsumowania, a następnie wybierz pozycję **Utwórz**.
11. Utwórz jeszcze dwie maszyny wirtualne, *myVM2* i *myVM3* i umieść je w sieci wirtualnej *MyVNet* i podsieci *myBackendSubnet.*

### <a name="install-iis"></a>Instalowanie usług IIS

1. Otwórz powłokę interaktywną i upewnij się, że jest ustawiona na program **PowerShell**.

    ![Instalowanie rozszerzenia niestandardowego](./media/application-gateway-create-url-route-portal/application-gateway-extension.png)

2. Uruchom następujące polecenie, aby zainstalować usługi IIS na maszynie wirtualnej: 

    ```azurepowershell
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

3. Utwórz jeszcze dwie maszyny wirtualne i zainstaluj usługi IIS, wykonując kroki, które właśnie zostały ukończone. Wprowadź nazwy nazw i nazw i *VM3* *w nazwach vmm* i vmname w polu Set-AzVMExtension.

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

1. Wybierz pozycję **Utwórz zasób** w menu po lewej stronie w witrynie Azure Portal. Zostanie wyświetlone okno **Nowe**.

2. Wybierz **pozycję Sieć,** a następnie wybierz pozycję **Brama aplikacji** na liście **Polecane.**

### <a name="basics-tab"></a>Karta Podstawowe

1. Na karcie **Podstawy** wprowadź następujące wartości dla następujących ustawień bramy aplikacji:

   - **Grupa zasobów**: Wybierz **myResourceGroupAG** dla grupy zasobów.
   - **Nazwa bramy aplikacji:** Wprowadź *myAppGateway* dla nazwy bramy aplikacji.
   - **Region** — wybierz **(USA) wschodnie stany USA**.

        ![Tworzenie nowej bramy aplikacji: Podstawy](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  W obszarze **Konfiguruj sieć wirtualną**wybierz **myVNet** dla nazwy sieci wirtualnej.
3. Wybierz **myAGSubnet** dla podsieci.
3. Zaakceptuj wartości domyślne dla innych ustawień, a następnie wybierz **przycisk Dalej: Przednie .**

### <a name="frontends-tab"></a>Karta Frontendy

1. Na karcie **Frontends** sprawdź, czy **typ adresu IP zaplecza** jest ustawiony na **Publiczny**.

   > [!NOTE]
   > Dla jednostki SKU bramy aplikacji w wersji 2 można wybrać tylko konfigurację adresów IP **publicznego** frontendu. Konfiguracja ip prywatnego frontonu nie jest obecnie włączona dla tej jednostki SKU w wersji 2.

2. Wybierz **pozycję Utwórz nowy** dla **publicznego adresu IP** i wprowadź adres *myAGPublicIPAddress* dla publicznej nazwy adresu IP, a następnie wybierz przycisk **OK**. 
3. Wybierz **dalej: Zaplecze**.

### <a name="backends-tab"></a>Karta Zaplecze

Pula wewnętrznej bazy danych jest używana do kierowania żądań do serwerów wewnętrznej bazy danych, które obsługują żądanie. Pule zaplecza mogą składać się z kart sieciowych, zestawów skalowania maszyn wirtualnych, publicznych adresów IP, wewnętrznych adresów IP, w pełni kwalifikowanych nazw domen (FQDN) i zaplecza wielu dzierżawców, takich jak usługa Azure App Service.

1. Na karcie **Wewnętrznej bazy zaznaczyć** pozycję **+Dodaj pulę zaplecza**.

2. W oknie **Dodaj pulę wewnętrznej bazy** danych, które zostanie otwarte, wprowadź następujące wartości, aby utworzyć pustą pulę wewnętrznej bazy danych:

    - **Nazwa**: Wprowadź *myBackendPool* dla nazwy puli wewnętrznej bazy danych.
3. W obszarze **Cele wewnętrznej bazy danych**, typ obiektu **docelowego**wybierz **pozycję Maszyna wirtualna** z listy rozwijanej.

5. W **obszarze Cel** wybierz interfejs sieciowy dla **myVM1**.
6. Wybierz pozycję **Dodaj**.
7. Powtórz, aby dodać pulę wewnętrznej bazy *danych Obrazy* z *myVM2* jako obiektem docelowym, a pulę zaplecza *wideo* z *myVM3* jako celem docelowym.
8. Wybierz **pozycję Dodaj,** aby zapisać konfigurację puli wewnętrznej bazy danych i powrócić do karty **Wewnętrznej bazy** danych.

4. Na karcie **Wewnętrznej bazy** wybierz pozycję **Dalej: Konfiguracja**.

### <a name="configuration-tab"></a>Karta Konfiguracja

Na karcie **Konfiguracja** połączysz pulę frontu i wewnętrznej bazy danych utworzonej przy użyciu reguły routingu.

1. Wybierz **pozycję Dodaj regułę** w kolumnie **Reguły routingu.**

2. W oknie **Dodawanie reguły routingu,** które się otworzy, wprowadź *myRoutingRule* dla **nazwy reguły**.

3. Reguła routingu wymaga odbiornika. Na karcie **Odbiornik** w oknie **Dodawanie reguły routingu** wprowadź następujące wartości dla odbiornika:

    - **Nazwa odbiornika**: Wprowadź *myListener* dla nazwy odbiornika.
    - **Adres IP frontendu:** Wybierz **opcję Publiczne,** aby wybrać publiczny adres IP utworzony dla frontendu.
    - **Port**: Typ *8080*
  
        Zaakceptuj wartości domyślne dla innych ustawień na karcie **Odbiornik,** a następnie wybierz kartę **Bazy danych,** aby skonfigurować pozostałą część reguły routingu.

4. Na karcie **Cele wewnętrznej bazy danych** wybierz **myBackendPool** dla **celu wewnętrznej bazy**danych .

5. W przypadku **ustawienia HTTP**wybierz pozycję **Utwórz nowy,** aby utworzyć nowe ustawienie HTTP. Ustawienie HTTP określi zachowanie reguły routingu. 

6. W oknie **ustawień Dodaj HTTP,** które się otworzy, wprowadź *pozycjęHTTPSetting* dla **nazwy ustawienia HTTP**. Zaakceptuj wartości domyślne dla innych ustawień w oknie **ustawień Dodaj HTTP,** a następnie wybierz pozycję **Dodaj,** aby powrócić do okna **Dodaj regułę routingu.**
7. W obszarze **Routing oparty na ścieżce**wybierz **pozycję Dodaj wiele obiektów docelowych, aby utworzyć regułę opartą na ścieżce**.
8. W przypadku **ścieżki**wpisz */images/*\*.
9. W przypadku **nazwy reguły ścieżki**wpisz *Images*.
10. W przypadku **ustawienia HTTP**wybierz **pozycjęhttpsetting**
11. W przypadku **celu wewnętrznej bazy**danych wybierz pozycję **Obrazy**.
12. Wybierz **pozycję Dodaj,** aby zapisać regułę ścieżki i powrócić do karty **Dodaj regułę routingu.**
13. Powtórz tę czynność, aby dodać kolejną regułę dla wideo.
14. Wybierz **pozycję Dodaj,** aby dodać regułę routingu i powrócić do karty **Konfiguracja.**
15. Wybierz **dalej: Tagi,** a następnie **Dalej: Recenzja + utwórz**.

> [!NOTE]
> Nie trzeba dodawać niestandardowej */** reguły ścieżki do obsługi spraw domyślnych. Jest to automatycznie obsługiwane przez domyślną pulę wewnętrznej bazy danych.

### <a name="review--create-tab"></a>Karta Recenzja + tworzenie

Przejrzyj ustawienia na karcie **Recenzja + utwórz,** a następnie wybierz pozycję **Utwórz,** aby utworzyć sieć wirtualną, publiczny adres IP i bramę aplikacji. Tworzenie bramy aplikacji na platformie Azure może potrwać kilka minut. Zaczekaj na pomyślne zakończenie wdrożenia, zanim przejdziesz do kolejnej sekcji.


## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

1. Wybierz pozycję **Wszystkie zasoby**, a następnie wybierz pozycję **myAppGateway**.

    ![Rejestrowanie publicznego adresu IP bramy aplikacji](./media/application-gateway-create-url-route-portal/application-gateway-record-ag-address.png)

2. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki. Takie jak,\/http: /52.188.72.175:8080.

    ![Testowanie podstawowego adresu URL w bramie aplikacji](./media/application-gateway-create-url-route-portal/application-gateway-iistest.png)

   Odbiornik na porcie 8080 kieruje to żądanie do domyślnej puli wewnętrznej bazy danych.

3. Zmień adres URL na *http:// adres&gt;&lt;IP :8080/images/test.htm* &lt;, zastępując&gt; adres IP swoim adresem IP i powinieneś zobaczyć coś takiego jak w poniższym przykładzie:

    ![Testowanie adresu URL obrazów w bramie aplikacji](./media/application-gateway-create-url-route-portal/application-gateway-iistest-images.png)

   Odbiornik na porcie 8080 kieruje to żądanie do puli wewnętrznej bazy *danych Obrazy.*

4. Zmień adres URL na *http:// adres&gt;&lt;IP:8080/video/test.htm*, &lt;zastępując adres&gt; IP swoim adresem IP i powinieneś zobaczyć coś takiego jak na poniższym przykładzie:

    ![Testowanie adresu URL wideo w bramie aplikacji](./media/application-gateway-create-url-route-portal/application-gateway-iistest-video.png)

   Odbiornik na porcie 8080 kieruje to żądanie do puli zaplecza *wideo.*


## <a name="next-steps"></a>Następne kroki

- [Włączanie end-to end TLS w usłudze Azure Application Gateway](application-gateway-backend-ssl.md)
