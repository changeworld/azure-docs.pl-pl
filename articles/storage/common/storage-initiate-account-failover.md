---
title: Zainicjuj magazynu konta tryb failover (wersja zapoznawcza) — usługi Azure Storage
description: Dowiedz się, jak zainicjować trybu failover konta w przypadku, gdy podstawowego punktu końcowego konta magazynu jest niedostępna. Przełączenie w tryb failover aktualizuje regionu pomocniczego, aby stać się w regionie podstawowym konta magazynu.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/11/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: fd8eecbd20446bfde8d3a7467e2982398c3b8c19
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60320519"
---
# <a name="initiate-a-storage-account-failover-preview"></a>Zainicjuj tryb failover konta magazynu (wersja zapoznawcza)

Jeśli podstawowego punktu końcowego konta magazynu geograficznie nadmiarowego stanie się niedostępna z jakiegokolwiek powodu, może zainicjować trybu failover konta (wersja zapoznawcza). Tryb failover konta aktualizuje pomocniczego punktu końcowego, aby stać się podstawowego punktu końcowego konta magazynu. Po zakończeniu pracy w trybie failover klientów można rozpocząć pisanie do nowego regionu podstawowego. Wymuszone trybu failover można zapewnić wysoką dostępność dla aplikacji.

W tym artykule pokazano, jak zainicjować failover konta dla konta magazynu przy użyciu witryny Azure portal, programu PowerShell lub wiersza polecenia platformy Azure. Aby dowiedzieć się więcej na temat trybu failover konta, zobacz [awaryjnego odzyskiwania i konto pracy awaryjnej (wersja zapoznawcza) w usłudze Azure Storage](storage-disaster-recovery-guidance.md).

