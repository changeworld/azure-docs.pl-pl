---
title: 'Szybki start: bezpośredni ruch internetowy za pomocą portalu'
titleSuffix: Azure Application Gateway
description: Dowiedz się, jak korzystać z witryny Azure Portal do tworzenia bramy aplikacji platformy Azure, która kieruje ruch sieci web do maszyn wirtualnych w puli zaplecza.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 03/09/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 17adc800bd5a2ae53e27350c7e0d588eaeee4a8f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241402"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-portal"></a>Szybki start: bezpośredni ruch internetowy w usłudze Azure Application Gateway — Azure Portal

W tym przewodniku Szybki start można użyć witryny Azure portal do utworzenia bramy aplikacji. Następnie przetestować go, aby upewnić się, że działa poprawnie. 

Brama aplikacji kieruje ruch sieci web aplikacji do określonych zasobów w puli wewnętrznej bazy danych. Przypisywanie odbiorników do portów, tworzenie reguł i dodawanie zasobów do puli zaplecza. Dla uproszczenia w tym artykule używa prostej konfiguracji z publicznym adresem IP front-end, odbiornika podstawowego do hostowania pojedynczej lokacji w bramie aplikacji, podstawowej reguły routingu żądań i dwóch maszyn wirtualnych w puli wewnętrznej bazy danych.

Ten przewodnik Szybki start można również ukończyć przy użyciu [programu Azure PowerShell](quick-create-powershell.md) lub [interfejsu wiersza polecenia platformy Azure.](quick-create-cli.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]



## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto za darmo](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do [witryny Azure portal](https://portal.azure.com) za pomocą konta platformy Azure.

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

Brama aplikacji zostanie utworzona przy użyciu kart na stronie **Tworzenie bramy aplikacji.**

1. W menu witryny Azure Portal lub na **stronie głównej** wybierz pozycję **Utwórz zasób**. Zostanie wyświetlone okno **Nowe**.

2. Wybierz **pozycję Sieć,** a następnie wybierz pozycję **Brama aplikacji** na liście **Polecane.**

### <a name="basics-tab"></a>Karta Podstawowe

