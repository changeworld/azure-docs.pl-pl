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
ms.openlocfilehash: 6188b141ec1a514d999f290366cd9ffbafc1d96c
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/22/2018
ms.locfileid: "53753136"
---
# <a name="azure-active-directory-report-retention-policies"></a>Zasady przechowywania raportów w usłudze Azure Active Directory

Ten artykuł zawiera informacje o zasadach przechowywania danych dla raportów innego działania w usłudze Azure Active Directory. 

### <a name="when-does-azure-ad-start-collecting-data"></a>Gdy usługa Azure AD zacząć zbierać dane?

| Wersja usługi Azure AD | Rozpoczęcia zbierania |
| :--              | :--   |
| Usługa Azure AD — warstwa Premium P1 <br /> Usługa Azure AD — warstwa Premium P2 | Gdy zarejestrujesz się w ramach subskrypcji |
| Usługa Azure AD — warstwa Bezpłatna <br /> Usługa Azure AD — warstwa Podstawowa | Przy pierwszym otwarciu [bloku Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) lub użyj [interfejsy API raportowania](https://aka.ms/aadreports)  |

---

### <a name="when-is-the-activity-data-available-in-the-azure-portal"></a>Gdy dane o aktywności jest dostępna w witrynie Azure portal?

- **Od razu** — Jeśli już pracy z raportami w witrynie Azure portal.
- **W ciągu 2 godzin** — Jeśli jeszcze nie jest włączone raportowanie w portalu Azure.

---

### <a name="when-does-azure-ad-start-collecting-security-signal-data"></a>Gdy usługi Azure AD rozpocząć zbieranie danych sygnału zabezpieczeń?  

Sygnały zabezpieczeń procesu zbierania rozpoczyna się po użytkownik zgody na korzystanie z **Centrum usługi Identity Protection**. 

---

### <a name="how-long-does-azure-ad-store-the-data"></a>Jak długo w usłudze Azure AD są przechowywane dane?

**Raporty dotyczące działań**    

| Raport                 | Usługa Azure AD — warstwa Bezpłatna | Usługa Azure AD — warstwa Podstawowa | Usługa Azure AD — warstwa Premium P1 | Usługa Azure AD — warstwa Premium P2 |
| :--                    | :--           | :--            | :--                 | :--                 |
| Inspekcja katalogu        | 7 dni        |  7 dni        | 30 dni             | 30 dni             |
| Aktywność związana z logowaniem       | ND           |  ND           | 30 dni             | 30 dni             |
| Użycie usługi Azure MFA        | 30 dni       |  30 dni       | 30 dni             | 30 dni             |

Można zachować dane działanie inspekcji i logowania przez okres dłuższy niż domyślny okres przechowywania opisanych powyżej, kierując do konta usługi Azure storage przy użyciu usługi Azure Monitor. Aby uzyskać więcej informacji, zobacz [archiwum usługi Azure AD loguje się do konta usługi Azure storage](quickstart-azure-monitor-route-logs-to-storage-account.md).

**Sygnały zabezpieczeń**

| Raport         | Usługa Azure AD — warstwa Bezpłatna | Usługa Azure AD — warstwa Podstawowa | Usługa Azure AD — warstwa Premium P1 | Usługa Azure AD — warstwa Premium P2 |
| :--            | :--           | :--            | :--                 | :--                 |
| Narażeni użytkownicy  | 7 dni        | 7 dni         | 30 dni             | 90 dni             |
| Ryzykowne logowania | 7 dni        | 7 dni         |  30 dni            | 90 dni             |

---
