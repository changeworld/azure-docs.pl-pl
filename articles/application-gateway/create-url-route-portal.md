---
title: Samouczek — Tworzenie bramy aplikacji przy użyciu reguł routingu opartych na ścieżkach URL — Azure Portal
description: W ramach tego samouczka nauczysz się tworzyć reguły routingu oparte na ścieżkach URL dla bramy aplikacji i zestawu skalowania maszyn wirtualnych przy użyciu Azure Portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 07/27/2019
ms.author: victorh
ms.openlocfilehash: 63a1faa79374e72eabfbee4ece454728c3b4cc05
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597609"
---
# <a name="tutorial-create-an-application-gateway-with-path-based-routing-rules-using-the-azure-portal"></a>Samouczek: Tworzenie bramy aplikacji z regułami routingu opartymi na ścieżce przy użyciu Azure Portal

Przy użyciu Azure Portal można skonfigurować [reguły routingu oparte na ścieżkach URL](url-route-overview.md) podczas tworzenia [bramy aplikacji](overview.md). W tym samouczku utworzysz pule zaplecza przy użyciu maszyn wirtualnych. Następnie utworzysz reguły routingu, aby zapewnić, że ruch internetowy dociera do odpowiednich serwerów w pulach.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie bramy aplikacji
> * Tworzenie maszyn wirtualnych dla serwerów zaplecza
> * Tworzenie pul zaplecza z serwerami zaplecza
> * Tworzenie odbiornika zaplecza
> * Tworzenie reguły routingu opartej na ścieżce

![Przykład routingu adresów URL](./media/create-url-route-portal/scenario.png)

