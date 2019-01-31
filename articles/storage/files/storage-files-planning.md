---
title: Planowanie wdrożenia usługi Azure Files | Dokumentacja firmy Microsoft
description: Dowiedz się, co należy wziąć pod uwagę podczas planowania wdrożenia usługi Azure Files.
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 06/12/2018
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: a9c37258d7c9631c6e5fe13007b78c4205a1c249
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55473888"
---
# <a name="planning-for-an-azure-files-deployment"></a>Planowanie wdrażania usługi Pliki Azure
[Usługa Azure Files](storage-files-introduction.md) oferuje w pełni zarządzane udziały plików w chmurze, które są dostępne za pośrednictwem standardowego protokołu SMB. Ponieważ usługi Azure Files jest w pełni zarządzana, wdrażania jej w scenariuszach produkcyjnych jest znacznie prostsze niż we wdrażaniu oraz zarządzaniu nim serwera plików lub urządzeń NAS. W tym artykule opisano tematów, aby wziąć pod uwagę podczas wdrażania udziału plików platformy Azure do użytku produkcyjnego w organizacji.

## <a name="management-concepts"></a>Pojęcia dotyczące usługi Management
 Na poniższym diagramie przedstawiono konstrukcje zarządzania usługi Azure Files:

![Struktura plików](./media/storage-files-introduction/files-concepts.png)

