---
title: Rozwiązywanie problemów z połączeniem typu punkt-lokacja platformy Azure
titleSuffix: Azure VPN Gateway
description: Dowiedz się, jak rozwiązywać problemy z połączeniem typu punkt-lokacja.
services: vpn-gateway
author: chadmath
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 03/26/2020
ms.author: genli
ms.openlocfilehash: 119f9c28b5413b8d2db5fa14ea839d1743f3d64a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297621"
---
# <a name="troubleshooting-azure-point-to-site-connection-problems"></a>Rozwiązywanie problemów: problemy z połączeniem typu punkt-lokacja platformy Azure

W tym artykule wymieniono typowe problemy z połączeniem typu punkt-lokacja, które mogą wystąpić. Omówiono również możliwe przyczyny i rozwiązania tych problemów.

## <a name="vpn-client-error-a-certificate-could-not-be-found"></a>Błąd klienta sieci VPN: nie można odnaleźć certyfikatu

### <a name="symptom"></a>Objaw

Podczas próby nawiązania połączenia z siecią wirtualną platformy Azure przy użyciu klienta sieci VPN zostanie wyświetlony następujący komunikat o błędzie:

**Nie można odnaleźć certyfikatu, który może być używany z tym protokołem uwierzytelniania rozszerzalnego. (Błąd 798)**

### <a name="cause"></a>Przyczyna

Ten problem występuje, jeśli w certyfikatach klienta brakuje **certyfikatu - Bieżący użytkownik\Osobisty\Certyfikaty**.

### <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, wykonaj następujące kroki:

1. Otwórz Menedżera certyfikatów: kliknij przycisk **Start**, wpisz **zarządzanie certyfikatami komputera**, a następnie kliknij pozycję **Zarządzaj certyfikatami komputerów** w wynikach wyszukiwania.

2. Upewnij się, że następujące certyfikaty znajdują się we właściwym miejscu:

    | Certyfikat | Lokalizacja |
    | ------------- | ------------- |
    | AzureClient.pfx  | Bieżący użytkownik\Osobiste\Certyfikaty |
    | Usługa AzureRoot.cer    | Komputer lokalny\Zaufane główne urzędy certyfikacji|

3. Przejdź do folderu\<C:\Users UserName>\AppData\Roaming\Microsoft\Network\Connections\Cm\<GUID>, ręcznie zainstaluj certyfikat (plik*.cer) w magazynie użytkownika i komputera.

Aby uzyskać więcej informacji na temat instalowania certyfikatu klienta, zobacz [Generowanie i eksportowanie certyfikatów dla połączeń typu punkt-lokacja](vpn-gateway-certificates-point-to-site.md).

> [!NOTE]
> Podczas importowania certyfikatu klienta nie należy wybierać opcji **Włącz silną ochronę klucza prywatnego.**

## <a name="the-network-connection-between-your-computer-and-the-vpn-server-could-not-be-established-because-the-remote-server-is-not-responding"></a>Nie można ustanowić połączenia sieciowego między komputerem a serwerem sieci VPN, ponieważ serwer zdalny nie odpowiada

### <a name="symptom"></a>Objaw

Podczas próby nawiązania połączenia z bramą sieci wirtualnej platformy Azure przy użyciu usługi IKEv2 w systemie Windows zostanie wyświetlony następujący komunikat o błędzie:

**Nie można ustanowić połączenia sieciowego między komputerem a serwerem sieci VPN, ponieważ serwer zdalny nie odpowiada**

### <a name="cause"></a>Przyczyna
 
 Ten problem występuje, jeśli wersja systemu Windows nie obsługuje fragmentacji usługi IKE
 
### <a name="solution"></a>Rozwiązanie

Protokół IKEv2 jest obsługiwany w systemach Windows 10 i Server 2016. Jednak aby można było używać protokołu IKEv2, należy zainstalować aktualizacje i lokalnie ustawić wartość klucza rejestru. Wersje systemu operacyjnego starsze niż Windows 10 nie są obsługiwane i mogą używać tylko protokołu SSTP.

