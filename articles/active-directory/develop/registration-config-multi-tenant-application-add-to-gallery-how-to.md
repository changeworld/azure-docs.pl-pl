---
title: Dodaj aplikację wielodostępną do galerii aplikacji usługi Azure AD | Dokumentacja firmy Microsoft
description: W tym artykule wyjaśniono, jak możesz wyświetlić listę aplikacji wielodostępnych niestandardowej w galerii aplikacji usługi Azure AD.
services: active-directory
documentationCenter: na
author: rwike77
manager: CelesteDG
ms.assetid: 92c1651a-675d-42c8-b337-f78e7dbcc40d
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ryanwi
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0434ec889a791079e2019bcb1d04f5ce0fceb731
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65545553"
---
# <a name="add-a-multitenant-application-to-the-azure-ad-application-gallery"></a>Dodaj aplikację wielodostępną do galerii aplikacji usługi Azure AD

## <a name="what-is-the-azure-ad-application-gallery"></a>Co to jest galerii aplikacji usługi Azure AD?

Azure Active Directory (Azure AD) to oparta na chmurze Usługa zarządzania tożsamościami. [Galerii aplikacji usługi Azure AD](https://azure.microsoft.com/marketplace/active-directory/all/) znajduje się w portalu Azure Marketplace sklepu z aplikacjami, gdzie wszystkie łączniki aplikacji są publikowane do logowania jednokrotnego i aprowizacji użytkowników. Klienci, którzy używają usługi Azure AD jako dostawcy tożsamości znaleźć innego łączników aplikacji SaaS, opublikowane w tym miejscu. Administratorzy IT Dodaj łączniki z galerii aplikacji i następnie konfigurowanie i używanie łączników do logowania jednokrotnego i aprowizacji. Usługa Azure AD obsługuje wszystkich protokołów federacyjnego głównej, w tym SAML 2.0, OpenID Connect, OAuth i WS-Fed dla logowania jednokrotnego. 

## <a name="if-your-application-supports-saml-or-openidconnect"></a>Jeśli aplikacja obsługuje SAML lub OpenIDConnect
W przypadku wielodostępnej aplikacji, która ma być wyświetlane w galerii aplikacji usługi Azure AD musi najpierw upewnij się, że aplikacja obsługuje jeden z następujących pojedynczego logowania jednokrotnego technologii:

- **OpenID Connect**: Aby na liście aplikacji, tworzenie wielodostępnych aplikacji w usłudze Azure AD i zaimplementować [platformy wyrażania zgody w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) dla aplikacji. Wyślij żądanie logowania do wspólnego punktu końcowego, aby każdy klient może zapewnić zgody na aplikację. Możesz kontrolować dostęp użytkownika na podstawie Identyfikatora dzierżawy i nazwa UPN użytkownika odebrane w tokenie. Przesyłanie aplikacji przy użyciu z procedurą opisaną w [listę aplikacji w galerii aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

- **SAML**: Jeśli aplikacja obsługuje SAML 2.0, aplikacja może wyświetlane w galerii. Postępuj zgodnie z instrukcjami w [listę aplikacji w galerii aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>Jeśli aplikacja nie obsługuje protokołu SAML lub OpenIDConnect
Galeria aplikacji za pomocą hasła pojedynczego logowania jednokrotnego technologii mogą być nadal zintegrowane aplikacje, które nie obsługują protokołu SAML lub OpenIDConnect.

Hasło logowania jednokrotnego, nazywany również magazynowanie haseł umożliwia zarządzanie dostępem użytkowników i hasła do aplikacji sieci web, które nie obsługują federacji tożsamości. Jest również przydatne w scenariuszach, w których kilka użytkownicy potrzebują do udostępnienia jednego konta, takie jak do kont aplikacji mediów społecznościowych w Twojej organizacji. 

Jeśli chcesz wyświetlić listę aplikacji przy użyciu tej technologii:
1. Utwórz aplikację internetową, która zawiera strony logowania HTML do skonfigurowania [hasło logowania jednokrotnego](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis). 
2. Przesyłanie żądania, zgodnie z opisem w [listę aplikacji w galerii aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

## <a name="escalations"></a>Eskalacji

Odpowiedzi na wszystkie pytania techniczne, Wyślij e-mail na adres [zespołu integracja logowania jednokrotnego usługi Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) i skontaktujemy się należy jak najszybciej.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się, jak [umieść swoją aplikację w galerii aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).
