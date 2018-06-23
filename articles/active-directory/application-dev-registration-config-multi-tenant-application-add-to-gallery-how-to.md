---
title: Dodaj wielodostępnych aplikacji w galerii aplikacji usługi Azure AD | Dokumentacja firmy Microsoft
description: W tym artykule wyjaśniono, jak można wyświetlić listę utworzonych niestandardowych aplikacji wielodostępnym w galerii aplikacji usługi Azure AD.
services: active-directory
documentationCenter: na
author: barbkess
manager: mtillman
ms.assetid: 92c1651a-675d-42c8-b337-f78e7dbcc40d
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2018
ms.author: barbkess
ms.reviewer: jeedes
ms.openlocfilehash: 4ff64847e057a67f0ed3378baefa7762c159aba8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "36333354"
---
# <a name="add-a-multitenant-application-to-the-azure-ad-application-gallery"></a>Dodaj wielodostępnych aplikacji w galerii aplikacji usługi Azure AD

## <a name="what-is-the-azure-ad-application-gallery"></a>Co to jest galerii aplikacji Azure AD?

Azure Active Directory (Azure AD) to usługa oparta na chmurze tożsamości. [Galerii aplikacji Azure AD](https://azure.microsoft.com/marketplace/active-directory/all/) znajduje się w magazynie Azure Marketplace aplikacji, w której wszystkie łączniki aplikacji są publikowane rejestracji jednokrotnej i Inicjowanie obsługi użytkowników. Klienci, którzy korzystają z usługi Azure AD jako dostawcy tożsamości Znajdź różnych łączniki aplikacji SaaS, opublikowane w tym miejscu. Administratorzy IT dodać łączników w galerii aplikacji Konfigurowanie i łączniki na użytek logowania jednokrotnego i inicjowania obsługi administracyjnej. Usługi Azure AD obsługuje wszystkich protokołów federacyjnego głównych, w tym SAML 2.0, OpenID Connect, OAuth i WS-Fed dla logowania jednokrotnego. 

## <a name="if-your-application-supports-saml-or-openidconnect"></a>Jeśli aplikacja obsługuje SAML lub OpenIDConnect
Jeśli masz wielodostępnym aplikacji, która ma być wyświetlane w galerii aplikacji Azure AD, najpierw należy się upewnić, czy aplikacja obsługuje jeden z następujących pojedynczego logowania jednokrotnego technologii:

- **OpenID Connect**: Aby aplikacji na liście, tworzenie wielodostępnych aplikacji w usłudze Azure AD i zaimplementować [framework zgody usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#overview-of-the-consent-framework) aplikacji. Wyślij żądanie logowania do wspólnego punktu końcowego tak, aby każdy klient może zapewnić zgody do aplikacji. Można kontrolować dostęp użytkownika na podstawie Identyfikatora dzierżawy i nazwy UPN użytkownika odebrane w tokenie. Przesyłanie aplikacji przy użyciu procesu opisane w temacie [listę aplikacji w galerii aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

- **SAML**: Jeśli aplikacja obsługuje SAML 2.0, aplikacja może być wymieniona w galerii. Postępuj zgodnie z instrukcjami [listę aplikacji w galerii aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>Jeśli aplikacja nie obsługuje SAML lub OpenIDConnect
Aplikacje, które nie obsługują SAML lub OpenIDConnect nadal można zintegrować galerii aplikacji za pomocą hasła pojedynczego logowania jednokrotnego technologii.

Hasło logowania jednokrotnego, nazywany również archiwizowanie haseł umożliwia zarządzanie dostępu użytkownika i hasła do aplikacji sieci web, które nie obsługują federacji tożsamości. Jest również przydatne w scenariuszach, w których wielu użytkowników należy do udostępnienia jednego konta, takich jak do organizacji mediów społecznościowych aplikacji kont. 

Jeśli chcesz wyświetlić listę aplikacji z tej technologii:
1. Tworzenie aplikacji sieci web strony logowania HTML do skonfigurowania [hasło logowania jednokrotnego](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis). 
2. Prześlij żądanie, zgodnie z opisem w [listę aplikacji w galerii aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

## <a name="escalations"></a>Eskalacji

Odpowiedzi na wszystkie pytania techniczne, Wyślij wiadomość e-mail do [zespołu integracji usługi Azure AD SSO](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) i skontaktujemy się należy jak najszybciej.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się, jak [wyświetlanie aplikacji w galerii aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).
