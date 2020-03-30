---
title: Szyfrowanie danych — witryna Azure portal — dla usługi Azure Database for PostgreSQL — pojedynczy serwer
description: Dowiedz się, jak skonfigurować szyfrowanie danych dla usługi Azure Database dla pojedynczego serwera PostgreSQL i zarządzać nimi przy użyciu witryny Azure portal.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: fe4c69787b606c601d2dc8b31cadc6dcf57458da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297071"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-by-using-the-azure-portal"></a>Szyfrowanie danych dla usługi Azure Database dla pojedynczego serwera PostgreSQL przy użyciu witryny Azure portal

Dowiedz się, jak skonfigurować szyfrowanie danych dla usługi Azure Database dla pojedynczego serwera PostgreSQL za pomocą portalu Azure i zarządzać nimi.

## <a name="prerequisites-for-azure-cli"></a>Wymagania wstępne dotyczące interfejsu wiersza polecenia platformy Azure

* Musisz mieć subskrypcję platformy Azure i być administratorem w tej subskrypcji.
* W usłudze Azure Key Vault utwórz magazyn kluczy i klucz do użycia dla klucza zarządzanego przez klienta.
* Magazyn kluczy musi mieć następujące właściwości, aby użyć go jako klucza zarządzanego przez klienta:
  * [Usuwanie nietrwałe](../key-vault/key-vault-ovw-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -test -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Czyszczenie chronione](../key-vault/key-vault-ovw-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* Klucz musi mieć następujące atrybuty, aby użyć go jako klucza zarządzanego przez klienta:
  * Brak daty ważności
  * Nie wyłączono
  * Możliwość wykonywania operacji get, wrap key i unwrapowania

## <a name="set-the-right-permissions-for-key-operations"></a>Ustawianie odpowiednich uprawnień dla operacji kluczy

1. W obszarze Przechowalnia kluczy wybierz pozycję **Zasady** > dostępu**Dodaj zasady dostępu**.

   ![Zrzut ekranu przedstawiający przechowalnię kluczy z wyróżnioną pojwale zasad programu Access i dodaj zasady dostępu](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. Wybierz **opcję Uprawnienia klucza**i wybierz pozycję **Pobierz**, **Zawiń**, **Rozpakuj**i **główny podmiot ,** który jest nazwą serwera PostgreSQL. Jeśli podmiotu zabezpieczeń serwera nie można znaleźć na liście istniejących podmiotów, należy go zarejestrować. Podczas pierwszej próby skonfigurowania szyfrowania danych po raz pierwszy zostanie wyświetlony monit o zarejestrowanie podmiotu zabezpieczeń serwera, co nie powiedzie się.  

   ![Omówienie zasad dostępu](media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png)

3. Wybierz **pozycję Zapisz**.

## <a name="set-data-encryption-for-azure-database-for-postgresql-single-server"></a>Ustawianie szyfrowania danych dla usługi Azure Database dla pojedynczego serwera PostgreSQL

1. W usłudze Azure Database for PostgreSQL wybierz **opcję Szyfrowanie danych,** aby skonfigurować klucz zarządzany przez klienta.

   ![Zrzut ekranu przedstawiający usługę Azure Database for PostgreSQL z wyróżnioną pozycją szyfrowanie danych](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. Można wybrać przechowalnię kluczy i parę kluczy lub wprowadzić identyfikator klucza.

   ![Zrzut ekranu przedstawiający usługę Azure Database for PostgreSQL z wyróżnionymi opcjami szyfrowania danych](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. Wybierz **pozycję Zapisz**.

4. Aby upewnić się, że wszystkie pliki (w tym pliki tymczasowe) są w pełni zaszyfrowane, uruchom ponownie serwer.

## <a name="restore-or-create-a-replica-of-the-server"></a>Przywracanie lub tworzenie repliki serwera

Po usłudze Azure Database for PostgreSQL Single server jest szyfrowany za pomocą klucza zarządzanego klienta przechowywane w key vault, każda nowo utworzona kopia serwera jest również szyfrowana. Tę nową kopię można wykonać za pomocą operacji lokalnego lub przywracania geograficznego lub za pośrednictwem operacji repliki (lokalnej/międzyregionowej). Tak więc dla zaszyfrowanego serwera PostgreSQL można wykonać następujące kroki, aby utworzyć zaszyfrowany serwer przywrócony.

1. Na serwerze wybierz pozycję **Przegląd** > **przywracania**.

   ![Zrzut ekranu przedstawiający usługę Azure Database for PostgreSQL z wyróżnioną pozycją Przegląd i przywracanie](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   Lub dla serwera obsługującego replikację w nagłówku **Ustawienia** wybierz pozycję **Replikacja**.

   ![Zrzut ekranu przedstawiający usługę Azure Database for PostgreSQL z wyróżnioną wyróżnioną funkcją replikacji](media/concepts-data-access-and-security-data-encryption/postgresql-replica.png)

2. Po zakończeniu operacji przywracania nowy utworzony serwer jest szyfrowany przy pomocą klucza serwera podstawowego. Jednak funkcje i opcje na serwerze są wyłączone, a serwer jest niedostępny. Zapobiega to manipulowaniu danymi, ponieważ tożsamość nowego serwera nie uzyskała jeszcze uprawnień dostępu do magazynu kluczy.

   ![Zrzut ekranu przedstawiający usługę Azure Database for PostgreSQL z wyróżnionym stanem niedostępnym](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. Aby udostępnić serwer, ponownie zatrwalić klucz na przywróconym serwerze. Wybierz > **pozycję Klucz ponownej oceny szyfrowania** **danych**.

   > [!NOTE]
   > Pierwsza próba ponownego oceny nie powiedzie się, ponieważ nowy podmiot usługi serwera musi mieć dostęp do magazynu kluczy. Aby wygenerować jednostkę usługi, wybierz **klucz ponownej oceny,** który wyświetli błąd, ale generuje jednostkę usługi. Następnie zapoznaj się z [tymi krokami](#set-the-right-permissions-for-key-operations) wcześniej w tym artykule.

   ![Zrzut ekranu przedstawiający usługę Azure Database for PostgreSQL z wyróżnionym krokiem ponownego unieważnienia](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   Będziesz musiał dać dostęp do magazynu kluczy do nowego serwera.

4. Po zarejestrowaniu jednostki usługi ponownie przywtniu certyfikat klucza, a serwer wznawia jego normalne funkcje.

   ![Zrzut ekranu przedstawiający usługę Azure Database for PostgreSQL z przywróconą funkcjonalnością](media/concepts-data-access-and-security-data-encryption/restore-successful.png)

## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>Włączanie szyfrowania danych za pomocą szablonu usługi Azure Resource Manager

Oprócz witryny Azure portal można również włączyć szyfrowanie danych na jednym serwerze usługi Azure Database dla postgreSQL przy użyciu szablonów usługi Azure Resource Manager dla nowego i istniejącego serwera.

### <a name="for-a-new-server"></a>Dla nowego serwera

Użyj jednego ze wstępnie utworzonych szablonów usługi Azure Resource Manager, aby aprowizować serwer z włączonym szyfrowaniem danych: [Przykład z szyfrowaniem danych](https://github.com/Azure/azure-postgresql/tree/master/arm-templates/ExampleWithDataEncryption)

Ten szablon usługi Azure Resource Manager tworzy usługę Azure Database dla pojedynczego serwera PostgreSQL i używa **keyvault** i **key** przekazywane jako parametry, aby włączyć szyfrowanie danych na serwerze.

### <a name="for-an-existing-server"></a>Dla istniejącego serwera
Ponadto można użyć szablonów usługi Azure Resource Manager, aby włączyć szyfrowanie danych na istniejących serwerach usługi Azure Database dla pojedynczych serwerów PostgreSQL.

* Przekaż identyfikator URI klucza usługi Azure Key Vault, który został skopiowany wcześniej pod właściwością `keyVaultKeyUri` w obiekcie właściwości.

* Użyj *wersji interfejsu API 2020-01-01-preview.*

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string"
    },
    "serverName": {
      "type": "string"
    },
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Key vault name where the key to use is stored"
      }
    },
    "keyVaultResourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "Key vault resource group name where it is stored"
      }
    },
    "keyName": {
      "type": "string",
      "metadata": {
        "description": "Key name in the key vault to use as encryption protector"
      }
    },
    "keyVersion": {
      "type": "string",
      "metadata": {
        "description": "Version of the key in the key vault to use as encryption protector"
      }
    }
  },
  "variables": {
    "serverKeyName": "[concat(parameters('keyVaultName'), '_', parameters('keyName'), '_', parameters('keyVersion'))]"
  },
  "resources": [
    {
      "type": "Microsoft.DBforPostgreSQL/servers",
      "apiVersion": "2017-12-01",
      "kind": "",
      "location": "[parameters('location')]",
      "identity": {
        "type": "SystemAssigned"
      },
      "name": "[parameters('serverName')]",
      "properties": {
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-05-01",
      "name": "addAccessPolicy",
      "resourceGroup": "[parameters('keyVaultResourceGroupName')]",
      "dependsOn": [
        "[resourceId('Microsoft.DBforPostgreSQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.KeyVault/vaults/accessPolicies",
              "name": "[concat(parameters('keyVaultName'), '/add')]",
              "apiVersion": "2018-02-14-preview",
              "properties": {
                "accessPolicies": [
                  {
                    "tenantId": "[subscription().tenantId]",
                    "objectId": "[reference(resourceId('Microsoft.DBforPostgreSQL/servers/', parameters('serverName')), '2017-12-01', 'Full').identity.principalId]",
                    "permissions": {
                      "keys": [
                        "get",
                        "wrapKey",
                        "unwrapKey"
                      ]
                    }
                  }
                ]
              }
            }
          ]
        }
      }
    },
    {
      "name": "[concat(parameters('serverName'), '/', variables('serverKeyName'))]",
      "type": "Microsoft.DBforPostgreSQL/servers/keys",
      "apiVersion": "2020-01-01-preview",
      "dependsOn": [
        "addAccessPolicy",
        "[resourceId('Microsoft.DBforPostgreSQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "serverKeyType": "AzureKeyVault",
        "uri": "[concat(reference(resourceId(parameters('keyVaultResourceGroupName'), 'Microsoft.KeyVault/vaults/', parameters('keyVaultName')), '2018-02-14-preview', 'Full').properties.vaultUri, 'keys/', parameters('keyName'), '/', parameters('keyVersion'))]"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Następne kroki

 Aby dowiedzieć się więcej na temat szyfrowania danych, zobacz [Usługa Azure Database for PostgreSQL Single server data encryption with customer-managed key](concepts-data-encryption-postgresql.md).
