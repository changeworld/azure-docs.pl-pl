---
title: Co to jest inicjowanie obsługi tożsamości w usłudze Azure AD? | Microsoft Docs
description: Opisuje Omówienie aprowizacji tożsamości.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 166fb9320672e63b8c53717133dc61aa93f57a62
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74868647"
---
# <a name="what-is-identity-provisioning"></a>Co to jest inicjowanie obsługi tożsamości?

Obecnie małe i duże firmy w coraz większym stopniu korzystają zarówno z aplikacji lokalnych, jak i aplikacji w chmurze.  Użytkownicy potrzebują dostępu do aplikacji zarówno lokalnie, jak i w chmurze. Konieczne jest posiadanie jednej tożsamości między tymi różnymi aplikacjami (lokalnie i w chmurze).

Inicjowanie obsługi jest procesem tworzenia obiektu na podstawie określonych warunków, zachowywania obiektu do daty i usuwania obiektu, gdy warunki nie są już spełnione. Na przykład gdy nowy użytkownik zostanie przyłączony do organizacji, ten użytkownik jest wprowadzany do systemu kadr.  W tym momencie Inicjowanie obsługi może utworzyć odpowiednie konto użytkownika w chmurze, w Active Directory i różne aplikacje, do których użytkownik będzie potrzebował dostępu.  Dzięki temu użytkownik może uruchamiać pracę i mieć dostęp do aplikacji i systemów, których potrzebują, w ciągu jednego dnia. 

![Inicjowanie obsługi chmury](media/what-is-provisioning/cloud1.png)

W odniesieniu do Azure Active Directory Inicjowanie obsługi można podzielić na następujące kluczowe scenariusze.  

- **[Obsługa administracyjna oparta na HR](#hr-driven-provisioning)**  
- **[Inicjowanie obsługi aplikacji](#app-provisioning)**  
- **[Inicjowanie obsługi administracyjnej katalogów](#directory-provisioning)** 

## <a name="hr-driven-provisioning"></a>Obsługa administracyjna oparta na HR

![Inicjowanie obsługi chmury](media/what-is-provisioning/cloud2.png)

Inicjowanie obsługi z poziomu usługi HR do chmury obejmuje tworzenie obiektów (użytkowników, ról, grup itp.) w oparciu o informacje znajdujące się w systemie kadr.  

Najbardziej typowym scenariuszem jest to, że w przypadku dołączenia nowego pracownika do firmy są one wprowadzane do systemu kadr.  Gdy to nastąpi, są one obsługiwane w chmurze.  W takim przypadku usługa Azure AD.  Inicjowanie obsługi administracyjnej z poziomu usługi kadr może obejmować następujące scenariusze. 

- **Zatrudnianie nowych pracowników** — po dodaniu nowego pracownika do usługi Cloud HR konto użytkownika jest automatycznie tworzone w Active Directory, Azure Active Directory i opcjonalnie pakiet Office 365 oraz inne aplikacje SaaS obsługiwane przez usługę Azure AD, z możliwością zapisu z tyłu adresu e-mail w usłudze Cloud HR.
- **Aktualizacje atrybutu pracownika i profilu** — gdy rekord pracownika zostanie zaktualizowany w usłudze Cloud HR (takie jak nazwa, tytuł lub Menedżer), konto użytkownika zostanie automatycznie zaktualizowane w Active Directory, Azure Active Directory i opcjonalnie pakiet Office 365 i inne aplikacje SaaS obsługiwane przez usługę Azure AD.
- **Zakończenie zatrudnienia pracownika** — po zakończeniu pracy pracownika w chmurze HR konto użytkownika jest automatycznie wyłączone w Active Directory, Azure Active Directory i opcjonalnie pakiet Office 365 i inne aplikacje SaaS obsługiwane przez usługę Azure AD.
- Przedziały **pracownika** — gdy pracownik jest ponownie zatrudniany w usłudze Cloud HR, jego stare konto można automatycznie ponownie aktywować lub zainicjowanie (zależnie od preferencji) do Active Directory, Azure Active Directory i opcjonalnie pakietu Office 365 i innych aplikacji SaaS obsługiwanych przez usługę Azure AD.


## <a name="app-provisioning"></a>Inicjowanie obsługi aplikacji

![Inicjowanie obsługi chmury](media/what-is-provisioning/cloud3.png)

Inicjowanie obsługi aplikacji obejmuje Inicjowanie obsługi użytkowników i ról w aplikacjach, do których użytkownik musi mieć dostęp.  

Najbardziej typowym scenariuszem jest to, że użytkownik w usłudze Azure AD jest obsługiwany w usłudze O365 lub Salesforce.

## <a name="directory-provisioning"></a>Inicjowanie obsługi administracyjnej katalogów

![Inicjowanie obsługi chmury](media/what-is-provisioning/cloud4.png)

Lokalna obsługa administracyjna obejmuje Inicjowanie obsługi administracyjnej ze źródeł lokalnych (takich jak Active Directory) w usłudze Azure AD.  

Najbardziej typowym scenariuszem jest to, kiedy użytkownik w Active Directory (AD) jest zainicjowany do usługi Azure AD.

Jest to realizowane przez Azure AD Connect synchronizację, Azure AD Connect aprowizacji i Microsoft Identity Manager w chmurze. 
 
## <a name="next-steps"></a>Następne kroki 

- [Co to jest Azure AD Connect aprowizacji w chmurze?](what-is-cloud-provisioning.md)
- [Zainstaluj aprowizacji w chmurze](how-to-install.md)