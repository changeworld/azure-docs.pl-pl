---
title: Dodawanie partnerów handlowych integracji B2B — Azure Logic Apps
description: Utwórz partnerów handlowych w ramach konta integracji do użycia z usługą Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 681f16132c1de2ec5f3b27f80633d32879b0746c
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2019
ms.locfileid: "67330255"
---
# <a name="add-trading-partners-to-integration-accounts-for-azure-logic-apps"></a>Dodawanie partnerów handlowych do kont integracji dla usługi Azure Logic Apps

W [usługi Azure Logic Apps](../logic-apps/logic-apps-overview.md), zautomatyzowane przepływy pracy integracji (B2B) business-to-business można utworzyć za pomocą [konta integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) za pomocą aplikacji logiki. Do reprezentowania Twojej organizacji i inne, Utwórz i Dodaj partnerami handlowymi jako artefaktów na koncie integracji. Partnerzy są jednostki, które uczestniczą w transakcji B2B i komunikatów ze sobą.

Przed utworzeniem tych partnerów, pamiętaj omówić i udostępniania informacji o sposobach identyfikacji i weryfikacji wiadomości, które wysyła innych partnerów. Po użytkownik zgadza się na te informacje, możesz przystąpić do tworzenia partnerów na koncie integracji.

## <a name="partner-roles-in-integration-accounts"></a>Role partnera konta integracji

Aby zdefiniować, szczegóły dotyczące komunikatów wymienianych z partnerami, Utwórz i Dodaj [umów](../logic-apps/logic-apps-enterprise-integration-agreements.md) jako artefaktów na koncie integracji. Umowy dotyczące wymagają co najmniej dwie firmy partnerskie na koncie integracji. Twoja organizacja jest zawsze *partner hosta* w umowie. Organizacja, który wymienia komunikaty, w których Twoja organizacja jest *partner gościa*. Partner gościa może być innej firmy lub nawet dział w Twojej organizacji. Po dodaniu tych partnerów, należy utworzyć umowę.

W umowie należy określić szczegóły dotyczące obsługi wiadomości przychodzących i wychodzących z punktu widzenia partner hosta. Dla wiadomości przychodzących **ustawienia odbierania** Określ, jak partner hosta odbiera komunikaty od partnera gościa w umowie. Dla wiadomości wychodzących **ustawienia wysyłania** Określ, jak partner hosta wysyła komunikaty do partnera gościa.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure [Załóż bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).

* [Konta integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) do przechowywania partnerów, umów i innych artefaktów B2B. To konto integracji muszą być skojarzone z subskrypcją platformy Azure.

## <a name="create-partner"></a>Utwórz partnerów

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W głównym menu platformy Azure, wybierz **wszystkich usług**. W polu wyszukiwania wpisz "integration", a następnie wybierz pozycję **kont integracji**.

   ![Wybierz opcję "konta integracji"](./media/logic-apps-enterprise-integration-partners/find-integration-accounts.png)

1. W obszarze **kont integracji**, wybierz konto integracji, w której chcesz dodać partnerów.

   ![Wybierz konto integracji](./media/logic-apps-enterprise-integration-partners/select-integration-account.png)

1. Wybierz **partnerów** kafelka.

   ![Wybierz Kafelek "Partnerskich"](./media/logic-apps-enterprise-integration-partners/choose-partners.png)

1. W obszarze **partnerów**, wybierz **Dodaj**. W obszarze **dodać partnera**, zapewniają szczegółowe informacje o partnerze zgodnie z opisem w poniższej tabeli.

   ![Wybierz pozycję "Dodaj" i podaj szczegółowe informacje o partnerze](./media/logic-apps-enterprise-integration-partners/add-partners.png)

   | Właściwość | Wymagane | Opis |
   |----------|----------|-------------|
   | **Nazwa** | Tak | Nazwa partnera |
   | **Kwalifikator** | Tak | Treść uwierzytelniania, która zawiera tożsamości biznesowych organizacji, na przykład **D-U-N-S (Dun & Bradstreet)** . <p>Partnerzy mogą wybrać korzystanie z tożsamości firm wzajemnie zdefiniowane. W tych scenariuszach wybierz **wzajemnie zdefiniowane** dla EDIFACT lub **wzajemnie zdefiniowane (X12)** dla X12. <p>Dla RosettaNet, wybierz tylko **DUNS**, który jest standardem. |
   | **Wartość** | Yes | Wartość, która identyfikuje dokumenty, które odbierają aplikacji logiki. <p>Dla RosettaNet ta wartość musi być dziewięć cyfrowy numer, który odpowiada numerowi DUNS. |
   ||||

   > [!NOTE]
   > Dla partnerów, które używają RosettaNet, należy określić dodatkowe informacje przez utworzenie tych partnerów i następnie [edytując je później](#edit-partner).

1. Gdy wszystko będzie gotowe, wybierz pozycję **OK**.

   Pojawia się na nowe partnerem **partnerów** listy. Ponadto **partnerów** kafelka aktualizacji bieżący numer partnerów.

   ![Nowy partner](./media/logic-apps-enterprise-integration-partners/new-partner.png)

<a name="edit-partner"></a>

## <a name="edit-partner"></a>Edytuj partnera

1. W [witryny Azure portal](https://portal.azure.com), Znajdź i wybierz swoje konto integracji.
Wybierz **partnerów** kafelka.

   ![Wybierz Kafelek "Partnerskich"](./media/logic-apps-enterprise-integration-partners/edit.png)

1. W obszarze **partnerów**, wybierz partnera, który chcesz edytować, a następnie wybierz **Edytuj**. W obszarze **Edytuj**, wprowadź zmiany.

   ![Wprowadź i Zapisz zmiany](./media/logic-apps-enterprise-integration-partners/edit-partner.png)

   Dla RosettaNet w obszarze **właściwości partnera RosettaNet**, możesz określić to informacje dodatkowe:

   | Właściwość | Wymagane | Opis |
   |----------|----------|-------------|
   | **Klasyfikacja partnera** | Nie | Typ organizacji partnera |
   | **Kod łańcucha dostaw** | Nie | Partnera dostaw łańcuch kodu, na przykład "Informatyka" lub "Komponentów elektronicznych" |
   | **Nazwisko osoby kontaktowej** | Nie | Nazwisko osoby kontaktowej partnera |
   | **Wiadomość e-mail** | Nie | Adres e-mail partnera |
   | **Faksów** | Nie | Numer faksu partnera |
   | **Telefon** | Nie | Numer telefonu partnera |
   ||||

1. Gdy wszystko będzie gotowe, wybierz pozycję **OK** Aby zapisać zmiany.

## <a name="delete-partner"></a>Usuwanie partnera

1. W [witryny Azure portal](https://portal.azure.com), Znajdź i wybierz swoje konto integracji. Wybierz **partnerów** kafelka.

   ![Wybierz Kafelek "Partnerskich"](./media/logic-apps-enterprise-integration-partners/choose-partners-to-delete.png)

1. W obszarze **partnerów**, wybierz partnera, którego chcesz usunąć. Wybierz **Usuń**.

   ![Usuwanie partnera](./media/logic-apps-enterprise-integration-partners/delete-partner.png)

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [umowy](../logic-apps/logic-apps-enterprise-integration-agreements.md)