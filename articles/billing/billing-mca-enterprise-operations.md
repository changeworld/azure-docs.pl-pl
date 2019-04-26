---
title: Wykonywanie zadań umowy Enterprise Agreement, umowy klienta firmy Microsoft — Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wykonać zadania z umową Enterprise Agreement do konta rozliczeniowego.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/24/2018
ms.author: banders
ms.openlocfilehash: 9b76fa935263904957c87cd062c84d0607771369
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60371351"
---
# <a name="complete-enterprise-agreement-tasks-in-your-billing-account-for-a-microsoft-customer-agreement"></a>Wykonywanie zadań umowy Enterprise Agreement do konta rozliczeniowego dla umowy klienta firmy Microsoft

Jeśli Twoja organizacja zarejestrowała umowy klienta firmy Microsoft, aby odnowić rejestrację umowy Enterprise Agreement, nowe konto rozliczeniowe zostanie utworzony dla umowy. Rozliczenia w nowym kontem jest zorganizowana inaczej niż w ramach umowy Enterprise Agreement. W tym artykule opisano, jak można użyć nowego konta rozliczeniowego do wykonywania zadań, które były wykonywane w ramach umowy Enterprise Agreement.

## <a name="how-billing-is-organized-in-the-new-account"></a>Jak rozliczenia jest zorganizowana w nowe konto

Na poniższym diagramie przedstawiono, jak rozliczenia jest zorganizowana w konta rozliczeniowego.

![Obraz przedstawiający ea mca-post przejścia hierarchii](./media/billing-mca-setup-account/mca-post-transition-hierarchy.png)

