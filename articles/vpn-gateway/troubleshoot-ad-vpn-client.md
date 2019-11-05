---
title: 'Rozwiązywanie problemów z klientem sieci VPN uwierzytelniania usługi Azure AD: Azure VPN Gateway | Microsoft Docs'
description: Rozwiązywanie problemów VPN Gateway P2S klientów uwierzytelniania usługi Azure AD
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: e239f283d5065e032e98e87a72a4bfe9fbafbdb1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517282"
---
# <a name="troubleshoot-an-azure-ad-authentication-vpn-client"></a>Rozwiązywanie problemów z klientem sieci VPN uwierzytelniania usługi Azure AD

Ten artykuł pomaga w rozwiązywaniu problemów z klientem sieci VPN w celu nawiązania połączenia z siecią wirtualną przy użyciu sieci VPN typu punkt-lokacja i uwierzytelniania Azure Active Directory.

## <a name="status"></a>Wyświetl dziennik stanu

Wyświetl dziennik stanu dla komunikatów o błędach.

![dzienniki](./media/troubleshoot-ad-vpn-client/1.png)

1. Kliknij ikonę strzałki w prawym dolnym rogu okna klienta, aby wyświetlić **dzienniki stanu**.
2. Sprawdź dzienniki pod kątem błędów, które mogą wskazywać na problem.
3. Komunikaty o błędach są wyświetlane na czerwono.

## <a name="clear"></a>Wyczyść informacje logowania

Wyczyść informacje logowania.

![logowanie](./media/troubleshoot-ad-vpn-client/2.png)

1. Wybierz... obok profilu, który chcesz rozwiązać. Wybierz pozycję **Konfiguruj-> wyczyść zapisane konto**.
2. Wybierz pozycję **Zapisz**.
3. Spróbuj nawiązać połączenie.
4. Jeśli połączenie nadal kończy się niepowodzeniem, przejdź do następnej sekcji.

## <a name="diagnostics"></a>Uruchom diagnostykę

Uruchom diagnostykę na kliencie sieci VPN.

![Diagnostyka](./media/troubleshoot-ad-vpn-client/3.png)

1. Kliknij przycisk **...** obok profilu, na którym chcesz przeprowadzić diagnostykę. Wybierz pozycję **Diagnozuj-> Uruchom diagnostykę**.
2. Klient uruchomi serię testów i wyświetli wynik testu

   * Dostęp do Internetu — sprawdza, czy klient ma łączność z Internetem
   * Poświadczenia klienta — Sprawdź, czy punkt końcowy uwierzytelniania Azure Active Directory jest osiągalny
   * Rozpoznawalny serwer — kontaktuje się z serwerem DNS w celu rozpoznania adresu IP skonfigurowanego serwera sieci VPN
   * Serwer osiągalny — sprawdza, czy serwer sieci VPN odpowiada lub nie
3. Jeśli którykolwiek z testów zakończy się niepowodzeniem, skontaktuj się z administratorem sieci, aby rozwiązać ten problem.
4. W następnej sekcji pokazano, jak zbierać dzienniki, w razie potrzeby.

## <a name="logfiles"></a>Zbierz pliki dziennika klienta

Zbierz pliki dziennika klienta sieci VPN. Pliki dziennika mogą być wysyłane do obsługi/administratorów za pomocą wybieranej metody. Na przykład poczta e-mail.

1. Kliknij przycisk "..." obok profilu, na którym chcesz przeprowadzić diagnostykę. Wybierz pozycję **Diagnozuj-> Pokaż katalog dzienników**.

   ![Pokaż dzienniki](./media/troubleshoot-ad-vpn-client/4.png)
2. Zostanie otwarty Eksplorator Windows do folderu, który zawiera pliki dziennika.

   ![Wyświetl plik](./media/troubleshoot-ad-vpn-client/5.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [Tworzenie dzierżawy Azure Active Directory dla P2S Otwórz połączenia sieci VPN korzystające z uwierzytelniania usługi Azure AD](openvpn-azure-ad-tenant.md).