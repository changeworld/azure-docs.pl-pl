---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/18/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e950d194ab48cec1a70c7bd17617332cb858a55d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77485680"
---
## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. Tworzenie dzierżawy usługi Azure AD

Tworzenie dzierżawy usługi Azure AD przy użyciu kroków w artykule [Tworzenie nowej dzierżawy:](../articles/active-directory/fundamentals/active-directory-access-create-new-tenant.md)

* Nazwa organizacji
* Początkowa nazwa domeny

  Przykład:

   ![Nowa dzierżawa usługi Azure AD](./media/openvpn-azure-ad-tenant-multi-app/new-tenant.png)

## <a name="2-create-tenant-users"></a><a name="users"></a>2. Tworzenie użytkowników dzierżawy

W tym kroku utworzysz dwóch użytkowników dzierżawy usługi Azure AD: jedno konto administratora globalnego i jedno główne konto użytkownika. Główne konto użytkownika jest używane jako główne konto osadzania (konto usługi). Podczas tworzenia konta użytkownika dzierżawy usługi Azure AD można dostosować rolę katalogu dla typu użytkownika, który chcesz utworzyć. Użyj kroków w [tym artykule,](../articles/active-directory/fundamentals/add-users-azure-active-directory.md) aby utworzyć co najmniej dwóch użytkowników dla dzierżawy usługi Azure AD. Pamiętaj, aby zmienić **rolę katalogu,** aby utworzyć typy kont:

* Administrator globalny
* Użytkownik

## <a name="3-register-the-vpn-client"></a><a name="register-client"></a>3. Zarejestruj klienta VPN

Zarejestruj klienta sieci VPN w dzierżawie usługi Azure AD.

1. Znajdź identyfikator katalogu, którego chcesz użyć do uwierzytelniania. Jest on wymieniony w sekcji właściwości na stronie usługi Active Directory.

    ![Identyfikator katalogu](./media/openvpn-azure-ad-tenant-multi-app/directory-id.png)

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

    ![Identyfikator katalogu](./media/openvpn-azure-ad-tenant-multi-app/pick.png)

6. Po wyświetleniu monitu wybierz **pozycję Zaakceptuj.**

    ![Zaakceptuj](./media/openvpn-azure-ad-tenant-multi-app/accept.jpg)

7. W obszarze usługi Azure AD w **aplikacjach dla przedsiębiorstw**zostanie wyświetlona lista **sieci VPN platformy Azure.**

     ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/azure-vpn.png)

## <a name="4-register-additional-applications"></a><a name="register-apps"></a>4. Rejestracja dodatkowych zgłoszeń

W tym kroku można zarejestrować dodatkowe aplikacje dla różnych użytkowników i grup.

1. W obszarze usługi Azure Active Directory kliknij pozycję **Rejestracje aplikacji,** a następnie **+ Nowa rejestracja**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app1.png)

2. Na stronie **Zarejestruj zgłoszenie** wprowadź **nazwę**. Wybierz żądane **typy obsługiwanych kont,** a następnie kliknij przycisk **Zarejestruj**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app2.png)

3. Po zarejestrowaniu nowej aplikacji kliknij **udostępnij interfejs API** w bloku aplikacji.

4. Kliknij **przycisk + Dodaj zakres**.

5. Pozostaw **domyślny identyfikator URI identyfikatora aplikacji**. Kliknij **pozycję Zapisz i kontynuuj**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app3.png)

6. Wypełnij wymagane pola i upewnij się, że **stan** jest **włączony**. Kliknij **pozycję Dodaj zakres**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app4.png)

7. Kliknij **pozycję Uwidacznij interfejs API,** a następnie **+ Dodaj aplikację kliencką**.  W przypadku **identyfikatora klienta**wprowadź następujące wartości w zależności od chmury:

    - Wprowadź **41b23e61-6c1e-4545-b367-cd054e0ed4b4** dla usługi Azure **Public**
    - Wprowadź **51bb15d4-3a4f-4ebf-9dca-40096fe32426** dla platformy Azure **Government**
    - Wprowadź **538ee9e6-310a-468d-afef-ea97365856a9** dla platformy Azure **Niemcy**
    - Wprowadź **49f817b6-84ae-4cc0-928c-73f27289b3aa** dla platformy Azure **China 21Vianet**

8. Kliknij **pozycję Dodaj aplikację**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app5.png)

9. Skopiuj **identyfikator aplikacji (klienta)** ze strony **Przegląd.** Te informacje będą potrzebne do skonfigurowania bramy sieci VPN.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app6.png)

10. Powtórz kroki opisane w tej sekcji [rejestru dodatkowych aplikacji,](#register-apps) aby utworzyć jak najwięcej aplikacji, które są potrzebne do wymagań dotyczących zabezpieczeń. Każda aplikacja będzie skojarzona z bramą sieci VPN i może mieć inny zestaw użytkowników. Tylko jedna aplikacja może być skojarzona z bramą.

## <a name="5-assign-users-to-applications"></a><a name="assign-users"></a>5. Przypisywanie użytkowników do aplikacji

Przypisz użytkowników do aplikacji.

1. W obszarze **Usługi Azure AD -> aplikacje enterprise**wybierz nowo zarejestrowaną aplikację i kliknij pozycję **Właściwości**. Upewnij się, że wymagane jest **yes** **przypisanie użytkownika?** Kliknij przycisk **Zapisz**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user2.png)

2. Na stronie aplikacji kliknij pozycję **Użytkownicy i grupy**, a następnie kliknij pozycję **+Dodaj użytkownika**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user3.png)

3. W obszarze **Dodawanie przydziału**kliknij pozycję **Użytkownicy i grupy**. Wybierz użytkowników, którzy mają mieć dostęp do tej aplikacji sieci VPN. Kliknij **pozycję Wybierz**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user4.png)