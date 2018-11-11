---
title: 'Synchronizacja programu Azure AD Connect: rozszerzenia katalogów | Dokumentacja firmy Microsoft'
description: W tym temacie opisano funkcję rozszerzenia katalogu w programie Azure AD Connect.
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
ms.date: 10/05/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: b6c5f55a575605eef3a280de4f973d9b2d0a4ace
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2018
ms.locfileid: "51288309"
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Synchronizacja programu Azure AD Connect: rozszerzenia katalogów
Umożliwia rozszerzeń katalogów rozszerzania schematu w usłudze Azure Active Directory (Azure AD) przy użyciu atrybutów z usługi Active Directory w środowisku lokalnym. Ta funkcja umożliwia tworzenie aplikacji biznesowych za korzystanie z atrybutów, które nadal zarządzać w środowisku lokalnym. Te atrybuty mogą być używane za pośrednictwem [rozszerzenia katalogów interfejsu API usługi Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) lub [programu Microsoft Graph](https://developer.microsoft.com/graph/). Zobaczysz dostępne atrybuty za pomocą [programu Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net/) i [programu Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)odpowiednio.

Obecnie nie obciążenie usługi Office 365 korzysta z tych atrybutów.

Możesz skonfigurować dodatkowych atrybutów, które chcesz synchronizować w ścieżce ustawienia niestandardowe w Kreatorze instalacji.

>[!NOTE]
>Pole dostępnych atrybutów jest rozróżniana wielkość liter.

![Kreator rozszerzenia schematu](./media/how-to-connect-sync-feature-directory-extensions/extension2.png)  

Instalacja pokazuje następujące atrybuty, które są kandydatami prawidłowy:

* Typy obiektów użytkowników i grup
* Atrybuty jednowartościowe: String, Boolean, Integer, dane binarne
* Atrybuty wielowartościowe: String, dane binarne


>[!NOTE]
> Azure AD Connect obsługuje synchronizowanie wielowartościowe atrybuty usługi Active Directory do usługi Azure AD jako rozszerzenia katalogów wielowartościowych. Ale żadnych funkcji w usłudze Azure AD obecnie obsługuje korzystanie z rozszerzenia katalogów wielowartościowe.

Lista atrybutów są odczytywane z pamięci podręcznej schematu, który jest tworzony podczas instalacji programu Azure AD Connect. Jeśli rozszerzono schemat usługi Active Directory przy użyciu dodatkowych atrybutów, musisz najpierw [odświeżyć schemat](how-to-connect-installation-wizard.md#refresh-directory-schema) przed te atrybuty są widoczne.

Każdy obiekt w usłudze Azure AD może mieć maksymalnie 100 atrybutów rozszerzeń katalogów. Maksymalna długość to 250 znaków. Jeśli wartość atrybutu jest dłuższy, aparat synchronizacji obcina go.

Podczas instalacji programu Azure AD Connect aplikacja będzie zarejestrowana, której te atrybuty są dostępne. Możesz zobaczyć tę aplikację w witrynie Azure portal.

![Schemat rozszerzenia aplikacji](./media/how-to-connect-sync-feature-directory-extensions/extension3new.png)

Atrybuty są poprzedzone z rozszerzeniem \_{AppClientId}\_. AppClientId ma taką samą wartość dla wszystkich atrybutów w dzierżawie usługi Azure AD.

Te atrybuty są teraz dostępne za pośrednictwem interfejsu API programu Graph usługi Azure AD. Użytkownik może wyszukiwać w nich za pomocą [programu Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net/).

![Eksplorator usługi Azure AD Graph](./media/how-to-connect-sync-feature-directory-extensions/extension4.png)

Lub można zbadać atrybutów za pośrednictwem interfejsu API Microsoft Graph, za pomocą [programu Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer#).

>[!NOTE]
> Musisz poprosić o atrybutów, które mają zostać zwrócone. Jawnie wybrać atrybuty następująco: https://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com?$select = extension_9d98ed114c4840d298fad781915f27e4_employeeID, extension_9d98ed114c4840d298fad781915f27e4_division. 
>
> Aby uzyskać więcej informacji, zobacz [programu Microsoft Graph: Użyj parametrów zapytania](https://developer.microsoft.com/graph/docs/concepts/query_parameters#select-parameter).

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [synchronizacji programu Azure AD Connect](how-to-connect-sync-whatis.md) konfiguracji.

Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
