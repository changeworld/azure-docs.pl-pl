---
title: Dowiedz się, jak zarządzać kontami bazy danych w usłudze Azure Cosmos DB
description: Dowiedz się, jak zarządzać zasobami usługi Azure Cosmos DB przy użyciu szablonów usługi Azure portal, PowerShell, CLI i Azure Resource Manager
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 61670d757611bd0c1dd11c389282b18edb3d7fa1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247386"
---
# <a name="manage-an-azure-cosmos-account"></a>Zarządzanie kontem usługi Azure Cosmos

W poniższym artykule opisano sposób zarządzania różnymi zadaniami w ramach konta usługi Azure Cosmos przy użyciu witryny Azure Portal, programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure i szablonów usługi Azure Resource Manager.

## <a name="create-an-account"></a>Tworzenie konta

### <a name="azure-portal"></a><a id="create-database-account-via-portal"></a>Portal Azure

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a name="azure-cli"></a><a id="create-database-account-via-cli"></a>Interfejs wiersza polecenia platformy Azure

Zobacz [Tworzenie konta usługi Azure Cosmos DB w interfejsie wiersza polecenia platformy Azure](manage-with-cli.md#create-an-azure-cosmos-db-account)

### <a name="azure-powershell"></a><a id="create-database-account-via-ps"></a>Azure PowerShell

Zobacz [Tworzenie konta usługi Azure Cosmos DB w programie Powershell](manage-with-powershell.md#create-account)

### <a name="azure-resource-manager-template"></a><a id="create-database-account-via-arm-template"></a>Szablon usługi Azure Resource Manager

Ten szablon usługi Azure Resource Manager utworzy konto usługi Azure Cosmos dla interfejsu API SQL skonfigurowany z dwoma regionami i opcjami, aby wybrać poziom spójności, automatyczną tryb failover i wiele wzorca. Aby wdrożyć ten szablon, kliknij pozycję Wdrażanie na platformie Azure na stronie [readme, Tworzenie konta usługi Azure Cosmos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-sql)

## <a name="addremove-regions-from-your-database-account"></a>Dodawanie/usuwanie regionów z poziomu konta bazy danych

### <a name="azure-portal"></a><a id="add-remove-regions-via-portal"></a>Portal Azure

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com).

1. Przejdź do swojego konta usługi Azure Cosmos i otwórz menu **Replikuj dane globalnie.**

1. Aby dodać regiony, wybierz sześciokąty na mapie **+** etykietą odpowiadającą żądanym regionom. Alternatywnie, aby dodać region, wybierz opcję **+ Dodaj region** i wybierz region z menu rozwijanego.

1. Aby usunąć regiony, usuń co najmniej jeden region z mapy, wybierając niebieskie sześciokąty ze znacznikami wyboru. Możesz też wybrać ikonę „kosza na śmieci” (🗑) z prawej strony regionu.

1. Aby zapisać zmiany, wybierz pozycję **OK**.

   ![Menu dodawania lub usuwania regionów](./media/how-to-manage-database-account/add-region.png)

W trybie zapisu w jednym regionie nie można usunąć regionu zapisu. Aby można było usunąć bieżący region zapisu, należy przejść do trybu failover w innym regionie.

W trybie zapisu w wielu regionach można dodawać lub usuwać dowolne regiony, o ile pozostaje co najmniej jeden region.

### <a name="azure-cli"></a><a id="add-remove-regions-via-cli"></a>Interfejs wiersza polecenia platformy Azure

Zobacz [Dodawanie lub usuwanie regionów za pomocą interfejsu wiersza polecenia platformy Azure](manage-with-cli.md#add-or-remove-regions)

### <a name="azure-powershell"></a><a id="add-remove-regions-via-ps"></a>Azure PowerShell

Zobacz [Dodawanie lub usuwanie regionów za pomocą programu Powershell](manage-with-powershell.md#update-account)

## <a name="configure-multiple-write-regions"></a><a id="configure-multiple-write-regions"></a>Konfigurowanie wielu regionów zapisu

### <a name="azure-portal"></a><a id="configure-multiple-write-regions-portal"></a>Portal Azure

Otwórz kartę **Globalnie replikuj dane** i wybierz pozycję **Włącz,** aby włączyć zapisy wieloregionowe. Po włączeniu zapisów wieloregionowych wszystkie regiony odczytu, które są obecnie na koncie, staną się regionami odczytu i zapisu.

![Zrzut ekranu przedstawiający konto usługi Azure Cosmos konfiguruje zrzut ekranu z wieloma wzorcami](./media/how-to-manage-database-account/single-to-multi-master.png)

### <a name="azure-cli"></a><a id="configure-multiple-write-regions-cli"></a>Interfejs wiersza polecenia platformy Azure

Zobacz [Włączanie regionów wielokrotnego zapisu za pomocą interfejsu wiersza polecenia platformy Azure](manage-with-cli.md#enable-multiple-write-regions)

### <a name="azure-powershell"></a><a id="configure-multiple-write-regions-ps"></a>Azure PowerShell

Zobacz [Włączanie regionów wielokrotnego zapisu za pomocą programu Powershell](manage-with-powershell.md#multi-master)

### <a name="resource-manager-template"></a><a id="configure-multiple-write-regions-arm"></a>Szablon Menedżera zasobów

Konto można migrować z jednego wzorca do wielu wzorca, wdrażając szablon `enableMultipleWriteLocations: true`Menedżera zasobów używany do tworzenia konta i ustawiania . Poniższy szablon usługi Azure Resource Manager jest minimalny szablon, który wdroży konto usługi Azure Cosmos dla interfejsu API SQL z dwóch regionów i wielu lokalizacji zapisu włączone.

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
            "apiVersion": "2019-08-01",
            "location": "[parameters('location')]",
            "tags": {},
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": { "defaultConsistencyLevel": "Session" },
                "locations":
                [
                    {
                        "locationName": "[parameters('primaryRegion')]",
                        "failoverPriority": 0,
                        "isZoneRedundant": false
                    },
                    {
                        "locationName": "[parameters('secondaryRegion')]",
                        "failoverPriority": 1,
                        "isZoneRedundant": false
                    }
                ],
                "enableMultipleWriteLocations": true
            }
        }
    ]
}
```

## <a name="enable-automatic-failover-for-your-azure-cosmos-account"></a><a id="automatic-failover"></a>Włączanie automatycznego przejścia do trybu failover dla konta usługi Azure Cosmos

Opcja automatycznego trybu failover umożliwia usługi Azure Cosmos DB do trybu failover do regionu o najwyższym priorytecie trybu failover bez działania użytkownika, jeśli region staną się niedostępne. Gdy automatyczna funkcja pracy awaryjnej jest włączona, można zmodyfikować priorytet regionu. Konto musi mieć dwa lub więcej regionów, aby włączyć automatyczne tryb failover.

### <a name="azure-portal"></a><a id="enable-automatic-failover-via-portal"></a>Portal Azure

1. Na koncie usługi Azure Cosmos otwórz okienko **Globalnie Replikuj dane.**

2. U góry okienka wybierz pozycję **Automatyczne przełączanie w tryb failover**.

   ![Menu globalnego replikowania danych](./media/how-to-manage-database-account/replicate-data-globally.png)

3. W okienku **Automatyczne przełączanie w tryb failover** upewnij się, że przełącznik **Włącz automatyczne przejście w tryb failover** jest ustawiony w pozycji **WŁ**. 

4. Wybierz **pozycję Zapisz**.

   ![Menu Automatyczne przełączanie w tryb failover w portalu](./media/how-to-manage-database-account/automatic-failover.png)

### <a name="azure-cli"></a><a id="enable-automatic-failover-via-cli"></a>Interfejs wiersza polecenia platformy Azure

Zobacz [Włączanie automatycznego trybu failover za pomocą interfejsu wiersza polecenia platformy Azure](manage-with-cli.md#enable-automatic-failover)

### <a name="azure-powershell"></a><a id="enable-automatic-failover-via-ps"></a>Azure PowerShell

Zobacz [Włączanie automatycznego trybu failover za pomocą programu Powershell](manage-with-powershell.md#enable-automatic-failover)

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>Ustawianie priorytetów trybu failover dla konta usługi Azure Cosmos

Po skonfigurowaniu konta usługi Cosmos do automatycznego trybu failover można zmienić priorytet trybu failover dla regionów.

> [!IMPORTANT]
> Nie można zmodyfikować regionu zapisu (priorytet trybu failover zero), gdy konto jest skonfigurowane do automatycznego trybu failover. Aby zmienić region zapisu, należy wyłączyć automatyczną pracę awaryjną i wykonać ręczne tryb failover.

### <a name="azure-portal"></a><a id="set-failover-priorities-via-portal"></a>Portal Azure

1. Na koncie usługi Azure Cosmos otwórz okienko **Globalnie Replikuj dane.**

2. U góry okienka wybierz pozycję **Automatyczne przełączanie w tryb failover**.

   ![Menu globalnego replikowania danych](./media/how-to-manage-database-account/replicate-data-globally.png)

3. W okienku **Automatyczne przełączanie w tryb failover** upewnij się, że przełącznik **Włącz automatyczne przejście w tryb failover** jest ustawiony w pozycji **WŁ**.

4. Aby zmodyfikować priorytety trybu failover, przeciągnij regiony odczytu za pośrednictwem trzech kropek znajdujących się po lewej stronie wiersza, który jest wyświetlany po umieszczeniu na nich wskaźnika myszy.

5. Wybierz **pozycję Zapisz**.

   ![Menu Automatyczne przełączanie w tryb failover w portalu](./media/how-to-manage-database-account/automatic-failover.png)

### <a name="azure-cli"></a><a id="set-failover-priorities-via-cli"></a>Interfejs wiersza polecenia platformy Azure

Zobacz [Ustawianie priorytetu pracy awaryjnej za pomocą interfejsu wiersza polecenia platformy Azure](manage-with-cli.md#set-failover-priority)

### <a name="azure-powershell"></a><a id="set-failover-priorities-via-ps"></a>Azure PowerShell

Zobacz [Ustawianie priorytetu pracy awaryjnej w programie Powershell](manage-with-powershell.md#modify-failover-priority)

## <a name="perform-manual-failover-on-an-azure-cosmos-account"></a><a id="manual-failover"></a>Ręczne przechodzenie do trybu failover na koncie usługi Azure Cosmos

> [!IMPORTANT]
> Konto usługi Azure Cosmos musi być skonfigurowane do ręcznego trybu failover dla tej operacji, aby ta operacja powiodła się.

Proces wykonywania ręcznego trybu failover polega na zmianie regionu zapisu konta (priorytet trybu failover = 0) na inny region skonfigurowany dla konta.

> [!NOTE]
> Kont wielobramkowych nie można ręcznie przechoń nie powiodło się. W przypadku aplikacji korzystających z narzędzia Azure Cosmos SDK zestaw SDK wykryje, kiedy region stanie się niedostępny, a następnie automatycznie przekieruje do najbliższego regionu, jeśli używa interfejsu API z wieloma plikamiwistycznymi w zestawie SDK.

### <a name="azure-portal"></a><a id="enable-manual-failover-via-portal"></a>Portal Azure

1. Przejdź do swojego konta usługi Azure Cosmos i otwórz menu **Replikuj dane globalnie.**

2. W górnej części menu wybierz pozycję **Ręczne przejście do trybu failover**.

   ![Menu globalnego replikowania danych](./media/how-to-manage-database-account/replicate-data-globally.png)

3. W menu **Ręczne przejście do trybu failover** wybierz nowy region zapisu. Zaznacz pole wyboru, aby wskazać, że rozumiesz, iż ta opcja zmienia region zapisu.

4. Aby wyzwolić tryb failover, wybierz pozycję **OK**.

   ![Menu Ręczne przejście do trybu failover w portalu](./media/how-to-manage-database-account/manual-failover.png)

### <a name="azure-cli"></a><a id="enable-manual-failover-via-cli"></a>Interfejs wiersza polecenia platformy Azure

Zobacz [ręczne uruchamianie w trybie failover wyzwalania za pomocą interfejsu wiersza polecenia platformy Azure](manage-with-cli.md#trigger-manual-failover)

### <a name="azure-powershell"></a><a id="enable-manual-failover-via-ps"></a>Azure PowerShell

Zobacz: [Ręczne uruchamianie awaryjne wyzwalania za pomocą programu Powershell](manage-with-powershell.md#trigger-manual-failover)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji i przykładów dotyczących zarządzania kontem usługi Azure Cosmos, a także bazy danych i kontenerów, przeczytaj następujące artykuły:

* [Zarządzanie usługą Azure Cosmos DB przy użyciu usługi Azure PowerShell](manage-with-powershell.md)
* [Zarządzanie usługą Azure Cosmos DB przy użyciu interfejsu wiersza polecenia platformy Azure](manage-with-cli.md)
