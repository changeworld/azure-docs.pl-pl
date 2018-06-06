---
title: Planowanie wdrożenia usługi pliki Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, co należy wziąć pod uwagę podczas planowania wdrożenia usługi pliki Azure.
services: storage
author: wmgries
manager: aungoo
ms.service: storage
ms.topic: article
ms.date: 03/06/2018
ms.author: wgries
ms.openlocfilehash: 86c577c23c97939a31551734dab9d45837011ffa
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34737398"
---
# <a name="planning-for-an-azure-files-deployment"></a>Planowanie wdrażania usługi Pliki Azure
[Usługa pliki Azure](storage-files-introduction.md) oferuje pełni zarządzanych udziałów plików w chmurze, które są dostępne przy użyciu standardowego protokołu SMB. Ponieważ pliki Azure jest pełna, jego wdrożeniem w środowisku produkcyjnym scenariuszach jest znacznie prostsze niż wdrażania i zarządzania nimi serwera plików lub urządzenie NAS. W tym artykule opisano tematy wziąć pod uwagę podczas wdrażania na udział plików na platformę Azure do użytku produkcyjnego w ramach danej organizacji.

## <a name="management-concepts"></a>Pojęcia dotyczące zarządzania
 Na poniższym diagramie przedstawiono konstrukcje zarządzania plików Azure:

![Struktura plików](./media/storage-files-introduction/files-concepts.png)

