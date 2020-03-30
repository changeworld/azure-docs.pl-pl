---
title: 'Tworzenie & instalowanie plików konfiguracyjnych klienta sieci VPN P2S: uwierzytelnianie certyfikatów'
titleSuffix: Azure VPN Gateway
description: Tworzenie i instalowanie plików konfiguracyjnych klienta sieci VPN systemu Windows, Linux, Linux (strongSwan) i Mac OS X do uwierzytelniania certyfikatów P2S.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 03/04/2020
ms.author: cherylmc
ms.openlocfilehash: d15efee635e131d658cd650b7f80eb9e670a0dea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279418"
---
# <a name="create-and-install-vpn-client-configuration-files-for-native-azure-certificate-authentication-p2s-configurations"></a>Tworzenie i instalowanie plików konfiguracyjnych klienta sieci VPN dla natywnych konfiguracji P2S uwierzytelniania certyfikatów platformy Azure

Pliki konfiguracyjne klienta sieci VPN znajdują się w pliku zip. Pliki konfiguracyjne zawierają ustawienia wymagane dla natywnych klientów systemu Windows, Mac IKEv2 VPN lub Linux do łączenia się z siecią wirtualną za pośrednictwem połączeń typu punkt-lokacja, które używają natywnego uwierzytelniania certyfikatów platformy Azure.

Pliki konfiguracyjne klienta są specyficzne dla konfiguracji sieci VPN dla sieci wirtualnej. Jeśli po wygenerowaniu plików konfiguracyjnych klienta sieci VPN, takich jak typ protokołu sieci VPN lub typ uwierzytelniania, nastąpi wprowadzenie jakichkolwiek zmian w konfiguracji sieci VPN, należy wygenerować nowe pliki konfiguracyjne klienta sieci VPN dla urządzeń użytkownika. 

* Aby uzyskać więcej informacji na temat połączeń punkt-lokacja, zobacz [About Point-to-Site VPN](point-to-site-about.md) (Informacje o sieci VPN typu punkt-lokacja).
* Aby uzyskać instrukcje OpenVPN, zobacz [Konfigurowanie OpenVPN dla P2S](vpn-gateway-howto-openvpn.md) i [Konfigurowanie klientów OpenVPN](vpn-gateway-howto-openvpn-clients.md).

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="generate-vpn-client-configuration-files"></a><a name="generate"></a>Generowanie plików konfiguracyjnych klienta sieci VPN

Przed rozpoczęciem upewnij się, że wszyscy użytkownicy łączący mają zainstalowany prawidłowy certyfikat na urządzeniu użytkownika. Aby uzyskać więcej informacji na temat instalowania certyfikatu klienta, zobacz [Instalowanie certyfikatu klienta](point-to-site-how-to-vpn-client-install-azure-cert.md).

Można wygenerować pliki konfiguracji klienta przy użyciu programu PowerShell lub przy użyciu witryny Azure Portal. Każda z tych metod zwraca ten sam plik zip. Rozpaj plik, aby wyświetlić następujące foldery:

  * **WindowsAmd64** i **WindowsX86**, które zawierają windows 32-bitowe i 64-bitowe pakiety instalacyjne, odpowiednio. Pakiet instalatora **WindowsAmd64** jest przeznaczony dla wszystkich obsługiwanych 64-bitowych klientów systemu Windows, nie tylko amd.
  * **Ogólny**, który zawiera ogólne informacje używane do tworzenia własnej konfiguracji klienta sieci VPN. Folder ogólny jest dostarczany, jeśli protokół IKEv2 lub SSTP+IKEv2 został skonfigurowany na bramie. Jeśli skonfigurowano tylko protokół SSTP, folder Ogólny nie jest obecny.

### <a name="generate-files-using-the-azure-portal"></a><a name="zipportal"></a>Generowanie plików za pomocą portalu Azure

1. W witrynie Azure portal przejdź do bramy sieci wirtualnej dla sieci wirtualnej, z którą chcesz się połączyć.
2. Na stronie bramy sieci wirtualnej kliknij pozycję **Konfiguracja lokacji**.

   ![pobieranie portalu klienta](./media/point-to-site-vpn-client-configuration-azure-cert/client-configuration-portal.png)
3. U góry strony konfiguracji wskaż lokację kliknij pozycję **Pobierz klienta sieci VPN**. Trwa kilka minut dla pakietu konfiguracji klienta do wygenerowania.
4. Przeglądarka wskazuje, że plik zip konfiguracji klienta jest dostępny. Nosi nazwę tej samej nazwy co brama. Rozpaj plik, aby wyświetlić foldery.

