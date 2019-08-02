---
title: Przewodnik przechodzenia do analizy kodu zabezpieczeń Microsoft Azure
description: Ten artykuł zawiera informacje o instalowaniu rozszerzenia analizy kodu zabezpieczającego
author: vharindra
manager: sukhans
ms.author: terrylan
ms.date: 07/31/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 8a8eca73205d4f8f33d4d069fda72638af61d355
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68718303"
---
# <a name="how-to-onboarding-and-installing"></a>Instrukcje: Dołączanie i Instalowanie

Wymagania wstępne umożliwiające rozpoczęcie pracy z analizą kodu zabezpieczeń firmy Microsoft
  - Oferta kwalifikujących się usług Microsoft Unified Support Services (szczegóły poniżej)
  - Organizacja usługi Azure DevOps
  - Uprawnienia do instalowania rozszerzeń w organizacji usługi Azure DevOps
  - Kod źródłowy, który można synchronizować z potokiem usługi Azure DevOps hostowanym w chmurze


## <a name="onboarding-microsoft-security-code-analysis-extension"></a>Rozszerzenie analizy kodu zabezpieczeń firmy Microsoft

- Jeśli masz już jedną z następujących ofert pomocy technicznej, skontaktuj się z administratorem konta technicznego i purchase\swap istniejące godziny, aby uzyskać dostęp do rozszerzenia.
   - Ujednolicona obsługa — warstwa zaawansowane i wydajność, pomoc techniczna Premier dla deweloperów, pomoc techniczna Premier dla partnerów lub pomoc techniczna Premier dla przedsiębiorstw.
- Jeśli masz jedną z następujących usług pomocy technicznej lub nie masz planu pomocy technicznej w firmie Microsoft, musisz przeprowadzić uaktualnienie do uprawnionej oferty pomocy technicznej:
   - Pomoc techniczna platformy Azure dla partnerów, Azure Basic, Azure Developer, Azure Standard, Azure Professional Direct lub Unified support — warstwa podstawowa
- Aby kupić uprawniającą ofertę pomocy technicznej, odwiedź naszą [stronę główną usług pomocy technicznej](https://www.microsoft.com/enterprise/services/support)
- Po zakończeniu kontraktu pomocy technicznej skontaktuj się z kierownikiem ds. klientów, który może pomóc Ci rozpocząć pracę i zebrać wszystkie wymagane szczegóły.
 
>[!NOTE]
> Tylko zarejestrowani partnerzy w sieci partnerów firmy Microsoft kwalifikują się do kupowania pomoc techniczna Premier dla partnerów. w przeciwnym razie Kup jedną z kwalifikujących się ofert pomocy technicznej wymienionych wcześniej

## <a name="installing-microsoft-security-code-analysis-extension"></a>Instalowanie rozszerzenia Microsoft Security code Analysis

1. Po udostępnieniu rozszerzenia organizacji usługi Azure DevOps przejdź do strony organizacji usługi Azure DevOps (na przykład http://dev.azure.com/contoso)
2. Kliknij ikonę torba zakupów w prawym górnym rogu obok nazwy, a następnie kliknij pozycję Zarządzaj rozszerzeniami 
3. Kliknij rozszerzenie Microsoft Security code Analysis, a następnie uruchom Kreatora interfejsu użytkownika usługi Azure DevOps, aby rozpocząć instalację.
4. Wybierz organizację DevOps platformy Azure, aby zainstalować rozszerzenie na podstawie listy rozwijanej
5. Kliknij przycisk Instaluj. Po jego zakończeniu można korzystać z rozszerzenia

>[!NOTE]
> Nawet jeśli nie masz dostępu, Kontynuuj kroki instalacji i będziesz mieć możliwość żądania dostępu od administratora organizacji usługi Azure DevOps w trakcie tego procesu.
>
Po zainstalowaniu rozszerzenia zadania tworzenia bezpiecznego rozwoju będą widoczne i dostępne do dodania do Azure Pipelines.

## <a name="adding-specific-build-tasks-to-your-devops-pipeline"></a>Dodawanie określonych zadań kompilacji do potoku DevOps

1. Otwórz projekt zespołowy z poziomu organizacji usługi Azure DevOps.
2. Przejdź do karty **kompilacje** w obszarze potoki 
3. Wybierz potok, do którego chcesz dodać zadania kompilacji rozszerzenia. 
   - Nowy — kliknij pozycję **Nowy** , a następnie wykonaj kroki opisane w sekcji Tworzenie nowego potoku.
   - Edytuj — wybierz potok, a następnie kliknij przycisk **Edytuj** , aby rozpocząć edytowanie istniejącego potoku.
4. Kliknij przycisk +, aby przejść do okienka **Dodawanie zadań** .
5. Znajdź zadanie kompilacji do dodania z listy lub przy użyciu pola wyszukiwania, a następnie kliknij przycisk **Dodaj**. 
6. Teraz można nadal określić parametry potrzebne do wykonania zadania.
>[!NOTE]
>Ścieżki pliku lub katalogu są względne dla katalogu głównego repozytorium źródłowego i parametry określające, że folder wyjściowy/pliki zostaną zastąpione wspólną lokalizacją zdefiniowaną w agencie kompilacji.

7. Utwórz nową kompilację w kolejce.
> [!TIP]
>  - Aby uruchomić analizę po kompilacji, umieść zadania kompilacji analizy kodu zabezpieczeń firmy Microsoft po kroku publikowanie artefaktów kompilacji. Dzięki temu kompilacja może zakończyć i publikować wyniki przed uruchomieniem narzędzi do analizy statycznej.
>  - Zawsze sprawdzaj opcję **"Kontynuuj przy błędzie"** w przypadku zadań związanych z tworzeniem kompilacji. Nawet jeśli jedno z narzędzi nie powiedzie się, inne mogą działać. Brak współzależności.
>  - Zadania kompilacji analizy kodu zabezpieczeń firmy Microsoft będą kończyć się niepowodzeniem tylko wtedy, gdy uruchomienie narzędzia nie powiedzie się, ale w przypadku, gdy narzędzie zidentyfikuje problemy w kodzie, nie powiedzie się. Możesz skonfigurować kompilację do przerwania, gdy narzędzie zidentyfikuje problemy w kodzie przy użyciu zadania kompilacji **po analizie** .
>  - Niektóre zadania kompilacji DevOps platformy Azure nie są obsługiwane, jeśli są uruchamiane za pośrednictwem potoku "Release". Jest to ograniczenie usługi Azure DevOps. Nie obsługują one zadań, które publikują artefakty z poziomu potoku wydania.
>  - Aby uzyskać listę wstępnie zdefiniowanych zmiennych w kompilacji zespołu usługi Azure DevOps, którą można określić jako parametry, zobacz [zmienne kompilacji DevOps platformy Azure](https://docs.microsoft.com/azure/devops/pipelines/build/variables?tabs=batch&view=vsts)

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat konfigurowania zadań kompilacji, zobacz nasze [Przewodnik po konfiguracji](security-code-analysis-customize.md)

Jeśli masz więcej pytań na temat rozszerzenia i oferowanych narzędzi, zapoznaj się z [naszą stroną często zadawanych pytań.](security-code-analysis-faq.md)


