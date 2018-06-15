---
title: Referencyjne protokołu SAML usługi Azure AD | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie profile rejestracji jednokrotnej i jednym SAML Sign-Out w usłudze Azure Active Directory.
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 88125cfc-45c1-448b-9903-a629d8f31b01
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: priyamo
ms.openlocfilehash: 1fcc854362428d4ae32e815ccbf611f4562399ad
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2017
ms.locfileid: "26615778"
---
# <a name="how-azure-active-directory-uses-the-saml-protocol"></a>Jak Azure Active Directory korzysta z protokołu SAML
Azure Active Directory (Azure AD) protokół używa SAML 2.0 na potrzeby aplikacji zapewnić jednego środowiska logowania jednokrotnego dla użytkowników. [Rejestracji jednokrotnej](active-directory-single-sign-on-protocol-reference.md) i [jednym wylogowania](active-directory-single-sign-out-protocol-reference.md) SAML profilów usługi Azure AD wyjaśniają, jak SAML potwierdzeń, protokołów i powiązania są używane w usługę dostawcy tożsamości.

Protokół SAML wymaga dostawcy tożsamości (Azure AD) i dostawcy usług (aplikacji) do wymiany informacji o sobie.

Gdy aplikacja jest zarejestrowana w usłudze Azure AD, Deweloper aplikacji rejestruje informacje związane z federacji z usługą Azure AD. Obejmuje to **identyfikator URI przekierowania** aplikacji.

Usługa Azure Active Directory udostępnia specyficznego dla dzierżawy i wspólne (niezależny od dzierżawcy) pojedynczego logowania jednokrotnego i pojedynczego wylogowania punktów końcowych. Te adresy URL reprezentują lokalizacje adresowanego — nie są one tylko identyfikatory —, możesz przejść do punktu końcowego, aby móc odczytać metadane.

* Punkt końcowy specyficznego dla dzierżawy znajduje się pod adresem `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`.  <TenantDomainName> Symbol zastępczy reprezentuje zarejestrowaną nazwę domeny lub identyfikator GUID dzierżawy usługi Azure AD dla identyfikatora dzierżawcy. Na przykład, metadanych Federacji dzierżawcy contoso.com wynosi: https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

* Punkt końcowy niezależny od dzierżawcy znajduje się pod adresem `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`. W tym adres punktu końcowego **wspólnej** zostanie wyświetlony, a nie nazwę domeny dzierżawy lub identyfikator.

Aby dowiedzieć się, dokumentów metadanych Federacji, który publikuje usługi Azure AD, zobacz [metadanych Federacji](active-directory-federation-metadata.md).
