---
title: Jak skonfigurować klientów OpenVPN dla bramy sieci VPN platformy Azure | Dokumentacja firmy Microsoft
description: Kroki, aby skonfigurować klientów OpenVPN dla bramy sieci VPN platformy Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: 141f29ea1c7ebdd8208dda6bc39a60f7873dab7d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46977846"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway-preview"></a>Konfigurowanie klientów OpenVPN dla bramy sieci VPN platformy Azure (wersja zapoznawcza)

Ten artykuł pomoże Ci skonfigurować OpenVPN klientów.

> [!IMPORTANT]
> Publiczna wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie należy korzystać z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, mogą mieć ograniczone możliwości lub mogą nie być dostępne we wszystkich lokalizacjach platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Przed rozpoczęciem

Sprawdź, że zostały wykonane kroki konfigurowania OpenVPN dla bramy sieci VPN. Aby uzyskać więcej informacji, zobacz [skonfigurować OpenVPN dla bramy sieci VPN Azure](vpn-gateway-howto-openvpn.md).

## <a name="windows"></a>Klienci Windows

1. Pobieranie i instalowanie klienta OpenVPN z oficjalnego [OpenVPN witryny sieci Web](https://openvpn.net/index.php/open-source/downloads.html).
2. Pobierz profil sieci VPN dla bramy. Można to zrobić na karcie Konfiguracja punkt lokacja w witrynie Azure portal lub "New-AzureRmVpnClientConfiguration" w programie PowerShell.
3. Rozpakuj profilu. Następnie otwórz plik konfiguracyjny vpnconfig.ovpn z folderu OpenVPN w Notatniku.
4. Wypełnij sekcji P2S klienta certyfikatu z kluczem publicznym certyfikatu klienta o P2S za pomocą kodowania base64. W certyfikacie sformatowane PEM wystarczy otworzyć plik cer i skopiuj nad kluczem base64 między nagłówki certyfikatu. W tym miejscu należy poznać sposób eksportowania certyfikatu można pobrać zakodowanego klucza publicznego.
5. Wypełnij sekcję klucza prywatnego przy użyciu klucza prywatnego certyfikatu klienta o P2S za pomocą kodowania base64. Aby uzyskać informacje o tym, jak można wyodrębnić klucza prywatnego, zobacz [wyeksportować klucz](vpn-gateway-certificates-point-to-site.md#clientexport).
6. Nie zmieniaj wszelkie inne pola. Użyj wypełniony w konfiguracji w danych wejściowych klienta nawiązać połączenia z siecią VPN.
7. Skopiuj plik vpnconfig.ovpn do folderu C:\Program Files\OpenVPN\config.
8. Kliknij prawym przyciskiem myszy ikonę OpenVPN na pasku zadań i kliknij przycisk Połącz.

## <a name="mac"></a>Klienci na komputery Mac

1. Pobieranie i instalowanie klienta OpenVPN, takich jak [TunnelBlik](https://tunnelblick.net/downloads.html). 
2. Pobierz profil sieci VPN dla bramy. Można to zrobić na karcie Konfiguracja punktu do lokacji w witrynie Azure portal lub za pomocą "New-AzureRmVpnClientConfiguration" w programie PowerShell.
3. Rozpakuj profilu. Otwórz plik konfiguracji vpnconfig.ovpn z folderu OpenVPN w Notatniku.
4. Wypełnij sekcji P2S klienta certyfikatu z kluczem publicznym certyfikatu klienta o P2S za pomocą kodowania base64. W certyfikacie sformatowane PEM wystarczy otworzyć plik cer i skopiuj nad kluczem base64 między nagłówki certyfikatu. Zobacz [wyeksportować klucz publiczny](vpn-gateway-certificates-point-to-site.md#cer) informacji o sposobie eksportowania certyfikatu można pobrać zakodowanego klucza publicznego.
5. Wypełnij sekcję klucza prywatnego przy użyciu klucza prywatnego certyfikatu klienta o P2S za pomocą kodowania base64. Zobacz [Eksportuj klucz prywatny](https://www.geotrust.eu/en/support/manuals/microsoft/all+windows+servers/export+private+key+or+certificate/) informacji o tym, jak można wyodrębnić klucza prywatnego.
6. Nie zmieniaj wszelkie inne pola. Użyj wypełniony w konfiguracji w danych wejściowych klienta nawiązać połączenia z siecią VPN.
7. Kliknij dwukrotnie plik profilu, aby utworzyć profil w tunnelblik.
8. Uruchom Tunnelblik z folderu aplikacji.
9. Kliknij ikonę Tunneblik na pasku zadań i połącz wyboru.

## <a name="linux"></a>Klienci systemu Linux

1. Otwórz nową sesję terminala. Można otworzyć nową sesję, naciskając klawisz "Ctrl + Alt + t", w tym samym czasie
2. Wprowadź następujące polecenie, aby zainstalować wymagane składniki:

  ```
  sudo apt-get install openvpn
  sudo apt-get -y install network-manager-openvpn
  sudo service network-manager restart
  ```
3. Pobierz profil sieci VPN dla bramy. Można to zrobić, na karcie Konfiguracja punktu do lokacji w witrynie Azure portal lub za pomocą "New-AzureRmVpnClientConfiguration" w programie PowerShell.
4. Wypełnij sekcję klucza prywatnego przy użyciu klucza prywatnego certyfikatu klienta o P2S za pomocą kodowania base64. Zobacz [Eksportuj klucz prywatny](https://www.geotrust.eu/en/support/manuals/microsoft/all+windows+servers/export+private+key+or+certificate/) informacji o tym, jak można wyodrębnić klucza prywatnego.
5. Nawiązywanie połączenia przy użyciu wiersza polecenia, wpisz następujące polecenie:
  
  ```
  Sudo openvpn –config <name and path of your VPN profile file>
  ```
5. Nawiązywanie połączenia przy użyciu graficznego interfejsu użytkownika, przejdź do ustawień systemu.
6. Kliknij przycisk **+** można dodać nowe połączenie VPN.
7. W obszarze **dodać sieci VPN**, wybierz **Importuj z pliku...**
8. Przejdź do pliku profilu i kliknij dwukrotnie plik lub wybierz **Otwórz**
9. Kliknij przycisk **Dodaj** na **dodać sieci VPN** okna.
  
  ![Importuj z pliku](./media/vpn-gateway-howto-openvpn-clients/importfromfile.png)
10. Można połączyć, wyłączając sieć VPN **ON** na **ustawienia sieciowe** strony, lub równy podanemu ikonę sieci na pasku zadań.

## <a name="next-steps"></a>Kolejne kroki

Jeśli chcesz, aby klienci sieci VPN, aby można było uzyskać dostęp do zasobów w innej sieci wirtualnej (produkcja), następnie postępuj zgodnie z instrukcjami [sieć wirtualna-sieć wirtualna](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) artykuł, aby skonfigurować połączenie sieć wirtualna-sieć wirtualna. Pamiętaj włączyć protokół BGP dla bramy i połączenia, w przeciwnym razie ruch nie będzie przepływać.
