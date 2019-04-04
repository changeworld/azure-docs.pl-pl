---
title: Samouczek — Tworzenie bramy aplikacji przy użyciu zapory aplikacji sieci web — witryna Azure portal | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć bramę aplikacji z zaporą aplikacji internetowych przy użyciu witryny Azure portal.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 03/25/2019
ms.author: victorh
ms.openlocfilehash: 1284ddec4cd9cea3ea53c20d437550405dd614d9
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58905872"
---
# <a name="create-an-application-gateway-with-a-web-application-firewall-using-the-azure-portal"></a>Tworzenie bramy aplikacji z zaporą aplikacji sieci web przy użyciu witryny Azure portal

> [!div class="op_single_selector"]
>
> - [Azure Portal](application-gateway-web-application-firewall-portal.md)
> - [PowerShell](tutorial-restrict-web-traffic-powershell.md)
> - [Interfejs wiersza polecenia platformy Azure](tutorial-restrict-web-traffic-cli.md)
>
> 

W tym samouczku dowiesz się, jak utworzyć za pomocą witryny Azure portal [bramy application gateway](application-gateway-introduction.md) z [zapory aplikacji sieci web](application-gateway-web-application-firewall-overview.md) (WAF). Zapora aplikacji internetowych chroni Twoje aplikacje, używając reguł [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project). Reguły te obejmują ochronę przed atakami, takimi jak iniekcja kodu SQL, działanie skryptów między witrynami i porywanie sesji. Po utworzeniu bramy application gateway, możesz go przetestować, aby upewnić się, że działa prawidłowo. Za pomocą usługi Azure Application Gateway możesz przekazać ruchu aplikacji sieci web do określonych zasobów, przypisując odbiorników portów, tworząc reguły i dodawanie zasobów do puli zaplecza. Dla uproszczenia w tym artykule używany jest prostą konfigurację obejmującą publicznego adresu IP frontonu, podstawowy odbiornik do hosta jednej lokacji w tej bramy application gateway, dwie maszyny wirtualne, które służy do puli zaplecza i regułę routingu podstawowego żądania.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Tworzenie bramy aplikacji z włączoną zaporą aplikacji internetowych
> * Tworzenie maszyn wirtualnych, używane jako serwery zaplecza
> * Tworzenie konta magazynu i konfigurowanie diagnostyki

![Przykład zapory aplikacji internetowych](./media/application-gateway-web-application-firewall-portal/scenario-waf.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com)

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

Do komunikacji między tworzonymi zasobami platforma Azure potrzebuje sieci wirtualnej. Możesz utworzyć nową sieć wirtualną lub użyć istniejącego. W tym przykładzie zostanie utworzona nowa sieć wirtualna. Sieć wirtualną można utworzyć podczas tworzenia bramy aplikacji. Wystąpienia bramy aplikacji są tworzone w różnych podsieciach. W tym przykładzie tworzysz dwie podsieci: jedną dla bramy aplikacji i drugą dla serwerów zaplecza.

Wybierz pozycję **Utwórz zasób** w menu po lewej stronie w witrynie Azure Portal. Zostanie wyświetlone okno **Nowe**.

Wybierz pozycję **Sieć**, a następnie wybierz pozycję **Application Gateway** z listy **Polecane**.

### <a name="basics-page"></a>Strona Podstawy

1. Na stronie **Podstawy** poniższe wartości następujących ustawień bramy aplikacji:
   - **Nazwa**: wprowadź *myAppGateway* jako nazwę bramy aplikacji.
   - **Grupa zasobów**: wybierz **myResourceGroupAG** jako grupę zasobów. Jeśli ta grupa nie istnieje, wybierz pozycję **Utwórz nową** w celu jej utworzenia.
   - Wybierz *zapory aplikacji sieci Web* dla warstwy usługi application gateway.![ Utwórz nową bramę aplikacji](./media/application-gateway-web-application-firewall-portal/application-gateway-create.png)

Zaakceptuj wartości domyślne dla innych ustawień, a następnie kliknij przycisk **OK**.

### <a name="settings-page"></a>Strona Ustawienia

