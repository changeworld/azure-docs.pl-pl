---
title: Jak skonfigurować aprowizację użytkowników do aplikacji galerii usługi Azure AD | Dokumentacja firmy Microsoft
description: Jak można szybko skonfigurować konto użytkownika sformatowanego aprowizacji i cofania aprowizacji aplikacjom, które są już wyświetlane w galerii aplikacji usługi Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: be43f0e100bc96de2be916cbf52bca7d3ba51431
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65784527"
---
# <a name="how-to-configure-user-provisioning-to-an-azure-ad-gallery-application"></a>Jak skonfigurować aprowizację użytkowników do aplikacji galerii usługi Azure AD

*Inicjowanie obsługi administracyjnej konta użytkownika* to proces tworzenia, aktualizowania i/lub wyłączaniu rekordów konta użytkownika w aplikacji w lokalnym magazynie profilów użytkownika. Większość aplikacji SaaS i chmura przechowywanie użytkowników, role i uprawnienia w ich własnych lokalnym magazynie profilów użytkownika i jest obecność takiego rekordu użytkownika w lokalnym magazynie *wymagane* logowanie jednokrotne i dostęp do pracy.

W witrynie Azure portal **aprowizacji** karcie w okienku nawigacji po lewej stronie w celu wyświetlania aplikacji przedsiębiorstwa, jakie metody inicjowania obsługi administracyjnej są obsługiwane dla tej aplikacji. Może to być jeden z dwóch wartości:

## <a name="configuring-an-application-for-manual-provisioning"></a>Konfigurowanie aplikacji do ręcznego inicjowania obsługi

*Ręczne* inicjowania obsługi administracyjnej oznacza, że konta użytkowników należy utworzyć ręcznie przy użyciu metod dostarczonych przez tę aplikację. Może to oznaczać, logując się do portalu administracyjnego dla danej aplikacji i dodawanie użytkowników przy użyciu interfejsu użytkownika opartego na sieci web. Lub można do niej przekazywanie arkusz kalkulacyjny z szczegółów konta użytkownika, przy użyciu mechanizmu udostępniane przez tę aplikację. Zapoznaj się z dokumentacją dostarczoną przez aplikację, lub skontaktuj się z deweloperem aplikacji, aby określić, czy są dostępne mechanizmy wat.

Jeśli ręczna jest jedynym trybem wyświetlane dla danej aplikacji, oznacza to, czy nie usługi Azure AD automatyczne Inicjowanie obsługi administracyjnej łącznika została utworzona dla aplikacji jeszcze. Lub, oznacza to, że aplikacja obsługuje interfejs API zarządzania wstępnych użytkownika umożliwiającego tworzenie automatycznego inicjowania obsługi administracyjnej łącznika.

Jeśli chcesz zażądać pomocy technicznej dla automatycznej aprowizacji dla danej aplikacji, możesz też wypełnić przy użyciu żądania [Azure Active Directory żądań aplikacji](https://aka.ms/aadapprequest).

## <a name="configuring-an-application-for-automatic-provisioning"></a>Konfigurowanie aplikacji do automatycznej aprowizacji

*Automatyczne* oznacza, że inicjowanie obsługi administracyjnej łącznika usługi Azure AD zostało opracowane dla tej aplikacji. Aby uzyskać więcej informacji na temat usługi Azure AD, usługę aprowizacji i jak to działa, zobacz [Automatyzowanie aprowizacji użytkowników oraz anulowania zastrzeżenia do aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning).

Aby uzyskać więcej informacji o sposobie inicjowania obsługi, konkretnych użytkowników i grup dla aplikacji, zobacz [zarządzania aprowizowaniem kont użytkowników dla aplikacji korporacyjnych](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning).

Rzeczywiste kroki wymagane do włączania i konfigurowania automatycznej aprowizacji się różnić w zależności od aplikacji.

>[!NOTE]
>Należy rozpocząć, wyszukując samouczek ustawienia specyficzne dla konfigurowania aprowizacji dla aplikacji i następujących te kroki, aby skonfigurować aplikację i Azure AD, aby utworzyć połączenie inicjowania obsługi administracyjnej. 
>
>

Samouczki aplikacji znajduje się w temacie [listę samouczków dotyczących integracji aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

Ważne jest, aby uwzględnić podczas konfigurowania aprowizacji można przejrzeć i skonfigurować mapowania atrybutów i przepływów pracy, które określają, które użytkownik (lub grupy) właściwości przepływu z usługi Azure AD do aplikacji. Obejmuje to ustawienie "property pasującego" używany do jednoznacznego identyfikowania i dopasować użytkowników/grup między dwoma systemami. Aby uzyskać więcej informacji na temat tego procesu ważne.

## <a name="next-steps"></a>Kolejne kroki
[Dostosowywanie użytkownika aprowizacji mapowań atrybutów dla aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings)

