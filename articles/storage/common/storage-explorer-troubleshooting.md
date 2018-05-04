---
title: Azure przewodnik rozwiązywania problemów z Eksploratora usługi Storage | Dokumentacja firmy Microsoft
description: Omówienie dwóch debugowanie funkcji platformy Azure
services: virtual-machines
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/08/2017
ms.author: delhan
ms.openlocfilehash: f58fb5090aba3c5052d1bbdec76225d0ae50e8f2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Podręczniku rozwiązywania problemów z Eksploratora usługi Storage platformy Azure

Eksplorator magazynu Microsoft Azure jest autonomiczną aplikację, która pozwala łatwo pracować z danymi usługi Azure Storage w systemie Windows, macOS i Linux. Aplikacja może nawiązać hostowanych na Azure, National chmur i stosu Azure kont magazynu.

Ten przewodnik zawiera podsumowanie rozwiązania dla typowe problemy występujące w Eksploratorze usługi Storage.

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Błąd: Certyfikat z podpisem własnym w łańcuchu certyfikatów (i podobne błędy)

Błędy certyfikatów są spowodowane przez jedną z następujących dwóch sytuacji:

1. Aplikacja jest połączony za pośrednictwem "przezroczystego obiektu pośredniczącego", co oznacza przechwycenia ruchu HTTPS, odszyfrowywania go i następnie szyfrowania za pomocą certyfikatu z podpisem własnym serwera (takich jak serwer firmy).
2. Uruchomiono aplikację, która jest wstrzykiwania certyfikatu SSL z podpisem własnym do otrzymywanych wiadomości protokołu HTTPS. Przykładowe aplikacje, które wstrzyknąć certyfikaty obejmuje oprogramowanie do kontroli ruchu oprogramowania antywirusowego i sieci.

Eksplorator usługi Storage widzi własnym podpisem lub niezaufany certyfikat może już znać czy odebranego komunikatu protokołu HTTPS została zmieniona. Jeśli masz kopię certyfikatu z podpisem własnym, można nakazać Eksploratora usługi Storage zaufania go, wykonując następujące czynności:

1. Uzyskać kopii certyfikatu X.509 (.cer) algorytmem Base-64
2. Kliknij przycisk **Edytuj** > **certyfikaty SSL** > **importu certyfikatów**, a następnie użyj selektora plików można znaleźć, wybierz i Otwórz plik cer

Ten problem może być również wynik wiele certyfikatów (główne i pośrednie). Aby rozwiązać ten błąd, należy dodać oba certyfikaty.

Jeśli nie wiesz, z której pochodzi certyfikat, możesz spróbować te kroki, aby go znaleźć:

