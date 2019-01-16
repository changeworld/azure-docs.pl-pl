---
title: 'Szybki start: bezpośredni ruch internetowy w usłudze Azure Application Gateway — Azure Portal | Microsoft Docs'
description: Dowiedz się, jak za pomocą witryny Azure Portal utworzyć bramę Azure Application Gateway, która kieruje ruch internetowy do maszyn wirtualnych w puli zaplecza.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 1/8/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 16e23f77509d2402f765981b39a30e08a2309f68
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2019
ms.locfileid: "54156531"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-portal"></a>Szybki start: bezpośredni ruch internetowy w usłudze Azure Application Gateway — Azure Portal

W tym przewodniku Szybki start przedstawiono sposób użycia witryny Azure Portal do tworzenia bramy aplikacji z dwiema maszynami wirtualnymi w puli zaplecza. Następnie będzie można ją przetestować, aby sprawdzić, czy działa prawidłowo. Dzięki usłudze Azure Application Gateway kierujesz ruch internetowy aplikacji do określonych zasobów, przypisując odbiorniki do portów, tworząc reguły i dodając zasoby do puli zaplecza.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu danych konta Azure.

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

Do komunikacji między tworzonymi zasobami platforma Azure potrzebuje sieci wirtualnej. W tym przykładzie tworzysz dwie podsieci: jedną dla bramy aplikacji i drugą dla serwerów zaplecza. Sieć wirtualną można utworzyć podczas tworzenia bramy aplikacji.

1. Wybierz pozycję **Utwórz zasób** w menu po lewej stronie w witrynie Azure Portal. Zostanie wyświetlone okno **Nowe**.

2. Wybierz pozycję **Sieć**, a następnie wybierz pozycję **Application Gateway** z listy **Polecane**.

### <a name="basics-page"></a>Strona Podstawy

1. Na stronie **Podstawy** poniższe wartości następujących ustawień bramy aplikacji:

    - **Nazwa**: wprowadź *myAppGateway* jako nazwę bramy aplikacji.
    - **Grupa zasobów**: wybierz **myResourceGroupAG** jako grupę zasobów. Jeśli ta grupa nie istnieje, wybierz pozycję **Utwórz nową** w celu jej utworzenia.

    ![Tworzenie nowej bramy aplikacji](./media/application-gateway-create-gateway-portal/application-gateway-create.png)

2. Zaakceptuj wartości domyślne innych ustawień, a następnie kliknij przycisk **OK**.

### <a name="settings-page"></a>Strona Ustawienia

1. Na stronie **Ustawienia** w obszarze **Konfiguracja podsieci** wybierz pozycję **Wybierz sieć wirtualną**.

2. Na stronie **Wybieranie sieci wirtualnej** wybierz pozycję **Utwórz nową**, a następnie wprowadź wartości następujących ustawień sieci wirtualnej:

    - **Nazwa**: wprowadź *myVNet* jako nazwę sieci wirtualnej.

    - **Przestrzeń adresowa**: wprowadź *10.0.0.0/16* jako przestrzeń adresową sieci wirtualnej.

    - **Nazwa podsieci**: wprowadź *myAGSubnet* jako nazwę podsieci.<br>Podsieć bramy aplikacji może zawierać tylko bramy aplikacji. Inne zasoby nie są dozwolone.

    - **Zakres adresów podsieci**: wprowadź *10.0.0.0/24* jako zakres adresów podsieci.

    ![Tworzenie sieci wirtualnej](./media/application-gateway-create-gateway-portal/application-gateway-vnet.png)

3. Wybierz przycisk **OK**, aby wrócić do strony **Ustawienia**.

4. W obszarze **Konfiguracja adresu IP frontonu** sprawdź, czy **Typ adresu IP** ustawiono na **Publiczny**. W obszarze **Publiczny adres IP** sprawdź, czy wybrano pozycję **Utwórz nową**. 

5. Wprowadź ciąg *myAGPublicIPAddress* jako nazwę publicznego adresu IP. 

6. Zaakceptuj wartości domyślne innych ustawień, a następnie kliknij przycisk **OK**.

### <a name="summary-page"></a>Strona podsumowania

Przejrzyj ustawienia na stronie **Podsumowanie**, a następnie wybierz przycisk **OK**, aby utworzyć sieć wirtualną, publiczny adres IP i bramę aplikacji. Tworzenie bramy aplikacji na platformie Azure może potrwać kilka minut. Zaczekaj na pomyślne zakończenie wdrożenia, zanim przejdziesz do kolejnej sekcji.

## <a name="add-a-subnet"></a>Dodawanie podsieci

Aby dodać podsieć do utworzonej sieci wirtualnej, wykonaj następujące kroki:

1. Wybierz pozycję **Wszystkie zasoby** w menu po lewej stronie w witrynie Azure Portal, wprowadź ciąg *myVNet* w polu wyszukiwania, a następnie wybierz pozycję **myVNet** w wynikach wyszukiwania.

2. Wybierz pozycję **Podsieci** z menu po lewej stronie, a następnie wybierz pozycję **+ Podsieć**. 

    ![Tworzenie podsieci](./media/application-gateway-create-gateway-portal/application-gateway-subnet.png)

3. Na stronie **Dodawanie podsieci** wprowadź ciąg *myBackendSubnet* w polu **Nazwa** podsieci, a następnie wybierz przycisk **OK**.

## <a name="create-backend-servers"></a>Tworzenie serwerów zaplecza

