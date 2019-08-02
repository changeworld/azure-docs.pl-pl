---
title: 'Azure AD Connect synchronizacji: Rozszerzenia katalogów | Microsoft Docs'
description: W tym temacie opisano funkcję rozszerzeń katalogów w Azure AD Connect.
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
ms.openlocfilehash: 88fdfce58bdd8e13637e77d01d4b6c0ab21f696a
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68607656"
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Azure AD Connect synchronizacji: Rozszerzenia katalogów
Możesz użyć rozszerzeń katalogów, aby rozszerzać schemat w Azure Active Directory (Azure AD) z własnymi atrybutami z Active Directory lokalnych. Ta funkcja umożliwia tworzenie aplikacji biznesowych przez konsumowanie atrybutów, które nadal są zarządzane lokalnie. Te atrybuty mogą być używane za pomocą [rozszerzeń katalogów usługi Azure AD interfejs API programu Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) lub [Microsoft Graph](https://developer.microsoft.com/graph/). Dostępne atrybuty można wyświetlić za pomocą [Eksploratora grafów usługi Azure AD](https://graphexplorer.azurewebsites.net/) i [Eksploratora Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer).

W tej chwili żadne obciążenie pakietu Office 365 nie korzysta z tych atrybutów.

Można skonfigurować dodatkowe atrybuty, które mają być synchronizowane w ścieżce ustawień niestandardowych w Kreatorze instalacji.

>[!NOTE]
>W polu dostępne atrybuty jest rozróżniana wielkość liter.

![Kreator rozszerzenia schematu](./media/how-to-connect-sync-feature-directory-extensions/extension2.png)  

Instalacja pokazuje następujące atrybuty, które są prawidłowymi kandydatami:

* Typy obiektów użytkowników i grup
* Atrybuty jednowartościowe: Ciąg, wartość logiczna, liczba całkowita, dane binarne
* Atrybuty wielowartościowe: Ciąg, dane binarne


>[!NOTE]
> Chociaż Azure AD Connect obsługuje synchronizowanie atrybutów Active Directory z obsługą wartości wielowartościowych w usłudze Azure AD jako rozszerzenia katalogów z obsługą wartości wielowartościowych, nie ma obecnie możliwości pobierania/wykorzystywania danych przekazywanych w atrybutach rozszerzenia katalogu o wiele wartości.

Lista atrybutów jest odczytana z pamięci podręcznej schematu, która została utworzona podczas instalacji Azure AD Connect. Jeśli schemat Active Directory został rozszerzony o dodatkowe atrybuty, należy [odświeżyć schemat](how-to-connect-installation-wizard.md#refresh-directory-schema) , zanim te nowe atrybuty są widoczne.

Obiekt w usłudze Azure AD może mieć do 100 atrybutów dla rozszerzeń katalogów. Maksymalna długość to 250 znaków. Jeśli wartość atrybutu jest dłuższa, aparat synchronizacji obcina go.

Podczas instalacji Azure AD Connect aplikacja jest zarejestrowana, w której te atrybuty są dostępne. Ta aplikacja jest widoczna w Azure Portal.

![Aplikacja rozszerzenia schematu](./media/how-to-connect-sync-feature-directory-extensions/extension3new.png)

Atrybuty są poprzedzone prefiksem \_{AppClientId}.\_ AppClientId ma tę samą wartość dla wszystkich atrybutów w dzierżawie usługi Azure AD.

Te atrybuty są teraz dostępne za pomocą usługi Azure AD interfejs API programu Graph. Możesz wysyłać zapytania do nich za pomocą [Eksploratora Azure AD Graph](https://graphexplorer.azurewebsites.net/).

![Eksplorator grafów usługi Azure AD](./media/how-to-connect-sync-feature-directory-extensions/extension4.png)

Lub można wykonywać zapytania dotyczące atrybutów za pośrednictwem interfejsu API Microsoft Graph przy użyciu [eksploratora Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer#).

>[!NOTE]
> Musisz poprosił o zwrócenie atrybutów. Jawnie wybierz następujące atrybuty: https\://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com? $SELECT = extension_9d98ed114c4840d298fad781915f27e4_employeeID, extension_9d98ed114c4840d298fad781915f27e4_division. 
>
> Aby uzyskać więcej informacji, [zobacz Microsoft Graph: Użyj parametrów](https://developer.microsoft.com/graph/docs/concepts/query_parameters#select-parameter)zapytania.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o konfiguracji [synchronizacji Azure AD Connect](how-to-connect-sync-whatis.md) .

Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