| Enterprise Agreement   | Umowa klienta firmy Microsoft    |
|------------------------|--------------------------------------------------------|
| Rejestracja            | Profil rozliczeniowy umożliwia zarządzanie rozliczeń dla całej organizacji, podobnie jak rejestrację umowy Enterprise Agreement. Administratorzy przedsiębiorstwa usług stają się właścicieli profil rozliczeniowy. Aby dowiedzieć się więcej na temat rozliczeń profilów, zobacz [zrozumieć profile rozliczeń](billing-mca-overview.md#understand-billing-profiles).
| Dział            | Sekcja faktury umożliwia organizowanie koszty, podobnie jak działom w rejestrację umowy Enterprise Agreement. Dział staje się sekcje faktury, a dział Administratorzy stają się właścicieli sekcje odpowiednich faktury. Aby uzyskać więcej informacji na temat sekcje faktury, zobacz [sekcje faktury omówienie](billing-mca-overview.md#understand-invoice-sections). |
| Konto               | Konta, które zostały utworzone w ramach umowy Enterprise Agreement, nie są obsługiwane przez nowe konta rozliczeniowego. Subskrypcje dla konta należą do sekcji faktury odpowiednie dla swojego wydziału. Właściciele kont można tworzyć i zarządzać subskrypcji dla ich sekcji faktury. |

## <a name="changes-for-enterprise-administrators-in-the-new-billing-account"></a>Zmiany dotyczące Administratorzy przedsiębiorstwa w nowe konto rozliczeniowe

Następujące zmiany dotyczą Administratorzy przedsiębiorstwa w umowy Enterprise Agreement, która otrzymała odnowienia umowy klienta firmy Microsoft.

- Profil rozliczeniowy jest tworzona dla Twojej rejestracji. Profil rozliczeniowy będzie używać do zarządzania rozliczeniami dla całej organizacji, takich jak rejestrację umowy Enterprise Agreement. Aby dowiedzieć się więcej o profilach rozliczeń [zrozumieć profile rozliczeń](billing-mca-overview.md#understand-billing-profiles).
- Sekcja faktura jest tworzony dla każdego działu w rejestrację umowy Enterprise Agreement. Użyjesz sekcje faktury Zarządzanie działy usługi. Można tworzyć nowe sekcje faktury, aby skonfigurować dodatkowe działów. Aby uzyskać więcej informacji na temat sekcje faktury, zobacz [zrozumienie faktury sekcje](billing-mca-overview.md#understand-invoice-sections).
- Korzystając z roli Twórca subskrypcji platformy Azure faktury sekcje udostępniania uprawnień do utworzenia subskrypcji platformy Azure, takich jak konta, które zostały utworzone w rejestracji umowy Enterprise Agreement.
- Użyjesz [witryny Azure portal](https://portal.azure.com) do zarządzania rozliczeniami dla całej organizacji, zamiast portalu Azure EA.

Następujące role są podane na nowe konto rozliczeniowe:

**Rozliczenia właściciela profilu** — są przypisywane rozliczeń Rola właściciela profilu na profil rozliczeniowy, który został utworzony po podpisaniu umowy. Rola umożliwia zarządzanie rozliczeń dla Twojej organizacji. Można wyświetlić opłat i faktur, organizowanie koszty, faktury, Zarządzaj metodami płatności i kontrolować dostęp do rozliczeń w organizacji.

**Właściciel sekcji faktury** — masz przypisaną rolę właściciela sekcji faktury na wszystkie sekcje faktury, które są tworzone dla działów w rejestrację umowy Enterprise Agreement. Rola pozwala na kontrolowanie, kto może tworzyć subskrypcje platformy Azure i nabywać produkty innych.

### <a name="view-charges-and-credits-balance-for-your-organization"></a>Wyświetl opłaty i środki na korzystanie z saldo dla Twojej organizacji

Twój profil rozliczeń umożliwia śledzenie opłaty i Saldo środków platformy Azure dla Twojej organizacji, które są podobne do Twojej rejestracji Enterprise Agreement.

Aby dowiedzieć się, jak wyświetlić salda środków dla profil rozliczeniowy, zobacz [śledzić salda środków platformy Azure dla swojego profilu rozliczeń](billing-mca-check-azure-credits-balance.md).

Aby dowiedzieć się, jak wyświetlić opłat za profil rozliczeniowy, zobacz [informacje o opłatach na fakturze umowy klienta Microsoft](billing-mca-understand-your-bill.md).

### <a name="view-charges-for-a-department"></a>Wyświetl opłaty dla działu

Sekcja faktura jest tworzony dla każdego działu, jaką miał w ramach umowy Enterprise Agreement. Opłaty za części faktury można wyświetlić w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz [wyświetlać transakcje w sekcjach faktury](billing-mca-understand-your-bill.md#view-transactions-by-invoice-sections).

### <a name="view-charges-for-an-account"></a>Wyświetl opłaty za konto

Konta, które zostały utworzone w rejestrację umowy Enterprise Agreement, nie są obsługiwane w nowe konta rozliczeniowego. Subskrypcje dla konta należą do sekcji faktury odpowiednie dla swojego wydziału. Właściciele kont można tworzyć i zarządzać subskrypcji dla ich sekcji faktury.

Aby wyświetlić łączny koszt subskrypcje należące do konta usługi, należy ustawić Centrum kosztów dla każdej subskrypcji. Następnie przy użyciu platformy Azure pliku csv użycia i opłat do filtrowania subskrypcji przez Centrum kosztów.

### <a name="download-usage-and-charges-csv-price-sheet-and-tax-documents"></a>Pobieranie pliku csv użycia i opłat, Arkusz cen i dokumenty podatku

Miesięcznej fakturze jest generowany dla każdego profilu rozliczeń w ramach Twojego konta rozliczeniowego. Na każdej fakturze można pobrać plików csv użycia i opłat platformy Azure, Arkusz cen i dokument podatku (jeśli dotyczy). Można również pobrać Azure użycia i opłat plik csv dla bieżącego miesiąca opłaty.

Aby dowiedzieć się, jak pobrać plik csv Azure użycia i opłat, zobacz [Pobierz dane użycia dla umowy klienta Microsoft](billing-download-azure-daily-usage.md#download-usage-for-your-microsoft-customer-agreement).

Aby dowiedzieć się, jak pobrać arkusz cen, zobacz [pobrać cen dla umowy klienta Microsoft](billing-ea-pricing.md#view-and-download-pricing-for-your-microsoft-customer-agreement).

Informacje na temat pobierania podatków dokumentów, zobacz [wyświetlić dokumenty podatku umowy klienta Microsoft](billing-mca-download-tax-document.md#view-and-download-tax-documents).

### <a name="add-an-additional-enterprise-administrator"></a>Dodaj administratora przedsiębiorstwa

Dostęp użytkowników do rozliczeń profilu, aby umożliwić im widoku i zarządzają rozliczeniami dla Twojej organizacji. Możesz użyć **kontrola dostępu (IAM)** strony w witrynie Azure portal, aby udzielić dostępu.  Aby dowiedzieć się więcej na temat rozliczeń rolami profilów, zobacz [rozliczeń profilu role i zadania](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

Aby dowiedzieć się, jak Podaj, dostęp do Twojego profilu rozliczeń, zobacz [Zarządzanie rolami rozliczeń w witrynie Azure portal](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="create-a-new-department"></a>Utwórz nowy dział

Utwórz sekcję faktur do organizowania kosztów na podstawie własnych potrzeb, takich jak działom w rejestrację umowy Enterprise Agreement. Nowa sekcja faktury można utworzyć w witrynie Azure portal. Aby dowiedzieć się więcej, zobacz [Tworzenie sekcji na fakturze w taki sposób, aby zorganizować koszty](billing-mca-section-invoice.md).

### <a name="create-a-new-account"></a>Utwórz nowe konto

Przypisywanie użytkowników roli Twórca subskrypcji platformy Azure na fakturze sekcji, aby nadać im uprawnienia do tworzenia subskrypcji platformy Azure, takich jak konta, które są tworzone w rejestracji umowy Enterprise Agreement. Aby uzyskać więcej informacji, zobacz [daje uprawnienia do tworzenia subskrypcji platformy Azure](billing-mca-create-subscription.md#give-others-permission-to-create-azure-subscriptions).

## <a name="changes-for-department-administrators-in-the-new-billing-account"></a>Zmiany dotyczące działu administratorów w nowe konto rozliczeniowe

Następujące zmiany są stosowane do administratorów dział na umowę Enterprise Agreement, która otrzymała odnowienia umowy klienta firmy Microsoft.

- Sekcja faktura jest tworzony dla każdego działu w rejestrację umowy Enterprise Agreement. Użyjesz pliku faktury Zarządzanie działy usługi. Aby uzyskać więcej informacji na temat sekcje faktury, zobacz [zrozumienie faktury sekcje](billing-mca-overview.md#understand-invoice-sections).
- Korzystając z roli Twórca subskrypcji platformy Azure w sekcji faktury udostępniania uprawnień do utworzenia subskrypcji platformy Azure, takich jak konta, które są tworzone w rejestracji umowy Enterprise Agreement.
- Użyjesz witryny Azure portal do zarządzania rozliczeniami dla całej organizacji, zamiast portalu Azure EA.

Następujące role są podane na nowe konto rozliczeniowe:

**Właściciel sekcji faktury** — masz przypisaną rolę właściciela sekcji faktury w sekcji faktury, które są tworzone dla działy, gdyby w umowy Enterprise Agreement. Rola pozwala przeglądania i opłaty śledzenia i kontrolowania mogących tworzyć subskrypcje platformy Azure i zakupu innych produktów w sekcji faktury.

### <a name="view-charges-for-your-department"></a>Wyświetl opłaty dla Twojego działu

Można wyświetlić opłat za sekcji faktury, który jest tworzony dla działu, w witrynie Azure portal [Zarządzanie kosztami i rozliczenia strony](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview).

### <a name="add-an-additional-department-administrator"></a>Dodaj administratora działu dodatkowe

Sekcja faktura jest tworzony dla każdego działu, jaką miał w ramach umowy Enterprise Agreement. Możesz użyć **Control(IAM) dostępu** strony w witrynie Azure portal, Zezwól na dostęp do wyświetlania i zarządzania sekcji faktury. Aby dowiedzieć się więcej o rolach sekcji faktury, zobacz [faktury sekcji role i zadania](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

Aby dowiedzieć się, jak Podaj, dostęp do sekcji faktury, zobacz [Zarządzanie rolami rozliczeń w witrynie Azure portal](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="create-a-new-account-in-your-department"></a>Utwórz nowe konto w Twoim dziale

Przypisywanie użytkowników roli Twórca subskrypcji platformy Azure w sekcji faktur, które są tworzone dla działu. Aby uzyskać więcej informacji, zobacz [daje uprawnienia do tworzenia subskrypcji platformy Azure](billing-mca-create-subscription.md#give-others-permission-to-create-azure-subscriptions).

### <a name="view-charges-for-accounts-in-your-departments"></a>Wyświetl opłaty dla kont w działu

Konta, które zostały utworzone w rejestrację umowy Enterprise Agreement, nie są obsługiwane w nowe konta rozliczeniowego. Subskrypcje dla konta należą do sekcji faktury odpowiednie dla swojego wydziału. Właściciele kont można tworzyć i zarządzać subskrypcji dla ich sekcji faktury.

Aby wyświetlić łączny koszt subskrypcje należące do konta w Twoim dziale, należy ustawić Centrum kosztów dla każdej subskrypcji. Następnie przy użyciu plików platformy Azure, użycia i opłat do filtrowania subskrypcji przez Centrum kosztów.

## <a name="changes-for-account-owners-in-the-new-billing-account"></a>Zmiany dla właścicieli kont z odpowiednimi w nowe konto rozliczeniowe

Właściciele kont z umowami Enterprise Agreement uzyskać uprawnienia do tworzenia subskrypcji platformy Azure na nowe konto rozliczeniowe. Istniejącej subskrypcji platformy Azure należą do sekcji faktury, który jest tworzony dla działu. Jeśli Twoje konto nie należy do działu, Twoje subskrypcje należą do sekcji faktury o nazwie domyślnej sekcji faktury.

Aby utworzyć dodatkowe subskrypcje platformy Azure, podano następującą rolę na nowe konto rozliczeniowe.

**Twórca subskrypcji platformy Azure** -przypisano rolę twórca subskrypcji platformy azure w sekcji faktury, który jest tworzony dla Twojego działu w umowy Enterprise Agreement. Jeśli Twoje konto nie należy do działu, otrzymasz roli Twórca subskrypcji platformy Azure w sekcji o nazwie domyślnej sekcji faktury. Rola pozwala tworzyć subskrypcje platformy Azure dla sekcji faktury.

### <a name="create-an-azure-subscription"></a>Tworzenie subskrypcji platformy Azure

Sekcji faktury w witrynie Azure portal można utworzyć subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [należy utworzyć dodatkową subskrypcję platformy Azure dla umowy klienta firmy Microsoft](billing-mca-create-subscription.md)

### <a name="view-charges-for-your-account"></a>Wyświetl opłaty dla swojego konta

Aby wyświetlić opłaty za subskrypcje należące do konta, przejdź do [strony subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) w witrynie Azure portal. Na stronie subskrypcje są wyświetlane opłaty dla Twojej subskrypcji.

### <a name="view-charges-for-a-subscription"></a>Wyświetl opłaty w przypadku subskrypcji

Opłaty za subskrypcję można wyświetlić albo na [strony subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) lub na platformie Azure, analiza kosztów. Aby uzyskać więcej informacji na temat analizy kosztów platformy Azure, zobacz [badanie i analizowanie kosztów za pomocą analizy kosztów](../cost-management/quick-acm-cost-analysis.md).

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli potrzebujesz pomocy, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem.

## <a name="next-steps"></a>Kolejne kroki

- [Opis konta rozliczeniowego dla umowy klienta firmy Microsoft](billing-mca-overview.md)
- [Zrozumienie faktury](billing-understand-your-bill.md)
- [Opis zawartości rachunku](billing-understand-your-invoice.md)
- [Pobierz rozliczeń własności subskrypcji platformy Azure od innych użytkowników](billing-mca-request-billing-ownership.md)
