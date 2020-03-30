---
title: 'Dzierżawa usługi Azure AD dla połączeń sieci VPN użytkowników: uwierzytelnianie usługi Azure AD'
description: Za pomocą sieci VPN użytkownika wirtualnej sieci WAN platformy Azure (point-to-site) można połączyć się z siecią wirtualną przy użyciu uwierzytelniania usługi Azure AD
titleSuffix: Azure Virtual WAN
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: alzam
ms.openlocfilehash: 74347ce969b6a5ffd57f5ca8396517e78590f3f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059458"
---
# <a name="create-an-azure-active-directory-tenant-for-user-vpn-openvpn-protocol-connections"></a>Tworzenie dzierżawy usługi Azure Active Directory dla połączeń protokołu OpenVPN usługi VPN użytkownika

Podczas łączenia się z siecią wirtualną można użyć uwierzytelniania opartego na certyfikatach lub uwierzytelniania RADIUS. Jednak podczas korzystania z protokołu Open VPN można również użyć uwierzytelniania usługi Azure Active Directory. Ten artykuł ułatwia konfigurowanie dzierżawy usługi Azure AD dla uwierzytelniania sieci VPN typu virtual WAN User VPN (point-to-site).

> [!NOTE]
> Uwierzytelnianie usługi Azure AD jest&reg; obsługiwane tylko dla połączeń protokołu OpenVPN.
>

## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. Tworzenie dzierżawy usługi Azure AD

Tworzenie dzierżawy usługi Azure AD przy użyciu kroków w artykule [Tworzenie nowej dzierżawy:](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

* Nazwa organizacji
* Początkowa nazwa domeny

Przykład:

   ![Nowa dzierżawa usługi Azure AD](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. Tworzenie użytkowników dzierżawy usługi Azure AD

Następnie utwórz dwa konta użytkowników. Utwórz jedno konto administratora globalnego i jedno główne konto użytkownika. Główne konto użytkownika jest używane jako główne konto osadzania (konto usługi). Podczas tworzenia konta użytkownika dzierżawy usługi Azure AD można dostosować rolę katalogu dla typu użytkownika, który chcesz utworzyć.

Użyj kroków w [tym artykule,](../active-directory/fundamentals/add-users-azure-active-directory.md) aby utworzyć co najmniej dwóch użytkowników dla dzierżawy usługi Azure AD. Pamiętaj, aby zmienić **rolę katalogu,** aby utworzyć typy kont:

* Administrator globalny
* Użytkownik

## <a name="3-enable-azure-ad-authentication-on-the-vpn-gateway"></a><a name="enable-authentication"></a>3. Włącz uwierzytelnianie usługi Azure AD w bramie sieci VPN

1. Znajdź identyfikator katalogu, którego chcesz użyć do uwierzytelniania. Jest on wymieniony w sekcji właściwości na stronie usługi Active Directory.

    ![Identyfikator katalogu](./media/openvpn-create-azure-ad-tenant/directory-id.png)

2. Skopiuj identyfikator katalogu.

3. Zaloguj się do witryny Azure portal jako użytkownik, który jest przypisany do roli **administratora globalnego.**

4. Następnie należy wyrazić zgodę administratora. Skopiuj i wklej adres URL odnoszący się do lokalizacji wdrożenia na pasku adresu przeglądarki:

    Public

    ```
    https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
    ````

    Azure Government

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

5. Wybierz konto **administratora globalnego,** jeśli zostanie wyświetlony monit.

    ![Identyfikator katalogu](./media/openvpn-create-azure-ad-tenant/pick.png)

6. Po wyświetleniu monitu wybierz **pozycję Zaakceptuj.**

    ![Zaakceptuj](./media/openvpn-create-azure-ad-tenant/accept.jpg)

7. W obszarze usługi Azure AD w **aplikacjach dla przedsiębiorstw**na liście znajduje się lista **sieci VPN platformy Azure.**

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/azurevpn.png)

8. Konfigurowanie uwierzytelniania usługi Azure AD dla sieci VPN użytkownika i przypisywanie go do centrum wirtualnego, wykonując kroki opisane w [temacie Konfigurowanie uwierzytelniania usługi Azure AD dla połączenia z lokacją z platformą Azure](virtual-wan-point-to-site-azure-ad.md)

## <a name="next-steps"></a>Następne kroki

Aby połączyć się z siecią wirtualną, należy utworzyć i skonfigurować profil klienta sieci VPN i skojarzyć go z centrum wirtualnym. Zobacz [Konfigurowanie uwierzytelniania usługi Azure AD dla połączenia typu punkt-lokacja z platformą Azure](virtual-wan-point-to-site-azure-ad.md).
