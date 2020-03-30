---
title: Wyświetlanie jednostki usługi tożsamości zarządzanej w witrynie Azure portal — Azure AD
description: Instrukcje krok po kroku dotyczące wyświetlania jednostki usługi tożsamości zarządzanej w witrynie Azure portal.
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
ms.openlocfilehash: c12f15cc79d5329d028239ade4e18a853000bf01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298601"
---
# <a name="view-the-service-principal-of-a-managed-identity-in-the-azure-portal"></a>Wyświetlanie jednostki usługi tożsamości zarządzanej w witrynie Azure portal

Tożsamości zarządzane dla zasobów platformy Azure zapewniają usługom platformy Azure automatycznie zarządzaną tożsamość w usłudze Azure Active Directory. Tej tożsamości można użyć do uwierzytelniania do dowolnej usługi, która obsługuje uwierzytelnianie usługi Azure AD, bez poświadczeń w kodzie. 

W tym artykule dowiesz się, jak wyświetlić jednostkę usługi tożsamości zarządzanej przy użyciu witryny Azure portal.

 > [!NOTE] 
 > Podmioty zabezpieczeń usług to aplikacje dla przedsiębiorstw. 

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz tożsamości zarządzanych dla zasobów platformy Azure, zapoznaj się z [sekcją omówienie](overview.md).
- Jeśli nie masz jeszcze konta platformy Azure, [zarejestruj się, aby uzyskać bezpłatne konto](https://azure.microsoft.com/free/).
- Włącz [tożsamość przypisaną do systemu na maszynie wirtualnej](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) lub [aplikacji](/azure/app-service/overview-managed-identity#add-a-system-assigned-identity).

## <a name="view-the-service-principal"></a>Wyświetl jednostkę usługi

W tej procedurze pokazano, jak wyświetlić jednostkę usługi maszyny Wirtualnej z włączoną tożsamością przypisaną do systemu (te same kroki dotyczą aplikacji).

1. Kliknij **pozycję Usługa Azure Active Directory,** a następnie kliknij pozycję **Aplikacje przedsiębiorstwa**.
2. W obszarze **Typ aplikacji**wybierz pozycję **Wszystkie aplikacje,** a następnie kliknij pozycję **Zastosuj**.
3. W polu filtru wyszukiwania wpisz nazwę maszyny Wirtualnej lub aplikacji, która ma włączoną tożsamość lub wybierz ją z prezentowanej listy.

   ![Wyświetlanie jednostki usługi tożsamości zarządzanej w portalu](./media/how-to-view-managed-identity-service-principal-portal/view-managed-identity-service-principal-portal.png)

## <a name="next-steps"></a>Następne kroki

[Tożsamości zarządzane dla zasobów platformy Azure](/azure/active-directory/managed-identities-azure-resources/overview)

