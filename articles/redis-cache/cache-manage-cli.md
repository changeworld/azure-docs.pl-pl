---
title: Zarządzanie usługi Azure Redis Cache za pomocą klasycznego wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zainstalować klasyczne wiersza polecenia platformy Azure na dowolnej platformie, jak z niej korzystać, aby nawiązać połączenie z kontem platformy Azure i jak tworzyć i zarządzać pamięci podręcznej Redis z klasyczny interfejs wiersza polecenia.
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 964ff245-859d-4bc1-bccf-62e4b3c1169f
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: wesmc
ms.openlocfilehash: 0e8bbaad920f35028c51641779a3272f73f81f37
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978441"
---
# <a name="how-to-create-and-manage-azure-redis-cache-using-the-azure-classic-cli"></a>Jak tworzyć i zarządzać nimi w usłudze Azure Redis Cache za pomocą klasycznego wiersza polecenia platformy Azure
> [!div class="op_single_selector"]
> * [Program PowerShell](cache-howto-manage-redis-cache-powershell.md)
> * [Klasyczny interfejs wiersza polecenia Azure](cache-manage-cli.md)
>

Klasyczny interfejs wiersza polecenia Azure jest doskonałym sposobem na zarządzanie infrastrukturą platformy Azure z dowolnej platformy. W tym artykule przedstawiono sposób tworzenia i zarządzania nimi swoich wystąpień usługi Azure Redis Cache za pomocą klasycznego wiersza polecenia platformy Azure.

[!INCLUDE [outdated-cli-content](../../includes/contains-classic-cli-content.md)]
> [!NOTE]
> Aby uzyskać najnowsze wiersza polecenia platformy Azure przykładowe skrypty, zobacz [przykłady użycia pamięci podręcznej Redis interfejsu wiersza polecenia platformy Azure](cli-samples.md).

## <a name="prerequisites"></a>Wymagania wstępne
Do tworzenia i zarządzania jego wystąpieniami usługi Azure Redis Cache za pomocą klasycznego wiersza polecenia platformy Azure, wykonaj następujące kroki.

* Musisz mieć konto platformy Azure. Jeśli nie masz, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) za kilka minut.
* [Instalowanie platformy Azure klasyczny interfejs wiersza polecenia](../cli-install-nodejs.md).
* Połącz instalacji wiersza polecenia platformy Azure przy użyciu osobistego konta platformy Azure lub przy użyciu służbowego lub służbowe konto platformy Azure i zaloguj się z klasycznym przy użyciu interfejsu wiersza polecenia `azure login` polecenia.
* Przed uruchomieniem dowolne z następujących poleceń, Przełącz klasyczny interfejs wiersza polecenia w trybie Menedżera zasobów, uruchamiając `azure config mode arm` polecenia. Aby uzyskać więcej informacji, zobacz [użycie platformy Azure klasyczny interfejs wiersza polecenia do zarządzania zasobami i grupami zasobów platformy Azure](../xplat-cli-azure-resource-manager.md).

## <a name="redis-cache-properties"></a>Właściwości pamięci podręcznej redis
Następujące właściwości są używane podczas tworzenia i aktualizowania wystąpienia pamięci podręcznej Redis.

