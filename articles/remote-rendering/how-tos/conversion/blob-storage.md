---
title: Używanie usługi Azure Blob Storage do konwersji modelu
description: W tym artykule opisano typowe kroki konfigurowania i używania magazynu obiektów blob do konwersji modelu.
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 6f0605077bd131c54f27e3bf46240331557fd92e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681651"
---
# <a name="use-azure-blob-storage-for-model-conversion"></a>Używanie usługi Azure Blob Storage do konwersji modelu

Usługa [konwersji modelu](model-conversion.md) wymaga dostępu do magazynu obiektów blob platformy Azure, dzięki czemu można pobrać dane wejściowe i przechowywać dane wyjściowe. W tym artykule opisano, jak wykonać najpopularniejsze kroki.

## <a name="prepare-azure-storage-accounts"></a>Przygotowywanie kont usługi Azure Storage

- Tworzenie konta magazynu (StorageV2)
- Tworzenie wejściowego kontenera obiektów blob na koncie magazynu (na przykład o nazwie "arrinput")
- Tworzenie wyjściowego kontenera obiektów blob na koncie magazynu (na przykład o nazwie "arroutput")

> [!TIP]
> Aby uzyskać instrukcje krok po kroku dotyczące konfigurowania konta magazynu, zapoznaj się z programem [Szybki start: Konwertowanie modelu do renderowania](../../quickstarts/convert-model.md)

Tworzenie konta magazynu i kontenerów obiektów blob można wykonać za pomocą jednego z następujących narzędzi:

- [Azure Portal](https://portal.azure.com)
- [wiersz polecenia az](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/)
- SDK (C#, Python ... )

## <a name="ensure-azure-remote-rendering-can-access-your-storage-account"></a>Upewnij się, że renderowanie zdalne platformy Azure może uzyskać dostęp do twojego konta magazynu

Usługa Azure Remote Rending musi pobrać dane modelu z konta magazynu i zapisać dane z powrotem do niego.

Dostęp do konta magazynu można udzielić platformie Azure Remote Rendering na dwa sposoby:

### <a name="connect-your-azure-storage-account-with-your-azure-remote-rendering-account"></a>Łączenie konta usługi Azure Storage z kontem zdalnego renderowania platformy Azure

Wykonaj kroki podane w sekcji [Tworzenie konta.](../create-an-account.md#link-storage-accounts)

### <a name="retrieve-sas-for-the-storage-containers"></a>Pobieranie sygnatury dostępu Współdzielonego dla kontenerów magazynu

Sygnatury dostępu przechowywane (SAS) są używane do udzielania dostępu do odczytu dla danych wejściowych i dostępu do zapisu dla danych wyjściowych. Zaleca się generowanie nowych identyfikatorów URI przy każdym przekonwertowaniu modelu. Ponieważ identyfikatory URI wygasają po pewnym czasie, utrwalanie ich przez dłuższy czas może nieoczekiwanie ryzykować przerwanie aplikacji.

Szczegółowe informacje na temat sas można znaleźć w [dokumentacji SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).

Identyfikator URI sygnatury dostępu Współdzielonego można wygenerować przy użyciu jednego z:

- Moduł programu AZ PowerShell
  - zobacz [przykładowe skrypty programu PowerShell](../../samples/powershell-example-scripts.md)
- [wiersz polecenia az](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/)
  - kliknij prawym przyciskiem myszy na kontenerze "Pobierz sygnaturę dostępu współdzielonego" (czytaj, dostęp do listy dla kontenera wejściowego, dostęp do zapisu dla kontenera wyjściowego)
- SDK (C#, Python ... )

Przykład użycia podpisów dostępu współdzielonego w konwersji zasobów jest pokazany w pliku Conversion.ps1 [przykładowych skryptów programu Powershell.](../../samples/powershell-example-scripts.md#script-conversionps1)

## <a name="upload-an-input-model"></a>Przekazywanie modelu wprowadzania

Aby rozpocząć konwertowanie modelu, należy go przesłać, korzystając z jednej z następujących opcji:

- [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) — wygodny interfejs użytkownika do przekazywania,pobierania/zarządzania plikami w magazynie obiektów blob platformy Azure
- [Wiersz polecenia platformy Azure](https://docs.microsoft.com/azure/storage/common/storage-azure-cli)
- [Moduł programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.2.0)
  - zobacz [przykładowe skrypty programu PowerShell](../../samples/powershell-example-scripts.md)
- [Korzystanie z sdk magazynu (Python, C# ... )](https://docs.microsoft.com/azure/storage/)
- [Korzystanie z interfejsów API REST usługi Azure Storage](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api)

Na przykład sposobu przekazywania danych do konwersji można znaleźć w pliku Conversion.ps1 [przykładowych skryptów programu Powershell.](../../samples/powershell-example-scripts.md#script-conversionps1)

## <a name="get-a-sas-uri-for-the-converted-model"></a>Pobierz identyfikator URI sygnatury dostępu Współdzielonego dla przekonwertowanego modelu

Ten krok jest podobny do [pobierania sygnatury dostępu Współdzielonego dla kontenerów magazynu](#retrieve-sas-for-the-storage-containers). Jednak tym razem należy pobrać identyfikator URI sygnatury dostępu Współdzielonego dla pliku modelu, który został zapisany w kontenerze wyjściowym.

Na przykład, aby pobrać identyfikator URI sygnatury dostępu Współdzielonego za pośrednictwem [Eksploratora usługi Azure Storage,](https://azure.microsoft.com/features/storage-explorer/)kliknij prawym przyciskiem myszy plik modelu i wybierz "Pobierz podpis dostępu współdzielonego".

Sygnatura dostępu współdzielonego (SAS) do ładowania modeli jest potrzebna, jeśli konto magazynu nie jest połączone z kontem zdalnego renderowania platformy Azure. Możesz dowiedzieć się, jak połączyć swoje konto w [aplikacji Utwórz konto](../create-an-account.md#link-storage-accounts).

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie konwersji modelu](configure-model-conversion.md)
- [Interfejs API REST konwersji modelu](conversion-rest-api.md)
