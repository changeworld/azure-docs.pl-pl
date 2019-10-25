---
title: Jak skonfigurować aprowizacji użytkowników w aplikacji galerii usługi Azure AD | Microsoft Docs
description: Jak szybko skonfigurować rozbudowane i anulowane Inicjowanie obsługi konta użytkownika w aplikacjach znajdujących się już w galerii aplikacji usługi Azure AD
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
ms.openlocfilehash: 8bcc53b97b1187314404cfe075f6593f437e7bf4
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72789045"
---
# <a name="how-to-configure-user-provisioning-to-an-azure-ad-gallery-application"></a>Jak skonfigurować aprowizacji użytkowników w aplikacji z galerii usługi Azure AD

*Inicjowanie obsługi konta użytkownika* to czynność tworzenia, aktualizowania i/lub wyłączania rekordów kont użytkowników w lokalnym magazynie profilów użytkownika aplikacji. Większość aplikacji w chmurze i SaaS przechowuje rolę i uprawnienia użytkowników w lokalnym magazynie profilów użytkownika, a obecność takiego rekordu użytkownika w lokalnym magazynie użytkownika jest *wymagana* do logowania jednokrotnego i dostępu do pracy.

W Azure Portal na karcie **aprowizacji** w okienku nawigacji po lewej stronie dla aplikacji przedsiębiorstwa są wyświetlane tryby aprowizacji obsługiwane przez tę aplikację. Może to być jedna z dwóch wartości:

## <a name="configuring-an-application-for-manual-provisioning"></a>Konfigurowanie aplikacji do ręcznego inicjowania obsługi

*Ręczna* obsługa administracyjna oznacza, że konta użytkowników muszą być tworzone ręcznie przy użyciu metod udostępnianych przez aplikację. Może to oznaczać zalogowanie się w portalu administracyjnym dla tej aplikacji i dodanie użytkowników przy użyciu interfejsu użytkownika opartego na sieci Web. Może być też możliwe przekazanie arkusza kalkulacyjnego z informacjami o koncie użytkownika przy użyciu mechanizmu dostarczonego przez tę aplikację. Zapoznaj się z dokumentacją dostarczoną przez aplikację lub skontaktuj się z deweloperem aplikacji, aby określić, jakie mechanizmy są dostępne.

Jeśli *ręcznie* jest jedynym trybem pokazywanym dla danej aplikacji, oznacza to, że nie ma jeszcze automatycznego łącznika aprowizacji usługi Azure AD dla aplikacji. Oznacza to, że aplikacja nie obsługuje wymagań wstępnych dla interfejsu API zarządzania użytkownikami firmy Microsoft, który jest używany do tworzenia zautomatyzowanego łącznika aprowizacji.

Jeśli chcesz zażądać obsługi automatycznej aprowizacji dla danej aplikacji, możesz podać żądanie, korzystając z [żądań aplikacji Azure Active Directory](https://aka.ms/aadapprequest).

## <a name="configuring-an-application-for-automatic-provisioning"></a>Konfigurowanie aplikacji do automatycznej aprowizacji

*Automatyczne* oznacza, że dla tej aplikacji opracowano łącznik aprowizacji usługi Azure AD. Aby uzyskać więcej informacji na temat usługi Azure AD Provisioning i sposobu jej działania, zobacz [Automatyzowanie aprowizacji użytkowników i Anulowanie udostępniania aplikacji SaaS przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning).

Aby uzyskać więcej informacji na temat udostępniania aplikacji dla konkretnych użytkowników i grup, zobacz [Zarządzanie kontami użytkowników w aplikacjach dla przedsiębiorstw](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning).

Rzeczywiste kroki wymagane do włączenia i skonfigurowania automatycznej aprowizacji różnią się w zależności od aplikacji.

> [!NOTE]
> Należy zacząć od znalezienia samouczka instalacji specyficznego dla konfigurowania aprowizacji dla aplikacji, a następnie wykonując te kroki w celu skonfigurowania aplikacji i usługi Azure AD w celu utworzenia połączenia aprowizacji. 

Samouczki aplikacji można znaleźć na [liście samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

Ważną kwestią do uwzględnienia podczas konfigurowania aprowizacji jest przejrzenie i skonfigurowanie mapowań atrybutów i przepływów pracy, które definiują, które właściwości użytkownika (lub grupy) będą przepływać z usługi Azure AD do aplikacji. Obejmuje to ustawienie "dopasowania właściwości" służącego do unikatowego identyfikowania i dopasowywania użytkowników/grup między tymi dwoma systemami. Aby uzyskać więcej informacji na temat mapowań atrybutów, zobacz link w *sekcji Następne kroki* .

## <a name="next-steps"></a>Następne kroki
[Dostosowywanie mapowań atrybutów aprowizacji użytkowników dla aplikacji SaaS w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings)

