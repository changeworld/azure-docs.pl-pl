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
ms.openlocfilehash: 6726c876d0895f9488aa2ae5c225a6b2ac19e69f
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490998"
---
# <a name="manage-access-to-billing-information-for-azure"></a>Zarządzanie dostępem do informacji dotyczących rozliczeń dla platformy Azure

Można zapewnić dostęp do informacje rozliczeniowe innych dla swojego konta w witrynie Azure portal. Typ rozliczeń ról i instrukcje, aby zapewnić dostęp do informacje rozliczeniowe zależą od rodzaju konta rozliczeniowego. Aby określić typ konta rozliczeniowego, zobacz [Sprawdź typ konta rozliczeniowego](#check-the-type-of-your-billing-account).

Artykuł ma zastosowanie do klientów przy użyciu kont programu usług Microsoft Online Services. Jeśli jesteś klientem Azure z Enterprise Agreement (EA) i Administratorzy przedsiębiorstwa, należy nadać uprawnienia administratorom działów i właścicieli kont z odpowiednimi w witrynie Enterprise portal. Aby uzyskać więcej informacji, zobacz [Enterprise Agreement platformy Azure zrozumienie ról administracyjnych na platformie Azure](billing-understand-ea-roles.md). Jeśli jesteś klientem z umową klienta firmy Microsoft, znajduje się pozycja [ról administracyjnych zrozumienie umowy klienta firmy Microsoft na platformie Azure](billing-understand-mca-roles.md). 

## <a name="account-administrators-for-microsoft-online-service-program-accounts"></a>Administratorzy konta dla konta programu usług Microsoft Online Services

Administrator konta jest tylko właściciel konta rozliczeniowego programu usług Microsoft Online Service. Rola jest przypisywana do osoby, który utworzył konto na platformie Azure. Administratorzy konta mają uprawnienia do wykonywania różnych zadań rozliczeń, jak tworzyć subskrypcje, wyświetlanie faktur lub zmienić rozliczeń dla subskrypcji.

## <a name="give-others-access-to-view-billing-information"></a>Zezwól na dostęp do wyświetlania informacji dotyczących rozliczeń

Administrator konta można przyznać osobom dostęp do informacji dotyczących rozliczeń platformy Azure, przypisując jedną z następujących ról w ramach subskrypcji na jego koncie.

- Administrator usługi
- Współadministrator
- Właściciel
- Współautor
- Czytelnik
- Czytelnik rozliczenia

Te role mają dostęp do informacji dotyczących rozliczeń w [witryny Azure portal](https://portal.azure.com/). Osoby, które są przypisane te role można również użyć [interfejsów API rozliczeń](billing-usage-rate-card-overview.md) programowo pobrać faktury i szczegółów użycia.

Aby przypisać role, zobacz [zarządzanie dostępem przy użyciu RBAC i witryny Azure portal](../role-based-access-control/role-assignments-portal.md).

** Jeśli jesteś klientem z umową EA, właściciel konta można przypisać rolę powyżej innym użytkownikom ich zespołu. Jednak dla tych użytkowników do wyświetlania informacji dotyczących rozliczeń, Administrator przedsiębiorstwa musi włączyć AO Wyświetl opłaty w witrynie Enterprise portal.


### <a name="opt-in"></a> Zezwalaj użytkownikom na pobieranie faktur

Po administrator konta ma przypisane odpowiednie role, do innych użytkowników, należy włączyć dostęp do uprawnień umożliwiających pobieranie faktur w witrynie Azure portal. Faktury starsze niż grudnia 2016 są dostępne tylko do konta administratora.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/), jako Administrator konta

1. Wyszukiwanie **Cost Management + rozliczenia**.

    ![Zrzut ekranu pokazujący usługi Azure search w portalu](./media/billing-manage-access/billing-search-cost-management-billing.png)
 
1. Wybierz **subskrypcje** z okienka po lewej stronie. W zależności od dostęp, może być konieczne wybierz zakres rozliczeń, a następnie wybierz pozycję **subskrypcje**.
 
    ![Zrzut ekranu przedstawia Wybieranie subskrypcji](./media/billing-manage-access/billing-select-subscriptions.png)

1. Wybierz **faktur** i następnie **dostęp do faktury**.

    ![Zrzut ekranu pokazuje, jak można delegować dostępu do faktury](./media/billing-manage-access/AA-optin.png)

1. Wybierz **na** i Zapisz.

    ![Zrzut ekranu przedstawia wł. / wył można delegować dostępu do faktury](./media/billing-manage-access/AA-optinAllow.png)

Administrator konta można również skonfigurować wysyłanie faktur pocztą e-mail. Aby dowiedzieć się więcej, zobacz [Get your invoice in email](billing-download-azure-invoice-daily-usage-date.md) (Pobieranie faktur za pomocą poczty e-mail).

## <a name="give-read-only-access-to-billing"></a>Zapewnić dostęp tylko do odczytu do rozliczeń

Przypisz rolę Czytelnik rozliczenia osobie, która musi mieć dostęp tylko do odczytu do informacji o rozliczeń subskrypcji, ale nie zdolność do tworzenie usług systemu Azure i zarządzanie nim. Ta rola jest odpowiednia dla użytkowników w organizacji, którzy są odpowiedzialni za zarządzanie finansowe i koszty dla subskrypcji platformy Azure.

Funkcja Czytelnik rozliczenia jest w wersji zapoznawczej i nie obsługuje jeszcze nieglobalnych chmury.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/), jako Administrator konta

