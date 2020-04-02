---
title: Konfigurowanie kluczy zarządzanych przez klienta przy użyciu portalu Azure
description: W tym artykule opisano sposób konfigurowania szyfrowania kluczy zarządzanych przez klienta na danych w Eksploratorze danych platformy Azure.
author: orspod
ms.author: orspodek
ms.reviewer: itsagui
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: 180196f2c368207b76811700fd845406098600df
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529435"
---
# <a name="configure-customer-managed-keys-using-the-azure-portal"></a>Konfigurowanie kluczy zarządzanych przez klienta za pomocą portalu Azure

> [!div class="op_single_selector"]
> * [Portal](customer-managed-keys-portal.md)
> * [C#](customer-managed-keys-csharp.md)
> * [Szablon usługi Azure Resource Manager](customer-managed-keys-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](../../includes/data-explorer-configure-customer-managed-keys.md)]

## <a name="enable-encryption-with-customer-managed-keys-in-the-azure-portal"></a>Włączanie szyfrowania za pomocą kluczy zarządzanych przez klienta w witrynie Azure portal

W tym artykule pokazano, jak włączyć szyfrowanie kluczy zarządzanych przez klienta przy użyciu witryny Azure portal. Domyślnie szyfrowanie usługi Azure Data Explorer używa kluczy zarządzanych przez firmę Microsoft. Skonfiguruj klaster usługi Azure Data Explorer do używania kluczy zarządzanych przez klienta i określ klucz do skojarzenia z klastrem.

1. W [witrynie Azure portal](https://portal.azure.com/)przejdź do zasobu [klastra usługi Azure Data Explorer.](create-cluster-database-portal.md#create-a-cluster) 
1. Wybierz **pozycję Szyfrowanie ustawień** > **Encryption** w lewym okienku portalu.
1. W okienku **Szyfrowanie** wybierz pozycję **Włącz** dla ustawienia **klucza zarządzanego przez klienta.**
1. Kliknij **pozycję Wybierz klawisz**.

    ![Konfigurowanie kluczy zarządzanych przez klienta](media/customer-managed-keys-portal/cmk-encryption-setting.png)

1. W oknie **Wybierz klucz z usługi Azure Key Vault** wybierz istniejącą **przechowalnię kluczy** z listy rozwijanej. Jeśli wybierzesz **Pozycję Utwórz nowy,** aby [utworzyć nową przechowalnię kluczy,](/azure/key-vault/quick-create-portal#create-a-vault)zostaniesz przekierowany na ekran **Tworzenie magazynu kluczy.**

1. Wybierz **klawisz**.
1. Wybierz **wersja**.
1. Kliknij **pozycję Wybierz**.

    ![Wybieranie klucza z usługi Azure Key Vault](media/customer-managed-keys-portal/cmk-key-vault.png)

1. W okienku **Szyfrowanie,** które teraz zawiera klucz, wybierz pozycję **Zapisz**. Gdy tworzenie cmk zakończy się pomyślnie, zobaczysz komunikat o sukcesie w **powiadomieniach**.

    ![Zapisywanie klucza zarządzanego przez klienta](media/customer-managed-keys-portal/cmk-encryption-setting.png)

Włączając klucze zarządzane przez klienta dla klastra usługi Azure Data Explorer, będziesz tworzyć tożsamość przypisaną do systemu dla klastra, jeśli nie istnieje. Ponadto będziesz dostarczać wymagane uprawnienia get, wrapKey i unwarpKey do klastra usługi Azure Data Explorer w wybranym magazynie kluczy i uzyskać właściwości usługi Key Vault. 

> [!NOTE]
> Wybierz **opcję Wyłączone,** aby usunąć klucz zarządzany przez klienta po jego utworzeniu.

## <a name="next-steps"></a>Następne kroki

* [Zabezpieczanie klastrów eksploratora danych platformy Azure na platformie Azure](security.md)
* [Zabezpiecz swój klaster w usłudze Azure Data Explorer — witryna Azure portal,](manage-cluster-security.md) włączając szyfrowanie w spoczynku.
* [Konfigurowanie kluczy zarządzanych przez klienta przy użyciu szablonu usługi Azure Resource Manager](customer-managed-keys-resource-manager.md)
* [Konfigurowanie kluczy zarządzanych przez klienta przy użyciu języka C #](customer-managed-keys-csharp.md)



