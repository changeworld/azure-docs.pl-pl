---
title: 'VPN Gateway: dzierżawa usługi Azure AD dla różnych grup użytkowników: uwierzytelnianie w usłudze Azure AD'
description: Aby nawiązać połączenie z siecią wirtualną przy użyciu uwierzytelniania usługi Azure AD, możesz użyć sieci VPN P2S
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 1/10/2020
ms.author: alzam
ms.openlocfilehash: 90244b9dcf30c2ef01d4e57c9d8e35fa1d71f434
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76985649"
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

    Microsoft Cloud Niemcy

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

    ![Odebrać](./media/openvpn-azure-ad-tenant-multi-app/accept.jpg)

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
   ```

> [!NOTE]
> Nie używaj identyfikatora aplikacji klienta sieci VPN platformy Azure w powyższych poleceniach, ponieważ spowoduje to przyznanie wszystkim użytkownikom dostępu do bramy sieci VPN. Użyj identyfikatora zarejestrowanej aplikacji.

## <a name="hub"></a>7. Edytowanie przypisania centrum

1. Przejdź do bloku **centra** w wirtualnej sieci WAN.
2. Wybierz centrum, do którego chcesz skojarzyć konfigurację serwera sieci VPN, a następnie kliknij przycisk wielokropka (...).

   ![nowa lokacja](media/virtual-wan-point-to-site-azure-ad/p2s4.jpg)
3. Kliknij pozycję **Edytuj koncentrator wirtualny**.
4. Zaznacz pole wyboru **Uwzględnij bramę punkt-lokacja** i wybierz żądaną **jednostkę skalowania bramy** .

   ![nowa lokacja](media/virtual-wan-point-to-site-azure-ad/p2s2.jpg)
5. Wprowadź **pulę adresów** , z której będą przypisywane adresy IP klientów sieci VPN.
6. Kliknij pozycję **Potwierdź**.
7. Wykonanie operacji może potrwać do 30 minut.

## <a name="device"></a>8. Pobieranie profilu sieci VPN

Użyj profilu sieci VPN, aby skonfigurować klientów.

1. Na stronie wirtualnej sieci WAN kliknij pozycję **konfiguracje sieci VPN użytkownika**.
2. W górnej części strony kliknij pozycję **Pobierz konfigurację sieci VPN użytkownika**.
3. Po zakończeniu tworzenia pliku możesz kliknąć link, aby go pobrać.
4. Użyj pliku profilu, aby skonfigurować klientów sieci VPN.

4. Wyodrębnij pobrany plik zip.

5. Przejdź do folderu rozpakowanego "AzureVPN".

6. Zanotuj lokalizację pliku "azurevpnconfig. xml". Azurevpnconfig. xml zawiera ustawienie dla połączenia sieci VPN i można je zaimportować bezpośrednio do aplikacji klienckiej sieci VPN platformy Azure. Możesz również dystrybuować ten plik do wszystkich użytkowników, którzy muszą łączyć się za pośrednictwem poczty e-mail lub w inny sposób. Aby nawiązać połączenie, użytkownik musi dysponować prawidłowymi poświadczeniami usługi Azure AD.
## <a name="configure-user-vpn-clients"></a>Konfigurowanie klientów VPN użytkowników

Aby nawiązać połączenie, należy pobrać klienta sieci VPN platformy Azure (wersja zapoznawcza) i zaimportować profil klienta sieci VPN, który został pobrany w poprzednich krokach na każdym komputerze, który chce nawiązać połączenie z siecią wirtualną.

> [!NOTE]
> Uwierzytelnianie usługi Azure AD jest obsługiwane tylko dla połączeń protokołu OpenVPN®.
>

#### <a name="to-download-the-azure-vpn-client"></a>Aby pobrać klienta sieci VPN platformy Azure

Użyj tego [linku](https://www.microsoft.com/p/azure-vpn-client-preview/9np355qt2sqb?rtc=1&activetab=pivot:overviewtab) , aby pobrać klienta sieci VPN platformy Azure (wersja zapoznawcza).

#### <a name="import"></a>Aby zaimportować profil klienta

1. Na stronie wybierz pozycję **Importuj**.

    ![zaimportować](./media/virtual-wan-point-to-site-azure-ad/import/import1.jpg)

2. Przejdź do pliku XML profilu i wybierz go. Po wybraniu pliku wybierz pozycję **Otwórz**.

    ![zaimportować](./media/virtual-wan-point-to-site-azure-ad/import/import2.jpg)

3. Określ nazwę profilu i wybierz pozycję **Zapisz**.

    ![zaimportować](./media/virtual-wan-point-to-site-azure-ad/import/import3.jpg)

4. Wybierz pozycję **Połącz** , aby nawiązać połączenie z siecią VPN.

    ![zaimportować](./media/virtual-wan-point-to-site-azure-ad/import/import4.jpg)

5. Po nawiązaniu połączenia ikona zmieni kolor na zielony i powiedzie **się.**

    ![zaimportować](./media/virtual-wan-point-to-site-azure-ad/import/import5.jpg)

#### <a name="delete"></a>Aby usunąć profil klienta

1. Wybierz wielokropek (...) obok profilu klienta, który chcesz usunąć. Następnie wybierz pozycję **Usuń**.

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete1.jpg)

2. Wybierz pozycję **Usuń** , aby usunąć.

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete2.jpg)

#### <a name="diagnose"></a>Diagnozuj problemy z połączeniem

1. Aby zdiagnozować problemy z połączeniem, można użyć narzędzia do **diagnozowania** . Wybierz wielokropek (...) obok połączenia sieci VPN, które chcesz zdiagnozować, aby wyświetlić menu. Następnie wybierz pozycję **Diagnozuj**.

    ![Sprawdź](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose1.jpg)

2. Na stronie **Właściwości połączenia** wybierz pozycję **Uruchom diagnostykę**.

    ![Sprawdź](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose2.jpg)

3. Zaloguj się przy użyciu swoich poświadczeń.

    ![Sprawdź](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose3.jpg)

4. Wyświetl wyniki diagnostyki.

    ![Sprawdź](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose4.jpg)

## <a name="viewwan"></a>Wyświetlanie wirtualnej sieci WAN

1. Przejdź do wirtualnej sieci WAN.
2. Na stronie Przegląd każdy punkt na mapie przedstawia koncentrator. Umieść kursor na dowolnym punkcie, aby wyświetlić podsumowanie kondycji koncentratora.
3. W sekcji dotyczącej koncentratorów i połączeń możesz wyświetlić stan koncentratora, lokację, region, stan połączenia sieci VPN oraz bajty przychodzące i wychodzące.

## <a name="viewhealth"></a>Wyświetlanie kondycji zasobów

1. Przejdź do sieci WAN.
2. Na stronie sieci WAN w sekcji **Pomoc techniczna i rozwiązywanie problemów** kliknij pozycję **Kondycja** i wyświetl zasób.


## <a name="cleanup"></a>Czyszczenie zasobów

Gdy grupa zasobów i zawarte w niej zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup). Zastąp wartość „myResourceGroup” nazwą grupy zasobów, a następnie uruchom następujące polecenie programu PowerShell:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Virtual WAN, zobacz stronę [Omówienie usługi Virtual WAN](virtual-wan-about.md).
