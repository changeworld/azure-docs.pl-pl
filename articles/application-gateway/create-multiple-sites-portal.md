---
title: Utwórz bramę aplikacji, który obsługuje wiele witryn sieci web - portalu Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć bramę aplikacji, który obsługuje wiele witryn sieci web przy użyciu portalu Azure.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 12/28/2017
ms.author: victorh
ms.openlocfilehash: 9d55db498624d7f23bc499989333ba46340c43b0
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="create-and-configure-an-application-gateway-to-host-multiple-web-sites-using-the-azure-portal"></a>Utwórz i skonfiguruj bramę aplikacji do obsługi wielu witryn sieci web przy użyciu portalu Azure

Można użyć portalu Azure, aby [konfigurowania, obsługi wielu witryn sieci web](multiple-site-overview.md) podczas tworzenia [brama aplikacji w](overview.md). W tym samouczku można zdefiniować pul adresów zaplecza przy użyciu maszyn wirtualnych. Następnie skonfiguruj odbiorników i reguły na podstawie domen, do których należą do upewnij się, że ruch w sieci web dociera do odpowiednich serwerów w pulach. Ten samouczek zakłada, że masz wiele domen i używa przykłady *www.contoso.com* i *www.fabrikam.com*.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Tworzenie bramy aplikacji
> * Tworzenie maszyn wirtualnych dla serwerów wewnętrznej bazy danych
> * Tworzenie puli wewnętrznej bazy danych z serwerami wewnętrznej bazy danych
> * Tworzenie odbiorników wewnętrznej bazy danych
> * Tworzenie reguły routingu
> * Utwórz rekord CNAME w domenie

