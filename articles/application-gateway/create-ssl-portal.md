---
title: Utwórz bramę aplikacji z kończenia żądań SSL - portalu Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć bramę aplikacji i Dodaj certyfikat dla zakończenia połączenia SSL przy użyciu portalu Azure.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/26/2018
ms.author: victorh
ms.openlocfilehash: a24cec5dfa8034cf43ce7cfe26308587cf20dc44
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
---
# <a name="create-an-application-gateway-with-ssl-termination-using-the-azure-portal"></a>Utwórz bramę aplikacji z kończenia żądań SSL przy użyciu portalu Azure

Azure portal umożliwia tworzenie [brama aplikacji w](overview.md) certyfikat dla zakończenia połączenia SSL, używającego maszyn wirtualnych dla serwerów wewnętrznej bazy danych.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Utwórz certyfikat z podpisem własnym
> * Utwórz bramę aplikacji przy użyciu certyfikatu
> * Tworzenie maszyn wirtualnych, używane jako serwery wewnętrznej bazy danych

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do portalu Azure pod adresem [http://portal.azure.com](http://portal.azure.com)

## <a name="create-a-self-signed-certificate"></a>Utwórz certyfikat z podpisem własnym

W tej sekcji użyjesz [SelfSignedCertificate nowy](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) do utworzenia certyfikatu z podpisem własnym, który należy przekazać do portalu Azure, podczas tworzenia odbiornika dla bramy aplikacji.

Na komputerze lokalnym Otwórz okno programu Windows PowerShell jako administrator. Uruchom następujące polecenie, aby utworzyć certyfikat:

```powershell
New-SelfSignedCertificate \
  -certstorelocation cert:\localmachine\my \
  -dnsname www.contoso.com
```

Powinien zostać wyświetlony ekran podobny do tej odpowiedzi:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com

Use [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate) with the Thumbprint that was returned to export a pfx file from the certificate:
```

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456!" -Force -AsPlainText
Export-PfxCertificate \
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 \
  -FilePath c:\appgwcert.pfx \
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

Sieć wirtualna jest wymagany dla komunikacji między zasobami, które można utworzyć. Dwie podsieci są tworzone w tym przykładzie: jeden dla bramy aplikacji, a drugi dla serwerów zaplecza. W tym samym czasie utworzonego bramy aplikacji może utworzyć sieć wirtualną.

1. Kliknij przycisk **nowy** znaleziono w lewym górnym rogu portalu Azure.
2. Wybierz **sieci** , a następnie wybierz **brama aplikacji w** na liście duży.
3. Wprowadź *myAppGateway* dla nazwy bramy aplikacji i *myResourceGroupAG* dla nowej grupy zasobów.
4. Zaakceptuj wartości domyślne dla innych ustawień, a następnie kliknij przycisk **OK**.
5. Kliknij przycisk **wybierz sieć wirtualną**, kliknij przycisk **Utwórz nowy**, a następnie wprowadź wartości dla sieci wirtualnej:

    - *myVNet* — dla nazwy sieci wirtualnej.
    - *10.0.0.0/16* — do przestrzeni adresowej sieci wirtualnej.
    - *myAGSubnet* — dla nazwy podsieci.
    - *10.0.0.0/24* — do przestrzeni adresowej podsieci.

    ![Tworzenie sieci wirtualnej](./media/create-ssl-portal/application-gateway-vnet.png)

6. Kliknij przycisk **OK** do tworzenia sieci wirtualnej i podsieci.
7. Kliknij przycisk **wybierz publiczny adres IP**, kliknij przycisk **Utwórz nowy**, a następnie wprowadź nazwę publicznego adresu IP. W tym przykładzie publiczny adres IP o nazwie *myAGPublicIPAddress*. Zaakceptuj wartości domyślne dla innych ustawień, a następnie kliknij przycisk **OK**.
8. Kliknij przycisk **HTTPS** dla protokołu odbiornika i upewnij się, że port jest zdefiniowany jako **443**.
9. Kliknij ikonę folderu i przejdź do *appgwcert.pfx* wcześniej utworzony go przekazać certyfikat.
10. Wprowadź *mycert1* dla nazwy certyfikatu i *Azure123456!* hasło, a następnie kliknij przycisk **OK**.

    ![Utwórz nową bramę aplikacji](./media/create-ssl-portal/application-gateway-create.png)

11. Przejrzyj ustawienia na stronie Podsumowanie, a następnie kliknij przycisk **OK** tworzyć zasoby sieciowe i bramy aplikacji. Może upłynąć kilka minut dla bramy aplikacji można utworzyć, poczekaj na wdrożenie zakończy się pomyślnie przed przejściem do następnej sekcji.

### <a name="add-a-subnet"></a>Dodaj podsieć

1. Kliknij przycisk **wszystkie zasoby** w menu po lewej stronie, a następnie kliknij przycisk **myVNet** na liście zasobów.
2. Kliknij przycisk **podsieci**, a następnie kliknij przycisk **podsieci**.

    ![Tworzenie podsieci](./media/create-ssl-portal/application-gateway-subnet.png)

3. Wprowadź *myBackendSubnet* dla nazwy podsieci, a następnie kliknij przycisk **OK**.

## <a name="create-backend-servers"></a>Tworzenie serwerów zaplecza

W tym przykładzie utworzysz dwie maszyny wirtualne do użycia jako serwery zaplecza bramy aplikacji. Należy również zainstalować usług IIS na maszynach wirtualnych, aby sprawdzić, czy brama aplikacji została pomyślnie utworzona.

### <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. Kliknij przycisk **Nowy**.
2. Kliknij przycisk **obliczeniowe** , a następnie wybierz **systemu Windows Server 2016 Datacenter** na liście duży.
3. Wprowadź wartości dla maszyny wirtualnej:

    - *myVM* — Nazwa maszyny wirtualnej.
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

    ![Zainstaluj rozszerzenia niestandardowego](./media/create-ssl-portal/application-gateway-extension.png)

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

3. Utwórz maszynę wirtualną drugiej i zainstaluj usługi IIS, wykonując kroki, które właśnie zostało zakończone. Wprowadź *myVM2* dla nazwy i VMName w AzureRmVMExtension zestawu.

### <a name="add-backend-servers"></a>Dodawanie serwerów wewnętrznej bazy danych

3. Kliknij przycisk **wszystkie zasoby**, a następnie kliknij przycisk **myAppGateway**.
4. Kliknij przycisk **pul zaplecza**. Domyślna pula został utworzony automatycznie z bramy aplikacji. Kliknij przycisk **appGateayBackendPool**.
5. Kliknij przycisk **docelowy Dodaj** dodawania każdej maszyny wirtualnej, który został utworzony do puli wewnętrznej bazy danych.

    ![Dodawanie serwerów wewnętrznej bazy danych](./media/create-ssl-portal/application-gateway-backend.png)

6. Kliknij pozycję **Zapisz**.

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

1. Kliknij przycisk **wszystkie zasoby**, a następnie kliknij przycisk **myAGPublicIPAddress**.

    ![Zarejestruj publiczny adres IP bramy aplikacji](./media/create-ssl-portal/application-gateway-ag-address.png)

2. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki. Aby zaakceptować ostrzeżenie o zabezpieczeniach, jeśli używasz certyfikatu z podpisem własnym, wybierz szczegóły, a następnie przejdź do strony sieci Web:

    ![Ostrzeżenie bezpieczne](./media/create-ssl-portal/application-gateway-secure.png)

    Zostanie wyświetlona zabezpieczona witryna internetowa usług IIS, tak jak w poniższym przykładzie:

    ![Podstawowy adres URL testu bramy aplikacji](./media/create-ssl-portal/application-gateway-iistest.png)

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz certyfikat z podpisem własnym
> * Utwórz bramę aplikacji przy użyciu certyfikatu
> * Tworzenie maszyn wirtualnych, używane jako serwery wewnętrznej bazy danych

Aby dowiedzieć się więcej na temat bram aplikacji i ich skojarzonych zasobów, nadal artykuły.