---
title: Inicjowanie pracy awaryjnej konta magazynu (w wersji zapoznawczej) — usługa Azure Storage
description: Dowiedz się, jak zainicjować przetwórnie awaryjne konta w przypadku, gdy podstawowy punkt końcowy dla konta magazynu stanie się niedostępny. Przewijalnia awaryjna aktualizuje region pomocniczy, aby stał się regionem podstawowym dla konta magazynu.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/11/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 0c619224201d6225d5e5c127b342f71f2f7fced9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535356"
---
# <a name="initiate-a-storage-account-failover-preview"></a>Inicjowanie pracy awaryjnej konta magazynu (wersja zapoznawcza)

Jeśli podstawowy punkt końcowy dla konta magazynu geograficznie nadmiarowego staje się niedostępny z jakiegokolwiek powodu, można zainicjować pracę awaryjną konta (wersja zapoznawcza). Przewijalnia awaryjna konta aktualizuje pomocniczy punkt końcowy, aby stał się podstawowym punktem końcowym dla konta magazynu. Po zakończeniu pracy awaryjnej klienci mogą rozpocząć zapisywanie w nowym regionie podstawowym. Wymuszone praca awaryjna umożliwia utrzymanie wysokiej dostępności dla aplikacji.

W tym artykule pokazano, jak zainicjować konto pracy awaryjnej dla konta magazynu przy użyciu witryny Azure portal, PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby dowiedzieć się więcej o pracy awaryjnej konta, zobacz [Odzyskiwanie po awarii i tryb failover konta (wersja zapoznawcza) w usłudze Azure Storage](storage-disaster-recovery-guidance.md).

