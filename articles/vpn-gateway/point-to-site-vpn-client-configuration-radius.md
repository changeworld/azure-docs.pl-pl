---
title: 'Utwórz i zainstaluj pliki konfiguracji klienta sieci VPN dla połączeń P2S RADIUS: środowiska PowerShell: Azure | Dokumentacja firmy Microsoft'
description: Tworzenie klienta systemu Windows, Mac OS X i Linux VPN pliki konfiguracji dla połączeń, które korzystają z uwierzytelniania usługi RADIUS.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/07/2018
ms.author: cherylmc
ms.openlocfilehash: 19b1090a37ae1f97537fcabe128e7958fc26a96a
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35235893"
---
# <a name="create-and-install-vpn-client-configuration-files-for-p2s-radius-authentication"></a>Utwórz i zainstaluj pliki konfiguracji klienta sieci VPN do uwierzytelniania P2S RADIUS

Aby połączyć sieć wirtualną za pośrednictwem punkt lokacja (P2S), należy skonfigurować urządzenie klienckie, którym będą łączyć z. Można utworzyć połączenia sieci VPN P2S z systemu Windows, Mac OS X i Linux urządzeniach klienckich. 

Podczas korzystania z uwierzytelniania usługi RADIUS, istnieje wiele opcji uwierzytelniania: uwierzytelnianie nazwy użytkownika i hasła, uwierzytelnianie certyfikatu i inne typy uwierzytelniania. Konfiguracja klienta sieci VPN jest różne dla każdego typu uwierzytelniania. Aby skonfigurować klienta sieci VPN, należy użyć plików konfiguracji klienta, które zawierają wymagane ustawienia. Ten artykuł pomaga utworzyć i zainstalować Konfiguracja klienta sieci VPN dla typu uwierzytelniania usługi RADIUS, którego chcesz używać.

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

Konfiguracja przepływu pracy do uwierzytelniania P2S RADIUS wygląda następująco:

