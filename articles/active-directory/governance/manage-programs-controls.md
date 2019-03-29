---
title: Zarządzanie programami i kontrolami dla przeglądów dostępu — w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć dodatkowe programy dla każdego nadzoru, zarządzania ryzykiem i zgodność inicjatywy w Twojej organizacji, aby zbierać i organizować, przeglądy dostępu w usłudze Azure Active Directory jako formantów.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 06/21/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43c0f1c041bfed1b041a9926efd869d167c6f1e9
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2019
ms.locfileid: "58577267"
---
# <a name="manage-programs-and-controls-for-azure-ad-access-reviews"></a>Zarządzanie programami i przeglądów dostępu dla formantów dla usługi Azure AD

Azure Active Directory (Azure AD) obejmuje przeglądów dostępu członków grupy i dostęp do aplikacji. Te przykłady formantów upewnij się, nadzoru dla użytkowników mających dostęp do aplikacji i członkostwa w grupach w organizacji. Organizacje mogą używać tych kontrolek, aby efektywnie rozwiązać ich nadzoru, zarządzania ryzykiem i wymagań dotyczących zgodności.

## <a name="create-and-manage-programs-and-their-controls"></a>Tworzenie i zarządzanie programami i ich kontrolami
Można uprościć jak śledzenie i zbierać przeglądów dostępu do różnych celów, organizując je w programach. Każdy przeglądu dostępu mogą być połączone z programem. Następnie podczas przygotowania raporty Audytor możesz skupić się na przeglądów dostępu w zakresie określonym inicjatywy.  Programy i wyników przeglądu dostępu są widoczne dla użytkowników w administrator globalny, administrator użytkownika, administratora zabezpieczeń lub roli czytelnika zabezpieczeń.

Aby wyświetlić listę programów, przejdź do [strony przeglądów dostępu](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/) i wybierz **programy**.

**Domyślny Program** zawsze jest obecny. Jeśli jesteś administratorem globalnym lub rola administratora, możesz utworzyć dodatkowe programy. Na przykład możesz mieć jeden program dla każdego inicjatywy zgodności lub celach biznesowych.

Jeśli nie potrzebujesz już program i nie ma żadnych formantów z nim połączone, możesz go usunąć.

## <a name="next-steps"></a>Kolejne kroki

- [Tworzenie przeglądu dostępu grup lub aplikacji](create-access-review.md)
- [Pobieranie wyników przeglądu dostępu dla grup ani aplikacji](retrieve-access-review.md)