* **Konto magazynu**: cały dostęp do usługi Azure Storage odbywa się przez konto magazynu. Aby uzyskać szczegółowe informacje na temat pojemności konta magazynu, zobacz [Cele dotyczące skalowalności i wydajności](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* **Udział**: udział usługi File Storage jest udziałem plików SMB na platformie Azure. Wszystkie pliki i katalogi muszą być tworzone w udziale nadrzędnym. Konto może zawierać nieograniczoną liczbę udziałów, a udział może przechowywać nieograniczoną liczbę plików, maksymalnie 5 TiB całkowita pojemność udziału plików.

* **Katalog**: opcjonalna hierarchia katalogów.

* **Plik**: plik w udziale. Plik może mieć maksymalnie 1 TiB rozmiar.

* **Format adresu URL**: dla żądań kierowanych do udziału plików na platformę Azure z protokołem REST pliku, plików mają następujący format adresu URL:

    ```
    https://<storage account>.file.core.windows.net/<share>/<directory>/directories>/<file>
    ```

## <a name="data-access-method"></a>Metoda dostępu do danych
Azure pliki ofert dwóch metod, której można oddzielnie lub w połączeniu ze sobą, aby uzyskać dostęp do danych dostępu do danych wbudowanych i wygodny:

1. **Bezpośredni dostęp do chmury**: Any Azure udział plików może być instalowany przez [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md), i/lub [Linux](storage-how-to-use-files-linux.md) z branży standardowego bloku komunikatów serwera (SMB) Protokół lub za pośrednictwem interfejsu API REST pliku. Z protokołem SMB Odczyt i zapis do plików w udziale są dokonywane bezpośrednio na udział plików na platformie Azure. Aby zainstalować przez maszynę Wirtualną na platformie Azure, klient protokołu SMB w systemie operacyjnym musi obsługiwać co najmniej SMB 2.1. Do zainstalowania lokalnie, takie jak na stacji roboczej użytkownika, klient protokołu SMB, obsługiwane przez stacji roboczej musi obsługiwać co najmniej SMB 3.0 (przy użyciu szyfrowania). Oprócz protokołu SMB nowych aplikacji lub usług może bezpośrednio dostęp do udziału plików za pośrednictwem interfejsu REST pliku, która zapewnia interfejs programowania aplikacji łatwego i skalowalnego do rozwoju oprogramowania.
2. **Synchronizacja programu Azure pliku** (wersja zapoznawcza): Azure synchronizacji plików, udziały mogą być replikowane do serwerów z systemem Windows lokalnego lub na platformie Azure. Użytkownicy będą dostęp do udziału plików, za pośrednictwem systemu Windows Server, takich jak za pośrednictwem protokołu SMB lub NFS w udziale. Jest to przydatne w scenariuszach, w których danych można uzyskać dostępu do i zmodyfikować daleko od centrum danych Azure, takich jak w przypadku oddziału firmy. Dane mogą być replikowane między wiele systemu Windows Server punktów końcowych, takich jak między wielu oddziałów. Na koniec danych może należeć do warstwy do usługi pliki Azure, tak, aby wszystkie dane są nadal dostępne za pośrednictwem serwera, ale serwer nie ma pełną kopię danych. Zamiast dane są bezproblemowo wywoływane po otwarciu przez użytkownika.

W poniższej tabeli przedstawiono, jak użytkowników i aplikacji dostęp do udziału plików platformy Azure:

| | Dostęp bezpośredni chmury | Usługa Azure File Sync |
|------------------------|------------|-----------------|
| Jakie protokoły musisz użyć? | Usługa pliki Azure obsługuje protokół SMB 2.1, SMB 3.0 i interfejsu API REST plików. | Dostęp do udziału plików platformy Azure za pomocą dowolnego protokołu obsługiwanych w systemie Windows Server (protokół SMB, systemu plików NFS, FTPS itp.) |  
| Gdy używasz Twoje obciążenie? | **Na platformie Azure**: plików Azure oferuje bezpośredni dostęp do danych. | **Lokalnej z wolną sieć**: klientów systemu Windows, Linux i macOS można zainstalować lokalnie lokalnego udziału plików systemu Windows jako szybkiego pamięci podręcznej udziału plików na platformę Azure. |
| Jaki poziom kontroli dostępu potrzebujesz? | Poziom udziału i plików. | Poziom udziału plików i użytkowników. |

## <a name="data-security"></a>Bezpieczeństwo danych
Usługa pliki Azure zawiera kilka wbudowanych opcji równoczesnym zapewnieniu bezpieczeństwa danych:

* Obsługa szyfrowania w obu tych protokołów w locie: szyfrowanie protokołu SMB 3.0 i REST pliku za pośrednictwem protokołu HTTPS. Domyślnie: 
    * Klientów, które obsługuje szyfrowanie protokołu SMB 3.0 wysyłać i odbierać dane za pośrednictwem kanału szyfrowanego.
    * Klienci, którzy nie obsługują protokołu SMB 3.0 mogą komunikować się centrum danych wewnątrz przez protokół SMB 2.1 lub SMB 3.0 bez szyfrowania. Należy pamiętać, że klienci nie są dozwolone do komunikacji między centrum danych za pośrednictwem protokołu SMB 2.1 lub SMB 3.0 bez szyfrowania.
    * Klienci mogą komunikować się za pośrednictwem pliku REST z protokołu HTTP lub HTTPS.
* Szyfrowanie na rest ([szyfrowanie usługi Magazyn Azure](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)): szyfrowanie usługi Magazyn (SSE) jest domyślnie włączona dla wszystkich kont magazynu. Dane na rest jest szyfrowana za pełni zarządzanych kluczy. Szyfrowanie na rest zwiększenie kosztów magazynowania lub nie zmniejszyć wydajność. 
* Opcjonalne wymagania zaszyfrowanych danych podczas przesyłania: po wybraniu plików Azure odrzuca dostępu do danych za pośrednictwem nieszyfrowanego kanałów. W szczególności są dozwolone tylko protokołu HTTPS i SMB 3.0 z szyfrowania połączenia. 

    > [!Important]  
    > Wymaganie bezpiecznego transferu danych spowoduje, że nie może komunikować się z protokołem SMB 3.0 z szyfrowania starsze klienty SMB się niepowodzeniem. Zobacz [zainstalować w systemie Windows](storage-how-to-use-files-windows.md), [zainstalować w systemie Linux](storage-how-to-use-files-linux.md), [zainstalować na macOS](storage-how-to-use-files-mac.md) Aby uzyskać więcej informacji.

Dla zapewnienia maksymalnego poziomu bezpieczeństwa zdecydowanie zaleca się zawsze Włączanie zarówno szyfrowanie podczas spoczynku i włączania szyfrowania danych podczas przesyłania, gdy używasz nowoczesnych klientów dostępu do danych. Na przykład jeśli musisz zainstalować udziały na systemu Windows Server 2008 R2 maszyny Wirtualnej, który obsługuje tylko protokół SMB 2.1, musisz zezwolić nieszyfrowanego ruchu do konta magazynu, ponieważ protokół SMB 2.1 nie obsługuje szyfrowania.

Jeśli używasz synchronizacji plików Azure na dostęp do udziału plików platformy Azure, zawsze używamy HTTPS i SMB 3.0 z szyfrowaniem do synchronizacji danych do serwerów systemu Windows, niezależnie od tego, czy Wymaganie szyfrowania danych na rest.

## <a name="data-redundancy"></a>Nadmiarowość danych
Usługa pliki Azure obsługuje trzy opcje nadmiarowości danych: Magazyn lokalnie nadmiarowy (LRS), strefy magazyn geograficznie nadmiarowy (ZRS) i magazynu geograficznie nadmiarowego (GRS). W poniższych sekcjach opisano różnice między nadmiarowość różne opcje:

### <a name="locally-redundant-storage"></a>Magazyn lokalnie nadmiarowy
[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

### <a name="zone-redundant-storage"></a>Strefa magazynu geograficznie nadmiarowego
[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-ZRS.md)]

### <a name="geo-redundant-storage"></a>Magazyn geograficznie nadmiarowy
[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-GRS.md)]

