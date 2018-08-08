---
title: Zarządzanie programami i przeglądów dostępu dla formantów dla usługi Azure AD | Dokumentacja firmy Microsoft
description: Możesz utworzyć dodatkowe programy dla każdego nadzoru, zarządzania ryzykiem i zgodność inicjatywy w Twojej organizacji, aby zbierać i organizować, przeglądy dostępu w usłudze Azure Active Directory jako formantów.
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
ms.component: compliance
ms.date: 06/21/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.openlocfilehash: a1a08ac29605e66dc14ac5e32d21e00dad8cb655
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39622290"
---
# <a name="manage-programs-and-their-controls"></a>Zarządzanie programami i ich kontrolami 

Azure Active Directory (Azure AD) obejmuje przeglądów dostępu członków grupy i dostęp do aplikacji. Te przykłady formantów upewnij się, nadzoru dla użytkowników mających dostęp do aplikacji i członkostwa w grupach w organizacji. Organizacje mogą używać tych kontrolek, aby efektywnie rozwiązać ich nadzoru, zarządzania ryzykiem i wymagań dotyczących zgodności.

## <a name="create-and-manage-programs-and-their-controls"></a>Tworzenie i zarządzanie programami i ich kontrolami
Można uprościć jak śledzenie i zbierać przeglądów dostępu do różnych celów, organizując je w programach. Każdy przeglądu dostępu mogą być połączone z programem. Następnie podczas przygotowania raporty Audytor możesz skupić się na przeglądów dostępu w zakresie określonym inicjatywy.  Programy i wyników przeglądu dostępu są widoczne dla użytkowników w roli administratora globalnego, administratora konta użytkownika, administratora zabezpieczeń lub czytelnika zabezpieczeń.

Aby wyświetlić listę programów, przejdź do [strony przeglądów dostępu](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/) i wybierz **programy**.

**Domyślny Program** zawsze jest obecny. Jeśli jesteś administratorem globalnym lub roli administratora konta użytkownika, możesz utworzyć dodatkowe programy. Na przykład możesz mieć jeden program dla każdego inicjatywy zgodności lub celach biznesowych.

Jeśli nie potrzebujesz już program i nie ma żadnych formantów z nim połączone, możesz go usunąć.

## <a name="next-steps"></a>Kolejne kroki

- [Create an access review for members of a group or access to an application (Tworzenie przeglądu dostępu dla członków grupy lub na potrzeby dostępu do aplikacji)](active-directory-azure-ad-controls-create-access-review.md)
- [Pobieranie wyników przeglądu dostępu](active-directory-azure-ad-controls-retrieve-access-review.md)
