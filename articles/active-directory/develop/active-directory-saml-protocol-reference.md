---
title: Sposób korzystania z protokołu SAML w usłudze Azure AD | Dokumenty firmy Microsoft
description: Ten artykuł zawiera omówienie profilów SAML logowania jednokrotnego i wylogowania pojedynczego w usłudze Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 88125cfc-45c1-448b-9903-a629d8f31b01
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/05/2018
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: dc7771f29fb5d00aedfe5162a98f5f0c14544a7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77161175"
---
# <a name="how-azure-ad-uses-the-saml-protocol"></a>Jak usługa Azure AD używa protokołu SAML

Usługa Azure Active Directory (Azure AD) używa protokołu SAML 2.0, aby umożliwić aplikacjom zapewnienie użytkownikom środowiska logowania jednokrotnego. Profile [saml logowania jednokrotnego](single-sign-on-saml-protocol.md) i [wylogowania jednokrotnego](single-sign-out-saml-protocol.md) usługi Azure AD wyjaśniają, w jaki sposób potwierdzenia, protokoły i powiązania SAML są używane w usłudze dostawcy tożsamości.

Protokół SAML wymaga dostawcy tożsamości (Azure AD) i dostawcy usług (aplikacji) do wymiany informacji o sobie.

Gdy aplikacja jest zarejestrowana w usłudze Azure AD, deweloper aplikacji rejestruje informacje związane z federacją w usłudze Azure AD. Informacje te obejmują **redirect URI** i **identyfikator URI metadanych** aplikacji.

Usługa Azure AD używa **identyfikatora URI metadanych** usługi w chmurze do pobierania klucza podpisywania i identyfikatora URI wylogowania. Klient może otworzyć aplikację w **usłudze Azure AD -> rejestracja aplikacji,** a następnie w **ustawieniach -> właściwości**, mogą zaktualizować adres URL wylogowania. W ten sposób usługa Azure AD może wysłać odpowiedź do poprawnego adresu URL. 

Usługa Azure Active Directory udostępnia specyficzne dla dzierżawy i wspólne (niezależne od dzierżawy) pojedyncze punkty końcowe logowania i pojedynczego wylogowywania. Te adresy URL reprezentują adresowalne lokalizacje — nie są tylko identyfikatorami — więc możesz przejść do punktu końcowego, aby odczytać metadane.

* Punkt końcowy specyficzny dla dzierżawy znajduje się w . `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml` Symbol zastępczy * \<>TenantDomainName* reprezentuje zarejestrowaną nazwę domeny lub identyfikator GUID identyfikatora dzierżawy dzierżawy usługi Azure AD. Na przykład metadane federacji dzierżawy contoso.com są następujące:https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

* Punkt końcowy niezależny od dzierżawy znajduje się w punkcie `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`. W tym adresie punktu końcowego **często** pojawia się zamiast nazwy domeny dzierżawy lub identyfikatora.

Aby uzyskać informacje o dokumentach metadanych federacji publikowanych przez usługę Azure AD, zobacz [Metadane federacji](../azuread-dev/azure-ad-federation-metadata.md).