1. Zainstaluj protokół Open SSL

    * [Windows](https://slproweb.com/products/Win32OpenSSL.html) (wersje światła powinno wystarczyć)
    * Mac i Linux: powinien być dołączony do systemu operacyjnego
2. Uruchom protokół Open SSL

    * Windows: Otwórz katalog instalacyjny, kliknij przycisk **/bin/**, a następnie kliknij dwukrotnie **openssl.exe**.
    * Mac i Linux: Uruchom **openssl** z terminalu.
3. Wykonaj polecenie `s_client -showcerts -connect microsoft.com:443`
4. Wyszukaj certyfikaty z podpisem własnym. Jeśli nie wiesz, które są podpisem, wyszukaj dowolnym podmiot `("s:")` i Wystawca `("i:")` są takie same.
5. Po odnalezieniu wszelkie certyfikaty z podpisem własnym dla każdego z nich, skopiować i wkleić wszystko z tym **---BEGIN CERTIFICATE---** do **---END CERTIFICATE---** do nowego pliku .cer.
6. Otwórz Eksploratora usługi Storage, kliknij pozycję **Edytuj** > **certyfikaty SSL** > **importu certyfikatów**, a następnie użyj selektora plików, aby znaleźć, wybrać, i Otwieranie plików .cer, które zostały utworzone.

Jeśli nie można znaleźć żadnych certyfikatów z podpisem własnym za pomocą powyższych kroków, skontaktuj się z nami za pomocą narzędzia opinii, aby uzyskać dalszą pomoc. Alternatywnie można wybrać, czy można uruchomić z wiersza polecenia z Eksploratora usługi Storage `--ignore-certificate-errors` flagi. Gdy uruchamiana z tą flagą, Eksploratora usługi Storage zignoruje błędów certyfikatów.

## <a name="sign-in-issues"></a>Problemy z logowaniem

Jeśli nie możesz się zalogować, wypróbuj następujące metody rozwiązywania problemów:

* Ponownie uruchom Eksploratora usługi Storage
* Jeśli okno uwierzytelniania jest pusty, zaczekaj co najmniej jedną minutę przed zamknięciem okna dialogowego uwierzytelniania.
* Upewnij się, że serwer proxy, a certyfikat się, że ustawienia są poprawnie skonfigurowane dla komputera i Eksploratora usługi Storage
* Jeśli jesteś w systemie Windows i mają dostęp do programu Visual Studio 2017 na tym samym komputerze i logowania, spróbuj zalogować się do programu Visual Studio 2017 r.

Jeśli te metody nie działają [Otwórz problemu w serwisie GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="unable-to-retrieve-subscriptions"></a>Brak możliwości pobrania subskrypcji

Jeśli nie można pobrać subskrypcji, po pomyślnym zalogowaniu, wypróbuj następujące metody rozwiązywania problemów:

* Sprawdź, czy konto użytkownika ma dostęp do subskrypcji, oczekiwane. Aby sprawdzić, czy masz dostęp po zalogowaniu się do portalu dla środowiska platformy Azure, którą próbujesz użyć.
* Upewnij się, że zalogowano się za pomocą poprawne platformy Azure środowiska (Azure, chińskiej wersji platformy Azure, platformy Azure w Niemczech, Azure instytucji rządowych Stanów Zjednoczonych lub niestandardowe środowiska).
* Jeśli używasz serwera proxy, upewnij się, poprawnie skonfigurowany serwer proxy Eksploratora usługi Storage.
* Spróbuj usunąć i ponowne dodawanie konta.
* Obejrzyj konsoli narzędzi dla deweloperów (Pomoc > Narzędzia dla deweloperów przełącznika) podczas ładowania subskrypcji Eksploratora usługi Storage. Poszukaj komunikatów o błędach (czerwony) lub dowolny tekst komunikat "nie można załadować subskrypcji dzierżawcy." Jeśli widzisz komunikaty dotyczące [Otwórz problemu w serwisie GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="cannot-remove-attached-account-or-storage-resource"></a>Nie można usunąć dołączonego konta lub magazynu zasobu

Jeśli nie można usunąć konto podłączone lub zasobów magazynu za pośrednictwem interfejsu użytkownika, można ręcznie usunąć wszystkie zasoby podłączone przez usunięcie następujących folderów:

* System Windows: `%AppData%/StorageExplorer`
* macOS: `/Users/<your_name>/Library/Applicaiton Support/StorageExplorer`
* Linux: `~/.config/StorageExplorer`

> [!NOTE]
>  Zamknij Eksploratora usługi Storage przed usunięciem folderów powyżej.

> [!NOTE]
>  Jeśli kiedykolwiek zaimportowano żadnych certyfikatów SSL, wykonaj kopię zapasową zawartość `certs` katalogu. Później można użyć kopii zapasowej, aby ponownie zaimportować certyfikaty SSL.

## <a name="proxy-issues"></a>Problemy z serwera proxy

Najpierw upewnij się, że wszystkie poprawne są następujące wprowadzone informacje:

* Adres URL serwera proxy i numer portu * Nazwa użytkownika i hasło, jeśli jest to wymagane przez serwer proxy

### <a name="common-solutions"></a>Typowe rozwiązania

Jeśli nadal występują problemy, wypróbuj następujące metody rozwiązywania problemów:

* Jeśli można połączyć się przez Internet, bez użycia serwera proxy, sprawdź, czy Eksploratora usługi Storage działa bez włączone ustawienia serwera proxy. Jeśli tak jest, może to być problem z ustawieniami serwera proxy. Skontaktować się z administratorem serwera proxy, aby zidentyfikować problemy.
* Sprawdź, czy inne aplikacje korzystające z serwera proxy działają zgodnie z oczekiwaniami.
* Sprawdzić, czy można połączyć się do portalu do środowiska platformy Azure, którą próbujesz użyć
* Sprawdź, czy możesz odbierać odpowiedzi z punktami końcowymi usługi. Wprowadź jeden z adresami URL punktu końcowego w przeglądarce. Jeśli można połączyć, powinien zostać wyświetlony InvalidQueryParameterValue lub podobne odpowiedzi XML.
* Jeśli ktoś inny korzysta również Eksploratora usługi Storage z serwerem proxy, sprawdź, czy można połączyć. Jeśli można się połączyć, należy skontaktować się z administratorem serwera proxy

### <a name="tools-for-diagnosing-issues"></a>Narzędzia do diagnozowania problemów

Jeśli masz sieci narzędzi, takich jak Fiddler dla systemu Windows, można zdiagnozować problemy w następujący sposób:

* Masz pracy za pośrednictwem serwera proxy, należy skonfigurować narzędzie sieci na łączenie się za pośrednictwem serwera proxy.
* Sprawdź numer portu używanego przez narzędzie do sieci.
* Wprowadź adres URL lokalnego hosta i numer portu sieci narzędzie zgodnie z ustawieniami serwera proxy w Eksploratorze usługi Storage. Jeśli zostaną prawidłowo wykonane, narzędzie sieci uruchamia rejestrowanie żądań sieci wprowadzone przez Eksploratora usługi Storage, zarządzania i punktów końcowych usługi. Na przykład wprowadź https://cawablobgrs.blob.core.windows.net/ dla punktu końcowego obiektu blob w przeglądarce, na które będą otrzymywać odpowiedź podobny do następującego, które sugeruje zasób istnieje, mimo że nie można do niego dostęp.

![Przykładowy kod](./media/storage-explorer-troubleshooting/4022502_en_2.png)

### <a name="contact-proxy-server-admin"></a>Skontaktuj się z administratorem serwera proxy

Jeśli ustawienia serwera proxy są prawidłowe, należy skontaktować się z administratorem serwera proxy i

* Upewnij się, że serwer proxy blokuje ruch do punkty końcowe systemu Azure zarządzania lub zasobu.
* Sprawdź protokół uwierzytelniania używany przez serwer proxy. Eksplorator usługi Storage aktualnie nie obsługuje serwerów proxy NTLM.

## <a name="unable-to-retrieve-children-error-message"></a>"Nie można pobrać elementów podrzędnych" komunikat o błędzie

Jeśli na platformie Azure są połączone za pośrednictwem serwera proxy, sprawdź, czy ustawienia serwera proxy są poprawne. Jeśli od właściciela subskrypcji lub konta przyznano dostęp do zasobu, sprawdź, czy znasz, lub listę uprawnień dla tego zasobu.

### <a name="issues-with-sas-url"></a>Problemy z adresem URL SAS
Jeśli łączysz się z usługą przy użyciu adresu URL SAS i występuje błąd:

* Upewnij się, że adres URL zawiera wystarczających uprawnień do odczytu lub listy zasobów.
* Upewnij się, że adres URL nie wygasł.
* Jeśli adres URL SAS jest oparta na zasadach dostępu, sprawdź, czy zasady dostępu nie został odwołany.

Jeśli przypadkowo dołączone przy użyciu nieprawidłowego adresu URL SAS i nie można odłączyć, wykonaj następujące kroki:
1.  Podczas uruchamiania Eksploratora usługi Storage, naciśnij klawisz F12, aby otworzyć okno narzędzia developer.
2.  Kliknij kartę aplikacji, a następnie kliknij przycisk Magazyn lokalny > file:// w drzewie po lewej stronie.
3.  Znajdź klucz skojarzony z typem usługi problematyczne identyfikatora URI sygnatury dostępu Współdzielonego. Na przykład w przypadku nieprawidłowy identyfikator URI SAS dla kontenera obiektów blob, poszukaj klucza o nazwie `StorageExplorer_AddStorageServiceSAS_v1_blob`.
4.  Wartość klucza powinien być tablicy JSON. Znajdź obiektów skojarzonych z nieprawidłowy identyfikator URI i usuń go.
5.  Naciśnij klawisze Ctrl + R, aby załadować ponownie Eksploratora usługi Storage.

## <a name="linux-dependencies"></a>Zależności systemu Linux

Dla dystrybucjach systemu Linux niż Ubuntu 16.04 może być konieczne ręczne zainstalowanie niektórych zależności. Ogólnie rzecz biorąc wymagane są następujące pakiety:
* [.NET Core 2.x](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x)
* `libsecret`
* `libgconf-2-4`
* Aktualne GCC

W zależności od Twojego distro mogą istnieć inne pakiety, które należy zainstalować. Eksplorator magazynu [wersji](https://go.microsoft.com/fwlink/?LinkId=838275&clcid=0x409) zawiera etapy niektóre dystrybucjach.

## <a name="next-steps"></a>Kolejne kroki

Jeśli te rozwiązania nie działają, następnie [Otwórz problemu w serwisie GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues). Możesz również szybki dostęp do usługi GitHub przy użyciu przycisku "Zgłoś problem do usługi GitHub" w lewym dolnym rogu.

![Opinia](./media/storage-explorer-troubleshooting/feedback-button.PNG)
