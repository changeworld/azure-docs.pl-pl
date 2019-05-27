---
title: Konfigurowanie usługi Azure Application Gateway przy użyciu prywatnego adresu IP frontonu
description: Ten artykuł zawiera informacje na temat sposobu konfigurowania bramy aplikacji przy użyciu prywatnego adresu IP frontonu
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/26/2019
ms.author: absha
ms.openlocfilehash: cfc63349e20aa6dbef4e0d31e81842d325bd3ec6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66134627"
---
# <a name="configure-an-application-gateway-with-an-internal-load-balancer-ilb-endpoint"></a>Konfigurowanie bramy aplikacji z punktem końcowym usługi wewnętrznego load balancer (ILB)

Usługa Azure Application Gateway można skonfigurować z wirtualnym adresem IP dostępnym z Internetu lub za pomocą wewnętrznego punktu końcowego nie jest połączone z Internetem (przy użyciu prywatnego adresu IP dla adresu IP frontonu), nazywany też wewnętrznego modułu równoważenia obciążenia (ILB) w punkcie końcowym. Konfigurowanie bramy przy użyciu prywatnego adresu IP frontonu jest przydatne w przypadku wewnętrznych aplikacji line-of-business, które nie są połączone z Internetem. Ta opcja jest również przydatna w przypadku usług i warstw w aplikacji wielowarstwowej, która znajduje się w granicach zabezpieczeń bez połączenia z Internetem, ale nadal wymaga dystrybucji obciążenia z działaniem okrężnym, lepkości sesji lub zakończenia protokołu SSL (Secure Sockets Layer).

W tym artykule przedstawiono kroki, aby skonfigurować bramę aplikacji przy użyciu prywatnego adresu IP frontonu z witryny Azure Portal.

W tym artykule dowiesz się jak:

- Utwórz konfigurację adresu IP frontonu prywatne dla bramy aplikacji
- Tworzenie bramy aplikacji przy użyciu konfiguracji adresu IP frontonu prywatne


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do witryny Azure Portal pod adresem <https://portal.azure.com>

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

Do komunikacji między tworzonymi zasobami platforma Azure potrzebuje sieci wirtualnej. Możesz utworzyć nową sieć wirtualną lub użyć istniejącego. W tym przykładzie zostanie utworzona nowa sieć wirtualna. Sieć wirtualną można utworzyć podczas tworzenia bramy aplikacji. Wystąpienia bramy aplikacji są tworzone w różnych podsieciach. W tym przykładzie tworzysz dwie podsieci: jedną dla bramy aplikacji i drugą dla serwerów zaplecza.

1. Kliknij przycisk **New** w lewym górnym rogu witryny Azure portal.
2. Wybierz pozycję **Sieć**, a następnie z listy Polecane wybierz pozycję **Application Gateway**.
3. Wprowadź *myAppGateway* dla nazwy usługi application gateway i *myResourceGroupAG* dla nowej grupy zasobów.
4. Zaakceptuj wartości domyślne dla innych ustawień, a następnie kliknij przycisk **OK**.
5. Kliknij kolejno pozycje **Wybierz sieć wirtualną**, **Utwórz nową**, a następnie wprowadź następujące wartości dla sieci wirtualnej:
   - myVNet * - dla nazwy sieci wirtualnej.
   - 10.0.0.0/16* — do przestrzeni adresowej sieci wirtualnej.
   - *myAGSubnet* — jako nazwę podsieci.
   - *10.0.0.0/24* — jako przestrzeń adresową podsieci.  
     ![private-frontendip-1](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-1.png)
6. Kliknij przycisk **OK**, aby utworzyć sieć wirtualną i podsieć.
7. Wybierz konfigurację adresu IP frontonu, prywatnych i domyślnie jest dynamiczne przypisywanie adresów IP. Pierwszy dostępny adres wybrana podsieć zostanie przypisany jako adres IP frontonu.
8. Jeśli chcesz wybrać prywatny adres IP z zakresu adresów podsieci (alokacji statycznej), kliknij pole **wybierz konkretny prywatny adres IP** i określ adres IP.
   > [!NOTE]
   > Po przydzieleniu, typ adresu IP (statyczne lub dynamiczne) nie można zmienić później.
9. Wybierz konfigurację odbiornika protokołu i portu, konfiguracji zapory aplikacji sieci Web (jeśli jest to konieczne), a następnie kliknij przycisk OK.
    ![private-frontendip-2](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-2.png)
10. Przejrzyj ustawienia na stronie podsumowania, a następnie kliknij przycisk **OK** do tworzenia zasobów sieciowych i bramy aplikacji. Może upłynąć kilka minut w usłudze application gateway można utworzyć, poczekaj na wdrożenie zakończy się pomyślnie przed przejściem do następnej sekcji.

## <a name="add-backend-pool"></a>Dodaj pulę zaplecza

Pula zaplecza jest używany do kierowania żądań do serwerów wewnętrznej bazy danych, które będą obsługująca żądanie. Zaplecze może składać się z kartami sieciowymi, zestawy skalowania maszyn wirtualnych, publiczne adresy IP, wewnętrzne adresy IP, w pełni kwalifikowaną nazwę (FQDN), a wielodostępne zaplecza takich jak usługa Azure App Service. W tym przykładzie użyjemy maszyny wirtualne jako docelowego w wewnętrznej bazie danych. Możemy użyć istniejących maszyn wirtualnych lub utworzyć nowe. W tym przykładzie utworzymy dwie maszyny wirtualne, które platforma Azure używa jako serwery zaplecza w usłudze application gateway. Aby to zrobić, obejmuje następujące czynności:

1. Tworzenie nowych maszyn wirtualnych 2, *myVM* i *myVM2*, który zostanie użyty jako serwery zaplecza.
2. Zainstaluj usługi IIS na maszynach wirtualnych, aby sprawdzić, czy brama aplikacji została pomyślnie utworzona.
3. Dodawanie serwerów wewnętrznej bazy danych do puli zaplecza.

### <a name="create-a-virtual-machine"></a>Utwórz maszynę wirtualną

1. Kliknij przycisk **Nowy**.
2. Kliknij pozycję **Compute** (Wystąpienia obliczeniowe), a następnie z listy Polecane wybierz pozycję **Windows Server 2016 Datacenter**.
3. Wprowadź poniższe wartości dla maszyny wirtualnej:
   - *myVM* — jako nazwę maszyny wirtualnej.
   - *azureuser* — jako nazwę użytkownika administratora.
   - *Azure123456!* jako hasło.
   - Wybierz pozycję **Użyj istniejącej** i wybierz grupę *myResourceGroupAG*.
4. Kliknij przycisk **OK**.
5. Wybierz **DS1_V2** dla rozmiaru maszyny wirtualnej i kliknij przycisk **wybierz**.
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