| Właściwość | Przełącznik | Opis |
| --- | --- | --- |
| name |-n,--name |Nazwa pamięci podręcznej Redis. |
| grupa zasobów |-g,--grupy zasobów |Nazwa grupy zasobów. |
| location |-l,--lokalizacji |Lokalizacja, aby utworzyć pamięć podręczną. |
| rozmiar |-z, — rozmiar |Rozmiar pamięci podręcznej Redis. Prawidłowe wartości: [C0 C1, C2, C3, C4, C5, C6, P1, P2, P3, P4] |
| sku |-x, --sku |Usługa redis jednostki SKU. Powinien być jednym z: [podstawowa, standardowa, Premium] |
| EnableNonSslPort |-e,--enable bez protokołu ssl — portu |Właściwość EnableNonSslPort pamięci podręcznej Redis. Dodaj tę flagę, jeśli chcesz włączyć Port bez protokołu SSL dla pamięci podręcznej |
| Konfigurowanie pamięci podręcznej redis |-c, — konfiguracja pamięci podręcznej redis |Konfigurowanie pamięci podręcznej redis. Wprowadź ciąg formatu JSON konfiguracji kluczy i wartości w tym miejscu. Format: "{" ":""," ":" "}" |
| Konfigurowanie pamięci podręcznej redis |-f, — plik w przypadku konfiguracji pamięci podręcznej redis |Konfigurowanie pamięci podręcznej redis. Wprowadź ścieżkę pliku zawierającego kluczy i wartości w tym miejscu konfiguracji. Format wpisu pliku: {"": "","": ""} |
| Liczba fragmentów |-r,--liczba fragmentów |Liczba fragmentów, aby utworzyć na Cache klaster w warstwie Premium z usługą klastrowania. |
| Virtual Network |-v,--sieci wirtualnej |W przypadku hostowania w sieci Wirtualnej pamięci podręcznej, określa dokładną ARM identyfikator zasobu sieci wirtualnej do wdrożenia pamięci podręcznej redis w. Przykładowy format: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| Typ klucza |-t, — typ klucza |Typ klucza do odnowienia. Prawidłowe wartości: [podstawowego, pomocniczego] |
| StaticIP |-p,--static ip < static ip > |W przypadku hostowania w sieci Wirtualnej pamięci podręcznej, określa unikatowy adres IP w podsieci dla pamięci podręcznej. Jeśli nie zostanie podana, jeden jest wybierany automatycznie z podsieci. |
| Podsieć |t, — podsieci <subnet> |W przypadku hostowania w sieci Wirtualnej pamięci podręcznej, określa nazwę podsieci, w której ma zostać wdrożony w pamięci podręcznej. |
| VirtualNetwork |-v,--sieci wirtualnej <-sieci wirtualnej > |W przypadku hostowania w sieci Wirtualnej pamięci podręcznej, określa dokładną ARM identyfikator zasobu sieci wirtualnej do wdrożenia pamięci podręcznej redis w. Przykładowy format: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| Subskrypcja |-s,--subskrypcji |Identyfikator subskrypcji. |

## <a name="see-all-redis-cache-commands"></a>Zobacz wszystkie polecenia Redis Cache
Aby wyświetlić wszystkie polecenia Redis Cache i ich parametrów, należy użyć `azure rediscache -h` polecenia.

    C:\>azure rediscache -h
    help:    Commands to manage your Azure Redis Cache(s)
    help:
    help:    Create a Redis Cache
    help:      rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
    help:
    help:    Delete an existing Redis Cache
    help:      rediscache delete [--name <name> --resource-group <resource-group> ]
    help:
    help:    List all Redis Caches within your Subscription or Resource Group
    help:      rediscache list [options]
    help:
    help:    Show properties of an existing Redis Cache
    help:      rediscache show [--name <name> --resource-group <resource-group>]
    help:
    help:    Change settings of an existing Redis Cache
    help:      rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
    help:
    help:    Renew the authentication key for an existing Redis Cache
    help:      rediscache renew-key [--name <name> --resource-group <resource-group> ]
    help:
    help:    Lists Primary and Secondary key of an existing Redis Cache
    help:      rediscache list-keys [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help  output usage information
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="create-a-redis-cache"></a>Tworzenie pamięci podręcznej Redis Cache
Aby utworzyć pamięć podręczną Redis, użyj następującego polecenia:

    azure rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]

Aby uzyskać więcej informacji na temat tego polecenia, uruchom `azure rediscache create -h` polecenia.

    C:\>azure rediscache create -h
    help:    Create a Redis Cache
    help:
    help:    Usage: rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
    help:
    help:    Options:
    help:      -h, --help                                               output usage information
    help:      -v, --verbose                                            use verbose output
    help:      -vv                                                      more verbose with debug output
    help:      --json                                                   use json output
    help:      -n, --name <name>                                        Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>                    Name of the Resource Group
    help:      -l, --location <location>                                Location to create cache.
    help:      -z, --size <size>                                        Size of the Redis Cache. Valid values: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4]
    help:      -x, --sku <sku>                                          Redis SKU. Should be one of : [Basic, Standard, Premium]
    help:      -e, --enable-non-ssl-port                                EnableNonSslPort property of the Redis Cache. Add this flag if you want to enable the Non SSL Port for your cache
    help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here. Format:"{"<key1>":"<value1>","<key2>":"<value2>"}"
    help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here. Format for the file entry: {"<key1>":"<value1>","<key2>":"<value2>"}
    help:      -r, --shard-count <shard-count>                          Number of Shards to create on a Premium Cluster Cache
    help:      -v, --virtual-network <virtual-network>                  The exact ARM resource ID of the virtual network to deploy the redis cache in. Example format: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1
    help:      -t, --subnet <subnet>                                    Required when deploying a redis cache inside an existing Azure Virtual Network
    help:      -p, --static-ip <static-ip>                              Required when deploying a redis cache inside an existing Azure Virtual Network
    help:      -s, --subscription <id>                                  the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="delete-an-existing-redis-cache"></a>Usuń istniejące pamięć podręczną Redis
