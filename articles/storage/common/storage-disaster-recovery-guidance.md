---
title: Awaryjnego odzyskiwania i przechowywania konta pracy awaryjnej (wersja zapoznawcza) — usługi Azure Storage
description: Usługa Azure Storage obsługuje konta pracy awaryjnej (wersja zapoznawcza) dla kont usługi storage geograficznie nadmiarowy. Z trybem failover konta można zainicjować procesu pracy awaryjnej dla konta magazynu, jeśli podstawowy punkt końcowy stanie się niedostępny.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/25/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: f0963e7f558de7b591576a49a74750d6697d7127
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58486068"
---
# <a name="disaster-recovery-and-storage-account-failover-preview-in-azure-storage"></a>Awaryjnego odzyskiwania i przechowywania konta pracy awaryjnej (wersja zapoznawcza) w usłudze Azure Storage

Firma Microsoft dokłada starań upewnić się, że usługi platformy Azure są zawsze dostępne. Jednak mogą wystąpić przerwy w działaniu usługi nieplanowane. Jeśli aplikacja wymaga odporności, firma Microsoft zaleca korzystanie z magazynu geograficznie nadmiarowego magazynu tak, aby Twoje dane są replikowane w drugim regionie. Ponadto klienci powinien mieć planu obsługi awarii regionalnej usługi odzyskiwania po awarii. Ważną częścią planu odzyskiwania po awarii jest przygotowywana do trybu failover do pomocniczego punktu końcowego w przypadku, gdy podstawowy punkt końcowy stanie się niedostępny. 

Usługa Azure Storage obsługuje konta pracy awaryjnej (wersja zapoznawcza) dla kont usługi storage geograficznie nadmiarowy. Z trybem failover konta można zainicjować procesu pracy awaryjnej dla konta magazynu, jeśli podstawowy punkt końcowy stanie się niedostępny. Przełączenie w tryb failover aktualizuje pomocniczego punktu końcowego, aby stać się podstawowego punktu końcowego konta magazynu. Po zakończeniu pracy w trybie failover klientów można rozpocząć zapisywanie do nowego podstawowego punktu końcowego.

W tym artykule opisano pojęcia i proces związane z trybem failover konta i w tym artykule omówiono sposób przygotowania konta magazynu do odzyskiwania za pomocą minimalnej liczbie klientów. Aby dowiedzieć się, jak zainicjować trybu failover konta, w witrynie Azure portal lub programu PowerShell, zobacz [zainicjowania trybu failover konta (wersja zapoznawcza)](storage-initiate-account-failover.md).

## <a name="choose-the-right-redundancy-option"></a>Wybierz opcję nadmiarowości w prawo

Wszystkie konta magazynu są replikowane w celu zapewnienia nadmiarowości. Wybranej opcji nadmiarowości na Twoje konto jest zależna od stopnia odporności, których potrzebujesz. W celu ochrony przed regionalnej awarii należy wybrać magazyn geograficznie nadmiarowy z lub bez możliwości dostępu do odczytu z regionu pomocniczego:  

**Magazyn geograficznie nadmiarowy (GRS)** replikuje dane asynchronicznie w dwóch regionach geograficznych, które są co najmniej kilkuset mil od siebie. Jeśli w regionie podstawowym wystąpi awaria, regionu pomocniczego służy jako nadmiarowe źródło danych. Możesz zainicjować trybu failover do przekształcania pomocniczego punktu końcowego podstawowego punktu końcowego.

**Dostęp do odczytu magazynu geograficznie nadmiarowego (RA-GRS)** zapewnia dodatkowe korzyści wynikające z dostępu do odczytu do pomocniczego punktu końcowego magazynu geograficznie nadmiarowego. Jeśli wystąpi awaria podstawowego punktu końcowego, aplikacje skonfigurowane w przypadku usługi RA-GRS i zaprojektowane w celu zapewnienia wysokiej dostępności można nadal odczytywać z pomocniczego punktu końcowego. Firma Microsoft zaleca RA-GRS pod kątem maksymalnej odporności dla aplikacji.

