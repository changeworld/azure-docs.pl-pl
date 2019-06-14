---
title: Bezpieczne sieci VPN platformy Azure brama uwierzytelnianie usługi RADIUS za pomocą serwera NPS do uwierzytelniania wieloskładnikowego | Dokumentacja firmy Microsoft
description: W tym artykule opisano integrowanie uwierzytelniania RADIUS brama platformy Azure za pomocą serwera NPS do uwierzytelniania wieloskładnikowego.
services: vpn-gateway
documentationcenter: na
author: ahmadnyasin
manager: willchen
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 4e11b1bc16f874f892288f9677a71023f483de7c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60458126"
---
# <a name="integrate-azure-vpn-gateway-radius-authentication-with-nps-server-for-multi-factor-authentication"></a>Integrowanie uwierzytelniania RADIUS bramy sieci VPN platformy Azure za pomocą serwera NPS do uwierzytelniania wieloskładnikowego 

W artykule opisano sposób integracji serwera zasad sieciowych (NPS) z uwierzytelnianiem RADIUS bramy sieci VPN platformy Azure, aby dostarczać usługi Multi-Factor Authentication (MFA) dla połączeń VPN typu punkt lokacja. 

## <a name="prerequisite"></a>Wymagania wstępne

Aby włączyć uwierzytelnianie wieloskładnikowe, użytkownicy muszą być w usłudze Active Directory (Azure AD), które muszą zostać zsynchronizowane z lokalnie lub w chmurze, środowisku. Ponadto użytkownik musi wykonano już procesu automatycznej rejestracji usługi MFA.  Aby uzyskać więcej informacji, zobacz [Skonfiguruj moje konto na potrzeby weryfikacji dwuetapowej](../active-directory/user-help/multi-factor-authentication-end-user-first-time.md)

## <a name="detailed-steps"></a>Szczegółowe procedury

### <a name="step-1-create-a-virtual-network-gateway"></a>Krok 1: Tworzenie bramy sieci wirtualnej

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W sieci wirtualnej, który będzie hostował bramy sieci wirtualnej, wybierz **podsieci**, a następnie wybierz pozycję **podsieci bramy** utworzyć podsieć. 

    ![Obraz o tym, jak dodać podsieć bramy](./media/vpn-gateway-radiuis-mfa-nsp/gateway-subnet.png)
3. Utwórz bramę sieci wirtualnej, określając następujące ustawienia:

    - **Typ bramy**: wybierz pozycję **VPN**.
    - **Typ sieci VPN**: wybierz pozycję **Oparte na trasach**.
    - **SKU**: Wybierz typ jednostki SKU, w zależności od wymagań.
    - **Sieć wirtualna**: Wybierz sieć wirtualną, w której utworzono podsieć bramy.

        ![Obraz dotyczący ustawień bramy sieci wirtualnej](./media/vpn-gateway-radiuis-mfa-nsp/create-vpn-gateway.png)


 
### <a name="step-2-configure-the-nps-for-azure-mfa"></a>Krok 2 skonfiguruj serwer NPS dla usługi Azure MFA

1. Na serwerze NPS [zainstalować rozszerzenia serwera NPS dla usługi Azure MFA](../active-directory/authentication/howto-mfa-nps-extension.md#install-the-nps-extension).
2. Otwórz konsolę NSP, kliknij prawym przyciskiem myszy **klientów RADUIS**, a następnie wybierz pozycję **New**. Tworzenie klienta RADUIS, określając następujące ustawienia:

    - **Przyjazna nazwa**: Wpisz dowolną nazwę.
    - **Adres (IP lub DNS)** : Wpisz podsieć bramy, który został utworzony w kroku 1.
    - **Wspólny klucz tajny**: wpisz wszelkie klucz tajny i Zapamiętaj je w celu późniejszego użycia.

      ![Obraz informacje o ustawieniach klienta RADUIS](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client1.png)

 
3.  Na **zaawansowane** karcie należy ustawić nazwę dostawcy **RADIUS Standard** i upewnij się, że **dodatkowe opcje** nie zaznaczono pole wyboru.

    ![Obraz o zaawansowanych ustawieniach RADUIS](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client2.png)

4. Przejdź do **zasady** > **zasad sieciowych**, kliknij dwukrotnie **połączenia z serwerem programu Microsoft Routing i dostęp zdalny** zasad, wybierz opcję  **Udzielanie dostępu**, a następnie kliknij przycisk **OK**.

### <a name="step-3-configure-the-virtual-network-gateway"></a>Krok 3 Konfigurowanie bramy sieci wirtualnej

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com).
2. Otwórz bramy sieci wirtualnej, który został utworzony. Upewnij się, że typ bramy jest ustawiona na **VPN** i czy typ sieci VPN jest **oparte na trasach**.
3. Kliknij przycisk **wskaż konfigurację witryny** > **teraz skonfigurować**, a następnie określ następujące ustawienia:

    - **Pula adresów**: Wpisz podsieć bramy, który został utworzony w kroku 1.
    - **Typ uwierzytelniania**: Wybierz **uwierzytelnianie usługi RADIUS**.
    - **Adres IP serwera**: Wpisz adres IP serwera NPS.

      ![Obraz dotyczący wskaż Ustawienia witryny](./media/vpn-gateway-radiuis-mfa-nsp/configure-p2s.png)

## <a name="next-steps"></a>Kolejne kroki

- [Usługa Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md)
- [Integrowanie istniejącej infrastruktury NPS z usługą Azure Multi-Factor Authentication](../active-directory/authentication/howto-mfa-nps-extension.md)
