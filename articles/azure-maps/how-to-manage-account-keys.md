---
title: Jak zarządzać swoim kontem usługi Azure Maps i klucze | Dokumentacja firmy Microsoft
description: Za pomocą witryny Azure portal do zarządzania kontem usługi Azure Maps oraz Zarządzaj kluczami dostępu.
author: dsk-2015
ms.author: dkshir
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 38926cc42b44e61bd83f6b1e33946a9026cda06d
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2018
ms.locfileid: "38989405"
---
# <a name="how-to-manage-your-azure-maps-account-and-keys"></a>Jak zarządzać swoim kontem usługi Azure Maps i klucze

Możesz zarządzać swoim kontem usługi Azure Maps i klucze w witrynie Azure portal. Po utworzeniu konta i klucz, można zaimplementować interfejsów API w witrynie sieci web lub aplikacji mobilnej.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-new-account"></a>Utwórz nowe konto

1. Zaloguj się w [Portalu Azure](http://portal.azure.com).

1. W lewym górnym rogu witryny Azure Portal kliknij przycisk **Utwórz zasób**.

2. Wyszukaj i wybierz pozycję **mapy**, następnie kliknij przycisk **Utwórz**.

3. Wprowadź informacje dla nowego konta. 

![Wprowadź informacje o koncie w portalu](./media/how-to-manage-account-keys/new-account-portal.png)

## <a name="manage-keys-on-the-account-page"></a>Zarządzanie kluczami na stronie konta

Po utworzeniu konta otrzymasz dwa klucze losowo generowany. Klawisze do uwierzytelniania względem interfejsów API usługi mapy, gdy chcesz pobrać dane mapy lub Utwórz nowe wystąpienie mapy JavaScript. 

Klucze możesz znaleźć w witrynie Azure portal. Przejdź do swojego konta, a następnie wybierz pozycję **klucze** z menu.

![Zarządzanie kluczami konta w portalu](./media/how-to-manage-account-keys/account-keys-portal.png)

Na tej stronie możesz skopiować klucze lub wygenerować nowe. 

## <a name="delete-an-account"></a>Usuwanie konta

Możesz usunąć konto w witrynie Azure portal. Przejdź do strony Przegląd konta i wybierz **Usuń**.

![Usunąć swoje konto w portalu](./media/how-to-manage-account-keys/account-delete-portal.png)

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak używać [mapy interfejsu API zarządzania](https://docs.microsoft.com/rest/api/maps-management/accounts) do tworzenia, aktualizowania i usuwania kontami usługi Maps. 