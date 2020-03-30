---
title: Aktualizowanie konfiguracji magazynu usług odzyskiwania za pomocą interfejsu API REST
description: W tym artykule dowiesz się, jak zaktualizować konfigurację magazynu przy użyciu interfejsu API REST.
ms.topic: conceptual
ms.date: 12/06/2019
ms.assetid: 9aafa5a0-1e57-4644-bf79-97124db27aa2
ms.openlocfilehash: 6cecbb18e0cd6f548e1688ef978f10dcee7d9fbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252365"
---
# <a name="update-azure-recovery-services-vault-configurations-using-rest-api"></a>Aktualizowanie konfiguracji magazynu usług odzyskiwania platformy Azure przy użyciu interfejsu API REST

W tym artykule opisano sposób aktualizowania konfiguracji powiązanych z tworzeniem kopii zapasowych w magazynie usług Azure Recovery Services przy użyciu interfejsu API REST.

## <a name="soft-delete-state"></a>Stan usuwania nietrwałego

Usuwanie kopii zapasowych chronionego elementu jest istotną operacją, która musi być monitorowana. Aby chronić przed przypadkowymi usunięciami, magazyn usług Azure Recovery Services ma funkcję usuwania nietrwałego. Ta funkcja umożliwia klientom przywracanie usuniętych kopii zapasowych, jeśli to konieczne, w okresie po usunięciu.

Istnieją jednak scenariusze, w których ta funkcja nie jest wymagana. Nie można usunąć magazynu usług odzyskiwania platformy Azure, jeśli znajdują się w niej elementy kopii zapasowej, nawet usunięte nietrwale. Może to stanowić problem, jeśli magazyn musi zostać natychmiast usunięty. Na przykład: operacje wdrażania często czyszczą utworzone zasoby w tym samym przepływie pracy. Wdrożenie może utworzyć magazyn, skonfigurować kopie zapasowe dla elementu, wykonać przywracanie testowe, a następnie przystąpić do usuwania elementów kopii zapasowej i magazynu. Jeśli usunięcie magazynu nie powiedzie się, całe wdrożenie może zakończyć się niepowodzeniem. Wyłączenie usuwania nietrwałego jest jedynym sposobem zagwarantowania natychmiastowego usunięcia.

W związku z tym klient musi starannie wybrać, czy wyłączyć soft-delete dla określonego magazynu w zależności od scenariusza. Aby uzyskać więcej informacji, zobacz [artykuł o usuwaniu nietrwałym](backup-azure-security-feature-cloud.md#soft-delete).

### <a name="fetch-soft-delete-state-using-rest-api"></a>Pobieranie stanu usuwania nietrwałego przy użyciu interfejsu API REST

Domyślnie stan usuwania nietrwałego będzie włączony dla każdego nowo utworzonego magazynu usług odzyskiwania. Aby pobrać/zaktualizować stan usuwania nietrwałego dla magazynu, użyj [dokumentu interfejsu API interfejsu REST](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs) związanych z konfiguracyjnym magazynu kopii zapasowych

Aby pobrać bieżący stan usuwania nietrwałego dla przechowalni, użyj następującej operacji *GET*

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-05-13
```

Identyfikator URI `{subscriptionId}`GET `{vaultName}` `{vaultresourceGroupName}` ma , , parametry. W tym `{vaultName}` przykładzie jest "testVault" i `{vaultresourceGroupName}` jest "testVaultRG". Ponieważ wszystkie wymagane parametry są podane w identyfikatorze URI, nie ma potrzeby oddzielnej treści żądania.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-05-13
```

#### <a name="responses"></a>Odpowiedzi

Pomyślna odpowiedź dla operacji "GET" jest pokazana poniżej:

|Nazwa  |Typ  |Opis  |
|---------|---------|---------|
|200 ok.     |   [Kopia zapasowaWwsoconfig](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | OK        |

##### <a name="example-response"></a>Przykładowa odpowiedź

Po przesłaniu żądania "GET" zwracana jest odpowiedź 200 (pomyślna).

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testvaultRG/providers/Microsoft.RecoveryServices/vaults/testvault/backupconfig/vaultconfig",
  "name": "vaultconfig",
  "type": "Microsoft.RecoveryServices/vaults/backupconfig",
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Enabled"
  }
}
```

### <a name="update-soft-delete-state-using-rest-api"></a>Aktualizowanie stanu usuwania nietrwałego przy użyciu interfejsu API REST

Aby zaktualizować stan usuwania nietrwałego magazynu usług odzyskiwania przy użyciu interfejsu API REST, należy użyć następującej operacji *PATCH*

```http
PATCH https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-05-13
```

Identyfikator URI `{subscriptionId}`patcha ma parametry `{vaultName}`, . `{vaultresourceGroupName}` W tym `{vaultName}` przykładzie jest "testVault" i `{vaultresourceGroupName}` jest "testVaultRG". Jeśli zastąpimy identyfikator URI z powyższymi wartościami, identyfikator URI będzie wyglądać następująco.

```http
PATCH https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-05-13
```

#### <a name="create-the-request-body"></a>Tworzenie treści żądania

TOn następujące wspólne definicje są używane do tworzenia treści żądania

Więcej informacji można znaleźć [w dokumentacji interfejsu API REST](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/update#request-body)

|Nazwa  |Wymagany  |Typ  |Opis  |
|---------|---------|---------|---------|
|Etag     |         |   Ciąg      |  Opcjonalny eTag       |
|location     |  true       |Ciąg         |   Lokalizacja zasobu      |
|properties     |         | [Właściwości VaultProperties](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  Właściwości przechowalni       |
|tags     |         | Obiekt        |     Tagi zasobów    |

#### <a name="example-request-body"></a>Przykładowa treść żądania

Poniższy przykład służy do aktualizacji stanu usuwania nietrwałego do "wyłączone".

```json
{
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Disabled"
  }
}
```

#### <a name="responses"></a>Odpowiedzi

Poniżej przedstawiono pomyślną odpowiedź na operację "PATCH":

|Nazwa  |Typ  |Opis  |
|---------|---------|---------|
|200 ok.     |   [Kopia zapasowaWwsoconfig](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | OK        |

##### <a name="example-response"></a>Przykładowa odpowiedź

Po przesłaniu żądania "PATCH" zwracana jest odpowiedź 200 (pomyślna).

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testvaultRG/providers/Microsoft.RecoveryServices/vaults/testvault/backupconfig/vaultconfig",
  "name": "vaultconfig",
  "type": "Microsoft.RecoveryServices/vaults/backupconfig",
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Disabled"
  }
}
```

## <a name="next-steps"></a>Następne kroki

[Utwórz zasady tworzenia kopii zapasowych do tworzenia kopii zapasowych maszyny Wirtualnej platformy Azure w tym magazynie](backup-azure-arm-userestapi-createorupdatepolicy.md).

Aby uzyskać więcej informacji na temat interfejsów API usługi Azure REST, zobacz następujące dokumenty:

- [Interfejs API REST dostawcy usług odzyskiwania platformy Azure](/rest/api/recoveryservices/)
- [Get started with Azure REST API (Rozpoczęcie pracy z interfejsem API REST platformy Azure)](/rest/api/azure/)
