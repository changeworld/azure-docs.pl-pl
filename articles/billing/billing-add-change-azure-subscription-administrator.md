---
title: Dodawanie lub zmienianie ról subskrypcji Azure administratora | Dokumentacja firmy Microsoft
description: Opisuje, jak dodać lub zmienić administratora współpracującego Azure, Administrator usługi i konto administratora
services: ''
documentationcenter: ''
author: genlin
manager: jlian
editor: ''
tags: billing
ms.assetid: 13a72d76-e043-4212-bcac-a35f4a27ee26
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 09c2badf6116dd36add6cccc82486d7f5b8f8697
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="add-or-change-azure-subscription-administrators"></a>Dodawanie lub zmienianie administratorów subskrypcji platformy Azure

Administratorzy subskrypcji platformy Azure classic i Azure [kontroli dostępu opartej na rolach (RBAC)](../role-based-access-control/overview.md) są dwa systemy do zarządzania dostępem do zasobów platformy Azure:

* Role administratora subskrypcji klasycznego oferować zarządzania dostępem do warstwy podstawowa i to konto administratora, administratora usługi i Współadministratorzy.
    * Gdy utworzysz nową subskrypcję platformy Azure, Twoje konto jest domyślnie jako konto administratora i administratora usługi.
    * Współadministratorzy mogą być dodawane po logowaniu.
* RBAC jest nowszego systemu, która umożliwia precyzyjne zarządzanie dostępem z wielu wbudowanych ról, elastyczność w zakresie i role niestandardowe.
    * Jednak użytkownikom tylko role RBAC i ról administratora nie klasycznego subskrypcji nie może zarządzać Azure w przypadku wdrożeń klasycznych.

Aby zapewnić lepszą kontrolę i uprościć zarządzanie dostępem, zaleca się użycie funkcji RBAC dla wszystkich potrzeb dotyczących zarządzania dostępu. Jeśli to możliwe zaleca się ponownie skonfigurować przy użyciu funkcji RBAC istniejących zasad dostępu. 

<a name="add-an-admin-for-a-subscription"></a>

## <a name="add-an-rbac-owner-admin-for-a-subscription-in-azure-portal"></a>Dodawanie administratora właściciela RBAC dla subskrypcji w portalu Azure 

Aby dodać osobę administratora dla subskrypcji platformy Azure usługi administracji, nadanie im rolę właściciela RBAC do subskrypcji. Rola właściciela można zarządzać zasobów w subskrypcji przypisany i nie ma uprawnień dostępu do innych subskrypcji.

