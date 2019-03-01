---
title: Zbieranie danych usługi Azure AD Identity Protection w wersji zapoznawczej Azure przez wartownika | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zbieranie danych usługi Azure AD Identity Protection przez wartownika platformy Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 02057fc0f288c8ae77a700f09a8b6e599f6d1b16
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2019
ms.locfileid: "56993230"
---
# <a name="collect-data-from-azure-ad-identity-protection"></a>Zbieranie danych z usługi Azure AD Identity Protection

> [!IMPORTANT]
> Wartownik platformy Azure jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Można przesyłać strumieniowo dzienniki z [usługi Azure AD Identity Protection](https://docs.microsoft.com/azure/information-protection/reports-aip) do platformy Azure przez wartownika strumienia alertów do platformy Azure przez wartownika wyświetlanie pulpitów nawigacyjnych, tworzyć niestandardowe alerty i lepsze badanie. Usługa Azure Active Directory Identity Protection udostępnia skonsolidowany widok narażonych użytkowników, zdarzeń o podwyższonym ryzyku i luk w zabezpieczeniach, z możliwością natychmiastowego eliminowania ryzyka i ustawienia zasad w celu automatycznego korygowania przyszłych zdarzeń. Usługa jest oparta na doświadczeniu firmy Microsoft w ochronie tożsamości klientów i uzyskuje ogromną dokładność z sygnałów z ponad 13 miliardów log-ins dziennie. 


## <a name="prerequisites"></a>Wymagania wstępne

- Konieczne jest posiadanie [licencji usługi Azure Active Directory Premium P1 lub P2](https://azure.microsoft.com/pricing/details/active-directory/)
- Użytkownik z administratorem globalnym lub uprawnienia administratora zabezpieczeń


## <a name="connect-to-azure-ad-identity-protection"></a>Łączenie usługi Azure AD Identity Protection

Jeśli masz już usługę Azure AD Identity Protection, upewnij się, że jest [włączone w sieci](../active-directory/identity-protection/enable.md).
Jeśli wdrożono usługę Azure AD Identity Protection i pobieranie danych, dane alertu można łatwo przesyłane strumieniowo do usługi Azure przez wartownika.


1. Na platformie Azure przez wartownika, wybierz **zbierania danych** a następnie kliknij przycisk **usługi Azure AD Identity Protection** kafelka.

2. Kliknij przycisk **Connect** aby rozpocząć przesyłanie strumieniowe zdarzeń usługi Azure AD Identity Protection do platformy Azure przez wartownika.


6. Aby użyć odpowiednich schematu w usłudze Log Analytics dla alertów usługi Azure AD Identity Protection, możesz wyszukać **IdentityProtectionLogs_CL**.

## <a name="next-steps"></a>Kolejne kroki
W tym dokumencie przedstawiono sposób nawiązać połączenie z usługi Azure AD Identity Protection przez wartownika platformy Azure. Aby dowiedzieć się więcej na temat platformy Azure przez wartownika, zobacz następujące artykuły:
- Dowiedz się, jak [Uzyskaj wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpoczynanie pracy [wykrywanie zagrożeń za pomocą platformy Azure przez wartownika](tutorial-detect-threats.md).