![Przykład routingu obejmujący wiele lokacji](./media/create-multiple-sites-portal/scenario.png)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do portalu Azure pod adresem [http://portal.azure.com](http://portal.azure.com)

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

Sieć wirtualna jest wymagany dla komunikacji między zasobami, które można utworzyć. Dwie podsieci są tworzone w tym przykładzie: jeden dla bramy aplikacji, a drugi dla serwerów zaplecza. W tym samym czasie utworzonego bramy aplikacji może utworzyć sieć wirtualną.

1. Kliknij przycisk **nowy** znaleziono w lewym górnym rogu portalu Azure.
2. Wybierz **sieci** , a następnie wybierz **brama aplikacji w** na liście duży.
3. Wprowadź wartości dla bramy aplikacji:

    - *myAppGateway* — nazwa bramy aplikacji.
    - *myResourceGroupAG* — dla nowej grupy zasobów.

    ![Utwórz nową bramę aplikacji](./media/create-multiple-sites-portal/application-gateway-create.png)

4. Zaakceptuj wartości domyślne dla innych ustawień, a następnie kliknij przycisk **OK**.
5. Kliknij przycisk **wybierz sieć wirtualną**, kliknij przycisk **Utwórz nowy**, a następnie wprowadź wartości dla sieci wirtualnej:

    - *myVNet* — dla nazwy sieci wirtualnej.
    - *10.0.0.0/16* — do przestrzeni adresowej sieci wirtualnej.
    - *myAGSubnet* — dla nazwy podsieci.
    - *10.0.0.0/24* — do przestrzeni adresowej podsieci.

    ![Tworzenie sieci wirtualnej](./media/create-multiple-sites-portal/application-gateway-vnet.png)

6. Kliknij przycisk **OK** do tworzenia sieci wirtualnej i podsieci.
7. Kliknij przycisk **wybierz publiczny adres IP**, kliknij przycisk **Utwórz nowy**, a następnie wprowadź nazwę publicznego adresu IP. W tym przykładzie publiczny adres IP o nazwie *myAGPublicIPAddress*. Zaakceptuj wartości domyślne dla innych ustawień, a następnie kliknij przycisk **OK**.
8. Zaakceptuj wartości domyślne w konfiguracji odbiornika, pozostaw zapory aplikacji sieci Web, które są wyłączone, a następnie kliknij **OK**.
9. Przejrzyj ustawienia na stronie Podsumowanie, a następnie kliknij przycisk **OK** tworzyć zasoby sieciowe i bramy aplikacji. Może upłynąć kilka minut dla bramy aplikacji można utworzyć, poczekaj na wdrożenie zakończy się pomyślnie przed przejściem do następnej sekcji.

### <a name="add-a-subnet"></a>Dodaj podsieć

1. Kliknij przycisk **wszystkie zasoby** w menu po lewej stronie, a następnie kliknij przycisk **myVNet** na liście zasobów.
2. Kliknij przycisk **podsieci**, a następnie kliknij przycisk **podsieci**.

    ![Tworzenie podsieci](./media/create-multiple-sites-portal/application-gateway-subnet.png)

3. Wprowadź *myBackendSubnet* dla nazwy podsieci, a następnie kliknij przycisk **OK**.

## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

W tym przykładzie utworzysz dwie maszyny wirtualne do użycia jako serwery zaplecza bramy aplikacji. Usługi IIS są także zainstalować na maszynach wirtualnych, aby sprawdzić poprawnie routingu ruchu.

1. Kliknij przycisk **Nowy**.
2. Kliknij przycisk **obliczeniowe** , a następnie wybierz **systemu Windows Server 2016 Datacenter** na liście duży.
3. Wprowadź wartości dla maszyny wirtualnej:

    - *contosoVM* — Nazwa maszyny wirtualnej.
    - *azureuser* — jako nazwę użytkownika administratora.
    - *Azure123456!* hasła.
    - Wybierz **Użyj istniejącego**, a następnie wybierz *myResourceGroupAG*.

4. Kliknij przycisk **OK**.
5. Wybierz **DS1_V2** jako rozmiar maszyny wirtualnej, a następnie kliknij pozycję **Wybierz**.
6. Upewnij się, że **myVNet** został wybrany do sieci wirtualnej i podsieci jest **myBackendSubnet**. 
7. Kliknij pozycję **Wyłączone**, aby wyłączyć diagnostykę rozruchu.
8. Kliknij przycisk **OK**, przejrzyj ustawienia na stronie podsumowania, a następnie kliknij przycisk **Utwórz**.

### <a name="install-iis"></a>Instalowanie usług IIS

1. Otwórz powłokę interakcyjne i upewnij się, że jest ustawiona na **PowerShell**.

    ![Zainstaluj rozszerzenia niestandardowego](./media/create-multiple-sites-portal/application-gateway-extension.png)

2. Uruchom następujące polecenie, aby zainstalować usługi IIS na maszynie wirtualnej: 

    ```azurepowershell-interactive
    $publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/vhorne/samplescripts/master/appgatewayurl.ps1");  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
    Set-AzureRmVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -Location eastus `
      -ExtensionName IIS `
      -VMName contosoVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -Settings $publicSettings
    ```

3. Tworzenie drugiej maszyny wirtualnej i zainstaluj usługi IIS, wykonując kroki, które właśnie zostało zakończone. Wprowadź nazwy *fabrikamVM* dla nazwy i wartości VMName w AzureRmVMExtension zestawu.

## <a name="create-backend-pools-with-the-virtual-machines"></a>Tworzenie puli wewnętrznej bazy danych z maszynami wirtualnymi

1. Kliknij przycisk **wszystkie zasoby** , a następnie kliknij przycisk **myAppGateway**.
2. Kliknij przycisk **pul zaplecza**, a następnie kliknij przycisk **Dodaj**.
3. Wprowadź nazwę *contosoPool* i Dodaj *contosoVM* przy użyciu **docelowy Dodaj**.

    ![Dodawanie serwerów wewnętrznej bazy danych](./media/create-multiple-sites-portal/application-gateway-multisite-backendpool.png)

4. Kliknij przycisk **OK**.
5. Kliknij przycisk **pul zaplecza** , a następnie kliknij przycisk **Dodaj**.
6. Utwórz *fabrikamPool* z *fabrikamVM* przy użyciu kroków, które właśnie zostało zakończone.

## <a name="create-backend-listeners"></a>Tworzenie odbiorników wewnętrznej bazy danych

1. Kliknij przycisk **odbiorników** , a następnie kliknij przycisk **obejmujący wiele lokacji**.
2. Wprowadź wartości dla odbiornika:
    
    - *contosoListener* — w przypadku nazwę odbiornika.
    - *www.contoso.com* — w tym przykładzie nazwa hosta Zamień na nazwę domeny.

3. Kliknij przycisk **OK**.
4. Utwórz odbiornik drugi przy użyciu nazwy *fabrikamListener* i korzystać z drugiego nazwy domeny. W tym przykładzie *www.fabrikam.com* jest używany.

## <a name="create-routing-rules"></a>Tworzenie reguły routingu

Reguły są przetwarzane w kolejności są wyświetlane, a ruch jest przekierowywany przy użyciu pierwszej reguły, odpowiadający niezależnie od szczegółowością. Na przykład jeśli utworzono regułę przy użyciu odbiornika podstawowe i regułę przy użyciu odbiornika obejmujący wiele lokacji zarówno w tym samym porcie, reguły z wieloma lokacjami odbiornika musi być wymieniona przed regułę przy użyciu podstawowego odbiornika w kolejności reguły obejmujący wiele lokacji, aby działać zgodnie z oczekiwaniami. 

W tym przykładzie utworzenie dwóch nowych reguł i usunąć domyślnej reguły, który został utworzony podczas tworzenia bramy aplikacji. 

1. Kliknij przycisk **reguły** , a następnie kliknij przycisk **podstawowe**.
2. Wprowadź *contosoRule* dla nazwy.
3. Wybierz *contosoListener* dla odbiornika.
4. Wybierz *contosoPool* puli wewnętrznej bazy danych.

    ![Tworzenie reguły na podstawie ścieżki](./media/create-multiple-sites-portal/application-gateway-multisite-rule.png)

5. Kliknij przycisk **OK**.
6. Należy utworzyć drugą regułę, przy użyciu nazwy *fabrikamRule*, *fabrikamListener*, i *fabrikamPool*.
7. Usunąć domyślnej reguły o nazwie *rule1* przez kliknięcie go, a następnie klikając **usunąć**.

## <a name="create-a-cname-record-in-your-domain"></a>Utwórz rekord CNAME w domenie

Po utworzeniu bramy aplikacji z publicznego adresu IP można pobrać adresu DNS i użyj go, aby utworzyć rekord CNAME w domenie. Użycie rekordów A nie jest zalecane, ponieważ adres VIP może ulec zmianie po ponownym uruchomieniu bramy aplikacji.

1. Kliknij przycisk **wszystkie zasoby**, a następnie kliknij przycisk **myAGPublicIPAddress**.

    ![Brama aplikacji w rekordu adresu DNS](./media/create-multiple-sites-portal/application-gateway-multisite-dns.png)

2. Skopiuj adres DNS i używać go jako wartość nowy rekord CNAME w domenie.

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

1. Wpisz nazwę domeny na pasku adresu przeglądarki. Takie jak http://www.contoso.com.

    ![Lokacja contoso testu bramy aplikacji](./media/create-multiple-sites-portal/application-gateway-iistest.png)

2. Zmienić adres na inne domeny i powinny zostać wyświetlone informacje, jak w następującym przykładzie:

    ![Testowanie witryny firmy fabrikam w bramy aplikacji](./media/create-multiple-sites-portal/application-gateway-iistest2.png)

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono sposób:

> [!div class="checklist"]
> * Tworzenie bramy aplikacji
> * Tworzenie maszyn wirtualnych dla serwerów wewnętrznej bazy danych
> * Tworzenie puli wewnętrznej bazy danych z serwerami wewnętrznej bazy danych
> * Tworzenie odbiorników wewnętrznej bazy danych
> * Tworzenie reguły routingu
> * Utwórz rekord CNAME w domenie
