---
title: 'Brama sieci VPN: rozwiązywanie problemów z klientem sieci VPN — uwierzytelnianie usługi Azure AD'
description: Rozwiązywanie problemów z klientami uwierzytelniania usługi Azure AD bramy sieci VPN
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 8871e92f0911c4d3cbcc1772bef1daeb5c70b5d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74151976"
---
# <a name="troubleshoot-an-azure-ad-authentication-vpn-client"></a>Rozwiązywanie problemów z klientem sieci VPN uwierzytelniania usługi Azure AD

Ten artykuł ułatwia rozwiązywanie problemów z klientem sieci VPN w celu nawiązania połączenia z siecią wirtualną przy użyciu sieci VPN typu lokacja i uwierzytelniania usługi Azure Active Directory.

## <a name="view-status-log"></a><a name="status"></a>Wyświetl dziennik stanu

Wyświetl dziennik stanu dla komunikatów o błędach.

![dzienniki](./media/troubleshoot-ad-vpn-client/1.png)

1. Kliknij ikonę strzałek w prawym dolnym rogu okna klienta, aby wyświetlić **dzienniki stanu**.
2. Sprawdź dzienniki pod kątem błędów, które mogą wskazywać na problem.
3. Komunikaty o błędach są wyświetlane na czerwono.

## <a name="clear-sign-in-information"></a><a name="clear"></a>Wyczyść informacje logowania

Wyczyść informacje logowania.

![logowanie](./media/troubleshoot-ad-vpn-client/2.png)

1. Wybierz ... obok profilu, który chcesz rozwiązać. Wybierz **pozycję Konfiguruj -> wyczyść zapisane konto**.
2. Wybierz **pozycję Zapisz**.
3. Spróbuj nawiązać połączenie.
4. Jeśli połączenie nadal nie powiedzie się, przejdź do następnej sekcji.

## <a name="run-diagnostics"></a><a name="diagnostics"></a>Uruchamianie diagnostyki

Uruchom diagnostykę na kliencie sieci VPN.

![Diagnostyka](./media/troubleshoot-ad-vpn-client/3.png)

1. Kliknij **przycisk ...** obok profilu, na który chcesz uruchomić diagnostykę. Wybierz **opcję Diagnozuj -> Uruchom diagnostykę**.
2. Klient uruchomi serię testów i wyświetli wynik testu

   * Dostęp do Internetu — sprawdza, czy klient ma łączność z Internetem
   * Poświadczenia klienta — sprawdź, czy punkt końcowy uwierzytelniania usługi Azure Active Directory jest osiągalny
   * Serwer rozwiązywalny — kontaktuje się z serwerem DNS w celu rozpoznania adresu IP skonfigurowanego serwera sieci VPN
   * Serwer osiągalny — sprawdza, czy serwer sieci VPN odpowiada, czy nie
3. Jeśli którykolwiek z testów nie powiedzie się, skontaktuj się z administratorem sieci, aby rozwiązać problem.
4. W następnej sekcji pokazano, jak zbierać dzienniki, jeśli to konieczne.

## <a name="collect-client-log-files"></a><a name="logfiles"></a>Zbieranie plików dziennika klienta

Zbieranie plików dziennika klienta sieci VPN. Pliki dziennika mogą być wysyłane do pomocy technicznej/administratora za pomocą wybranej metody. Na przykład e-mail.

1. Kliknij "..." obok profilu, na który chcesz uruchomić diagnostykę. Wybierz **pozycję Diagnozuj -> Pokaż katalog dzienników**.

   ![pokaż dzienniki](./media/troubleshoot-ad-vpn-client/4.png)
2. Eksplorator Windows otwiera folder zawierający pliki dziennika.

   ![plik widoku](./media/troubleshoot-ad-vpn-client/5.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [Tworzenie dzierżawy usługi Azure Active Directory dla połączeń P2S Open VPN korzystających z uwierzytelniania usługi Azure AD](openvpn-azure-ad-tenant.md).