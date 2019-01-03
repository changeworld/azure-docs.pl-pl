---
title: Jak wyświetlić jednostka tożsamość zarządzaną w witrynie Azure portal
description: Szczegółowe instrukcje dotyczące wyświetlania jednostka tożsamość zarządzaną w witrynie Azure portal.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: daveba
ms.openlocfilehash: c61220242a5a2ff84204c84b11aa132d00279e49
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2018
ms.locfileid: "53726599"
---
# <a name="view-the-service-principal-of-a-managed-identity-in-the-azure-portal"></a>Wyświetl jednostki usługi tożsamości zarządzanych w witrynie Azure portal

Zarządzanych tożsamości dla zasobów platformy Azure udostępnia usługi platformy Azure przy użyciu automatycznie zarządzanych tożsamości w usłudze Azure Active Directory. Można użyć tej tożsamości do uwierzytelniania na dowolne usługi obsługujące uwierzytelnianie usługi Azure AD bez poświadczeń w kodzie. 

W tym artykule dowiesz się, jak wyświetlić jednostki usługi z tożsamości zarządzanej przy użyciu witryny Azure portal.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli jesteś zaznajomiony z zarządzanych tożsamości dla zasobów platformy Azure, zapoznaj się z [sekcji Przegląd](overview.md).
- Jeśli nie masz jeszcze konta platformy Azure, [Załóż bezpłatne konto](https://azure.microsoft.com/free/).
- Włącz [tożsamości przypisanej w systemie na maszynie wirtualnej](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) lub [aplikacji](/azure/app-service/overview-managed-identity#adding-a-system-assigned-identity).

## <a name="view-the-service-principal"></a>Wyświetl nazwy głównej usługi

Ta procedura pokazuje sposób wyświetlania nazwy głównej usługi, maszyny wirtualnej przy użyciu tożsamości przypisanej w systemie włączony (obowiązują te same kroki dla aplikacji).

1. Kliknij przycisk **usługi Azure Active Directory** a następnie kliknij przycisk **aplikacje dla przedsiębiorstw**.
2. W obszarze **typ aplikacji**, wybierz **wszystkie aplikacje**.
3. W polu filtru wyszukiwania wpisz nazwę maszyny Wirtualnej lub aplikacji, która ma tożsamości zarządzanej włączono lub wybierz go z listy przedstawione.

   ![Wyświetl nazwy głównej usługi tożsamość zarządzaną w portalu](./media/how-to-view-managed-identity-service-principal-portal/view-managed-identity-service-principal-portal.png)

## <a name="next-steps"></a>Kolejne kroki

[Tożsamości zarządzane dla zasobów platformy Azure](/azure/active-directory/managed-identities-azure-resources/overview)

