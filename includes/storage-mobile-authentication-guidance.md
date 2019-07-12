---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: fe4ecc237b56575f99844d3ec074225fadb69d3c
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673452"
---
## <a name="configure-your-application-to-access-azure-storage"></a>Konfigurowanie aplikacji dostępu do magazynu Azure
Istnieją dwa sposoby uwierzytelniania aplikacji na dostęp do usług magazynu:

* Klucz współużytkowany: Użyj klucza wspólnego tylko do celów testowych
* Sygnatura dostępu współdzielonego (SAS): Użyj sygnatury dostępu Współdzielonego dla aplikacji produkcyjnych

### <a name="shared-key"></a>Klucz wspólny
Uwierzytelnianie za pomocą udostępnionego klucza oznacza, że aplikacja będzie używać swojego konta i klucz konta dostępu do usług magazynu. Na potrzeby szybkiego przedstawiający sposób użycia tej biblioteki firma Microsoft będzie używać uwierzytelniania klucza wspólnego, w tym wprowadzenie.

> [!WARNING] 
> **Tylko do celów testowych należy użyć uwierzytelniania klucza wspólnego!** Nazwa konta i klucz konta, które udzielić Pełny odczyt/zapis dostępu do skojarzonego konta magazynu, zostanie przekazany do każdej osoby, która pobiera aplikację. Jest to **nie** jest dobrą rozwiązaniem, ponieważ istnieje ryzyko, że masz klucz zagrozić niezaufani klienci.
> 
> 

Korzystając z uwierzytelniania klucza wspólnego, utworzysz [parametry połączenia](../articles/storage/common/storage-configure-connection-string.md). Parametry połączenia składa się z:  

* **DefaultEndpointsProtocol** — możesz wybrać HTTP lub HTTPS. Jednak przy użyciu protokołu HTTPS zdecydowanie zaleca się.
* **Nazwa konta** -nazwa konta magazynu
* **Klucz konta** — na [witryny Azure Portal](https://portal.azure.com), przejdź do swojego konta magazynu i kliknij **klucze** ikonę, aby znaleźć te informacje.
* (Opcjonalnie) **EndpointSuffix** — używany do usług magazynu w regionach z innym punktem końcowym sufiksy, takich jak Azure — Chiny lub Azure rządów.

Oto przykład parametrów połączenia przy użyciu uwierzytelniania klucza wspólnego:

`"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"`

### <a name="shared-access-signatures-sas"></a>Sygnatury dostępu współdzielonego (SAS)
W przypadku aplikacji mobilnych zalecaną metodą uwierzytelnić żądania przez klienta usługi Azure Storage jest przy użyciu sygnatury dostępu współdzielonego (SAS). Sygnatury dostępu Współdzielonego umożliwia udzielenie dostępu klienta do zasobu w określonym przedziale czasu, z określonym zestawem uprawnień.
Jako właściciel konta magazynu należy wygenerować sygnaturę dostępu Współdzielonego dla klientów mobilnych korzystać. Aby wygenerować sygnaturę dostępu Współdzielonego, prawdopodobnie należy zapisać osobną usługą, która generuje sygnaturę dostępu Współdzielonego do dystrybucji do klientów. Do celów testowych możesz użyć [Microsoft Azure Storage Explorer](https://storageexplorer.com) lub [witryny Azure Portal](https://portal.azure.com) aby wygenerować sygnaturę dostępu Współdzielonego. Podczas tworzenia sygnatury dostępu Współdzielonego, można określić interwał czasu, przez który sygnatura dostępu Współdzielonego jest prawidłowy oraz uprawnienia, które sygnatury dostępu Współdzielonego nadaje się do klienta.

Poniższy przykład pokazuje, jak wygenerować sygnaturę dostępu Współdzielonego za pomocą Eksploratora usługi Microsoft Azure Storage.

1. Jeśli jeszcze nie, [Zainstaluj oprogramowanie Microsoft Azure Storage Explorer](https://storageexplorer.com)
2. Nawiąż połączenie ze swoją subskrypcją.
3. Kliknij na swoim koncie magazynu, a następnie kliknij kartę "Akcje" w lewym dolnym rogu. Kliknij przycisk "Uzyskaj sygnaturę dostępu współdzielonego" w celu wygenerowania ciąg"połączenia" dla Twojego sygnatury dostępu Współdzielonego.
4. Oto przykład parametrów połączenia sygnatury dostępu Współdzielonego, przyznaje uprawnienia odczytu i zapisu na poziomie usługi, kontenerów i obiektów usługi obiektów blob konta magazynu.
   
   `"SharedAccessSignature=sv=2015-04-05&ss=b&srt=sco&sp=rw&se=2016-07-21T18%3A00%3A00Z&sig=3ABdLOJZosCp0o491T%2BqZGKIhafF1nlM3MzESDDD3Gg%3D;BlobEndpoint=https://youraccount.blob.core.windows.net"`

Jak widać, korzystając z sygnatury dostępu Współdzielonego, klucz konta jest ujawniany nie w aplikacji. Dowiedz się więcej na temat sygnatury dostępu Współdzielonego i najlepsze rozwiązania dotyczące przy użyciu sygnatury dostępu Współdzielonego, sprawdzając [sygnatur dostępu współdzielonego: Opis modelu sygnatur dostępu Współdzielonego](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md).

