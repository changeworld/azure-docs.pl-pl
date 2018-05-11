---
title: Zasady przechowywania raportów w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Zasady przechowywania danych raportu w usłudze Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 05/10/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 9101b3877f8a011878baeed0d5c23d29fddaeaad
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="azure-active-directory-report-retention-policies"></a>Zasady przechowywania raportów w usłudze Azure Active Directory


W tym temacie przedstawiono odpowiedzi na często zadawane pytania w połączeniu z przechowywaniem danych dla raportów innej aktywności w usłudze Azure Active Directory. 

### <a name="q-how-can-you-get-the-collection-of-activity-data-started"></a>Pytanie: jak możesz uzyskać zbierania danych działania uruchomiona?

**ODP.:**

| Wersja usługi Azure AD | Początkowy kolekcji |
| :--              | :--   |
| Usługa Azure AD — warstwa Premium P1 <br /> Usługa Azure AD — warstwa Premium P2 | Jeśli możesz przystąpić do subskrypcji |
| Usługa Azure AD — warstwa Bezpłatna | Przy pierwszym otwarciu [bloku usługi Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) lub użyj [raportowania interfejsów API](https://aka.ms/aadreports)  |

---
### <a name="q-when-is-your-activity-data-available-in-the-azure-portal"></a>Pytanie: kiedy dane działanie jest dostępne w portalu Azure

**ODP.:**

- **Natychmiast** — Jeśli już pracy z raportami w portalu Azure
- **W ciągu 2 godzin** — Jeśli nie włączono raportowanie w portalu Azure

---

### <a name="q-how-can-you-get-the-collection-of-security-signals-started"></a>Pytanie: jak uzyskać kolekcja sygnałów zabezpieczeń uruchomić?  

**Odpowiedź:** dla sygnałów zabezpieczeń procesu zbierania rozpoczyna się, gdy należy wyrazić zgodę na Użyj Centrum ochrony tożsamości. 


---

### <a name="q-for-how-long-is-the-collected-data-stored"></a>Pytanie: na jak długo są zebrane dane przechowywane?

**ODP.:**

**Raporty dotyczące działań**    

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
