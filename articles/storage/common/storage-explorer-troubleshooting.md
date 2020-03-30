---
title: Przewodnik po rozwiązywaniu problemów z eksplorujem usługi Azure Storage | Dokumenty firmy Microsoft
description: Omówienie technik debugowania dla Eksploratora usługi Azure Storage
services: storage
author: Deland-Han
manager: dcscontentpm
ms.service: storage
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: aec8048c7ef2eb0d944cdd2a863e23578f4f87e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77561684"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Przewodnik po rozwiązywaniu problemów z Eksploratorem usługi Azure Storage

Microsoft Azure Storage Explorer to autonomiczna aplikacja, która ułatwia pracę z danymi usługi Azure Storage w systemach Windows, macOS i Linux. Aplikacja może łączyć się z kontami magazynu hostowanymi na platformie Azure, chmurach krajowych i usłudze Azure Stack.

W tym przewodniku podsumowano rozwiązania problemów, które są powszechnie widoczne w Eksploratorze magazynu.

## <a name="rbac-permissions-issues"></a>Problemy z uprawnieniami RBAC

Kontrola dostępu oparta na rolach [RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview) umożliwia wysoce szczegółowe zarządzanie zasobami platformy Azure poprzez łączenie zestawów uprawnień w _role._ Oto kilka strategii, aby uzyskać RBAC działa optymalnie w Eksploratorze magazynu.

### <a name="how-do-i-access-my-resources-in-storage-explorer"></a>Jak uzyskać dostęp do zasobów w Eksploratorze magazynu?

Jeśli masz problemy z dostępem do zasobów magazynu za pośrednictwem funkcji RBAC, być może nie przypisano odpowiednich ról. W poniższych sekcjach opisano uprawnienia, które Obecnie wymaga Eksplorator magazynu w celu uzyskania dostępu do zasobów magazynu. Skontaktuj się z administratorem konta platformy Azure, jeśli nie masz pewności, że masz odpowiednie role lub uprawnienia.

#### <a name="read-listget-storage-accounts-permissions-issue"></a>Problem z uprawnieniami "Read: List/Get Storage Account(s)"

Musisz mieć uprawnienia do listy kont magazynu. Aby uzyskać to uprawnienie, należy przypisać rolę _czytelnika._

#### <a name="list-storage-account-keys"></a>Lista kluczy konta magazynu

Eksplorator magazynu może również używać kluczy kont do uwierzytelniania żądań. Dostęp do kluczy konta można uzyskać za pośrednictwem bardziej zaawansowanych ról, takich jak rola _współautora._

