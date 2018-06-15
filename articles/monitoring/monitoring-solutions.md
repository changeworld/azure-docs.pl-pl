---
title: Rozwiązania do zarządzania na platformie Azure | Dokumentacja firmy Microsoft
description: Rozwiązania do zarządzania na platformie Azure są Kolekcja reguł nabycia logiki, wizualizacji i danych zawierających metryki przestawiać wokół obszaru określonego problemu.  Ten artykuł zawiera informacje na temat instalowania i korzystania z rozwiązań zarządzania.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: f029dd6d-58ae-42c5-ad27-e6cc92352b3b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: bwren
ms.openlocfilehash: 0df54d1758693bce5fb5fd74c3be9c4cfd7dccb6
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
ms.locfileid: "33885272"
---
# <a name="management-solutions-in-azure"></a>Rozwiązania do zarządzania na platformie Azure
Rozwiązania do zarządzania korzystać z usług Azure, aby zapewnić dodatkowy wgląd w funkcjonowanie określonej aplikacji lub usługi. Ten artykuł zawiera krótki przegląd rozwiązań do zarządzania Azure i uzyskać szczegółowe informacje na i ich instalowania.

Rozwiązania do zarządzania zwykle zbierać informacje do analizy dzienników i podaj widoków do analizowania danych zebranych i dziennik wyszukiwania. Mogą one również korzystać z innych usług, takich jak usługi Automatyzacja Azure do wykonywania akcji związanych z aplikacji lub usługi.

Rozwiązania do zarządzania można dodać do subskrypcji platformy Azure dla dowolnej aplikacji i usług, których używasz. Te są zwykle dostępne w żadnych kosztów, ale zbieranie danych, które może wywołać opłaty za użycie. Oprócz rozwiązań dostarczonych przez firmę Microsoft, partnerów i klientów można [tworzenia rozwiązań do zarządzania](../monitoring/monitoring-solutions-creating.md) do użycia w środowisku własnych lub udostępniona użytkownikom za pośrednictwem przez społeczność.

## <a name="using-management-solutions"></a>Przy użyciu rozwiązań do zarządzania
**Omówienie** strony dla każdego obszaru roboczego analizy dzienników Wyświetla kafelków dla każdego rozwiązania zainstalowane w obszarze roboczym. Kliknij Kafelek dla rozwiązania, aby otworzyć jego widoku, który zawiera bardziej szczegółowe analizy jego zebranych danych.

![Przegląd](media/monitoring-solutions/overview.png)

Rozwiązania do zarządzania może zawierać wiele typów zasobów platformy Azure i można wyświetlić wszystkie zasoby dołączone do rozwiązania, podobnie jak w innych zasobów. Na przykład żadnych wyszukiwań dziennika zawarty w rozwiązaniu są dołączone **zapisane wyszukiwania** w obszarze roboczym. Podczas przeprowadzania analizy ad hoc w module dziennika analiz, można użyć wyszukiwania.

## <a name="list-installed-management-solutions"></a>Wyświetlanie listy zainstalowanych rozwiązań do zarządzania 
Użyj poniższej procedury, aby wyświetlić listę rozwiązań zarządzania zainstalowany w ramach subskrypcji.

1. Zaloguj się w witrynie Azure Portal.
2. W okienku po lewej stronie wybierz **wszystkie usługi**.
3. Albo przewiń w dół do **rozwiązań** lub typ *rozwiązań* do **filtru** okna dialogowego.
4. Rozwiązania zainstalowane w wszystkie obszary robocze są wyświetlane. Nazwa rozwiązania następuje nazwa obszaru roboczego analizy dzienników, który jest zainstalowana.
1. Użyj pola listy rozwijanej w górnej części ekranu, aby filtrować według subskrypcji lub grupy zasobów.


![Wyświetl listę wszystkich rozwiązań](media/monitoring-solutions/list-solutions-all.png)

