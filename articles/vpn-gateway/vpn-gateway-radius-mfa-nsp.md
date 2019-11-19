---
title: Bezpieczne sieci VPN platformy Azure brama uwierzytelnianie usługi RADIUS za pomocą serwera NPS do uwierzytelniania wieloskładnikowego | Dokumentacja firmy Microsoft
description: W tym artykule opisano integrowanie uwierzytelniania RADIUS brama platformy Azure za pomocą serwera NPS do uwierzytelniania wieloskładnikowego.
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
ms.openlocfilehash: ab152cca1d809d92803a3e50ea83da1cbcd8243c
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74174618"
---
# <a name="integrate-azure-vpn-gateway-radius-authentication-with-nps-server-for-multi-factor-authentication"></a>Integrowanie uwierzytelniania RADIUS bramy sieci VPN platformy Azure za pomocą serwera NPS do uwierzytelniania wieloskładnikowego 

W artykule opisano sposób integracji serwera zasad sieciowych (NPS) z uwierzytelnianiem RADIUS bramy sieci VPN platformy Azure, aby dostarczać usługi Multi-Factor Authentication (MFA) dla połączeń VPN typu punkt lokacja. 

## <a name="prerequisite"></a>Warunek wstępny

Aby włączyć uwierzytelnianie wieloskładnikowe, użytkownicy muszą być w usłudze Active Directory (Azure AD), które muszą zostać zsynchronizowane z lokalnie lub w chmurze, środowisku. Ponadto użytkownik musi wykonano już procesu automatycznej rejestracji usługi MFA.  Aby uzyskać więcej informacji, zobacz [Skonfiguruj moje konto na potrzeby weryfikacji dwuetapowej](../active-directory/user-help/multi-factor-authentication-end-user-first-time.md)

## <a name="detailed-steps"></a>Szczegółowe procedury

### <a name="step-1-create-a-virtual-network-gateway"></a>Krok 1: Tworzenie bramy sieci wirtualnej

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W sieci wirtualnej, który będzie hostował bramy sieci wirtualnej, wybierz **podsieci**, a następnie wybierz pozycję **podsieci bramy** utworzyć podsieć. 

    ![Obraz o tym, jak dodać podsieć bramy](./media/vpn-gateway-radiuis-mfa-nsp/gateway-subnet.png)
3. Utwórz bramę sieci wirtualnej, określając następujące ustawienia:

    - **Typ bramy**: Wybierz pozycję **Sieć VPN**.
    - **Typ sieci VPN**: Wybierz **oparte na trasach**.
    - **Jednostka SKU**: Wybierz typ jednostki SKU zgodnie z wymaganiami.
    - **Sieć wirtualna**: Wybierz sieć wirtualną, w której utworzono podsieć bramy.

        ![Obraz dotyczący ustawień bramy sieci wirtualnej](./media/vpn-gateway-radiuis-mfa-nsp/create-vpn-gateway.png)


 
### <a name="step-2-configure-the-nps-for-azure-mfa"></a>Krok 2 skonfiguruj serwer NPS dla usługi Azure MFA

1. Na serwerze NPS [zainstalować rozszerzenia serwera NPS dla usługi Azure MFA](../active-directory/authentication/howto-mfa-nps-extension.md#install-the-nps-extension).
2. Otwórz konsolę serwera zasad sieciowych, kliknij prawym przyciskiem myszy pozycję **klienci usługi RADIUS**, a następnie wybierz pozycję **Nowy**. Utwórz klienta usługi RADIUS, określając następujące ustawienia:

    - **Przyjazna nazwa**: wpisz dowolną nazwę.
    - **Adres (IP lub DNS)** : wpisz podsieć bramy, który został utworzony w kroku 1.
    - **Wspólny klucz tajny**: wpisz wszelkie klucz tajny i Zapamiętaj je w celu późniejszego użycia.

      ![Obraz dotyczący ustawień klienta usługi RADIUS](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client1.png)

 
3.  Na **zaawansowane** karcie należy ustawić nazwę dostawcy **RADIUS Standard** i upewnij się, że **dodatkowe opcje** nie zaznaczono pole wyboru.

    ![Obraz dotyczący ustawień zaawansowanych klienta usługi RADIUS](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client2.png)

4. Przejdź do **zasady** > **zasad sieciowych**, kliknij dwukrotnie **połączenia z serwerem programu Microsoft Routing i dostęp zdalny** zasad, wybierz opcję  **Udzielanie dostępu**, a następnie kliknij przycisk **OK**.

### <a name="step-3-configure-the-virtual-network-gateway"></a>Krok 3 Konfigurowanie bramy sieci wirtualnej

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com).
2. Otwórz bramy sieci wirtualnej, który został utworzony. Upewnij się, że typ bramy jest ustawiona na **VPN** i czy typ sieci VPN jest **oparte na trasach**.
3. Kliknij przycisk **wskaż konfigurację witryny** > **teraz skonfigurować**, a następnie określ następujące ustawienia:

    - **Pula adresów**: wpisz podsieć bramy, utworzony w kroku 1.
    - **Typ uwierzytelniania**: Wybierz **uwierzytelnianie usługi RADIUS**.
    - **Adres IP serwera**: wpisz adres IP serwera NPS.

      ![Obraz dotyczący wskaż Ustawienia witryny](./media/vpn-gateway-radiuis-mfa-nsp/configure-p2s.png)

## <a name="next-steps"></a>Następne kroki

- [Usługa Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md)
- [Integrowanie istniejącej infrastruktury NPS z usługą Azure Multi-Factor Authentication](../active-directory/authentication/howto-mfa-nps-extension.md)
