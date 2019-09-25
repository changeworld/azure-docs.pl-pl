---
title: Wprowadzenie do Eksplorator usługi Storage | Microsoft Docs
description: Zarządzanie zasobami usługi Azure Storage za pomocą Eksplorator usługi Storage
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 04/22/2019
ms.author: cawa
ms.openlocfilehash: 43e76b9331eb8dbe95265810b9191a10d4caee08
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71272027"
---
# <a name="get-started-with-storage-explorer"></a>Wprowadzenie do Eksplorator usługi Storage

## <a name="overview"></a>Przegląd

Eksplorator usługi Microsoft Azure Storage jest aplikacją autonomiczną, która ułatwia współpracę z danymi usługi Azure Storage w systemach Windows, macOS i Linux. W tym artykule przedstawiono kilka sposobów łączenia się z kontami usługi Azure Storage i zarządzania nimi.

![Microsoft Azure Storage Explorer][0]

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

Eksplorator usługi Storage jest obsługiwana w następujących wersjach systemu Windows:

* Windows 10 (zalecane)
* Windows 8
* Windows 7

W przypadku wszystkich wersji systemu Windows wymagany jest .NET Framework 4.6.2 lub nowszy.

# <a name="macostabmacos"></a>[macOS](#tab/macos)

Eksplorator usługi Storage jest obsługiwana w następujących wersjach programu macOS:

* macOS 10,12 "Sierra" i nowsze wersje

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

