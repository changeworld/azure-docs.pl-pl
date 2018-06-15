---
title: Rozwiązania do zarządzania platformy Azure | Dokumentacja firmy Microsoft
description: Rozwiązania do zarządzania obejmują scenariusze pakietów zarządzania na platformie Azure, która klientów można dodać do swojego obszaru roboczego analizy dzienników.  Ten artykuł zawiera szczegółowe informacje na temat niestandardowych rozwiązań utworzonych przez klientom i partnerom.
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 1f054a4e-6243-4a66-a62a-0031adb750d8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d46b869815fef44a8137bb5121133a1c0140ca30
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2018
ms.locfileid: "29394259"
---
# <a name="working-with-management-solutions-in-azure-preview"></a>Praca z rozwiązaniami do zarządzania na platformie Azure (wersja zapoznawcza)
> [!NOTE]
> To jest wstępna dokumentacji dla rozwiązań zarządzania na platformie Azure, które są obecnie w wersji zapoznawczej.    
> 
> 

Rozwiązania do zarządzania obejmują scenariusze zarządzania spakowanych, dodawanych przez klientów do ich środowiska platformy Azure.  Oprócz [rozwiązań dostarczonych przez firmę Microsoft](../log-analytics/log-analytics-add-solutions.md), partnerów i klientów można tworzyć rozwiązania do zarządzania do użycia w środowisku własnych lub udostępniona użytkownikom za pośrednictwem przez społeczność.

## <a name="finding-and-installing-management-solutions"></a>Znajdowanie i instalowanie rozwiązania do zarządzania
Istnieje wiele metod do lokalizowania i instalowanie rozwiązania do zarządzania, zgodnie z opisem w poniższych sekcjach.

### <a name="azure-marketplace"></a>Azure Marketplace
Rozwiązania do zarządzania są dostarczane przez firmę Microsoft i partnerów zaufanych może być zainstalowana z portalu Azure Marketplace w portalu Azure.

