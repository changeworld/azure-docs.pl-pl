---
title: Zagadnienia dotyczące magazynu dla usług Azure Functions
description: Dowiedz się więcej o wymaganiach dotyczących magazynowania usług Azure Functions i szyfrowaniu przechowywanych danych.
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 3bacc93ad6c1851d9165e8efb7d27b427050e6f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276584"
---
# <a name="storage-considerations-for-azure-functions"></a>Zagadnienia dotyczące magazynu dla usług Azure Functions

Usługa Azure Functions wymaga konta usługi Azure Storage podczas tworzenia wystąpienia aplikacji funkcji. Aplikacja funkcji może korzystać z następujących usług magazynu:


|Usługa magazynowania  | Użycie funkcji  |
|---------|---------|
| [Magazyn obiektów Blob platformy Azure](../storage/blobs/storage-blobs-introduction.md)     | Obsługa stanu powiązań i kluczy funkcyjnych.  <br/>Używane również przez [koncentratory zadań w funkcji trwałych](durable/durable-functions-task-hubs.md). |
| [Pliki platformy Azure](../storage/files/storage-files-introduction.md)  | Udział plików używany do przechowywania i uruchamiania kodu aplikacji funkcji w [planie zużycia](functions-scale.md#consumption-plan). |
| [Magazyn kolejki platformy Azure](../storage/queues/storage-queues-introduction.md)     | Używane przez [koncentratory zadań w funkcji trwałych](durable/durable-functions-task-hubs.md).   |
| [Azure Table storage](../storage/tables/table-storage-overview.md)  |  Używane przez [koncentratory zadań w funkcji trwałych](durable/durable-functions-task-hubs.md).       |

> [!IMPORTANT]
> Podczas korzystania z planu hostingu Zużycie kod funkcji i pliki konfiguracji powiązania są przechowywane w usłudze Azure File Storage na głównym koncie magazynu. Po usunięciu głównego konta magazynu ta zawartość zostanie usunięta i nie będzie można jej odzyskać.

## <a name="storage-account-requirements"></a>Wymagania konta magazynu

Podczas tworzenia aplikacji funkcji, należy utworzyć lub łącze do ogólnego przeznaczenia konta usługi Azure Storage, który obsługuje blob, kolejki i magazynu tabel. Dzieje się tak, ponieważ funkcje opiera się na usłudze Azure Storage dla operacji, takich jak zarządzanie wyzwalania i rejestrowanie wykonywania funkcji. Niektóre konta magazynu nie obsługują kolejek i tabel. Te konta obejmują konta magazynu tylko dla obiektów blob, usługi Azure Premium Storage i konta magazynu ogólnego przeznaczenia z replikacją ZRS. Te nieobsługiwały konta są filtrowane z bloku Konto magazynu podczas tworzenia aplikacji funkcji.

Aby dowiedzieć się więcej na temat typów kont magazynu, zobacz [Wprowadzenie do usług Azure Storage](../storage/common/storage-introduction.md#azure-storage-services). 

Chociaż można użyć istniejącego konta magazynu z aplikacją funkcji, należy upewnić się, że spełnia te wymagania. Konta magazynu utworzone w ramach przepływu tworzenia aplikacji funkcji są gwarantowane, aby spełnić te wymagania dotyczące konta magazynu.  

## <a name="storage-account-guidance"></a>Wskazówki dotyczące konta magazynu

Każda aplikacja funkcji wymaga konta magazynu do działania. Jeśli to konto zostanie usunięte, aplikacja funkcji nie będzie działać. Aby rozwiązać problemy związane z magazynowaniem, zobacz [Jak rozwiązywać problemy związane z magazynowaniem](functions-recover-storage-account.md). Następujące dodatkowe zagadnienia dotyczą konta magazynu używanego przez aplikacje funkcji.

### <a name="storage-account-connection-setting"></a>Ustawienie połączenia konta magazynu

Połączenie z kontem magazynu jest obsługiwane w [ustawieniu aplikacji AzureWebJobsStorage](./functions-app-settings.md#azurewebjobsstorage). 

Parametry połączenia konta magazynu muszą zostać zaktualizowane podczas ponownego generowania kluczy magazynu. [Dowiedz się więcej o zarządzaniu kluczami pamięci masowej tutaj](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account).

### <a name="shared-storage-accounts"></a>Udostępnione konta magazynu

Wiele aplikacji funkcji może udostępniać to samo konto magazynu bez żadnych problemów. Na przykład w programie Visual Studio można tworzyć wiele aplikacji przy użyciu emulatora usługi Azure Storage. W takim przypadku emulator działa jak jedno konto magazynu. To samo konto magazynu używane przez aplikację funkcji może również służyć do przechowywania danych aplikacji. Jednak takie podejście nie zawsze jest dobrym pomysłem w środowisku produkcyjnym.

### <a name="optimize-storage-performance"></a>Optymalizowanie wydajności magazynu

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

## <a name="storage-data-encryption"></a>Szyfrowanie danych magazynu

Usługa Azure Storage szyfruje wszystkie dane na koncie magazynu w stanie spoczynku. Aby uzyskać więcej informacji, zobacz [Szyfrowanie usługi Azure Storage dla danych w spoczynku](../storage/common/storage-service-encryption.md).

Domyślnie dane są szyfrowane za pomocą kluczy zarządzanych przez firmę Microsoft. Aby uzyskać dodatkową kontrolę nad kluczami szyfrowania, można podać klucze zarządzane przez klienta do szyfrowania danych obiektów blob i plików. Te klucze muszą być obecne w usłudze Azure Key Vault for Functions, aby mieć dostęp do konta magazynu. Aby dowiedzieć się więcej, zobacz [Konfigurowanie kluczy zarządzanych przez klienta za pomocą usługi Azure Key Vault przy użyciu witryny Azure portal](../storage/common/storage-encryption-keys-portal.md).  

## <a name="mount-file-shares-linux"></a>Instalowanie udziałów plików (Linux)

Istniejące udziały usługi Azure Files można zainstalować w aplikacjach funkcji systemu Linux. Montując udział w aplikacji funkcji systemu Linux, można korzystać z istniejących modeli uczenia maszynowego lub innych danych w swoich funkcjach. Za pomocą [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) polecenia można zainstalować istniejący udział w aplikacji funkcji systemu Linux. 

W tym `share-name` poleceniu jest nazwa istniejącego `custom-id` udziału usługi Azure Files i może być dowolny ciąg, który jednoznacznie definiuje udział po zainstalowaniu do aplikacji funkcji. Ponadto `mount-path` jest ścieżka, z której jest dostępny udział w aplikacji funkcji. `mount-path`musi być w `/dir-name`formacie i nie może `/home`zaczynać się od .

Pełny przykład można znaleźć w obszarze [Tworzenie aplikacji funkcji Języka Python i montowanie udziału usługi Azure Files.](scripts/functions-cli-mount-files-storage-linux.md) 

Obecnie obsługiwany jest `storage-type` `AzureFiles` tylko jeden z nich. Można zainstalować tylko pięć udziałów w danej aplikacji funkcji. Montaż udziału plików może wydłużyć czas zimnego rozruchu o co najmniej 200-300 m, a nawet więcej, gdy konto magazynu znajduje się w innym regionie.

Zainstalowany udział jest dostępny dla kodu `mount-path` funkcji w określonym. Na przykład, `mount-path` `/path/to/mount`gdy jest , można uzyskać dostęp do katalogu docelowego za pomocą interfejsów API systemu plików, jak w poniższym przykładzie Pythona:

```python
import os
...

files_in_share = os.listdir("/path/to/mount")
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o opcjach hostingu usługi Azure Functions.

> [!div class="nextstepaction"]
> [Skalowanie i hosting usługi Azure Functions](functions-scale.md)


