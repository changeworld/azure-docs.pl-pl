---
title: Konfigurowanie kluczy zarządzanych przez klienta dla konta Azure Cosmos DB
description: Dowiedz się, jak skonfigurować klucze zarządzane przez klienta dla konta Azure Cosmos DB
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/11/2020
ms.author: thweiss
ROBOTS: noindex, nofollow
ms.openlocfilehash: 964c3e4e2de43e6bcae353f0b525eb62e6613361
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911858"
---
# <a name="configure-customer-managed-keys-for-your-azure-cosmos-db-account"></a>Konfigurowanie kluczy zarządzanych przez klienta dla konta Azure Cosmos DB

> [!NOTE]
> W tej chwili należy zażądać dostępu do korzystania z tej funkcji. Aby to zrobić, skontaktuj się z [cosmosdbpm@microsoft.com](mailto:cosmosdbpm@microsoft.com).

Dane przechowywane na koncie Azure Cosmos DB są automatycznie i szyfrowane bezproblemowo. Azure Cosmos DB oferuje dwie opcje zarządzania kluczami używanymi do szyfrowania danych w spoczynku:
- **Klucze zarządzane przez usługę**. Domyślnie firma Microsoft zarządza kluczami używanymi do szyfrowania konta Azure Cosmos DB.
- **Klucze zarządzane przez klienta (CMK)** . Opcjonalnie możesz dodać drugą warstwę szyfrowania z kluczami, którymi zarządzasz.

Klucze zarządzane przez klienta muszą być przechowywane w [Azure Key Vault](../key-vault/key-vault-overview.md). Dla każdego konta z włączoną obsługą CMK należy podać klucz, który jest używany do szyfrowania wszystkich danych przechowywanych w ramach tego konta.

## <a name="setup"></a>Konfiguracja

Obecnie klucze zarządzane przez klienta są dostępne tylko dla nowych kont i należy je skonfigurować podczas tworzenia konta.

### <a name="1-make-sure-the-azure-cosmos-db-resource-provider-is-registered-for-your-azure-subscription"></a>1. Upewnij się, że dostawca zasobów Azure Cosmos DB został zarejestrowany dla Twojej subskrypcji platformy Azure

W Azure Portal przejdź do subskrypcji platformy Azure i wybierz pozycję **dostawcy zasobów** z menu po lewej stronie:

![Wpis "dostawcy zasobów" z menu po lewej stronie](./media/how-to-setup-cmk/portal-rp.png)

Wyszukaj dostawcę zasobów **Microsoft. DocumentDB** .
- Jeśli dostawca zasobów został już oznaczony jako zarejestrowany, nie trzeba nic robić.
- Jeśli nie, zaznacz ją i kliknij pozycję **zarejestruj**się:

    ![Rejestrowanie dostawcy zasobów Microsoft. DocumentDB](./media/how-to-setup-cmk/portal-rp-register.png)

### <a name="2-configure-your-azure-key-vault-instance"></a>2. Skonfiguruj wystąpienie Azure Key Vault

Używanie kluczy zarządzanych przez klienta z Azure Cosmos DB wymaga ustawienia dwóch właściwości w wystąpieniu Azure Key Vault, które ma być używane do hostowania kluczy szyfrowania: **usuwanie nietrwałe** i **nie przeczyszczanie**. Te właściwości nie są domyślnie włączone, ale można je włączyć przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

Aby dowiedzieć się, jak włączyć te właściwości na istniejącym wystąpieniu Azure Key Vault, zobacz sekcję zatytułowaną Włączanie usuwania nietrwałego i włączanie ochrony przed przeczyszczeniem w jednym z następujących artykułów:
- [Jak używać nietrwałego usuwania przy użyciu programu PowerShell](../key-vault/key-vault-soft-delete-powershell.md)
- [Jak używać nietrwałego usuwania przy użyciu interfejsu wiersza polecenia platformy Azure](../key-vault/key-vault-soft-delete-cli.md)

### <a name="3-add-an-access-policy-to-your-azure-key-vault-instance"></a>3. Dodaj zasady dostępu do wystąpienia Azure Key Vault

W Azure Portal przejdź do wystąpienia Azure Key Vault, które ma być używane do hostowania kluczy szyfrowania. Następnie wybierz pozycję **zasady dostępu** w menu po lewej stronie:

!["Zasady dostępu" z menu po lewej stronie](./media/how-to-setup-cmk/portal-akv-ap.png)

