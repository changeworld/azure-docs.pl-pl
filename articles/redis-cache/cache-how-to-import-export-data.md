---
title: Importowanie i eksportowanie danych w usłudze Azure Redis Cache | Dokumentacja firmy Microsoft
description: Dowiedz się, jak importować i eksportować dane do i z magazynu obiektów blob za pomocą swoich wystąpień usługi Azure Redis Cache — wersja premium
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
ms.openlocfilehash: b48235b1c1912ac767070d5890d4768138a9db8b
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2018
ms.locfileid: "45732095"
---
# <a name="import-and-export-data-in-azure-redis-cache"></a>Importowanie i eksportowanie danych w usłudze Azure Redis Cache
Import/Export jest operacji zarządzania danych usługi Azure Redis Cache, która umożliwia importowanie danych do usługi Azure Redis Cache lub eksportowanie danych z usługi Azure Redis Cache, importowania i eksportowania migawki usługi Redis Cache bazy danych (RDB) z cache w wersji premium do obiektu blob na platformie Azure Konto magazynu. 

- **Eksportuj** — migawki z pliku RDB pamięci podręcznej Redis Azure można wyeksportować do stronicowych obiektów Blob.
- **Importowanie** — Usługi migawek RDB pamięci Redis Cache można importować z stronicowych obiektów Blob lub blokowego obiektu Blob.

Import/Export umożliwia migrację między różnymi wystąpieniami usługi Azure Redis Cache lub wypełnienie pamięci podręcznej z danymi, przed użyciem.

Ten artykuł zawiera wskazówki do importowania i eksportowania danych za pomocą usługi Azure Redis Cache i zawiera odpowiedzi na często zadawane pytania.

> [!IMPORTANT]
> Import/Export jest dostępna w wersji zapoznawczej i jest dostępna tylko dla [w warstwie premium](cache-premium-tier-intro.md) zapisuje w pamięci podręcznej.
>
>

## <a name="import"></a>Import
Import może służyć do dostosowania zgodne pliki RDB pamięci podręcznej Redis z dowolnego serwera Redis, działające w dowolnej chmurze lub środowisku, w tym Redis działających w systemie Linux, Windows lub wśród dostawców chmury, takich jak usługi Amazon Web Services i inne. Importowanie danych jest łatwe tworzenie pamięci podręcznej z wstępnie wypełniony danymi. Podczas procesu importowania usługi Azure Redis Cache ładuje pliki RDB z usługi Azure storage do pamięci i wstawianie kluczy do pamięci podręcznej.