1. Wyszukiwanie **Cost Management + rozliczenia**.

    ![Zrzut ekranu pokazujący usługi Azure search w portalu](./media/billing-manage-access/billing-search-cost-management-billing.png)

1. Wybierz **subskrypcje** z okienka po lewej stronie. W zależności od dostęp, może być konieczne wybierz zakres rozliczeń, a następnie wybierz pozycję **subskrypcje**.
 
    ![Zrzut ekranu przedstawia Wybieranie subskrypcji](./media/billing-manage-access/billing-select-subscriptions.png)

1. Wybierz **kontrola dostępu (IAM)** .
1. Wybierz **Dodaj** w górnej części strony.

    ![Zrzut ekranu pokazujący, klikając przycisk Dodaj przypisanie roli](./media/billing-manage-access/billing-click-add-role-assignment.png)

1. W **roli** listy rozwijanej wybierz **Czytelnik rozliczenia**.
1. W **wybierz** polu tekstowym wpisz nazwę lub poczty e-mail dla użytkownika, które chcesz dodać.
1. Wybierz użytkownika.
1. Wybierz pozycję **Zapisz**.
    ![Zrzut ekranu pokazujący, klikając przycisk Dodaj przypisanie roli](./media/billing-manage-access/billing-save-role-assignment.png)

1. Po kilku chwilach użytkownik przypisany do roli Czytelnik rozliczenia dla subskrypcji.

** Jeśli jesteś klientem z umową EA, właściciel konta lub administratora działu można przypisać roli Czytelnik rozliczenia dla innych członków zespołu. Ale dla tej Czytelnik rozliczenia do wyświetlania informacji dotyczących rozliczeń dla działu lub konta, Administrator przedsiębiorstwa musi włączyć **AO Wyświetl opłaty** lub **DA Wyświetl opłaty** zasady w witrynie Enterprise portal.

## <a name="check-the-type-of-your-billing-account"></a>Sprawdź typ konta rozliczeniowego
[!INCLUDE [billing-check-account-type](../../includes/billing-check-account-type.md)]

## <a name="next-steps"></a>Kolejne kroki

- Użytkowników w innych ról, takich jak właściciela lub współautora, można uzyskać dostęp do informacji nie tylko rozliczeniowych, ale także w przypadku usług platformy Azure. Aby zarządzać tymi rolami, zobacz [zarządzanie dostępem przy użyciu RBAC i witryny Azure portal](../role-based-access-control/role-assignments-portal.md).
- Aby uzyskać więcej informacji na temat ról, zobacz [wbudowane role zasobów platformy Azure](../role-based-access-control/built-in-roles.md).

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://go.microsoft.com/fwlink/?linkid=2083458).