Inne opcje nadmiarowości usługi Azure Storage to magazyn strefowo nadmiarowy (ZRS), który replikuje dane w różnych strefach dostępności w jednym regionie i Magazyn lokalnie nadmiarowy (LRS), który replikuje dane w jednym centrum danych w jednym regionie. Skonfigurowanie konta magazynu ZRS lub LRS można przekonwertować to konto do użycia GRS lub RA-GRS. Konfigurowanie konta na potrzeby magazynu geograficznie nadmiarowego spowoduje naliczenie dodatkowych kosztów. Aby uzyskać więcej informacji, zobacz [replikacja usługi Azure Storage](storage-redundancy.md).

> [!WARNING]
> Magazyn geograficznie nadmiarowy niesie ze sobą ryzyko utraty danych. Dane są replikowane do regionu pomocniczego asynchronicznie, co oznacza, że występuje opóźnienie między kiedy dane zapisywane w regionie głównym są zapisywane do regionu pomocniczego. W przypadku awarii zapisu operacji do podstawowego punktu końcowego, które nie zostały jeszcze zreplikowane do pomocniczego punktu końcowego zostaną utracone. 

## <a name="design-for-high-availability"></a>Projektowanie pod kątem wysokiej dostępności

Należy tak zaprojektować aplikację wysokiej dostępności od samego początku. Zapoznaj się z następujących zasobów platformy Azure, aby uzyskać wskazówki dotyczące projektowania aplikacji i planowania odzyskiwania po awarii:

