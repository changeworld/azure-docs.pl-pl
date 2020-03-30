---
title: 'Wirtualna sieć WAN: dzierżawa usługi Azure AD dla różnych grup użytkowników: uwierzytelnianie usługi Azure AD'
description: Za pomocą sieci VPN P2S można łączyć się z siecią wirtualną przy użyciu uwierzytelniania usługi Azure AD
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: alzam
ms.openlocfilehash: af5ff5817ee9ae7e6d7432fe281ecb440bf25b9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060718"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Tworzenie dzierżawy usługi Azure Active Directory dla połączeń protokołów OpenVPN P2S

Podczas łączenia się z siecią wirtualną można użyć uwierzytelniania opartego na certyfikatach lub uwierzytelniania RADIUS. Jednak podczas korzystania z protokołu Open VPN można również użyć uwierzytelniania usługi Azure Active Directory. Jeśli chcesz, aby inny zestaw użytkowników mógł łączyć się z różnymi bramami, możesz zarejestrować wiele aplikacji w urzędzie AD i połączyć je z różnymi bramami.

Ten artykuł ułatwia konfigurowanie dzierżawy usługi Azure AD dla uwierzytelniania P2S OpenVPN oraz tworzenie i rejestrowanie wielu aplikacji w usłudze Azure AD w celu umożliwienia różnych dostępu dla różnych użytkowników i grup.

> [!NOTE]
> Uwierzytelnianie usługi Azure AD jest&reg; obsługiwane tylko dla połączeń protokołu OpenVPN.
>

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="6-create-a-new-p2s-configuration"></a><a name="site"></a>6. Tworzenie nowej konfiguracji P2S

Konfiguracja P2S definiuje parametry służące do łączenia z klientami zdalnymi.

1. Ustaw następujące zmienne, zastępując wartości zgodnie z potrzebami dla środowiska.

   ```azurepowershell-interactive
   $aadAudience = "00000000-abcd-abcd-abcd-999999999999"
   $aadIssuer = "https://sts.windows.net/00000000-abcd-abcd-abcd-999999999999/"
   $aadTenant = "https://login.microsoftonline.com/00000000-abcd-abcd-abcd-999999999999"    
   ```

2. Uruchom następujące polecenia, aby utworzyć konfigurację:

   ```azurepowershell-interactive
   $aadConfig = New-AzVpnServerConfiguration -ResourceGroupName <ResourceGroup> -Name newAADConfig -VpnProtocol OpenVPN -VpnAuthenticationType AAD -AadTenant $aadTenant -AadIssuer $aadIssuer -AadAudience $aadAudience -Location westcentralus
   ```

   > [!NOTE]
   > Nie należy używać identyfikatora aplikacji klienta sieci Vpn platformy Azure w powyższych poleceniach: zapewni wszystkim użytkownikom dostęp do bramy. Użyj identyfikatora zarejestrowanych aplikacji.

## <a name="7-edit-hub-assignment"></a><a name="hub"></a>7. Edytowanie przypisania centrum

1. Przejdź do **centrum** bloku w wirtualnej sieci WAN.

2. Wybierz koncentrator, z którego chcesz skojarzyć konfigurację serwera sieci VPN, i kliknij wielokropek (...).

    ![nowa lokacja](media/openvpn-azure-ad-tenant-multi-app/p2s4.jpg)

3. Kliknij **pozycję Edytuj koncentrator wirtualny**.

4. Zaznacz pole wyboru **Dołącz bramę typu punkt-lokacja** i wybierz **odpowiednią jednostkę skalowania bramy.**

    ![nowa lokacja](media/openvpn-azure-ad-tenant-multi-app/p2s2.jpg)

5. Wprowadź **pulę adresów,** z której klientom sieci VPN będą przypisywane adresy IP.

6. Kliknij pozycję **Potwierdź**.

7. Ukończenie operacji może potrwać do 30 minut.

## <a name="8-download-vpn-profile"></a><a name="device"></a>8. Pobierz profil VPN

Użyj profilu sieci VPN, aby skonfigurować klientów.

1. Na stronie wirtualnej sieci WAN kliknij pozycję **Konfiguracje sieci VPN użytkownika**.

2. U góry strony kliknij pozycję **Pobierz konfigur sieci VPN użytkownika**.

3. Po zakończeniu tworzenia pliku możesz kliknąć link, aby go pobrać.

4. Użyj pliku profilu, aby skonfigurować klientów sieci VPN.

5. Wyodrębnij pobrany plik zip.

6. Przejdź do rozpakowanego folderu "AzureVPN".

