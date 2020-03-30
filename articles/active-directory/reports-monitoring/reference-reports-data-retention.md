---
title: Jak długo usługa Azure AD przechowuje dane raportowania? | Microsoft Docs
description: Dowiedz się, jak długo platforma Azure przechowuje różne typy danych raportowania.
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
ms.date: 03/24/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54636600c208f8f5df9fa2e25460c63dd9f46e85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239544"
---
# <a name="how-long-does-azure-ad-store-reporting-data"></a>Jak długo usługa Azure AD przechowuje dane raportowania?


W tym artykule dowiesz się więcej o zasadach przechowywania danych dla różnych raportów aktywności w usłudze Azure Active Directory. 

### <a name="when-does-azure-ad-start-collecting-data"></a>Kiedy usługa Azure AD rozpoczyna zbieranie danych?

| Azure AD Edition | Początek odbioru |
| :--              | :--   |
| Usługa Azure AD — warstwa Premium P1 <br /> Usługa Azure AD — warstwa Premium P2 | Kiedy rejestrujesz się w subskrypcji |
| Usługa Azure AD — warstwa Bezpłatna| Przy pierwszym otwarciu [bloku usługi Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) lub użyciu [interfejsów API raportowania](https://aka.ms/aadreports)  |

---

### <a name="when-is-the-activity-data-available-in-the-azure-portal"></a>Kiedy dane aktywności są dostępne w witrynie Azure portal?

- **Natychmiast** — jeśli już pracujesz z raportami w witrynie Azure portal.
- **W ciągu 2 godzin** — jeśli raportowanie nie jest włączone w witrynie Azure portal.

---

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>Jak szybko mogę zobaczyć dane aktywności po uzyskaniu licencji premium?

Jeśli masz już dane dotyczące działań z bezpłatną licencją, możesz je zobaczyć natychmiast po uaktualnieniu. Jeśli nie masz żadnych danych, po uaktualnieniu do licencji premium dane będą wyświetlane w raportach przez jeden lub dwa dni.

---

### <a name="can-i-see-last-months-data-after-getting-an-azure-ad-premium-license"></a>Czy mogę zobaczyć dane z ostatniego miesiąca po uzyskaniu licencji premium usługi Azure AD?

Jeśli niedawno przełączyłeś się na wersję premium (w tym wersję próbną), początkowo możesz zobaczyć dane do 7 dni. Gdy dane się gromadzą, można wyświetlić dane z ostatnich 30 dni.

---

### <a name="when-does-azure-ad-start-collecting-security-signal-data"></a>Kiedy usługa Azure AD rozpoczyna zbieranie danych sygnału zabezpieczeń?  

W przypadku sygnałów bezpieczeństwa proces zbierania rozpoczyna się po włączeniu do korzystania z **Centrum ochrony tożsamości**. 

---

### <a name="how-long-does-azure-ad-store-the-data"></a>Jak długo usługa Azure AD przechowuje dane?

**Raporty dotyczące działań**    

| Raport                 | Usługa Azure AD — warstwa Bezpłatna | Usługa Azure AD — warstwa Premium P1 | Usługa Azure AD — warstwa Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| Dzienniki inspekcji             | 7 dni        | 30 dni             | 30 dni             |
| Logowania               | 7 dni        | 30 dni             | 30 dni             |
| Użycie usługi Azure MFA        | 30 dni       | 30 dni             | 30 dni             |

Dane działania inspekcji i logowania można zachować dłużej niż domyślny okres przechowywania opisany powyżej, kierując je do konta magazynu platformy Azure przy użyciu usługi Azure Monitor. Aby uzyskać więcej informacji, zobacz [Archiwizowanie dzienników usługi Azure AD na koncie magazynu platformy Azure.](quickstart-azure-monitor-route-logs-to-storage-account.md)

**Sygnały bezpieczeństwa**

| Raport         | Usługa Azure AD — warstwa Bezpłatna | Usługa Azure AD — warstwa Premium P1 | Usługa Azure AD — warstwa Premium P2 |
| :--            | :--           | :--                 | :--                 |
| Narażeni użytkownicy  | 7 dni        | 30 dni             | 90 dni             |
| Ryzykowne logowania | 7 dni        | 30 dni             | 90 dni             |

---
