---
title: Błąd nieznalezienia subskrypcji — logowanie się do witryny Azure Portal
description: Rozwiązanie problemu polegającego na występowaniu błędu Nie znaleziono subskrypcji podczas logowania się do witryny Azure Portal lub do Centrum konta platformy Azure.
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 76f109645e4a27e712066cec7010f2ac7eb6b507
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411657"
---
# <a name="no-subscriptions-found-sign-in-error-for-azure-portal-or-azure-account-center"></a>Błąd logowania „Nie znaleziono subskrypcji” w witrynie Azure Portal lub w Centrum konta platformy Azure

Podczas próby zalogowania się do witryny [Azure Portal](https://portal.azure.com/) lub [Centrum konta platformy Azure](https://account.windowsazure.com/Subscriptions) może być wyświetlany komunikat o błędzie „Nie znaleziono subskrypcji”. Ten artykuł zawiera rozwiązanie tego problemu.

## <a name="symptom"></a>Objaw

Podczas próby zalogowania się do witryny [Azure Portal](https://portal.azure.com/) lub [Centrum konta platformy Azure](https://account.windowsazure.com/Subscriptions) jest wyświetlany następujący komunikat o błędzie: „Nie znaleziono subskrypcji”.

## <a name="cause"></a>Przyczyna

Ten problem występuje, jeśli wybrano niewłaściwy katalog lub jeśli konto nie ma wystarczających uprawnień.

## <a name="solution"></a>Rozwiązanie

### <a name="scenario-1-error-message-is-received-in-the-azure-portal"></a>Scenariusz 1. Komunikat o błędzie jest wyświetlany w witrynie [Azure Portal](https://portal.azure.com)

Aby rozwiązać ten problem:

* Upewnij się, że wybrano prawidłowy katalog platformy Azure, klikając swoje konto w prawym górnym rogu.

  ![Wybieranie katalogu w prawym górnym rogu witryny Azure Portal](./media/no-subscriptions-found/directory-switch.png)
* Jeśli wybrano właściwy katalog platformy Azure, ale nadal pojawia się komunikat o błędzie, [przypisz do swojego konta rolę Właściciel](../../role-based-access-control/role-assignments-portal.md).

### <a name="scenario-2-error-message-is-received-in-the-azure-account-center"></a>Scenariusz 2. Komunikat o błędzie jest wyświetlany w [Centrum konta platformy Azure](https://account.windowsazure.com/Subscriptions)

Sprawdź, czy używane konto ma uprawnienia administratora konta. Aby sprawdzić, kto jest administratorem konta, wykonaj następujące czynności:

1. Zaloguj się do [widoku Subskrypcje w witrynie Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Wybierz subskrypcję, którą chcesz sprawdzić, a następnie przejrzyj **Ustawienia**.
1. Wybierz pozycję **Właściwości**. Administrator konta subskrypcji jest wyświetlany w polu **Administrator konta**.  

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).
