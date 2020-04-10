---
title: Zarządzanie pamięcią podręczną platformy Azure dla programu Redis przy użyciu klasycznego interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak zainstalować klasyczną platformę cli platformy Azure na dowolnej platformie, jak go używać do łączenia się z kontem platformy Azure oraz jak utworzyć pamięć podręczną Azure Cache dla programu Redis i zarządzać nią z klasycznego interfejsu wiersza polecenia.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 01/23/2017
ms.author: yegu
ms.openlocfilehash: f71476d7d41ae45d2f1014ed1b257870622487e6
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010838"
---
# <a name="how-to-create-and-manage-azure-cache-for-redis-using-the-azure-classic-cli"></a>Jak tworzyć pamięć podręczną Azure Cache dla programu Redis i zarządzać nią przy użyciu klasycznego interfejsu wiersza polecenia platformy Azure
> [!div class="op_single_selector"]
> * [PowerShell](cache-how-to-manage-redis-cache-powershell.md)
> * [Klasyczny interfejs wiersza polecenia platformy Azure](cache-manage-cli.md)
>

Klasyczny interfejs wiersza polecenia platformy Azure to świetny sposób na zarządzanie infrastrukturą platformy Azure z dowolnej platformy. W tym artykule pokazano, jak utworzyć pamięć podręczną Azure Cache dla wystąpień redis i zarządzać nią przy użyciu klasycznego interfejsu wiersza polecenia platformy Azure.

[!INCLUDE [outdated-cli-content](../../includes/contains-classic-cli-content.md)]
> [!NOTE]
> Aby uzyskać najnowsze przykładowe skrypty interfejsu wiersza polecenia platformy Azure, zobacz [przykłady usługi Azure CLI Azure Cache for Redis.](cli-samples.md)

## <a name="prerequisites"></a>Wymagania wstępne
Aby utworzyć pamięć podręczną Azure Cache dla wystąpień redis i zarządzać nimi przy użyciu klasycznego interfejsu wiersza polecenia platformy Azure, należy wykonać następujące kroki.

* Musisz mieć konto platformy Azure. Jeśli go nie masz, możesz utworzyć [darmowe konto](https://azure.microsoft.com/pricing/free-trial/) w ciągu kilku chwil.
* [Zainstaluj klasyczny interfejs wiersza polecenia platformy Azure](../cli-install-nodejs.md).
* Połącz swoją instalację interfejsu wiersza polecenia platformy Azure z osobistym kontem platformy Azure `azure login` lub z kontem platformy Azure w służbie pracy lub szkole, a następnie zaloguj się z klasycznego interfejsu wiersza polecenia za pomocą polecenia.
* Przed uruchomieniem któregokolwiek z następujących poleceń przełącz klasyczną `azure config mode arm` interfejs wiersza polecenia w tryb Menedżera zasobów, uruchamiając to polecenie. Aby uzyskać więcej informacji, zobacz [Zarządzanie zasobami i grupami zasobów platformy Azure za pomocą klasycznego interfejsu wiersza polecenia platformy Azure.](../xplat-cli-azure-resource-manager.md)

## <a name="azure-cache-for-redis-properties"></a>Właściwości usługi Azure Cache for Redis
Następujące właściwości są używane podczas tworzenia i aktualizowania pamięci podręcznej azure dla wystąpień Redis.

| Właściwość | Przełącznik | Opis |
| --- | --- | --- |
| name |-n, --nazwa |Nazwa pamięci podręcznej azure dla redis. |
| grupa zasobów |-g, --resource-group |Nazwa grupy zasobów. |
| location |-l, --lokalizacja |Lokalizacja do tworzenia pamięci podręcznej. |
| size |-z, --size |Rozmiar pamięci podręcznej platformy Azure dla redis. Prawidłowe wartości: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4] |
| sku |-x, --sku |Jednostka SKU Redis. Powinien być jednym z: [Basic, Standard, Premium] |
| EnableNonSslPort |-e, --enable-non-ssl-port |EnableNonSslPort właściwość usługi Azure Cache for Redis. Dodaj tę flagę, jeśli chcesz włączyć port nienawiązywany/SSL dla pamięci podręcznej |
| Konfiguracja Redis |-c, --redis-konfiguracja |Konfiguracja Redis. Wprowadź w tym miejscu sformatowany ciąg kluczy konfiguracji i wartości JSON. Format:"{"":"","":""}" |
| Konfiguracja Redis |-f, --redis-configuration-file |Konfiguracja Redis. Wprowadź tutaj ścieżkę pliku zawierającego klucze konfiguracji i wartości. Format wpisu pliku: {"":""":"""} |
| Liczba odłamków |-r, --shard-count |Liczba fragmentów do utworzenia w pamięci podręcznej klastra premium z klastrem. |
| Virtual Network |-v, --sieć wirtualna |Podczas hostowania pamięci podręcznej w sieci wirtualnej, określa dokładny identyfikator zasobu ARM sieci wirtualnej do wdrożenia pamięci podręcznej Azure cache dla redis w. Przykładowy format: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| typ klucza |-t, --key-type |Typ klucza do odnowienia. Prawidłowe wartości: [Podstawowy, Pomocniczy] |
| StatyczneIP |-p, --static-ip \<statyczne-ip\> |Podczas hostowania pamięci podręcznej w sieci wirtualnej, określa unikatowy adres IP w podsieci dla pamięci podręcznej. Jeśli nie podano, jeden jest wybrany dla Ciebie z podsieci. |
| Podsieć |t, --podsieć \<podsieci\> |Podczas hostowania pamięci podręcznej w sieci wirtualnej, określa nazwę podsieci, w którym można wdrożyć pamięć podręczną. |
| VirtualNetwork |-v, --wirtualna \<sieć wirtualna-sieć\> |Podczas hostowania pamięci podręcznej w sieci wirtualnej, określa dokładny identyfikator zasobu ARM sieci wirtualnej do wdrożenia pamięci podręcznej Azure cache dla redis w. Przykładowy format: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| Subskrypcja |-s, --subskrypcja |Identyfikator subskrypcji. |

