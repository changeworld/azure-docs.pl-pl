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
ms.openlocfilehash: 5980b8b701695d002d58b6b5239e6fa04c02fb2f
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2019
ms.locfileid: "57249101"
---
# <a name="complete-enterprise-agreement-tasks-in-your-billing-account-for-a-microsoft-customer-agreement"></a>Wykonywanie zadań umowy Enterprise Agreement do konta rozliczeniowego dla umowy klienta firmy Microsoft

Jeśli Twoja organizacja zarejestrowała umowy klienta firmy Microsoft, aby odnowić rejestrację umowy Enterprise Agreement, należy skonfigurować nowe konto rozliczeń, utworzone umowy. Nowe konto rozliczeniowe zapewnia ulepszone możliwości zarządzania dotyczące rozliczeń i kosztów za pośrednictwem nowe środowisko prostsze, ujednoliconemu zarządzaniu;. Aby uzyskać więcej informacji, zobacz [Ustawianie konta rozliczeniowego dla umowy klienta Microsoft](billing-mca-setup-account.md).

Po zakończeniu instalacji, należy użyć w tym artykule, aby zrozumieć sposób wykonywania zadań, które były wykonywane w Twojej rejestracji Enterprise Agreement.

Ten artykuł dotyczy konta rozliczeniowego dla umowy klienta firmy Microsoft. [Sprawdź, czy dostęp do umowy klienta Microsoft](#check-your-access-to-a-microsoft-customer-agreement).

## <a name="mapping-of-enterprise-agreements-billing-to-the-new-billing-account"></a>Mapowanie rozliczeń umowy Enterprise Agreement do nowego konta rozliczeniowego

Rozliczenia w nowym kontem jest zorganizowana inaczej niż rejestrację umowy Enterprise Agreement. W poniższej tabeli opisano mapowanie rozliczeń z rejestrację umowy Enterprise Agreement do konta rozliczeniowego. Aby dowiedzieć się więcej na temat konta rozliczeniowego, zobacz [Rozpoczynanie korzystania z konta rozliczeniowego dla umowy klienta Microsoft](billing-mca-overview.md).

| Umowa Enterprise   | Umowa klienta firmy Microsoft    |
|------------------------|--------------------------------------------------------|
| Rejestracja            | Profil rozliczeniowy umożliwia zarządzanie rozliczeń dla całej organizacji, podobnie jak rejestrację umowy Enterprise Agreement. Administratorzy przedsiębiorstwa usług stają się właścicieli profil rozliczeniowy. Aby dowiedzieć się więcej na temat rozliczeń profilów, zobacz [zrozumieć profile rozliczeń](billing-mca-overview.md#understand-billing-profiles).
| Dział            | Sekcja faktury umożliwia organizowanie koszty, podobnie jak działom w rejestrację umowy Enterprise Agreement. Dział staje się sekcje faktury, a dział Administratorzy stają się właścicieli sekcje odpowiednich faktury. Aby uzyskać więcej informacji na temat sekcje faktury, zobacz [sekcje faktury omówienie](billing-mca-overview.md#understand-invoice-sections). |
| Konto               | Konta, które zostały utworzone w ramach umowy Enterprise Agreement, nie są obsługiwane przez nowe konta rozliczeniowego. Subskrypcje dla konta należą do sekcji faktury odpowiednie dla swojego wydziału. Właściciele kont można tworzyć i zarządzać subskrypcji dla ich sekcji faktury. |

## <a name="tasks-performed-by-an-enterprise-administrator"></a>Zadania wykonywane przez administratora przedsiębiorstwa

Jeśli jesteś administratorem przedsiębiorstwa umowy Enterprise Agreement, która otrzymała odnowienia umowy klienta firmy Microsoft, są przypisane następujące role na nowe konto rozliczeniowe utworzone umowy:

**Rozliczenia właściciela profilu** — są przypisywane rozliczeń Rola właściciela profilu na profil rozliczeniowy, który został utworzony po podpisaniu umowy. Rola umożliwia zarządzanie rozliczeń dla Twojej organizacji. Można wyświetlić opłat i faktur, organizowanie koszty, faktury, Zarządzaj metodami płatności i kontrolować dostęp do rozliczeń w organizacji.

**Właściciel sekcji faktury** — masz przypisaną rolę właściciela sekcji faktury na wszystkie sekcje faktury, które są tworzone dla działów w rejestrację umowy Enterprise Agreement. Rola pozwala na kontrolowanie, kto może tworzyć subskrypcje platformy Azure i nabywać produkty innych.

### <a name="view-charges-and-credits-balance-for-your-organization"></a>Wyświetl opłaty i środki na korzystanie z saldo dla Twojej organizacji

Twój profil rozliczeń umożliwia śledzenie opłaty i Saldo środków platformy Azure dla Twojej organizacji, które są podobne do Twojej rejestracji Enterprise Agreement.

Aby dowiedzieć się, jak wyświetlić salda środków dla profil rozliczeniowy, zobacz [Azure śledzenie salda środków dla swojego profilu rozliczeń](billing-mca-check-azure-credits-balance.md).

Aby dowiedzieć się, jak wyświetlić opłat za profil rozliczeniowy, zobacz [informacje o opłatach na fakturze umowy klienta Microsoft](billing-mca-understand-your-bill.md).

### <a name="view-charges-for-a-department"></a>Wyświetl opłaty dla działu

Sekcja faktura jest tworzony dla każdego działu, jaką miał w ramach umowy Enterprise Agreement. Opłaty za części faktury można wyświetlić w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz [wyświetlać transakcje w sekcjach faktury](billing-mca-understand-your-bill.md#view-transactions-by-invoice-sections).

### <a name="view-charges-for-an-account"></a>Wyświetl opłaty za konto

Konta, które zostały utworzone w rejestrację umowy Enterprise Agreement, nie są obsługiwane w nowe konta rozliczeniowego. Subskrypcje dla konta należą do sekcji faktury odpowiednie dla swojego wydziału. Właściciele kont można tworzyć i zarządzać subskrypcji dla ich sekcji faktury.

Aby wyświetlić łączny koszt subskrypcje należące do konta usługi, należy ustawić Centrum kosztów dla każdej subskrypcji. Następnie przy użyciu platformy Azure pliku csv użycia i opłat do filtrowania subskrypcji przez Centrum kosztów.

### <a name="download-usage-and-charges-csv-price-sheet-and-tax-documents"></a>Pobieranie pliku csv użycia i opłat, Arkusz cen i dokumenty podatku

Miesięcznej fakturze jest generowany dla każdego profilu rozliczeń w ramach Twojego konta rozliczeniowego. Na każdej fakturze można pobrać plików csv użycia i opłat platformy Azure, Arkusz cen i dokument podatku (jeśli dotyczy). Można również pobrać Azure użycia i opłat plik csv dla bieżącego miesiąca opłaty.

Aby dowiedzieć się, jak pobrać plik csv Azure użycia i opłat, zobacz [pobierania lub wyświetlania faktury i dane dziennego użycia rozliczeniową za platformę Azure](billing-download-azure-invoice-daily-usage-date.md). <!--Todo update the link -->

Informacje na temat pobierania dokumentów arkusza i opodatkowaniem cen, zobacz [dokumenty podatku Pobierz fakturę](billing-download-azure-invoice-daily-usage-date.md). <!--Todo update the link -->

### <a name="add-an-additional-enterprise-administrator"></a>Dodaj administratora przedsiębiorstwa

Użyj **Control(IAM) dostępu** strony w witrynie Azure portal, Zezwól na dostęp do wyświetlania i zarządzania profil rozliczeniowy. Aby dowiedzieć się więcej na temat rozliczeń rolami profilów, zobacz [rozliczeń profilu role i zadania](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

Aby dowiedzieć się, jak Podaj, dostęp do Twojego profilu rozliczeń, zobacz [Zarządzanie rolami rozliczeń w witrynie Azure portal](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="create-a-new-department"></a>Utwórz nowy dział

W nowego konta rozliczeniowego sekcja faktury jest służy do organizowania koszty, zgodnie z potrzebami, podobnie jak działom w rejestrację umowy Enterprise Agreement. Nowa sekcja faktury można utworzyć w witrynie Azure portal, aby skonfigurować platformy Azure dla nowego wydziału. Aby dowiedzieć się więcej, zobacz [Tworzenie sekcji na fakturze w taki sposób, aby zorganizować koszty](billing-mca-section-invoice.md).

### <a name="create-a-new-account"></a>Utwórz nowe konto

Kontekstu rozliczeń konta w ramach umowy Enterprise Agreement nie jest obsługiwana w ramach nowego konta rozliczeniowego. Może jednak zapewniać roli Twórca subskrypcji platformy Azure dla użytkowników w Twojej organizacji, aby umożliwić mu Tworzenie subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [daje uprawnienia do tworzenia subskrypcji platformy Azure](billing-mca-create-subscription.md#give-others-permission-to-create-azure-subscriptions).

## <a name="tasks-performed-by-a-department-administrator"></a>Zadania wykonywane przez administratora działu

Jeśli jesteś administratorem działu z umowami Enterprise Agreement odnowienia umowy klienta firmy Microsoft, są przypisane następujące role na nowe konto rozliczeniowe:

**Właściciel sekcji faktury** — masz przypisaną rolę właściciela sekcji faktury w sekcji faktury, które są tworzone dla działów, gdyby w umowy Enterprise Agreement. Rola pozwala przeglądania i opłaty śledzenia i kontrolowania mogących tworzyć subskrypcje platformy Azure i zakupu innych produktów w sekcji faktury.

Jeśli jesteś administratorem działu na wiele działów w umowy Enterprise Agreement, są przypisywane roli właściciel sekcji faktury na wszystkie sekcje faktury utworzone dla działów.

### <a name="view-charges-for-your-department"></a>Wyświetl opłaty dla Twojego działu

Sekcja faktura jest tworzony dla każdego działu, jaką miał w ramach umowy Enterprise Agreement. Sekcja faktury ma taką samą nazwę jak dział w umowie Enterprise Agreement. Opłaty za części faktury można wyświetlić w witrynie Azure portal. <!-- Todo Add a link -->

### <a name="add-an-additional-department-administrator"></a>Dodaj administratora działu dodatkowe

Sekcja faktura jest tworzony dla każdego działu, jaką miał w ramach umowy Enterprise Agreement. Możesz użyć **Control(IAM) dostępu** strony w witrynie Azure portal, Zezwól na dostęp do wyświetlania i zarządzania sekcji faktury. Aby dowiedzieć się więcej o rolach sekcji faktury, zobacz [faktury profilu role i zadania](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

Aby dowiedzieć się, jak Podaj, dostęp do sekcji faktury, zobacz [Zarządzanie rolami rozliczeń w witrynie Azure portal](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="create-a-new-account-in-your-department"></a>Utwórz nowe konto w Twoim dziale

Kontekstu rozliczeń konta w ramach umowy Enterprise Agreement nie jest obsługiwana w ramach nowego konta rozliczeniowego. Może jednak zapewniać roli Twórca subskrypcji platformy Azure dla użytkowników w Twojej organizacji, aby umożliwić mu Tworzenie subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [daje uprawnienia do tworzenia subskrypcji platformy Azure](billing-mca-create-subscription.md#give-others-permission-to-create-azure-subscriptions).

### <a name="view-charges-for-accounts-in-your-departments"></a>Wyświetl opłaty dla kont w działu

Kontekstu rozliczeń konta w ramach umowy Enterprise Agreement nie jest obsługiwana w ramach nowego konta rozliczeniowego. Subskrypcje dla konta należą do sekcji faktury, utworzyć dla działu. Właściciele kont można tworzyć i zarządzać subskrypcjami dla sekcji faktury.

Aby wyświetlić łączny koszt subskrypcje należące do konta w Twoim dziale, należy ustawić Centrum kosztów dla każdej subskrypcji. Następnie przy użyciu plików platformy Azure, użycia i opłat do filtrowania subskrypcji przez Centrum kosztów. <!-- Todo - can they go to cost analysis -->

## <a name="tasks-performed-by-an-account-owner"></a>Zadania wykonywane przez właściciela konta

Jeśli jesteś właścicielem konta umowy Enterprise Agreement, która otrzymała odnowienia umowy klienta firmy Microsoft, są przypisane następującą rolę na nowe konto rozliczeniowe utworzone umowy:

**Twórca subskrypcji platformy Azure** -przypisano rolę twórca subskrypcji platformy azure w sekcji faktury, który jest tworzony dla Twojego działu w umowy Enterprise Agreement. Jeśli Twoje konto nie należy do działu, subskrypcje będą należeć do sekcji faktury o nazwie domyślnej sekcji faktury, a twórca subskrypcji platformy Azure możesz uzyskać w sekcji. Rola pozwala tworzyć subskrypcje platformy Azure dla sekcji faktury.

### <a name="view-charges-for-your-account"></a>Wyświetl opłaty dla swojego konta

Kontekstu rozliczeń konta w ramach umowy Enterprise Agreement nie jest obsługiwana w ramach nowego konta rozliczeniowego. Subskrypcje dla konta należą do sekcji faktury, utworzyć dla działu Twoje konto.

Aby wyświetlić opłaty za subskrypcje należące do konta, przejdź do **strony subskrypcje** w witrynie Azure portal. Na stronie subskrypcje są wyświetlane opłaty dla Twojej subskrypcji. <!-- Todo - can they go to cost analysis -->

### <a name="view-charges-for-a-subscription"></a>Wyświetl opłaty w przypadku subskrypcji

Analiza kosztów platformy Azure służy do wyświetlania opłaty za subskrypcję. Aby uzyskać więcej informacji, zobacz [badanie i analizowanie kosztów za pomocą analizy kosztów](../cost-management/quick-acm-cost-analysis.md).

### <a name="create-an-azure-subscription"></a>Tworzenie subskrypcji platformy Azure

Sekcji faktury w witrynie Azure portal można utworzyć subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [należy utworzyć dodatkową subskrypcję platformy Azure dla umowy klienta firmy Microsoft](billing-mca-create-subscription.md)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Sprawdź dostęp do umowy klienta firmy Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli potrzebujesz pomocy, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem.

## <a name="next-steps"></a>Kolejne kroki

- [Opis konta rozliczeniowego dla umowy klienta firmy Microsoft](billing-mca-overview.md)
- [Zrozumienie faktury](billing-understand-your-bill.md)
- [Opis zawartości rachunku](billing-understand-your-invoice.md)
- [Pobierz rozliczeń własności subskrypcji platformy Azure od innych użytkowników](billing-mca-request-billing-ownership.md)
