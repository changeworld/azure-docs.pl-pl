---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 921e22e637782ffd744af1a28e6bd43e7dd53c67
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066107"
---
## <a name="windows-clients"></a><a name="windows"></a>Klienci systemu Windows

1. Pobierz i zainstaluj klienta OpenVPN (wersja 2.4 lub nowsza) z oficjalnej [strony OpenVPN](https://openvpn.net/index.php/open-source/downloads.html).
2. Pobierz profil sieci VPN dla bramy. Można to zrobić na karcie Konfiguracja point-to-site w witrynie Azure portal lub "New-AzVpnClientConfiguration" w programie PowerShell.
3. Rozpakuj profil. Następnie otwórz plik konfiguracyjny *vpnconfig.ovpn* z folderu OpenVPN za pomocą Notatnika.
4. Wyeksportuj utworzony i przekazany certyfikat klienta typu punkt-lokacja do konfiguracji P2S na bramie. Użyj następujących łączy artykułów:

   * Instrukcje [dotyczące bramy sieci VPN](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport)
   
   * Instrukcje [wirtualnej sieci WAN](../articles/virtual-wan/certificates-point-to-site.md#clientexport)
5. Wyodrębnij klucz prywatny i odcisk palca base64 z *pliku .pfx*. Istnieje wiele sposobów, aby to zrobić. Korzystanie z OpenSSL na komputerze jest jednym ze sposobów. Plik *profileinfo.txt* zawiera klucz prywatny i odcisk palca dla urzędu certyfikacji i certyfikatu klienta. Pamiętaj, aby użyć odcisku palca certyfikatu klienta.

   ```
   openssl pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
   ```
6. Otwórz *plik profileinfo.txt* w Notatniku. Aby uzyskać odcisk palca certyfikatu klienta (podrzędnego), zaznacz tekst (w tym między)"-----BEGIN CERTIFICATE-----" i "-----END CERTIFICATE-----" dla certyfikatu podrzędnego i skopiuj go. Certyfikat podrzędny można zidentyfikować, patrząc na temat=/ wiersz.
7. Przełącz się na plik *vpnconfig.ovpn* otwarty w Notatniku z kroku 3. Znajdź sekcję pokazaną poniżej i zastąp wszystko między "cert" i "/cert".

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. Otwórz *plik profileinfo.txt* w Notatniku. Aby uzyskać klucz prywatny, zaznacz tekst (w tym i między) "-----BEGIN KLUCZ PRYWATNY-----" i "-----END KLUCZ PRYWATNY-----" i skopiuj go.
9. Wróć do pliku vpnconfig.ovpn w Notatniku i znajdź tę sekcję. Wklej klucz prywatny zastępując wszystko między i "key" i "/key".

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

## <a name="mac-clients"></a><a name="mac"></a>Klienci komputerów Mac

1. Pobierz i zainstaluj klienta OpenVPN, takiego jak [TunnelBlick](https://tunnelblick.net/downloads.html). 
2. Pobierz profil sieci VPN dla bramy. Można to zrobić na karcie konfiguracji typu punkt-lokacja w witrynie Azure portal lub przy użyciu "New-AzVpnClientConfiguration" w programie PowerShell.
3. Rozpakuj profil. Otwórz plik konfiguracyjny vpnconfig.ovpn z folderu OpenVPN w edytorze tekstu.
4. W sekcji certyfikatu klienta P2S wprowadź klucz publiczny certyfikatu klienta P2S w formacie base64. W przypadku certyfikatu w formacie PEM można po prostu otworzyć plik cer i skopiować klucz w formacie base64 znajdujący się pomiędzy nagłówkami certyfikatów. Skorzystaj z następujących łączy artykułów, aby uzyskać informacje na temat eksportowania certyfikatu w celu uzyskania zakodowanego klucza publicznego:

   * Instrukcje [dotyczące bramy sieci VPN](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#cer) 
   
   * Instrukcje [wirtualnej sieci WAN](../articles/virtual-wan/certificates-point-to-site.md#cer)
5. W sekcji klucza prywatnego wprowadź klucz prywatny certyfikatu klienta P2S w formacie base64. Zobacz [Eksportowanie klucza prywatnego](https://openvpn.net/community-resources/how-to/#pki) w witrynie OpenVPN, aby uzyskać informacje na temat wyodrębniania klucza prywatnego.
6. Nie zmieniaj innych pól. Użyj konfiguracji wprowadzonej w danych wejściowych klienta, aby nawiązać połączenie z siecią VPN.
7. Kliknij dwukrotnie plik profilu, aby utworzyć profil w Tunnelblick.
8. Uruchom Tunnelblick z folderu aplikacji.
9. Kliknij ikonę Tunnelblick w zasobniku systemowym i wybierz connect.

> [!IMPORTANT]
>Tylko iOS 11.0 i powyżej i MacOS 10.13 i powyżej są obsługiwane z protokołem OpenVPN.
>
## <a name="ios-clients"></a><a name="iOS"></a>Klienci systemu iOS

1. Zainstaluj klienta OpenVPN (wersja 2.4 lub nowsza) ze sklepu App Store.
2. Pobierz profil sieci VPN dla bramy. Można to zrobić na karcie konfiguracji typu punkt-lokacja w witrynie Azure portal lub przy użyciu "New-AzVpnClientConfiguration" w programie PowerShell.
3. Rozpakuj profil. Otwórz plik konfiguracyjny vpnconfig.ovpn z folderu OpenVPN w edytorze tekstu.
4. W sekcji certyfikatu klienta P2S wprowadź klucz publiczny certyfikatu klienta P2S w formacie base64. W przypadku certyfikatu w formacie PEM można po prostu otworzyć plik cer i skopiować klucz w formacie base64 znajdujący się pomiędzy nagłówkami certyfikatów. Skorzystaj z następujących łączy artykułów, aby uzyskać informacje na temat eksportowania certyfikatu w celu uzyskania zakodowanego klucza publicznego:

   * Instrukcje [dotyczące bramy sieci VPN](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#cer) 
   
   * Instrukcje [wirtualnej sieci WAN](../articles/virtual-wan/certificates-point-to-site.md#cer)
5. W sekcji klucza prywatnego wprowadź klucz prywatny certyfikatu klienta P2S w formacie base64. Aby uzyskać informacje na temat wyodrębniania klucza prywatnego, zobacz [Eksportowanie klucza prywatnego](https://openvpn.net/community-resources/how-to/#pki) w witrynie OpenVPN.
6. Nie zmieniaj innych pól.
7. Wyślij plik profilu (.ovpn) pocztą e-mail na swoje konto e-mail skonfigurowane w aplikacji poczty na telefonie iPhone. 
8. Otwórz wiadomość e-mail w aplikacji poczty na iPhonie i naciśnij załączony plik

    ![Otwórz wiadomość e-mail](./media/vpn-gateway-vwan-config-openvpn-clients/ios2.png)

9. Dotknij **przycisku Więcej,** jeśli nie widzisz opcji **Kopiuj do OpenVPN**

    ![Więcej](./media/vpn-gateway-vwan-config-openvpn-clients/ios3.png)

10. Dotknij **kopiuj do OpenVPN** 

    ![Kopiuj do OpenVPN](./media/vpn-gateway-vwan-config-openvpn-clients/ios4.png)

11. Naciśnij **pozycję ADD** na stronie **Importuj profil**

    ![Dodaj](./media/vpn-gateway-vwan-config-openvpn-clients/ios5.png)

12. Naciśnij **pozycję ADD** na stronie **Importowany profil**

    ![Naciśnij pozycję DODAJ](./media/vpn-gateway-vwan-config-openvpn-clients/ios6.png)

13. Uruchom aplikację OpenVPN i przesuń przełącznik na stronie **Profilu** w prawo, aby połączyć

    ![Połącz](./media/vpn-gateway-vwan-config-openvpn-clients/ios8.png)


## <a name="linux-clients"></a><a name="linux"></a>Klienci systemu Linux

1. Otwórz nową sesję terminala. Możesz otworzyć nową sesję, naciskając jednocześnie "Ctrl + Alt + t".
2. Wprowadź następujące polecenie, aby zainstalować potrzebne składniki:

   ```
   sudo apt-get install openvpn
   sudo apt-get -y install network-manager-openvpn
   sudo service network-manager restart
   ```
3. Pobierz profil sieci VPN dla bramy. Można to zrobić na karcie Konfiguracja point-to-site w witrynie Azure portal.
4. Wyeksportuj utworzony i przekazany certyfikat klienta P2S do konfiguracji P2S na bramie. Użyj następujących łączy artykułów:

   * Instrukcje [dotyczące bramy sieci VPN](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport) 
   
   * Instrukcje [wirtualnej sieci WAN](../articles/virtual-wan/certificates-point-to-site.md#clientexport)
5. Wyodrębnij klucz prywatny i odcisk palca base64 z .pfx. Istnieje wiele sposobów, aby to zrobić. Korzystanie z OpenSSL na komputerze jest jednym ze sposobów.

    ```
    openssl.exe pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
    ```
   Plik *profileinfo.txt* będzie zawierał klucz prywatny i odcisk palca urzędu certyfikacji i certyfikat klienta. Pamiętaj, aby użyć odcisku palca certyfikatu klienta.

6. Otwórz *plik profileinfo.txt* w edytorze tekstu. Aby uzyskać odcisk palca certyfikatu klienta (podrzędnego), zaznacz tekst, w tym między "-----BEGIN CERTIFICATE-----" i "-----END CERTIFICATE-----" dla certyfikatu podrzędnego i skopiuj go. Certyfikat podrzędny można zidentyfikować, patrząc na temat=/ wiersz.

7. Otwórz plik *vpnconfig.ovpn* i znajdź sekcję pokazaną poniżej. Wymień wszystko między i "cert" i "/cert".

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. Otwórz plik profileinfo.txt w edytorze tekstu. Aby uzyskać klucz prywatny, zaznacz tekst, w tym między "-----BEGIN KLUCZ PRYWATNY-----" i "-----END KLUCZ PRYWATNY-----" i skopiuj go.

9. Otwórz plik vpnconfig.ovpn w edytorze tekstu i znajdź tę sekcję. Wklej klucz prywatny zastępując wszystko między i "key" i "/key".

   ```
   # P2S client root certificate private key
   # please fill this field with a PEM formatted key
   <key>
   $PRIVATEKEY
   </key>
   ```

10. Nie zmieniaj innych pól. Użyj konfiguracji wprowadzonej w danych wejściowych klienta, aby nawiązać połączenie z siecią VPN.
11. Aby połączyć się za pomocą wiersza polecenia, wpisz następujące polecenie:
  
    ```
    sudo openvpn –-config <name and path of your VPN profile file>&
    ```
12. Aby połączyć się za pomocą graficznego interfejsu użytkownika, przejdź do ustawień systemowych.
13. Kliknij, **+** aby dodać nowe połączenie sieci VPN.
14. W obszarze **Dodaj VPN**wybierz **pozycję Importuj z pliku...**
15. Przejdź do pliku profilu i kliknij dwukrotnie lub wybierz opcję **Otwórz**.
16. Kliknij pozycję **Dodaj** w oknie **Dodaj sieć VPN.**
  
    ![Importuj z pliku](./media/vpn-gateway-vwan-config-openvpn-clients/import.png)
17. Połączenie można połączyć, włączając sieć VPN **na** stronie **Ustawienia sieciowe** lub pod ikoną sieci w zasobniku systemowym.