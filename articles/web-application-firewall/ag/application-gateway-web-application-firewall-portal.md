---
title: 'Samouczek: Tworzenie przy użyciu portalu - Zapora aplikacji sieci Web'
description: W tym samouczku dowiesz się, jak utworzyć bramę aplikacji z zaporą aplikacji sieci Web przy użyciu portalu Azure.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 68a9f051bf3d59cbf32377cb503e9ded0a54d5e9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74049219"
---
# <a name="tutorial-create-an-application-gateway-with-a-web-application-firewall-using-the-azure-portal"></a>Samouczek: Tworzenie bramy aplikacji z zaporą aplikacji sieci Web przy użyciu portalu Azure

W tym samouczku pokazano, jak utworzyć bramę aplikacji za pomocą portalu Azure z zaporą aplikacji sieci Web (WAF). Zapora aplikacji internetowych chroni Twoje aplikacje, używając reguł [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project). Reguły te obejmują ochronę przed atakami, takimi jak iniekcja kodu SQL, działanie skryptów między witrynami i porywanie sesji. Po utworzeniu bramy aplikacji należy ją przetestować, aby upewnić się, że działa poprawnie. Za pomocą usługi Azure Application Gateway możesz kierować ruch sieci web aplikacji do określonych zasobów, przypisując odbiorniki do portów, tworząc reguły i dodając zasoby do puli wewnętrznej bazy danych. Ze względu na prostotę w tym samouczku używa prostej konfiguracji z publicznym adresem IP front-end, odbiornika podstawowego do hostowania pojedynczej lokacji w tej bramie aplikacji, dwóch maszyn wirtualnych używanych dla puli wewnętrznej bazy danych i podstawowej reguły routingu żądań.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie bramy aplikacji z włączoną zaporą aplikacji internetowych
> * Tworzenie maszyn wirtualnych używanych jako serwery zaplecza
> * Tworzenie konta magazynu i konfigurowanie diagnostyki
> * Testowanie bramy aplikacji

![Przykład zapory aplikacji internetowych](../media/application-gateway-web-application-firewall-portal/scenario-waf.png)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