* **Konto magazynu**: Dostęp do usługi Azure Storage odbywa się za pośrednictwem konta magazynu. Aby uzyskać szczegółowe informacje na temat pojemności konta magazynu, zobacz [Cele dotyczące skalowalności i wydajności](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* **Udostępnij**: Udział usługi File Storage jest udziałem plików SMB na platformie Azure. Wszystkie pliki i katalogi muszą być tworzone w udziale nadrzędnym. Konto może zawierać nieograniczoną liczbę udziałów, a udział może przechowywać nieograniczoną liczbę plików, nieprzekraczającą 5 TiB całkowita objętość udziału plików.

* **Katalog**: Opcjonalna hierarchia katalogów.

* **Plik**: Plik w udziale. Plik może być maksymalnie 1 TiB rozmiar.

* **Format adresu URL**: Dla żądań kierowanych do udziału plików platformy Azure wykonywanych przy użyciu protokołu REST plików pliki są adresy, przy użyciu następującego formatu adresu URL:

    ```
    https://<storage account>.file.core.windows.net/<share>/<directory>/directories>/<file>
    ```

## <a name="data-access-method"></a>Metoda dostępu do danych
Usługa pliki Azure oferuje dwa, metody, że dostęp do danych można użyć oddzielnie lub w połączeniu ze sobą, dostępu do danych wbudowane, wygodną:

1. **Bezpośredni dostęp do chmury**: Wszystkie udziały plików platformy Azure mogą być instalowane przez [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md), i/lub [Linux](storage-how-to-use-files-linux.md) ze standardowego protokołu bloku komunikatów serwera (SMB) lub za pośrednictwem interfejsu API REST plików. Za pomocą protokołu SMB operacji odczytu i zapisu do plików w udziale są wykonywane bezpośrednio na udział plików na platformie Azure. Aby zainstalować przez maszynę Wirtualną na platformie Azure, klient SMB w systemie operacyjnym musi obsługiwać co najmniej protokół SMB 2.1. Aby zainstalować lokalnie, takich jak na stacji roboczej użytkownika, klient SMB, obsługiwane przez stacji roboczej musi obsługiwać co najmniej protokół SMB 3.0 (przy użyciu szyfrowania). Oprócz protokołu SMB nowych aplikacji lub usług może uzyskać bezpośredniego dostępu udziału plików za pośrednictwem interfejsu REST pliku, który udostępnia interfejs programowania proste i skalowalnych aplikacji do tworzenia oprogramowania.
2. **Azure File Sync**: Za pomocą usługi Azure File Sync mogą być replikowane udziałów na serwerach Windows w środowisku lokalnym lub na platformie Azure. Użytkownikom dostępu do udziału plików, za pośrednictwem systemu Windows Server, takich jak za pomocą protokołu SMB lub NFS w udziale. Jest to przydatne w scenariuszach, w których dane będą dostępne, a modyfikacji daleko od centrum danych platformy Azure, takie jak w przypadku oddziału firmy. Dane mogą być replikowane między wieloma punkty końcowe serwera systemu Windows, takich jak między wieloma biur oddziałów. Na koniec dane mogą warstwowe do usługi Azure Files, taki sposób, że wszystkie dane są nadal dostępne za pośrednictwem serwera, ale serwer nie ma pełną kopię danych. Przeciwnie dane są bezproblemowo wywoływane po otwarciu przez użytkowników.

W poniższej tabeli przedstawiono, jak usługi użytkownikom i aplikacjom dostęp do udziału plików platformy Azure:

| | Bezpośredni dostęp do chmury | Azure File Sync |
|------------------------|------------|-----------------|
| Jakie protokoły trzeba użyć? | Usługa pliki systemu Azure obsługuje protokół SMB 2.1, protokołu SMB 3.0 i interfejsu API REST plików. | Dostęp do udziału plików platformy Azure za pomocą dowolnego obsługiwanego protokołu w systemie Windows Server (SMB, systemu plików NFS, FTPS itp.) |  
| Gdzie są uruchomione Twoje obciążenie? | **Na platformie Azure**: Usługa Azure Files oferuje bezpośredni dostęp do danych. | **W środowisku lokalnym za pomocą wolną sieć**: Klienci Windows, Linux i macOS można zainstalować udział plików Windows lokalnego jako szybkie pamięć podręczną udziału plików platformy Azure. |
| Jaki poziom list kontroli dostępu jest wymagana? | Poziom udziału i pliku. | Poziom udostępniania, plików i użytkowników. |

## <a name="data-security"></a>Bezpieczeństwo danych
Usługa pliki systemu Azure oferuje kilka opcji wbudowanych zapewniających bezpieczeństwo danych:

* Obsługa szyfrowania w obu tych protokołów przez transmisji: Szyfrowanie protokołu SMB 3.0 i REST pliku przy użyciu protokołu HTTPS. Domyślnie: 
    * Klienci, którzy obsługują szyfrowanie protokołu SMB 3.0 wysyłać i odbierać dane za pośrednictwem szyfrowanego kanału.
    * Klientów, które nie obsługują przy użyciu szyfrowania protokołu SMB 3.0 mogą komunikować się wewnątrz od centrum danych za pośrednictwem protokołu SMB 2.1 lub SMB 3.0 bez szyfrowania. Klienci SMB nie są dozwolone do komunikacji między centrum danych za pośrednictwem protokołu SMB 2.1 lub SMB 3.0 bez szyfrowania.
    * Klienci mogą komunikować się za pośrednictwem REST pliku przy użyciu protokołu HTTP lub HTTPS.
* Szyfrowanie w spoczynku ([szyfrowanie usługi Azure Storage](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)): Szyfrowanie usługi Storage (SSE) jest włączona dla wszystkich kont magazynu. Magazynowanych danych jest szyfrowany za pomocą kluczy w pełni zarządzane. Szyfrowanie w spoczynku zwiększyć koszty magazynowania lub nie obniżenie wydajności. 
* Opcjonalne wymaganie zaszyfrowanych danych podczas przesyłania: po wybraniu usługi Azure Files odrzuca dostęp do danych za pośrednictwem kanałów niezaszyfrowane. W szczególności są dozwolone tylko protokołu HTTPS i SMB 3.0 za pomocą szyfrowania połączenia. 

    > [!Important]  
    > Wymaganie bezpiecznego transferu danych spowoduje, że nie może komunikować się z protokołem SMB 3.0 z szyfrowania starsze klienty SMB nie powiedzie się. Aby uzyskać więcej informacji, zobacz [zainstalować na Windows](storage-how-to-use-files-windows.md), [instalowanie w systemie Linux](storage-how-to-use-files-linux.md), i [instalacji w systemie macOS](storage-how-to-use-files-mac.md).

Maksymalne bezpieczeństwo zdecydowanie zalecamy zawsze włączenie zarówno szyfrowania podczas spoczynku oraz włączania szyfrowania danych podczas przesyłania, ilekroć są przy użyciu nowoczesnych klientów oraz dostęp do danych. Na przykład jeśli musisz zainstalować udział na Windows Server 2008 R2 maszyny Wirtualnej, który obsługuje tylko protokół SMB 2.1, musisz zezwolić na niezaszyfrowane ruch do swojego konta magazynu, ponieważ protokół SMB 2.1 nie obsługuje szyfrowania.

Jeśli używasz usługi Azure File Sync na dostęp do udziału plików platformy Azure, zawsze użyjemy HTTPS i SMB 3.0 za pomocą szyfrowania można zsynchronizować dane serwerów Windows, niezależnie od tego, czy Wymaganie szyfrowania danych magazynowanych.

## <a name="file-share-performance-tiers"></a>Warstwy wydajności udziału plików
Usługa Azure Files obsługuje dwie warstwy wydajności: standardowa i premium.

* **Udziały plików standardowych** są wspierane przez obrotowych dysków twardych (HDD), które zapewniają niezawodność, wydajność dla obciążeń we/wy, które są mniej podatne na zmiany wydajności, takich jak udziały plików ogólnego przeznaczenia i środowiska deweloperskie i testowe. Udziały plików standardowa są dostępne tylko w modelu rozliczeń zgodnie z rzeczywistym użyciem.
* **Udziały plików w warstwie Premium (wersja zapoznawcza)** są wspierane przez dyski półprzewodnikowe (SSD), które umożliwiają spójne, wysokiej wydajności i niskich opóźnieniach, w milisekundach oznaczona jedną cyfrą dla większości operacji We/Wy, dla większości obciążeń intensywnie korzystających z operacji We/Wy. To sprawia, że ich odpowiednie dla różnych obciążeń, takich jak bazy danych, hostowanie witryn sieci web, środowisk deweloperskich itp. Udziały plików w warstwie Premium są dostępne tylko w elastycznie model rozliczeń.

### <a name="provisioned-shares"></a>Elastycznie udziałów
Udziały plików w warstwie Premium są aprowizowane w oparciu stały współczynnik operacji We/Wy/GiB/przepływność. Dla każdego GiB zainicjowano obsługę administracyjną udziału pojawi się w jednej operacji We/Wy i 0,1 przepływność MiB/s do maksymalnego limitu na jedną akcję. Minimalny dozwolony aprowizacji to 100 GiB z minimum operacji We/Wy/przepływności. Rozmiar udziału w dowolnym momencie można zwiększyć i zmniejszyć w dowolnym momencie, ale można zmniejszyć co 24 godziny od czasu ostatniego wzrost.

Na optymalne rozwiązanie wszystkie udziały serii można maksymalnie trzy operacje We/Wy za GiB aprowizowanego magazynu przez 60 minut lub dłużej w zależności od wielkości udziału. Nowe udziały Rozpocznij od środków pełną serii, w oparciu zaprowizowaną pojemnością.

| Zaprowizowaną pojemnością | 100 GiB | 500 GiB | 1 TiB | 5 TiB | 
|----------------------|---------|---------|-------|-------|
| Baseline IOPS | 100 | 500 | 1,024 | 5,120 | 
| Limit serii | 300 | 1,500 | 3072 | 15,360 | 
| Przepływność | 110 MiB/s | 150 MiB/s | 202 MiB/s | 612 MiB/s |

## <a name="file-share-redundancy"></a>Nadmiarowość udziału plików
Usługa Azure Files obsługuje trzy opcje nadmiarowości danych: Magazyn lokalnie nadmiarowy (LRS), Magazyn strefowo nadmiarowy (ZRS) i Magazyn geograficznie nadmiarowy (GRS). W poniższych sekcjach opisano różnice między Opcje nadmiarowości inną:

### <a name="locally-redundant-storage"></a>Magazyn lokalnie nadmiarowy
[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

### <a name="zone-redundant-storage"></a>Magazyn strefowo nadmiarowy
[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-ZRS.md)]

### <a name="geo-redundant-storage"></a>Magazyn geograficznie nadmiarowy
[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-GRS.md)]

