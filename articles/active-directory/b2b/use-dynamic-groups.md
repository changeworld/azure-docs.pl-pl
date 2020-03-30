---
title: Grupy dynamiczne i współpraca B2B — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Pokazuje, jak używać grup dynamicznych usługi Azure AD przy współpracy usługi Azure Active Directory B2B
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 41e8b81bc3594c6a378757636f70058510a38cc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78226921"
---
# <a name="dynamic-groups-and-azure-active-directory-b2b-collaboration"></a>Grupy dynamiczne i współpraca b2b usługi Azure Active Directory

## <a name="what-are-dynamic-groups"></a>Co to są grupy dynamiczne?
Dynamiczna konfiguracja członkostwa w grupie zabezpieczeń dla usługi Azure Active Directory (Azure AD) jest dostępna w [witrynie Azure Portal](https://portal.azure.com). Administratorzy mogą ustawić reguły wypełniania grup utworzonych w usłudze Azure AD na podstawie atrybutów użytkownika (takich jak userType, dział lub kraj/region). Członkowie mogą być automatycznie dodawane do grupy zabezpieczeń lub usuwane z niej na podstawie ich atrybutów. Grupy te mogą zapewniać dostęp do aplikacji lub zasobów w chmurze (witryny, dokumenty programu SharePoint) i przypisywać licencje członkom. Dowiedz się więcej o grupach dynamicznych w [grupach dedykowanych w usłudze Azure Active Directory](../active-directory-accessmanagement-dedicated-groups.md).

Odpowiednie [licencjonowanie usługi Azure AD Premium P1 lub P2](https://azure.microsoft.com/pricing/details/active-directory/) jest wymagane do tworzenia i używania grup dynamicznych. Więcej informacji w [artykule Tworzenie reguł opartych na atrybutach dla dynamicznego członkostwa w grupie w usłudze Azure Active Directory](../users-groups-roles/groups-dynamic-membership.md).

## <a name="creating-an-all-users-dynamic-group"></a>Tworzenie grupy dynamicznej "wszyscy użytkownicy"
Można utworzyć grupę zawierającą wszystkich użytkowników w dzierżawie przy użyciu reguły członkostwa. Gdy użytkownicy są dodawane lub usuwane z dzierżawy w przyszłości, członkostwo grupy jest dostosowywane automatycznie.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy za pomocą konta, które jest przypisane do administratora globalnego lub administratora użytkownika roli w dzierżawie.
1. Wybierz pozycję **Azure Active Directory**.
2. W obszarze **Zarządzaj**wybierz pozycję **Grupy**, a następnie wybierz pozycję **Nowa grupa**.
1. Na stronie **Nowa grupa** w obszarze **Typ grupy**wybierz pozycję **Zabezpieczenia**. Wypełnij pola **Nazwa grupy** i **Opis grupy** dotyczące nowej grupy. 
2. W obszarze **Typ członkostwa**wybierz pozycję **Użytkownik dynamiczny**, a następnie wybierz pozycję **Dodaj kwerendę dynamiczną**. 
4. Nad polem **tekstowym składni reguły** wybierz pozycję **Edytuj**. Na stronie **składni reguły edycji** wpisz następujące wyrażenie w polu tekstowym:

   ```
   user.objectId -ne null
   ```
1. Kliknij przycisk **OK**. Reguła pojawi się w polu Składnia reguły:

   ![Składnia reguły dla grupy dynamicznej wszystkich użytkowników](media/use-dynamic-groups/all-user-rule-syntax.png)

1.  Wybierz **pozycję Zapisz**. Nowa grupa dynamiczna będzie teraz zawierać użytkowników-gości B2B, a także użytkowników członkowskich.


1. Wybierz **pozycję Utwórz** na stronie **Nowa grupa,** aby utworzyć grupę.

## <a name="creating-a-group-of-members-only"></a>Tworzenie tylko grupy członków

Jeśli chcesz, aby grupa wykluczała użytkowników-gości i obejmowała tylko członków dzierżawy, utwórz grupę dynamiczną, jak opisano powyżej, ale w polu **składni reguły** wprowadź następujące wyrażenie:

```
(user.objectId -ne null) and (user.userType -eq "Member")
```

Na poniższej ilustracji przedstawiono składnię reguły dla grupy dynamicznej zmodyfikowanej w celu uwzględnienia tylko członków i wykluczenia gości.

![Pokazuje regułę, w której typ użytkownika jest równy element członkowski](media/use-dynamic-groups/all-member-user-rule-syntax.png)

## <a name="creating-a-group-of-guests-only"></a>Tworzenie tylko grupy gości

Przydatne może być również utworzenie nowej grupy dynamicznej, która zawiera tylko użytkowników-gości, dzięki czemu można zastosować zasady (takie jak zasady dostępu warunkowego usługi Azure AD). Utwórz grupę dynamiczną w sposób opisany powyżej, ale w polu **składni reguły** wprowadź następujące wyrażenie:

```
(user.objectId -ne null) and (user.userType -eq "Guest")
```

Na poniższej ilustracji przedstawiono składnię reguły dla grupy dynamicznej zmodyfikowanej w celu uwzględnienia tylko gości i wykluczenia użytkowników członkowskich.

![Pokazuje regułę, w której typ użytkownika jest równy gościowi](media/use-dynamic-groups/all-guest-user-rule-syntax.png)

## <a name="next-steps"></a>Następne kroki

- [B2B collaboration user properties (Właściwości użytkowników współpracy B2B)](user-properties.md)
- [Dodawanie użytkownika współpracy B2B do roli](add-guest-to-role.md)
- [Dostęp warunkowy dla użytkowników współpracy B2B](conditional-access.md)

