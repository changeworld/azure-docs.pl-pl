---
title: Samouczek — kopia zapasowa SAP HANA DB na platformie Azure przy użyciu interfejsu wiersza polecenia
description: W tym samouczku dowiesz się, jak wykonać kopię zapasową baz danych SAP HANA uruchomionych na maszynie Wirtualnej Platformy Azure w magazynie usług odzyskiwania kopii zapasowych platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure.
ms.topic: tutorial
ms.date: 12/4/2019
ms.openlocfilehash: cdc8a8fb09a086a2b9212c21d071f267991fa275
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78206626"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Samouczek: Tworzenie kopii zapasowych baz danych SAP HANA na maszynie Wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure

Narzędzie CLI platformy Azure służy do tworzenia zasobów platformy Azure i zarządzania nimi z wiersza polecenia lub za pomocą skryptów. Ta dokumentacja zawiera szczegółowe informacje, jak wykonać kopię zapasową bazy danych SAP HANA i wyzwalać kopie zapasowe na żądanie — wszystkie przy użyciu interfejsu wiersza polecenia platformy Azure. Można również wykonać te kroki za pomocą [witryny Azure portal](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database).

W tym dokumencie przyjęto założenie, że masz już zainstalowaną bazę danych SAP HANA na maszynie wirtualnej platformy Azure. (Można również [utworzyć maszynę wirtualną przy użyciu interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-cli)). Pod koniec tego samouczka, będziesz mógł:

> [!div class="checklist"]
>
> * Tworzenie magazynu usługi Recovery Services
> * Zarejestruj wystąpienie SAP HANA i odnajduj na nim bazy danych
> * Włączanie tworzenia kopii zapasowych w bazie danych SAP HANA
> * Wyzwalanie kopii zapasowej na żądanie

Zapoznaj się ze [scenariuszami, które obecnie obsługujemy](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support) dla sap HANA.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Aby zainstalować i używać interfejsu wiersza polecenia lokalnie, należy uruchomić wersję interfejsu wiersza polecenia platformy Azure xx.xxx.x lub nowszą. Aby znaleźć wersję interfejsu wiersza polecenia, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

Magazyn usług odzyskiwania to kontener logiczny, który przechowuje dane kopii zapasowej dla każdego chronionego zasobu, takiego jak maszyny wirtualne platformy Azure lub obciążenia uruchomione na maszynach wirtualnych platformy Azure — takie jak bazy danych SQL lub HANA. Gdy zadanie tworzenia kopii zapasowej chronionego zasobu zostaje uruchomione, tworzony jest punkt odzyskiwania w magazynie usługi Recovery Services. Następnie można użyć jednego z tych punktów odzyskiwania w celu przywrócenia danych do danego punktu w czasie.

