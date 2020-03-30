---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: fe4ecc237b56575f99844d3ec074225fadb69d3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67673452"
---
## <a name="configure-your-application-to-access-azure-storage"></a>Konfigurowanie aplikacji w celu uzyskiwania dostępu do usługi Azure Storage
Istnieją dwa sposoby uwierzytelniania aplikacji w celu uzyskania dostępu do usług magazynu:

* Klucz udostępniony: Używanie klucza udostępnionego tylko do celów testowych
* Sygnatura dostępu współdzielonego (SAS): używanie sygnatury dostępu współdzielonego dla aplikacji produkcyjnych

### <a name="shared-key"></a>Klucz wspólny
Uwierzytelnianie za pomocą klucza udostępnionego oznacza, że aplikacja będzie używać nazwy konta i klucza konta, aby uzyskać dostęp do usług magazynu. W celu szybkiego pokazania, jak korzystać z tej biblioteki, będziemy używać uwierzytelniania klucza udostępnionego w tym początku.

> [!WARNING] 
> **Uwierzytelnianie klucza udostępnionego należy używać tylko do celów testowych!** Nazwa konta i klucz konta, które zapewniają pełny dostęp do odczytu/zapisu do powiązanego konta Magazynu, zostaną rozesłane do każdej osoby, która pobierze aplikację. **Nie** jest to dobra praktyka, ponieważ istnieje ryzyko, że klucz zostanie naruszony przez niezaufanych klientów.
> 
> 

Korzystając z uwierzytelniania klucza udostępnionego, utworzysz [parametry połączenia](../articles/storage/common/storage-configure-connection-string.md). Parametry połączenia składają się z:  

* **DefaultEndpointsProtocol** — można wybrać protokół HTTP lub HTTPS. Jednak przy użyciu protokołu HTTPS jest wysoce zalecane.
* **Nazwa konta** - nazwa konta magazynu
* **Klucz konta** — w witrynie [Azure Portal](https://portal.azure.com)przejdź do konta magazynu i kliknij ikonę **Klucze,** aby znaleźć te informacje.
* (Opcjonalnie) **EndpointSuffix** — jest używany do usług magazynu w regionach z różnymi sufiksami punktów końcowych, takimi jak Azure China lub Azure Governance.

Oto przykład ciągu połączenia przy użyciu uwierzytelniania klucza udostępnionego:

`"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"`

### <a name="shared-access-signatures-sas"></a>Sygnatury dostępu współdzielonego (SAS)
W przypadku aplikacji mobilnej zalecaną metodą uwierzytelniania żądania przez klienta względem usługi Azure Storage jest użycie sygnatury dostępu współdzielonego (SAS). Sygnatury dostępu współdzielonego umożliwia klientowi dostęp do zasobu przez określony czas, z określonym zestawem uprawnień.
Jako właściciel konta magazynu musisz wygenerować sygnaturę współdzieloną dla klientów mobilnych, aby zużyć. Aby wygenerować sygnatury dostępu Współdzielonego, prawdopodobnie należy napisać oddzielną usługę, która generuje sygnatury dostępu Współdzielonego do dystrybucji do klientów. Do celów testowych można użyć [Eksploratora magazynu platformy Microsoft Azure](https://storageexplorer.com) lub [witryny Azure Portal](https://portal.azure.com) do wygenerowania sygnatury dostępu Współdzielonego. Podczas tworzenia sygnatury dostępu Współdzielonego można określić przedział czasu, w którym sygnatury dostępu Współdzielonego jest prawidłowy i uprawnienia, które sygnatury dostępu Współdzielonego udziela klientowi.

W poniższym przykładzie pokazano, jak za pomocą Eksploratora magazynu platformy Microsoft Azure do generowania sygnatury dostępu Współdzielonego.

1. Jeśli jeszcze tego nie zrobiłeś, [zainstaluj Eksploratora magazynu platformy Microsoft Azure](https://storageexplorer.com)
2. Nawiąż połączenie ze swoją subskrypcją.
3. Kliknij swoje konto magazynu i kliknij kartę "Akcje" w lewym dolnym rogu. Kliknij przycisk "Pobierz podpis dostępu współdzielonego", aby wygenerować "parametry połączenia" dla sygnatury dostępu Współdzielonego.
4. Oto przykład ciągu połączenia sygnatury dostępu Współdzielonego, który udziela uprawnień do odczytu i zapisu na poziomie usługi, kontenera i obiektu dla usługi obiektu blob konta magazynu.
   
   `"SharedAccessSignature=sv=2015-04-05&ss=b&srt=sco&sp=rw&se=2016-07-21T18%3A00%3A00Z&sig=3ABdLOJZosCp0o491T%2BqZGKIhafF1nlM3MzESDDD3Gg%3D;BlobEndpoint=https://youraccount.blob.core.windows.net"`

Jak widać, podczas korzystania z sygnatury dostępu Współdzielonego nie ujawniasz klucza konta w aplikacji. Więcej informacji na temat sygnatury dostępu Współdzielonego i najlepszych rozwiązań dotyczących używania sygnatury dostępu współdzielonego można uzyskać, wyewidencjonowywanie [sygnatur dostępu współdzielonego: opis modelu sygnatury dostępu współdzielonego.](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md)