W tym przykładzie utworzysz dwie maszyny wirtualne, których platforma Azure użyje jako serwerów zaplecza dla bramy aplikacji. Zainstalujesz również usługi IIS na maszynach wirtualnych, aby sprawdzić, czy platforma Azure pomyślnie utworzyła bramę aplikacji.

### <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. W witrynie Azure Portal wybierz pozycję **Utwórz zasób**. Zostanie wyświetlone okno **Nowe**.

2. Wybierz pozycję **Compute**, a następnie z wybierz pozycję **Windows Server 2016 Datacenter** z listy **Polecane**. Zostanie wyświetlona strona **Tworzenie maszyny wirtualnej**.

3. Wprowadź następujące wartości na karcie **Podstawy** dla poniższych ustawień maszyny wirtualnej:

    - **Grupa zasobów**: wybierz **myResourceGroupAG** jako grupę zasobów.
    - **Nazwa maszyny wirtualnej**: wprowadź *myVM* jako nazwę maszyny wirtualnej.
    - **Nazwa użytkownika**: wprowadź *azureuser* jako nazwę użytkownika administratora.
    - **Hasło**: wprowadź *Azure123456!* jako hasło administratora.

4. Zaakceptuj pozostałe wartości domyślne, a następnie wybierz pozycję **Dalej: Dyski**.  

5. Zaakceptuj wartości domyślne na karcie **Dyski**, a następnie wybierz pozycję **Dalej: Sieć**.

6. Na karcie **Sieć** sprawdź, czy wybrano pozycję **myVNet** w obszarze **Sieć wirtualna** oraz czy pozycja **Podsieć** została ustawiona na wartość **myBackendSubnet**. Zaakceptuj pozostałe wartości domyślne, a następnie wybierz pozycję **Dalej: Zarządzanie**.

8. Na karcie **Zarządzanie** ustaw pozycję **Diagnostyka rozruchu** na **Wył.** Zaakceptuj pozostałe wartości domyślne, a następnie wybierz pozycję **Przeglądanie + tworzenie**.

9. Na karcie **Przeglądanie + tworzenie** przejrzyj ustawienia, usuń wszystkie błędy walidacji, a następnie wybierz pozycję **Utwórz**.

10. Poczekaj na ukończenie tworzenia maszyny wirtualnej, zanim przejdziesz dalej.

### <a name="install-iis"></a>Instalowanie usług IIS

1. Otwórz program [Azure PowerShell](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell). W tym celu wybierz pozycję **Cloud Shell** na górnym pasku nawigacyjnym w witrynie Azure Portal, a następnie wybierz pozycję **PowerShell** z listy rozwijanej. 

    ![Instalowanie rozszerzenia niestandardowego](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

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

3. Utwórz drugą maszynę wirtualną i zainstaluj usługi IIS, wykonując kroki ukończone wcześniej. Użyj wartości *myVM2* jako nazwy maszyny wirtualnej oraz jako wartości ustawienia **VMName** polecenia cmdlet **Set-AzureRmVMExtension**.

### <a name="add-backend-servers"></a>Dodawanie serwerów zaplecza

1. Wybierz pozycję **Wszystkie zasoby**, a następnie wybierz pozycję **myAppGateway**.

2. Wybierz pozycję **Pule zaplecza** w menu po lewej stronie. Platforma Azure automatycznie utworzyła pulę domyślną, **appGatewayBackendPool**, podczas tworzenia bramy aplikacji. 

3. Wybierz pozycję **appGatewayBackendPool**.

4. W obszarze **Cele** wybierz pozycję **Maszyna wirtualna** z listy rozwijanej.

5. W obszarach **MASZYNA WIRTUALNA** i **INTERFEJSY SIECIOWE** wybierz maszyny wirtualne **myVM** i **myVM2** oraz ich skojarzone interfejsy sieciowe z list rozwijanych.

    ![Dodawanie serwerów zaplecza](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Wybierz pozycję **Zapisz**.

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

1. Na ekranie **Omówienie** znajdź publiczny adres IP bramy aplikacji.

    ![Rejestrowanie publicznego adresu IP bramy aplikacji](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png)

    Alternatywnie możesz wybrać pozycję **Wszystkie zasoby**, wprowadzić ciąg *myAGPublicIPAddress* w polu wyszukiwania, a następnie wybrać tę pozycję w wynikach wyszukiwania. Platforma Azure wyświetla publiczny adres IP na stronie **Omówienie**.

2. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki.

    ![Testowanie bramy aplikacji](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie potrzebujesz już zasobów utworzonych za pomocą bramy aplikacji, usuń grupę zasobów. Usuwając grupę zasobów, usuwasz również bramę aplikacji i wszystkie powiązane z nią zasoby. 

Aby usunąć grupę zasobów:
1. W menu po lewej stronie witryny Azure Portal wybierz pozycję **Grupy zasobów**.
2. Na stronie **Grupy zasobów** wyszukaj pozycję **myResourceGroupAG** na liście i wybierz ją.
3. Na **stronie grupy zasobów** wybierz pozycję **Usuń grupę zasobów**.
4. Wprowadź ciąg *myResourceGroupAG* w polu **WPISZ NAZWĘ GRUPY ZASOBÓW**, a następnie wybierz pozycję **Usuń**

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zarządzanie ruchem internetowym przy użyciu bramy aplikacji za pomocą interfejsu wiersza polecenia platformy Azure](./tutorial-manage-web-traffic-cli.md)
