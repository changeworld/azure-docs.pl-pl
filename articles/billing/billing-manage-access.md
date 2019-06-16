---
title: Zarządzanie dostępem do rozliczenia platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak udzielić dostępu do informacji rozliczeniowych platformy Azure dla członków zespołu.
services: ''
documentationcenter: ''
author: vikramdesai01
manager: amberb
editor: ''
tags: billing
ms.assetid: e4c4d136-2826-4938-868f-a7e67ff6b025
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 8c2843840790d1e0dbfd4a789775c6c7ceb51a54
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60918691"
---
# <a name="manage-access-to-billing-information-for-azure"></a>Zarządzanie dostępem do informacji dotyczących rozliczeń dla platformy Azure

W przypadku większości subskrypcji, można przekazać rozliczeń dostęp do informacji o członków zespołu od **subskrypcje** w witrynie Azure portal. Jeśli jesteś klientem Azure z umową Enterprise Agreement (EA klienta) i Administratorzy przedsiębiorstwa, należy nadać uprawnienia administratorom działów i właścicieli kont z odpowiednimi w witrynie Enterprise portal.

## <a name="give-access-to-billing"></a>Udzielić dostępu do rozliczeń

Wszystkie z wyjątkiem klientów z umową EA udzielić dostępu do informacji dotyczących rozliczeń platformy Azure, przypisując jedną z następujących ról użytkownika do członków zespołu:

- Administrator konta
- Administrator usługi
- Współadministrator
- Właściciel
- Współautor
- Czytelnik
- Czytelnik rozliczeń

Aby przypisać role, zobacz [zarządzanie dostępem przy użyciu RBAC i witryny Azure portal](../role-based-access-control/role-assignments-portal.md).

Te role mają dostęp do informacji dotyczących rozliczeń w [witryny Azure portal](https://portal.azure.com/). Osoby, które są przypisane te role można również użyć [interfejsów API rozliczeń](billing-usage-rate-card-overview.md) programowo pobrać faktury i szczegółów użycia. Aby uzyskać więcej informacji, zobacz [ról RBAC platformy Azure](../role-based-access-control/built-in-roles.md).

### <a name="opt-in"></a> Zezwalaj użytkownikom na pobieranie faktur

Po przypisaniu odpowiednie role do członków zespołu, Administrator konta musi włączyć dostęp do uprawnień umożliwiających pobieranie faktur w witrynie Azure portal. Faktury starsze niż grudnia 2016 są dostępne tylko do konta administratora.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Jako Administrator konta, wybierz swoją subskrypcję z [bloku subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) w witrynie Azure portal.

1. Wybierz **faktur** i następnie **dostęp do faktury**.

    ![Zrzut ekranu pokazuje, jak można delegować dostępu do faktury](./media/billing-manage-access/AA-optin.png)

1. Wybierz **na** i Zapisz.

    ![Zrzut ekranu przedstawia wł. / wył można delegować dostępu do faktury](./media/billing-manage-access/AA-optinAllow.png)

Administrator konta można również skonfigurować wysyłanie faktur pocztą e-mail. Aby dowiedzieć się więcej, zobacz [Get your invoice in email](billing-download-azure-invoice-daily-usage-date.md) (Pobieranie faktur za pomocą poczty e-mail).

## <a name="give-read-only-access-to-billing"></a>Zapewnić dostęp tylko do odczytu do rozliczeń

Przypisz rolę Czytelnik rozliczenia osobie, która musi mieć dostęp tylko do odczytu do informacji o rozliczeń subskrypcji, ale nie zdolność do tworzenie usług systemu Azure i zarządzanie nim. Ta rola jest odpowiednia dla użytkowników w organizacji, którzy są odpowiedzialni za zarządzanie finansowe i koszty dla subskrypcji platformy Azure.

Jeśli jesteś klientem z umową EA, właściciel konta lub administratora działu można przypisać roli Czytelnik rozliczenia dla innych członków zespołu. Ale dla tej Czytelnik rozliczenia do wyświetlania informacji dotyczących rozliczeń dla działu lub konta, Administrator przedsiębiorstwa musi włączyć **AO Wyświetl opłaty** lub **DA Wyświetl opłaty** zasady w witrynie Enterprise portal.

Funkcja Czytelnik rozliczenia jest w wersji zapoznawczej i nie obsługuje jeszcze nieglobalnych chmury.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Wybierz swoją subskrypcję z [bloku Subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) w witrynie Azure Portal.

1. Wybierz **kontrola dostępu (IAM)** .
1. Wybierz **przypisań ról** Aby wyświetlić wszystkie przypisania roli dla tej subskrypcji.
1. Wybierz **Dodaj przypisanie roli**.
1. W **roli** listy rozwijanej wybierz **Czytelnik rozliczenia**.
1. W **wybierz** polu tekstowym wpisz nazwę lub poczty e-mail dla użytkownika, które chcesz dodać.
1. Wybierz użytkownika.
1. Wybierz pozycję **Zapisz**.
1. Po kilku chwilach użytkownik przypisany do roli Czytelnik rozliczenia w zakresie subskrypcji.
1. Czytelnik rozliczenia otrzymuje wiadomość e-mail z linkiem do logowania.

    ![Zrzut ekranu, który pokazuje, jakie dane może wyświetlać Czytelnik rozliczenia w witrynie Azure portal](./media/billing-manage-access/billing-reader-view.png)

## <a name="allow-department-administrator-or-account-owner-billing-access"></a>Dostęp do administratora działu lub właściciela konta rozliczeniowego

Administrator przedsiębiorstwa umożliwia administratorom działów i właścicieli kont z odpowiednimi wyświetlić szczegóły użycia i kosztów związanych z działów i kont, którymi zarządzają.

1. Zaloguj się jako Administrator przedsiębiorstwa do [portalu EA portal](https://ea.azure.com/).
1. Wybierz **zarządzanie**.
1. W obszarze **rejestracji**, zmień **DA Wyświetl opłaty** do **włączone** administratora działu w celu wyświetlenia użycia i kosztów.
1. Zmiana **AO Wyświetl opłaty** do **włączone** dotyczące właściciela konta w celu wyświetlenia użycia i kosztów.


Aby uzyskać więcej informacji, zobacz [Enterprise Agreement platformy Azure zrozumienie ról administracyjnych na platformie Azure](billing-understand-ea-roles.md).

## <a name="only-account-admins-can-access-account-center"></a>Tylko administratorzy kont mogą uzyskiwać dostęp do Centrum konta

Administrator konta jest prawnym właścicielem subskrypcji. Domyślnie, kto uzyskał lub zakupiono subskrypcję platformy Azure jest Administrator konta, chyba że [własności subskrypcji została przeniesiona](billing-subscription-transfer.md) do innej osoby. Administrator konta może tworzyć subskrypcje, anulowanie subskrypcji, Zmień adres do rozliczeń dla subskrypcji i zarządzanie zasadami dostępu dla subskrypcji z [Centrum kont](https://account.azure.com/Subscriptions).

## <a name="next-steps"></a>Kolejne kroki

- Użytkowników w innych ról, takich jak właściciela lub współautora, można uzyskać dostęp do informacji nie tylko rozliczeniowych, ale także w przypadku usług platformy Azure. Aby zarządzać tymi rolami, zobacz [zarządzanie dostępem przy użyciu RBAC i witryny Azure portal](../role-based-access-control/role-assignments-portal.md).
- Aby uzyskać więcej informacji na temat ról, zobacz [wbudowane role zasobów platformy Azure](../role-based-access-control/built-in-roles.md).

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://go.microsoft.com/fwlink/?linkid=2083458).
