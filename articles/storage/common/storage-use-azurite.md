---
title: Korzystanie z emulatora typu open source azurite na potrzeby programowania i testowania usługi BLOB Storage (wersja zapoznawcza)
description: Emulator typu open source azurite (wersja zapoznawcza) udostępnia bezpłatne środowisko lokalne do testowania aplikacji usługi Azure Blob Storage.
services: storage
author: mhopkins-msft
ms.service: storage
ms.topic: article
ms.date: 06/12/2019
ms.author: mhopkins
ms.subservice: common
ms.openlocfilehash: 2ccb19253c762bad69875a7b7bba7cd11d46e132
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2019
ms.locfileid: "67869796"
---
# <a name="use-the-azurite-open-source-emulator-for-blob-storage-development-and-testing-preview"></a>Korzystanie z emulatora typu open source azurite na potrzeby programowania i testowania usługi BLOB Storage (wersja zapoznawcza)

Emulator typu open source azurite w wersji 3 (wersja zapoznawcza) zawiera bezpłatne środowisko lokalne do testowania aplikacji usługi Azure Blob Storage. Gdy aplikacja działa lokalnie, przejdź do korzystania z konta usługi Azure Storage w chmurze. Emulator zapewnia obsługę wielu platform w systemach Windows, Linux i MacOS. Azurite v3 obsługuje interfejsy API zaimplementowane przez Blob service platformy Azure.

Azurite to przyszła platforma emulatora magazynu. Azurite zastępuje [emulator usługi Azure Storage](storage-use-emulator.md). Azurite będzie nadal aktualizowana w celu obsługi najnowszych wersji interfejsów API usługi Azure Storage.