- Wybierz pozycję **+ Dodaj zasady dostępu**
- W menu rozwijanym **uprawnienia klucza** wybierz pozycję **Pobierz**, **Odpakuj klucz** i **Zawijaj klucz**:

    ![Wybieranie odpowiednich uprawnień](./media/how-to-setup-cmk/portal-akv-add-ap-perm2.png)

- W obszarze **Wybierz podmiot zabezpieczeń**wybierz pozycję **nie wybrano**. Następnie wyszukaj i wybierz **Azure Cosmos DB** podmiotu zabezpieczeń. Na koniec kliknij pozycję **Wybierz** u dołu (jeśli nie można znaleźć **Azure Cosmos DB** podmiotu zabezpieczeń, konieczne może być ponowne zarejestrowanie dostawcy zasobów **Microsoft. DocumentDB** w kroku 1):

    ![Wybieranie Azure Cosmos DB podmiotu zabezpieczeń](./media/how-to-setup-cmk/portal-akv-add-ap.png)

- Wybierz pozycję **Dodaj** , aby dodać nowe zasady dostępu

### <a name="4-generate-a-key-in-azure-key-vault"></a>4. Wygeneruj klucz w Azure Key Vault

W Azure Portal przejdź do wystąpienia Azure Key Vault, którego planujesz użyć do hostowania kluczy szyfrowania. Następnie wybierz pozycję **klucze** z menu po lewej stronie:

![Wpis "klucze" z menu po lewej stronie](./media/how-to-setup-cmk/portal-akv-keys.png)

- Wybierz pozycję **Generuj/Importuj**
- Podaj nazwę nowego klucza, wybierz rozmiar klucza RSA (co najmniej 3072 jest zalecane w celu uzyskania najlepszego zabezpieczenia) i wybierz pozycję **Utwórz**:

    ![Tworzenie nowego klucza](./media/how-to-setup-cmk/portal-akv-gen.png)

- Po utworzeniu klucza kliknij nowo utworzony klucz, a następnie w jego bieżącej wersji
- Skopiuj **Identyfikator klucza** klucza z wyjątkiem części po ostatnim ukośniku:

    ![Kopiowanie identyfikatora klucza klucza](./media/how-to-setup-cmk/portal-akv-keyid.png)

### <a name="5-create-a-new-azure-cosmos-db-account"></a>5. Utwórz nowe konto Azure Cosmos DB

#### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Podczas tworzenia nowego konta Azure Cosmos DB z Azure Portal, wybierz opcję **klucz zarządzany przez klienta** w kroku **szyfrowanie** . W polu **Identyfikator URI klucza** Przekaż identyfikator URI klucza Azure Key Vault skopiowanego z kroku 4:

![Ustawianie parametrów CMK w Azure Portal](./media/how-to-setup-cmk/portal-cosmos-enc.png)

#### <a name="using-powershell"></a>Używanie programu PowerShell

Podczas tworzenia nowego konta Azure Cosmos DB przy użyciu programu PowerShell
- Przekaż identyfikator URI klucza Azure Key Vault skopiowanego z kroku 4 we właściwości **keyVaultKeyUri** w **właściwościobject**,
- Upewnij się, że jako wersja interfejsu API jest używana wartość "2019-12-12".

> [!IMPORTANT]
> Należy jawnie ustawić parametr `Location`, aby konto zostało pomyślnie utworzone za pomocą CMK.

```powershell
$resourceGroupName = "myResourceGroup"
$accountLocation = "West US 2"
$accountName = "mycosmosaccount"

$failoverLocations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 }
)

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$failoverLocations;
    "keyVaultKeyUri" = "https://<my-vault>.vault.azure.net/keys/<my-key>";
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2019-12-12" -ResourceGroupName $resourceGroupName `
    -Location $accountLocation -Name $accountName -PropertyObject $CosmosDBProperties
