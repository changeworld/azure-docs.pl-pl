---
title: Dodawanie partnerów handlowych integracji B2B — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Utwórz partnerów handlowych dla konta integracji w usłudze Azure Logic Apps z pakietem integracyjnym dla przedsiębiorstw
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: b179325c-a511-4c1b-9796-f7484b4f6873
ms.date: 07/08/2016
ms.openlocfilehash: 137ed89c276338b534cad8fdf81ec31b5e5610b5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60846075"
---
# <a name="add-trading-partners-for-integration-accounts-in-azure-logic-apps-with-enterprise-integration-pack"></a>Dodaj partnerami handlowymi umożliwiająca konta integracji w usłudze Azure Logic Apps z pakietem integracyjnym dla przedsiębiorstw

Partnerzy są jednostki, które uczestniczą w transakcji (B2B) business-to-business i wymiany komunikatów między sobą. Przed utworzeniem partnerów, które reprezentują razem z innej organizacji w tych transakcji, należy najpierw zarówno udostępnianie informacji, która identyfikuje i weryfikuje komunikatów wysłanych przez siebie nawzajem. Po omówiono te szczegóły i gotowe do rozpoczęcia relacji biznesowych, należy utworzyć partnerów na koncie integracji do reprezentowania obie.

## <a name="what-roles-do-partners-play-in-your-integration-account"></a>Na koncie integracji role, jakie są odtwarzane partnerów?

Aby zdefiniować szczegółowe informacje o wiadomości wymieniane między partnerami, należy utworzyć umów między tych partnerów. Jednakże przed utworzeniem umowę, należy dodać co najmniej dwie firmy partnerskie na koncie integracji. Twoja organizacja musi być częścią umowy jako **partner hosta**. Innego partnera lub **partner gościa** to organizacja, który wymienia wiadomości z Twoją organizacją. Partner gościa może być innej firmy lub nawet dział w Twojej organizacji.

Po dodaniu tych partnerów, należy utworzyć umowę.

Odbieranie i wysyłanie ustawienia zostały opracowane z punktu widzenia hostowanych partnera. Na przykład ustawienia odbierania umowy określić, jak hostowanej partnera odbiera komunikaty wysyłane przez partnera gościa. Podobnie ustawieniach wysyłania umowy wskazują, jak hostowanej partner wysyła komunikaty do partnera gościa.

## <a name="create-partner"></a>Utwórz partnerów

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. W głównym menu platformy Azure, wybierz **wszystkich usług**. W polu wyszukiwania wprowadź "integration", a następnie wybierz pozycję **kont integracji**.

   ![Znajdź konto integracji](./media/logic-apps-enterprise-integration-partners/account-1.png)

3. W obszarze **kont integracji**, wybierz konto integracji, w której chcesz dodać partnerów.

   ![Wybierz konto integracji](./media/logic-apps-enterprise-integration-partners/account-2.png)

4. Wybierz **partnerów** kafelka.

   ![Wybierz pozycję "Partnerskich"](./media/logic-apps-enterprise-integration-partners/partner-1.png)

5. W obszarze **partnerów**, wybierz **Dodaj**.

   ![Wybieranie pozycji "Dodaj"](./media/logic-apps-enterprise-integration-partners/partner-2.png)

6. Wprowadź nazwę dla partnera, a następnie wybierz **kwalifikator**. Wprowadź **wartość** do identyfikowania dokumentów, które odbierają swoje aplikacje. Gdy wszystko będzie gotowe, wybierz pozycję **OK**.

   ![Dodaj szczegóły dotyczące partnera](./media/logic-apps-enterprise-integration-partners/partner-3.png)

7. Wybierz **partnerów** Kafelek ponownie.

   ![Wybierz Kafelek "Partnerskich"](./media/logic-apps-enterprise-integration-partners/partner-5.png)

   Pojawi się nowy partnera. 

   ![Wyświetl nowy partner](./media/logic-apps-enterprise-integration-partners/partner-6.png)

## <a name="edit-partner"></a>Edytuj partnera

1. W [witryny Azure portal](https://portal.azure.com), Znajdź i wybierz swoje konto integracji. Wybierz **partnerów** kafelka.

   ![Wybierz Kafelek "Partnerskich"](./media/logic-apps-enterprise-integration-partners/edit.png)

2. W obszarze **partnerów**, wybierz partnera, który chcesz edytować.

   ![Wybierz partnera do usunięcia](./media/logic-apps-enterprise-integration-partners/edit-1.png)

3. W obszarze **aktualizacji partnera**, wprowadź zmiany.
Po wykonaniu tych czynności wybierz **Zapisz**. 

   ![Wprowadź i Zapisz zmiany](./media/logic-apps-enterprise-integration-partners/edit-2.png)

   Aby anulować zmiany, wybierz pozycję **odrzucić**.

## <a name="delete-partner"></a>Usuwanie partnera

1. W [witryny Azure portal](https://portal.azure.com), Znajdź i wybierz swoje konto integracji. Wybierz **partnerów** kafelka.

   ![Wybierz Kafelek "Partnerskich"](./media/logic-apps-enterprise-integration-partners/delete.png)

2. W obszarze **partnerów**, wybierz partnera, którego chcesz usunąć.
Wybierz **Usuń**.

   ![Usuwanie partnera](./media/logic-apps-enterprise-integration-partners/delete-1.png)

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się więcej o umowach dotyczących](../logic-apps/logic-apps-enterprise-integration-agreements.md "Dowiedz się więcej o umowach dotyczących integracji przedsiębiorstw")  

