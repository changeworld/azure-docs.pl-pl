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
ms.openlocfilehash: 7b4d3baac791012e18e65c9e2e42272410e73fc1
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621661"
---
# <a name="manage-user-access-with-azure-ad-access-reviews"></a>Zarządzanie dostępem użytkowników za pomocą przeglądów dostępu w usłudze Azure AD

Usługa Azure Active Directory (Azure AD) pozwala łatwo zapewnić użytkownikom dostęp na odpowiednim poziomie. Możesz poprosić bezpośrednio użytkowników lub osobę podejmującą decyzje o udział w przeglądzie dostępu i ponowne certyfikowanie (potwierdzenie) dostępu użytkowników. Na podstawie sugestii pochodzących z usługi Azure AD recenzenci mogą wyrazić opinię dotyczącą przedłużenia dostępu poszczególnych użytkowników. Po zakończeniu przeglądu dostępu można wprowadzić zmiany i odebrać dostęp użytkownikom, którzy już go nie potrzebują.

> [!NOTE]
> Jeśli zamiast wszystkich typów użytkowników chcesz przejrzeć tylko dostęp gości, zobacz [Manage guest user access with access reviews (Zarządzanie dostępem gości za pomocą przeglądów dostępu)](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md). Jeśli chcesz przejrzeć członkostwo użytkowników w rolach administracyjnych, takich jak administrator globalny, zobacz [Start an access review in Azure AD Privileged Identity Management (Rozpoczynanie przeglądu dostępu w usłudze Azure AD Privileged Identity Management)](privileged-identity-management/pim-how-to-start-security-review.md). 
>
>

## <a name="prerequisites"></a>Wymagania wstępne 


Przeglądy dostępu są oferowane wraz z usługą Azure AD w wersji Premium P2, która jest udostępniana z pakietem Microsoft Enterprise Mobility + Security E5. Aby uzyskać więcej informacji, zobacz [Wersje usługi Azure Active Directory](active-directory-editions.md). Użytkownicy, którzy chcą korzystać z tej funkcji, w tym tworzyć lub wypełniać przeglądy albo potwierdzać swój dostęp, muszą mieć licencję. 

## <a name="create-and-perform-an-access-review"></a>Tworzenie i przeprowadzanie przeglądu dostępu

W przeglądzie dostępu może uczestniczyć jeden lub większa liczba recenzentów.  

1. Wybierz grupę w usłudze Azure AD, która ma co najmniej jednego członka. Możesz też wybrać aplikację połączoną z usługą Azure AD, do której jest przypisany co najmniej jeden użytkownik. 

2. Zdecyduj, czy poszczególni użytkownicy mają dokonać przeglądu własnego dostępu, czy wybrana grupa użytkowników ma przeprowadzić przegląd dostępu wszystkich użytkowników.

3. Włącz wyświetlanie przeglądów dostępu na panelach dostępu recenzentów. Korzystając z uprawnień administratora globalnego lub administratora kont użytkowników, przejdź do [strony przeglądów dostępu](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

4. Rozpocznij przegląd dostępu. Aby uzyskać więcej informacji, zobacz [Tworzenie przeglądu dostępu](active-directory-azure-ad-controls-create-access-review.md).

5. Poproś recenzentów o wyrażenie opinii. Domyślnie otrzymają oni wiadomość e-mail z usługi Azure AD z linkiem do panelu dostępu, który umożliwia [przeprowadzenie przeglądu dostępu](active-directory-azure-ad-controls-perform-access-review.md).

6. Jeśli recenzenci nie przekażą opinii, można wysłać im przypomnienia za pośrednictwem usługi Azure AD. Domyślnie usługa Azure AD automatycznie wysyła przypomnienia recenzentom, którzy jeszcze nie odpowiedzieli, po upływie połowy czasu przeznaczonego na przekazanie opinii.

7. Po otrzymaniu opinii recenzentów zakończ przegląd dostępu i zastosuj zmiany. Aby uzyskać więcej informacji, zobacz [Kończenie przeglądu dostępu](active-directory-azure-ad-controls-complete-access-review.md).


## <a name="next-steps"></a>Kolejne kroki

[Create an access review for members of a group or access to an application (Tworzenie przeglądu dostępu dla członków grupy lub na potrzeby dostępu do aplikacji)](active-directory-azure-ad-controls-create-access-review.md)




