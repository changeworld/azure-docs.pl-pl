---
title: Zakończenie przeglądu dostępu do elementów członkowskich grupy lub użytkownikom dostępu do aplikacji z usługą Azure AD | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przeprowadzić przegląd dostępu dla członków grupy lub użytkownicy z dostępem do aplikacji w usłudze Azure Active Directory.
services: active-directory
documentationcenter: ''
author: markwahl-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2018
ms.author: billmath
ms.openlocfilehash: 7998d69a079c4858c54bea22dbd24e4e84c8c793
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="complete-an-access-review-of-members-of-a-group-or-users-access-to-an-application-in-azure-ad"></a>Zakończenie przeglądu dostępu do elementów członkowskich grupy lub użytkownikom dostępu do aplikacji w usłudze Azure AD

Administratorzy mogą używać usługi Azure Active Directory (Azure AD) do [utworzyć Przegląd dostępu](active-directory-azure-ad-controls-create-access-review.md) dla członków grupy lub użytkowników przypisanych do aplikacji. Usługi Azure AD automatycznie wysyła wiadomość e-mail zawierająca monit o ich sprawdzaj dostęp osoby dokonujące przeglądu. Jeśli użytkownik nie otrzymasz wiadomość e-mail, możesz wysłać je zgodnie z instrukcjami [Przejrzyj dostęp](active-directory-azure-ad-controls-perform-access-review.md). (Należy pamiętać, że gości, którzy są przypisane jako osób dokonujących przeglądu, ale nie zostały zaakceptowane zaproszenie nie otrzymają wiadomość e-mail z przeglądami dostępu, jak będą musieli najpierw zaakceptować zaproszenie przed recenzowania.) Po zakończeniu okresu przeglądu dostępu lub administrator zatrzymuje Przegląd dostępu, wykonaj kroki opisane w tym artykule, aby zobaczyć i zastosować wyniki.

## <a name="view-an-access-review-in-the-azure-portal"></a>Wyświetl przegląd dostępu w portalu Azure

1. Przejdź do [dostępu monitoruje strony](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/), wybierz pozycję **programy**i wybierz program, który zawiera przegląd kontroli dostępu.

2. Wybierz **Zarządzaj**i wybierz kontroli dostępu przeglądu. Jeśli istnieje wiele formantów w programie, można filtrować dla formantów określonego typu i sortowanie według ich stanu. Również można wyszukiwać według nazwy kontroli dostępu do przeglądu lub wyświetlana nazwa właściciela, który go utworzył. 

## <a name="stop-a-review-that-hasnt-finished"></a>Zatrzymaj przeglądu, która nie została zakończona

Jeśli przeglądu nie osiągnięto planowana data zakończenia, administrator może wybrać **zatrzymać** wczesne zakończenia przeglądu. Po zatrzymaniu przeglądu użytkownicy nie mogą być przeglądane. Nie można ponownie uruchomić przeglądu, po zatrzymaniu.

## <a name="apply-the-changes"></a>Zastosuj zmiany 

Po zakończeniu Przegląd dostępu, albo ponieważ osiągnął Data zakończenia lub ręcznie zatrzymany przez administratora i automatycznie Zastosuj nie został skonfigurowany do przeglądu, można wybrać **Zastosuj** Aby ręcznie zastosować zmiany. Wyniku przeglądu jest implementowany przez aktualizację aplikacji lub grupy. Jeśli w przeglądzie, nastąpiła odmowa dostępu użytkownika, gdy administrator może wybrać tę opcję, usługi Azure AD usuwa przypisanie ich członkostwa lub aplikacji. 

Po zakończeniu Przegląd dostępu i automatycznie Zastosuj został skonfigurowany, stan przeglądu ulegnie zmianie z ukończono za pośrednictwem pośrednich stanów i na koniec zostanie zmieniony na stan zastosowano. Należy się spodziewać odmowy użytkowników, jeśli dowolne usuwana z zasobu grupy aplikacji lub członkostwa przypisania za kilka minut.

Skonfigurowane automatyczne stosowanie przeglądu, lub wybierz **Zastosuj** nie ma wpływu na grupę, która pochodzi z katalogu lokalnego lub grupą dynamiczną. Jeśli chcesz zmienić grupę, która pochodzi z lokalnymi pobierania wyników i zastosować te zmiany do reprezentacji grupy, w tym katalogu.

## <a name="download-the-results-of-the-review"></a>Pobieranie wyników przeglądu

Aby pobrać wyników przeglądu, wybierz **zatwierdzenia** , a następnie wybierz **Pobierz**. Wynikowy plik CSV można wyświetlić w programie Excel lub w inne programy, które otwierają UTF-8 zakodowane plików CSV.

## <a name="optional-delete-a-review"></a>Opcjonalnie: Usuń przeglądu
Jeśli możesz już nie są zainteresowane w przeglądzie, można go usunąć. Wybierz **usunąć** usunąć przeglądu z usługi Azure AD.

> [!IMPORTANT]
> Nie istnieje żadne ostrzeżenie, zanim nastąpi jego usunięcia, dlatego należy się upewnić, że chcesz usunąć przeglądu.
> 
> 

## <a name="next-steps"></a>Kolejne kroki

- [Zarządzanie dostępem użytkowników za pomocą przeglądów dostępu w usłudze Azure AD](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)
- [Zarządzanie dostępem gości za pomocą przeglądów dostępu w usłudze Azure AD](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)
- [Zarządzanie programami i kontrolami obejmującymi przeglądy dostępu w usłudze Azure AD](active-directory-azure-ad-controls-manage-programs-controls.md)
- [Create an access review for members of a group or access to an application (Tworzenie przeglądu dostępu dla członków grupy lub na potrzeby dostępu do aplikacji)](active-directory-azure-ad-controls-create-access-review.md)
- [Tworzenie przeglądu dostępu użytkowników ról administracyjnych usługi Azure AD](active-directory-privileged-identity-management-how-to-start-security-review.md)
