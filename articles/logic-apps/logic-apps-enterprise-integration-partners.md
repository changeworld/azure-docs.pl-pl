---
title: Utwórz partnerów wiadomości między firmami (B2B) - Azure Logic Apps | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodać partnerów do swojego konta integracji z pakiet integracyjny dla przedsiębiorstw i Logic Apps
services: logic-apps
documentationcenter: .net,nodejs,java
author: divyaswarnkar
manager: jeconnoc
editor: ''
ms.assetid: b179325c-a511-4c1b-9796-f7484b4f6873
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; padmavc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4abfdaa8145f14bc6496e57ebce0da10cdd2c365
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299743"
---
# <a name="add-or-update-partners-in-business-to-business-agreements-in-your-workflow"></a>Dodaj lub zaktualizuj partnerów w umowach business-to-business w przepływie pracy

Partnerzy to podmiotom uczestniczenie w transakcjach (B2B) business-to-business i wymiany wiadomości między sobą. Przed utworzeniem partnerów, które reprezentują i innej organizacji w tych transakcji, należy najpierw zarówno udostępnianie informacji, która identyfikuje i weryfikuje komunikatów wysłanych przez siebie nawzajem. Po omówiono te szczegóły i zacząć z firmą, można utworzyć partnerów na koncie integracji do reprezentowania obie.

## <a name="what-roles-do-partners-play-in-your-integration-account"></a>Na koncie integracji role są odtwarzane partnerów?

Zdefiniuj szczegóły wiadomości wymieniane między partnerami, utworzysz umów tych partnerów. Jednakże przed utworzeniem umowę, należy dodać co najmniej dwie firmy partnerskie do swojego konta integracji. Twoja organizacja musi być częścią umowy jako **partnera hosta**. Drugiego partnera, lub **partnera gościa** to organizacja, który wymienia wiadomości w swojej organizacji. Partner gościa może być innej firmy lub nawet działów w organizacji.

Po dodaniu tych partnerów, można utworzyć umowy.

Odbierania i wysyłania, ustawienia są obiektowe z punktu widzenia hostowanej partnera. Na przykład odbierania ustawienia w umowie określają sposób hostowanej partnera odbiera wiadomości wysyłane z partnerem gościa. Podobnie, Wyślij ustawienia na umowie wskazują, jak hostowanej partner wysyła komunikaty do partnera gościa.

## <a name="create-partner"></a>Utwórz partnerów

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. W menu głównym Azure wybierz **wszystkie usługi**. W polu wyszukiwania wpisz "integrację", a następnie wybierz **konta integracji**.

   ![Konta integracji](./media/logic-apps-enterprise-integration-partners/account-1.png)

3. W obszarze **konta integracji**, wybierz konto integracji, w której chcesz dodać partnerów.

   ![Wybierz konto integracji](./media/logic-apps-enterprise-integration-partners/account-2.png)

4. Wybierz **partnerów** kafelka.

   ![Wybierz pozycję "Partners"](./media/logic-apps-enterprise-integration-partners/partner-1.png)

5. W obszarze **partnerów**, wybierz **Dodaj**.

   ![Wybierz opcję "Dodaj"](./media/logic-apps-enterprise-integration-partners/partner-2.png)

6. Wprowadź nazwę dla partnera, a następnie wybierz **kwalifikator**. Wprowadź **wartość** do identyfikowania dokumenty otrzymujących aplikacji. Gdy wszystko będzie gotowe, wybierz pozycję **OK**.

   ![Dodaj szczegóły partnera](./media/logic-apps-enterprise-integration-partners/partner-3.png)

7. Wybierz **partnerów** kafelka ponownie.

   ![Wybierz Kafelek "Partners"](./media/logic-apps-enterprise-integration-partners/partner-5.png)

   Pojawi się nowy partnera. 

   ![Nowy serwer partnerski widoku](./media/logic-apps-enterprise-integration-partners/partner-6.png)

## <a name="edit-partner"></a>Edytuj partnera

1. W [portalu Azure](https://portal.azure.com), Znajdź i wybierz konto integracji. Wybierz **partnerów** kafelka.

   ![Wybierz Kafelek "Partners"](./media/logic-apps-enterprise-integration-partners/edit.png)

2. W obszarze **partnerów**, wybierz partnera, którą chcesz edytować.

   ![Wybierz partnera, aby usunąć](./media/logic-apps-enterprise-integration-partners/edit-1.png)

3. W obszarze **partnera aktualizacji**, wprowadź zmiany.
Po zakończeniu wybierz **zapisać**. 

   ![Wprowadź i Zapisz zmiany](./media/logic-apps-enterprise-integration-partners/edit-2.png)

   Aby anulować zmiany, wybierz **odrzucić**.

## <a name="delete-partner"></a>Usunąć partnera

1. W [portalu Azure](https://portal.azure.com), Znajdź i wybierz konto integracji. Wybierz **partnerów** kafelka.

   ![Wybierz Kafelek "Partners"](./media/logic-apps-enterprise-integration-partners/delete.png)

2. W obszarze **partnerów**, wybierz partnera, którego chcesz usunąć.
Wybierz **usunąć**.

   ![Usunąć partnera](./media/logic-apps-enterprise-integration-partners/delete-1.png)

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się więcej na temat umów](../logic-apps/logic-apps-enterprise-integration-agreements.md "więcej informacji na temat umowy integracji dla przedsiębiorstw")  

