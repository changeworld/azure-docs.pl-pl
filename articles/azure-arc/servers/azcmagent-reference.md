---
title: Interfejs wiersza polecenia agenta komputera połączonego z platformą Azure
description: Dokumentacja referencyjna dla interfejsu wiersza polecenia agenta połączonego komputera platformy Azure
author: bobbytreed
manager: carmonm
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
ms.topic: reference
ms.date: 11/04/2019
ms.author: robreed
ms.openlocfilehash: d35c5e283f2e1e2f8afd431d83775167dc2a531a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73513200"
---
# <a name="azure-connected-machine-agent-cli-interface"></a>Interfejs wiersza polecenia agenta komputera połączonego z platformą Azure

Narzędzie `Azcmagent` (Azure Connected Machine Agent) służy do konfigurowania i rozwiązywania problemów z połączeniem maszyn z platformą Azure bez platformy Azure.

Sam agent jest procesem demona `himdsd` wywołanego na Linuksie, a usługa systemu Windows wywoływana `himds` w systemie Windows.

W normalnym `azcmagent connect` użyciu jest używany do ustanawiania połączenia `azcmagent disconnect` między tym komputerem a platformą Azure, a jeśli zdecydujesz, że nie chcesz już tego połączenia. Inne polecenia służą do rozwiązywania problemów lub innych szczególnych przypadków.

## <a name="options"></a>Opcje

```none
  -h, --help      help for azcmagent
  -v, --verbose   Increase logging verbosity to show all logs
```

## <a name="see-also"></a>ZOBACZ TEŻ

