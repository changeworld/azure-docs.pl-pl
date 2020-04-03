---
title: Łączenie danych usługi Azure AD Identity Protection z usługą Azure Sentinel
description: Dowiedz się, jak połączyć dane usługi Azure AD Identity Protection z usługą Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 11/17/2019
ms.author: yelevin
ms.openlocfilehash: b82ddfef57efaaca0ae43750cd306a63a772b911
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80616821"
---
# <a name="connect-data-from-azure-ad-identity-protection"></a>Łączenie danych z usługi Azure AD Identity Protection



Dzienniki [usługi Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection) można przesyłać strumieniowo do usługi Azure Sentinel, aby przesyłać strumieniowo alerty do usługi Azure Sentinel w celu wyświetlania pulpitów nawigacyjnych, tworzenia niestandardowych alertów i usprawnienia badania. Usługa Azure Active Directory Identity Protection zapewnia skonsolidowany widok użytkowników ryzyka, wykrywanie ryzyka i luki w zabezpieczeniach, z możliwością natychmiastowego korygować ryzyko i ustawiania zasad w celu automatycznego korygowanie przyszłych zdarzeń. Usługa jest oparta na doświadczeniu firmy Microsoft w ochronie tożsamości konsumentów i zyskuje ogromną dokładność z sygnału z ponad 13 miliardów logowań dziennie. 


## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć [licencję Premium P1 lub P2 usługi Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
- Użytkownik z uprawnieniami administratora globalnego lub administratora zabezpieczeń


## <a name="connect-to-azure-ad-identity-protection"></a>Łączenie się z usługą Azure AD Identity Protection

Jeśli masz już usługę Azure AD Identity Protection, upewnij się, że jest [włączona w sieci](../active-directory/identity-protection/overview-identity-protection.md).
Jeśli usługa Azure AD Identity Protection zostanie wdrożona i będzie otrzymywać dane, dane alertów można łatwo przesyłać strumieniowo do usługi Azure Sentinel.


1. W usłudze Azure Sentinel wybierz **łączniki danych,** a następnie kliknij kafelek **usługi Azure AD Identity Protection.**

2. Kliknij **przycisk Połącz,** aby rozpocząć przesyłanie strumieniowe zdarzeń usługi Azure AD Identity Protection do usługi Azure Sentinel.


6. Aby użyć odpowiedniego schematu w usłudze Log Analytics dla alertów usługi Azure AD Identity Protection, wyszukaj **securityalert**.

## <a name="next-steps"></a>Następne kroki
W tym dokumencie dowiesz się, jak połączyć usługę Azure AD Identity Protection z usługą Azure Sentinel. Aby dowiedzieć się więcej o usłudze Azure Sentinel, zobacz następujące artykuły:
- Dowiedz się, jak [uzyskać wgląd w dane i potencjalne zagrożenia.](quickstart-get-visibility.md)
- Rozpocznij [wykrywanie zagrożeń za pomocą usługi Azure Sentinel](tutorial-detect-threats-built-in.md).
