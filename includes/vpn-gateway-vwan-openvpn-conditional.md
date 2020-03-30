---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/14/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 410570302eec418f1e4bcb75d6413936a96b5171
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471556"
---
Dostęp warunkowy umożliwia precyzyjną kontrolę dostępu dla aplikacji. Aby korzystać z dostępu warunkowego, należy zastosować licencjonowanie usługi Azure AD Premium 1 lub większej do użytkowników, którzy będą podlegać regułom dostępu warunkowego.

1. Przejdź do **strony Aplikacje — wszystkie aplikacje** i kliknij pozycję **Sieć VPN platformy Azure**.

   - Kliknij pozycję **Dostęp warunkowy**.
   - Kliknij **pozycję Nowe zasady,** aby otworzyć okienko **Nowe.**
2. W okienku **Nowy** przejdź do pozycji **Przydziały > Użytkownicy i grupy**. Na karcie **Użytkownicy i grupy ->** **Dołącz:**

   - Kliknij **pozycję Wybierz użytkowników i grupy**.
   - Sprawdź **użytkowników i grupy**.
   - Kliknij **przycisk Wybierz,** aby wybrać grupę lub zestaw użytkowników, na których ma mieć wpływ usługa MFA.
   - Kliknij przycisk **Gotowe**.

   ![Przypisania](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-assignments.png)
3. W okienku **Nowy** przejdź do okienka **Formanty dostępu > Grant:**

   - Kliknij **pozycję Udziel dostępu**.
   - Kliknij **pozycję Wymagaj uwierzytelniania wieloskładnikowego**.
   - Kliknij **pozycję Wymagaj wszystkich zaznaczonych kontrolek**.
   - Kliknij **pozycję Wybierz**.
   
   ![Udzielenie dostępu — usługi MFA](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-grant-mfa.png)
4. W sekcji **Włącz zasady:**

   - Wybierz **pozycję Włączone**.
   - Kliknij przycisk **Utwórz**.

   ![Włącz zasady](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-enable-policy.png)