```

#### <a name="using-azure-resource-manager-templates"></a>Korzystanie z szablonów Azure Resource Manager

Podczas tworzenia nowego konta Azure Cosmos DB przy użyciu szablonu Azure Resource Manager:
- Przekaż identyfikator URI klucza Azure Key Vault skopiowanego z kroku 4 we właściwości **keyVaultKeyUri** w obiekcie **Properties**
- Upewnij się, że jako wersja interfejsu API jest używana wartość "2019-12-12"

> [!IMPORTANT]
> Należy jawnie ustawić parametr `location`, aby konto zostało pomyślnie utworzone za pomocą CMK.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "keyVaultKeyUri": {
            "type": "string"
        }
    },
    "resources": 
    [
        {
            "type": "Microsoft.DocumentDB/databaseAccounts",
            "name": "[parameters('accountName')]",
            "apiVersion": "2019-12-12",
            "kind": "GlobalDocumentDB",
            "location": "[parameters('location')]",
            "properties": {
                "locations": [ 
                    {
                        "locationName": "[parameters('location')]",
                        "failoverPriority": 0,
                        "isZoneRedundant": false
                    }
                ],
                "databaseAccountOfferType": "Standard",
                "keyVaultKeyUri": "[parameters('keyVaultKeyUri')]"
            }
        }
    ]
}

```

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$accountLocation = "West US 2"
$keyVaultKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-key>"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "deploy.json" `
    -accountName $accountName `
    -location $accountLocation `
    -keyVaultKeyUri $keyVaultKeyUri
```

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="is-there-any-additional-charge-when-using-customer-managed-keys"></a>Czy w przypadku korzystania z kluczy zarządzanych przez klienta występuje dodatkowa opłata?

Tak. Aby można było obsłużyć dodatkowe obciążenie obliczeniowe wymagane do zarządzania szyfrowaniem i odszyfrowywaniem danych przy użyciu kluczy zarządzanych przez klienta, wszystkie operacje wykonywane na koncie Azure Cosmos DB są zwiększane o 25% w zużytych [jednostkach żądania](./request-units.md) .

### <a name="what-data-gets-encrypted-with-the-cmk"></a>Jakie dane są szyfrowane za pomocą CMK?

Wszystkie dane przechowywane na koncie Azure Cosmos DB są szyfrowane za pomocą CMK, z wyjątkiem następujących metadanych:
- nazwy [kont Azure Cosmos DB, baz danych i kontenerów](./account-overview.md#elements-in-an-azure-cosmos-account),
- nazwy [procedur przechowywanych](./stored-procedures-triggers-udfs.md),
- ścieżki właściwości zadeklarowane w [zasadach indeksowania](./index-policy.md),
- wartości [klucza partycji](./partitioning-overview.md)kontenerów.

### <a name="will-customer-managed-keys-be-supported-for-existing-accounts"></a>Czy klucze zarządzane przez klienta będą obsługiwane dla istniejących kont?

Ta funkcja jest obecnie dostępna tylko dla nowych kont.

### <a name="is-there-a-plan-to-support-finer-granularity-than-account-level-keys"></a>Czy istnieje plan obsługi bardziej szczegółowego stopnia szczegółowości niż klucze poziomu konta?

Obecnie nie są brane pod uwagę klucze poziomu kontenera.

### <a name="how-does-customer-managed-keys-affect-backups"></a>Jak klucze zarządzane przez klienta mają wpływ na kopie zapasowe?

Azure Cosmos DB [regularnie i automatycznie tworzy kopie zapasowe](./online-backup-and-restore.md) danych przechowywanych na koncie. Ta operacja tworzy kopię zapasową zaszyfrowanych danych. Aby przywrócona kopia zapasowa była użyteczna, klucz szyfrowania używany w czasie wykonywania kopii zapasowej musi być nadal dostępny. Oznacza to, że nie zostały wprowadzone żadne odwołania, a wersja klucza, która była używana w momencie tworzenia kopii zapasowej, nadal jest włączona.

### <a name="how-do-i-revoke-an-encryption-key"></a>Jak mogę odwołać klucz szyfrowania?

Odwoływanie klucza jest wykonywane przez wyłączenie najnowszej wersji klucza:

![Wyłączanie wersji klucza](./media/how-to-setup-cmk/portal-akv-rev2.png)

Alternatywnie, aby odwołać wszystkie klucze z wystąpienia Azure Key Vault, można usunąć zasady dostępu przyznane do Azure Cosmos DB podmiotu zabezpieczeń:

![Usuwanie zasad dostępu dla podmiotu zabezpieczeń Azure Cosmos DB](./media/how-to-setup-cmk/portal-akv-rev.png)

### <a name="what-operations-are-available-after-a-customer-managed-key-is-revoked"></a>Jakie operacje są dostępne po odwołaniu klucza zarządzanego przez klienta?

Jedyną operacją możliwą do odwołania klucza szyfrowania jest usunięcie konta.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [szyfrowaniu danych w Azure Cosmos DB](./database-encryption-at-rest.md)
- Zapoznaj się z omówieniem [bezpiecznego dostępu do danych w Cosmos DB](secure-access-to-data.md)