---
title: Synchronizowanie atrybutów do usługi Azure AD dla mapowania | Dokumentacja firmy Microsoft
description: Dowiedz się, jak synchronizować atrybutów z usługi Active Directory lokalnych z usługą Azure AD. Podczas konfigurowania aprowizacji użytkowników do aplikacji SaaS, użyj funkcji rozszerzenia katalogu, aby dodać atrybutów źródłowych, które nie są domyślnie synchronizowane.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: mimart
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9460bc924ea662646360d1a3f5087949a39a03f
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65806119"
---
# <a name="sync-an-attribute-from-your-on-premises-active-directory-to-azure-ad-for-provisioning-to-an-application"></a>Synchronizowanie atrybutów z lokalnej usługi Active Directory do usługi Azure AD na potrzeby inicjowania obsługi administracyjnej aplikacji

Podczas dostosowywania mapowań atrybutów dla aprowizacji użytkowników, może się okazać, że atrybut, którą chcesz zamapować nie jest wyświetlane w **atrybut źródłowy** listy. W tym artykule przedstawiono sposób dodawania Brak atrybutu przez synchronizację z sieci lokalnej usługi Active Directory (AD), usługą Azure Active Directory (Azure AD).

Usługa Azure AD musi zawierać wszystkie dane wymagane do utworzenia profilu użytkownika podczas aprowizowania kont użytkowników z usługi Azure AD do aplikacji SaaS. W niektórych przypadkach, aby udostępnić dane mogą muszą synchronizacji atrybutów z lokalnej usługi AD z usługą Azure AD. Azure AD Connect synchronizuje automatycznie niektórych atrybutów do usługi Azure AD, ale nie wszystkie atrybuty. Ponadto niektóre atrybuty (na przykład SAMAccountName), które są domyślnie synchronizowane nie mogą być dostępne za pośrednictwem interfejsu API programu Graph usługi Azure AD. W takich przypadkach można użyć funkcji rozszerzenia katalogu usługi Azure AD Connect do synchronizowania atrybutu do usługi Azure AD. W ten sposób atrybut będzie widoczny dla interfejsu API programu Graph usługi Azure AD i usługi aprowizacji usługi Azure AD.

Jeśli dane potrzebne do inicjowania obsługi administracyjnej znajduje się w usłudze Active Directory, ale nie jest dostępna dla inicjowania obsługi administracyjnej z powodów opisanych powyżej, wykonaj następujące kroki.
 
## <a name="sync-an-attribute"></a>Synchronizuj atrybutu 

1. Otwórz kreatora programu Azure AD Connect, wybierz pozycję zadania, a następnie wybierz **Dostosowywanie opcji synchronizacji**.

   ![Strona: Azure dodatkowe zadania kreatora Active Directory Connect](media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. Zaloguj się jako Administrator globalny usługi Azure AD. 

3. Na **funkcje opcjonalne** wybierz opcję **synchronizacja atrybutów rozszerzeń katalogów**.
 
   ![Strona: Azure opcjonalne funkcje kreatora Active Directory Connect](media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. Wybierz atrybuty, które ma zostać rozszerzony do usługi Azure AD.
   > [!NOTE]
   > Wyszukaj w **dostępne atrybuty** jest uwzględniana wielkość liter.

   ![Usługa Azure Active Directory Connect katalogu rozszerzeń zaznaczenia strona kreatora](media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. Zakończ pracę kreatora Azure AD Connect i umożliwiają cyklu pełnej synchronizacji uruchomić. Po zakończeniu cyklu rozszerzania schematu, a nowe wartości są synchronizowane między lokalnej usługi AD i Azure AD.
 
6. W witrynie Azure portal, gdy jesteś [edytowanie mapowania atrybutów użytkownika](customize-application-attributes.md), **atrybut źródłowy** listy będzie teraz zawierać dodano atrybut w formacie `<attributename> (extension_<appID>_<attributename>)`. Wybierz atrybut, a następnie dokonaj mapowania go do aplikacji docelowej dla udostępniania.

   ![Usługa Azure Active Directory Connect katalogu rozszerzeń zaznaczenia strona kreatora](media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> Możliwość inicjowania obsługi atrybutach odwołania z lokalnej usługi AD, takie jak **managedby** lub **DN/DistinguishedName**, nie jest obecnie obsługiwane. Możesz zażądać tej funkcji na [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory). 

## <a name="next-steps"></a>Kolejne kroki

* [Zdefiniuj, który znajduje się w zakresie do inicjowania obsługi](define-conditional-rules-for-provisioning-user-accounts.md)
