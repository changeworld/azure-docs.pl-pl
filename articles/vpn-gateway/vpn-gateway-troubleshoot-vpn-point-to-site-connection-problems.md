---
title: Rozwiązywanie problemów z połączeniem usługi Azure point-to-site | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy z połączeniem punkt lokacja.
services: vpn-gateway
documentationcenter: na
author: chadmath
manager: cshepard
editor: ''
tags: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/11/2018
ms.author: genli
ms.openlocfilehash: 174bc4895bbad4546392581c2c769aac762d6106
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2019
ms.locfileid: "59492383"
---
# <a name="troubleshooting-azure-point-to-site-connection-problems"></a>Rozwiązywanie problemów: Problemy z połączeniem usługi Azure point-to-site

W tym artykule wymieniono typowe problemy z połączeniem punkt lokacja, które mogą wystąpić. Omówiono w nim również możliwe przyczyny i potencjalne rozwiązania tych problemów.

## <a name="vpn-client-error-a-certificate-could-not-be-found"></a>Błąd klienta sieci VPN: Nie można odnaleźć certyfikatu

### <a name="symptom"></a>Objaw

Podczas próby nawiązania połączenia z siecią wirtualną platformy Azure, korzystając z klienta sieci VPN, pojawi się następujący komunikat o błędzie:

**Nie można odnaleźć certyfikatu, który może być używana z protokołem uwierzytelniania rozszerzonego. (Błąd 798)**

### <a name="cause"></a>Przyczyna

Ten problem występuje, jeśli brakuje certyfikatu klienta **Certyfikaty - bieżący użytkownik\osobisty\certyfikat**.

### <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, wykonaj następujące kroki:

1. Otwórz Menedżera certyfikatów: Kliknij przycisk **Start**, typ **zarządzania certyfikatami komputera**, a następnie kliknij przycisk **zarządzania certyfikatami komputera** w wynikach wyszukiwania.

2. Upewnij się, że następujące certyfikaty znajdują się w poprawnej lokalizacji:

    | Certyfikat | Lokalizacja |
    | ------------- | ------------- |
    | AzureClient.pfx  | Bieżący użytkownik\osobisty\certyfikat |
    | Azuregateway-*GUID*.cloudapp.net  | Bieżący User\Trusted główne urzędy certyfikacji|
    | AzureGateway-*GUID*.cloudapp.net, AzureRoot.cer    | Lokalny komputer lokalny\Zaufane główne urzędy certyfikacji|

3. Przejdź do C:\Users\<UserName > \AppData\Roaming\Microsoft\Network\Connections\Cm\<GUID >, ręcznie zainstaluj certyfikat (plik *.cer) na użytkownika i magazynie komputera.

Aby uzyskać więcej informacji na temat sposobu instalowania certyfikatu klienta, zobacz [Generowanie i eksportowanie certyfikatów dla połączeń punkt lokacja](vpn-gateway-certificates-point-to-site.md).

> [!NOTE]
> Po zaimportowaniu certyfikatu klienta, nie należy wybierać **włączanie silnej ochrony klucza prywatnego** opcji.

## <a name="the-network-connection-between-your-computer-and-the-vpn-server-could-not-be-established-because-the-remote-server-is-not-responding"></a>Nie można nawiązać połączenie sieciowe między komputerem a serwerem sieci VPN, ponieważ serwer zdalny nie odpowiada.

### <a name="symptom"></a>Objaw

Kiedy sprawdzasz i nawiązać połączenie z gteway sieci wirtualnej platformy Azure za pomocą protokołu IKEv2 w Windows, otrzymasz następujący komunikat o błędzie:

**Nie można nawiązać połączenie sieciowe między komputerem a serwerem sieci VPN, ponieważ serwer zdalny nie odpowiada.**

### <a name="cause"></a>Przyczyna
 
 Ten problem występuje, jeśli wersja systemu Windows nie ma obsługę protokołu IKE fragmentacji
 
### <a name="solution"></a>Rozwiązanie

Protokół IKEv2 jest obsługiwany w systemach Windows 10 i Server 2016. Jednak aby można było używać protokołu IKEv2, należy zainstalować aktualizacje i lokalnie ustawić wartość klucza rejestru. Wersje systemu operacyjnego starsze niż Windows 10 nie są obsługiwane i mogą używać tylko protokołu SSTP.

Aby przygotowywać system Windows 10 lub Server 2016 pod kątem protokołu IKEv2:

