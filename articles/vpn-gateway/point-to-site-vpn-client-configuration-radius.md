---
title: 'Tworzenie i instalowanie plików konfiguracji klienta sieci VPN dla połączeń P2S RADIUS: Program PowerShell: Azure | Microsoft Docs'
description: Tworzenie klienta Windows, Mac OS X i Linux w sieci VPN pliki konfiguracji dla połączenia, które korzystają z uwierzytelniania usługi RADIUS.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/27/2019
ms.author: cherylmc
ms.openlocfilehash: 34d8eb976a2a1e173f234be214799832dae7e9ca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66115388"
---
# <a name="create-and-install-vpn-client-configuration-files-for-p2s-radius-authentication"></a>Tworzenie i instalowanie plików konfiguracji klienta sieci VPN, uwierzytelnianie usługi RADIUS P2S

Aby połączyć się z siecią wirtualną za pośrednictwem punkt lokacja (P2S), należy skonfigurować urządzenie klienckie, które połączysz z. Można utworzyć połączenia sieci VPN typu P2S z Windows, Mac OS X i Linux urządzeniach klienckich. 

Podczas korzystania z uwierzytelniania usługi RADIUS, istnieje wiele opcji uwierzytelniania: uwierzytelnianie przy użyciu nazwy użytkownika/hasła, uwierzytelnianie certyfikatu i innych typów uwierzytelniania. Konfiguracja klienta sieci VPN jest inny dla każdego typu uwierzytelniania. Aby skonfigurować klienta sieci VPN, należy użyć plików konfiguracji klienta, które zawierają wymagane ustawienia. Ten artykuł ułatwia tworzenie i instalowanie konfiguracji klienta sieci VPN dla typu uwierzytelniania usługi RADIUS, którego chcesz używać.

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

Konfiguracja przepływu pracy dla uwierzytelniania P2S RADIUS jest następująca:

