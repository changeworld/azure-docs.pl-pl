---
title: "Opis dostęp do zasobów na platformie Azure | Dokumentacja firmy Microsoft"
description: "W tym temacie opisano pojęcia o używaniu administratorów subskrypcji do kontrolowania dostępu do zasobów w pełnej wersji portalu Azure"
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
ms.assetid: 174f1706-b959-4230-9a75-bf651227ebf6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: curtand
ms.custom: it-pro;
ms.openlocfilehash: 621ebec898e5b345556832097b12ca9b54506e7c
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2018
---
# <a name="understanding-resource-access-in-azure"></a>Opis dostęp do zasobów na platformie Azure

Kontrola dostępu na platformie Azure rozpoczyna się z punktu widzenia rozliczeń. Właściciel konta platformy Azure, dostępne po przejściu na stronę [Centrum konta platformy Azure](https://account.azure.com), jest kontem administratora (AA). Subskrypcje są kontener rozliczeń, ale również działają jako granic zabezpieczeń: Każda subskrypcja ma usługi administratora kto dodawania, usuwania i modyfikowania zasobów platformy Azure w ramach tej subskrypcji przy użyciu [portalu Azure](https://portal.azure.com/). SA domyślne nowej subskrypcji jest AA, ale AA można zmienić SA w Centrum konta platformy Azure.

<br><br>![Konta platformy Azure][1]

Subskrypcje mają również skojarzenie z katalogiem. Katalog definiuje zestaw użytkowników. Mogą to być użytkownikom pracy lub nauki, który utworzył katalog lub można je zewnętrznych gości. Subskrypcje są dostępne dla podzbioru użytkowników katalogu, na których został przypisany jako usługi administratora lub administratora współpracującego (CA); Jedynym wyjątkiem jest w starszej wersji ze względu na kont Microsoft (dawniej identyfikator Windows Live ID) można przypisać jako administratora systemu lub urzędu certyfikacji nie jest obecny w katalogu.

<br><br>![Kontrola dostępu na platformie Azure][2]

Funkcja w portalu Azure umożliwia skojarzeń zabezpieczeń, które jest zalogowany przy użyciu Account firmy Microsoft, aby zmienić katalog, z którym jest skojarzona subskrypcja. Ta operacja ma wpływ na kontroli dostępu w tej subskrypcji.

<br><br>![Proste użytkownika logowania przepływu][3]

W przypadku prostego organizacji (na przykład Contoso) będzie wymuszać rozliczeń i kontroli dostępu na tym samym zestawie subskrypcji. Katalog jest skojarzony z subskrypcji, które należą do jednego konta Azure. Po pomyślnym logowaniu do portalu Azure użytkownicy widzą dwie kolekcje zasobów (kolor pomarańczowy na poprzedniej ilustracji przedstawiono):

* Katalogi Jeśli istnieje ich konta użytkownika (powierzając jej ich konserwację lub dodany jako obcego podmiotu zabezpieczeń). Należy pamiętać, że katalog służący do logowania nie spełniają te obliczenia, katalogi są zawsze wyświetlane niezależnie od tego, w którym się zalogowano.
* Zasoby, które są częścią subskrypcje, które są skojarzone z katalogu używanego do logowania, które użytkownik ma dostęp (gdy są SA lub urząd certyfikacji).

<br><br>![Użytkownik z wieloma subskrypcjami i katalogami][4]

Użytkownicy z subskrypcjami w wielu katalogach mieć możliwość przełączania bieżącego kontekstu portalu Azure przy użyciu filtru subskrypcji. W obszarze obejmuje powoduje to oddzielne logowania do innego katalogu, ale odbywa się bezproblemowo przy użyciu rejestracji jednokrotnej (SSO).

Operacje, takie jak przeniesienie zasobów między subskrypcjami może być trudniejsza w wyniku tego widoku jednego katalogu subskrypcji. Do transferu zasobów może być konieczne do pierwszego użycia **Edytuj katalog** na stronie subskrypcji w **ustawienia** do skojarzenia subskrypcji w tym samym katalogu.

## <a name="next-steps"></a>Następne kroki
* Aby dowiedzieć się więcej o modyfikowaniu administratorów subskrypcji platformy Azure, zobacz [How to add or change Azure administrator roles](../billing/billing-add-change-azure-subscription-administrator.md) (Jak dodać lub zmienić role administratora platformy Azure).
* Aby uzyskać więcej informacji dotyczących sposobu usługi Azure Active Directory odnosi się do subskrypcji platformy Azure, zobacz [jak subskrypcje platformy Azure są kojarzone z usługi Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
* Aby uzyskać więcej informacji na temat sposobu przypisywania ról w usłudze Azure AD, zobacz [Przypisywanie ról administratorów w usłudze Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md).

<!--Image references-->
[1]: ./media/active-directory-understanding-resource-access/IC707931.png
[2]: ./media/active-directory-understanding-resource-access/IC707932.png
[3]: ./media/active-directory-understanding-resource-access/IC707933.png
[4]: ./media/active-directory-understanding-resource-access/IC707934.png
