---
title: Jak zarządzać nieaktywnymi kontami użytkowników w usłudze Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się, jak wykrywać i obsługiwać konta użytkowników w usłudze Azure AD, które stały się przestarzałe
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/07/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56e44059268037cfd839fc7c877c5d6c972dead8
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886045"
---
# <a name="how-to-manage-inactive-user-accounts-in-azure-ad"></a>Jak: Zarządzanie nieaktywnymi kontami użytkowników w usłudze Azure AD

W dużych środowiskach konta użytkowników nie zawsze są usuwane, gdy pracownicy opuszczają organizację. Jako administrator IT chcesz wykryć i obsługiwać te przestarzałe konta użytkowników, ponieważ stanowią one zagrożenie bezpieczeństwa.

W tym artykule opisano metodę obsługi przestarzałych kont użytkowników w usłudze Azure AD. 

## <a name="what-are-inactive-user-accounts"></a>Co to są nieaktywne konta użytkowników?

Nieaktywne konta to konta użytkowników, które nie są już wymagane przez członków instytucji do uzyskania dostępu do zasobów. Jednym z kluczowych identyfikatorów nieaktywnych kont jest to, że nie były one używane *przez pewien czas* do logowania się w środowisku. Ponieważ nieaktywne konta są powiązane z aktywnością logowania, można użyć sygnatury czasowej ostatniego logowania, które zakończyło się pomyślnie, aby je wykryć. 

Wyzwaniem tej metody jest zdefiniowanie, co *oznacza przez pewien czas* w przypadku środowiska. Na przykład użytkownicy mogą nie logować się do środowiska *przez jakiś czas,* ponieważ są na wakacjach. Podczas definiowania, co delta dla nieaktywnych kont użytkowników jest, należy wziąć pod uwagę wszystkie uzasadnione powody, aby nie zalogować się do środowiska. W wielu organizacjach delta dla nieaktywnych kont użytkowników wynosi od 90 do 180 dni. 

Ostatnie pomyślne logowanie zapewnia potencjalny wgląd w ciągłe zapotrzebowanie użytkownika na dostęp do zasobów.  Może pomóc w określeniu, czy członkostwo w grupie lub dostęp do aplikacji jest nadal potrzebne lub mogą zostać usunięte. W przypadku zarządzania użytkownikami zewnętrznymi można zrozumieć, czy użytkownik zewnętrzny jest nadal aktywny w obrębie dzierżawy lub powinien zostać oczyszczony. 

    
## <a name="how-to-detect-inactive-user-accounts"></a>Jak wykryć nieaktywne konta użytkowników

Wykrywane nieaktywne konta, oceniając **właściwość lastSignInDateTime** udostępnianą przez typ zasobu **signInActivity** interfejsu API **programu Microsoft Graph.** Za pomocą tej właściwości można zaimplementować rozwiązanie dla następujących scenariuszy:

- **Użytkownicy według nazwy**: W tym scenariuszu wyszukujesz określonego użytkownika według nazwy, co umożliwia ocenę lastSignInDate:`https://graph.microsoft.com/beta/users?$filter=startswith(displayName,'markvi')&$select=displayName,signInActivity`

- **Użytkownicy według daty:** W tym scenariuszu należy poprosić o listę użytkowników z lastSignInDateTime przed określoną datą:`https://graph.microsoft.com/beta/users?filter=signInActivity/lastSignInDateTime le 2019-06-01T00:00:00Z`






## <a name="what-you-need-to-know"></a>Co musisz wiedzieć

W tej sekcji wymieniono, co należy wiedzieć o lastSignInDateTime właściwości.

### <a name="how-can-i-access-this-property"></a>Jak uzyskać dostęp do tej usługi?

Właściwość **lastSignInDateTime** jest udostępniana przez [typ zasobu signInActivity](https://docs.microsoft.com/graph/api/resources/signinactivity?view=graph-rest-beta) [interfejsu API REST programu Microsoft Graph.](https://docs.microsoft.com/graph/overview?view=graph-rest-beta#whats-in-microsoft-graph)   

### <a name="is-the-lastsignindatetime-property-available-through-the-get-azureaduser-cmdlet"></a>Czy właściwość lastSignInDateTime jest dostępna za pośrednictwem polecenia cmdlet Get-AzureAdUser?

Nie.

### <a name="what-edition-of-azure-ad-do-i-need-to-access-the-property"></a>Jakiej wersji usługi Azure AD potrzebuję, aby uzyskać dostęp do właściwości?

Dostęp do tej właściwości można uzyskać we wszystkich wersjach usługi Azure AD.

### <a name="what-permission-do-i-need-to-read-the-property"></a>Jakie uprawnienia potrzebuję do odczytania właściwości?

Aby przeczytać tę właściwość, należy udzielić następujących praw: 

- AuditLogs.Read.All
- Organisation.Read.All  


### <a name="when-does-azure-ad-update-the-property"></a>Kiedy usługa Azure AD aktualizuje właściwość?

Każde logowanie interaktywne, które zakończyło się pomyślnie, powoduje aktualizację bazowego magazynu danych. Zazwyczaj pomyślne logowania pojawiają się w raporcie logowania pokrewnych w ciągu 10 minut.
 

### <a name="what-does-a-blank-property-value-mean"></a>Co oznacza pusta wartość właściwości?

Aby wygenerować sygnaturę czasową lastSignInDateTime, potrzebujesz pomyślnego logowania. Ponieważ właściwość lastSignInDateTime jest nową funkcją, wartość właściwości lastSignInDateTime może być pusta, jeśli:

- Ostatnie pomyślne zalogowanie użytkownika miało miejsce przed wydaniem tej funkcji (1 grudnia 2019 r.).
- Konto użytkownika, którego dotyczy problem, nigdy nie było używane do pomyślnego logowania.

## <a name="next-steps"></a>Następne kroki

* [Pobieranie danych przy użyciu interfejsu API raportowania usługi Azure Active Directory z certyfikatami](tutorial-access-api-with-certificates.md)
* [Inspekcja odwołania interfejsu API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Odwołanie do interfejsu API raportu aktywności logowania](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
