---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/06/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 57368331e83111836e13b6c3796cacad9816e318
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521293"
---
Aby wyświetlić i skopiować klucze dostępu do konta magazynu lub parametry połączenia z witryny Azure Portal:

1. Przejdź do [witryny Azure portal](https://portal.azure.com).
2. Odszukaj konto magazynu.
3. W obszarze **Ustawienia**wybierz pozycję **Klawisze dostępu**. Zostaną wyświetlone klucze dostępu do Twojego konta, a także pełne parametry połączenia dla każdego klucza.
4. Znajdź wartość **Klucz** w obszarze **key1** i kliknij przycisk **Kopiuj**, aby skopiować klucz konta.
5. Alternatywnie można skopiować cały ciąg połączenia. Znajdź wartość **Parametry połączenia** w obszarze **key1**i kliknij przycisk **Kopiuj**, aby skopiować parametry połączenia.

    ![Zrzut ekranu przedstawiający sposób wyświetlania kluczy dostępu w witrynie Azure portal](media/storage-view-keys-include/portal-connection-string.png)

Możesz użyć albo klucz, aby uzyskać dostęp do usługi Azure Storage, ale ogólnie jest dobrą praktyką, aby użyć pierwszego klucza i zarezerwować użycie drugiego klucza podczas obracania kluczy.

Aby wyświetlić lub odczytać klucze dostępu do konta, użytkownik musi być administratorem usługi lub musi mieć przypisaną rolę RBAC, która zawiera **microsoft.Storage/storageAccounts/listkeys/action**. Niektóre wbudowane role RBAC, które zawierają tę akcję, to role roli roli usługi **Właściciel,** **Współautor**i **Operator klucza magazynu.** Aby uzyskać więcej informacji na temat roli administratora usługi, zobacz [klasyczne role administratora subskrypcji, role usługi Azure RBAC i role usługi Azure AD](../articles/role-based-access-control/rbac-and-directory-admin-roles.md). Aby uzyskać szczegółowe informacje na temat wbudowanych ról usługi Azure Storage, zobacz sekcję **Magazyn** [w wbudowanych rolach platformy Azure dla usługi Azure RBAC.](../articles/role-based-access-control/built-in-roles.md#storage)
