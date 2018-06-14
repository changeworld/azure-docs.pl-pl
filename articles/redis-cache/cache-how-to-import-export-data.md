---
title: Importowanie i eksportowanie danych w pamięci podręcznej Redis Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak importowanie i eksportowanie danych z magazynu obiektów blob wystąpieniami usługi pamięć podręczna Redis Azure — warstwa premium
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 4a68ac38-87af-4075-adab-569d37d7cc9e
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: wesmc
ms.openlocfilehash: db488f759752880a47a78dfeec13b14f65bd503c
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2018
ms.locfileid: "27910091"
---
# <a name="import-and-export-data-in-azure-redis-cache"></a>Importowanie i eksportowanie danych w pamięci podręcznej Redis Azure
Import/Eksport jest operacji zarządzania pamięcią podręczną Redis Azure danych, która służy do importowania danych do pamięci podręcznej Redis Azure lub eksportowania danych z pamięci podręcznej Redis Azure przez importowanie i Eksportowanie migawki Redis pamięci podręcznej bazy danych (RDB) z usługi pamięć podręczna premium do obiektu blob na platformie Azure Konto magazynu. 

- **Eksportuj** — stronicowy obiekt Blob można wyeksportować z migawki RDB pamięci podręcznej Redis Azure.
- **Importuj** — z migawki RDB pamięci podręcznej Redis można importować z stronicowych obiektów Blob lub blokowych obiektów Blob.

Importu/eksportu umożliwia migrację między różnymi wystąpieniami usługi pamięć podręczna Redis Azure lub wypełnienie pamięci podręcznej danych przed użyciem.

Ten artykuł zawiera przewodnik do importowania i eksportowania danych z pamięci podręcznej Redis Azure i zawiera odpowiedzi na często zadawane pytania.

> [!IMPORTANT]
> Import/Eksport jest w wersji zapoznawczej i jest dostępny tylko dla [warstwy premium](cache-premium-tier-intro.md) przechowuje w pamięci podręcznej.
>
>

## <a name="import"></a>Import
Import może służyć do pamięci podręcznej Redis zgodne RDB plików z dowolnego serwera Redis uruchomiona w chmurze lub środowiska, w tym Redis w systemie Linux, Windows albo dowolnego dostawcy chmury, takich jak usług Amazon Web Services i inne. Importowanie danych jest w prosty sposób tworzenia pamięci podręcznej z wstępnie wypełnione danych. Podczas procesu importowania pamięć podręczna Redis Azure ładuje RDB pliki z magazynu Azure do pamięci i wstawianie klucze do pamięci podręcznej.