1. Na karcie **Podstawy** wprowadź następujące wartości dla następujących ustawień bramy aplikacji:

   - **Grupa zasobów**: Wybierz **myResourceGroupAG** dla grupy zasobów. Jeśli ta grupa nie istnieje, wybierz pozycję **Utwórz nową** w celu jej utworzenia.
   - **Nazwa bramy aplikacji:** Wprowadź *myAppGateway* dla nazwy bramy aplikacji.

     ![Tworzenie nowej bramy aplikacji: Podstawy](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2. Do komunikacji między tworzonymi zasobami platforma Azure potrzebuje sieci wirtualnej. Można utworzyć nową sieć wirtualną lub użyć istniejącej. W tym przykładzie utworzysz nową sieć wirtualną w tym samym czasie, w tym co brama aplikacji. Wystąpienia bramy aplikacji są tworzone w oddzielnych podsieciach. W tym przykładzie tworzysz dwie podsieci: jedną dla bramy aplikacji i drugą dla serwerów zaplecza.

    W obszarze **Konfigurowanie sieci wirtualnej**utwórz nową sieć wirtualną, wybierając **pozycję Utwórz nowy**. W oknie **Utwórz sieć wirtualną,** które zostanie otwarte, wprowadź następujące wartości, aby utworzyć sieć wirtualną i dwie podsieci:

    - **Nazwa**: Wprowadź *myVNet* dla nazwy sieci wirtualnej.

    - **Nazwa podsieci** (podsieć bramy aplikacji): W siatce **podsieci** zostanie wyświetlonych podsieć o nazwie *Domyślna*. Zmień nazwę tej podsieci na *myAGSubnet*.<br>Podsieć bramy aplikacji może zawierać tylko bramy aplikacji. Inne zasoby nie są dozwolone.

    - **Nazwa podsieci** (podsieć serwera wewnętrznej bazy danych): W drugim wierszu siatki **Podsieci** wprowadź *myBackendSubnet* w kolumnie **Nazwa podsieci.**

    - **Zakres adresów** (podsieć serwera wewnętrznej bazy danych): W drugim wierszu siatki **podsieci** wprowadź zakres adresów, który nie pokrywa się z zakresem adresów *myAGSubnet*. Jeśli na przykład zakres adresów *myAGSubnet* wynosi 10.0.0.0/24, wprowadź *10.0.1.0/24* dla zakresu adresów *myBackendSubnet*.

    Wybierz **przycisk OK,** aby zamknąć okno **Utwórz sieć wirtualną** i zapisać ustawienia sieci wirtualnej.

     ![Tworzenie nowej bramy aplikacji: sieć wirtualna](./media/application-gateway-create-gateway-portal/application-gateway-create-vnet.png)
    
3. Na karcie Podstawy zaakceptuj wartości **domyślne** dla innych ustawień, a następnie wybierz pozycję **Dalej: Przednie przyciski**.

### <a name="frontends-tab"></a>Karta Frontendy

1. Na karcie **Frontends** sprawdź, czy **typ adresu IP zaplecza** jest ustawiony na **Publiczny**. <br>Adres IP frontu można skonfigurować jako publiczny lub prywatny, zgodnie z przypadkiem użycia. W tym przykładzie wybierzesz adres IP frontu publicznego.
   > [!NOTE]
   > Dla jednostki SKU bramy aplikacji w wersji 2 musi istnieć konfiguracja ip **publicznego** frontonia. Nadal można mieć konfigurację IP publicznego i prywatnego frontonu, ale konfiguracja IP tylko private (tylko tryb ILB) nie jest obecnie włączona dla jednostki SKU w wersji 2. 

2. Wybierz **pozycję Utwórz nowy** dla **publicznego adresu IP** i wprowadź adres *myAGPublicIPAddress* dla publicznej nazwy adresu IP, a następnie wybierz przycisk **OK**. 

     ![Tworzenie nowej bramy aplikacji: frontendy](./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png)

3. Wybierz **dalej: Zaplecze**.

### <a name="backends-tab"></a>Karta Zaplecze

Pula wewnętrznej bazy danych jest używana do kierowania żądań do serwerów wewnętrznej bazy danych, które obsługują żądanie. Pule zaplecza mogą składać się z kart sieciowych, zestawów skalowania maszyn wirtualnych, publicznych adresów IP, wewnętrznych adresów IP, w pełni kwalifikowanych nazw domen (FQDN) i zaplecza wielu dzierżawców, takich jak usługa Azure App Service. W tym przykładzie utworzysz pustą pulę wewnętrznej bazy danych z bramą aplikacji, a następnie dodasz obiekty docelowe wewnętrznej bazy danych do puli wewnętrznej bazy danych.

1. Na karcie **Wewnętrznej bazy zaznaczyć** pozycję **+Dodaj pulę zaplecza**.

2. W oknie **Dodaj pulę wewnętrznej bazy** danych, które zostanie otwarte, wprowadź następujące wartości, aby utworzyć pustą pulę wewnętrznej bazy danych:

    - **Nazwa**: Wprowadź *myBackendPool* dla nazwy puli wewnętrznej bazy danych.
    - **Dodaj pulę wewnętrznej bazy danych bez obiektów docelowych:** Wybierz **tak,** aby utworzyć pulę wewnętrznej bazy danych bez obiektów docelowych. Po utworzeniu bramy aplikacji dodasz obiekty docelowe wewnętrznej bazy danych.

3. W oknie **Dodawanie puli wewnętrznej bazy bazy danych** wybierz pozycję **Dodaj,** aby zapisać konfigurację puli wewnętrznej bazy danych i powrócić do karty **Wewnętrznej bazy** danych.

     ![Tworzenie nowej bramy aplikacji: zaplecze](./media/application-gateway-create-gateway-portal/application-gateway-create-backends.png)

4. Na karcie **Wewnętrznej bazy** wybierz pozycję **Dalej: Konfiguracja**.

### <a name="configuration-tab"></a>Karta Konfiguracja

Na karcie **Konfiguracja** połączysz pulę frontu i wewnętrznej bazy danych utworzonej przy użyciu reguły routingu.

1. Wybierz **pozycję Dodaj regułę** w kolumnie **Reguły routingu.**

2. W oknie **Dodawanie reguły routingu,** które się otworzy, wprowadź *myRoutingRule* dla **nazwy reguły**.

3. Reguła routingu wymaga odbiornika. Na karcie **Odbiornik** w oknie **Dodawanie reguły routingu** wprowadź następujące wartości dla odbiornika:

    - **Nazwa odbiornika**: Wprowadź *myListener* dla nazwy odbiornika.
    - **Adres IP frontendu:** Wybierz **opcję Publiczne,** aby wybrać publiczny adres IP utworzony dla frontendu.
  
      Zaakceptuj wartości domyślne dla innych ustawień na karcie **Odbiornik,** a następnie wybierz kartę **Bazy danych,** aby skonfigurować pozostałą część reguły routingu.

   ![Tworzenie nowej bramy aplikacji: odbiornik](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-listener.png)

4. Na karcie **Cele wewnętrznej bazy danych** wybierz **myBackendPool** dla **celu wewnętrznej bazy**danych .

5. W przypadku **ustawienia HTTP**wybierz pozycję **Utwórz nowy,** aby utworzyć nowe ustawienie HTTP. Ustawienie HTTP określi zachowanie reguły routingu. W oknie **ustawień Dodawanie protokołu HTTP,** które się otworzy, wprowadź *pozycjęhttpsetting* dla **nazwy ustawienia HTTP** i *80* dla **portu wewnętrznej bazy**danych . Zaakceptuj wartości domyślne dla innych ustawień w oknie **ustawień Dodaj HTTP,** a następnie wybierz pozycję **Dodaj,** aby powrócić do okna **Dodaj regułę routingu.** 

     ![Tworzenie nowej bramy aplikacji: ustawienie HTTP](./media/application-gateway-create-gateway-portal/application-gateway-create-httpsetting.png)

6. W oknie **Dodawanie reguły routingu** wybierz pozycję **Dodaj,** aby zapisać regułę routingu i wróć do karty **Konfiguracja.**

     ![Tworzenie nowej bramy aplikacji: reguła routingu](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-backends.png)

7. Wybierz **dalej: Tagi,** a następnie **Dalej: Recenzja + utwórz**.

### <a name="review--create-tab"></a>Karta Recenzja + tworzenie

Przejrzyj ustawienia na karcie **Recenzja + utwórz,** a następnie wybierz pozycję **Utwórz,** aby utworzyć sieć wirtualną, publiczny adres IP i bramę aplikacji. Tworzenie bramy aplikacji na platformie Azure może potrwać kilka minut. Zaczekaj na pomyślne zakończenie wdrożenia, zanim przejdziesz do kolejnej sekcji.

## <a name="add-backend-targets"></a>Dodawanie obiektów docelowych wewnętrznej bazy danych

W tym przykładzie użyjesz maszyn wirtualnych jako docelowego zaplecza. Można użyć istniejących maszyn wirtualnych lub utworzyć nowe. Utworzysz dwie maszyny wirtualne jako serwery wewnętrznej bazy danych dla bramy aplikacji.

Aby to zrobić, będziesz:

1. Utwórz dwie nowe maszyny wirtualne, *myVM* i *myVM2*, które mają być używane jako serwery wewnętrznej bazy danych.
2. Zainstaluj usługi IIS na maszynach wirtualnych, aby sprawdzić, czy brama aplikacji została pomyślnie utworzona.
3. Dodaj serwery wewnętrznej bazy danych do puli wewnętrznej bazy danych.

### <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. W menu witryny Azure Portal lub na **stronie głównej** wybierz pozycję **Utwórz zasób**. Zostanie wyświetlone okno **Nowe**.
2. Wybierz **pozycję Centrum danych systemu Windows Server 2016** na liście **Popularne.** Zostanie wyświetlona strona **Tworzenie maszyny wirtualnej**.<br>Brama aplikacji może kierować ruch do dowolnego typu maszyny wirtualnej używanej w puli wewnętrznej bazy danych. W tym przykładzie należy użyć centrum danych systemu Windows Server 2016.
3. Wprowadź następujące wartości na karcie **Podstawy** dla poniższych ustawień maszyny wirtualnej:

    - **Grupa zasobów**: Wybierz **myResourceGroupAG** dla nazwy grupy zasobów.
    - **Nazwa maszyny wirtualnej**: Wprowadź *myVM* dla nazwy maszyny wirtualnej.
    - **Region**: Wybierz ten sam region, w którym utworzono bramę aplikacji.
    - **Nazwa użytkownika:** Wpisz *azureuser* dla nazwy użytkownika administratora.
    - **Hasło**: Wpisz hasło.
4. Zaakceptuj inne ustawienia domyślne, a następnie wybierz pozycję **Dalej: Dyski**.  
5. Zaakceptuj domyślne ustawienia karty **Dyski,** a następnie wybierz pozycję **Dalej: Sieć**.
6. Na karcie **Sieć** sprawdź, czy wybrano pozycję **myVNet** w obszarze **Sieć wirtualna** oraz czy pozycja **Podsieć** została ustawiona na wartość **myBackendSubnet**. Zaakceptuj inne ustawienia domyślne, a następnie wybierz **pozycję Dalej: Zarządzanie**.<br>Brama aplikacji może komunikować się z wystąpieniami poza siecią wirtualną, w której się znajduje, ale musisz upewnić się, że istnieje łączność IP.
7. Na karcie **Zarządzanie** ustaw pozycję **Diagnostyka rozruchu** na **Wył.** Zaakceptuj pozostałe wartości domyślne, a następnie wybierz pozycję **Przeglądanie + tworzenie**.
8. Na karcie **Przeglądanie + tworzenie** przejrzyj ustawienia, usuń wszystkie błędy walidacji, a następnie wybierz pozycję **Utwórz**.
9. Poczekaj na ukończenie tworzenia maszyny wirtualnej, zanim przejdziesz dalej.

### <a name="install-iis-for-testing"></a>Instalowanie usługi IIS do testowania

W tym przykładzie można zainstalować usługi IIS na maszynach wirtualnych tylko w celu sprawdzenia, czy platforma Azure pomyślnie utworzyła bramę aplikacji.

1. Otwórz program Azure PowerShell. Wybierz **powłokę chmury** z górnego paska nawigacyjnego witryny Azure portal, a następnie wybierz pozycję **PowerShell** z listy rozwijanej. 

    ![Instalowanie rozszerzenia niestandardowego](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. Uruchom następujące polecenie, aby zainstalować usługi IIS na maszynie wirtualnej. W razie potrzeby zmień parametr *Lokalizacja:* 

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

1. W menu portalu Azure wybierz **pozycję Wszystkie zasoby** lub wyszukaj i wybierz pozycję Wszystkie *zasoby*. Następnie wybierz **myAppGateway**.

2. Wybierz pozycję **Pule zaplecza** w menu po lewej stronie.

3. Wybierz **myBackendPool**.

4. W **obszarze Obiekty docelowe wewnętrznej bazy danych**wybierz pozycję Maszyna **Target type** **wirtualna** z listy rozwijanej.

5. W obszarze **Cel**wybierz maszyny wirtualne **myVM** i **myVM2** oraz skojarzone z nimi interfejsy sieciowe z list rozwijanych.


   > [!div class="mx-imgBorder"]
   > ![Dodawanie serwerów zaplecza](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Wybierz **pozycję Zapisz**.

7. Poczekaj na zakończenie wdrożenia przed przejściem do następnego kroku.

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

Mimo że usługi IIS nie są wymagane do utworzenia bramy aplikacji, zainstalowano ją w tym przewodniku Szybki start, aby sprawdzić, czy platforma Azure pomyślnie utworzyła bramę aplikacji. Użyj usług do przetestowania bramy aplikacji:

1. Znajdź publiczny adres IP bramy aplikacji na jej stronie **Przegląd.** ![Zarejestruj publiczny adres](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) IP bramy aplikacji Lub możesz wybrać wszystkie **zasoby**, wprowadź *myAGPublicIPAddress* w polu wyszukiwania, a następnie zaznacz go w wynikach wyszukiwania. Platforma Azure wyświetla publiczny adres IP na stronie **Omówienie**.
2. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki, aby przeglądać ten adres IP.
3. Sprawdź odpowiedź. Prawidłowa odpowiedź sprawdza, czy brama aplikacji została pomyślnie utworzona i może pomyślnie połączyć się z zapleczem.

   ![Testowanie bramy aplikacji](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

   Odśwież przeglądarkę wiele razy i powinieneś zobaczyć połączenia zarówno z myVM, jak i myVM2.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie są już potrzebne zasoby utworzone za pomocą bramy aplikacji, usuń grupę zasobów. Po usunięciu grupy zasobów można również usunąć bramę aplikacji i wszystkie powiązane zasoby.

Aby usunąć grupę zasobów:

1. W menu portalu platformy Azure wybierz pozycję **Grupy zasobów** lub wyszukaj i wybierz pozycję *Grupy zasobów*.
2. Na stronie **Grupy zasobów** wyszukaj pozycję **myResourceGroupAG** na liście i wybierz ją.
3. Na **stronie grupy zasobów** wybierz pozycję **Usuń grupę zasobów**.
4. Wprowadź ciąg *myResourceGroupAG* w polu **WPISZ NAZWĘ GRUPY ZASOBÓW**, a następnie wybierz pozycję **Usuń**

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [zarządzanie ruchem internetowym przy użyciu bramy aplikacji za pomocą interfejsu wiersza polecenia platformy Azure](./tutorial-manage-web-traffic-cli.md)