## <a name="see-all-azure-cache-for-redis-commands"></a>Zobacz wszystkie polecenia Usługi Azure Cache for Redis
Aby wyświetlić wszystkie polecenia usługi Azure Cache for `azure rediscache -h` Redis i ich parametry, użyj polecenia.

    C:\>azure rediscache -h
    help:    Commands to manage your Azure Cache for Redis(s)
    help:
    help:    Create an Azure Cache for Redis
    help:      rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
    help:
    help:    Delete an existing Azure Cache for Redis
    help:      rediscache delete [--name <name> --resource-group <resource-group> ]
    help:
    help:    List all Azure Cache for Redis within your Subscription or Resource Group
    help:      rediscache list [options]
    help:
    help:    Show properties of an existing Azure Cache for Redis
    help:      rediscache show [--name <name> --resource-group <resource-group>]
    help:
    help:    Change settings of an existing Azure Cache for Redis
    help:      rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
    help:
    help:    Renew the authentication key for an existing Azure Cache for Redis
    help:      rediscache renew-key [--name <name> --resource-group <resource-group> ]
    help:
    help:    Lists Primary and Secondary key of an existing Azure Cache for Redis
    help:      rediscache list-keys [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help  output usage information
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="create-an-azure-cache-for-redis"></a>Tworzenie usługi Azure Cache for Redis
Aby utworzyć pamięć podręczną Azure cache dla programu Redis, należy użyć następującego polecenia:

    azure rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]

Aby uzyskać więcej informacji na `azure rediscache create -h` temat tego polecenia, uruchom polecenie.

    C:\>azure rediscache create -h
    help:    Create an Azure Cache for Redis
    help:
    help:    Usage: rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
    help:
    help:    Options:
    help:      -h, --help                                               output usage information
    help:      -v, --verbose                                            use verbose output
    help:      -vv                                                      more verbose with debug output
    help:      --json                                                   use json output
    help:      -n, --name <name>                                        Name of the Azure Cache for Redis.
    help:      -g, --resource-group <resource-group>                    Name of the Resource Group
    help:      -l, --location <location>                                Location to create cache.
    help:      -z, --size <size>                                        Size of the Azure Cache for Redis. Valid values: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4]
    help:      -x, --sku <sku>                                          Redis SKU. Should be one of : [Basic, Standard, Premium]
    help:      -e, --enable-non-ssl-port                                EnableNonSslPort property of the Azure Cache for Redis. Add this flag if you want to enable the non-TLS/SSL Port for your cache
    help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here. Format:"{"<key1>":"<value1>","<key2>":"<value2>"}"
    help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here. Format for the file entry: {"<key1>":"<value1>","<key2>":"<value2>"}
    help:      -r, --shard-count <shard-count>                          Number of Shards to create on a Premium Cluster Cache
    help:      -v, --virtual-network <virtual-network>                  The exact ARM resource ID of the virtual network to deploy the Azure Cache for Redis in. Example format: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1
    help:      -t, --subnet <subnet>                                    Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network
    help:      -p, --static-ip <static-ip>                              Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network
    help:      -s, --subscription <id>                                  the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="delete-an-existing-azure-cache-for-redis"></a>Usuwanie istniejącej pamięci podręcznej platformy Azure dla redis