> [!NOTE]
> Przed rozpoczęciem operacji importowania, upewnij się, czy pliku bazy danych Redis (RDB) lub pliki są przekazywane do strony lub blokowych obiektów blob w magazynie Azure, w tym samym regionie i subskrypcji jako wystąpienie pamięci podręcznej Redis Azure. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z magazynem obiektów Blob platformy Azure](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Jeśli została wyeksportowana, używając pliku RDB [Azure Redis pamięci podręcznej wyeksportować](#export) funkcja, plik RDB jest już zapisana w stronicowych obiektów blob i jest gotowy do zaimportowania.
>
>

1. Aby zaimportować co najmniej jeden wyeksportowanego pamięci podręcznej obiekty BLOB, [przejdź do pamięci podręcznej](cache-configure.md#configure-redis-cache-settings) w portalu Azure i kliknij **importowania danych** z **zasobów menu**.

    ![Importowanie danych][cache-import-data]
2. Kliknij przycisk **wybierz Blob(s)** i wybierz konto magazynu, który zawiera dane do zaimportowania.

    ![Wybierz konto magazynu][cache-import-choose-storage-account]
3. Kliknij kontener, który zawiera dane do zaimportowania.

    ![Wybierz kontener][cache-import-choose-container]
4. Wybierz jeden lub więcej obiektów blob do zaimportowania, klikając obszarze na lewo od Nazwa obiektu blob, a następnie kliknij przycisk **wybierz**.

    ![Wybierz obiekty BLOB][cache-import-choose-blobs]
5. Kliknij przycisk **zaimportować** do rozpoczęcia procesu importowania.

   > [!IMPORTANT]
   > Pamięć podręczna nie jest dostępny dla klientów pamięci podręcznej podczas procesu importowania, a wszystkie istniejące dane w pamięci podręcznej jest usunięte.
   >
   >

    ![Import][cache-import-blobs]

    Można monitorować postęp operacji importowania, wykonując powiadomień z portalu Azure lub przez wyświetlanie zdarzeń w [dziennik inspekcji](../azure-resource-manager/resource-group-audit.md).

    ![Postęp importowania][cache-import-data-import-complete]

## <a name="export"></a>Eksportowanie
Eksport pozwala na wyeksportowanie danych przechowywanych w pamięci podręcznej Redis Azure do Redis zgodne pliki RDB. Ta funkcja służy do przenoszenia danych z jednego wystąpienia pamięci podręcznej Redis Azure do innego lub do innego serwera Redis. Podczas procesu eksportowania plik tymczasowy zostanie utworzony na maszynie Wirtualnej, która obsługuje wystąpienie serwera pamięci podręcznej Redis Azure, a plik jest przekazywany do konta magazynu wyznaczonego. Po zakończeniu operacji eksportowania stan powodzenia lub błędu, plik tymczasowy zostanie usunięty.

1. Aby wyeksportować bieżącej zawartości pamięci podręcznej do magazynu, [przejdź do pamięci podręcznej](cache-configure.md#configure-redis-cache-settings) w portalu Azure i kliknij **eksportować dane** z **zasobów menu**.

    ![Wybierz kontener magazynu][cache-export-data-choose-storage-container]
2. Kliknij przycisk **Wybierz kontener magazynu** i wybierz odpowiednie konto magazynu. Konta magazynu musi być w tej samej subskrypcji i regionu jako pamięci podręcznej.

   > [!IMPORTANT]
   > Eksportuj współpracuje z stronicowe obiekty BLOB, które są obsługiwane zarówno klasycznego i Menedżera zasobów konta magazynu, ale nie są obsługiwane przez [konta usługi Blob storage](../storage/common/storage-account-options.md#blob-storage-accounts) w tej chwili.
   >
   >

    ![Konto magazynu][cache-export-data-choose-account]
3. Wybierz kontener żądanego obiektu blob, a następnie kliknij przycisk **wybierz**. Aby użyć nowego kontenera, kliknij **dodać kontener** najpierw Dodaj i wybierz go z listy.

    ![Wybierz kontener magazynu][cache-export-data-container]
4. Wpisz **prefiks nazwy obiektu Blob** i kliknij przycisk **wyeksportować** do uruchomienia procesu eksportowania. Prefiks nazwy obiektów blob służy do prefiksu nazwy plików generowane przez tę operację eksportu.

    ![Eksportowanie][cache-export-data]

    Można monitorować postęp operacji eksportowania, wykonując powiadomień z portalu Azure lub przez wyświetlanie zdarzeń w [dziennik inspekcji](../azure-resource-manager/resource-group-audit.md).

    ![Eksportowanie danych jest pełny][cache-export-data-export-complete]

    Pamięci podręczne pozostają dostępne do użycia podczas procesu eksportowania.

## <a name="importexport-faq"></a>Import/Eksport — często zadawane pytania
Ta sekcja zawiera często zadawane pytania dotyczące funkcji importowania/eksportowania.

* [Warstw cenowych, jakie można użyć importu/eksportu?](#what-pricing-tiers-can-use-importexport)
* [Można importować dane z dowolnego serwera Redis?](#can-i-import-data-from-any-redis-server)
* [Które wersje RDB można importować?](#what-rdb-versions-can-i-import)
* [Moje pamięci podręcznej jest dostępna podczas operacji importu/eksportu?](#is-my-cache-available-during-an-importexport-operation)
* [Import/Eksport można używać z klastrem Redis?](#can-i-use-importexport-with-redis-cluster)
* [Jak działa importu/eksportu z niestandardowych baz danych, ustawienia](#how-does-importexport-work-with-a-custom-databases-setting)
* [Sposób importu/eksportu jest inny niż trwałość Redis](#how-is-importexport-different-from-redis-persistence)
* [Czy można zautomatyzować przy użyciu programu PowerShell, interfejsu wiersza polecenia lub innych klientów zarządzania importu/eksportu?](#can-i-automate-importexport-using-powershell-cli-or-other-management-clients)
* [Otrzymuję błąd upływu limitu czasu podczas mojej operacji importu i eksportu. Co to znaczy?](#i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean)
* [Otrzymano wystąpił błąd podczas eksportowania danych do magazynu obiektów Blob Azure. Co się stało?](#i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened)

### <a name="what-pricing-tiers-can-use-importexport"></a>Warstw cenowych, jakie można użyć importu/eksportu?
Narzędzie importu/eksportu jest dostępna tylko w warstwie cenowej premium.

### <a name="can-i-import-data-from-any-redis-server"></a>Można importować dane z dowolnego serwera Redis?
Tak, oprócz importowania danych wyeksportowanych z wystąpienia pamięci podręcznej Redis Azure, można importować RDB plików z dowolnego serwera Redis uruchomiona w chmurze lub środowiska, takich jak Linux, Windows, lub dostawców, takich jak usług Amazon Web Services w chmurze. W tym celu należy przekazać plik RDB z serwerem Redis do strony lub blokowych obiektów blob na koncie magazynu Azure, a następnie zaimportuj go do wystąpienia pamięci podręcznej Redis Azure premium. Na przykład można wyeksportować dane z pamięci podręcznej produkcyjnych i zaimportuj go do pamięci podręcznej używany jako część środowiska przemieszczania do testowania lub migracji.

> [!IMPORTANT]
> Aby pomyślnie importować dane wyeksportowane z pamięci podręcznej Redis serwerów innych niż pamięć podręczna Redis Azure, korzystając z obiektu blob strony, rozmiar obiektu blob strony musi być wyrównany na granicy 512-bajtowe. Przykładowy kod do wykonania dowolnego dopełnienie wymaganych bajtów, zobacz [przykładowe strony blogu przekazywania](https://github.com/JimRoberts-MS/SamplePageBlobUpload).
> 
> 

### <a name="what-rdb-versions-can-i-import"></a>Które wersje RDB można importować?

Pamięć podręczna Redis Azure obsługuje importu RDB za pośrednictwem RDB w wersji 7.

### <a name="is-my-cache-available-during-an-importexport-operation"></a>Moje pamięci podręcznej jest dostępna podczas operacji importu/eksportu?
* **Eksportuj** — pamięci podręcznych pozostają dostępne i można nadal używać pamięci podręcznej podczas operacji eksportowania.
* **Importuj** — pamięci podręcznych staną się niedostępne po uruchomieniu operacji importowania i stają się dostępne po zakończeniu operacji importowania.

### <a name="can-i-use-importexport-with-redis-cluster"></a>Import/Eksport można używać z klastrem Redis?
Tak, a użytkownik może importu/eksportu między klastrowanym pamięci podręcznej i nieklastrowanych pamięci podręcznej. Ponieważ klaster Redis [tylko obsługuje bazy danych 0](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering), nie jest importowane żadnych danych w bazach danych innych niż 0. Podczas importowania danych z pamięci podręcznej klastrowanych klucze są rozdzielane odłamków klastra.

### <a name="how-does-importexport-work-with-a-custom-databases-setting"></a>Jak działa importu/eksportu z niestandardowych baz danych, ustawienia
Niektóre warstw cenowych mają różne [baz danych limity](cache-configure.md#databases), więc istnieją pewne kwestie podczas importu, jeśli skonfigurowano niestandardowej wartości dla `databases` ustawienie podczas tworzenia pamięci podręcznej.

* Podczas importowania do warstwy cenowej dolna `databases` limit niż warstwy, z którego zostały wyeksportowane:
  * Jeśli używasz domyślną liczbę `databases`, która jest 16 dla wszystkich warstw cenowych, nie zostały utracone żadne dane.
  * Jeśli używasz niestandardowego liczba `databases` tego wypada w granicach na warstwę, do którego są importowane, zostały utracone żadne dane.
  * Jeśli wyeksportowane dane zawarte w danych w bazie danych, która przekracza limit nową warstwę, dane z tych wyższej baz danych nie został zaimportowany.

### <a name="how-is-importexport-different-from-redis-persistence"></a>Sposób importu/eksportu jest inny niż trwałość Redis
Trwałości pamięci podręcznej Redis Azure można utrwalić danych przechowywanych w pamięci podręcznej Redis do magazynu Azure. Po skonfigurowaniu trwałości, pamięć podręczna Redis Azure utrzymuje migawki pamięci podręcznej Redis w formacie binarnym Redis do dysku, na podstawie można skonfigurować częstotliwości tworzenia kopii zapasowej. W przypadku krytycznego zdarzenia, który powoduje wyłączenie pamięci podręcznej podstawowy i repliki, dane pamięci podręcznej został przywrócony automatycznie przy użyciu najnowszych migawki. Aby uzyskać więcej informacji, zobacz [Konfigurowanie trwałości danych dla podręczna Redis Azure Premium](cache-how-to-premium-persistence.md).

Importu / eksportu umożliwia przenoszenie danych do lub wyeksportować z pamięci podręcznej Redis Azure. Nie skonfigurowanie kopii zapasowej i przywracania przy użyciu trwałość Redis.

### <a name="can-i-automate-importexport-using-powershell-cli-or-other-management-clients"></a>Czy można zautomatyzować przy użyciu programu PowerShell, interfejsu wiersza polecenia lub innych klientów zarządzania importu/eksportu?
Tak, dla środowiska PowerShell instrukcje można znaleźć [do zaimportowania pamięci podręcznej Redis](cache-howto-manage-redis-cache-powershell.md#to-import-a-redis-cache) i [można wyeksportować pamięci podręcznej Redis](cache-howto-manage-redis-cache-powershell.md#to-export-a-redis-cache).

### <a name="i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean"></a>Otrzymuję błąd upływu limitu czasu podczas mojej operacji importu i eksportu. Co to znaczy?
Jeśli pozostają na **importowania danych** lub **eksportować dane** bloku dłuższy niż 15 minut przed zainicjowaniem operacji, komunikat o błędzie z komunikatem o błędzie podobny do poniższego przykładu:

    The request to import data into cache 'contoso55' failed with status 'error' and error 'One of the SAS URIs provided could not be used for the following reason: The SAS token end time (se) must be at least 1 hour from now and the start time (st), if given, must be at least 15 minutes in the past.

Aby rozwiązać ten problem, zainicjować importu lub eksportu przed 15 minut upłynął.

### <a name="i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened"></a>Otrzymano wystąpił błąd podczas eksportowania danych do magazynu obiektów Blob Azure. Co się stało?
Eksport działa tylko w przypadku plików RDB przechowywane jako stronicowych obiektów blob. Inne typy obiektów blob nie są obecnie obsługiwane, łącznie z konta magazynu obiektów blob z warstwami gorącego i chłodnego. Aby uzyskać więcej informacji, zobacz [konta usługi Blob storage](../storage/common/storage-account-options.md#blob-storage-accounts).

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się, jak korzystać z funkcji premium więcej w pamięci podręcznej.

* [Wprowadzenie do warstwy Premium pamięci podręcznej Redis Azure](cache-premium-tier-intro.md)    

<!-- IMAGES -->
[cache-settings-import-export-menu]: ./media/cache-how-to-import-export-data/cache-settings-import-export-menu.png
[cache-export-data-choose-account]: ./media/cache-how-to-import-export-data/cache-export-data-choose-account.png
[cache-export-data-choose-storage-container]: ./media/cache-how-to-import-export-data/cache-export-data-choose-storage-container.png
[cache-export-data-container]: ./media/cache-how-to-import-export-data/cache-export-data-container.png
[cache-export-data-export-complete]: ./media/cache-how-to-import-export-data/cache-export-data-export-complete.png
[cache-export-data]: ./media/cache-how-to-import-export-data/cache-export-data.png
[cache-import-data]: ./media/cache-how-to-import-export-data/cache-import-data.png
[cache-import-choose-storage-account]: ./media/cache-how-to-import-export-data/cache-import-choose-storage-account.png
[cache-import-choose-container]: ./media/cache-how-to-import-export-data/cache-import-choose-container.png
[cache-import-choose-blobs]: ./media/cache-how-to-import-export-data/cache-import-choose-blobs.png
[cache-import-blobs]: ./media/cache-how-to-import-export-data/cache-import-blobs.png
[cache-import-data-import-complete]: ./media/cache-how-to-import-export-data/cache-import-data-import-complete.png
