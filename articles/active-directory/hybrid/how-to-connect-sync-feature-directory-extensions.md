---
title: 'Synchronizacja usługi Azure AD Connect: rozszerzenia katalogów | Dokumenty firmy Microsoft'
description: W tym temacie opisano funkcję rozszerzeń katalogów w usłudze Azure AD Connect.
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
ms.date: 11/12/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 80438319a6337dd6f28f9bdca8a428829b6cb0b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77917917"
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Synchronizacja usługi Azure AD Connect: rozszerzenia katalogów
Rozszerzenia katalogów można użyć, aby rozszerzyć schemat w usłudze Azure Active Directory (Azure AD) o własne atrybuty z lokalnej usługi Active Directory. Ta funkcja umożliwia tworzenie aplikacji LOB przez użycie atrybutów, które nadal zarządzać lokalnie. Te atrybuty mogą być używane przez [rozszerzenia](https://docs.microsoft.com/graph/extensibility-overview
). Dostępne atrybuty można zobaczyć za pomocą [Programu Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer). Za pomocą tej funkcji można również tworzyć grupy dynamiczne w usłudze Azure AD.

Obecnie żadne obciążenie usługi Office 365 nie zużywa tych atrybutów.

## <a name="customize-which-attributes-to-synchronize-with-azure-ad"></a>Dostosowywanie atrybutów do synchronizacji z usługą Azure AD

W kreatorze instalacji można skonfigurować dodatkowe atrybuty, które mają zostać zsynchronizowane.

>[!NOTE]
>W polu Dostępne atrybuty rozróżniana jest wielkość liter.

![Kreator rozszerzenia schematu](./media/how-to-connect-sync-feature-directory-extensions/extension2.png)  

Instalacja pokazuje następujące atrybuty, które są ważne kandydatów:

* Typy obiektów użytkowników i grup
* Atrybuty o pojedynczej wartości: ciąg, wartość logiczna, liczba całkowita, binarny
* Atrybuty wielowartościowe: ciąg, binarny


>[!NOTE]
> Mimo że usługa Azure AD Connect obsługuje synchronizowanie atrybutów usługi Active Directory z usługą Azure AD z wieloma wartościami jako wielowartościowych rozszerzeń katalogów, obecnie nie ma możliwości pobierania/korzystania z danych przekazanych w atrybutach rozszerzenia katalogów o wielu wartościach.

Lista atrybutów jest odczytywana z pamięci podręcznej schematu, która jest tworzona podczas instalacji usługi Azure AD Connect. Jeśli schemat usługi Active Directory został rozszerzony o dodatkowe atrybuty, należy [odświeżyć schemat,](how-to-connect-installation-wizard.md#refresh-directory-schema) zanim te nowe atrybuty będą widoczne.

Obiekt w usłudze Azure AD może mieć maksymalnie 100 atrybutów dla rozszerzeń katalogów. Maksymalna długość wynosi 250 znaków. Jeśli wartość atrybutu jest dłuższa, aparat synchronizacji obcina ją.

## <a name="configuration-changes-in-azure-ad-made-by-the-wizard"></a>Zmiany konfiguracji usługi Azure AD wprowadzone przez kreatora

Podczas instalacji usługi Azure AD Connect aplikacja jest rejestrowana, gdzie te atrybuty są dostępne. Możesz zobaczyć tę aplikację w witrynie Azure portal. Jego nazwa jest zawsze **Aplikacja rozszerzenia schematu dzierżawy**.

![Aplikacja rozszerzenia schematu](./media/how-to-connect-sync-feature-directory-extensions/extension3new.png)

Upewnij się, że wybrałeś **wszystkie aplikacje,** aby wyświetlić tę aplikację.

Atrybuty są poprzedzone **rozszerzeniem \_\_{ApplicationId}**. Identyfikator aplikacji ma taką samą wartość dla wszystkich atrybutów w dzierżawie usługi Azure AD. Ta wartość będzie potrzebna dla wszystkich innych scenariuszy w tym temacie.

## <a name="viewing-attributes-using-the-microsoft-graph-api"></a>Wyświetlanie atrybutów przy użyciu interfejsu API programu Microsoft Graph

Te atrybuty są teraz dostępne za pośrednictwem interfejsu API programu Microsoft Graph przy użyciu [programu Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer#).

>[!NOTE]
> W interfejsie API programu Microsoft Graph należy poprosić o atrybuty, które mają zostać zwrócone. Jawnie wybierz atrybuty `https://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com?$select=extension_9d98ed114c4840d298fad781915f27e4_employeeID,extension_9d98ed114c4840d298fad781915f27e4_division`w ten sposób: .
>
> Aby uzyskać więcej informacji, zobacz [Microsoft Graph: Use query parameters](https://developer.microsoft.com/graph/docs/concepts/query_parameters#select-parameter).

## <a name="use-the-attributes-in-dynamic-groups"></a>Używanie atrybutów w grupach dynamicznych

Jednym z bardziej przydatnych scenariuszy jest użycie tych atrybutów w dynamicznych grupach zabezpieczeń lub office 365.

1. Utwórz nową grupę w usłudze Azure AD. Nadaj mu dobre imię i upewnij się, że **typem członkostwa** jest **użytkownik dynamiczny**.

   ![Zrzut ekranu z nową grupą](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup1.png)

2. Wybierz, aby **dodać kwerendę dynamiczną**. Jeśli spojrzysz na właściwości, nie zobaczysz tych atrybutów rozszerzonych. Najpierw musisz je dodać. Kliknij **pozycję Pobierz niestandardowe właściwości rozszerzenia**, wprowadź identyfikator aplikacji i kliknij pozycję Odśwież **właściwości**.

   ![Zrzut ekranu, na którym dodano rozszerzenia katalogów](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup2.png) 

3. Otwórz wykaz właściwości i zwróć uwagę, że dodane atrybuty są teraz widoczne.

   ![Zrzut ekranu z nowymi atrybutami wyświetlanymi w interfejsie użytkownika](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup3.png)

   Uzupełnij wyrażenie zgodnie z twoimi wymaganiami. W naszym przykładzie reguła jest ustawiona na **(user.extension_9d98ed114c4840d298fad781915f27e4_division -eq "Sprzedaż i marketing")**.

4. Po utworzeniu grupy, daj usłudze Azure AD trochę czasu, aby wypełnić członków, a następnie przejrzeć członków.

   ![Zrzut ekranu z członkami grupy dynamicznej](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup4.png)  

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o konfiguracji [synchronizacji usługi Azure AD Connect.](how-to-connect-sync-whatis.md)

Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
