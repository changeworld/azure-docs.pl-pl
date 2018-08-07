---
title: Referencyjne protokołu SAML usługi Azure AD | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie logowania jednokrotnego i jednym SAML wylogowania profilów w usłudze Azure Active Directory.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 88125cfc-45c1-448b-9903-a629d8f31b01
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: celested
ms.custom: aaddev
ms.reviewer: hirsin, dastrock
ms.openlocfilehash: 067924294838459c866a0603ab092d139f1e6331
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2018
ms.locfileid: "39579235"
---
# <a name="how-azure-active-directory-uses-the-saml-protocol"></a>Jak Azure Active Directory korzysta z protokołu SAML
Azure Active Directory (Azure AD) protokół używa języka SAML 2.0 umożliwiające aplikacji w celu zapewnienia logowania jednokrotnego do użytkowników. [Logowania jednokrotnego](single-sign-on-saml-protocol.md) i [wylogowania jednokrotnego](single-sign-out-saml-protocol.md) SAML profilów usługi Azure AD wyjaśniają, jak twierdzenia SAML protokołów i powiązań są używane w usłudze dostawcy tożsamości.

Protokół SAML wymaga dostawcy tożsamości (Azure AD) i dostawcy usług (aplikacji) do wymiany informacji o sobie.

Gdy aplikacja będzie zarejestrowana w usłudze Azure AD, Deweloper aplikacji rejestruje informacje związane z federacji z usługą Azure AD. Informacje te obejmują **identyfikator URI przekierowania** i **URI metadanych** aplikacji.

Usługa Azure AD używa usługi w chmurze **URI metadanych** można pobrać klucza podpisywania i wylogowania identyfikatora URI. Aplikacja nie obsługuje metadanych identyfikatora URI, deweloper powinni skontaktować się ze pomocy technicznej firmy Microsoft w zapewnienie wylogowania identyfikatora URI i klucz podpisywania.

Usługa Azure Active Directory uwidacznia specyficznym dla dzierżawy i wspólne (niezależny od dzierżawcy) pojedynczego logowania jednokrotnego i jednym wylogowania punkty końcowe. Te adresy URL przedstawiają lokalizacji mogą być adresowane — nie są one tylko identyfikatory — dzięki czemu można przejść do punktu końcowego do odczytu metadanych.

* Punkt końcowy specyficznym dla dzierżawy znajduje się w `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`. *<TenantDomainName>* Symbol zastępczy reprezentuje zarejestrowaną nazwę domeny lub identyfikator GUID identyfikatora dzierżawy dzierżawę usługi Azure AD. Na przykład metadanych Federacji dzierżawy contoso.com wynosi: https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

* Punkt końcowy dzierżawy niezależny znajduje się w `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`. W tym adres punktu końcowego **wspólnej** pojawia się zamiast nazwy domeny dzierżawy lub identyfikatora organizacji.

Aby uzyskać informacji na temat dokumentów metadanych Federacji, który publikuje w usłudze Azure AD, zobacz [metadanych Federacji](azure-ad-federation-metadata.md).
