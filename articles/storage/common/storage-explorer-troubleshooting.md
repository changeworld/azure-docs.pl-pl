---
title: Przewodnik rozwiązywania problemów z Eksploratora usługi Storage platformy Azure | Dokumentacja firmy Microsoft
description: Przegląd debugowania dla usługi Azure Storage Explorer
services: virtual-machines
author: Deland-Han
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: 980dc850537b7419e4ee48391acd5ba971fb3fed
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306734"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Przewodnik rozwiązywania problemów do usługi Azure Storage Explorer

Eksplorator usługi Microsoft Azure Storage jest aplikacją autonomiczną, która umożliwia łatwą obsługę danych w usłudze Azure Storage w Windows, macOS i Linux. Aplikacja, mogą łączyć się kont magazynu hostowana na platformie Azure, chmurach krajowych i usługi Azure Stack.

Ten przewodnik zawiera podsumowanie rozwiązań typowych problemów występujących w Eksploratorze usługi Storage.

## <a name="role-based-access-control-permission-issues"></a>Problemy z uprawnieniami kontroli dostępu opartej na rolach

[Kontrola dostępu oparta na rolach (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) umożliwia szczegółowe zarządzanie dostępem zasobów platformy Azure, łącząc zestawy uprawnień do _role_. Poniżej przedstawiono kilka sugestii, które można wykonać, aby pobrać RBAC Praca w Eksploratorze usługi Storage.

### <a name="what-do-i-need-to-see-my-resources-in-storage-explorer"></a>Co muszę zobaczyć Moje zasoby w Eksploratorze usługi Storage?

Jeśli występują problemy z dostępem do zasobów magazynu, korzystając z modelu RBAC, może to być, ponieważ nie zostały przypisane odpowiednie role. W poniższych sekcjach opisano uprawnienia, obecnie Eksploratora usługi Storage wymaga dostępu do zasobów magazynu.

Jeśli wiesz, że masz odpowiednie role i uprawnienia, skontaktuj się z administratorem konta platformy Azure.

#### <a name="read-listget-storage-accounts"></a>Odczyt: Wyświetl/Pobierz konta magazynu

Musi mieć uprawnienia do listy kont magazynu. To uprawnienie można uzyskać po przypisaniu roli "Czytelnik".

#### <a name="list-storage-account-keys"></a>Wyświetl klucze konta magazynu

Eksplorator usługi Storage umożliwia również klucze konta do uwierzytelniania żądań. Możesz uzyskać dostęp do kluczy za pomocą bardziej zaawansowanych ról, takich jak rola "Współpracownik".

> [!NOTE]
> Klucze dostępu przyznaj uprawnienia bez ograniczeń dla każdego, kto je ma. W związku z tym zazwyczaj nie zaleca ich można przekazać do kont użytkowników. Jeśli zajdzie potrzeba odwołania kluczy dostępu, można odtworzyć je z [witryny Azure Portal](https://portal.azure.com/).

#### <a name="data-roles"></a>Role danych

Musi być przypisany co najmniej jedną rolę, która udziela dostępu do odczytu danych z zasobów. Na przykład jeśli potrzebujesz wyświetlić listę lub Pobierz obiekty BLOB, należy co najmniej rolę "Czytnik danych obiektu Blob magazynu".

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>Dlaczego muszę roli warstwę zarządzania, aby zobaczyć Moje zasoby w Eksploratorze usługi Storage?

Usługa Azure Storage ma dwie warstwy dostępu: _zarządzania_ i _danych_. Subskrypcje i konta magazynu są dostępne za pośrednictwem warstwy zarządzania. Kontenerów, obiektów blob i inne zasoby danych są dostępne za pośrednictwem warstwy danych. Na przykład jeśli chcesz uzyskać listę kont magazynu platformy Azure, wysłaniu żądania do punktu końcowego zarządzania. Chcąc listę kontenerów obiektów blob na koncie, wysłaniu żądania do punktu końcowego odpowiednią usługę.

Role RBAC, może zawierać uprawnień dostępu do warstwy zarządzania lub danych. Tej roli "Czytelnik", udziela na przykład zasoby warstwy zarządzania dostęp tylko do odczytu.

Ściśle rzecz ujmując roli "Czytelnik" zapewnia brak uprawnień do warstwy danych i nie jest konieczne do uzyskania dostępu do warstwy danych.

Eksplorator usługi Storage ułatwia dostęp do zasobów Dzięki zbieraniu informacje wymagane do połączenia z zasobami platformy Azure dla Ciebie. Na przykład aby wyświetlić swoje kontenery obiektów blob, Eksploratora usługi Storage wysyła żądanie kontenery listy punkt końcowy usługi blob. Aby uzyskać punkt końcowy, Eksploratora usługi Storage wyszukuje listę subskrypcji, i kont magazynu, że masz dostęp do. Jednak aby dowiedzieć się, Twoje subskrypcje i konta magazynu, Eksploratora usługi Storage również musi mieć dostęp do warstwy zarządzania.

Jeśli nie masz roli udzielanie jakiegokolwiek uprawnień warstwy Eksploratora usługi Storage nie można pobrać informacji wymaganych do łączenia z warstwą danych.

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>Co zrobić, jeśli nie mogę uruchomić Zarządzanie uprawnieniami warstwy potrzebujesz mojego administratora?

Nie mamy jeszcze rozwiązanie związane z funkcji RBAC w tej chwili. Jako obejście tego problemu, możesz poprosić o identyfikatora URI sygnatury dostępu Współdzielonego do [dołączyć do zasobu](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux#attach-a-service-by-using-a-shared-access-signature-sas).

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Błąd: Certyfikat z podpisem własnym w łańcuchu certyfikatów (i podobne błędy)

Błędy certyfikatów są spowodowane przez jedną z następujących dwóch sytuacji:

1. Aplikacja jest połączona za pośrednictwem "przezroczystym serwerem proxy", co oznacza przechwytuje ruch HTTPS, odszyfrowuje go i następnie szyfruje za pomocą certyfikatu z podpisem własnym serwera (np. serwera firmy).
2. Używasz aplikacji, która wprowadza certyfikat protokołu SSL z podpisem własnym do wiadomości protokołu HTTPS, które otrzymujesz. Przykłady aplikacji, które wstrzyknąć certyfikaty obejmuje oprogramowanie do kontroli ruchu oprogramowanie antywirusowe i sieci.

Gdy Eksplorator usługi Storage widzi podpisem własnym lub niezaufany certyfikat, go nie będzie już wiedział, czy odebrano komunikat HTTPS został zmieniony. Jeśli masz kopię certyfikatu z podpisem własnym, możesz wydać polecenie Eksploratora usługi Storage zaufania temu certyfikatowi, wykonując następujące czynności:

1. Uzyskaj kopia certyfikatu X.509 (.cer) z kodowaniem Base-64
2. Kliknij przycisk **Edytuj** > **certyfikaty SSL** > **Importuj certyfikaty**, a następnie za pomocą selektora plików Znajdź, wybierz i Otwórz plik cer

Ten problem może być również wynikiem wielu certyfikatów (głównych i pośrednich). Aby wyeliminować ten błąd, należy dodać certyfikaty.

Jeśli masz pewności, z której pochodzi z certyfikatu, możesz wypróbować następujące kroki, aby go znaleźć:

1. Zainstaluj protokół Open SSL
    * [Windows](https://slproweb.com/products/Win32OpenSSL.html) (z wersji światła, powinny być wystarczające)
    * Systemów Mac i Linux: powinien być dołączony do systemu operacyjnego
2. Uruchom protokół Open SSL
    * Windows: kliknij pozycję Otwórz katalog instalacyjny **/bin/** , a następnie kliknij dwukrotnie **openssl.exe**.
    * Systemów Mac i Linux: Uruchom **openssl** z poziomu terminalu.
3. Wykonaj polecenie `s_client -showcerts -connect microsoft.com:443`
4. Wyszukaj certyfikaty z podpisem własnym. Jeśli masz pewności o certyfikatach, które mają podpis własny, poszukaj dowolnym podmiotu `("s:")` i Wystawca `("i:")` są takie same.
5. Po znalezieniu żadnych certyfikatów z podpisem własnym dla każdego z nich, skopiuj i Wklej wszystko, od i tym **---BEGIN CERTIFICATE---** do **---END CERTIFICATE---** do nowego pliku cer.
6. Otwórz Eksploratora usługi Storage, kliknij przycisk **Edytuj** > **certyfikaty SSL** > **Importuj certyfikaty**, a następnie za pomocą selektora plików Znajdź, wybierz opcję, i Otwórz pliki cer, które zostały utworzone.

Jeśli nie można odnaleźć żadnych certyfikatów z podpisem własnym za pomocą powyższych kroków, skontaktuj się z nami za pośrednictwem narzędzia opinii, aby uzyskać dalszą pomoc. Możesz również uruchomić Eksploratora usługi Storage z poziomu wiersza polecenia przy użyciu `--ignore-certificate-errors` flagi. Gdy uruchomiony przy użyciu tej flagi, Eksploratora usługi Storage będzie ignorować błędy certyfikatów.

## <a name="sign-in-issues"></a>Problemy dotyczące logowania

### <a name="blank-sign-in-dialog"></a>Puste okno logowania

Puste logowania wyświetlanymi w oknach dialogowych są najczęściej powodowane przez usługi AD FS pytaniem Eksploratora usługi Storage do wykonania przekierowania, który jest nieobsługiwany przez elektronów. Aby obejść ten problem, można spróbować użyć przepływu kodu urządzenia podczas logowania. Aby to zrobić, wykonaj następujące czynności:

1. Menu: -> (Wersja zapoznawcza), "Użyj logowania kodu urządzenia".
2. Otwórz okno dialogowe Łączenie (za pośrednictwem ikony plug pionowy pasek po lewej stronie, lub "Dodawanie konta" na panelu konta).
3. Wybierz środowisko, które chcesz zalogować się do.
4. Kliknij przycisk "Zaloguj".
5. Postępuj zgodnie z instrukcjami w następnej panelu.

Jeśli okaże się problemy, logując się do konta którego chcesz użyć, ponieważ domyślna przeglądarka już zalogowali się do innego konta, możesz:

1. Ręcznie skopiować link i kod do prywatnego sesji przeglądarki.
2. Ręcznie skopiować link i kodu w innej przeglądarce.

### <a name="reauthentication-loop-or-upn-change"></a>Ponowne uwierzytelnianie pętli lub zmień nazwę UPN

Jeśli znajdujesz się w pętli ponownego lub zostały zmienione nazwy UPN jednego z kont, spróbuj wykonać następujące kroki:

1. Usuń wszystkie konta, a następnie Zamknij Eksploratora usługi Storage
2. Usuń. Folder IdentityService z Twojego komputera. W Windows, folderze znajduje się w `C:\users\<username>\AppData\Local`. Dla systemów Mac i Linux można znaleźć folderu w folderze głównym katalogu użytkownika.
3. Jeśli masz na komputerze Mac lub Linux, należy również usunąć wpis Microsoft.Developer.IdentityService z magazynu kluczy Twojego systemu operacyjnego. Na komputerze Mac magazynu kluczy to aplikacja "Gnome Pęk kluczy". Dla systemu Linux aplikacji jest zwykle nazywane "Pęku kluczy", ale nazwa może się różnić w zależności od Twojej dystrybucji.

### <a name="conditional-access"></a>Dostęp warunkowy

Dostęp warunkowy nie jest obsługiwana, gdy Eksplorator usługi Storage jest używany w systemie Windows 10, Linux lub macOS. Jest to ze względu na ograniczenia w bibliotece usługi AAD używane przez Eksploratora usługi Storage.

## <a name="mac-keychain-errors"></a>Błędy pęku kluczy komputera Mac.

Systemu macOS pęku kluczy czasami może przejść do stanu, który powoduje, że problemy z biblioteki uwierzytelniania usługi Storage Explorer. Aby uzyskiwać dostęp do pęku kluczy najważniejszych ten stan, spróbuj wykonać następujące kroki:

1. Zamknij Eksploratora usługi Storage.
2. Otwórz łańcucha kluczy (**cmd + spacja**, wpisz w pęku kluczy, trafienia wprowadź).
3. Wybierz łańcucha kluczy "Logowanie".
4. Kliknij ikonę kłódki, aby zablokować łańcucha kluczy (kłódki będzie animować położenie zablokowany, po zakończeniu może potrwać kilka sekund w zależności od aplikacji, które należy otworzyć).

    ![image](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Uruchom Eksploratora usługi Storage.
6. Okno podręczne powinna pojawić się informacją o tym rodzaju "Centrum usługi chce uzyskać dostęp łańcucha kluczy". Po jego, wprowadź hasło konta administratora Mac i kliknięciu **zawsze Zezwalaj na** (lub **Zezwalaj** Jeśli **zawsze Zezwalaj na** nie jest dostępna).
7. Spróbuj zalogować się.

### <a name="general-sign-in-troubleshooting-steps"></a>Ogólne logowania kroki rozwiązywania problemów

* Jeśli jesteś w systemie macOS, i za pośrednictwem "Oczekiwanie na uwierzytelnianie..." nigdy nie zostanie wyświetlone okno logowania okno dialogowe, spróbuj [następujące kroki](#mac-keychain-errors)
* Uruchom program Storage Explorer
* Jeśli okno uwierzytelniania jest pusty, zaczekaj co najmniej jedną minutę przed zamknięciem okna dialogowego uwierzytelniania.
* Upewnij się, że usługi serwera proxy i certyfikatu, że ustawienia są poprawnie skonfigurowane dla komputera i Eksploratora usługi Storage.
* Jeśli jesteś na Windows i mieć dostęp do programu Visual Studio 2019 r na tym samym komputerze i zaloguj się, spróbuj logowanie do programu Visual Studio 2019 r. Po pomyślnym zalogowaniu do programu Visual Studio 2019 r możesz otworzyć Eksploratora usługi Storage i wyświetlenia ekranu konto użytkownika w panelu konta.

Jeśli żadna z tych metod działa [Otwórz problem w serwisie GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

### <a name="missing-subscriptions-and-broken-tenants"></a>Brak subskrypcji i dzierżaw w uszkodzona

Jeśli nie można pobrać subskrypcji po pomyślnym zalogowaniu, wypróbuj poniższe metody rozwiązywania problemów:

* Sprawdź, czy Twoje konto ma dostęp do subskrypcji, których oczekujesz. Możesz sprawdzić dostęp, logując się do portalu dla środowiska platformy Azure, którą próbujesz użyć.
* Upewnij się, że zalogowaniu przy użyciu Azure poprawne środowiska (Azure, Azure China 21Vianet, Azure (Niemcy), dla administracji USA lub środowisko niestandardowe).
* Jeśli znajdujesz się za serwerem proxy, upewnij się, że serwer proxy Eksploratora usługi Storage zostały skonfigurowane prawidłowo.
* Spróbuj usunąć i ponownie dodać konto.
* Jeśli łącze "Informacje dodatkowe", przejrzyj i zobacz, jakie komunikaty o błędach są zgłaszane w przypadku dzierżaw, które kończą się niepowodzeniem. Jeśli you'ren t się, że co należy zrobić z powodu błędu wiadomości możesz znaleźć, a następnie możesz [Otwórz problem w serwisie GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="cant-remove-attached-account-or-storage-resource"></a>Nie można usunąć dołączonych zasobów lub konta usługi storage

Jeśli nie możesz usunąć konta dołączonych lub zasób magazynu za pośrednictwem interfejsu użytkownika, należy ręcznie usunąć wszystkie dołączone zasoby, usuwając następujące foldery:

* Windows: `%AppData%/StorageExplorer`
* macOS: `/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux: `~/.config/StorageExplorer`

> [!NOTE]
> Zamknij Eksploratora usługi Storage przed usunięciem powyżej folderów.

> [!NOTE]
> Jeśli nigdy nie zaimportowano żadnych certyfikatów SSL, następnie utworzyć kopię zapasową zawartości `certs` katalogu. Później można użyć kopii zapasowej, aby ponowne zaimportować certyfikaty SSL.

## <a name="proxy-issues"></a>Problemy dotyczące serwera proxy

Najpierw upewnij się, że następujące wprowadzone informacje są poprawne:

* Adres URL serwera proxy i numer portu
* Nazwa użytkownika i hasło, jeśli jest to wymagane przez serwer proxy

> [!NOTE]
> Eksplorator usługi Storage nie obsługuje plików automatyczna konfiguracja serwera proxy do konfigurowania ustawień serwera proxy.

### <a name="common-solutions"></a>Typowe rozwiązania

Jeśli nadal występują problemy, wypróbuj poniższe metody rozwiązywania problemów:

* Jeżeli może połączyć się z Internetem, bez użycia serwera proxy, sprawdź, czy Eksploratora usługi Storage działa bez ustawienia serwera proxy włączony. Jeśli jest to możliwe, może to być problem z ustawieniami serwera proxy. Współpracować z administratorem serwera proxy, aby zidentyfikować problemy.
* Sprawdź, czy inne aplikacje korzystające z serwera proxy działają zgodnie z oczekiwaniami.
* Sprawdź, czy możesz nawiązać połączenie portal dla środowiska platformy Azure, którą próbujesz użyć
* Upewnij się, że można odbierać odpowiedzi z punktów końcowych usługi. Wprowadź swoje adresy URL punktu końcowego w przeglądarce. Jeśli można się połączyć, powinna pojawić się InvalidQueryParameterValue lub podobne odpowiedzi XML.
* Jeśli ktoś inny się również za pomocą Eksploratora usługi Storage z serwerem proxy, sprawdź, czy można połączyć. Jeśli można nawiązać połączenie, może być konieczne skontaktuj się z administratorem serwera proxy

### <a name="tools-for-diagnosing-issues"></a>Narzędzia do diagnozowania problemów

Jeśli masz sieci narzędzi, takich jak program Fiddler for Windows można diagnozować problemy w następujący sposób:

* Jeśli trzeba pracować za pośrednictwem serwera proxy, może być konieczne konfigurowanie sieci narzędziem do łączenia za pośrednictwem serwera proxy.
* Sprawdź numer portu używanego przez narzędzie do sieci.
* Wprowadź adres URL hosta lokalnego i numer portu sieci narzędzie zgodnie z ustawieniami serwera proxy w Eksploratorze usługi Storage. Jeśli zostaną prawidłowo wykonane, rozpoczyna się narzędziem sieci, rejestrowanie żądań sieciowych podjęte przez Eksploratora usługi Storage w celu zarządzania i punktów końcowych usługi. Na przykład, wprowadź https://cawablobgrs.blob.core.windows.net/ dla punktu końcowego usługi blob, w przeglądarce, a otrzymasz odpowiedź podobne do następujących, co sugeruje istniejący zasób, mimo że nie można do niego dostęp.

![Przykładowy kod](./media/storage-explorer-troubleshooting/4022502_en_2.png)

### <a name="contact-proxy-server-admin"></a>Skontaktuj się z administratorem serwera proxy

Jeśli ustawienia serwera proxy są poprawne, należy skontaktować się z administratorem serwera proxy i

* Upewnij się, że Twój serwer proxy nie blokuje ruchu skierowanego do punkty końcowe platformy Azure, zarządzania lub zasobu.
* Sprawdź, protokół uwierzytelniania używany przez serwer proxy. Eksplorator usługi Storage obecnie nie obsługuje serwerów proxy NTLM.

## <a name="unable-to-retrieve-children-error-message"></a>Komunikat o błędzie "Nie można pobrać elementów podrzędnych"

Jeśli masz połączenie na platformie Azure za pośrednictwem serwera proxy, sprawdź, czy ustawienia serwera proxy są poprawne. Jeśli masz uprawnienia dostępu do zasobu, od właściciela subskrypcji lub konta, sprawdź, czy po ich przeczytaniu lub listy uprawnień dla tego zasobu.

## <a name="connection-string-doesnt-have-complete-configuration-settings"></a>Parametry połączenia nie mają pełną ustawień konfiguracji

Jeśli zostanie wyświetlony ten komunikat o błędzie, jest to możliwe, że nie masz wymaganych uprawnień do uzyskania kluczy dla konta magazynu. Aby upewnić się, jeśli jest to możliwe, przejdź do portalu, a następnie zlokalizuj konto magazynu. To zrobić szybko prawym przyciskiem myszy węzeł konta usługi Storage, a następnie klikając polecenie "Otwórz w Portal". Po wykonaniu tej czynności, przejdź do bloku "Klucze dostępu". Jeśli nie masz uprawnień do wyświetlania kluczy, zostanie wyświetlona strona, z komunikatem "Nie mają dostępu". Aby obejść ten problem, można uzyskać klucz konta od kogoś innego i dołączyć nazwą i kluczem, lub może być poproszenie o sygnatury dostępu Współdzielonego do konta magazynu i umożliwia dołączanie konta magazynu.

Jeśli widzisz kluczy konta pliku problemu w serwisie GitHub, dzięki czemu możemy Ci pomóc rozwiązać ten problem.

## <a name="issues-with-sas-url"></a>Problemy związane z adresu URL sygnatury dostępu Współdzielonego

Jeśli łączysz się z usługą przy użyciu adresu URL sygnatury dostępu Współdzielonego i występuje ten błąd:

* Upewnij się, że adres URL zawiera niezbędne uprawnienia do odczytu lub wyświetla listę zasobów.
* Upewnij się, że adres URL nie wygasł.
* Adres URL sygnatury dostępu Współdzielonego zależy od zasad dostępu, sprawdź, czy zasady dostępu nie został odwołany.

Jeśli przypadkowo dołączany przy użyciu nieprawidłowego adresu URL sygnatury dostępu Współdzielonego i nie można odłączyć, wykonaj następujące kroki:

1. Podczas uruchamiania programu Storage Explorer, naciśnij klawisz F12, aby otworzyć okno narzędzi programistycznych.
2. Kliknij kartę aplikacji, a następnie kliknij pozycję Magazyn lokalny > file:// w drzewie po lewej stronie.
3. Znajdź klucz skojarzony z typem usługi problematyczne identyfikatora URI sygnatury dostępu Współdzielonego. Na przykład, jeśli nieprawidłowego identyfikatora URI połączenia SAS dla kontenera obiektów blob, poszukaj klucza o nazwie `StorageExplorer_AddStorageServiceSAS_v1_blob`.
4. Wartość klucza powinna być tablicą JSON. Znajdź obiekt skojarzony z nieprawidłowy identyfikator URI i usuń go.
5. Naciśnij klawisze Ctrl + R, aby ponownie załadować Eksploratora usługi Storage.

## <a name="linux-dependencies"></a>Zależności systemu Linux

Ogólnie rzecz biorąc następujące pakiety są wymagane do uruchamiania Eksploratora usługi Storage w systemie Linux:

* [.NET core 2.0 środowisko uruchomieniowe](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x) Uwaga: Eksplorator usługi Storage w wersji 1.7.0 i wcześniej wymaga platformy .NET Core 2.0. Jeśli masz nowszej wersji programu .NET Core zainstalowane następnie musisz stosowanie poprawek do programu Storage Explorer (patrz poniżej). Jeśli korzystasz z programu Storage Explorer 1.8.0 lub większą, następnie należy umożliwia platformy .NET Core 2.2. Do pracy w tej chwili nie został zweryfikowany wersji starszych niż 2.2.
* `libgnome-keyring-common` i `libgnome-keyring-dev`
* `libgconf-2-4`

W zależności od Twojej dystrybucji może różnić się lub więcej pakietów należy zainstalować.

Eksplorator usługi Storage jest oficjalnie obsługiwany na naciśnięcie i przytrzymanie 18.04 Ubuntu 16.04 i 14.04. Kroki instalacji wyczyść maszyn są następujące:

# <a name="ubuntu-1804tab1804"></a>[Ubuntu 18.04](#tab/1804)

1. Pobierz bezpłatnie Eksplorator magazynu
2. Zainstaluj środowisko uruchomieniowe programu .NET Core, najbardziej aktualną wersję zweryfikowaną: [2.0.8](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu18-04/runtime-2.0.8) (Jeśli użytkownik jest już zainstalowana nowsza wersja, konieczne może być stosowanie poprawek do Eksploratora usługi Storage, zobacz poniżej)
3. Uruchom polecenie `sudo apt-get install libgconf-2-4`
4. Uruchom polecenie `sudo apt install libgnome-keyring-common libgnome-keyring-dev`

# <a name="ubuntu-1604tab1604"></a>[Ubuntu 16.04](#tab/1604)

1. Pobierz bezpłatnie Eksplorator magazynu
2. Zainstaluj środowisko uruchomieniowe programu .NET Core, najbardziej aktualną wersję zweryfikowaną: [2.0.8](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu16-04/runtime-2.0.8) (Jeśli użytkownik jest już zainstalowana nowsza wersja, konieczne może być stosowanie poprawek do Eksploratora usługi Storage, zobacz poniżej)
3. Uruchom polecenie `sudo apt install libgnome-keyring-dev`

# <a name="ubuntu-1404tab1404"></a>[Ubuntu 14.04](#tab/1404)

1. Pobierz bezpłatnie Eksplorator magazynu
2. Zainstaluj środowisko uruchomieniowe programu .NET Core, najbardziej aktualną wersję zweryfikowaną: [2.0.8](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu14-04/runtime-2.0.8) (Jeśli użytkownik jest już zainstalowana nowsza wersja, konieczne może być stosowanie poprawek do Eksploratora usługi Storage, zobacz poniżej)
3. Uruchom polecenie `sudo apt install libgnome-keyring-dev`

---

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>Stosowanie poprawek Eksploratora usługi Storage dla nowszych wersji programu .NET Core 
Jeśli masz wersję platformy .NET Core, większa niż 2.0 zainstalowana i uruchomiona wersja programu Storage Explorer 1.7.0 lub starszej, najprawdopodobniej konieczne będzie patch Eksploratora usługi Storage, wykonując następujące czynności:
1. Pobierz wersję 1.5.43 StreamJsonRpc [z pakietów nuget](https://www.nuget.org/packages/StreamJsonRpc/1.5.43). Poszukaj linku "Pobierz" w prawej części strony.
2. Po pobraniu pakietu, zmień rozszerzenie pliku z `.nupkg` do `.zip`
3. Rozpakuj pakiet
4. Przejdź do strony `streamjsonrpc.1.5.43/lib/netstandard1.1/`
5. Kopiuj `StreamJsonRpc.dll` w następujących lokalizacjach znajdujące się w folderze Eksploratora usługi Storage:
    1. `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
    2. `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-azure-portal-doesnt-work"></a>Otwórz w Eksploratorze z witryny Azure portal nie działa

Jeśli przycisk "Otwórz w Eksploratorze" w witrynie Azure portal nie działa, upewnij się, że używasz zgodnej przeglądarki. Następujące przeglądarki zostały przetestowane na zgodność.
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="next-steps"></a>Kolejne kroki

Jeśli te rozwiązania nie działają, następnie [Otwórz problem w serwisie GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues). Możesz także szybki dostęp do usługi GitHub za pomocą przycisku "Zgłoś problem w usłudze GitHub" w lewym dolnym rogu.

![Opinia](./media/storage-explorer-troubleshooting/feedback-button.PNG)