7. Zanotuj lokalizację pliku "azurevpnconfig.xml". Plik azurevpnconfig.xml zawiera ustawienie połączenia sieci VPN i można go zaimportować bezpośrednio do aplikacji klienta sieci VPN platformy Azure. Można również rozpowszechniać ten plik do wszystkich użytkowników, którzy muszą połączyć się za pośrednictwem poczty e-mail lub w inny sposób. Użytkownik będzie potrzebował prawidłowych poświadczeń usługi Azure AD, aby połączyć się pomyślnie.

## <a name="9-configure-user-vpn-clients"></a>9. Konfigurowanie klientów sieci VPN użytkowników

Aby nawiązać połączenie, należy pobrać klienta sieci VPN platformy Azure i zaimportować profil klienta sieci VPN, który został pobrany w poprzednich krokach na każdym komputerze, który chce połączyć się z siecią wirtualną.

> [!NOTE]
> Uwierzytelnianie usługi Azure AD jest&reg; obsługiwane tylko dla połączeń protokołu OpenVPN.
>

#### <a name="to-download-the-azure-vpn-client"></a>Aby pobrać klienta sieci VPN platformy Azure

Użyj tego [łącza,](https://go.microsoft.com/fwlink/?linkid=2117554) aby pobrać klienta sieci VPN platformy Azure.

#### <a name="to-import-a-client-profile"></a><a name="import"></a>Aby zaimportować profil klienta

1. Na stronie wybierz pozycję **Importuj**.

    ![import](./media/openvpn-azure-ad-tenant-multi-app/import/import1.jpg)

2. Przejdź do pliku xml profilu i wybierz go. Po wybraniu wybranego pliku wybierz pozycję **Otwórz**.

    ![import](./media/openvpn-azure-ad-tenant-multi-app/import/import2.jpg)

3. Określ nazwę profilu i wybierz pozycję **Zapisz**.

    ![import](./media/openvpn-azure-ad-tenant-multi-app/import/import3.jpg)

4. Wybierz **pozycję Połącz,** aby połączyć się z siecią VPN.

    ![import](./media/openvpn-azure-ad-tenant-multi-app/import/import4.jpg)

5. Po podłączeniu ikona zmieni kolor na zielony i na **powiedzmię Connected**.

    ![import](./media/openvpn-azure-ad-tenant-multi-app/import/import5.jpg)

#### <a name="to-delete-a-client-profile"></a><a name="delete"></a>Aby usunąć profil klienta

1. Wybierz wielokropek (...) obok profilu klienta, który chcesz usunąć. Następnie wybierz pozycję **Usuń**.

    ![delete](./media/openvpn-azure-ad-tenant-multi-app/delete/delete1.jpg)

2. Wybierz **pozycję Usuń,** aby usunąć.

    ![delete](./media/openvpn-azure-ad-tenant-multi-app/delete/delete2.jpg)

#### <a name="to-diagnose-connection-issues"></a><a name="diagnose"></a>Aby zdiagnozować problemy z połączeniem

1. Aby zdiagnozować problemy z połączeniem, można użyć narzędzia **Diagnozowanie.** Wybierz wielokropek (...) obok połączenia sieci VPN, które chcesz zdiagnozować, aby wyświetlić menu. Następnie wybierz **polecenie Diagnozuj**.

    ![Zdiagnozować](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose1.jpg)

2. Na stronie **Właściwości połączenia** wybierz pozycję **Uruchom diagnostykę**.

    ![Zdiagnozować](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose2.jpg)

3. Zaloguj się przy użyciu poświadczeń.

    ![Zdiagnozować](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose3.jpg)

4. Wyświetl wyniki diagnozy.

    ![Zdiagnozować](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose4.jpg)

## <a name="10-view-your-virtual-wan"></a><a name="viewwan"></a>10. Zobacz swoją wirtualną sieć WAN

1. Przejdź do wirtualnej sieci WAN.

2. Na stronie Przegląd każdy punkt na mapie przedstawia koncentrator.

3. W sekcji dotyczącej koncentratorów i połączeń możesz wyświetlić stan koncentratora, lokację, region, stan połączenia sieci VPN oraz bajty przychodzące i wychodzące.

## <a name="clean-up-resources"></a><a name="cleanup"></a>Oczyszczanie zasobów

Gdy grupa zasobów i zawarte w niej zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup). Zastąp wartość „myResourceGroup” nazwą grupy zasobów, a następnie uruchom następujące polecenie programu PowerShell:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Virtual WAN, zobacz stronę [Omówienie usługi Virtual WAN](virtual-wan-about.md).
