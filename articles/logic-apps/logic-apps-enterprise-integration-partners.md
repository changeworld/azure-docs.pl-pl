---
title: Dodaj partnerów handlowych do integracji B2B
description: Tworzenie partnerów handlowych na koncie integracji do użycia z usługą Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: e58cbe85f30ea09adde45d55bb7b80c710c45495
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792439"
---
# <a name="add-trading-partners-to-integration-accounts-for-azure-logic-apps"></a>Dodawanie partnerów handlowych do kont integracji usługi Azure Logic Apps

W [usłudze Azure Logic Apps](../logic-apps/logic-apps-overview.md)można tworzyć zautomatyzowane przepływy pracy integracji między firmami (B2B) przy użyciu konta [integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) z aplikacjami logiki. Aby reprezentować swoją organizację i inne osoby, należy utworzyć i dodać partnerów handlowych jako artefakty do konta integracji. Partnerzy to jednostki, które uczestniczą w transakcjach B2B i wymieniają się ze sobą wiadomościami.

Przed utworzeniem tych partnerów należy omówić i udostępnić partnerom informacje dotyczące sposobu identyfikowania i sprawdzania poprawności wiadomości wysyłane przez innych. Po uzgodnieniu tych danych możesz utworzyć partnerów na swoim koncie integracyjnym.

## <a name="partner-roles-in-integration-accounts"></a>Role partnerów na kontach integracyjnych

Aby zdefiniować szczegóły dotyczące wiadomości wymienianych z partnerami, należy utworzyć i dodać [umowy](../logic-apps/logic-apps-enterprise-integration-agreements.md) jako artefakty do konta integracji. Umowy wymagają co najmniej dwóch partnerów na koncie integracji. Twoja organizacja jest zawsze *partnerem hostingowym* w umowie. Organizacją, która wymienia wiadomości z organizacją, jest *partnerem-gościem.* Partnerem gościa może być inna firma, a nawet dział we własnej organizacji. Po dodaniu tych partnerów można utworzyć umowę.

W umowie można określić szczegóły obsługi wiadomości przychodzących i wychodzących z perspektywy partnera hosta. W przypadku wiadomości przychodzących **ustawienia odbierania** określają sposób, w jaki partner hosta odbiera wiadomości od partnera gościa w umowie. W przypadku wiadomości wychodzących **ustawienia wysyłania** określają sposób wysyłania wiadomości do partnera gościa.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, [zarejestruj się, aby uzyskać bezpłatne konto platformy Azure.](https://azure.microsoft.com/free/)

* [Konto integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) do przechowywania partnerów, umów i innych artefaktów B2B. To konto integracji musi być skojarzone z subskrypcją platformy Azure.

## <a name="create-partner"></a>Tworzenie partnera

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).

1. W głównym menu platformy Azure wybierz pozycję **Wszystkie usługi**. W polu wyszukiwania wprowadź "integracja" i wybierz **pozycję Konta integracji**.

   ![Wybierz "Konta integracyjne"](./media/logic-apps-enterprise-integration-partners/find-integration-accounts.png)

1. W obszarze **Konta integracji**wybierz konto integracji, na którym chcesz dodać partnerów.

   ![Wybierz konto integracji](./media/logic-apps-enterprise-integration-partners/select-integration-account.png)

1. Wybierz kafelek **Partnerzy.**

   ![Wybierz kafelek "Partnerzy"](./media/logic-apps-enterprise-integration-partners/choose-partners.png)

1. W obszarze **Partnerzy**wybierz pozycję **Dodaj**. W obszarze **Dodaj partnera**podaj dane partnera w sposób opisany w poniższej tabeli.

   ![Wybierz "Dodaj" i podaj szczegóły partnera](./media/logic-apps-enterprise-integration-partners/add-partners.png)

   | Właściwość | Wymagany | Opis |
   |----------|----------|-------------|
   | **Nazwa** | Tak | Imię i nazwisko partnera |
   | **Kwalifikator** | Tak | Organ uwierzytelniający, który zapewnia unikalne tożsamości biznesowe organizacjom, na przykład **D-U-N-S (Dun & Bradstreet).** <p>Partnerzy mogą zdecydować się na wzajemnie zdefiniowaną tożsamość biznesową. W przypadku tych scenariuszy wybierz **opcję Wzajemnie zdefiniowane** dla EDIFACT lub **Wzajemnie zdefiniowane (X12)** dla X12. <p>W przypadku RosettaNet wybierz tylko **DUNS**, który jest standardem. |
   | **Wartość** | Tak | Wartość identyfikująca dokumenty, które otrzymują aplikacje logiki. <p>W przypadku RosettaNet wartość ta musi być dziewięciocyfrową liczbą odpowiadającą numerowi DUNS. |
   ||||

   > [!NOTE]
   > W przypadku partnerów korzystających z RosettaNet można określić dodatkowe informacje, najpierw tworząc tych partnerów, a następnie [edytując je później.](#edit-partner)

1. Gdy wszystko będzie gotowe, wybierz pozycję **OK**.

   Twój nowy partner pojawi się teraz na liście **Partnerzy.** Ponadto kafelek **Partnerzy** aktualizuje bieżącą liczbę partnerów.

   ![Nowy partner](./media/logic-apps-enterprise-integration-partners/new-partner.png)

<a name="edit-partner"></a>

## <a name="edit-partner"></a>Edytuj partnera

1. W [witrynie Azure portal](https://portal.azure.com)znajdź i wybierz swoje konto integracji.
Wybierz kafelek **Partnerzy.**

   ![Wybierz kafelek "Partnerzy"](./media/logic-apps-enterprise-integration-partners/edit.png)

1. W obszarze **Partnerzy**wybierz partnera, którego chcesz edytować, a następnie wybierz pozycję **Edytuj**. W obszarze **Edytuj**wykonuj zmiany.

   ![Wprowadzanie i zapisywanie zmian](./media/logic-apps-enterprise-integration-partners/edit-partner.png)

   W przypadku RosettaNet w obszarze **Właściwości partnera RosettaNet**można określić te dodatkowe informacje:

   | Właściwość | Wymagany | Opis |
   |----------|----------|-------------|
   | **Klasyfikacja partnerów** | Nie | Typ organizacji partnera |
   | **Kod łańcucha dostaw** | Nie | Kod łańcucha dostaw partnera, na przykład "Technologia informacyjna" lub "Komponenty elektroniczne" |
   | **Nazwisko osoby kontaktowej** | Nie | Nazwa kontaktu partnera |
   | **Adres e-mail** | Nie | Adres e-mail partnera |
   | **Faks** | Nie | Numer faksu partnera |
   | **Telephone** | Nie | Numer telefonu partnera |
   ||||

1. Po zakończeniu wybierz przycisk **OK,** aby zapisać zmiany.

## <a name="delete-partner"></a>Usuń partnera

1. W [witrynie Azure portal](https://portal.azure.com)znajdź i wybierz swoje konto integracji. Wybierz kafelek **Partnerzy.**

   ![Wybierz kafelek "Partnerzy"](./media/logic-apps-enterprise-integration-partners/choose-partners-to-delete.png)

1. W obszarze **Partnerzy**wybierz partnera, którego chcesz usunąć. Wybierz przycisk **Usuń**.

   ![Usuń partnera](./media/logic-apps-enterprise-integration-partners/delete-partner.png)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [umowach](../logic-apps/logic-apps-enterprise-integration-agreements.md)