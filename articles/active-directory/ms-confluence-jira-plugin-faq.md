---
title: Często zadawane pytania dotyczące usługi Azure Active Directory SSO wtyczki | Dokumentacja firmy Microsoft
description: Odpowiedzi na często zadawane pytania na temat konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Jira/zlewiska.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4b663047-7f88-443b-97bd-54224b232815
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2018
ms.author: jeedes
ms.openlocfilehash: b642d6de9fd9265d7f7ee11b827a36d20e5bcce7
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="faq-for-the-azure-active-directory-sso-plug-in"></a>Często zadawane pytania dotyczące usługi Azure Active Directory SSO wtyczki 

## <a name="what-does-the-plug-in-do"></a>Jaki jest wtyczki?

Dodatek zapewnia rejestracji jednokrotnej (SSO) Atlassian Jira (w tym Jira Core, oprogramowanie Jira, Jira działu) i zlewiska oprogramowania lokalnego. Wtyczka współpracuje z usługą Azure Active Directory (Azure AD) funkcję dostawcy tożsamości (IdP).

## <a name="which-atlassian-products-does-the-plug-in-work-with"></a>Które produkty Atlassian działa wtyczki z?

Wtyczka współpracuje z wersjami lokalnymi Jira i zlewiska.

## <a name="does-the-plug-in-work-on-cloud-versions"></a>Wykonuje pracę wtyczki w wersjach chmury?

Nie. Wtyczka obsługuje tylko lokalne wersje Jira i zlewiska.

## <a name="which-versions-of-jira-and-confluence-does-the-plug-in-support"></a>Które wersje Jira i zlewiska czy wtyczki obsługuje?

Wtyczka obsługuje następujące wersje:

* Podstawowe Jira i oprogramowania: 6.0 do 7.2.2 
* Jira działu: 3.2 do 3.0 
* Zlewiska: 5.10 do 5.0

## <a name="is-the-plug-in-free-or-paid"></a>Wtyczka jest bezpłatne lub płatną?

Jest to bezpłatny dodatek. Można go zainstalować z witryny Atlassian Marketplace.

## <a name="do-i-need-to-restart-jira-or-confluence-after-i-deploy-the-plug-in"></a>Należy ponownie uruchomić Jira lub zlewiska po zainstalować dodatek typu plug-in?

Ponowne uruchomienie nie jest wymagane. Można uruchomić bezpośrednio za pomocą wtyczki.

## <a name="how-do-i-get-support-for-the-plug-in"></a>Jak uzyskać pomoc techniczną dla wtyczki?

Dotrzeć do [zespołu integracji usługi Azure AD SSO](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Zespół reaguje w 24-48 godzin. 

Może też wiązać biletu pomocy technicznej w firmie Microsoft za pośrednictwem kanału portalu Azure.

## <a name="would-the-plug-in-work-on-a-mac-or-ubuntu-installation-of-jira-and-confluence"></a>Czy wtyczki pracy w instalacji Mac lub Ubuntu Jira i zlewiska?

Przetestowano wtyczkę tylko w 64-bitowej instalacji systemu Windows Server Jira i zlewiska.

## <a name="does-the-plug-in-work-with-idps-other-than-azure-ad"></a>Wykonuje pracę wtyczki z IdPs innego niż Azure AD?

Nie. Działa tylko z usługą Azure AD.

## <a name="what-version-of-saml-does-the-plug-in-work-with"></a>Która wersja SAML działa wtyczki z?

Działa on z SAML 2.0.

## <a name="does-the-plug-in-do-user-provisioning"></a>Wtyczka robi Inicjowanie obsługi użytkowników?

Nie. Dodatek zapewnia tylko logowania jednokrotnego SAML 2.0 na podstawie. Użytkownik ma być aprowizowana w aplikacji przed logowanie SSO.

## <a name="does-the-plug-in-support-cluster-versions-of-jira-and-confluence"></a>Czy obsługa wtyczki wersje klastra Jira i zlewiska?

Nie. Wtyczka współpracuje z wersjami lokalnymi Jira i zlewiska.

## <a name="does-the-plug-in-work-with-http-versions-of-jira-and-confluence"></a>Wykonuje pracę wtyczki z Jira i zlewiska wersji protokołu HTTP?

Nie. Wtyczka współpracuje z włączonym protokołem HTTPS tylko instalacji.