---
title: Zarządzanie dostępem użytkowników za pomocą przeglądów dostępu w usłudze Azure AD | Microsoft Docs
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
ms.component: compliance
ms.date: 06/21/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.openlocfilehash: 9ada31066b905ca4335acf78ae87534e4c359651
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2019
ms.locfileid: "54331864"
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

3. Korzystając z uprawnień administratora globalnego lub administratora kont użytkowników, przejdź do [strony przeglądów dostępu](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

4. Utwórz Przegląd dostępu. Aby uzyskać więcej informacji, zobacz [Tworzenie przeglądu dostępu](create-access-review.md).

5. Podczas uruchamiania przeglądu dostępu, poproś recenzentów o wyrażenie opinii danych wejściowych. Domyślnie otrzymają oni wiadomość e-mail z usługi Azure AD z linkiem do panelu dostępu, który umożliwia [przeprowadzenie przeglądu dostępu](perform-access-review.md).

6. Jeśli recenzenci nie przekażą opinii, można wysłać im przypomnienia za pośrednictwem usługi Azure AD. Domyślnie usługa Azure AD automatycznie wysyła przypomnienia recenzentom, którzy jeszcze nie odpowiedzieli, po upływie połowy czasu przeznaczonego na przekazanie opinii.

7. Po otrzymaniu opinii recenzentów zakończ przegląd dostępu i zastosuj zmiany. Aby uzyskać więcej informacji, zobacz [Kończenie przeglądu dostępu](complete-access-review.md).


## <a name="next-steps"></a>Kolejne kroki

[Create an access review for members of a group or access to an application (Tworzenie przeglądu dostępu dla członków grupy lub na potrzeby dostępu do aplikacji)](create-access-review.md)




