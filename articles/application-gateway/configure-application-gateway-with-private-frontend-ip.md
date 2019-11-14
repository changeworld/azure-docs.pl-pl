---
title: Konfigurowanie punktu końcowego wewnętrznego modułu równoważenia obciążenia (ILB)
titleSuffix: Azure Application Gateway
description: Ten artykuł zawiera informacje dotyczące sposobu konfigurowania Application Gateway przy użyciu prywatnego adresu IP frontonu
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: a9e3150a5382e4d690ddf66c43bbe51e125509d3
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075216"
---
# <a name="configure-an-application-gateway-with-an-internal-load-balancer-ilb-endpoint"></a>Konfigurowanie bramy aplikacji za pomocą punktu końcowego wewnętrznego modułu równoważenia obciążenia (ILB)

Usługę Azure Application Gateway można skonfigurować za pomocą internetowego adresu IP lub wewnętrznego punktu końcowego, który nie jest narażony na Internet (przy użyciu prywatnego adresu IP dla adresu IP frontonu), znanego również jako punkt końcowy wewnętrznego modułu równoważenia obciążenia (ILB). Konfigurowanie bramy przy użyciu prywatnego adresu IP frontonu jest przydatne w przypadku wewnętrznych aplikacji biznesowych, które nie są dostępne w Internecie. Ta opcja jest również przydatna w przypadku usług i warstw w aplikacji wielowarstwowej, która znajduje się w granicach zabezpieczeń bez połączenia z Internetem, ale nadal wymaga dystrybucji obciążenia z działaniem okrężnym, lepkości sesji lub zakończenia protokołu SSL (Secure Sockets Layer).

W tym artykule przedstawiono kroki konfigurowania bramy aplikacji przy użyciu prywatnego adresu IP frontonu w witrynie Azure Portal.

W tym artykule dowiesz się jak:

- Utwórz prywatną konfigurację adresu IP frontonu dla Application Gateway
- Tworzenie bramy aplikacji z konfiguracją prywatnego adresu IP frontonu


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do witryny Azure Portal pod adresem <https://portal.azure.com>

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

Do komunikacji między tworzonymi zasobami platforma Azure potrzebuje sieci wirtualnej. Można utworzyć nową sieć wirtualną lub użyć istniejącej. W tym przykładzie utworzymy nową sieć wirtualną. Sieć wirtualną można utworzyć podczas tworzenia bramy aplikacji. Wystąpienia Application Gateway są tworzone w różnych podsieciach. W tym przykładzie tworzysz dwie podsieci: jedną dla bramy aplikacji i drugą dla serwerów zaplecza.

1. Kliknij przycisk **Nowy** znajdujący się w lewym górnym rogu Azure Portal.
2. Wybierz pozycję **Sieć**, a następnie z listy Polecane wybierz pozycję **Application Gateway**.
3. Wprowadź *myAppGateway* dla nazwy bramy aplikacji i *myResourceGroupAG* dla nowej grupy zasobów.
4. Zaakceptuj wartości domyślne dla innych ustawień, a następnie kliknij przycisk **OK**.
5. Kliknij kolejno pozycje **Wybierz sieć wirtualną**, **Utwórz nową**, a następnie wprowadź następujące wartości dla sieci wirtualnej:
   - myVNet * — jako nazwę sieci wirtualnej.
   - 10.0.0.0/16 * — dla przestrzeni adresów sieci wirtualnej.
   - *myAGSubnet* — jako nazwę podsieci.
   - *10.0.0.0/24* — jako przestrzeń adresową podsieci.  
     ![private-frontendip-1](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-1.png)
6. Kliknij przycisk **OK**, aby utworzyć sieć wirtualną i podsieć.
7. Wybierz konfigurację adresów IP frontonu jako prywatną i domyślnie jest to przypisanie dynamicznego adresu IP. Pierwszy dostępny adres wybranej podsieci zostanie przypisany jako adres IP frontonu.
8. Jeśli chcesz wybrać prywatny adres IP z zakresu adresów podsieci (alokacja statyczna), kliknij pole **wyboru wybierz określony prywatny** i określ adres IP.
   > [!NOTE]
   > Po przydzieleniu nie można później zmienić typu adresu IP (statycznego lub dynamicznego).
9. Wybierz konfigurację odbiornika dla protokołu i portu, WAF konfigurację (w razie potrzeby), a następnie kliknij przycisk OK.
    ![private-frontendip-2](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-2.png)
