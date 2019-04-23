---
title: Samouczek — Tworzenie bramy aplikacji, który hostuje wiele witryn sieci web przy użyciu witryny Azure portal
description: W tym samouczku dowiesz się, jak utworzyć bramę aplikacji, który hostuje wiele witryn sieci web przy użyciu witryny Azure portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 4/18/2019
ms.author: victorh
ms.openlocfilehash: 3e27a79c7a6e3d39679118f532dd464a32463d69
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "59999029"
---
# <a name="tutorial-create-and-configure-an-application-gateway-to-host-multiple-web-sites-using-the-azure-portal"></a>Samouczek: Tworzenie i konfigurowanie bramy aplikacji do hostowania wielu witryn sieci web przy użyciu witryny Azure portal

Można użyć portalu Azure w celu [Konfigurowanie hostingu wielu witryn sieci web](multiple-site-overview.md) po utworzeniu [bramy application gateway](overview.md). W tym samouczku zdefiniujesz pule adresów zaplecza przy użyciu maszyn wirtualnych. Następnie, bazując na należących do Ciebie domenach, skonfigurujesz odbiorniki i reguły, aby się upewnić, że ruch internetowy dociera do odpowiednich serwerów w pulach. W tym samouczku przyjęto założenie, że jesteś właścicielem wielu domen, przykładami których są *www.contoso.com* i *www.fabrikam.com*.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie bramy aplikacji
> * Tworzenie maszyn wirtualnych dla serwerów wewnętrznej bazy danych
> * Utwórz pule zaplecza z serwerami wewnętrznej bazy danych
> * Tworzenie odbiorników zaplecza
> * Tworzenie reguł routingu
> * Tworzenie rekordu CNAME w domenie

![Przykład routingu obejmujący wiele witryn](./media/create-multiple-sites-portal/scenario.png)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com)

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

Sieć wirtualna jest potrzebna do komunikacji między utworzonymi zasobami. W tym przykładzie są tworzone dwie podsieci: jedna dla bramy aplikacji i druga dla serwerów zaplecza. Sieć wirtualną można utworzyć podczas tworzenia bramy aplikacji.

1. Kliknij przycisk **New** w lewym górnym rogu witryny Azure portal.
2. Wybierz pozycję **Sieć**, a następnie z listy Polecane wybierz pozycję **Application Gateway**.
3. Wprowadź następujące wartości dla bramy aplikacji:

   - *myAppGateway* — jako nazwę bramy aplikacji.
   - *myResourceGroupAG* — jako nową grupę zasobów.

     ![Tworzenie nowej bramy aplikacji](./media/create-multiple-sites-portal/application-gateway-create.png)

4. Zaakceptuj wartości domyślne dla innych ustawień, a następnie kliknij przycisk **OK**.
5. Kliknij kolejno pozycje **Wybierz sieć wirtualną**, **Utwórz nową**, a następnie wprowadź następujące wartości dla sieci wirtualnej:

   - *myVNet* — jako nazwę sieci wirtualnej.
   - *10.0.0.0/16* — jako przestrzeń adresową sieci wirtualnej.
   - *myAGSubnet* — jako nazwę podsieci.
   - *10.0.0.0/24* — jako przestrzeń adresową podsieci.

     ![Tworzenie sieci wirtualnej](./media/create-multiple-sites-portal/application-gateway-vnet.png)

6. Kliknij przycisk **OK**, aby utworzyć sieć wirtualną i podsieć.
7. Kliknij przycisk **wybierz publiczny adres IP**, kliknij przycisk **Utwórz nową**, a następnie wprowadź nazwę publicznego adresu IP. W tym przykładzie publiczny adres IP nosi nazwę *myAGPublicIPAddress*. Zaakceptuj wartości domyślne dla innych ustawień, a następnie kliknij przycisk **OK**.
8. Zaakceptuj wartości domyślne dla konfiguracji odbiornika, pozostaw zapory aplikacji sieci Web, które są wyłączone, a następnie kliknij przycisk **OK**.
9. Przejrzyj ustawienia na stronie podsumowania, a następnie kliknij przycisk **OK** do tworzenia zasobów sieciowych i bramy aplikacji. Może upłynąć kilka minut w usłudze application gateway można utworzyć, poczekaj na wdrożenie zakończy się pomyślnie przed przejściem do następnej sekcji.

