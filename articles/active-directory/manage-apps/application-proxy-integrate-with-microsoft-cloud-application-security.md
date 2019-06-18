---
title: Integracja aplikacji lokalnych z usługą Cloud App Security — usługa Azure Active Directory | Dokumentacja firmy Microsoft
description: W usłudze Azure Active Directory do pracy z zabezpieczeń aplikacji Microsoft Cloud (MCAS), należy skonfigurować aplikację w środowisku lokalnym. Użyj MCAS kontroli dostępu warunkowego aplikacji monitorowanie i kontrolowanie sesji w czasie rzeczywistym na podstawie zasad dostępu warunkowego. Te zasady można stosować do aplikacji lokalnych, które używają serwera Proxy aplikacji w usłudze Azure Active Directory (Azure AD).
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7b9b27a3b8329f906b1adfd48d28892a7edc4d1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67108448"
---
# <a name="configure-real-time-application-access-monitoring-with-microsoft-cloud-app-security-and-azure-active-directory"></a>Konfigurowanie, monitorowanie dostępu do aplikacji w czasie rzeczywistym za pomocą Microsoft Cloud App Security i Azure Active Directory
Skonfiguruj aplikację w środowisku lokalnym w usłudze Azure Active Directory (Azure AD) do korzystania z programu Microsoft Cloud App Security (MCAS) monitorowania w czasie rzeczywistym. MCAS używa kontroli dostępu warunkowego aplikacji do monitorowania i sesje kontroli w czasie rzeczywistym na podstawie zasad dostępu warunkowego. Te zasady można stosować do aplikacji lokalnych, które używają serwera Proxy aplikacji w usłudze Azure Active Directory (Azure AD).

Poniżej przedstawiono kilka przykładów typów zasad, które można utworzyć za pomocą MCAS:

- Blokowanie lub Ochrona pobierania poufnych dokumentów na urządzeniach niezarządzanych.
- Monitor, gdy użytkowników o wysokim ryzyku zalogować się do aplikacji, a następnie zaloguj się w ramach sesji swoje działania z. Dzięki tym informacjom można analizować zachowanie użytkowników, aby określić, jak zastosować zasady sesji.
- Aby zablokować dostęp do określonych aplikacji z niezarządzanych urządzeń, należy użyć certyfikatów klienta lub zasad zgodności urządzeń.
- Ograniczyć sesje użytkowników od sieci innych niż firmowe. Można zapewnić dostęp ograniczony do użytkowników uzyskujących dostęp do aplikacji spoza sieci firmowej. Na przykład ten ograniczony dostęp mogą Zablokuj użytkownikowi możliwość pobierania poufnych dokumentów.

Aby uzyskać więcej informacji, zobacz [ochrony aplikacji w usłudze Microsoft Cloud App Security kontroli dostępu warunkowego aplikacji](/cloud-app-security/proxy-intro-aad).

## <a name="requirements"></a>Wymagania

Licencja:

- EMS E5 licencji, lub 
- Usługa Azure Active Directory Premium P1 i autonomicznych MCAS.

Aplikacja lokalna:

- Aplikacja lokalna, należy użyć delegowanie ograniczone protokołu Kerberos (KCD)

Skonfiguruj serwer Proxy aplikacji:

- Konfigurowanie usługi Azure AD, aby użyć serwera Proxy aplikacji, w tym przygotowywanie środowiska i instalowanie łącznika serwera Proxy aplikacji. Aby zapoznać się z samouczkiem, zobacz [dodać aplikacje w środowisku lokalnym, dostępu zdalnego za pośrednictwem serwera Proxy aplikacji w usłudze Azure AD](application-proxy-add-on-premises-application.md). 

## <a name="add-on-premises-application-to-azure-ad"></a>Dodawanie aplikacji lokalnych do usługi Azure AD

Dodawanie aplikacji lokalnych do usługi Azure AD. Aby uzyskać szybki Start, zobacz [Dodawanie aplikacji lokalnych do usługi Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). Podczas dodawania aplikacji, należy ustawić następujące dwa ustawienia **Dodaj aplikację w środowisku lokalnym** bloku:

- **Wstępne uwierzytelnianie**: Enter **Azure Active Directory**.
- **Przekształć adresy URL w treści aplikacji**: Wybierz **tak**.

Te dwa ustawienia są wymagane dla aplikacji do pracy z MCAS.

## <a name="test-the-on-premises-application"></a>Testowanie aplikacji w środowisku lokalnym

Po dodaniu aplikacji do usługi Azure AD, wykonaj kroki w [przetestować aplikację](application-proxy-add-on-premises-application.md#test-the-application) można dodać użytkownika do testowania i testowanie logowania jednokrotnego. 

## <a name="deploy-conditional-access-app-control"></a>Wdrażanie kontroli dostępu warunkowego aplikacji

Aby skonfigurować aplikację przy użyciu kontroli dostępu warunkowego w aplikacji, postępuj zgodnie z instrukcjami [wdrażanie kontroli usługi aplikacji dostępu warunkowego dla usługi Azure AD aplikacji](/cloud-app-security/proxy-deployment-aad).


## <a name="test-conditional-access-app-control"></a>Kontrola testu dostępu warunkowego aplikacji

Aby przetestować wdrażanie aplikacji usługi Azure AD przy użyciu kontroli dostępu warunkowego w aplikacji, postępuj zgodnie z instrukcjami [. testowanie wdrożenia dla aplikacji usługi Azure AD](/cloud-app-security/proxy-deployment-aad).





