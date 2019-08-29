---
title: Łączenie danych Azure AD Identity Protection z wersją zapoznawczą platformy Azure Microsoft Docs
description: Dowiedz się, jak połączyć Azure AD Identity Protection dane z platformą Azure — wskaźnikiem.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 7478e5a5ec2260760bb6ddb1a90a66e3acdf2201
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129256"
---
# <a name="connect-data-from-azure-ad-identity-protection"></a>Łączenie danych z Azure AD Identity Protection

> [!IMPORTANT]
> W publicznej wersji zapoznawczej jest obecnie dostępna usługa Azure.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Można przesyłać strumieniowo dzienniki z [Azure AD Identity Protection](https://docs.microsoft.com/azure/information-protection/reports-aip) do usługi Azure wskaźnikowego, aby przesyłać strumieniowo alerty do usługi Azure wskaźnikowego, aby wyświetlać pulpity nawigacyjne, tworzyć niestandardowe alerty i ulepszać badanie. Azure Active Directory Identity Protection zapewnia skonsolidowany widok narażonych użytkowników, wykrywanie ryzyka i luki w zabezpieczeniach, z możliwością natychmiastowego korygowania ryzyka i ustawiania zasad automatycznego korygowania przyszłych zdarzeń. Usługa jest oparta na doświadczeniu firmy Microsoft chroniącym tożsamości klientów i uzyskuje ogromną dokładność od sygnału od ponad 13 000 000 000 logowań dziennie. 


## <a name="prerequisites"></a>Wymagania wstępne

- Wymagana jest licencja na [Azure Active Directory — wersja Premium P1 lub P2](https://azure.microsoft.com/pricing/details/active-directory/)
- Użytkownik z uprawnieniami administratora globalnego lub administratora zabezpieczeń


## <a name="connect-to-azure-ad-identity-protection"></a>Połącz z Azure AD Identity Protection

Jeśli masz już Azure AD Identity Protection, upewnij się, że jest [włączona w sieci](../active-directory/identity-protection/enable.md).
Jeśli Azure AD Identity Protection jest wdrożona i pobiera dane, dane alertów można łatwo przesłać strumieniowo do usługi Azure wskaźnikowej.


1. W obszarze wskaźnik platformy Azure wybierz pozycję **Łączniki danych** , a następnie kliknij kafelek **Azure AD Identity Protection** .

2. Kliknij przycisk **Połącz** , aby rozpocząć przesyłanie strumieniowe zdarzeń Azure AD Identity Protection do usługi Azure wskaźnikowej.


6. Aby użyć odpowiedniego schematu w Log Analytics dla alertów Azure AD Identity Protection, Wyszukaj pozycję **IdentityProtectionLogs_CL**.

## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób nawiązywania połączenia Azure AD Identity Protection z platformą Azure — wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats.md).
