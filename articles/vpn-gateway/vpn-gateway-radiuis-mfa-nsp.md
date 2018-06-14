---
title: Bezpieczne sieci VPN platformy Azure bramy uwierzytelniania RADIUS z serwerem zasad Sieciowych uwierzytelniania wieloskładnikowego | Dokumentacja firmy Microsoft
description: W tym artykule opisano integracji Azure bramy uwierzytelniania RADIUS z serwera NPS uwierzytelnianie wieloskładnikowe.
services: vpn-gateway
documentationcenter: na
author: ahmadnyasin
manager: willchen
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/13/2018
ms.author: genli
ms.openlocfilehash: 665e1914f44d7c5e650a1b632d8b11c6d8a5931a
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
ms.locfileid: "31793277"
---
# <a name="integrate-azure-vpn-gateway-radius-authentication-with-nps-server-for-multi-factor-authentication"></a>Integrowanie uwierzytelniania RADIUS bramy sieci VPN platformy Azure z serwerem zasad Sieciowych uwierzytelnianie wieloskładnikowe 

Artykuł opisuje sposób integrowania serwera zasad sieciowych (NPS) z uwierzytelnianiem RADIUS bramy sieci VPN platformy Azure do świadczenia usługi Multi-Factor Authentication (MFA) dla połączeń sieci VPN punkt lokacja. 

## <a name="prerequisite"></a>Wymagania wstępne

Aby włączyć uwierzytelnianie wieloskładnikowe, użytkowników musi być w usłudze Active Directory (Azure AD), który musi być synchronizowane z lokalnie lub w chmurze środowiska. Ponadto użytkownik musi już zakończył proces automatycznej rejestracji dla usługi MFA.  Aby uzyskać więcej informacji, zobacz [Skonfiguruj moje konto na potrzeby weryfikacji dwuetapowej](../multi-factor-authentication/end-user/multi-factor-authentication-end-user-first-time.md)

## <a name="detailed-steps"></a>Szczegółowe procedury

### <a name="step-1-create-a-virtual-network-gateway"></a>Krok 1: Tworzenie bramy sieci wirtualnej

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W sieci wirtualnej, który będzie obsługiwał bramy sieci wirtualnej, wybierz **podsieci**, a następnie wybierz **podsieci bramy** Aby utworzyć podsieć. 

    ![Obraz o sposobie dodawania podsieci bramy](./media/vpn-gateway-radiuis-mfa-nsp/gateway-subnet.png)
3. Tworzenie bramy sieci wirtualnej przez określenie następujących ustawień:

    - **Typ bramy**: Wybierz pozycję **Sieć VPN**.
    - **Typ sieci VPN**: Wybierz **opartej na trasach**.
    - **Jednostka SKU**: Wybierz typ jednostki SKU zgodnie z wymaganiami.
    - **Sieć wirtualna**: Wybierz sieć wirtualną, w której utworzono podsieć bramy.

        ![Obraz o ustawieniach bramy sieci wirtualnej](./media/vpn-gateway-radiuis-mfa-nsp/create-vpn-gateway.png)


 
### <a name="step-2-configure-the-nps-for-azure-mfa"></a>Krok 2: Konfigurowanie zasad Sieciowych dla usługi Azure MFA

1. Na serwerze NPS [zainstalować rozszerzenie serwera zasad Sieciowych dla usługi Azure MFA](../active-directory/authentication/howto-mfa-nps-extension.md#install-the-nps-extension).
2. Otwórz konsolę NSP, kliknij prawym przyciskiem myszy **klientów RADUIS**, a następnie wybierz **nowy**. Tworzenie klienta RADUIS przez określenie następujących ustawień:

    - **Przyjazna nazwa**: wpisz dowolną nazwę.
    - **Adres (IP lub DNS)**: wpisz podsieć bramy, który został utworzony w kroku 1.
    - **Wspólny klucz tajny**: wpisz wszelkie klucz tajny i Zapamiętaj je do późniejszego użycia.

    ![Obraz o ustawieniach klienta RADUIS](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client1.png)

 
3.  Na **zaawansowane** karcie należy ustawić nazwę dostawcy **RADIUS Standard** i upewnij się, że **dodatkowe opcje** nie zaznaczono pola wyboru.

    ![Obraz o zaawansowanych ustawieniach klienta RADUIS](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client2.png)

4. Przejdź do **zasady** > **zasady sieciowe**, kliknij dwukrotnie **połączenia z serwerem programu Microsoft Routing i dostęp zdalny** zasady, wybierz  **Udziel dostępu**, a następnie kliknij przycisk **OK**.

### <a name="step-3-configure-the-virtual-network-gateway"></a>Krok 3 Konfigurowanie bramy sieci wirtualnej

1. Zaloguj się do [portalu Azure](https://portal.azure.com).
2. Otwórz bramy sieci wirtualnej, który został utworzony. Upewnij się, że typ bramy jest ustawiona na **VPN** , a typ sieci VPN jest **opartej na trasach**.
3. Kliknij przycisk **wskaż konfigurację witryny** > **teraz skonfigurować**, a następnie określ następujące ustawienia:

    - **Pula adresów**: wpisz podsieć bramy, utworzony w kroku 1.
    - **Typ uwierzytelniania**: Wybierz **uwierzytelnianie usługi RADIUS**.
    - **Adres IP serwera**: wpisz adres IP serwera NPS.

    ![Obraz o punkcie ustawień lokacji](./media/vpn-gateway-radiuis-mfa-nsp/configure-p2s.png)

## <a name="next-steps"></a>Kolejne kroki

- [Uwierzytelnianie wieloskładnikowe platformy Azure](../active-directory/authentication/multi-factor-authentication.md)
- [Integrowanie istniejącej infrastruktury NPS z usługą Azure Multi-Factor Authentication](../active-directory/authentication/howto-mfa-nps-extension.md)