### <a name="add-a-subnet"></a>Dodawanie podsieci

1. W menu po lewej stronie kliknij pozycję **Wszystkie zasoby**, a następnie na liście zasobów kliknij pozycję **myVNet**.
2. Kliknij przycisk **podsieci**, a następnie kliknij przycisk **podsieci**.

    ![Tworzenie podsieci](./media/create-multiple-sites-portal/application-gateway-subnet.png)

3. Wprowadź *myBackendSubnet* jako nazwę podsieci, a następnie kliknij przycisk **OK**.

## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

W tym przykładzie utworzysz dwie maszyny wirtualne, które będą używane jako serwery zaplecza dla bramy aplikacji. Możesz także zainstalować usługi IIS na maszynach wirtualnych, aby sprawdzić, poprawnie routingu ruchu.

1. Kliknij przycisk **Nowy**.
2. Kliknij pozycję **Compute** (Wystąpienia obliczeniowe), a następnie z listy Polecane wybierz pozycję **Windows Server 2016 Datacenter**.
3. Wprowadź poniższe wartości dla maszyny wirtualnej:

    - *contosoVM* — jako nazwę maszyny wirtualnej.
    - *azureuser* — jako nazwę użytkownika administratora.
    - *Azure123456!* jako hasło.
    - Wybierz pozycję **Użyj istniejącej** i wybierz grupę *myResourceGroupAG*.

4. Kliknij przycisk **OK**.
5. Wybierz **DS1_V2** jako rozmiar maszyny wirtualnej, a następnie kliknij pozycję **Wybierz**.
6. Upewnij się, że wybrano sieć wirtualną **myVNet** i podsieć **myBackendSubnet**. 
7. Kliknij pozycję **Wyłączone**, aby wyłączyć diagnostykę rozruchu.
8. Kliknij przycisk **OK**, przejrzyj ustawienia na stronie podsumowania, a następnie kliknij przycisk **Utwórz**.

### <a name="install-iis"></a>Instalowanie usług IIS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Otwórz powłokę interaktywną i upewnij się, że jest ustawiona na program **PowerShell**.

    ![Instalowanie rozszerzenia niestandardowego](./media/create-multiple-sites-portal/application-gateway-extension.png)

2. Uruchom następujące polecenie, aby zainstalować usługi IIS na maszynie wirtualnej: 

    ```azurepowershell-interactive
    $publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1");  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -Location eastus `
      -ExtensionName IIS `
      -VMName contosoVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -Settings $publicSettings
    ```

3. Tworzenie drugiej maszyny wirtualnej i instalowanie usług IIS przy użyciu czynności, które właśnie zostało zakończone. Wprowadź nazwy *fabrikamVM* dla nazwy i wartości VMName w AzVMExtension zestawu.

## <a name="create-backend-pools-with-the-virtual-machines"></a>Tworzenie puli wewnętrznej bazy danych z maszynami wirtualnymi

1. Kliknij przycisk **wszystkie zasoby** a następnie kliknij przycisk **myAppGateway**.
2. Kliknij przycisk **pule zaplecza**, a następnie kliknij przycisk **Dodaj**.
3. Wprowadź nazwę *contosoPool* i Dodaj *contosoVM* przy użyciu **Add target**.

    ![Dodawanie serwerów zaplecza](./media/create-multiple-sites-portal/application-gateway-multisite-backendpool.png)

4. Kliknij przycisk **OK**.
5. Kliknij przycisk **pule zaplecza** a następnie kliknij przycisk **Dodaj**.
6. Tworzenie *fabrikamPool* z *fabrikamVM* wykonując kroki, które właśnie zostało zakończone.

## <a name="create-backend-listeners"></a>Tworzenie odbiorników zaplecza

1. Kliknij przycisk **odbiorników** a następnie kliknij przycisk **obejmujące wiele lokacji**.
2. Wprowadź następujące wartości dla odbiornika:
    
   - *contosoListener* — jako nazwę odbiornika.
   - *www.contoso.com* — w tym przykładzie nazwa hosta Zamień na nazwę domeny.

