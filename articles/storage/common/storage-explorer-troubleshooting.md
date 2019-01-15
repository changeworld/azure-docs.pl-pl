---
title: Przewodnik rozwiązywania problemów z Eksploratora usługi Storage platformy Azure | Dokumentacja firmy Microsoft
description: Omówienie dwóch debugowanie funkcji platformy Azure
services: virtual-machines
author: Deland-Han
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.component: common
ms.openlocfilehash: 2434c04260c223fdcc03bc7f91a6fd147e1d8503
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2019
ms.locfileid: "54264109"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Przewodnik rozwiązywania problemów z Eksploratora usługi Storage platformy Azure

Eksplorator usługi Microsoft Azure Storage jest aplikacją autonomiczną, która umożliwia łatwą obsługę danych w usłudze Azure Storage w Windows, macOS i Linux. Aplikacja, mogą łączyć się kont magazynu hostowana na platformie Azure, chmurach krajowych i usługi Azure Stack.

Ten przewodnik zawiera podsumowanie rozwiązań typowych problemów występujących w Eksploratorze usługi Storage.

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Błąd: Certyfikat z podpisem własnym w łańcuchu certyfikatów (i podobne błędy)

Błędy certyfikatów są spowodowane przez jedną z następujących dwóch sytuacji:

1. Aplikacja jest połączona za pośrednictwem "przezroczystym serwerem proxy", co oznacza przechwytuje ruch HTTPS, odszyfrowuje go i następnie szyfruje za pomocą certyfikatu z podpisem własnym serwera (np. serwera firmy).
2. Uruchomiono aplikację, która wprowadza certyfikat protokołu SSL z podpisem własnym do wiadomości protokołu HTTPS, które otrzymujesz. Przykłady aplikacji, które wstrzyknąć certyfikaty obejmuje oprogramowanie do kontroli ruchu oprogramowanie antywirusowe i sieci.

Kiedy Eksplorator usługi Storage widzi podpisem własnym lub niezaufany certyfikat, go nie będzie już wiedział, czy odebrano komunikat HTTPS został zmieniony. Jeśli masz kopię certyfikatu z podpisem własnym, możesz wydać polecenie Eksploratora usługi Storage zaufania temu certyfikatowi, wykonując następujące czynności:

1. Uzyskaj kopia certyfikatu X.509 (.cer) z kodowaniem Base-64
2. Kliknij przycisk **Edytuj** > **certyfikaty SSL** > **Importuj certyfikaty**, a następnie za pomocą selektora plików Znajdź, wybierz i Otwórz plik cer

Ten problem może być również wynikiem wielu certyfikatów (głównych i pośrednich). Aby wyeliminować ten błąd, należy dodać certyfikaty.

Jeśli wiesz skąd pochodzą certyfikatu, możesz wypróbować następujące kroki, aby go znaleźć:

1. Zainstaluj protokół Open SSL

    * [Windows](https://slproweb.com/products/Win32OpenSSL.html) (z wersji światła, powinny być wystarczające)
    * Systemów Mac i Linux: powinien być dołączony do systemu operacyjnego
2. Uruchom protokół Open SSL

    * Windows: kliknij pozycję Otwórz katalog instalacyjny **/bin/**, a następnie kliknij dwukrotnie **openssl.exe**.
    * Systemów Mac i Linux: Uruchom **openssl** z poziomu terminalu.
3. Wykonaj polecenie `s_client -showcerts -connect microsoft.com:443`
4. Wyszukaj certyfikaty z podpisem własnym. Jeśli wiesz, które są podpisem, poszukaj dowolnym temat `("s:")` i Wystawca `("i:")` są takie same.
5. Po znalezieniu żadnych certyfikatów z podpisem własnym dla każdego z nich, skopiuj i Wklej wszystko, od i tym **---BEGIN CERTIFICATE---** do **---END CERTIFICATE---** do nowego pliku cer.
6. Otwórz Eksploratora usługi Storage, kliknij przycisk **Edytuj** > **certyfikaty SSL** > **Importuj certyfikaty**, a następnie za pomocą selektora plików Znajdź, wybierz opcję, i Otwórz pliki cer, które zostały utworzone.

Jeśli nie można odnaleźć żadnych certyfikatów z podpisem własnym za pomocą powyższych kroków, skontaktuj się z nami za pośrednictwem narzędzia opinii, aby uzyskać dalszą pomoc. Alternatywnie można wybrać, czy można uruchomić Eksploratora usługi Storage z poziomu wiersza polecenia przy użyciu `--ignore-certificate-errors` flagi. Gdy uruchomiony przy użyciu tej flagi, Eksploratora usługi Storage będzie ignorować błędy certyfikatów.

## <a name="sign-in-issues"></a>Problemy dotyczące logowania

### <a name="reauthentication-loop-or-upn-change"></a>Ponowne uwierzytelnianie pętli lub zmień nazwę UPN
Jeśli w pętli ponownego lub zostały zmienione nazwy UPN jednego z kont, spróbuj wykonać następujące czynności:
1. Usuń wszystkie konta, a następnie Zamknij Eksploratora usługi Storage
2. Usuń. Folder IdentityService z Twojego komputera. W Windows, folderze znajduje się w `C:\users\<username>\AppData\Local`. Dla systemów Mac i Linux można znaleźć folderu w folderze głównym katalogu użytkownika.
3. Jeśli na komputerze Mac lub Linux, należy również usunąć wpis Microsoft.Developer.IdentityService z magazynu kluczy Twojego systemu operacyjnego. Na komputerze Mac magazynu kluczy to aplikacja "Gnome Pęk kluczy". Dla systemu Linux aplikacji jest zwykle nazywane "Pęku kluczy", ale nazwa może się różnić w zależności od Twojej dystrybucji.

### <a name="conditional-access"></a>Dostęp warunkowy
Dostęp warunkowy nie jest obsługiwana, gdy Eksplorator usługi Storage jest używany w systemie Windows 10, Linux lub macOS. Jest to spowodowane ograniczenia w bibliotece usługi AAD używane przez Eksploratora usługi Storage.

## <a name="mac-keychain-errors"></a>Błędy pęku kluczy komputera Mac.
Systemu macOS pęku kluczy czasami może przejść do stanu, który powoduje, że problemy z biblioteki uwierzytelniania usługi Storage Explorer. Aby uzyskać łańcucha kluczy, poza tym spróbuj stanu następujące czynności:
1. Zamknij Eksploratora usługi Storage.
2. Otwórz łańcucha kluczy (**cmd + spacja**, wpisz w pęku kluczy, trafienia wprowadź).
3. Wybierz łańcucha kluczy "Logowanie".
4. Kliknij ikonę kłódki, aby zablokować łańcucha kluczy (kłódki będzie animować położenie zablokowany, po zakończeniu może potrwać kilka sekund w zależności od aplikacji, które należy otworzyć).

    ![image](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Uruchom Eksploratora usługi Storage.
6. Okno podręczne powinna pojawić się informacją o tym rodzaju "Centrum usługi chce uzyskać dostęp łańcucha kluczy". Po jego, wprowadź hasło konta administratora Mac i kliknięciu **zawsze Zezwalaj na** (lub **Zezwalaj** Jeśli **zawsze Zezwalaj na** nie jest dostępna).
7. Spróbuj zalogować się.

### <a name="general-sign-in-troubleshooting-steps"></a>Ogólne logowania kroki rozwiązywania problemów
* Jeśli jesteś w systemie macOS i za pośrednictwem okna dialogowego "Oczekiwanie... uwierzytelniania" nigdy nie zostanie wyświetlone okno logowania, spróbuj [następujące kroki](#mac-keychain-errors)
* Uruchom program Storage Explorer
* Jeśli okno uwierzytelniania jest pusty, zaczekaj co najmniej jedną minutę przed zamknięciem okna dialogowego uwierzytelniania.
* Upewnij się, że usługi serwera proxy i certyfikatu, że ustawienia są poprawnie skonfigurowane dla komputera i Eksploratora usługi Storage.
* Jeśli znajdują się na Windows i mieć dostęp do programu Visual Studio 2017 na tym samym komputerze i logowania, zaloguj się do programu Visual Studio 2017. Po pomyślnym zalogowaniu do programu Visual Studio 2017 należy otworzyć Eksploratora usługi Storage i wyświetlenia ekranu konto użytkownika w panelu konta.

Jeśli żadna z tych metod działa [Otwórz problem w serwisie GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

### <a name="missing-subscriptions-and-broken-tenants"></a>Brak subskrypcji i dzierżaw w uszkodzona

Jeśli nie można pobrać subskrypcji po pomyślnym zalogowaniu, wypróbuj poniższe metody rozwiązywania problemów:

* Sprawdź, czy Twoje konto ma dostęp do subskrypcji, których oczekujesz. Aby sprawdzić, czy masz dostęp, logując się do portalu dla środowiska platformy Azure, którą próbujesz użyć.
* Upewnij się, że jesteś użytkownikiem zarejestrowanym w przy użyciu Azure poprawne środowiska (Azure, chińska wersja platformy Azure, Azure (Niemcy), dla administracji USA lub środowisko niestandardowe).
* Jeśli używasz serwera proxy, upewnij się, poprawnie skonfigurowany serwer proxy Eksploratora usługi Storage.
* Spróbuj usunąć i ponownie dodać konto.
* Jeśli łącze "Informacje dodatkowe", przejrzyj i zobacz, jakie komunikaty o błędach są zgłaszane w przypadku dzierżaw, które kończą się niepowodzeniem. Jeśli nie masz pewności co należy zrobić komunikaty o błędach można znaleźć, a następnie możesz [Otwórz problem w serwisie GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="cannot-remove-attached-account-or-storage-resource"></a>Nie można usunąć dołączonych zasobów lub konta usługi storage

Jeśli nie można usunąć konta dołączonych lub zasób magazynu za pośrednictwem interfejsu użytkownika, należy ręcznie usunąć wszystkie dołączone zasoby, usuwając następujące foldery:

* Windows: `%AppData%/StorageExplorer`
* macOS: `/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux: `~/.config/StorageExplorer`

> [!NOTE]
>  Zamknij Eksploratora usługi Storage przed usunięciem powyżej folderów.

> [!NOTE]
>  Jeśli nigdy nie zaimportowano żadnych certyfikatów SSL, następnie utworzyć kopię zapasową zawartości `certs` katalogu. Później można użyć kopii zapasowej, aby ponowne zaimportować certyfikaty SSL.

## <a name="proxy-issues"></a>Problemy dotyczące serwera proxy

Najpierw upewnij się, że następujące wprowadzone informacje są poprawne:

* Adres URL serwera proxy i numer portu
* Nazwa użytkownika i hasło, jeśli jest to wymagane przez serwer proxy

Należy pamiętać, że Eksplorator usługi Storage nie obsługuje plik PAC do konfigurowania ustawień serwera proxy.

### <a name="common-solutions"></a>Typowe rozwiązania

Jeśli nadal występują problemy, wypróbuj poniższe metody rozwiązywania problemów:

* Jeżeli może połączyć się z Internetem, bez użycia serwera proxy, sprawdź, czy Eksploratora usługi Storage działa bez ustawienia serwera proxy włączony. Jeśli jest to możliwe, może to być problem z ustawieniami serwera proxy. Współpracować z administratorem serwera proxy, aby zidentyfikować problemy.
* Sprawdź, czy inne aplikacje korzystające z serwera proxy działają zgodnie z oczekiwaniami.
* Sprawdzić, czy można połączyć się do portalu dla środowiska platformy Azure, którą próbujesz użyć
* Upewnij się, że można odbierać odpowiedzi z punktów końcowych usługi. Wprowadź swoje adresy URL punktu końcowego w przeglądarce. Jeśli można się połączyć, powinna pojawić się InvalidQueryParameterValue lub podobne odpowiedzi XML.
* Jeśli ktoś inny się również za pomocą Eksploratora usługi Storage z serwerem proxy, sprawdź, czy można połączyć. Jeśli można nawiązać połączenie, może być konieczne skontaktuj się z administratorem serwera proxy

### <a name="tools-for-diagnosing-issues"></a>Narzędzia do diagnozowania problemów

Jeśli masz sieci narzędzi, takich jak program Fiddler for Windows można diagnozować problemy w następujący sposób:

* Jeśli trzeba pracować za pośrednictwem serwera proxy, może być konieczne konfigurowanie sieci narzędziem do łączenia za pośrednictwem serwera proxy.
* Sprawdź numer portu używanego przez narzędzie do sieci.
* Wprowadź adres URL hosta lokalnego i numer portu sieci narzędzie zgodnie z ustawieniami serwera proxy w Eksploratorze usługi Storage. Jeśli zostaną prawidłowo wykonane, rozpoczyna się narzędziem sieci, rejestrowanie żądań sieciowych podjęte przez Eksploratora usługi Storage w celu zarządzania i punktów końcowych usługi. Na przykład, wprowadź https://cawablobgrs.blob.core.windows.net/ dla punktu końcowego usługi blob, w przeglądarce, na które będą otrzymywać odpowiedzi podobne do następujących, co sugeruje istniejący zasób, mimo że nie można do niego dostęp.

![Przykładowy kod](./media/storage-explorer-troubleshooting/4022502_en_2.png)

### <a name="contact-proxy-server-admin"></a>Skontaktuj się z administratorem serwera proxy

Jeśli ustawienia serwera proxy są poprawne, należy skontaktować się z administratorem serwera proxy i

* Upewnij się, że Twój serwer proxy nie blokuje ruchu do punktów końcowych platformy Azure, zarządzania lub zasobu.
* Sprawdź, protokół uwierzytelniania używany przez serwer proxy. Eksplorator usługi Storage obecnie nie obsługuje serwerów proxy NTLM.

## <a name="unable-to-retrieve-children-error-message"></a>Komunikat o błędzie "Nie można pobrać elementów podrzędnych"

Jeśli na platformie Azure są połączone za pośrednictwem serwera proxy, sprawdź, czy ustawienia serwera proxy są poprawne. Jeśli z właścicielem subskrypcji, czy konta zostały przyznano dostęp do zasobu, sprawdź, czy po ich przeczytaniu lub listy uprawnień dla tego zasobu.

## <a name="connection-string-does-not-have-complete-configuration-settings"></a>Parametry połączenia nie ma pełną konfigurację ustawień

Jeśli zostanie wyświetlony ten komunikat o błędzie, jest to możliwe, że nie masz wymaganych uprawnień do uzyskania kluczy dla konta magazynu. Aby upewnić się, jeśli jest to możliwe, przejdź do portalu, a następnie zlokalizuj konto magazynu. Możesz szybko to zrobić, kliknij prawym przyciskiem myszy kliknij węzeł konta usługi Storage, a następnie klikając polecenie "Otwórz w Portal". Po wykonaniu tej czynności, przejdź do bloku "Klucze dostępu". Jeśli nie masz uprawnień do wyświetlania kluczy zostanie wyświetlona strona, z komunikatem "Nie masz dostępu.". Aby obejść ten problem, można uzyskać klucz konta od kogoś innego i dołączyć nazwą i kluczem lub może być poproszenie o sygnatury dostępu Współdzielonego do konta magazynu i umożliwia dołączanie konta magazynu.

Jeśli możesz wyświetlić klucze konta, a następnie Zgłoś problem w serwisie GitHub, dzięki czemu możemy Ci pomóc rozwiązać ten problem.

## <a name="issues-with-sas-url"></a>Problemy związane z adresu URL sygnatury dostępu Współdzielonego
Jeśli łączysz się z usługą przy użyciu adresu URL sygnatury dostępu Współdzielonego i występuje ten błąd:

* Upewnij się, że adres URL zawiera niezbędne uprawnienia do odczytu lub wyświetla listę zasobów.
* Upewnij się, że adres URL nie wygasł.
* Adres URL sygnatury dostępu Współdzielonego zależy od zasad dostępu, sprawdź, czy zasady dostępu nie został odwołany.

Jeśli przypadkowo dołączany przy użyciu nieprawidłowego adresu URL sygnatury dostępu Współdzielonego i nie można odłączyć, wykonaj następujące kroki:
1.  Podczas uruchamiania programu Storage Explorer, naciśnij klawisz F12, aby otworzyć okno narzędzi programistycznych.
2.  Kliknij kartę aplikacji, a następnie kliknij pozycję Magazyn lokalny > file:// w drzewie po lewej stronie.
3.  Znajdź klucz skojarzony z typem usługi problematyczne identyfikatora URI sygnatury dostępu Współdzielonego. Na przykład, jeśli nieprawidłowego identyfikatora URI połączenia SAS dla kontenera obiektów blob, poszukaj klucza o nazwie `StorageExplorer_AddStorageServiceSAS_v1_blob`.
4.  Wartość klucza powinna być tablicą JSON. Znajdź obiekt skojarzony z nieprawidłowy identyfikator URI i usuń go.
5.  Naciśnij klawisze Ctrl + R, aby ponownie załadować Eksploratora usługi Storage.

## <a name="linux-dependencies"></a>Zależności systemu Linux

Na dystrybucje systemu Linux niż Ubuntu 16.04 może być konieczne ręczne zainstalowanie niektóre zależności. Ogólnie rzecz biorąc wymagane są następujące pakiety:
* [.NET Core 2.x](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x)
* `libsecret`
* `libgconf-2-4`
* Aktualne GCC

W zależności od Twojej dystrybucji może być inne pakiety, które są potrzebne do zainstalowania. Program Storage Explorer [wersji](https://go.microsoft.com/fwlink/?LinkId=838275&clcid=0x409) zawierają poznać konkretne kroki dla niektórych dystrybucjach.

## <a name="next-steps"></a>Kolejne kroki

Jeśli te rozwiązania nie działają, następnie [Otwórz problem w serwisie GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues). Możesz także szybki dostęp do usługi GitHub za pomocą przycisku "Zgłoś problem w usłudze GitHub" w lewym dolnym rogu.

![Opinia](./media/storage-explorer-troubleshooting/feedback-button.PNG)
