---
title: Wprowadzenie do Eksploratora usługi Storage | Dokumentacja firmy Microsoft
description: Zarządzanie zasobami usługi Azure storage za pomocą Eksploratora usługi Storage
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 04/22/2019
ms.author: cawa
ms.openlocfilehash: f7dd6d3d30f34ba2c69b40111bb28d484ce572e7
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508739"
---
# <a name="get-started-with-storage-explorer"></a>Wprowadzenie do Eksploratora usługi Storage

## <a name="overview"></a>Przegląd

Eksplorator usługi Azure Storage jest aplikacją autonomiczną, która umożliwia łatwą obsługę danych w usłudze Azure Storage w Windows, macOS i Linux. W tym artykule dowiesz się kilka sposobów nawiązywania połączenia z kontami i zarządzania nimi usługi Azure storage.

![Microsoft Azure Storage Explorer][0]

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

Eksplorator usługi Azure Storage jest obsługiwana w następujących wersjach systemu Windows:

* Windows 10 (recommended)
* Windows 8
* Windows 7

Wszystkie wersje systemu Windows, platformy .NET Framework 4.6.2 jest nowsze niż wymagana.

[Pobieranie i instalowanie Eksploratora usługi Storage](https://www.storageexplorer.com)

# <a name="macostabmacos"></a>[macOS](#tab/macos)

Eksplorator usługi Azure Storage jest obsługiwana w następujących wersjach systemu macOS:

* System macOS 10.12 "Sierra" i nowsze wersje

[Pobieranie i instalowanie Eksploratora usługi Storage](https://www.storageexplorer.com)

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

Eksplorator usługi Azure Storage jest obsługiwana na poniższe dystrybucje systemu Linux:

* Ubuntu 18.04 x64
* Ubuntu 16.04 x64
* Ubuntu 14.04 x64

Eksplorator usługi Azure Storage może działać na inne dystrybucje, ale oficjalnie obsługiwane są tylko wymienione powyżej.

Aby uzyskać dodatkową pomoc, instalowanie Eksploratora usługi Storage w systemie Linux, zobacz [przewodnik rozwiązywania problemów](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#linux-dependencies).

Eksplorator usługi Azure Storage [wersji](https://go.microsoft.com/fwlink/?LinkId=838275&clcid=0x409) zawierają poznać konkretne kroki dla niektórych dystrybucji.

[Pobieranie i instalowanie Eksploratora usługi Storage](https://www.storageexplorer.com)

---

## <a name="connect-to-a-storage-account-or-service"></a>Łączenie się z usługą lub kontem magazynu

Eksplorator usługi Storage oferuje kilka sposobów nawiązywania połączenia z kontami magazynu. Ogólnie rzecz biorąc można:

* [Logowanie do platformy Azure, dostęp do subskrypcji i ich zasobów](#sign-in-to-azure)
* [Dołączanie na określony zasób magazynu lub bazy danych cosmos DB](#attach-a-specific-resource)

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

> [!NOTE]
> Po zarejestrowaniu się w pełni dostęp do zasobów, Eksploratora usługi Storage wymaga uprawnień warstwy danych i zarządzania (ARM). Oznacza to, że wymagane uprawnienia usługi Azure AD, które umożliwiają dostęp do konta magazynu, kontenerów na koncie, a dane w kontenerach. Jeśli masz uprawnienia tylko w warstwie danych, należy rozważyć użycie [Dołącz z usługą Azure AD](#add-a-resource-via-azure-ad). Aby uzyskać więcej informacji na temat Eksploratora usługi Storage wymaga uprawnienia, zobacz [przewodnik rozwiązywania problemów z](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#role-based-access-control-permission-issues).
>
>

1. W Eksploratorze usługi Storage wybierz **Zarządzanie kontami** można przejść do **Panelu zarządzania kontem**.

    ![Zarządzanie kontami][1]

2. W okienku po lewej stronie zostaną wyświetlone wszystkie konta platformy Azure, których użytkownik został zarejestrowany w usłudze. Aby połączyć z innym kontem, wybierz **Dodaj konto**

3. Jeśli chcesz zalogować się do chmur krajowych lub usługi Azure Stack, wybierz polecenie **środowiska platformy Azure** listę rozwijaną, aby wybrać, jaka chmura platformy Azure, której chcesz użyć. Po wybraniu środowiska kliknij **Zaloguj się...**  przycisku. Jeśli logujesz się do usługi Azure Stack, zobacz [Połącz Eksplorator usługi Storage z subskrypcją usługi Azure Stack](/azure-stack/user/azure-stack-storage-connect-se) Aby uzyskać więcej informacji.

    ![Zaloguj się w — opcja][2]

4. Po pomyślnym zalogowaniu się przy użyciu konta platformy Azure, konta i subskrypcji platformy Azure skojarzone z tym kontem są dodawane do okienka po lewej stronie. Wybierz subskrypcje platformy Azure, które chcesz pracować, a następnie wybierz pozycję **Zastosuj** (wybranie **wszystkie subskrypcje:** wyświetlaniem wszystkich lub żadnej z wymienionych subskrypcji platformy Azure).

    ![Wybieranie subskrypcji platformy Azure][3]

    W okienku po lewej stronie są wyświetlane wszystkie konta magazynu skojarzone z wybranymi subskrypcjami platformy Azure.

    ![Wybrane subskrypcje platformy Azure][4]

### <a name="attach-a-specific-resource"></a>Dołącz określonego zasobu
    
Istnieją różne opcje dołączania zasób do Eksploratora usługi Storage. Możesz:

* [Dodaj zasób za pomocą usługi Azure AD](#add-a-resource-via-azure-ad): Jeśli masz uprawnienia tylko w warstwie danych, można użyć tę opcję, aby dodać kontener obiektów Blob lub kontenera usługi Azure Data Lake Store Gen2 Blob.
* [Użyj parametrów połączenia](#use-a-connection-string): Jeśli masz parametry połączenia z kontem magazynu. Eksplorator usługi Storage obsługuje zarówno klucz i [sygnatury dostępu Współdzielonego](storage/common/storage-dotnet-shared-access-signature-part-1.md) parametry połączenia.
* [Użyj identyfikatora URI sygnatury dostępu Współdzielonego](#use-a-sas-uri): Jeśli masz [sygnatury dostępu Współdzielonego](storage/common/storage-dotnet-shared-access-signature-part-1.md) identyfikator URI do kontenera obiektów Blob, udziału plików, kolejki lub tabeli. Aby uzyskać identyfikator URI sygnatury dostępu Współdzielonego, można użyć [Eksploratora usługi Storage](#generate-a-sas-in-storage-explorer) lub [witryny Azure portal](https://portal.azure.com).
* [Użyj nazwy i klucza](#use-a-name-and-key): Jeśli znasz jeden z kluczy konta do swojego konta magazynu, można użyć tę opcję, szybko nawiązać. Klucze konta magazynu znajdują się na koncie magazynu **klucze dostępu** bloku [witryny Azure portal](https://portal.azure.com).
* [Dołącz do lokalnego emulatora](#attach-to-a-local-emulator): Jeśli używasz jednego z dostępnych emulatorów usługi Azure Storage, użyj tej opcji, aby łatwo uzyskiwać dostęp do Twojego emulatora.
* [Łączenie z kontem usługi Azure Cosmos DB za pomocą parametrów połączenia](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string): Jeśli masz parametry połączenia z wystąpieniem bazy danych cosmos DB.
* [Nawiązać połączenie z usługi Azure Data Lake Store przez identyfikator URI](#connect-to-azure-data-lake-store-by-uri): Jeśli masz identyfikator URI do usługi Azure Data Lake Store.

#### <a name="add-a-resource-via-azure-ad"></a>Dodaj zasób za pomocą usługi Azure AD

1. Otwórz **połączyć z okna dialogowego** , klikając **przycisk Połącz** po lewej, pionowym pasku narzędzi.

    ![Opcja Połącz z usługą Azure Storage][9]

2. Jeśli jeszcze tego nie zrobiono, należy użyć **Dodaj konto platformy Azure** opcję, aby zalogować się do konta platformy Azure, które ma dostęp do zasobu. Po zarejestrowaniu się w zamian **połączyć z okna dialogowego**.

3. Wybierz **Dodaj zasób za pomocą usługi Azure Active Directory (Azure AD)** opcji, a następnie kliknij przycisk **dalej**.

4. Wybierz konto platformy Azure, który ma dostęp do zasobów magazynu, którego chcesz dołączyć i subskrypcją, w której zasób, a następnie kliknij przycisk **dalej**.

5. Wybierz typ zasobu, który chcesz dołączyć, a następnie wprowadź informacje wymagane do połączenia. Dane wejściowe na tej stronie zmieni się w zależności od rodzaju zasobów, które dodajesz. Upewnij się wybrać poprawny typ zasobu. Po wprowadzeniu wymaganych informacji kliknij **dalej**.

6. Przejrzyj podsumowanie połączenia i upewnij się, że wszystkie informacje są poprawne. Jeśli wszystkie informacje wydaje się prawidłowe kliknij **Connect**, w przeciwnym razie wróć do poprzedniej strony z **ponownie** przycisk, aby rozwiązać problem z żadnych informacji.

Po pomyślnym dodaniu połączenia drzewo zasobów automatycznie spowoduje przejście do węzła reprezentującą połączenie. Jeśli z jakiegoś powodu nie jest Sprawdź w obszarze **lokalny i dołączony** → **kont magazynu** → **(kontenery dołączony)** → **kontenery obiektów Blob** . Jeśli programu Storage Explorer nie może dodać połączenie, lub jeśli użytkownik nie może uzyskiwać dostęp do danych po pomyślnym dodaniu połączenia, a następnie zapoznaj się z [przewodnik rozwiązywania problemów z](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) Aby uzyskać pomoc.

#### <a name="use-a-connection-string"></a>Użyj parametrów połączenia

1. Otwórz **połączyć z okna dialogowego** , klikając **przycisk Połącz** po lewej, pionowym pasku narzędzi.

    ![Opcja Połącz z usługą Azure Storage][9]

2. Wybierz **Użyj parametrów połączenia** opcji, a następnie kliknij przycisk **dalej**.

3. Wybierz nazwę wyświetlaną w celu nawiązania połączenia i wprowadź parametry połączenia. Następnie kliknij przycisk **Next** (Dalej).

4. Przejrzyj podsumowanie połączenia i upewnij się, że wszystkie informacje są poprawne. Jeśli wszystkie informacje wydaje się prawidłowe kliknij **Connect**, w przeciwnym razie wróć do poprzedniej strony z **ponownie** przycisk, aby rozwiązać problem z żadnych informacji.

Po pomyślnym dodaniu połączenia drzewo zasobów automatycznie spowoduje przejście do węzła reprezentującą połączenie. Jeśli z jakiegoś powodu nie jest Sprawdź w obszarze **lokalny i dołączony** → **kont magazynu**. Jeśli programu Storage Explorer nie może dodać połączenie, lub jeśli użytkownik nie może uzyskiwać dostęp do danych po pomyślnym dodaniu połączenia, a następnie zapoznaj się z [przewodnik rozwiązywania problemów z](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) Aby uzyskać pomoc.

#### <a name="use-a-sas-uri"></a>Użyj identyfikatora URI sygnatury dostępu Współdzielonego

1. Otwórz **połączyć z okna dialogowego** , klikając **przycisk Połącz** po lewej, pionowym pasku narzędzi.

    ![Opcja Połącz z usługą Azure Storage][9]

2. Wybierz **Użyj sygnatury dostępu współdzielonego (SAS) identyfikator URI** opcji, a następnie kliknij przycisk **dalej**.

3. Wybierz nazwę wyświetlaną w celu nawiązania połączenia i wprowadź identyfikator URI sygnatury dostępu Współdzielonego. Punkt końcowy usługi dla typu zasobu, który jest podłączany powinna automatycznego uzupełniania. Jeśli używasz niestandardowego punktu końcowego jest możliwe, nie może. Kliknij przycisk **Dalej**.

4. Przejrzyj podsumowanie połączenia i upewnij się, że wszystkie informacje są poprawne. Jeśli wszystkie informacje wydaje się prawidłowe kliknij **Connect**, w przeciwnym razie wróć do poprzedniej strony z **ponownie** przycisk, aby rozwiązać problem z żadnych informacji.

Po pomyślnym dodaniu połączenia drzewo zasobów automatycznie spowoduje przejście do węzła reprezentującą połączenie. Jeśli z jakiegoś powodu nie jest Sprawdź w obszarze **lokalny i dołączony** → **kont magazynu** → **(kontenery dołączony)** → **węzła usługi dla typu kontener, w przypadku dołączenia**. Jeśli programu Storage Explorer nie może dodać połączenie, lub jeśli użytkownik nie może uzyskiwać dostęp do danych po pomyślnym dodaniu połączenia, a następnie zapoznaj się z [przewodnik rozwiązywania problemów z](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) Aby uzyskać pomoc.

#### <a name="use-a-name-and-key"></a>Użyj nazwy i klucza

1. Otwórz **połączyć z okna dialogowego** , klikając **przycisk Połącz** po lewej, pionowym pasku narzędzi.

    ![Opcja Połącz z usługą Azure Storage][9]

2. Wybierz **Użyj klucza i nazwy konta magazynu** opcji, a następnie kliknij przycisk **dalej**.

3. Wybierz nazwę wyświetlaną dla połączenia.

4. Wprowadź nazwę konta magazynu i jeden z kluczy dostępu.

5. Wybierz **domena magazynu** użyć, a następnie kliknij przycisk **dalej**.

4. Przejrzyj podsumowanie połączenia i upewnij się, że wszystkie informacje są poprawne. Jeśli wszystkie informacje wydaje się prawidłowe kliknij **Connect**, w przeciwnym razie wróć do poprzedniej strony z **ponownie** przycisk, aby rozwiązać problem z żadnych informacji.

Po pomyślnym dodaniu połączenia drzewo zasobów automatycznie spowoduje przejście do węzła reprezentującą połączenie. Jeśli z jakiegoś powodu nie jest Sprawdź w obszarze **lokalny i dołączony** → **kont magazynu**. Jeśli programu Storage Explorer nie może dodać połączenie, lub jeśli użytkownik nie może uzyskiwać dostęp do danych po pomyślnym dodaniu połączenia, a następnie zapoznaj się z [przewodnik rozwiązywania problemów z](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) Aby uzyskać pomoc.

#### <a name="attach-to-a-local-emulator"></a>Dołącz do emulatora lokalnego

Eksplorator usługi Storage obsługuje emulatorów na wszystkich platformach. Dwa obecnie oficjalne emulatorów dostępne są:
* [Emulator usługi Azure storage](storage/common/storage-use-emulator.md) (tylko Windows)
* [Azurite](https://github.com/azure/azurite) (Windows, macOS lub Linux)

Jeśli Twoje emulator działa na domyślnych portach można użyć **emulatora — domyślne porty** węzła, który zawsze znajdują się w obszarze **lokalny i dołączony** → **kont magazynu** , aby szybko uzyskać dostęp do Twojego emulatora. Jeśli chcesz użyć innej nazwy dla danego połączenia lub jeśli emulator usługi nie jest uruchomiona na domyślnych portach, następnie postępuj zgodnie z poniższych kroków.

1. Uruchom emulator usługi. Gdy Zwróć uwagę na jakie porty emulator nasłuchuje dla każdego typu usług. Musisz znać te informacje później.

   > [!IMPORTANT]
   > Eksplorator usługi Storage nie jest automatycznie uruchamiana z emulatora. Należy uruchomić je samodzielnie.

2. Otwórz **połączyć z okna dialogowego** , klikając **przycisk Połącz** po lewej, pionowym pasku narzędzi.

    ![Opcja Połącz z usługą Azure Storage][9]

3. Wybierz **dołączyć do lokalnego emulatora** opcji, a następnie kliknij przycisk **dalej**.

4. Wybierz nazwę wyświetlaną w celu nawiązania połączenia i wprowadź w porty, które Twoja emulatora nasłuchuje dla każdego typu usług. Domyślnie pól tekstowych będzie zawierać domyślne wartości portów dla większości emulatorów. **Pliki portu** jest również puste domyślnie jako żadne oficjalne emulatorów aktualnie obsługuje usługi pliki. Jeśli emulator, którego używasz obsługuje jednak, można wprowadzić do portu, który jest używany. Kliknij przycisk **Dalej**.

5. Przejrzyj podsumowanie połączenia i upewnij się, że wszystkie informacje są poprawne. Jeśli wszystkie informacje wydaje się prawidłowe kliknij **Connect**, w przeciwnym razie wróć do poprzedniej strony z **ponownie** przycisk, aby rozwiązać problem z żadnych informacji.

Po pomyślnym dodaniu połączenia drzewo zasobów automatycznie spowoduje przejście do węzła reprezentującą połączenie. Jeśli z jakiegoś powodu nie jest Sprawdź w obszarze **lokalny i dołączony** → **kont magazynu**. Jeśli programu Storage Explorer nie może dodać połączenie, lub jeśli użytkownik nie może uzyskiwać dostęp do danych po pomyślnym dodaniu połączenia, a następnie zapoznaj się z [przewodnik rozwiązywania problemów z](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) Aby uzyskać pomoc.

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Łączenie z kontem usługi Azure Cosmos DB za pomocą parametrów połączenia

Oprócz zarządzania kontami usługi Azure Cosmos DB przy użyciu subskrypcji platformy Azure, to alternatywny sposób nawiązywania połączenia z usługą Azure Cosmos DB jest użycie parametrów połączenia. Aby nawiązać połączenie przy użyciu parametrów połączenia, wykonaj następujące kroki.

1. Znajdź pozycję **Lokalne i dołączone** w drzewie po lewej stronie, kliknij prawym przyciskiem myszy pozycję **Konta usługi Azure Cosmos DB** i wybierz pozycję **Połącz z usługą Azure Cosmos DB**.

    ![połączyć z usługą Azure Cosmos DB przy użyciu parametrów połączenia][21]

2. Wybierz interfejs API usługi Azure Cosmos DB, Wklej swoje **parametry połączenia**, a następnie kliknij przycisk **OK** połączyć konto usługi Azure Cosmos DB. Aby uzyskać informacje dotyczące pobierania parametrów połączenia, zobacz [Get the connection string (Pobieranie parametrów połączenia)](https://docs.microsoft.com/azure/cosmos-db/manage-account).

    ![connection-string][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>Nawiązać połączenie z usługi Azure Data Lake Store przez identyfikator URI

Jeśli chcesz uzyskać dostęp do zasobów, które nie istnieją w Twojej subskrypcji, ale inne osoby przyznają Ci dostęp do identyfikatora URI zasobów. W takim przypadku możesz nawiązać połączenie z usługą Data Lake Store, używając identyfikatora URI po zalogowaniu się. Przyjrzyj się poniższym krokom.

1. Otwórz Eksploratora usługi Storage.
2. W okienku po lewej stronie rozwiń pozycję **Lokalny i dołączony**.
3. Kliknij prawym przyciskiem myszy pozycję **Data Lake Store** i z menu kontekstowego wybierz pozycję **Połącz z usługą Data Lake Store**.

    ![Menu kontekstowe Połącz z usługą Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach.png)

4. Wprowadź identyfikator URI. Narzędzie spowoduje przejście do wprowadzonej lokalizacji adresu URL.

    ![Kontekstowe okno dialogowe Łączenie z usługą Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

    ![Wynik działania polecenia Połącz z usługą Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-attach-finish.png)


## <a name="generate-a-sas-in-storage-explorer"></a>Generowanie sygnatury dostępu Współdzielonego w Eksploratorze usługi Storage

### <a name="account-level-sas"></a>Konto dostępu Współdzielonego na poziomie

1. Kliknij prawym przyciskiem myszy konto magazynu, które chcesz udostępnić, a następnie wybierz **Uzyskaj sygnaturę dostępu współdzielonego...** .

    ![Opcja menu kontekstowego Uzyskaj sygnaturę dostępu współdzielonego][14]

2. W **wygenerować sygnaturę dostępu współdzielonego** okna dialogowego określ przedział czasu i uprawnienia dla konta, a następnie kliknij przycisk **Utwórz** przycisku.

    ![Uzyskiwanie sygnatury dostępu Współdzielonego, okno dialogowe][15]

3. Możesz teraz Skopiuj **parametry połączenia** lub nieprzetworzonych **ciągu zapytania** do Schowka.

### <a name="service-level-sas"></a>Poziom usług sygnatury dostępu Współdzielonego

[Sposób uzyskiwania sygnatury dostępu Współdzielonego dla kontenera obiektów blob w Eksploratorze usługi Storage](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)

## <a name="search-for-storage-accounts"></a>Wyszukiwanie kont magazynu

Jeśli chcesz znaleźć zasobu magazynu i nie wiesz, gdzie jest, można użyć pola wyszukiwania w górnej części okienka po lewej stronie do wyszukiwania dla zasobu.

Podczas wpisywania w polu wyszukiwania w okienku po lewej stronie wyświetla wszystkie zasoby, które pasują do wartości wyszukiwania, wprowadzone do tego punktu. Na przykład wyszukiwanie **punktów końcowych** przedstawiono na poniższym zrzucie ekranu:

![Wyszukiwanie kont magazynu][23]

> [!NOTE]
> Użyj **Panelu zarządzania kontem** zaznaczenie wszystkich subskrypcji, które nie zawierają elementu, aby poprawić czas wykonywania wyszukiwania są wyszukiwane. Można również kliknąć prawym przyciskiem myszy w węźle i wybierz **wyszukiwania z tutaj** się rozpocząć wyszukiwanie z określonego węzła.
>
>

## <a name="next-steps"></a>Kolejne kroki

* [Zarządzanie zasobami usługi Azure Blob Storage za pomocą Eksploratora usługi Storage](vs-azure-tools-storage-explorer-blobs.md)
* [Zarządzanie usługą Azure Cosmos DB w Eksploratorze usługi Azure Storage (wersja zapoznawcza)](./cosmos-db/storage-explorer.md)
* [Zarządzanie zasobami usługi Azure Data Lake Store za pomocą Eksploratora usługi Storage](./data-lake-store/data-lake-store-in-storage-explorer.md)

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
