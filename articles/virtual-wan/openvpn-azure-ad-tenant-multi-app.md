---
title: 'VPN Gateway: dzierżawa usługi Azure AD dla różnych grup użytkowników: uwierzytelnianie w usłudze Azure AD'
description: Aby nawiązać połączenie z siecią wirtualną przy użyciu uwierzytelniania usługi Azure AD, możesz użyć sieci VPN P2S
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 1/10/2020
ms.author: alzam
ms.openlocfilehash: b390b5f8b00f61994db820a3af7bce26a3e0a30d
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934975"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Tworzenie dzierżawy Azure Active Directory dla połączeń protokołu OpenVPN P2S

Podczas nawiązywania połączenia z siecią wirtualną można użyć uwierzytelniania opartego na certyfikatach lub uwierzytelniania usługi RADIUS. Jednak w przypadku korzystania z otwartego protokołu sieci VPN można również użyć uwierzytelniania Azure Active Directory. Jeśli chcesz, aby inny zestaw użytkowników mógł łączyć się z różnymi bramami sieci VPN, możesz zarejestrować wiele aplikacji w usłudze AD i połączyć je z innymi bramami sieci VPN. Ten artykuł ułatwia skonfigurowanie dzierżawy usługi Azure AD na potrzeby uwierzytelniania P2S OpenVPN i tworzenie i rejestrowanie wielu aplikacji w usłudze Azure AD w celu umożliwienia różnym użytkownikom i grupom dostępu do różnych użytkowników.

> [!NOTE]
> Uwierzytelnianie usługi Azure AD jest obsługiwane tylko dla połączeń protokołu OpenVPN®.
>

## <a name="tenant"></a>1. Tworzenie dzierżawy usługi Azure AD

Utwórz dzierżawę usługi Azure AD, wykonując czynności opisane w artykule [Tworzenie nowej dzierżawy](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) :

* Nazwa organizacji
* Początkowa nazwa domeny

Przykład:

   ![Nowa dzierżawa usługi Azure AD](./media/openvpn-azure-ad-tenant-multi-app/newtenant.png)

## <a name="users"></a>2. tworzenie użytkowników dzierżawy usługi Azure AD

Następnie Utwórz dwa konta użytkowników. Utwórz jedno konto administratora globalnego i jedno konto użytkownika głównego. Główne konto użytkownika jest używane jako główne konto osadzania (konto usługi). Podczas tworzenia konta użytkownika dzierżawy usługi Azure AD można dostosować rolę katalogu dla typu użytkownika, który ma zostać utworzony.

Wykonaj kroki opisane w [tym artykule](../active-directory/fundamentals/add-users-azure-active-directory.md) , aby utworzyć co najmniej dwóch użytkowników dla dzierżawy usługi Azure AD. Pamiętaj, aby zmienić **rolę katalogu** w celu utworzenia typów kont:

* Administrator globalny
* Użytkownik

## <a name="enable-authentication"></a>3. Zarejestruj klienta sieci VPN platformy Azure w dzierżawie usługi Azure AD

1. Znajdź identyfikator katalogu katalogu, który ma być używany do uwierzytelniania. Jest on wymieniony w sekcji właściwości na stronie Active Directory.

    ![Identyfikator katalogu](./media/openvpn-azure-ad-tenant-multi-app/directory-id.png)

2. Skopiuj identyfikator katalogu.

3. Zaloguj się do Azure Portal jako użytkownik, do którego przypisano rolę **administratora globalnego** .

4. Następnie udziel zgody administratora. Skopiuj i wklej adres URL odnoszący się do lokalizacji wdrożenia na pasku adresu przeglądarki:

    Publiczne

    ```
    https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
    ````

    Platforma Azure dla instytucji rządowych

    ```
    https://login-us.microsoftonline.com/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
    ````

    Microsoft Cloud Germany

    ```
    https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
    ````

    Azure w Chinach — 21Vianet

    ```
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. Jeśli zostanie wyświetlony monit, wybierz konto **administratora globalnego** .

    ![Identyfikator katalogu](./media/openvpn-azure-ad-tenant-multi-app/pick.png)

6. Wybierz pozycję **Akceptuj** po wyświetleniu monitu.

    ![Zaakceptuj](./media/openvpn-azure-ad-tenant-multi-app/accept.jpg)

7. W ramach usługi Azure AD w **aplikacjach dla przedsiębiorstw**zostanie wyświetlona lista **sieci VPN platformy Azure** .

    ![Sieć VPN platformy Azure](./media/openvpn-azure-ad-tenant-multi-app/azurevpn.png)

