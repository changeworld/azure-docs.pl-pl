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
ms.date: 05/10/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 68028fd1ba116251860e5c370e9e9ce61fd314bb
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2018
ms.locfileid: "42054880"
---
# <a name="azure-active-directory-report-retention-policies"></a>Zasady przechowywania raportów w usłudze Azure Active Directory


Ten artykuł zawiera odpowiedzi na często zadawane pytania w połączeniu z przechowywaniem danych dla raportów innego działania w usłudze Azure Active Directory. 

### <a name="q-how-can-you-get-the-collection-of-activity-data-started"></a>P: jak można uzyskać zbierania danych działania pracę?

**ODP.:**

| Wersja usługi Azure AD | Rozpoczęcia zbierania |
| :--              | :--   |
| Usługa Azure AD — warstwa Premium P1 <br /> Usługa Azure AD — warstwa Premium P2 | Gdy zarejestrujesz się w ramach subskrypcji |
| Usługa Azure AD — warstwa Bezpłatna | Przy pierwszym otwarciu [bloku Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) lub użyj [interfejsy API raportowania](https://aka.ms/aadreports)  |

---
### <a name="q-when-is-your-activity-data-available-in-the-azure-portal"></a>Pyt. kiedy dane o aktywności są dostępne w witrynie Azure portal

**ODP.:**

- **Od razu** — Jeśli już pracy z raportami w witrynie Azure portal.
- **W ciągu 2 godzin** — Jeśli jeszcze nie jest włączone raportowanie w portalu Azure.

---

### <a name="q-how-can-you-get-the-collection-of-security-signals-started"></a>P: jak można uzyskać kolekcja sygnałów zabezpieczeń pracę?  

**Odp.:** sygnały zabezpieczeń procesu zbierania rozpoczyna się po użytkownik zgody na korzystanie z Centrum usługi Identity Protection. 


---

### <a name="q-for-how-long-is-the-collected-data-stored"></a>P: na jak długo zebrane dane są przechowywane?

**ODP.:**

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
