---
title: Kończenie przeglądu dostępu ról usługi Azure AD PIM — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak kończenie przeglądu dostępu ról usługi Azure AD w usłudze Azure AD Privileged Identity Management (PIM) i wyświetlić wyniki
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/06/2017
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a7fa3bfe159620130bc0962b470cea8e7422646
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2019
ms.locfileid: "65602172"
---
# <a name="complete-an-access-review-of-azure-ad-roles-in-pim"></a>Kończenie przeglądu dostępu ról usługi Azure AD w usłudze PIM
Administratorzy ról uprzywilejowanych mogą raz Sprawdź dostęp uprzywilejowany [przeglądu dostępu została uruchomiona](pim-how-to-start-security-review.md). Usługa Azure Active Directory (Azure AD) Privileged Identity Management (PIM) automatycznie wyśle wiadomość e-mail monitowaniu użytkowników, aby zapoznać się z ich dostęp. Jeśli użytkownik nie pobrały wiadomość e-mail, możesz wysłać im instrukcje [jak przeprowadzić przegląd dostępu](pim-how-to-perform-security-review.md).

Po zakończeniu okresu przeglądu dostępu lub wszystkich użytkowników została zakończona w ich własnym Przejrzyj, wykonaj kroki opisane w tym artykule, aby zarządzać przeglądu i wyświetlić wyniki.

## <a name="manage-access-reviews"></a>Zarządzanie przeglądów dostępu
1. Przejdź do [witryny Azure portal](https://portal.azure.com/) i wybierz **usługi Azure AD Privileged Identity Management** aplikacji na pulpicie nawigacyjnym.
2. Wybierz **przeglądów dostępu** części pulpitu nawigacyjnego.
3. Wybierz przeglądu dostępu, które mają być zarządzane.

W bloku szczegółów przeglądu dostępu istnieją opcje liczb do zarządzania przeglądu.

![Przyciski PIM do przeglądu dostępu — zrzut ekranu](./media/pim-how-to-complete-review/review-buttons.png)

### <a name="remind"></a>Przypomnij
Jeśli przeglądu dostępu jest skonfigurowana tak, aby użytkownicy Przejrzyj, **Przypomnij** przycisk wysyła powiadomienie. 

### <a name="stop"></a>Zatrzymaj
Wszystkie przeglądy dostępu mają datę końcową, ale można użyć **zatrzymać** przycisk, aby zakończyć jej początku. Jeśli jeszcze nie zostały sprawdzone wszyscy użytkownicy w tej chwili, będą mogli po zatrzymaniu przeglądu. Nie można ponownie uruchomić przeglądu, po jest zatrzymana.

### <a name="apply"></a>Zastosuj
Po zakończeniu przeglądu dostępu, ponieważ osiągnięto datę zakończenia lub ręcznie, zatrzymanie **Zastosuj** przycisk implementuje wyniku przeglądu. W przeglądzie nastąpiła odmowa dostępu użytkownika, to krok, który spowoduje usunięcie przypisania roli.  

### <a name="export"></a>Eksportowanie
Jeśli użytkownik chce ręcznie zastosowania wyników przeglądu dostępu, możesz wyeksportować przeglądu. **Wyeksportować** przycisk rozpocznie się pobieranie pliku CSV. Wyniki w programie Excel lub inne programy, które otwierają pliki CSV można zarządzać.

### <a name="delete"></a>Usuń
Jeśli nie jesteś zainteresowany przeglądu w bardziej, należy go usunąć. **Usuń** przycisk usuwa przeglądu z poziomu aplikacji PIM.

> [!IMPORTANT]
> Nie zostanie wyświetlone ostrzeżenie, zanim nastąpi usunięcie, więc upewnij się, że chcesz usunąć tego przeglądu. 

## <a name="next-steps"></a>Kolejne kroki

- [Rozpoczynanie przeglądu dostępu dla ról usługi Azure AD w usłudze PIM](pim-how-to-start-security-review.md)
- [Wykonywanie przeglądu dostępu Moje ról usługi Azure AD w usłudze PIM](pim-how-to-perform-security-review.md)
