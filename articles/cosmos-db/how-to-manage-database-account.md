---
title: Dowiedz się, jak zarządzać kontami bazy danych w Azure Cosmos DB
description: Dowiedz się, jak zarządzać kontami bazy danych w Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: f67487f6da5c9be028703d7890e16ffab0c858c6
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71812526"
---
# <a name="manage-an-azure-cosmos-account"></a>Zarządzanie kontem usługi Azure Cosmos

W tym artykule opisano sposób zarządzania różnymi zadaniami na koncie usługi Azure Cosmos za pomocą szablonów Azure Portal, Azure PowerShell, interfejsu wiersza polecenia platformy Azure i Azure Resource Manager.

## <a name="create-an-account"></a>Utwórz konto

### <a id="create-database-account-via-portal"></a>Azure Portal

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a id="create-database-account-via-cli"></a>Interfejs wiersza polecenia platformy Azure

Zobacz [Tworzenie konta Azure Cosmos dB przy użyciu interfejsu wiersza polecenia platformy Azure](manage-with-cli.md#create-an-azure-cosmos-db-account)

### <a id="create-database-account-via-ps"></a>Azure PowerShell

Zobacz [Tworzenie konta Azure Cosmos dB przy użyciu programu PowerShell](manage-with-powershell.md#create-account)

### <a id="create-database-account-via-arm-template"></a>Szablon Azure Resource Manager

Ten szablon Azure Resource Manager utworzy konto usługi Azure Cosmos dla dowolnego obsługiwanego interfejsu API skonfigurowanego z dwoma regionami i opcjami w celu wybrania poziomu spójności, automatycznej pracy awaryjnej i wielu wzorców. Aby wdrożyć ten szablon, kliknij pozycję Wdróż na platformie Azure na stronie Readme, [Utwórz konto usługi Azure Cosmos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-create-multi-region-account)

## <a name="addremove-regions-from-your-database-account"></a>Dodaj/Usuń regiony z konta bazy danych

### <a id="add-remove-regions-via-portal"></a>Azure Portal

1. Zaloguj się do [Azure Portal](https://portal.azure.com). 

1. Przejdź do konta usługi Azure Cosmos i otwórz menu **Replikacja danych globalnie** .

1. Aby dodać regiony, zaznacz sześciokąty na mapie z etykietą **+** odpowiadającą żądanym regionom. Alternatywnie, aby dodać region, wybierz opcję **+ Dodaj region** i wybierz region z menu rozwijanego.

1. Aby usunąć regiony, wyczyść jeden lub więcej regionów z mapy, zaznaczając niebieskie sześciokąty ze znacznikami wyboru. Lub wybierz ikonę "wastebasket" (🗑) obok regionu po prawej stronie.

1. Aby zapisać zmiany, wybierz **przycisk OK**.

   ![Menu Dodaj lub Usuń regiony](./media/how-to-manage-database-account/add-region.png)

W trybie zapisu z jednym regionem nie można usunąć regionu zapisu. Aby można było usunąć bieżący region zapisu, należy przełączyć się do trybu failover w innym regionie.

W trybie zapisu wieloregionowego można dodać lub usunąć dowolny region, jeśli masz co najmniej jeden region.

### <a id="add-remove-regions-via-cli"></a>Interfejs wiersza polecenia platformy Azure

Zobacz [Dodawanie lub usuwanie regionów przy użyciu interfejsu wiersza polecenia platformy Azure](manage-with-cli.md#add-or-remove-regions)

### <a id="add-remove-regions-via-ps"></a>Azure PowerShell

Zobacz [Dodawanie lub usuwanie regionów przy użyciu programu PowerShell](manage-with-powershell.md#update-account)

## <a id="configure-multiple-write-regions"></a>Konfigurowanie wielu regionów zapisu

### <a id="configure-multiple-write-regions-portal"></a>Azure Portal

Otwórz kartę **Replikuj dane globalnie** i wybierz pozycję **Włącz** , aby włączyć zapisywanie w ramach wieloregionu. Po włączeniu zapisów obejmujących wiele regionów wszystkie regiony odczytu znajdujące się obecnie na koncie staną się regionami odczytu i zapisu.

![Konto usługi Azure Cosmos służy do konfigurowania wieloskładnikowego zrzutu ekranu](./media/how-to-manage-database-account/single-to-multi-master.png)

### <a id="configure-multiple-write-regions-cli"></a>Interfejs wiersza polecenia platformy Azure

Zobacz [Włączanie regionów wielu zapisów za pomocą interfejsu wiersza polecenia platformy Azure](manage-with-cli.md#enable-multiple-write-regions)

### <a id="configure-multiple-write-regions-ps"></a>Azure PowerShell

Zobacz [Włączanie regionów wielu zapisów przy użyciu programu PowerShell](manage-with-powershell.md#multi-master)

### <a id="configure-multiple-write-regions-arm"></a>Szablon Menedżer zasobów

Konto można migrować z jednego serwera głównego do wielu wzorców, wdrażając szablon Menedżer zasobów używany do tworzenia konta i ustawienia `enableMultipleWriteLocations: true`. Następujący szablon Azure Resource Manager jest nieminimalnym szablonem, który spowoduje wdrożenie konta Azure Cosmos dla interfejsu API SQL z dwoma regionami i włączenie wielu lokalizacji zapisu.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "String"
        },
        "location": {
            "type": "String",
            "defaultValue": "[resourceGroup().location]"
        },
        "primaryRegion":{
            "type":"string",
            "metadata": {
                "description": "The primary replica region for the Cosmos DB account."
            }
        },
        "secondaryRegion":{
            "type":"string",
            "metadata": {
              "description": "The secondary replica region for the Cosmos DB account."
          }
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDb/databaseAccounts",
            "kind": "GlobalDocumentDB",
            "name": "[parameters('name')]",
            "apiVersion": "2015-04-08",
            "location": "[parameters('location')]",
            "tags": {},
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": { "defaultConsistencyLevel": "Session" },
                "locations":
                [
                    {
                        "locationName": "[parameters('primaryRegion')]",
                        "failoverPriority": 0
                    },
                    {
                        "locationName": "[parameters('secondaryRegion')]",
                        "failoverPriority": 1
                    }
                ],
                "enableMultipleWriteLocations": true
            }
        }
    ]
}
```

## <a id="automatic-failover"></a>Włącz automatyczną pracę w trybie failover dla konta usługi Azure Cosmos

Opcja automatycznej pracy awaryjnej umożliwia Azure Cosmos DB przełączenia w tryb failover do regionu z najwyższym priorytetem trybu failover bez akcji użytkownika, jeśli region staje się niedostępny. Gdy włączona jest automatyczna tryb failover, priorytet regionu można modyfikować. Aby można było włączyć automatyczne przełączanie do trybu failover, konto musi mieć co najmniej dwa regiony.

### <a id="enable-automatic-failover-via-portal"></a>Azure Portal

1. Na koncie usługi Azure Cosmos Otwórz okienko **Replikuj dane globalnie** .

2. W górnej części okienka wybierz pozycję **Automatyczne przełączanie do trybu failover**.

   ![Replikuj dane globalne — menu](./media/how-to-manage-database-account/replicate-data-globally.png)

3. W okienku **Automatyczne przełączanie do trybu failover** upewnij się, że ustawienie **Włącz automatyczną pracę awaryjną** jest ustawione na wartość **włączone**. 

4. Wybierz pozycję **Zapisz**.

   ![Menu portalu automatycznej pracy awaryjnej](./media/how-to-manage-database-account/automatic-failover.png)

### <a id="enable-automatic-failover-via-cli"></a>Interfejs wiersza polecenia platformy Azure

Zobacz [Włączanie automatycznego przełączania do trybu failover przy użyciu interfejsu wiersza polecenia platformy Azure](manage-with-cli.md#enable-automatic-failover)

### <a id="enable-automatic-failover-via-ps"></a>Azure PowerShell

Zobacz [Włączanie automatycznego przełączania do trybu failover przy użyciu programu PowerShell](manage-with-powershell.md#enable-automatic-failover)

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>Ustawianie priorytetów trybu failover dla konta usługi Azure Cosmos

Po skonfigurowaniu konta usługi Cosmos do automatycznego przełączania w tryb failover priorytet trybu failover dla regionów można zmienić.

> [!IMPORTANT]
> Nie można zmodyfikować regionu zapisu (priorytet trybu failover równy zero), jeśli konto jest skonfigurowane do automatycznego przełączania do trybu failover. Aby zmienić region zapisu, należy wyłączyć automatyczne przełączanie do trybu failover i przeprowadzić ręczną pracę awaryjną.

### <a id="set-failover-priorities-via-portal"></a>Azure Portal

1. Na koncie usługi Azure Cosmos Otwórz okienko **Replikuj dane globalnie** .

2. W górnej części okienka wybierz pozycję **Automatyczne przełączanie do trybu failover**.

   ![Replikuj dane globalne — menu](./media/how-to-manage-database-account/replicate-data-globally.png)

3. W okienku **Automatyczne przełączanie do trybu failover** upewnij się, że ustawienie **Włącz automatyczną pracę awaryjną** jest ustawione na wartość **włączone**.

4. Aby zmodyfikować priorytet trybu failover, przeciągnij regiony odczytu za pośrednictwem trzech kropek po lewej stronie wiersza, który jest wyświetlany po umieszczeniu wskaźnika myszy nad nimi.

5. Wybierz pozycję **Zapisz**.

   ![Menu portalu automatycznej pracy awaryjnej](./media/how-to-manage-database-account/automatic-failover.png)

### <a id="set-failover-priorities-via-cli"></a>Interfejs wiersza polecenia platformy Azure

Zobacz [Ustawianie priorytetu trybu failover przy użyciu interfejsu wiersza polecenia platformy Azure](manage-with-cli.md#set-failover-priority)

### <a id="set-failover-priorities-via-ps"></a>Azure PowerShell

Zobacz [Ustawianie priorytetu trybu failover przy użyciu programu PowerShell](manage-with-powershell.md#modify-failover-priority)

## <a id="manual-failover"></a>Wykonaj ręczną pracę awaryjną na koncie usługi Azure Cosmos

> [!IMPORTANT]
> Aby ta operacja zakończyła się pomyślnie, należy skonfigurować konto usługi Azure Cosmos do ręcznego przełączania do trybu failover.

Proces przeprowadzania ręcznej pracy awaryjnej polega na zmianie regionu zapisu konta (priorytetu trybu failover = 0) w innym regionie skonfigurowanym dla tego konta.

> [!NOTE]
> Kont wielu wzorców nie można przełączyć ręcznie do trybu failover. W przypadku aplikacji korzystających z zestawu SDK usługi Azure Cosmos zestaw SDK wykrywa, kiedy region stanie się niedostępny, a następnie przekierowuje automatycznie do następnego najbliższego regionu, jeśli używany jest interfejs API multihostingu w zestawie SDK.

### <a id="enable-manual-failover-via-portal"></a>Azure Portal

1. Przejdź do konta usługi Azure Cosmos i otwórz menu **Replikacja danych globalnie** .

2. W górnej części menu wybierz pozycję **Ręczne przełączanie do trybu failover**.

   ![Replikuj dane globalne — menu](./media/how-to-manage-database-account/replicate-data-globally.png)

3. W menu **ręczny tryb failover** wybierz nowy region zapisu. Zaznacz pole wyboru, aby wskazać, że rozumiesz, że ta opcja zmienia region zapisu.

4. Aby wyzwolić przejście w tryb failover, wybierz **przycisk OK**.

   ![Ręczne menu portalu trybu failover](./media/how-to-manage-database-account/manual-failover.png)

### <a id="enable-manual-failover-via-cli"></a>Interfejs wiersza polecenia platformy Azure

Zobacz [wyzwalanie ręcznego przełączania do trybu failover przy użyciu interfejsu wiersza polecenia platformy Azure](manage-with-cli.md#trigger-manual-failover)

### <a id="enable-manual-failover-via-ps"></a>Azure PowerShell

Zobacz [wyzwalanie ręcznego przełączania w tryb failover przy użyciu programu PowerShell](manage-with-powershell.md#trigger-manual-failover)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji i zapoznać się z przykładami dotyczącymi zarządzania kontem usługi Azure Cosmos oraz bazą danych i kontenerami, przeczytaj następujące artykuły:

* [Zarządzanie Azure Cosmos DB przy użyciu Azure PowerShell](manage-with-powershell.md)
* [Zarządzanie Azure Cosmos DB przy użyciu interfejsu wiersza polecenia platformy Azure](manage-with-cli.md)