Aby usunąć pamięci podręcznej Redis, użyj następującego polecenia:

    azure rediscache delete [--name <name> --resource-group <resource-group> ]

Aby uzyskać więcej informacji na temat tego polecenia, uruchom `azure rediscache delete -h` polecenia.

    C:\>azure rediscache delete -h
    help:    Delete an existing Redis Cache
    help:
    help:    Usage: rediscache delete [--name <name> --resource-group <resource-group> ]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which the cache exists
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="list-all-redis-caches-within-your-subscription-or-resource-group"></a>Lista wszystkich Redis Cache w ramach Twojej subskrypcji lub grupy zasobów
Aby wyświetlić listę wszystkich Redis Cache w ramach Twojej subskrypcji lub grupy zasobów, użyj następującego polecenia:

    azure rediscache list [options]

Aby uzyskać więcej informacji na temat tego polecenia, uruchom `azure rediscache list -h` polecenia.

    C:\>azure rediscache list -h
    help:    List all Redis Caches within your Subscription or Resource Group
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

## <a name="show-properties-of-an-existing-redis-cache"></a>Pokaż właściwości istniejącej pamięci podręcznej Redis
Aby wyświetlić właściwości istniejącej pamięci podręcznej redis cache, użyj następującego polecenia:

    azure rediscache show [--name <name> --resource-group <resource-group>]

Aby uzyskać więcej informacji na temat tego polecenia, uruchom `azure rediscache show -h` polecenia.

    C:\>azure rediscache show -h
    help:    Show properties of an existing Redis Cache
    help:
    help:    Usage: rediscache show [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

<a name="scale"></a>

## <a name="change-settings-of-an-existing-redis-cache"></a>Zmień ustawienia istniejącej pamięci podręcznej Redis
Aby zmienić ustawienia istniejących pamięci podręcznej Redis, użyj następującego polecenia:

    azure rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]

Aby uzyskać więcej informacji na temat tego polecenia, uruchom `azure rediscache set -h` polecenia.

    C:\>azure rediscache set -h
    help:    Change settings of an existing Redis Cache
    help:
    help:    Usage: rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
    help:
    help:    Options:
    help:      -h, --help                                               output usage information
    help:      -v, --verbose                                            use verbose output
    help:      -vv                                                      more verbose with debug output
    help:      --json                                                   use json output
    help:      -n, --name <name>                                        Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>                    Name of the Resource Group
    help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here.
    help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here.
    help:      -s, --subscription <subscription>                        the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="renew-the-authentication-key-for-an-existing-redis-cache"></a>Odnów klucz uwierzytelniania dla istniejącej pamięci podręcznej Redis
Aby odnowić klucz uwierzytelniania dla istniejącej pamięci podręcznej redis cache, użyj następującego polecenia:

    azure rediscache renew-key [--name <name> --resource-group <resource-group> --key-type <key-type>]

Określ `Primary` lub `Secondary` dla `key-type`.

Aby uzyskać więcej informacji na temat tego polecenia, uruchom `azure rediscache renew-key -h` polecenia.

    C:\>azure rediscache renew-key -h
    help:    Renew the authentication key for an existing Redis Cache
    help:
    help:    Usage: rediscache renew-key [--name <name> --resource-group <resource-group> ]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which cache exists
    help:      -t, --key-type <key-type>              type of key to renew. Valid values are: 'Primary', 'Secondary'.
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="list-primary-and-secondary-keys-of-an-existing-redis-cache"></a>Lista podstawowe i pomocnicze klucze istniejąca pamięć podręczna Redis
Na liście podstawowe i pomocnicze klucze istniejącej pamięci podręcznej redis Cache Użyj następującego polecenia:

    azure rediscache list-keys [--name <name> --resource-group <resource-group>]

Aby uzyskać więcej informacji na temat tego polecenia, uruchom `azure rediscache list-keys -h` polecenia.

    C:\>azure rediscache list-keys -h
    help:    Lists Primary and Secondary key of an existing Redis Cache
    help:
    help:    Usage: rediscache list-keys [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which Cache exists
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)