## <a name="data-growth-pattern"></a>Wzorzec wzrostu ilości danych
Obecnie maksymalny rozmiar udziału plików platformy Azure jest wynosi 5 TiB. Ze względu na to bieżące ograniczenie podczas wdrażania udziału plików platformy Azure należy wziąć pod uwagę przewidywanego wzrostu ilości danych. 

Istnieje możliwość synchronizowania wielu plików platformy Azure udostępnia do jednego serwera plików Windows za pomocą usługi Azure File Sync. Dzięki temu można zapewnić, że udziały plików starsze, duże, że może mieć w środowisku lokalnym, może być wprowadzana do usługi Azure File Sync. Aby uzyskać więcej informacji, zobacz [Planowanie wdrażania usługi Azure pliku synchronizacji](storage-files-planning.md).

## <a name="data-transfer-method"></a>Metoda transferu danych
Istnieje wiele opcji łatwy do zbiorczego transferu danych z istniejącego pliku udostępniania, takie jak udział plików lokalnych do usługi Azure Files. Kilka popularnych usług obejmują (niepełna lista):

* **Azure File Sync**: Jako część pierwsza synchronizacja między udziału plików platformy Azure ("punktu końcowego w chmurze") i Windows katalogu przestrzeni nazw ("punkt końcowy serwera") usługi Azure File Sync będą replikowane do usługi Azure Files wszystkich danych z istniejącego udziału plików.
* **[Usługa Azure Import/Export](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)**: Usługa Azure Import/Export umożliwia bezpieczne przesyłanie dużych ilości danych do udziału plików platformy Azure przez wysyłanie dysków twardych do centrum danych platformy Azure. 
* **[Robocopy](https://technet.microsoft.com/library/cc733145.aspx)**: ROBOCOPY jest narzędziem dobrze znanych kopiowania, które jest dostarczany z programem Windows i Windows Server. ROBOCOPY mogą służyć do przenoszenia danych do usługi pliki Azure przez zainstalowanie udziału plików lokalnie, a następnie użycie lokalizacji zainstalowanego jako miejsce docelowe polecenia Robocopy.
* **[AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#upload-files-to-an-azure-file-share)**: AzCopy to narzędzie wiersza polecenia przeznaczone do kopiowania danych do i z usługi Azure Files, a także usługi Azure Blob storage przy użyciu prostych poleceń z optymalną wydajnością. Narzędzie AzCopy jest dostępna dla Windows i Linux.

## <a name="next-steps"></a>Kolejne kroki
* [Planowanie wdrażania usługi synchronizacji plików platformy Azure](storage-sync-files-planning.md)
* [Wdrażanie usługi Azure Files](storage-files-deployment-guide.md)
* [Wdrażanie usługi Azure File Sync](storage-sync-files-deployment-guide.md)