> [!WARNING]
> Przewijanie awaryjne konta zazwyczaj powoduje utratę niektórych danych. Aby zrozumieć implikacje pracy awaryjnej konta i przygotować się do utraty danych, przejrzyj [proces pracy awaryjnej konta](storage-disaster-recovery-guidance.md#understand-the-account-failover-process).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed wykonaniem pracy awaryjnej konta na koncie magazynu upewnij się, że wykonano następujący krok:

- Upewnij się, że konto magazynu jest skonfigurowane do używania magazynu geograficznie nadmiarowego (GRS) lub magazynu geograficznego dostępu do odczytu (RA-GRS). Aby uzyskać więcej informacji na temat magazynu geograficznie nadmiarowego, zobacz [Nadmiarowość usługi Azure Storage](storage-redundancy.md).

## <a name="important-implications-of-account-failover"></a>Ważne implikacje pracy awaryjnej konta

Po zainicjowaniu pracy awaryjnej konta dla konta magazynu rekordy DNS dla pomocniczego punktu końcowego są aktualizowane tak, aby pomocniczy punkt końcowy stał się podstawowym punktem końcowym. Przed zainicjowaniem pracy awaryjnej upewnij się, że rozumiesz potencjalny wpływ na konto magazynu.

Aby oszacować zakres prawdopodobnej utraty danych przed zainicjowaniem pracy awaryjnej, `Get-AzStorageAccount` sprawdź właściwość Czas ostatniej `-IncludeGeoReplicationStats` **synchronizacji** przy użyciu polecenia cmdlet programu PowerShell i dołącz parametr. Następnie sprawdź `GeoReplicationStats` właściwość dla swojego konta. \

Po przemienniu awaryjnym typ konta magazynu jest automatycznie konwertowany na magazyn nadmiarowy lokalnie (LRS) w nowym regionie podstawowym. Można ponownie włączyć magazyn geograficznie nadmiarowy (GRS) lub magazyn geograficzny dostępu do odczytu (RA-GRS) dla konta. Należy zauważyć, że konwersja z LRS do GRS lub RA-GRS wiąże się z dodatkowymi kosztami. Aby uzyskać dodatkowe informacje, zobacz [Szczegóły cen przepustowości](https://azure.microsoft.com/pricing/details/bandwidth/).

Po ponownym włączeniu GRS dla konta magazynu firma Microsoft rozpoczyna replikowanie danych na koncie do nowego regionu pomocniczego. Czas replikacji zależy od ilości replikowanych danych.  

## <a name="portal"></a>[Portal](#tab/azure-portal)

Aby zainicjować przebłaję konta z witryny Azure portal, wykonaj następujące kroki:

1. Przejdź do swojego konta magazynu.
2. W obszarze **Ustawienia**wybierz pozycję **Replikacja geograficzna**. Na poniższej ilustracji przedstawiono stan replikacji geograficznej i pracy awaryjnej konta magazynu.

    ![Zrzut ekranu przedstawiający replikację geograficzną i stan trybu failover](media/storage-initiate-account-failover/portal-failover-prepare.png)

3. Sprawdź, czy konto magazynu jest skonfigurowane dla magazynu geograficznie nadmiarowego (GRS) lub magazynu geograficznego dostępu do odczytu (RA-GRS). Jeśli tak nie jest, wybierz **pozycję Konfiguracja** w obszarze **Ustawienia,** aby zaktualizować konto jako geobeksochy. 
4. **Właściwość Czas ostatniej synchronizacji** wskazuje, jak daleko pomocniczy jest za od podstawowego. **Czas ostatniej synchronizacji** zapewnia oszacowanie zakresu utraty danych, który będzie doświadczany po zakończeniu pracy awaryjnej.
5. Wybierz pozycję **Przygotuj się do pracy awaryjnej (wersja zapoznawcza).** 
6. Przejrzyj okno dialogowe potwierdzenia. Gdy będziesz gotowy, wprowadź **tak,** aby potwierdzić i zainicjować przebienie awaryjne.

    ![Zrzut ekranu przedstawiający okno dialogowe potwierdzenia pracy awaryjnej konta](media/storage-initiate-account-failover/portal-failover-confirm.png)

## <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Aby za pomocą programu PowerShell zainicjować tryb failover konta, należy najpierw zainstalować moduł podglądu 6.0.1. Aby zainstalować moduł, wykonaj następujące czynności:

1. Odinstaluj wszystkie poprzednie instalacje programu Azure PowerShell:

    - Usuń wszystkie poprzednie instalacje programu Azure PowerShell z systemu Windows przy użyciu ustawienia **funkcji & aplikacje** w obszarze **Ustawienia**.
    - Usuń wszystkie moduły `%Program Files%\WindowsPowerShell\Modules`platformy **Azure** z pliku .

1. Upewnij się, że masz zainstalowaną najnowszą wersję programu PowerShellGet. Otwórz okno programu Windows PowerShell i uruchom następujące polecenie, aby zainstalować najnowszą wersję:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. Zamknij i ponownie otwórz okno programu PowerShell po zainstalowaniu programu PowerShellGet. 

1. Zainstaluj najnowszą wersję programu Azure PowerShell:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Zainstaluj moduł usługi Azure Storage w wersji zapoznawczej, który obsługuje funkcję failover konta:

    ```powershell
    Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.1.1-preview –AllowPrerelease –AllowClobber –Force 
    ```

1. Zamknij i ponownie otwórz okno programu PowerShell.
 
Aby zainicjować przekonywowanie pracy awaryjnej konta z programu PowerShell, wykonaj następujące polecenie:

```powershell
Invoke-AzStorageAccountFailover -ResourceGroupName <resource-group-name> -Name <account-name> 
```

## <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby za pomocą narzędzia Azure CLI zainicjować przebój obsługi konta, wykonaj następujące polecenia:

```azurecli
az storage account show \ --name accountName \ --expand geoReplicationStats
az storage account failover \ --name accountName
```

---

## <a name="next-steps"></a>Następne kroki

- [Odzyskiwanie po awarii i przewijanie awaryjne konta (wersja zapoznawcza) w usłudze Azure Storage](storage-disaster-recovery-guidance.md)
- [Projektowanie wysoko dostępnych aplikacji przy użyciu magazynu RA-GRS](storage-designing-ha-apps-with-ragrs.md)
- [Samouczek: Tworzenie aplikacji o wysokiej dostępności z pamięcią blob](../blobs/storage-create-geo-redundant-storage.md) 
