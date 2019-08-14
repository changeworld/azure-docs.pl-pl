---
title: Przewodnik rozwiązywania problemów Eksplorator usługi Azure Storage | Microsoft Docs
description: Omówienie technik debugowania dla Eksplorator usługi Azure Storage
services: virtual-machines
author: Deland-Han
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: 96a8eab57f1714eed4831bea01508e9140d1dfad
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934985"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Przewodnik rozwiązywania problemów Eksplorator usługi Azure Storage

Eksplorator usługi Microsoft Azure Storage jest aplikacją autonomiczną, która umożliwia łatwe współdziałanie z danymi usługi Azure Storage w systemach Windows, macOS i Linux. Aplikacja może łączyć się z kontami magazynu hostowanymi na platformie Azure, chmurach narodowych i Azure Stack.

Ten przewodnik zawiera podsumowanie rozwiązań typowych problemów występujących w Eksplorator usługi Storage.

## <a name="role-based-access-control-permission-issues"></a>Problemy z uprawnieniami Access Control opartych na rolach

[Kontrola dostępu oparta na rolach (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) zapewnia precyzyjne zarządzanie dostępem do zasobów platformy Azure przez łączenie zestawów uprawnień w _role_. Poniżej przedstawiono kilka sugestii, które można wykonać w celu uzyskania dostępu RBAC w Eksplorator usługi Storage.

### <a name="what-do-i-need-to-see-my-resources-in-storage-explorer"></a>Co jest potrzebne, aby zobaczyć moje zasoby w Eksplorator usługi Storage?

Jeśli masz problemy z uzyskaniem dostępu do zasobów magazynu przy użyciu RBAC, może to być spowodowane tym, że nie zostały przypisane odpowiednie role. W poniższych sekcjach opisano uprawnienia Eksplorator usługi Storage obecnie wymagane w celu uzyskania dostępu do zasobów magazynu.

Jeśli nie masz pewności, czy masz odpowiednie role lub uprawnienia, skontaktuj się z administratorem konta platformy Azure.

#### <a name="read-listget-storage-accounts"></a>Przeczytaj Wyświetl/pobierz konta magazynu

Musisz mieć uprawnienia do wyświetlania listy kont magazynu. Uprawnienie to można uzyskać przez przypisaną rolę "czytelnik".

#### <a name="list-storage-account-keys"></a>Wyświetl klucze konta magazynu

Do uwierzytelniania żądań Eksplorator usługi Storage można także użyć kluczy konta. Możesz uzyskać dostęp do kluczy z bardziej wydajnymi rolami, takimi jak rola "Współautor".

> [!NOTE]
> Klucze dostępu udzielają nieograniczonych uprawnień osobom, które je przechowują. W związku z tym zazwyczaj nie zaleca się ich przekazanie do użytkowników konta. Jeśli musisz odwołać klucze dostępu, możesz je wygenerować ponownie w [witrynie Azure Portal](https://portal.azure.com/).

#### <a name="data-roles"></a>Role danych

Musisz mieć przypisaną co najmniej jedną rolę, która przyznaje dostęp do odczytu danych z zasobów. Jeśli na przykład chcesz wyświetlić lub pobrać obiekty blob, musisz mieć co najmniej rolę "czytnik danych obiektów blob magazynu".

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>Dlaczego do wyświetlania moich zasobów w Eksplorator usługi Storage jest potrzebna rola warstwy zarządzania?

Usługa Azure Storage ma dwie warstwy dostępu: _Zarządzanie_ i _dane_. Do subskrypcji i kont magazynu uzyskuje się dostęp za pomocą warstwy zarządzania. Do kontenerów, obiektów blob i innych zasobów danych uzyskuje się dostęp za pomocą warstwy danych. Jeśli na przykład chcesz uzyskać listę kont magazynu z platformy Azure, Wyślij żądanie do punktu końcowego zarządzania. Jeśli potrzebujesz listy kontenerów obiektów BLOB na koncie, Wyślij żądanie do odpowiedniego punktu końcowego usługi.

Role RBAC mogą zawierać uprawnienia do zarządzania lub dostępu do warstwy danych. Na przykład rola "czytelnik" przyznaje zasoby warstwy zarządzania dostępem tylko do odczytu.

Mówiąc ściślej, rola "czytelnik" nie zapewnia żadnych uprawnień do warstwy danych i nie jest konieczna do uzyskania dostępu do warstwy danych.

Eksplorator usługi Storage ułatwia uzyskiwanie dostępu do zasobów, zbierając informacje niezbędne do nawiązania połączenia z zasobami platformy Azure. Na przykład, aby wyświetlić kontenery obiektów blob, Eksplorator usługi Storage wysyła żądanie listy kontenerów do punktu końcowego usługi BLOB Service. Aby uzyskać ten punkt końcowy, Eksplorator usługi Storage przeszukuje listę subskrypcji i kont magazynu, do których masz dostęp. Jednak aby znaleźć subskrypcje i konta magazynu, Eksplorator usługi Storage wymaga również dostępu do warstwy zarządzania.

Jeśli nie masz roli przyznającej uprawnienia do warstwy zarządzania, Eksplorator usługi Storage nie może pobrać informacji potrzebnych do nawiązania połączenia z warstwą danych.

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>Co zrobić, jeśli nie mogę uzyskać wymaganych uprawnień do warstwy zarządzania z mojego administratora?

W tej chwili nie mamy jeszcze rozwiązania dotyczącego kontroli RBAC. Obejście tego problemu pozwala na zażądanie identyfikatora URI sygnatury dostępu współdzielonego w celu dołączenia [do zasobu](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux#use-a-sas-uri).

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Błąd: Certyfikat z podpisem własnym w łańcuchu certyfikatów (i podobne błędy)

Błędy certyfikatów są spowodowane jedną z dwóch następujących sytuacji:

1. Aplikacja jest połączona za pośrednictwem "przezroczystego serwera proxy", co oznacza, że serwer (na przykład serwer firmy) przechwytuje ruch HTTPS, odszyfrowuje go, a następnie szyfruje przy użyciu certyfikatu z podpisem własnym.
2. Korzystasz z aplikacji, która wprowadza certyfikat SSL z podpisem własnym do odbieranych komunikatów HTTPS. Przykłady aplikacji, które wprowadzają certyfikaty, obejmują oprogramowanie chroniące przed wirusami i ruchem sieciowym.

Gdy Eksplorator usługi Storage widzi certyfikat z podpisem własnym lub niezaufany, nie może już wiedzieć, czy odebrany komunikat HTTPS został zmieniony. Jeśli masz kopię certyfikatu z podpisem własnym, możesz wydać Eksplorator usługi Storage zaufania, wykonując następujące czynności:

1. Uzyskaj kopię certyfikatu X. 509 z kodowaniem Base-64 (. cer)
2. Kliknij pozycję **Edytuj** > **Certyfikaty** > SSL**Importuj certyfikaty**, a następnie użyj selektora plików, aby znaleźć, wybrać i otworzyć plik CER.

Ten problem może być również wynikiem wielu certyfikatów (głównych i pośrednich). Aby można było przezwyciężyć błąd, należy dodać oba certyfikaty.

Jeśli nie masz pewności, skąd pochodzi certyfikat, możesz wykonać następujące kroki, aby je znaleźć:

1. Zainstaluj protokół Open SSL
    * [System Windows](https://slproweb.com/products/Win32OpenSSL.html) (dowolna wersja oświetlenia powinna być wystarczająca)
    * Komputery Mac i Linux: powinny być dołączone do systemu operacyjnego
2. Uruchom protokół Open SSL
    * Windows: Otwórz katalog instalacji, kliknij pozycję **/bin/** , a następnie kliknij dwukrotnie **plik OpenSSL. exe**.
    * Mac i Linux: uruchamianie **OpenSSL** z terminalu.
3. Wykonaj polecenie `s_client -showcerts -connect microsoft.com:443`
4. Wyszukaj certyfikaty z podpisem własnym. Jeśli nie masz pewności, które certyfikaty są z podpisem własnym, sprawdź, czy każdy temat `("s:")` i wystawca `("i:")` są takie same.
5. Po znalezieniu wszystkich certyfikatów z podpisem własnym dla każdej z nich skopiuj i Wklej wszystkie elementy z i, w tym **-----Rozpocznij certyfikat-----** , aby **-----końcowy-----certyfikatów** do nowego pliku. cer.
6. Otwórz Eksplorator usługi Storage, kliknij przycisk **Edytuj** > **Certyfikaty** > SSL**Importuj certyfikaty**, a następnie użyj selektora plików, aby znaleźć, wybrać i otworzyć utworzone pliki CER.

Jeśli nie możesz znaleźć żadnych certyfikatów z podpisem własnym za pomocą powyższych kroków, skontaktuj się z nami za pomocą narzędzia opinii, aby uzyskać pomoc. Możesz również uruchomić Eksplorator usługi Storage z wiersza polecenia z `--ignore-certificate-errors` flagą. Po uruchomieniu z tą flagą Eksplorator usługi Storage zignoruje Błędy certyfikatów.

## <a name="sign-in-issues"></a>Problemy dotyczące logowania

### <a name="blank-sign-in-dialog"></a>Puste okno dialogowe logowania

Puste okna dialogowe logowania są najczęściej spowodowane przez usługi AD FS z prośbą o Eksplorator usługi Storage przekierowania, które nie są obsługiwane przez elektron. Aby obejść ten problem, możesz spróbować użyć przepływu kodu urządzenia do logowania. Aby to zrobić, wykonaj następujące czynności:

1. DodajMenu Wersja zapoznawcza — > "Użyj logowania przy użyciu kodu urządzenia".
2. Otwórz okno dialogowe Połącz (za pomocą ikony wtyczki na lewym pasku pionowym lub "Dodaj konto" w panelu konta).
3. Wybierz środowisko, do którego chcesz się zalogować.
4. Kliknij przycisk "Zaloguj się".
5. Postępuj zgodnie z instrukcjami wyświetlanymi na następnym panelu.

Jeśli masz problemy z zalogowaniem się do konta, którego chcesz użyć, ponieważ domyślna przeglądarka została już zapisana na innym koncie, możesz:

1. Ręcznie skopiuj link i kod do prywatnej sesji przeglądarki.
2. Ręcznie skopiuj link i kod do innej przeglądarki.

### <a name="reauthentication-loop-or-upn-change"></a>Pętla ponownego uwierzytelniania lub zmiana nazwy UPN

Jeśli jesteś w pętli ponownego uwierzytelniania lub zmieniono nazwę UPN jednego z kont, spróbuj wykonać następujące czynności:

1. Usuń wszystkie konta, a następnie zamknij Eksplorator usługi Storage
2. Usuń. IdentityService z komputera. W systemie Windows folder znajduje się w `C:\users\<username>\AppData\Local`folderze. W przypadku systemów Mac i Linux można znaleźć folder w katalogu głównym katalogu użytkownika.
3. Jeśli jesteś w systemie Mac lub Linux, musisz również usunąć wpis Microsoft. developer. IdentityService z magazynu kluczy systemu operacyjnego. Na komputerach Mac magazyn kluczy jest aplikacją "GNOME pęku". W przypadku systemu Linux aplikacja jest zazwyczaj nazywana "pęku kluczy", ale nazwa może się różnić w zależności od dystrybucji.

### <a name="conditional-access"></a>Dostęp warunkowy

Dostęp warunkowy nie jest obsługiwany, gdy Eksplorator usługi Storage jest używany w systemie Windows 10, Linux lub macOS. Wynika to z ograniczenia w bibliotece usługi AAD używanej przez Eksplorator usługi Storage.

## <a name="mac-keychain-errors"></a>Błędy łańcucha kluczy Mac

MacOS pęku kluczy może czasami przejść w stan, który powoduje problemy z biblioteką uwierzytelniania Eksplorator usługi Storage. Aby uzyskać dostęp do łańcucha kluczy z tego stanu, spróbuj wykonać następujące czynności:

1. Zamknij Eksplorator usługi Storage.
2. Otwórz łańcuch kluczy (**cmd + spacja**, wpisz w pęku kluczy, naciśnij klawisz ENTER).
3. Wybierz łańcuch kluczy logowania.
4. Kliknij ikonę kłódki, aby zablokować łańcuch łańcucha (kłódka zostanie podzielna na zablokowaną pozycję po zakończeniu, może upłynąć kilka sekund w zależności od tego, jakie aplikacje zostały otwarte).

    ![image](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Uruchom Eksplorator usługi Storage.
6. Wyskakujące okienko powinno wyglądać podobnie do tego, jak "centrum usług chce uzyskać dostęp do łańcucha kluczy". W takim przypadku wprowadź hasło konta administratora dla komputerów Mac i kliknij opcję **zawsze Zezwalaj** (lub **Zezwalaj** , jeśli **zawsze Zezwalaj** jest niedostępne).
7. Spróbuj się zalogować.

### <a name="general-sign-in-troubleshooting-steps"></a>Ogólne kroki rozwiązywania problemów z logowaniem

* Jeśli jesteś w macOS, a okno logowania nigdy nie pojawia się w ciągu "Oczekiwanie na uwierzytelnienie..." , a następnie spróbuj wykonać [następujące czynności](#mac-keychain-errors)
* Uruchom ponownie Eksplorator usługi Storage
* Jeśli okno uwierzytelniania jest puste, odczekaj co najmniej jedną minutę przed zamknięciem okna dialogowego uwierzytelnianie.
* Upewnij się, że ustawienia serwera proxy i certyfikatu zostały prawidłowo skonfigurowane dla komputera i Eksplorator usługi Storage.
* Jeśli jesteś w systemie Windows i masz dostęp do programu Visual Studio 2019 na tym samym komputerze i zaloguj się, spróbuj zalogować się do programu Visual Studio 2019. Po pomyślnym zalogowaniu się do programu Visual Studio 2019 możesz otworzyć Eksplorator usługi Storage i zobaczyć swoje konto w panelu konta.

Jeśli żadna z tych metod nie zadziałała [, należy otworzyć problem w serwisie GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

### <a name="missing-subscriptions-and-broken-tenants"></a>Brakujące subskrypcje i uszkodzone dzierżawy

Jeśli nie możesz pobrać subskrypcji po pomyślnym zalogowaniu, wypróbuj następujące metody rozwiązywania problemów:

* Sprawdź, czy Twoje konto ma dostęp do oczekiwanych subskrypcji. Możesz sprawdzić dostęp, logując się do portalu dla środowiska platformy Azure, którego próbujesz użyć.
* Upewnij się, że zalogowano się przy użyciu poprawnego środowiska platformy Azure (platformy Azure, platformy Azure z Chin, platformy Azure (Niemcy, platformy Azure dla instytucji rządowych USA lub środowiska niestandardowego).
* Jeśli jesteś za serwerem proxy, upewnij się, że poprawnie skonfigurowano serwer proxy Eksplorator usługi Storage.
* Spróbuj usunąć i ponownie dodać konto.
* Jeśli istnieje link "więcej informacji", sprawdź i sprawdź, jakie komunikaty o błędach są raportowane dla dzierżawców, które kończą się niepowodzeniem. Jeśli you'ren't się, co należy zrobić z wyświetlonymi komunikatami o błędach, możesz [otworzyć problem w usłudze GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="cant-remove-attached-account-or-storage-resource"></a>Nie można usunąć dołączonego konta lub zasobu magazynu

Jeśli nie możesz usunąć dołączonego konta lub zasobu magazynu za pomocą interfejsu użytkownika, możesz ręcznie usunąć wszystkie dołączone zasoby, usuwając następujące foldery:

* Windows: `%AppData%/StorageExplorer`
* macOS: `/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux: `~/.config/StorageExplorer`

> [!NOTE]
> Zamknij Eksplorator usługi Storage przed usunięciem powyższych folderów.

> [!NOTE]
> Jeśli kiedykolwiek zaimportowano wszystkie certyfikaty SSL, Utwórz kopię zapasową `certs` zawartości katalogu. Później można użyć kopii zapasowej do zaimportowania certyfikatów SSL.

## <a name="proxy-issues"></a>Problemy z serwerem proxy

Najpierw upewnij się, że podane zostały poprawne następujące informacje:

* Adres URL serwera proxy i numer portu
* Nazwa użytkownika i hasło, jeśli jest to wymagane przez serwer proxy

> [!NOTE]
> Eksplorator usługi Storage nie obsługuje plików autokonfiguracji serwera proxy na potrzeby konfigurowania ustawień serwera proxy.

### <a name="common-solutions"></a>Typowe rozwiązania

Jeśli nadal występują problemy, spróbuj wykonać następujące metody rozwiązywania problemów:

* Jeśli możesz nawiązać połączenie z Internetem bez użycia serwera proxy, sprawdź, czy Eksplorator usługi Storage działa bez włączonych ustawień serwera proxy. W takim przypadku może wystąpić problem z ustawieniami serwera proxy. Skontaktuj się z administratorem serwera proxy, aby zidentyfikować problemy.
* Sprawdź, czy inne aplikacje korzystające z serwera proxy działają zgodnie z oczekiwaniami.
* Sprawdź, czy możesz nawiązać połączenie z portalem dla środowiska platformy Azure, którego próbujesz użyć
* Sprawdź, czy można odbierać odpowiedzi z punktów końcowych usługi. Wprowadź jeden z adresów URL punktu końcowego do przeglądarki. Jeśli można nawiązać połączenie, należy odebrać InvalidQueryParameterValue lub podobną odpowiedź XML.
* Jeśli ktoś inny używa również Eksplorator usługi Storage z serwerem proxy, sprawdź, czy mogą się z nim połączyć. Jeśli można nawiązać połączenie, może być konieczne skontaktowanie się z administratorem serwera proxy.

### <a name="tools-for-diagnosing-issues"></a>Narzędzia do diagnozowania problemów

Jeśli masz narzędzia sieciowe, takie jak programu Fiddler for Windows, możesz zdiagnozować problemy w następujący sposób:

* Jeśli trzeba będzie korzystać z serwera proxy, może być konieczne skonfigurowanie narzędzia sieci do łączenia się z serwerem proxy.
* Sprawdź numer portu używany przez narzędzie sieciowe.
* Wprowadź adres URL hosta lokalnego i numer portu Narzędzia sieciowego jako ustawienia serwera proxy w Eksplorator usługi Storage. Po poprawnym wykonaniu tego narzędzia sieciowego rozpocznie rejestrowanie żądań sieci wysyłanych przez Eksplorator usługi Storage do punktów końcowych zarządzania i usług. Na przykład wprowadź https://cawablobgrs.blob.core.windows.net/ dla punktu końcowego obiektu BLOB w przeglądarce i otrzymasz odpowiedź podobną do następującej, która sugeruje, że zasób istnieje, chociaż nie możesz uzyskać do niego dostępu.

![przykładowy kod](./media/storage-explorer-troubleshooting/4022502_en_2.png)

### <a name="contact-proxy-server-admin"></a>Skontaktuj się z administratorem serwera proxy

Jeśli ustawienia serwera proxy są poprawne, może być konieczne skontaktowanie się z administratorem serwera proxy i

* Upewnij się, że serwer proxy nie blokuje ruchu do usługi Azure Management lub punktów końcowych zasobów.
* Sprawdź protokół uwierzytelniania używany przez serwer proxy. Eksplorator usługi Storage nie obsługuje obecnie proxy NTLM.

## <a name="unable-to-retrieve-children-error-message"></a>Komunikat o błędzie "nie można pobrać elementu podrzędnego"

Jeśli nawiązano połączenie z platformą Azure za pomocą serwera proxy, sprawdź, czy ustawienia serwera proxy są poprawne. Jeśli masz udzielony dostęp do zasobu od właściciela subskrypcji lub konta, sprawdź, czy masz uprawnienia do odczytu lub listy dla tego zasobu.

## <a name="connection-string-doesnt-have-complete-configuration-settings"></a>Parametry połączenia nie mają pełnych ustawień konfiguracji

Jeśli zostanie wyświetlony komunikat o błędzie, istnieje możliwość, że nie masz wymaganych uprawnień do uzyskania kluczy dla konta magazynu. Aby potwierdzić, że jest to przypadek, przejdź do portalu i Znajdź swoje konto magazynu. Możesz szybko to zrobić, klikając prawym przyciskiem myszy węzeł konta magazynu, a następnie klikając polecenie "Otwórz w portalu". Po wykonaniu tych czynności przejdź do bloku "klucze dostępu". Jeśli nie masz uprawnień do wyświetlania kluczy, zostanie wyświetlona strona z komunikatem "nie masz dostępu". Aby obejść ten problem, możesz uzyskać klucz konta od kogoś innego i dołączyć nazwę i klucz lub poprosił kogoś o sygnaturę dostępu współdzielonego z kontem magazynu i użyć go do dołączenia konta magazynu.

Jeśli zobaczysz klucze konta, wygeneruj problem w usłudze GitHub, aby pomóc Ci rozwiązać ten problem.

## <a name="issues-with-sas-url"></a>Problemy z adresem URL SAS

Jeśli łączysz się z usługą przy użyciu adresu URL sygnatury dostępu współdzielonego i wystąpił następujący błąd:

* Sprawdź, czy adres URL zawiera niezbędne uprawnienia do odczytu lub wyświetlania zasobów.
* Sprawdź, czy adres URL nie wygasł.
* Jeśli adres URL SAS jest oparty na zasadach dostępu, sprawdź, czy zasady dostępu nie zostały odwołane.

Jeśli przypadkowo dołączono przy użyciu nieprawidłowego adresu URL sygnatury dostępu współdzielonego i nie można odłączyć, wykonaj następujące kroki:

1. Podczas uruchamiania Eksplorator usługi Storage naciśnij klawisz F12, aby otworzyć okno narzędzia deweloperskie.
2. Kliknij kartę aplikacja, a następnie kliknij pozycję Magazyn lokalny > file://w drzewie po lewej stronie.
3. Znajdź klucz skojarzony z typem usługi problematycznego identyfikatora URI sygnatury dostępu współdzielonego. Na przykład jeśli zły identyfikator URI sygnatury dostępu współdzielonego dotyczy kontenera obiektów blob, poszukaj `StorageExplorer_AddStorageServiceSAS_v1_blob`klucza o nazwie.
4. Wartość klucza powinna być tablicą JSON. Znajdź obiekt skojarzony z nieprawidłowym identyfikatorem URI i usuń go.
5. Naciśnij klawisze CTRL + R, aby ponownie załadować Eksplorator usługi Storage.

## <a name="linux-dependencies"></a>Zależności systemu Linux

<!-- Storage Explorer 1.9.0 and later is available as a snap from the Snap Store. The Storage Explorer snap installs all of its dependencies with no extra hassle.

Storage Explorer requires the use of a password manager, which may need to be connected manually before Storage Explorer will work correctly. You can connect Storage Explorer to your system's password manager with the following command:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

You can also download the application .tar.gz file, but you'll have to install dependencies manually. -->

> [!IMPORTANT]
> Eksplorator usługi Storage, zgodnie z opisem w pobraniu elementu. tar. gz, jest obsługiwana tylko dla dystrybucji Ubuntu. Inne dystrybucje nie zostały zweryfikowane i mogą wymagać alternatywnych lub dodatkowych pakietów.

Te pakiety stanowią najczęstsze wymagania dotyczące Eksplorator usługi Storage w systemie Linux:

* [Środowisko uruchomieniowe programu .NET Core 2,0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x)
* `libgconf-2-4`
* `libgnome-keyring0` lub `libgnome-keyring-dev`
* `libgnome-keyring-common`

> [!NOTE]
> Eksplorator usługi Storage wersja 1.7.0 i wcześniejsze wymagają programu .NET Core 2,0. Jeśli masz zainstalowaną nowszą wersję programu .NET Core, musisz [poprawić Eksplorator usługi Storage](#patching-storage-explorer-for-newer-versions-of-net-core). Jeśli korzystasz z programu Eksplorator usługi Storage 1.8.0 lub nowszego, powinno być możliwe użycie do .NET Core 2,2. W tej chwili nie zweryfikowano wersji ponad 2,2.

# <a name="ubuntu-1904tab1904"></a>[Ubuntu 19,04](#tab/1904)

1. Pobierz Eksplorator usługi Storage.
2. Zainstaluj [środowisko uruchomieniowe programu .NET Core](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu19-04/runtime-current).
3. Uruchom następujące polecenie:
   ```bash
   sudo apt-get install libgconf-2-4 libgnome-keyring0
   ```

# <a name="ubuntu-1804tab1804"></a>[Ubuntu 18,04](#tab/1804)

1. Pobierz Eksplorator usługi Storage.
2. Zainstaluj [środowisko uruchomieniowe programu .NET Core](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu18-04/runtime-current).
3. Uruchom następujące polecenie:
   ```bash
   sudo apt-get install libgconf-2-4 libgnome-keyring-common libgnome-keyring0
   ```

# <a name="ubuntu-1604tab1604"></a>[Ubuntu 16,04](#tab/1604)

1. Pobierz Eksplorator usługi Storage
2. Zainstaluj [środowisko uruchomieniowe programu .NET Core](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu16-04/runtime-current).
3. Uruchom następujące polecenie:
   ```bash
   sudo apt install libgnome-keyring-dev
   ```

# <a name="ubuntu-1404tab1404"></a>[Ubuntu 14,04](#tab/1404)

1. Pobierz Eksplorator usługi Storage
2. Zainstaluj [środowisko uruchomieniowe programu .NET Core](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu14-04/runtime-current).
3. Uruchom następujące polecenie:
   ```bash
   sudo apt install libgnome-keyring-dev
   ```
---

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>Stosowanie poprawek Eksplorator usługi Storage w przypadku nowszych wersji platformy .NET Core

W przypadku Eksplorator usługi Storage 1.7.0 lub starszej może być konieczne zainstalowanie wersji programu .NET Core używanej przez Eksplorator usługi Storage.

1. Pobierz wersję 1.5.43 z StreamJsonRpc [z narzędzia NuGet](https://www.nuget.org/packages/StreamJsonRpc/1.5.43). Poszukaj linku "Pobierz pakiet" po prawej stronie strony.
2. Po pobraniu pakietu zmień jego rozszerzenie z `.nupkg` na. `.zip`
3. Rozpakuj pakiet.
4. Otwórz folder `streamjsonrpc.1.5.43/lib/netstandard1.1/`.
5. Skopiuj `StreamJsonRpc.dll` do następujących lokalizacji w folderze Eksplorator usługi Storage:
   * `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
   * `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-azure-portal-doesnt-work"></a>Otwórz w Eksploratorze z Azure Portal nie działa

Jeśli przycisk "Otwórz w Eksploratorze" na Azure Portal nie działa, upewnij się, że używasz zgodnej przeglądarki. Następujące przeglądarki zostały przetestowane pod kątem zgodności.
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="next-steps"></a>Następne kroki

Jeśli żadna z rozwiązań nie będzie dla Ciebie zadziałała, [Otwórz problem w usłudze GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues). Możesz również szybko przejść do usługi GitHub za pomocą przycisku "Zgłoś problem do serwisu GitHub" w lewym dolnym rogu.

![Opinia](./media/storage-explorer-troubleshooting/feedback-button.PNG)
