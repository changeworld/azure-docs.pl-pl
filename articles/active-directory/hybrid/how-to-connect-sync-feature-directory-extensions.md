---
title: 'Synchronizacja programu Azure AD Connect: Rozszerzenia katalogów | Dokumentacja firmy Microsoft'
description: W tym temacie opisano funkcję rozszerzenia katalogu w programie Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 995ee876-4415-4bb0-a258-cca3cbb02193
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/05/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff0fd4d01eab739b79685c1de67cb8fe28873961
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60347993"
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Synchronizacja programu Azure AD Connect: Rozszerzenia katalogów
Umożliwia rozszerzeń katalogów rozszerzania schematu w usłudze Azure Active Directory (Azure AD) przy użyciu atrybutów z usługi Active Directory w środowisku lokalnym. Ta funkcja umożliwia tworzenie aplikacji biznesowych za korzystanie z atrybutów, które nadal zarządzać w środowisku lokalnym. Te atrybuty mogą być używane za pośrednictwem [rozszerzenia katalogów interfejsu API usługi Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) lub [programu Microsoft Graph](https://developer.microsoft.com/graph/). Zobaczysz dostępne atrybuty za pomocą [programu Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net/) i [programu Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)odpowiednio.

Obecnie nie obciążenie usługi Office 365 korzysta z tych atrybutów.

Możesz skonfigurować dodatkowych atrybutów, które chcesz synchronizować w ścieżce ustawienia niestandardowe w Kreatorze instalacji.

>[!NOTE]
>Pole dostępnych atrybutów jest rozróżniana wielkość liter.

![Kreator rozszerzenia schematu](./media/how-to-connect-sync-feature-directory-extensions/extension2.png)  

Instalacja pokazuje następujące atrybuty, które są kandydatami prawidłowy:

* Typy obiektów użytkowników i grup
* Atrybuty jednowartościowe: String, Boolean, Integer, dane binarne
* Atrybuty wielowartościowe: Ciąg binarny


>[!NOTE]
> Mimo że program Azure AD Connect obsługuje synchronizacji wielowartościowe usługi Active Directory atrybutów do usługi Azure AD jako rozszerzenia katalogów wielokrotne, obecnie nie ma możliwości pobierania/korzystanie z danych przekazanych w atrybuty rozszerzenia katalogu wielowartościowych.

Lista atrybutów są odczytywane z pamięci podręcznej schematu, który jest tworzony podczas instalacji programu Azure AD Connect. Jeśli rozszerzono schemat usługi Active Directory przy użyciu dodatkowych atrybutów, musisz najpierw [odświeżyć schemat](how-to-connect-installation-wizard.md#refresh-directory-schema) przed te atrybuty są widoczne.

Każdy obiekt w usłudze Azure AD może mieć maksymalnie 100 atrybutów rozszerzeń katalogów. Maksymalna długość to 250 znaków. Jeśli wartość atrybutu jest dłuższy, aparat synchronizacji obcina go.

Podczas instalacji programu Azure AD Connect aplikacja będzie zarejestrowana, której te atrybuty są dostępne. Możesz zobaczyć tę aplikację w witrynie Azure portal.

![Schemat rozszerzenia aplikacji](./media/how-to-connect-sync-feature-directory-extensions/extension3new.png)

Atrybuty są poprzedzone z rozszerzeniem \_{AppClientId}\_. AppClientId ma taką samą wartość dla wszystkich atrybutów w dzierżawie usługi Azure AD.

Te atrybuty są teraz dostępne za pośrednictwem interfejsu API programu Graph usługi Azure AD. Użytkownik może wyszukiwać w nich za pomocą [programu Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net/).

![Eksplorator usługi Azure AD Graph](./media/how-to-connect-sync-feature-directory-extensions/extension4.png)

Lub można zbadać atrybutów za pośrednictwem interfejsu API Microsoft Graph, za pomocą [programu Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer#).

>[!NOTE]
> Musisz poprosić o atrybutów, które mają zostać zwrócone. Jawnie wybrać atrybuty następująco: https://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com? $select = extension_9d98ed114c4840d298fad781915f27e4_employeeID, extension_9d98ed114c4840d298fad781915f27e4_division. 
>
> Aby uzyskać więcej informacji, zobacz [programu Microsoft Graph: Korzystanie z parametrów zapytania](https://developer.microsoft.com/graph/docs/concepts/query_parameters#select-parameter).

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [synchronizacji programu Azure AD Connect](how-to-connect-sync-whatis.md) konfiguracji.

Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
