---
title: Jak usługa Azure AD używa protokołu SAML | Microsoft Docs
description: Ten artykuł zawiera Omówienie profilów protokołu SAML logowania jednokrotnego i logowania jednokrotnego w programie Azure Active Directory.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 88125cfc-45c1-448b-9903-a629d8f31b01
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/05/2018
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c53cb7ee7bc76b07059d68962c606cdcf866196
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68835272"
---
# <a name="how-azure-ad-uses-the-saml-protocol"></a>Jak usługa Azure AD używa protokołu SAML

Azure Active Directory (Azure AD) korzysta z protokołu SAML 2,0, aby umożliwić aplikacjom udostępnianie użytkownikom funkcji logowania jednokrotnego. Profil SAML [logowania](single-sign-on-saml-protocol.md) jednokrotnego [](single-sign-out-saml-protocol.md) i logowania jednokrotnego w usłudze Azure AD objaśnia, w jaki sposób potwierdzenia SAML, protokoły i powiązania są używane w usłudze dostawca tożsamości.

Protokół SAML wymaga od dostawcy tożsamości (Azure AD) i dostawcy usług (aplikacji) do wymiany informacji o sobie.

Gdy aplikacja jest zarejestrowana w usłudze Azure AD, Deweloper aplikacji rejestruje informacje dotyczące Federacji w usłudze Azure AD. Te informacje obejmują **Identyfikator URI przekierowania** i **Identyfikator URI metadanych** aplikacji.

Usługa Azure AD używa **identyfikatora URI metadanych** usługi w chmurze do pobrania klucza podpisywania i identyfikatora URI wylogowania. Klient może otworzyć aplikację w **usłudze Azure AD — > rejestracji aplikacji** , a następnie we **właściwościach ustawienia->** , które mogą zaktualizować adres URL wylogowywania. Dzięki temu usługa Azure AD może wysłać odpowiedź do poprawnego adresu URL. 

Azure Active Directory udostępnia specyficzne dla dzierżawców i wspólne (niezależne od dzierżawy) punkty końcowe logowania jednokrotnego i wylogowania jednokrotnego. Te adresy URL przedstawiają lokalizacje adresowane — nie są tylko identyfikatorami, więc możesz przejść do punktu końcowego, aby odczytać metadane.

* Punkt końcowy specyficzny dla dzierżawy znajduje `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`się w lokalizacji. Symbol zastępczy  *nazwadomenydzierżawy>reprezentujezarejestrowanąnazwędomenylubidentyfikatorGUIDTenantIDdzierżawyusługiAzureAD.\<* Na przykład metadane federacji dzierżawy contoso.com są następujące: https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

* Punkt końcowy niezależny od dzierżawy `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`znajduje się w lokalizacji. W tym adresie punktu końcowego pojawia się **typowy** zamiast nazwy domeny dzierżawy lub identyfikatora.

Aby uzyskać informacje o dokumentach metadanych Federacji publikowanych przez usługę Azure AD, zobacz [metadane federacji](azure-ad-federation-metadata.md).
