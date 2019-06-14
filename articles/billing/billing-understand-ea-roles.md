---
title: Omówienie ról administratora dla przedsiębiorstw na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o rolach administratorów przedsiębiorstwa na platformie Azure.
services: billing
documentationcenter: ''
author: adpick
manager: adpick
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2018
ms.author: banders
ms.openlocfilehash: 98ed28af8df246549fb521a81f1968e1f5c28cc4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60370717"
---
# <a name="understand-azure-enterprise-agreement-administrative-roles-in-azure"></a>Omówienie usługi Azure Enterprise Agreement ról administracyjnych na platformie Azure

Ułatwiające zarządzanie organizacji użycia i wydatków Azure klienci z Enterprise Agreement (EA) można przypisać pięć różnych ról administracyjnych:

- Enterprise Administrator
- Administrator przedsiębiorstwa (tylko odczyt)
- Administrator działu
- Administrator działu (tylko odczyt)
- Właściciel konta
 
Te role są specyficzne dla umów Enterprise platformy zarządzania i w niniejszym dokumencie stanowią wbudowane role, platforma Azure ma kontrolować dostęp do zasobów. Aby uzyskać więcej informacji, zobacz [wbudowane role zasobów platformy Azure](../role-based-access-control/built-in-roles.md).

Poniższe sekcje opisują, ograniczenia i możliwości poszczególnych ról.

## <a name="user-limit-for-admin-roles"></a>Limit użytkowników dla ról administratora

|Rola| Limit użytkowników|
|---|---|
|Enterprise Administrator|Nieograniczona liczba|
|Administrator przedsiębiorstwa (tylko odczyt)|Nieograniczona liczba|
|Administrator działu|Nieograniczona liczba|
|Administrator działu (tylko odczyt)|Nieograniczona liczba|
|Właściciel konta|1 na jednym koncie<sup>1</sup>|

<sup>1</sup> każde konto wymaga unikatowego konta Microsoft lub konta służbowego lub szkolnego.

## <a name="organization-structure-and-permissions-by-role"></a>Struktura organizacyjna i uprawnień według roli

|Zadania| Enterprise Administrator|Administrator przedsiębiorstwa (tylko odczyt)|Administrator działu|Administrator działu (tylko odczyt)|Właściciel konta|
|---|---|---|---|---|---|
|Wyświetl administratorów przedsiębiorstwa|✔|✔|✘|✘|✘|
|Dodaj lub usuń administratorów przedsiębiorstwa|✔|✘|✘|✘|✘|
|Wyświetl kontakty powiadomień<sup>2</sup> |✔|✔|✘|✘|✘|
|Dodawanie lub usuwanie kontaktów powiadomień<sup>2</sup> |✔|✘|✘|✘|✘|
|Tworzenie i zarządzanie działy |✔|✘|✘|✘|✘|
|Administratorzy działów widoku|✔|✔|✔|✔|✘|
|Dodawanie lub usuwanie administratorów działu|✔|✔|✔|✘|✘|
|Widok kont w rejestracji |✔|✔|✔<sup>3</sup>|✔<sup>3</sup>|✘|
|Dodaj konta do rejestracji i Zmień właściciela konta|✔|✘|✔<sup>3</sup>|✘|✘|
|Tworzenie i zarządzanie subskrypcjami i uprawnienia do subskrypcji|✘|✘|✘|✘|✔|

- <sup>2</sup> kontakty powiadomienia są wysyłane wiadomości e-mail z informacjami o usłudze Azure Enterprise Agreement.
- <sup>3</sup> zadanie jest ograniczona do kont w Twoim dziale.


## <a name="usage-and-costs-access-by-role"></a>Użycie i koszty dostępu według roli