> [!NOTE]
> Przed rozpoczęciem operacji importowania, upewnij się, że pliku RDB pamięci Redis bazy danych () lub pliki są przekazywane do strony lub blokowego obiektów blob w usłudze Azure storage, w tym samym regionie i subskrypcji co wystąpienie usługi Azure Redis Cache. Aby uzyskać więcej informacji, zobacz [wprowadzenie do usługi Azure Blob storage](../storage/blobs/storage-dotnet-how-to-use-blobs.md). W przypadku eksportu, używając pliku RDB [usługi Azure Redis Cache wyeksportować](#export) funkcji pliku RDB jest już przechowywany w stronicowych obiektów blob i będzie gotowy do importowania.
>
>

1. Aby zaimportować co najmniej jeden wyeksportowanego pamięci podręcznej obiektów blob, [przejdź do swojej pamięci podręcznej](cache-configure.md#configure-redis-cache-settings) w witrynie Azure portal i kliknij **importowania danych** z **menu zasobów**.

    ![Importowanie danych][cache-import-data]
2. Kliknij przycisk **wybierz obiekty BLOB** i wybierz konto magazynu, który zawiera dane do zaimportowania.

    ![Wybierz konto magazynu][cache-import-choose-storage-account]
3. Kliknij kontener, który zawiera dane do zaimportowania.

    ![Wybierz kontener][cache-import-choose-container]
4. Wybierz jeden lub więcej obiektów blob do zaimportowania, klikając obszar po lewej stronie nazwy obiektu blob, a następnie kliknij przycisk **wybierz**.

    ![Wybierz obiekty BLOB][cache-import-choose-blobs]
5. Kliknij przycisk **zaimportować** aby rozpocząć proces importowania.

   > [!IMPORTANT]
   > Pamięć podręczna nie jest dostępny dla klientów pamięci podręcznej podczas procesu importowania, a wszelkie istniejące dane w pamięci podręcznej zostaną usunięte.
   >
   >

    ![Import][cache-import-blobs]

    Można monitorować postęp operacji importu, wykonując powiadomienia w witrynie Azure portal lub wyświetlając dane zdarzeń w [dziennik inspekcji](../azure-resource-manager/resource-group-audit.md).

    ![Postęp importowania][cache-import-data-import-complete]

## <a name="export"></a>Eksportowanie
Eksport umożliwia eksportowanie danych przechowywanych w usłudze Azure Redis Cache do Redis zgodne pliki RDB. Ta funkcja służy do przenoszenia danych z jednego wystąpienia usługi Azure Redis Cache do innego lub do innego serwera Redis. Podczas procesu eksportowania plik tymczasowy zostanie utworzony na maszynie Wirtualnej, który hostuje wystąpienie serwera usługi Azure Redis Cache, a plik jest przekazywany do konta magazynu wyznaczonego. Po zakończeniu operacji eksportowania ze stanem powodzenie lub niepowodzenie, plik tymczasowy zostanie usunięty.

1. Można wyeksportować bieżącej zawartości pamięci podręcznej do magazynu, [przejdź do swojej pamięci podręcznej](cache-configure.md#configure-redis-cache-settings) w witrynie Azure portal i kliknij **eksportowanie danych** z **menu zasobów**.

    ![Wybierz kontener magazynu][cache-export-data-choose-storage-container]
2. Kliknij przycisk **Wybierz kontener magazynu** i wybrać odpowiednie konto magazynu. Konto magazynu musi być w tej samej subskrypcji i regionie jako pamięci podręcznej.

   > [!IMPORTANT]
   > Wyeksportuj sprawdza w przypadku stronicowych obiektów blob, które są obsługiwane przez wdrożeń klasycznych, jak i kont magazynu usługi Resource Manager, ale nie są obsługiwane przez kont usługi Blob storage w tej chwili. Aby uzyskać więcej informacji, zobacz [Przegląd konta usługi Azure storage](../storage/common/storage-account-overview.md).
   >
   >

    ![Konto magazynu][cache-export-data-choose-account]
3. Wybierz kontener żądany obiekt blob, a następnie kliknij przycisk **wybierz**. Aby użyć nowego kontenera, kliknij **Dodaj kontener** najpierw Dodaj, a następnie wybierz pozycję z listy.

    ![Wybierz kontener magazynu][cache-export-data-container]
4. Wpisz **prefiks nazwy obiektu Blob** i kliknij przycisk **wyeksportować** można uruchomić procesu eksportu. Prefiks nazwy obiektu blob jest używany jako prefiks nazw plików wygenerowanych przez tę operację eksportowania.

    ![Eksportowanie][cache-export-data]

    Możesz monitorować postęp operacji eksportowania, wykonując powiadomienia w witrynie Azure portal lub wyświetlając dane zdarzeń w [dziennik inspekcji](../azure-resource-manager/resource-group-audit.md).

    ![Eksportowanie danych ukończone][cache-export-data-export-complete]

    Pamięci podręczne pozostają dostępne do użycia podczas procesu eksportowania.

## <a name="importexport-faq"></a>Import/Export — często zadawane pytania
Ta sekcja zawiera często zadawane pytania dotyczące funkcji importowania/eksportowania.

* [Jakie ceny warstwy można użyć importu/eksportu?](#what-pricing-tiers-can-use-importexport)
* [Można importować dane z dowolnego serwera Redis?](#can-i-import-data-from-any-redis-server)
* [Jakie wersje RDB można importować?](#what-rdb-versions-can-i-import)
* [Przepełnieniu pamięci podręcznej jest dostępna podczas operacji importu/eksportu?](#is-my-cache-available-during-an-importexport-operation)
* [Za pomocą klastra pamięci podręcznej Redis można używać importu/eksportu?](#can-i-use-importexport-with-redis-cluster)
* [Jak działa Import/Export za pomocą niestandardowych baz danych, ustawienie?](#how-does-importexport-work-with-a-custom-databases-setting)
* [Jak importu/eksportu jest inny niż stan trwały pamięci podręcznej Redis](#how-is-importexport-different-from-redis-persistence)
* [Czy można zautomatyzować przy użyciu programu PowerShell, interfejsu wiersza polecenia lub innych klientów zarządzania importu/eksportu?](#can-i-automate-importexport-using-powershell-cli-or-other-management-clients)
* [Wystąpił błąd upływu limitu czasu podczas operacji mojego importu/eksportu. Co to oznacza?](#i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean)
* [Otrzymuję błąd podczas eksportowania danych do usługi Azure Blob Storage. Co się stało?](#i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened)

### <a name="what-pricing-tiers-can-use-importexport"></a>Jakie ceny warstwy można użyć importu/eksportu?
Import/Export jest dostępna tylko w przypadku warstwy cenowej premium.

### <a name="can-i-import-data-from-any-redis-server"></a>Można importować dane z dowolnego serwera Redis?
Tak, oprócz importowanie danych wyeksportowanych z wystąpień usługi Azure Redis Cache, możesz importować pliki RDB z dowolnego serwera Redis, uruchomiony w dowolnej chmurze lub środowiska, takich jak Linux, Windows, lub dostawców, takich jak usługi Amazon Web Services w chmurze. Aby to zrobić, przekazywanie pliku RDB z serwerem Redis do strony lub blokowego obiektu blob na koncie usługi Azure Storage, a następnie zaimportować go do Twojego wystąpienia usługi Azure Redis Cache — wersja premium. Na przykład można wyeksportować dane z pamięci podręcznej produkcji i zaimportuj go do pamięci podręcznej używane jako część środowiska przemieszczania do testowania lub migracji.

> [!IMPORTANT]
> Pomyślnie import danych wyeksportowanych z serwerów Redis innych niż Azure Redis Cache, korzystając z stronicowych obiektów blob, rozmiar stronicowego obiektu blob musi być wyrównany na granicy 512-bajtowe. Aby uzyskać przykładowy kod wykonać wszelkie wymagane bajt dopełnienie, zobacz [przekazywania obiektów blob strony przykładowe](https://github.com/JimRoberts-MS/SamplePageBlobUpload).
> 
> 

### <a name="what-rdb-versions-can-i-import"></a>Jakie wersje RDB można importować?

Usługa Azure Redis Cache obsługuje importowanie pliku RDB się za pośrednictwem pliku RDB jest w wersji 7.

### <a name="is-my-cache-available-during-an-importexport-operation"></a>Przepełnieniu pamięci podręcznej jest dostępna podczas operacji importu/eksportu?
* **Eksportuj** — pamięci podręcznych pozostaną dostępne, i będzie można kontynuować używanie pamięci podręcznej podczas operacji eksportu.
* **Importowanie** — pamięci podręcznej staną się niedostępne po rozpoczęciu operacji importowania i stają się dostępne do użycia po zakończeniu operacji importu.

### <a name="can-i-use-importexport-with-redis-cluster"></a>Za pomocą klastra pamięci podręcznej Redis można używać importu/eksportu?
Tak, a użytkownik może importu/eksportu między klastra pamięci podręcznej i nieklastrowanych pamięci podręcznej. Od klastra pamięci podręcznej Redis [tylko obsługuje bazy danych 0](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering), zaimportowane nie ma żadnych danych w bazach danych innych niż 0. Podczas importowania danych z klastra pamięci podręcznej kluczy są redystrybuowane między fragmentami klastra.

### <a name="how-does-importexport-work-with-a-custom-databases-setting"></a>Jak działa Import/Export za pomocą niestandardowych baz danych, ustawienie?
Kilka warstw cenowych mają różne [baz danych limity](cache-configure.md#databases), więc istnieją pewne zagadnienia podczas importowania, jeśli skonfigurowano niestandardowej wartości dla `databases` ustawienie podczas tworzenia pamięci podręcznej.

* Podczas importowania do warstwy cenowej o niższych `databases` limit niż warstwa, z którego został wyeksportowany:
  * Jeśli używasz domyślną liczbę `databases`, czyli 16 dla wszystkich warstw cenowych, zostaną utracone żadne dane.
  * Jeśli używasz niestandardowego szereg `databases` ten znajduje się w granicach, dla warstwy, do którego są importowane, zostaną utracone żadne dane.
  * Wyeksportowane dane zawiera dane w bazie danych, która przekracza limit nową warstwę, dane z tych baz danych, wyższe, nie zostanie zaimportowany.

### <a name="how-is-importexport-different-from-redis-persistence"></a>Jak importu/eksportu jest inny niż stan trwały pamięci podręcznej Redis
Azure trwałość pamięci podręcznej Redis umożliwia utrwalanie danych przechowywanych w pamięci podręcznej Redis do usługi Azure Storage. Po skonfigurowaniu stanów trwałych usługi Azure Redis Cache będzie się powtarzał migawkę pamięci podręcznej Redis w formacie binarnym pamięci podręcznej Redis na dysku, na podstawie konfigurowalnych częstotliwości tworzenia kopii zapasowej. Sytuacji katastrofy wyłączającą podstawowej i replice pamięci podręcznej danych z pamięci podręcznej zostanie przywrócony automatycznie przy użyciu najnowszej migawki. Aby uzyskać więcej informacji, zobacz [Konfigurowanie trwałości danych usługi Premium Azure Redis Cache](cache-how-to-premium-persistence.md).

Import / Export umożliwia przenoszenie danych do lub wyeksportować z usługi Azure Redis Cache. Nie Konfiguruj kopię zapasową i przywrócić dane przy użyciu stanu trwałego pamięci podręcznej Redis.

### <a name="can-i-automate-importexport-using-powershell-cli-or-other-management-clients"></a>Czy można zautomatyzować przy użyciu programu PowerShell, interfejsu wiersza polecenia lub innych klientów zarządzania importu/eksportu?
Tak, środowiska PowerShell uzyskać instrukcje, zobacz [do zaimportowania pamięci podręcznej Redis](cache-howto-manage-redis-cache-powershell.md#to-import-a-redis-cache) i [do wyeksportowania pamięci podręcznej Redis](cache-howto-manage-redis-cache-powershell.md#to-export-a-redis-cache).

### <a name="i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean"></a>Wystąpił błąd upływu limitu czasu podczas operacji mojego importu/eksportu. Co to oznacza?
Jeśli pozostają na **importowania danych** lub **eksportowanie danych** bloku dłuższy niż 15 minut przed rozpoczęciem operacji, otrzymasz komunikat o błędzie, komunikat o błędzie podobny do poniższego przykładu:

    The request to import data into cache 'contoso55' failed with status 'error' and error 'One of the SAS URIs provided could not be used for the following reason: The SAS token end time (se) must be at least 1 hour from now and the start time (st), if given, must be at least 15 minutes in the past.

Aby rozwiązać ten problem, rozpocząć import lub Eksport przed 15 minut upłynie.

### <a name="i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened"></a>Otrzymuję błąd podczas eksportowania danych do usługi Azure Blob Storage. Co się stało?
Eksportu działa tylko w przypadku plików z pliku RDB jest przechowywane jako stronicowe obiekty BLOB. Inne typy obiektów blob nie są obsługiwane obecnie, w tym kont usługi Blob storage z warstwami gorąca i chłodna. Aby uzyskać więcej informacji, zobacz [Przegląd konta usługi Azure storage](../storage/common/storage-account-overview.md).

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się, jak korzystać z funkcji premium więcej w pamięci podręcznej.

* [Wprowadzenie do usługi Azure Redis Cache w warstwie Premium](cache-premium-tier-intro.md)    

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
