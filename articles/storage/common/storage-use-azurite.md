---
title: Używanie emulatora Azurite do lokalnego tworzenia usługi Azure Storage
description: Emulator open source Azurite (wersja zapoznawcza) zapewnia bezpłatne środowisko lokalne do testowania aplikacji magazynu platformy Azure.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/31/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: 5e1fce0852a4e820d7ee0af626ce3fddf6773750
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76029927"
---
# <a name="use-the-azurite-emulator-for-local-azure-storage-development-and-testing-preview"></a>Użyj emulatora Azurite do lokalnego tworzenia i testowania usługi Azure Storage (wersja zapoznawcza)

Emulator open source azurite w wersji 3.2 (wersja zapoznawcza) zapewnia bezpłatne środowisko lokalne do testowania aplikacji do przechowywania obiektów blob i kolejek platformy Azure. Jeśli użytkownik jest zadowolony z lokalnego działania aplikacji, przełącz się na konto usługi Azure Storage w chmurze. Emulator zapewnia obsługę między platformami w systemach Windows, Linux i MacOS. Azurite w wersji 3 obsługuje interfejsy API zaimplementowane przez usługę Azure Blob.

Azurite to przyszła platforma emulatora pamięci masowej. Azurite zastępuje [emulator usługi Azure Storage](storage-use-emulator.md). Azurite będzie nadal aktualizowany do obsługi najnowszych wersji interfejsów API usługi Azure Storage.

