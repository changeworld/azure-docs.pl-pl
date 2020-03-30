---
title: Synchronizowanie atrybutów z usługą Azure AD w celu mapowania | Dokumenty firmy Microsoft
description: Dowiedz się, jak synchronizować atrybuty z lokalnej usługi Active Directory z usługą Azure AD. Podczas konfigurowania inicjowania obsługi administracyjnej użytkowników w aplikacjach SaaS należy użyć funkcji rozszerzenia katalogu, aby dodać atrybuty źródłowe, które nie są domyślnie synchronizowane.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: mimart
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 09d1efaf54bee65bd3274987e68e643f887baade
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522275"
---
# <a name="sync-an-attribute-from-your-on-premises-active-directory-to-azure-ad-for-provisioning-to-an-application"></a>Synchronizowanie atrybutu z lokalnej usługi Active Directory z usługą Azure AD w celu inicjowania obsługi administracyjnej aplikacji

Podczas dostosowywania mapowania atrybutów do inicjowania obsługi administracyjnej przez użytkowników może się okazać, że atrybut, który chcesz zamapować, nie jest wyświetlany na liście **atrybutów Źródło.** W tym artykule pokazano, jak dodać brakujący atrybut, synchronizując go z lokalnej usługi Active Directory (AD) do usługi Azure Active Directory (Azure AD).

Usługa Azure AD musi zawierać wszystkie dane wymagane do utworzenia profilu użytkownika podczas inicjowania obsługi administracyjnej kont użytkowników z usługi Azure AD do aplikacji SaaS. W niektórych przypadkach, aby udostępnić dane, może być konieczne zsynchronizowanie atrybutów z lokalnej usługi AD na usługę Azure AD. Usługa Azure AD Connect automatycznie synchronizuje niektóre atrybuty z usługą Azure AD, ale nie wszystkie atrybuty. Ponadto niektóre atrybuty (takie jak SAMAccountName), które są domyślnie synchronizowane, mogą nie być udostępniane przy użyciu interfejsu API programu Microsoft Graph. W takich przypadkach można użyć funkcji rozszerzenia katalogu usługi Azure AD Connect, aby zsynchronizować atrybut z usługą Azure AD. W ten sposób atrybut będzie widoczny dla interfejsu API programu Microsoft Graph i usługi inicjowania obsługi administracyjnej usługi Azure AD.

Jeśli dane potrzebne do inicjowania obsługi administracyjnej znajdują się w usłudze Active Directory, ale nie są dostępne do inicjowania obsługi administracyjnej z przyczyn opisanych powyżej, wykonaj następujące kroki.
 
## <a name="sync-an-attribute"></a>Synchronizowanie atrybutu 

1. Otwórz kreatora usługi Azure AD Connect, wybierz pozycję Zadania, a następnie wybierz pozycję **Dostosuj opcje synchronizacji**.

   ![Kreator łączyć usługi Azure Active Directory](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. Zaloguj się jako globalny administrator usługi Azure AD. 

3. Na stronie **Funkcje opcjonalne** wybierz pozycję **Synchronizacja atrybutów rozszerzenia katalogu**.
 
   ![Kreator łączyć usługi Azure Active Directory — strona Funkcje opcjonalne](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. Wybierz atrybuty, które chcesz rozszerzyć na usługę Azure AD.
   > [!NOTE]
   > W obszarze **Dostępne atrybuty** rozróżniana jest wielkość liter.

   ![Strona wyboru rozszerzenia rozszerzeń usługi Azure Active Directory Connect](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. Zakończ kreatora usługi Azure AD Connect i zezwalaj na uruchomienie pełnego cyklu synchronizacji. Po zakończeniu cyklu schemat jest rozszerzany, a nowe wartości są synchronizowane między lokalną usługą AD i usługą Azure AD.
 
6. W witrynie Azure Portal podczas [edytowania mapowań atrybutów użytkownika](customize-application-attributes.md)lista **atrybutów Źródło** będzie teraz `<attributename> (extension_<appID>_<attributename>)`zawierać dodany atrybut w formacie. Wybierz atrybut i zamapuj go na aplikację docelową do inicjowania obsługi administracyjnej.

   ![Strona wyboru rozszerzenia rozszerzeń usługi Azure Active Directory Connect](./media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> Możliwość aprowizowania atrybutów odwołań z lokalnej usługi AD, takiej jak **managedby** lub **DN/DistinguishedName**, nie jest obecnie obsługiwana. Możesz poprosić o tę funkcję w [u użytkownika voice](https://feedback.azure.com/forums/169401-azure-active-directory). 

## <a name="next-steps"></a>Następne kroki

* [Określanie, kto ma zakres inicjowania obsługi administracyjnej](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
