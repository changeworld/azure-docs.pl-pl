---
title: Uaktualnienie konta magazynu ogólnego przeznaczenia do wersji 2
titleSuffix: Azure Storage
description: Uaktualnij do kont magazynu ogólnego przeznaczenia w wersji 2.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/25/2019
ms.author: tamram
ms.openlocfilehash: 9afbade408d6f95fcd3a61aa1ba65bc09c7a875b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067228"
---
# <a name="upgrade-to-a-general-purpose-v2-storage-account"></a>Uaktualnienie konta magazynu ogólnego przeznaczenia do wersji 2

Konta magazynu ogólnego przeznaczenia w wersji 2 obsługują najnowsze funkcje usługi Azure Storage i zawierają wszystkie funkcje kont magazynu ogólnego przeznaczenia w wersji 1 i blob. Konta ogólnego przeznaczenia w wersji 2 są zalecane w większości scenariuszy magazynowania. Konta ogólnego przeznaczenia w wersji 2 zapewniają najniższe ceny pojemności dla gigabajtów usługi Azure Storage, a także konkurencyjne w branży ceny transakcyjne. Konta ogólnego przeznaczenia w wersji 2 obsługują domyślne warstwy dostępu do konta na poziomie hot lub cool i blob między gorącymi, chłodnymi lub archiwalnymi.

Uaktualnienie do konta magazynu ogólnego przeznaczenia w wersji 2 z kont magazynu ogólnego przeznaczenia w wersji 1 lub obiektów Blob jest proste. Można uaktualnić przy użyciu witryny Azure portal, PowerShell lub interfejsu wiersza polecenia platformy Azure. Nie ma przestojów ani ryzyka utraty danych związanych z uaktualnieniem do konta magazynu ogólnego przeznaczenia w wersji 2. Uaktualnienie konta odbywa się za pomocą prostej operacji usługi Azure Resource Manager, która zmienia typ konta.