Istnieje kilka różnych sposobów instalowania i uruchamiania azurite w systemie lokalnym:

  1. [Instalowanie i uruchamianie rozszerzenia kodu programu Azurite Visual Studio](#install-and-run-the-azurite-visual-studio-code-extension)
  1. [Instalowanie i uruchamianie aulitu przy użyciu usługi NPM](#install-and-run-azurite-by-using-npm)
  1. [Instalowanie i uruchamianie obrazu platformy Docker Azurite](#install-and-run-the-azurite-docker-image)
  1. [Klonuj, buduj i uruchamiaj Azurite z repozytorium GitHub](#clone-build-and-run-azurite-from-the-github-repository)

## <a name="install-and-run-the-azurite-visual-studio-code-extension"></a>Instalowanie i uruchamianie rozszerzenia kodu programu Azurite Visual Studio

W programie Visual Studio Code wybierz okienko **ROZSZERZENIA** i wyszukaj *pozycję Azurite* w **pliku EXTENSIONS:MARKETPLACE**.

![Rynek rozszerzeń kodu programu Visual Studio](media/storage-use-azurite/azurite-vs-code-extension.png)

Alternatywnie przejdź do [rynku rozszerzenia kodu VS](https://marketplace.visualstudio.com/items?itemName=Azurite.azurite) w przeglądarce. Wybierz przycisk **Zainstaluj,** aby otworzyć program Visual Studio Code i przejdź bezpośrednio do strony rozszerzenia Azurite.

Azurite można szybko uruchomić lub zamknąć, klikając **przycisk [Azurite Blob Service]** lub **[Azurite Queue Service]** na pasku stanu kodu PROGRAMU VS lub wydając następujące polecenia na palecie poleceń kodu programu VS. Aby otworzyć paletę poleceń, naciśnij **klawisz F1** w programie VS Code.

Rozszerzenie obsługuje następujące polecenia Kodu programu Visual Studio:

   * **Azurite: Start** - Uruchom wszystkie usługi Azurite
   * **Azurite: Zamknij** - Zamknij wszystkie usługi Azurite
   * **Azurite: Clean** - Resetowanie wszystkich danych trwałości usług Azurite
   * **Azurite: Uruchom usługę obiektów blob** — uruchom usługę obiektów blob
   * **Azurite: Zamknij usługę obiektów blob** — zamknij usługę obiektów blob
   * **Azurite: Clean Blob Service** - Czysta usługa obiektów blob
   * **Azurite: Uruchom usługę kolejki** — uruchom usługę kolejki
   * **Azurite: Zamknij usługę kolejki** — zamknij usługę kolejki
   * **Azurite: Usługa czystej kolejki** - czysta kolejka

Aby skonfigurować Azurite w programie Visual Studio Code, wybierz okienko rozszerzeń. Wybierz ikonę **Zarządzaj** (biegiem) dla **Azurite**. Wybierz pozycję **Konfiguruj ustawienia rozszerzenia**.

![Azurite konfiguruje ustawienia rozszerzenia](media/storage-use-azurite/azurite-configure-extension-settings.png)

Obsługiwane są następujące ustawienia:

   * **Azurite: Host obiektów blob** — punkt końcowy nasłuchiwania usługi obiektów blob. Ustawieniem domyślnym jest 127.0.0.1.
   * **Azurite: Port obiektów Blob** — port nasłuchiwania usługi obiektów blob. Domyślny port to 10000.
   * **Azurite: Debug** - Wyjście dziennika debugowania do kanału Azurite. Wartość domyślna to **fałsz**.
   * **Azurite: Lokalizacja** — ścieżka lokalizacji obszaru roboczego. Domyślnie jest folder roboczy programu Visual Studio Code.
   * **Azurite: Host kolejki** — punkt końcowy nasłuchiwania usługi kolejki. Ustawieniem domyślnym jest 127.0.0.1.
   * **Azurite: Port kolejki** — port nasłuchiwania usługi kolejki. Domyślny port to 10001.
   * **Azurite: Cichy** - Tryb cichy wyłącza dziennik dostępu. Wartość domyślna to **fałsz**.

## <a name="install-and-run-azurite-by-using-npm"></a>Instalowanie i uruchamianie aulitu przy użyciu usługi NPM

Ta metoda instalacji wymaga zainstalowania [pliku Node.js w wersji 8.0 lub nowszej.](https://nodejs.org) **npm** to narzędzie do zarządzania pakietami dołączone do każdej instalacji Node.js. Po zainstalowaniu node.js, wykonaj następujące polecenie **npm,** aby zainstalować Azurite.

```console
npm install -g azurite
```

Po zainstalowaniu Azurite zobacz [Uruchamianie Azurite z wiersza polecenia](#run-azurite-from-a-command-line).

## <a name="install-and-run-the-azurite-docker-image"></a>Instalowanie i uruchamianie obrazu platformy Docker Azurite

Użyj [usługi DockerHub,](https://hub.docker.com/) aby wyciągnąć [najnowszy obraz Azurite](https://hub.docker.com/_/microsoft-azure-storage-azurite) za pomocą następującego polecenia:

```console
docker pull mcr.microsoft.com/azure-storage/azurite
```

**Uruchom obraz platformy Docker Azurite**:

Następujące polecenie uruchamia obraz platformy Docker Azurite. Parametr `-p 10000:10000` przekierowuje żądania z portu 10000 komputera hosta do wystąpienia platformy Docker.

```console
docker run -p 10000:10000 -p 10001:10001 mcr.microsoft.com/azure-storage/azurite
```

**Określ lokalizację obszaru roboczego:**

W poniższym przykładzie `-v c:/azurite:/data` parametr określa *c:/azurite* jako utrwalone lokalizacji danych Azurite. Katalog *c:/azurite*musi zostać utworzony przed uruchomieniem polecenia Platformy Docker.

```console
docker run -p 10000:10000 -p 10001:10001 -v c:/azurite:/data mcr.microsoft.com/azure-storage/azurite
```

**Uruchom tylko usługę obiektu blob**

```console
docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite
    azurite-blob --blobHost 0.0.0.0 --blobPort 10000
```

**Ustaw wszystkie parametry Azurite:**

W tym przykładzie pokazano, jak ustawić wszystkie parametry wiersza polecenia. Wszystkie poniższe parametry powinny być umieszczone w jednym wierszu polecenia.

```console
docker run -p 8888:8888
           -p 9999:9999
           -v c:/azurite:/workspace mcr.microsoft.com/azure-storage/azurite azurite
           -l /workspace
           -d /workspace/debug.log
           --blobPort 8888
           --blobHost 0.0.0.0
           --queuePort 9999
           --queueHost 0.0.0.0
```

Aby uzyskać więcej informacji na temat konfigurowania azurite podczas uruchamiania, zobacz [Opcje wiersza](#command-line-options) polecenia.

## <a name="clone-build-and-run-azurite-from-the-github-repository"></a>Klonuj, buduj i uruchamiaj Azurite z repozytorium GitHub

Ta metoda instalacji wymaga zainstalowania [git.](https://git-scm.com/) Sklonuj [repozytorium GitHub](https://github.com/azure/azurite) dla projektu Azurite za pomocą następującego polecenia konsoli.

```console
git clone https://github.com/Azure/Azurite.git
```

Po sklonowaniu kodu źródłowego wykonaj następujące polecenia z katalogu głównego sklonowanego repozytorium, aby zbudować i zainstalować Azurite.

```console
npm install
npm run build
npm install -g
```

Po zainstalowaniu i zbudowaniu Azurite zobacz [Uruchamianie Azurite z wiersza polecenia](#run-azurite-from-a-command-line).

## <a name="run-azurite-from-a-command-line"></a>Uruchamianie Azurite z wiersza polecenia

> [!NOTE]
> Azurite nie można uruchomić z wiersza polecenia, jeśli zainstalowano tylko rozszerzenie kodu programu Visual Studio. Zamiast tego należy użyć palety poleceń kodu programu VS. Aby uzyskać więcej informacji, zobacz [Instalowanie i uruchamianie rozszerzenia kodu programu Visual Studio Azurite](#install-and-run-the-azurite-visual-studio-code-extension).

Aby natychmiast rozpocząć pracę z wierszem polecenia, utwórz katalog o nazwie **c:\azurite**, a następnie uruchom Azurite, wydając następujące polecenie:

```console
azurite --silent --location c:\azurite --debug c:\azurite\debug.log
```

To polecenie nakazuje Azurite przechowywanie wszystkich danych w danym katalogu, **c:\azurite**. Jeśli opcja **--location** zostanie pominięta, użyje bieżącego katalogu roboczego.

## <a name="command-line-options"></a>Opcje wiersza polecenia

W tej sekcji opisano przełączniki wiersza polecenia dostępne podczas uruchamiania Azurite. Wszystkie przełączniki wiersza polecenia są opcjonalne.

```console
C:\Azurite> azurite [--blobHost <IP address>] [--blobPort <port address>] 
    [-d | --debug <log file path>] [-l | --location <workspace path>]
    [--queueHost <IP address>] [--queuePort <port address>]
    [-s | --silent] [-h | --help]
```

**-d** jest skrótem do **--debug**, **-l** switch jest skrótem do **--location**, **-s** jest skrótem do **--silent**, a **-h** jest skrótem do **--help**.

### <a name="blob-listening-host"></a>Host nasłuchiwania obiektów blob

**Opcjonalnie** Domyślnie Azurite będzie słuchać 127.0.0.1 jako serwer lokalny. Użyj **przełącznika --blobHost,** aby ustawić adres do wymagań.

Akceptuj żądania tylko na komputerze lokalnym:

```console
azurite --blobHost 127.0.0.1
```

Zezwalaj na żądania zdalne:

```console
azurite --blobHost 0.0.0.0
```

> [!CAUTION]
> Zezwalanie na żądania zdalne może narazić system na ataki zewnętrzne.

### <a name="blob-listening-port-configuration"></a>Konfiguracja portu nasłuchiwania obiektów blob

**Opcjonalnie** Domyślnie Azurite nasłuchuje usługi blob na porcie 10000. Użyj przełącznika **--blobPort,** aby określić wymagany port nasłuchiwania.

> [!NOTE]
> Po użyciu niestandardowego portu należy zaktualizować parametry połączenia lub odpowiednią konfigurację w narzędziach usługi Azure Storage lub zestawach SDK.

Dostosuj port nasłuchiwania usługi obiektów Blob:

```console
azurite --blobPort 8888
```

Pozwól systemowi automatycznie wybrać dostępny port:

```console
azurite --blobPort 0
```

Używany port jest wyświetlany podczas uruchamiania Azurite.

### <a name="queue-listening-host"></a>Host nasłuchiwania kolejki

**Opcjonalnie** Domyślnie Azurite będzie słuchać 127.0.0.1 jako serwer lokalny. Użyj **przełącznika --queueHost,** aby ustawić adres do wymagań.

Akceptuj żądania tylko na komputerze lokalnym:

```console
azurite --queueHost 127.0.0.1
```

Zezwalaj na żądania zdalne:

```console
azurite --queueHost 0.0.0.0
```

> [!CAUTION]
> Zezwalanie na żądania zdalne może narazić system na ataki zewnętrzne.

### <a name="queue-listening-port-configuration"></a>Konfiguracja portu nasłuchiwania kolejki

**Opcjonalnie** Domyślnie Azurite nasłuchuje usługi Kolejka na porcie 10001. Użyj przełącznika **--queuePort,** aby określić wymagany port nasłuchiwania.

> [!NOTE]
> Po użyciu niestandardowego portu należy zaktualizować parametry połączenia lub odpowiednią konfigurację w narzędziach usługi Azure Storage lub zestawach SDK.

Dostosuj port nasłuchiwania usługi kolejki:

```console
azurite --queuePort 8888
```

Pozwól systemowi automatycznie wybrać dostępny port:

```console
azurite --queuePort 0
```

Używany port jest wyświetlany podczas uruchamiania Azurite.

### <a name="workspace-path"></a>Ścieżka obszaru roboczego

**Opcjonalnie** Azurite przechowuje dane na dysku lokalnym podczas wykonywania. Użyj przełącznika **--location,** aby określić ścieżkę jako lokalizację obszaru roboczego. Domyślnie zostanie użyty bieżący katalog roboczy procesu.

```console
azurite --location c:\azurite
```

```console
azurite -l c:\azurite
```

### <a name="access-log"></a>Dziennik dostępu

**Opcjonalnie** Domyślnie dziennik dostępu jest wyświetlany w oknie konsoli. Wyłącz wyświetlanie dziennika dostępu za pomocą **przełącznika --silent.**

```console
azurite --silent
```

```console
azurite -s
```

### <a name="debug-log"></a>Dziennik debugowania

**Opcjonalnie** Dziennik debugowania zawiera szczegółowe informacje na temat każdego żądania i śledzenia stosu wyjątków. Włącz dziennik debugowania, udostępniając prawidłową ścieżkę pliku lokalnego do przełącznika **--debug.**

```console
azurite --debug path/debug.log
```

```console
azurite -d path/debug.log
```

### <a name="loose-mode"></a>Tryb luźny

**Opcjonalnie** Domyślnie Azurite stosuje tryb ścisły, aby zablokować nieobsługiwały nagłówki żądań i parametry. Wyłącz tryb ścisły za pomocą **przełącznika --loose.**

```console
azurite --loose
```

Zwróć uwagę na stołeczny przełącznik skrótów "L":

```console
azurite -L
```

## <a name="authorization-for-tools-and-sdks"></a>Autoryzacja narzędzi i zestawów SDK

Połącz się z Azurite za pomocą zestawów SDK usługi Azure Storage lub narzędzi, takich jak [Eksplorator usługi Azure Storage,](https://azure.microsoft.com/features/storage-explorer/)przy użyciu dowolnej strategii uwierzytelniania. Wymagane jest uwierzytelnienie. Azurite obsługuje autoryzację za pomocą klucza udostępnionego i sygnatur dostępu współdzielonego (SAS). Azurite obsługuje również anonimowy dostęp do kontenerów publicznych.

### <a name="well-known-storage-account-and-key"></a>Dobrze znane konto pamięci masowej i klucz

W Azurite można użyć następującej nazwy konta i klucza. Jest to samo dobrze znane konto i klucz używany przez starszy emulator magazynu platformy Azure.

* Nazwa konta:`devstoreaccount1`
* Klucz konta:`Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==`

> [!NOTE]
> Oprócz uwierzytelniania SharedKey, Azurite obsługuje uwierzytelnianie konta i usługi SAS. Dostęp anonimowy jest również dostępny, gdy kontener jest ustawiony, aby umożliwić dostęp publiczny.

### <a name="connection-string"></a>Parametry połączenia

Najprostszym sposobem na połączenie się z Azurite z aplikacji jest skonfigurowanie ciągu połączenia w pliku konfiguracyjnym aplikacji, który odwołuje się do skrótu *UseDevelopmentStorage=true*. Oto przykład ciągu połączenia w pliku *app.config:*

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

Aby uzyskać więcej informacji, zobacz [Konfigurowanie ciągów połączeń usługi Azure Storage](storage-configure-connection-string.md).

### <a name="custom-storage-accounts-and-keys"></a>Niestandardowe konta i klucze magazynu

Azurite obsługuje niestandardowe nazwy kont `AZURITE_ACCOUNTS` magazynu i klucze, `account1:key1[:key2];account2:key1[:key2];...`ustawiając zmienną środowiskową w następującym formacie: .

Na przykład użyj niestandardowego konta magazynu, które ma jeden klucz:

```cmd
set AZURITE_ACCOUNTS="account1:key1"
```

Możesz też użyć wielu kont magazynu z 2 kluczami:

```cmd
set AZURITE_ACCOUNTS="account1:key1:key2;account2:key1:key2"
```

Azurite domyślnie odświeża niestandardowe nazwy kont i klucze ze zmiennej środowiskowej co minutę. Dzięki tej funkcji można dynamicznie obracać klucz konta lub dodawać nowe konta magazynu bez ponownego uruchamiania Azurite.

> [!NOTE]
> Domyślne `devstoreaccount1` konto magazynu jest wyłączone po ustawieniu niestandardowych kont magazynu.

> [!NOTE]
> Odpowiednio zaktualizuj parametry połączenia podczas używania niestandardowych nazw kont i kluczy.

> [!NOTE]
> Użyj `export` słowa kluczowego, aby ustawić zmienne `set` środowiskowe w środowisku systemu Linux, używane w systemie Windows.

### <a name="storage-explorer"></a>Storage Explorer

W Eksploratorze usługi Azure Storage połącz się z Azurite, klikając ikonę **Dodaj konto,** a następnie wybierz pozycję **Dołącz do lokalnego emulatora** i kliknij przycisk **Połącz**.

## <a name="differences-between-azurite-and-azure-storage"></a>Różnice między usługą Azurite a usługą Azure Storage

Istnieją różnice funkcjonalne między lokalnym wystąpieniem Azurite i kontem usługi Azure Storage w chmurze.

### <a name="endpoint-and-connection-url"></a>Adres URL punktu końcowego i połączenia

Punkty końcowe usługi dla Azurite różnią się od punktów końcowych konta usługi Azure Storage. Komputer lokalny nie wykonuje rozpoznawania nazw domen, co wymaga, aby punkty końcowe Azurite były adresami lokalnymi.

Gdy adres zasobu na koncie usługi Azure Storage nazwa konta jest częścią nazwy hosta identyfikatora URI. Zasób, którego dotyczy problem, jest częścią ścieżki identyfikatora URI:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

Następujący identyfikator URI jest prawidłowym adresem obiektu blob na koncie usługi Azure Storage:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

Ponieważ komputer lokalny nie wykonuje rozpoznawania nazw domen, nazwa konta jest częścią ścieżki identyfikatora URI zamiast nazwy hosta. Użyj następującego formatu URI dla zasobu w Azurite:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

Do uzyskiwania dostępu do obiektu blob w programie Azurite można użyć następującego adresu:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

### <a name="scaling-and-performance"></a>Skalowanie i wydajność

Azurite nie jest skalowalną usługą magazynu i nie obsługuje dużej liczby równoczesnych klientów. Nie ma gwarancji wydajności. Azurite jest przeznaczony do celów rozwojowych i testowych.

### <a name="error-handling"></a>Obsługa błędów

Azurite jest wyrównany z logiką obsługi błędów usługi Azure Storage, ale istnieją różnice. Na przykład komunikaty o błędach mogą być różne, podczas gdy kody stanu błędów są wyrównywać.

### <a name="ra-grs"></a>RA-GRS

Azurite obsługuje replikację geograficzną dostępu do odczytu (RA-GRS). W przypadku zasobów magazynu należy uzyskać dostęp do lokalizacji pomocniczej, dołączając **-secondary** do nazwy konta. Na przykład do uzyskiwania dostępu do obiektu blob przy użyciu pomocniczego tylko do odczytu w programie Azurite może służyć następujący adres:

`http://127.0.0.1:10000/devstoreaccount1-secondary/mycontainer/myblob.txt`

## <a name="azurite-is-open-source"></a>Azurite jest open-source

Uwagi i sugestie dotyczące Azurite są mile widziane. Przejdź do strony projektu Azurite [GitHub](https://github.com/Azure/Azurite/projects) lub [problemów z githubem,](https://github.com/Azure/Azurite/issues) aby uzyskać punkty kontrolne i elementy robocze, które śledzimy pod kątem nadchodzących funkcji i poprawek błędów. Szczegółowe elementy robocze są również śledzone w usłudze GitHub.

## <a name="next-steps"></a>Następne kroki

* [Użyj emulatora magazynu platformy Azure do tworzenia i testowania](storage-use-emulator.md) dokumentów starszego emulatora magazynu platformy Azure, który jest zastępowany przez Azurite.
* [Konfigurowanie ciągów połączeń usługi Azure Storage](storage-configure-connection-string.md) wyjaśnia, jak zebrać prawidłowy ciąg połączenia Usługi Azure STorage.
