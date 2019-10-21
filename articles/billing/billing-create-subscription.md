---
title: Tworzenie dodatkowej subskrypcji platformy Azure | Microsoft Docs
description: Dowiedz się, jak dodać nową subskrypcję platformy Azure w witrynie Azure Portal.
services: billing
documentationcenter: ''
author: amberb
manager: amberb
editor: ''
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: banders
ms.openlocfilehash: acb41de8344d1467e86c38c7c99e8ad4ab517ead
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72375822"
---
# <a name="create-an-additional-azure-subscription"></a>Tworzenie dodatkowej subskrypcji platformy Azure

Możesz utworzyć dodatkową subskrypcję dla konta rozliczeniowego [umowy Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), [umowy klienta firmy Microsoft](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) lub [umowy partnerskiej firmy Microsoft](https://www.microsoft.com/licensing/news/introducing-microsoft-partner-agreement) w witrynie Azure Portal. Dzięki dodatkowej subskrypcji możesz na przykład unikać przekraczania limitów subskrypcji, tworzyć oddzielne środowiska na potrzeby zabezpieczeń lub izolować dane ze względu na zapewnienie zgodności.

Jeśli masz konto rozliczeniowe Microsoft Online Service Program (MOSP), możesz tworzyć dodatkowe subskrypcje [na stronie rejestracji w witrynie Azure Portal](https://account.azure.com/signup?offer=ms-azr-0003p).

Aby dowiedzieć się więcej na temat kont rozliczeniowych i identyfikowania typu konta rozliczeniowego, zobacz [Wyświetlanie kont rozliczeniowych w witrynie Azure Portal](billing-view-all-accounts.md).

## <a name="permission-required-to-create-azure-subscriptions"></a>Uprawnienia wymagane do tworzenia subskrypcji platformy Azure

Do tworzenia subskrypcji wymagane są następujące uprawnienia:

|Konto billingowe  |Uprawnienie  |
|---------|---------|
|Umowa Enterprise Agreement (EA) |  Rola właściciela konta rejestracji umowy Enterprise Aby uzyskać więcej informacji, zobacz [Omówienie ról administracyjnych dla umowy Azure Enterprise Agreement na platformie Azure](billing-understand-ea-roles.md).    |
|Umowa klienta firmy Microsoft (MCA) |  Rola właściciela lub współautora w sekcji faktury, profilu rozliczeń lub koncie rozliczeniowym Rola twórcy subskrypcji platformy Azure w sekcji faktury  Aby uzyskać więcej informacji, zobacz [Role rozliczeniowe i zadania w subskrypcji](billing-understand-mca-roles.md#subscription-billing-roles-and-tasks).    |
|Umowa partnerska firmy Microsoft |   Rola administratora globalnego i agenta administratora w organizacji partnerskiej Aby dowiedzieć się więcej, zobacz [Centrum partnerskie — Przypisywanie ról i uprawnień użytkowników](https://docs.microsoft.com/partner-center/permissions-overview).  |

## <a name="create-a-subscription-in-the-azure-portal"></a>Tworzenie subskrypcji w witrynie Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wyszukaj pozycję **Subskrypcje**.

   ![Zrzut ekranu przedstawiający wyszukiwanie subskrypcji w portalu](./media/billing-create-subscription/billing-search-subscription-portal.png)

1. Wybierz pozycję **Dodaj**.

   ![Zrzut ekranu przedstawiający przycisk Dodaj w widoku Subskrypcje](./media/billing-create-subscription/subscription-add.png)

1. Jeśli masz dostęp do wielu kont rozliczeń, wybierz konto rozliczeniowe, dla którego chcesz utworzyć subskrypcję.

1. Wypełnij formularz i kliknij przycisk **Utwórz**. W poniższych tabelach przedstawiono pola formularza dla poszczególnych typów kont rozliczeniowych.

**Umowa Enterprise Agreement**

|Pole  |Definicja  |
|---------|---------|
|Name (Nazwa)     | Nazwa wyświetlana ułatwia znajdowanie subskrypcji w witrynie Azure Portal.  |
|Oferta     | Wybierz ofertę EA Tworzenie i testowanie, jeśli zamierzasz używać tej subskrypcji do obciążeń programistycznych lub testowych. W przeciwnym razie wybierz ofertę Microsoft Azure Enterprise. Oferta Tworzenie i testowanie musi być włączona dla konta rejestracji, aby można było tworzyć subskrypcje w ramach umowy EA Tworzenie i testowanie.|

**Umowa klienta firmy Microsoft**

|Pole  |Definicja  |
|---------|---------|
|Profil rozliczeniowy     | Opłaty za subskrypcję będą naliczane w wybranym profilu rozliczeniowym. Jeśli masz dostęp tylko do jednego profilu rozliczeniowego, opcje wyboru będą wyszarzone.     |
|Sekcja faktury     | Opłaty za subskrypcję będą wyświetlane w tej sekcji faktury w profilu rozliczeniowym. Jeśli masz dostęp tylko do jednej sekcji faktury, opcje wyboru będą wyszarzone.  |
|Planowanie     | Wybierz plan platformy Microsoft Azure na potrzeby tworzenia i testowania, jeśli zamierzasz używać tej subskrypcji do obciążeń programistycznych lub testowych. W przeciwnym razie wybierz plan platformy Microsoft Azure. Jeśli dla profilu rozliczeniowego włączono tylko jeden plan, opcje wyboru będą wyszarzone.  |
|Name (Nazwa)     | Nazwa wyświetlana ułatwia znajdowanie subskrypcji w witrynie Azure Portal.  |

**Umowa partnerska firmy Microsoft**

|Pole  |Definicja  |
|---------|---------|
|Klient    | Subskrypcja zostanie utworzona dla wybranego klienta. Jeśli masz tylko jednego klienta, opcje do wyboru będą wyszarzone.  |
|Odsprzedawca    | Odsprzedawca, który będzie świadczyć usługi klientom. To jest pole opcjonalne, które ma zastosowanie tylko do dostawców pośrednich w dwuwarstwowym modelu usług CSP. |
|Name (Nazwa)     | Nazwa wyświetlana ułatwia znajdowanie subskrypcji w witrynie Azure Portal.  |

## <a name="create-an-additional-azure-subscription-programmatically"></a>Programowe tworzenie dodatkowej subskrypcji platformy Azure

Dodatkowe subskrypcje można również tworzyć programowo. Aby uzyskać więcej informacji, zobacz [Programowe tworzenie subskrypcji platformy Azure](../azure-resource-manager/programmatically-create-subscription.md).

## <a name="next-steps"></a>Następne kroki

- [Dodawanie lub zmienianie administratorów subskrypcji platformy Azure](billing-add-change-azure-subscription-administrator.md)
- [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/resource-group-move-resources.md?toc=/azure/billing/TOC.json)
- [Tworzenie grup zarządzania na potrzeby organizowania zasobów i zarządzania nimi](../governance/management-groups/create.md?toc=/azure/billing/TOC.json)
- [Anulowanie subskrypcji platformy Azure](billing-how-to-cancel-azure-subscription.md)

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).
