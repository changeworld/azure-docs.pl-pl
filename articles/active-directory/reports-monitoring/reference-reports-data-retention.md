---
title: Jak długo w usłudze Azure AD są przechowywane dane raportowania? | Microsoft Docs
description: Dowiedz się, jak długo Azure przechowuje różne rodzaje danych raportowania.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 41fa12c9d79d14a6602d995ed93b5d1a23be8a4d
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65781039"
---
# <a name="how-long-does-azure-ad-store-reporting-data"></a>Jak długo w usłudze Azure AD są przechowywane dane raportowania?

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

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>Termin może wyświetlać danych działania po otrzymaniu licencji premium?

Jeśli masz już danych dotyczących działań związanych z bezpłatnej licencji, a następnie natychmiast go zobaczyć, w czasie uaktualniania. Jeśli nie masz żadnych danych, następnie potrwa jeden lub dwa dni, dane wyświetlane w raportach, po uaktualnieniu do licencji premium.

---

### <a name="can-i-see-last-months-data-after-getting-an-azure-ad-premium-license"></a>Po otrzymaniu licencja premium usługi Azure AD może wyświetlać dane w ciągu ostatniego miesiąca?

Po przełączeniu ostatnio (w tym wersja próbna) w wersji premium, widać danych się do 7 dni początkowo. Gdy dane są gromadzone, dane można wyświetlić w ciągu ostatnich 30 dni.

---

### <a name="when-does-azure-ad-start-collecting-security-signal-data"></a>Gdy usługi Azure AD rozpocząć zbieranie danych sygnału zabezpieczeń?  

Sygnały zabezpieczeń procesu zbierania rozpoczyna się po użytkownik zgody na korzystanie z **Centrum usługi Identity Protection**. 

---

### <a name="how-long-does-azure-ad-store-the-data"></a>Jak długo w usłudze Azure AD są przechowywane dane?

**Raporty dotyczące działań**    

| Zgłoś                 | Usługa Azure AD — warstwa Bezpłatna | Usługa Azure AD — warstwa Podstawowa | Usługa Azure AD — warstwa Premium P1 | Usługa Azure AD — warstwa Premium P2 |
| :--                    | :--           | :--            | :--                 | :--                 |
| Dzienniki inspekcji             | 7 dni        |  7 dni        | 30 dni             | 30 dni             |
| Logowania               | ND           |  ND           | 30 dni             | 30 dni             |
| Użycie platformy Azure MFA        | 30 dni       |  30 dni       | 30 dni             | 30 dni             |

Można zachować dane działanie inspekcji i logowania przez okres dłuższy niż domyślny okres przechowywania opisanych powyżej, kierując do konta usługi Azure storage przy użyciu usługi Azure Monitor. Aby uzyskać więcej informacji, zobacz [archiwum usługi Azure AD loguje się do konta usługi Azure storage](quickstart-azure-monitor-route-logs-to-storage-account.md).

**Sygnały zabezpieczeń**

| Zgłoś         | Usługa Azure AD — warstwa Bezpłatna | Usługa Azure AD — warstwa Podstawowa | Usługa Azure AD — warstwa Premium P1 | Usługa Azure AD — warstwa Premium P2 |
| :--            | :--           | :--            | :--                 | :--                 |
| Narażeni użytkownicy  | 7 dni        | 7 dni         | 30 dni             | 90 dni             |
| Ryzykowne logowania | 7 dni        | 7 dni         |  30 dni            | 90 dni             |

---