Istnieje kilka różnych sposobów instalowania i uruchamiania programu azurite w systemie lokalnym:

  1. [Instalowanie i uruchamianie rozszerzenia azurite Visual Studio Code](#install-and-run-the-azurite-visual-studio-code-extension)
  1. [Instalowanie i uruchamianie azurite za pomocą NPM](#install-and-run-azurite-by-using-npm)
  1. [Instalowanie i uruchamianie obrazu platformy Docker azurite](#install-and-run-the-azurite-docker-image)
  1. [Klonowanie, kompilowanie i uruchamianie azurite z repozytorium GitHub](#clone-build-and-run-azurite-from-the-github-repository)

## <a name="install-and-run-the-azurite-visual-studio-code-extension"></a>Instalowanie i uruchamianie rozszerzenia azurite Visual Studio Code

W obszarze Visual Studio Code Wybierz okienko **rozszerzenia** i wyszukaj ciąg *azurite* w **witrynie rozszerzenia: Marketplace**.

![Portal Visual Studio Code rozszerzenia Marketplace](media/storage-use-azurite/azurite-vs-code-extension.png)

Alternatywnie przejdź do [vs Code rynku rozszerzenia](https://marketplace.visualstudio.com/items?itemName=Azurite.azurite) w przeglądarce. Wybierz przycisk **Zainstaluj** , aby otworzyć Visual Studio Code i przejdź bezpośrednio do strony rozszerzenia azurite.

Aby szybko uruchomić lub zamknąć azurite, kliknij pozycję **Usługa BLOB azurite** na pasku stanu vs Code lub wydawanie następujących poleceń w palecie poleceń vs Code. Aby otworzyć paletę poleceń, naciśnij klawisz **F1** w vs Code.

Rozszerzenie obsługuje następujące polecenia Visual Studio Code:

   * **Azurite: Uruchom** — Uruchom wszystkie usługi azurite
   * **Azurite: Zamknij** wszystkie usługi azurite
   * **Azurite: Wyczyść** — Zresetuj wszystkie dane usługi azurite Services utrwalenie
   * **Azurite: Start** -obiekt BLOB Start BLOB Service
   * **Azurite: Usługa** zamykania obiektów BLOB typu Close-BLOB
   * **Azurite: Clean** -BLOB Clean BLOB Service

Aby skonfigurować azurite w Visual Studio Code, zaznacz okienko rozszerzenia i kliknij prawym przyciskiem myszy pozycję **azurite**. Wybierz pozycję **Konfiguruj ustawienia rozszerzenia**.

![Azurite Skonfiguruj ustawienia rozszerzenia](media/storage-use-azurite/azurite-configure-extension-settings.png)

Obsługiwane są następujące ustawienia:

   * **Azurite: Host** obiektów BLOB — BLOB Service punkt końcowy nasłuchiwania. Ustawieniem domyślnym jest 127.0.0.1.
   * **Azurite: Port** obiektu BLOB — BLOB Service port nasłuchiwania. Domyślnym portem jest 10000.
   * **Azurite: Debuguj** — wyprowadza Dziennik debugowania do kanału azurite. Wartość domyślna to **false**.
   * **Azurite: Lokalizacja** — ścieżka lokalizacji obszaru roboczego. Wartość domyślna to Visual Studio Code folder roboczy.
   * **Azurite: Tryb** dyskretny powoduje wyłączenie dziennika dostępu. Wartość domyślna to **false**.

## <a name="install-and-run-azurite-by-using-npm"></a>Instalowanie i uruchamianie azurite za pomocą NPM

Ta metoda instalacji wymaga zainstalowanego programu [Node. js w wersji 8,0 lub nowszej](https://nodejs.org) . **npm** to narzędzie do zarządzania pakietami dołączone do każdej instalacji środowiska Node. js. Po zainstalowaniu środowiska Node. js wykonaj następujące polecenie **npm** , aby zainstalować azurite.

```console
npm install -g azurite
```

Po zainstalowaniu azurite zobacz [Uruchom azurite z wiersza polecenia](#run-azurite-from-a-command-line).

## <a name="install-and-run-the-azurite-docker-image"></a>Instalowanie i uruchamianie obrazu platformy Docker azurite

Użyj [DockerHub](https://hub.docker.com/) , aby ściągnąć [najnowszy obraz azurite](https://hub.docker.com/_/microsoft-azure-storage-azurite) przy użyciu następującego polecenia:

```console
docker pull mcr.microsoft.com/azure-storage/azurite
```

**Uruchom obraz platformy Docker azurite**:

Następujące polecenie uruchamia obraz platformy Docker azurite. `-p 10000:10000` Parametr przekierowuje żądania z portu 10000 maszyny hosta do wystąpienia platformy Docker.

```console
docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite
```

**Określ lokalizację obszaru roboczego**:

W poniższym przykładzie `-v c:/azurite:/data` parametr określa `c:/azurite` jako azurite lokalizację danych.

```console
docker run -p 10000:10000 -v c:/azurite:/data mcr.microsoft.com/azure-storage/azurite
```

**Ustaw wszystkie parametry azurite**:

Ten przykład pokazuje, jak ustawić wszystkie parametry wiersza polecenia. Wszystkie parametry poniżej powinny być umieszczone w jednym wierszu polecenia.

```console
docker run -p 8888:8888
           -v c:/azurite:/workspace mcr.microsoft.com/azure-storage/azurite azurite
           -l /workspace
           -d /workspace/debug.log
           --blobPort 8888
           --blobHost 0.0.0.0
```

Zobacz [Opcje wiersza polecenia](#command-line-options) , aby uzyskać więcej informacji o konfigurowaniu azurite podczas uruchamiania.

## <a name="clone-build-and-run-azurite-from-the-github-repository"></a>Klonowanie, kompilowanie i uruchamianie azurite z repozytorium GitHub

Ta metoda instalacji wymaga, aby program [git](https://git-scm.com/) został zainstalowany. Sklonuj [repozytorium GitHub](https://github.com/azure/azurite) dla projektu azurite za pomocą następującego polecenia konsoli.

```console
git clone https://github.com/Azure/Azurite.git
```

Po sklonowaniu kodu źródłowego wykonaj następujące polecenia z katalogu głównego sklonowanego repozytorium, aby skompilować i zainstalować azurite.

```console
npm install
npm run build
npm install -g
```

Po zainstalowaniu i utworzeniu azurite, zobacz [Uruchamianie azurite z wiersza polecenia](#run-azurite-from-a-command-line).

## <a name="run-azurite-from-a-command-line"></a>Uruchamianie azurite z poziomu wiersza polecenia

> [!NOTE]
> Nie można uruchomić azurite z wiersza polecenia, jeśli zainstalowano tylko rozszerzenie Visual Studio Code. Zamiast tego należy użyć palety poleceń VS Code. Aby uzyskać więcej informacji, zobacz [Instalowanie i uruchamianie rozszerzenia Azurite Visual Studio Code](#install-and-run-the-azurite-visual-studio-code-extension).

Aby rozpocząć od razu rozpocząć pracę z wierszem polecenia, Utwórz katalog o nazwie **c:\azurite**, a następnie uruchom polecenie azurite, wykonując następujące polecenia:

```console
azurite --silent --location c:\azurite --debug c:\azurite\debug.log
```

To polecenie informuje azurite o przechowywaniu wszystkich danych w określonym katalogu, **c:\azurite**. Jeśli opcja **--Location** zostanie pominięta, będzie korzystać z bieżącego katalogu roboczego.

## <a name="command-line-options"></a>Opcje wiersza polecenia

Ta sekcja zawiera szczegółowe informacje na temat przełączników wiersza polecenia dostępnych podczas uruchamiania azurite. Wszystkie przełączniki wiersza polecenia są opcjonalne.

```console
C:\Azurite> azurite [--blobHost <IP address>] [--blobPort <port address>]
    [-l | --location <workspace path>] [-s | --silent] [-d | --debug <log file path>]
```

Przełącznik **-l** to skrót do parametru- **-Location**, **-s** jest skrótem **--Silent**i **-d** jest skrótem **--Debug**.

### <a name="listening-host"></a>Host nasłuchiwania

**Opcjonalne** Domyślnie azurite będzie nasłuchiwał adres 127.0.0.1 jako serwer lokalny. Aby ustawić adres na wymagania, użyj przełącznika **--blobHost** .

Akceptuj tylko żądania na komputerze lokalnym:

```console
azurite --blobHost 127.0.0.1
```

Zezwalaj na żądania zdalne:

```console
azurite --blobHost 0.0.0.0
```

> [!CAUTION]
> Zezwalanie na żądania zdalne może sprawić, że system będzie narażony na ataki zewnętrzne.

### <a name="listening-port-configuration"></a>Konfiguracja portu nasłuchiwania

**Opcjonalne** Domyślnie azurite nasłuchuje Blob service na porcie 10000. Użyj przełącznika **--blobPort** , aby określić wymagany port nasłuchujący.

> [!NOTE]
> Po użyciu dostosowanego portu należy zaktualizować parametry połączenia lub odpowiednią konfigurację w narzędziach lub zestawach SDK usługi Azure Storage.

Dostosuj port nasłuchujący Blob service:

```console
azurite --blobPort 8888
```

Pozwól, aby system automatycznie wybierał dostępny port:

```console
azurite --blobPort 0
```

Używany port jest wyświetlany podczas uruchamiania azurite.

### <a name="workspace-path"></a>Ścieżka obszaru roboczego

**Opcjonalne** Azurite przechowuje dane na dysku lokalnym podczas wykonywania. Użyj przełącznika **--Location** , aby określić ścieżkę jako lokalizację obszaru roboczego. Domyślnie zostanie użyty katalog roboczy bieżącego procesu.

```console
azurite --location c:\azurite
```

```console
azurite -l c:\azurite
```

### <a name="access-log"></a>Dziennik dostępu

**Opcjonalne** Domyślnie dziennik dostępu jest wyświetlany w oknie konsoli. Wyłącz wyświetlanie dziennika dostępu przy użyciu przełącznika **--Silent** .

```console
azurite --silent
```

```console
azurite -s
```

### <a name="debug-log"></a>Dziennik debugowania

**Opcjonalne** Dziennik debugowania zawiera szczegółowe informacje dotyczące każdego żądania i śladu stosu wyjątków. Włącz Dziennik debugowania, podając prawidłową ścieżkę do przełącznika **--Debug** .

```console
azurite --debug path/debug.log
```

```console
azurite -d path/debug.log
```

## <a name="authorization-for-tools-and-sdks"></a>Autoryzacja narzędzi i zestawów SDK

Łączenie się z usługą azurite z zestawów SDK lub narzędzi usługi Azure Storage, takich jak [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/), przy użyciu dowolnej strategii uwierzytelniania. Wymagane jest uwierzytelnianie. Azurite obsługuje autoryzację za pomocą klucza współużytkowanego i sygnatur dostępu współdzielonego (SAS). Azurite obsługuje również dostęp anonimowy do kontenerów publicznych.

### <a name="well-known-storage-account-and-key"></a>Dobrze znane konto magazynu i klucz

Możesz użyć następującej nazwy konta i klucza z azurite. Jest to to samo dobrze znane konto i klucz używany przez starszy emulator usługi Azure Storage.

* Nazwa konta:`devstoreaccount1`
* Klucz konta:`Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==`

> [!NOTE]
> Oprócz uwierzytelniania SharedKey azurite obsługuje uwierzytelnianie SAS kont i usług. Dostęp anonimowy jest również dostępny, gdy kontener jest ustawiony tak, aby zezwalać na dostęp publiczny.

### <a name="connection-string"></a>Parametry połączenia

Najprostszym sposobem nawiązywania połączenia z usługą azurite z poziomu aplikacji jest skonfigurowanie parametrów połączenia w pliku konfiguracji aplikacji, który odwołuje się do skrótu *UseDevelopmentStorage = true*. Oto przykład parametrów połączenia w pliku *App. config* :

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

Aby uzyskać więcej informacji, zobacz [Konfigurowanie parametrów połączenia usługi Azure Storage](storage-configure-connection-string.md).

### <a name="storage-explorer"></a>Storage Explorer

W Eksplorator usługi Azure Storage Połącz się z usługą azurite, klikając ikonę **Dodaj konto** , a następnie wybierz pozycję **Dołącz do lokalnego emulatora** , a następnie kliknij przycisk **Połącz**.

## <a name="differences-between-azurite-and-azure-storage"></a>Różnice między azurite i usługą Azure Storage

Istnieją różnice funkcjonalne między lokalnym wystąpieniem azurite i kontem usługi Azure Storage w chmurze.

### <a name="endpoint-and-connection-url"></a>Punkt końcowy i adres URL połączenia

Punkty końcowe usługi azurite różnią się od punktów końcowych konta usługi Azure Storage. Komputer lokalny nie obsługuje rozpoznawania nazw domen, wymagając punktów końcowych azurite jako adresów lokalnych.

Gdy adresowanie zasobu odbywa się na koncie usługi Azure Storage, nazwa konta jest częścią nazwy hosta identyfikatora URI. Zasób, który jest adresowany, jest częścią ścieżki URI:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

Następujący identyfikator URI jest prawidłowym adresem obiektu BLOB na koncie usługi Azure Storage:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

Ponieważ komputer lokalny nie wykonuje rozpoznawania nazw domen, nazwa konta jest częścią ścieżki URI zamiast nazwy hosta. Użyj następującego formatu identyfikatora URI dla zasobu w azurite:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

Następujący adres może służyć do uzyskiwania dostępu do obiektu BLOB w azurite:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

### <a name="scaling-and-performance"></a>Skalowanie i wydajność

Azurite nie jest skalowalną usługą magazynu i nie obsługuje dużej liczby równoczesnych klientów. Nie ma gwarancji wydajności. Azurite jest przeznaczony do celów deweloperskich i testowych.

### <a name="error-handling"></a>Obsługa błędów

Azurite jest wyrównany do logiki obsługi błędów usługi Azure Storage, ale istnieją różnice. Na przykład komunikaty o błędach mogą się różnić, podczas gdy kody stanu błędu są wyrównane.

### <a name="ra-grs"></a>RA-GRS

Azurite obsługuje replikację Geograficznie nadmiarowy do odczytu (RA-GRS). W przypadku zasobów magazynu uzyskaj dostęp do lokalizacji dodatkowej przez dołączenie **-pomocnicze** do nazwy konta. Na przykład następujący adres może być używany do uzyskiwania dostępu do obiektu BLOB za pomocą pomocniczego tylko do odczytu w azurite:

`http://127.0.0.1:10000/devstoreaccount1-secondary/mycontainer/myblob.txt`

## <a name="azurite-is-open-source"></a>Azurite jest otwartym źródłem

Zamieszczamy wkłady i sugestie dotyczące azurite. Przejdź na stronę [projektu GitHub](https://github.com/Azure/Azurite/projects) azurite lub problemy z usługą [GitHub](https://github.com/Azure/Azurite/issues) dla punktów kontrolnych i elementów roboczych, które są śledzone pod kątem nadchodzących funkcji i poprawek błędów. Szczegółowe elementy robocze są również śledzone w usłudze GitHub.

## <a name="next-steps"></a>Kolejne kroki

* [Korzystanie z emulatora usługi Azure Storage na potrzeby tworzenia i testowania](storage-use-emulator.md) dokumentów w starszym emulatorze usługi Azure Storage, który jest zastępowany przez azurite.
* [Konfigurowanie parametrów połączenia usługi Azure Storage](storage-configure-connection-string.md) wyjaśnia, jak utworzyć prawidłowe parametry połączenia usługi Azure Storage.
