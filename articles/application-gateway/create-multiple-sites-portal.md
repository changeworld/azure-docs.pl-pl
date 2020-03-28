---
title: 'Samouczek: Hostuje wiele witryn sieci web za pomocą portalu Azure'
titleSuffix: Azure Application Gateway
description: W tym samouczku dowiesz się, jak utworzyć bramę aplikacji, która obsługuje wiele witryn sieci web przy użyciu witryny Azure portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: victorh
ms.openlocfilehash: ca6be666a9b77532b4f1c61f6e3391c239e82c91
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74075146"
---
# <a name="tutorial-create-and-configure-an-application-gateway-to-host-multiple-web-sites-using-the-azure-portal"></a>Samouczek: Tworzenie i konfigurowanie bramy aplikacji do obsługi wielu witryn sieci Web przy użyciu portalu Azure

Za pomocą portalu Azure można [skonfigurować hosting wielu witryn sieci web](multiple-site-overview.md) podczas tworzenia bramy [aplikacji](overview.md). W tym samouczku można zdefiniować pul adresów wewnętrznej bazy danych przy użyciu maszyn wirtualnych. Następnie, bazując na należących do Ciebie domenach, skonfigurujesz odbiorniki i reguły, aby się upewnić, że ruch internetowy dociera do odpowiednich serwerów w pulach. W tym samouczku przyjęto założenie, że jesteś właścicielem wielu domen, przykładami których są *www.contoso.com* i *www.fabrikam.com*.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie bramy aplikacji
> * Tworzenie maszyn wirtualnych dla serwerów zaplecza
> * Tworzenie pul zaplecza z serwerami zaplecza
> * Tworzenie odbiorników zaplecza
> * Tworzenie reguł routingu
> * Tworzenie rekordu CNAME w domenie