1. Zaloguj się w witrynie Azure Portal.
2. W okienku po lewej stronie wybierz **wszystkie usługi**.
3. Albo przewiń w dół do **rozwiązań** lub typ *rozwiązań* do **filtru** okna dialogowego.
4. Kliknij przycisk **+ Dodaj** przycisku.
5. Wyszukaj rozwiązania, które interesują Cię przez przeglądanie, klikając pozycję **filtru** przycisk lub wprowadzając w **Everthing wyszukiwania** pole.
6. Kliknij element marketplace, aby wyświetlić szczegółowe informacje.
7. Kliknij przycisk **Utwórz** otworzyć **Dodaj rozwiązanie** okienka.
8. Użytkownik otrzyma monit wymaganych informacji takich jak [obszaru roboczego analizy dzienników i konto automatyzacji](#log-analytics-workspace-and-automation-account) oprócz wartości parametrów w rozwiązaniu.
9. Kliknij przycisk **Utwórz** zainstalować rozwiązania.

### <a name="oms-portal"></a>Portalu OMS
Rozwiązania do zarządzania firmy Microsoft mogą być zainstalowane z galerii rozwiązań w portalu OMS.

1. Zaloguj się do portalu OMS.
2. Kliknij przycisk **galerii rozwiązań** kafelka.
3. Na stronie galerii rozwiązań OMS informacje na temat każdego z dostępnych rozwiązań. Kliknij nazwę rozwiązanie, które chcesz dodać.
4. Na stronie dla wybranego rozwiązania są wyświetlane szczegółowe informacje o rozwiązaniu. Kliknij pozycję **Add** (Dodaj).
5. Nowy Kafelek dla rozwiązania, które zostały dodane, pojawi się na przegląd strony w portalu i rozpocząć korzystanie z niej po analizy dzienników przetwarza dane.

### <a name="azure-quickstart-templates"></a>Szablony szybkiego startu platformy Azure
Członków społeczności można przesłać do szablonów Szybki Start Azure rozwiązania do zarządzania.  Możesz pobrać te szablony do nowszej instalacji lub sprawdzić je, aby dowiedzieć się jak [tworzenia własnych rozwiązań](#creating-a-solution).

1. Wykonaj czynności opisane w [obszaru roboczego analizy dzienników i konto automatyzacji](#log-analytics-workspace-and-automation-account) połączyć obszaru roboczego i konta.
2. Przejdź do [szablonów Szybki Start Azure](https://azure.microsoft.com/documentation/templates/).  
3. Wyszukiwanie rozwiązania, które Cię interesują.
4. Wybierz rozwiązanie z wyników, aby wyświetlić jego szczegóły.
5. Kliknij przycisk **wdrażanie na platformie Azure** przycisku.
6. Pojawi się monit o podanie informacji takich jak grupy zasobów i lokalizacji oprócz wartości parametrów w rozwiązaniu.
7. Kliknij przycisk **zakupu** zainstalować rozwiązania.

### <a name="deploy-azure-resource-manager-template"></a>Wdrażanie szablonu usługi Azure Resource Manager
Rozwiązania, które można uzyskać od społeczności lub [samodzielnie utworzyć](#creating-a-solution) są zaimplementowane jako szablon Menedżera zasobów i można użyć dowolnego z standardowe metody [wdrażania szablonu](../azure-resource-manager/resource-group-template-deploy-portal.md).  Należy pamiętać, że przed zainstalowaniem rozwiązania, musisz utworzyć i połączyć [obszaru roboczego analizy dzienników i konto automatyzacji](#log-analytics-workspace-and-automation-account).

## <a name="log-analytics-workspace-and-automation-account"></a>Obszar roboczy analizy dzienników i konta automatyzacji
Większość rozwiązań do zarządzania wymagają [obszaru roboczego analizy dzienników](../log-analytics/log-analytics-manage-access.md) zawiera widoki i [konto automatyzacji](../automation/automation-security-overview.md#automation-account-overview) zawiera elementy runbook i powiązanych zasobów. Obszar roboczy i konto musi spełniać następujące wymagania.

* Rozwiązanie można używać tylko jednego obszaru roboczego analizy dzienników i jedno konto automatyzacji.  
* Obszar roboczy analizy dzienników i automatyzacji konta używanego przez rozwiązanie muszą być połączone z sobą. Obszar roboczy analizy dzienników może odnosić się tylko do jednego konta automatyzacji, a konto usługi Automatyzacja może odnosić się tylko do jednego obszaru roboczego analizy dzienników.
* Połączone, obszar roboczy analizy dzienników i konto automatyzacji musi być w tej samej grupie zasobów i region.  Wyjątek jest obszar roboczy w regionie wschodnie stany USA i i konto usługi Automatyzacja w wschodnie stany USA 2.

### <a name="creating-a-link-between-a-log-analytics-workspace-and-automation-account"></a>Tworzenie połączenia między obszaru roboczego analizy dzienników i konta automatyzacji
Jak można określić obszaru roboczego analizy dzienników i konto usługi Automatyzacja zależy od metody instalacji dla rozwiązania.

* Po zainstalowaniu rozwiązanie firmy Microsoft za pośrednictwem portalu OMS jest zainstalowana w bieżącym obszarze roboczym i żadne konto automatyzacji nie jest wymagane.
* Po zainstalowaniu rozwiązania za pośrednictwem portalu Azure Marketplace, zostanie wyświetlony monit o obszaru roboczego i konto usługi Automatyzacja i jest tworzone łącze między nimi.  
* Rozwiązania poza portalu Azure Marketplace możesz połączyć obszaru roboczego analizy dzienników i konto automatyzacji przed zainstalowaniem rozwiązania.  Można to zrobić, wybranie dowolnego rozwiązania w portalu Azure Marketplace i wybierając obszar roboczy analizy dzienników i konta automatyzacji.  Nie masz faktycznie zainstalować rozwiązania, ponieważ łącze zostanie utworzony, jak zaznaczono obszaru roboczego analizy dzienników i konta automatyzacji.  Po utworzeniu łącza, a następnie można użyć tego obszaru roboczego analizy dzienników i konto automatyzacji dla dowolnego rozwiązania. 

### <a name="verifying-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Sprawdzanie połączenia między obszaru roboczego analizy dzienników i konta automatyzacji
Możesz sprawdzić połączenie między obszaru roboczego analizy dzienników i konto usługi Automatyzacja, korzystając z następującej procedury.

1. Wybierz konto usługi Automatyzacja w portalu Azure.
2. Jeśli **obszaru roboczego** w **powiązane zasoby** części menu jest włączona, a następnie to konto jest dołączony do obszaru roboczego analizy dzienników.  Możesz kliknąć **obszaru roboczego** Aby wyświetlić szczegółowe informacje o obszarze roboczym.

## <a name="listing-management-solutions"></a>Lista rozwiązań do zarządzania
Umożliwia poniższej procedury do wyświetlanie rozwiązania do zarządzania w obszary robocze połączone z subskrypcją platformy Azure.

1. Zaloguj się w witrynie Azure Portal.
2. W okienku po lewej stronie wybierz **wszystkie usługi**.
3. Albo przewiń w dół do **rozwiązań** lub typ *rozwiązań* do **filtru** okna dialogowego.
4. Rozwiązania zainstalowane w wszystkie obszary robocze zostaną wyświetlone.

Należy pamiętać, że można wyświetlić tylko rozwiązania firmy Microsoft zainstalowane w bieżącym obszarze roboczym przy użyciu portalu OMS.

## <a name="removing-a-management-solution"></a>Usuwanie rozwiązania do zarządzania
Rozwiązania do zarządzania zostanie usunięty, również zostaną usunięte wszystkie zasoby w rozwiązaniu.  

1. Znajdź rozwiązania w portalu Azure, korzystając z procedury w [listę rozwiązań](#listing-solutions).
2. Wybierz rozwiązanie, które chcesz usunąć.
3. Kliknij przycisk **usunąć** przycisku.

## <a name="creating-a-management-solution"></a>Tworzenie rozwiązania do zarządzania
Pełne wskazówki dotyczące tworzenia rozwiązań do zarządzania są dostępne pod adresem [tworzenie rozwiązań w Operations Management Suite (OMS)](operations-management-suite-solutions-creating.md). 

## <a name="next-steps"></a>Kolejne kroki
* Wyszukiwanie [szablonów Szybki Start Azure](https://azure.microsoft.com/documentation/templates) przykłady różnych szablonów usługi Resource Manager.
* Utwórz swój własny [rozwiązań do zarządzania](operations-management-suite-solutions-creating.md).

