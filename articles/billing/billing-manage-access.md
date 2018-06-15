---
title: Zarządzanie dostępem do rozliczeń, za pomocą ról platformy Azure | Dokumentacja firmy Microsoft
description: ''
services: ''
documentationcenter: ''
author: vikramdesai01
manager: vikdesai
editor: ''
tags: billing
ms.assetid: e4c4d136-2826-4938-868f-a7e67ff6b025
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/22/2017
ms.author: vikdesai
ms.openlocfilehash: 7329b06171bd538cc6e9aa8172380a2d4dd47dae
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
ms.locfileid: "33204126"
---
# <a name="manage-access-to-billing-information-for-azure-using-role-based-access-control"></a>Zarządzanie dostępem do informacji dotyczących rozliczeń dla platformy Azure przy użyciu kontroli dostępu opartej na rolach

Można przyznać dostęp do informacji dotyczących rozliczeń platformy Azure do członków zespołu przypisując jedną z następujących ról użytkownika do subskrypcji: konto administratora, administratora usługi, administratora współpracującego, właściciela, współautora, czytnika i rozliczeń czytnika. Mają oni dostęp do informacji dotyczących rozliczeń w [portalu Azure](https://portal.azure.com/), oraz mogą używać [rozliczeń interfejsów API](billing-usage-rate-card-overview.md) programowo uzyskać faktury (raz nie zgłoszono — ruch przychodzący) i szczegóły użycia. Aby uzyskać więcej informacji o który Przyznaj ról, a role można zrobić, zobacz [role w programie Azure RBAC](../role-based-access-control/built-in-roles.md).

## <a name="opt-in"></a> Zezwolenie dodatkowym użytkownikom na dostęp do faktury

Administrator konta musi włączyć za pomocą [portalu Azure](https://portal.azure.com/) zezwolić na dostęp do faktury dla innych użytkowników, jak i za pomocą interfejsu API.

1. Jako administratora konta, wybierz subskrypcję z [bloku subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) w portalu Azure.

1. Wybierz **faktury** , a następnie **dostęp do faktury**.

    ![Zrzut ekranu pokazuje, jak delegować dostęp do faktury](./media/billing-manage-access/AA-optin.png)

1. Włącz **na** role, aby pobrać faktury zakres dostępu następuje zapisywania zmian, aby umożliwić użytkownikom w ramach subskrypcji.

    ![Zrzut ekranu przedstawia na — wyłączone na delegowany dostęp na potrzeby faktury](./media/billing-manage-access/AA-optinAllow.png)

Zgodzie na rozwiązanie umożliwia administratora usługi, administratora współpracującego właściciela, współautora, czytnika i rozliczeń czytnika na subskrypcję, aby pobierać faktury PDF w portalu Azure. Jednak faktury starsze niż grudnia 2016 są dostępne tylko do konta administratora teraz.

Administrator konta można również skonfigurować mają faktury wysyłane za pośrednictwem poczty e-mail. Aby dowiedzieć się więcej, zobacz [uzyskać potwierdzenia w wiadomości e-mail](billing-download-azure-invoice-daily-usage-date.md).

## <a name="adding-users-to-the-billing-reader-role"></a>Dodawanie użytkowników do roli Czytelnik rozliczeń

Rola rozliczeń czytnik ma dostęp tylko do odczytu do informacji dotyczących rozliczeń subskrypcję w portalu Azure i Brak dostępu do usług, takich jak konta magazynu i maszyn wirtualnych. Przypisać rolę czytelnika rozliczeń osobie, która potrzebuje dostępu do informacji dotyczących rozliczeń subskrypcji, ale nie umożliwia zarządzanie usługami Azure. Ta rola jest odpowiednia dla użytkowników w organizacji, którzy finansowych i kosztów zarządzania należy wykonać tylko dla subskrypcji platformy Azure.

1. Wybierz subskrypcję z [bloku subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) w portalu Azure.

1. Wybierz **(IAM) kontroli dostępu** , a następnie kliknij przycisk **Dodaj**.

    ![Zrzut ekranu przedstawia IAM w bloku subskrypcji](./media/billing-manage-access/select-iam.PNG)

1. Wybierz **rozliczeń czytnika** w **wybierz rolę** strony.

    ![Zrzut ekranu przedstawia rozliczeń czytnika w widoku menu podręczne](./media/billing-manage-access/select-roles.PNG)

1. Wpisz adres e-mail użytkownika, aby zaprosić, a następnie kliknij przycisk **OK** można wysłać zaproszenia.

    ![Zrzut ekranu pokazujący wprowadzenia wiadomości e-mail z zaproszeniem ktoś](./media/billing-manage-access/add-user.PNG)

1. Wykonaj instrukcje w wiadomości e-mail zaproszenia, aby zalogować się jako czytnik rozliczeń.

    ![Zrzut ekranu pokazujący jakie dane może wyświetlać czytnika rozliczeń w portalu Azure](./media/billing-manage-access/billing-reader-view.png)

> [!NOTE]
> Funkcja rozliczeń czytnik jest w wersji zapoznawczej i jeszcze nie obsługuje subskrypcji enterprise (EA) lub nieglobalna chmury.

## <a name="adding-users-to-other-roles"></a>Dodawanie użytkowników do innych ról

Dostęp użytkownicy w innych ról, takich jak właścicielem lub współautorem, nie tylko rozliczeniowymi, ale również usług platformy Azure. Aby zarządzać tymi rolami, zobacz [Dodawanie lub zmienianie ról administrator usługi Azure, które zarządzają subskrypcji lub usługi](billing-add-change-azure-subscription-administrator.md).

## <a name="who-can-access-the-account-centerhttpsaccountwindowsazurecom"></a>Kto ma dostęp do [Centrum konta](https://account.windowsazure.com)?

Tylko Administrator konta może zalogować się do Centrum konta. Administrator konta jest prawnym właścicielem subskrypcji. Domyślnie osoba, która utworzył konto na lub zakupiono subskrypcję platformy Azure jest administratorem konta, chyba że [przeniesienia własności subskrypcji](billing-subscription-transfer.md) do innej osoby. Administrator konta można tworzyć subskrypcje, anulowanie subskrypcji, zmienić adres do rozliczeń dla subskrypcji i zarządzanie zasadami dostępu dla subskrypcji.

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną.

Jeśli masz więcej pytań, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) uzyskać szybkie rozwiązanie problemu.
