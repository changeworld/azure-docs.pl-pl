---
title: 'Brama sieci VPN platformy Azure: rozwiązywanie problemów z połączeniami typu "punkt-lokacja": klienci systemu Mac OS X'
description: Kroki rozwiązywania problemów z połączeniami klientów sieci VPN p2S Mac OS X
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 03/27/2018
ms.author: alzam
ms.openlocfilehash: f88053c93884e10e46a0f7d70106bda67b057562
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425729"
---
# <a name="troubleshoot-point-to-site-vpn-connections-from-mac-os-x-vpn-clients"></a>Rozwiązywanie problemów z połączeniami sieci VPN typu punkt-lokacja z klientami sieci VPN w systemie Mac OS X

Ten artykuł ułatwia rozwiązywanie problemów z łącznością typu punkt-lokacja z systemu Mac OS X przy użyciu natywnego klienta sieci VPN i protokołu IKEv2. Klient VPN w macu dla IKEv2 jest bardzo prosty i nie pozwala na wiele dostosowywania. Istnieją tylko cztery ustawienia, które należy sprawdzić:

* Adres serwera
* Identyfikator zdalny
* Identyfikator lokalny
* Ustawienia uwierzytelniania
* Wersja systemu operacyjnego (10.11 lub nowsza)


## <a name="troubleshoot-certificate-based-authentication"></a><a name="VPNClient"></a>Rozwiązywanie problemów z uwierzytelnianiem opartym na certyfikatach
1. Sprawdź ustawienia klienta sieci VPN. Przejdź do **ustawienia sieci,** naciskając klawisze Command + Shift, a następnie wpisz "VPN", aby sprawdzić ustawienia klienta sieci VPN. Na liście kliknij wpis sieci VPN, który musi zostać zbadany.

   ![Uwierzytelnianie oparte na certyfikatach IKEv2](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2cert1.jpg)
2. Sprawdź, czy **adres serwera** jest kompletnym fqdn i zawiera cloudapp.net.
3. Identyfikator **zdalny** powinien być taki sam jak adres serwera (nazwa FQDN bramy).
4. **Identyfikator lokalny** powinien być taki sam jak **przedmiot** certyfikatu klienta.
5. Kliknij **ustawienia uwierzytelniania,** aby otworzyć stronę Ustawienia uwierzytelniania.

   ![Ustawienia uwierzytelniania](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth2.jpg)
6. Sprawdź, czy **certyfikat** jest wybrany z listy rozwijanej.
7. Kliknij przycisk **Wybierz** i sprawdź, czy jest zaznaczony właściwy certyfikat. Kliknij **przycisk OK,** aby zapisać wszelkie zmiany.

## <a name="troubleshoot-username-and-password-authentication"></a><a name="ikev2"></a>Rozwiązywanie problemów z uwierzytelnianiem nazwy użytkownika i hasła

1. Sprawdź ustawienia klienta sieci VPN. Przejdź do **ustawienia sieci,** naciskając klawisze Command + Shift, a następnie wpisz "VPN", aby sprawdzić ustawienia klienta sieci VPN. Na liście kliknij wpis sieci VPN, który musi zostać zbadany.

   ![Hasło nazwy użytkownika IKEv2](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2user3.jpg)
2. Sprawdź, czy **adres serwera** jest kompletnym fqdn i zawiera cloudapp.net.
3. Identyfikator **zdalny** powinien być taki sam jak adres serwera (nazwa FQDN bramy).
4. **Identyfikator lokalny** może być pusty.
5. Kliknij przycisk **Ustawienia uwierzytelniania** i sprawdź, czy z listy rozwijanej wybrano opcję "Nazwa użytkownika".

   ![Ustawienia uwierzytelniania](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth4.png)
6. Sprawdź, czy wprowadzone są poprawne poświadczenia.

## <a name="additional-steps"></a><a name="additional"></a>Dodatkowe kroki

Jeśli spróbujesz wykonać poprzednie kroki i wszystko jest poprawnie skonfigurowane, pobierz [Wireshark](https://www.wireshark.org/#download) i wykonaj przechwytywanie pakietów.

1. Filtruj *na isakmp* i spójrz na **pakiety IKE_SA.** Powinieneś być w stanie przyjrzeć się szczegóły propozycji SA w ramach **Payload: Security Association**. 
2. Sprawdź, czy klient i serwer mają wspólny zestaw.

   ![Pakietów](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/packet5.jpg) 
  
3. Jeśli nie ma odpowiedzi serwera na ślady sieci, sprawdź, czy włączono protokół IKEv2 na stronie Konfiguracja bramy azure w witrynie sieci Web witryny azure portal.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać dodatkową pomoc, zobacz [Pomoc techniczna firmy Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