<!---If you prefer, you can complete this tutorial using [Azure PowerShell](tutorial-restrict-web-traffic-powershell.md) or [Azure CLI](tutorial-restrict-web-traffic-cli.md).--->

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure portal w [https://portal.azure.com](https://portal.azure.com).

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

Aby platforma Azure komunikowała się między zasobami, potrzebuje sieci wirtualnej. Można utworzyć nową sieć wirtualną lub użyć istniejącej. W tym przykładzie utworzysz nową sieć wirtualną. Sieć wirtualną można utworzyć podczas tworzenia bramy aplikacji. Wystąpienia bramy aplikacji są tworzone w oddzielnych podsieciach. W tym przykładzie tworzysz dwie podsieci: jedną dla bramy aplikacji i drugą dla serwerów zaplecza.

Wybierz pozycję **Utwórz zasób** w menu po lewej stronie w witrynie Azure Portal. Zostanie wyświetlone okno **Nowe**.

Wybierz **pozycję Sieć,** a następnie wybierz pozycję **Brama aplikacji** na liście **Polecane.**

### <a name="basics-tab"></a>Karta Podstawowe

1. Na karcie **Podstawy** wprowadź następujące wartości dla następujących ustawień bramy aplikacji:

   - **Grupa zasobów**: Wybierz **myResourceGroupAG** dla grupy zasobów. Jeśli ta grupa nie istnieje, wybierz pozycję **Utwórz nową** w celu jej utworzenia.
   - **Nazwa bramy aplikacji:** Wprowadź *myAppGateway* dla nazwy bramy aplikacji.
   - **Poziom**: wybierz **WAF V2**.

     ![Tworzenie nowej bramy aplikacji: Podstawy](../media/application-gateway-web-application-firewall-portal/application-gateway-create-basics.png)

2.  Do komunikacji między tworzonymi zasobami platforma Azure potrzebuje sieci wirtualnej. Można utworzyć nową sieć wirtualną lub użyć istniejącej. W tym przykładzie utworzysz nową sieć wirtualną w tym samym czasie, w tym co brama aplikacji. Wystąpienia bramy aplikacji są tworzone w oddzielnych podsieciach. W tym przykładzie tworzysz dwie podsieci: jedną dla bramy aplikacji i drugą dla serwerów zaplecza.

    W obszarze **Konfigurowanie sieci wirtualnej**utwórz nową sieć wirtualną, wybierając **pozycję Utwórz nowy**. W oknie **Utwórz sieć wirtualną,** które zostanie otwarte, wprowadź następujące wartości, aby utworzyć sieć wirtualną i dwie podsieci:

    - **Nazwa**: Wprowadź *myVNet* dla nazwy sieci wirtualnej.

    - **Nazwa podsieci** (podsieć bramy aplikacji): W siatce **podsieci** zostanie wyświetlonych podsieć o nazwie *Domyślna*. Zmień nazwę tej podsieci na *myAGSubnet*.<br>Podsieć bramy aplikacji może zawierać tylko bramy aplikacji. Inne zasoby nie są dozwolone.

    - **Nazwa podsieci** (podsieć serwera wewnętrznej bazy danych): W drugim wierszu siatki **Podsieci** wprowadź *myBackendSubnet* w kolumnie **Nazwa podsieci.**

    - **Zakres adresów** (podsieć serwera wewnętrznej bazy danych): W drugim wierszu siatki **podsieci** wprowadź zakres adresów, który nie pokrywa się z zakresem adresów *myAGSubnet*. Jeśli na przykład zakres adresów *myAGSubnet* wynosi 10.0.0.0/24, wprowadź *10.0.1.0/24* dla zakresu adresów *myBackendSubnet*.

    Wybierz **przycisk OK,** aby zamknąć okno **Utwórz sieć wirtualną** i zapisać ustawienia sieci wirtualnej.

     ![Tworzenie nowej bramy aplikacji: sieć wirtualna](../media/application-gateway-web-application-firewall-portal/application-gateway-create-vnet.png)
    
3. Na karcie Podstawy zaakceptuj wartości **domyślne** dla innych ustawień, a następnie wybierz pozycję **Dalej: Przednie przyciski**.

### <a name="frontends-tab"></a>Karta Frontendy

1. Na karcie **Frontends** sprawdź, czy **typ adresu IP zaplecza** jest ustawiony na **Publiczny**. <br>Adres IP frontu można skonfigurować jako publiczny lub prywatny, zgodnie z przypadkiem użycia. W tym przykładzie wybierzesz adres IP frontu publicznego.
   > [!NOTE]
   > Dla jednostki SKU bramy aplikacji w wersji 2 można wybrać tylko konfigurację adresów IP **publicznego** frontendu. Konfiguracja ip prywatnego frontonu nie jest obecnie włączona dla tej jednostki SKU w wersji 2.

2. Wybierz **pozycję Utwórz nowy** dla **publicznego adresu IP** i wprowadź adres *myAGPublicIPAddress* dla publicznej nazwy adresu IP, a następnie wybierz przycisk **OK**. 

     ![Tworzenie nowej bramy aplikacji: frontendy](../media/application-gateway-web-application-firewall-portal/application-gateway-create-frontends.png)

3. Wybierz **dalej: Zaplecze**.

### <a name="backends-tab"></a>Karta Zaplecze

Pula wewnętrznej bazy danych jest używana do kierowania żądań do serwerów wewnętrznej bazy danych, które obsługują żądanie. Pule zaplecza mogą składać się z kart sieciowych, zestawów skalowania maszyn wirtualnych, publicznych adresów IP, wewnętrznych adresów IP, w pełni kwalifikowanych nazw domen (FQDN) i zaplecza wielu dzierżawców, takich jak usługa Azure App Service. W tym przykładzie utworzysz pustą pulę wewnętrznej bazy danych z bramą aplikacji, a następnie dodasz obiekty docelowe wewnętrznej bazy danych do puli wewnętrznej bazy danych.

1. Na karcie **Wewnętrznej bazy zaznaczyć** pozycję **+Dodaj pulę zaplecza**.

2. W oknie **Dodaj pulę wewnętrznej bazy** danych, które zostanie otwarte, wprowadź następujące wartości, aby utworzyć pustą pulę wewnętrznej bazy danych:

    - **Nazwa**: Wprowadź *myBackendPool* dla nazwy puli wewnętrznej bazy danych.
    - **Dodaj pulę wewnętrznej bazy danych bez obiektów docelowych:** Wybierz **tak,** aby utworzyć pulę wewnętrznej bazy danych bez obiektów docelowych. Po utworzeniu bramy aplikacji dodasz obiekty docelowe wewnętrznej bazy danych.

3. W oknie **Dodawanie puli wewnętrznej bazy bazy danych** wybierz pozycję **Dodaj,** aby zapisać konfigurację puli wewnętrznej bazy danych i powrócić do karty **Wewnętrznej bazy** danych.

     ![Tworzenie nowej bramy aplikacji: zaplecze](../media/application-gateway-web-application-firewall-portal/application-gateway-create-backends.png)

4. Na karcie **Wewnętrznej bazy** wybierz pozycję **Dalej: Konfiguracja**.

### <a name="configuration-tab"></a>Karta Konfiguracja

Na karcie **Konfiguracja** połączysz pulę frontu i wewnętrznej bazy danych utworzonej przy użyciu reguły routingu.

1. Wybierz **pozycję Dodaj regułę** w kolumnie **Reguły routingu.**

2. W oknie **Dodawanie reguły routingu,** które się otworzy, wprowadź *myRoutingRule* dla **nazwy reguły**.

3. Reguła routingu wymaga odbiornika. Na karcie **Odbiornik** w oknie **Dodawanie reguły routingu** wprowadź następujące wartości dla odbiornika:

    - **Nazwa odbiornika**: Wprowadź *myListener* dla nazwy odbiornika.
    - **Adres IP frontendu:** Wybierz **opcję Publiczne,** aby wybrać publiczny adres IP utworzony dla frontendu.
  
      Zaakceptuj wartości domyślne dla innych ustawień na karcie **Odbiornik,** a następnie wybierz kartę **Bazy danych,** aby skonfigurować pozostałą część reguły routingu.

   ![Tworzenie nowej bramy aplikacji: odbiornik](../media/application-gateway-web-application-firewall-portal/application-gateway-create-rule-listener.png)

4. Na karcie **Cele wewnętrznej bazy danych** wybierz **myBackendPool** dla **celu wewnętrznej bazy**danych .

5. W przypadku **ustawienia HTTP**wybierz pozycję **Utwórz nowy,** aby utworzyć nowe ustawienie HTTP. Ustawienie HTTP określi zachowanie reguły routingu. W oknie **ustawień Dodaj HTTP,** które się otworzy, wprowadź *pozycjęHTTPSetting* dla **nazwy ustawienia HTTP**. Zaakceptuj wartości domyślne dla innych ustawień w oknie **ustawień Dodaj HTTP,** a następnie wybierz pozycję **Dodaj,** aby powrócić do okna **Dodaj regułę routingu.** 

     ![Tworzenie nowej bramy aplikacji: ustawienie HTTP](../media/application-gateway-web-application-firewall-portal/application-gateway-create-httpsetting.png)

6. W oknie **Dodawanie reguły routingu** wybierz pozycję **Dodaj,** aby zapisać regułę routingu i wróć do karty **Konfiguracja.**

     ![Tworzenie nowej bramy aplikacji: reguła routingu](../media/application-gateway-web-application-firewall-portal/application-gateway-create-rule-backends.png)

7. Wybierz **dalej: Tagi,** a następnie **Dalej: Recenzja + utwórz**.

### <a name="review--create-tab"></a>Karta Recenzja + tworzenie

Przejrzyj ustawienia na karcie **Recenzja + utwórz,** a następnie wybierz pozycję **Utwórz,** aby utworzyć sieć wirtualną, publiczny adres IP i bramę aplikacji. Tworzenie bramy aplikacji na platformie Azure może potrwać kilka minut. 

Zaczekaj na pomyślne zakończenie wdrożenia, zanim przejdziesz do kolejnej sekcji.

## <a name="add-backend-targets"></a>Dodawanie obiektów docelowych wewnętrznej bazy danych

W tym przykładzie użyjesz maszyn wirtualnych jako docelowego zaplecza. Można użyć istniejących maszyn wirtualnych lub utworzyć nowe. Utworzysz dwie maszyny wirtualne, które platforma Azure używa jako serwerów wewnętrznej bazy danych dla bramy aplikacji.

Aby to zrobić, będziesz:

1. Utwórz dwie nowe maszyny wirtualne, *myVM* i *myVM2*, które mają być używane jako serwery wewnętrznej bazy danych.
2. Zainstaluj usługi IIS na maszynach wirtualnych, aby sprawdzić, czy brama aplikacji została pomyślnie utworzona.
3. Dodaj serwery wewnętrznej bazy danych do puli wewnętrznej bazy danych.

### <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. W witrynie Azure Portal wybierz pozycję **Utwórz zasób**. Zostanie wyświetlone okno **Nowe**.
2. Wybierz **pozycję Centrum danych systemu Windows Server 2016** na liście **Popularne.** Zostanie wyświetlona strona **Tworzenie maszyny wirtualnej**.<br>Brama aplikacji może kierować ruch do dowolnego typu maszyny wirtualnej używanej w puli wewnętrznej bazy danych. W tym przykładzie należy użyć centrum danych systemu Windows Server 2016.
3. Wprowadź następujące wartości na karcie **Podstawy** dla poniższych ustawień maszyny wirtualnej:

    - **Grupa zasobów**: Wybierz **myResourceGroupAG** dla nazwy grupy zasobów.
    - **Nazwa maszyny wirtualnej**: Wprowadź *myVM* dla nazwy maszyny wirtualnej.
    - **Nazwa użytkownika**: Wprowadź *użytkownika azureuser* dla nazwy użytkownika administratora.
    - **Hasło**: Wprowadź *Azure123456!* jako hasło administratora.
4. Zaakceptuj inne ustawienia domyślne, a następnie wybierz pozycję **Dalej: Dyski**.  
5. Zaakceptuj domyślne ustawienia karty **Dyski,** a następnie wybierz pozycję **Dalej: Sieć**.
6. Na karcie **Sieć** sprawdź, czy wybrano pozycję **myVNet** w obszarze **Sieć wirtualna** oraz czy pozycja **Podsieć** została ustawiona na wartość **myBackendSubnet**. Zaakceptuj inne ustawienia domyślne, a następnie wybierz **pozycję Dalej: Zarządzanie**.<br>Brama aplikacji może komunikować się z wystąpieniami poza siecią wirtualną, w której się znajduje, ale musisz upewnić się, że istnieje łączność IP.
7. Na karcie **Zarządzanie** ustaw pozycję **Diagnostyka rozruchu** na **Wył.** Zaakceptuj pozostałe wartości domyślne, a następnie wybierz pozycję **Przeglądanie + tworzenie**.
8. Na karcie **Przeglądanie + tworzenie** przejrzyj ustawienia, usuń wszystkie błędy walidacji, a następnie wybierz pozycję **Utwórz**.
9. Poczekaj na ukończenie tworzenia maszyny wirtualnej, zanim przejdziesz dalej.

### <a name="install-iis-for-testing"></a>Instalowanie usługi IIS do testowania

W tym przykładzie można zainstalować usługi IIS na maszynach wirtualnych tylko w celu sprawdzenia, czy platforma Azure pomyślnie utworzyła bramę aplikacji.

1. Otwórz program [Azure PowerShell](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell). W tym celu wybierz pozycję **Cloud Shell** na górnym pasku nawigacyjnym w witrynie Azure Portal, a następnie wybierz pozycję **PowerShell** z listy rozwijanej. 

    ![Instalowanie rozszerzenia niestandardowego](../media/application-gateway-web-application-firewall-portal/application-gateway-extension.png)

2. Uruchom następujące polecenie, aby zainstalować usługi IIS na maszynie wirtualnej: 

    ```azurepowershell-interactive
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName myVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location EastUS
    ```

3. Utwórz drugą maszynę wirtualną i zainstaluj usługi IIS, wykonując kroki ukończone wcześniej. Nazwa maszyny wirtualnej i nazwa maszyny **wirtualnej** umożliwia użycie *myVM2* dla nazwy maszyny wirtualnej i dla ustawienia polecenia cmdlet **Set-AzVMExtension.**

### <a name="add-backend-servers-to-backend-pool"></a>Dodawanie serwerów zaplecza do puli wewnętrznej bazy danych

1. Wybierz pozycję **Wszystkie zasoby**, a następnie wybierz pozycję **myAppGateway**.

2. Wybierz pozycję **Pule zaplecza** w menu po lewej stronie.

3. Wybierz **myBackendPool**.

4. W obszarze **Cele** wybierz pozycję **Maszyna wirtualna** z listy rozwijanej.

5. W obszarach **MASZYNA WIRTUALNA** i **INTERFEJSY SIECIOWE** wybierz maszyny wirtualne **myVM** i **myVM2** oraz ich skojarzone interfejsy sieciowe z list rozwijanych.

    ![Dodawanie serwerów zaplecza](../media/application-gateway-web-application-firewall-portal/application-gateway-backend.png)

6. Wybierz **pozycję Zapisz**.

7. Poczekaj na zakończenie wdrożenia przed przejściem do następnego kroku.

## <a name="create-a-storage-account-and-configure-diagnostics"></a>Tworzenie konta magazynu i konfigurowanie diagnostyki

### <a name="create-a-storage-account"></a>Tworzenie konta magazynu

W tym artykule brama aplikacji używa konta magazynu do przechowywania danych do celów wykrywania i zapobiegania. Do rejestrowania danych można też użyć dzienników usługi Azure Monitor lub usługi Event Hub.

1. Wybierz **pozycję Utwórz zasób** w lewym górnym rogu witryny Azure portal.
1. Wybierz **pozycję Magazyn**, a następnie wybierz pozycję Konto **magazynu**.
1. W przypadku *grupy zasobów*wybierz **myResourceGroupAG** dla grupy zasobów.
1. Wpisz *myagstore1* dla nazwy konta magazynu.
1. Zaakceptuj wartości domyślne dla innych ustawień, a następnie wybierz pozycję **Recenzja + Utwórz**.
1. Przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz**.

### <a name="configure-diagnostics"></a>Konfigurowanie diagnostyki

Skonfiguruj diagnostykę do rejestrowania danych w dziennikach ApplicationGatewayAccessLog, ApplicationGatewayPerformanceLog i ApplicationGatewayFirewallLog.

1. W menu po lewej stronie wybierz pozycję **Wszystkie zasoby**, a następnie wybierz *pozycję myAppGateway*.
2. W obszarze Monitorowanie wybierz pozycję **Ustawienia diagnostyki**.
3. wybierz **pozycję Dodaj ustawienie diagnostyki**.
4. Wprowadź *myDiagnosticsSettings* jako nazwę ustawień diagnostycznych.
5. Wybierz **pozycję Archiwizuj konto magazynu**, a następnie wybierz pozycję **Konfiguruj,** aby wybrać wcześniej utworzone konto magazynu *myagstore1,* a następnie wybierz przycisk **OK**.
6. Wybierz dzienniki bramy aplikacji do zbierania i przechowywania.
7. Wybierz **pozycję Zapisz**.

    ![Konfigurowanie diagnostyki](../media/application-gateway-web-application-firewall-portal/application-gateway-diagnostics.png)

## <a name="create-and-link-a-web-application-firewall-policy"></a>Tworzenie i łączenie zasad Zapory aplikacji sieci Web

Wszystkie dostosowania i ustawienia WAF znajdują się w oddzielnym obiekcie, nazywanym zasadami WAF. Zasady muszą być skojarzone z bramą aplikacji. Aby utworzyć zasady WAF, zobacz [Tworzenie zasad WAF](create-waf-policy-ag.md). Po jego utworzeniu można następnie skojarzyć zasady z waf (lub odbiornika indywidualnego) z zasad WAF w **skojarzone bramy aplikacji** kartę. 

![Skojarzone bramy aplikacji](../media/application-gateway-web-application-firewall-portal/associated-application-gateways.png)

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

Mimo że usługi IIS nie są wymagane do utworzenia bramy aplikacji, zainstalowano ją, aby sprawdzić, czy platforma Azure pomyślnie utworzyła bramę aplikacji. Użyj usług do przetestowania bramy aplikacji:

1. Znajdź publiczny adres IP bramy aplikacji na jej stronie **Przegląd.** ![Rejestrowanie publicznego adresu IP bramy aplikacji](../media/application-gateway-web-application-firewall-portal/application-gateway-record-ag-address.png) 

   Możesz też wybrać **opcję Wszystkie zasoby**, wpisz *myAGPublicIPAddress* w polu wyszukiwania, a następnie zaznacz ją w wynikach wyszukiwania. Platforma Azure wyświetla publiczny adres IP na stronie **Omówienie**.
1. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki.
1. Sprawdź odpowiedź. Prawidłowa odpowiedź sprawdza, czy brama aplikacji została pomyślnie utworzona i może pomyślnie połączyć się z zapleczem.

   ![Testowanie bramy aplikacji](../media/application-gateway-web-application-firewall-portal/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie potrzebujesz już zasobów utworzonych za pomocą bramy aplikacji, usuń grupę zasobów. Usuwając grupę zasobów, usuwasz również bramę aplikacji i wszystkie powiązane z nią zasoby. 

Aby usunąć grupę zasobów:

1. W menu po lewej stronie witryny Azure Portal wybierz pozycję **Grupy zasobów**.
2. Na stronie **Grupy zasobów** wyszukaj pozycję **myResourceGroupAG** na liście i wybierz ją.
3. Na **stronie grupy zasobów** wybierz pozycję **Usuń grupę zasobów**.
4. Wprowadź *myResourceGroupAG* dla **TYPU NAZWA GRUPY ZASOBÓW,** a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o Zaporze aplikacji sieci Web](../overview.md)
