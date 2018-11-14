---
title: Zasady przechowywania raportów w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Zasady przechowywania dotyczące danych raportu w usłudze Azure Active Directory
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 601169cc62a99438f661adc06ab166b545606edb
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51624594"
---
# <a name="azure-active-directory-report-retention-policies"></a>Zasady przechowywania raportów w usłudze Azure Active Directory

Ten artykuł zawiera informacje o zasadach przechowywania danych dla raportów innego działania w usłudze Azure Active Directory. 

### <a name="when-does-azure-ad-start-collecting-data"></a>Gdy usługa Azure AD zacząć zbierać dane?

| Wersja usługi Azure AD | Rozpoczęcia zbierania |
| :--              | :--   |
| Usługa Azure AD — warstwa Premium P1 <br /> Usługa Azure AD — warstwa Premium P2 | Gdy zarejestrujesz się w ramach subskrypcji |
| Usługa Azure AD — warstwa Bezpłatna | Przy pierwszym otwarciu [bloku Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) lub użyj [interfejsy API raportowania](https://aka.ms/aadreports)  |

---

### <a name="when-is-the-activity-data-available-in-the-azure-portal"></a>Gdy dane o aktywności jest dostępna w witrynie Azure portal?

- **Od razu** — Jeśli już pracy z raportami w witrynie Azure portal.
- **W ciągu 2 godzin** — Jeśli jeszcze nie jest włączone raportowanie w portalu Azure.

---

### <a name="when-does-azure-ad-start-collecting-security-signal-data"></a>Gdy usługi Azure AD rozpocząć zbieranie danych sygnału zabezpieczeń?  

Sygnały zabezpieczeń procesu zbierania rozpoczyna się po użytkownik zgody na korzystanie z **Centrum usługi Identity Protection**. 

---

### <a name="how-long-does-azure-ad-store-the-data"></a>Jak długo w usłudze Azure AD są przechowywane dane?

**Raporty aktywności**    

| Raport                 | Usługa Azure AD — warstwa Bezpłatna | Usługa Azure AD — warstwa Premium P1 | Usługa Azure AD — warstwa Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| Inspekcja katalogu        | 7 dni        | 30 dni             | 30 dni             |
| Aktywność związana z logowaniem       | ND           | 30 dni             | 30 dni             |
| Użycie usługi Azure MFA        | 30 dni       | 30 dni             | 30 dni             |

**Sygnały zabezpieczeń**

| Raport         | Usługa Azure AD — warstwa Bezpłatna | Usługa Azure AD — warstwa Premium P1 | Usługa Azure AD — warstwa Premium P2 |
| :--            | :--           | :--                 | :--                 |
| Narażeni użytkownicy  | 7 dni        | 30 dni             | 90 dni             |
| Ryzykowne logowania | 7 dni        | 30 dni             | 90 dni             |

---
