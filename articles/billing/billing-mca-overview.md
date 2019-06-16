---
title: Rozpoczynanie korzystania z konta rozliczeniowego dla umowy klienta firmy Microsoft — Azure | Dokumentacja firmy Microsoft
description: Opis konta rozliczeniowego dla umowy klienta firmy Microsoft
services: billing
documentationcenter: ''
author: amberbhargava
manager: amberbhargava
editor: banders
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: banders
ms.openlocfilehash: ea625a61ed600dbaa22fef85987e9570a6fb7dbc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60371470"
---
# <a name="get-started-with-your-billing-account-for-a-microsoft-customer-agreement"></a>Rozpoczynanie korzystania z konta rozliczeniowego dla umowy klienta firmy Microsoft

Konto rozliczeniowe jest tworzona dla każdej umowy, zarejestruj się z firmą Microsoft za pomocą platformy Azure. Konto rozliczeniowe umożliwia zarządzanie rozliczeniami i śledzenie kosztów. Masz dostęp do wielu kont rozliczeń. Na przykład może być zarejestrowaniu się na platformie Azure dla Twoich projektów osobistych. Może również mieć dostęp do platformy Azure za pośrednictwem umowy Enterprise Agreement lub umowy klienta firmy Microsoft Twojej organizacji. Dla każdego z tych scenariuszy trzeba oddzielnego konta rozliczeniowego.

