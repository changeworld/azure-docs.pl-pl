---
title: Wyświetlanie jednostki usługi tożsamości zarządzanej — interfejsu wiersza polecenia platformy Azure — azure ad
description: Instrukcje krok po kroku dotyczące wyświetlania jednostki usługi tożsamości zarządzanej przy użyciu interfejsu wiersza polecenia platformy Azure.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fedef003c6d4143d3ad1814081d55b61d5ee020
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298703"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-azure-cli"></a>Wyświetlanie jednostki usługi tożsamości zarządzanej przy użyciu interfejsu wiersza polecenia platformy Azure

Tożsamości zarządzane dla zasobów platformy Azure zapewniają usługom platformy Azure automatycznie zarządzaną tożsamość w usłudze Azure Active Directory. Tej tożsamości można użyć do uwierzytelniania do dowolnej usługi, która obsługuje uwierzytelnianie usługi Azure AD bez poświadczeń w kodzie. 

W tym artykule dowiesz się, jak wyświetlić jednostkę usługi tożsamości zarządzanej przy użyciu interfejsu wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz tożsamości zarządzanych dla zasobów platformy Azure, zapoznaj się z [sekcją omówienie](overview.md).
- Jeśli nie masz jeszcze konta platformy Azure, [zarejestruj się, aby uzyskać bezpłatne konto](https://azure.microsoft.com/free/).
- Włącz [tożsamość przypisaną do systemu na maszynie wirtualnej](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) lub [aplikacji](/azure/app-service/overview-managed-identity#add-a-system-assigned-identity).
- Aby uruchomić przykłady skryptów interfejsu wiersza polecenia, dostępne są trzy opcje:
    - Użyj [usługi Azure Cloud Shell](../../cloud-shell/overview.md) z witryny Azure portal (zobacz następną sekcję).
    - Użyj osadzonej usługi Azure Cloud Shell za pomocą przycisku "Wypróbuj", znajdującego się w prawym górnym rogu każdego bloku kodu.
    - [Zainstaluj najnowszą wersję interfejsu wiersza polecenia platformy Azure,](https://docs.microsoft.com/cli/azure/install-azure-cli) jeśli wolisz używać lokalnej konsoli interfejsu wiersza polecenia i zalogować się na platformie Azure przy użyciu`az login`
 
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="view-the-service-principal"></a>Wyświetl jednostkę usługi

To następujące polecenie pokazuje, jak wyświetlić jednostkę usługi maszyny Wirtualnej lub aplikacji z włączoną tożsamością zarządzaną. Zamień `<VM or application name>` na własne wartości. 

```azurecli-interactive
az ad sp list --display-name <VM or application name>
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat zarządzania podmiotami usługi Azure AD przy użyciu interfejsu wiersza polecenia platformy Azure, zobacz [az ad sp](/cli/azure/ad/sp).


