---
title: Jak skonfigurować Inicjowanie obsługi użytkowników dla aplikacji w galerii Azure AD | Dokumentacja firmy Microsoft
description: Jak można szybko skonfigurować konto użytkownika sformatowanego alokowania i anulowania alokowania aplikacjom wymienione w galerii aplikacji usługi Azure AD
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 79f836b4fc5964ffeb5cd9c1e179c3140395c219
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "36334459"
---
# <a name="how-to-configure-user-provisioning-to-an-azure-ad-gallery-application"></a>Jak skonfigurować Inicjowanie obsługi użytkowników dla aplikacji w galerii Azure AD

*Inicjowanie obsługi konta użytkownika* polega na tworzenie, aktualizowanie i/lub wyłączenie rekordów konta użytkowników w magazynie profil lokalny użytkownika aplikacji. Większość chmury i aplikacji SaaS przechowywać role użytkowników i uprawnienia własny magazyn profilu użytkownika lokalnego, a jest obecność takich rekordu użytkownika w lokalnym magazynie *wymagane* potrzeby logowania jednokrotnego i uzyskiwania dostępu do pracy.

W portalu Azure **inicjowania obsługi administracyjnej** karcie w lewym okienku nawigacji dla aplikacji przedsiębiorstwa Wyświetla, jakie tryby inicjowania obsługi administracyjnej są obsługiwane dla danej aplikacji. Może to być jedną z dwóch wartości:

## <a name="configuring-an-application-for-manual-provisioning"></a>Konfigurowanie aplikacji do ręcznego inicjowania obsługi

*Ręczne* inicjowania obsługi administracyjnej oznacza, że należy utworzyć konta użytkownika ręcznie przy użyciu metody dostarczone przez danej aplikacji. Może to oznaczać logowania do portalu administracyjnego dla danej aplikacji i dodawanie użytkowników przy użyciu interfejsu użytkownika opartego na sieci web. Lub może być przekazywania arkusz kalkulacyjny zawierający szczegółów konta użytkownika, przy użyciu mechanizmu udostępniane przez tę aplikację. Zapoznaj się z dokumentacją dostarczoną przez aplikację, lub skontaktuj się z deweloperem aplikacji, aby określić, czy mechanizmy wat są dostępne.

Podręcznik jest tylko tryb wyświetlany dla danej aplikacji, oznacza, że nie usługi Azure AD automatycznego inicjowania obsługi administracyjnej łącznik został utworzony dla aplikacji jeszcze. Lub oznacza to, że aplikacja nie obsługuje interfejsu API zarządzania wstępnych użytkownika, na których można utworzyć łącznik automatycznego inicjowania obsługi administracyjnej.

Jeśli chcesz zażądać pomocy technicznej dla automatyczne Inicjowanie obsługi administracyjnej dla danej aplikacji, możesz też wypełnić żądania przy użyciu [Azure Active Directory żądań aplikacji](https://aka.ms/aadapprequest).

## <a name="configuring-an-application-for-automatic-provisioning"></a>Konfigurowanie aplikacji do automatycznego inicjowania obsługi

*Automatyczne* oznacza, że usługi Azure AD, inicjowania obsługi administracyjnej łącznik został opracowany dla tej aplikacji. Aby uzyskać więcej informacji o usłudze Azure AD, inicjowania obsługi administracyjnej usługi oraz sposób jej działania, zobacz [zautomatyzować Inicjowanie obsługi użytkowników i anulowania zastrzeżenia do aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning).

Aby uzyskać więcej informacji na temat sposobu obsługi administracyjnej konkretnych użytkowników i grup do aplikacji, zobacz [Zarządzanie aprowizacja konta użytkowników dla aplikacji przedsiębiorstwa](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning).

Rzeczywiste kroki wymagane do włączania i konfigurowania automatyczne udostępnianie się różnić w zależności od aplikacji.

>[!NOTE]
>Należy zacząć od znajdowanie samouczek ustawień dotyczących przygotowywania Inicjowanie obsługi administracyjnej dla aplikacji, a także następujące te kroki konfigurowania aplikacji i usługi Azure AD, aby utworzyć połączenie inicjowania obsługi administracyjnej. 
>
>

Samouczki aplikacji można znaleźć w folderze [listy samouczki dotyczące integracji aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

Ważne jest, aby uwzględnić podczas konfigurowania udostępniania można przejrzeć i skonfigurować mapowanie atrybutów i przepływów pracy, określających, które użytkownik (lub grupa) właściwości przepływu z usługi Azure AD do aplikacji. Dotyczy to również ustawienie "zgodnej właściwości" używany do jednoznacznego identyfikowania i odpowiada użytkowników/grupy między tymi dwoma systemami. Aby uzyskać więcej informacji na ten proces ważne.

## <a name="next-steps"></a>Kolejne kroki
[Dostosowywanie użytkownika udostępniania mapowań atrybutów dla aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings)

