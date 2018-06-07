---
title: Jak zarządzać Twoje konto Azure map i klucze | Dokumentacja firmy Microsoft
description: Azure portal umożliwia zarządzanie kontem mapy Azure i zarządzanie kluczami dostępu.
author: kgremban
ms.author: kgremban
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 153fb87b0e2b576fd67a24bb833165f2a1c93c56
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34599664"
---
# <a name="how-to-manage-your-azure-maps-account-and-keys"></a>Jak zarządzać Twoje konto Azure map i kluczy

Twoje konto Azure map i klucze za pomocą portalu Azure można zarządzać. Po utworzeniu konta i klucz, można zaimplementować interfejsów API w witrynie sieci web lub aplikacji mobilnej.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-new-account"></a>Utwórz nowe konto

1. Zaloguj się w [Portalu Azure](http://portal.azure.com).

1. W lewym górnym rogu witryny Azure Portal kliknij przycisk **Utwórz zasób**.

2. Wyszukaj i wybierz **mapy**, następnie kliknij przycisk **Utwórz**.

3. Wprowadź informacje o Twoim koncie. 

![Wprowadź informacje o koncie w portalu](./media/how-to-manage-account-keys/new-account-portal.png)

## <a name="manage-keys-on-the-account-page"></a>Zarządzanie kluczami na stronie konta

Po utworzeniu konta otrzymasz dwa klucze losowo wygenerowany. Klucze służy do uwierzytelniania względem interfejsów API map, jeśli chcesz pobrać dane mapy lub Utwórz nowe wystąpienie mapy JavaScript. 

Klucze można znaleźć w portalu Azure. Przejdź do swojego konta, a następnie wybierz **klucze** z menu.

![Zarządzanie kluczami konta w portalu](./media/how-to-manage-account-keys/account-keys-portal.png)

Na tej stronie można skopiować kluczy lub wygenerować nowe. 

## <a name="delete-an-account"></a>Usuń konto

Możesz usunąć konto z portalu Azure. Przejdź do strony Przegląd konta i wybierz **usunąć**.

![Usuwanie konta w portalu](./media/how-to-manage-account-keys/account-delete-portal.png)

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak używać [mapy interfejsu API zarządzania](https://docs.microsoft.com/rest/api/maps-management/accounts) do tworzenia, aktualizowania i usuwania kont mapy. 