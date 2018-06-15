---
title: Rozwiązywanie problemów z połączeniem punkt lokacja Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązać problemy z połączeniem punkt lokacja.
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
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 0db2291b53c4fe7d2d0894a4c266ed60f78219de
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
ms.locfileid: "34072236"
---
# <a name="troubleshooting-azure-point-to-site-connection-problems"></a>Rozwiązywanie problemów: Problemów Azure połączenie punkt lokacja

W tym artykule wymieniono typowe problemy połączenie punkt lokacja, które mogą wystąpić. Omówiono w nim również możliwe przyczyny i potencjalne rozwiązania tych problemów.

## <a name="vpn-client-error-a-certificate-could-not-be-found"></a>Błąd klienta sieci VPN: nie można odnaleźć certyfikatu

### <a name="symptom"></a>Objaw

Podczas próby nawiązania sieci wirtualnej platformy Azure przy użyciu klienta sieci VPN, pojawi się następujący komunikat o błędzie:

**Nie można odnaleźć certyfikatu, który może zostać użyty z protokołem uwierzytelniania rozszerzonego. (Błąd 798)**

### <a name="cause"></a>Przyczyna

Ten problem występuje, jeśli brakuje certyfikatu klienta **Certyfikaty — bieżący User\Personal\Certificates**.

### <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, wykonaj następujące kroki:

1. Kliknij przycisk Otwórz Menedżera certyfikatów: **Start**, typu **zarządzanie certyfikatami komputera**, a następnie kliknij przycisk **zarządzanie certyfikatami komputera** w wynikach wyszukiwania.

2. Upewnij się, że następujące certyfikaty są w poprawnej lokalizacji:

    | Certyfikat | Lokalizacja |
    | ------------- | ------------- |
    | AzureClient.pfx  | Bieżący User\Personal\Certificates |
    | Azuregateway-*GUID*.cloudapp.net  | Bieżący User\Trusted główne urzędy certyfikacji|
    | AzureGateway-*GUID*.cloudapp.net, AzureRoot.cer    | Lokalny komputer lokalny\Zaufane główne urzędy certyfikacji|

3. Przejdź do użytkowników\<nazwa użytkownika > \AppData\Roaming\Microsoft\Network\Connections\Cm\<GUID >, ręcznie zainstalować certyfikat (plik *.cer) w magazynie komputera i użytkownika.

Aby uzyskać więcej informacji o sposobie instalowania certyfikatu klienta, zobacz [Generowanie i eksportowania certyfikatów połączeń punkt lokacja](vpn-gateway-certificates-point-to-site.md).

> [!NOTE]
> Po zaimportowaniu certyfikatu klienta, nie należy wybierać **Włącz silną ochronę klucza prywatnego** opcji.

## <a name="vpn-client-error-the-message-received-was-unexpected-or-badly-formatted"></a>Błąd klienta sieci VPN: Odebrano komunikat jest nieoczekiwany lub nieprawidłowo sformatowany

### <a name="symptom"></a>Objaw

Podczas próby nawiązania sieci wirtualnej platformy Azure przy użyciu klienta sieci VPN, pojawi się następujący komunikat o błędzie:

**Odebrany komunikat jest nieoczekiwany lub nieprawidłowo sformatowany. (Błąd 0x80090326)**

### <a name="cause"></a>Przyczyna

Ten problem występuje, gdy spełniony jest jeden z następujących warunków:

- Użyj zdefiniowane przez użytkownika tras (przez) z trasy domyślnej w podsieci bramy jest niepoprawna.
- Klucz publiczny certyfikatu głównego nie jest przekazany do bramy sieci VPN platformy Azure. 
- Klucz jest uszkodzony lub wygasł.

### <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, wykonaj następujące kroki:

