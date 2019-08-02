---
title: Tworzenie aplikacji usługi Azure Storage o wysokiej dostępności w ramach magazynu Strefowo nadmiarowego (ZRS) | Microsoft Docs
description: Magazyn strefowo nadmiarowy (ZRS) oferuje prosty sposób tworzenia aplikacji o wysokiej dostępności. ZRS chroni przed awariami sprzętu w centrum danych i z niektórymi regionalnymi awariami.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/24/2018
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 5fefe469bfac4816a67c6ceb344f12c1e52de60c
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68550459"
---
# <a name="zone-redundant-storage-zrs-highly-available-azure-storage-applications"></a>Magazyn strefowo nadmiarowy (ZRS): Aplikacje usługi Azure Storage o wysokiej dostępności
[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-zrs.md)]

## <a name="support-coverage-and-regional-availability"></a>Zakres pomocy technicznej i dostępność regionalna
ZRS obecnie obsługuje standardowe typy kont ogólnego przeznaczenia w wersji 2. Aby uzyskać więcej informacji dotyczących typów kont magazynu, zobacz temat [Przegląd konta usługi Azure Storage](storage-account-overview.md).

ZRS jest dostępny dla blokowych obiektów blob, niedyskowych obiektów blob, plików, tabel i kolejek.

ZRS jest ogólnie dostępna w następujących regionach:

- Azja Południowo-Wschodnia
- Europa Zachodnia
- Europa Północna
- Francja Środkowa
- Japonia Wschodnia
- Południowe Zjednoczone Królestwo
- Środkowe stany USA
- Wschodnie stany USA
- Wschodnie stany USA 2
- Zachodnie stany USA 2

