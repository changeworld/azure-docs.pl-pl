---
title: Interfejs wiersza polecenia agenta połączonej maszyny platformy Azure
description: Dokumentacja referencyjna interfejsu wiersza polecenia programu Azure Connect Machine Agent
author: bobbytreed
manager: carmonm
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
ms.topic: reference
ms.date: 11/04/2019
ms.author: robreed
ms.openlocfilehash: d35c5e283f2e1e2f8afd431d83775167dc2a531a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513200"
---
# <a name="azure-connected-machine-agent-cli-interface"></a>Interfejs wiersza polecenia agenta połączonej maszyny platformy Azure

Narzędzie `Azcmagent` (Agent połączonej maszyny platformy Azure) służy do konfigurowania i rozwiązywania problemów z połączeniami maszyn nienależących do platformy Azure z platformą Azure.

Sam Agent to proces demona o nazwie `himdsd` w systemie Linux i usługa systemu Windows o nazwie `himds` w systemie Windows.

W normalnych warunkach użytkowania `azcmagent connect` jest używany do nawiązywania połączenia między tą maszyną i platformą Azure, a `azcmagent disconnect`, jeśli zdecydujesz, że nie chcesz już używać tego połączenia. Inne polecenia są przeznaczone do rozwiązywania problemów lub innych specjalnych przypadków.

## <a name="options"></a>Opcje

```none
  -h, --help      help for azcmagent
  -v, --verbose   Increase logging verbosity to show all logs
```

## <a name="see-also"></a>ZOBACZ RÓWNIEŻ

* [azcmagent Connect](#azcmagent-connect) — łączy tę maszynę z platformą Azure
* [azcmagent disconnect — rozłącza](#azcmagent-disconnect) tę maszynę od platformy Azure
* [azcmagent ponownie połącz](#azcmagent-reconnect) — ponownie nawiązuje połączenie z tą maszyną z platformą Azure
* [azcmagent show](#azcmagent-show) -pobiera metadane komputera i stan agenta. Jest to szczególnie przydatne w przypadku rozwiązywania problemów.
* [wersja azcmagent](#azcmagent-version) — wyświetla wersję agenta zarządzania hybrydowego

## <a name="azcmagent-connect"></a>azcmagent połączenie

Łączy tę maszynę z platformą Azure

### <a name="synopsis"></a>Streszczenie

Tworzy zasób na platformie Azure reprezentujący tę maszynę.

Spowoduje to użycie opcji uwierzytelniania dostarczonych do utworzenia zasobu w Azure Resource Manager reprezentującego tę maszynę. Zasób należy do subskrypcji i grupy zasobów, a dane dotyczące maszyny są przechowywane w regionie świadczenia usługi Azure określonym przez parametr Location.
Domyślna nazwa zasobu jest nazwą hosta tej maszyny, jeśli nie została przesłonięta.

Certyfikat odpowiadający tożsamości przypisanej do systemu jest następnie pobierany i przechowywany lokalnie. Po zakończeniu tego kroku usługa **metadanych połączonej maszyny platformy Azure** i Agent konfiguracji gościa rozpocznie synchronizację z chmurą platformy Azure.

Opcje uwierzytelniania:

* `azcmagent connect --access-token <> --subscription-id <> --resource-group <> --location <>` tokenu dostępu
* Identyfikator jednostki usługi i klucz tajny `azcmagent connect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid> --subscription-id <> --resource-group <> --location <>`
* Logowanie do urządzenia (Interactive) `azcmagent connect --tenant-id <> --subscription-id <> --resource-group <> --location <>`

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

## <a name="azcmagent-disconnect"></a>Odłączanie azcmagent

Rozłącza tę maszynę od platformy Azure

### <a name="synopsis"></a>Streszczenie

Usuwa zasób platformy Azure, który reprezentuje ten serwer.

To polecenie używa podanych opcji uwierzytelniania, aby usunąć zasób Azure Resource Manager reprezentujący tę maszynę. Po tym momencie **Metadata Service maszyny połączonej z platformą Azure** i Agent konfiguracji gościa zostaną odłączone. To polecenie nie zatrzymuje ani nie usuwa usług: Usuń pakiet, aby to zrobić.

To polecenie wymaga wyższych uprawnień niż rola "Dołączanie maszyny połączonej z platformą Azure".

Po odłączeniu komputera użyj `azcmagent connect`, a nie `azcmagent reconnect`, jeśli chcesz utworzyć nowy zasób dla niego na platformie Azure.

Opcje uwierzytelniania:

* `azcmagent disconnect --access-token <>` tokenu dostępu
* Identyfikator jednostki usługi i klucz tajny `azcmagent disconnect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid>`
* Logowanie interakcyjne urządzenia `azcmagent disconnect --tenant-id <>`

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

## <a name="azcmagent-reconnect"></a>azcmagent ponowne łączenie

Ponownie nawiązuje połączenie z tą maszyną z platformą Azure

### <a name="synopsis"></a>Streszczenie

Połącz ponownie maszynę z nieprawidłowymi poświadczeniami na platformie Azure.

Jeśli na komputerze znajduje się już zasób platformy Azure, ale nie można go uwierzytelnić, można go ponownie połączyć za pomocą tego polecenia. Jest to możliwe, jeśli maszyna została wyłączona wystarczająco długo, aby jej certyfikat wygaśnie (co najmniej 45 dni).

Jeśli maszyna została odłączona od `azcmagent disconnect`, użyj `azcmagent connect` zamiast tego.

To polecenie używa podanych opcji uwierzytelniania do pobrania nowych poświadczeń odpowiadających zasobowi Azure Resource Manager reprezentującemu ten komputer.

To polecenie wymaga wyższych uprawnień niż rola **dołączania maszyny połączonej z platformą Azure** .

Opcje uwierzytelniania

* `azcmagent reconnect --access-token <>` tokenu dostępu
* Identyfikator jednostki usługi i klucz tajny `azcmagent reconnect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid>`
* Logowanie interakcyjne urządzenia `azcmagent reconnect --tenant-id <>`

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

## <a name="azcmagent-show"></a>Pokaż azcmagent

Pobiera metadane komputera i stan agenta. Jest to szczególnie przydatne w przypadku rozwiązywania problemów.

### <a name="synopsis"></a>Streszczenie

Pobiera metadane komputera i stan agenta. Jest to szczególnie przydatne w przypadku rozwiązywania problemów.


### <a name="syntax"></a>Składnia

```
azcmagent show [flags]
```

### <a name="options"></a>Opcje

```
  -h, --help   help for show
```

## <a name="azcmagent-version"></a>wersja azcmagent

Wyświetl wersję agenta zarządzania hybrydowego

### <a name="synopsis"></a>Streszczenie

Wyświetl wersję agenta zarządzania hybrydowego

### <a name="syntax"></a>Składnia

```none
azcmagent version [flags]
```

### <a name="options"></a>Opcje

```none
  -h, --help   help for version
```
