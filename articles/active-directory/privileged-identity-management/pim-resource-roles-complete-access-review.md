---
title: Kończenie przeglądu dostępu w przypadku zasobów platformy Azure przy użyciu Privileged Identity Management | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób wykonania przeglądu dostępu do zasobów platformy Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: protection
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: e21d0240469a9c775e610c97f98c073b8f83ce8e
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37442202"
---
# <a name="complete-an-access-review-for-azure-resources-by-using-privileged-identity-management"></a>Kończenie przeglądu dostępu w przypadku zasobów platformy Azure przy użyciu Privileged Identity Management
Administratorzy ról uprzywilejowanych, można przejrzeć uprzywilejowanego dostępu po [przeglądu zabezpieczeń została uruchomiona](pim-resource-roles-start-access-review.md). Privileged Identity Management (PIM) dla zasobów platformy Azure automatycznie wysyła wiadomość e-mail, który monituje użytkowników, aby zapoznać się z ich dostęp. Jeśli użytkownik nie otrzymają wiadomość e-mail, możesz wysłać im instrukcje [jak przeprowadzić przegląd zabezpieczeń](pim-resource-roles-perform-access-review.md).

Po zakończeniu okresu przeglądu zabezpieczeń lub po wszystkich użytkowników została zakończona w ich własnym Przejrzyj, wykonaj kroki opisane w tym artykule, aby zarządzać przeglądu i wyświetlić wyniki.

## <a name="manage-security-reviews"></a>Zarządzanie przeglądów zabezpieczeń
1. Przejdź do witryny [Azure Portal](https://portal.azure.com/). Na pulpicie nawigacyjnym wybierz **zasobów platformy Azure** aplikacji.

2. Wybierz zasób.

3. Wybierz **przeglądów dostępu** części pulpitu nawigacyjnego.
![Przeglądy dostępu](media/azure-pim-resource-rbac/rbac-access-review-home-list.png)

4. Wybierz przeglądu dostępu, które mają być zarządzane.

W bloku szczegółów przeglądu dostępu istnieje kilka opcji związanych z zarządzaniem przeglądu. Dostępne są następujące opcje:

![Opcje zarządzania przeglądu](media/azure-pim-resource-rbac/rbac-access-review-menu.png)

### <a name="stop"></a>Stop
Wszystkie przeglądy dostępu mają datę końcową, ale można użyć **zatrzymać** przycisk, aby zakończyć jej początku. Wszyscy użytkownicy, którzy nie została jeszcze swojego przeglądu po tym czasie nie będzie mógł zakończyć ją po zakończeniu przeglądu. Nie można ponownie uruchomić przeglądu, po jest zatrzymana.

### <a name="reset"></a>Reset
Możesz zresetować przeglądu dostępu, aby usunąć wszystkie decyzje, które składają się na nim. Po zresetowaniu Przegląd dostępu wszystkich użytkowników, są oznaczone jako wykonany ponownie. 

### <a name="apply"></a>Zastosuj
Po zakończeniu przeglądu dostępu za pomocą **Zastosuj** przycisku, zaimplementować wyniku przeglądu. Jeśli w przeglądzie nastąpiła odmowa dostępu użytkownika, ten krok powoduje usunięcie przypisania roli.  

### <a name="delete"></a>Usuwanie
Jeśli nie chcesz w przeglądzie więcej, należy go usunąć. **Usuń** przycisk usuwa przeglądu z poziomu aplikacji PIM.

## <a name="results"></a>Wyniki
Na **wyniki** kartę, przeglądanie i pobieranie listy wyników przeglądu. 
![Karta wyników](media/azure-pim-resource-rbac/rbac-access-review-results.png)

## <a name="reviewers"></a>Recenzenci
Wyświetlanie i dodawanie recenzentów do Twojej istniejący Przegląd dostępu. Przypomnij osób dokonujących przeglądu, aby ukończyć przeglądów ich.
![Dodaj recenzentów](media/azure-pim-resource-rbac/rbac-access-review-reviewers.png)



