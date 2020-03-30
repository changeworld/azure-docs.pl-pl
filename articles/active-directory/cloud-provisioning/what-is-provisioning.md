---
title: Co to jest inicjowanie obsługi administracyjnej tożsamości za pomocą usługi Azure AD? | Microsoft Docs
description: W tym artykule opisano omówienie inicjowania obsługi administracyjnej tożsamości.
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
ms.openlocfilehash: 28513c57101af67695d10056b3dc8e6537dcddb2
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76712540"
---
# <a name="what-is-identity-provisioning"></a>Co to jest aprowizacja tożsamości?

Obecnie małe i duże firmy w coraz większym stopniu korzystają zarówno z aplikacji lokalnych, jak i aplikacji w chmurze.  Użytkownicy wymagają dostępu do aplikacji zarówno lokalnie, jak i w chmurze. Istnieje potrzeba posiadania jednej tożsamości w tych różnych aplikacjach (lokalnie, a także w chmurze).

Inicjowanie obsługi administracyjnej to proces tworzenia obiektu na podstawie określonych warunków, aktualizowanie obiektu i usuwanie obiektu, gdy warunki nie są już spełnione. Na przykład, gdy nowy użytkownik dołącza do organizacji, ten użytkownik jest wprowadzany do systemu kadr.  W tym momencie inicjowania obsługi administracyjnej można utworzyć odpowiednie konto użytkownika w chmurze, w usłudze Active Directory i różnych aplikacji, które użytkownik potrzebuje dostępu do.  Dzięki temu użytkownik może rozpocząć pracę i mieć dostęp do aplikacji i systemów, których potrzebują pierwszego dnia. 

![aprowizacji w chmurze](media/what-is-provisioning/cloud1.png)

W odniesieniu do usługi Azure Active Directory inicjowania obsługi administracyjnej można podzielić na następujące kluczowe scenariusze.  

- **[Aprowizacji opartej na zasobach ludzkich](#hr-driven-provisioning)**  
- **[Inicjowanie obsługi administracyjnej aplikacji](#app-provisioning)**  
- **[Inicjowanie obsługi administracyjnej katalogu](#directory-provisioning)** 

## <a name="hr-driven-provisioning"></a>Aprowizacji opartej na zasobach ludzkich

![aprowizacji w chmurze](media/what-is-provisioning/cloud2.png)

Inicjowanie obsługi administracyjnej z działu kadr do chmury obejmuje tworzenie obiektów (użytkowników, ról, grup itp.) na podstawie informacji znajdujących się w systemie HR.  

Najczęstszym scenariuszem jest, gdy nowy pracownik dołącza do firmy, są one wprowadzane do systemu HR.  Gdy to nastąpi, są one aprowizowane do chmury.  W takim przypadku usługi Azure AD.  Inicjowanie obsługi administracyjnej z działu kadr może obejmować następujące scenariusze. 

- **Zatrudnianie nowych pracowników** — gdy nowy pracownik jest dodawany do działu kadr w chmurze, konto użytkownika jest automatycznie tworzone w usłudze Active Directory, usłudze Azure Active Directory i opcjonalnie w usłudze Office 365 i innych aplikacjach SaaS obsługiwanych przez usługę Azure AD, z odpisem adresu e-mail do działu hr w chmurze.
- **Aktualizacje atrybutów i profilu pracownika** — gdy rekord pracownika jest aktualizowany w chmurze HR (na przykład ich nazwa, tytuł lub menedżer), ich konto użytkownika zostanie automatycznie zaktualizowane w usłudze Active Directory, usłudze Azure Active Directory i opcjonalnie w usłudze Office 365 i innych aplikacjach SaaS obsługiwanych przez usługę Azure AD.
- **Wypakanie pracowników** — po zakończeniu pracy pracownika w chmurze HR jego konto użytkownika jest automatycznie wyłączane w usłudze Active Directory, usłudze Azure Active Directory i opcjonalnie w usłudze Office 365 i innych aplikacjach SaaS obsługiwanych przez usługę Azure AD.
- **Pracownik ponownie zatrudnia** — gdy pracownik jest ponownie zatrudniony w chmurze HR, jego stare konto może być automatycznie ponownie aktywowane lub ponownie aprowizowana (w zależności od preferencji) do usługi Active Directory, usługi Azure Active Directory i opcjonalnie usługi Office 365 i innych aplikacji SaaS obsługiwanych przez usługę Azure AD.


## <a name="app-provisioning"></a>Inicjowanie obsługi administracyjnej aplikacji

![aprowizacji w chmurze](media/what-is-provisioning/cloud3.png)

W usłudze Azure Active Directory (Azure AD) termin **[inicjowania obsługi administracyjnej aplikacji](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)** odnosi się do automatycznego tworzenia tożsamości użytkowników i ról w aplikacjach w chmurze, do których użytkownicy potrzebują dostępu. Oprócz tworzenia tożsamości użytkowników automatyczne inicjowanie obsługi administracyjnej obejmuje obsługę i usuwanie tożsamości użytkowników w miarę zmiany stanu lub ról. Typowe scenariusze obejmują inicjowanie obsługi administracyjnej użytkownika usługi Azure AD do aplikacji, takich jak [Dropbox,](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial) [Salesforce,](https://docs.microsoft.com/azure/active-directory/saas-apps/salesforce-provisioning-tutorial) [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial)i innych.

## <a name="directory-provisioning"></a>Inicjowanie obsługi administracyjnej katalogu

![aprowizacji w chmurze](media/what-is-provisioning/cloud4.png)

Inicjowanie administracyjne lokalne obejmuje inicjowanie obsługi administracyjnej ze źródeł lokalnych (takich jak usługa Active Directory) do usługi Azure AD.  

Najbardziej typowym scenariuszem będzie, gdy użytkownik w usłudze Active Directory (AD) jest aprowizowana do usługi Azure AD.

Zostało to osiągnięte przez synchronizację usługi Azure AD Connect, inicjowanie obsługi administracyjnej w chmurze usługi Azure AD Connect i menedżera tożsamości firmy Microsoft Identity Manager. 
 
## <a name="next-steps"></a>Następne kroki 

- [Co to jest aprowizacja w chmurze programu Azure AD Connect?](what-is-cloud-provisioning.md)
- [Instalowanie inicjowania obsługi administracyjnej w chmurze](how-to-install.md)