1. Usuń przez podsieci bramy. Upewnij się, że poprawnie przekazuje przez cały ruch.
2. Sprawdź stan certyfikatu głównego w portalu Azure, aby zobaczyć, czy został odwołany. Jeśli nie został odwołany, spróbuj usunąć certyfikatu głównego i reupload. Aby uzyskać więcej informacji, zobacz [tworzenia certyfikatów](vpn-gateway-howto-point-to-site-classic-azure-portal.md#generatecerts).

## <a name="vpn-client-error-a-certificate-chain-processed-but-terminated"></a>Błąd klienta sieci VPN: łańcuch certyfikatów przetworzona, ale została przerwana 

### <a name="symptom"></a>Objaw 

Podczas próby nawiązania sieci wirtualnej platformy Azure przy użyciu klienta sieci VPN, pojawi się następujący komunikat o błędzie:

**Łańcuch certyfikatów przetworzona, ale została przerwana w certyfikacie głównym, który nie jest zaufany przez dostawcę zaufania.**

### <a name="solution"></a>Rozwiązanie

1. Upewnij się, że następujące certyfikaty są w poprawnej lokalizacji:

    | Certyfikat | Lokalizacja |
    | ------------- | ------------- |
    | AzureClient.pfx  | Bieżący User\Personal\Certificates |
    | Azuregateway-*GUID*.cloudapp.net  | Bieżący User\Trusted główne urzędy certyfikacji|
    | AzureGateway-*GUID*.cloudapp.net, AzureRoot.cer    | Lokalny komputer lokalny\Zaufane główne urzędy certyfikacji|

2. Jeśli certyfikaty znajdują się już w lokalizacji, spróbuj usunąć certyfikaty i zainstaluj je ponownie. **Azuregateway -*GUID*. cloudapp.net** certyfikat znajduje się w pakietu konfiguracji klienta sieci VPN, który został pobrany z portalu Azure. Archivers pliku można użyć, aby wyodrębnić pliki z pakietu.

## <a name="file-download-error-target-uri-is-not-specified"></a>Błąd pobierania pliku: nie określono docelowego identyfikatora URI

### <a name="symptom"></a>Objaw

Pojawi się następujący komunikat o błędzie:

**Wystąpił błąd podczas pobierania pliku. Nie określono docelowego identyfikatora URI.**

### <a name="cause"></a>Przyczyna 

Ten problem występuje z powodu typu niepoprawne bramy. 

### <a name="solution"></a>Rozwiązanie

Typ bramy sieci VPN musi być **VPN**, a typ sieci VPN musi być **RouteBased**.

## <a name="vpn-client-error-azure-vpn-custom-script-failed"></a>Błąd klienta sieci VPN: sieci VPN platformy Azure niestandardowego skryptu nie powiodło się 

### <a name="symptom"></a>Objaw

Podczas próby nawiązania sieci wirtualnej platformy Azure przy użyciu klienta sieci VPN, pojawi się następujący komunikat o błędzie:

**Niestandardowego skryptu (Aby uaktualnić tabelę routingu) nie powiodło się. (Błąd 8007026f)**

### <a name="cause"></a>Przyczyna

Ten problem może wystąpić, jeśli próbujesz otworzyć lokacji do punktu połączenia sieci VPN przy użyciu skrótu.

### <a name="solution"></a>Rozwiązanie 

Otwórz pakiet sieci VPN, zamiast bezpośrednio z skrótu.

## <a name="cannot-install-the-vpn-client"></a>Nie można zainstalować klienta sieci VPN

### <a name="cause"></a>Przyczyna 

Dodatkowy certyfikat jest wymagany do zaufania bramy sieci VPN dla sieci wirtualnej. Certyfikat znajduje się w pakiecie konfiguracji klienta sieci VPN, który jest generowany przy użyciu portalu Azure.

### <a name="solution"></a>Rozwiązanie

Wyodrębnij konfiguracji pakietu klienta VPN, a następnie znajdź plik cer. Aby zainstalować certyfikat, wykonaj następujące kroki:

1. Otwórz mmc.exe.
2. Dodaj **certyfikaty** przystawki.
3. Wybierz **komputera** konta na komputerze lokalnym.
4. Kliknij prawym przyciskiem myszy **zaufane główne urzędy certyfikacji** węzła. Kliknij przycisk **wszystkie zadania** > **importu**, a następnie przejdź do pliku .cer wyodrębniony z konfiguracji pakietu klienta VPN.
5. Uruchom ponownie komputer. 
6. Spróbuj zainstalować klienta sieci VPN.

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-data-is-invalid"></a>Błąd portalu Azure: nie można zapisać bramy sieci VPN, a dane są nieprawidłowe

### <a name="symptom"></a>Objaw

Podczas zapisywania zmian dla bramy sieci VPN w portalu Azure, pojawi się następujący komunikat o błędzie:

**Nie można zapisać bramy sieci wirtualnej &lt; *nazwa bramy*&gt;. Dane certyfikatu &lt; *certyfikatu identyfikator* &gt; jest nieprawidłowy.**

### <a name="cause"></a>Przyczyna 

Ten problem może wystąpić, jeśli klucz publiczny certyfikatu głównego, który został przekazany zawiera nieprawidłowy znak, takich jak miejsce.

### <a name="solution"></a>Rozwiązanie

Upewnij się, że dane w certyfikacie nie zawiera nieprawidłowe znaki, takie jak podziały wiersza (znaki powrotu karetki). Całą wartość powinna być jeden długi wiersz. Następujący tekst to przykład certyfikatu:

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

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-resource-name-is-invalid"></a>Błąd portalu Azure: nie można zapisać bramy sieci VPN, a nazwa zasobu jest nieprawidłowy

### <a name="symptom"></a>Objaw

Podczas zapisywania zmian dla bramy sieci VPN w portalu Azure, pojawi się następujący komunikat o błędzie: 

**Nie można zapisać bramy sieci wirtualnej &lt; *nazwa bramy*&gt;. Nazwa zasobu &lt; *nazwę certyfikatu, spróbuj przekazać* &gt; jest nieprawidłowa**.

### <a name="cause"></a>Przyczyna

Ten problem występuje, ponieważ nazwa certyfikatu zawiera nieprawidłowy znak, takich jak miejsce. 

## <a name="azure-portal-error-vpn-package-file-download-error-503"></a>Błąd portalu Azure: 503 błąd pobierania pliku pakietu sieci VPN

### <a name="symptom"></a>Objaw

Podczas próby pobrania konfiguracji pakietu klienta VPN, pojawi się następujący komunikat o błędzie:

**Nie można pobrać pliku. Szczegóły błędu: błąd 503. Serwer jest zajęty.**
 
### <a name="solution"></a>Rozwiązanie

Przyczyną tego błędu może być tymczasowy problem z siecią. Spróbuj pobrać pakiet VPN ponownie za kilka minut.

## <a name="azure-vpn-gateway-upgrade-all-point-to-site-clients-are-unable-to-connect"></a>Uaktualnianie programu Azure bramy sieci VPN: wskaż wszystkich klientów lokacji są nie można nawiązać połączenia

### <a name="cause"></a>Przyczyna

Jeśli certyfikat jest więcej niż 50% przez jego okres istnienia przerzuceniem certyfikatu.

### <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, należy ponownie wdrożyć punkt do lokacji pakietu na wszystkich klientach.

## <a name="too-many-vpn-clients-connected-at-once"></a>Zbyt wielu klientów sieci VPN na raz podłączone

Dla każdej bramy sieci VPN maksymalną liczbę dozwolonych połączeń to 128. Całkowita liczba podłączonych klientów w portalu Azure jest widoczny.

## <a name="point-to-site-vpn-incorrectly-adds-a-route-for-100008-to-the-route-table"></a>Sieć VPN punkt lokacja niepoprawnie dodaje trasę dla 10.0.0.0/8 do tabeli tras

### <a name="symptom"></a>Objaw

Podczas wybierania połączenia sieci VPN na komputerze klienckim punkt lokacja klienta sieci VPN należy dodać trasy do sieci wirtualnej platformy Azure. Usługa Pomocnika IP należy dodać trasę dla podsieci klientów sieci VPN. 

Zakres klienta sieci VPN należy do mniejszych podsiecią 10.0.0.0/8, takich jak 10.0.12.0/24. Zamiast trasę 10.0.12.0/24 trasę dla 10.0.0.0/8 dodaniu, która ma wyższy priorytet. 

Ta trasa niepoprawne dzieli łączności z innych sieciach lokalnych, które mogą należeć do innej podsieci w zakresie 10.0.0.0/8, takich jak 10.50.0.0/24, które nie mają określoną trasę zdefiniowane. 

### <a name="cause"></a>Przyczyna

To zachowanie jest celowe w przypadku klientów z systemem Windows. Jeśli klient używa protokołu PPP IPCP, otrzymuje adres IP dla interfejsu tunelu z serwera (bramy sieci VPN w tym przypadku). Jednak ze względu na ograniczenia w protokole, klient nie ma maskę podsieci. Ponieważ nie istnieje inny sposób pobrać go, klient próbuje odgadnąć maski podsieci, oparty na klasie adresu IP interfejsu tunelu. 

W związku z tym trasę został dodany w zależności od następującego mapowania statyczne: 

Jeśli adres należy do klasy A--> Zastosuj /8

Jeśli adres należy do klasy B--> Zastosuj/16 do /

Jeśli adres należy do klasy C--> Zastosuj prefiksie/24

### <a name="solution"></a>Rozwiązanie

Mieć trasy dla innych sieci, można wprowadzić w tabeli routingu o najdłuższe dopasowanie prefiksu lub niższej metryce (stąd wyższy priorytet) niż punkt do lokacji. 

## <a name="vpn-client-cannot-access-network-file-shares"></a>Klient sieci VPN nie może uzyskać dostępu udziałów plików sieciowych

### <a name="symptom"></a>Objaw

Klient sieci VPN został podłączony do sieci wirtualnej platformy Azure. Jednak klienta nie może uzyskać dostępu do udziałów sieciowych.

### <a name="cause"></a>Przyczyna

Protokół SMB służy do dostępu do udziału plików. Po zainicjowaniu połączenia klienta sieci VPN dodaje poświadczenia sesji i występuje błąd. Po nawiązaniu połączenia, klient musi używać pamięci podręcznej poświadczeń dla uwierzytelniania Kerberos. Ten proces jest inicjowany zapytań do Centrum dystrybucji kluczy (kontroler domeny) w celu pobrania tokenu. Ponieważ klient jest połączony z Internetu, go nie można nawiązać połączenia z kontrolerem domeny. W związku z tym klient nie może przełączyć się z protokołu Kerberos do uwierzytelniania NTLM. 

Tylko czas klienta jest monitów o podanie poświadczeń jest, gdy ma prawidłowy certyfikat (z siecią SAN = nazwy UPN) wydanego przez domenę, do której jest dołączony. Klient również musi być fizycznie podłączeni do sieci z domeną. W takim przypadku klient próbuje użyć certyfikatu i osiągnie na kontrolerze domeny. Centrum dystrybucji kluczy zwraca błąd "KDC_ERR_C_PRINCIPAL_UNKNOWN". Klient wymusza do trybu failover protokołu NTLM. 

### <a name="solution"></a>Rozwiązanie

Aby obejść ten problem, wyłącz buforowanie poświadczeń domeny z następujący podklucz rejestru: 

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\DisableDomainCreds - Set the value to 1 


## <a name="cannot-find-the-point-to-site-vpn-connection-in-windows-after-reinstalling-the-vpn-client"></a>Nie można znaleźć lokacji punktu połączenia sieci VPN w systemie Windows przed ponowną instalację klienta sieci VPN

### <a name="symptom"></a>Objaw

Usuń połączenie VPN punkt lokacja, a następnie ponownie zainstalować klienta sieci VPN. W takiej sytuacji nie skonfigurowano pomyślnie połączenia sieci VPN. Nie ma połączenia sieci VPN w **połączenia sieciowe** ustawienia systemu Windows.

### <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, Usuń stare pliki konfiguracji klienta sieci VPN z **C:\users\username\AppData\Microsoft\Network\Connections\<VirtualNetworkId >**, a następnie ponownie uruchom Instalatora klienta sieci VPN.

## <a name="point-to-site-vpn-client-cannot-resolve-the-fqdn-of-the-resources-in-the-local-domain"></a>Klient VPN punkt lokacja nie może rozpoznać nazwę FQDN zasobów w domenie lokalnej

### <a name="symptom"></a>Objaw

Gdy klient nawiąże połączenie z Azure przy użyciu połączenia VPN punkt lokacja, nie można rozpoznać FQND zasobów w domenie lokalnej.

### <a name="cause"></a>Przyczyna

Klient sieci VPN punkt lokacja używa serwerów usługi Azure DNS, które są skonfigurowane w sieci wirtualnej platformy Azure. Serwery Azure DNS mają pierwszeństwo przed lokalnych serwerów DNS, które skonfigurowano w kliencie, więc wszystkie zapytania DNS są wysyłane do serwerów DNS platformy Azure. Jeśli nie ma rekordów zasobów lokalnych serwerów usługi Azure DNS, zapytanie kończy się niepowodzeniem.

### <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, upewnij się, że serwery DNS platformy Azure, które używane w sieci wirtualnej platformy Azure można rozwiązać rekordy DNS dla zasobów lokalnych. Aby to zrobić, można użyć usług przesyłania dalej DNS lub usług warunkowego przesyłania dalej. Aby uzyskać więcej informacji, zobacz [rozpoznawanie nazw przy użyciu serwera DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)

## <a name="the-point-to-site-vpn-connection-is-established-but-you-still-cannot-connect-to-azure-resources"></a>Ustanowieniu połączenia sieci VPN punkt lokacja, ale nadal nie można połączyć zasobów platformy Azure 

### <a name="cause"></a>Przyczyna

Ten problem może wystąpić, jeśli klient sieci VPN nie otrzymują trasy z bramy sieci VPN platformy Azure.

### <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, [zresetować bramy sieci VPN platformy Azure](vpn-gateway-resetgw-classic.md).

## <a name="error-the-revocation-function-was-unable-to-check-revocation-because-the-revocation-server-was-offlineerror-0x80092013"></a>Błąd: "Funkcja odwołania nie może sprawdzić odwołania, ponieważ serwer odwołań jest w trybie offline. (Błąd 0x80092013)"

### <a name="causes"></a>Powoduje, że
Ten błąd występuje, jeśli klient nie może uzyskać dostępu do http://crl3.digicert.com/ssca-sha2-g1.crl i http://crl4.digicert.com/ssca-sha2-g1.cr.  Sprawdzanie odwołań wymaga dostępu do tych dwóch witryn.  Ten problem zwykle odbywa się na komputerze klienckim, który ma skonfigurowany serwer proxy. W przypadku środowisk Jeśli nie będą żądania przez serwer proxy go będą odrzucane na brzegowej zaporze sieciowej.

### <a name="solution"></a>Rozwiązanie

Sprawdź ustawienia serwera proxy, należy upewnić się, że klient ma dostęp do http://crl3.digicert.com/ssca-sha2-g1.crl i http://crl4.digicert.com/ssca-sha2-g1.cr.

## <a name="vpn-client-error-the-connection-was-prevented-because-of-a-policy-configured-on-your-rasvpn-server-error-812"></a>Błąd klienta sieci VPN: Połączenie nie mógł ze względu na zasady skonfigurowane na serwerze RAS lub sieć VPN. (Błąd 812)

### <a name="cause"></a>Przyczyna

Ten błąd występuje, gdy serwer usługi RADIUS, używany do uwierzytelniania klientów sieci VPN, ma nieprawidłowe ustawienia, lub bramy usługi Azure nie może uzyskać dostęp do serwera Radius.

### <a name="solution"></a>Rozwiązanie

Upewnij się, że serwer RADIUS jest skonfigurowany prawidłowo. Aby uzyskać więcej informacji, zobacz [integracji uwierzytelnianie usługi RADIUS z serwera usługi Azure Multi-Factor Authentication](../active-directory/authentication/howto-mfaserver-dir-radius.md).

## <a name="error-405-when-you-download-root-certificate-from-vpn-gateway"></a>"Błąd 405" po pobraniu certyfikatu głównego z bramy sieci VPN

### <a name="cause"></a>Przyczyna

Nie zainstalowano certyfikat główny. Certyfikat główny jest zainstalowany na komputerze klienckim **zaufane certyfikaty** przechowywania.

## <a name="vpn-client-error-the-remote-connection-was-not-made-because-the-attempted-vpn-tunnels-failed-error-800"></a>Błąd klienta sieci VPN: Nie został utworzony połączenia zdalnego, ponieważ nie powiodła się próba tuneli VPN. (Błąd 800) 

### <a name="cause"></a>Przyczyna

Sterownik karty Sieciowej jest nieaktualna.

### <a name="solution"></a>Rozwiązanie

Aktualizacja sterownika karty Sieciowej:

1. Kliknij przycisk **Start**, typ **Menedżera urządzeń**i wybierz go z listy wyników. Jeśli zostanie wyświetlony monit o hasło administratora lub potwierdzenie, wpisz hasło lub potwierdź.
2. W **karty sieciowe** kategorii, znaleźć karty Sieciowej, który chcesz zaktualizować.  
3. Kliknij dwukrotnie nazwę urządzenia, zaznacz **aktualizacji sterownika**, wybierz pozycję **Wyszukaj automatycznie zaktualizowane sterowniki**.
4. Jeśli system Windows nie wykrywa nowego sterownika, można spróbuj poszukać na witrynie sieci Web producenta urządzenia i wykonaj instrukcje.
5. Uruchom ponownie komputer i spróbuj ponownie nawiązać połączenie.

## <a name="error-file-download-error-target-uri-is-not-specified"></a>Błąd: "Błąd pobierania, nie jest określana przez identyfikator URI docelowego pliku"

### <a name="cause"></a>Przyczyna

Jest to spowodowane przez bramę niepoprawny typ jest skonfigurowany.

### <a name="solution"></a>Rozwiązanie

Sieci VPN musi być typu bramy sieci VPN platformy Azure i typ sieci VPN musi być **RouteBased**.

## <a name="vpn-package-installer-doesnt-complete"></a>Instalator pakietu sieci VPN nie zakończyć.

### <a name="cause"></a>Przyczyna

Ten problem może być spowodowane poprzednich instalacji klienta sieci VPN. 

### <a name="solution"></a>Rozwiązanie

Usuń stare pliki konfiguracji klienta sieci VPN z **C:\users\username\AppData\Microsoft\Network\Connections\<VirtualNetworkId >** i ponownie uruchom Instalatora klienta sieci VPN. 

## <a name="the-vpn-client-hibernates-or-sleep-after-some-time"></a>Klient sieci VPN w stan hibernacji lub uśpienia za jakiś czas

### <a name="solution"></a>Rozwiązanie

Sprawdź uśpienia i hibernacji ustawienia w komputerze, na którym działa klient sieci VPN na.