Kliknij nazwę rozwiązania, aby otworzyć jej stronę podsumowania. Ta strona wyświetla wszystkie widoki analizy dzienników zawarty w rozwiązaniu i zapewnia różne opcje dla rozwiązania się i obszaru roboczego. Wyświetl stronę podsumowania rozwiązania przy użyciu jednej z powyższych procedur do listy rozwiązań, a następnie kliknij nazwę rozwiązania.

![Właściwości rozwiązania](media/monitoring-solutions/solution-properties.png)


## <a name="find-management-solutions"></a>Znajdź rozwiązania do zarządzania
Można przeglądanie i instalowanie dostępnych rozwiązań do zarządzania firmy Microsoft i partnerów w [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace). Wykonaj [Wyszukaj *rozwiązań do zarządzania* ](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions) Filtruj rozwiązań do zarządzania, a następnie kliknij przycisk dowolny element, aby uzyskać więcej szczegółów.

![Portal Marketplace](media/monitoring-solutions/marketplace.png)

## <a name="install-a-management-solution"></a>Instalowanie rozwiązania do zarządzania

### <a name="install-a-management-solution-from-the-azure-marketplace"></a>Instalowanie rozwiązania do zarządzania z poziomu portalu Azure Marketplace
Można użyć dowolnej z następujących metod do lokalizowania i rozpocząć instalację rozwiązania do zarządzania.

