---
title: Samouczek — przywracanie bazy danych SAP HANA na platformie Azure przy użyciu interfejsu wiersza polecenia
description: W tym samouczku dowiesz się, jak przywrócić bazy danych SAP HANA uruchomione na maszynie Wirtualnej platformy Azure z magazynu usług odzyskiwania kopii zapasowych platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure.
ms.topic: tutorial
ms.date: 12/4/2019
ms.openlocfilehash: 6dbe0c4382b648506d853feb281c70a8e8401595
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75472072"
---
# <a name="tutorial-restore-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Samouczek: Przywracanie baz danych SAP HANA na maszynie Wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure

Narzędzie CLI platformy Azure służy do tworzenia zasobów platformy Azure i zarządzania nimi z wiersza polecenia lub za pomocą skryptów. W tej dokumentacji opisano, jak przywrócić kopię zapasową bazy danych SAP HANA na maszynie Wirtualnej platformy Azure — przy użyciu interfejsu wiersza polecenia platformy Azure. Można również wykonać te kroki za pomocą [witryny Azure portal](https://docs.microsoft.com/azure/backup/sap-hana-db-restore).

Użyj [usługi Azure Cloud Shell](tutorial-sap-hana-backup-cli.md) do uruchamiania poleceń interfejsu wiersza polecenia.

Pod koniec tego samouczka będziesz mógł:

> [!div class="checklist"]
>
> * Wyświetlanie punktów przywracania dla bazy danych kopii zapasowej
> * Przywracanie bazy danych

W tym samouczku przyjęto założenie, że masz bazę danych SAP HANA uruchomionych na maszynie Wirtualnej platformy Azure, która jest kopią zapasową przy użyciu usługi Azure Backup. Jeśli użyto [kopii zapasowej bazy danych SAP HANA na platformie Azure przy użyciu interfejsu wiersza polecenia](tutorial-sap-hana-backup-cli.md) do utworzenia kopii zapasowej bazy danych SAP HANA, używasz następujących zasobów:

* grupa zasobów o nazwie *saphanaResourceGroup*
* skarbiec o nazwie *saphanaVault*
* chroniony kontener o nazwie *VMAppContainer; Oblicz;saphanaResourceGroup;saphanaVM*
* kopia zapasowa bazy danych/elementu o nazwie *saphanadatabase;hxe;hxe*
* zasobów w regionie *westus2*

## <a name="view-restore-points-for-a-backed-up-database"></a>Wyświetlanie punktów przywracania dla bazy danych kopii zapasowej

Aby wyświetlić listę wszystkich punktów odzyskiwania bazy danych, użyj polecenia cmdlet [listy kopii zapasowych az recoverypoint](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain) w następujący sposób:

```azurecli-interactive
az backup recoverypoint list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
   --output table
```

Lista punktów odzyskiwania będzie wyglądać następująco:

```output
Name                      Time                               BackupManagementType   Item Name               RecoveryPointType
-------------------       ---------------------------------  ---------------------  ----------------------  ------------------
7660777527047692711       2019-12-10T04:00:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Full
7896624824685666836       2019-12-15T10:33:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Differential
DefaultRangeRecoveryPoint                                    AzureWorkload          SAPHanaDtabase;hxe;hxe  Log
```

Jak widać, powyższa lista zawiera trzy punkty odzyskiwania: po jednym dla pełnej, różnicowej i kopii zapasowej dziennika.

>[!NOTE]
>Można również wyświetlić punkty początkowe i końcowe każdego nieprzerwanego łańcucha kopii zapasowej dziennika, używając polecenia cmdlet [az backup recoverypoint show-log-chain.](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain)

## <a name="prerequisites-to-restore-a-database"></a>Wymagania wstępne do przywrócenia bazy danych

Przed przywróceniem bazy danych upewnij się, że spełnione są następujące wymagania wstępne:

* Bazę danych można przywrócić tylko do wystąpienia SAP HANA, które znajduje się w tym samym regionie
* Wystąpienie docelowe musi być zarejestrowane w tym samym magazynie co źródło
* Usługa Azure Backup nie może zidentyfikować dwóch różnych wystąpień SAP HANA na tej samej maszynie wirtualnej. W związku z tym przywracanie danych z jednego wystąpienia do innego na tej samej maszynie wirtualnej nie jest możliwe.

## <a name="restore-a-database"></a>Przywracanie bazy danych

Usługa Azure Backup może przywrócić bazy danych SAP HANA, które są uruchomione na maszynach wirtualnych platformy Azure w następujący sposób:

* Przywróć do określonej daty lub godziny (do drugiej) przy użyciu kopii zapasowych dziennika. Usługa Azure Backup automatycznie określa odpowiednie pełne, różnicowe kopie zapasowe i łańcuch kopii zapasowych dziennika, które są wymagane do przywrócenia na podstawie wybranego czasu.
* Przywróć do określonej pełnej lub różnicowej kopii zapasowej, aby przywrócić do określonego punktu odzyskiwania.

Aby przywrócić bazę danych, należy użyć polecenia cmdlet [przywracania przywracania az,](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl) które wymaga obiektu konfiguracyjnego odzyskiwania jako jednego z danych wejściowych. Ten obiekt może być generowany przy użyciu polecenia cmdlet [az backup recoveryconfig show.](https://docs.microsoft.com/cli/azure/backup/recoveryconfig?view=azure-cli-latest#az-backup-recoveryconfig-show) Obiekt konfiguracji odzyskiwania zawiera wszystkie szczegóły do wykonania przywracania. Jednym z nich jest tryb przywracania - **OriginalWorkloadRestore** lub **AlternateWorkloadRestore**.

>[!NOTE]
> **OriginalWorkloadRestore** — przywracanie danych do tego samego wystąpienia SAP HANA jako oryginalnego źródła. Ta opcja zastępuje oryginalną bazę danych. <br>
> **AlternateWorkloadRestore** — przywraca bazę danych do lokalizacji alternatywnej i zachowaj oryginalną źródłową bazę danych.

## <a name="restore-to-alternate-location"></a>Przywracanie do lokalizacji alternatywnej

Aby przywrócić bazę danych do lokalizacji alternatywnej, należy użyć **AlternateWorkloadRestore** jako tryb przywracania. Następnie należy wybrać punkt przywracania, który może być poprzedni punkt w czasie lub którykolwiek z poprzednich punktów przywracania.

W tym samouczku przywrócisz do poprzedniego punktu przywracania. [Wyświetl listę punktów przywracania](#view-restore-points-for-a-backed-up-database) bazy danych i wybierz punkt, do którego chcesz przywrócić. W tym samouczku użyje się punktu przywracania o nazwie *7660777527047692711*.

Korzystając z powyższej nazwy punktu przywracania i trybu przywracania, utwórzmy obiekt konfiguracji odzyskiwania przy użyciu polecenia cmdlet [az backup recoveryconfig show.](https://docs.microsoft.com/cli/azure/backup/recoveryconfig?view=azure-cli-latest#az-backup-recoveryconfig-show) Przyjrzyjmy się, co oznacza każdy z pozostałych parametrów w tym cmdlet:

* **--target-item-name** Jest to nazwa, która będzie używać przywróconej bazy danych. W tym przypadku użyliśmy nazwy *restored_database*.
* **--target-server-name** Jest to nazwa serwera SAP HANA, który został pomyślnie zarejestrowany w magazynie usług odzyskiwania i znajduje się w tym samym regionie co baza danych, która ma zostać przywrócona. W tym samouczku przywrócimy bazę danych do tego samego serwera SAP HANA, który mamy chronione, o nazwie *hxehost*.
* **--target-server-type** Do przywracania baz danych SAP HANA należy użyć **saphanadatabase.**

```azurecli-interactive

az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode AlternateWorkloadRestore \
    --rp-name 7660777527047692711 \
    --target-item-name restored_database \
    --target-server-name hxehost \
    --target-server-type HANAInstance \
    --workload-type SAPHANA \
    --output json
```

Odpowiedzią na powyższe zapytanie będzie obiekt konfiguracji odzyskiwania, który wygląda mniej więcej tak:

```output
"{\"restore_mode\": \"OriginalLocation\", \"container_uri\": \" VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \", \"item_uri\": \"SAPHanaDatabase;hxe;hxe\", \"recovery_point_id\": \"DefaultRangeRecoveryPoint\", \"log_point_in_time\": \"28-11-2019-09:53:00\", \"item_type\": \"SAPHana\", \"source_resource_id\": \"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm\", \"database_name\": null, \"container_id\": null, \"alternate_directory_paths\": null}"
```

Teraz, aby przywrócić bazy danych uruchom [az restore restore-azurewl](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl) polecenia cmdlet. Aby użyć tego polecenia, wprowadzimy powyższe wyjście json, które jest zapisywane w pliku o nazwie *recoveryconfig.json*.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output table
```

Dane wyjściowe będą wyglądać następująco:

```output
Name                                  Resource
------------------------------------  -------
5b198508-9712-43df-844b-977e5dfc30ea  SAPHANA
```

Odpowiedź poda nazwę zadania. Ta nazwa zadania może służyć do śledzenia stanu zadania przy użyciu polecenia cmdlet [az backup job show.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)

## <a name="restore-and-overwrite"></a>Przywracanie i zastępowanie

Aby przywrócić do oryginalnej lokalizacji, użyjemy **OrignialWorkloadRestore** jako tryb przywracania. Następnie należy wybrać punkt przywracania, który może być poprzedni punkt w czasie lub którykolwiek z poprzednich punktów przywracania.

W tym samouczku wybierzemy poprzedni punkt w czasie "28-11-2019-09:53:00", aby przywrócić. Ten punkt przywracania można podać w następujących formatach: dd-mm-yyyy, dd-mm-yyyy-hh:mm:ss. Aby wybrać prawidłową wstawienie w czasie do przywrócenia, użyj polecenia cmdlet [az backup recoverypoint show-log-chain,](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain) który zawiera listę interwałów nieprzerwanych kopii zapasowych łańcucha dziennika.

```azurecli-interactive
az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode OriginalWorkloadRestore \
    --log-point-in-time 28-11-2019-09:53:00 \
    --output json
```

Odpowiedzią na powyższe zapytanie będzie obiekt konfiguracji odzyskiwania, który wygląda następująco:

```output
"{\"restore_mode\": \"OriginalLocation\", \"container_uri\": \" VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \", \"item_uri\": \"SAPHanaDatabase;hxe;hxe\", \"recovery_point_id\": \"DefaultRangeRecoveryPoint\", \"log_point_in_time\": \"28-11-2019-09:53:00\", \"item_type\": \"SAPHana\", \"source_resource_id\": \"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm\", \"database_name\": null, \"container_id\": null, \"alternate_directory_paths\": null}"
```

Teraz, aby przywrócić bazy danych uruchom [az restore restore-azurewl](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl) polecenia cmdlet. Aby użyć tego polecenia, wprowadzimy powyższe wyjście json, które jest zapisywane w pliku o nazwie *recoveryconfig.json*.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output table
```

Dane wyjściowe będą wyglądać następująco:

```output
Name                                  Resource
------------------------------------  --------
5b198508-9712-43df-844b-977e5dfc30ea  SAPHANA
```

Odpowiedź poda nazwę zadania. Ta nazwa zadania może służyć do śledzenia stanu zadania przy użyciu polecenia cmdlet [zadania kopii zapasowej az.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak zarządzać bazami danych SAP HANA, których kopię zapasową jest przy użyciu interfejsu wiersza polecenia platformy Azure, przejdź do [samouczka Zarządzanie bazą danych SAP HANA na maszynie Wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia](tutorial-sap-hana-backup-cli.md)

* Aby dowiedzieć się, jak przywrócić bazę danych SAP HANA działającą na maszynie Wirtualnej platformy Azure przy użyciu portalu Azure, zobacz [Przywracanie baz danych SAP HANA na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