1. Na stronie **Ustawienia** w obszarze **Konfiguracja podsieci** wybierz pozycję **Wybierz sieć wirtualną**. <br>
2. Na stronie **Wybieranie sieci wirtualnej** wybierz pozycję **Utwórz nową**, a następnie wprowadź wartości następujących ustawień sieci wirtualnej:
   - **Nazwa**: wprowadź *myVNet* jako nazwę sieci wirtualnej.
   - **Przestrzeń adresowa**: wprowadź *10.0.0.0/16* jako przestrzeń adresową sieci wirtualnej.
   - **Nazwa podsieci**: wprowadź *myAGSubnet* jako nazwę podsieci.<br>Podsieć bramy aplikacji może zawierać tylko bramy aplikacji. Inne zasoby nie są dozwolone.
   - **Zakres adresów podsieci**: Wprowadź *10.0.0.0/24* dla zakresu adresów podsieci.![ Tworzenie sieci wirtualnej](./media/application-gateway-web-application-firewall-portal/application-gateway-vnet.png)
3. Kliknij przycisk **OK**, aby utworzyć sieć wirtualną i podsieć.
4. Wybierz **konfiguracja adresu IP frontonu**. W obszarze **Konfiguracja adresu IP frontonu** sprawdź, czy **Typ adresu IP** ustawiono na **Publiczny**. W obszarze **Publiczny adres IP** sprawdź, czy wybrano pozycję **Utwórz nową**. <br>Można skonfigurować adresu IP frontonu za publicznych lub prywatnych zgodnie z danego przypadku użycia. W tym przykładzie Wybierzmy publicznego adresu IP frontonu. 
5. Wprowadź ciąg *myAGPublicIPAddress* jako nazwę publicznego adresu IP. 
6. Zaakceptuj wartości domyślne innych ustawień, a następnie kliknij przycisk **OK**.<br>Wybierzmy wartości domyślne w tym artykule dla uproszczenia, ale można skonfigurować niestandardowe wartości dla innych ustawień, w zależności od danego przypadku użycia 

### <a name="summary-page"></a>Strona podsumowania

Przejrzyj ustawienia na stronie **Podsumowanie**, a następnie wybierz przycisk **OK**, aby utworzyć sieć wirtualną, publiczny adres IP i bramę aplikacji. Tworzenie bramy aplikacji na platformie Azure może potrwać kilka minut. Zaczekaj na pomyślne zakończenie wdrożenia, zanim przejdziesz do kolejnej sekcji.

## <a name="add-backend-pool"></a>Dodaj pulę zaplecza

Pula zaplecza jest używany do kierowania żądań do serwerów wewnętrznej bazy danych, które będą obsługująca żądanie. Pule zaplecza może składać się z kartami sieciowymi, zestawy skalowania maszyn wirtualnych, publiczne adresy IP, wewnętrzne adresy IP, w pełni kwalifikowaną nazwę (FQDN), a wielodostępne zaplecza takich jak usługa Azure App Service. Musisz dodać swoje cele wewnętrznej bazy danych do puli zaplecza.

W tym przykładzie użyjemy maszyny wirtualne jako docelowego w wewnętrznej bazie danych. Możemy użyć istniejących maszyn wirtualnych lub utworzyć nowe. W tym przykładzie utworzymy dwie maszyny wirtualne, które platforma Azure używa jako serwery zaplecza w usłudze application gateway. Aby to zrobić, obejmuje następujące czynności:

1. Utwórz nową podsieć *myBackendSubnet*, w której zostanie utworzone nowe maszyny wirtualne. 
2. Tworzenie nowych maszyn wirtualnych 2, *myVM* i *myVM2*, który zostanie użyty jako serwery zaplecza.
3. Zainstaluj usługi IIS na maszynach wirtualnych, aby sprawdzić, czy brama aplikacji została pomyślnie utworzona.
4. Dodawanie serwerów wewnętrznej bazy danych do puli zaplecza.

### <a name="add-a-subnet"></a>Dodawanie podsieci

Aby dodać podsieć do utworzonej sieci wirtualnej, wykonaj następujące kroki:

