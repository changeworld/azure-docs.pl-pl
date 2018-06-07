---
title: 'Synchronizacja programu Azure AD Connect: rozszerzenia katalogów | Dokumentacja firmy Microsoft'
description: W tym temacie opisano funkcję rozszerzeń katalogu w programie Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 995ee876-4415-4bb0-a258-cca3cbb02193
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: dda35e63c209951547a667c46639dc0f37c87b43
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34593636"
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Synchronizacja programu Azure AD Connect: rozszerzenia katalogów
Umożliwia rozszerzenia katalogów rozszerzania schematu w usłudze Azure Active Directory (Azure AD) z własnych atrybutów z lokalnej usługi Active Directory. Ta funkcja umożliwia tworzenie aplikacji biznesowych przez korzystanie z atrybutów, które nadal zarządzać lokalnymi. Te atrybuty mogą być używane za pośrednictwem [rozszerzeń katalogu Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) lub [Microsoft Graph](https://graph.microsoft.io/). Dostępne atrybuty można wyświetlić za pomocą [Explorer Azure AD Graph](https://graphexplorer.azurewebsites.net/) i [Eksplorator systemu Microsoft Graph](https://developer.microsoft.com/en-us/graph/graph-explorer)odpowiednio.

Obecnie nie obciążenie usługi Office 365 wykorzystuje te atrybuty.

Możesz skonfigurować dodatkowych atrybutów, które chcesz synchronizować w ścieżce ustawienia niestandardowe w Kreatorze instalacji.

![Kreator rozszerzenia schematu](./media/active-directory-aadconnectsync-feature-directory-extensions/extension2.png)  

Instalacja zawiera następujące atrybuty, które są kandydatami prawidłowy:

* Typy obiektów użytkowników i grup
* Atrybuty jednowartościowe: String, Boolean, Integer, dane binarne
* Atrybuty wielowartościowe: ciąg, dane binarne


>[!NOTE]
> Azure AD Connect obsługuje synchronizacji wielowartościowe atrybuty usługi Active Directory do usługi Azure AD jako rozszerzenia katalogów wielowartościowych. Ale nie funkcji w usłudze Azure AD obecnie obsługuje korzystanie z rozszerzeń wielowartościowe katalogu.

Lista atrybutów jest do odczytu z pamięci podręcznej schematu, która jest tworzona podczas instalacji programu Azure AD Connect. Jeśli rozszerzono schemat usługi Active Directory przy użyciu dodatkowych atrybutów, należy najpierw [Odśwież schemat](active-directory-aadconnectsync-installation-wizard.md#refresh-directory-schema) przed te nowe atrybuty są widoczne.

Obiektu w usłudze Azure AD mogą mieć maksymalnie 100 atrybuty rozszerzenia katalogu. Maksymalna długość to 250 znaków. Jeśli wartość atrybutu jest dłuższy, aparat synchronizacji obcina go.

Podczas instalacji programu Azure AD Connect aplikacja jest zarejestrowany, których te atrybuty są dostępne. Można wyświetlić tę aplikację w portalu Azure.

![Aplikacja rozszerzenia schematu](./media/active-directory-aadconnectsync-feature-directory-extensions/extension3new.png)

Atrybuty są poprzedzane prefiksem rozszerzenia \_{AppClientId}\_. AppClientId ma taką samą wartość dla wszystkich atrybutów w dzierżawie usługi Azure AD.

Te atrybuty są teraz dostępne za pośrednictwem interfejsu API Azure AD Graph. Użytkownik może wyszukiwać w nich przy użyciu [Explorer Azure AD Graph](https://graphexplorer.azurewebsites.net/).

![Eksplorator usługi Azure AD Graph](./media/active-directory-aadconnectsync-feature-directory-extensions/extension4.png)

Lub atrybutów za pośrednictwem interfejsu API Microsoft Graph, można badać przy użyciu [Eksplorator systemu Microsoft Graph](https://developer.microsoft.com/en-us/graph/graph-explorer#).

>[!NOTE]
> Musisz poprosić o atrybutów, które mają zostać zwrócone. Jawnie wybrać atrybuty następująco: https://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com?$select = extension_9d98ed114c4840d298fad781915f27e4_employeeID, extension_9d98ed114c4840d298fad781915f27e4_division. 
>
> Aby uzyskać więcej informacji, zobacz [Microsoft Graph: Użyj parametrów zapytania](https://developer.microsoft.com/en-us/graph/docs/concepts/query_parameters#select-parameter).

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [synchronizacja programu Azure AD Connect](active-directory-aadconnectsync-whatis.md) konfiguracji.

Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md).
