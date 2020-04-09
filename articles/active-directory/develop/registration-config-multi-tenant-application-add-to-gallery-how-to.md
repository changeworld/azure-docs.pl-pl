---
title: Dodawanie aplikacji wielodostępne do galerii aplikacji usługi Azure AD
description: W tym artykule wyjaśniono, jak można wyświetlić listę aplikacji wielodostępnych opracowanych na zamówienie w galerii aplikacji usługi Azure AD.
services: active-directory
documentationCenter: na
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ryanwi
ms.reviewer: jeedes
ms.openlocfilehash: 90f4d22ba782fd71460595f69a52e70be87a3047
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80883257"
---
# <a name="add-a-multitenant-application-to-the-azure-ad-application-gallery"></a>Dodawanie aplikacji wielodostępne do galerii aplikacji usługi Azure AD

## <a name="what-is-the-azure-ad-application-gallery"></a>Co to jest galeria aplikacji usługi Azure AD?

Usługa Azure Active Directory (Azure AD) to usługa tożsamości oparta na chmurze. [Galeria aplikacji usługi Azure AD](https://azure.microsoft.com/marketplace/active-directory/all/) znajduje się w sklepie z aplikacjami portalu Azure Marketplace, w którym publikowane są wszystkie łączniki aplikacji w celu logowania jednokrotnego i inicjowania obsługi administracyjnej użytkowników. Klienci, którzy używają usługi Azure AD jako dostawcy tożsamości, znajdują różne łączniki aplikacji SaaS opublikowane tutaj. Administratorzy IT dodają łączniki z galerii aplikacji, a następnie konfigurują i używają łączników do logowania jednokrotnego i inicjowania obsługi administracyjnej. Usługa Azure AD obsługuje wszystkie główne protokoły federacyjne, w tym SAML 2.0, OpenID Connect, OAuth i WS-Fed dla logowania jednokrotnego. 

## <a name="if-your-application-supports-saml-or-openidconnect"></a>Jeśli aplikacja obsługuje SAML lub OpenIDConnect
Jeśli masz aplikację wielodostępną, która ma być wymieniona w galerii aplikacji usługi Azure AD, należy najpierw upewnić się, że aplikacja obsługuje jedną z następujących technologii logowania jednokrotnego:

- **OpenID Connect:** Aby mieć aplikację na liście, należy utworzyć aplikację wielodostępną w usłudze Azure AD i zaimplementować [platformę zgody usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/consent-framework) dla aplikacji. Wyślij żądanie logowania do wspólnego punktu końcowego, aby każdy klient mógł wyrazić zgodę na aplikację. Można kontrolować dostęp użytkownika na podstawie identyfikatora dzierżawy i nazwy UPN użytkownika odebrane w tokenie. Prześlij zgłoszenie przy użyciu procesu opisanego w [wykazie aplikacji w galerii aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

- **SAML**: Jeśli aplikacja obsługuje SAML 2.0, aplikacja może być wymieniona w galerii. Postępuj zgodnie z instrukcjami [w aukcji aplikacji w galerii aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>Jeśli aplikacja nie obsługuje saml lub OpenIDConnect
Aplikacje, które nie obsługują SAML lub OpenIDConnect nadal można zintegrować z galerią aplikacji za pomocą technologii logowania jednokrotnego hasła.

Logowanie jednokrotne hasła, nazywane również magazynowanie haseł, umożliwia zarządzanie dostępem użytkowników i hasłami do aplikacji sieci Web, które nie obsługują federacji tożsamości. Jest to również przydatne w scenariuszach, w których kilku użytkowników musi udostępniać jedno konto, na przykład na kontach aplikacji mediów społecznościowych organizacji. 

Jeśli chcesz wyświetlić listę aplikacji za pomocą tej technologii:
1. Utwórz aplikację sieci Web, która ma stronę logowania HTML, aby skonfigurować [logowanie jednokrotne hasła](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis). 
2. Prześlij żądanie zgodnie z opisem w [wykazie aplikacji w galerii aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

## <a name="escalations"></a>Eskalacji

W przypadku eskalacji wyślij wiadomość e-mail do [zespołu integracji sso usługi Azure AD,](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) a my wrócimy do Ciebie tak szybko, jak to możliwe.

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak [wyświetlić listę aplikacji w galerii aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).
