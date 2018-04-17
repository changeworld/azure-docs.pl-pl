---
title: Utwórz Power BI Embedded pojemności w portalu Azure | Dokumentacja firmy Microsoft
description: W tym artykule przedstawiono sposób tworzenia Power BI Embedded pojemności w Microsoft Azure.
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/28/2017
ms.author: maghan
ms.openlocfilehash: 2fadfde13aee3aaf965c6ba30188544a8a075b9d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="create-power-bi-embedded-capacity-in-the-azure-portal"></a>Utwórz Power BI Embedded pojemności w portalu Azure

W tym artykule przedstawiono sposób tworzenia Power BI Embedded pojemności w Microsoft Azure. Usługa Power BI Embedded upraszcza możliwości usługi Power BI pomaga szybko dodać wspaniałych elementy wizualne, raporty i pulpity nawigacyjne do aplikacji.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

> [!VIDEO https://www.youtube.com/embed/aXrvFfg_iSk]

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby ukończyć ten przewodnik Szybki Start, musisz spełnić następujące warunki:

* **Subskrypcja platformy Azure:** odwiedź [bezpłatnej wersji próbnej Azure](https://azure.microsoft.com/free/) tworzenia konta.
* **Azure Active Directory:** subskrypcji musi być skojarzony z dzierżawy usługi Azure Active Directory (AAD). A ***musisz być zalogowany na platformie Azure przy użyciu konta w tej dzierżawie***. Konta Microsoft nie są obsługiwane. Aby dowiedzieć się więcej, zobacz [Authentication and user permissions (Uwierzytelnianie i uprawnienia użytkownika)](../analysis-services/analysis-services-manage-users.md).
* **Power BI dzierżawy:** co najmniej jedno konto w dzierżawie usługi AAD muszą utworzyli konto usługi Power BI.
* **Grupa zasobów:** Użyj masz już grupę zasobów lub [Utwórz nową](../azure-resource-manager/resource-group-overview.md).

## <a name="create-a-capacity"></a>Utwórz pojemności

1. Zaloguj się do [Azure Portal](https://portal.azure.com/).

2. Wybierz **Utwórz zasób** > **dane i analiza**.

3. W polu wyszukiwania, wyszukaj *Power BI Embedded*.

4. W usłudze Power BI Embedded, wybierz **Utwórz**.

5. Wprowadź wymagane informacje, a następnie wybierz **Utwórz**.

    ![Pola, aby wypełnić do tworzenia nowego miejsca](media/create-capacity/azure-portal-create-power-bi-embedded.png)

    |Ustawienie |Opis |
    |---------|---------|
    |**Nazwa zasobu**|Nazwę identyfikującą pojemność. Nazwa zasobu jest wyświetlana w portalu administracyjnym usługi Power BI oprócz portalu Azure.|
    |**Subskrypcja**|Subskrypcji chcesz utworzyć pojemności przed.|
    |**Grupa zasobów**|Grupy zasobów zawierającej tego nowego miejsca. Wybierz z istniejącej grupy zasobów lub utworzyć inną. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).|
    |**Power BI pojemności administratora**|Administratorzy programu Power BI pojemności wyświetlać pojemność w portalu administracyjnym usługi Power BI i nadaj przypisanie uprawnienia do innych użytkowników. Domyślnie Twoje konto jest administrator pojemności. Administrator pojemność musi być w dzierżawie usługi Power BI.|
    |**Lokalizacja**|Lokalizacja, gdzie jest hostowana usługa Power BI dla Twojej dzierżawy. To ustawienie zostanie rozwiązany automatycznie, nie można wybrać inną lokalizację.|
    |**Warstwa cenowa**|Wybierz SKU (rozmiar pamięci i liczba rdzeni v), które spełni wymagania organizacji.  Aby uzyskać więcej informacji, zobacz [Power BI Embedded — cennik](https://azure.microsoft.com/pricing/details/power-bi-embedded/)|

6. Wybierz pozycję **Utwórz**.

Tworzenie potrzebuje zazwyczaj w obszarze chwilę. często tylko kilka sekund. W przypadku wybrania **Przypnij do pulpitu nawigacyjnego**, przejdź do pulpitu nawigacyjnego, aby zobaczyć nowe możliwości. Lub przejdź do **wszystkie usługi** > **Power BI Embedded** czy wydajność jest gotowy.

![Pulpitu nawigacyjnego portalu Azure w usłudze Power BI Embedded pojemności](media/create-capacity/azure-portal-dashboard.png)

## <a name="next-steps"></a>Kolejne kroki

Aby użyć nowego miejsca do Power BI Embedded, przejdź do portalu administracyjnego usługi Power BI, aby przypisać obszarów roboczych. Aby uzyskać więcej informacji, zobacz [Manage capacities within Power BI Premium and Power BI Embedded (Zarządzanie pojemnościami w usługach Power BI Premium i Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-admin-premium-manage/).

Jeśli nie trzeba używać tej pojemności, zatrzymaj ją zatrzymać, rozliczeń. Aby uzyskać więcej informacji, zobacz [Wstrzymaj i uruchomić Power BI Embedded wydajność w portalu Azure](pause-start.md).

Aby rozpocząć, osadzanie zawartość usługi Power BI w aplikacji, zobacz [jak osadzić usługi Power BI pulpity nawigacyjne, raporty i programu Kafelki](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/).

Masz więcej pytań? [Spróbuj skorzystać z społeczności Power BI](http://community.powerbi.com/)