3. Kliknij przycisk **OK**.
4. Utwórz odbiornik drugi przy użyciu nazwy *fabrikamListener* i korzystania z drugiego nazwy domeny. W tym przykładzie *www.fabrikam.com* jest używany.

![odbiorniki iloczyn lokacji](media/create-multiple-sites-portal/be-listeners.png)

## <a name="create-routing-rules"></a>Tworzenie reguł routingu

Reguły są przetwarzane w kolejności, w jakiej są wymienione, a ruch jest przekierowywany przy użyciu pierwszej zgodnej reguły niezależnie od specyficzności. Na przykład jeśli na tym samym porcie utworzono dwie reguły: jedną przy użyciu odbiornika podstawowego, a drugą przy użyciu odbiornika obejmującego wiele witryn, reguła z odbiornikiem obejmującym wiele witryn musi znajdować się przed regułą z odbiornikiem podstawowym, aby funkcja reguły obejmującej wiele witryn działała zgodnie z oczekiwaniami. 

W tym przykładzie utworzenie dwóch nowych reguł i usunąć domyślnej reguły utworzone podczas tworzenia bramy aplikacji.

1. Kliknij przycisk **reguły** a następnie kliknij przycisk **podstawowe**.
2. Wprowadź *contosoRule* dla nazwy.
3. Wybierz *contosoListener* dla odbiornika.
4. Wybierz *contosoPool* dla puli zaplecza.

    ![Utwórz regułę bazującą na ścieżce](./media/create-multiple-sites-portal/application-gateway-multisite-rule.png)

5. Kliknij przycisk **OK**.
6. Należy utworzyć drugą regułę, przy użyciu nazwy *fabrikamRule*, *fabrikamListener*, i *fabrikamPool*.
7. Usunąć domyślnej reguły o nazwie *rule1* , klikając go, a następnie klikając **Usuń**.

## <a name="create-a-cname-record-in-your-domain"></a>Tworzenie rekordu CNAME w domenie

Po utworzeniu bramy aplikacji z publicznym adresem IP można pobrać adres DNS i użyć go w celu utworzenia rekordu CNAME w domenie. Korzystanie z rekordów A nie jest zalecane, ponieważ adres VIP może ulec zmianie po ponownym uruchomieniu bramy aplikacji.

1. Kliknij przycisk **wszystkie zasoby**, a następnie kliknij przycisk **myAGPublicIPAddress**.

    ![Bramy application gateway adres serwera DNS dla rekordu](./media/create-multiple-sites-portal/application-gateway-multisite-dns.png)

2. Skopiuj adres DNS i używać go jako wartość nowy rekord CNAME w domenie.

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

1. Wpisz nazwę swojej domeny na pasku adresu przeglądarki. Na przykład http://www.contoso.com.

    ![Testowanie witryny contoso w bramie aplikacji](./media/create-multiple-sites-portal/application-gateway-iistest.png)

2. Zmień adres na drugą domenę. Powinny zostać wyświetlone informacje, jak w następującym przykładzie:

    ![Testowanie witryny fabrikam w bramy aplikacji](./media/create-multiple-sites-portal/application-gateway-iistest2.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie potrzebujesz już zasobów utworzonych za pomocą bramy aplikacji, usuń grupę zasobów. Usuwając grupę zasobów, usuwasz również bramę aplikacji i wszystkie powiązane z nią zasoby.

Aby usunąć grupę zasobów:

1. W menu po lewej stronie witryny Azure Portal wybierz pozycję **Grupy zasobów**.
2. Na stronie **Grupy zasobów** wyszukaj pozycję **myResourceGroupAG** na liście i wybierz ją.
3. Na **stronie grupy zasobów** wybierz pozycję **Usuń grupę zasobów**.
4. Wprowadź ciąg *myResourceGroupAG* w polu **WPISZ NAZWĘ GRUPY ZASOBÓW**, a następnie wybierz pozycję **Usuń**

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o co można zrobić za pomocą usługi Azure Application Gateway](application-gateway-introduction.md)