![Przykład routingu obejmujący wiele witryn](./media/create-multiple-sites-portal/scenario.png)

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure portal at[https://portal.azure.com](https://portal.azure.com)

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

1. Wybierz pozycję **Utwórz zasób** w menu po lewej stronie w witrynie Azure Portal. Zostanie wyświetlone okno **Nowe**.

2. Wybierz **pozycję Sieć,** a następnie wybierz pozycję **Brama aplikacji** na liście **Polecane.**

### <a name="basics-tab"></a>Karta Podstawowe

1. Na karcie **Podstawy** wprowadź następujące wartości dla następujących ustawień bramy aplikacji:

   - **Grupa zasobów**: Wybierz **myResourceGroupAG** dla grupy zasobów. Jeśli ta grupa nie istnieje, wybierz pozycję **Utwórz nową** w celu jej utworzenia.
   - **Nazwa bramy aplikacji:** Wprowadź *myAppGateway* dla nazwy bramy aplikacji.

     ![Tworzenie nowej bramy aplikacji: Podstawy](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  Do komunikacji między tworzonymi zasobami platforma Azure potrzebuje sieci wirtualnej. Można utworzyć nową sieć wirtualną lub użyć istniejącej. W tym przykładzie utworzysz nową sieć wirtualną w tym samym czasie, w tym co brama aplikacji. Wystąpienia bramy aplikacji są tworzone w oddzielnych podsieciach. W tym przykładzie tworzysz dwie podsieci: jedną dla bramy aplikacji i drugą dla serwerów zaplecza.

    W obszarze **Konfigurowanie sieci wirtualnej**wybierz pozycję **Utwórz nowy,** aby utworzyć nową sieć wirtualną . W oknie **Utwórz sieć wirtualną,** które zostanie otwarte, wprowadź następujące wartości, aby utworzyć sieć wirtualną i dwie podsieci:

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
   > Dla jednostki SKU bramy aplikacji w wersji 2 można wybrać tylko konfigurację adresów IP **publicznego** frontendu. Konfiguracja ip prywatnego frontonu nie jest obecnie włączona dla tej jednostki SKU w wersji 2.

2. Wybierz **pozycję Utwórz nowy** dla **publicznego adresu IP** i wprowadź adres *myAGPublicIPAddress* dla publicznej nazwy adresu IP, a następnie wybierz przycisk **OK**. 

     ![Tworzenie nowej bramy aplikacji: frontendy](./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png)

3. Wybierz **dalej: Zaplecze**.

### <a name="backends-tab"></a>Karta Zaplecze

Pula wewnętrznej bazy danych jest używana do kierowania żądań do serwerów wewnętrznej bazy danych, które obsługują żądanie. Pule zaplecza mogą być kartami sieciowymi, zestawami skalowania maszyn wirtualnych, publicznymi adresami IP, wewnętrznymi adresami IP, w pełni kwalifikowanymi nazwami domen (FQDN) i zapleczem wielu dzierżawców, takimi jak usługa Azure App Service. W tym przykładzie utworzysz pustą pulę wewnętrznej bazy danych z bramą aplikacji, a następnie dodasz obiekty docelowe wewnętrznej bazy danych do puli wewnętrznej bazy danych.

1. Na karcie **Wewnętrznej bazy zaznaczyć** pozycję **+Dodaj pulę zaplecza**.

2. W oknie **Dodaj pulę wewnętrznej bazy** danych, które zostanie otwarte, wprowadź następujące wartości, aby utworzyć pustą pulę wewnętrznej bazy danych:

    - **Nazwa**: Wprowadź *contosoPool* dla nazwy puli wewnętrznej bazy danych.
    - **Dodaj pulę wewnętrznej bazy danych bez obiektów docelowych:** Wybierz **tak,** aby utworzyć pulę wewnętrznej bazy danych bez obiektów docelowych. Po utworzeniu bramy aplikacji dodasz obiekty docelowe wewnętrznej bazy danych.

3. W oknie **Dodawanie puli wewnętrznej bazy bazy danych** wybierz pozycję **Dodaj,** aby zapisać konfigurację puli wewnętrznej bazy danych i powrócić do karty **Wewnętrznej bazy** danych.
4. Teraz dodaj kolejną pulę zaplecza o nazwie *fabrikamPool*.

     ![Tworzenie nowej bramy aplikacji: zaplecze](./media/create-multiple-sites-portal/backend-pools.png)

4. Na karcie **Wewnętrznej bazy** wybierz pozycję **Dalej: Konfiguracja**.

### <a name="configuration-tab"></a>Karta Konfiguracja

Na **karcie Konfiguracja** połączysz pule frontendu i wewnętrznej bazy danych utworzone przy użyciu reguły routingu.

1. Wybierz **pozycję Dodaj regułę** w kolumnie **Reguły routingu.**

2. W oknie **Dodaj regułę routingu,** które się otworzy, wprowadź *contosoRule* dla **nazwy reguły**.

3. Reguła routingu wymaga odbiornika. Na karcie **Odbiornik** w oknie **Dodawanie reguły routingu** wprowadź następujące wartości dla odbiornika:

    - **Nazwa odbiornika**: Wprowadź *contosoListener* dla nazwy odbiornika.
    - **Adres IP frontendu:** Wybierz **opcję Publiczne,** aby wybrać publiczny adres IP utworzony dla frontendu.

   W **obszarze Dodatkowe ustawienia:**
   - **Typ odbiornika:** Wiele witryn
   - **Nazwa hosta**: **www.contoso.com**

   Zaakceptuj wartości domyślne dla innych ustawień na karcie **Odbiornik,** a następnie wybierz kartę **Bazy danych,** aby skonfigurować pozostałą część reguły routingu.

   ![Tworzenie nowej bramy aplikacji: odbiornik](./media/create-multiple-sites-portal/routing-rule.png)

4. Na karcie **Cele wewnętrznej bazy danych** wybierz **contosoPool** dla **celu wewnętrznej bazy**danych .

5. W przypadku **ustawienia HTTP**wybierz pozycję **Utwórz nowy,** aby utworzyć nowe ustawienie HTTP. Ustawienie HTTP określi zachowanie reguły routingu. W oknie **ustawień Dodawanie protokołu HTTP,** które się otworzy, wprowadź *contosoHTTPSetting* dla **nazwy ustawienia HTTP**. Zaakceptuj wartości domyślne dla innych ustawień w oknie **ustawień Dodaj HTTP,** a następnie wybierz pozycję **Dodaj,** aby powrócić do okna **Dodaj regułę routingu.** 

6. W oknie **Dodawanie reguły routingu** wybierz pozycję **Dodaj,** aby zapisać regułę routingu i wróć do karty **Konfiguracja.**
7. Wybierz **pozycję Dodaj regułę** i dodaj podobną regułę, odbiornik, miejsce docelowe wewnętrznej bazy danych i ustawienie HTTP dla firmy Fabrikam.

     ![Tworzenie nowej bramy aplikacji: reguła routingu](./media/create-multiple-sites-portal/fabrikamRule.png)

7. Wybierz **dalej: Tagi,** a następnie **Dalej: Recenzja + utwórz**.

### <a name="review--create-tab"></a>Karta Recenzja + tworzenie

Przejrzyj ustawienia na karcie **Recenzja + utwórz,** a następnie wybierz pozycję **Utwórz,** aby utworzyć sieć wirtualną, publiczny adres IP i bramę aplikacji. Tworzenie bramy aplikacji na platformie Azure może potrwać kilka minut.

Zaczekaj na pomyślne zakończenie wdrożenia, zanim przejdziesz do kolejnej sekcji.

## <a name="add-backend-targets"></a>Dodawanie obiektów docelowych wewnętrznej bazy danych

W tym przykładzie użyjesz maszyn wirtualnych jako docelowego zaplecza. Można użyć istniejących maszyn wirtualnych lub utworzyć nowe. Utworzysz dwie maszyny wirtualne, które platforma Azure używa jako serwerów wewnętrznej bazy danych dla bramy aplikacji.

Aby dodać cele zaplecza, musisz:

1. Utwórz dwie nowe maszyny wirtualne, *contosoVM* i *fabrikamVM*, które mają być używane jako serwery wewnętrznej bazy danych.
2. Zainstaluj usługi IIS na maszynach wirtualnych, aby sprawdzić, czy brama aplikacji została pomyślnie utworzona.
3. Dodaj serwery wewnętrznej bazy danych do pul wewnętrznej bazy danych.

### <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. W witrynie Azure Portal wybierz pozycję **Utwórz zasób**. Zostanie wyświetlone okno **Nowe**.
2. Wybierz **pozycję Oblicz,** a następnie wybierz pozycję **Centrum danych systemu Windows Server 2016** na liście **Popularne.** Zostanie wyświetlona strona **Tworzenie maszyny wirtualnej**.<br>Brama aplikacji może kierować ruch do dowolnego typu maszyny wirtualnej używanej w puli wewnętrznej bazy danych. W tym przykładzie należy użyć centrum danych systemu Windows Server 2016.
3. Wprowadź następujące wartości na karcie **Podstawy** dla poniższych ustawień maszyny wirtualnej:

    - **Grupa zasobów**: Wybierz **myResourceGroupAG** dla nazwy grupy zasobów.
    - **Nazwa maszyny wirtualnej**: Wprowadź *contosoVM* dla nazwy maszyny wirtualnej.
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

    ![Instalowanie rozszerzenia niestandardowego](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. Uruchom następujące polecenie, aby zainstalować usługi IIS na maszynie wirtualnej: 

    ```azurepowershell-interactive
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName contosoVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location EastUS
    ```

3. Utwórz drugą maszynę wirtualną i zainstaluj usługi IIS, wykonując kroki, które zostały wcześniej wykonane. Funkcja *fabrikamVM* służy do określania nazwy maszyny wirtualnej i dla ustawienia **nazwy maszyny Wirtualnej** polecenia cmdlet **Set-AzVMExtension.**

### <a name="add-backend-servers-to-backend-pools"></a>Dodawanie serwerów zaplecza do pul zaplecza

1. Wybierz pozycję **Wszystkie zasoby**, a następnie wybierz pozycję **myAppGateway**.

2. Wybierz pozycję **Pule zaplecza** w menu po lewej stronie.

3. Wybierz **contosoPool**.

4. W obszarze **Cele** wybierz pozycję **Maszyna wirtualna** z listy rozwijanej.

5. W obszarze **MASZYNY WIRTUALNE** i **INTERFEJSY SIECI WYBIERZ**maszynę wirtualną **contosoVM** i skojarzony z nią interfejs sieciowy z list rozwijanych.

    ![Dodawanie serwerów zaplecza](./media/create-multiple-sites-portal/edit-backend-pool.png)

6. Wybierz **pozycję Zapisz**.
7. Powtórz, aby dodać *fabrikamVM* i interfejs do *fabrikamPool*.

Poczekaj na zakończenie wdrożenia przed przejściem do następnego kroku.

## <a name="create-a-www-a-record-in-your-domains"></a>Tworzenie rekordu www A w domenach

Po utworzeniu bramy aplikacji z jej publicznym adresem IP można uzyskać adres IP i użyć go do utworzenia rekordu A w domenach. 

1. Kliknij **pozycję Wszystkie zasoby**, a następnie kliknij pozycję **myAGPublicIPAddress**.

    ![Rejestrowanie adresu DNS bramy aplikacji](./media/create-multiple-sites-portal/public-ip.png)

2. Skopiuj adres IP i użyj go jako wartości nowego rekordu *www* A w swoich domenach.

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

1. Wpisz nazwę swojej domeny na pasku adresu przeglądarki. Na przykład `http://www.contoso.com`.

    ![Testowanie witryny contoso w bramie aplikacji](./media/create-multiple-sites-portal/application-gateway-iistest.png)

2. Zmień adres na drugą domenę. Powinny zostać wyświetlone informacje, jak w następującym przykładzie:

    ![Testowanie witryny fabrikam w bramy aplikacji](./media/create-multiple-sites-portal/application-gateway-iistest2.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie potrzebujesz już zasobów utworzonych za pomocą bramy aplikacji, usuń grupę zasobów. Po usunięciu grupy zasobów można również usunąć bramę aplikacji i wszystkie powiązane z nią zasoby.

Aby usunąć grupę zasobów:

1. W menu po lewej stronie witryny Azure Portal wybierz pozycję **Grupy zasobów**.
2. Na stronie **Grupy zasobów** wyszukaj pozycję **myResourceGroupAG** na liście i wybierz ją.
3. Na **stronie grupy zasobów** wybierz pozycję **Usuń grupę zasobów**.
4. Wprowadź ciąg *myResourceGroupAG* w polu **WPISZ NAZWĘ GRUPY ZASOBÓW**, a następnie wybierz pozycję **Usuń**

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o tym, co możesz zrobić z usługą Azure Application Gateway](application-gateway-introduction.md)