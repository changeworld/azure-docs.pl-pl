---
title: Zarządzanie dostępem do platformy Azure, rozliczenia, przy użyciu ról | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: 38702fde344bb5fb831f7c26177438456035beae
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2018
ms.locfileid: "44294463"
---
# <a name="manage-access-to-billing-information-for-azure-using-role-based-access-control"></a>Zarządzanie dostępem do informacji rozliczeniowych dla platformy Azure przy użyciu kontroli dostępu opartej na rolach

Dostęp do informacji dotyczących rozliczeń platformy Azure można udzielić członkom zespołu, przypisując jedną z następujących ról użytkownika do subskrypcji: administratora konta, Administrator usługi, administrator współpracujący, właściciela, współautora, czytnika i Czytelnik rozliczeń. Mają oni dostęp do informacji dotyczących rozliczeń w [witryny Azure portal](https://portal.azure.com/), a następnie użyć [interfejsów API rozliczeń](billing-usage-rate-card-overview.md) programowo uzyskać faktur (po wyrażeniu zgody na uczestnictwo — ruch przychodzący) i uzyskać szczegółowe informacje o. Aby uzyskać więcej informacji na temat kto przyznać ról, a które role można zrobić, zobacz [ról RBAC platformy Azure](../role-based-access-control/built-in-roles.md).

## <a name="opt-in"></a> Zezwolenie dodatkowym użytkownikom na dostęp do faktury

Administrator konta musi włączyć za pomocą [witryny Azure portal](https://portal.azure.com/) zezwolić na dostęp do faktury, przy użyciu interfejsu API i innych użytkowników.

1. Jako Administrator konta, wybierz swoją subskrypcję z [bloku subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) w witrynie Azure portal.

1. Wybierz **faktur** i następnie **dostęp do faktury**.

    ![Zrzut ekranu pokazuje, jak można delegować dostępu do faktury](./media/billing-manage-access/AA-optin.png)

1. Włącz **na** dostępu, a następnie zapisywania zmian, aby umożliwić użytkownikom w ramach subskrypcji o określonym zakresie ról do pobierania faktur.

    ![Zrzut ekranu przedstawia wł. / wył można delegować dostępu do faktury](./media/billing-manage-access/AA-optinAllow.png)

Zgodzie na rozwiązanie umożliwia administratora usługi, administrator współpracujący, właściciel, współautor, Czytelnik i Czytelnik rozliczenia w subskrypcji, aby pobierać faktury PDF w witrynie Azure portal. Jednak faktur starsze niż grudnia 2016 są dostępne tylko do konta administratora teraz.

Administrator konta można również skonfigurować mieć faktur pocztą e-mail. Aby dowiedzieć się więcej, zobacz [Pobierz fakturę pocztą e-mail](billing-download-azure-invoice-daily-usage-date.md).

## <a name="adding-users-to-the-billing-reader-role"></a>Dodawanie użytkowników do roli Czytelnik rozliczenia

Rola Czytelnik rozliczenia ma dostęp tylko do odczytu do informacji rozliczeniowych subskrypcji w witrynie Azure portal i Brak dostępu do usług, takich jak maszyny wirtualne i konta magazynu. Przypisz rolę Czytelnik rozliczenia osobie, która wymaga dostępu do informacji o rozliczeń subskrypcji, ale nie umożliwia zarządzanie usługami platformy Azure. Ta rola jest odpowiednia dla użytkowników w organizacji, którzy finansowe i koszty zarządzania należy wykonać tylko dla subskrypcji platformy Azure.

1. Wybierz swoją subskrypcję z [bloku subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) w witrynie Azure portal.

1. Wybierz **kontrola dostępu (IAM)** a następnie kliknij przycisk **Dodaj**.

    ![Zrzut ekranu przedstawia zarządzania tożsamościami i Dostępem w bloku subskrypcji](./media/billing-manage-access/select-iam.PNG)

1. Wybierz **Czytelnik rozliczenia** w **wybierz rolę** strony.

    ![Zrzut ekranu przedstawia Czytelnik rozliczenia w widoku okna podręcznego](./media/billing-manage-access/select-roles.PNG)

1. Wpisz adres e-mail użytkownika, aby zaprosić, a następnie kliknij przycisk **OK** można wysłać zaproszenia.

    ![Zrzut ekranu przedstawiający wprowadzenie wiadomości e-mail z zaproszeniem ktoś](./media/billing-manage-access/add-user.PNG)

1. Postępuj zgodnie z instrukcjami w wiadomości e-mail z zaproszeniem, aby zalogować się jako czytelnik rozliczenia.

    ![Zrzut ekranu, który pokazuje, jakie dane może wyświetlać Czytelnik rozliczenia w witrynie Azure portal](./media/billing-manage-access/billing-reader-view.png)

> [!NOTE]
> Funkcja Czytelnik rozliczenia jest w wersji zapoznawczej i nie obsługuje jeszcze subskrypcji przedsiębiorstwa (EA) lub nieglobalna chmury.

## <a name="adding-users-to-other-roles"></a>Dodawanie użytkowników do innych ról

Użytkowników w innych ról, takich jak właściciela lub współautora, można uzyskać dostęp do informacji nie tylko rozliczeniowych, ale także w przypadku usług platformy Azure. Aby zarządzać tymi rolami, zobacz [zarządzanie dostępem przy użyciu RBAC i witryny Azure portal](../role-based-access-control/role-assignments-portal.md).

## <a name="who-can-access-the-account-centerhttpsaccountwindowsazurecom"></a>Kto ma dostęp do [Centrum kont](https://account.windowsazure.com)?

Tylko Administrator konta może zalogować się do Centrum konta. Administrator konta jest prawnym właścicielem subskrypcji. Domyślnie, kto uzyskał lub zakupiono subskrypcję platformy Azure jest Administrator konta, chyba że [własności subskrypcji została przeniesiona](billing-subscription-transfer.md) do innej osoby. Administrator konta może tworzyć subskrypcje, anulowanie subskrypcji, Zmień adres do rozliczeń dla subskrypcji i zarządzanie zasadami dostępu dla subskrypcji.

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną.

Jeśli nadal masz dodatkowe pytania, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem.
