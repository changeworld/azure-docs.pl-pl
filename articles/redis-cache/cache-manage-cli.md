---
title: Zarządzanie pamięcią podręczną Redis Azure za pomocą wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zainstalować wiersza polecenia platformy Azure na dowolnej platformie, jak z niego korzystać do łączenia się z kontem platformy Azure i sposobu tworzenia i zarządzania nimi pamięci podręcznej Redis z wiersza polecenia platformy Azure.
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
ms.openlocfilehash: fdb0989af2215166b69f10474a0d22aab7b4d593
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2018
ms.locfileid: "27911282"
---
# <a name="how-to-create-and-manage-azure-redis-cache-using-the-azure-command-line-interface-azure-cli"></a>Jak utworzyć i zarządzać pamięcią podręczną Redis Azure za pomocą interfejsu wiersza polecenia platformy Azure (Azure CLI)
> [!div class="op_single_selector"]
> * [Program PowerShell](cache-howto-manage-redis-cache-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure](cache-manage-cli.md)
>
>

Azure CLI jest doskonałym sposobem zarządzających infrastrukturą platformy Azure z dowolną platformą. W tym artykule przedstawiono sposób tworzenia i zarządzania nimi z wystąpień pamięci podręcznej Redis Azure za pomocą wiersza polecenia platformy Azure.

> [!NOTE]
> Ten artykuł dotyczy poprzedniej wersji interfejsu wiersza polecenia Azure. Uzyskać najnowsze Azure CLI 2.0 przykładowe skrypty, zobacz [przykłady pamięci podręcznej Azure CLI Redis](cli-samples.md).
> 
> 

## <a name="prerequisites"></a>Wymagania wstępne
Tworzenie i zarządzanie nimi wystąpień pamięci podręcznej Redis Azure za pomocą interfejsu wiersza polecenia Azure, wykonaj następujące kroki.

* Musi mieć konto platformy Azure. Jeśli nie masz, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) za kilka minut.
* [Instalowanie interfejsu wiersza polecenia platformy Azure](../cli-install-nodejs.md).
* Połącz instalacji wiersza polecenia platformy Azure z osobistego konta Azure lub z firmowego lub szkolnego konta platformy Azure i zaloguj z wiersza polecenia platformy Azure przy użyciu `azure login` polecenia. Aby poznać różnice, a następnie wybierz pozycję, zobacz [Połącz z subskrypcją platformy Azure z interfejsu wiersza polecenia platformy Azure (Azure CLI)](/cli/azure/authenticate-azure-cli).
* Przed uruchomieniem dowolne z poniższych poleceń, przełącznik wiersza polecenia platformy Azure w trybie Menedżera zasobów, uruchamiając `azure config mode arm` polecenia. Aby uzyskać więcej informacji, zobacz [użyć wiersza polecenia platformy Azure do zarządzania zasobami Azure i grup zasobów](../xplat-cli-azure-resource-manager.md).

## <a name="redis-cache-properties"></a>Właściwości pamięci podręcznej redis
Następujące właściwości są używane podczas tworzenia i aktualizowania wystąpienia pamięci podręcznej Redis.