1. [Konfigurowanie bramy sieci VPN platformy Azure dla łączności P2S](point-to-site-how-to-radius-ps.md).
2. [Konfigurowanie serwera usługi RADIUS na potrzeby uwierzytelniania](point-to-site-how-to-radius-ps.md#radius). 
3. **Uzyskiwanie konfiguracji klienta sieci VPN dla opcji uwierzytelniania w wybranym i używać go do skonfigurowania klienta sieci VPN** (w tym artykule).
4. [Ukończenie konfiguracji P2S i połącz](point-to-site-how-to-radius-ps.md).

>[!IMPORTANT]
>W przypadku zmiany wprowadzone w konfiguracji sieci VPN typu punkt lokacja po wygenerowaniu profilu konfiguracji klienta sieci VPN, takie jak typ protokołu sieci VPN lub typ uwierzytelniania, musisz wygenerować i zainstalować nowe konfiguracji klienta sieci VPN na urządzeniach Twoich użytkowników.
>
>

Aby użyć sekcji w niniejszym artykule, najpierw zdecyduj, jaki typ uwierzytelniania chcesz użyć: nazwy użytkownika/hasła, certyfikat lub inne typy uwierzytelniania. Każda sekcja zawiera kroki dla Windows, Mac OS X i Linux (ograniczony kroków opisanych w tej chwili).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="adeap"></a>Uwierzytelnianie za pomocą nazwy użytkownika/hasła

Można skonfigurować uwierzytelniania nazwy użytkownika/hasła korzysta z usługi Active Directory lub użyć usługi Active Directory. Z dowolnym scenariuszu upewnij się, że wszyscy użytkownicy nawiązującego połączenie jest poświadczenia nazwy użytkownika/hasła, które mogą być uwierzytelniane za pośrednictwem usługi RADIUS.

Po skonfigurowaniu uwierzytelniania nazwy użytkownika i hasła, można tworzyć tylko konfiguracji protokołu EAP-MSCHAPv2 uwierzytelniania nazwy użytkownika i hasła. W poleceniach `-AuthenticationMethod` jest `EapMSChapv2`.

### <a name="usernamefiles"></a> 1. Generowanie plików konfiguracji klienta sieci VPN

Za pomocą witryny Azure portal lub za pomocą programu Azure PowerShell, można wygenerować plików konfiguracji klienta sieci VPN.

#### <a name="azure-portal"></a>Azure Portal

1. Przejdź do bramy sieci wirtualnej.
2. Kliknij przycisk **Point-to-Site configuration**.
3. Kliknij przycisk **klienta VPN Pobierz**.
4. Wybierz klienta, a następnie wypełnij wszelkie informacje, które są wymagane.
5. Kliknij przycisk **Pobierz** Generowanie pliku zip.
6. Plik zip pobierze się zwykle w folderze pobrane.

#### <a name="azure-powershell"></a>Azure PowerShell

Generowanie plików konfiguracji klienta sieci VPN do użycia przy użyciu uwierzytelniania nazwy użytkownika i hasła. Pliki konfiguracji klienta sieci VPN można wygenerować za pomocą następującego polecenia:

```azurepowershell-interactive
New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapMSChapv2"
```
 
Uruchamiając polecenie zwraca link. Skopiuj i Wklej link do przeglądarki sieci web, aby pobrać **VpnClientConfiguration.zip**. Rozpakuj plik, aby wyświetlić następujące foldery: 
 
* **WindowsAmd64** i **WindowsX86**: Te foldery zawierają pakietów Instalatora Windows 64-bitowe i 32-bitowych, odpowiednio. 
* **Ogólny**: Ten folder zawiera ogólne informacje, które umożliwia tworzenie własnych konfiguracji klienta sieci VPN. Ten folder nie jest wymagany dla konfiguracji uwierzytelniania nazwy użytkownika i hasła.
* **Mac**: Jeśli podczas tworzenia bramy sieci wirtualnej skonfigurowano protokół IKEv2, zostanie wyświetlony folder o nazwie **Mac** zawierający **mobileconfig** pliku. Skonfiguruj klientów na komputery Mac przy użyciu tego pliku.

Jeśli już utworzono klienta pliki konfiguracji, można je pobrać za pomocą `Get-AzVpnClientConfiguration` polecenia cmdlet. Ale jeśli wprowadzisz zmiany w konfiguracji sieci VPN P2S, takie jak typ protokołu sieci VPN lub typ uwierzytelniania, konfiguracja nie jest aktualizowane automatycznie. Należy uruchomić `New-AzVpnClientConfiguration` polecenia cmdlet, aby utworzyć nowy plik do pobrania konfiguracji.

Aby pobrać pliki konfiguracyjne wcześniej wygenerowanego klienta, użyj następującego polecenia:

```azurepowershell-interactive
Get-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW"
```

### <a name="setupusername"></a> 2. Konfigurowanie klientów sieci VPN

Można skonfigurować następujące klientów sieci VPN:

* [Windows](#adwincli)
* [Mac (OS X)](#admaccli)
* [Systemu Linux przy użyciu strongSwan](#adlinuxcli)
 
#### <a name="adwincli"></a>Konfiguracja klienta sieci VPN Windows

Na każdym komputerze klienckim Windows można użyć tego samego pakietu konfiguracji klienta sieci VPN, tak długo, jak wersja jest zgodna z architekturą dla klienta. Aby uzyskać listę obsługiwanych systemów operacyjnych klienta, zobacz [— często zadawane pytania](vpn-gateway-vpn-faq.md#P2S).

Wykonaj następujące kroki, aby skonfigurować natywnego klienta sieci VPN Windows w celu uwierzytelnienia certyfikatu:

1. Wybierz pliki konfiguracji klienta sieci VPN, które odpowiadają architekturze komputera z systemem Windows. Architektura 64-bitowy procesor, można wybrać **VpnClientSetupAmd64** pakiet Instalatora. Dla architektury 32-bitowym procesorze, wybierz **VpnClientSetupX86** pakiet Instalatora. 
2. Aby zainstalować pakiet, kliknij go dwukrotnie. Jeśli zostanie wyświetlone okno podręczne SmartScreen, wybierz opcję **więcej informacji o** > **Uruchom mimo to**.
3. Na komputerze klienckim przejdź do **ustawienia sieciowe** i wybierz **VPN**. Połączenie z siecią VPN zawiera nazwę sieci wirtualnej, z którą jest nawiązywane połączenie. 

#### <a name="admaccli"></a>Konfiguracja klienta sieci VPN Mac (OS X)

1. Wybierz **VpnClientSetup mobileconfig** plików oraz wysyłać je do każdego z użytkowników. Można użyć poczty e-mail lub innej metody.

2. Znajdź **mobileconfig** pliku na komputerze Mac.

   ![Lokalizacja pliku mobileconfig](./media/point-to-site-vpn-client-configuration-radius/admobileconfigfile.png)

3. Krok opcjonalny — Jeśli chcesz określić niestandardowe DNS, Dodaj następujące wiersze do **mobileconfig** pliku:

   ```xml
    <key>DNS</key>
    <dict>
      <key>ServerAddresses</key>
        <array>
            <string>10.0.0.132</string>
        <array>
      <key>SupplementalMatchDomains</key>
        <array>
            <string>TestDomain.com</string>
        </array>
    </dict> 
   ```
4. Kliknij dwukrotnie profil, aby go zainstalować, a następnie wybierz pozycję **Kontynuuj**. Nazwa profilu jest taka sama jak nazwa sieci wirtualnej.

   ![Komunikat instalacji](./media/point-to-site-vpn-client-configuration-radius/adinstall.png)
5. Wybierz **Kontynuuj** ufasz nadawcy profilu i kontynuować instalację.

   ![Komunikat z potwierdzeniem](./media/point-to-site-vpn-client-configuration-radius/adcontinue.png)
6. Podczas instalacji profilu masz możliwość określenia nazwy użytkownika i hasło dla uwierzytelniania sieci VPN. Nie jest wymagane, aby wprowadzić te informacje. Jeśli to zrobisz, informacje są zapisywane i automatycznie używane podczas inicjowania połączenia. Wybierz **zainstalować** aby kontynuować.

   ![Pola Nazwa użytkownika i hasło dla sieci VPN](./media/point-to-site-vpn-client-configuration-radius/adsettings.png)
7. Wprowadź nazwę użytkownika i hasło dla uprawnień, które są wymagane do zainstalowania profilu na komputerze. Kliknij przycisk **OK**.

   ![Pola Nazwa użytkownika i hasło dla instalacji profilu](./media/point-to-site-vpn-client-configuration-radius/adusername.png)
8. Po zainstalowaniu profilu jest widoczna w **profile** okno dialogowe. Możesz również otworzyć to okno dialogowe później **preferencjach systemowych**.

   ![Okno dialogowe "Profile"](./media/point-to-site-vpn-client-configuration-radius/adsystempref.png)
9. Aby uzyskać dostęp do połączenia sieci VPN, należy otworzyć **sieci** okno dialogowe z **preferencjach systemowych**.

   ![Ikony w preferencjach systemowych](./media/point-to-site-vpn-client-configuration-radius/adnetwork.png)
10. Połączenie sieci VPN jest wyświetlany jako **sieci VPN IkeV2**. Nazwę można zmienić, aktualizując **mobileconfig** pliku.

    ![Szczegółowe informacje dla połączenia sieci VPN](./media/point-to-site-vpn-client-configuration-radius/adconnection.png)
11. Wybierz **ustawienia uwierzytelniania**. Wybierz **Username** na liście i wprowadź swoje poświadczenia. Jeśli zostały podane poświadczenia wcześniej, następnie **Username** zostanie automatycznie wybrana z listy i nazwę użytkownika i hasło są wstępnie wypełnione. Wybierz **OK** Aby zapisać ustawienia.

    ![Ustawienia uwierzytelniania](./media/point-to-site-vpn-client-configuration-radius/adauthentication.png)
12. Ponownie **sieci** okno dialogowe, wybierz opcję **Zastosuj** Aby zapisać zmiany. Aby zainicjować połączenie, wybierz pozycję **Connect**.

#### <a name="adlinuxcli"></a>Instalacja klienta sieci VPN w systemie Linux za pomocą strongSwan

Poniższe instrukcje zostały utworzone za pomocą strongSwan 5.5.1 w systemie Ubuntu 17.0.4. Rzeczywiste Ekrany mogą się różnić, w zależności od używanej wersji systemu Linux i strongSwan.

1. Otwórz **terminalu** zainstalował **strongSwan** i jego Menedżera sieci, uruchamiając polecenie w przykładzie. Jeśli otrzymasz komunikat o błędzie, który jest powiązany z `libcharon-extra-plugins`, zastąp go wartością `strongswan-plugin-eap-mschapv2`.

   ```Terminal
   sudo apt-get install strongswan libcharon-extra-plugins moreutils iptables-persistent network-manager-strongswan
   ```
2. Wybierz **Menedżera sieci** ikona (Strzałka/w dół — strzałek w górę), a następnie wybierz **edycji połączenia**.

   ![Wybór "Edytuj połączenia" w Menedżerze sieci](./media/point-to-site-vpn-client-configuration-radius/EditConnection.png)
3. Wybierz **Dodaj** przycisk, aby utworzyć nowe połączenie.

   ![Przycisk "Dodaj" dla połączenia](./media/point-to-site-vpn-client-configuration-radius/AddConnection.png)
4. Wybierz **protokołu IPsec/IKEv2 (strongswan)** z menu rozwijanego, a następnie wybierz **Utwórz**. Można zmienić nazwę połączenia w taki sposób, w tym kroku.

   ![Wybieranie typu połączenia](./media/point-to-site-vpn-client-configuration-radius/AddIKEv2.png)
5. Otwórz **VpnSettings.xml** plik wchodzącej w skład **ogólny** folderu plików konfiguracji klienta pobrane. Znajdź tag o nazwie `VpnServer` i skopiuj nazwę, począwszy od `azuregateway` i kończąc `.cloudapp.net`.

   ![Zawartość pliku VpnSettings.xml](./media/point-to-site-vpn-client-configuration-radius/VpnSettings.png)
6. Wklej tę nazwę do **adres** pole nowego połączenia sieci VPN **bramy** sekcji. Następnie wybierz ikonę folderu, na końcu **certyfikatu** pola, przejdź do **ogólny** folder, a następnie wybierz **VpnServerRoot** pliku.
7. W **klienta** części połączenia, wybierz opcję **EAP** dla **uwierzytelniania**, a następnie wprowadź nazwę użytkownika i hasło. Trzeba wybrać ikonę blokady po prawej stronie, aby zapisać te informacje. Następnie wybierz pozycję **Zapisz**.

   ![Edytowanie ustawień połączenia](./media/point-to-site-vpn-client-configuration-radius/editconnectionsettings.png)
8. Wybierz **Menedżera sieci** ikona (Strzałka/w dół — strzałek w górę) i umieść kursor nad **połączeń sieci VPN**. Widać wcześniej utworzone połączenie sieci VPN. Aby zainicjować połączenie, wybierz ją.

   ![Połączenie "Radius sieci VPN" w Menedżerze sieci](./media/point-to-site-vpn-client-configuration-radius/ConnectRADIUS.png)

## <a name="certeap"></a>Uwierzytelnianie certyfikatu
 
Pliki konfiguracji do uwierzytelniania certyfikatu usługi RADIUS, który korzysta z protokołu EAP-TLS można utworzyć klienta sieci VPN. Zwykle certyfikat wystawiony przez przedsiębiorstwa jest używany do uwierzytelniania użytkownika dla sieci VPN. Upewnij się, że wszyscy użytkownicy połączenia mają certyfikatu zainstalowanego na swoich urządzeniach i czy serwera RADIUS można sprawdzić poprawności certyfikatu.

>[!NOTE]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

W poleceniach `-AuthenticationMethod` jest `EapTls`. Podczas uwierzytelniania certyfikatu klient sprawdza poprawność serwera RADIUS, sprawdzając poprawność swojego certyfikatu. `-RadiusRootCert` jest to plik cer zawierający certyfikat główny, który służy do sprawdzania poprawności serwera RADIUS.

Każde urządzenie klienta sieci VPN wymaga zainstalowany certyfikat klienta. Czasami urządzenie Windows może mieć wielu certyfikatów klienta. Podczas uwierzytelniania może to spowodować wyskakujące okno dialogowe zawierające listę wszystkich certyfikatów. Użytkownik musi następnie wybierz certyfikat do użycia. Prawidłowy certyfikat można odfiltrowane przez określenie certyfikatu głównego, do którego należy utworzyć łańcuch certyfikatów klienta. 

`-ClientRootCert` jest to plik cer zawierający certyfikat główny. Jest parametrem opcjonalnym. Urządzenia, z którym chcesz nawiązać połączenie z ma tylko jeden certyfikat klienta, nie trzeba określić ten parametr.

### <a name="certfiles"></a>1. Generowanie plików konfiguracji klienta sieci VPN

Generowanie plików konfiguracji klienta sieci VPN do użycia przy użyciu uwierzytelniania certyfikatu. Pliki konfiguracji klienta sieci VPN można wygenerować za pomocą następującego polecenia:
 
```azurepowershell-interactive
New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls" -RadiusRootCert <full path name of .cer file containing the RADIUS root> -ClientRootCert <full path name of .cer file containing the client root> | fl
```

Uruchamiając polecenie zwraca link. Skopiuj i Wklej link do przeglądarki sieci web, aby pobrać VpnClientConfiguration.zip. Rozpakuj plik, aby wyświetlić następujące foldery:

* **WindowsAmd64** i **WindowsX86**: Te foldery zawierają pakietów Instalatora Windows 64-bitowe i 32-bitowych, odpowiednio. 
* **GenericDevice**: Ten folder zawiera ogólne informacje, które są używane do tworzenia własnych konfiguracji klienta sieci VPN.

Jeśli już utworzono klienta pliki konfiguracji, można je pobrać za pomocą `Get-AzVpnClientConfiguration` polecenia cmdlet. Ale jeśli wprowadzisz zmiany w konfiguracji sieci VPN P2S, takie jak typ protokołu sieci VPN lub typ uwierzytelniania, konfiguracja nie jest aktualizowane automatycznie. Należy uruchomić `New-AzVpnClientConfiguration` polecenia cmdlet, aby utworzyć nowy plik do pobrania konfiguracji.

Aby pobrać pliki konfiguracyjne wcześniej wygenerowanego klienta, użyj następującego polecenia:

```azurepowershell-interactive
Get-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" | fl
```
 
### <a name="setupusername"></a> 2. Konfigurowanie klientów sieci VPN

Można skonfigurować następujące klientów sieci VPN:

* [Windows](#certwincli)
* [Mac (OS X)](#certmaccli)
* Linux (obsługiwane, nie artykule podano instrukcje jeszcze)

#### <a name="certwincli"></a>Konfiguracja klienta sieci VPN Windows

1. Wybierz pakiet konfiguracji, a następnie zainstaluj go na urządzeniu klienckim. Architektura 64-bitowy procesor, można wybrać **VpnClientSetupAmd64** pakiet Instalatora. Dla architektury 32-bitowym procesorze, wybierz **VpnClientSetupX86** pakiet Instalatora. Jeśli zostanie wyświetlone okno podręczne SmartScreen, wybierz opcję **więcej informacji o** > **Uruchom mimo to**. Możesz także zapisać pakiet w celu zainstalowania go na innych komputerach klienckich.
2. Każdy klient wymaga certyfikatu klienta do uwierzytelniania. Instalowanie certyfikatu klienta. Aby uzyskać informacji na temat certyfikatów klienta, zobacz [certyfikatów klienta dla point-to-site](vpn-gateway-certificates-point-to-site.md). Aby zainstalować certyfikat, który został wygenerowany, zobacz [Zainstaluj certyfikat na klientach Windows](point-to-site-how-to-vpn-client-install-azure-cert.md).
3. Na komputerze klienckim przejdź do **ustawienia sieciowe** i wybierz **VPN**. Połączenie z siecią VPN zawiera nazwę sieci wirtualnej, z którą jest nawiązywane połączenie.

#### <a name="certmaccli"></a>Konfiguracja klienta sieci VPN Mac (OS X)

Należy utworzyć osobny profil dla każdego urządzenia Mac, który nawiązuje połączenie z sieci wirtualnej platformy Azure. Jest tak, ponieważ te urządzenia wymagają certyfikatu użytkownika do uwierzytelniania należy określić w profilu. **Ogólny** folder ma wszystkie informacje, które są wymagane do utworzenia profilu:

* **VpnSettings.xml** zawiera ważne ustawienia, takie jak adres i tunel typu serwera.
* **VpnServerRoot.cer** zawiera certyfikat główny, który jest wymagany do sprawdzania poprawności bramy sieci VPN podczas konfigurowania połączenia P2S.
* **RadiusServerRoot.cer** zawiera certyfikat główny, które są wymagane w celu zweryfikowania serwera usługi RADIUS podczas uwierzytelniania.

Poniższe kroki umożliwiają konfigurowanie natywnego klienta sieci VPN na komputerach Mac w celu uwierzytelnienia certyfikatu:

1. Importuj **VpnServerRoot** i **RadiusServerRoot** rtyfikaty na komputerze Mac. Skopiuj każdy plik z Twoim komputerem Mac, kliknij go dwukrotnie, a następnie wybierz **Dodaj**.

   ![Dodawanie certyfikatu VpnServerRoot](./media/point-to-site-vpn-client-configuration-radius/addcert.png)

   ![Dodawanie certyfikatu RadiusServerRoot](./media/point-to-site-vpn-client-configuration-radius/radiusrootcert.png)
2. Każdy klient wymaga certyfikatu klienta do uwierzytelniania. Instalacja certyfikatu klienta na urządzeniu klienckim.
3. Otwórz **sieci** okno dialogowe, w obszarze **preferencje sieci**. Wybierz **+** Aby utworzyć nowy profil połączenia klienta sieci VPN dla połączeń P2S, siecią wirtualną platformy Azure.

   **Interfejsu** wartość **VPN**i **typu sieci VPN** wartość **IKEv2**. Określ nazwę profilu w **nazwa usługi** , a następnie wybierz **Utwórz** tworzenia profilu połączenia sieci VPN klienta.

   ![Informacje o nazwie interfejsu i usługi](./media/point-to-site-vpn-client-configuration-radius/network.png)
4. W **ogólny** folderu z **VpnSettings.xml** plików, skopiuj **VpnServer** wartość tagu. Wklej tę wartość w **adres serwera** i **identyfikator zdalnego** pola profilu. Pozostaw **lokalnego Identyfikatora** puste pole.

   ![Informacje o serwerze](./media/point-to-site-vpn-client-configuration-radius/servertag.png)
5. Wybierz **ustawienia uwierzytelniania**i wybierz **certyfikatu**. 

   ![Ustawienia uwierzytelniania](./media/point-to-site-vpn-client-configuration-radius/certoption.png)
6. Kliknij przycisk **wybierz** wybrać certyfikat, który ma zostać użyty do uwierzytelniania.

   ![Wybieranie certyfikatu na potrzeby uwierzytelniania](./media/point-to-site-vpn-client-configuration-radius/certificate.png)
7. **Wybierz tożsamość** jest wyświetlana lista certyfikatów, można wybrać. Wybierz odpowiedni certyfikat, a następnie wybierz **Kontynuuj**.

   ![Listy "Wybierz tożsamość"](./media/point-to-site-vpn-client-configuration-radius/identity.png)
8. W **lokalnego Identyfikatora** należy określić nazwę certyfikatu (z kroku 6). W tym przykładzie przedstawiono w nim **ikev2Client.com**. Następnie wybierz **Zastosuj** przycisk, aby zapisać zmiany.

   ![Pole "Identyfikator lokalny"](./media/point-to-site-vpn-client-configuration-radius/applyconnect.png)
9. W **sieci** okno dialogowe, wybierz opcję **Zastosuj** Aby zapisać wszystkie zmiany. Następnie wybierz **Connect** nawiązaniu połączenia P2S, siecią wirtualną platformy Azure.

## <a name="otherauth"></a>Praca z innych typów uwierzytelniania lub protokołów

Aby użyć innego typu uwierzytelniania (na przykład OTP) lub użyć innego protokołu uwierzytelniania (np. PEAP-MS-CHAPv2 zamiast protokołu EAP-MSCHAPv2), należy utworzyć własny profil konfiguracji klienta sieci VPN. Aby utworzyć profil, potrzebne informacje, takie jak adres IP bramy sieci wirtualnej, Typ tunelu i trasy tunelowania podzielonego. Aby uzyskać te informacje, wykonując następujące czynności:

1. Użyj `Get-AzVpnClientConfiguration` polecenia cmdlet w celu wygenerowania konfiguracji klienta sieci VPN dla EAP Mschapv2.

2. Rozpakuj plik VpnClientConfiguration.zip i poszukaj **GenericDevice** folderu. Ignoruj foldery zawierające pliki instalacyjne dla architektury 64-bitowe i 32-bitowe programów Windows.
 
3. **GenericDevice** folder zawiera plik XML o nazwie **VpnSettings**. Ten plik zawiera wszystkie wymagane informacje:

   * **VpnServer**: Nazwa FQDN bramy sieci VPN platformy Azure. Jest to adres, który łączy się z klienta.
   * **VpnType**: Typ tunelu, który umożliwia łączenie.
   * **Trasy**: Trasy, które należy skonfigurować w profilu, tak aby tylko ruch, który jest powiązany dla sieci wirtualnej platformy Azure są wysyłane za pośrednictwem tunelu P2S.
   
   **GenericDevice** folder zawiera także plik cer o nazwie **VpnServerRoot**. Ten plik zawiera certyfikat główny, który jest wymagany do sprawdzania poprawności bramy sieci VPN platformy Azure podczas konfigurowania połączenia P2S. Zainstaluj certyfikat na wszystkich urządzeniach, które będą łączyć z siecią wirtualną platformy Azure.

## <a name="next-steps"></a>Kolejne kroki

Wróć do artykułu, aby [ukończyć konfigurację P2S](point-to-site-how-to-radius-ps.md).

Aby uzyskać P2S Rozwiązywanie problemów z informacji, zobacz [połączeń punkt lokacja Rozwiązywanie problemów z Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