1. Odwiedź stronę [ **subskrypcje** w portalu Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
2. Wybierz subskrypcję, którą chcesz przyznać dostęp.
3. Wybierz **Dodaj**  
   (Jeśli brakuje przycisk Dodaj, nie masz uprawnień do dodawania uprawnień.)
4. Wybierz **(IAM) kontroli dostępu** w menu.
5. W **roli** wybierz opcję **właściciela**. 
6. W **przypisany dostęp** wybierz opcję **użytkownika usługi Azure AD, grupy lub aplikacji**. 
7. W **wybierz** wpisz adres e-mail użytkownika, które chcesz dodać jako właściciela. Wybierz użytkownika, a następnie wybierz **zapisać**.

    ![Zrzut ekranu pokazujący rolę właściciela wybrane](./media/billing-add-change-azure-subscription-administrator/add-role.png)

Dzięki temu użytkownikowi pełny dostęp do wszystkich zasobów łącznie z prawem delegować dostęp do innych użytkowników. Aby udzielić dostępu w zakresie różnych, takich jak grupy zasobów, odwiedź stronę menu IAM dla tego zakresu. 

## <a name="add-or-change-co-administrator"></a>Dodawanie lub zmienianie współadministratorem

Jako współadministrator można dodawać tylko jego właściciel. Inni użytkownicy z ról, takich jak współautora i czytnika nie można dodać jako współadministratorów.

> [!TIP]
> Musisz dodać konto "Właściciela" jako współadministrator, jeśli użytkownik chce zarządzać Azure w przypadku wdrożeń klasycznych. Zaleca się przy użyciu funkcji RBAC dla innych celów.

1. Jeśli nie jest jeszcze dodać osobę jako właściciela, postępując zgodnie z instrukcjami z powyżej.
2. **Kliknij prawym przyciskiem myszy** użytkownika właściciela zostanie dodany, a następnie wybierz **Dodaj jako współadministrator**. Jeśli nie widzisz **Dodaj jako współadministrator** opcji, Odśwież stronę lub spróbuj innej przeglądarki internetowej. 

    ![Zrzut ekranu, który dodaje współadministratorem](./media/billing-add-change-azure-subscription-administrator/add-coadmin.png)

    Aby usunąć uprawnienia administratora współpracującego **kliknij prawym przyciskiem myszy** użytkownika "Współadministratorem", a następnie wybierz **usunąć administratora współpracującego**.

    ![Zrzut ekranu, która usuwa współadministratorem](./media/billing-add-change-azure-subscription-administrator/remove-coadmin.png)

<a name="change-service-administrator-for-a-subscription"></a>

## <a name="change-the-service-administrator-for-an-azure-subscription"></a>Zmiany administratora usługi dla subskrypcji platformy Azure

Tylko Administrator konta może zmienić administratora usługi dla subskrypcji. Domyślnie po utworzeniu konta, administratora usługi jest taki sam jak konto administratora. Jeśli Administrator usługi zostanie zmieniona dla innego użytkownika, administratora konta traci dostęp do portalu Azure. Jednak Administrator konta może zawsze używać Centrum konta zmienić administratora usługi do siebie.

1. Upewnij się, że dany scenariusz jest obsługiwany przez sprawdzanie [limity dla administratorów usługi zmiana](#limits).
1. Zaloguj się do [Centrum konta](https://account.windowsazure.com/subscriptions) jako administratora konta.
1. Wybierz subskrypcję.
1. Po prawej stronie wybierz **Edytuj szczegóły subskrypcji**.

    ![Zrzut ekranu przedstawiający przycisk Edytuj subskrypcji w Centrum konta](./media/billing-add-change-azure-subscription-administrator/editsub.png)
1. W **administratora usługi** wprowadź adres e-mail nowego administratora usługi.

    ![Zrzut ekranu przedstawiający okno Zmień adres e-mail administratora usługi](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

<a name="limits"></a>

### <a name="limitations-for-changing-service-administrators"></a>Ograniczenia dotyczące zmiana Administratorzy usługi

* Każda subskrypcja jest skojarzona z katalogu usługi Azure AD. Aby znaleźć subskrypcja jest skojarzona z katalogu, przejdź do [ **subskrypcje**](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), następnie wybierz subskrypcję, aby wyświetlić katalog.
* Jeśli logujesz się przy użyciu konta służbowego, można dodać inne konta w organizacji jako administratora usługi. Na przykład abby@contoso.com można dodać bob@contoso.com jako administratora usługi, ale nie można dodać john@notcontoso.com chyba że john@notcontoso.com ma obecności w katalogu contoso.com. Użytkownicy zalogowany za pomocą konta służbowego można kontynuować dodawanie użytkowników Account Microsoft jako administratora usługi.

  | Metoda logowania | Dodaj Account Microsoft użytkownika jako administratora systemu? | Dodaj konto służbowe w tej samej organizacji jako SA? | Dodaj konto służbowe w innej organizacji jako SA? |
  | --- | --- | --- | --- |
  |  Konto Microsoft |Yes |Nie |Nie |
  |  Konto służbowe |Yes |Yes |Nie |

## <a name="change-the-account-administrator-for-an-azure-subscription"></a>Zmienić administratora konta subskrypcji platformy Azure

Administrator konta jest użytkownik, który początkowo konta subskrypcji platformy Azure i odpowiada jako rozliczeń właściciela subskrypcji. Aby zmienić administratora konta subskrypcji, zobacz [przeniesienia własności subskrypcji platformy Azure na inne konto](billing-subscription-transfer.md).

<a name="check-the-account-administrator-of-the-subscription"></a>

**Nie masz pewności, który jest administratorem konta?** Wykonaj następujące kroki:

1. Odwiedź stronę [ **subskrypcje** w portalu Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Wybierz subskrypcję, którą chcesz sprawdzić, a następnie sprawdź w obszarze **ustawienia**.
1. Wybierz **właściwości**. Administratora konta subskrypcji jest wyświetlany w **administrator konta** pole.  

## <a name="types-of-classic-subscription-admins"></a>Typy klasyczni Administratorzy

 Konto administratora, Administrator usługi i współadministrator są trzy rodzaje ról administratorów klasycznego subskrypcji platformy Azure. Konto, które jest używane podczas tworzenia konta platformy Azure jest automatycznie ustawiana jako konto administratora i administratora usługi. Następnie można dodać dodatkowe Współadministratorów. W poniższej tabeli opisano dokładnie różnice między tych trzech ról administracyjnych. 

> [!TIP]
> Lepszą kontrolę i precyzyjne zarządzanie dostępem firma Microsoft zaleca używanie opartej na rolach Azure kontroli dostępu (RBAC), dzięki czemu użytkownicy mają zostać dodane do wielu ról. Aby dowiedzieć się więcej, zobacz [kontroli dostępu opartej na roli Azure Active Directory](../role-based-access-control/overview.md).

| Klasyczny administrator subskrypcji | Limit | Opis |
| --- | --- | --- |
| Konto administratora (AA) |1 na konto platformy Azure |To jest użytkownik, który konta subskrypcji platformy Azure i uprawnień dostępu [Centrum konta](https://account.azure.com/Subscriptions) i wykonywać różne zadania zarządzania. Obejmują one, można tworzyć nowe subskrypcje, anulowanie subskrypcji, zmienić rozliczeń dla subskrypcji oraz zmienić administratora usługi. Administrator konta jest koncepcyjnie, rozliczeń właściciela subskrypcji. W RBAC konto administratora nie ma przypisaną rolę.|
| Administrator usługi (SA) |1 na subskrypcji platformy Azure |Ta rola jest autoryzowany do zarządzania usługami w [portalu Azure](https://portal.azure.com). Domyślnie na nową subskrypcję konto administratora jest również administratorem usługi. W RBAC rolę właściciela znajduje się w zakresie subskrypcji do administratora usługi.|
| Współadministrator (CA) |200 na subskrypcję |Ta rola ma takie same uprawnienia dostępu jak administrator usługi, ale nie może zmieniać skojarzenia subskrypcji do katalogów platformy Azure. W RBAC rolę właściciela otrzymuje Współadministratorem w zakresie subskrypcji.|

## <a name="learn-more-about-resource-access-control-and-active-directory"></a>Dowiedz się więcej na temat kontroli dostępu do zasobów i usługi Active Directory

* Aby dowiedzieć się więcej na temat sposobu jest kontrolowany dostęp do zasobów w systemie Microsoft Azure, zobacz [opis dostęp do zasobów na platformie Azure](../role-based-access-control/rbac-and-directory-admin-roles.md).
* Aby uzyskać więcej informacji o usłudze Azure Active Directory, zobacz [jak subskrypcje platformy Azure są kojarzone z usługi Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md) i [przypisywanie ról administratorów w usłudze Azure Active Directory](../active-directory/active-directory-assign-admin-roles-azure-portal.md).

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną.

Jeśli nadal potrzebujesz pomocy, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) uzyskać szybkie rozwiązanie problemu.
