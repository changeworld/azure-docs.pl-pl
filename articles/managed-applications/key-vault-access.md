---
title: Użyj usługi Azure Key Vault z aplikacji zarządzanych | Dokumentacja firmy Microsoft
description: Ilustruje sposób używania wpisów tajnych dostępu w usłudze Azure Key Vault, wdrażając Managed Applications
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 07/09/2018
ms.author: tomfitz
ms.openlocfilehash: 232bea437b38335bdaa189e504d4e5fd9b080a05
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38724057"
---
# <a name="access-key-vault-secret-when-deploying-azure-managed-applications"></a>Dostęp do klucza tajnego usługi Key Vault, podczas wdrażania usługi Azure Managed Applications

Gdy potrzebujesz przekazać wartość bezpieczną (na przykład hasło) jako parametr podczas wdrażania można pobrać wartości z [usługi Azure Key Vault](../key-vault/key-vault-whatis.md). Aby uzyskać dostęp do usługi Key Vault, podczas wdrażania aplikacji zarządzanych, musi udzielić dostępu do **dostawcy zasobów urządzenia** nazwy głównej usługi. W tym artykule opisano sposób konfigurowania usługi Key Vault do pracy z aplikacji zarządzanych.

## <a name="enable-template-deployment"></a>Włącz wdrożenie szablonu

1. W portalu wybierz magazyn kluczy.

1. Wybierz pozycję **Zasady dostępu**.   

   ![Wybierz zasady dostępu](./media/key-vault-access/select-access-policies.png)

1. Wybierz **kliknij, aby wyświetlić zaawansowane zasady dostępu**.

   ![Pokaż zaawansowane zasady dostępu](./media/key-vault-access/advanced.png)

1. Wybierz **włączyć dostęp do usługi Azure Resource Manager dla wdrożenia szablonu**. Następnie wybierz pozycję **Zapisz**.

   ![Włącz wdrożenie szablonu](./media/key-vault-access/enable-template.png)

## <a name="add-service-as-contributor"></a>Dodaj usługę jako współautor

1. Wybierz **kontrola dostępu (IAM)**.

   ![Wybierz kontroli dostępu](./media/key-vault-access/access-control.png)

1. Wybierz pozycję **Dodaj**.

   ![Wybieranie opcji dodawania](./media/key-vault-access/add-access-control.png)

1. Wybierz **Współautor** dla roli. Wyszukaj **dostawcy zasobów urządzenia** i wybierz ją z listy dostępnych opcji.

   ![Wyszukiwanie dostawcy](./media/key-vault-access/search-provider.png)

1. Wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Kolejne kroki

Skonfigurowano usługi Key Vault była dostępna podczas wdrażania aplikacji zarządzanych.

* Aby uzyskać informacji o przekazywaniu wartości z magazynu kluczy, jako parametr szablonu, zobacz [użycia usługi Azure Key Vault do przekazywania wartości parametru secure podczas wdrażania](../azure-resource-manager/resource-manager-keyvault-parameter.md).
* Przykłady aplikacji zarządzanych, zobacz [przykładowych projektów dla platformy Azure zarządzane aplikacje](sample-projects.md).
* Aby dowiedzieć się, jak utworzyć plik definicji interfejsu użytkownika dla aplikacji zarządzanej, zobacz [Rozpoczynanie pracy z plikiem CreateUiDefinition](create-uidefinition-overview.md).