> [!IMPORTANT]
> Uaktualnienie konta magazynu ogólnego przeznaczenia w wersji 1 lub blob do wersji 2 ogólnego przeznaczenia jest trwałe i nie można go cofnąć.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Przejdź do swojego konta magazynu.
3. W sekcji **Ustawienia** kliknij pozycję **Konfiguracja**.
4. W obszarze **Rodzaj konta**kliknij **upgrade**.
5. W obszarze **Potwierdź uaktualnienie**wpisz nazwę swojego konta.
6. Kliknij **przycisk Uaktualnij** w dolnej części bloku.

    ![Rodzaj konta uaktualnienia](../blobs/media/storage-blob-account-upgrade/upgrade-to-gpv2-account.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Aby uaktualnić konto ogólnego przeznaczenia w wersji 1 do konta ogólnego przeznaczenia w wersji 2 przy użyciu programu PowerShell, należy najpierw zaktualizować program PowerShell, aby użyć najnowszej wersji modułu **Az.Storage.** Aby uzyskać więcej informacji na temat instalowania programu PowerShell, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

Następnie zadzwoń do następującego polecenia, aby uaktualnić konto, zastępując nazwę grupy zasobów, nazwę konta magazynu i żądaną warstwę dostępu do konta.

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group> -AccountName <storage-account> -UpgradeToStorageV2 -AccessTier <Hot/Cool>
```
# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby uaktualnić konto ogólnego przeznaczenia w wersji 1 do konta ogólnego przeznaczenia w wersji 2 przy użyciu interfejsu wiersza polecenia platformy Azure, należy najpierw zainstalować najnowszą wersję interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji na temat instalowania interfejsu wiersza polecenia, zobacz [Instalacja interfejsu wiersza polecenia platformy Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

Następnie zadzwoń do następującego polecenia, aby uaktualnić konto, zastępując nazwę grupy zasobów, nazwę konta magazynu i żądaną warstwę dostępu do konta.

```azurecli
az storage account update -g <resource-group> -n <storage-account> --set kind=StorageV2 --access-tier=<Hot/Cool>
```

---

## <a name="specify-an-access-tier-for-blob-data"></a>Określanie warstwy dostępu dla danych obiektów blob

Konta ogólnego przeznaczenia w wersji 2 obsługują wszystkie usługi magazynu platformy Azure i obiekty danych, ale warstwy dostępu są dostępne tylko do bloków obiektów blob w magazynie obiektów Blob. Podczas uaktualniania do ogólnego przeznaczenia konta magazynu w wersji 2, można określić domyślną warstwę dostępu do konta hot lub cool, która wskazuje domyślną warstwę danych obiektu blob zostaną przekazane tak, jakby parametr warstwy dostępu poszczególnych obiektów blob nie jest określony.

Warstwy dostępu do obiektów blob umożliwiają wybranie najbardziej opłacalnej pamięci masowej na podstawie przewidywanych wzorców użycia. Blokowe obiekty blob mogą być przechowywane w warstwach gorących, chłodnych lub archiwalnych. Aby uzyskać więcej informacji na temat warstw dostępu, zobacz [Usługi Azure Blob storage: Hot, Cool i Archive storage tiers](../blobs/storage-blob-storage-tiers.md).

Domyślnie nowe konto magazynu jest tworzone w warstwie dostępu gorąca, a ogólne przeznaczenie konta magazynu w wersji 1 można uaktualnić do warstwy konta gorąca lub chłodna. Jeśli warstwa dostępu do konta nie jest określona podczas uaktualniania, domyślnie zostanie uaktualniona do poziomu hot. Jeśli eksplorujesz warstwę dostępu do użycia w uaktualnieniu, należy wziąć pod uwagę bieżący scenariusz użycia danych. Istnieją dwa typowe scenariusze użytkownika do migracji do konta ogólnego przeznaczenia w wersji 2:

* Masz istniejące konto magazynu ogólnego przeznaczenia w wersji 1 i chcesz ocenić uaktualnienie do konta magazynu ogólnego przeznaczenia w wersji 2, z odpowiednią warstwą dostępu magazynu dla danych obiektów blob.
* Zdecydowano się użyć ogólnego przeznaczenia konta magazynu w wersji 2 lub już je masz i chcesz ocenić, czy należy użyć warstwy dostępu do magazynu gorąca lub chłodna dla danych obiektów blob.

W obu przypadkach pierwszym priorytetem jest oszacowanie kosztów przechowywania, uzyskiwania dostępu i obsługi danych przechowywanych na koncie magazynu ogólnego przeznaczenia w wersji 2 i porównanie ich z bieżącymi kosztami.

## <a name="pricing-and-billing"></a>Cennik i rozliczenia

Uaktualnienie konta magazynu w wersji 1 do konta ogólnego przeznaczenia w wersji 2 jest bezpłatne. Podczas procesu uaktualniania można określić żądaną warstwę konta. Jeśli warstwa konta nie jest określona podczas uaktualniania, domyślną warstwą konta uaktualnionego konta będzie `Hot`. Jednak zmiana warstwy dostępu do magazynu po uaktualnieniu może spowodować zmiany na rachunku, więc zaleca się określenie nowej warstwy konta podczas uaktualniania.

Wszystkie konta magazynu używają modelu cenowego dla magazynu obiektów blob opartego na warstwie każdego obiektu blob. W przypadku korzystania z konta magazynu mają zastosowanie następujące zagadnienia dotyczące rozliczeń:

* **Koszty magazynowania:** Oprócz ilości przechowywanych danych koszt przechowywania danych różni się w zależności od warstwy dostępu do magazynu. Koszt za gigabajt zmniejsza się w miarę, jak warstwa staje się chłodniejsza.

* **Koszty dostępu do danych**: opłaty za dostęp do danych wzrastają w miarę, jak warstwa staje się chłodniejsza. W przypadku danych w warstwie dostępu do chłodnej i archiwum pobierana jest opłata za dostęp do danych za gigabajt dla odczytów.

* **Koszty transakcji**: w przypadku wszystkich warstw naliczana jest opłata za transakcję, która wzrasta w miarę, jak warstwa staje się chłodniejsza.

* **Koszty transferu danych replikacji geograficznej**: ta opłata dotyczy tylko kont ze skonfigurowaną replikacją geograficzną, w tym GRS i RA-GRS. Transfer danych w ramach replikacji geograficznej powoduje naliczanie opłaty za każdy gigabajt.

* **Koszty transferu danych wychodzących**: transfery danych wychodzących (dane przesyłane poza region platformy Azure) powodują naliczanie opłat za zużycie przepustowości za każdy gigabajt, co jest spójne z kontami magazynu ogólnego przeznaczenia.

* **Zmiana warstwy dostępu do magazynu:** Zmiana warstwy dostępu do magazynu konta z chłodnej na gorącą wiąże się z opłatą równą odczytywania wszystkich danych istniejących na koncie magazynu. Jednak zmiana warstwy dostępu do konta z gorącej na chłodną wiąże się z opłatą równą zapisywaniu wszystkich danych w warstwie chłodnej (tylko konta GPv2).

> [!NOTE]
> Więcej informacji dotyczących modelu cenowego dla kont magazynu można znaleźć na stronie [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/). Więcej informacji dotyczących opłat za transfer danych wychodzących można znaleźć na stronie [Szczegóły cennika transferów danych](https://azure.microsoft.com/pricing/details/data-transfers/).

### <a name="estimate-costs-for-your-current-usage-patterns"></a>Oszacuj koszty bieżących wzorców użycia

Aby oszacować koszt przechowywania i uzyskiwania dostępu do danych obiektów blob na koncie magazynu ogólnego przeznaczenia w wersji 2 w określonej warstwie, należy ocenić istniejący wzorzec użycia lub przybliżyć oczekiwany wzorzec użycia. Ogólnie potrzebne są odpowiedzi na następujące pytania:

* Zużycie pamięci blob w gigabajtach, w tym:
  * Ile danych jest przechowywanych na koncie magazynu?
  * Jak zmienia się ilość danych w miesiącu? Czy nowe dane nieustannie zastępują stare dane?

* Podstawowy wzorzec dostępu dla danych magazynu obiektów Blob, w tym:
  * Ile danych jest odczytywanych i zapisywanych na koncie magazynu?
  * Ile operacji odczytu w porównaniu z operacjami zapisu występuje na danych na koncie magazynu?

Aby zdecydować o najlepszej warstwie dostępu dla Twoich potrzeb, może być przydatne określenie pojemności danych obiektu blob i sposobu, w jaki te dane są używane. Najlepiej to zrobić, patrząc na metryki monitorowania dla twojego konta.

### <a name="monitoring-existing-storage-accounts"></a>Monitorowanie istniejących kont magazynu

Aby monitorować istniejące konta magazynu i gromadzić uzyskane dane, można skorzystać z usługi Azure Storage Analytics, która umożliwia rejestrowanie i dostarcza danych metryk dotyczących konta magazynu. Usługa Storage Analytics może przechowywać metryki, które obejmują zagregowane statystyki transakcji oraz dane pojemności o żądaniach do usługi Storage dla następujących rodzajów kont: GPv1, GPv2 i Blob Storage. Te dane są przechowywane w dobrze znanych tabelach tego samego konta magazynu.

Aby uzyskać więcej informacji, zapoznaj się z artykułami [About Storage Analytics Metrics (Informacje o metrykach w usłudze Storage Analytics)](https://msdn.microsoft.com/library/azure/hh343258.aspx) i [Storage Analytics Metrics Table Schema (Schemat tabeli metryk usługi Storage Analytics)](https://msdn.microsoft.com/library/azure/hh343264.aspx)

> [!NOTE]
> Konta usługi Blob Storage ujawniają punkt końcowy usługi tabel tylko w odniesieniu do przechowywania i uzyskiwania dostępu do danych metryk dla tego konta.

Aby monitorować użycie magazynu dla usługi Blob Storage, należy włączyć metryki pojemności.
Po włączeniu tej pojemności dane pojemności są rejestrowane codziennie dla usługi obiektów Blob konta magazynu i rejestrowane jako wpis tabeli, który jest zapisywany w tabeli *$MetricsCapacityBlob* w ramach tego samego konta magazynu.

Aby monitorować wzorce dostępu do danych dla usługi Blob Storage, należy włączyć godzinowe metryki transakcji z poziomu interfejsu API. Dzięki włączeniu godzinowych metryk transakcji transakcje interfejsu API są agregowane co godzinę i rejestrowane jako wpis tabeli, który jest zapisywany w tabeli *$MetricsHourPrimaryTransactionsBlob* w obrębie tego samego konta magazynu. W przypadku używania kont magazynu RA-GRS tabela *$MetricsHourSecondaryTransactionsBlob* rejestruje transakcje kierowane do pomocniczego punktu końcowego.

> [!NOTE]
> Jeśli masz konto magazynu ogólnego przeznaczenia, w którym są przechowywane stronicowe obiekty blob i dyski maszyny wirtualnej albo kolejki, pliki lub tabele (obok danych blokowych obiektów blob i uzupełnialnych obiektów blob), ten proces szacowania nie ma zastosowania. Dane pojemności nie obejmują rozróżnienia blokowych obiektów blob od innych typów ani danych pojemności dla innych typów danych. Jeśli używasz tych typów, alternatywną metodologią będzie zapoznanie się z ilościami na najnowszym rachunku.

Aby uzyskać najbardziej zbliżone do prawdziwych informacje o użyciu danych i wzorcu dostępu, zalecamy wybranie takiego okresu przechowywania dla metryk, który odzwierciedla normalne użycie, i ekstrapolację. Jedną z opcji jest przechowywanie danych metryk przez siedem dni i zbieranie danych co tydzień, aby przeprowadzić analizę pod koniec miesiąca. Innym rozwiązaniem jest przechowywanie danych metryk z ostatnich 30 dni i zbieranie oraz analizowanie danych z końcem 30-dniowego okresu.

Aby uzyskać szczegółowe informacje na temat włączania, zbierania i wyświetlania danych metryk, zobacz [Metryki analizy magazynu](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> Za przechowywanie i pobieranie danych analitycznych oraz uzyskiwanie dostępu do nich również są naliczane opłaty, podobnie jak za zwykłe dane użytkowników.

### <a name="utilizing-usage-metrics-to-estimate-costs"></a>Używanie metryk użycia do szacowania kosztów

#### <a name="capacity-costs"></a>Koszty zdolności produkcyjnych

Najnowszy wpis w tabeli metryk pojemności *$MetricsCapacityBlob* z kluczem wiersza *„data”* pokazuje pojemność magazynu zajętą przez dane użytkownika. Najnowszy wpis w tabeli metryk pojemności *$MetricsCapacityBlob* z kluczem wiersza *„analytics”* pokazuje pojemność magazynu zajętą przez dzienniki analiz.

Łączna pojemność zajęta przez dane użytkownika i dzienniki analiz (jeśli są włączone) może być użyta do szacowania kosztów magazynowania danych w ramach konta magazynu. Tę samą metodę można również użyć do szacowania kosztów magazynowania na kontach magazynu GPv1.

#### <a name="transaction-costs"></a>Koszty transakcji

Suma *„TotalBillableRequests”* dla wszystkich wpisów interfejsu API w tabeli metryk transakcji wskazuje całkowitą liczbę transakcji dla tego konkretnego interfejsu API. *Na przykład* całkowitą liczbę transakcji *„GetBlob”* w danym okresie można obliczyć, sumując liczbę płatnych żądań dla wszystkich wpisów z kluczem wiersza *'user;GetBlob'*.

Aby oszacować koszty transakcji dla kont usługi Blob Storage, konieczne będzie podzielenie transakcji na trzy grupy, ponieważ mają one różne ceny.

* Transakcje zapisu, takie jak *„PutBlob”*, *„PutBlock”*, *„PutBlockList”*, *„AppendBlock"*, *„ListBlobs”*, *„ListContainers”*, *„CreateContainer”*, *„SnapshotBlob”* i *„CopyBlob”*.
* Transakcje usuwania, takie jak *„DeleteBlob”* i *„DeleteContainer”*.
* Wszystkie inne transakcje.

W celu oszacowania kosztów transakcji dla kont magazynu GPv1 należy zagregować wszystkie transakcje niezależnie od operacji/interfejsu API.

#### <a name="data-access-and-geo-replication-data-transfer-costs"></a>Dostęp do danych i koszty transferu danych replikacji geograficznej

Usługa Storage Analytics nie udostępnia informacji na temat ilości odczytywanych i zapisywanych danych na koncie magazynu, ale można je w przybliżeniu oszacować, analizując tabelę metryk transakcji. Suma *„TotalIngress”* dla wszystkich wpisów interfejsu API w tabeli metryk transakcji wskazuje całkowitą ilość danych przychodzących w bajtach dla tego konkretnego interfejsu API. Podobnie suma *„TotalEgress”* wskazuje całkowitą ilość danych wychodzących w bajtach.

Aby oszacować koszty dostępu do danych dla kont usługi Blob Storage, konieczne będzie podzielenie transakcji na dwie grupy.

* Ilość danych pobieranych z konta magazynu można oszacować, przyglądając się sumie *„TotalEgress”* przede wszystkim dla operacji *„GetBlob”* i *„CopyBlob”*.

* Ilość danych zapisywanych na koncie magazynu można oszacować, przyglądając się sumie *„TotalIngress”* przede wszystkim dla operacji *„PutBlob”*, *„PutBlock”*, *„CopyBlob”* i *„AppendBlock”*.

Koszt transferu danych replikacji geograficznej dla kont usługi Blob Storage można obliczyć, korzystając z kalkulacji ilości danych zapisanych w przypadku używania konta magazynu GRS lub RA-GRS.

> [!NOTE]
> Aby uzyskać bardziej szczegółowy przykład na temat obliczania kosztów korzystania z warstwy dostępu do magazynu na gorąco lub chłodno, zapoznaj się z często zadawanymi *pytaniami zatytułowanymi "Co to są warstwy dostępu gorąca i chłodna i jak określić, którego z nich użyć?".* na stronie z [cennikiem usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

## <a name="next-steps"></a>Następne kroki

* [Tworzenie konta magazynu](storage-account-create.md)