* [azcmagent connect](#azcmagent-connect) — łączy ten komputer z platformą Azure
* [azcmagent disconnect](#azcmagent-disconnect) - Rozłącza ten komputer od platformy Azure
* [azcmagent reconnect](#azcmagent-reconnect) - Ponowne podłączanie tego komputera do platformy Azure
* [azcmagent show](#azcmagent-show) — pobiera metadane maszyny i stan agenta. Jest to przydatne przede wszystkim do rozwiązywania problemów.
* [wersja azcmagent](#azcmagent-version) - Wyświetlanie wersji hybrydowego agenta zarządzania

## <a name="azcmagent-connect"></a>azcmagent połączyć

Łączy tę maszynę z platformą Azure

### <a name="synopsis"></a>Streszczenie

Tworzy zasób na platformie Azure reprezentujący ten komputer.

Spowoduje to użycie opcji uwierzytelniania dostarczonych do utworzenia zasobu w usłudze Azure Resource Manager reprezentujący ten komputer. Zasób znajduje się w żądanej grupie subskrypcji i zasobów, a dane dotyczące komputera są przechowywane w regionie platformy Azure określonym przez parametr lokalizacji.
Domyślna nazwa zasobu jest nazwą hosta tego komputera, jeśli nie zostanie zastąpiona.

Certyfikat odpowiadający tożsamości przypisanej systemowi tego komputera jest następnie pobierany i przechowywany lokalnie. Po zakończeniu tego kroku usługa **metadanych połączonego komputera platformy Azure** i agent konfiguracji gościa rozpoczynają synchronizację z chmurą platformy Azure.

Opcje uwierzytelniania:

* Token dostępu`azcmagent connect --access-token <> --subscription-id <> --resource-group <> --location <>`
* Identyfikator jednostki głównej usługi i klucz tajny`azcmagent connect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid> --subscription-id <> --resource-group <> --location <>`
* Logowanie urządzenia (interaktywne)`azcmagent connect --tenant-id <> --subscription-id <> --resource-group <> --location <>`

### <a name="syntax"></a>Składnia

```none
azcmagent connect [flags]
```

### <a name="options"></a>Opcje

```none
      --access-token string               Access token
  -h, --help                              help for connect
  -l, --location string                   Location of the resource [Required]
      --physical-location string          Physical location of the resource
  -g, --resource-group string             Name of the resource group. [Required]
  -n, --resource-name string              Name of the resource. Defaults to Host Name
  -i, --service-principal-id string       Service Principal Id
  -p, --service-principal-secret string   Service Principal Secret
  -s, --subscription-id string            Subscription Id [Required]
  -t, --tags string                       Tags for resource
      --tenant-id string                  Tenant Id
```

## <a name="azcmagent-disconnect"></a>azcmagent odłączyć

Rozłącza ten komputer od platformy Azure

### <a name="synopsis"></a>Streszczenie

Usuwa zasób na platformie Azure, który reprezentuje ten serwer.

To polecenie używa opcji uwierzytelniania dostarczonych w celu usunięcia zasobu usługi Azure Resource Manager reprezentującego ten komputer. Po tym czasie **usługa metadanych połączonego komputera platformy Azure** i agent konfiguracji gościa zostaną rozłączone. To polecenie nie zatrzymuje ani nie usuwa usług: usuń pakiet, aby to zrobić.

To polecenie wymaga wyższych uprawnień niż rola "Dołączanie połączonego komputera platformy Azure".

Po odłączeniu komputera należy `azcmagent connect`użyć `azcmagent reconnect` , nie, jeśli chcesz utworzyć nowy zasób dla niego na platformie Azure.

Opcje uwierzytelniania:

* Token dostępu`azcmagent disconnect --access-token <>`
* Identyfikator jednostki głównej usługi i klucz tajny`azcmagent disconnect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid>`
* Logowanie się do urządzenia interaktywnego`azcmagent disconnect --tenant-id <>`

### <a name="syntax"></a>Składnia

```none
azcmagent disconnect [flags]
```

### <a name="options"></a>Opcje

```none
      --access-token string               Access token
  -h, --help                              help for disconnect
  -r, --resource-group string             Name of the resource group
  -n, --resource-name string              Name of the resource
  -i, --service-principal-id string       Service Principal Id
  -p, --service-principal-secret string   Service Principal Secret
  -s, --subscription-id string            Subscription Id
  -t, --tenant-id string                  Tenant Id
```

## <a name="azcmagent-reconnect"></a>azcmagent ponownie połączyć

Ponowne połączenie tego komputera z platformą Azure

### <a name="synopsis"></a>Streszczenie

Podłącz ponownie komputer z nieprawidłowymi poświadczeniami na platformie Azure.

Jeśli komputer ma już zasób na platformie Azure, ale nie jest w stanie uwierzytelnić się do niego, można go ponownie połączyć za pomocą tego polecenia. Jest to możliwe, jeśli urządzenie zostało wyłączone wystarczająco długo, aby jego certyfikat wygaśnie (co najmniej 45 dni).

Jeśli komputer został odłączony , `azcmagent disconnect`użyj zamiast tego. `azcmagent connect`

To polecenie używa opcji uwierzytelniania dostarczonych do pobierania nowych poświadczeń odpowiadających zasobowi usługi Azure Resource Manager reprezentującym ten komputer.

To polecenie wymaga wyższych uprawnień niż rola **dołączania połączonego komputera platformy Azure.**

Opcje uwierzytelniania

* Token dostępu`azcmagent reconnect --access-token <>`
* Identyfikator jednostki głównej usługi i klucz tajny`azcmagent reconnect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid>`
* Logowanie się do urządzenia interaktywnego`azcmagent reconnect --tenant-id <>`

### <a name="syntax"></a>Składnia

```none
azcmagent reconnect [flags]
```

### <a name="options"></a>Opcje

```none
      --access-token string               Access token
  -h, --help                              help for reconnect
  -l, --location string                   Location of the resource
  -g, --resource-group string             Name of the resource group.
  -n, --resource-name string              Name of the resource. Defaults to Host Name
  -i, --service-principal-id string       Service Principal Id
  -p, --service-principal-secret string   Service Principal Secret
  -s, --subscription-id string            Subscription Id
      --tenant-id string                  tenant id
```

## <a name="azcmagent-show"></a>azcmagent pokaż

Pobiera metadanych komputera i stan agenta. Jest to przydatne przede wszystkim do rozwiązywania problemów.

### <a name="synopsis"></a>Streszczenie

Pobiera metadanych komputera i stan agenta. Jest to przydatne przede wszystkim do rozwiązywania problemów.


### <a name="syntax"></a>Składnia

```
azcmagent show [flags]
```

### <a name="options"></a>Opcje

```
  -h, --help   help for show
```

## <a name="azcmagent-version"></a>wersja azcmagent

Wyświetlanie wersji hybrydowego agenta zarządzania

### <a name="synopsis"></a>Streszczenie

Wyświetlanie wersji hybrydowego agenta zarządzania

### <a name="syntax"></a>Składnia

```none
azcmagent version [flags]
```

### <a name="options"></a>Opcje

```none
  -h, --help   help for version
```