## <a name="enable-authentication"></a>4. Zarejestruj dodatkowe aplikacje dla różnych użytkowników/grup

1. W Azure Active Directory kliknij pozycję **rejestracje aplikacji** , a następnie **+ Nowa rejestracja**

    ![Sieć VPN platformy Azure](./media/openvpn-azure-ad-tenant-multi-app/app1.png)

2. W bloku **zarejestruj aplikację** wprowadź **nazwę** i wybierz żądane **obsługiwane typy kont** , a następnie kliknij pozycję **zarejestruj** .

    ![Sieć VPN platformy Azure](./media/openvpn-azure-ad-tenant-multi-app/app2.png)

3. Po zarejestrowaniu nowej aplikacji kliknij pozycję **Uwidacznianie interfejsu API** w bloku aplikacji

4. Kliknij pozycję **+ Dodaj zakres**
5. Pozostaw domyślny **Identyfikator URI aplikacji** , a następnie kliknij przycisk **Zapisz i Kontynuuj** .

    ![Sieć VPN platformy Azure](./media/openvpn-azure-ad-tenant-multi-app/app3.png)
6. Wypełnij wymagane pola i upewnij się, że **stan** jest **włączony**. Kliknij pozycję **Dodaj zakres**

    ![Sieć VPN platformy Azure](./media/openvpn-azure-ad-tenant-multi-app/app4.png)
7. Kliknij pozycję **uwidaczniaj interfejs API,** a następnie **Dodaj aplikację kliencką**.  W polu **Identyfikator klienta**wprowadź następujące wartości w zależności od chmury:
    -   Wprowadź **41b23e61-6c1e-4545-b367-cd054e0ed4b4** for Azure **Public**
    -   Wprowadź **51bb15d4-3a4f-4ebf-9dca-40096fe32426** dla **instytucji rządowych** platformy Azure
    -   Wprowadź **538ee9e6-310a-468d-afef-ea97365856a9** dla platformy Azure ( **Niemcy** )
    -   Wprowadź **49f817b6-84ae-4cc0-928c-73f27289b3aa** dla platformy Azure ( **Chiny** )


8. Kliknij pozycję **Dodaj aplikację** .

    ![Sieć VPN platformy Azure](./media/openvpn-azure-ad-tenant-multi-app/app5.png)
9. Skopiuj **Identyfikator aplikacji (klienta)** ze strony **Przegląd** . Będziesz potrzebować tego, aby skonfigurować bramy sieci VPN

    ![Sieć VPN platformy Azure](./media/openvpn-azure-ad-tenant-multi-app/app6.png)

10. Powtórz kroki opisane w tej sekcji (4), aby utworzyć dowolną liczbę aplikacji potrzebnych do wymagania dotyczącego zabezpieczeń. Każda aplikacja zostanie skojarzona z bramą sieci VPN i może mieć innego zestawu użytkowników. Tylko jedna aplikacja może być skojarzona z bramą.

## <a name="enable-authentication"></a>5. Przypisywanie użytkowników do aplikacji

1. W obszarze Azure AD, **aplikacje dla przedsiębiorstw**wybierz nowo zarejestrowana aplikacja i kliknij pozycję **Właściwości**. Upewnij się, że **przypisanie użytkownika jest wymagane?** ma ustawioną **wartość tak**. Kliknij pozycję **Zapisz**

    ![Sieć VPN platformy Azure](./media/openvpn-azure-ad-tenant-multi-app/user2.png)

2. Na stronie Aplikacja kliknij pozycję **Użytkownicy i grupy** , a następnie **Dodaj użytkownika**

    ![Sieć VPN platformy Azure](./media/openvpn-azure-ad-tenant-multi-app/user3.png)
3. W obszarze **Dodaj przypisanie**kliknij pozycję **Użytkownicy i grupy**. Wybierz użytkowników, którym chcesz mieć dostęp do tej aplikacji sieci VPN. Kliknij pozycję **Wybierz**

    ![Sieć VPN platformy Azure](./media/openvpn-azure-ad-tenant-multi-app/user4.png)

## <a name="site"></a>6. Utwórz nową konfigurację P2S

Konfiguracja P2S definiuje parametry służące do łączenia z klientami zdalnymi.

1. Ustaw następujące zmienne, zastępując wartości odpowiednie dla danego środowiska.

   ```powershell
   $aadAudience = "00000000-abcd-abcd-abcd-999999999999"
   $aadIssuer = "https://sts.windows.net/00000000-abcd-abcd-abcd-999999999999/"
   $aadTenant = "https://login.microsoftonline.com/00000000-abcd-abcd-abcd-999999999999"    
   ```

