---
title: Rozwiązywanie problemów z połączeniami sieci VPN Azure punkt-lokacja z klientów systemu Mac OS X | Dokumentacja firmy Microsoft
description: Kroki rozwiązywania problemów z połączeń klienckich P2S Mac OS X w sieci VPN
services: vpn-gateway
documentationcenter: na
author: anzaman
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2018
ms.author: alzam
ms.openlocfilehash: 1cf8195cbf65f27c71a4db18c0c61c8a25673acd
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="troubleshoot-point-to-site-vpn-connections-from-mac-os-x-vpn-clients"></a>Rozwiązywanie problemów z połączeniami sieci VPN typu punkt-lokacja z klientów Mac OS X w sieci VPN

W tym artykule pomocy w rozwiązywaniu problemów z połączeniem punkt-lokacja z systemem Mac OS X przy użyciu natywny klient VPN i IKEv2. Klient VPN komputerów Mac na potrzeby protokołu IKEv2 jest bardzo proste i nie zezwala na wiele dostosowania. Istnieją cztery tylko ustawienia, które muszą zostać sprawdzone:

* Adres serwera
* Identyfikator zdalnego
* Identyfikator lokalny
* Ustawienia uwierzytelniania
* Wersja systemu operacyjnego (10.11 lub nowszej)


## <a name="VPNClient"></a> Rozwiązywanie problemów z uwierzytelniania opartego na certyfikatach
1. Sprawdź ustawienia klienta sieci VPN. Przejdź do **ustawienie sieci** przez naciśnięcie przycisku polecenia + Shift, a następnie wpisz "VPN" do sprawdzenia ustawień klienta sieci VPN. Na liście kliknij wpis sieci VPN, który musi być zbadana.

  ![Protokół IKEv2 uwierzytelniania opartego na certyfikatach](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2cert1.jpg)
2. Sprawdź, czy **adres serwera** jest pełną nazwę FQDN i obejmuje cloudapp.net.
3. **Identyfikator zdalny** powinien być taki sam jak adres serwera (nazwa FQDN bramy).
4. **Identyfikator lokalny** powinna być taka sama jak **podmiotu** certyfikatu klienta.
5. Polecenie **ustawienia uwierzytelniania** aby otworzyć stronę ustawienia uwierzytelniania.

  ![Ustawienia uwierzytelniania](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth2.jpg)
6. Sprawdź, czy **certyfikatu** wybrany z listy rozwijanej.
7. Kliknij przycisk **wybierz** przycisk i sprawdź, czy wybrano prawidłowego certyfikatu. Kliknij przycisk **OK** Aby zapisać zmiany.

## <a name="ikev2"></a>Rozwiązywanie problemów z uwierzytelniania nazwa użytkownika i hasło

1. Sprawdź ustawienia klienta sieci VPN. Przejdź do **ustawienie sieci** przez naciśnięcie przycisku polecenia + Shift, a następnie wpisz "VPN" do sprawdzenia ustawień klienta sieci VPN. Na liście kliknij wpis sieci VPN, który musi być zbadana.

  ![Hasło nazwy użytkownika IKEv2](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2user3.jpg)
2. Sprawdź, czy **adres serwera** jest pełną nazwę FQDN i obejmuje cloudapp.net.
3. **Identyfikator zdalny** powinien być taki sam jak adres serwera (nazwa FQDN bramy).
4. **Identyfikator lokalny** może być pusta.
5. Kliknij przycisk **ustawienia uwierzytelniania** przycisk i sprawdź, czy wybrano "Nazwa_użytkownika", z listy rozwijanej.

  ![Ustawienia uwierzytelniania](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth4.jpg)
6. Sprawdź, czy wprowadzono poprawne poświadczenia.

## <a name="additional"></a>Dodatkowe kroki

Jeśli poprzednie kroki wszystko, co jest skonfigurowany prawidłowo, należy pobrać [Wireshark](https://www.wireshark.org/#download) i wykonywać przechwytywania pakietów.

1. Filtrowanie według *iskmp* i przyjrzyj się **IKE_SA** pakietów. Powinno być możliwe do przeglądania informacji propozycji SA w obszarze **ładunku: skojarzenia zabezpieczeń**. 
2. Sprawdź, czy klient i serwer cechują się wspólnym zestawem.

  ![Pakiet](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/packet5.jpg)

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać dodatkową pomoc, zobacz [Microsoft Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