Firma Microsoft nadal włącza ZRS w dodatkowych regionach świadczenia usługi Azure. Sprawdź regularnie informacje o nowych regionach na stronie [aktualizacji usługi platformy Azure](https://azure.microsoft.com/updates/) .

**Znane ograniczenia**

- Warstwa archiwum nie jest obecnie obsługiwana na kontach ZRS. Aby uzyskać więcej informacji [, zobacz warstwy dostępu gorąca, chłodna i archiwalna](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) .
- Dyski zarządzane nie obsługują ZRS. Można przechowywać migawki i obrazy dla SSD w warstwie Standardowa Managed Disks HDD w warstwie Standardowa magazynu i [wybierać między opcjami LRS i ZRS](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="what-happens-when-a-zone-becomes-unavailable"></a>Co się stanie, gdy strefa stanie się niedostępna?
Dane są nadal dostępne dla operacji odczytu i zapisu, nawet jeśli strefa będzie niedostępna. Firma Microsoft zaleca, aby nadal stosować praktyki dotyczące obsługi błędów przejściowych. Te praktyki obejmują implementację zasad ponawiania z wycofywaniem wykładniczym.

Gdy strefa jest niedostępna, platforma Azure podniesie aktualizacje sieciowe, takie jak repunktowanie DNS. Te aktualizacje mogą mieć wpływ na aplikację, Jeśli uzyskujesz dostęp do danych przed ukończeniem aktualizacji.

ZRS może nie chronić danych przed awarią regionalną, w której mają stałe zmiany w wielu strefach. Zamiast tego ZRS zapewnia odporność na dane, jeśli staną się tymczasowo niedostępne. Aby chronić przed awariami regionalnymi, firma Microsoft zaleca korzystanie z magazynu geograficznie nadmiarowego (GRS). Aby uzyskać więcej informacji na temat GRS [, zobacz Magazyn Geograficznie nadmiarowy (GRS): Replikacja między regionami w ramach usługi](storage-redundancy-grs.md)Azure Storage.

## <a name="converting-to-zrs-replication"></a>Konwertowanie na replikację ZRS
Migrowanie do lub z LRS, GRS i RA-GRS jest proste. Użyj Azure Portal lub interfejsu API dostawcy zasobów magazynu, aby zmienić typ nadmiarowości konta. Na platformie Azure zostaną następnie zreplikowane dane. 

Migrowanie danych do ZRS wymaga innej strategii. Migracja ZRS obejmuje fizyczne przenoszenie danych z pojedynczej sygnatury magazynu do wielu sygnatur w regionie.

Dostępne są dwie podstawowe opcje migracji do ZRS: 

- Ręcznie Kopiuj lub Przenieś dane do nowego konta ZRS z istniejącego konta.
- Zażądaj migracji na żywo.

Firma Microsoft zdecydowanie zaleca przeprowadzenie ręcznej migracji. Migracja ręczna zapewnia większą elastyczność niż migracja na żywo. W przypadku ręcznej migracji masz kontrolę nad chronometrażem.

Aby przeprowadzić migrację ręczną, dostępne są następujące opcje:
- Korzystaj z istniejących narzędzi, takich jak AzCopy, jednej z bibliotek klienckich usługi Azure Storage lub niezawodnych narzędzi innych firm.
- Jeśli znasz już usługi Hadoop lub HDInsight, Dołącz konto Source i Destination (ZRS) do klastra. Następnie zrównoleglanie proces kopiowania danych za pomocą narzędzia, takiego jak pomocą distcp.
- Utwórz własne narzędzia przy użyciu jednej z bibliotek klienta usługi Azure Storage.

Migracja ręczna może skutkować przestojem aplikacji. Jeśli aplikacja wymaga wysokiej dostępności, firma Microsoft udostępnia również opcję migracji na żywo. Migracja na żywo to migracja w miejscu. 

Podczas migracji na żywo można używać konta magazynu podczas migrowania danych między źródłową i docelową sygnaturami magazynowymi. Podczas migracji masz taki sam poziom trwałości i umowy SLA, jak zwykle.

Należy pamiętać o następujących ograniczeniach migracji na żywo:

- Mimo że firma Microsoft obsłuży Twoje żądanie migracji na żywo tak szybko, jak to możliwe, nie ma możliwości określenia, kiedy migracja na żywo zostanie ukończona. Jeśli chcesz, aby dane były migrowane do ZRS przez pewien dzień, firma Microsoft zaleca przeprowadzenie ręcznej migracji. Ogólnie rzecz biorąc, im więcej danych zawiera Twoje konto, tym dłużej trwa ich migracja. 
- Migracja na żywo jest obsługiwana tylko w przypadku kont magazynu, które używają replikacji LRS lub GRS. Jeśli Twoje konto używa RA-GRS, przed kontynuowaniem musisz najpierw zmienić typ replikacji konta na LRS lub GRS. Ten krok pośrednika usuwa pomocniczy punkt końcowy tylko do odczytu dostarczony przez RA-GRS przed migracją.
- Twoje konto musi zawierać dane.
- Dane można migrować tylko w tym samym regionie. Jeśli chcesz przeprowadzić migrację danych do konta ZRS znajdującego się w regionie innym niż konto źródłowe, należy przeprowadzić migrację ręczną.
- Tylko standardowe typy kont magazynu obsługują migrację na żywo. Konta magazynu w warstwie Premium muszą być migrowane ręcznie.
- Migracja na żywo z ZRS do LRS, GRS lub RA-GRS nie jest obsługiwana. Konieczne będzie ręczne przeniesienie danych na nowe lub istniejące konto magazynu.
- Dyski zarządzane są dostępne tylko dla LRS i nie można ich migrować do ZRS. Można przechowywać migawki i obrazy dla SSD w warstwie Standardowa Managed Disks HDD w warstwie Standardowa magazynu i [wybierać między opcjami LRS i ZRS](https://azure.microsoft.com/pricing/details/managed-disks/). Aby poznać integrację z zestawami dostępności [, zobacz Wprowadzenie do usługi Azure Managed disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#integration-with-availability-sets).
- Kont LRS lub GRS z danymi archiwalnymi nie można migrować do ZRS.

Możesz zażądać migracji na żywo za pomocą [portalu pomocy technicznej systemu Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). W portalu wybierz konto magazynu, które chcesz skonwertować do ZRS.
1. Wybierz **nowe żądanie obsługi**
2. Wypełnij **podstawowe** informacje na podstawie informacji o koncie. W sekcji **Usługa** wybierz pozycję **Zarządzanie kontem magazynu** i zasób, który chcesz przekonwertować na ZRS. 
3. Wybierz opcję **Dalej**. 
4. Określ następujące wartości w sekcji **problem** : 
    - **Ważność**: Pozostaw wartość domyślną.
    - **Typ problemu**: Wybierz pozycję **migracja danych**.
    - **Kategoria**: Wybierz pozycję **Migruj do ZRS w regionie**.
    - **Tytuł**: Wpisz opisowy tytuł, na przykład **ZRS**.
    - **Szczegóły**: Wpisz dodatkowe szczegóły w polu **szczegóły** , na przykład chcę przeprowadzić migrację do ZRS z [LRS, GRS] w \_ \_ regionie. 
5. Wybierz opcję **Dalej**.
6. Sprawdź, czy informacje kontaktowe są poprawne w bloku **informacje kontaktowe** .
7. Wybierz pozycję **Utwórz**.

Osoba odpowiedzialna za pomoc techniczną skontaktuje się z Tobą i pomoże Ci uzyskać pomoc.

## <a name="live-migration-to-zrs-faq"></a>Migracja na żywo do ZRS — często zadawane pytania

**Czy należy zaplanować ewentualne przestoje w trakcie migracji?**

Brak przestojów spowodowanych przez migrację. Podczas migracji na żywo można nadal korzystać z konta magazynu podczas migrowania danych między źródłową i docelową sygnaturami magazynowymi. Podczas migracji masz taki sam poziom trwałości i umowy SLA, jak zwykle.

**Czy istnieje utrata danych skojarzona z migracją?**

Brak utraty danych skojarzonych z migracją. Podczas migracji masz taki sam poziom trwałości i umowy SLA, jak zwykle.

**Czy aktualizacje są wymagane dla aplikacji po zakończeniu migracji?**

Po zakończeniu migracji typ replikacji kont zmieni się na "magazyn strefowo nadmiarowy (ZRS)". Punkty końcowe usługi, klucze dostępu, SAS i inne opcje konfiguracji konta pozostają bez zmian i nie zostały naruszone.

**Czy mogę zażądać migracji na żywo moich kont ogólnego przeznaczenia w wersji 1 do ZRS?**

ZRS obsługuje tylko konta ogólnego przeznaczenia w wersji 2, dlatego przed przesłaniem żądania migracji na żywo do ZRS upewnij się, że Twoje konta zostały uaktualnione do poziomu ogólnego przeznaczenia w wersji 2. Aby uzyskać więcej informacji, zobacz [Omówienie konta usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-account-overview) i [Uaktualnij do konta magazynu ogólnego przeznaczenia w wersji 2](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) .

**Czy mogę zażądać migracji na żywo kont magazynu geograficznie nadmiarowego (RA-GRS) z dostępem do odczytu do usługi ZRS?**

Przed przesłaniem żądania migracji na żywo do ZRS upewnij się, że aplikacje lub obciążenia nie wymagają już dostępu do pomocniczego punktu końcowego tylko do odczytu i Zmień typ replikacji kont magazynu na magazyn Geograficznie nadmiarowy (GRS). Aby uzyskać więcej informacji, zobacz [Zmienianie strategii replikacji](https://docs.microsoft.com/azure/storage/common/storage-redundancy#changing-replication-strategy) .

**Czy mogę zażądać migracji na żywo moich kont magazynu do ZRS do innego regionu?**

Jeśli chcesz przeprowadzić migrację danych do konta ZRS znajdującego się w regionie innym niż region konta źródłowego, musisz przeprowadzić migrację ręczną.

## <a name="zrs-classic-a-legacy-option-for-block-blobs-redundancy"></a>ZRS klasyczny: Starsza opcja dla nadmiarowości blokowych obiektów BLOB
> [!NOTE]
> Firma Microsoft będzie wycofać i zmigrować klasyczne konta ZRS w dniu 31 marca 2021. Więcej szczegółowych informacji ZRS klasycznym klientom przed jego wycofaniem. 
>
> Gdy ZRS stanie się [ogólnie dostępna](#support-coverage-and-regional-availability) w regionie, klienci nie będą mogli tworzyć klasycznych kont ZRS w tym regionie. Za pomocą programu Microsoft PowerShell i interfejsu wiersza polecenia platformy Azure do tworzenia kont klasycznych ZRS jest opcja do momentu, w którym jest przestarzałe ZRS klasyczne.

ZRS klasyczny asynchronicznie replikuje dane między centrami danych w jednym lub dwóch regionach. Zreplikowane dane mogą nie być dostępne, jeśli firma Microsoft zainicjuje przejście w tryb failover do pomocniczej. Nie można przekonwertować klasycznego konta ZRS na lub z LRS, GRS lub RA-GRS. Klasyczne konta ZRS również nie obsługują metryk ani rejestrowania.

ZRS klasyczny jest dostępny tylko dla **blokowych obiektów BLOB** w ramach kont magazynu ogólnego przeznaczenia w wersji 1 (GPv1). Aby uzyskać więcej informacji dotyczących kont magazynu, zobacz temat [Azure Storage account overview](storage-account-overview.md) (Omówienie konta usługi Azure Storage).

Aby ręcznie przeprowadzić migrację danych konta ZRS do lub z konta LRS, ZRS klasycznego, GRS lub RA-GRS, użyj jednego z następujących narzędzi: AzCopy, Eksplorator usługi Azure Storage, Azure PowerShell lub interfejs wiersza polecenia platformy Azure. Możesz również utworzyć własne rozwiązanie migracji przy użyciu jednej z bibliotek klienckich usługi Azure Storage.

Możesz również uaktualnić swoje klasyczne konta ZRS do ZRS w portalu lub za pomocą Azure PowerShell lub interfejsu wiersza polecenia platformy Azure w regionach, w których ZRS jest dostępny.

Aby uaktualnić do ZRS w portalu, przejdź do sekcji Konfiguracja konta i wybierz pozycję Uaktualnij:![Uaktualnij ZRS klasyczne do ZRS w portalu](media/storage-redundancy-zrs/portal-zrs-classic-upgrade.jpg)

Aby przeprowadzić uaktualnienie do ZRS przy użyciu programu PowerShell, wywołaj następujące polecenie:
```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> -AccountName <storage_account> -UpgradeToStorageV2
```

Aby przeprowadzić uaktualnienie do ZRS przy użyciu interfejsu wiersza polecenia, wywołaj następujące polecenie:
```cli
az storage account update -g <resource_group> -n <storage_account> --set kind=StorageV2
```

## <a name="see-also"></a>Zobacz także
- [Replikacja usługi Azure Storage](storage-redundancy.md)
- [Magazyn lokalnie nadmiarowy (LRS): Niski koszt nadmiarowości danych dla usługi Azure Storage](storage-redundancy-lrs.md)
- [Magazyn Geograficznie nadmiarowy (GRS): Replikacja między regionami w ramach usługi Azure Storage](storage-redundancy-grs.md)
