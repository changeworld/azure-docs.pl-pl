---
title: Wprowadzenie do Eksploratora usługi Storage | Dokumentacja firmy Microsoft
description: Zarządzanie zasobami usługi Azure storage za pomocą Eksploratora usługi Storage
services: storage
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: ''
ms.assetid: 1ed0f096-494d-49c4-ab71-f4164ee19ec8
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2017
ms.author: cawa
ms.openlocfilehash: 38a857b1d309b92c48137a46655155e0e131908c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60459432"
---
# <a name="get-started-with-storage-explorer"></a>Wprowadzenie do Eksploratora usługi Storage

## <a name="overview"></a>Omówienie

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

Aby uzyskać dodatkową pomoc, instalowanie Eksploratora usługi Storage w systemie Linux, zobacz [przewodnik rozwiązywania problemów](https://docs.microsoft.com/en-us/azure/storage/common/storage-explorer-troubleshooting#linux-dependencies).

Eksplorator usługi Azure Storage [wersji](https://go.microsoft.com/fwlink/?LinkId=838275&clcid=0x409) zawierają poznać konkretne kroki dla niektórych dystrybucji.

[Pobieranie i instalowanie Eksploratora usługi Storage](https://www.storageexplorer.com)

---

## <a name="connect-to-a-storage-account-or-service"></a>Łączenie się z usługą lub kontem magazynu

Eksplorator usługi Storage oferuje kilka sposobów nawiązywania połączenia z kontami magazynu. Można na przykład:

* Łączyć się z kontami magazynu skojarzonymi z subskrypcjami platformy Azure.
* Łączyć się z usługami i kontami magazynu udostępnianymi z innych subskrypcji platformy Azure.
* Łączyć się z magazynem lokalnym i zarządzać nim przy użyciu emulatora usługi Azure Storage.

Ponadto można pracować z kontami magazynu na globalnej i krajowej platformie Azure:

* [Łączenie się z subskrypcją platformy Azure](#connect-to-an-azure-subscription): Zarządzanie zasobami magazynu należącymi do subskrypcji platformy Azure.
* [Praca z lokalnym magazynem projektowym](#work-with-local-development-storage): Zarządzanie magazynem lokalnym przy użyciu emulatora usługi Azure Storage.
* [Dołączanie do magazynu zewnętrznego](#attach-or-detach-an-external-storage-account): Zarządzanie zasobami magazynu, które należą do innej subskrypcji platformy Azure lub które są w innych chmurach krajowej platformy Azure przy użyciu nazwy, klucza i punktów końcowych konta magazynu.
* [Dołączanie konta magazynu przy użyciu sygnatury dostępu Współdzielonego](#attach-a-storage-account-by-using-a-shared-access-signature-sas): Zarządzanie zasobami magazynu należącymi do innej subskrypcji platformy Azure przy użyciu sygnatury dostępu współdzielonego (SAS).
* [Dołączanie usługi przy użyciu sygnatury dostępu Współdzielonego](#attach-a-service-by-using-a-shared-access-signature-sas): Zarządzanie określoną usługą storage (kontener obiektów blob, kolejką lub tabelą) należącą do innej subskrypcji platformy Azure przy użyciu sygnatury dostępu Współdzielonego.
* [Łączenie z kontem usługi Azure Cosmos DB za pomocą parametrów połączenia](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string): Zarządzanie kontem usługi Cosmos DB przy użyciu parametrów połączenia.

## <a name="connect-to-an-azure-subscription"></a>Łączenie się z subskrypcją platformy Azure

> [!NOTE]
> Jeśli nie masz konta platformy Azure, możesz [utworzyć konto bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) lub [aktywować korzyści dla subskrybentów programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).
>
>

1. W Eksploratorze usługi Storage wybierz **Zarządzanie kontami** można przejść do **Panelu zarządzania kontem**.

    ![Zarządzaj kontami][1]

2. W okienku po lewej stronie zostaną wyświetlone wszystkie konta platformy Azure, których użytkownik został zarejestrowany w usłudze. Aby połączyć z innym kontem, wybierz **Dodaj konto**

3. Jeśli chcesz zalogować się do chmur krajowych lub usługi Azure Stack, wybierz polecenie **środowiska platformy Azure** listę rozwijaną, aby wybrać, jaka chmura platformy Azure, której chcesz użyć. Po wybraniu środowiska kliknij **Zaloguj się...**  przycisku. Jeśli logujesz się do usługi Azure Stack, zobacz [Połącz Eksplorator usługi Storage z subskrypcją usługi Azure Stack](/azure-stack/user/azure-stack-storage-connect-se) Aby uzyskać więcej informacji.

    ![Zaloguj się w — opcja][2]

4. Po pomyślnym zalogowaniu się przy użyciu konta platformy Azure, konta i subskrypcji platformy Azure skojarzone z tym kontem są dodawane do okienka po lewej stronie. Wybierz subskrypcje platformy Azure, które chcesz pracować, a następnie wybierz pozycję **Zastosuj** (wybranie **wszystkie subskrypcje:** wyświetlaniem wszystkich lub żadnej z wymienionych subskrypcji platformy Azure).

    ![Wybieranie subskrypcji platformy Azure][3]

    W okienku po lewej stronie są wyświetlane wszystkie konta magazynu skojarzone z wybranymi subskrypcjami platformy Azure.

    ![Wybrane subskrypcje platformy Azure][4]

## <a name="work-with-local-development-storage"></a>Praca z lokalnym magazynem projektowym

Za pomocą Eksploratora usługi Storage możesz pracować z magazynem lokalnym przy użyciu emulatora. Takie podejście umożliwia symulowanie pracy z usługą Azure Storage bez konieczności posiadania konta magazynu wdrożonego na platformie Azure.

Począwszy od wersji 1.1.0 lokalnym emulatorze magazynu jest obsługiwane na wszystkich platformach. Eksplorator usługi Storage można nawiązać dowolnej usługi emulowanej nasłuchiwania na jego domyślnymi punktami końcowymi magazynu lokalnego.

> [!NOTE]
> Obsługa usług i funkcji magazynowych może różnią w zależności od wybranego emulatora. Upewnij się, że Twoje emulator obsługuje usługi i funkcje, które zamierzasz pracować.

1. Konfigurowanie usług emulatora, wybranym do nasłuchiwania nieużywanego portu.

   Emulowane usługi | Domyślny punkt końcowy
   -----------------|-------------------------
   Obiekty blob            | `http://127.0.0.1:10000`
   Kolejki           | `http://127.0.0.1:10001`
   Tabele           | `http://127.0.0.1:10002`

2. Uruchom emulator.
   > [!IMPORTANT]
   > Eksplorator usługi Storage nie jest automatycznie uruchamiana z emulatora. Należy uruchomić je samodzielnie.

3. W Eksploratorze usługi Storage, kliknij przycisk **Dodaj konto** przycisku. Wybierz **dołączyć do lokalnego emulatora** i kliknij przycisk **dalej**.

4. Wprowadź numery portów dla usług, skonfigurowanych powyżej (pozostaw puste, jeśli nie zamierzasz używać tej usługi). Kliknij przycisk **dalej** następnie **Connect** do utworzenia połączenia.

5. Rozwiń **lokalny i dołączony** > **kont magazynu** > węzłów, następnie rozwiń węzły usługi poniżej tego węzła odpowiadających emulatorze połączenia.

   Ten węzeł umożliwia tworzenie i Praca z lokalnych obiektów blob, kolejki i tabele. Aby dowiedzieć się, jak pracować z poszczególnymi typami kont magazynu, zapoznaj się z następującymi przewodnikami:

   * [Zarządzanie zasobami usługi Azure Blob storage](vs-azure-tools-storage-explorer-blobs.md)
   * [Zarządzanie zasobami usługi Azure File storage](vs-azure-tools-storage-explorer-files.md)

## <a name="attach-or-detach-an-external-storage-account"></a>Dołączanie lub odłączanie konta magazynu zewnętrznego

Za pomocą Eksploratora usługi Storage możesz dołączyć do kont magazynu zewnętrznego, dzięki czemu można łatwo udostępniać konta magazynu. W tej sekcji opisano sposób dołączania (i odłączania) kont magazynu zewnętrznego.

### <a name="get-the-storage-account-credentials"></a>Uzyskiwanie poświadczeń konta magazynu

Aby udostępnić konto magazynu zewnętrznego, właściciel tego konta musi najpierw uzyskać poświadczenia (nazwę konta i klucz) dla konta, a następnie Udostępnij, że informacje o osobie, która chce dołączyć do powiedział konta. Możesz uzyskać poświadczenia konta magazynu w witrynie Azure portal, wykonując następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wybierz pozycję **Przeglądaj**.

3. Wybierz pozycję **Konta usługi Storage**.

4. Na liście **kont magazynu**, wybierz odpowiednie konto magazynu.

5. W obszarze **Ustawienia** wybierz pozycję **Klucze dostępu**.

    ![Opcja Klucze dostępu][7]

6. Kopiuj **nazwa konta magazynu** i **klucz1**.

    ![Klawisze dostępu][8]

### <a name="attach-to-an-external-storage-account"></a>Dołączanie konta magazynu zewnętrznego

Aby dołączyć konto magazynu zewnętrznego, potrzebna jest nazwa konta i klucz. W sekcji „Uzyskiwanie poświadczeń konta magazynu” wyjaśniono sposób uzyskiwania tych wartości z witryny Azure Portal. Należy pamiętać, że w witrynie Azure Portal klucz konta ma nazwę **klucz 1**. Dlatego podczas Eksploratora usługi Storage poprosi o podanie klucza konta usługi, należy wprowadzić **klucz1** wartości.

1. Otwórz w Eksploratorze usługi Storage **połączyć z okna dialogowego**.

    ![Opcja Połącz z usługą Azure Storage][9]

2. W **połączyć z okna dialogowego**, wybierz **Użyj klucza i nazwy konta magazynu**

    ![Dodawanie przy użyciu nazwy i opcji klucza][10]

3. Wklej nazwę konta w **nazwa konta** tekstu i Wklej klucz konta ( **klucz1** wartości z witryny Azure portal) do **klucz konta** polu tekstowym, a następnie wybierz pozycję **Dalej**.

    ![Nazwy i klucza][11]

    > [!NOTE]
    > Aby użyć nazwy i klucza z chmury krajowe, użyj **domena punktów końcowych magazynu:** listę rozwijaną, aby wybrać domenę odpowiednich punktów końcowych:
    >
    >

4. Sprawdź informacje wyświetlone w oknie dialogowym **Podsumowanie połączenia**. Aby wprowadzić zmiany, wybierz przycisk **Wstecz** i ponownie wprowadź odpowiednie ustawienia.

5. Wybierz przycisk **Połącz**.

6. Po pomyślnie dołączono konta magazynu, konto magazynu jest wyświetlane z **(zewnętrzne)** dołączonym do jego nazwy.

    ![Wynik połączenia z kontem magazynu zewnętrznego][12]

### <a name="detach-from-an-external-storage-account"></a>Odłączanie konta magazynu zewnętrznego

1. Kliknij prawym przyciskiem myszy konto magazynu zewnętrznego, które chcesz odłączyć, a następnie wybierz polecenie **Odłącz**.

    ![Opcja odłączenia magazynu][13]

2. W oknie komunikatu z potwierdzeniem wybierz przycisk **Tak**, aby potwierdzić odłączenie konta magazynu zewnętrznego.

## <a name="attach-a-storage-account-by-using-a-shared-access-signature-sas"></a>Dołączanie konta magazynu przy użyciu sygnatury dostępu współdzielonego (SAS)

A Shared Access Signature lub [sygnatury dostępu Współdzielonego](storage/common/storage-dotnet-shared-access-signature-part-1.md), zapewnia administratorowi subskrypcji platformy Azure, do udzielania tymczasowego dostępu do konta magazynu bez konieczności podawania poświadczeń subskrypcji platformy Azure.

Aby zilustrować ten scenariusz, załóżmy, że Użytkownik_A jest administratorem subskrypcji platformy Azure i Użytkownik_A chce zezwolić Użytkownikowi_B na dostęp do konta magazynu przez ograniczony czas z określonymi uprawnieniami:

1. Użytkownik_a generuje ciąg połączenia SAS dla określonego przedziału czasu i z odpowiednimi uprawnieniami.

2. Użytkownik_a udostępnia sygnaturę dostępu Współdzielonego osobie, która chce uzyskać dostęp do konta magazynu (w tym przykładzie Użytkownik_b).

3. Użytkownik_b korzysta z Eksploratora usługi Storage można dołączyć do konta należącego do Użytkownika_a przy użyciu dostarczonej sygnatury dostępu Współdzielonego.

### <a name="generate-a-sas-query-string-for-the-account-you-want-to-share"></a>Generowanie ciągu zapytania sygnatury dostępu Współdzielonego dla konta, które chcesz udostępnić

1. W Eksploratorze usługi Storage, kliknij prawym przyciskiem myszy konto magazynu, które chcesz udostępnić, a następnie wybierz **Uzyskaj sygnaturę dostępu współdzielonego...** .

    ![Opcja menu kontekstowego Uzyskaj sygnaturę dostępu współdzielonego][14]

2. W **wygenerować sygnaturę dostępu współdzielonego** okna dialogowego określ przedział czasu i uprawnienia dla konta, a następnie kliknij przycisk **Utwórz** przycisku.

    ![Uzyskiwanie sygnatury dostępu Współdzielonego, okno dialogowe][15]

3. Obok pozycji **ciągu zapytania** pola tekstowego, wybierz opcję **kopiowania** skopiuj go do Schowka, a następnie kliknij przycisk **Zamknij**.

### <a name="attach-to-a-storage-account-by-using-a-sas-connection-string"></a>Aby dołączyć do konta magazynu przy użyciu parametrów połączenia sygnatury dostępu Współdzielonego

1. Otwórz w Eksploratorze usługi Storage **połączyć z okna dialogowego**.

    ![Opcja Połącz z usługą Azure Storage][9]

2. W **połączyć z okna dialogowego** okno dialogowe, wybierz **za pomocą parametrów połączenia lub identyfikatora URI sygnatury dostępu współdzielonego** a następnie kliknij przycisk **dalej**.

    ![Okno dialogowe Łączenie z usługą Azure Storage][16]

3. Wybierz **Użyj parametrów połączenia** i Wklej parametry połączenia do **ciąg połączenia:** pola. Kliknij przycisk **Dalej**.

    ![Okno dialogowe Łączenie z usługą Azure Storage][17]

4. Sprawdź informacje wyświetlone w oknie dialogowym **Podsumowanie połączenia**. Aby wprowadzić zmiany, wybierz przycisk **Wstecz**, a następnie wprowadź odpowiednie ustawienia.

5. Wybierz przycisk **Połącz**.

6. Po pomyślnie dołączono konta magazynu, konto magazynu jest wyświetlane z **(SAS)** dołączonym do jego nazwy.

    ![Wynik dołączania do konta przy użyciu sygnatury dostępu współdzielonego][18]

## <a name="attach-a-service-by-using-a-shared-access-signature-sas"></a>Dołączanie usługi przy użyciu sygnatury dostępu współdzielonego (SAS)

W sekcji "Dołączanie konta magazynu przy użyciu sygnatury dostępu Współdzielonego" wyjaśniono sposób udzielania tymczasowego dostępu do konta magazynu przez wygenerowanie i udostępnienie sygnatury dostępu Współdzielonego dla konta magazynu przez administratora subskrypcji platformy Azure. Podobnie można wygenerować sygnaturę dostępu Współdzielonego dla określonej usługi (kontenera obiektów blob, kolejka, tabela lub udział plików) w ramach konta magazynu.

### <a name="generate-an-sas-for-the-service-that-you-want-to-share"></a>Generowanie sygnatury dostępu współdzielonego dla usługi, którą chcesz udostępnić

W tym kontekście usługi mogą być kontenera obiektów blob, kolejek, tabel lub udziału plików. Aby wygenerować sygnaturę dostępu współdzielonego dla wymienionych usług, zobacz:

* [Uzyskiwanie sygnatury dostępu współdzielonego dla kontenera obiektów blob](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)

### <a name="attach-to-the-shared-account-service-by-using-a-sas-uri"></a>Dołączanie do usługi udostępnionego konta przy użyciu identyfikatora URI sygnatury dostępu Współdzielonego

1. Otwórz w Eksploratorze usługi Storage **połączyć z okna dialogowego**.

    ![Opcja Połącz z usługą Azure Storage][9]

2. W **połączyć z okna dialogowego** okna dialogowego wybierz **za pomocą parametrów połączenia lub identyfikatora URI sygnatury dostępu współdzielonego** a następnie kliknij przycisk **dalej**.

    ![Okno dialogowe Łączenie z usługą Azure Storage][16]

3. Wybierz **korzystania z identyfikatora URI sygnatury dostępu Współdzielonego** i wklej identyfikator URI do **identyfikatora URI:** pola. Kliknij przycisk **Dalej**.

    ![Okno dialogowe Łączenie z usługą Azure Storage][19]

4. Sprawdź informacje wyświetlone w oknie dialogowym **Podsumowanie połączenia**. Aby wprowadzić zmiany, wybierz przycisk **Wstecz**, a następnie wprowadź odpowiednie ustawienia.

5. Wybierz przycisk **Połącz**.

6. Po dołączone usługi spowoduje wyświetlenie tej usługi w ramach **(SAS-Attached usługi)** węzła.

    ![Wynik dołączenia do usługi udostępnionej przy użyciu sygnatury dostępu współdzielonego][20]

## <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Łączenie z kontem usługi Azure Cosmos DB za pomocą parametrów połączenia

Oprócz zarządzania kontami usługi Azure Cosmos DB przy użyciu subskrypcji platformy Azure, to alternatywny sposób nawiązywania połączenia z usługą Azure Cosmos DB jest użycie parametrów połączenia. Aby nawiązać połączenie przy użyciu parametrów połączenia, wykonaj następujące kroki.

1. Znajdź pozycję **Lokalne i dołączone** w drzewie po lewej stronie, kliknij prawym przyciskiem myszy pozycję **Konta usługi Azure Cosmos DB** i wybierz pozycję **Połącz z usługą Azure Cosmos DB**.

    ![połączyć z usługą Azure Cosmos DB przy użyciu parametrów połączenia][21]

2. Wybierz interfejs API usługi Azure Cosmos DB, Wklej swoje **parametry połączenia**, a następnie kliknij przycisk **OK** połączyć konto usługi Azure Cosmos DB. Aby uzyskać informacje dotyczące pobierania parametrów połączenia, zobacz [Get the connection string (Pobieranie parametrów połączenia)](https://docs.microsoft.com/azure/cosmos-db/manage-account).

    ![connection-string][22]

## <a name="connect-to-azure-data-lake-store-by-uri"></a>Nawiązać połączenie z usługi Azure Data Lake Store przez identyfikator URI

Jeśli chcesz uzyskać dostęp do zasobów, które nie istnieją w Twojej subskrypcji, ale inne osoby przyznają Ci dostęp do identyfikatora URI zasobów. W takim przypadku możesz nawiązać połączenie z usługą Data Lake Store, używając identyfikatora URI po zalogowaniu się. Przyjrzyj się poniższym krokom.

1. Otwórz Eksploratora usługi Storage.
2. W okienku po lewej stronie rozwiń pozycję **Lokalny i dołączony**.
3. Kliknij prawym przyciskiem myszy pozycję **Data Lake Store** i z menu kontekstowego wybierz pozycję **Połącz z usługą Data Lake Store**.

    ![Menu kontekstowe Połącz z usługą Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach.png)

4. Wprowadź identyfikator URI. Narzędzie spowoduje przejście do wprowadzonej lokalizacji adresu URL.

    ![Kontekstowe okno dialogowe Łączenie z usługą Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

    ![Wynik działania polecenia Połącz z usługą Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-attach-finish.png)

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