* [Projektowanie aplikacji odpornych na błędy dla platformy Azure](https://docs.microsoft.com/azure/architecture/resiliency/): Przegląd kluczowych założeń zapewnianie wysokiej dostępności aplikacji na platformie Azure.
* [Lista kontrolna dotycząca dostępności](https://docs.microsoft.com/azure/architecture/checklist/availability): Lista kontrolna w celu sprawdzenia, czy aplikacja wykonuje najlepszych rozwiązań projektowania w celu zapewnienia wysokiej dostępności.
* [Projektowanie wysoko dostępnych aplikacji przy użyciu RA-GRS](storage-designing-ha-apps-with-ragrs.md): Wskazówki dotyczące tworzenia aplikacji, aby móc korzystać z RA-GRS projektowania.
* [Samouczek: Tworzenie aplikacji o wysokiej dostępności z magazynu obiektów Blob](../blobs/storage-create-geo-redundant-storage.md): Samouczek, w którym pokazano, jak utworzyć aplikację o wysokiej dostępności, która automatycznie przełącza między punktami końcowymi jako błędy i odzyskiwania są symulowane. 

Ponadto należy przestrzegać tych najlepszych rozwiązań dotyczących obsługi wysokiej dostępności dla danych usługi Azure Storage:

* **Dyski:** Użyj [kopia zapasowa Azure](https://azure.microsoft.com/services/backup/) do tworzenia kopii zapasowych dysków maszyny Wirtualnej, z usługi Azure virtual machines. Ponadto należy wziąć pod uwagę przy użyciu [usługi Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) do ochrony maszyn wirtualnych w przypadku regionalnej awarii.
* **Obiekty BLOB typu Block:** Włącz [usuwania nietrwałego](../blobs/storage-blob-soft-delete.md) do ochrony przed usuwania na poziomie obiektu i zastępuje lub skopiuj blokowych obiektów blob do innego konta magazynu w innym regionie przy użyciu [AzCopy](storage-use-azcopy.md), [programu Azure PowerShell ](storage-powershell-guide-full.md), lub [Biblioteka przenoszenia danych usługi Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/).
* **Pliki:** Użyj [AzCopy](storage-use-azcopy.md) lub [programu Azure PowerShell](storage-powershell-guide-full.md) Aby skopiować pliki do innego konta magazynu w innym regionie.
* **Tabele:** użyj [AzCopy](storage-use-azcopy.md) do eksportowania danych z tabeli do innego konta magazynu w innym regionie.

## <a name="track-outages"></a>Śledź awarie

Klienci mogą subskrybować [pulpit nawigacyjny kondycji usługi platformy Azure](https://azure.microsoft.com/status/) do śledzenia kondycję i stan usługi Azure Storage i innymi usługami platformy Azure.

Firma Microsoft zaleca również, że projektujesz aplikację, aby przygotować się do możliwości błędy zapisu. Aplikację należy ujawnić błędy zapisu w taki sposób, że ostrzega o możliwości wystąpienia awarii w regionie podstawowym.

## <a name="understand-the-account-failover-process"></a>Omówienie procesu pracy awaryjnej konta

Konto usługi zarządzane przez klienta trybu failover (wersja zapoznawcza) umożliwia awaryjnie swoje całe konto magazynu do regionu pomocniczego, jeśli podstawowy stanie się niedostępna z jakiegokolwiek powodu. Jeśli wymusisz przejścia w tryb failover do regionu pomocniczego, klientów można rozpocząć zapisywanie danych do pomocniczego punktu końcowego, po zakończeniu pracy w trybie failover. Przełączenie w tryb failover trwa zwykle około godziny.

### <a name="how-an-account-failover-works"></a>Jak działa tryb failover konta

W normalnych warunkach klient zapisuje dane do konta usługi Azure Storage w regionie podstawowym, a dane są replikowane asynchronicznie w regionie pomocniczym. Na poniższej ilustracji przedstawiono scenariusz, gdy region podstawowy jest dostępny:

![Klienci zapisu danych do konta magazynu w regionie podstawowym](media/storage-disaster-recovery-guidance/primary-available.png)

Jeśli podstawowy punkt końcowy stanie się niedostępna z jakiegokolwiek powodu, klient nie będzie już możliwość zapisywania do konta magazynu. Na poniższej ilustracji przedstawiono scenariusz, w którym podstawowy stał się niedostępny, ale odzyskiwania nie miało jeszcze miejsca:

![Podstawowy jest niedostępny, dzięki czemu klienci nie mogą zapisywać danych](media/storage-disaster-recovery-guidance/primary-unavailable-before-failover.png)

Klient inicjuje konta tryb failover do pomocniczego punktu końcowego. Proces w tryb failover aktualizuje wpis DNS udostępniony przez usługę Azure Storage tak, aby pomocniczego punktu końcowego staje się nowego podstawowego punktu końcowego konta magazynu, jak pokazano na poniższej ilustracji:

![Klienta przełączenia w tryb failover konta pomocniczego punktu końcowego](media/storage-disaster-recovery-guidance/failover-to-secondary.png)

Dostęp do zapisu zostanie przywrócony do kont GRS i RA-GRS został zaktualizowany wpis DNS i żądania są są kierowane do nowego podstawowego punktu końcowego. Istniejące punkty końcowe usługi storage dla obiektów blob, tabel, kolejek i plików pozostają niezmienione po przełączeniu w tryb failover.

> [!IMPORTANT]
> Po zakończeniu pracy w trybie failover na koncie magazynu jest skonfigurowane jako lokalnie nadmiarowy w nowego podstawowego punktu końcowego. Aby wznowić replikację na nowym serwerem pomocniczym, należy skonfigurować konto używane dla magazynu geograficznie nadmiarowego ponownie (RA-GRS lub GRS).
>
> Należy pamiętać, że konwersja konta z LRS na RA-GRS lub GRS generuje koszt. Ten koszt dotyczy Trwa aktualizowanie konta magazynu w nowym regionie podstawowym, po przejściu w tryb failover używane RA-GRS lub GRS.  

### <a name="anticipate-data-loss"></a>Przewidywanie utraty danych

> [!CAUTION]
> Tryb failover konta zazwyczaj polega na utratę danych. Jest ważne umożliwić poznanie skutków inicjowania trybu failover konta.  

Ponieważ dane są zapisywane asynchronicznie z regionu podstawowego do regionu pomocniczego, istnieje opóźnienie przed zapisu do regionu podstawowego są replikowane do regionu pomocniczego. Jeśli region podstawowy staje się niedostępny, najbardziej aktualną zapisów może nie jeszcze zostały zreplikowane do regionu pomocniczego.

Po przejściu w tryb failover możesz wymusić, wszystkie dane w regionie podstawowym zostaną utracone, zgodnie z regionu pomocniczego staje się nowym regionie podstawowym i konto magazynu jest skonfigurowane jako lokalnie nadmiarowe. Wszystkie dane, które już są replikowane do regionu pomocniczego jest obsługiwane w przypadku pracy w trybie failover. Jednak wszystkie dane zapisane do podstawowej, która nie ma również replikowane do regionu pomocniczego utracenie trwałe. 

**Czas ostatniej synchronizacji** właściwość wskazuje najbardziej czasu najnowszych danych z regionu podstawowego jest gwarantowane, zostały zapisane w regionie pomocniczym. Wszystkie dane zapisane przed czas ostatniej synchronizacji jest dostępna w lokacji dodatkowej, podczas danych zapisane po czas ostatniej synchronizacji nie zapisano do regionu pomocniczego i mogą zostać utracone. Ta właściwość umożliwia przestoju oszacować ilość utraconych danych, który może spowodować naliczenie inicjując konta trybu failover. 

Najlepszym rozwiązaniem jest projektowanie aplikacji tak, aby czas ostatniej synchronizacji można użyć do oceny, utratę oczekiwanych danych. Na przykład podczas logowania się wszystkie operacje zapisu, można porównać ostatni czas ostatniej operacji zapisu, a następnie zsynchronizować czas w celu sprawdzenia, zapisu, które nie zostały zsynchronizowane do regionu pomocniczego.

### <a name="use-caution-when-failing-back-to-the-original-primary"></a>Należy zachować ostrożność podczas powrót do oryginalnej podstawowej

Po przełączysz z serwera podstawowego do regionu pomocniczego, konto magazynu jest skonfigurowane jako lokalnie nadmiarowy w nowym regionie podstawowym. Ponownie skonfigurować konta nadmiarowości geograficznej, aktualizując go do korzystania ze GRS lub RA-GRS. Gdy konto jest skonfigurowane w celu zapewnienia nadmiarowości geograficznej ponownie po przejściu w tryb failover, nowy region podstawowy natychmiast rozpoczyna replikację danych do nowego regionu pomocniczego został podstawowe przed włączeniem oryginalnego trybu failover. Jednak może potrwać przez pewien czas, zanim dane istniejące w podstawowym jest w pełni replikowana na nowym serwerem pomocniczym.

Konto magazynu jest konfigurowana ponownie nadmiarowości geograficznej, po możliwa do zainicjowania innego trybu failover z nowej kopii głównej na nowym serwerem pomocniczym. W tym przypadku oryginalnego regionu podstawowego przed przełączenie w tryb failover staje się ponownie z regionu podstawowego, a jest skonfigurowany jako lokalnie nadmiarowy. Wszystkie dane w regionie podstawowym po przełączeniu w tryb failover (pomocniczy oryginalnego) są następnie utracone. Większość danych na koncie magazynu nie został zreplikowany na nowym serwerem pomocniczym przed możesz wykonać powrotu po awarii, może to spowodować obniżenie utratą najważniejszych danych. 

Aby uniknąć utraty danych głównych, należy sprawdzić wartość **czas ostatniej synchronizacji** właściwości przed powrotem. Porównania czas ostatniej synchronizacji ostatni czasach te dane zostały zapisane do nowej podstawowej, można obliczyć utratę oczekiwanych danych. 

## <a name="initiate-an-account-failover"></a>Inicjowanie trybu failover konta

Możesz zainicjować trybu failover konta, z witryny Azure portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejs API dostawcy zasobów usługi Azure Storage. Aby uzyskać więcej informacji na temat sposobu inicjowania przejścia w tryb failover, zobacz [zainicjowania trybu failover konta (wersja zapoznawcza)](storage-initiate-account-failover.md).

## <a name="about-the-preview"></a>O wersji zapoznawczej

konto w tryb failover jest dostępna w wersji zapoznawczej dla wszystkich klientów korzystających z GRS lub RA-GRS w przypadku wdrożeń usługi Azure Resource Manager. Ogólnego przeznaczenia w wersji 1, ogólnego przeznaczenia v2 i typy kont magazynu obiektów Blob są obsługiwane. tryb failover konta jest obecnie dostępny w tych regionach:

- Zachodnie stany USA 2
- Zachodnio-środkowe stany USA

Korzystania z wersji zapoznawczej jest przeznaczony tylko do użytku nieprodukcyjnych. Produkcyjne usługi poziomu usług (SLA) nie są obecnie dostępne.

### <a name="register-for-the-preview"></a>Zarejestruj się w celu korzystania z wersji zapoznawczej

Aby zarejestrować się do korzystania z wersji zapoznawczej, uruchom następujące polecenia w programie PowerShell. Upewnij się, że Zamień symbol zastępczy w nawiasach identyfikator subskrypcji:

```powershell
Connect-AzureRmAccount -SubscriptionId <subscription-id>
Register-AzureRmProviderFeature -FeatureName CustomerControlledFailover -ProviderNamespace Microsoft.Storage
```

Może upłynąć, 1 – 2 dni, aby uzyskać zatwierdzenie wersji zapoznawczej. Aby sprawdzić, proces rejestracji został zatwierdzony, uruchom następujące polecenie:

```powershell
Get-AzureRmProviderFeature -FeatureName CustomerControlledFailover -ProviderNamespace Microsoft.Storage
```

### <a name="additional-considerations"></a>Dodatkowe zagadnienia 

Przejrzyj dodatkowe zagadnienia, opisane w tej sekcji, aby zrozumieć, jak aplikacje i usługi może mieć wpływ na po wymuszenie przejścia w tryb failover w okresie zapoznawczym.

#### <a name="azure-virtual-machines"></a>Maszyny wirtualne platformy Azure

Maszyn wirtualnych (VM) pracy awaryjnej w ramach konta przejścia w tryb failover. Jeśli region podstawowy staje się niedostępny, a przejścia w tryb failover do regionu pomocniczego, a następnie konieczne będzie ponowne utworzenie maszyn wirtualnych po przejściu w tryb failover. 

#### <a name="azure-unmanaged-disks"></a>Dyski niezarządzane platformy Azure

Firma Microsoft zaleca, konwertowanie dysków niezarządzanych do dysków zarządzanych. Jednak jeśli potrzebujesz konta, które zawiera niezarządzane dyski dołączone do maszyn wirtualnych platformy Azure w tryb failover, konieczne będzie zamykania maszyny Wirtualnej przed przejściem w tryb failover.

Niezarządzane dyski są przechowywane jako stronicowe obiekty BLOB w usłudze Azure Storage. Gdy maszyna wirtualna jest uruchomiona na platformie Azure, są dzierżawione żadnych dysków niezarządzanych, dołączony do maszyny Wirtualnej. Tryb failover konta nie może kontynuować działania, gdy ma dzierżawę obiektu blob. Aby wykonać przełączenie w tryb failover, wykonaj następujące kroki:

1. Przed rozpoczęciem należy zanotować nazwy wszelkich dysków niezarządzanych, ich numery jednostek logicznych (LUN) i maszyny Wirtualnej, do której są dołączone. Ten sposób ułatwi ponownie dołączyć dyski po przełączeniu w tryb failover. 
2. Zamknij maszynę Wirtualną.
3. Usuń maszynę Wirtualną, ale przechowywać pliki wirtualnego dysku twardego dla dysków niezarządzanych. Należy pamiętać, czasu, jaką są usuwane maszyny Wirtualnej.
4. Poczekaj, aż **czas ostatniej synchronizacji** został zaktualizowany i jest późniejsza niż godzina, na którym są usuwane maszyny Wirtualnej. Ten krok jest ważny, ponieważ jeśli pomocniczy punkt końcowy nie został w pełni zaktualizowany przy użyciu plików wirtualnego dysku twardego po przejściu do trybu failover, następnie maszyna wirtualna może nie działać poprawnie w nowym regionie podstawowym.
5. Zainicjuj tryb failover konta.
6. Poczekaj, aż konta przełączenie w tryb failover została ukończona, a w regionie pomocniczym stał się nowego regionu podstawowego.
7. Tworzenie maszyny Wirtualnej w nowym regionie podstawowym, a następnie ponownie Dołącz wirtualne dyski twarde.
8. Uruchom nową maszynę Wirtualną.

Należy pamiętać, że wszystkie dane przechowywane w dysku tymczasowym zostaną utracone, gdy maszyna wirtualna zostanie zamknięta.

### <a name="unsupported-features-or-services"></a>Nieobsługiwane funkcje lub usługi
Następujące funkcje lub usługi nie są obsługiwane dla konta trybu failover, w wersji zapoznawczej:

- Usługa Azure File Sync nie obsługuje trybu failover z konta magazynu. Konta magazynu, zawierające udziałów plików platformy Azure jako punkty końcowe w chmurze w usłudze Azure File Sync nie powinny być przełączone w tryb failover. Ten sposób synchronizacji Przyczyna zatrzymania pracy i może również spowoduje nieoczekiwanej utraty danych w przypadku plików nowo warstwowych.  
- Kontami magazynu przy użyciu usługi Azure Data Lake Storage Gen2 hierarchicznej przestrzeni nazw nie może być przełączone w tryb failover.
- Konto magazynu zawierające obiekty BLOB zarchiwizowane nie przełączenie do trybu failover. Obsługa zarchiwizowane obiektów blob w oddzielne konto magazynu, którego nie chcesz w trybie Failover.
- Konto magazynu zawierające obiekty BLOB typu block w warstwie premium nie przełączenie do trybu failover. Kont magazynu, które obsługują obiekty BLOB typu block w warstwie premium nie obsługują obecnie nadmiarowości geograficznej.
- Po zakończeniu pracy w trybie failover następujące funkcje przestaną działać, jeśli pierwotnie włączone: [Subskrypcje zdarzeń](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-overview), [zasady cyklu życia](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts), [Storage Analytics rejestrowania](https://docs.microsoft.com/rest/api/storageservices/about-storage-analytics-logging).

## <a name="copying-data-as-an-alternative-to-failover"></a>Kopiowanie danych jako alternatywę do trybu failover

Jeśli skonfigurowano konta magazynu RA-GRS, musisz dostęp do odczytu do danych przy użyciu pomocniczego punktu końcowego. Jeśli wolisz nie w trybie Failover w przypadku awarii w regionie podstawowym, możesz użyć narzędzi takich jak [AzCopy](storage-use-azcopy.md), [programu Azure PowerShell](storage-powershell-guide-full.md), lub [Biblioteka przenoszenia danych usługi Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/) do Kopiowanie danych z konta magazynu w regionie pomocniczym do innego konta magazynu w regionie nie ma wpływu. Można następnie punkt aplikacji do tego konta magazynu dla odczytu i zapisu dostępności.

## <a name="microsoft-managed-failover"></a>Zarządzany przez firmę Microsoft trybu failover

W skrajnych okolicznościach, gdy region jest utracone z powodu awarii znaczące firmy Microsoft może zainicjować regionalnej pracy awaryjnej. W takim wypadku żadna akcja ze strony użytkownika jest wymagana. Przed ukończeniem pracy awaryjnej zarządzanych przez firmę Microsoft, nie będą mieć dostęp do zapisu do swojego konta magazynu. Twoje aplikacje mogą odczytywać z regionu pomocniczego, jeśli konto magazynu jest skonfigurowany w przypadku usługi RA-GRS. 

## <a name="see-also"></a>Zobacz także

* [Zainicjuj tryb failover konta (wersja zapoznawcza)](storage-initiate-account-failover.md)
* [Projektowanie wysoko dostępnych aplikacji przy użyciu magazynu RA-GRS](storage-designing-ha-apps-with-ragrs.md)
* [Samouczek: Tworzenie aplikacji o wysokiej dostępności z magazynu obiektów Blob](../blobs/storage-create-geo-redundant-storage.md) 
