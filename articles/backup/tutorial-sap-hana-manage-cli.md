---
title: 'Samouczek: Zarządzanie kopiią zapasową sap HANA DB przy użyciu interfejsu wiersza polecenia'
description: W tym samouczku dowiesz się, jak zarządzać bazami danych SAP HANA uruchomionymi na maszynie Wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure.
ms.topic: tutorial
ms.date: 12/4/2019
ms.openlocfilehash: 2c088c27a678a4541cbba3c4c43c9cd830c60ff0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238741"
---
# <a name="tutorial-manage-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Samouczek: Zarządzanie bazami danych SAP HANA na maszynie Wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure

Narzędzie CLI platformy Azure służy do tworzenia zasobów platformy Azure i zarządzania nimi z wiersza polecenia lub za pomocą skryptów. W tej dokumentacji opisano, jak zarządzać bazą danych SAP HANA, z którymi można się skontaktować na maszynie Wirtualnej platformy Azure — wszystkie przy użyciu interfejsu wiersza polecenia platformy Azure. Można również wykonać te kroki za pomocą [witryny Azure portal](https://docs.microsoft.com/azure/backup/sap-hana-db-manage).

Użyj [usługi Azure Cloud Shell](tutorial-sap-hana-backup-cli.md) do uruchamiania poleceń interfejsu wiersza polecenia.

Pod koniec tego samouczka, będziesz mógł:

> [!div class="checklist"]
>
> * Monitorowanie zadań tworzenia kopii zapasowych i przywracania
> * Ochrona nowych baz danych dodanych do wystąpienia SAP HANA
> * Zmienianie zasad
> * Zatrzymywanie ochrony
> * Ochrona wznawiania

Jeśli użyto [kopii zapasowej bazy danych SAP HANA na platformie Azure przy użyciu interfejsu wiersza polecenia](tutorial-sap-hana-backup-cli.md) do utworzenia kopii zapasowej bazy danych SAP HANA, używasz następujących zasobów:

* grupa zasobów o nazwie *saphanaResourceGroup*
* skarbiec o nazwie *saphanaVault*
* chroniony kontener o nazwie *VMAppContainer; Oblicz;saphanaResourceGroup;saphanaVM*
* kopia zapasowa bazy danych/elementu o nazwie *saphanadatabase;hxe;hxe*
* zasobów w regionie *westus2*

Narzędzie cli platformy Azure ułatwia zarządzanie bazą danych SAP HANA działającą na maszynie Wirtualnej platformy Azure, która jest kopią zapasową przy użyciu usługi Azure Backup. Ten samouczek szczegółowo każdej operacji zarządzania.

## <a name="monitor-backup-and-restore-jobs"></a>Monitorowanie zadań tworzenia kopii zapasowych i przywracania

Aby monitorować ukończone lub aktualnie uruchomione zadania (tworzenie kopii zapasowych lub przywracanie), użyj polecenia cmdlet [listy zadań kopii zapasowej az.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) Cli pozwala również [zawiesić aktualnie uruchomione zadanie](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-stop) lub [poczekać, aż zadanie zakończy](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-wait)się .

```azurecli-interactive
az backup job list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --output table
```

Dane wyjściowe będą przypominać następujące:

```output
Name                                  Operation              Status      Item Name       Start Time UTC
------------------------------------  ---------------        ---------   ----------      -------------------  
e0f15dae-7cac-4475-a833-f52c50e5b6c3  ConfigureBackup        Completed   hxe             2019-12-03T03:09:210831+00:00  
ccdb4dce-8b15-47c5-8c46-b0985352238f  Backup (Full)          Completed   hxe [hxehost]   2019-12-01T10:30:58.867489+00:00
4980af91-1090-49a6-ab96-13bc905a5282  Backup (Differential)  Completed   hxe [hxehost]   2019-12-01T10:36:00.563909+00:00
F7c68818-039f-4a0f-8d73-e0747e68a813  Restore (Log)          Completed   hxe [hxehost]   2019-12-03T05:44:51.081607+00:00
```

## <a name="change-policy"></a>Zmienianie zasad

Aby zmienić zasady leżące u podstaw konfiguracji kopii zapasowej SAP HANA, użyj polecenia cmdlet [zestawu zasad tworzenia kopii zapasowych az.](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-set) Parametr name w tym powiększe odnosi się do elementu kopii zapasowej, którego zasady chcemy zmienić. W tym samouczku zastąpimy politykę naszej bazy danych SAP HANA *saphanadatabase;hxe;hxe* nową *zasadą newsaphanaPolicy*. Nowe zasady można tworzyć przy użyciu polecenia cmdlet [tworzenia zasad tworzenia kopii zapasowych az.](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-create)

```azurecli-interactive
az backup item set policy --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --policy-name newsaphanaPolicy \
    --name saphanadatabase;hxe;hxe \
```

Dane wyjściowe powinny wyglądać następująco:

```output
Name                                  Resource Group
------------------------------------- --------------
cb110094-9b15-4c55-ad45-6899200eb8dd  SAPHANA
```

## <a name="protect-new-databases-added-to-an-sap-hana-instance"></a>Ochrona nowych baz danych dodanych do wystąpienia SAP HANA

[Rejestrowanie wystąpienia SAP HANA w magazynie usług odzyskiwania](tutorial-sap-hana-backup-cli.md#register-and-protect-the-sap-hana-instance) automatycznie odnajduje wszystkie bazy danych w tym wystąpieniu.

Jednak w przypadkach, gdy nowe bazy danych są dodawane do wystąpienia SAP HANA później, należy użyć [az kopii zapasowej elementu chronionego zainicjować polecenie](https://docs.microsoft.com/cli/azure/backup/protectable-item?view=azure-cli-latest#az-backup-protectable-item-initialize) cmdlet. To polecenie cmdlet odnajduje nowe dodane bazy danych.

```azurecli-interactive
az backup protectable-item initialize --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --workload-type SAPHANA
```

Następnie użyj polecenia cmdlet [listy chronionych elementu az,](https://docs.microsoft.com/cli/azure/backup/protectable-item?view=azure-cli-latest#az-backup-protectable-item-list) aby wyświetlić listę wszystkich baz danych, które zostały wykryte w wystąpieniu SAP HANA. Ta lista wyklucza jednak te bazy danych, na których kopia zapasowa została już skonfigurowana. Po odnajdyciu bazy danych, która ma być zabezpieczonych, zapoznaj się [z opcję Włącz tworzenie kopii zapasowej w bazie danych SAP HANA](tutorial-sap-hana-backup-cli.md#enable-backup-on-sap-hana-database).

```azurecli-interactive
az backup protectable-item list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --workload-type SAPHANA \
    --output table
```

Nowa baza danych, której chcesz sympować, pojawi się na tej liście, która będzie wyglądać następująco:

```output
Name                            Protectable Item Type    ParentName    ServerName    IsProtected
---------------------------     ----------------------   ------------  -----------   ------------
saphanasystem;hxe               SAPHanaSystem            HXE           hxehost       NotProtected  
saphanadatabase;hxe;systemdb    SAPHanaDatabase          HXE           hxehost       NotProtected
saphanadatabase;hxe;newhxe      SAPHanaDatabase          HXE           hxehost       NotProtected
```

## <a name="stop-protection-for-an-sap-hana-database"></a>Ochrona zatrzymania bazy danych SAP HANA

Możesz przestać chronić bazę danych SAP HANA na kilka sposobów:

* Zatrzymanie wszystkich przyszłych zadań tworzenia kopii zapasowych i usunięcie wszystkich punktów odzyskiwania.
* Zatrzymaj wszystkie przyszłe zadania tworzenia kopii zapasowych i pozostaw punkty odzyskiwania bez zmian.

Jeśli zdecydujesz się pozostawić punkty odzyskiwania, pamiętaj o tych szczegółach:

* Wszystkie punkty odzyskiwania pozostaną nienaruszone na zawsze, wszystkie przycinanie zatrzymuje się na ochronie przystanku z zachowaniem danych.
* Zostanie naliczona opłata za chronione wystąpienie i zużyty magazyn.
* Jeśli usuniesz źródło danych bez zatrzymywania kopii zapasowych, nowe kopie zapasowe nie powiodą się.

Przyjrzyjmy się każdemu ze sposobów, aby zatrzymać ochronę bardziej szczegółowo.

### <a name="stop-protection-with-retain-data"></a>Zatrzymanie ochrony z zachowaniem danych

Aby zatrzymać ochronę za pomocą przechowywania danych, użyj polecenia cmdlet [ochrony kopii zapasowej az disable.](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable)

```azurecli-interactive
az backup protection disable --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --workload-type SAPHANA \
    --output table
```

Dane wyjściowe powinny wyglądać następująco:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
g0f15dae-7cac-4475-d833-f52c50e5b6c3  saphanaResourceGroup
```

Aby sprawdzić stan tej operacji, użyj polecenia cmdlet [zadania kopii zapasowej az.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)

### <a name="stop-protection-without-retain-data"></a>Zatrzymaj ochronę bez przechowywania danych

Aby zatrzymać ochronę bez zachowywania danych, użyj polecenia cmdlet [ochrony kopii zapasowej az.](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable)

```azurecli-interactive
az backup protection disable --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --workload-type SAPHANA \
    --delete-backup-data true \
    --output table
```

Dane wyjściowe powinny wyglądać następująco:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
g0f15dae-7cac-4475-d833-f52c50e5b6c3  saphanaResourceGroup
```

Aby sprawdzić stan tej operacji, użyj polecenia cmdlet [zadania kopii zapasowej az.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)

## <a name="resume-protection"></a>Ochrona wznawiania

Po zatrzymaniu ochrony bazy danych SAP HANA z zachować dane, można później wznowić ochronę. Jeśli nie zachowasz kopii zapasowej danych, nie będzie można wznowić ochrony.

Aby wznowić ochronę, użyj polecenia cmdlet [wznawiania ochrony kopii zapasowej az.](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-resume)

```azurecli-interactive
az backup protection resume --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --policy-name saphanaPolicy \
    --output table
```

Dane wyjściowe powinny wyglądać następująco:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
b2a7f108-1020-4529-870f-6c4c43e2bb9e  saphanaResourceGroup
```

Aby sprawdzić stan tej operacji, użyj polecenia cmdlet [zadania kopii zapasowej az.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak wykonać kopię zapasową bazy danych SAP HANA uruchomionej na maszynie Wirtualnej platformy Azure przy użyciu portalu Azure, zapoznaj się [z tworzeniem kopii zapasowych baz danych SAP HANA na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)

* Aby dowiedzieć się, jak zarządzać bazą danych SAP HANA z pomocą platformy Azure przy użyciu portalu Azure, zobacz [Zarządzanie bazami danych SAP HANA, które zostały utworzone na maszynie Wirtualnej platformy Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)
