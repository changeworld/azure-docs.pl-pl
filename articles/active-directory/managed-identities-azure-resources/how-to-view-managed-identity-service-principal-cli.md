---
title: Sposób wyświetlania nazwy głównej usługi z tożsamości zarządzanej przy użyciu wiersza polecenia platformy Azure
description: Szczegółowe instrukcje dotyczące wyświetlania nazwy głównej usługi z tożsamości zarządzanej przy użyciu wiersza polecenia platformy Azure.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: priyamo
ms.openlocfilehash: 8aadb8f0f8b3de9aab37689cc80cea211ad01165
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55162280"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-azure-cli"></a>Wyświetl jednostki usługi z tożsamości zarządzanej przy użyciu wiersza polecenia platformy Azure

Zarządzanych tożsamości dla zasobów platformy Azure udostępnia usługi platformy Azure przy użyciu automatycznie zarządzanych tożsamości w usłudze Azure Active Directory. Można użyć tej tożsamości do uwierzytelniania na dowolne usługi obsługujące uwierzytelnianie usługi Azure AD bez poświadczeń w kodzie. 

W tym artykule dowiesz się, jak wyświetlić jednostki usługi z tożsamości zarządzanej przy użyciu wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli jesteś zaznajomiony z zarządzanych tożsamości dla zasobów platformy Azure, zapoznaj się z [sekcji Przegląd](overview.md).
- Jeśli nie masz jeszcze konta platformy Azure, [Załóż bezpłatne konto](https://azure.microsoft.com/free/).
- Włącz [tożsamości przypisanej w systemie na maszynie wirtualnej](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) lub [aplikacji](/azure/app-service/overview-managed-identity#adding-a-system-assigned-identity).
- Aby uruchomić przykłady skryptów interfejsu wiersza polecenia, masz trzy opcje:
    - Użyj [usługi Azure Cloud Shell](../../cloud-shell/overview.md) w witrynie Azure portal (patrz następny rozdział).
    - Użyj osadzonego usługi Azure Cloud Shell za pomocą "Try It" przycisk znajdujący się w prawym górnym rogu każdego bloku kodu.
    - [Zainstaluj najnowszą wersję interfejsu wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) Jeśli wolisz używać lokalnej konsoli interfejsu wiersza polecenia i logowanie się do platformy Azure za pomocą `az login`
 
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="view-the-service-principal"></a>Wyświetl nazwy głównej usługi

Następujące polecenie pokazuje sposób wyświetlania nazwy głównej usługi, maszyny Wirtualnej lub aplikacji za pomocą tożsamości zarządzanej włączone. Zastąp `<VM or application name>` własnymi wartościami. 

```azurecli-interactive
az ad sp list --display-name <VM or application name>
```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat zarządzania jednostki usługi Azure AD przy użyciu wiersza polecenia platformy Azure, zobacz [az ad sp](/cli/azure/ad/sp).


