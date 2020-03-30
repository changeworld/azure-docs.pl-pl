---
title: Wskazówki dotyczące odzyskiwania po awarii dla usługi Avere vFXT dla platformy Azure
description: Jak chronić dane w avere vFXT for Azure przed przypadkowym usunięciem lub awariami
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: rohogue
ms.openlocfilehash: 28278f76497d6e9d0fee221bb4ef32fe6d369db0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75966650"
---
# <a name="disaster-recovery-guidance-for-avere-vfxt-for-azure"></a>Wskazówki dotyczące odzyskiwania po awarii dla usługi Avere vFXT dla platformy Azure

W tym artykule opisano strategie ochrony przepływu pracy avere vFXT for Azure i zawiera wskazówki dotyczące tworzenia kopii zapasowych danych, dzięki czemu można odzyskać po wypadkach lub awariach.

Avere vFXT for Azure tymczasowo przechowuje dane w swojej pamięci podręcznej. Dane są przechowywane długoterminowe w systemach magazynu zaplecza — lokalne systemy sprzętowe, kontenery magazynu obiektów Blob platformy Azure lub oba te elementy.

Aby chronić przed awariami i możliwą utratą danych, należy wziąć pod uwagę te cztery obszary:

* Ochrona przed przestojami, jeśli system Avere vFXT for Azure stanie się niedostępny
* Ochrona danych w pamięci podręcznej klastra
* Ochrona danych w sprzętowej pamięci masowej NAS zaplecza
* Ochrona danych w wewnętrznej części magazynu w chmurze obiektów Blob platformy Azure

