---
title: Uaktualnianie do konta magazynu ogólnego przeznaczenia v2 — usługi Azure Storage | Dokumentacja firmy Microsoft
description: Uaktualnij do kont magazynu ogólnego przeznaczenia w wersji 2.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/26/2019
ms.author: tamram
ms.openlocfilehash: a1ee95ad847d20159c79af8f080cc7878e114759
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58444532"
---
# <a name="upgrade-to-a-general-purpose-v2-storage-account"></a>Uaktualnienie do konta magazynu ogólnego przeznaczenia w wersji 2

Konta magazynu ogólnego przeznaczenia v2 obsługi najnowszych funkcji usługi Azure Storage i uwzględnić je wszystkie funkcje ogólnego przeznaczenia w wersji 1 i kont usługi Blob storage. Konta ogólnego przeznaczenia v2 są zalecane w przypadku większości scenariuszy magazynu. Konta ogólnego przeznaczenia w wersji 2 dostarczanie najniższy gigabajt ceny pojemności dla usługi Azure Storage oraz konkurencyjne w branży ceny transakcji.

Uaktualnienie do konta magazynu ogólnego przeznaczenia v2 z Twojej ogólnego przeznaczenia w wersji 1 lub kont usługi Blob storage jest proste. Można uaktualnić za pomocą witryny Azure portal, programu PowerShell lub wiersza polecenia platformy Azure.

> [!IMPORTANT]
> Uaktualnianie ogólnego przeznaczenia w wersji 1 lub konta magazynu usługi Blob storage do ogólnego przeznaczenia w wersji 2 jest trwały i nie można cofnąć.

## <a name="upgrade-using-the-azure-portal"></a>Uaktualnianie za pomocą witryny Azure portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Przejdź do swojego konta magazynu.
3. W **ustawienia** kliknij **konfiguracji**.
4. W obszarze **Rodzaj konta** kliknij pozycję **Uaktualnij**.
5. W obszarze **Potwierdź uaktualnianie** wpisz nazwę konta.
6. Kliknij przycisk **uaktualnienia** w dolnej części bloku.

    ![Uaktualnij rodzaj konta](../blobs/media/storage-blob-account-upgrade/upgrade-to-gpv2-account.png)

## <a name="upgrade-with-powershell"></a>Podnoszenie poziomu przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Aby podnieść poziom konta ogólnego przeznaczenia w wersji 1 do konta ogólnego przeznaczenia w wersji 2 przy użyciu programu PowerShell, należy najpierw zaktualizować program PowerShell w celu używania najnowszej wersji programu **Az.Storage** modułu. Aby uzyskać więcej informacji na temat instalowania programu PowerShell, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