1. Zainstaluj aktualizację.

   | Wersja systemu operacyjnego | Date | Numer/link |
   |---|---|---|---|
   | Windows Server 2016<br>Windows 10 w wersji 1607 | 17 stycznia 2018 r. | [KB4057142](https://support.microsoft.com/help/4057142/windows-10-update-kb4057142) |
   | Windows 10 w wersji 1703 | 17 stycznia 2018 r. | [KB4057144](https://support.microsoft.com/help/4057144/windows-10-update-kb4057144) |
   | System Windows 10 w wersji 1709 | 22 marca 2018 r. | [KB4089848](https://www.catalog.update.microsoft.com/search.aspx?q=kb4089848) |
   |  |  |  |  |

2. Ustaw wartość klucza rejestru. Utwórz lub ustaw klucz rejestru REG_DWORD „HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\RasMan\ IKEv2\DisableCertReqPayload”na wartość 1.

## <a name="vpn-client-error-the-message-received-was-unexpected-or-badly-formatted"></a>Błąd klienta sieci VPN: Odebrano komunikat jest nieoczekiwany lub nieprawidłowo sformatowany

### <a name="symptom"></a>Objaw

Podczas próby nawiązania połączenia z siecią wirtualną platformy Azure, korzystając z klienta sieci VPN, pojawi się następujący komunikat o błędzie:

**Odebrano komunikat jest nieoczekiwany lub niepoprawnie sformatowana. (Błąd 0x80090326)**

### <a name="cause"></a>Przyczyna

Ten problem występuje, gdy jest spełniony jeden z następujących warunków:

- Użyj trasy zdefiniowane przez użytkownika (UDR) przy użyciu trasy domyślnej w ramach bramy podsieci jest niepoprawna.
- Klucz publiczny certyfikatu głównego nie jest przekazywany do bramy sieci VPN platformy Azure. 
- Klucz jest uszkodzony lub wygasła.

### <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, wykonaj następujące kroki:

1. Usuń trasę zdefiniowaną przez użytkownika w podsieci bramy. Upewnij się, że trasa zdefiniowana przez użytkownika przekazuje poprawnie cały ruch.
2. Sprawdź stan certyfikatu głównego w witrynie Azure portal, aby zobaczyć, czy został odwołany. Jeśli nie został odwołany, spróbuj usunąć certyfikat główny i reupload. Aby uzyskać więcej informacji, zobacz [Tworzenie certyfikatów](vpn-gateway-howto-point-to-site-classic-azure-portal.md#generatecerts).

## <a name="vpn-client-error-a-certificate-chain-processed-but-terminated"></a>Błąd klienta sieci VPN: Łańcuch certyfikatów przetwarzane, ale została przerwana 

### <a name="symptom"></a>Objaw 

Podczas próby nawiązania połączenia z siecią wirtualną platformy Azure, korzystając z klienta sieci VPN, pojawi się następujący komunikat o błędzie:

**Przetwarzanie, ale łańcuch certyfikatów przerwana w certyfikacie głównym, który nie jest zaufany przez dostawcę zaufania.**

### <a name="solution"></a>Rozwiązanie

1. Upewnij się, że następujące certyfikaty znajdują się w poprawnej lokalizacji:

    | Certyfikat | Lokalizacja |
    | ------------- | ------------- |
    | AzureClient.pfx  | Bieżący użytkownik\osobisty\certyfikat |
    | Azuregateway-*GUID*.cloudapp.net  | Bieżący User\Trusted główne urzędy certyfikacji|
    | AzureGateway-*GUID*.cloudapp.net, AzureRoot.cer    | Lokalny komputer lokalny\Zaufane główne urzędy certyfikacji|

2. Jeśli certyfikaty znajdują się już w lokalizacji, spróbuj usunąć certyfikaty i zainstaluj je ponownie. **Azuregateway -*GUID*. cloudapp.net** certyfikat znajduje się w pakietu konfiguracyjnego klienta VPN, który został pobrany z witryny Azure portal. Archivers pliku można użyć, aby wyodrębnić pliki z pakietu.

## <a name="file-download-error-target-uri-is-not-specified"></a>Błąd pobierania pliku: Docelowy identyfikator URI nie jest określony.

### <a name="symptom"></a>Objaw

Pojawi się następujący komunikat o błędzie:

**Błąd pobierania pliku. Nie określono docelowego identyfikatora URI.**

### <a name="cause"></a>Przyczyna 

Ten problem występuje ze względu na typ bramy niepoprawne. 

### <a name="solution"></a>Rozwiązanie

Typ bramy sieci VPN musi być **VPN**, a typ sieci VPN musi być **RouteBased**.

## <a name="vpn-client-error-azure-vpn-custom-script-failed"></a>Błąd klienta sieci VPN: Sieć VPN niestandardowego skryptu platformy Azure nie powiodło się 

### <a name="symptom"></a>Objaw

Podczas próby nawiązania połączenia z siecią wirtualną platformy Azure, korzystając z klienta sieci VPN, pojawi się następujący komunikat o błędzie:

**Skrypt niestandardowy (można zaktualizować tabeli routingu) nie powiodło się. (Błąd 8007026f)**

### <a name="cause"></a>Przyczyna

Ten problem może wystąpić, jeśli próbujesz otworzyć lokacji – punkt połączenia z siecią VPN przy użyciu skrótu.

### <a name="solution"></a>Rozwiązanie 

Otwórz pakiet sieci VPN bezpośrednio, zamiast otwierając go za pomocą skrótu.

## <a name="cannot-install-the-vpn-client"></a>Nie można zainstalować klienta sieci VPN

### <a name="cause"></a>Przyczyna 

Dodatkowy certyfikat jest wymagany do zaufania bramy sieci VPN dla sieci wirtualnej. Certyfikat znajduje się w pakietu konfiguracyjnego klienta VPN, który jest generowany w witrynie Azure portal.

### <a name="solution"></a>Rozwiązanie

Wyodrębnij pakiet konfiguracyjny klienta VPN, a następnie znajdź plik cer. Aby zainstalować certyfikat, wykonaj następujące kroki:

1. Otwórz mmc.exe.
2. Dodaj **certyfikaty** przystawki.
3. Wybierz **komputera** konta na komputerze lokalnym.
4. Kliknij prawym przyciskiem myszy **zaufane główne urzędy certyfikacji** węzła. Kliknij przycisk **wszystkie zadania** > **importu**, a następnie przejdź do pliku cer wyodrębnione z pakietu konfiguracyjnego klienta VPN.
5. Uruchom ponownie komputer. 
6. Spróbuj zainstalować klienta sieci VPN.

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-data-is-invalid"></a>Azure portal błąd: Nie można zapisać bramy sieci VPN, a dane są nieprawidłowe

### <a name="symptom"></a>Objaw

Podczas próby zapisania zmiany dla bramy sieci VPN w witrynie Azure portal, pojawi się następujący komunikat o błędzie:

**Nie można zapisać bramy sieci wirtualnej &lt; *nazwę bramy*&gt;. Dane dotyczące certyfikatu &lt; *certyfikatu identyfikator* &gt; jest nieprawidłowy.**

### <a name="cause"></a>Przyczyna 

Ten problem może wystąpić, jeśli klucz publiczny certyfikatu głównego, który został przekazany zawiera nieprawidłowe znaki, takie jak spacja.

### <a name="solution"></a>Rozwiązanie

Upewnij się, że dane w certyfikacie nie zawiera nieprawidłowe znaki, takie jak podziały wiersza (znaki powrotu karetki). Całą wartość powinna być jeden długi wiersz. Poniższy tekst znajduje się przykład certyfikatu:

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

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-resource-name-is-invalid"></a>Azure portal błąd: Nie można zapisać bramy sieci VPN, a nazwa zasobu jest nieprawidłowa

### <a name="symptom"></a>Objaw

Podczas próby zapisania zmiany dla bramy sieci VPN w witrynie Azure portal, pojawi się następujący komunikat o błędzie: 

**Nie można zapisać bramy sieci wirtualnej &lt; *nazwę bramy*&gt;. Nazwa zasobu &lt; *nazwy certyfikatu, spróbuj przekazać* &gt; jest nieprawidłowa**.

### <a name="cause"></a>Przyczyna

Ten problem występuje, ponieważ nazwa certyfikatu zawiera nieprawidłowe znaki, takie jak spacja. 

## <a name="azure-portal-error-vpn-package-file-download-error-503"></a>Azure portal błąd: Błąd pobierania pliku pakietu sieci VPN 503

### <a name="symptom"></a>Objaw

Podczas próby pobrania pakietu konfiguracyjnego klienta VPN, pojawi się następujący komunikat o błędzie:

**Nie można pobrać pliku. Szczegóły błędu: błąd 503. Serwer jest zajęty.**
 
### <a name="solution"></a>Rozwiązanie

Ten błąd może być spowodowany przez tymczasowy problem z siecią. Spróbuj ponownie za kilka minut. Pobieranie pakietu sieci VPN.

## <a name="azure-vpn-gateway-upgrade-all-point-to-site-clients-are-unable-to-connect"></a>Azure uaktualniania bramy sieci VPN: Nie można nawiązać połączenia są wskazujących na klientach w lokacji

### <a name="cause"></a>Przyczyna

Jeśli certyfikat jest więcej niż 50% za pośrednictwem jego okres istnienia certyfikatu jest przenoszone.

### <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, Pobierz ponownie i Wdróż ponownie punkt do lokacji pakietu na wszystkich klientach.

## <a name="too-many-vpn-clients-connected-at-once"></a>Zbyt wielu klientów sieci VPN połączony na raz

Osiągnięto maksymalną liczbę dozwolonych połączeń. Całkowita liczba podłączonych klientów w witrynie Azure portal są widoczne.

## <a name="point-to-site-vpn-incorrectly-adds-a-route-for-100008-to-the-route-table"></a>Sieci VPN typu punkt lokacja niepoprawnie dodaje trasę dla 10.0.0.0/8 do tabeli tras

### <a name="symptom"></a>Objaw

Podczas wybierania połączenia sieci VPN na komputerze klienckim point-to-site klienta sieci VPN należy dodać trasę do sieci wirtualnej platformy Azure. Usługa Pomocnika IP należy dodać trasy podsieci dla klientów sieci VPN. 

Zakres klienta sieci VPN należy do mniejszych podsieci 10.0.0.0/8, takich jak 10.0.12.0/24. Zamiast trasę 10.0.12.0/24 dodawana jest trasa dla 10.0.0.0/8 o wyższym priorytecie. 

Ta trasa niepoprawne przerywa łączność z innymi sieciami lokalnymi, które mogą należeć do innej podsieci w zakresie 10.0.0.0/8, takich jak 10.50.0.0/24, które nie mają określoną trasę zdefiniowane. 

### <a name="cause"></a>Przyczyna

To zachowanie jest celowe w przypadku klientów Windows. Jeśli klient korzysta z protokołu PPP IPCP, otrzymuje adres IP dla interfejsu tunelu z serwera (Brama sieci VPN w tym przypadku). Jednak ze względu na ograniczenia w protokole, klient nie ma maskę podsieci. Ponieważ nie ma innego sposobu jego, klient próbuje odgadnąć maski podsieci, na podstawie klasy adres IP interfejsu tunelu. 

W związku z tym w oparciu o następujące mapowania statycznego jest dodawana trasa: 

Jeśli adres należy do klasy A--> zastosować /8

Jeśli adres należy do klasy B--> zastosowania /16

Jeśli adres należy do klasy C--> zastosowania prefiksie/24

### <a name="solution"></a>Rozwiązanie

Mieć trasy dla innych sieci, które zostały wprowadzone w tabeli routingu z dopasowanie najdłuższego prefiksu lub niższe metryki (dlatego wyższy priorytet) niż punkt do lokacji. 

## <a name="vpn-client-cannot-access-network-file-shares"></a>Klient sieci VPN nie uzyskać dostępu do udziałów plików sieciowych

### <a name="symptom"></a>Objaw

Klient sieci VPN został podłączony do sieci wirtualnej platformy Azure. Jednak klient nie może uzyskać dostęp do udziałów sieciowych.

### <a name="cause"></a>Przyczyna

Protokół SMB jest używany dla dostępu do udziału plików. Po zainicjowaniu połączenia klienta sieci VPN dodaje poświadczenia sesji i występuje błąd. Po nawiązaniu połączenia klienta jest zmuszony do pamięci podręcznej poświadczeń na użytek uwierzytelniania Kerberos. Ten proces powoduje zainicjowanie zapytania, aby Centrum dystrybucji kluczy (kontroler domeny) w celu pobrania tokenu. Ponieważ klient nawiąże połączenie z Internetem, może nie być mogli skontaktować się z kontrolerem domeny. W związku z tym klient nie może przełączyć się z protokołu Kerberos do uwierzytelniania NTLM. 

Jedyną sytuacją, który klient jest monitowany o poświadczenia jest, kiedy ma prawidłowy certyfikat (z siecią SAN = nazwa UPN) wydany przez domenę, do której jest dołączony. Klienta również musi być fizycznie podłączony do sieci z domeną. W takim przypadku klient próbuje użyć certyfikatu i łączy się z kontrolerem domeny. Następnie Centrum dystrybucji kluczy zwraca błąd "KDC_ERR_C_PRINCIPAL_UNKNOWN". Klient jest zmuszony do trybu failover protokołu NTLM. 

### <a name="solution"></a>Rozwiązanie

Aby obejść ten problem, należy wyłączyć buforowanie poświadczeń domeny, z następującym podkluczu rejestru: 

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\DisableDomainCreds - Set the value to 1 


## <a name="cannot-find-the-point-to-site-vpn-connection-in-windows-after-reinstalling-the-vpn-client"></a>Nie można odnaleźć połączenia sieci VPN typu punkt lokacja w Windows po ponownej instalacji klienta sieci VPN

### <a name="symptom"></a>Objaw

Usuń połączenie sieci VPN typu punkt lokacja, a następnie ponownie zainstalować klienta sieci VPN. W takiej sytuacji połączenia sieci VPN nie jest skonfigurowany pomyślnie. Nie ma połączenia sieci VPN w **połączenia sieciowe** ustawienia w Windows.

### <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, Usuń stare pliki konfiguracji klienta sieci VPN z **C:\Users\UserName\AppData\Roaming\Microsoft\Network\Connections\<VirtualNetworkId >**, a następnie ponownie uruchom Instalatora klienta sieci VPN .

## <a name="point-to-site-vpn-client-cannot-resolve-the-fqdn-of-the-resources-in-the-local-domain"></a>Klient sieci VPN typu punkt lokacja nie może rozpoznać nazwę FQDN zasobów w domenie lokalnej

### <a name="symptom"></a>Objaw

Gdy klient łączy się na platformie Azure przy użyciu połączenia sieci VPN typu punkt lokacja, nie można rozpoznać nazwę FQDN zasobów w domenie lokalnej.

### <a name="cause"></a>Przyczyna

Klient sieci VPN punkt lokacja używa serwerów usługi Azure DNS, które są skonfigurowane w sieci wirtualnej platformy Azure. Serwery usługi Azure DNS mają pierwszeństwo przed lokalnych serwerów DNS, które są skonfigurowane w kliencie, dzięki czemu wszystkie zapytania DNS są wysyłane do serwerów usługi Azure DNS. Jeśli nie ma rekordy zasobów lokalnych serwerów usługi Azure DNS, zapytanie kończy się niepowodzeniem.

### <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, upewnij się, że serwery usługi Azure DNS, które używane w sieci wirtualnej platformy Azure można rozwiązać rekordy DNS dla zasobów lokalnych. Aby to zrobić, można użyć usług przesyłania dalej DNS lub usług warunkowego przesyłania dalej. Aby uzyskać więcej informacji, zobacz [rozpoznawanie nazw przy użyciu własnego serwera DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)

## <a name="the-point-to-site-vpn-connection-is-established-but-you-still-cannot-connect-to-azure-resources"></a>Zostanie nawiązane połączenie sieci VPN typu punkt lokacja, ale nadal nie można nawiązać połączenia zasobów platformy Azure 

### <a name="cause"></a>Przyczyna

Ten problem może wystąpić, jeśli klient sieci VPN nie otrzymują trasy z bramą Azure VPN gateway.

### <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, [Resetowanie bramy Azure VPN gateway](vpn-gateway-resetgw-classic.md). Aby upewnić się, że są używane nowe trasy, klienci sieci VPN typu punkt-lokacja musi zostać ponownie pobrana po komunikacji równorzędnej sieci wirtualnych zostało pomyślnie skonfigurowane.

## <a name="error-the-revocation-function-was-unable-to-check-revocation-because-the-revocation-server-was-offlineerror-0x80092013"></a>Błąd: "Funkcja odwołania nie może sprawdzić odwołania, ponieważ serwer odwołań jest w trybie offline. (Błąd 0x80092013)"

### <a name="causes"></a>Przyczyny
Ten błąd występuje, jeśli klient nie może uzyskać dostępu do http://crl3.digicert.com/ssca-sha2-g1.crl i http://crl4.digicert.com/ssca-sha2-g1.crl.  Sprawdzanie odwołań wymaga dostępu do tych dwóch witryn.  Ten problem zwykle odbywa się na komputerze klienckim, który został skonfigurowany serwer proxy. W niektórych środowiskach Jeśli żądania nie są kierowane za pośrednictwem serwera proxy, jego będą odrzucane na zaporze brzegowej.

### <a name="solution"></a>Rozwiązanie

Sprawdź ustawienia serwera proxy, upewnij się, że ten klient uzyskuje dostęp http://crl3.digicert.com/ssca-sha2-g1.crl i http://crl4.digicert.com/ssca-sha2-g1.crl.

## <a name="vpn-client-error-the-connection-was-prevented-because-of-a-policy-configured-on-your-rasvpn-server-error-812"></a>Błąd klienta sieci VPN: Połączenie nie została uruchomiona, ze względu na zasady skonfigurowane na serwerze RAS/sieci VPN. (Błąd 812)

### <a name="cause"></a>Przyczyna

Ten błąd występuje, jeśli serwer usługi RADIUS, używany do uwierzytelniania klienta sieci VPN ma nieprawidłowe ustawienia lub bramy platformy Azure nie można nawiązać połączenia serwera usługi Radius.

### <a name="solution"></a>Rozwiązanie

Upewnij się, że serwer RADIUS jest skonfigurowany prawidłowo. Aby uzyskać więcej informacji, zobacz [integrowanie uwierzytelniania usługi RADIUS z serwera Azure Multi-Factor Authentication](../active-directory/authentication/howto-mfaserver-dir-radius.md).

## <a name="error-405-when-you-download-root-certificate-from-vpn-gateway"></a>"Błąd 405" po pobraniu certyfikatu głównego z bramy sieci VPN

### <a name="cause"></a>Przyczyna

Certyfikat główny nie zostały zainstalowane. Zainstalowano certyfikat główny w kliencie programu **zaufane certyfikaty** przechowywania.

## <a name="vpn-client-error-the-remote-connection-was-not-made-because-the-attempted-vpn-tunnels-failed-error-800"></a>Błąd klienta sieci VPN: Nie można nawiązać zdalnego połączenia, ponieważ nie powiodła się próba tuneli sieci VPN. (Błąd 800) 

### <a name="cause"></a>Przyczyna

Sterownik karty Sieciowej jest nieaktualny.

### <a name="solution"></a>Rozwiązanie

Aktualizacja sterownika karty Sieciowej:

1. Kliknij przycisk **Start**, typ **Menedżera urządzeń**i wybierz ją z listy wyników. Jeśli zostanie wyświetlony monit o hasło administratora lub potwierdzenie, wpisz hasło lub potwierdź.
2. W **karty sieciowe** kategorie, Znajdź kartę Sieciową, która ma zostać zaktualizowany.  
3. Kliknij dwukrotnie nazwę urządzenia, zaznacz **aktualizacji sterownika**, wybierz opcję **wyszukiwanie automatycznie oprogramowania zaktualizowanym sterowniku**.
4. Jeśli Windows nie jest dostępny nowy sterownik, możesz spróbuj poszukać jeden w witrynie sieci Web producenta urządzenia i wykonaj instrukcje.
5. Uruchom ponownie komputer i spróbuj ponownie nawiązać połączenie.

## <a name="error-file-download-error-target-uri-is-not-specified"></a>Błąd: "Błąd pobierania pliku docelowego identyfikatora URI nie zostanie określony"

### <a name="cause"></a>Przyczyna

Jest to spowodowane przez niepoprawne bramę skonfigurowano.

### <a name="solution"></a>Rozwiązanie

Typ bramy sieci VPN platformy Azure musi być sieci VPN i typ sieci VPN musi być **RouteBased**.

## <a name="vpn-package-installer-doesnt-complete"></a>Instalator pakietu sieci VPN nie wykona

### <a name="cause"></a>Przyczyna

Ten problem może być spowodowane przez poprzednie instalacje klienta sieci VPN. 

### <a name="solution"></a>Rozwiązanie

Usuń stare pliki konfiguracji klienta sieci VPN z **C:\Users\UserName\AppData\Roaming\Microsoft\Network\Connections\<VirtualNetworkId >** i ponownie uruchom Instalatora klienta sieci VPN. 

## <a name="the-vpn-client-hibernates-or-sleep-after-some-time"></a>Klient sieci VPN w stan hibernacji lub uśpienia po pewnym czasie

### <a name="solution"></a>Rozwiązanie

Sprawdź obsługę trybu uśpienia i hibernacji ustawień w komputerze, na którym działa klient sieci VPN na.