Jeśli wolisz, możesz wykonać ten samouczek przy użyciu [interfejsu wiersza polecenia platformy Azure](tutorial-url-route-cli.md) lub [Azure PowerShell](tutorial-url-route-powershell.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

1. Wybierz pozycję **Utwórz zasób** w menu po lewej stronie w witrynie Azure Portal. Zostanie wyświetlone okno **Nowe**.

2. Wybierz pozycję **Sieć**, a następnie wybierz pozycję **Application Gateway** z listy **Polecane**.

### <a name="basics-tab"></a>Karta podstawy

1. Na karcie **podstawowe** wprowadź następujące wartości następujących ustawień bramy aplikacji:

   - **Grupa zasobów**: wybierz **myResourceGroupAG** jako grupę zasobów. Jeśli ta grupa nie istnieje, wybierz pozycję **Utwórz nową** w celu jej utworzenia.
   - **Nazwa bramy aplikacji**: wprowadź *myAppGateway* jako nazwę bramy aplikacji.

     ![Utwórz nową bramę aplikacji: Ustawienia podstawowe](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  Do komunikacji między tworzonymi zasobami platforma Azure potrzebuje sieci wirtualnej. Można utworzyć nową sieć wirtualną lub użyć istniejącej. W tym przykładzie utworzysz nową sieć wirtualną w tym samym czasie, podczas tworzenia bramy aplikacji. Wystąpienia Application Gateway są tworzone w różnych podsieciach. W tym przykładzie tworzysz dwie podsieci: jedną dla bramy aplikacji i drugą dla serwerów zaplecza.

    W obszarze **Konfigurowanie sieci wirtualnej**wybierz pozycję **Utwórz nową** , aby utworzyć nową sieć wirtualną. W otwartym oknie **Tworzenie sieci wirtualnej** wprowadź następujące wartości, aby utworzyć sieć wirtualną i dwie podsieci:

    - **Nazwa**: wprowadź *myVNet* jako nazwę sieci wirtualnej.

    - **Nazwa podsieci** (Podsieć Application Gateway): W siatce **podsieci** zostanie wyświetlona podsieć o nazwie *default*. Zmień nazwę tej podsieci na *myAGSubnet*.

      Podsieć bramy aplikacji może zawierać tylko bramy aplikacji. Inne zasoby nie są dozwolone.

    - **Nazwa podsieci** (podsieć serwera wewnętrznej bazy danych): W drugim wierszu siatki **podsieci** wprowadź *myBackendSubnet* w kolumnie **Nazwa podsieci** .

    - **Zakres adresów** (podsieć serwera wewnętrznej bazy danych): W drugim wierszu siatki **podsieci** Wprowadź zakres adresów, który nie nakłada się na zakres adresów *myAGSubnet*. Na przykład jeśli zakres adresów *myAGSubnet* jest równy 10.0.0.0/24, wprowadź *10.0.1.0/24* dla zakresu adresów *myBackendSubnet*.

    Wybierz **przycisk OK** , aby zamknąć okno **Tworzenie sieci wirtualnej** i zapisać ustawienia sieci wirtualnej.

     ![Utwórz nową bramę aplikacji: Sieć wirtualna](./media/application-gateway-create-gateway-portal/application-gateway-create-vnet.png)
    
3. Na karcie **podstawy** zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz pozycję **dalej: Frontony**.

### <a name="frontends-tab"></a>Karta frontonów

1. Na karcie **frontony** Sprawdź, czy **Typ adresu IP frontonu** jest ustawiony na wartość **Public**. <br>Adres IP frontonu można skonfigurować tak, aby był publiczny lub prywatny jak w przypadku użycia. W tym przykładzie wybrano publiczny adres IP frontonu.
   > [!NOTE]
   > W przypadku jednostki SKU Application Gateway v2 można wybrać tylko **publiczną** konfigurację adresu IP frontonu. Konfiguracja prywatnego adresu IP frontonu nie jest obecnie włączona dla tej jednostki SKU w wersji 2.

2. Wybierz opcję **Utwórz nowy** dla **publicznego adresu IP** i wprowadź *MYAGPUBLICIPADDRESS* dla nazwy publicznego adresu IP, a następnie wybierz przycisk **OK**. 

     ![Utwórz nową bramę aplikacji: frontony](./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png)

3. Wybierz opcję **Dalej: Zakończenie**.

### <a name="backends-tab"></a>Karta zakończyła się

Pula zaplecza służy do kierowania żądań do serwerów zaplecza, które obsługują żądanie. Pule zaplecza mogą być kartami sieciowymi, zestawami skalowania maszyn wirtualnych, publicznymi adresami IP, wewnętrznymi adresami IP, w pełni kwalifikowanymi nazwami domen (FQDN) i wielodostępnymi zaplecami, jak Azure App Service W tym przykładzie utworzysz pustą pulę zaplecza z bramą aplikacji, a następnie dodasz cele zaplecza do puli zaplecza.

1. Na karcie **nadkończenie** wybierz pozycję **+ Dodaj pulę zaplecza**.

2. W otwartym oknie **Dodawanie puli zaplecza** wprowadź następujące wartości, aby utworzyć pustą pulę zaplecza:

    - **Nazwa**: Wprowadź *appGatewayBackendPool* jako nazwę puli zaplecza.
    - **Dodaj pulę zaplecza bez elementów docelowych**: Wybierz pozycję **tak** , aby utworzyć pulę zaplecza bez elementów docelowych. Po utworzeniu bramy aplikacji należy dodać cele zaplecza.

3. W oknie **Dodawanie puli zaplecza** wybierz pozycję **Dodaj** , aby zapisać konfigurację puli zaplecza i powrócić **do karty zaplecze** .
4. Teraz Dodaj dwie więcej pul zaplecza o nazwie *imagesBackendPool*i *videoBackendPool*.

     ![Utwórz nową bramę aplikacji: zafrontony](./media/create-url-route-portal/backends.png)

4. Na karcie **nadkończenie** wybierz pozycję **dalej: Konfiguracja**.

### <a name="configuration-tab"></a>Karta Konfiguracja

Na karcie **Konfiguracja** zostanie nawiązane połączenie frontonu i pul zaplecza utworzonych przy użyciu reguł routingu.

1. Wybierz pozycję **Dodaj regułę** w kolumnie **reguły routingu** .
2. W otwartym oknie **Dodawanie reguły routingu** wpisz *Rule1* dla **nazwy reguły**.
3. Reguła routingu wymaga odbiornika. Na karcie **odbiornik** w oknie **Dodawanie reguły routingu** wprowadź następujące wartości dla odbiornika:

    - **Nazwa odbiornika**: Wprowadź *DefaultListener* dla nazwy odbiornika.
    - **Adres IP frontonu**: Wybierz pozycję **Public (publiczny** ), aby wybrać publiczny adres IP utworzony dla frontonu.

   Zaakceptuj wartości domyślne pozostałych ustawień na karcie **odbiornik** , a następnie wybierz kartę **cele zaplecza** , aby skonfigurować resztę reguły routingu.
4. Na karcie **cele zaplecza** wybierz pozycję **appGatewayBackendPool** dla **elementu docelowego zaplecza**.

5. Dla **Ustawienia http**wybierz pozycję **Utwórz nowy** , aby utworzyć nowe ustawienie http. Ustawienie HTTP określi zachowanie reguły routingu. W oknie **Dodawanie ustawienia protokołu HTTP** , które zostanie otwarte, wprowadź *myHTTPSetting* dla **nazwy ustawienia http**. Zaakceptuj wartości domyślne pozostałych ustawień w oknie **Dodawanie ustawienia protokołu HTTP** , a następnie wybierz pozycję **Dodaj** , aby powrócić do okna **Dodawanie reguły routingu** . 

6. W oknie **Dodawanie reguły routingu** wybierz pozycję **Dodaj** , aby zapisać regułę routingu, i wróć do karty **Konfiguracja** .



1. Wybierz pozycję **Dodaj regułę** w kolumnie **reguły routingu** .

2. W otwartym oknie **Dodawanie reguły routingu** wpisz *ograniczeniem zakresu wystąpień* dla **nazwy reguły**.

3. Reguła routingu wymaga odbiornika. Na karcie **odbiornik** w oknie **Dodawanie reguły routingu** wprowadź następujące wartości dla odbiornika:

    - **Nazwa odbiornika**: Wprowadź *myBackendListener* dla nazwy odbiornika.
    - **Adres IP frontonu**: Wybierz pozycję **Public (publiczny** ), aby wybrać publiczny adres IP utworzony dla frontonu.
    - **Port**: 8080

   W obszarze **Ustawienia dodatkowe**:
   - **Typ odbiornika**: Podstawowa

   Zaakceptuj wartości domyślne pozostałych ustawień na karcie **odbiornik** , a następnie wybierz kartę **cele zaplecza** , aby skonfigurować resztę reguły routingu.

4. Na karcie **cele zaplecza** wybierz pozycję **appGatewayBackendPool** dla **elementu docelowego zaplecza**.

5. Dla **Ustawienia http**wybierz pozycję *myHTTPSetting*. Zaakceptuj wartości domyślne pozostałych ustawień w oknie **Dodawanie ustawienia protokołu HTTP** , a następnie wybierz pozycję **Dodaj** , aby powrócić do okna **Dodawanie reguły routingu** . 

1. W obszarze **routing oparty na ścieżce**wybierz opcję **Dodaj wiele obiektów docelowych, aby utworzyć regułę opartą na ścieżce**.
2. W oknie **Dodawanie reguły ścieżki** wprowadź następujące wartości dla reguły ścieżki:

   - **Ścieżka**: */images/\**
   - **Nazwa reguły ścieżki**: *Obrazy*
   - **Ustawienie http**: Wybierz *myHTTPSetting*
   - **Cel zaplecza**: *imagesBackendPool*
9. Wybierz pozycję **Dodaj**.
10. Dodaj kolejną regułę ścieżki o nazwie *Video*, z ścieżką */Video/\**  i *videoBackendPool*.
11. Wybierz pozycję **Zapisz zmiany i wróć do reguły routingu**.

    ![Dodaj regułę routingu](media/create-url-route-portal/add-routing-rule.png)

12. Wybierz pozycję **Dodaj**.

7. Wybierz opcję **Dalej: Tagi** , a **następnie następne: Przejrzyj i Utwórz**.

### <a name="review--create-tab"></a>Przejrzyj i Utwórz kartę

Przejrzyj ustawienia na karcie **Przegląd + tworzenie** , a następnie wybierz pozycję **Utwórz** , aby utworzyć sieć wirtualną, publiczny adres IP i bramę aplikacji. Tworzenie bramy aplikacji na platformie Azure może potrwać kilka minut.

Zaczekaj na pomyślne zakończenie wdrożenia, zanim przejdziesz do kolejnej sekcji.


## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

W tym przykładzie utworzysz trzy maszyny wirtualne, które będą używane jako serwery zaplecza dla bramy aplikacji. Należy również zainstalować usługi IIS na maszynach wirtualnych, aby sprawdzić, czy Brama aplikacji została pomyślnie utworzona.

1. W witrynie Azure Portal wybierz pozycję **Utwórz zasób**. Zostanie wyświetlone okno **Nowe**.
2. Wybierz pozycję **obliczenia** , a następnie wybierz pozycję **Windows Server 2016 Datacenter** na **popularnej** liście. Zostanie wyświetlona strona **Tworzenie maszyny wirtualnej**.

   Application Gateway może kierować ruch do dowolnego typu maszyny wirtualnej używanej w puli zaplecza. W tym przykładzie używane jest centrum danych systemu Windows Server 2016.
1. Wprowadź następujące wartości na karcie **Podstawy** dla poniższych ustawień maszyny wirtualnej:

    - **Grupa zasobów**: wybierz **myResourceGroupAG** jako grupę zasobów.
    - **Nazwa maszyny wirtualnej**: Wprowadź *myVM1* dla nazwy maszyny wirtualnej.
    - **Nazwa użytkownika**: wprowadź *azureuser* jako nazwę użytkownika administratora.
    - **Hasło**: wprowadź *Azure123456!* jako hasło administratora.
4. Zaakceptuj pozostałe wartości domyślne, a następnie wybierz pozycję **Dalej: Dyski**.  
5. Zaakceptuj wartości domyślne na karcie **Dyski**, a następnie wybierz pozycję **Dalej: Sieć**.
6. Na karcie **Sieć** sprawdź, czy wybrano pozycję **myVNet** w obszarze **Sieć wirtualna** oraz czy pozycja **Podsieć** została ustawiona na wartość **myBackendSubnet**. Zaakceptuj pozostałe wartości domyślne, a następnie wybierz pozycję **Dalej: Zarządzanie**.

   Application Gateway może komunikować się z wystąpieniami poza siecią wirtualną, w której znajduje się, ale należy upewnić się, że połączenie IP jest nawiązywane.
1. Na karcie **Zarządzanie** ustaw pozycję **Diagnostyka rozruchu** na **Wył.** Zaakceptuj pozostałe wartości domyślne, a następnie wybierz pozycję **Przeglądanie + tworzenie**.
2. Na karcie **Przeglądanie + tworzenie** przejrzyj ustawienia, usuń wszystkie błędy walidacji, a następnie wybierz pozycję **Utwórz**.
3. Poczekaj na ukończenie tworzenia maszyny wirtualnej, zanim przejdziesz dalej.

### <a name="install-iis"></a>Instalowanie usług IIS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Otwórz powłokę interaktywną i upewnij się, że jest ona ustawiona na program **PowerShell**.

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

3. Utwórz dwie więcej maszyn wirtualnych i zainstaluj usługi IIS, używając właśnie wykonanych kroków. Użyj *myVM2* i *myVM3* dla nazw maszyn wirtualnych i dla wartości **VMName** w opcji Set-AzVMExtension.

## <a name="add-backend-servers-to-backend-pools"></a>Dodawanie serwerów zaplecza do pul zaplecza

1. Wybierz pozycję **Wszystkie zasoby**, a następnie wybierz pozycję **myAppGateway**.

2. Wybierz pozycję **Pule zaplecza** w menu po lewej stronie.

3. Wybierz **appGatewayBackendPool**.

4. W obszarze **Cele** wybierz pozycję **Maszyna wirtualna** z listy rozwijanej.

5. W obszarze interfejsy **maszyn wirtualnych** i **sieciowych**wybierz maszynę wirtualną **myVM1** , a jej interfejs sieciowy jest skojarzony z list rozwijanych.

    ![Dodawanie serwerów zaplecza](./media/create-url-route-portal/backend-pool.png)

6. Wybierz pozycję **Zapisz**.
7. Powtórz to polecenie, aby dodać *myVM2* i interfejs do *imagesBackendPool*, a następnie *myVM3* i interfejs *videoBackendPool*.

Przed przejściem do następnego kroku poczekaj na zakończenie wdrożenia.

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

1. Wybierz pozycję **wszystkie zasoby**, a następnie wybierz pozycję **myAGPublicIPAddress**.

    ![Rejestrowanie publicznego adresu IP bramy aplikacji](./media/create-url-route-portal/application-gateway-record-ag-address.png)

2. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki. Na przykład http://40.121.222.19.

    ![Testowanie podstawowego adresu URL w bramie aplikacji](./media/create-url-route-portal/application-gateway-iistest.png)

3. Zmień adres URL na http://&lt;IP-Address&gt;: 8080/images/test.htm, zastępując &lt;adres&gt; IP adresem IP, a powinien wyglądać podobnie do następującego przykładu:

    ![Testowanie adresu URL obrazów w bramie aplikacji](./media/create-url-route-portal/application-gateway-iistest-images.png)

4. Zmień adres URL na http://&lt;IP-Address&gt;: 8080/Video/test.htm, zastępując &lt;adres&gt; IP adresem IP, a powinien wyglądać podobnie do następującego przykładu:

    ![Testowanie adresu URL wideo w bramie aplikacji](./media/create-url-route-portal/application-gateway-iistest-video.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie potrzebujesz już zasobów utworzonych za pomocą bramy aplikacji, usuń grupę zasobów. Usuwając grupę zasobów, usuwasz również bramę aplikacji i wszystkie powiązane z nią zasoby. 

Aby usunąć grupę zasobów:

1. W menu po lewej stronie witryny Azure Portal wybierz pozycję **Grupy zasobów**.
2. Na stronie **Grupy zasobów** wyszukaj pozycję **myResourceGroupAG** na liście i wybierz ją.
3. Na **stronie grupy zasobów** wybierz pozycję **Usuń grupę zasobów**.
4. Wprowadź *myResourceGroupAG* w **polu wpisz nazwę grupy zasobów** , a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat tego, co możesz zrobić za pomocą usługi Azure Application Gateway](application-gateway-introduction.md)
