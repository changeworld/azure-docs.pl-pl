---
title: Microsoft Graph interfejsów API dla usługi PIM (wersja zapoznawcza) — Azure Active Directory | Microsoft Docs
description: Zawiera informacje o korzystaniu z Microsoft Graph interfejsów API dla Azure AD Privileged Identity Management (PIM) (wersja zapoznawcza).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: overview
ms.date: 11/13/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45c7f42d536880f2578c62c6c4866b21be1cc9dc
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804563"
---
# <a name="microsoft-graph-apis-for-pim-preview"></a>Interfejsy API programu Microsoft Graph dla usługi PIM (wersja zapoznawcza)

W przypadku większości zadań, które można wykonać w Azure Active Directory (Azure AD) Privileged Identity Management (PIM) przy użyciu Azure Portal, można również wykonać za pomocą [Microsoft Graph interfejsów API](https://developer.microsoft.com/graph/docs/concepts/overview). W tym artykule opisano niektóre ważne pojęcia dotyczące korzystania z interfejsów API programu Microsoft Graph dla usługi PIM. Szczegółowe informacje na temat interfejsów API programu Microsoft Graph można znaleźć w [dokumentacji interfejsu API usługi Azure AD Privileged Identity Management](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root).

> [!IMPORTANT]
> Interfejsy API w wersji beta w programie Microsoft Graph są w wersji zapoznawczej i mogą ulec zmianie. Te interfejsy API nie są obsługiwane w zastosowaniach produkcyjnych.

## <a name="required-permissions"></a>Wymagane uprawnienia

Aby wywoływać interfejsy API programu Microsoft Graph dla usługi PIM, musisz posiadać **co najmniej jedno** z następujących uprawnień:

- `Directory.AccessAsUser.All`
- `Directory.Read.All`
- `Directory.ReadWrite.All`
- `PrivilegedAccess.ReadWrite.AzureAD`

### <a name="set-permissions"></a>Ustawianie uprawnień

Aplikacje do wywoływania interfejsów API programu Microsoft Graph dla usługi PIM muszą mieć wymagane uprawnienia. Najprostszym sposobem określenia wymaganych uprawnień jest użycie [platformy wyrażania zgody w usłudze Azure AD](../develop/consent-framework.md).

### <a name="set-permissions-in-graph-explorer"></a>Ustawianie uprawnień w Eksploratorze programu Graph

Jeśli do testowania wywołań używasz Eksploratora programu Graph, możesz określić uprawnienia w tym narzędziu.

1. Zaloguj się do [Eksploratora programu Graph](https://developer.microsoft.com/graph/graph-explorer) jako administrator globalny.

1. Kliknij pozycję **Modyfikuj uprawnienia**.

    ![Eksplorator programu Graph — modyfikowanie uprawnień](./media/pim-apis/graph-explorer.png)

1. Dodaj znaczniki wyboru obok uprawnień, które mają zostać uwzględnione. Uprawnienia `PrivilegedAccess.ReadWrite.AzureAD` nie są jeszcze dostępne w Eksploratorze programu Graph.

    ![Eksplorator programu Graph — modyfikowanie uprawnień](./media/pim-apis/graph-explorer-modify-permissions.png)

1. Kliknij pozycję **Modyfikuj uprawnienia**, aby zastosować zmiany uprawnień.

## <a name="next-steps"></a>Następne kroki

- [Dokumentacja interfejsu API usługi Azure AD Privileged Identity Management](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root)
