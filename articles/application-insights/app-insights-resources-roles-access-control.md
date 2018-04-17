---
title: Kontrolę zasobów, ról i dostępu w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: Właściciele, współautorzy i czytelnicy wgląd w organizacji.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 49f736a5-67fe-4cc6-b1ef-51b993fb39bd
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: mbullwin
ms.openlocfilehash: 5bb6c33c083e1be690b70b63ca087c923d43f3ee
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="resources-roles-and-access-control-in-application-insights"></a>Zasoby, ról i kontroli dostępu w usłudze Application Insights
Można kontrolować, kto ma odczytywanie i aktualizowanie dostępu do danych na platformie Azure [usługi Application Insights][start], za pomocą [kontroli dostępu opartej na rolach w systemie Microsoft Azure](../role-based-access-control/role-assignments-portal.md).

> [!IMPORTANT]
> Przypisywanie dostępu dla użytkowników w **grupy zasobów lub subskrypcji** do której należy zasób aplikacji — nie w zasobie samej siebie. Przypisz **współautora składników usługi Application Insights** roli. Dzięki temu uniform kontroli dostępu do testów sieci web i alerty wraz z zasobu aplikacji. [Dowiedz się więcej](#access).
> 
> 

## <a name="resources-groups-and-subscriptions"></a>Zasoby, grup i subskrypcji
Pierwszy, definicje:

* **Zasób** — wystąpienie usługi Microsoft Azure. Zasób usługi Application Insights gromadzi, analizuje i wyświetla danych telemetrycznych wysłanych z aplikacji.  Inne typy zasobów platformy Azure obejmują aplikacje sieci web, bazy danych i maszyn wirtualnych.
  
    Aby wyświetlić zasoby, otwórz [Azure Portal][portal], zaloguj się i kliknij pozycję wszystkie zasoby. Aby znaleźć zasobu, wpisz część nazwy w polu filtru.
  
    ![Lista zasobów platformy Azure](./media/app-insights-resources-roles-access-control/10-browse.png)

<a name="resource-group"></a>

* [**Grupa zasobów** ] [ group] — każdy zasób należy do jednej grupy. Grupa jest wygodnym sposobem zarządzania powiązane zasoby, szczególnie dla kontroli dostępu. Na przykład w jednej grupy zasobów można umieścić w aplikacji sieci Web, zasobu usługi Application Insights do monitorowania aplikacji i zasobów magazynu, należy wyeksportowane dane.

    ![Kliknij przycisk Przeglądaj, grupy zasobów, a następnie wybierz grupę](./media/app-insights-resources-roles-access-control/11-group.png)

* [**Subskrypcja** ](https://portal.azure.com) — Aby używać usługi Application Insights lub innych zasobów platformy Azure, zaloguj się do subskrypcji platformy Azure. Każda grupa zasobów należy do jednej subskrypcji platformy Azure, które wybierz pakiet ceny i, jeśli jest subskrypcji organizacji wybrać członków i uprawnień dostępu.
* [**Konto Microsoft** ] [ account] — nazwa użytkownika i hasło, którego używasz do logowania się na Microsoft Azure subskrypcji, XBox Live, Outlook.com i innych usług firmy Microsoft.

## <a name="access"></a> Kontroli dostępu w grupie zasobów
Należy zrozumieć, oprócz zasobów utworzonej dla aplikacji, czy też oddzielne zasoby ukryte alertów i testy sieci web. Są one dołączone do tej samej [grupy zasobów](#resource-group) jako aplikacji. Może również umieszczono innymi usługami Azure w nim, takich jak magazyny lub witryn sieci Web.

![Zasoby w usłudze Application Insights](./media/app-insights-resources-roles-access-control/00-resources.png)

Aby kontrolować dostęp do tych zasobów w związku z tym zaleca się:

* Kontrola dostępu w **grupy zasobów lub subskrypcji** poziom.
* Przypisz **współautora Application Insights składnika** roli do użytkowników. Umożliwia im to edytować testy sieci web, alerty i zasobów usługi Application Insights, bez konieczności podawania dostępu do innych usług w grupie.

## <a name="to-provide-access-to-another-user"></a>Aby zapewnić dostęp do innego użytkownika
Musi mieć prawa właściciela subskrypcji lub grupy zasobów.

Użytkownik musi mieć [Account Microsoft][account], lub dostęp do ich [Account Microsoft organizacji](../active-directory/sign-up-organization.md). Można zapewnić dostęp do osób, a także do grup użytkowników zdefiniowanych w usłudze Azure Active Directory.

#### <a name="navigate-to-the-resource-group"></a>Przejdź do grupy zasobów
Dodaj użytkownika istnieje.

![W bloku zasobów aplikacji otwórz Essentials, otwórz grupę zasobów, a wybierz ustawienia/użytkowników. Kliknij pozycję Add (Dodaj).](./media/app-insights-resources-roles-access-control/01-add-user.png)

Lub może do góry innego poziomu i dodać użytkownika do subskrypcji.

#### <a name="select-a-role"></a>Wybieranie roli
![Wybierz rolę dla nowego użytkownika](./media/app-insights-resources-roles-access-control/03-role.png)

| Rola | W grupie zasobów |
| --- | --- |
| Właściciel |Można zmienić wszystko, w tym dostępu użytkownika |
| Współautor |Można edytować wszystko, w tym wszystkie zasoby |
| Współautor Insights składnika aplikacji |Można edytować zasobów usługi Application Insights, testy sieci web i alerty |
| Czytelnik |Można wyświetlać, ale nie zmienia sytuacji |

"Edycja" obejmuje tworzenie, usuwanie i aktualizowanie:

* Zasoby
* Testy sieci Web
* Alerty
* Eksport ciągły

#### <a name="select-the-user"></a>Wybierz użytkownika

Jeśli użytkownik, który ma nie znajduje się w katalogu, możesz poprosić każda osoba z kontem Microsoft.
(Jeśli korzystają z usług takich jak Outlook.com, OneDrive, Windows Phone lub XBox Live, mają konta Microsoft.)

## <a name="related-content"></a>Zawartość pokrewna

* [Oparta na rolach kontrola dostępu na platformie Azure](../role-based-access-control/role-assignments-portal.md)

<!--Link references-->

[account]: https://account.microsoft.com
[group]: ../azure-resource-manager/resource-group-overview.md
[portal]: https://portal.azure.com/
[start]: app-insights-overview.md
