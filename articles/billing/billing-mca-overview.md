---
title: Wprowadzenie do konta rozliczeniowego umowy klienta firmy Microsoft — Azure
description: Zrozumienie umowy klienta firmy Microsoft, konto rozliczeniowe
author: bandersmsft
manager: amberbhargava
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 87483c967641489e9548f38c99eebbf121d0d252
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490748"
---
# <a name="get-started-with-your-microsoft-customer-agreement-billing-account"></a>Rozpoczynanie pracy z kontem rozliczeniowym umowy klienta firmy Microsoft

Konto rozliczeniowe powstaje po zarejestrowaniu się za pomocą platformy Azure. Użyj konta rozliczeniowego do faktury, płatności, zarządzania i śledzenie kosztów. Masz dostęp do wielu kont rozliczeń. Na przykład może być zarejestrowaniu się na platformie Azure dla Twoich projektów osobistych. Może również mieć dostęp do platformy Azure za pośrednictwem umowy Enterprise Agreement lub umowy klienta firmy Microsoft Twojej organizacji. Dla każdego z tych scenariuszy trzeba oddzielnego konta rozliczeniowego.

Ten artykuł dotyczy konta rozliczeniowego dla umowy klienta firmy Microsoft. [Sprawdź, czy dostęp do umowy klienta Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="your-billing-account"></a>Konto rozliczeniowe

Konto rozliczeniowe dla umowy klienta firmy Microsoft zawiera co najmniej jeden profil rozliczeń, które pozwalają zarządzać metodami płatności i faktur. Każdy profil rozliczeniowy zawiera jeden lub więcej sekcji faktur, które umożliwiają organizowanie koszty, faktury profil rozliczeniowy.

Na poniższym diagramie przedstawiono relację między sekcjach faktury, rozliczeń profile i konta rozliczeniowego.

![Diagram przedstawiający hierarchii rozliczeniowy z umowy klienta firmy Microsoft](./media/billing-mca-overview/mca-billing-hierarchy.png)

Role na konto rozliczeniowe mają najwyższy poziom uprawnień. Domyślnie tylko użytkownik, który utworzył konto na potrzeby platformy Azure pobiera dostęp do konta rozliczeniowego. Tych ról powinny być przypisane do użytkowników, których potrzebuje do wyświetlania faktur i śledzenie kosztów dla całej organizacji, takich jak finansowego lub przekazać wyższym kierownikom IT. Aby uzyskać więcej informacji, zobacz [rozliczenia konta role i zadania](billing-understand-mca-roles.md#billing-account-roles-and-tasks).

## <a name="billing-profiles"></a>Profile rozliczeń

Profil rozliczeniowy umożliwia zarządzanie metody faktury i płatności. Miesięcznej fakturze jest generowany po rozpoczęciu miesiąca dla każdego profilu rozliczeń na koncie. Faktura zawiera odpowiednie opłaty za wszystkie subskrypcje systemu Azure i innych zakupy z poprzedniego miesiąca.

Profil rozliczeniowy automatycznie jest tworzony dla swojego konta rozliczeniowego. Domyślnie zawiera jedną sekcję faktury. Możesz utworzyć dodatkowe sekcje łatwe śledzenie i organizowanie kosztów, w zależności od potrzeb tego, czy jest na projekt, działu lub tworzenia środowiska. Poniższe sekcje zostaną wyświetlone na fakturze profil rozliczeniowy odzwierciedlający użytkowania każdej subskrypcji i zakupów, przypisanej do niego.

Role na rozliczeń profile mają uprawnienia do wyświetlania i zarządzania faktury i metody płatności. Przypisz te role do użytkowników, którzy płaci faktur, takich jak członkowie zespołu ewidencjonowania aktywności w Twojej organizacji. Aby uzyskać więcej informacji, zobacz [rozliczeń profilu role i zadania](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

### <a name="each-billing-profile-gets-a-monthly-invoice"></a>Każdy profil rozliczeniowy pobiera miesięcznej fakturze

Miesięcznej fakturze jest generowany po rozpoczęciu miesiąca dla każdego profilu rozliczeń. Faktura zawiera wszystkie opłaty z poprzedniego miesiąca.

Możesz wyświetlić faktury, pobierania dokumentów i zmienić ustawienie, aby uzyskać przyszłe faktury za pośrednictwem poczty e-mail w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz [pobierać faktury dla umowy klienta Microsoft](billing-download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement).

### <a name="invoice-payment-methods"></a>Metodami płatności faktury

Każdy profil rozliczeniowy ma swoje własne metody płatności, które są używane do płacenia jego faktur. Obsługiwane są następujące formy płatności:

| Typ             | Definicja  |
|------------------|-------------|
|Kredytów systemu Azure    |  Środki są automatycznie stosowane do kwalifikujących się opłat na fakturze, skracając, który trzeba płacić. Aby uzyskać więcej informacji, zobacz [śledzić salda środków platformy Azure dla swojego profilu rozliczeń](billing-mca-check-azure-credits-balance.md). |
|Sprawdź/przewodowy transferu | Jeśli Twoje konto zostanie zatwierdzona do płatności za pośrednictwem wyboru/przelew bankowy. Można płacić, kwota należności fakturę za pośrednictwem wyboru/przelew bankowy. Instrukcje dotyczące płatności są podane na fakturze |
|Karta kredytowa | Klienci, którzy utworzą konto na platformie Azure za pośrednictwem witryny internetowej platformy Azure można płacić za pomocą karty kredytowej. |

### <a name="apply-policies-to-control-purchases"></a>Stosować zasady umożliwiające kontrolowanie zakupów

Stosowanie zasad do kontroli portalu Azure Marketplace i zakup rezerwacji przy użyciu profilu rozliczeń. Można ustawić zasady, aby wyłączyć zakupu rezerwacji platformy Azure i produkty w portalu Marketplace. Gdy zasady są stosowane, subskrypcją rozliczaną w taki sposób, aby profil rozliczeniowy nie może służyć do tych zakupów.

### <a name="azure-plans-determine-pricing-and-service-level-agreement-for-subscriptions"></a>Azure plany określić cen i usługi umowy dotyczącej poziomu subskrypcji

Plany usługi Azure określania ceny i umowami dotyczącymi poziomu dla subskrypcji platformy Azure. Są one włączane automatycznie podczas tworzenia profilu rozliczeń. Wszystkie sekcje faktury, które są skojarzone z profilem rozliczeń można użyć tych planów. Użytkownicy z dostępem do sekcji faktury Użyj plany, aby utworzyć subskrypcje platformy Azure. Następujące plany usługi Azure są obsługiwane w rozliczeniach kont do umowy klienta firmy Microsoft:

| Planowanie             | Definicja  |
|------------------|-------------|
|Plan platformy Microsoft Azure   | Zezwalaj użytkownikom na tworzenie subskrypcji, które można uruchomić żadnych obciążeń. Aby uzyskać więcej informacji, zobacz [Plan usługi Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g/) |
|Plan platformy Microsoft Azure — tworzenie i testowanie | Zezwalaj na subskrybentów programu Visual Studio do utworzenia subskrypcji, które są ograniczone do tworzenia lub testowania obciążenia. Te subskrypcje korzyści takie jak niższe stawki oraz dostęp do obrazów maszyn wirtualnych wyłączności w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz [Microsoft Azure planowanie DevTest](https://azure.microsoft.com/offers/ms-azr-0148g/)|

## <a name="invoice-sections"></a>Sekcje faktury

Tworzenie sekcji faktur do organizowania kosztów na fakturze. Na przykład może być konieczne pojedynczej faktury dla Twojej organizacji, ale chcesz organizować koszty według działu, zespołu lub projektu. W tym scenariuszu masz jeden profil rozliczeniowym, w której utworzono sekcję faktury dla każdego działu, zespołu lub projektu.

Po utworzeniu faktury sekcji, można zezwolić innym tworzyć subskrypcje platformy Azure, które są rozliczane w sekcji. Wszelkie opłaty za użycie i zakupów dla subskrypcji, następnie są rozliczane w sekcji.

Role w sekcji faktury mają uprawnienia do kontroli, który tworzy subskrypcji platformy Azure. Przypisz te role do użytkowników, którzy konfigurowania środowiska platformy Azure dla zespołów w naszej organizacji, takich jak inżynierów potencjalnych klientów i architektów Technical Preview. Aby uzyskać więcej informacji, zobacz [faktury sekcji role i zadania](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Sprawdź dostęp do umowy klienta firmy Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną.

Jeśli potrzebujesz pomocy, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem.

## <a name="next-steps"></a>Kolejne kroki

Zobacz następujące artykuły, aby dowiedzieć się więcej na temat konta rozliczeniowego:

- [Zrozumienie umowy klienta Microsoft ról administracyjnych na platformie Azure](billing-understand-mca-roles.md)
- [Należy utworzyć dodatkową subskrypcję platformy Azure dla umowy klienta firmy Microsoft](billing-mca-create-subscription.md)
- [Tworzenie sekcji na fakturze w taki sposób, aby zorganizować koszty](billing-mca-section-invoice.md)