1. Wybierz pozycję **Wszystkie zasoby** w menu po lewej stronie w witrynie Azure Portal, wprowadź ciąg *myVNet* w polu wyszukiwania, a następnie wybierz pozycję **myVNet** w wynikach wyszukiwania.

2. Wybierz pozycję **Podsieci** z menu po lewej stronie, a następnie wybierz pozycję **+ Podsieć**. 

   ![Tworzenie podsieci](./media/application-gateway-create-gateway-portal/application-gateway-subnet.png)

3. Na stronie **Dodawanie podsieci** wprowadź ciąg *myBackendSubnet* w polu **Nazwa** podsieci, a następnie wybierz przycisk **OK**.

### <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. W witrynie Azure Portal wybierz pozycję **Utwórz zasób**. Zostanie wyświetlone okno **Nowe**.
2. Wybierz pozycję **Compute**, a następnie z wybierz pozycję **Windows Server 2016 Datacenter** z listy **Polecane**. Zostanie wyświetlona strona **Tworzenie maszyny wirtualnej**.<br>Usługa Application Gateway może kierować ruchem do dowolnego typu maszyny wirtualnej używane w puli zaplecza. W tym przykładzie należy użyć systemu Windows Server 2016 Datacenter.
3. Wprowadź następujące wartości na karcie **Podstawy** dla poniższych ustawień maszyny wirtualnej:
   - **Grupa zasobów**: wybierz **myResourceGroupAG** jako grupę zasobów.
   - **Nazwa maszyny wirtualnej**: wprowadź *myVM* jako nazwę maszyny wirtualnej.
   - **Nazwa użytkownika**: wprowadź *azureuser* jako nazwę użytkownika administratora.
   - **Hasło**: wprowadź *Azure123456!* jako hasło administratora.
4. Zaakceptuj pozostałe wartości domyślne, a następnie wybierz pozycję **Dalej: Dyski**.  
5. Zaakceptuj wartości domyślne na karcie **Dyski**, a następnie wybierz pozycję **Dalej: Sieć**.
6. Na karcie **Sieć** sprawdź, czy wybrano pozycję **myVNet** w obszarze **Sieć wirtualna** oraz czy pozycja **Podsieć** została ustawiona na wartość **myBackendSubnet**. Zaakceptuj pozostałe wartości domyślne, a następnie wybierz pozycję **Dalej: Zarządzanie**.<br>Usługa Application Gateway może komunikować się z wystąpieniami poza siecią wirtualną, która znajduje się w, ale należy upewnić się, że istnieje łączność adresów IP. 
7. Na karcie **Zarządzanie** ustaw pozycję **Diagnostyka rozruchu** na **Wył.** Zaakceptuj pozostałe wartości domyślne, a następnie wybierz pozycję **Przeglądanie + tworzenie**.
8. Na karcie **Przeglądanie + tworzenie** przejrzyj ustawienia, usuń wszystkie błędy walidacji, a następnie wybierz pozycję **Utwórz**.
9. Poczekaj na ukończenie tworzenia maszyny wirtualnej, zanim przejdziesz dalej.

### <a name="install-iis-for-testing"></a>Instalowanie usług IIS na potrzeby testowania

W tym przykładzie firma Microsoft IIS jest instalowany na maszynach wirtualnych tylko w celu sprawdzenia, że pomyślnie utworzono Azure application gateway. 