> [!WARNING]
> Tryb failover konta zwykle powoduje utratę danych. Aby umożliwić poznanie skutków konta przejścia w tryb failover i przygotować się do utraty danych, przeczytaj [zrozumienie procesu pracy awaryjnej konta](storage-disaster-recovery-guidance.md#understand-the-account-failover-process).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy awaryjnej konta na swoim koncie magazynu, upewnij się, czy wykonano następujące czynności:

- Rejestrowanie na potrzeby trybu failover konto w wersji zapoznawczej. Aby uzyskać informacje o tym jak zarejestrować, zobacz [o wersji zapoznawczej](storage-disaster-recovery-guidance.md#about-the-preview).
- Upewnij się, że konta magazynu jest skonfigurowany do użycia, dostęp do odczytu magazynu geograficznie nadmiarowego (RA-GRS) lub magazyn geograficznie nadmiarowy (GRS). Aby uzyskać więcej informacji na temat magazynu geograficznie nadmiarowego zobacz [magazyn geograficznie nadmiarowy (GRS): Replikacji między regionami dla usługi Azure Storage](storage-redundancy-grs.md). 

## <a name="important-implications-of-account-failover"></a>Istotny wpływ na konto przejścia w tryb failover

Po zainicjowaniu trybu failover konta dla konta magazynu, rekordy DNS dla pomocniczego punktu końcowego są aktualizowane, tak że pomocniczego punktu końcowego staje się podstawowego punktu końcowego. Upewnij się, zrozumieć potencjalny wpływ na koncie magazynu, przed rozpoczęciem pracy w trybie failover.

Aby oszacować stopnia utraty danych prawdopodobnie przed rozpoczęciem pracy w trybie failover, zapoznaj się z **czas ostatniej synchronizacji** właściwość za pomocą `Get-AzStorageAccount` polecenia cmdlet programu PowerShell i obejmują `-IncludeGeoReplicationStats` parametru. Następnie sprawdź `GeoReplicationStats` właściwości konta. 

Po włączeniu trybu failover typ konta magazynu jest automatycznie konwertowane na magazyn lokalnie nadmiarowy (LRS) w nowym regionie podstawowym. Można ponownie włączyć magazyn geograficznie nadmiarowy (GRS) lub magazyn geograficznie nadmiarowy dostęp do odczytu (RA-GRS) dla konta. Należy pamiętać, że konwersja z LRS na GRS lub RA-GRS spowoduje naliczenie dodatkowych kosztów. Aby uzyskać więcej informacji, zobacz [przepustowość — szczegóły cennika](https://azure.microsoft.com/pricing/details/bandwidth/). 

Ponownie włącz GRS konta magazynu, Microsoft rozpoczyna replikację danych w ramach Twojego konta do nowego regionu pomocniczego. Czas replikacji jest zależna od ilości danych jest replikowana.  

## <a name="azure-portal"></a>Azure Portal

Aby zainicjować trybu failover konta w witrynie Azure portal, wykonaj następujące kroki:

1. Przejdź do swojego konta magazynu.
2. W obszarze **ustawienia**, wybierz opcję **Geografickou replikaci**. Na poniższej ilustracji przedstawiono stan replikacji geograficznej i trybu failover na koncie magazynu.

    ![Zrzut ekranu przedstawiający stan replikacji geograficznej i trybu failover](media/storage-initiate-account-failover/portal-failover-prepare.png)

3. Sprawdź, czy konta magazynu została skonfigurowana dla magazynu geograficznie nadmiarowego (GRS) lub magazyn geograficznie nadmiarowy dostęp do odczytu (RA-GRS). Jeśli tak nie jest, wybierz **konfiguracji** w obszarze **ustawienia** można zaktualizować swoje konto magazynu geograficznie nadmiarowego. 
4. **Czas ostatniej synchronizacji** właściwość wskazuje, jak daleko pomocnicza jest za zaporą z serwera podstawowego. **Czas ostatniej synchronizacji** zapewnia oszacowanie stopnia utraty danych, które wystąpią po zakończeniu pracy w trybie failover.
5. Wybierz **przygotować się do trybu failover (wersja zapoznawcza)**. 
6. Przejrzyj okno dialogowe potwierdzenia. Gdy wszystko będzie gotowe, wprowadź **tak** aby potwierdzić, a następnie zainicjuj tryb failover.

    ![Zrzut ekranu przedstawiający potwierdzenia w oknie dialogowym dla konta trybu failover](media/storage-initiate-account-failover/portal-failover-confirm.png)

## <a name="powershell"></a>PowerShell

Aby zainicjować trybu failover konta przy użyciu programu PowerShell, należy najpierw zainstalować 6.0.1 modułu w wersji zapoznawczej. Wykonaj następujące kroki, aby zainstalować moduł:

1. Odinstaluj wszystkie poprzednie instalacje programu Azure PowerShell:

    - Usuń wszystkie poprzednie instalacje programu Azure PowerShell z Windows przy użyciu **aplikacje i funkcje** w obszarze **ustawienia**.
    - Usuń wszystkie **Azure*** modułów z `%Program Files%\WindowsPowerShell\Modules`.
    
1. Upewnij się, że masz najnowszą wersję zainstalowanego modułu PowerShellGet. Otwórz okno programu Windows PowerShell i uruchom następujące polecenie, aby zainstalować najnowszą wersję:
 
    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```
1. Zamknij i Otwórz okno programu PowerShell po zainstalowaniu modułu PowerShellGet. 

1. Zainstaluj najnowszą wersję programu Azure PowerShell:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Instalowanie modułu w wersji zapoznawczej usługi Azure Storage, który obsługuje usługi Azure AD:
   
    ```powershell
    Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.1.1-preview –AllowPrerelease –AllowClobber –Force 
    ```
1. Zamknij i Otwórz okno programu PowerShell.
 

Aby zainicjować trybu failover konta, za pomocą programu PowerShell, uruchom następujące polecenie:

```powershell
Invoke-AzStorageAccountFailover -ResourceGroupName <resource-group-name> -Name <account-name> 
```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby zainicjować trybu failover konta za pomocą wiersza polecenia platformy Azure, wykonaj następujące polecenia:

```cli
az storage account show \ --name accountName \ --expand geoReplicationStats
az storage account failover \ --name accountName
```

## <a name="next-steps"></a>Kolejne kroki

- [Awaryjnego odzyskiwania i konto pracy awaryjnej (wersja zapoznawcza) w usłudze Azure Storage](storage-disaster-recovery-guidance.md)
- [Projektowanie wysoko dostępnych aplikacji przy użyciu magazynu RA-GRS](storage-designing-ha-apps-with-ragrs.md)
- [Samouczek: Tworzenie aplikacji o wysokiej dostępności z magazynu obiektów Blob](../blobs/storage-create-geo-redundant-storage.md) 
