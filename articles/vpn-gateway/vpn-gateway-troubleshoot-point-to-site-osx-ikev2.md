---
title: 'VPN Gateway platformy Azure: Rozwiązywanie problemów z połączeniami punkt-lokacja: Mac OS X klientów'
description: Kroki umożliwiające rozwiązywanie problemów z połączeniami klienta sieci VPN P2S Mac OS X
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 03/27/2018
ms.author: alzam
ms.openlocfilehash: f76fd7bce539ebcf79216aabb5bf868b2d18107a
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75780185"
---
# <a name="troubleshoot-point-to-site-vpn-connections-from-mac-os-x-vpn-clients"></a>Rozwiązywanie problemów z połączeniami sieci VPN typu punkt-lokacja z klientów Mac OS X w sieci VPN

Ten artykuł pomaga rozwiązać problemy z połączeniem punkt-lokacja z systemem Mac OS X przy użyciu natywnego klienta sieci VPN i protokołu IKEv2. Klient VPN Mac dla protokołu IKEv2 jest bardzo proste i nie zezwala na wiele dostosowywania. Dostępne są tylko cztery ustawienia, które muszą być sprawdzone:

* Adres serwera
* Identyfikator zdalnego
* Identyfikator lokalny
* Ustawienia uwierzytelniania
* Wersja systemu operacyjnego (10.11 lub nowszej)


## <a name="VPNClient"></a> Rozwiązywanie problemów z uwierzytelniania opartego na certyfikatach
1. Sprawdź ustawienia klienta sieci VPN. Przejdź do **ustawienia sieci** naciskając polecenie + klawisz Shift, a następnie wpisz "Sieć VPN" do sprawdzenia ustawień klienta sieci VPN. Z listy kliknij wpis sieci VPN, który musi zostać zbadana.

   ![Uwierzytelnianie oparte na certyfikatach protokołu IKEv2](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2cert1.jpg)
2. Upewnij się, że **adres serwera** jest pełną nazwę FQDN i uwzględnia cloudapp.net.
3. **Identyfikator zdalnego** powinna być taka sama jak adres serwera (nazwa FQDN bramy).
4. **Lokalnego Identyfikatora** powinna być taka sama jak **podmiotu** certyfikatu klienta.
5. Kliknij pozycję **ustawienia uwierzytelniania** o otwarcie strony ustawień uwierzytelniania.

   ![ustawienia uwierzytelniania](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth2.jpg)
6. Upewnij się, że **certyfikatu** wybrania z listy rozwijanej.
7. Kliknij przycisk **wybierz** znajdujący się i sprawdź, czy wybrano prawidłowy certyfikat. Kliknij przycisk **OK** umożliwia zapisanie wszelkich zmian.

## <a name="ikev2"></a>Rozwiązywanie problemów z uwierzytelniania nazwy użytkownika i hasła

1. Sprawdź ustawienia klienta sieci VPN. Przejdź do **ustawienia sieci** naciskając polecenie + klawisz Shift, a następnie wpisz "Sieć VPN" do sprawdzenia ustawień klienta sieci VPN. Z listy kliknij wpis sieci VPN, który musi zostać zbadana.

   ![Hasło nazwy użytkownika protokołu IKEv2](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2user3.jpg)
2. Upewnij się, że **adres serwera** jest pełną nazwę FQDN i uwzględnia cloudapp.net.
3. **Identyfikator zdalnego** powinna być taka sama jak adres serwera (nazwa FQDN bramy).
4. **Lokalnego Identyfikatora** może być pusta.
5. Kliknij przycisk **ustawienia uwierzytelniania** znajdujący się i sprawdź, czy wybrano "Nazwa_użytkownika", z listy rozwijanej.

   ![ustawienia uwierzytelniania](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth4.jpg)
6. Sprawdź, czy wprowadzono poprawne poświadczenia.

## <a name="additional"></a>Dodatkowe kroki

Jeśli spróbujesz poprzednich kroków i wszystko jest poprawnie skonfigurowana, Pobierz [Wireshark](https://www.wireshark.org/#download) i wykonać przechwytywania pakietów.

1. Filtrowanie według *isakmp* i przyjrzyj się **IKE_SA** pakietów. Powinno być możliwe wyświetlić szczegóły propozycji skojarzeń zabezpieczeń, w obszarze **ładunek: oferty skojarzeń zabezpieczeń**. 
2. Sprawdź, czy klient i serwer cechują się wspólnym zestawem.

   ![pakiet](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/packet5.jpg) 
  
3. Jeśli istnieje serwer nie odpowiedział na danych śledzenia sieci, sprawdź, czy włączono protokół IKEv2, na stronie konfiguracji bramy platformy Azure w witrynie Azure Portal.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać dodatkową pomoc, zobacz [Microsoft Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