|Zadania| Enterprise Administrator|Administrator przedsiębiorstwa (tylko odczyt)|Administrator działu|Administrator działu (tylko odczyt) |Właściciel konta|
|---|---|---|---|---|---|
|Saldo środków widoku, łącznie z zobowiązania pieniężnego|✔|✔|✘|✘|✘|
|Limity przydziału wydatków działu widoku|✔|✔|✘|✘|✘|
|Ustaw limity przydziału wydatków działu|✔|✘|✘|✘|✘|
|Wyświetl arkusz cen EA w organizacji|✔|✔|✘|✘|✘|
|Wyświetl szczegóły użycia i kosztów|✔|✔|✔<sup>4</sup>|✔<sup>4</sup>|✔<sup>5</sup>|
|Zarządzanie zasobami w witrynie Azure portal|✘|✘|✘|✘|✔|

- <sup>4</sup> wymaga, aby włączyć Administrator przedsiębiorstwa **DA Wyświetl opłaty** zasady w witrynie Enterprise portal. Administrator działu może następnie zobaczyć szczegóły kosztów dla działu.
- <sup>5</sup> wymaga, aby włączyć Administrator przedsiębiorstwa **AO Wyświetl opłaty** zasady w witrynie Enterprise portal. Właściciel konta może wtedy wyświetlone szczegóły kosztów dla konta.


## <a name="pricing-in-azure-portal"></a>Ceny w witrynie Azure portal

Mogą pojawić się różnymi cenami w witrynie Azure portal, w zależności od Twojej roli administracyjnej i jak Wyświetl opłaty zasady są ustawione przez administratora przedsiębiorstwa. Dostępne są następujące dwie zasady w witrynie Enterprise portal, które wpływają na ceny, które są widoczne w witrynie Azure portal:

- Akcelerator deweloperski w wersji Wyświetl opłaty
- AO Wyświetl opłaty

Informacje na temat ustawiania tych zasad, zobacz [zarządzanie dostępem do informacji dotyczących rozliczeń dla platformy Azure](billing-manage-access.md).

W poniższej tabeli przedstawiono relację między ról administratora umowy Enterprise Agreement, Wyświetl zasady opłaty, opartej na rolach dostęp do kontroli (RBAC) rolą w witrynie Azure portal i ceny widoczne w witrynie Azure portal. Administrator przedsiębiorstwa zawsze widzi Szczegóły obciążenia oparte na temat cen EA w organizacji. Jednak Administrator działu i właściciela konta zobaczyć różne widoki cen opartych na zasadach opłaty widoku i ich rolę RBAC. Rola administratora działu, wymienione w poniższej tabeli odwołuje się do administratora działu i ról administratora działu (tylko do odczytu).

|Rola administratora umowy Enterprise Agreement|Wyświetl zasady opłaty dla roli|Rola RBAC|Wyświetl cennik|
|---|---|---|---|
|Właściciel konta lub administratora działu|✔ Enabled|Właściciel|Cennik usługi organizacji EA|
|Właściciel konta lub administratora działu|✘ Wyłączone|Właściciel|Ceny detalicznej|
|Właściciel konta lub administratora działu|✔ Enabled |Brak|Nie ceny|
|Właściciel konta lub administratora działu|✘ Wyłączone |Brak|Nie ceny|
|Brak|Nie dotyczy |Właściciel|Ceny detalicznej|

Ustaw rolę administratora przedsiębiorstwa i wyświetlanie opłat zasady w witrynie Enterprise portal. Rola RBAC, może zostać zaktualizowana w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz [zarządzanie dostępem przy użyciu RBAC i witryny Azure portal](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Kolejne kroki

- [Zarządzanie dostępem do informacji dotyczących rozliczeń dla platformy Azure](billing-manage-access.md)
- [Manage access using RBAC and the Azure portal (Zarządzanie dostępem przy użyciu kontroli dostępu opartej na rolach i witryny Azure Portal)](../role-based-access-control/role-assignments-portal.md)
- [Wbudowane role dla zasobów platformy Azure](../role-based-access-control/built-in-roles.md)
