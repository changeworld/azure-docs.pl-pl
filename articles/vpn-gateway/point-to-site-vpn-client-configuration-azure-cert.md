---
title: 'Tworzenie i instalowanie plików konfiguracji klienta sieci VPN P2S do uwierzytelniania certyfikatu platformy Azure: Azure'
description: Tworzenie i instalowanie Windows, Linux, systemu Linux (strongSwan) i Mac OS X w sieci VPN z plików konfiguracji klienta do uwierzytelniania certyfikatów P2S.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 03/20/2019
ms.author: cherylmc
ms.openlocfilehash: b590dabbe4b2c6526f2c602aeed64667348eefa9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66113934"
---
# <a name="create-and-install-vpn-client-configuration-files-for-native-azure-certificate-authentication-p2s-configurations"></a>Tworzenie i instalowanie plików konfiguracji klienta sieci VPN dla konfiguracji uwierzytelniania P2S natywnych certyfikatu platformy Azure

Pliki konfiguracji klienta sieci VPN są zawarte w pliku zip. Pliki konfiguracji zawierają ustawienia wymagane dla natywnych klientów Windows, sieci VPN IKEv2 Mac lub Linux można łączyć z siecią wirtualną za pośrednictwem połączenia punkt-lokacja, które używają uwierzytelniania certyfikatu platformy Azure natywnych.

Pliki konfiguracji klienta są specyficzne dla konfiguracji sieci VPN dla sieci wirtualnej. W przypadku zmiany wprowadzone w konfiguracji sieci VPN typu punkt-lokacja po wygenerowaniu plików konfiguracji klienta sieci VPN, takie jak typ protokołu sieci VPN lub typ uwierzytelniania, pamiętaj wygenerować nowe pliki konfiguracji klienta sieci VPN dla urządzeń użytkownika. 

* Aby uzyskać więcej informacji na temat połączeń punkt-lokacja, zobacz [About Point-to-Site VPN](point-to-site-about.md) (Informacje o sieci VPN typu punkt-lokacja).
* OpenVPN instrukcje można znaleźć [skonfigurować OpenVPN dla P2S](vpn-gateway-howto-openvpn.md) i [klientom Konfigurowanie OpenVPN](vpn-gateway-howto-openvpn-clients.md).

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="generate"></a>Generowanie plików konfiguracji klienta sieci VPN

Przed rozpoczęciem upewnij się, że wszyscy użytkownicy nawiązującego połączenie jest prawidłowy certyfikat zainstalowany na urządzeniu użytkownika. Aby uzyskać więcej informacji na temat instalowania certyfikatu klienta, zobacz [Instalowanie certyfikatu klienta](point-to-site-how-to-vpn-client-install-azure-cert.md).

Można wygenerować plików konfiguracji klienta przy użyciu programu PowerShell lub za pomocą witryny Azure portal. Każda z tych metod zwraca ten sam plik zip. Rozpakuj plik, aby wyświetlić następujące foldery:

  * **WindowsAmd64** i **WindowsX86**, pakietów Instalatora Windows 32-bitowych i 64-bitowych, które zawierają odpowiednio. **WindowsAmd64** pakiet Instalatora jest dla wszystkich obsługiwanych klientach Windows 64-bitowych, Amd nie tylko.
  * **Ogólny**, który zawiera informacje ogólne, służący do tworzenia własnych konfiguracji klienta sieci VPN. Ile folderze ogólnym jest, że protokół IKEv2 lub SSTP + IKEv2 została skonfigurowana w bramie. Jeśli protokół SSTP jest skonfigurowany, tylko folderze ogólnym nie jest obecny.

### <a name="zipportal"></a>Generowanie plików za pomocą witryny Azure portal

1. W witrynie Azure portal przejdź do bramy sieci wirtualnej dla sieci wirtualnej, którą chcesz nawiązać połączenie.
2. Na stronie bramy sieci wirtualnej kliknij **Point-to-site configuration**.
3. W górnej części strony Konfiguracja punktu do lokacji, kliknij przycisk **klienta VPN Pobierz**. Trwa kilka minut dla klienta pakietu konfiguracyjnego w celu wygenerowania.
4. Przeglądarka wskazuje można pliku zip konfiguracji klienta. Nosi on taką samą nazwę jak bramy. Rozpakuj plik, aby wyświetlić foldery.

### <a name="zipps"></a>Generowanie plików za pomocą programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Podczas generowania konfiguracji klienta sieci VPN plików, wartość "-AuthenticationMethod" to "EAP TLS". Generowanie plików konfiguracji klienta sieci VPN przy użyciu następującego polecenia:

   ```azurepowershell-interactive
   $profile=New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

   $profile.VPNProfileSASUrl
   ```
2. Skopiuj adres URL do przeglądarki, aby pobrać plik zip, a następnie Rozpakuj plik, aby wyświetlić foldery.