Aby usunąć pamięć podręczną Azure cache dla programu Redis, należy użyć następującego polecenia:

    azure rediscache delete [--name <name> --resource-group <resource-group> ]

Aby uzyskać więcej informacji na `azure rediscache delete -h` temat tego polecenia, uruchom polecenie.

    C:\>azure rediscache delete -h
    help:    Delete an existing Azure Cache for Redis
    help:
    help:    Usage: rediscache delete [--name <name> --resource-group <resource-group> ]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Azure Cache for Redis.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which the cache exists
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="list-all-azure-cache-for-redis-within-your-subscription-or-resource-group"></a>Lista wszystkich zasobów pamięci podręcznej Azure dla redis w ramach subskrypcji lub grupy zasobów
Aby wyświetlić listę wszystkich pamięci podręcznej Azure cache dla funkcji Redis w ramach subskrypcji lub grupy zasobów, użyj następującego polecenia:

    azure rediscache list [options]

Aby uzyskać więcej informacji na `azure rediscache list -h` temat tego polecenia, uruchom polecenie.

    C:\>azure rediscache list -h
    help:    List all Azure Cache for Redis within your Subscription or Resource Group
    help:
    help:    Usage: rediscache list [options]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -g, --resource-group <resource-group>  Name of the Resource Group
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="show-properties-of-an-existing-azure-cache-for-redis"></a>Pokaż właściwości istniejącej pamięci podręcznej platformy Azure dla programu Redis
Aby wyświetlić właściwości istniejącej pamięci podręcznej Azure cache dla programu Redis, należy użyć następującego polecenia:

    azure rediscache show [--name <name> --resource-group <resource-group>]

Aby uzyskać więcej informacji na `azure rediscache show -h` temat tego polecenia, uruchom polecenie.

    C:\>azure rediscache show -h
    help:    Show properties of an existing Azure Cache for Redis
    help:
    help:    Usage: rediscache show [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Azure Cache for Redis.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

<a name="scale"></a>

## <a name="change-settings-of-an-existing-azure-cache-for-redis"></a>Zmienianie ustawień istniejącej pamięci podręcznej platformy Azure dla redis
Aby zmienić ustawienia istniejącej pamięci podręcznej Azure cache dla programu Redis, użyj następującego polecenia:

    azure rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]

Aby uzyskać więcej informacji na `azure rediscache set -h` temat tego polecenia, uruchom polecenie.

    C:\>azure rediscache set -h
    help:    Change settings of an existing Azure Cache for Redis
    help:
    help:    Usage: rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
    help:
    help:    Options:
    help:      -h, --help                                               output usage information
    help:      -v, --verbose                                            use verbose output
    help:      -vv                                                      more verbose with debug output
    help:      --json                                                   use json output
    help:      -n, --name <name>                                        Name of the Azure Cache for Redis.
    help:      -g, --resource-group <resource-group>                    Name of the Resource Group
    help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here.
    help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here.
    help:      -s, --subscription <subscription>                        the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="renew-the-authentication-key-for-an-existing-azure-cache-for-redis"></a>Odnawianie klucza uwierzytelniania istniejącej pamięci podręcznej platformy Azure dla programu Redis
Aby odnowić klucz uwierzytelniania dla istniejącej pamięci podręcznej Azure dla redis, należy użyć następującego polecenia:

    azure rediscache renew-key [--name <name> --resource-group <resource-group> --key-type <key-type>]

`Primary` Określ `Secondary` `key-type`lub dla .

Aby uzyskać więcej informacji na `azure rediscache renew-key -h` temat tego polecenia, uruchom polecenie.

    C:\>azure rediscache renew-key -h
    help:    Renew the authentication key for an existing Azure Cache for Redis
    help:
    help:    Usage: rediscache renew-key [--name <name> --resource-group <resource-group> ]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Azure Cache for Redis.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which cache exists
    help:      -t, --key-type <key-type>              type of key to renew. Valid values are: 'Primary', 'Secondary'.
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="list-primary-and-secondary-keys-of-an-existing-azure-cache-for-redis"></a>Lista kluczy podstawowych i pomocniczych istniejącej pamięci podręcznej platformy Azure dla redis
Aby wyświetlić listę kluczy podstawowych i pomocniczych istniejącej pamięci podręcznej platformy Azure dla programu Redis, należy użyć następującego polecenia:

    azure rediscache list-keys [--name <name> --resource-group <resource-group>]

Aby uzyskać więcej informacji na `azure rediscache list-keys -h` temat tego polecenia, uruchom polecenie.

    C:\>azure rediscache list-keys -h
    help:    Lists Primary and Secondary key of an existing Azure Cache for Redis
    help:
    help:    Usage: rediscache list-keys [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Azure Cache for Redis.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which Cache exists
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)