1. Otwórz program [Azure PowerShell](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell). W tym celu wybierz pozycję **Cloud Shell** na górnym pasku nawigacyjnym w witrynie Azure Portal, a następnie wybierz pozycję **PowerShell** z listy rozwijanej. 

   ![Instalowanie rozszerzenia niestandardowego](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

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

3. Utwórz drugą maszynę wirtualną i zainstaluj usługi IIS, wykonując kroki ukończone wcześniej. Użyj *myVM2* dla nazwy maszyny wirtualnej i **VMName** ustawienie **AzVMExtension zestaw** polecenia cmdlet.

### <a name="add-backend-servers-to-backend-pool"></a>Dodawanie serwerów wewnętrznej bazy danych do puli zaplecza

1. Wybierz pozycję **Wszystkie zasoby**, a następnie wybierz pozycję **myAppGateway**.

2. Wybierz pozycję **Pule zaplecza** w menu po lewej stronie. Platforma Azure automatycznie utworzyła pulę domyślną, **appGatewayBackendPool**, podczas tworzenia bramy aplikacji. 

3. Wybierz pozycję **appGatewayBackendPool**.

4. W obszarze **Cele** wybierz pozycję **Maszyna wirtualna** z listy rozwijanej.

5. W obszarach **MASZYNA WIRTUALNA** i **INTERFEJSY SIECIOWE** wybierz maszyny wirtualne **myVM** i **myVM2** oraz ich skojarzone interfejsy sieciowe z list rozwijanych.

   ![Dodawanie serwerów zaplecza](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Wybierz pozycję **Zapisz**.

## <a name="create-a-storage-account-and-configure-diagnostics"></a>Tworzenie konta magazynu i konfigurowanie diagnostyki

### <a name="create-a-storage-account"></a>Tworzenie konta magazynu

W tym samouczku brama aplikacji używa konta magazynu do przechowywania danych na potrzeby wykrywania i zapobiegania. Do rejestrowania danych można też użyć dzienników usługi Azure Monitor lub usługi Event Hub.

1. Kliknij przycisk **New** w lewym górnym rogu witryny Azure portal.
2. Wybierz pozycję **Magazyn**, a następnie wybierz pozycję **Konto magazynu — obiekt blob, plik, tabela, kolejka**.
3. Wprowadź nazwę konta magazynu, wybierz **Użyj istniejącej** dla grupy zasobów, a następnie wybierz **myResourceGroupAG**. W tym przykładzie nazwa konta magazynu jest *myagstore1*. Zaakceptuj wartości domyślne dla pozostałych ustawień, a następnie kliknij przycisk **Utwórz**.

### <a name="configure-diagnostics"></a>Konfigurowanie diagnostyki

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

Mimo że zainstalowanie usług IIS nie jest wymagane do utworzenia bramy aplikacji, zainstalowano je w ramach tego przewodnika Szybki start, aby sprawdzić, czy platforma Azure pomyślnie utworzyła bramę aplikacji. Użyj usług do przetestowania bramy aplikacji:

1. Znajdź publiczny adres IP dla bramy aplikacji na jego **Przegląd** strony.![ Zapisz publiczny adres IP bramy aplikacji](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png)Alternatywnie, można wybrać **wszystkie zasoby**, wprowadź *myAGPublicIPAddress* do wyszukiwania, a następnie wybierz w wyszukiwaniu wyniki. Platforma Azure wyświetla publiczny adres IP na stronie **Omówienie**.
2. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki.
3. Sprawdź odpowiedzi. Prawidłowe odpowiedzi sprawdza, czy brama aplikacji został pomyślnie utworzony, i jest w stanie pomyślnym nawiązaniu połączenia z zapleczem.![Testowanie bramy aplikacji](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie potrzebujesz już zasobów utworzonych za pomocą bramy aplikacji, usuń grupę zasobów. Usuwając grupę zasobów, usuwasz również bramę aplikacji i wszystkie powiązane z nią zasoby. 

Aby usunąć grupę zasobów:

1. W menu po lewej stronie witryny Azure Portal wybierz pozycję **Grupy zasobów**.
2. Na stronie **Grupy zasobów** wyszukaj pozycję **myResourceGroupAG** na liście i wybierz ją.
3. Na **stronie grupy zasobów** wybierz pozycję **Usuń grupę zasobów**.
4. Wprowadź ciąg *myResourceGroupAG* w polu **WPISZ NAZWĘ GRUPY ZASOBÓW**, a następnie wybierz pozycję **Usuń**

## <a name="next-steps"></a>Kolejne kroki

W tym artykule zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie bramy aplikacji z włączoną zaporą aplikacji internetowych
> * Tworzenie maszyn wirtualnych, używane jako serwery zaplecza
> * Tworzenie konta magazynu i konfigurowanie diagnostyki

Aby dowiedzieć się więcej na temat bramy aplikacji i skojarzonych z nimi zasobów, przejdź do artykuły z poradami.