## <a name="installwin"></a>Windows

Na każdym komputerze klienckim Windows można użyć tego samego pakietu konfiguracji klienta sieci VPN, tak długo, jak wersja jest zgodna z architekturą dla klienta. Aby uzyskać listę obsługiwanych systemów operacyjnych klienta, zobacz sekcję Point-to-Site [— często zadawane pytania dla bramy sieci VPN](vpn-gateway-vpn-faq.md#P2S).

>[!NOTE]
>Musi mieć prawa administratora na komputerze klienckim Windows, w którym chcesz się połączyć.
>
>

Wykonaj następujące kroki, aby skonfigurować natywnego klienta sieci VPN Windows w celu uwierzytelnienia certyfikatu:

1. Wybierz pliki konfiguracji klienta sieci VPN, które odpowiadają architekturze komputera z systemem Windows. W przypadku 64-bitowej architektury procesora wybierz pakiet instalatora „VpnClientSetupAmd64”. W przypadku 32-bitowej architektury procesora wybierz pakiet instalatora „VpnClientSetupX86”. 
2. Kliknij dwukrotnie pakiet, aby go zainstalować. Jeśli zostanie wyświetlone okno podręczne SmartScreen, kliknij pozycję **Więcej informacji**, a następnie pozycję **Uruchom mimo to**.
3. Na komputerze klienckim przejdź do obszaru **Ustawienia sieci** i kliknij pozycję **Sieć VPN**. Połączenie z siecią VPN zawiera nazwę sieci wirtualnej, z którą jest nawiązywane połączenie. 
4. Zanim spróbujesz nawiązać połączenie, sprawdź, czy zainstalowano certyfikat klienta na komputerze klienckim. Certyfikat klienta jest wymagany w celu uwierzytelniania podczas korzystania z natywnego typu certyfikatu uwierzytelniania platformy Azure. Aby uzyskać więcej informacji na temat generowania certyfikatów, zobacz [generowanie certyfikatów](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Aby uzyskać informacje o sposobie instalowania certyfikatu klienta, zobacz [Instalowanie certyfikatu klienta](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="installmac"></a>Mac (OS X)

 Należy ręcznie skonfigurować natywnego klienta sieci VPN z protokołem IKEv2 na każdym komputerze Mac, który jest połączony z platformą Azure. Platforma Azure nie zapewnia pliku mobileconfig dla uwierzytelniania natywnego certyfikatu platformy Azure. **Ogólny** zawiera wszystkie informacje potrzebne do konfiguracji. Jeśli nie widzisz folderu Generic w elementach pobranych, prawdopodobnie nie wybrano protokołu IKEv2 jako typu tunelu. Należy pamiętać, że podstawowa jednostka SKU bramy sieci VPN nie obsługuje protokół IKEv2. Po wybraniu protokołu IKEv2 należy ponownie wygenerować plik zip, aby pobrać folder Generic.<br>Folder Generic zawiera następujące pliki:

* **VpnSettings.xml**, zawierającą ważnych ustawień, takich jak typ serwera adres i tunelowania. 
* **VpnServerRoot.cer**, który zawiera certyfikat główny wymagany do sprawdzania poprawności bramy sieci VPN Azure podczas konfigurowania połączenia P2S.

Poniższe kroki umożliwiają konfigurowanie natywnego klienta sieci VPN na komputerze Mac na potrzeby uwierzytelniania certyfikatu. Musisz wykonać następujące kroki na komputerze Mac, co którą będą nawiązywać połączenie platformy Azure:

1. Importuj **VpnServerRoot** certyfikatu głównego na komputerze Mac. Można to zrobić przez skopiowanie pliku za pośrednictwem z Twoim komputerem Mac i go dwukrotnie. Kliknij przycisk **Dodaj** do zaimportowania.

   ![Dodaj certyfikat](./media/point-to-site-vpn-client-configuration-azure-cert/addcert.png)
  
    >[!NOTE]
    >Dwukrotne kliknięcie certyfikatu mogą nie wyświetlać się **Dodaj** okna dialogowego, ale certyfikat jest zainstalowany w poprawnym magazynie. Możesz sprawdzić, czy certyfikat w łańcuchu kluczy logowania w obszarze Certyfikaty.
    >
  
2. Sprawdź, czy zainstalowano certyfikat klienta, który został wystawiony przez certyfikat główny, który został przekazany na platformę Azure możesz skonfigurowano ustawienia P2S. To różni się od VPNServerRoot, który został zainstalowany w poprzednim kroku. Certyfikat klienta jest używany do uwierzytelniania i jest wymagana. Aby uzyskać więcej informacji na temat generowania certyfikatów, zobacz [generowanie certyfikatów](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Aby uzyskać informacje o sposobie instalowania certyfikatu klienta, zobacz [Instalowanie certyfikatu klienta](point-to-site-how-to-vpn-client-install-azure-cert.md).
3. Otwórz **sieci** , okno dialogowe **preferencje sieci** i kliknij przycisk **'+'** Aby utworzyć nowy profil połączenia klienta sieci VPN typu P2S połączenie z siecią wirtualną platformy Azure.

   **Interfejsu** wartość to "VPN" i **typu sieci VPN** wartość to "IKEv2". Określ nazwę profilu w **nazwa usługi** pola, a następnie kliknij przycisk **Utwórz** tworzenia profilu połączenia sieci VPN klienta.

   ![sieć](./media/point-to-site-vpn-client-configuration-azure-cert/network.png)
4. W **ogólny** folderu z **VpnSettings.xml** plików, skopiuj **VpnServer** wartość tagu. Wklej tę wartość w **adres serwera** i **identyfikator zdalnego** pola profilu.

   ![informacje o serwerze](./media/point-to-site-vpn-client-configuration-azure-cert/server.png)
5. Kliknij przycisk **ustawienia uwierzytelniania** i wybierz **certyfikatu**. 

   ![ustawienia uwierzytelniania](./media/point-to-site-vpn-client-configuration-azure-cert/authsettings.png)
6. Kliknij przycisk **wybierz...** Aby wybrać certyfikat klienta, który ma być używany do uwierzytelniania. Jest to certyfikat, który został zainstalowany w kroku 2.

   ![Certyfikat](./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png)
7. **Wybierz tożsamość** jest wyświetlana lista certyfikatów, można wybrać. Wybierz odpowiedni certyfikat, a następnie kliknij przycisk **Kontynuuj**.

   ![identity](./media/point-to-site-vpn-client-configuration-azure-cert/identity.png)
8. W **lokalnego Identyfikatora** Określ nazwę certyfikatu (z kroku 6). W tym przykładzie jest "ikev2Client.com". Następnie kliknij przycisk **Zastosuj** przycisk, aby zapisać zmiany.

   ![zastosuj](./media/point-to-site-vpn-client-configuration-azure-cert/applyconnect.png)
9. Na **sieci** okno dialogowe, kliknij przycisk **Zastosuj** Aby zapisać wszystkie zmiany. Następnie kliknij przycisk **Connect** nawiązaniu połączenia P2S do sieci wirtualnej platformy Azure.

## <a name="linuxgui"></a>Linux (strongSwan graficznego interfejsu użytkownika)

### <a name="extract-the-key-and-certificate"></a>Wyodrębnij kluczami i certyfikatami

StrongSwan należy wyodrębnić klucz i certyfikatów z certyfikatu klienta (plik pfx) i zapisują je do plików PEM indywidualnych.
Wykonaj poniższe kroki:

1. Pobrać i zainstalować protokół OpenSSL z [OpenSSL](https://www.openssl.org/source/).
2. Otwórz okno wiersza polecenia i przejdź do katalogu, w którym zainstalowano biblioteki OpenSSL, na przykład "c:\OpenSLL-Win64\bin\'.
3. Uruchom następujące polecenie, aby wyodrębnić klucz prywatny i zapisz go jako nowy plik o nazwie "privatekey.pem" z Twój certyfikat klienta:

   ```
   C:\ OpenSLL-Win64\bin> openssl pkcs12 -in clientcert.pfx -nocerts -out privatekey.pem -nodes
   ```
4. Teraz uruchom następujące polecenie, aby wyodrębnić certyfikatu publicznego i zapisz go do nowego pliku:

   ```
   C:\ OpenSLL-Win64\bin> openssl pkcs12 -in clientcert.pfx -nokeys -out publiccert.pem -nodes
   ```

### <a name="install"></a>Instalowanie i konfigurowanie

Poniższe instrukcje zostały utworzone za pomocą strongSwan 5.5.1 w systemie Ubuntu 17.0.4. Ubuntu 16.0.10 nie obsługuje strongSwan graficznego interfejsu użytkownika. Jeśli chcesz użyć Ubuntu 16.0.10, będą musieli używać [wiersza polecenia](#linuxinstallcli). Poniższe przykłady mogą być niezgodne ekrany, które zostanie wyświetlone, w zależności od używanej wersji systemu Linux i strongSwan.

1. Otwórz **terminalu** zainstalował **strongSwan** i jego Menedżera sieci, uruchamiając polecenie w przykładzie. Jeśli otrzymasz komunikat o błędzie, który jest powiązany z *libcharon-extra wtyczek*, Zamień "strongswan wtyczka eap-mschapv2".

   ```
   sudo apt-get install strongswan libcharon-extra-plugins moreutils iptables-persistent network-manager-strongswan
   ```
2. Wybierz **Menedżera sieci** ikona (Strzałka/w dół — strzałek w górę), następnie wybierz pozycję **edycji połączenia**.

   ![Edytowanie połączenia](./media/point-to-site-vpn-client-configuration-azure-cert/editconnections.png)
3. Kliknij przycisk **Dodaj** przycisk, aby utworzyć nowe połączenie.

   ![Dodawanie połączenia](./media/point-to-site-vpn-client-configuration-azure-cert/addconnection.png)
4. Wybierz **protokołu IPsec/IKEv2 (strongswan)** z menu rozwijanego, a następnie kliknij **Utwórz**. Można zmienić nazwę połączenia w taki sposób, w tym kroku.

   ![Wybierz typ połączenia](./media/point-to-site-vpn-client-configuration-azure-cert/choosetype.png)
5. Otwórz **VpnSettings.xml** plik wchodzącej w skład **ogólny** zawarty w plikach konfiguracji klienta pobrane. Znajdź tag o nazwie **VpnServer** i skopiuj nazwę, zaczynając od "azuregateway" i kończącego się ". cloudapp.net".

   ![Kopiuj nazwę](./media/point-to-site-vpn-client-configuration-azure-cert/vpnserver.png)
6. Wklej tę nazwę do **adres** pole nowego połączenia sieci VPN **bramy** sekcji. Następnie wybierz ikonę folderu, na końcu **certyfikatu** pola, przejdź do **ogólny** folder, a następnie wybierz **VpnServerRoot** pliku.
7. W **klienta** części połączenia, dla **uwierzytelniania**, wybierz opcję **klucz prywatnycertyfikatu/**. Aby uzyskać **certyfikatu** i **klucza prywatnego**, wybierz certyfikat i klucz prywatny, które zostały utworzone wcześniej. W **opcje**, wybierz opcję **wewnętrzny adres IP żądania**. Następnie kliknij przycisk **Dodaj**.

   ![żądanie wewnętrznego adresu IP](./media/point-to-site-vpn-client-configuration-azure-cert/inneripreq.png)
8. Kliknij przycisk **Menedżera sieci** ikona (Strzałka/w dół — strzałek w górę) i umieść kursor nad **połączeń sieci VPN**. Widać wcześniej utworzone połączenie sieci VPN. Kliknij, aby zainicjować połączenie.

## <a name="linuxinstallcli"></a>Linux (strongSwan interfejsu wiersza polecenia)

### <a name="install-strongswan"></a>Zainstaluj strongSwan

Użyj następujących poleceń interfejsu wiersza polecenia lub użyj kroków strongSwan w [graficznego interfejsu użytkownika](#install) zainstalował strongSwan.

1. `apt-get install strongswan-ikev2 strongswan-plugin-eap-tls`
2. `apt-get install libstrongswan-standard-plugins`

### <a name="install-and-configure"></a>Instalacja i konfiguracja

1. Pobierz pakiet klienta VPN z witryny Azure portal.
2. Wyodrębnij plik.
3. Z **ogólny** folder, skopiować lub przenieść VpnServerRoot.cer /etc/ipsec.d/cacerts.
4. Skopiuj lub Przenieś cp client.p12 do /etc/ipsec.d/private/. Ten plik jest certyfikat klienta dla usługi Azure VPN Gateway.
5. Otwórz plik VpnSettings.xml i kopiowania `<VpnServer>` wartość. Ta wartość zostanie użyta w następnym kroku.
6. Dostosuj wartości w poniższym przykładzie, a następnie dodaj przykładu tak, aby konfiguracja /etc/ipsec.conf.
  
   ```
   conn azure
   keyexchange=ikev2
   type=tunnel
   leftfirewall=yes
   left=%any
   leftauth=eap-tls
   leftid=%client # use the DNS alternative name prefixed with the %
   right= Enter the VPN Server value here# Azure VPN gateway address
   rightid=% # Enter the VPN Server value here# Azure VPN gateway FQDN with %
   rightsubnet=0.0.0.0/0
   leftsourceip=%config
   auto=add
   ```
6. Dodaj następujące polecenie, aby */etc/ipsec.secrets*.

   ```
   : P12 client.p12 'password' # key filename inside /etc/ipsec.d/private directory
   ```

7. Uruchom następujące polecenia:

   ```
   # ipsec restart
   # ipsec up azure
   ```

## <a name="next-steps"></a>Kolejne kroki

Wróć do artykułu, aby [ukończyć konfigurację P2S](vpn-gateway-howto-point-to-site-rm-ps.md).

Aby rozwiązać problemy połączeń P2S, zobacz następujące artykuły:

  * [Rozwiązywanie problemów z połączeniami usługi Azure point-to-site](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
  * [Rozwiązywanie problemów z połączeniami sieci VPN od klientów Mac OS X w sieci VPN](vpn-gateway-troubleshoot-point-to-site-osx-ikev2.md)