| Właściwość | Przełącznik | Opis |
| --- | --- | --- |
| name |-n, — nazwa |Nazwa pamięci podręcznej Redis. |
| grupa zasobów |-g,--grupy zasobów |Nazwa grupy zasobów. |
| location |-l, — lokalizacja |Lokalizacja, w celu tworzenia pamięci podręcznej. |
| rozmiar |-z, — rozmiar |Rozmiar pamięci podręcznej Redis. Prawidłowe wartości: [C0 C1, C2, C3, C4, C5, C6, P1, P2, P3, P4] |
| sku |-x, --sku |W pamięci podręcznej redis jednostki SKU. Powinien być jednym z: [Basic, Standard, Premium] |
| EnableNonSslPort |-e, - enable bez protokołu ssl portu |Właściwość EnableNonSslPort pamięci podręcznej Redis. Dodaj tę flagę, aby włączyć Port bez protokołu SSL dla pamięci podręcznej |
| Redis konfiguracji |-c,--konfiguracja pamięci podręcznej redis |W pamięci podręcznej redis konfiguracji. Wprowadź ciąg w formacie JSON konfiguracji kluczy i wartości w tym miejscu. Format: "{" ":""," ":" "}" |
| Redis konfiguracji |-f, — plik w przypadku konfiguracji pamięci podręcznej redis |W pamięci podręcznej redis konfiguracji. Wprowadź ścieżkę pliku zawierającego konfiguracji kluczy i wartości w tym miejscu. Wpis w pliku formatu: {"": "","": ""} |
| Liczba niezależnych |-r--niezależnego fragmentu-, count, liczba |Liczba fragmentów, aby utworzyć na pamięć podręczna Premium klastrowania z klastra. |
| Virtual Network |-v, — sieci wirtualnej |Odnośnie do hostowania pamięci podręcznej w sieci Wirtualnej, określa dokładne ARM identyfikator zasobu sieci wirtualnej do pamięci podręcznej redis w wdrożenia. Przykładowy format: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| Typ klucza |-t, — typ klucza |Typ klucza do odnowienia. Prawidłowe wartości: [podstawową, dodatkowej] |
| StaticIP |-p,--static ip < static ip > |Podczas obsługi pamięci podręcznej w sieci Wirtualnej, określa unikatowy adres IP w podsieci dla pamięci podręcznej. Jeśli nie zostanie podana, co zostanie wybrany dla Ciebie podsieci. |
| Podsieć |t, --subnet <subnet> |Podczas obsługi pamięci podręcznej w sieci Wirtualnej, określa nazwę podsieci, w której chcesz wdrożyć w pamięci podręcznej. |
| VirtualNetwork |-v, — sieci wirtualnej <-sieci wirtualnej > |Odnośnie do hostowania pamięci podręcznej w sieci Wirtualnej, określa dokładne ARM identyfikator zasobu sieci wirtualnej do pamięci podręcznej redis w wdrożenia. Przykładowy format: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| Subskrypcja |-s, - subskrypcji |Identyfikator subskrypcji. |

## <a name="see-all-redis-cache-commands"></a>Zobacz wszystkie polecenia pamięci podręcznej Redis
Aby wyświetlić wszystkie polecenia pamięci podręcznej Redis i ich parametry, użyj `azure rediscache -h` polecenia.

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
Aby utworzyć pamięci podręcznej Redis, użyj następującego polecenia:

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

## <a name="delete-an-existing-redis-cache"></a>Usuń istniejące pamięci podręcznej Redis
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

## <a name="list-all-redis-caches-within-your-subscription-or-resource-group"></a>Wyświetl listę wszystkich pamięci podręczne Redis w ramach Twojej subskrypcji lub grupy zasobów
Aby wyświetlić listę wszystkich pamięci podręczne Redis w ramach Twojej subskrypcji lub grupy zasobów, użyj następującego polecenia:

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
Aby wyświetlić właściwości istniejącej pamięci podręcznej Redis, użyj następującego polecenia:

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

## <a name="change-settings-of-an-existing-redis-cache"></a>Zmień ustawienia istniejących pamięci podręcznej Redis
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

## <a name="renew-the-authentication-key-for-an-existing-redis-cache"></a>Odnawianie klucz uwierzytelniania dla istniejącej pamięci podręcznej Redis
Aby odnowić klucz uwierzytelniania dla istniejącej pamięci podręcznej Redis, użyj następującego polecenia:

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

## <a name="list-primary-and-secondary-keys-of-an-existing-redis-cache"></a>Lista podstawowe i pomocnicze klucze istniejącej pamięci podręcznej Redis
Lista podstawowe i pomocnicze klucze istniejącej pamięci podręcznej Redis Użyj następującego polecenia:

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
