---
title: Jak skonfigurować klientów OpenVPN dla bramy sieci VPN platformy Azure | Dokumentacja firmy Microsoft
description: Kroki, aby skonfigurować klientów OpenVPN dla bramy sieci VPN platformy Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 5/21/2019
ms.author: cherylmc
ms.openlocfilehash: fdfabf328ddfa6b5e4b578be5a1b329cb3219a18
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65989108"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>Konfigurowanie klientów OpenVPN dla bramy sieci VPN platformy Azure

Ten artykuł pomoże Ci skonfigurować **OpenVPN® protokołu** klientów.

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Sprawdź, że zostały wykonane kroki konfigurowania OpenVPN dla bramy sieci VPN. Aby uzyskać więcej informacji, zobacz [skonfigurować OpenVPN dla bramy sieci VPN Azure](vpn-gateway-howto-openvpn.md).

## <a name="windows"></a>Klienci Windows

1. Pobieranie i instalowanie klienta OpenVPN z oficjalnego [OpenVPN witryny sieci Web](https://openvpn.net/index.php/open-source/downloads.html).
2. Pobierz profil sieci VPN dla bramy. Można to zrobić na karcie Konfiguracja punktu do lokacji w witrynie Azure portal lub "New-AzVpnClientConfiguration" w programie PowerShell.
3. Rozpakuj profil. Następnie otwórz *vpnconfig.ovpn* pliku konfiguracji z folderu OpenVPN za pomocą Notatnika.
4. [Eksportuj](vpn-gateway-certificates-point-to-site.md#clientexport) certyfikatu klienta P2S, utworzony i przekazany do konfiguracji P2S w bramie.
5. Wyodrębnij klucz prywatny i odcisk palca base64 z *PFX*. Istnieje wiele sposobów, aby to zrobić. Przy użyciu biblioteki OpenSSL na maszynie jest jednym ze sposobów. *Profileinfo.txt* plik zawiera klucz prywatny i odcisk palca urzędu certyfikacji i certyfikatu klienta. Pamiętaj użyć odcisk palca certyfikatu klienta.

   ```
   openssl pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
   ```
6. Otwórz *profileinfo.txt* w Notatniku. Aby uzyskać odcisk palca certyfikatu klienta (podrzędne), zaznacz tekst (łącznie z i od) "---BEGIN CERTIFICATE---" i "---koniec certyfikatu---" dla elementu podrzędnego certyfikatu, a następnie skopiuj go. Certyfikat podrzędnego można zidentyfikować, sprawdzając = podmiotu / linii.
7. Przełącz się do *vpnconfig.ovpn* plik otwarty w Notatniku od kroku 3. Znajdź sekcję wskazaną poniżej i Zamień wszystko pomiędzy "cert" i "/ cert".

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. Otwórz *profileinfo.txt* w Notatniku. Aby uzyskać klucz prywatny, zaznacz tekst (łącznie z i od) "---BEGIN PRIVATE KEY---" i "---END PRIVATE KEY---" i skopiuj go.
9. Wróć do pliku vpnconfig.ovpn w programie Notatnik i Znajdź w tej sekcji. Wklej klucz prywatny, zastępując wszystko pomiędzy i "klucz" i "/ klucza".

   ```
   # P2S client root certificate private key
   # please fill this field with a PEM formatted key
   <key>
   $PRIVATEKEY
   </key>
   ```
10. Nie zmieniaj innych pól. Użyj konfiguracji wprowadzonej w danych wejściowych klienta, aby nawiązać połączenie z siecią VPN.
11. Skopiuj plik vpnconfig.ovpn do folderu C:\Program Files\OpenVPN\config.
12. Kliknij prawym przyciskiem myszy ikonę OpenVPN na pasku zadań i kliknij przycisk Połącz.

## <a name="mac"></a>Klienci na komputery Mac

1. Pobieranie i instalowanie klienta OpenVPN, takich jak [TunnelBlik](https://tunnelblick.net/downloads.html). 
2. Pobierz profil sieci VPN dla bramy. Można to zrobić na karcie Konfiguracja punktu do lokacji w witrynie Azure portal lub za pomocą "New-AzVpnClientConfiguration" w programie PowerShell.
3. Rozpakuj profil. Otwórz plik konfiguracji vpnconfig.ovpn z folderu OpenVPN w Notatniku.
4. W sekcji certyfikatu klienta P2S wprowadź klucz publiczny certyfikatu klienta P2S w formacie base64. W przypadku certyfikatu w formacie PEM można po prostu otworzyć plik cer i skopiować klucz w formacie base64 znajdujący się pomiędzy nagłówkami certyfikatów. Zobacz [wyeksportować klucz publiczny](vpn-gateway-certificates-point-to-site.md#cer) informacji o sposobie eksportowania certyfikatu można pobrać zakodowanego klucza publicznego.
5. W sekcji klucza prywatnego wprowadź klucz prywatny certyfikatu klienta P2S w formacie base64. Zobacz [Eksportuj klucz prywatny](https://openvpn.net/community-resources/how-to/#pki) informacji o tym, jak można wyodrębnić klucza prywatnego.
6. Nie zmieniaj innych pól. Użyj konfiguracji wprowadzonej w danych wejściowych klienta, aby nawiązać połączenie z siecią VPN.
7. Kliknij dwukrotnie plik profilu, aby utworzyć profil w tunnelblik.
8. Uruchom Tunnelblik z folderu aplikacji.
9. Kliknij ikonę Tunnelblik na pasku zadań i połącz wyboru.

> [!IMPORTANT]
>Tylko system iOS 11.0 i nowszych i MacOS 10.13 i nowszej są obsługiwane przy użyciu protokołu OpenVPN.
>

## <a name="linux"></a>Klienci systemu Linux

1. Otwórz nową sesję terminala. Możesz otworzyć nową sesję, naciskając klawisz "Ctrl + Alt + t", w tym samym czasie.
2. Wprowadź następujące polecenie, aby zainstalować wymagane składniki:

   ```
   sudo apt-get install openvpn
   sudo apt-get -y install network-manager-openvpn
   sudo service network-manager restart
   ```
3. Pobierz profil sieci VPN dla bramy. Można to zrobić na karcie Konfiguracja punktu do lokacji w witrynie Azure portal.
4. [Eksportuj](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-certificates-point-to-site#clientexport) certyfikatu klienta P2S, utworzony i przekazany do konfiguracji P2S w bramie. 
5. Wyodrębnij klucz prywatny i odcisk palca base64 z plik pfx. Istnieje wiele sposobów, aby to zrobić. Przy użyciu biblioteki OpenSSL na komputerze jest jednym ze sposobów.

    ```
    openssl.exe pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
    ```
   *Profileinfo.txt* plik będzie zawierać klucz prywatny i odcisk palca urzędu certyfikacji i certyfikatu klienta. Pamiętaj użyć odcisk palca certyfikatu klienta.

6. Otwórz *profileinfo.txt* w edytorze tekstów. Aby uzyskać odcisk palca certyfikatu klienta (podrzędne), zaznacz tekst, włącznie z i od "---początek certyfikatu---" i "---koniec certyfikatu---" dla elementu podrzędnego certyfikatu, a następnie skopiuj go. Certyfikat podrzędnego można zidentyfikować, sprawdzając = podmiotu / linii.

7. Otwórz *vpnconfig.ovpn* plików i Znajdź sekcję wskazaną poniżej. Zamień wszystko pomiędzy i "cert" i "/ cert".

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. Otwórz profileinfo.txt w edytorze tekstów. Aby uzyskać klucz prywatny, zaznacz tekst, włącznie z i od "---początek klucza prywatnego---" i "---END PRIVATE KEY---" i skopiuj go.

9. Otwórz plik vpnconfig.ovpn w edytorze tekstów i Znajdź w tej sekcji. Wklej klucz prywatny, zastępując wszystko pomiędzy i "klucz" i "/ klucza".

   ```
   # P2S client root certificate private key
   # please fill this field with a PEM formatted key
   <key>
   $PRIVATEKEY
   </key>
   ```

10. Nie zmieniaj innych pól. Użyj konfiguracji wprowadzonej w danych wejściowych klienta, aby nawiązać połączenie z siecią VPN.
11. Nawiązywanie połączenia przy użyciu wiersza polecenia, wpisz następujące polecenie:
  
    ```
    sudo openvpn –-config <name and path of your VPN profile file>
    ```
12. Nawiązywanie połączenia przy użyciu graficznego interfejsu użytkownika, przejdź do ustawień systemu.
13. Kliknij przycisk **+** można dodać nowe połączenie VPN.
14. W obszarze **dodać sieci VPN**, wybierz **Importuj z pliku...**
15. Przejdź do pliku profilu i kliknij dwukrotnie plik lub wybierz **Otwórz**.
16. Kliknij przycisk **Dodaj** na **dodać sieci VPN** okna.
  
    ![Importuj z pliku](./media/vpn-gateway-howto-openvpn-clients/importfromfile.png)
17. Można połączyć, wyłączając sieć VPN **ON** na **ustawienia sieciowe** strony, lub równy podanemu ikonę sieci na pasku zadań.

## <a name="next-steps"></a>Kolejne kroki

Jeśli chcesz, aby klienci sieci VPN, aby można było uzyskać dostęp do zasobów w innej sieci wirtualnej, następnie postępuj zgodnie z instrukcjami [sieć wirtualna-sieć wirtualna](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) artykuł, aby skonfigurować połączenie sieć wirtualna-sieć wirtualna. Pamiętaj włączyć protokół BGP dla bramy i połączenia, w przeciwnym razie ruch nie będzie przepływać.

**"OpenVPN" jest zastrzeżonym znakiem towarowym firmy OpenVPN Inc.**
