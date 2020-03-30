---
title: Integracja serwera NPS z uwierzytelnianiem RADIUS bramy sieci VPN dla usługi MFA
description: W tym artykule opisano integrację uwierzytelniania RADIUS bramy platformy Azure z serwerem NPS w celu uwierzytelniania wieloskładnikowego.
services: vpn-gateway
documentationcenter: na
author: ahmadnyasin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/16/2019
ms.author: genli
ms.openlocfilehash: 941b6ac86941824351f83592998e8735e3eb8ee5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75780372"
---
# <a name="integrate-azure-vpn-gateway-radius-authentication-with-nps-server-for-multi-factor-authentication"></a>Integracja uwierzytelniania RADIUS bramy sieci VPN platformy Azure z serwerem NPS w celu uwierzytelniania wieloskładnikowego 

W tym artykule opisano sposób integrowania serwera zasad sieciowych (NPS) z uwierzytelnianiem radius bramy sieci VPN platformy Azure w celu dostarczania uwierzytelniania wieloskładnikowego (MFA) dla połączeń sieci VPN typu punkt-lokacja. 

## <a name="prerequisite"></a>Wymagania wstępne

Aby włączyć usługę MFA, użytkownicy muszą być w usłudze Azure Active Directory (Azure AD), która musi być synchronizowana ze środowiska lokalnego lub chmurowego. Ponadto użytkownik musi już zakończyć proces automatycznej rejestracji dla usługi MFA.  Aby uzyskać więcej informacji, zobacz [Konfigurowanie konta do weryfikacji dwuetapowej](../active-directory/user-help/multi-factor-authentication-end-user-first-time.md)

## <a name="detailed-steps"></a>Szczegółowe procedury

### <a name="step-1-create-a-virtual-network-gateway"></a>Krok 1: Tworzenie bramy sieci wirtualnej

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W sieci wirtualnej, która będzie obsługiwać bramę sieci wirtualnej, wybierz pozycję **Podsieci**, a następnie wybierz **podsieć Bramy,** aby utworzyć podsieć. 

    ![Obraz dotyczący dodawania podsieci bramy](./media/vpn-gateway-radiuis-mfa-nsp/gateway-subnet.png)
3. Utwórz bramę sieci wirtualnej, określając następujące ustawienia:

    - **Typ bramy**: Wybierz pozycję **Sieć VPN**.
    - **Typ sieci VPN:** Wybierz **opcję Oparta na marszrutach**.
    - **Jednostka SKU**: Wybierz typ jednostki SKU na podstawie wymagań.
    - **Sieć wirtualna**: Wybierz sieć wirtualną, w której utworzono podsieć bramy.

        ![Obraz dotyczący ustawień bramy sieci wirtualnej](./media/vpn-gateway-radiuis-mfa-nsp/create-vpn-gateway.png)


 
### <a name="step-2-configure-the-nps-for-azure-mfa"></a>Krok 2 Konfigurowanie serwera nps dla usługi Azure MFA

1. Na serwerze NPS [zainstaluj rozszerzenie NPS dla usługi Azure MFA](../active-directory/authentication/howto-mfa-nps-extension.md#install-the-nps-extension).
2. Otwórz konsolę NPS, kliknij prawym przyciskiem myszy **pozycję Klienci RADIUS**, a następnie wybierz polecenie **Nowy**. Utwórz klienta USŁUGI RADIUS, określając następujące ustawienia:

    - **Przyjazna nazwa:** Wpisz dowolną nazwę.
    - **Adres (IP lub DNS)**: Wpisz podsieć bramy utworzoną w kroku 1.
    - **Wspólny klucz tajny:** wpisz dowolny klucz tajny i zapamiętaj go do późniejszego użycia.

      ![Obraz dotyczący ustawień klienta usługi RADIUS](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client1.png)

 
3.  Na karcie **Zaawansowane** ustaw nazwę dostawcy na **STANDARD RADIUS** i upewnij się, że pole wyboru **Opcje dodatkowe** nie jest zaznaczone.

    ![Obraz dotyczący ustawień zaawansowanych klienta RADIUS](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client2.png)

4. Przejdź do **opcji Zasady** > **zasad sieciowych**, kliknij dwukrotnie pozycję **Połączenia z zasadami serwera Routingu i dostępu zdalnego firmy Microsoft,** wybierz pozycję **Udziel dostępu**, a następnie kliknij przycisk **OK**.

### <a name="step-3-configure-the-virtual-network-gateway"></a>Krok 3 Konfigurowanie bramy sieci wirtualnej

1. Zaloguj się do [portalu Azure](https://portal.azure.com).
2. Otwórz utworzoną bramę sieci wirtualnej. Upewnij się, że typ bramy jest ustawiony na **sieć VPN** i że typ sieci VPN jest oparty **na marszruty.**
3. Kliknij **pozycję Wskaż konfigurację lokacji** > **Konfiguruj teraz,** a następnie określ następujące ustawienia:

    - **Pula adresów:** Wpisz podsieć bramy utworzoną w kroku 1.
    - **Typ uwierzytelniania**: Wybierz **uwierzytelnianie RADIUS**.
    - **Adres IP serwera:** Wpisz adres IP serwera NPS.

      ![Obraz dotyczący ustawień punktu do witryny](./media/vpn-gateway-radiuis-mfa-nsp/configure-p2s.png)

## <a name="next-steps"></a>Następne kroki

- [Uwierzytelnianie wieloskładnikowe platformy Azure](../active-directory/authentication/multi-factor-authentication.md)
- [Integrowanie istniejącej infrastruktury NPS z usługą Azure Multi-Factor Authentication](../active-directory/authentication/howto-mfa-nps-extension.md)