Aby przygotowywać system Windows 10 lub Server 2016 pod kątem protokołu IKEv2:

1. Zainstaluj aktualizację.

   | Wersja systemu operacyjnego | Data | Numer/link |
   |---|---|---|---|
   | Windows Server 2016<br>Windows 10 w wersji 1607 | 17 stycznia 2018 r. | [KB4057142](https://support.microsoft.com/help/4057142/windows-10-update-kb4057142) |
   | Windows 10 w wersji 1703 | 17 stycznia 2018 r. | [KB4057144](https://support.microsoft.com/help/4057144/windows-10-update-kb4057144) |
   | Windows 10 wersja 1709 | 22 marca 2018 r. | [aktualizacja KB4089848](https://www.catalog.update.microsoft.com/search.aspx?q=kb4089848) |
   |  |  |  |  |

2. Ustaw wartość klucza rejestru. Utwórz `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\RasMan\ IKEv2\DisableCertReqPayload` lub ustaw REG_DWORD klucz w rejestrze na 1.

## <a name="vpn-client-error-the-message-received-was-unexpected-or-badly-formatted"></a>Błąd klienta sieci VPN: odebrany komunikat był nieoczekiwany lub źle sformatowany

### <a name="symptom"></a>Objaw

Podczas próby nawiązania połączenia z siecią wirtualną platformy Azure przy użyciu klienta sieci VPN zostanie wyświetlony następujący komunikat o błędzie:

**Odebrana wiadomość była nieoczekiwana lub źle sformatowana. (Błąd 0x80090326)**

### <a name="cause"></a>Przyczyna

Ten problem występuje, jeśli spełniony jest jeden z następujących warunków:

- Użyj tras zdefiniowanych przez użytkownika (UDR) z trasą domyślną w podsieci bramy jest ustawiona niepoprawnie.
- Klucz publiczny certyfikatu głównego nie jest przekazyany do bramy sieci VPN platformy Azure. 
- Klucz jest uszkodzony lub wygasł.

### <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, wykonaj następujące kroki:

1. Usuń UDR w podsieci bramy. Upewnij się, że UDR przekazuje cały ruch prawidłowo.
2. Sprawdź stan certyfikatu głównego w witrynie Azure portal, aby sprawdzić, czy został odwołany. Jeśli nie zostanie odwołany, spróbuj usunąć certyfikat główny i ponownie załadować. Aby uzyskać więcej informacji, zobacz [Tworzenie certyfikatów](vpn-gateway-howto-point-to-site-classic-azure-portal.md#generatecerts).

## <a name="vpn-client-error-a-certificate-chain-processed-but-terminated"></a>Błąd klienta sieci VPN: łańcuch certyfikatów przetworzony, ale zakończony 

### <a name="symptom"></a>Objaw 

Podczas próby nawiązania połączenia z siecią wirtualną platformy Azure przy użyciu klienta sieci VPN zostanie wyświetlony następujący komunikat o błędzie:

**Łańcuch certyfikatów przetworzony, ale zakończony w certyfikacie głównym, który nie jest zaufany przez dostawcę zaufania.**

### <a name="solution"></a>Rozwiązanie

1. Upewnij się, że następujące certyfikaty znajdują się we właściwym miejscu:

    | Certyfikat | Lokalizacja |
    | ------------- | ------------- |
    | AzureClient.pfx  | Bieżący użytkownik\Osobiste\Certyfikaty |
    | Azuregateway-*GUID*.cloudapp.net  | Bieżący użytkownik\Zaufane główne urzędy certyfikacji|
    | AzureGateway-*GUID*.cloudapp.net, AzureRoot.cer    | Komputer lokalny\Zaufane główne urzędy certyfikacji|

2. Jeśli certyfikaty znajdują się już w lokalizacji, spróbuj usunąć certyfikaty i ponownie je zainstalować. Certyfikat **azuregateway-*GUID*.cloudapp.net** znajduje się w pakiecie konfiguracji klienta sieci VPN pobranym z witryny Azure portal. Można użyć archiwizatorów plików, aby wyodrębnić pliki z pakietu.

## <a name="file-download-error-target-uri-is-not-specified"></a>Błąd pobierania pliku: nie określono docelowego identyfikatora URI

### <a name="symptom"></a>Objaw

Zostanie wyświetlony następujący komunikat o błędzie:

**Błąd pobierania pliku. Docelowy identyfikator URI nie jest określony.**

### <a name="cause"></a>Przyczyna 

Ten problem występuje z powodu niepoprawnego typu bramy. 

### <a name="solution"></a>Rozwiązanie

Typ bramy sieci VPN musi być **sieci VPN,** a typ sieci VPN musi być **routebased**.

## <a name="vpn-client-error-azure-vpn-custom-script-failed"></a>Błąd klienta sieci VPN: niestandardowy skrypt usługi Azure VPN nie powiódł się 

### <a name="symptom"></a>Objaw

Podczas próby nawiązania połączenia z siecią wirtualną platformy Azure przy użyciu klienta sieci VPN zostanie wyświetlony następujący komunikat o błędzie:

**Skrypt niestandardowy (do aktualizacji tabeli routingu) nie powiódł się. (Błąd 8007026f)**

### <a name="cause"></a>Przyczyna

Ten problem może wystąpić, jeśli próbujesz otworzyć połączenie sieci VPN lokacja-punkt za pomocą skrótu.

### <a name="solution"></a>Rozwiązanie 

Otwórz pakiet VPN bezpośrednio zamiast otwierać go ze skrótu.

## <a name="cannot-install-the-vpn-client"></a>Nie można zainstalować klienta sieci VPN

### <a name="cause"></a>Przyczyna 

Dodatkowy certyfikat jest wymagany do ufania bramie sieci VPN dla sieci wirtualnej. Certyfikat znajduje się w pakiecie konfiguracji klienta sieci VPN, który jest generowany z witryny Azure portal.

### <a name="solution"></a>Rozwiązanie

Wyodrębnij pakiet konfiguracji klienta sieci VPN i znajdź plik cer. Aby zainstalować certyfikat, wykonaj następujące czynności:

1. Otwórz program mmc.exe.
2. Dodaj przystawkę **Certyfikaty.**
3. Wybierz konto **Komputer** dla komputera lokalnego.
4. Kliknij prawym przyciskiem myszy węzeł **Zaufane główne urzędy certyfikacji.** Kliknij pozycję**Import** **wszystkich zadań** > i przejdź do pliku cer wyodrębniony z pakietu konfiguracyjnego klienta sieci VPN.
5. Uruchom ponownie komputer. 
6. Spróbuj zainstalować klienta sieci VPN.

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-data-is-invalid"></a>Błąd portalu Azure: nie można zapisać bramy sieci VPN, a dane są nieprawidłowe

### <a name="symptom"></a>Objaw

Podczas próby zapisania zmian dla bramy sieci VPN w witrynie Azure Portal zostanie wyświetlony następujący komunikat o błędzie:

**Nie można zapisać &lt; *nazwy*&gt;bramy bramy sieci wirtualnej . Dane dotyczące &lt; *identyfikatora* &gt; certyfikatu są nieprawidłowe.**

### <a name="cause"></a>Przyczyna 

Ten problem może wystąpić, jeśli przekazany klucz publiczny certyfikatu głównego zawiera nieprawidłowy znak, taki jak spacja.

### <a name="solution"></a>Rozwiązanie

Upewnij się, że dane w certyfikacie nie zawierają nieprawidłowych znaków, takich jak podziały wierszy (zwroty karetki). Cała wartość powinna być jedną długą linią. Poniższy tekst jest próbką certyfikatu:

    -----BEGIN CERTIFICATE-----
    MIIC5zCCAc+gAwIBAgIQFSwsLuUrCIdHwI3hzJbdBjANBgkqhkiG9w0BAQsFADAW
    MRQwEgYDVQQDDAtQMlNSb290Q2VydDAeFw0xNzA2MTUwMjU4NDZaFw0xODA2MTUw
    MzE4NDZaMBYxFDASBgNVBAMMC1AyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEF
    AAOCAQ8AMIIBCgKCAQEAz8QUCWCxxxTrxF5yc5uUpL/bzwC5zZ804ltB1NpPa/PI
    sa5uwLw/YFb8XG/JCWxUJpUzS/kHUKFluqkY80U+fAmRmTEMq5wcaMhp3wRfeq+1
    G9OPBNTyqpnHe+i54QAnj1DjsHXXNL4AL1N8/TSzYTm7dkiq+EAIyRRMrZlYwije
    407ChxIp0stB84MtMShhyoSm2hgl+3zfwuaGXoJQwWiXh715kMHVTSj9zFechYd7
    5OLltoRRDyyxsf0qweTFKIgFj13Hn/bq/UJG3AcyQNvlCv1HwQnXO+hckVBB29wE
    sF8QSYk2MMGimPDYYt4ZM5tmYLxxxvGmrGhc+HWXzMeQIDAQABozEwLzAOBgNVHQ8B
    Af8EBAMCAgQwHQYDVR0OBBYEFBE9zZWhQftVLBQNATC/LHLvMb0OMA0GCSqGSIb3
    DQEBCwUAA4IBAQB7k0ySFUQu72sfj3BdNxrXSyOT4L2rADLhxxxiK0U6gHUF6eWz
    /0h6y4mNkg3NgLT3j/WclqzHXZruhWAXSF+VbAGkwcKA99xGWOcUJ+vKVYL/kDja
    gaZrxHlhTYVVmwn4F7DWhteFqhzZ89/W9Mv6p180AimF96qDU8Ez8t860HQaFkU6
    2Nw9ZMsGkvLePZZi78yVBDCWMogBMhrRVXG/xQkBajgvL5syLwFBo2kWGdC+wyWY
    U/Z+EK9UuHnn3Hkq/vXEzRVsYuaxchta0X2UNRzRq+o706l+iyLTpe6fnvW6ilOi
    e8Jcej7mzunzyjz4chN0/WVF94MtxbUkLkqP
    -----END CERTIFICATE-----

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-resource-name-is-invalid"></a>Błąd portalu Azure: nie można zapisać bramy sieci VPN, a nazwa zasobu jest nieprawidłowa

### <a name="symptom"></a>Objaw

Podczas próby zapisania zmian dla bramy sieci VPN w witrynie Azure Portal zostanie wyświetlony następujący komunikat o błędzie: 

**Nie można zapisać &lt; *nazwy*&gt;bramy bramy sieci wirtualnej . Nazwa &lt; *certyfikatu nazwy zasobu, którą próbujesz przekazać,* &gt; jest nieprawidłowa**.

### <a name="cause"></a>Przyczyna

Ten problem występuje, ponieważ nazwa certyfikatu zawiera nieprawidłowy znak, taki jak spacja. 

## <a name="azure-portal-error-vpn-package-file-download-error-503"></a>Błąd portalu Azure: błąd pobierania pliku pakietu SIECI VPN 503

### <a name="symptom"></a>Objaw

Podczas próby pobrania pakietu konfiguracji klienta sieci VPN pojawia się następujący komunikat o błędzie:

**Nie można pobrać pliku. Szczegóły błędu: błąd 503. Serwer jest zajęty.**
 
### <a name="solution"></a>Rozwiązanie

Ten błąd może być spowodowany tymczasowym problemem z siecią. Spróbuj ponownie pobrać pakiet VPN po kilku minutach.

## <a name="azure-vpn-gateway-upgrade-all-point-to-site-clients-are-unable-to-connect"></a>Uaktualnienie bramy sieci VPN platformy Azure: nie można połączyć klientów typu "punkt— lokacja".

### <a name="cause"></a>Przyczyna

Jeśli certyfikat jest więcej niż 50 procent przez jego okres istnienia, certyfikat jest przerzucony.

### <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, ponownie pobierz i ponownie wdrożyć pakiet wskaż do witryny na wszystkich klientach.

## <a name="too-many-vpn-clients-connected-at-once"></a>Zbyt wielu klientów VPN połączonych jednocześnie

Osiągnięto maksymalną liczbę dozwolonych połączeń. Możesz zobaczyć całkowitą liczbę połączonych klientów w witrynie Azure portal.

## <a name="vpn-client-cannot-access-network-file-shares"></a>Klient sieci VPN nie może uzyskać dostępu do udziałów plików sieciowych

### <a name="symptom"></a>Objaw

Klient sieci VPN ma połączenie z siecią wirtualną platformy Azure. Jednak klient nie może uzyskać dostępu do udziałów sieciowych.

### <a name="cause"></a>Przyczyna

Protokół SMB jest używany do dostępu do udziału plików. Po zainicjowaniu połączenia klient sieci VPN dodaje poświadczenia sesji i wystąpi błąd. Po nawiązaniu połączenia klient jest zmuszony do używania poświadczeń pamięci podręcznej do uwierzytelniania Kerberos. Ten proces inicjuje zapytania do Centrum dystrybucji kluczy (kontrolera domeny), aby uzyskać token. Ponieważ klient łączy się z Internetem, może nie być w stanie dotrzeć do kontrolera domeny. W związku z tym klient nie może przejść awaryjnie z protokołu Kerberos do NTLM. 

Jedynym monitem klienta o podanie poświadczeń jest moment, w którym ma prawidłowy certyfikat (z siecią SAN=UPN) wystawiony przez domenę, do której jest przyłączony. Klient musi być również fizycznie połączony z siecią domeny. W takim przypadku klient próbuje użyć certyfikatu i dociera do kontrolera domeny. Następnie Centrum dystrybucji kluczy zwraca błąd "KDC_ERR_C_PRINCIPAL_UNKNOWN". Klient jest zmuszony do pracy awaryjnej do NTLM. 

### <a name="solution"></a>Rozwiązanie

Aby obejść ten problem, należy wyłączyć buforowanie poświadczeń domeny z następującego podklucza rejestru: 

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\DisableDomainCreds - Set the value to 1 


## <a name="cannot-find-the-point-to-site-vpn-connection-in-windows-after-reinstalling-the-vpn-client"></a>Nie można odnaleźć połączenia sieci VPN typu punkt-lokacja w systemie Windows po ponownej instalacji klienta sieci VPN

### <a name="symptom"></a>Objaw

Możesz usunąć połączenie sieci VPN typu punkt-lokacja, a następnie ponownie zainstalować klienta sieci VPN. W tej sytuacji połączenie sieci VPN nie jest pomyślnie skonfigurowane. Połączenie sieci VPN nie jest widoczne w **ustawieniach połączeń sieciowych** w systemie Windows.

### <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, usuń stare pliki konfiguracyjne klienta sieci VPN z **języka C:\Users\UserName\AppData\Roaming\Microsoft\Network\Connections\<VirtualNetworkId>**, a następnie ponownie uruchom instalator klienta sieci VPN.

## <a name="point-to-site-vpn-client-cannot-resolve-the-fqdn-of-the-resources-in-the-local-domain"></a>Klient sieci VPN typu point-to-site nie może rozpoznać nazwy FQDN zasobów w domenie lokalnej

### <a name="symptom"></a>Objaw

Gdy klient łączy się z platformą Azure przy użyciu połączenia sieci VPN typu lokacja, nie może rozpoznać nazwy FQDN zasobów w domenie lokalnej.

### <a name="cause"></a>Przyczyna

Klient sieci VPN typu punkt-lokacja używa serwerów DNS platformy Azure skonfigurowanych w sieci wirtualnej platformy Azure. Serwery DNS platformy Azure mają pierwszeństwo przed lokalnymi serwerami DNS skonfigurowanymi w kliencie, więc wszystkie kwerendy DNS są wysyłane do serwerów DNS platformy Azure. Jeśli serwery DNS platformy Azure nie mają rekordów dla zasobów lokalnych, kwerenda kończy się niepowodzeniem.

### <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, upewnij się, że serwery DNS platformy Azure używane w sieci wirtualnej platformy Azure mogą rozpoznawać rekordy DNS dla zasobów lokalnych. Aby to zrobić, można użyć usługi przesyłania dalej DNS lub warunkowego przesyłania dalej. Aby uzyskać więcej informacji, zobacz [Rozpoznawanie nazw przy użyciu własnego serwera DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)

## <a name="the-point-to-site-vpn-connection-is-established-but-you-still-cannot-connect-to-azure-resources"></a>Nawiązane jest połączenie sieci VPN typu punkt-lokacja, ale nadal nie można połączyć się z zasobami platformy Azure 

### <a name="cause"></a>Przyczyna

Ten problem może wystąpić, jeśli klient sieci VPN nie otrzymuje tras z bramy sieci VPN platformy Azure.

### <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, [zresetuj bramę sieci VPN platformy Azure](vpn-gateway-resetgw-classic.md). Aby upewnić się, że nowe trasy są używane, klienci sieci VPN typu punkt-lokacja muszą zostać pobrani ponownie po pomyślnym skonfigurowaniu komunikacji równorzędnej w sieci wirtualnej.

## <a name="error-the-revocation-function-was-unable-to-check-revocation-because-the-revocation-server-was-offlineerror-0x80092013"></a>Błąd: "Funkcja odwołania nie może sprawdzić odwołania, ponieważ serwer odwołania był w trybie offline. (Błąd 0x80092013)"

### <a name="causes"></a>Przyczyny
Ten komunikat o błędzie występuje, jeśli klient nie może uzyskać dostępu http://crl3.digicert.com/ssca-sha2-g1.crl i http://crl4.digicert.com/ssca-sha2-g1.crl.  Sprawdzanie odwołania wymaga dostępu do tych dwóch witryn.  Ten problem zazwyczaj występuje na kliencie, który ma skonfigurowany serwer proxy. W niektórych środowiskach, jeśli żądania nie przechodzą przez serwer proxy, zostanie ono odrzucone w zapory brzegowej.

### <a name="solution"></a>Rozwiązanie

Sprawdź ustawienia serwera proxy, upewnij się, http://crl3.digicert.com/ssca-sha2-g1.crl http://crl4.digicert.com/ssca-sha2-g1.crlże klient ma dostęp i .

## <a name="vpn-client-error-the-connection-was-prevented-because-of-a-policy-configured-on-your-rasvpn-server-error-812"></a>Błąd klienta sieci VPN: Połączenie zostało uniemożliwione z powodu zasad skonfigurowanych na serwerze RAS/VPN. (Błąd 812)

### <a name="cause"></a>Przyczyna

Ten błąd występuje, jeśli serwer RADIUS używany do uwierzytelniania klienta sieci VPN ma niepoprawne ustawienia lub usługa Azure Gateway nie może dotrzeć do serwera Radius.

### <a name="solution"></a>Rozwiązanie

Upewnij się, że serwer RADIUS jest poprawnie skonfigurowany. Aby uzyskać więcej informacji, zobacz [Integrowanie uwierzytelniania RADIUS z serwerem uwierzytelniania wieloskładnikowego platformy Azure](../active-directory/authentication/howto-mfaserver-dir-radius.md).

## <a name="error-405-when-you-download-root-certificate-from-vpn-gateway"></a>"Błąd 405" podczas pobierania certyfikatu głównego z bramy sieci VPN

### <a name="cause"></a>Przyczyna

Certyfikat główny nie został zainstalowany. Certyfikat główny jest zainstalowany w magazynie **zaufanych certyfikatów** klienta.

## <a name="vpn-client-error-the-remote-connection-was-not-made-because-the-attempted-vpn-tunnels-failed-error-800"></a>Błąd klienta sieci VPN: Połączenie zdalne nie zostało nawiązane, ponieważ próba tuneli sieci VPN nie powiodła się. (Błąd 800) 

### <a name="cause"></a>Przyczyna

Sterownik karty sieciowej jest przestarzały.

### <a name="solution"></a>Rozwiązanie

Zaktualizuj sterownik karty sieciowej:

1. Kliknij **przycisk Start**, wpisz Menedżer **urządzeń**i wybierz go z listy wyników. Jeśli zostanie wyświetlony monit o podanie hasła administratora lub potwierdzenia, wpisz hasło lub podaj potwierdzenie.
2. W **kategoriach Karty sieciowe** znajdź kartę sieciową, którą chcesz zaktualizować.  
3. Kliknij dwukrotnie nazwę urządzenia, wybierz **pozycję Aktualizuj sterownik**, wybierz pozycję **Wyszukaj automatycznie zaktualizowane oprogramowanie sterownika**.
4.  Jeśli system Windows nie znajduje nowego sterownika, możesz spróbować znaleźć go w witrynie internetowej producenta urządzenia i wykonać podane w niej instrukcje.
5. Uruchom ponownie komputer i spróbuj ponownie nawiązać połączenie.

## <a name="vpn-client-error-dialing-vpn-connection-vpn-connection-name-status--vpn-platform-did-not-trigger-connection"></a>Błąd klienta sieci VPN: <VPN Connection Name>wybieranie połączenia VPN , Status = Platforma VPN nie wyzwoliła połączenia

W Podglądzie zdarzeń z RasClient może zostać wyświetlony <User> następujący błąd: <VPN Connection Name> "Użytkownik wybrał połączenie o nazwie, które nie powiodło się. Kod błędu zwrócony w przypadku awarii wynosi 1460."

### <a name="cause"></a>Przyczyna

Klient sieci VPN platformy Azure nie ma włączonego uprawnienia aplikacji "Aplikacje w tle" w ustawieniach aplikacji dla systemu Windows.

### <a name="solution"></a>Rozwiązanie

1. W systemie Windows przejdź do aplikacji Ustawienia -> Privacy -> Background
2. Przełączanie "Pozwól aplikacjom działać w tle" na Włączone

## <a name="error-file-download-error-target-uri-is-not-specified"></a>Błąd: "Nie określono błędu pobierania plików Docelowy identyfikator URI"

### <a name="cause"></a>Przyczyna

Jest to spowodowane przez niepoprawny typ bramy jest skonfigurowany.

### <a name="solution"></a>Rozwiązanie

Typ bramy sieci VPN platformy Azure musi być siecią VPN, a typ sieci VPN musi być **routebased**.

## <a name="vpn-package-installer-doesnt-complete"></a>Instalator pakietów VPN nie jest kompletny

### <a name="cause"></a>Przyczyna

Ten problem może być spowodowany przez poprzednie instalacje klienta sieci VPN. 

### <a name="solution"></a>Rozwiązanie

Usuń stare pliki konfiguracyjne klienta sieci VPN z **języka C:\Users\UserName\AppData\Roaming\Microsoft\Network\Connections\<VirtualNetworkId>** i ponownie uruchom instalator klienta sieci VPN. 

## <a name="the-vpn-client-hibernates-or-sleep-after-some-time"></a>Klient sieci VPN hibernuje lub śpi po pewnym czasie

### <a name="solution"></a>Rozwiązanie

Sprawdź ustawienia uśpienia i hibernacji na komputerze, na których działa klient sieci VPN.