> [!NOTE]
> Klucze dostępu udzielają nieograniczonych uprawnień każdemu, kto je posiada. W związku z tym nie zaleca się rozdawać te klucze do użytkowników kont. Jeśli chcesz odwołać klucze dostępu, możesz ponownie wygenerować je z [witryny Azure portal](https://portal.azure.com/).

#### <a name="data-roles"></a>Role danych

Musisz mieć przypisaną co najmniej jedną rolę, która udziela dostępu do odczytu danych z zasobów. Na przykład jeśli chcesz wyświetlić listę lub pobrać obiekty BLOB, musisz mieć co najmniej rolę _czytnika danych obiektów blob magazynu._

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>Dlaczego potrzebuję roli warstwy zarządzania, aby wyświetlić moje zasoby w Eksploratorze magazynu?

Usługa Azure Storage ma dwie warstwy dostępu: _zarządzanie_ i _dane._ Subskrypcje i konta magazynu są dostępne za pośrednictwem warstwy zarządzania. Kontenery, obiekty BLOB i inne zasoby danych są dostępne za pośrednictwem warstwy danych. Na przykład jeśli chcesz uzyskać listę kont magazynu z platformy Azure, wyślij żądanie do punktu końcowego zarządzania. Jeśli chcesz listę kontenerów obiektów blob na koncie, wyślij żądanie do punktu końcowego usługi odpowiednie.

Role RBAC mogą zawierać uprawnienia do zarządzania lub dostępu do warstwy danych. Na przykład rola czytnika udziela dostępu tylko do odczytu do zasobów warstwy zarządzania.

Ściśle rzecz biorąc, reader rola nie zapewnia żadnych uprawnień warstwy danych i nie jest konieczne do uzyskania dostępu do warstwy danych.

Eksplorator magazynu ułatwia dostęp do zasobów, zbierając informacje niezbędne do nawiązania połączenia z zasobami platformy Azure. Na przykład, aby wyświetlić kontenery obiektów blob, Eksplorator magazynu wysyła żądanie "kontenery listy" do punktu końcowego usługi obiektu blob. Aby uzyskać ten punkt końcowy, Eksplorator magazynu przeszukuje listę subskrypcji i kont magazynu, do których masz dostęp. Aby znaleźć subskrypcje i konta magazynu, Eksplorator magazynu potrzebuje również dostępu do warstwy zarządzania.

Jeśli nie masz roli, która przyznaje żadnych uprawnień warstwy zarządzania, Eksplorator magazynu nie może uzyskać informacji, których potrzebuje do połączenia się z warstwą danych.

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>Co zrobić, jeśli nie mogę uzyskać potrzebnych uprawnień warstwy zarządzania od administratora?

Obecnie nie mamy rozwiązania związanego z RBAC dla tego problemu. Aby obejść ten problem, można zażądać identyfikatora URI sygnatury dostępu Współdzielonego do [dołączenia do zasobu](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux#use-a-shared-access-signature-uri).

### <a name="recommended-built-in-rbac-roles"></a>Zalecane wbudowane role RBAC

Istnieje kilka wbudowanych ról RBAC, które mogą zapewnić uprawnienia potrzebne do korzystania z Eksploratora magazynu. Niektóre z tych ról są:
- [Właściciel](/azure/role-based-access-control/built-in-roles#owner): Zarządzaj wszystkim, w tym dostępem do zasobów. **Uwaga:** ta rola daje dostęp do klucza.
- [Współautor:](/azure/role-based-access-control/built-in-roles#contributor)Zarządzaj wszystkim, z wyłączeniem dostępu do zasobów. **Uwaga:** ta rola daje dostęp do klucza.
- [Czytnik](/azure/role-based-access-control/built-in-roles#reader): Odczyt i lista zasobów.
- [Współautor konta magazynu:](/azure/role-based-access-control/built-in-roles#storage-account-contributor)Pełne zarządzanie kontami magazynu. **Uwaga:** ta rola daje dostęp do klucza.
- [Właściciel danych obiektów blob magazynu:](/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)pełny dostęp do kontenerów obiektów blob usługi Azure Storage i danych.
- [Współautor danych obiektów blob magazynu:](/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor)odczytywanie, zapisywanie i usuwanie kontenerów i obiektów BLOB usługi Azure Storage.
- [Czytnik danych obiektów blob magazynu:](/azure/role-based-access-control/built-in-roles#storage-blob-data-reader)odczytu i listy kontenerów i obiektów blob usługi Azure Storage.

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Błąd: certyfikat z podpisem własnym w łańcuchu certyfikatów (i podobne błędy)

Błędy certyfikatów zazwyczaj występują w jednej z następujących sytuacji:

- Aplikacja jest połączona za pośrednictwem _przezroczystego serwera proxy,_ co oznacza, że serwer (taki jak serwer firmowy) przechwytuje ruch HTTPS, odszyfrowuje go, a następnie szyfruje za pomocą certyfikatu z podpisem własnym.
- Używasz aplikacji, która wstrzykuje samodzielnie podpisany certyfikat SSL do otrzymywanych wiadomości HTTPS. Przykłady aplikacji, które wstrzykują certyfikaty obejmują oprogramowanie antywirusowe i sieciowe do kontroli ruchu.

Gdy Eksplorator magazynu widzi certyfikat z podpisem własnym lub niezaufany, nie wie już, czy odebrana wiadomość HTTPS została zmieniona. Jeśli masz kopię certyfikatu z podpisem własnym, możesz poinstruować Eksploratora magazynu, aby ufał mu, wykonując następujące kroki:

1. Uzyskaj zakodowaną kopię certyfikatu X.509 (.cer) zakodowaną w bazie podstawowej 64.Obtain a Base-64 encoded X.509 (.cer) copy of the certificate.
2. Przejdź do **strony Edytuj** > **certyfikaty importu certyfikatów** > **SSL,** a następnie użyj selektora plików, aby znaleźć, zaznaczyć i otworzyć plik cer.

Ten problem może również wystąpić, jeśli istnieje wiele certyfikatów (główny i pośredni). Aby naprawić ten błąd, należy dodać oba certyfikaty.

Jeśli nie masz pewności, skąd pochodzi certyfikat, wykonaj następujące czynności, aby go znaleźć:

1. Zainstaluj OpenSSL.
    * [Okna](https://slproweb.com/products/Win32OpenSSL.html): Każda z wersji świetlnych powinna być wystarczająca.
    * Mac i Linux: Powinny być dołączone do systemu operacyjnego.
2. Uruchom OpenSSL.
    * Windows: Otwórz katalog instalacji, wybierz **/bin/**, a następnie kliknij dwukrotnie **openssl.exe**.
    * Mac i Linux: Uruchom `openssl` z terminala.
3. Uruchom polecenie `s_client -showcerts -connect microsoft.com:443`.
4. Wyszukaj certyfikaty z podpisem własnym. Jeśli nie masz pewności, które certyfikaty są podpisane `("s:")` samodzielnie, `("i:")` zanotuj, gdziekolwiek temat i wystawca są takie same.
5. Po znalezieniu certyfikatów z podpisem własnym dla każdego z nich `-----BEGIN CERTIFICATE-----` `-----END CERTIFICATE-----` skopiuj i wklej wszystko od (włącznie) do nowego pliku cer.
6. Otwórz Eksploratora magazynu i przejdź do **edytowania** >  > **certyfikatów importu****certyfikatów SSL**. Następnie użyj selektora plików, aby znaleźć, zaznaczyć i otworzyć utworzone pliki cer.

Jeśli nie możesz znaleźć żadnych certyfikatów z podpisem własnym, wykonując następujące kroki, skontaktuj się z nami za pośrednictwem narzędzia opinii. Eksploratora magazynu można również otworzyć `--ignore-certificate-errors` z wiersza polecenia przy użyciu flagi. Po otwarciu z tą flagą Eksplorator magazynu ignoruje błędy certyfikatów.

## <a name="sign-in-issues"></a>Problemy dotyczące logowania

### <a name="blank-sign-in-dialog-box"></a>Puste okno dialogowe logowania

Puste okna dialogowe logowania najczęściej występują, gdy usługi federacyjne Active Directory (AD FS) monitują Eksploratora magazynu o wykonanie przekierowania, które nie jest obsługiwane przez electron. Aby obejść ten problem, możesz spróbować użyć przepływu kodu urządzenia do logowania. Aby to zrobić, wykonaj następujące kroki:

1. Na lewym pionowym pasku narzędzi otwórz okno **Ustawienia**. W Panelu Ustawienia przejdź do pozycji**Logowanie do** **aplikacji** > . Włącz **logowanie przepływu przepływu kodu urządzenia**.
2. Otwórz okno dialogowe **Łączenie** (za pomocą ikony wtyczki na pasku pionowym po lewej stronie lub wybierając pozycję **Dodaj konto** w panelu konta).
3. Wybierz środowisko, do którego chcesz się zalogować.
4. Wybierz pozycję **Zaloguj**się .
5. Postępuj zgodnie z instrukcjami na następnym panelu.

Jeśli nie możesz zalogować się na konto, którego chcesz użyć, ponieważ domyślna przeglądarka jest już zalogowana na inne konto, wykonaj jedną z następujących czynności:

- Ręcznie skopiuj link i kod do prywatnej sesji przeglądarki.
- Ręcznie skopiuj łącze i kod do innej przeglądarki.

### <a name="reauthentication-loop-or-upn-change"></a>Pętla ponownego uwierzytelniania lub zmiana upn

Jeśli jesteś w pętli ponownego uwierzytelniania lub zmieniłeś nazwę UPN jednego z kont, wykonaj następujące czynności:

1. Usuń wszystkie konta, a następnie zamknij Eksploratora magazynu.
2. Usuń plik . IdentityService folder z komputera. W systemie Windows folder `C:\users\<username>\AppData\Local`znajduje się pod adresem . W przypadku komputerów Mac i Linuksa folder można znaleźć w katalogu głównym użytkownika.
3. Jeśli korzystasz z komputera Mac lub Linux, musisz również usunąć wpis Microsoft.Developer.IdentityService z magazynu kluczy systemu operacyjnego. Na komputerze Mac magazyn kluczy jest aplikacją *Gnome Keychain.* W systemie Linux aplikacja jest zwykle nazywana _Keyring_, ale nazwa może się różnić w zależności od dystrybucji.

### <a name="conditional-access"></a>Dostęp warunkowy

Ze względu na ograniczenia w bibliotece usługi Azure AD używane przez Eksploratora magazynu, dostęp warunkowy nie jest obsługiwany, gdy Eksplorator magazynu jest używany w systemie Windows 10, Linux lub macOS.

## <a name="mac-keychain-errors"></a>Błędy pęku kluczy mac

Pęk kluczy systemu macOS może czasami wprowadzić stan, który powoduje problemy z biblioteką uwierzytelniania Eksploratora magazynu. Aby uzyskać pęk kluczy z tego stanu, wykonaj następujące kroki:

1. Zamknij Eksploratora magazynu.
2. Otwórz pęk kluczy (naciśnij klawisze Command+Spacja, wpisz **pęk kluczy**i naciśnij klawisz Enter).
3. Wybierz "login" pęk kluczy.
4. Wybierz ikonę kłódki, aby zablokować pęk kluczy. (Kłódka pojawi się zablokowana po zakończeniu procesu. Może to potrwać kilka sekund, w zależności od otwartych aplikacji).

    ![Ikona kłódki](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Otwórz Eksploratora usługi Storage.
6. Zostanie wyświetlony monit z komunikatem "Centrum usługi chce uzyskać dostęp do pęku kluczy". Wprowadź hasło konta administratora komputera Mac i wybierz pozycję **Zawsze zezwalaj** (lub **Zezwól,** jeśli **zawsze zezwalaj** nie jest dostępna).
7. Spróbuj się zalogować.

### <a name="general-sign-in-troubleshooting-steps"></a>Ogólne kroki rozwiązywania problemów z logowanie

* Jeśli korzystasz z systemu macOS, a okno logowania nigdy nie pojawia się w oknie dialogowym **Oczekiwanie na uwierzytelnienie,** spróbuj wykonać [następujące czynności](#mac-keychain-errors).
* Uruchom ponownie Eksploratora magazynu.
* Jeśli okno uwierzytelniania jest puste, odczekaj co najmniej minutę przed zamknięciem okna dialogowego uwierzytelniania.
* Upewnij się, że ustawienia serwera proxy i certyfikatu są poprawnie skonfigurowane zarówno dla komputera, jak i Eksploratora magazynu.
* Jeśli korzystasz z systemu Windows i masz dostęp do programu Visual Studio 2019 na tym samym komputerze i do poświadczeń logowania, spróbuj zalogować się do programu Visual Studio 2019. Po pomyślnym zalogowaniu się do programu Visual Studio 2019 możesz otworzyć Eksploratora magazynu i wyświetlić swoje konto w panelu konta.

Jeśli żadna z tych metod nie zadziała, [otwórz problem w usłudze GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

### <a name="missing-subscriptions-and-broken-tenants"></a>Brak subskrypcji i uszkodzonych dzierżaw

Jeśli nie możesz pobrać subskrypcji po pomyślnym zalogowaniu się, wypróbuj następujące metody rozwiązywania problemów:

* Sprawdź, czy Twoje konto ma dostęp do oczekiwanych subskrypcji. Możesz zweryfikować swój dostęp, logując się do portalu dla środowiska platformy Azure, którego próbujesz użyć.
* Upewnij się, że zalogowano się za pośrednictwem prawidłowego środowiska platformy Azure (Azure, Azure China 21Vianet, Azure Germany, Azure US Government lub Custom Environment).
* Jeśli jesteś za serwerem proxy, upewnij się, że serwer proxy Eksploratora magazynu został poprawnie skonfigurowany.
* Spróbuj usunąć i ponownie dodać konto.
* Jeśli istnieje łącze "Więcej informacji", sprawdź, które komunikaty o błędach są zgłaszane dla dzierżawców, które nie działają. Jeśli nie wiesz, jak reagować na komunikaty o błędach, [możesz otworzyć problem w usłudze GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="cant-remove-an-attached-account-or-storage-resource"></a>Nie można usunąć dołączonego konta lub zasobu magazynu

Jeśli nie można usunąć dołączonego konta lub zasobu magazynu za pośrednictwem interfejsu użytkownika, możesz ręcznie usunąć wszystkie dołączone zasoby, usuwając następujące foldery:

* Windows:`%AppData%/StorageExplorer`
* Macos:`/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux:`~/.config/StorageExplorer`

> [!NOTE]
> Zamknij Eksploratora magazynu przed usunięciem tych folderów.

> [!NOTE]
> Jeśli kiedykolwiek zaimportowano certyfikaty SSL, `certs` należy ponownie wywróć zawartość katalogu. Później można użyć kopii zapasowej, aby ponownie zaimportować certyfikaty SSL.

## <a name="proxy-issues"></a>Problemy z serwerem proxy

Najpierw upewnij się, że wprowadzone informacje są poprawne:

* Adres URL serwera proxy i numer portu
* Nazwa użytkownika i hasło, jeśli wymaga ich serwer proxy

> [!NOTE]
> Eksplorator magazynu nie obsługuje plików autokonfiguracyjnych serwerów proxy do konfigurowania ustawień serwera proxy.

### <a name="common-solutions"></a>Wspólne rozwiązania

Jeśli nadal występują problemy, wypróbuj następujące metody rozwiązywania problemów:

* Jeśli można połączyć się z Internetem bez korzystania z serwera proxy, sprawdź, czy Eksplorator magazynu działa bez włączonych ustawień serwera proxy. W takim przypadku może występować problem z ustawieniami serwera proxy. Skontaktuj się z administratorem, aby zidentyfikować problemy.
* Sprawdź, czy inne aplikacje korzystające z serwera proxy działają zgodnie z oczekiwaniami.
* Sprawdź, czy możesz połączyć się z portalem dla środowiska platformy Azure, którego próbujesz użyć.
* Sprawdź, czy można odbierać odpowiedzi z punktów końcowych usługi. Wprowadź jeden z adresów URL punktu końcowego w przeglądarce. Jeśli można połączyć, należy otrzymać InvalidQueryParameterValue lub podobną odpowiedź XML.
* Jeśli ktoś inny również używa Eksploratora magazynu z serwerem proxy, sprawdź, czy może się połączyć. Jeśli to możliwe, może być trzeba skontaktować się z administratorem serwera proxy.

### <a name="tools-for-diagnosing-issues"></a>Narzędzia do diagnozowania problemów

Jeśli masz narzędzia sieciowe, takie jak Fiddler for Windows, możesz zdiagnozować problemy w następujący sposób:

* Jeśli trzeba pracować za pośrednictwem serwera proxy, może być trzeba skonfigurować narzędzie sieciowe do łączenia się za pośrednictwem serwera proxy.
* Sprawdź numer portu używany przez narzędzie sieciowe.
* Wprowadź lokalny adres URL hosta i numer portu narzędzia sieciowego jako ustawienia serwera proxy w Eksploratorze magazynu. Po wykonaniu tej tej usługi narzędzie sieciowe rozpoczyna rejestrowanie żądań sieciowych przez Eksploratora magazynu do punktów końcowych zarządzania i usługi. Na przykład `https://cawablobgrs.blob.core.windows.net/` wprowadź punkt końcowy obiektu blob w przeglądarce, a otrzymasz odpowiedź podobną do następującej:

  ![Przykład kodu](./media/storage-explorer-troubleshooting/4022502_en_2.png)

  Ta odpowiedź sugeruje, że zasób istnieje, nawet jeśli nie można uzyskać do niego dostępu.

### <a name="contact-proxy-server-admin"></a>Skontaktuj się z administratorem serwera proxy

Jeśli ustawienia serwera proxy są poprawne, może być nawiązywać kontakt z administratorem serwera proxy w celu:

* Upewnij się, że serwer proxy nie blokuje ruchu do zarządzania platformy Azure lub punktów końcowych zasobów.
* Sprawdź protokół uwierzytelniania używany przez serwer proxy. Eksplorator magazynu nie obsługuje obecnie serwerów proxy NTLM.

## <a name="unable-to-retrieve-children-error-message"></a>Komunikat o błędzie "Nie można pobrać dzieci"

Jeśli masz połączenie z platformą Azure za pośrednictwem serwera proxy, sprawdź, czy ustawienia serwera proxy są poprawne. Jeśli masz dostęp do zasobu od właściciela subskrypcji lub konta, sprawdź, czy masz uprawnienia do odczytu lub listy dla tego zasobu.

## <a name="connection-string-doesnt-have-complete-configuration-settings"></a>Parametry połączenia nie mają pełnych ustawień konfiguracji

Jeśli zostanie wyświetlony ten komunikat o błędzie, możliwe, że nie masz uprawnień niezbędnych do uzyskania kluczy dla konta magazynu. Aby potwierdzić, że tak jest, przejdź do portalu i znajdź swoje konto magazynu. Można to zrobić, klikając prawym przyciskiem myszy węzeł dla konta magazynu i wybierając **pozycję Otwórz w portalu**. Następnie przejdź do bloku **Klucze dostępu.** Jeśli nie masz uprawnień do wyświetlania kluczy, zobaczysz komunikat "Nie masz dostępu". Aby obejść ten problem, można uzyskać klucz konta od innej osoby i dołączyć za pomocą nazwy i klucza, lub poprosić kogoś o sygnatury dostępu Współdzielonego do konta magazynu i użyć go do dołączenia konta magazynu.

Jeśli widzisz klucze konta, zgłoś problem w usłudze GitHub, abyśmy mogli pomóc Ci rozwiązać problem.

## <a name="error-occurred-while-adding-new-connection-typeerror-cannot-read-property-version-of-undefined"></a>Wystąpił błąd podczas dodawania nowego połączenia: TypeError: Nie można odczytać właściwości "version" niezdefiniowanego

Jeśli ten komunikat o błędzie jest wyświetlany podczas próby dodania połączenia niestandardowego, dane połączenia przechowywane w lokalnym menedżerze poświadczeń mogą być uszkodzone. Aby obejść ten problem, spróbuj usunąć uszkodzone połączenia lokalne, a następnie ponownie je dodać:

1. Uruchom Eksploratora magazynu. Z menu przejdź do **pozycji Pomoc** > **Przełączanie narzędzi programistycznych**.
2. W otwartym oknie na karcie **Aplikacja** przejdź do strony **Magazyn lokalny** (po lewej stronie) > **file://**.
3. W zależności od typu połączenia, z którym masz problem, poszukaj jego klucza, a następnie skopiuj jego wartość do edytora tekstu. Wartość jest tablicą niestandardowych nazw połączeń, takich jak następujące:
    * Konta magazynu
        * `StorageExplorer_CustomConnections_Accounts_v1`
    * Kontenery obiektów blob
        * `StorageExplorer_CustomConnections_Blobs_v1`
        * `StorageExplorer_CustomConnections_Blobs_v2`
    * Udziały plików
        * `StorageExplorer_CustomConnections_Files_v1`
    * Kolejki
        * `StorageExplorer_CustomConnections_Queues_v1`
    * Tabele
        * `StorageExplorer_CustomConnections_Tables_v1`
4. Po zapisaniu bieżących nazw połączeń ustaw wartość `[]`w narzędziach deweloperskich na .

Jeśli chcesz zachować połączenia, które nie są uszkodzone, możesz użyć następujących kroków, aby zlokalizować uszkodzone połączenia. Jeśli nie masz nic przeciwko utracie wszystkich istniejących połączeń, możesz pominąć te kroki i postępować zgodnie z instrukcjami dotyczącymi platformy, aby wyczyścić dane połączenia.

1. W edytorze tekstu ponownie dodaj każdą nazwę połączenia do narzędzi deweloperskich, a następnie sprawdź, czy połączenie nadal działa.
2. Jeśli połączenie działa poprawnie, nie jest uszkodzone i można je bezpiecznie zostawić. Jeśli połączenie nie działa, usuń jego wartość z narzędzi deweloperskich i zarejestruj je, aby można było dodać je z powrotem później.
3. Powtarzaj tę czynność, aż zostaną zbadane wszystkie połączenia.

Po przejściu przez wszystkie połączenia, dla wszystkich nazw połączeń, które nie są dodawane z powrotem, należy wyczyścić ich uszkodzone dane (jeśli istnieją) i dodać je z powrotem przy użyciu standardowych kroków w Eksploratorze magazynu:

# <a name="windows"></a>[Windows](#tab/Windows)

1. W menu **Start** wyszukaj **Menedżera poświadczeń** i otwórz go.
2. Przejdź do programu **Poświadczenia systemu Windows**.
3. W obszarze **Poświadczenia ogólne**poszukaj `<connection_type_key>/<corrupted_connection_name>` wpisów, `StorageExplorer_CustomConnections_Accounts_v1/account1`które mają klucz (na przykład ).
4. Usuń te wpisy i ponownie dodaj połączenia.

# <a name="macos"></a>[Macos](#tab/macOS)

1. Otwórz spotlight (Command+Spacja) i wyszukaj **dostęp do pęku kluczy**.
2. Poszukaj wpisów, które `<connection_type_key>/<corrupted_connection_name>` mają `StorageExplorer_CustomConnections_Accounts_v1/account1`klucz (na przykład ).
3. Usuń te wpisy i ponownie dodaj połączenia.

# <a name="linux"></a>[Linux](#tab/Linux)

Lokalne zarządzanie poświadczeniami różni się w zależności od dystrybucji systemu Linux. Jeśli dystrybucja systemu Linux nie zapewnia wbudowanego narzędzia gui do lokalnego zarządzania poświadczeniami, można zainstalować narzędzie innej firmy do zarządzania poświadczeniami lokalnymi. Na przykład można użyć [Seahorse](https://wiki.gnome.org/Apps/Seahorse/), narzędzia graficznego typu open source do zarządzania poświadczeniami lokalnymi systemu Linux.

1. Otwórz lokalne narzędzie do zarządzania poświadczeniami i znajdź zapisane poświadczenia.
2. Poszukaj wpisów, które `<connection_type_key>/<corrupted_connection_name>` mają `StorageExplorer_CustomConnections_Accounts_v1/account1`klucz (na przykład ).
3. Usuń te wpisy i ponownie dodaj połączenia.
---

Jeśli nadal napotkasz ten błąd po uruchomieniu tych kroków lub jeśli chcesz udostępnić to, co podejrzewasz, że uszkodzone połączenia, [otwórz problem](https://github.com/microsoft/AzureStorageExplorer/issues) na naszej stronie GitHub.

## <a name="issues-with-sas-url"></a>Problemy z adresem URL sygnatury dostępu Współdzielonego

Jeśli łączysz się z usługą za pośrednictwem adresu URL sygnatury dostępu Współdzielonego i występuje błąd:

* Sprawdź, czy adres URL zapewnia niezbędne uprawnienia do odczytu lub listy zasobów.
* Sprawdź, czy adres URL nie wygasł.
* Jeśli adres URL sygnatury dostępu współdzielonego jest oparty na zasadach dostępu, sprawdź, czy zasady dostępu nie zostały odwołane.

Jeśli przypadkowo przyłączono przy użyciu nieprawidłowego adresu URL sygnatury dostępu Współdzielonego, a teraz nie można odłączyć, wykonaj następujące kroki:

1. Gdy korzystasz z Eksploratora magazynu, naciśnij klawisz F12, aby otworzyć okno Narzędzia deweloperskie.
2. Na karcie **Aplikacja** wybierz pozycję **Magazyn** > lokalny**file://** w drzewie po lewej stronie.
3. Znajdź klucz skojarzony z typem usługi problematycznego identyfikatora URI sygnatury dostępu Współdzielonego. Na przykład jeśli zły identyfikator URI sygnatury dostępu `StorageExplorer_AddStorageServiceSAS_v1_blob`Współdzielonego jest dla kontenera obiektów blob, poszukaj klucza o nazwie .
4. Wartość klucza powinna być tablicą JSON. Znajdź obiekt skojarzony ze złym identyfikatorem URI, a następnie usuń go.
5. Naciśnij klawisze Ctrl+R, aby ponownie załadować Eksploratora magazynu.

## <a name="linux-dependencies"></a>Zależności linuksa

Explorer magazynu 1.10.0 i nowsze jest dostępny jako przystawka ze Sklepu Snap. Przyciąganie Eksploratora magazynu automatycznie instaluje wszystkie swoje zależności i jest aktualizowane, gdy dostępna jest nowa wersja przyciągania. Zainstalowanie przystawki Eksploratora magazynu jest zalecaną metodą instalacji.

Eksplorator magazynu wymaga użycia menedżera haseł, który może być konieczne do łączenia się ręcznie, zanim Eksplorator magazynu będzie działać poprawnie. Eksploratora magazynu można podłączyć do menedżera haseł systemu, uruchamiając następujące polecenie:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

Możesz również pobrać aplikację jako plik .tar.gz, ale musisz zainstalować zależności ręcznie.

> [!IMPORTANT]
> Eksplorator magazynu podany w pliku .tar.gz do pobrania jest obsługiwany tylko w przypadku dystrybucji Ubuntu. Inne dystrybucje nie zostały zweryfikowane i mogą wymagać alternatywnych lub dodatkowych pakietów.

Te pakiety są najbardziej typowe wymagania dla Eksploratora magazynu w systemie Linux:

* [Środowisko uruchomieniowe .NET Core 2.2](/dotnet/core/install/dependencies?tabs=netcore22&pivots=os-linux)
* `libgconf-2-4`
* `libgnome-keyring0` lub `libgnome-keyring-dev`
* `libgnome-keyring-common`

> [!NOTE]
> Explorer magazynu w wersji 1.7.0 i wcześniejszych wymagają .NET Core 2.0. Jeśli masz zainstalowaną nowszą wersję programu .NET Core, musisz [załatać Eksploratora magazynu](#patching-storage-explorer-for-newer-versions-of-net-core). Jeśli używasz Eksploratora magazynu 1.8.0 lub nowszego, powinieneś mieć możliwość użycia do platformy .NET Core 2.2. Wersje powyżej 2.2 nie zostały zweryfikowane do pracy w tej chwili.

# <a name="ubuntu-1904"></a>[Ubuntu 19.04](#tab/1904)

1. Pobierz Eksploratora magazynu.
2. Zainstaluj [program .NET Core Runtime](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu19-04/runtime-current).
3. Uruchom następujące polecenie:
   ```bash
   sudo apt-get install libgconf-2-4 libgnome-keyring0
   ```

# <a name="ubuntu-1804"></a>[Ubuntu 18.04](#tab/1804)

1. Pobierz Eksploratora magazynu.
2. Zainstaluj [program .NET Core Runtime](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu18-04/runtime-current).
3. Uruchom następujące polecenie:
   ```bash
   sudo apt-get install libgconf-2-4 libgnome-keyring-common libgnome-keyring0
   ```

# <a name="ubuntu-1604"></a>[Ubuntu 16.04](#tab/1604)

1. Pobierz Eksploratora magazynu.
2. Zainstaluj [program .NET Core Runtime](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu16-04/runtime-current).
3. Uruchom następujące polecenie:
   ```bash
   sudo apt install libgnome-keyring-dev
   ```

# <a name="ubuntu-1404"></a>[Ubuntu 14.04](#tab/1404)

1. Pobierz Eksploratora magazynu.
2. Zainstaluj [program .NET Core Runtime](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu14-04/runtime-current).
3. Uruchom następujące polecenie:
   ```bash
   sudo apt install libgnome-keyring-dev
   ```
---

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>Patching Storage Explorer dla nowszych wersji programu .NET Core

W przypadku Eksploratora magazynu 1.7.0 lub wcześniejszego może być załatanie wersji rdzenia .NET używanej przez Eksploratora magazynu:

1. Pobierz wersję 1.5.43 StreamJsonRpc [z NuGet](https://www.nuget.org/packages/StreamJsonRpc/1.5.43). Poszukaj linku "Pobierz pakiet" po prawej stronie strony.
2. Po pobraniu pakietu zmień jego `.nupkg` `.zip`rozszerzenie pliku z na .
3. Rozpaj paczkę.
4. Otwórz folder `streamjsonrpc.1.5.43/lib/netstandard1.1/`.
5. Skopiuj `StreamJsonRpc.dll` do następujących lokalizacji w folderze Eksplorator magazynu:
   * `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
   * `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-the-azure-portal-doesnt-work"></a>"Otwórz w Eksploratorze" z witryny Azure portal nie działa

Jeśli przycisk **Otwórz w Eksploratorze** w witrynie Azure portal nie działa, upewnij się, że używasz zgodnej przeglądarki. Pod kątem zgodności przetestowano następujące przeglądarki:
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="next-steps"></a>Następne kroki

Jeśli żadne z tych rozwiązań nie działa dla Ciebie, [otwórz problem w usłudze GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues). Można to również zrobić, wybierając przycisk **Zgłoś problem z githubem** w lewym dolnym rogu.

![Opinia](./media/storage-explorer-troubleshooting/feedback-button.PNG)