Eksplorator usługi Storage jest dostępny w [magazynie Snap](https://snapcraft.io/storage-explorer) dla najbardziej typowych dystrybucji systemu Linux i jest zalecaną metodą instalacji. Eksplorator usługi Storage Snap automatycznie instaluje wszystkie zależności i aktualizuje, gdy nowe wersje są publikowane w magazynie Snap.

Aby zapoznać się z listą obsługiwanych dystrybucji, przejdź do [strony instalacji z przystawką](https://snapcraft.io/docs/installing-snapd).

Eksplorator usługi Storage wymaga użycia Menedżera haseł, aby można było połączyć się ręcznie, zanim Eksplorator usługi Storage będzie działał poprawnie. Możesz połączyć Eksplorator usługi Storage z menedżerem haseł systemu, uruchamiając następujące polecenie:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

Eksplorator usługi Storage jest również dostępna jako pobieranie tar. gz, ale należy ręcznie zainstalować zależności. Instalacja. tar. gz jest obsługiwana w następujących dystrybucjach systemu Linux:

* Ubuntu 18,04 x64
* Ubuntu 16,04 x64
* Ubuntu 14,04 x64

Instalacja. tar. gz może współpracować z innymi dystrybucjami, ale tylko te wymienione na liście są oficjalnie obsługiwane.

Aby uzyskać więcej informacji na temat instalowania Eksplorator usługi Storage w systemie Linux, zobacz [Przewodnik rozwiązywania problemów](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#linux-dependencies).

---

## <a name="download-and-install"></a>Pobierz i zainstaluj

[Pobieranie i instalowanie Eksploratora usługi Storage](https://www.storageexplorer.com)

## <a name="connect-to-a-storage-account-or-service"></a>Łączenie się z usługą lub kontem magazynu

Eksplorator usługi Storage oferuje kilka sposobów nawiązywania połączenia z kontami magazynu. Ogólnie można:

* [Zaloguj się do platformy Azure, aby uzyskać dostęp do subskrypcji i ich zasobów](#sign-in-to-azure)
* [Dołącz określony magazyn lub zasób CosmosDB](#attach-a-specific-resource)

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

> [!NOTE]
> Aby w pełni uzyskać dostęp do zasobów po zalogowaniu, Eksplorator usługi Storage wymaga zarówno uprawnień do zarządzania (Azure Resource Manager), jak i warstwy danych. Oznacza to, że potrzebne są uprawnienia Azure Active Directory (Azure AD), które zapewniają dostęp do konta magazynu, kontenerów na koncie i danych w kontenerach. Jeśli masz uprawnienia tylko w warstwie danych, rozważ [dodanie zasobu za pomocą usługi Azure AD](#add-a-resource-via-azure-ad). Aby uzyskać więcej informacji na temat określonych uprawnień Eksplorator usługi Storage wymaganych, zobacz [Przewodnik rozwiązywania problemów](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#rbac-permissions-issues).

1. W Eksplorator usługi Storage wybierz pozycję **Zarządzaj kontami** , aby przejść do panelu zarządzania kontami.

    ![Zarządzaj kontami][1]

2. W okienku po lewej stronie zostaną wyświetlone wszystkie konta platformy Azure, do których się zalogowano. Aby połączyć się z innym kontem, wybierz pozycję **Dodaj konto**.

3. Jeśli chcesz zalogować się do chmury krajowej lub Azure Stack, wybierz listę rozwijaną **środowisko platformy Azure** , aby wybrać chmurę platformy Azure, której chcesz użyć. Po wybraniu środowiska wybierz przycisk **Zaloguj** . Aby uzyskać więcej informacji, zobacz [łączenie Eksplorator usługi Storage z subskrypcją Azure Stack](/azure-stack/user/azure-stack-storage-connect-se).

    ![Opcja logowania][2]

4. Po pomyślnym zalogowaniu się przy użyciu konta platformy Azure konto i subskrypcje platformy Azure skojarzone z tym kontem zostaną dodane do okienka po lewej stronie. Wybierz subskrypcje platformy Azure, z którymi chcesz pracować, a następnie wybierz przycisk **Zastosuj**. (Wybranie opcji **wszystkie subskrypcje** powoduje przełączenie wyboru między wszystkimi lub żadną z wymienionych subskrypcji platformy Azure).

    ![Wybieranie subskrypcji platformy Azure][3]

    W okienku po lewej stronie są wyświetlane wszystkie konta magazynu skojarzone z wybranymi subskrypcjami platformy Azure.

    ![Wybrane subskrypcje platformy Azure][4]

### <a name="attach-a-specific-resource"></a>Dołączanie określonego zasobu

Istnieje wiele sposobów dołączenia do zasobu w Eksplorator usługi Storage:

* [Dodaj zasób za pośrednictwem usługi Azure AD](#add-a-resource-via-azure-ad). Jeśli masz uprawnienia tylko w warstwie danych, Użyj tej opcji, aby dodać kontener obiektów blob lub Azure Data Lake Storage Gen2 kontener magazynu obiektów BLOB.
* [Użyj parametrów połączenia](#use-a-connection-string). Użyj tej opcji, jeśli masz parametry połączenia z kontem magazynu. Eksplorator usługi Storage obsługuje parametry połączenia klucza i [sygnatury dostępu współdzielonego](storage/common/storage-dotnet-shared-access-signature-part-1.md) .
* [Użyj identyfikatora URI sygnatury dostępu współdzielonego](#use-a-sas-uri). Jeśli masz [Identyfikator URI sygnatury dostępu współdzielonego](storage/common/storage-dotnet-shared-access-signature-part-1.md) do kontenera obiektów blob, udziału plików, kolejki lub tabeli, użyj go do dołączenia do zasobu. Aby uzyskać identyfikator URI sygnatury dostępu współdzielonego, można użyć [Eksplorator usługi Storage](#generate-a-sas-in-storage-explorer) lub [Azure Portal](https://portal.azure.com).
* [Użyj nazwy i klucza](#use-a-name-and-key). Jeśli znasz jeden z kluczy konta do konta magazynu, możesz użyć tej opcji, aby szybko nawiązać połączenie. Klucze konta magazynu znajdują się w panelu **klucze dostępu** do konta magazynu w [Azure Portal](https://portal.azure.com).
* [Dołącz do lokalnego emulatora](#attach-to-a-local-emulator). Jeśli używasz jednego z dostępnych emulatorów usługi Azure Storage, Użyj tej opcji, aby łatwo połączyć się z emulatorem.
* [Połącz się z kontem Azure Cosmos dB przy użyciu parametrów połączenia](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string). Użyj tej opcji, jeśli masz parametry połączenia z wystąpieniem CosmosDB.
* [Połącz się z Azure Data Lake Store według identyfikatora URI](#connect-to-azure-data-lake-store-by-uri). Użyj tej opcji, jeśli masz identyfikator URI do Azure Data Lake Store.

#### <a name="add-a-resource-via-azure-ad"></a>Dodawanie zasobu za pośrednictwem usługi Azure AD

1. Otwórz okno dialogowe **łączenie** , wybierając przycisk **Połącz** na pionowym pasku narzędzi po lewej stronie:

    ![Opcja Połącz z usługą Azure Storage][9]

2. Jeśli jeszcze tego nie zrobiono, użyj opcji **Dodaj konto platformy Azure** , aby zalogować się do konta platformy Azure, które ma dostęp do zasobu. Po zalogowaniu Wróć do okna dialogowego **Połącz** .

3. Wybierz pozycję **Dodaj zasób za pośrednictwem Azure Active Directory (Azure AD)** , a następnie wybierz pozycję **dalej**.

4. Wybierz konto platformy Azure i dzierżawcę, które mają dostęp do zasobu magazynu, do którego chcesz dołączyć. Wybierz opcję **Dalej**.

5. Wybierz typ zasobu, który chcesz dołączyć, a następnie wprowadź informacje wymagane do nawiązania połączenia. Informacje wprowadzane na tej stronie zależą od typu dodawanego zasobu. Upewnij się, że wybrano poprawny typ zasobu. Po wprowadzeniu wymaganych informacji wybierz pozycję **dalej**.

6. Przejrzyj podsumowanie połączenia i upewnij się, że wszystkie informacje są poprawne. Jeśli tak jest, wybierz pozycję **Połącz**. W przeciwnym razie użyj przycisku **Wstecz** , aby powrócić do poprzednich stron w celu naprawy wszelkich nieprawidłowych informacji.

Po pomyślnym dodaniu połączenia drzewo zasobów automatycznie przechodzi do węzła, który reprezentuje połączenie. Jeśli nie, przejdź do tego węzła, w > obszarze **lokalne & dołączone** > **kontenery obiektów BLOB** **(dołączone kontenery)**  > . Jeśli nie można dodać połączenia Eksplorator usługi Storage lub nie możesz uzyskać dostępu do danych po pomyślnym dodaniu połączenia, zobacz [Przewodnik rozwiązywania problemów](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="use-a-connection-string"></a>Użyj parametrów połączenia

1. Otwórz okno dialogowe **łączenie** , wybierając przycisk **Połącz** na pionowym pasku narzędzi po lewej stronie:

    ![Opcja Połącz z usługą Azure Storage][9]

2. Wybierz pozycję **Użyj parametrów połączenia**, a następnie wybierz przycisk **dalej**.

3. Wybierz nazwę wyświetlaną dla połączenia i wprowadź parametry połączenia. Następnie wybierz opcję **Dalej**.

4. Przejrzyj podsumowanie połączenia i upewnij się, że wszystkie informacje są poprawne. Jeśli tak jest, wybierz pozycję **Połącz**. W przeciwnym razie użyj przycisku **Wstecz** , aby powrócić do poprzednich stron w celu naprawy wszelkich nieprawidłowych informacji.

Po pomyślnym dodaniu połączenia drzewo zasobów automatycznie przechodzi do węzła, który reprezentuje połączenie. Jeśli nie, przejdź do tego węzła, w obszarze **lokalne & dołączone** > **konta magazynu**. Jeśli nie można dodać połączenia Eksplorator usługi Storage lub nie możesz uzyskać dostępu do danych po pomyślnym dodaniu połączenia, zobacz [Przewodnik rozwiązywania problemów](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="use-a-sas-uri"></a>Użyj identyfikatora URI sygnatury dostępu współdzielonego

1. Otwórz okno dialogowe **łączenie** , wybierając przycisk **Połącz** na pionowym pasku narzędzi po lewej stronie:

    ![Opcja Połącz z usługą Azure Storage][9]

2. Wybierz pozycję **Użyj identyfikatora URI sygnatury dostępu współdzielonego (SAS)** , a następnie wybierz przycisk **dalej**.

3. Wybierz nazwę wyświetlaną dla połączenia i wprowadź identyfikator URI sygnatury dostępu współdzielonego. Punkt końcowy usługi dla typu zasobu, który jest dołączany, powinien mieć wartość Autowypełnianie. Jeśli używasz niestandardowego punktu końcowego, możliwe, że może to nie być. Wybierz opcję **Dalej**.

4. Przejrzyj podsumowanie połączenia i upewnij się, że wszystkie informacje są poprawne. Jeśli tak jest, wybierz pozycję **Połącz**. W przeciwnym razie użyj przycisku **Wstecz** , aby powrócić do poprzednich stron w celu naprawy wszelkich nieprawidłowych informacji.

Po pomyślnym dodaniu połączenia drzewo zasobów automatycznie przechodzi do węzła, który reprezentuje połączenie. Jeśli nie przejdzie do tego węzła, poszukaj w obszarze **lokalny & dołączone** > **konta** > magazynu **(dołączone kontenery)**  > *węzeł usługi dla typu dołączonego kontenera*. Jeśli nie można dodać połączenia Eksplorator usługi Storage lub nie możesz uzyskać dostępu do danych po pomyślnym dodaniu połączenia, zobacz [Przewodnik rozwiązywania problemów](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="use-a-name-and-key"></a>Użyj nazwy i klucza

1. Otwórz okno dialogowe **łączenie** , wybierając przycisk **Połącz** na pionowym pasku narzędzi po lewej stronie:

    ![Opcja Połącz z usługą Azure Storage][9]

2. Wybierz pozycję **Użyj nazwy i klucza konta magazynu**, a następnie wybierz przycisk **dalej**.

3. Wybierz nazwę wyświetlaną dla połączenia.

4. Wprowadź nazwę konta magazynu i jeden z jego kluczy dostępu.

5. Wybierz **domenę magazynu** , która ma zostać użyta, a następnie wybierz pozycję **dalej**.

6. Przejrzyj podsumowanie połączenia i upewnij się, że wszystkie informacje są poprawne. Jeśli tak jest, wybierz pozycję **Połącz**. W przeciwnym razie użyj przycisku **Wstecz** , aby powrócić do poprzednich stron w celu naprawy wszelkich nieprawidłowych informacji.

Po pomyślnym dodaniu połączenia drzewo zasobów automatycznie przechodzi do węzła, który reprezentuje połączenie. Jeśli nie, przejdź do tego węzła, w obszarze **lokalne & dołączone** > **konta magazynu**. Jeśli nie można dodać połączenia Eksplorator usługi Storage lub nie możesz uzyskać dostępu do danych po pomyślnym dodaniu połączenia, zobacz [Przewodnik rozwiązywania problemów](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="attach-to-a-local-emulator"></a>Dołącz do lokalnego emulatora

Eksplorator usługi Storage obecnie obsługuje dwa oficjalne emulatory magazynu:
* [Emulator usługi Azure Storage](storage/common/storage-use-emulator.md) (Tylko system Windows)
* [Azurite](https://github.com/azure/azurite) (Windows, macOS lub Linux)

Jeśli emulator nasłuchuje na portach domyślnych, można użyć węzła **emulator-default Ports** (znajdującego się w obszarze **lokalne & dołączone** > **konta magazynu**), aby szybko uzyskać dostęp do emulatora.

Jeśli chcesz użyć innej nazwy dla połączenia lub jeśli emulator nie jest uruchomiony na portach domyślnych, wykonaj następujące kroki:

1. Uruchom emulator. Gdy to zrobisz, zanotuj porty, dla których emulator nasłuchuje dla każdego typu usługi.

   > [!IMPORTANT]
   > Eksplorator usługi Storage nie uruchamia automatycznie emulatora. Należy ją uruchomić ręcznie.

2. Otwórz okno dialogowe **łączenie** , wybierając przycisk **Połącz** na pionowym pasku narzędzi po lewej stronie:

    ![Opcja Połącz z usługą Azure Storage][9]

3. Wybierz opcję **Dołącz do lokalnego emulatora**, a następnie wybierz przycisk **dalej**.

4. Wybierz nazwę wyświetlaną dla połączenia i wprowadź porty, dla których emulator nasłuchuje dla każdego typu usługi. Pola tekstowe rozpoczną się od domyślnych wartości portów dla większości emulatorów. Pole **port plików** jest puste, ponieważ żaden z oficjalnych emulatorów aktualnie nie obsługuje usługi Files. Jeśli używany emulator obsługuje pliki, możesz wprowadzić używany port. Następnie wybierz opcję **Dalej**.

5. Przejrzyj podsumowanie połączenia i upewnij się, że wszystkie informacje są poprawne. Jeśli tak jest, wybierz pozycję **Połącz**. W przeciwnym razie użyj przycisku **Wstecz** , aby powrócić do poprzednich stron w celu naprawy wszelkich nieprawidłowych informacji.

Po pomyślnym dodaniu połączenia drzewo zasobów automatycznie przechodzi do węzła, który reprezentuje połączenie. Jeśli nie, przejdź do tego węzła, w obszarze **lokalne & dołączone** > **konta magazynu**. Jeśli nie można dodać połączenia Eksplorator usługi Storage lub nie możesz uzyskać dostępu do danych po pomyślnym dodaniu połączenia, zobacz [Przewodnik rozwiązywania problemów](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Nawiązywanie połączenia z kontem Azure Cosmos DB przy użyciu parametrów połączenia

Alternatywą dla zarządzania kontami Azure Cosmos DB za pośrednictwem subskrypcji platformy Azure jest również połączenie z Azure Cosmos DB przy użyciu parametrów połączenia. W tym celu wykonaj następujące kroki:

1. Po lewej stronie drzewa zasobów rozwiń węzeł **lokalne i dołączone**, kliknij prawym przyciskiem myszy pozycję **Azure Cosmos DB konta**, a następnie wybierz pozycję **Połącz z Azure Cosmos DB**.

    ![Łączenie się z usługą Azure Cosmos DB przy użyciu parametrów połączenia][21]

2. Wybierz interfejs API Azure Cosmos DB, wprowadź dane **parametrów połączenia** , a następnie wybierz przycisk **OK** , aby połączyć konto Azure Cosmos DB. Aby uzyskać informacje na temat pobierania parametrów połączenia, zobacz [pobieranie parametrów połączenia](https://docs.microsoft.com/azure/cosmos-db/manage-account).

    ![Parametry połączenia][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>Połącz z Azure Data Lake Store według identyfikatora URI

Jeśli chcesz uzyskać dostęp do zasobu, którego nie ma w subskrypcji, będziesz potrzebować komuś, kto ma dostęp do tego zasobu, aby udostępnić identyfikator URI zasobu. Po zalogowaniu się możesz nawiązać połączenie z Data Lake Store przy użyciu identyfikatora URI. W tym celu wykonaj następujące kroki:

1. Otwórz Eksploratora usługi Storage.
2. W okienku po lewej stronie rozwiń pozycję **Lokalny i dołączony**.
3. Kliknij prawym przyciskiem myszy **Data Lake Store**. Z menu skrótów wybierz pozycję **Połącz z Data Lake Store**.

    ![menu kontekstowe łączenia z Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach.png)

4. Wprowadź identyfikator URI. Narzędzie przechodzi do lokalizacji adresu URL, który właśnie został wprowadzony.

    ![Okno dialogowe łączenie z kontekstem Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

    ![Łączenie z wynikami Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-attach-finish.png)


## <a name="generate-a-sas-in-storage-explorer"></a>Generowanie sygnatury dostępu współdzielonego w Eksplorator usługi Storage

### <a name="account-level-sas"></a>SAS na poziomie konta

1. Kliknij prawym przyciskiem myszy konto magazynu, które chcesz udostępnić, a następnie wybierz pozycję **Pobierz sygnaturę dostępu współdzielonego**.

    ![Opcja menu kontekstowego Uzyskaj sygnaturę dostępu współdzielonego][14]

2. W oknie dialogowym **generowanie sygnatury dostępu współdzielonego** Określ przedział czasu i uprawnienia dla konta, a następnie wybierz pozycję **Utwórz**.

    ![Pobierz sygnatura dostępu współdzielonego — okno dialogowe][15]

3. Teraz można skopiować **ciąg połączenia** lub nieprzetworzony **ciąg zapytania** do Schowka.

### <a name="service-level-sas"></a>Sygnatura dostępu współdzielonego poziomu usługi

[Jak uzyskać sygnaturę dostępu współdzielonego dla kontenera obiektów BLOB w Eksplorator usługi Storage](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)

## <a name="search-for-storage-accounts"></a>Wyszukiwanie kont magazynu

Jeśli potrzebujesz znaleźć zasób magazynu i nie wiadomo, gdzie jest, możesz użyć pola wyszukiwania w górnej części okienka po lewej stronie, aby wyszukać zasób.

Podczas wpisywania w polu wyszukiwania w okienku po lewej stronie są wyświetlane wszystkie zasoby, które pasują do podanej wartości wyszukiwania. Na przykład wyszukiwanie **punktów końcowych** jest pokazane na poniższym zrzucie ekranu:

![Wyszukiwanie kont magazynu][23]

> [!NOTE]
> Aby przyspieszyć wyszukiwanie, użyj panelu zarządzania kontami, aby usunąć wszystkie subskrypcje, które nie zawierają szukanego elementu. Możesz również kliknąć prawym przyciskiem myszy węzeł i w **tym miejscu** wybrać pozycję Wyszukaj, aby rozpocząć wyszukiwanie od określonego węzła.
>
>

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie zasobami usługi Azure Blob Storage za pomocą Eksplorator usługi Storage](vs-azure-tools-storage-explorer-blobs.md)
* [Zarządzanie Azure Cosmos DB w Eksplorator usługi Storage (wersja zapoznawcza)](./cosmos-db/storage-explorer.md)
* [Zarządzanie zasobami Azure Data Lake Store przy użyciu Eksplorator usługi Storage](./data-lake-store/data-lake-store-in-storage-explorer.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Overview.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageAccounts.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-SignInSelected.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccountPanel.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SubscriptionNode.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/PortalAccessKeys.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccessKeys.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-AddWithKeySelected.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-NameAndKeyPage.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount-Detach.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/GetSharedAccessSignature.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SharedAccessSignatureDialog.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-WithConnStringOrSASSelected.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-1.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithSASAccount.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-2.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ServiceAttachedWithSAS.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-db-by-connection-string.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Search.png
