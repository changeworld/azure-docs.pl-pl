---
title: Zarządzanie dostępem użytkowników za pomocą przeglądów dostępu — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak za pomocą przeglądów dostępu w usłudze Azure Active Directory zarządzać dostępem użytkowników — członkostwem w grupie lub przypisaniem do aplikacji
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
ms.openlocfilehash: f8dee430b0ae1bf0c8cd7151272045467ce51c57
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2019
ms.locfileid: "58575066"
---
# <a name="manage-user-access-with-azure-ad-access-reviews"></a>Zarządzanie dostępem użytkowników za pomocą przeglądów dostępu w usłudze Azure AD

Usługa Azure Active Directory (Azure AD) pozwala łatwo zapewnić użytkownikom dostęp na odpowiednim poziomie. Możesz poprosić bezpośrednio użytkowników lub osobę podejmującą decyzje o udział w przeglądzie dostępu i ponowne certyfikowanie (potwierdzenie) dostępu użytkowników. Na podstawie sugestii pochodzących z usługi Azure AD recenzenci mogą wyrazić opinię dotyczącą przedłużenia dostępu poszczególnych użytkowników. Po zakończeniu przeglądu dostępu można wprowadzić zmiany i odebrać dostęp użytkownikom, którzy już go nie potrzebują.

> [!NOTE]
> Jeśli zamiast wszystkich typów użytkowników chcesz przejrzeć tylko dostęp gości, zobacz [Manage guest user access with access reviews (Zarządzanie dostępem gości za pomocą przeglądów dostępu)](manage-guest-access-with-access-reviews.md). Jeśli chcesz przejrzeć członkostwo użytkowników w rolach administracyjnych, takich jak administrator globalny, zobacz [Start an access review in Azure AD Privileged Identity Management (Rozpoczynanie przeglądu dostępu w usłudze Azure AD Privileged Identity Management)](../privileged-identity-management/pim-how-to-start-security-review.md). 
>
>

## <a name="prerequisites"></a>Wymagania wstępne 


Przeglądy dostępu są oferowane wraz z usługą Azure AD w wersji Premium P2, która jest udostępniana z pakietem Microsoft Enterprise Mobility + Security E5. Aby uzyskać więcej informacji, zobacz [Wersje usługi Azure Active Directory](../fundamentals/active-directory-whatis.md). Użytkownicy, którzy chcą korzystać z tej funkcji, w tym tworzyć lub wypełniać przeglądy albo potwierdzać swój dostęp, muszą mieć licencję. 

## <a name="create-and-perform-an-access-review"></a>Tworzenie i przeprowadzanie przeglądu dostępu

W przeglądzie dostępu może uczestniczyć jeden lub większa liczba recenzentów.  

1. Wybierz grupę w usłudze Azure AD, która ma co najmniej jednego członka. Możesz też wybrać aplikację połączoną z usługą Azure AD, do której jest przypisany co najmniej jeden użytkownik. 

2. Zdecyduj, czy poszczególni użytkownicy mają dokonać przeglądu własnego dostępu, czy wybrana grupa użytkowników ma przeprowadzić przegląd dostępu wszystkich użytkowników.

3. Jako administrator globalny lub administrator użytkowników, przejdź do [strony przeglądów dostępu](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

4. Utwórz Przegląd dostępu. Aby uzyskać więcej informacji, zobacz [tworzenie przeglądu dostępu grup lub aplikacji](create-access-review.md).

5. Podczas uruchamiania przeglądu dostępu, poproś recenzentów o wyrażenie opinii danych wejściowych. Domyślnie otrzymają oni wiadomość e-mail z usługi Azure AD z linkiem do panelu dostępu do których one [Przegląd dostępu wszystkich użytkowników do grup ani aplikacji](perform-access-review.md).

6. Jeśli recenzenci nie przekażą opinii, można wysłać im przypomnienia za pośrednictwem usługi Azure AD. Domyślnie usługa Azure AD automatycznie wysyła przypomnienia recenzentom, którzy jeszcze nie odpowiedzieli, po upływie połowy czasu przeznaczonego na przekazanie opinii.

7. Po otrzymaniu opinii recenzentów zakończ przegląd dostępu i zastosuj zmiany. Aby uzyskać więcej informacji, zobacz [Kończenie przeglądu dostępu grup lub aplikacji](complete-access-review.md).


## <a name="next-steps"></a>Kolejne kroki

[Tworzenie przeglądu dostępu grup lub aplikacji](create-access-review.md)