- Kliknij przycisk **Pobierz teraz** na rozwiązanie do zarządzania w [portalu Azure Marketplace](#find-management-solutions).
- Z [listę rozwiązań dla Twojej subskrypcji](#list-installed-management-solutions), kliknij przycisk **Dodaj**. Po prawej stronie **rozwiązań do zarządzania**, kliknij przycisk **więcej**. Znajdź rozwiązania do zarządzania i kliknij przycisk **Utwórz**.
- W portalu Azure wybierz **Utwórz zasób** > **monitorowanie i zarządzanie** > **zobaczyć wszystkie**. Po prawej stronie **rozwiązań do zarządzania**, kliknij przycisk **więcej**. Znajdź rozwiązania do zarządzania i kliknij przycisk **Utwórz**.

Podczas uruchamiania procesu instalacji, zostanie wyświetlony monit o podanie wymaganej konfiguracji, który różni się każde rozwiązanie. Wszystkie z nich wymaga wybierz obszar roboczy analizy dzienników zainstalowaną rozwiązania i gdzie będą zbierane dane. Może być również konieczne [Określ konto usługi automatyzacja](#log-analytics-workspace-and-automation-account) Jeśli wymagane w rozwiązaniu.

### <a name="install-a-solution-from-the-community"></a>Instalowanie rozwiązania przez społeczność
Członków społeczności można przesłać do szablonów Szybki Start Azure rozwiązania do zarządzania. Można zainstalować te rozwiązania bezpośrednio lub je pobrać szablonów dla nowszej instalacji.

1. Wykonaj czynności opisane w [obszaru roboczego analizy dzienników i konto automatyzacji](#log-analytics-workspace-and-automation-account) połączyć obszaru roboczego i konta.
2. Przejdź do [szablonów Szybki Start Azure](https://azure.microsoft.com/documentation/templates/). 
3. Wyszukiwanie rozwiązania, które Cię interesują.
4. Wybierz rozwiązanie z wyników, aby wyświetlić jego szczegóły.
5. Kliknij przycisk **wdrażanie na platformie Azure** przycisku.
6. Zostanie wyświetlony monit o podanie informacje, takie jak grupy zasobów i lokalizacji oprócz wartości parametrów w rozwiązaniu.
7. Kliknij przycisk **zakupu** zainstalować rozwiązania.


## <a name="log-analytics-workspace-and-automation-account"></a>Obszar roboczy analizy dzienników i konta automatyzacji
Wszystkie rozwiązania do zarządzania wymagają [obszaru roboczego analizy dzienników](../log-analytics/log-analytics-manage-access.md) do przechowywania danych zbieranych przez rozwiązanie i hosta, widoków i dziennik wyszukiwania. Niektóre rozwiązania również wymagają [konto automatyzacji](../automation/automation-security-overview.md#automation-account-overview) zawiera elementy runbook i powiązanych zasobów. Obszar roboczy i konto musi spełniać następujące wymagania.

* Każda instalacja rozwiązania można używać tylko jednego obszaru roboczego analizy dzienników i jedno konto automatyzacji. Rozwiązanie można zainstalować oddzielnie na wiele obszarów roboczych.
* Jeśli rozwiązanie wymaga konto usługi Automatyzacja, następnie obszaru roboczego analizy dzienników i konto automatyzacji musi być połączony ze sobą. Obszar roboczy analizy dzienników może odnosić się tylko do jednego konta automatyzacji, a konto usługi Automatyzacja może odnosić się tylko do jednego obszaru roboczego analizy dzienników.
* Połączone, obszar roboczy analizy dzienników i konto automatyzacji musi być w tej samej grupie zasobów i region. Wyjątkiem jest obszar roboczy w regionie wschodnie stany USA, a konto usługi Automatyzacja w wschodnie stany USA 2.

### <a name="creating-a-link-between-a-log-analytics-workspace-and-automation-account"></a>Tworzenie połączenia między obszaru roboczego analizy dzienników i konta automatyzacji
Jak można określić obszaru roboczego analizy dzienników i konto usługi Automatyzacja zależy od metody instalacji dla rozwiązania.

* Po zainstalowaniu rozwiązania za pośrednictwem portalu Azure Marketplace, zostanie wyświetlony monit dla obszaru roboczego i konto usługi Automatyzacja. Połączenia między nimi jest tworzony, jeśli ich nie są już połączone.
* Rozwiązania poza portalu Azure Marketplace możesz połączyć obszaru roboczego analizy dzienników i konto automatyzacji przed zainstalowaniem rozwiązania. Można to zrobić, wybranie dowolnego rozwiązania w portalu Azure Marketplace i wybierając obszar roboczy analizy dzienników i konta automatyzacji. Nie masz faktycznie zainstalować rozwiązania, ponieważ link jest tworzony, jak zaznaczono obszaru roboczego analizy dzienników i konta automatyzacji. Po utworzeniu łącza, a następnie można użyć tego obszaru roboczego analizy dzienników i konto automatyzacji dla dowolnego rozwiązania.

### <a name="verifying-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Sprawdzanie połączenia między obszaru roboczego analizy dzienników i konta automatyzacji
Możesz sprawdzić połączenie między obszaru roboczego analizy dzienników i konto usługi Automatyzacja, korzystając z następującej procedury.

1. Wybierz konto usługi Automatyzacja w portalu Azure.
1. Przewiń do **powiązane zasoby** części menu.
1. Jeśli **obszaru roboczego** ustawienie jest włączone, a następnie to konto jest połączone z obszaru roboczego analizy dzienników. Możesz kliknąć **obszaru roboczego** Aby wyświetlić szczegółowe informacje o obszarze roboczym.

## <a name="remove-a-management-solution"></a>Usuń rozwiązanie do zarządzania
Aby usunąć zainstalowane rozwiązanie, zlokalizuj je w [listę zainstalowanych rozwiązań](#list-installed-management-solutions). Kliknij nazwę rozwiązania, aby otworzyć jej stronę podsumowania, a następnie kliknij polecenie **usunąć**.




## <a name="next-steps"></a>Kolejne kroki
* Pobierz [listę rozwiązań do zarządzania firmy Microsoft](monitoring-solutions-inventory.md).
* Dowiedz się, jak [tworzenie zapytań](../log-analytics/log-analytics-log-searches.md) do analizowania danych zebranych przez rozwiązania do zarządzania.