2. Uruchom następujące polecenia, aby utworzyć konfigurację:

   ```powershell
   $aadConfig = New-AzVpnServerConfiguration -ResourceGroupName <ResourceGroup> -Name newAADConfig -VpnProtocol OpenVPN -VpnAuthenticationType AAD -AadTenant $aadTenant -AadIssuer $aadIssuer -AadAudience $aadAudience -Location westcentralus
> [!NOTE]
> Do not use the Azure VPN client's application ID in the commands above as it will grant all users access to the VPN gateway. Use the ID of the application(s) you registered.

## <a name="hub"></a>7. Edit hub assignment

1. Navigate to the **Hubs** blade under the virtual WAN.
2. Select the hub that you want to associate the vpn server configuration to and click the ellipsis (...).

   ![new site](media/virtual-wan-point-to-site-azure-ad/p2s4.jpg)
3. Click **Edit virtual hub**.
4. Check the **Include point-to-site gateway** check box and pick the **Gateway scale unit** that you want.

   ![new site](media/virtual-wan-point-to-site-azure-ad/p2s2.jpg)
5. Enter the **Address pool** from which the VPN clients will be assigned IP addresses.
6. Click **Confirm**.
7. The operation will can take up to 30 minutes to complete.

## <a name="device"></a>8. Download VPN profile

Use the VPN profile to configure your clients.

1. On the page for your virtual WAN, click **User VPN configurations**.
2. At the top of the  page, click **Download user VPN config**.
3. Once the file has finished creating, you can click the link to download it.
4. Use the profile file to configure the VPN clients.

4. Extract the downloaded zip file.

5. Browse to the unzipped “AzureVPN” folder.

6. Make a note of the location of the “azurevpnconfig.xml” file. The azurevpnconfig.xml contains the setting for the VPN connection and can be imported directly into the Azure VPN Client application. You can also distribute this file to all the users that need to connect via e-mail or other means. The user will need valid Azure AD credentials to connect successfully.
## Configure user VPN clients

To connect, you need to download the Azure VPN Client (Preview) and import the VPN client profile that was downloaded in the previous steps on every computer that wants to connect to the VNet.

> [!NOTE]
> Azure AD authentication is supported only for OpenVPN® protocol connections.
>

#### To download the Azure VPN client

Use this [link](https://www.microsoft.com/p/azure-vpn-client-preview/9np355qt2sqb?rtc=1&activetab=pivot:overviewtab) to download the Azure VPN Client (Preview).

#### <a name="import"></a>To import a client profile

1. On the page, select **Import**.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import1.jpg)

2. Browse to the profile xml file and select it. With the file selected, select **Open**.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import2.jpg)

3. Specify the name of the profile and select **Save**.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import3.jpg)

4. Select **Connect** to connect to the VPN.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import4.jpg)

5. Once connected, the icon will turn green and say **Connected**.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import5.jpg)

#### <a name="delete"></a>To delete a client profile

1. Select the ellipsis (...) next to the client profile that you want to delete. Then, select **Remove**.

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete1.jpg)

2. Select **Remove** to delete.

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete2.jpg)

#### <a name="diagnose"></a>Diagnose connection issues

1. To diagnose connection issues, you can use the **Diagnose** tool. Select the ellipsis (...) next to the VPN connection that you want to diagnose to reveal the menu. Then select **Diagnose**.

    ![diagnose](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose1.jpg)

2. On the **Connection Properties** page, select **Run Diagnosis**.

    ![diagnose](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose2.jpg)

3. Sign in with your credentials.

    ![diagnose](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose3.jpg)

4. View the diagnosis results.

    ![diagnose](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose4.jpg)

## <a name="viewwan"></a>View your virtual WAN

1. Navigate to the virtual WAN.
2. On the Overview page, each point on the map represents a hub. Hover over any point to view the hub health summary.
3. In the Hubs and connections section, you can view hub status, site, region, VPN connection status, and bytes in and out.

## <a name="viewhealth"></a>View your resource health

1. Navigate to your WAN.
2. On your WAN page, in the **SUPPORT + Troubleshooting** section, click **Health** and view your resource.


## <a name="cleanup"></a>Clean up resources

When you no longer need these resources, you can use [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) to remove the resource group and all of the resources it contains. Replace "myResourceGroup" with the name of your resource group and run the following PowerShell command:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Virtual WAN, zobacz stronę [Omówienie usługi Virtual WAN](virtual-wan-about.md).