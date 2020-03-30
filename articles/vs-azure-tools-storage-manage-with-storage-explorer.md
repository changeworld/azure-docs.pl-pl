---
title: Wprowadzenie do Eksploratora pamięci masowej | Dokumenty firmy Microsoft
description: Zarządzanie zasobami magazynu platformy Azure za pomocą Eksploratora magazynu
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 11/08/2019
ms.author: cawa
ms.openlocfilehash: 7886d5a1ad0745550767b7d6f19592ca3c84b00a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279795"
---
# <a name="get-started-with-storage-explorer"></a>Wprowadzenie do Eksploratora usługi Storage

## <a name="overview"></a>Omówienie

Microsoft Azure Storage Explorer to autonomiczna aplikacja, która ułatwia pracę z danymi usługi Azure Storage w systemach Windows, macOS i Linux. W tym artykule dowiesz się kilka sposobów łączenia się z kontami magazynu platformy Azure i zarządzania nimi.

![Microsoft Azure Storage Explorer][0]

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="windows"></a>[Windows](#tab/windows)

Następujące wersje Eksploratora magazynu w systemie Windows:

* Windows 10 (zalecane)
* Windows 8
* Windows 7

Dla wszystkich wersji systemu Windows Eksplorator magazynu wymaga programu .NET Framework 4.6.2 lub nowszego.

# <a name="macos"></a>[Macos](#tab/macos)

Następujące wersje eksploratora magazynu obsługującego system macOS:

* macOS 10.12 Sierra i nowsze wersje

# <a name="linux"></a>[Linux](#tab/linux)

Eksplorator magazynu jest dostępny w [Snap Store](https://snapcraft.io/storage-explorer) dla większości typowych dystrybucji systemu Linux. Dla tej instalacji firma Snap Store jest zalecana. Przystawka Eksploratora magazynu instaluje wszystkie swoje zależności i aktualizacje, gdy nowe wersje są publikowane w Sklepie Snap.

Obsługiwane dystrybucje można znaleźć na [stronie instalacji przystawki](https://snapcraft.io/docs/installing-snapd).

Eksplorator magazynu wymaga użycia menedżera haseł. Może być wymagane ręczne połączenie z menedżerem haseł. Eksploratora magazynu można podłączyć do menedżera haseł systemu, uruchamiając następujące polecenie:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

Eksplorator pamięci masowej jest również dostępny do pobrania w *pliku .tar.gz.* Należy zainstalować zależności ręcznie. Następujące dystrybucje systemu Linux obsługują instalację *.tar.gz:*

* Ubuntu 18.04 x64
* Ubuntu 16.04 x64
* Ubuntu 14.04 x64

Instalacja *.tar.gz* może działać na innych dystrybucjach, ale tylko te wymienione są oficjalnie obsługiwane.

Aby uzyskać więcej pomocy dotyczącej instalowania Eksploratora magazynu w systemie Linux, zobacz [zależności systemu Linux](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#linux-dependencies) w przewodniku dotyczącym rozwiązywania problemów z Eksploratorem usługi Azure Storage.

---

## <a name="download-and-install"></a>Pobieranie i instalowanie

Aby pobrać i zainstalować Eksploratora magazynu, zobacz [Eksplorator usług Azure Storage](https://www.storageexplorer.com).

## <a name="connect-to-a-storage-account-or-service"></a>Łączenie się z usługą lub kontem magazynu

Eksplorator usługi Storage oferuje kilka sposobów nawiązywania połączenia z kontami magazynu. Ogólnie rzecz biorąc, można:

* [Zaloguj się na platformie Azure, aby uzyskać dostęp do subskrypcji i ich zasobów](#sign-in-to-azure)
* [Dołączanie określonego zasobu magazynu lub usługi CosmosDB](#attach-a-specific-resource)

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

> [!NOTE]
> Aby w pełni uzyskać dostęp do zasobów po zalogowaniu się, Eksplorator magazynu wymaga zarówno zarządzania (Usługi Azure Resource Manager), jak i uprawnień warstwy danych. Oznacza to, że potrzebujesz uprawnień usługi Azure Active Directory (Azure AD), które zapewniają dostęp do konta magazynu, kontenerów na koncie i danych w kontenerach. Jeśli masz uprawnienia tylko w warstwie danych, rozważ [dodanie zasobu za pośrednictwem usługi Azure AD](#add-a-resource-via-azure-ad). Aby uzyskać więcej informacji na temat określonych uprawnień, które wymaga Eksplorator magazynu, zobacz [Przewodnik dotyczący rozwiązywania problemów z Eksploratorem usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#rbac-permissions-issues).

1. W Eksploratorze magazynu wybierz pozycję **Wyświetl** > **zarządzanie kontem** lub przycisk **Zarządzaj kontami.**

    ![Zarządzaj kontami][1]

1. **ZARZĄDZANIE KONTEM** wyświetla teraz wszystkie konta platformy Azure, do których się zalogowałeś. Aby połączyć się z innym **kontem,** wybierz pozycję Dodaj konto .

1. W **obszarze Połącz z usługą Azure Storage**wybierz chmurę platformy Azure ze środowiska platformy **Azure,** aby zalogować się do chmury krajowej lub usługi Azure Stack. Po wybraniu środowiska wybierz pozycję **Dalej**.

    ![Możliwość zalogowania się][2]

    Eksplorator magazynu otwiera stronę do zalogowania się. Aby uzyskać więcej informacji, zobacz [Łączenie Eksploratora magazynu z kontem subskrypcji lub magazynu usługi Azure Stack.](/azure-stack/user/azure-stack-storage-connect-se)

1. Po pomyślnym zalogowaniu się za pomocą konta platformy Azure konto i subskrypcje platformy Azure skojarzone z tym kontem są wyświetlane w obszarze **ZARZĄDZANIE KONTEM**. Wybierz **wszystkie subskrypcje,** aby przełączyć wybór między wszystkimi lub żadną z wymienionych subskrypcji platformy Azure. Wybierz subskrypcje platformy Azure, z którymi chcesz pracować, a następnie wybierz przycisk **Zastosuj**.

    ![Wybieranie subskrypcji platformy Azure][3]

    **EXPLORER** wyświetla konta magazynu skojarzone z wybranymi subskrypcjami platformy Azure.

    ![Wybrane subskrypcje platformy Azure][4]

### <a name="attach-a-specific-resource"></a>Dołączanie określonego zasobu

Istnieje kilka sposobów dołączania do zasobu w Eksploratorze magazynu:

* [Dodawanie zasobu za pośrednictwem usługi Azure AD](#add-a-resource-via-azure-ad). Jeśli masz uprawnienia tylko w warstwie danych, użyj tej opcji, aby dodać kontener obiektów blob lub kontener magazynu obiektów Blob usługi Azure Usługi Data Lake Storage Gen2.
* [Użyj ciągu połączenia](#use-a-connection-string). Użyj tej opcji, jeśli masz parametry połączenia z kontem magazynu. Eksplorator magazynu obsługuje parametry połączenia [podpisu klucza i udostępnionego dostępu.](storage/common/storage-dotnet-shared-access-signature-part-1.md)
* [Użyj identyfikatora URI podpisu dostępu udostępnionego](#use-a-shared-access-signature-uri). Jeśli masz identyfikator [URI podpisu dostępu udostępnionego](storage/common/storage-dotnet-shared-access-signature-part-1.md) do kontenera obiektów blob, udziału plików, kolejki lub tabeli, użyj go do dołączenia do zasobu. Aby uzyskać identyfikator URI podpisu dostępu udostępnionego, można użyć [Eksploratora magazynu](#generate-a-sas-in-storage-explorer) lub [witryny Azure portal](https://portal.azure.com).
* [Użyj nazwy i klucza](#use-a-name-and-key). Jeśli znasz jeden z kluczy konta do konta magazynu, możesz użyć tej opcji, aby szybko się połączyć. Znajdź klucze na stronie konta **Settings** > magazynu, wybierając**pozycję Ustawienia dostępu do kluczy** w [witrynie Azure portal](https://portal.azure.com).
* [Dołącz do lokalnego emulatora](#attach-to-a-local-emulator). Jeśli używasz jednego z dostępnych emulatorów usługi Azure Storage, użyj tej opcji, aby łatwo połączyć się z emulatorem.
* [Połącz się z kontem usługi Azure Cosmos DB przy użyciu ciągu połączenia](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string). Użyj tej opcji, jeśli masz parametry połączenia z wystąpieniem usługi CosmosDB.
* [Połącz się z usługą Azure Data Lake Store za pomocą identyfikatora URI](#connect-to-azure-data-lake-store-by-uri). Użyj tej opcji, jeśli masz identyfikator URI do usługi Azure Data Lake Store.

#### <a name="add-a-resource-via-azure-ad"></a>Dodawanie zasobu za pośrednictwem usługi Azure AD

1. Wybierz symbol **Połącz,** aby otworzyć opcję **Połącz z usługą Azure Storage**.

    ![Opcja Połącz z usługą Azure Storage][9]

1. Jeśli jeszcze tego nie zrobiono, użyj opcji **Dodaj konto platformy Azure,** aby zalogować się do konta platformy Azure, które ma dostęp do zasobu. Po zalogowaniu się wróć **do usługi Połącz do usługi Azure Storage**.

1. Wybierz **pozycję Dodaj zasób za pośrednictwem usługi Azure Active Directory (Azure AD),** a następnie wybierz pozycję **Dalej**.

1. Wybierz konto platformy Azure i dzierżawę. Te wartości muszą mieć dostęp do zasobu magazynu, do którego chcesz dołączyć. Wybierz **pozycję Dalej**.

1. Wybierz typ zasobu, który chcesz dołączyć. Wprowadź informacje potrzebne do nawiązania połączenia. 

   Informacje wprowadzane na tej stronie zależą od typu dodanego zasobu. Upewnij się, aby wybrać odpowiedni typ zasobu. Po wprowadzeniu wymaganych informacji wybierz przycisk **Dalej**.

1. Przejrzyj **podsumowanie połączenia,** aby upewnić się, że wszystkie informacje są poprawne. Jeśli tak, wybierz opcję **Połącz**. W przeciwnym razie wybierz **pozycję Wstecz,** aby powrócić do poprzednich stron, aby naprawić nieprawidłowe informacje.

Po pomyślnym dodaniu połączenia drzewo zasobów przechodzi do węzła, który reprezentuje połączenie. Zasób pojawia się w obszarze Kontenery obiektów blob **dołączane** > **Storage Accounts** > & lokalne **(dołączone kontenery** > **kontenerów).** Jeśli Eksplorator magazynu nie może dodać połączenia lub nie możesz uzyskać dostępu do danych po pomyślnym dodaniu połączenia, zobacz [przewodnik rozwiązywania problemów z Eksploratorem usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="use-a-connection-string"></a>Używanie ciągu połączenia

1. Wybierz symbol **Połącz,** aby otworzyć opcję **Połącz z usługą Azure Storage**.

    ![Opcja Połącz z usługą Azure Storage][9]

1. Wybierz **pozycję Użyj ciągu połączenia**, a następnie wybierz pozycję **Dalej**.

1. Wybierz nazwę wyświetlaną połączenia i wprowadź parametry połączenia. Następnie wybierz przycisk **Dalej**.

1. Przejrzyj **podsumowanie połączenia,** aby upewnić się, że wszystkie informacje są poprawne. Jeśli tak, wybierz opcję **Połącz**. W przeciwnym razie wybierz **pozycję Wstecz,** aby powrócić do poprzednich stron, aby naprawić nieprawidłowe informacje.

Po pomyślnym dodaniu połączenia drzewo zasobów przechodzi do węzła, który reprezentuje połączenie. Zasób pojawi się w obszarze **Lokalne & dołączone** > **konta magazynu**. Jeśli Eksplorator magazynu nie może dodać połączenia lub nie możesz uzyskać dostępu do danych po pomyślnym dodaniu połączenia, zobacz [przewodnik rozwiązywania problemów z Eksploratorem usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="use-a-shared-access-signature-uri"></a>Korzystanie z identyfikatora URI podpisu dostępu udostępnionego

1. Wybierz symbol **Połącz,** aby otworzyć opcję **Połącz z usługą Azure Storage**.

    ![Opcja Połącz z usługą Azure Storage][9]

1. Wybierz **pozycję Użyj identyfikatora URI podpisu dostępu udostępnionego (SAS),** a następnie wybierz pozycję **Dalej**.

1. Wybierz nazwę wyświetlaną połączenia i wprowadź identyfikator URI podpisu dostępu współdzielonego. Punkt końcowy usługi dla typu dołączonego zasobu należy autouzupełniać. Jeśli używasz niestandardowego punktu końcowego, jest możliwe, że może nie. Wybierz **pozycję Dalej**.

1. Przejrzyj **podsumowanie połączenia,** aby upewnić się, że wszystkie informacje są poprawne. Jeśli tak, wybierz opcję **Połącz**. W przeciwnym razie wybierz **pozycję Wstecz,** aby powrócić do poprzednich stron, aby naprawić nieprawidłowe informacje.

Po pomyślnym dodaniu połączenia drzewo zasobów przechodzi do węzła, który reprezentuje połączenie. Zasób pojawia się w obszarze **Lokalne & dołączone** > **konta** > magazynu **(dołączone kontenery)** > *węzeł usługi dla typu dołączonego kontenera*. Jeśli Eksplorator magazynu nie może dodać połączenia, zobacz [przewodnik rozwiązywania problemów z Eksploratorem usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting). Jeśli po pomyślnym dodaniu połączenia nie możesz uzyskać dostępu do danych, zapoznaj się z przewodnikiem rozwiązywania problemów.

#### <a name="use-a-name-and-key"></a>Używanie nazwy i klucza

1. Wybierz symbol **Połącz,** aby otworzyć opcję **Połącz z usługą Azure Storage**.

    ![Opcja Połącz z usługą Azure Storage][9]

1. Wybierz **pozycję Użyj nazwy i klucza konta magazynu,** a następnie wybierz pozycję **Dalej**.

1. Wybierz nazwę wyświetlaną połączenia.

1. Wprowadź nazwę konta magazynu i jeden z jego kluczy dostępu.

1. Wybierz **domenę Magazyn,** której chcesz użyć, a następnie wybierz pozycję **Dalej**.

1. Przejrzyj **podsumowanie połączenia,** aby upewnić się, że wszystkie informacje są poprawne. Jeśli tak, wybierz opcję **Połącz**. W przeciwnym razie wybierz **pozycję Wstecz,** aby powrócić do poprzednich stron, aby naprawić nieprawidłowe informacje.

Po pomyślnym dodaniu połączenia drzewo zasobów przechodzi do węzła, który reprezentuje połączenie. Zasób pojawi się w obszarze **Lokalne & dołączone** > **konta magazynu**. Jeśli Eksplorator magazynu nie może dodać połączenia lub nie możesz uzyskać dostępu do danych po pomyślnym dodaniu połączenia, zobacz [przewodnik rozwiązywania problemów z Eksploratorem usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="attach-to-a-local-emulator"></a>Dołączanie do lokalnego emulatora

Eksplorator magazynu obsługuje obecnie dwa oficjalne emulatory magazynu:

* [Emulator usługi Azure Storage](storage/common/storage-use-emulator.md) (tylko system Windows)
* [Azurite](https://github.com/azure/azurite) (Windows, macOS lub Linux)

Jeśli emulator nasłuchuje na portach domyślnych, można użyć **emulatora — domyślne porty** węzła, aby uzyskać dostęp do emulatora. **Poszukaj emulatora — porty domyślne** w obszarze **Lokalne & dołączone** > **konta magazynu**.

Jeśli chcesz użyć innej nazwy połączenia lub jeśli emulator nie jest uruchomiony na portach domyślnych, wykonaj następujące kroki:

1. Uruchom emulator. Wprowadź polecenie, `AzureStorageEmulator.exe status` aby wyświetlić porty dla każdego typu usługi.

   > [!IMPORTANT]
   > Eksplorator magazynu nie uruchamia automatycznie emulatora. Należy uruchomić go ręcznie.

1. Wybierz symbol **Połącz,** aby otworzyć opcję **Połącz z usługą Azure Storage**.

    ![Opcja Połącz z usługą Azure Storage][9]

1. Wybierz **pozycję Dołącz do emulatora lokalnego,** a następnie wybierz pozycję **Dalej**.

1. Wybierz nazwę wyświetlaną połączenia i wprowadź porty, na które nasłuchuje emulator dla każdego typu usługi. **Dołącz do emulatora lokalnego** sugeruje domyślne wartości portów dla większości emulatorów. **Port plików** jest pusty, ponieważ żaden z oficjalnych emulatorów obecnie nie obsługuje usługi Pliki. Jeśli emulator, którego używasz, obsługuje pliki, możesz wejść do portu, aby użyć. Następnie wybierz przycisk **Dalej**.

1. Przejrzyj **podsumowanie połączenia** i upewnij się, że wszystkie informacje są poprawne. Jeśli tak, wybierz opcję **Połącz**. W przeciwnym razie wybierz **pozycję Wstecz,** aby powrócić do poprzednich stron, aby naprawić nieprawidłowe informacje.

Po pomyślnym dodaniu połączenia drzewo zasobów przechodzi do węzła, który reprezentuje połączenie. Węzeł powinien pojawić się w obszarze **Lokalne & dołączone** > **konta magazynu**. Jeśli Eksplorator magazynu nie może dodać połączenia lub nie możesz uzyskać dostępu do danych po pomyślnym dodaniu połączenia, zobacz [przewodnik rozwiązywania problemów z Eksploratorem usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Łączenie się z kontem usługi Azure Cosmos DB przy użyciu ciągu połączenia

Zamiast zarządzać kontami usługi Azure Cosmos DB za pośrednictwem subskrypcji platformy Azure, możesz połączyć się z usługą Azure Cosmos DB przy użyciu ciągu połączenia. Aby nawiązać połączenie, wykonaj następujące kroki:

1. W obszarze **EXPLORER**rozwiń rozwiń pozycję **& lokalne dołączone**kliknij prawym przyciskiem myszy pozycję Konta usługi **Cosmos DB**i wybierz polecenie **Połącz z usługą Azure Cosmos DB**.

    ![Łączenie się z usługą Azure Cosmos DB przy użyciu parametrów połączenia][21]

1. Wybierz interfejs API usługi Azure Cosmos DB, wprowadź dane **ciągu połączenia,** a następnie wybierz **przycisk OK,** aby połączyć konto usługi Azure Cosmos DB. Aby uzyskać informacje dotyczące pobierania ciągu połączenia, zobacz [Zarządzanie kontem usługi Azure Cosmos](https://docs.microsoft.com/azure/cosmos-db/manage-account).

    ![Parametry połączenia][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>Łączenie się z usługą Azure Data Lake Store za pomocą identyfikatora URI

Można uzyskać dostęp do zasobu, który nie jest w ramach subskrypcji. Potrzebujesz kogoś, kto ma dostęp do tego zasobu, aby dać ci identyfikator URI zasobu. Po zalogowaniu się połącz się z magazynem usługi Data Lake przy użyciu identyfikatora URI. Aby nawiązać połączenie, wykonaj następujące kroki:

1. W obszarze **EXPLORER**rozwiń **rozwiń & lokalny Dołączony**.

1. Kliknij prawym przyciskiem myszy **pozycję Data Lake Storage Gen1**i wybierz polecenie **Połącz z magazynem danych Lake Gen1**.

    ![Łączenie się z menu kontekstowym magazynu Usługi Data Lake](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-connect-data-lake-storage.png)

1. Wprowadź identyfikator URI, a następnie wybierz przycisk **OK**. Sklep Usługi Data Lake jest wyświetlany w obszarze **Magazyn usługi Data Lake**.

    ![Połącz się z wynikiem magazynu Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-attach-data-lake-finished.png)

W tym przykładzie użyto usługi Data Lake Storage Gen1. Usługa Azure Data Lake Storage Gen2 jest już dostępna. Aby uzyskać więcej informacji, zobacz [Co to jest usługa Azure Data Lake Storage Gen1](./data-lake-store/data-lake-store-overview.md).

## <a name="generate-a-shared-access-signature-in-storage-explorer"></a>Generowanie podpisu dostępu współdzielonego w Eksploratorze magazynu<a name="generate-a-sas-in-storage-explorer"></a>

### <a name="account-level-shared-access-signature"></a>Podpis dostępu współdzielonego na poziomie konta

1. Kliknij prawym przyciskiem myszy konto magazynu, które chcesz udostępnić, a następnie wybierz pozycję **Pobierz podpis dostępu współdzielonego**.

    ![Pobierz opcję menu kontekstowego podpisu dostępu współdzielonego][14]

1. W **obszarze Podpis dostępu współdzielonego**określ przedział czasu i uprawnienia, które mają dla konta, a następnie wybierz pozycję **Utwórz**.

    ![Uzyskiwanie podpisu dostępu współdzielonego][15]

1. Skopiuj do schowka **ciąg połączenia** lub nieprzetworzony **ciąg zapytania.**

### <a name="service-level-shared-access-signature"></a>Podpis dostępu współdzielonego na poziomie usługi

Możesz uzyskać podpis dostępu współdzielonego na poziomie usługi. Aby uzyskać więcej informacji, zobacz [Pobierz sygnatury dostępu Współdzielonego dla kontenera obiektów blob](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container).

## <a name="search-for-storage-accounts"></a>Wyszukiwanie kont magazynu

Aby znaleźć zasób magazynu, można wyszukać w okienku **EXPLORER.**

Po wprowadzeniu tekstu w polu wyszukiwania Eksplorator magazynu wyświetla wszystkie zasoby zgodne z wartością wyszukiwania wprowadzona do tego momentu. W tym przykładzie pokazano wyszukiwanie **punktów końcowych:**

![Wyszukiwanie kont magazynu][23]

> [!NOTE]
> Aby przyspieszyć wyszukiwanie, użyj **funkcji Zarządzanie kontem,** aby usunąć zaznaczenie wszystkich subskrypcji, które nie zawierają szukanych elementów. Można również kliknąć prawym przyciskiem myszy węzeł i wybrać **opcję Wyszukaj tutaj,** aby rozpocząć wyszukiwanie z określonego węzła.
>
>

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie zasobami magazynu obiektów Blob platformy Azure za pomocą Eksploratora magazynu](vs-azure-tools-storage-explorer-blobs.md)
* [Praca z danymi za pomocą Eksploratora usługi Azure Storage](./cosmos-db/storage-explorer.md)
* [Zarządzanie zasobami usługi Azure Data Lake Store za pomocą Eksploratora magazynu](./data-lake-store/data-lake-store-in-storage-explorer.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Overview.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageAccounts.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-azure-environment.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/account-panel-subscriptions-apply.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SubscriptionNode.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/PortalAccessKeys.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccessKeys.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-AddWithKeySelected.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-NameAndKeyPage.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount-Detach.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-shared-access-signature-for-storage-explorer.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/create-shared-access-signature-for-storage-explorer.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-WithConnStringOrSASSelected.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-1.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithSASAccount.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-2.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ServiceAttachedWithSAS.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-cosmos-db-by-connection-string.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string-for-cosmos-db.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-search-for-resource.png