Ten artykuł dotyczy konta rozliczeniowego dla umowy klienta firmy Microsoft. [Sprawdź, czy dostęp do umowy klienta Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="understand-billing-account"></a>Zrozumienie konto rozliczeniowe

Konto rozliczeniowe dla umowy klienta firmy Microsoft zawiera co najmniej jeden profil rozliczeń, które pozwalają zarządzać metodami płatności i faktur. Każdy profil rozliczeniowy zawiera jeden lub więcej sekcji faktur, które umożliwiają organizowanie koszty, faktury profil rozliczeniowy.

Na poniższym diagramie przedstawiono relację między kontem rozliczeniowym, profilami rozliczeniowymi i sekcjami faktury.

![Diagram przedstawiający hierarchii rozliczeń dla umowy klienta firmy Microsoft](./media/billing-mca-overview/mca-billing-hierarchy.png)

Role na konto rozliczeniowe mają najwyższy poziom uprawnień. Domyślnie tylko administratorzy globalni w Twojej organizacji usługi Azure Active Directory, Uzyskaj dostęp do konta rozliczeniowego. Tych ról powinny być przypisane do użytkowników, których potrzebuje do wyświetlania faktur i śledzenie kosztów dla całej organizacji, takich jak finansowego lub przekazać wyższym kierownikom IT. Aby uzyskać więcej informacji, zobacz [rozliczenia konta role i zadania](billing-understand-mca-roles.md#billing-account-roles-and-tasks).

## <a name="understand-billing-profiles"></a>Zrozumieć profile rozliczeń

Profil rozliczeniowy umożliwia zarządzanie metody faktury i płatności. Miesięcznej fakturze jest generowany dla subskrypcji platformy Azure i innych produktów zakupionych przy użyciu profilu rozliczeń. Możesz użyć metody płatności fakturę.

Profil rozliczeniowy automatycznie jest tworzony dla swojego konta rozliczeniowego. Można tworzyć nowych profilów rozliczeń, aby skonfigurować dodatkowe faktur. Na przykład możesz różnych faktury dla każdego działu lub projektu w Twojej organizacji.

Można również utworzyć sekcje faktury w celu organizowania koszty, faktury profil rozliczeniowy. Opłaty za subskrypcje platformy Azure i produktów zakupionych w sekcji faktury są wyświetlane w sekcji. Faktury profil rozliczeniowy obejmuje opłaty za wszystkie faktury sekcje.

Role na rozliczeń profile mają uprawnienia do wyświetlania i zarządzania faktury i metody płatności. Przypisz te role do użytkowników, którzy płaci faktur, takich jak członkowie zespołu ewidencjonowania aktywności w Twojej organizacji. Aby uzyskać więcej informacji, zobacz [rozliczeń profilu role i zadania](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

### <a name="monthly-invoice-generated-for-each-billing-profile"></a>Miesięcznej fakturze generowane dla każdego profilu rozliczeń

Miesięcznej fakturze jest generowany dla daty faktury dla każdego profilu rozliczeń. Faktura zawiera wszystkie opłaty w poprzednim miesiącu.

Możesz wyświetlić faktury, pobierania dokumentów i zmienić ustawienie, aby uzyskać przyszłe faktury za pośrednictwem poczty e-mail w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz [pobierać faktury dla umowy klienta Microsoft](billing-download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement).

### <a name="invoices-paid-through-payment-methods"></a>Fakturach wypłacane przy użyciu metody płatności

Każdy profil rozliczeniowy ma swoje własne metody płatności, które są używane do płacenia jego faktur. Obsługiwane są następujące formy płatności:

| Typ             | Definicja  |
|------------------|-------------|
|Kredytów systemu Azure    |  Środki są automatycznie stosowane do łączna kwota rachunku na fakturze do obliczenia wartości, które trzeba płacić. Aby uzyskać więcej informacji, zobacz [śledzić salda środków platformy Azure dla swojego profilu rozliczeń](billing-mca-check-azure-credits-balance.md). |
|Sprawdzanie lub okablowanie transferu | Możesz wykupić wynosi należna kwota fakturę za pośrednictwem wyboru lub okablowanie transferu. Instrukcje dotyczące płatności są podane na fakturze |

### <a name="control-azure-marketplace-and-reservation-purchases-by-applying-policies"></a>Kontrola Azure Marketplace i rezerwacji zakupów za pomocą zasad

Należy stosować zasady umożliwiające kontrolowanie zakupy dokonane przy użyciu profilu rozliczeń. Można ustawić zasady, aby wyłączyć zakupu rezerwacji platformy Azure i produkty w portalu Marketplace. Gdy zasady są stosowane, subskrypcje utworzone dla sekcji faktury w profil rozliczeniowy nie można kupować rezerwacje platformy Azure i produkty w portalu Marketplace.

### <a name="allow-users-to-create-azure-subscriptions-by-enabling-azure-plans"></a>Zezwalaj użytkownikom na tworzenie subskrypcji platformy Azure, umożliwiając plany platformy Azure

Plany platformy Azure są włączane automatycznie, gdy tworzysz profil rozliczeniowy. Wszystkie sekcje faktury w profil rozliczeniowy uzyskują dostęp do tych planów. Użytkownicy z dostępem do sekcji faktury Użyj plany, aby utworzyć subskrypcje platformy Azure. Nie mogą tworzyć subskrypcje platformy Azure, chyba że planu platformy Azure jest włączony dla profil rozliczeniowy. Następujące plany usługi Azure są obsługiwane w rozliczeniach kont do umowy klienta firmy Microsoft:

| Planowanie             | Definicja  |
|------------------|-------------|
|Plan platformy Microsoft Azure   | Zezwalaj użytkownikom na tworzenie subskrypcji, które można uruchomić żadnych obciążeń. Aby uzyskać więcej informacji, zobacz [Plan usługi Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g/) |
|Plan platformy Microsoft Azure — tworzenie i testowanie | Zezwalaj na subskrybentów programu Visual Studio do utworzenia subskrypcji, które są ograniczone do tworzenia lub testowania obciążenia. Te subskrypcje korzyści takie jak niższe stawki oraz dostęp do obrazów maszyn wirtualnych wyłączności w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz [Microsoft Azure planowanie DevTest](https://azure.microsoft.com/offers/ms-azr-0148g/)|

## <a name="understand-invoice-sections"></a>Zrozumienie faktury sekcje

Tworzenie sekcji faktur do organizowania koszty, faktury profil rozliczeniowy. Na przykład może być konieczne pojedynczej faktury dla Twojej organizacji, ale chcesz organizować koszty według działu, zespołu lub projektu. W tym scenariuszu masz jeden profil rozliczeniowym, w której utworzono sekcję faktury dla każdego działu, zespołu lub projektu.

Po utworzeniu faktury sekcji, można zezwolić innym tworzyć subskrypcje platformy Azure, w sekcji. Wszelkie opłaty za użycie i zakupów dla subskrypcji, następnie są odzwierciedlane w odpowiedniej sekcji faktury.

Role w sekcji faktury mają uprawnienia do kontroli, który tworzy subskrypcji platformy Azure. Przypisz te role do użytkowników, którzy konfigurowania środowiska platformy Azure dla zespołów w naszej organizacji, takich jak inżynierów potencjalnych klientów i architektów Technical Preview. Aby uzyskać więcej informacji, zobacz [faktury sekcji role i zadania](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Sprawdź dostęp do umowy klienta firmy Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną

Jeśli potrzebujesz pomocy, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem.

## <a name="next-steps"></a>Kolejne kroki

Zobacz następujące artykuły, aby dowiedzieć się więcej na temat konta rozliczeniowego:

- [Zrozumienie umowy klienta Microsoft ról administracyjnych na platformie Azure](billing-understand-mca-roles.md)
- [Należy utworzyć dodatkową subskrypcję platformy Azure dla umowy klienta firmy Microsoft](billing-mca-create-subscription.md)
- [Tworzenie sekcji na fakturze w taki sposób, aby zorganizować koszty](billing-mca-section-invoice.md)