10. Przejrzyj ustawienia na stronie Podsumowanie, a następnie kliknij przycisk **OK** , aby utworzyć zasoby sieciowe i bramę aplikacji. Może upłynąć kilka minut w usłudze application gateway można utworzyć, poczekaj na wdrożenie zakończy się pomyślnie przed przejściem do następnej sekcji.

## <a name="add-backend-pool"></a>Dodawanie puli zaplecza

Pula zaplecza służy do kierowania żądań do serwerów zaplecza, które będą obsługiwać żądania. Zaplecze może składać się z kart sieciowych, zestawów skalowania maszyn wirtualnych, publicznych adresów IP, wewnętrznych adresów IP, w pełni kwalifikowanych nazw domen (FQDN) i wielodostępnych zaplecza, takich jak Azure App Service. W tym przykładzie będziemy używać maszyn wirtualnych jako zaplecza docelowego. Możemy użyć istniejących maszyn wirtualnych lub utworzyć nowe. W tym przykładzie utworzymy dwie maszyny wirtualne używane przez platformę Azure jako serwery zaplecza dla bramy aplikacji. W tym celu będziemy:

1. Utwórz 2 nowe maszyny wirtualne, *myVM* i *myVM2*, które mają być używane jako serwery zaplecza.
2. Zainstaluj usługi IIS na maszynach wirtualnych, aby sprawdzić, czy Brama aplikacji została utworzona pomyślnie.
3. Dodaj serwery zaplecza do puli zaplecza.

### <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. Kliknij przycisk **Nowy**.
2. Kliknij pozycję **Compute** (Wystąpienia obliczeniowe), a następnie z listy Polecane wybierz pozycję **Windows Server 2016 Datacenter**.
3. Wprowadź poniższe wartości dla maszyny wirtualnej:
   - *myVM* — jako nazwę maszyny wirtualnej.
   - *azureuser* — jako nazwę użytkownika administratora.
   - *Azure123456!* jako hasło.
   - Wybierz pozycję **Użyj istniejącej** i wybierz grupę *myResourceGroupAG*.
4. Kliknij przycisk **OK**.
5. Wybierz **DS1_V2** rozmiaru maszyny wirtualnej, a następnie kliknij przycisk **Wybierz**.
6. Upewnij się, że wybrano sieć wirtualną **myVNet** i podsieć **myBackendSubnet**.
7. Kliknij pozycję **Wyłączone**, aby wyłączyć diagnostykę rozruchu.
8. Kliknij przycisk **OK**, przejrzyj ustawienia na stronie podsumowania, a następnie kliknij przycisk **Utwórz**.

### <a name="install-iis"></a>Instalowanie usług IIS

1. Otwórz powłokę interaktywną i upewnij się, że jest ustawiona na program **PowerShell**.
    ![private-frontendip-3](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-3.png)
2. Uruchom następujące polecenie, aby zainstalować usługi IIS na maszynie wirtualnej:

   ```azurepowershell
   Set-AzVMExtension `
   
     -ResourceGroupName myResourceGroupAG `
   
     -ExtensionName IIS `
   
     -VMName myVM `
   
     -Publisher Microsoft.Compute `
   
     -ExtensionType CustomScriptExtension `
   
     -TypeHandlerVersion 1.4 `
   
     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' -Location EastUS  ```



3. Create a second virtual machine and install IIS using the steps that you just finished. Enter myVM2 for its name and for VMName in Set-AzVMExtension.

### Add backend servers to backend pool

1. Click **All resources**, and then click **myAppGateway**.
2. Click **Backend pools**. A default pool was automatically created with the application gateway. Click **appGatewayBackendPool**.
3. Click **Add target** to add each virtual machine that you created to the backend pool.
   ![private-frontendip-4](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-4.png)
4. Click **Save.**

## Test the application gateway

1. Check your frontend IP that got assigned by clicking the **Frontend IP Configurations** blade in the portal.
    ![private-frontendip-5](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-5.png)
2. Copy the private IP address, and then paste it into the address bar of your browser of a VM in the same VNet or on-premises which has connectivity to this VNet and try to access the Application Gateway.

## Next steps

In this tutorial, you learned how to:

- Create a private frontend IP configuration for an Application Gateway
- Create an application gateway with private frontend IP configuration

If you want to monitor the health of your backend, see [Application Gateway Diagnostics](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).
