---
title: Informacje o rolach administratorów dla przedsiębiorstwa na platformie Azure | Microsoft Docs
description: Dowiedz się więcej na temat ról administratorów przedsiębiorstwa na platformie Azure.
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
ms.openlocfilehash: 39526defb8f41ddacb0a26d7ad852f820ca6ea77
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034524"
---
# <a name="understand-azure-enterprise-agreement-administrative-roles-in-azure"></a>Informacje na temat ról administracyjnych Umowa Enterprise platformy Azure na platformie Azure

Aby ułatwić zarządzanie użyciem organizacji i wydatków, klienci platformy Azure z Umowa Enterprise (EA) mogą przypisywać pięć odrębnych ról administracyjnych:

- Administrator przedsiębiorstwa
- Administrator przedsiębiorstwa (tylko do odczytu)
- Administrator działu
- Administrator działu (tylko do odczytu)
- Właściciel konta
 
Te role są specyficzne dla zarządzania umowami platformy Azure Enterprise i oprócz wbudowanych ról platforma Azure musi kontrolować dostęp do zasobów. Aby uzyskać więcej informacji, zobacz [wbudowane role dla zasobów platformy Azure](../role-based-access-control/built-in-roles.md).

W poniższych sekcjach opisano ograniczenia i możliwości poszczególnych ról.

## <a name="user-limit-for-admin-roles"></a>Limit użytkowników dla ról administratorów

|Role| Limit użytkowników|
|---|---|
|Administrator przedsiębiorstwa|Nieograniczona liczba|
|Administrator przedsiębiorstwa (tylko do odczytu)|Nieograniczona liczba|
|Administrator działu|Nieograniczona liczba|
|Administrator działu (tylko do odczytu)|Nieograniczona liczba|
|Właściciel konta|1 na konto<sup>1</sup>|

<sup>1</sup> każde konto wymaga unikatowego konto Microsoft lub konta służbowego.

## <a name="organization-structure-and-permissions-by-role"></a>Struktura i uprawnienia organizacji według roli

|Zadania| Administrator przedsiębiorstwa|Administrator przedsiębiorstwa (tylko do odczytu)|Administrator działu|Administrator działu (tylko do odczytu)|Właściciel konta|
|---|---|---|---|---|---|
|Wyświetl administratorów przedsiębiorstwa|✔|✔|✘|✘|✘|
|Dodawanie lub usuwanie administratorów przedsiębiorstwa|✔|✘|✘|✘|✘|
|Wyświetl kontakty powiadomień<sup>2</sup> |✔|✔|✘|✘|✘|
|Dodawanie lub usuwanie kontaktów powiadomień<sup>2</sup> |✔|✘|✘|✘|✘|
|Tworzenie działów i zarządzanie nimi |✔|✘|✘|✘|✘|
|Wyświetl administratorów działu|✔|✔|✔|✔|✘|
|Dodawanie lub usuwanie administratorów działu|✔|✘|✔|✘|✘|
|Wyświetlanie kont w rejestracji |✔|✔|✔<sup>3</sup>|✔<sup>3</sup>|✘|
|Dodaj konta do rejestracji i Zmień właściciela konta|✔|✘|✔<sup>3</sup>|✘|✘|
|Tworzenie subskrypcji i uprawnień subskrypcji oraz zarządzanie nimi|✘|✘|✘|✘|✔|

- <sup>2</sup> kontakty powiadomień wysyłają wiadomości e-mail dotyczące Umowa Enterprise platformy Azure.
- <sup>3</sup> zadanie jest ograniczone do kont w Twoim dziale.


## <a name="usage-and-costs-access-by-role"></a>Użycie i koszty dostępu według roli

|Zadania| Administrator przedsiębiorstwa|Administrator przedsiębiorstwa (tylko do odczytu)|Administrator działu|Administrator działu (tylko do odczytu) |Właściciel konta|
|---|---|---|---|---|---|
|Wyświetl saldo środków, w tym zobowiązanie pieniężne|✔|✔|✘|✘|✘|
|Wyświetl przydziały wydatków działu|✔|✔|✘|✘|✘|
|Ustawianie przydziałów wydatków działu|✔|✘|✘|✘|✘|
|Wyświetl arkusz cen EA organizacji|✔|✔|✘|✘|✘|
|Wyświetl szczegóły użycia i kosztów|✔|✔|✔<sup>4</sup>|✔<sup>4</sup>|✔<sup>5</sup>|
|Zarządzanie zasobami w Azure Portal|✘|✘|✘|✘|✔|

- <sup>4</sup> wymaga, aby administrator przedsiębiorstwa włączył zasady funkcji **da** w witrynie Enterprise Portal. Administrator działu może następnie zobaczyć szczegóły kosztów dla działu.
- <sup>5</sup> wymaga, aby administrator przedsiębiorstwa włączył zasady **Ao wyświetlają opłaty** w witrynie Enterprise Portal. Właściciel konta może następnie wyświetlić szczegóły kosztu konta.


## <a name="pricing-in-azure-portal"></a>Cennik w Azure Portal

W Azure Portal w zależności od roli administracyjnej i sposobu ustawiania zasad wyświetlania opłat przez administratora przedsiębiorstwa mogą pojawić się różne ceny. Dwie zasady w witrynie Enterprise Portal, które mają wpływ na ceny widoczne w Azure Portal są następujące:

- Opłaty za Podgląd DA
- AO Wyświetl opłaty

Aby dowiedzieć się, jak ustawić te zasady, zobacz [Zarządzanie dostępem do informacji rozliczeniowych na platformie Azure](billing-manage-access.md).

W poniższej tabeli przedstawiono relacje między rolami administratora Umowa Enterprise, zasadami wyświetlania opłat, rolą kontroli dostępu opartej na rolach (RBAC) w Azure Portal i cennikami widocznymi w Azure Portal. Administrator przedsiębiorstwa zawsze widzi szczegóły użycia na podstawie cen umowy EA w organizacji. Jednak administrator działu i właściciel konta zobaczą różne widoki cen w oparciu o zasady wyświetlania opłat i ich rolę RBAC. Rola Administrator działu wymienione w poniższej tabeli odnosi się do ról Administrator działu i administrator działu (tylko do odczytu).

|Umowa Enterprise rolę administratora|Wyświetl zasady opłat dla roli|Rola RBAC|Widok cen|
|---|---|---|---|
|Właściciel konta lub administrator działu|✔ Włączony|Właściciel|Cennik umowy EA organizacji|
|Właściciel konta lub administrator działu|✘ Wyłączone|Właściciel|Ceny detaliczne|
|Właściciel konta lub administrator działu|✔ Włączony |brak|Bez cen|
|Właściciel konta lub administrator działu|✘ Wyłączone |brak|Bez cen|
|Brak|Nie dotyczy |Właściciel|Ceny detaliczne|

Ustaw rolę Administrator przedsiębiorstwa i Wyświetl zasady opłat w witrynie Enterprise Portal. Rolę RBAC można zaktualizować w Azure Portal. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem przy użyciu RBAC i Azure Portal](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie dostępem do informacji dotyczących rozliczeń dla platformy Azure](billing-manage-access.md)
- [Manage access using RBAC and the Azure portal (Zarządzanie dostępem przy użyciu kontroli dostępu opartej na rolach i witryny Azure Portal)](../role-based-access-control/role-assignments-portal.md)
- [Wbudowane role dla zasobów platformy Azure](../role-based-access-control/built-in-roles.md)
