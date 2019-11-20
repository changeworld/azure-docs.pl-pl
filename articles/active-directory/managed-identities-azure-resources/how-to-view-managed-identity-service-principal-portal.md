---
title: Wyświetlanie jednostki usługi zarządzanej tożsamości w Azure Portal — Azure AD
description: Instrukcje krok po kroku dotyczące wyświetlania nazwy głównej usługi tożsamości zarządzanej w Azure Portal.
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
ms.openlocfilehash: b51aeabaa4bb7fa9884c95cf0da2dc68ba5b64b0
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184098"
---
# <a name="view-the-service-principal-of-a-managed-identity-in-the-azure-portal"></a>Wyświetl nazwę główną usługi tożsamości zarządzanej w Azure Portal

Zarządzane tożsamości dla zasobów platformy Azure zapewniają usługi platformy Azure z automatycznie zarządzaną tożsamością w Azure Active Directory. Tej tożsamości można użyć do uwierzytelniania w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, bez poświadczeń w kodzie. 

W tym artykule dowiesz się, jak wyświetlić nazwę główną usługi tożsamości zarządzanej przy użyciu Azure Portal.

 > [!NOTE] 
 > Nazwy główne usług są aplikacjami przedsiębiorstwa. 

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli jesteś zaznajomiony z zarządzanych tożsamości dla zasobów platformy Azure, zapoznaj się z [sekcji Przegląd](overview.md).
- Jeśli nie masz jeszcze konta platformy Azure, [Utwórz bezpłatne konto](https://azure.microsoft.com/free/).
- Włącz [tożsamość przypisaną przez system na maszynie wirtualnej lub w](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) [aplikacji](/azure/app-service/overview-managed-identity#adding-a-system-assigned-identity).

## <a name="view-the-service-principal"></a>Wyświetlanie nazwy głównej usługi

Ta procedura przedstawia sposób wyświetlania nazwy głównej usługi maszyny wirtualnej z włączoną tożsamością przypisanej do systemu (te same kroki dotyczą aplikacji).

1. Kliknij **Azure Active Directory** a następnie kliknij pozycję **aplikacje dla przedsiębiorstw**.
2. W obszarze **Typ aplikacji**wybierz pozycję **wszystkie aplikacje**.
3. W polu filtr wyszukiwania wpisz nazwę maszyny wirtualnej lub aplikacji, która ma włączoną tożsamość zarządzaną, lub wybierz ją z wyświetlonej listy.

   ![Wyświetlanie jednostki usługi zarządzania tożsamościami w portalu](./media/how-to-view-managed-identity-service-principal-portal/view-managed-identity-service-principal-portal.png)

## <a name="next-steps"></a>Kolejne kroki

[Tożsamości zarządzane dla zasobów platformy Azure](/azure/active-directory/managed-identities-azure-resources/overview)

