---
title: Importowanie i eksportowanie danych w pamięci podręcznej platformy Azure dla redis
description: Dowiedz się, jak importować i eksportować dane do i z magazynu obiektów blob za pomocą najwyższej jakości pamięci podręcznej azure dla wystąpień Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/31/2017
ms.author: yegu
ms.openlocfilehash: 29ad5ca6c9058b88a539c7a3bb8ace4d9a65083a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278092"
---
# <a name="import-and-export-data-in-azure-cache-for-redis"></a>Importowanie i eksportowanie danych w pamięci podręcznej platformy Azure dla redis
Import/Eksport to operacja zarządzania danymi usługi Azure Cache for Redis, która umożliwia importowanie danych do pamięci podręcznej Azure Cache for Redis lub eksportowanie danych z usługi Azure Cache for Redis przez importowanie i eksportowanie migawki usługi Azure Cache for Redis Database (RDB) z pamięci podręcznej premium do obiektu blob na koncie usługi Azure Storage.

- **Eksportuj** — możesz wyeksportować pamięć podręczną Azure dla migawek RDB redis do obiektu blob strony.
- **Import -** można zaimportować pamięć podręczną Azure dla migawek RDB Redis z obiektu blob strony lub bloku obiektu blob.

Import/eksport umożliwia migrację między różnymi pamięciami podręcznymi platformy Azure dla wystąpień Redis lub wypełnić pamięć podręczną danymi przed użyciem.

Ten artykuł zawiera przewodnik dotyczący importowania i eksportowania danych za pomocą usługi Azure Cache for Redis i zawiera odpowiedzi na często zadawane pytania.

> [!IMPORTANT]
> Import/Eksport jest dostępny tylko dla pamięci podręcznych [warstwy premium.](cache-premium-tier-intro.md)
>
>

## <a name="import"></a>Import
Import może służyć do przynoszenia zgodnych z Redis plików RDB z dowolnego serwera Redis działającego w dowolnej chmurze lub środowisku, w tym Redis działającego w systemie Linux, Windows lub dowolnym dostawcy chmury, takiego jak Amazon Web Services i innych. Importowanie danych to prosty sposób na utworzenie pamięci podręcznej z wstępnie wypełnionymi danymi. Podczas procesu importowania usługa Azure Cache for Redis ładuje pliki RDB z magazynu platformy Azure do pamięci, a następnie wstawia klucze do pamięci podręcznej.