Utwórz magazyn usługi Recovery Services za pomocą polecenia [az backup vault create](https://docs.microsoft.com/cli/azure/backup/vault#az-backup-vault-create). Określ taką samą grupę zasobów i lokalizację, jak w przypadku maszyny wirtualnej, która ma być chroniona. Dowiedz się, jak utworzyć maszynę wirtualną przy użyciu narzędzia Azure CLI za pomocą tego [przewodnika Szybki start na maszynie Wirtualnej.](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-cli)

W tym samouczku będziemy używać następujących czynności:

* grupa zasobów o nazwie *saphanaResourceGroup*
* maszyna wirtualna o nazwie *saphanaVM*
* zasobów w lokalizacji *westus2.*

Będziemy tworzyć skarbiec o nazwie *saphanaVault*.

```azurecli-interactive
az backup vault create --resource-group saphanaResourceGroup \
    --name saphanaVault \
    --location westus2
```

Domyślnie magazyn usługi Recovery Services jest ustawiony na magazyn geograficznie nadmiarowy. Magazyn geograficznie nadmiarowy gwarantuje, że dane kopii zapasowej są replikowane do dodatkowego regionu świadczenia usługi Azure, który znajduje się setki kilometrów od regionu podstawowego. Jeśli należy zmodyfikować ustawienie nadmiarowości magazynu, należy użyć polecenia cmdlet [właściwości magazynu kopii zapasowej az.](https://docs.microsoft.com/cli/azure/backup/vault/backup-properties?view=azure-cli-latest#az-backup-vault-backup-properties-set)

```azurecli
az backup vault backup-properties set \
    --name saphanaVault  \
    --resource-group saphanaResourceGroup \
    --backup-storage-redundancy "LocallyRedundant/GeoRedundant"
```

Aby sprawdzić, czy przechowalnia została pomyślnie utworzona, użyj polecenia cmdlet [listy kopii zapasowych az.](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-list) Zobaczysz następującą odpowiedź:

```output
Location   Name             ResourceGroup
---------  ---------------  -------------  
westus2    saphanaVault     saphanaResourceGroup
```

## <a name="register-and-protect-the-sap-hana-instance"></a>Zarejestruj i chroń wystąpienie SAP HANA

Dla wystąpienia SAP HANA (maszyny Wirtualnej z SAP HANA zainstalowany na nim) do wykrycia przez usługi platformy Azure, [skrypt rejestracji wstępnej](https://aka.ms/scriptforpermsonhana) musi być uruchomiony na komputerze SAP HANA. Upewnij się, że wszystkie [wymagania wstępne](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#prerequisites) są spełnione przed uruchomieniem skryptu. Aby dowiedzieć się więcej o tym, co robi skrypt, zapoznaj się z sekcją [Co skrypt rejestracji wstępnej.](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)

Po uruchomieniu skryptu wystąpienie SAP HANA można zarejestrować w magazynie usług odzyskiwania, który utworzyliśmy wcześniej. Aby zarejestrować wystąpienie, użyj polecenia cmdlet [rejestru kontenerów kopii zapasowej az.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-register) *VMResourceId* jest identyfikatorem zasobu maszyny Wirtualnej utworzonej w celu zainstalowania sap HANA.

```azurecli-interactive
az backup container register --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --location westus2 \
    --workload-type SAPHANA \
    --backup-management-type AzureWorkload \
    --resource-id VMResourceId
```

>[!NOTE]
>Jeśli maszyna wirtualna nie znajduje się w tej samej grupie zasobów co magazyn, *saphanaResourceGroup* odwołuje się do grupy zasobów, w której utworzono przechowalnię.

Rejestrowanie wystąpienia SAP HANA automatycznie odnajduje wszystkie jego bieżące bazy danych. Jednak aby odkryć nowe bazy danych, które mogą zostać dodane w przyszłości, odnoszą się do [odkrywania nowych baz danych dodanych do sekcji wystąpienia zarejestrowanego sap HANA.](tutorial-sap-hana-manage-cli.md#protect-new-databases-added-to-an-sap-hana-instance)

Aby sprawdzić, czy wystąpienie SAP HANA jest pomyślnie zarejestrowane w magazynie, użyj polecenia cmdlet [listy kontenerów kopii zapasowych az.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) Zobaczysz następującą odpowiedź:

```output
Name                                                    Friendly Name    Resource Group        Type           Registration Status
------------------------------------------------------  --------------   --------------------  ---------      ----------------------
VMAppContainer;Compute;saphanaResourceGroup;saphanaVM   saphanaVM        saphanaResourceGroup  AzureWorkload  Registered
```

>[!NOTE]
> Kolumna "name" w powyższym pliku danych danych odnosi się do nazwy kontenera. Ta nazwa kontenera będzie używana w następnych sekcjach, aby włączyć kopie zapasowe i wyzwolić je. Który w tym przypadku jest *VMAppContainer; Compute;saphanaResourceGroup;saphanaVM*.

## <a name="enable-backup-on-sap-hana-database"></a>Włączanie tworzenia kopii zapasowych w bazie danych SAP HANA

Polecenie cmdlet [listy chronionych elementów az](https://docs.microsoft.com/cli/azure/backup/protectable-item?view=azure-cli-latest#az-backup-protectable-item-list) zawiera listę wszystkich baz danych wykrytych w wystąpieniu SAP HANA zarejestrowanym w poprzednim kroku.

```azurecli-interactive
az backup protectable-item list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --workload-type SAPHANA \
    --output table
```

Na tej liście powinna znajdować się baza danych, której chcesz szepnac.

```output
Name                           Protectable Item Type    ParentName    ServerName    IsProtected
-----------------------------  ----------------------   ------------  -----------   ------------
saphanasystem;hxe              SAPHanaSystem            HXE           hxehost       NotProtected  
saphanadatabase;hxe;systemdb   SAPHanaDatabase          HXE           hxehost       NotProtected
saphanadatabase;hxe;hxe        SAPHanaDatabase          HXE           hxehost       NotProtected
```

Jak widać z powyższego wyjścia, identyfikator SID systemu SAP HANA to HXE. W tym samouczku skonfigurujemy kopię zapasową dla bazy danych *saphanadatabase;hxe;hxe,* która znajduje się na serwerze *hxehost.*

Aby chronić i skonfigurować kopię zapasową w bazie danych, po jednym na raz, używamy polecenia cmdlet [az backup protection enable-for-azurewl.](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-enable-for-azurewl) Podaj nazwę zasad, których chcesz użyć. Aby utworzyć zasadę przy użyciu interfejsu wiersza polecenia, użyj polecenia cmdlet [tworzenia zasad kopii zapasowej az.](https://docs.microsoft.com//cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-create) W tym samouczku będziemy używać *sapahanaPolicy* zasady.

```azurecli-interactive
az backup protection enable-for-azurewl --resource-group saphanaResourceGroup \
    --policy-name saphanaPolicy \
    --protectable-item-name saphanadatabase;hxe;hxe  \
    --protectable-item-type SAPHANADatabse \
    --server-name hxehost \
    --workload-type SAPHANA \
    --output table
```

Można sprawdzić, czy powyższa konfiguracja kopii zapasowej została ukończona przy użyciu polecenia cmdlet [listy zadań kopii zapasowej az.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) Dane wyjściowe będą wyświetlane w następujący sposób:

```output
Name                                  Operation         Status     Item Name   Start Time UTC
------------------------------------  ---------------   ---------  ----------  -------------------  
e0f15dae-7cac-4475-a833-f52c50e5b6c3  ConfigureBackup   Completed  hxe         2019-12-03T03:09:210831+00:00  
```

Polecenie cmdlet [listy zadań kopii zapasowej az](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) zawiera listę wszystkich zadań tworzenia kopii zapasowych (zaplanowanych lub na żądanie), które zostały uruchomione lub są obecnie uruchomione w chronionej bazie danych, oprócz innych operacji, takich jak rejestracja, konfigurowanie kopii zapasowej, usuwanie danych kopii zapasowej itp.

## <a name="trigger-an-on-demand-backup"></a>Wyzwalanie kopii zapasowej na żądanie

W powyższej sekcji opisano sposób konfigurowania zaplanowanej kopii zapasowej, ale w tej sekcji o tym, jak wyzwolić kopię zapasową na żądanie. Aby to zrobić, używamy [az backup protection-now](https://docs.microsoft.com/cli/azure/backup/protection#az-backup-protection-backup-now) cmdlet.

>[!NOTE]
> Zasady przechowywania kopii zapasowej na żądanie są określane przez podstawowe zasady przechowywania bazy danych.

```azurecli-interactive
az backup protection backup-now --resource-group saphanaResourceGroup \
    --item-name saphanadatabase;hxe;hxe \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --backup-type Full
    --retain-until 01-01-2040
    --output table
```

Dane wyjściowe będą wyświetlane w następujący sposób:

```output
Name                                  ResourceGroup
------------------------------------  -------------
e0f15dae-7cac-4475-a833-f52c50e5b6c3  saphanaResourceGroup
```

Odpowiedź poda nazwę zadania. Ta nazwa zadania może służyć do śledzenia stanu zadania przy użyciu polecenia cmdlet [zadania kopii zapasowej az.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)

>[!NOTE]
>Oprócz planowania pełnej lub różnicowej kopii zapasowej, mogą one być również obecnie wyzwalane ręcznie. Kopie zapasowe dziennika są automatycznie wyzwalane i zarządzane przez SAP HANA wewnętrznie.
>
> Przyrostowe kopie zapasowe nie są obecnie obsługiwane przez usługę Azure Backup.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak przywrócić bazę danych SAP HANA na maszynie Wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia, przejdź do samouczka — [przywracanie bazy danych SAP HANA na maszynie Wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia](tutorial-sap-hana-restore-cli.md)

* Aby dowiedzieć się, jak wykonać kopię zapasową bazy danych SAP HANA uruchomionej na maszynie Wirtualnej platformy Azure przy użyciu portalu Azure, zapoznaj się [z tworzeniem kopii zapasowych baz danych SAP HANA na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)
