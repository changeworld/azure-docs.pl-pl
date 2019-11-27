---
title: Twórz aplikacje usługi Azure Storage o wysokiej dostępności za pomocą stref geograficznie nadmiarowych (GZRS) (wersja zapoznawcza) | Microsoft Docs
description: Magazyn strefy Geograficznie nadmiarowy (GZRS) cywilnego wysoką dostępność magazynu Strefowo nadmiarowego (ZRS) z ochroną z poziomu awarii regionalnej, zgodnie z magazynem geograficznie nadmiarowym (GRS). Dane na koncie magazynu GZRS są replikowane w strefach dostępności platformy Azure w regionie podstawowym, a także zreplikowane do pomocniczego regionu geograficznego na potrzeby ochrony przed awariami regionalnymi.
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: e749dc48b1834aedbfea048c49c1f9090e5b5bb8
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74534306"
---
# <a name="build-highly-available-azure-storage-applications-with-geo-zone-redundant-storage-gzrs-preview"></a>Twórz aplikacje usługi Azure Storage o wysokiej dostępności przy użyciu strefy geograficznej nadmiarowej (GZRS) (wersja zapoznawcza)

Magazyn Geograficznie nadmiarowy (GZRS) (wersja zapoznawcza) cywilnego wysokiej dostępności [magazynu Strefowo nadmiarowego (ZRS)](storage-redundancy-zrs.md) z ochroną z regionu w regionie [geograficznie nadmiarowym (GRS)](storage-redundancy-grs.md). Dane na koncie magazynu GZRS są replikowane w trzech [strefach dostępności platformy Azure](../../availability-zones/az-overview.md) w regionie podstawowym, a także zreplikowane do pomocniczego regionu geograficznego na potrzeby ochrony przed awariami regionalnymi. Każdy region platformy Azure jest sparowany z innym regionem w tej samej lokalizacji geograficznej, tworząc parę regionalną. Więcej informacji i wyjątków można znaleźć w [dokumentacji](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

Za pomocą konta magazynu GZRS można nadal odczytywać i zapisywać dane, jeśli strefa dostępności stanie się niedostępna lub nie można jej odzyskać. Ponadto dane są również trwałe w przypadku kompletnej awarii regionalnej lub awarii, w której region podstawowy nie jest możliwy do odzyskania. GZRS zaprojektowano w celu udostępnienia co najmniej 99.99999999999999% (16 9) trwałości obiektów w danym roku. GZRS oferuje również te same [elementy docelowe skalowalności](storage-scalability-targets.md) jak LRS, ZRS, GRS lub RA-GRS. Opcjonalnie można włączyć dostęp do odczytu do danych w regionie pomocniczym ze strefą geograficzną z dostępem do odczytu — nadmiarowy magazyn (RA-GZRS), jeśli aplikacje muszą mieć możliwość odczytywania danych w przypadku awarii w regionie podstawowym.

Firma Microsoft zaleca używanie GZRS dla aplikacji wymagających spójności, trwałości, wysokiej dostępności, doskonałej wydajności i odporności na odzyskiwanie po awarii. Aby uzyskać dodatkowe zabezpieczenia dotyczące dostępu do odczytu do regionu pomocniczego w przypadku awarii regionalnej, Włącz RA-GZRS dla konta magazynu.

## <a name="about-the-preview"></a>Informacje o wersji zapoznawczej

Tylko konta magazynu ogólnego przeznaczenia w wersji 2 obsługują GZRS i RA-GZRS. Aby uzyskać więcej informacji dotyczących typów kont magazynu, zobacz temat [Przegląd konta usługi Azure Storage](storage-account-overview.md). GZRS i RA-GZRS obsługują blokowe obiekty blob, stronicowe obiekty blob (które nie są dyskami VHD), pliki, tabele i kolejki.

GZRS i RA-GZRS są obecnie dostępne w wersji zapoznawczej w następujących regionach:

- Azja Południowo-wschodnia
- Europa Północna
- Europa Zachodnia
- Południowe Zjednoczone Królestwo
- Wschodnie stany USA
- Wschodnie stany USA 2
- Środkowe stany USA

Firma Microsoft nadal włącza GZRS i RA-GZRS w dodatkowych regionach świadczenia usługi Azure. Sprawdź regularnie informacje o obsługiwanych regionach na stronie [aktualizacji usługi platformy Azure](https://azure.microsoft.com/updates/) .

Aby uzyskać informacje na temat cennika wersji zapoznawczej, zobacz cennik usługi GZRS w wersji zapoznawczej dla [obiektów BLOB](https://azure.microsoft.com/pricing/details/storage/blobs), [plików](https://azure.microsoft.com/pricing/details/storage/files/), [kolejek](https://azure.microsoft.com/pricing/details/storage/queues/)i [tabel](https://azure.microsoft.com/pricing/details/storage/tables/).

> [!IMPORTANT]
> Firma Microsoft zaleca korzystanie z funkcji wersji zapoznawczej dla obciążeń produkcyjnych.

## <a name="how-gzrs-and-ra-gzrs-work"></a>Jak GZRS i RA-GZRS Work

Gdy dane są zapisywane na koncie magazynu z włączonym GZRS lub RA-GZRS, dane te są najpierw replikowane synchronicznie w regionie podstawowym w trzech strefach dostępności. Dane są następnie replikowane asynchronicznie do drugiego regionu, w którym znajduje się setki kilometrów. Gdy dane są zapisywane w regionie pomocniczym, są one następnie wielokrotnie replikowane synchronicznie w tym regionie przy użyciu [magazynu lokalnie nadmiarowego (LRS)](storage-redundancy-lrs.md).

> [!IMPORTANT]
> Replikacja asynchroniczna obejmuje opóźnienie między czasem zapisywania danych w regionie podstawowym a replikacją do regionu pomocniczego. W przypadku awarii regionalnej zmiany, które nie zostały jeszcze zreplikowane do regionu pomocniczego, mogą zostać utracone, jeśli nie można odzyskać tych danych z regionu podstawowego.

Podczas tworzenia konta magazynu należy określić sposób replikowania danych z tego konta, a także określić region podstawowy dla tego konta. Sparowany region pomocniczy dla konta z replikacją geograficzną jest określany na podstawie regionu podstawowego i nie można go zmienić. Aby uzyskać aktualne informacje dotyczące regionów obsługiwanych przez platformę Azure, zobacz temat [ciągłość działania i odzyskiwanie po awarii (BCDR): wielosparowane regiony platformy Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). Aby uzyskać informacje na temat tworzenia konta magazynu za pomocą usługi GZRS lub RA-GZRS, zobacz [Tworzenie konta magazynu](storage-quickstart-create-account.md).

### <a name="use-ra-gzrs-for-high-availability"></a>Korzystanie z usługi RA-GZRS w celu zapewnienia wysokiej dostępności

Po włączeniu usługi RA-GZRS dla konta magazynu dane można odczytać z pomocniczego punktu końcowego oraz z podstawowego punktu końcowego dla konta magazynu. Pomocniczy punkt końcowy dołącza sufiks *— pomocniczy* do nazwy konta. Na przykład jeśli podstawowy punkt końcowy Blob service jest `myaccount.blob.core.windows.net`, zostanie `myaccount-secondary.blob.core.windows.net`pomocniczy punkt końcowy. Klucze dostępu dla konta magazynu są takie same dla podstawowych i pomocniczych punktów końcowych.

Aby skorzystać z funkcji RA-GZRS w przypadku awarii regionalnej, musisz zaprojektować aplikację z wyprzedzeniem, aby obsłużyć ten scenariusz. Aplikacja powinna odczytywać i zapisywać w podstawowym punkcie końcowym, ale przełączyć się do korzystania z pomocniczego punktu końcowego w przypadku, gdy region podstawowy stał się niedostępny. Aby uzyskać wskazówki dotyczące projektowania wysokiej dostępności za pomocą usługi RA-GZRS, zobacz [projektowanie wysoce dostępnych aplikacji przy użyciu usługi RA-GZRS lub RA-GRS](https://docs.microsoft.com/azure/storage/common/storage-designing-ha-apps-with-ragrs).

Ponieważ dane są replikowane do regionu pomocniczego asynchronicznie, region pomocniczy jest często za regionem podstawowym. Aby określić, które operacje zapisu zostały zreplikowane do regionu pomocniczego, aplikacja sprawdza czas ostatniej synchronizacji dla konta magazynu. Wszystkie operacje zapisu zapisane w regionie podstawowym przed upływem czasu ostatniej synchronizacji zostały pomyślnie zreplikowane do regionu pomocniczego, co oznacza, że są dostępne do odczytu z pomocniczego. Wszystkie operacje zapisu zapisane w regionie podstawowym po ostatniej synchronizacji mogły lub nie zostały zreplikowane do regionu pomocniczego, co oznacza, że mogą nie być dostępne dla operacji odczytu.

Można badać wartość **ostatniej właściwości czasu synchronizacji** przy użyciu Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub jednej z bibliotek klienta usługi Azure Storage. Właściwość **czas ostatniej synchronizacji** jest wartością daty/godziny GMT.

Dodatkowe wskazówki dotyczące wydajności i skalowalności za pomocą usługi RA-GZRS można znaleźć Microsoft Azure Storage w temacie [Lista kontrolna wydajności i skalowalności](storage-performance-checklist.md).

### <a name="availability-zone-outages"></a>Awaria strefy dostępności

W przypadku błędu wpływającego na strefę dostępności w regionie podstawowym aplikacje mogą bezproblemowo kontynuować odczytywanie i zapisywanie danych na koncie magazynu przy użyciu innych stref dostępności dla danego regionu. Firma Microsoft zaleca, aby nadal stosować praktyki dotyczące obsługi błędów przejściowych podczas korzystania z programu GZRS lub ZRS. Te praktyki obejmują implementację zasad ponawiania z wycofywaniem wykładniczym.

Gdy strefa dostępności jest niedostępna, platforma Azure podniesie aktualizacje sieciowe, takie jak ponowne wskazanie DNS. Te aktualizacje mogą mieć wpływ na aplikację, Jeśli uzyskujesz dostęp do danych przed ukończeniem aktualizacji.

### <a name="regional-outages"></a>Awaria regionalna

Jeśli błąd dotyczy całego regionu podstawowego, firma Microsoft najpierw podejmie próbę przywrócenia regionu podstawowego. Jeśli przywracanie nie jest możliwe, firma Microsoft przejdzie w tryb failover do regionu pomocniczego, aby region pomocniczy stał się nowym regionem podstawowym. Jeśli konto magazynu ma włączone RA-GZRS, aplikacje przeznaczone dla tego scenariusza mogą odczytywać z regionu pomocniczego podczas oczekiwania na przejście w tryb failover. Jeśli konto magazynu nie ma włączonej usługi RA-GZRS, aplikacje nie będą mogły czytać z poziomu pomocniczego do momentu ukończenia pracy w trybie failover.

> [!NOTE]
> GZRS i RA-GZRS są obecnie w wersji zapoznawczej tylko w regionie Wschodnie stany USA. Konto zarządzane przez klienta w trybie failover (wersja zapoznawcza) nie jest jeszcze dostępne w regionie Wschodnie stany USA 2, dlatego klienci nie mogą obecnie zarządzać zdarzeniami trybu failover konta przy użyciu kont GZRS i RA-GZRS. W ramach wersji zapoznawczej firma Microsoft będzie zarządzać wszystkimi zdarzeniami trybu failover wpływającymi na konta GZRS i RA-GZRS.

Ponieważ dane są replikowane do regionu pomocniczego asynchronicznie, awaria wpływająca na region podstawowy może spowodować utratę danych, jeśli nie można odzyskać regionu podstawowego. Interwał między ostatnimi zapisami w regionie podstawowym a ostatnim zapisem w regionie pomocniczym jest znany jako cel punktu odzyskiwania (RPO). Cel punktu odzyskiwania wskazuje punkt w czasie, do którego można odzyskać dane. Usługa Azure Storage zazwyczaj ma cel punktu odzyskiwania krótszy niż 15 minut, chociaż obecnie nie ma umowy SLA dotyczącej czasu trwania replikacji danych do regionu pomocniczego.

Cel czasu odzyskiwania (RTO) to miara, jak długo trwa przechodzenie w tryb failover i przywrócenie konta magazynu z powrotem do trybu online. Ta miara wskazuje czas wymagany przez platformę Azure do przeprowadzenia przejścia w tryb failover, zmieniając podstawowe wpisy DNS w celu wskazywania lokalizacji pomocniczej.

## <a name="migrate-a-storage-account-to-gzrs-or-ra-gzrs"></a>Migrowanie konta magazynu do GZRS lub RA-GZRS

Każde istniejące konto magazynu można migrować do GZRS lub RA-GZRS. Migrowanie z istniejącego konta ZRS do usługi GZRS lub RA-GZRS jest proste, podczas gdy migracja z konta LRS, GRS lub RA-GRS jest większa. W poniższych sekcjach opisano, jak przeprowadzić migrację w obu przypadkach.

**Znane ograniczenia**

- Warstwa archiwum nie jest obecnie obsługiwana na kontach GZRS (RA-). Aby uzyskać więcej informacji [, zobacz warstwy dostępu gorąca, chłodna i archiwalna](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) .
- Dyski zarządzane nie obsługują GZRS (RA-). Można przechowywać migawki i obrazy dla SSD w warstwie Standardowa Managed Disks HDD w warstwie Standardowa magazynu i [wybierać między opcjami LRS i ZRS](https://azure.microsoft.com/pricing/details/managed-disks/).

### <a name="migrating-from-a-zrs-account"></a>Migrowanie z konta usługi ZRS

Aby przekonwertować istniejące konto ZRS na RA-GZRS, należy użyć polecenia cmdlet [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) w celu zmiany jednostki SKU dla konta. Pamiętaj, aby zastąpić wartości zastępcze własnymi wartościami:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group> -AccountName <storage-account> -SkuName "Standard_RAGZRS"
```

### <a name="migrating-from-an-lrs-grs-or-ra-grs-account"></a>Migrowanie z konta usługi LRS, GRS lub RA-GRS

Istnieją dwie opcje migracji do GZRS lub RA-GZRS z konta LRS, GRS lub RA-GRS:

- Możesz ręcznie kopiować lub przenosić dane do nowego konta GZRS lub RA-GZRS z istniejącego konta.
- Możesz zażądać migracji na żywo.

#### <a name="perform-a-manual-migration"></a>Przeprowadź migrację ręczną

Aby przeprowadzić migrację w określonym dniu, należy rozważyć przeprowadzenie ręcznej migracji. Migracja ręczna zapewnia większą elastyczność niż migracja na żywo. W przypadku migracji ręcznej masz kontrolę nad harmonogramem.

Aby ręcznie migrować dane z istniejącego konta do konta usługi GZRS lub RA-GZRS, użyj narzędzia, które może efektywnie kopiować dane. Oto niektóre przykłady:

- Użyj narzędzia, takiego jak AzCopy lub niezawodnego narzędzia innej firmy. Aby uzyskać informacje na temat AzCopy, zobacz Rozpoczynanie [pracy z AzCopy](storage-use-azcopy-v10.md).
- Jeśli znasz już usługi Hadoop lub HDInsight, Dołącz źródłowe i docelowe konta magazynu do klastra. Następnie zrównoleglanie proces kopiowania danych za pomocą narzędzia, takiego jak pomocą distcp.
- Utwórz własne narzędzia przy użyciu jednej z bibliotek klienta usługi Azure Storage.

#### <a name="perform-a-live-migration"></a>Przeprowadzanie migracji na żywo

Migracja ręczna może skutkować przestojem aplikacji. Jeśli aplikacja wymaga wysokiej dostępności, firma Microsoft udostępnia również opcję migracji na żywo. Migracja na żywo to migracja w miejscu bez przestojów.

Podczas migracji na żywo można korzystać z konta magazynu podczas migrowania danych między kontami magazynu źródłowego i docelowego. Podczas migracji na żywo Twoje konto nadal spełnia warunki umowy SLA dotyczące trwałości i dostępności. Migracja na żywo nie powoduje przestoju lub utraty danych.

Tylko konta ogólnego przeznaczenia w wersji 2 obsługują usługi GZRS/RA-GZRS, dlatego przed przesłaniem żądania migracji na żywo do usługi GZRS/RA-GZRS należy uaktualnić konto do poziomu ogólnego przeznaczenia w wersji 2. Aby uzyskać więcej informacji, zobacz [Omówienie konta usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-account-overview) i [uaktualnianie do konta magazynu ogólnego przeznaczenia w wersji 2](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade).

Po zakończeniu migracji ustawienie replikacji konta magazynu zostanie zaktualizowane do **magazynu geograficznie nadmiarowego (GZRS)** lub **do odczytu stref geograficznie nadmiarowego (Ra-GZRS)** . Punkty końcowe usługi, klucze dostępu, sygnatury dostępu współdzielonego (SAS) i inne opcje konfiguracji konta pozostają bez zmian i nie zostały naruszone.

Należy pamiętać o następujących ograniczeniach migracji na żywo:

- Mimo że firma Microsoft obsłuży Twoje żądanie migracji na żywo tak szybko, jak to możliwe, nie ma możliwości określenia, kiedy migracja na żywo zostanie ukończona. Jeśli chcesz, aby dane były migrowane do GZRS lub RA-GZRS w określonym dniu, firma Microsoft zaleca przeprowadzenie ręcznej migracji. Ogólnie rzecz biorąc, im więcej danych zawiera Twoje konto, tym dłużej trwa ich migracja.
- Twoje konto musi zawierać dane.
- Dane można migrować tylko w tym samym regionie.
- Tylko standardowe typy kont magazynu obsługują migrację na żywo. Konta magazynu w warstwie Premium muszą być migrowane ręcznie.
- Migracja na żywo z konta GZRS lub RA-GZRS do konta LRS, GRS lub RA-GRS nie jest obsługiwana. Konieczne będzie ręczne przeniesienie danych do nowego lub istniejącego konta magazynu.
- Możesz zażądać migracji na żywo z usługi RA-GRS do RA-GZRS. Migrowanie z usługi RA-GRS do usługi GZRS nie jest jednak obsługiwane. W takim przypadku należy zażądać migracji na żywo do RA-GZRS, a następnie ręcznie przekonwertować konto magazynu, aby użyć GZRS.
- Dyski zarządzane obsługują tylko LRS i nie można ich migrować do GZRS lub RA-GZRS. Aby uzyskać integrację z zestawami dostępności, zobacz [wprowadzenie do usługi Azure Managed disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#integration-with-availability-sets).
- Można przechowywać migawki i obrazy dla SSD w warstwie Standardowa Managed Disks HDD w warstwie Standardowa magazynu i [wybierać między opcjami LRS, ZRS, GZRS i RA-GZRS](https://azure.microsoft.com/pricing/details/managed-disks/).
- Konta zawierające duże udziały plików nie są obsługiwane w przypadku GZRS.

Aby zażądać migracji na żywo, użyj [Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). W portalu wybierz konto magazynu do migracji do GZRS lub RA-GZRS i wykonaj następujące instrukcje:

1. Wybierz pozycję **nowe żądanie obsługi**.
2. Wypełnij **podstawowe** informacje na podstawie informacji o koncie. W sekcji **Usługa** wybierz pozycję **Zarządzanie kontem magazynu** i określ konto, które ma zostać zmigrowane.
3. Wybierz opcję **Dalej**.
4. Określ następujące wartości w sekcji **problem** :
    - **Ważność**: pozostaw wartość domyślną równą-is.
    - **Typ problemu**: wybierz pozycję **migracja danych**.
    - **Kategoria**: wybierz pozycję **MIGRUJ do (Ra-) GZRS w regionie**.
    - **Title**: wpisz opisowy tytuł, na przykład **(Ra-) GZRS migracji konta**.
    - **Szczegóły**: wpisz dodatkowe szczegóły w polu **szczegóły** , na przykład "Chcę migrować do GZRS z [LRS, GRS] w regionie \_ \_". lub "Chcę migrować do usługi RA-GZRS z [LRS, RA-GRS] w regionie \_ \_".
5. Wybierz opcję **Dalej**.
6. Sprawdź, czy informacje kontaktowe są poprawne w bloku **informacje kontaktowe** .
7. Wybierz pozycję **Utwórz**.

Przedstawiciel działu pomocy technicznej skontaktuje się z Tobą, aby zapewnić pomoc.

## <a name="see-also"></a>Zobacz także

- [Replikacja usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy)
- [Magazyn lokalnie nadmiarowy (LRS): niski koszt nadmiarowości danych dla usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs)
- [Magazyn strefowo nadmiarowy (ZRS): aplikacje usługi Azure Storage o wysokiej dostępności](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) 