### <a name="generate-files-using-powershell"></a><a name="zipps"></a>Generowanie plików przy użyciu programu PowerShell


1. Podczas generowania plików konfiguracyjnych klienta sieci VPN wartość '-AuthenticationMethod' to 'EapTls'. Wygeneruj pliki konfiguracyjne klienta sieci VPN za pomocą następującego polecenia:

   ```azurepowershell-interactive
   $profile=New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

   $profile.VPNProfileSASUrl
   ```
2. Skopiuj adres URL do przeglądarki, aby pobrać plik zip, a następnie rozpakuj plik, aby wyświetlić foldery.

## <a name="windows"></a><a name="installwin"></a>Windows

Tego samego pakietu konfiguracji klienta sieci VPN można używać na każdym komputerze klienckim systemu Windows, o ile wersja jest zgodna z architekturą klienta. Aby uzyskać listę obsługiwanych systemów operacyjnych klienckich, zobacz sekcję Punkt lokacja [bramy sieci VPN — często zadawane pytania](vpn-gateway-vpn-faq.md#P2S).

>[!NOTE]
>Musisz mieć prawa administratora na komputerze klienckim systemu Windows, z którego chcesz się połączyć.
>
>

Aby skonfigurować macierzystego klienta sieci VPN systemu Windows, należy wykonać następujące czynności:

1. Wybierz pliki konfiguracji klienta sieci VPN, które odpowiadają architekturze komputera z systemem Windows. W przypadku 64-bitowej architektury procesora wybierz pakiet instalatora „VpnClientSetupAmd64”. W przypadku 32-bitowej architektury procesora wybierz pakiet instalatora „VpnClientSetupX86”. 
2. Kliknij dwukrotnie pakiet, aby go zainstalować. Jeśli zostanie wyświetlene okno podręczne SmartScreen, kliknij pozycję **Więcej informacji**, a następnie uruchom **mimo to**.
3. Na komputerze klienckim przejdź do obszaru **Ustawienia sieci** i kliknij pozycję **Sieć VPN**. Połączenie z siecią VPN zawiera nazwę sieci wirtualnej, z którą jest nawiązywane połączenie. 
4. Zanim spróbujesz nawiązać połączenie, sprawdź, czy zainstalowano certyfikat klienta na komputerze klienckim. Certyfikat klienta jest wymagany w celu uwierzytelniania podczas korzystania z natywnego typu certyfikatu uwierzytelniania platformy Azure. Aby uzyskać więcej informacji na temat generowania certyfikatów, zobacz [Generowanie certyfikatów](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Aby uzyskać informacje dotyczące instalowania certyfikatu klienta, zobacz [Instalowanie certyfikatu klienta](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="mac-os-x"></a><a name="installmac"></a>Mac (OS X)

 Należy ręcznie skonfigurować natywnego klienta sieci VPN z protokołem IKEv2 na każdym komputerze Mac, który jest połączony z platformą Azure. Platforma Azure nie zapewnia pliku mobileconfig dla uwierzytelniania natywnego certyfikatu platformy Azure. **Ogólny** zawiera wszystkie informacje potrzebne do konfiguracji. Jeśli nie widzisz folderu Generic w elementach pobranych, prawdopodobnie nie wybrano protokołu IKEv2 jako typu tunelu. Należy zauważyć, że podstawowa jednostka SKU bramy sieci VPN nie obsługuje protokołu IKEv2. Po wybraniu protokołu IKEv2 należy ponownie wygenerować plik zip, aby pobrać folder Generic.<br>Folder Generic zawiera następujące pliki:

* **VpnSettings.xml**, który zawiera ważne ustawienia, takie jak adres serwera i typ tunelu. 
* **VpnServerRoot.cer**, który zawiera certyfikat główny wymagany do sprawdzania poprawności bramy sieci VPN platformy Azure podczas konfigurowania połączenia P2S.

Aby skonfigurować natywnego klienta sieci VPN na komputerze Mac, należy wykonać następujące kroki do konfigurowania macierzystego klienta sieci VPN do uwierzytelniania certyfikatów. Musisz wykonać te kroki na każdym komputerze Mac, który połączy się z platformą Azure:

1. Zaimportuj certyfikat główny **VpnServerRoot** na komputerze Mac. Można to zrobić, kopiując plik na komputerze Mac i klikając go dwukrotnie. Kliknij przycisk **Dodaj** do zaimportowania.

   ![dodawanie certyfikatu](./media/point-to-site-vpn-client-configuration-azure-cert/addcert.png)
  
    >[!NOTE]
    >Dwukrotne kliknięcie certyfikatu może nie wyświetlać okna dialogowego **Dodaj,** ale certyfikat jest zainstalowany w odpowiednim magazynie. Certyfikat można sprawdzić w pęku kluczy logowania w kategorii certyfikatów.
    >
  
2. Sprawdź, czy zainstalowano certyfikat klienta wystawiony przez certyfikat główny przekazany na platformę Azure podczas konfigurowania ustawień P2S. Różni się to od vpnserverroot, który został zainstalowany w poprzednim kroku. Certyfikat klienta jest używany do uwierzytelniania i jest wymagany. Aby uzyskać więcej informacji na temat generowania certyfikatów, zobacz [Generowanie certyfikatów](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Aby uzyskać informacje dotyczące instalowania certyfikatu klienta, zobacz [Instalowanie certyfikatu klienta](point-to-site-how-to-vpn-client-install-azure-cert.md).
3. Otwórz okno dialogowe **Sieć** w obszarze **Preferencje sieciowe** i kliknij przycisk **"+",** aby utworzyć nowy profil połączenia klienta sieci VPN dla połączenia P2S z siecią wirtualną platformy Azure.

   Wartość **interfejsu** to "VPN", a wartość **typu sieci VPN** to "IKEv2". Określ nazwę profilu w polu **Nazwa usługi,** a następnie kliknij przycisk **Utwórz,** aby utworzyć profil połączenia klienta sieci VPN.

   ![network](./media/point-to-site-vpn-client-configuration-azure-cert/network.png)
4. W **folderze Ogólny** z pliku **VpnSettings.xml** skopiuj wartość tagu **VpnServer.** Wklej tę wartość w polach **Adres serwera** i **Identyfikator zdalny** profilu.

   ![informacje o serwerze](./media/point-to-site-vpn-client-configuration-azure-cert/server.png)
5. Kliknij **pozycję Ustawienia uwierzytelniania** i wybierz pozycję **Certyfikat**.W przypadku **catalina**kliknij pozycję **Brak,** a następnie **certyfikat**

   ![ustawienia uwierzytelniania](./media/point-to-site-vpn-client-configuration-azure-cert/authsettings.png)

   * W przypadku cataliny wybierz **pozycję Brak,** a następnie **pozycję Certyfikat**. **Wybierz** właściwy certyfikat:
   
   ![Catalina](./media/point-to-site-vpn-client-configuration-azure-cert/catalina.png)

6. Kliknij **pozycję Wybierz...** , aby wybrać certyfikat klienta, którego chcesz użyć do uwierzytelniania. Jest to certyfikat zainstalowany w kroku 2.

   ![certyfikat](./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png)
7. **Wybierz tożsamość** wyświetla listę certyfikatów do wyboru. Wybierz odpowiedni certyfikat, a następnie kliknij przycisk **Kontynuuj**.

   ![identity](./media/point-to-site-vpn-client-configuration-azure-cert/identity.png)
8. W polu **Identyfikator lokalny** określ nazwę certyfikatu (z kroku 6). W tym przykładzie jest "ikev2Client.com". Następnie kliknij przycisk **Zastosuj,** aby zapisać zmiany.

   ![apply](./media/point-to-site-vpn-client-configuration-azure-cert/applyconnect.png)
9. W oknie dialogowym **Sieć** kliknij przycisk **Zastosuj,** aby zapisać wszystkie zmiany. Następnie kliknij przycisk **Połącz,** aby uruchomić połączenie P2S z siecią wirtualną platformy Azure.

## <a name="linux-strongswan-gui"></a><a name="linuxgui"></a>Linux (strongSwan GUI)

### <a name="install-strongswan"></a><a name="installstrongswan"></a>Zainstaluj strongSwan

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a><a name="genlinuxcerts"></a>Generowanie certyfikatów klienta

Jeśli certyfikaty nie zostały jeszcze wygenerowane, należy wykonać następujące czynności:

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a><a name="install"></a>Instalowanie i konfigurowanie

Poniższe instrukcje zostały utworzone na Ubuntu 18.0.4. Ubuntu 16.0.10 nie obsługuje silnegoWuchy GUI. Jeśli chcesz użyć Ubuntu 16.0.10, będziesz musiał użyć [wiersza polecenia](#linuxinstallcli). Poniższe przykłady mogą nie pasować do ekranów, które widzisz, w zależności od wersji linuksa i strongSwan.

1. Otwórz **terminal,** aby zainstalować **strongSwan** i jego Menedżera sieci, uruchamiając polecenie w przykładzie.

   ```
   sudo apt install network-manager-strongswan
   ```
2. Wybierz **pozycję Ustawienia**, a następnie wybierz pozycję **Sieć**.

   ![edytowanie połączeń](./media/point-to-site-vpn-client-configuration-azure-cert/editconnections.png)
3. Kliknij **+** przycisk, aby utworzyć nowe połączenie.

   ![dodawanie połączenia](./media/point-to-site-vpn-client-configuration-azure-cert/addconnection.png)
4. Wybierz **IPsec/IKEv2 (strongSwan)** z menu i kliknij dwukrotnie. Połączenie można nadać nazwie w tym kroku.

   ![wybieranie typu połączenia](./media/point-to-site-vpn-client-configuration-azure-cert/choosetype.png)
5. Otwórz plik **VpnSettings.xml** z folderu **Generic** znajdującego się w pobranych plikach konfiguracyjnych klienta. Znajdź tag o nazwie **VpnServer** i skopiuj nazwę, zaczynając od "azuregateway", a kończąc na ".cloudapp.net".

   ![nazwa kopiowania](./media/point-to-site-vpn-client-configuration-azure-cert/vpnserver.png)
6. Wklej tę nazwę do pola **Adres** nowego połączenia sieci VPN w sekcji **Brama.** Następnie wybierz ikonę folderu na końcu pola **Certyfikat,** przejdź do folderu **Ogólne** i wybierz plik **VpnServerRoot.**
7. W sekcji **Klient** połączenia wybierz pozycję **Certyfikat/klucz prywatny**. **Authentication** W przypadku **certyfikatu** i **klucza prywatnego**wybierz certyfikat i klucz prywatny, które zostały utworzone wcześniej. W **obszarze Opcje**wybierz pozycję Poproś o wewnętrzny adres **IP**. Następnie kliknij przycisk **Add** (Dodaj).

   ![żądanie wewnętrznego adresu IP](./media/point-to-site-vpn-client-configuration-azure-cert/turnon.png)
8. Włącz **połączenie**.

## <a name="linux-strongswan-cli"></a><a name="linuxinstallcli"></a>Linux (strongSwan CLI)

### <a name="install-strongswan"></a>Zainstaluj strongSwan

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a>Generowanie certyfikatów klienta

Jeśli certyfikaty nie zostały jeszcze wygenerowane, należy wykonać następujące czynności:

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a>Instalowanie i konfigurowanie

1. Pobierz pakiet VPNClient z witryny Azure portal.
2. Wyodrębnij plik.
3. Z folderu **Generic** skopiuj lub przenieś plik VpnServerRoot.cer na /etc/ipsec.d/cacerts.
4. Skopiuj lub przenieś cp client.p12 do /etc/ipsec.d/private/. Ten plik jest certyfikatem klienta dla usługi Azure VPN Gateway.
5. Otwórz plik VpnSettings.xml `<VpnServer>` i skopiuj wartość. Użyjesz tej wartości w następnym kroku.
6. Dostosuj wartości w poniższym przykładzie, a następnie dodaj przykład do konfiguracji /etc/ipsec.conf.
  
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
6. Dodaj następujące elementy do */etc/ipsec.secrets*.

   ```
   : P12 client.p12 'password' # key filename inside /etc/ipsec.d/private directory
   ```

7. Uruchom następujące polecenia:

   ```
   # ipsec restart
   # ipsec up azure
   ```

## <a name="next-steps"></a>Następne kroki

Wróć do artykułu, aby [ukończyć konfigurację P2S](vpn-gateway-howto-point-to-site-rm-ps.md).

Aby rozwiązać problemy z połączeniami P2S, zobacz następujące artykuły:

  * [Rozwiązywanie problemów z połączeniami typu "punkt-lokacja" platformy Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
  * [Rozwiązywanie problemów z połączeniami sieci VPN z klientami sieci VPN w systemie Mac OS X](vpn-gateway-troubleshoot-point-to-site-osx-ikev2.md)