## <a name="data-growth-pattern"></a>Wzorzec wzrostu danych
Obecnie maksymalny rozmiar udziału plików na platformę Azure jest TiB 5, w tym udziale migawki. Ze względu na to aktualne ograniczenie wdrażając udziału plików na platformę Azure należy wziąć pod uwagę wzrost oczekiwane dane. Należy pamiętać, że konto usługi Azure Storage można przechowywać wiele udziałów w sumie 500 TiB przechowywane we wszystkich udziałów.

Istnieje możliwość synchronizacji wielu plików na platformę Azure udostępnia do jednego serwera plików systemu Windows za pomocą synchronizacji plików Azure. Dzięki temu można zapewnić, że udziały plików starszych, bardzo dużych wiążące lokalnymi mogą być wprowadzane do synchronizacji usługi Azure pliku. Zobacz [planowania wdrożenia synchronizacji plików Azure](storage-files-planning.md) Aby uzyskać więcej informacji.

## <a name="data-transfer-method"></a>Transfer danych — metoda
Istnieje wiele opcji łatwy do zbiorczego transferu danych z istniejącego pliku udostępnić, takie jak udział pliku lokalnego do usługi pliki Azure. Kilka najpowszechniejsze są (niepełna lista):

* **Synchronizacja programu Azure pliku**: W ramach pierwszej synchronizacji między udział plików na platformę Azure ("punktu końcowego w chmurze") i nazw katalogu systemu Windows ("punkt końcowy serwera"), synchronizacji plików Azure zostaną zreplikowane wszystkie dane z istniejącego udziału plików do usługi pliki Azure.
* **[Import/Eksport Azure](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)**: Usługa Import/Eksport Azure umożliwia bezpieczne transfer dużych ilości danych do udziału plików na platformę Azure poprzez wysyłanie dysków twardych do centrum danych Azure. 
* **[ROBOCOPY](https://technet.microsoft.com/library/cc733145.aspx)**: Robocopy jest narzędziem dobrze znanych kopiowania jest dostarczana z systemem Windows i Windows Server. ROBOCOPY może służyć do przesyłania danych do usługi pliki Azure instalowanie udziału plików lokalnie, a następnie używając lokalizacji zainstalowanego jako miejsce docelowe polecenia Robocopy.
* **[Narzędzie AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#upload-files-to-an-azure-file-share)**: narzędzie AzCopy to narzędzie wiersza polecenia przeznaczone do kopiowania danych z magazynu obiektów Blob platformy Azure, a także pliki Azure przy użyciu prostych poleceń z optymalną wydajnością. Narzędzie AzCopy jest dostępna dla systemów Windows i Linux.

## <a name="next-steps"></a>Kolejne kroki
* [Planowanie wdrożenia synchronizacji plików na platformę Azure](storage-sync-files-planning.md)
* [Wdrażanie plików platformy Azure](storage-files-deployment-guide.md)
* [Wdrażanie synchronizacji plików na platformę Azure](storage-sync-files-deployment-guide.md)