Każdy klient avere vFXT for Azure musi utworzyć własny kompleksowy plan odzyskiwania po awarii, który zawiera plany dla tych elementów. Można również budować odporność na aplikacje używane z klastrem vFXT. Przeczytaj łącza w [następnych krokach, aby](#next-steps) uzyskać pomoc.

## <a name="protect-against-downtime"></a>Ochrona przed przestojami

Nadmiarowość jest wbudowana w produkt Avere vFXT for Azure:

* Klaster jest wysoce dostępny, a poszczególne węzły klastra mogą przechodzić w stan failover przy minimalnych przerwach.
* Dane zmieniane w pamięci podręcznej są regularnie zapisywane do back-end core filers (hardware NAS lub Azure Blob) dla długoterminowego przechowywania.

Każdy klaster Avere vFXT for Azure musi znajdować się w jednej strefie dostępności, ale można użyć nadmiarowych klastrów znajdujących się w różnych strefach lub różnych regionach, aby szybko zapewnić dostęp w przypadku awarii regionalnej.

Można również umieścić kontenery magazynu w wielu regionach, jeśli obawiasz się utraty dostępu do danych. Należy jednak pamiętać, że transakcje między regionami mają większe opóźnienia i wyższe koszty niż transakcje, które pozostają w regionie.

## <a name="protect-data-in-the-cluster-cache"></a>Ochrona danych w pamięci podręcznej klastra

Buforowane dane są zawsze zapisywane do podstawowych filers przed regularnym zamknięciem, ale w niekontrolowanym zamknięciu, zmienione dane w pamięci podręcznej mogą zostać utracone.

Jeśli klaster służy do optymalizacji tylko odczytów plików, nie ma żadnych zmian do utraty. Jeśli klaster służy również do buforowania zmian plików (zapisów), należy rozważyć, czy dostosować [podstawowe zasady pamięci podręcznej](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html) filers<!-- link to legacy doc --> , aby dostosować często dane zapisywane do długoterminowego przechowywania.

Ogólnie rzecz biorąc plan odzyskiwania powinien koncentrować się na tworzeniu kopii zapasowych systemów pamięci masowej zaplecza, które przechowują więcej danych i zazwyczaj są ważniejsze dla przywrócenia przepływu pracy po awarii.

## <a name="protect-data-in-nas-core-filers"></a>Ochrona danych w głównych filers NAS

Użyj zaakceptowanych metod ochrony danych przechowywanych w lokalnym narzędziu plików serwerów PLIKÓW sprzętowych SERWERA NAS, w tym migawek i pełnych kopii zapasowych zgodnie z zaleceniami dostawcy serwera NAS. Odzyskiwanie po awarii dla tych podstawowych filers wykracza poza zakres tego artykułu.

## <a name="protect-data-in-azure-blob-storage"></a>Ochrona danych w magazynie obiektów Blob platformy Azure

Avere vFXT for Azure używa lokalnie nadmiarowego magazynu (LRS) dla głównych filerów obiektów Blob platformy Azure. Oznacza to, że dane w kontenerach obiektów blob są automatycznie kopiowane w celu ochrony przed przejściowymi awariami sprzętowymi w centrum danych.

W tej sekcji przedstawiono wskazówki dotyczące dalszego ochrony danych w magazynie obiektów Blob przed rzadkimi awariami w całym regionie lub przypadkowymi usunięciami.

Najważniejsze wskazówki dotyczące ochrony danych w magazynie obiektów Blob platformy Azure obejmują:

* Często kopiuj krytyczne dane na inne konto magazynu w innym regionie (tak często, jak określa się plan odzyskiwania po awarii).
* Kontroluj dostęp do danych we wszystkich systemach docelowych, aby zapobiec przypadkowemu usunięciu lub uszkodzeniu. Należy rozważyć użycie [blokad zasobów](../azure-resource-manager/management/lock-resources.md) w magazynie danych.
* Włącz funkcję [migawki w chmurze](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_snapshot_policies.html>) Avere vFXT dla platformy Azure dla głównych filerów obiektów blob.

### <a name="copy-avere-vfxt-core-filer-data-to-a-backup-account"></a>Kopiowanie podstawowych danych filera vFXT avere na konto kopii zapasowej

Wykonaj następujące kroki, aby utworzyć kopię zapasową danych na innym koncie.

1. W razie potrzeby wygeneruj nowy klucz szyfrowania i przechowuj go bezpiecznie poza systemami, których dotyczy problem.

   Jeśli dane są szyfrowane przez klaster Avere vFXT for Azure, należy wygenerować nowy klucz szyfrowania przed skopiowaniem danych do innego konta magazynu. Przechowuj ten klucz i hasło bezpiecznie w bezpiecznym obiekcie, na który nie będzie miało wpływu awaria regionalna.

   Należy podać ten klucz podczas dodawania kontenera do klastra — nawet jeśli ponownie dodajesz go do oryginalnego klastra.

   Odczyt [ustawień szyfrowania w chmurze](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_encryption_settings.html>)<!-- link to legacy doc site --> szczegółowe informacje.

   Jeśli kontener używa tylko wbudowanego szyfrowania platformy Azure, możesz pominąć ten krok.

1. Usuń podstawowego filera z systemu. Wymusza to klaster do zapisu wszystkich zmienionych danych do magazynu zaplecza.

   Chociaż będziesz musiał ponownie dodać podstawowy filer po wykonaniu kopii zapasowej, usunięcie go jest najlepszym sposobem, aby zagwarantować, że wszystkie dane są całkowicie zapisane na zapleczu. (Opcja "wstrzymaj" może czasami pozostawić zmienione dane w pamięci podręcznej). <!-- xxx true? or just metadata? -->

   Zanotuj nazwę i informacje o połączeniu głównego filera (wymienione na stronie **Obszar nazw** w panelu sterowania), aby można było je zreplikować po ponownym dodaniu kontenera po wykonaniu kopii zapasowej.

   Użyj panelu sterowania klastra, aby usunąć podstawowy filer. [Otwórz panel sterowania klastra](avere-vfxt-cluster-gui.md) i wybierz **pozycję Core filer** > **Manage core filers**. Znajdź system magazynowania, którego chcesz użyć kopii zapasowej, i użyj przycisku **Usuń,** aby usunąć go z klastra.

1. Utwórz nowy, pusty kontener magazynu obiektów Blob na innym koncie magazynu w innym regionie.

1. Użyj dowolnego wygodnego narzędzia do kopiowania, aby skopiować dane na głównym filer do nowego kontenera. Kopia musi replikować dane bez zmian i bez zakłócania zastrzeżonego formatu systemu plików w chmurze używanego przez Avere vFXT dla platformy Azure. Narzędzia oparte na platformie Azure obejmują [AzCopy,](../storage/common/storage-use-azcopy-v10.md) [Azure PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)i [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md).

1. Po skopiowaniu danych do kontenera kopii zapasowej dodaj oryginalny kontener z powrotem do klastra zgodnie z opisem w [temacie Konfigurowanie magazynu](avere-vfxt-add-storage.md).

   * Użyj tej samej podstawowej nazwy filera i informacji o połączeniach, aby przepływy pracy klienta nie musiały się zmieniać.
   * Ustaw wartość **zawartości zasobnika** na istniejącą opcję danych.
   * Jeśli kontener został zaszyfrowany przez klaster, należy wprowadzić bieżący klucz szyfrowania dla jego zawartości. (Jest to klucz zaktualizowany w kroku pierwszym).

W przypadku kopii zapasowych po pierwszym nie trzeba tworzyć nowego kontenera magazynu. Należy jednak rozważyć wygenerowanie nowego klucza szyfrowania przy każdym wykonaniu kopii zapasowej, aby upewnić się, że bieżący klucz jest przechowywany w miejscu, które zapamiętasz.

### <a name="access-a-backup-data-source-during-an-outage"></a>Uzyskiwanie dostępu do źródła danych kopii zapasowej podczas awarii

Aby uzyskać dostęp do kontenera kopii zapasowej z avere vFXT for Azure cluster, wykonaj ten proces:

1. W razie potrzeby utwórz nowy klaster Avere vFXT dla platformy Azure w niezmienionym regionie.

   > [!TIP]
   > Podczas tworzenia avere vFXT dla klastra platformy Azure, można zapisać kopię jego szablonu tworzenia i parametrów. Jeśli te informacje są zapisywane podczas tworzenia klastra podstawowego, można go użyć do utworzenia klastra zastępczego o tych samych właściwościach. Na stronie [podsumowania](avere-vfxt-deploy.md#validation-and-purchase) kliknij **łącze Pobierz szablon i parametry.** Zapisz informacje w pliku przed utworzeniem klastra.

1. Dodaj nowy filer rdzenia chmury, który wskazuje na kontenerzdli zduplikowany obiekt.

   Upewnij się, że kontener docelowy zawiera już dane w ustawieniu **zawartości zasobnika** kreatora tworzenia plików core. (System powinien cię ostrzec, jeśli przypadkowo opuścisz ten zestaw **pusty.)**  <!-- you can't add a populated volume at cluster creation time via template, only create a fresh one -->

1. W razie potrzeby zaktualizuj klientów, aby zamiast oryginału zainstalowali nowy klaster lub nowy podstawowy filer. (Jeśli dodasz zastępczy plik rdzenia o tej samej nazwie i ścieżce połączenia co oryginalny kontener, nie trzeba będzie aktualizować procesów klienckich, chyba że trzeba zainstalować nowy klaster pod nowym adresem IP).

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat dostosowywania ustawień dla avere vFXT for Azure, przeczytaj [dostrajanie klastra](avere-vfxt-tuning.md).
* Dowiedz się więcej o odzyskiwaniu po awarii i tworzeniu aplikacji odpornych na platformy Azure:

  * [Odporność platformy Azure — wskazówki techniczne](https://docs.microsoft.com/azure/architecture/framework/resiliency/overview)
  * [Odzyskiwanie po obejmujących cały region zakłóceniach usługi](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region)
  * [Odzyskiwanie aplikacji platformy Azure po awarii i ich wysoka dostępność](<https://docs.microsoft.com/azure/resiliency/resiliency-disaster-recovery-high-availability-azure-applications>)
  <!-- can't find these in the source tree to use relative links -->
