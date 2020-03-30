---
title: Magazyn danych tożsamości dla klientów z Australii — usługa Azure AD
description: Dowiedz się, gdzie usługa Azure Active Directory przechowuje dane związane z tożsamością dla swoich australijskich klientów.
services: active-directory
author: msaburnley
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0dc659f3d580646c1e86e5e97940268311e5546f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460538"
---
# <a name="identity-data-storage-for-australian-and-new-zealand-customers-in-azure-active-directory"></a>Przechowywanie danych tożsamości dla klientów z Australii i Nowej Zelandii w usłudze Azure Active Directory

Dane dotyczące tożsamości są przechowywane przez usługę Azure AD w lokalizacji geograficznej na podstawie adresu podanego przez organizację podczas subskrybowania usługi Microsoft Online, takiej jak Office 365 i Azure. Aby uzyskać informacje o tym, gdzie są przechowywane dane klienta tożsamości, można użyć sekcji [Gdzie znajdują się dane?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

> [!NOTE]
> Usługi i aplikacje integrujące się z usługą Azure AD mają dostęp do danych klienta tożsamości. Oceń każdą usługę i aplikację używaną do określenia sposobu przetwarzania danych klienta tożsamości przez tę konkretną usługę i aplikację oraz czy spełniają one wymagania firmy dotyczące przechowywania danych. Aby uzyskać więcej informacji na temat przechowywania danych usług firmy Microsoft, zobacz sekcję Gdzie przechowywane są Twoje dane? w Centrum zaufania Microsoft.

Dla klientów, którzy podali adres w Australii i Nowej Zelandii i korzystają z bezpłatnej wersji usługi Azure AD, usługa Azure AD przechowuje dane dotyczące danych umożliwiających identyfikację w australijskich centrach danych. 

Wszystkie inne usługi Premium usługi Azure AD przechowują dane klientów w globalnych centrach danych. Aby zlokalizować centrum danych dla usługi, zobacz [Usługa Azure Active Directory — gdzie znajdują się dane?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Uwierzytelnianie wieloskładnikowe platformy Microsoft Azure (MFA)

Usługa usługi MFA w usłudze Azure AD przechowuje dane klienta tożsamości w globalnych centrach danych w spoczynku. Aby dowiedzieć się więcej o informacjach o użytkownikach gromadzonych i przechowywanych przez chmurową usługę Azure MFA i serwer usługi Azure MFA, zobacz [Zbieranie danych użytkowników uwierzytelniania wieloskładnikowego platformy Azure.](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-data-residency) Jeśli klienci korzystają z usługi MFA, ich dane będą przechowywane poza centrami danych w Australii w spoczynku. 

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat dowolnej funkcji i funkcji opisanych powyżej, zobacz następujące artykuły:
- [Co to jest uwierzytelnianie wieloskładnikowe?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)