Następnie wywołaj następujące polecenie, aby uaktualnić konto, zastępując nazwę grupy zasobów i konto magazynu:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group> -AccountName <storage-account> -UpgradeToStorageV2
```

## <a name="upgrade-with-azure-cli"></a>Podnoszenie poziomu za pomocą interfejsu wiersza polecenia platformy Azure

Aby podnieść poziom konta ogólnego przeznaczenia w wersji 1 do konta ogólnego przeznaczenia w wersji 2 przy użyciu wiersza polecenia platformy Azure, należy najpierw zainstalować najnowszą wersję interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji na temat instalowania interfejsu wiersza polecenia, zobacz [Instalacja interfejsu wiersza polecenia platformy Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

Następnie wywołaj następujące polecenie, aby uaktualnić konto, zastępując nazwę grupy zasobów i konto magazynu:

```cli
az storage account update -g <resource-group> -n <storage-account> --set kind=StorageV2
```

## <a name="specify-an-access-tier-for-blob-data"></a>Określenie warstwy dostępu do danych obiektów blob

Konta ogólnego przeznaczenia v2 obsługują wszystkie usługi Azure storage i obiekty danych, ale warstwy dostępu są dostępne tylko dla blokowych obiektów blob w magazynie obiektów Blob. Podczas uaktualniania do konta magazynu ogólnego przeznaczenia w wersji 2 można określić warstwy dostępu do danych obiektów blob.

Warstwy dostępu umożliwiają wybranie najbardziej ekonomiczny magazyn na podstawie wzorców Twojej oczekiwanego użycia. Blokowe obiekty BLOB mogą być przechowywane w warstwie gorąca, chłodna lub archiwalna. Aby uzyskać więcej informacji na temat warstw dostępu, zobacz [usługi Azure Blob storage: Gorąca, chłodna i archiwalnego](../blobs/storage-blob-storage-tiers.md).

Domyślnie nowe konto magazynu jest tworzony w ramach warstwy dostępu gorąca, a konto magazynu ogólnego przeznaczenia w wersji 1 jest uaktualniany do warstwy dostępu gorąca. Jeśli analizujesz które warstwy dostępu na potrzeby usługi danych po uaktualnieniu, należy wziąć pod uwagę danego scenariusza. Istnieją dwa scenariusze typowy użytkownik migracji do konta ogólnego przeznaczenia v2:

* Masz już istniejące konto magazynu ogólnego przeznaczenia w wersji 1 i chcesz ocenić uaktualnienie na konto magazynu ogólnego przeznaczenia v2 z warstwą dostępu do przechowywania danych obiektów blob.
* Zamierzasz użyć konta magazynu ogólnego przeznaczenia w wersji 2 lub już mieć jedną i chcesz ocenić, czy należy używać gorąca lub chłodna Warstwa dostępu do magazynu danych obiektów blob.

W obu przypadkach priorytetem jest oszacować koszt przechowywania, uzyskiwanie dostępu i wykonywanie operacji na danych przechowywanych na koncie magazynu ogólnego przeznaczenia w wersji 2 i porównać z obecnie ponoszonymi kosztami.

## <a name="pricing-and-billing"></a>Cennik i rozliczenia

Trwa uaktualnianie konta magazynu w wersji 1 do konta ogólnego przeznaczenia w wersji 2 jest bezpłatny. Jednak zmiana warstwy dostępu do magazynu może spowodować zmiany na rachunku. 

Wszystkie konta magazynu używają modelu cenowego dla magazynu obiektów blob opartego na warstwie każdego obiektu blob. W przypadku korzystania z konta magazynu mają zastosowanie następujące zagadnienia dotyczące rozliczeń:

* **Koszty magazynowania**: Oprócz ilości przechowywanych danych koszt przechowywania danych różni się w zależności od warstwy dostępu do magazynu. Koszt za gigabajt zmniejsza się w miarę, jak warstwa staje się chłodniejsza.

* **Koszty dostępu do danych**: Dostęp do danych opłat wzrostu, jak warstwa staje się chłodniejsza. W przypadku danych w warstwie chłodna i archiwum dostępu do magazynu jest naliczana opłata dostępu do danych za każdy gigabajt dla operacji odczytu.

* **Koszty transakcji**: Brak opłat każdej transakcji dla wszystkich warstw, która zwiększa się, jak warstwa staje się chłodniejsza.

* **Koszty transferu danych replikacji geograficznej**: Ta opłata dotyczy tylko kont ze skonfigurowaną replikacją geograficzną, w tym GRS i RA-GRS. Transfer danych w ramach replikacji geograficznej powoduje naliczanie opłaty za każdy gigabajt.

* **Koszty transferu danych wychodzących**: Wychodzące transfery danych (dane przesyłane poza region platformy Azure) Naliczanie opłat za zużycie przepustowości za każdy gigabajt, co jest spójne z kontami magazynu ogólnego przeznaczenia.

* **Zmiana warstwy dostępu do magazynu**: Zmiana warstwy dostępu konta magazynu z chłodnej na gorącą spowoduje naliczenie opłaty równej odczytanie wszystkich danych na koncie magazynu. Jednak zmiana warstwy dostępu konta z gorąca na chłodna spowoduje naliczenie opłaty równej zapisanie wszystkich danych w warstwie chłodna (tylko konta GPv2).

> [!NOTE]
> Więcej informacji dotyczących modelu cenowego dla kont magazynu można znaleźć na stronie [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/). Więcej informacji dotyczących opłat za transfer danych wychodzących można znaleźć na stronie [Szczegóły cennika transferów danych](https://azure.microsoft.com/pricing/details/data-transfers/).

### <a name="estimate-costs-for-your-current-usage-patterns"></a>Oszacuj koszty swoje bieżące wzorce użycia

Aby oszacować koszty magazynowania i uzyskiwania dostępu do danych obiektów blob w ramach konta magazynu ogólnego przeznaczenia w wersji 2 w określonej warstwie, oceny istniejącego wzorca użycia lub określenie w przybliżeniu oczekiwanego wzorca użycia. Ogólnie potrzebne są odpowiedzi na następujące pytania:

* Obiekt Blob zużycie miejsca w magazynie, w gigabajtach, w tym:
    - Ile danych jest przechowywanych na koncie magazynu?
    - Jak zmienia się ilość danych w miesiącu? Czy nowe dane nieustannie zastępują stare dane?
* Wzorzec dostępu dane magazynu obiektów Blob, w tym:
    - Jak dużo danych jest odczytywanych i zapisywanych do konta magazynu?
    - Jak wiele operacji odczytu i zapisu operacje są wykonywane na danych w ramach konta magazynu?

Aby wybrać najważniejsze dla Twoich potrzeb warstwę dostępu, może być przydatne do określenia pojemnością danych obiektów blob i sposobu korzystania z tych danych. To jest najlepsze możliwe, analizując metryki monitorowania dla swojego konta.

### <a name="monitoring-existing-storage-accounts"></a>Monitorowanie istniejących kont magazynu

Aby monitorować istniejące konta magazynu i gromadzić uzyskane dane, można skorzystać z usługi Azure Storage Analytics, która umożliwia rejestrowanie i dostarcza danych metryk dotyczących konta magazynu. Usługa Storage Analytics może przechowywać metryki, które obejmują zagregowane statystyki transakcji oraz dane pojemności o żądaniach do usługi Storage dla następujących rodzajów kont: GPv1, GPv2 i Blob Storage. Te dane są przechowywane w dobrze znanych tabelach tego samego konta magazynu.

Aby uzyskać więcej informacji, zapoznaj się z artykułami [About Storage Analytics Metrics (Informacje o metrykach w usłudze Storage Analytics)](https://msdn.microsoft.com/library/azure/hh343258.aspx) i [Storage Analytics Metrics Table Schema (Schemat tabeli metryk usługi Storage Analytics)](https://msdn.microsoft.com/library/azure/hh343264.aspx)

> [!NOTE]
> Konta usługi Blob Storage ujawniają punkt końcowy usługi tabel tylko w odniesieniu do przechowywania i uzyskiwania dostępu do danych metryk dla tego konta.

Aby monitorować użycie magazynu dla usługi Blob Storage, należy włączyć metryki pojemności.
Dzięki włączeniu tej opcji dane pojemności są rejestrowane codziennie dla konta usługi Blob Storage oraz rejestrowane jako wpis tabeli, który jest zapisywany w tabeli *$MetricsCapacityBlob* w obrębie tego samego konta magazynu.

Aby monitorować wzorce dostępu do danych dla usługi Blob Storage, należy włączyć godzinowe metryki transakcji z poziomu interfejsu API. Dzięki włączeniu godzinowych metryk transakcji transakcje interfejsu API są agregowane co godzinę i rejestrowane jako wpis tabeli, który jest zapisywany w tabeli *$MetricsHourPrimaryTransactionsBlob* w obrębie tego samego konta magazynu. W przypadku używania kont magazynu RA-GRS tabela *$MetricsHourSecondaryTransactionsBlob* rejestruje transakcje kierowane do pomocniczego punktu końcowego.

> [!NOTE]
> Jeśli masz konto magazynu ogólnego przeznaczenia, w którym są przechowywane stronicowe obiekty BLOB i dyski maszyny wirtualnej albo kolejki, pliki lub tabele obok blokowych i danych uzupełnialnych obiektów blob, ten proces szacowania nie ma zastosowania. Dane pojemności nie obejmują rozróżnienia blokowych obiektów blob od innych typów ani danych pojemności dla innych typów danych. Jeśli używasz tych typów, alternatywną metodologią będzie zapoznanie się z ilościami na najnowszym rachunku.

Aby uzyskać najbardziej zbliżone do prawdziwych informacje o użyciu danych i wzorcu dostępu, zalecamy wybranie takiego okresu przechowywania dla metryk, który odzwierciedla normalne użycie, i ekstrapolację. Jedną z opcji jest przechowywanie danych metryk przez siedem dni i zbieranie danych co tydzień, aby przeprowadzić analizę pod koniec miesiąca. Innym rozwiązaniem jest przechowywanie danych metryk z ostatnich 30 dni i zbieranie oraz analizowanie danych z końcem 30-dniowego okresu.

Aby uzyskać więcej informacji na temat włączania, gromadzenia i wyświetlania danych metryk, zobacz [Storage analytics metrics](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> Za przechowywanie i pobieranie danych analitycznych oraz uzyskiwanie dostępu do nich również są naliczane opłaty, podobnie jak za zwykłe dane użytkowników.

### <a name="utilizing-usage-metrics-to-estimate-costs"></a>Używanie metryk użycia do szacowania kosztów

#### <a name="capacity-costs"></a>Koszty wydajności

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
> Aby uzyskać bardziej szczegółowy przykład temat obliczania kosztów używania magazynowania gorąca czy chłodna Warstwa dostępu, zapoznaj się z często Zadawanych pytań *"co to są warstwy dostępu gorąca i chłodna i jak określić, które?"* na stronie z [cennikiem usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

## <a name="next-steps"></a>Kolejne kroki

- [Tworzenie konta magazynu](storage-quickstart-create-account.md)
- [Zarządzanie kontami usługi Azure storage](storage-account-manage.md)