> [!NOTE]
> Przed rozpoczęciem operacji importowania upewnij się, że plik lub pliki bazy danych Redis (RDB) są przekazywane do strony lub blokują obiekty blob w usłudze Azure Storage w tym samym regionie i subskrypcji co usługa Azure Cache dla wystąpienia Redis. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do magazynu obiektów Blob platformy Azure](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Jeśli plik RDB został wyeksportowany przy użyciu funkcji [Eksportuj usługi Azure Cache for Redis,](#export) plik RDB jest już przechowywany w stronicowym obiekcie blob i jest gotowy do zaimportowania.
>
>

1. Aby zaimportować co najmniej jeden eksportowany obiekt blob pamięci podręcznej, [przejdź do pamięci podręcznej](cache-configure.md#configure-azure-cache-for-redis-settings) w witrynie Azure portal i kliknij polecenie **Importuj dane** z menu **Zasób**.

    ![Importowanie danych](./media/cache-how-to-import-export-data/cache-import-data.png)
2. Kliknij **pozycję Wybierz obiekty blob i** wybierz konto magazynu zawierające dane do zaimportowania.

    ![Wybieranie konta magazynu](./media/cache-how-to-import-export-data/cache-import-choose-storage-account.png)
3. Kliknij kontener zawierający dane do zaimportowania.

    ![Wybierz kontener](./media/cache-how-to-import-export-data/cache-import-choose-container.png)
4. Zaznacz jeden lub więcej obiektów blob do zaimportowania, klikając obszar po lewej stronie nazwy obiektu blob, a następnie kliknij przycisk **Wybierz**.

    ![Wybieranie obiektów blob](./media/cache-how-to-import-export-data/cache-import-choose-blobs.png)
5. Kliknij **przycisk Importuj,** aby rozpocząć proces importowania.

   > [!IMPORTANT]
   > Pamięć podręczna nie jest dostępna dla klientów pamięci podręcznej podczas procesu importowania, a wszystkie istniejące dane w pamięci podręcznej są usuwane.
   >
   >

    ![Import](./media/cache-how-to-import-export-data/cache-import-blobs.png)

    Postęp operacji importowania można monitorować, wykonując powiadomienia z witryny Azure portal lub wyświetlając zdarzenia w [dzienniku inspekcji.](../azure-resource-manager/management/view-activity-logs.md)

    ![Postęp importu](./media/cache-how-to-import-export-data/cache-import-data-import-complete.png)

## <a name="export"></a>Eksportowanie
Eksport umożliwia eksportowanie danych przechowywanych w pamięci podręcznej platformy Azure dla redis do plików RDB zgodnych z redis. Za pomocą tej funkcji można przenieść dane z jednej pamięci podręcznej platformy Azure dla wystąpienia Redis do innego lub do innego serwera Redis. Podczas procesu eksportowania plik tymczasowy jest tworzony na maszynie Wirtualnej, która obsługuje wystąpienie serwera usługi Azure Cache for Redis, a plik jest przekazywalny do wyznaczonego konta magazynu. Po zakończeniu operacji eksportowania ze stanem sukcesu lub niepowodzenia plik tymczasowy jest usuwany.

1. Aby wyeksportować bieżącą zawartość pamięci podręcznej do magazynu, [przejdź do pamięci podręcznej](cache-configure.md#configure-azure-cache-for-redis-settings) w witrynie Azure portal i kliknij polecenie **Eksportuj dane** z menu **Zasób**.

    ![Wybierz kontener magazynowy](./media/cache-how-to-import-export-data/cache-export-data-choose-storage-container.png)
2. Kliknij **pozycję Wybierz kontener magazynu** i wybierz żądane konto magazynu. Konto magazynu musi znajdować się w tej samej subskrypcji i regionie co pamięć podręczna.

   > [!IMPORTANT]
   > Eksportowanie działa z obiektami blob strony, które są obsługiwane zarówno przez klasyczne, jak i konta magazynu Menedżera zasobów, ale nie są obsługiwane przez konta magazynu obiektów Blob w tej chwili. Aby uzyskać więcej informacji, zobacz [Omówienie konta magazynu platformy Azure](../storage/common/storage-account-overview.md).
   >

    ![Konto magazynu](./media/cache-how-to-import-export-data/cache-export-data-choose-account.png)
3. Wybierz żądany kontener obiektów blob i kliknij przycisk **Wybierz**. Aby użyć nowego kontenera, kliknij przycisk **Dodaj kontener,** aby dodać go najpierw, a następnie wybierz go z listy.

    ![Wybierz kontener magazynowy](./media/cache-how-to-import-export-data/cache-export-data-container.png)
4. Wpisz **prefiks nazwy obiektu Blob** i kliknij pozycję **Eksportuj,** aby rozpocząć proces eksportowania. Prefiks nazwy obiektu blob służy do prefiksu nazw plików generowanych przez tę operację eksportowania.

    ![Eksportowanie](./media/cache-how-to-import-export-data/cache-export-data.png)

    Postęp operacji eksportowania można monitorować, wykonując powiadomienia z witryny Azure portal lub wyświetlając zdarzenia w [dzienniku inspekcji.](../azure-resource-manager/management/view-activity-logs.md)

    ![Pełne eksportowanie danych](./media/cache-how-to-import-export-data/cache-export-data-export-complete.png)

    Pamięci podręczne pozostają dostępne do użycia podczas procesu eksportowania.

## <a name="importexport-faq"></a>Często zadawane pytania dotyczące importu/eksportu
Ta sekcja zawiera często zadawane pytania dotyczące funkcji Importuj/Eksportuj.

* [Jakich warstw cenowych można używać importu/eksportowania?](#what-pricing-tiers-can-use-importexport)
* [Czy mogę importować dane z dowolnego serwera Redis?](#can-i-import-data-from-any-redis-server)
* [Jakie wersje RDB mogę zaimportować?](#what-rdb-versions-can-i-import)
* [Czy moja pamięć podręczna jest dostępna podczas operacji importowania/eksportowania?](#is-my-cache-available-during-an-importexport-operation)
* [Czy mogę używać klastra Importuj/Eksportuj z Redis?](#can-i-use-importexport-with-redis-cluster)
* [Jak importuj/eksportuj z ustawieniem niestandardowych baz danych?](#how-does-importexport-work-with-a-custom-databases-setting)
* [Czym importuje/eksportuje się od trwałości Redis?](#how-is-importexport-different-from-redis-persistence)
* [Czy można zautomatyzować importowanie/eksportowanie przy użyciu programu PowerShell, interfejsu wiersza polecenia lub innych klientów zarządzania?](#can-i-automate-importexport-using-powershell-cli-or-other-management-clients)
* [Wystąpił błąd limitu czasu podczas operacji importowania/eksportowania. Co to oznacza?](#i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean)
* [Wystąpił błąd podczas eksportowania danych do usługi Azure Blob Storage. Co się stało?](#i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened)

### <a name="what-pricing-tiers-can-use-importexport"></a>Jakich warstw cenowych można używać importu/eksportowania?
Import/Eksport jest dostępny tylko w warstwie cenowej premium.

### <a name="can-i-import-data-from-any-redis-server"></a>Czy mogę importować dane z dowolnego serwera Redis?
Tak, oprócz importowania danych eksportowanych z pamięci podręcznej azure dla wystąpień Redis, można importować pliki RDB z dowolnego serwera Redis działającego w dowolnej chmurze lub środowisku, takich jak Linux, Windows lub dostawców chmury, takich jak Amazon Web Services. Aby to zrobić, przekaż plik RDB z żądanego serwera Redis do strony lub blokuj obiekt blob na koncie usługi Azure Storage, a następnie zaimportuj go do pamięci podręcznej usługi Azure premium dla wystąpienia Redis. Na przykład można wyeksportować dane z produkcyjnej pamięci podręcznej i zaimportować je do pamięci podręcznej używanej jako część środowiska przejściowego do testowania lub migracji.

> [!IMPORTANT]
> Aby pomyślnie zaimportować dane eksportowane z serwerów Redis innych niż Azure Cache for Redis podczas korzystania z obiektu blob strony, rozmiar obiektu blob strony musi być wyrównany na granicy 512 bajtów. Aby uzyskać przykładowy kod do wykonania wymaganego dopełnienia bajtów, zobacz [Przykładowy podział obiektu blob strony](https://github.com/JimRoberts-MS/SamplePageBlobUpload).
>
>

### <a name="what-rdb-versions-can-i-import"></a>Jakie wersje RDB mogę zaimportować?

Usługa Azure Cache for Redis obsługuje importowanie bazy danych RDB do wersji 7 RDB.

### <a name="is-my-cache-available-during-an-importexport-operation"></a>Czy moja pamięć podręczna jest dostępna podczas operacji importowania/eksportowania?
* **Eksportuj** — pamięci podręczne pozostają dostępne i można nadal używać pamięci podręcznej podczas operacji eksportowania.
* **Import —** pamięci podręczne stają się niedostępne po rozpoczęciu operacji importowania i stają się dostępne do użycia po zakończeniu operacji importowania.

### <a name="can-i-use-importexport-with-redis-cluster"></a>Czy mogę używać klastra Importuj/Eksportuj z Redis?
Tak i można importować/eksportować między klastrowaną pamięcią podręczną a pamięcią podręczną nieklastrowaną. Ponieważ klaster Redis [obsługuje tylko bazę danych 0,](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)wszystkie dane w bazach danych innych niż 0 nie są importowane. Podczas importowania danych klastrowanej pamięci podręcznej klucze są rozdzielane między fragmenty klastra.

### <a name="how-does-importexport-work-with-a-custom-databases-setting"></a>Jak importuj/eksportuj z ustawieniem niestandardowych baz danych?
Niektóre warstwy cenowe mają różne [limity baz danych,](cache-configure.md#databases)więc istnieją pewne zagadnienia `databases` podczas importowania, jeśli skonfigurowano wartość niestandardową dla ustawienia podczas tworzenia pamięci podręcznej.

* Podczas importowania do warstwy cenowej z niższym `databases` limitem niż warstwa, z której została wyeksportowana:
  * Jeśli używasz domyślnej `databases`liczby , która wynosi 16 dla wszystkich warstw cenowych, żadne dane nie zostaną utracone.
  * Jeśli używasz niestandardowej `databases` liczby, która mieści się w granicach warstwy, do której są importowane, żadne dane nie zostaną utracone.
  * Jeśli wyeksportowane dane zawierały dane w bazie danych, która przekracza limity nowej warstwy, dane z tych wyższych baz danych nie są importowane.

### <a name="how-is-importexport-different-from-redis-persistence"></a>Czym importuje/eksportuje się od trwałości Redis?
Usługa Azure Cache for Redis trwałość umożliwia utrwalanie danych przechowywanych w redis do usługi Azure Storage. Po skonfigurowaniu trwałości usługa Azure Cache for Redis utrzymuje migawkę pamięci podręcznej Azure Cache for Redis w formacie binarnym Redis na dysku na podstawie konfigurowalnej częstotliwości tworzenia kopii zapasowych. Jeśli wystąpi katastrofalne zdarzenie, które wyłącza zarówno podstawową, jak i replikę pamięci podręcznej, dane pamięci podręcznej są przywracane automatycznie przy użyciu najnowszej migawki. Aby uzyskać więcej informacji, zobacz [Jak skonfigurować trwałość danych dla pamięci podręcznej premium Azure dla programu Redis.](cache-how-to-premium-persistence.md)

Import/ eksport umożliwia wprowadzanie danych do lub eksportowanie z usługi Azure Cache for Redis. Nie konfiguruje kopii zapasowej i przywracania przy użyciu trwałości Redis.

### <a name="can-i-automate-importexport-using-powershell-cli-or-other-management-clients"></a>Czy można zautomatyzować importowanie/eksportowanie przy użyciu programu PowerShell, interfejsu wiersza polecenia lub innych klientów zarządzania?
Tak, w przypadku instrukcji programu PowerShell zobacz [Aby zaimportować pamięć podręczną Azure dla redis](cache-how-to-manage-redis-cache-powershell.md#to-import-an-azure-cache-for-redis) i [wyeksportować pamięć podręczną platformy Azure dla programu Redis.](cache-how-to-manage-redis-cache-powershell.md#to-export-an-azure-cache-for-redis)

### <a name="i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean"></a>Wystąpił błąd limitu czasu podczas operacji importowania/eksportowania. Co to oznacza?
Jeśli pozostaniesz w **bloku Importuj dane** lub **Eksportuj dane** dłużej niż 15 minut przed rozpoczęciem operacji, zostanie wyświetlony komunikat o błędzie podobny do następującego przykładu:

    The request to import data into cache 'contoso55' failed with status 'error' and error 'One of the SAS URIs provided could not be used for the following reason: The SAS token end time (se) must be at least 1 hour from now and the start time (st), if given, must be at least 15 minutes in the past.

Aby rozwiązać ten problem, rozpocznij operację importu lub eksportu przed upływem 15 minut.

### <a name="i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened"></a>Wystąpił błąd podczas eksportowania danych do usługi Azure Blob Storage. Co się stało?
Eksport działa tylko z plikami RDB przechowywanymi jako obiekty blob stron. Inne typy obiektów blob nie są obecnie obsługiwane, w tym kont magazynu obiektów Blob z warstwami gorących i chłodnych. Aby uzyskać więcej informacji, zobacz [Omówienie konta magazynu platformy Azure](../storage/common/storage-account-overview.md).

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak korzystać z większej liczby funkcji pamięci podręcznej premium.

* [Wprowadzenie do warstwy Azure Cache for Redis Premium](cache-premium-tier-intro.md)