1. [Konfigurowanie bramy sieci VPN platformy Azure dla łączności P2S](point-to-site-how-to-radius-ps.md).
2. [Konfigurowanie serwera usługi RADIUS na potrzeby uwierzytelniania](point-to-site-how-to-radius-ps.md#radius). 
3. **Uzyskaj Konfiguracja klienta sieci VPN w przypadku opcji uwierzytelniania wybraną i umożliwia konfigurowanie klienta sieci VPN** (w tym artykule).
4. [Ukończenie konfiguracji P2S i połącz](point-to-site-how-to-radius-ps.md).

>[!IMPORTANT]
>Jeśli wystąpiły jakiekolwiek zmiany konfiguracji sieci VPN punkt lokacja po wygenerowaniu profilu konfiguracji klienta sieci VPN, takie jak typ protokołu sieci VPN lub typ uwierzytelniania, musisz wygenerować i zainstaluj nową konfigurację klienta sieci VPN na urządzeniach użytkowników.
>
>

Aby użyć sekcje w tym artykule, najpierw zdecydować, jaki typ uwierzytelniania do użycia: nazwy użytkownika i hasła, certyfikatu lub inne typy uwierzytelniania. Każda sekcja zawiera kroki dla systemu Windows, Mac OS X i Linux (ograniczone procedura w tej chwili niedostępna).

## <a name="adeap"></a>Uwierzytelnianie nazwy użytkownika i hasła

Uwierzytelnianie nazwy użytkownika i hasła, aby korzystać z usługi Active Directory lub nie używać usługi Active Directory można skonfigurować. Z każdym z tych scenariuszy upewnij się, że wszyscy użytkownicy nawiązującego połączenie jest poświadczenia nazwy użytkownika i hasła, które mogą być uwierzytelniane za pośrednictwem usługi RADIUS.

Konfigurując uwierzytelnianie nazwy użytkownika i hasła, można tworzyć tylko konfiguracji protokołu EAP-MSCHAPv2 uwierzytelnianie nazwy użytkownika i hasła. W poleceniach `-AuthenticationMethod` jest `EapMSChapv2`.

### <a name="usernamefiles"></a> 1. Generowanie plików konfiguracji klienta sieci VPN

Generowanie plików konfiguracji klienta sieci VPN do użycia z uwierzytelnianie nazwy użytkownika i hasła. Pliki konfiguracji klienta sieci VPN można wygenerować za pomocą następującego polecenia:

```powershell 
New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapMSChapv2"
```
 
Uruchamiając polecenie zwraca łącze. Skopiuj i Wklej łącze do przeglądarki sieci web, aby pobrać **VpnClientConfiguration.zip**. Rozpakuj plik, aby wyświetlić następujące foldery: 
 
* **WindowsAmd64** i **WindowsX86**: te foldery zawierają odpowiednio pakietów Instalatora Windows w wersji 64-bitowe i 32-bitowych. 
* **Ogólny**: ten folder zawiera ogólne informacje, które służy do tworzenia własnych Konfiguracja klienta sieci VPN. Ten folder nie jest konieczne w przypadku konfiguracji uwierzytelnianie nazwy użytkownika i hasła.
* **Mac**: Jeśli skonfigurowano IKEv2 po utworzeniu bramy sieci wirtualnej, zobacz folder o nazwie **Mac** zawierający **mobileconfig** pliku. Skonfiguruj klienci na komputery Mac przy użyciu tego pliku.

Jeśli utworzono już klienta pliki konfiguracji, można je odzyskać, używając `Get-AzureRmVpnClientConfiguration` polecenia cmdlet. Ale jeśli wprowadzisz zmiany w konfiguracji sieci VPN P2S, takie jak typ protokołu sieci VPN lub typ uwierzytelniania, konfiguracja nie jest aktualizowane automatycznie. Należy uruchomić `New-AzureRmVpnClientConfiguration` , aby utworzyć nowe pobranie konfiguracji.

Aby pobrać pliki konfiguracji wcześniej wygenerowanego klienta, użyj następującego polecenia:

```powershell
Get-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW"
```

### <a name="setupusername"></a> 2. Konfigurowanie klientów sieci VPN

Można skonfigurować następujące klientów sieci VPN:

* [Windows](#adwincli)
* [Mac (OS X)](#admaccli)
* [Systemu Linux przy użyciu strongSwan](#adlinuxcli)
 
#### <a name="adwincli"></a>Konfiguracja klienta VPN systemu Windows

Na każdym komputerze klienckim z systemem Windows można użyć tej samej konfiguracji pakietu klienta VPN, tak długo, jak wersja pasuje do architektury dla klienta. Aby uzyskać listę systemów operacyjnych klienta, które są obsługiwane, zobacz [— często zadawane pytania](vpn-gateway-vpn-faq.md#P2S).

Aby skonfigurować natywny klient VPN systemu Windows do uwierzytelniania certyfikatów, wykonaj następujące kroki:

1. Wybierz pliki konfiguracyjne klienta sieci VPN, które odpowiadają architektura komputera z systemem Windows. W 64-bitowy procesor, wybierz opcję **VpnClientSetupAmd64** pakiet Instalatora. W 32-bitowy procesor, wybierz opcję **VpnClientSetupX86** pakiet Instalatora. 
2. Aby zainstalować pakiet, kliknij go dwukrotnie. Jeśli zostanie wyświetlone okno podręczne SmartScreen, wybierz **więcej informacji o** > **Uruchom mimo to**.
3. Na komputerze klienckim, przejdź do **ustawienia sieciowe** i wybierz **VPN**. Połączenie z siecią VPN zawiera nazwę sieci wirtualnej, z którą jest nawiązywane połączenie. 

#### <a name="admaccli"></a>Instalacja klienta sieci VPN Mac (OS X)

1. Wybierz **VpnClientSetup mobileconfig** plików oraz wysyłać je do poszczególnych użytkowników. Możesz użyć poczty e-mail lub innej metody.

2. Zlokalizuj **mobileconfig** plików na komputerach Mac.

   ![Lokalizacja pliku mobilconfig](./media/point-to-site-vpn-client-configuration-radius/admobileconfigfile.png)
3. Kliknij dwukrotnie profilu, aby go zainstalować i wybierz **Kontynuuj**. Nazwa profilu jest taka sama jak nazwa sieci wirtualnej.

   ![Komunikat instalacji](./media/point-to-site-vpn-client-configuration-radius/adinstall.png)
4. Wybierz **Kontynuuj** ufasz nadawcy profilu i kontynuować instalację.

   ![Komunikat z potwierdzeniem](./media/point-to-site-vpn-client-configuration-radius/adcontinue.png)
5. Podczas instalacji profilu masz opcję, aby określić nazwę użytkownika i hasło dla uwierzytelniania sieci VPN. Nie jest to konieczne, aby wprowadzić te informacje. Jeśli to zrobisz, informacje są zapisywane i używane automatycznie podczas inicjowania połączenia. Wybierz **zainstalować** aby kontynuować.

   ![Pola Nazwa użytkownika i hasło dla sieci VPN](./media/point-to-site-vpn-client-configuration-radius/adsettings.png)
6. Wprowadź nazwę użytkownika i hasło dla uprawnienia, które są wymagane do zainstalowania profilu na komputerze. Kliknij przycisk **OK**.

   ![Pola Nazwa użytkownika i hasło dla instalacji profilu](./media/point-to-site-vpn-client-configuration-radius/adusername.png)
7. Po zainstalowaniu profilu nie jest widoczna **profile** okno dialogowe. Można również otworzyć to okno dialogowe później z **preferencjach systemowych**.

   ![Okno dialogowe "Profilów"](./media/point-to-site-vpn-client-configuration-radius/adsystempref.png)
8. Aby uzyskać dostęp do połączeń sieci VPN, należy otworzyć **sieci** okno dialogowe z **preferencjach systemowych**.

   ![Ikony w preferencjach systemowych](./media/point-to-site-vpn-client-configuration-radius/adnetwork.png)
9. Połączenia sieci VPN jest wyświetlana jako **IkeV2 VPN**. Można zmienić nazwę, aktualizując **mobileconfig** pliku.

   ![Szczegóły dla połączenia sieci VPN](./media/point-to-site-vpn-client-configuration-radius/adconnection.png)
10. Wybierz **ustawienia uwierzytelniania**. Wybierz **Username** na liście i wprowadź swoje poświadczenia. Jeśli wprowadzone poświadczenia wcześniej, następnie **Username** zostanie automatycznie wybrany z listy i nazwę użytkownika i hasło są wstępnie. Wybierz **OK** Aby zapisać ustawienia.

    ![Ustawienia uwierzytelniania](./media/point-to-site-vpn-client-configuration-radius/adauthentication.png)
11. W **sieci** okno dialogowe, wybierz opcję **Zastosuj** Aby zapisać zmiany. Aby zainicjować połączenie, wybierz **Connect**.

#### <a name="adlinuxcli"></a>Konfiguracja klienta VPN systemu Linux za pomocą strongSwan

Poniższe instrukcje zostały utworzone za pomocą strongSwan 5.5.1 na Ubuntu 17.0.4. Rzeczywiste Ekrany mogą być różne w zależności od używanej wersji systemu Linux i strongSwan.

1. Otwórz **terminali** do zainstalowania **strongSwan** i jego Menedżera sieci, za pomocą polecenia w przykładzie. Jeśli wystąpi błąd, który jest powiązany z `libcharon-extra-plugins`, zastąp ją z `strongswan-plugin-eap-mschapv2`.

   ```Terminal
   sudo apt-get install strongswan libcharon-extra-plugins moreutils iptables-persistent network-manager-strongswan
   ```
2. Wybierz **Menedżera sieci** ikona (up strzałkę/strzałki w dół), a następnie wybierz **edytować połączenia**.

   ![Wybór "Edytuj połączenia" w Menedżerze sieci](./media/point-to-site-vpn-client-configuration-radius/EditConnection.png)
3. Wybierz **Dodaj** przycisk, aby utworzyć nowe połączenie.

   ![Przycisk "Dodaj" dla połączenia](./media/point-to-site-vpn-client-configuration-radius/AddConnection.png)
4. Wybierz **IPsec/IKEv2 (strongswan)** z listy rozwijanej, a następnie wybierz **Utwórz**. Można zmienić nazwę połączenia w tym kroku.

   ![Wybieranie typu połączenia](./media/point-to-site-vpn-client-configuration-radius/AddIKEv2.png)
5. Otwórz **VpnSettings.xml** plik z **ogólnego** folderu plików konfiguracji klienta pobrane. Znajdź tag o nazwie `VpnServer` i skopiuj nazwę, począwszy od `azuregateway` i kończąc `.cloudapp.net`.

   ![Zawartość pliku VpnSettings.xml](./media/point-to-site-vpn-client-configuration-radius/VpnSettings.png)
6. Wklej tej nazwy do **adres** pole nowe połączenia sieci VPN w programie **bramy** sekcji. Następnie wybierz ikonę folderu na końcu **certyfikatu** pola, przejdź do **ogólnego** folder, a następnie wybierz **VpnServerRoot** pliku.
7. W **klienta** części połączenia, wybierz opcję **EAP** dla **uwierzytelniania**, a następnie wprowadź nazwę użytkownika i hasło. Może być konieczne wybierz ikonę blokady po prawej stronie, aby zapisać te informacje. Następnie wybierz pozycję **Zapisz**.

   ![Edytowanie ustawień połączenia](./media/point-to-site-vpn-client-configuration-radius/editconnectionsettings.png)
8. Wybierz **Menedżera sieci** ikony (up strzałkę/strzałki w dół), jak i zatrzymaj wskaźnik myszy **połączeń sieci VPN**. Połączenia sieci VPN, utworzony zostanie wyświetlony. Aby zainicjować połączenie, wybierz go.

   ![Połączenia "Radius sieci VPN" w Menedżerze sieci](./media/point-to-site-vpn-client-configuration-radius/ConnectRADIUS.png)

## <a name="certeap"></a>Uwierzytelnianie certyfikatu
 
Klient sieci VPN może tworzyć pliki konfiguracji uwierzytelniania certyfikatu usługi RADIUS, który korzysta z protokołu EAP-TLS. Zwykle certyfikat wystawiony przedsiębiorstwa jest używany do uwierzytelniania użytkownika dla sieci VPN. Upewnij się, że wszyscy użytkownicy łączącego mają certyfikat zainstalowany na urządzeniach i że serwer RADIUS można sprawdzić poprawności certyfikatu.

>[!NOTE]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

W poleceniach `-AuthenticationMethod` jest `EapTls`. Podczas uwierzytelniania certyfikatu klienta sprawdza serwer RADIUS weryfikując swojego certyfikatu. `-RadiusRootCert` jest plik cer zawierający certyfikat główny, który jest używany do sprawdzania poprawności serwera RADIUS.

Każde urządzenie klienta sieci VPN wymaga zainstalowany certyfikat klienta. Czasami urządzenia systemu Windows ma wiele certyfikatów klienta. Podczas uwierzytelniania może to spowodować okno podręczne okno dialogowe, które wyświetla listę wszystkich certyfikatów. Użytkownik musi następnie wybierz certyfikat do użycia. Prawidłowy certyfikat można odfiltrowane przez określenie certyfikatu głównego, który certyfikat klienta powinny być powiązane. 

`-ClientRootCert` jest to plik cer zawierający certyfikat główny. Jest parametrem opcjonalnym. Urządzenia, które chcesz się połączyć z ma tylko jeden certyfikat klienta, nie trzeba określić tego parametru.

### <a name="certfiles"></a>1. Generowanie plików konfiguracji klienta sieci VPN

Generowanie plików konfiguracji klienta sieci VPN do użycia przy użyciu uwierzytelniania certyfikatu. Pliki konfiguracji klienta sieci VPN można wygenerować za pomocą następującego polecenia:
 
```powershell
New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls" -RadiusRootCert <full path name of .cer file containing the RADIUS root> -ClientRootCert <full path name of .cer file containing the client root> | fl
```

Uruchamiając polecenie zwraca łącze. Skopiuj i Wklej łącze do przeglądarki sieci web, aby pobrać VpnClientConfiguration.zip. Rozpakuj plik, aby wyświetlić następujące foldery:

* **WindowsAmd64** i **WindowsX86**: te foldery zawierają odpowiednio pakietów Instalatora Windows w wersji 64-bitowe i 32-bitowych. 
* **GenericDevice**: ten folder zawiera ogólne informacje, które służy do tworzenia własnych Konfiguracja klienta sieci VPN.

Jeśli utworzono już klienta pliki konfiguracji, można je odzyskać, używając `Get-AzureRmVpnClientConfiguration` polecenia cmdlet. Ale jeśli wprowadzisz zmiany w konfiguracji sieci VPN P2S, takie jak typ protokołu sieci VPN lub typ uwierzytelniania, konfiguracja nie jest aktualizowane automatycznie. Należy uruchomić `New-AzureRmVpnClientConfiguration` , aby utworzyć nowe pobranie konfiguracji.

Aby pobrać pliki konfiguracji wcześniej wygenerowanego klienta, użyj następującego polecenia:

```powershell
Get-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" | fl
```
 
### <a name="setupusername"></a> 2. Konfigurowanie klientów sieci VPN

Można skonfigurować następujące klientów sieci VPN:

* [Windows](#certwincli)
* [Mac (OS X)](#certmaccli)
* Linux (obsługiwane, nie artykułu kroki jeszcze)

#### <a name="certwincli"></a>Konfiguracja klienta VPN systemu Windows

1. Wybierz pakiet konfiguracji i zainstaluj go na urządzeniu klienckim. W 64-bitowy procesor, wybierz opcję **VpnClientSetupAmd64** pakiet Instalatora. W 32-bitowy procesor, wybierz opcję **VpnClientSetupX86** pakiet Instalatora. Jeśli zostanie wyświetlone okno podręczne SmartScreen, wybierz **więcej informacji o** > **Uruchom mimo to**. Możesz także zapisać pakiet w celu zainstalowania go na innych komputerach klienckich.
2. Każdy klient wymaga certyfikatu klienta do uwierzytelniania. Zainstaluj certyfikat klienta. Informacji o certyfikatach klientów, zobacz [certyfikaty klientów dla lokacji punktu](vpn-gateway-certificates-point-to-site.md). Aby zainstalować certyfikat, który został wygenerowany, zobacz [zainstalować certyfikat na klientach systemu Windows](point-to-site-how-to-vpn-client-install-azure-cert.md).
3. Na komputerze klienckim, przejdź do **ustawienia sieciowe** i wybierz **VPN**. Połączenie z siecią VPN zawiera nazwę sieci wirtualnej, z którą jest nawiązywane połączenie.

#### <a name="certmaccli"></a>Instalacja klienta sieci VPN Mac (OS X)

Należy utworzyć osobny profil dla wszystkich urządzeń Mac, które łączy sieć wirtualna platformy Azure. Jest tak, ponieważ te urządzenia wymagają certyfikatu użytkownika do uwierzytelniania należy określić w profilu. **Ogólnego** znajduje się w nim wszystkie informacje, które są wymagane, aby utworzyć profil:

* **VpnSettings.xml** zawiera ważne ustawienia, takie jak adres i tunelu typ serwera.
* **VpnServerRoot.cer** zawiera certyfikat główny, która jest wymagana do sprawdzania poprawności bramy sieci VPN podczas konfigurowania połączenia P2S.
* **RadiusServerRoot.cer** zawiera certyfikat główny, które są wymagane do weryfikacji serwera usługi RADIUS podczas uwierzytelniania.

Aby skonfigurować natywny klient VPN na komputerze Mac na potrzeby uwierzytelniania certyfikatu, wykonaj następujące kroki:

1. Importuj **VpnServerRoot** i **RadiusServerRoot** główny certyfikatów na komputerze Macintosh. Skopiuj każdy plik do komputera Mac, kliknij go dwukrotnie, a następnie wybierz **Dodaj**.

   ![Trwa dodawanie certyfikatu VpnServerRoot](./media/point-to-site-vpn-client-configuration-radius/addcert.png)

   ![Trwa dodawanie certyfikatu RadiusServerRoot](./media/point-to-site-vpn-client-configuration-radius/radiusrootcert.png)
2. Każdy klient wymaga certyfikatu klienta do uwierzytelniania. Zainstaluj certyfikat klienta na urządzeniu klienckim.
3. Otwórz **sieci** okno dialogowe, w obszarze **preferencji sieci**. Wybierz **+** Aby utworzyć nowy profil połączenia klienta sieci VPN dla połączenia P2S sieci wirtualnej platformy Azure.

   **Interfejsu** wartość jest **VPN**i **typ sieci VPN** wartość jest **IKEv2**. Określ nazwę profilu w **nazwa usługi** , a następnie wybierz **Utwórz** do tworzenia profilu połączenia VPN klienta.

   ![Informacje o nazwie interfejsu i usługi](./media/point-to-site-vpn-client-configuration-radius/network.png)
4. W **ogólnego** folder, z **VpnSettings.xml** pliku, skopiuj **VpnServer** wartość tagu. Wklej tę wartość w **adres serwera** i **Identyfikator zdalny** pola profilu. Pozostaw **identyfikator lokalny** pole puste.

   ![Informacje o serwerze](./media/point-to-site-vpn-client-configuration-radius/servertag.png)
5. Wybierz **ustawienia uwierzytelniania**i wybierz **certyfikatu**. 

   ![Ustawienia uwierzytelniania](./media/point-to-site-vpn-client-configuration-radius/certoption.png)
6. Kliknij przycisk **wybierz** do wybrania certyfikatu, który ma być używany do uwierzytelniania.

   ![Wybranie certyfikatu na potrzeby uwierzytelniania](./media/point-to-site-vpn-client-configuration-radius/certificate.png)
7. **Wybierz tożsamość** zostanie wyświetlona lista certyfikatów dla można wybrać. Wybierz odpowiedni certyfikat, a następnie wybierz **Kontynuuj**.

   !["Wybierz tożsamość" listy](./media/point-to-site-vpn-client-configuration-radius/identity.png)
8. W **identyfikator lokalny** Określ nazwę certyfikatu (z kroku 6). W tym przykładzie ma **ikev2Client.com**. Następnie wybierz opcję **Zastosuj** przycisk, aby zapisać zmiany.

   ![Pole "ID lokalnych"](./media/point-to-site-vpn-client-configuration-radius/applyconnect.png)
9. W **sieci** okno dialogowe, wybierz opcję **Zastosuj** można zapisać wszystkich zmian. Następnie wybierz opcję **Connect** nawiązaniu połączenia P2S do sieci wirtualnej platformy Azure.

## <a name="otherauth"></a>Praca z innych typów uwierzytelniania lub protokołów

Aby używać typu uwierzytelniania inny (na przykład OTP) lub użyj innego protokołu uwierzytelniania (np. PEAP-MS-CHAPv2 zamiast protokołu EAP-MSCHAPv2), należy utworzyć własny profil konfiguracji klienta sieci VPN. Aby utworzyć profil, potrzebne informacje, takie jak adres IP bramy sieci wirtualnej, Typ tunelu i trasy tunelowania podzielonego. Te informacje można uzyskać za pomocą następujących kroków:

1. Użyj `Get-AzureRmVpnClientConfiguration` polecenia cmdlet, aby wygenerować EapMSChapv2 konfiguracji klienta sieci VPN. Aby uzyskać instrukcje, zobacz [w tej sekcji](#ccradius) artykułu.

2. Rozpakuj plik VpnClientConfiguration.zip i poszukaj **GenenericDevice** folderu. Ignoruj foldery zawierające pliki instalacyjne systemu Windows dla architektur 64-bitowe i 32-bitowych.
 
3. **GenenericDevice** folder zawiera plik XML o nazwie **VpnSettings**. Ten plik zawiera wszystkie wymagane informacje:

   * **VpnServer**: nazwa FQDN bramy sieci VPN platformy Azure. Jest to adres, który klient łączy się.
   * **VpnType**: tunelowania typu, który jest używany do łączenia.
   * **Trasy**: tras, na których należy skonfigurować w profilu, tak aby tylko ruch skierowany, przeznaczony dla sieci wirtualnej platformy Azure jest przesyłane przez tunel P2S.
   
   **GenenericDevice** folder zawiera także plik .cer o nazwie **VpnServerRoot**. Ten plik zawiera certyfikat główny, która jest wymagana do sprawdzania poprawności bramy sieci VPN platformy Azure, podczas konfigurowania połączenia P2S. Zainstaluj certyfikat na wszystkich urządzeniach, które będą łączyć się z sieci wirtualnej platformy Azure.

## <a name="next-steps"></a>Kolejne kroki

Wróć do tego artykułu, aby [ukończyć konfigurację P2S](point-to-site-how-to-radius-ps.md).

Aby P2S Rozwiązywanie problemów z informacji, zobacz [połączenia punkt lokacja Azure Rozwiązywanie problemów z](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).