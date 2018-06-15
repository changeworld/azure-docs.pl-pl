---
title: Tworzyć i dostosowywać plany odzyskiwania dla trybu failover i odzyskiwania w usłudze Azure Site Recovery | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzyć i dostosowywać plany odzyskiwania w usłudze Azure Site Recovery. W tym artykule opisano sposób pracy awaryjnej i odzyskiwania maszyn wirtualnych i serwerów fizycznych.
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/21/2018
ms.author: raynew
ms.openlocfilehash: e02672ea76eada2d660b20f91c4417019d4efc97
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
ms.locfileid: "30189838"
---
# <a name="create-and-customize-recovery-plans"></a>Tworzyć i dostosowywać plany odzyskiwania

W tym artykule opisano sposób tworzenia i dostosowywania planu odzyskiwania w [usługi Azure Site Recovery](site-recovery-overview.md). Przed rozpoczęciem [więcej](recovery-plan-overview.md) o planach odzyskiwania.

## <a name="create-a-recovery-plan"></a>Tworzenie planu odzyskiwania

1. W magazynie usług odzyskiwania, wybierz **plany odzyskiwania (lokacji odzyskiwania)** > **+ planu odzyskiwania**.
2. W **Utwórz plan odzyskiwania**, określ nazwę planu.
3. Wybierz źródło i cel oparte na maszynach w planie, a następnie wybierz **Resource Manager** modelu wdrażania. Lokalizacja źródłowa musi mieć maszyny, które są włączone dla trybu failover i odzyskiwania. 

   **Tryb failover** | **Źródło** | **docelowy** 
   --- | --- | ---
   Azure–Azure | Region platformy Azure |Region platformy Azure
   VMware do platformy Azure | Serwer konfiguracji | Azure
   Maszyn fizycznych do platformy Azure | Serwer konfiguracji | Azure   
   Funkcja Hyper-V zarządzanych przez program VMM na platformie Azure  | Nazwa wyświetlana programu VMM | Azure
   Funkcja Hyper-V bez programu VMM na platformie Azure | Nazwa lokacji funkcji Hyper-V | Azure
   Program VMM do programu VMM |Przyjazna nazwa programu VMM | Nazwa wyświetlana programu VMM 

   > [!NOTE]
   > Plan odzyskiwania może zawierać maszyn o tej samej źródłowych i docelowych. VMware i maszyn wirtualnych funkcji Hyper-V zarządzanych przez program VMM nie może należeć do tego samego planu. Maszyny wirtualne VMware i serwerów fizycznych mogą mieć tego samego planu, której źródłem jest serwer konfiguracji.

2. W **wybierz elementy maszyn wirtualnych**, wybierz komputery (lub grupy replikacji), które mają zostać dodane do planu. Następnie kliknij przycisk **OK**.
    - Komputery są dodawane domyślnej grupy (Grupa 1) w planie. Po przejściu w tryb failover wszystkie maszyny w tej grupie są uruchamiane w tym samym czasie.
    - Można wybrać tylko komputery są w lokalizacji źródłowej i docelowej, określone przez użytkownika. 
1. Kliknij przycisk **OK** Aby utworzyć plan.

## <a name="add-a-group-to-a-plan"></a>Dodaj grupę do planu

Możesz utworzyć dodatkowe grupy i Dodaj maszyny do różnych grup, dzięki czemu można określić różne zachowanie na podstawie grupy przez grupa. Na przykład można określić, gdy maszyny w grupie należy uruchomić po trybu failover, lub określić niestandardowe akcje dla grupy.

1. W **plany odzyskiwania**, kliknij prawym przyciskiem myszy planu > **Dostosuj**. Domyślnie po utworzeniu planu maszyn, które dodano do niego są wszystkie znajdujące się w domyślnym grupy 1.
2. Kliknij przycisk **+ grupy**. Domyślnie nowa grupa jest numerowane w kolejności, w którym został dodany. Może mieć maksymalnie siedem grup.
3. Wybierz maszynę, aby przejść do nowej grupy, kliknij przycisk **Zmień grupę**, a następnie wybierz nową grupę. Alternatywnie kliknij prawym przyciskiem myszy nazwę grupy > **chronionego elementu**i Dodaj maszyny do grupy. Maszyny lub replikacji grupy może wyłącznie należeć do jednej grupy w planie odzyskiwania.


## <a name="add-a-script-or-manual-action"></a>Dodaj akcję skryptu lub ręcznie

Plan odzyskiwania można dostosować, dodając skryptu lub akcji ręcznej. Należy pamiętać, że:

- Jeśli przeprowadzasz replikację do platformy Azure można zintegrować elementy runbook automatyzacji Azure planu odzyskiwania. [Dowiedz się więcej](site-recovery-runbook-automation.md).
- Jeśli przeprowadzasz replikację maszyn wirtualnych funkcji Hyper-V zarządzanych przez program System Center VMM, można utworzyć skrypt na serwerze programu VMM lokalnie i dodane do planu odzyskiwania.
- Po dodaniu skrypt dodaje nowy zestaw akcje dla grupy. Na przykład zestaw kroków wstępnego dla grupy 1 jest tworzony z nazwą *Grupa 1: kroki przed*. Wszystkie kroki wstępne są wymienione w tym zestawie. Skrypt w lokacji głównej można dodać tylko wtedy, gdy jest wdrożony serwer programu VMM.
- Jeśli dodasz akcji ręcznej, po uruchomieniu planu odzyskiwania, zatrzymuje się na punkt, jaką dodaje akcji ręcznej. Okno dialogowe zostanie wyświetlony monit o określenie, że akcja ręczna została zakończona.
- Aby utworzyć skrypt na serwerze programu VMM, postępuj zgodnie z instrukcjami [w tym artykule](hyper-v-vmm-recovery-script.md).
- Skrypty można zastosować w trybie failover do lokacji dodatkowej, a podczas powrotu po awarii w lokacji dodatkowej do podstawowych. Obsługa zależy od danego scenariusza replikacji:
    
    **Scenariusz** | **Tryb failover** | **Powrót po awarii**
    --- | --- | --- 
    Azure–Azure  | Element Runbook | Element Runbook
    Z programu VMware do platformy Azure | Element Runbook | Nie dotyczy 
    Funkcja Hyper-V w programie VMM na platformie Azure | Element Runbook | Skrypt
    Z lokacji funkcji Hyper-V na platformę Azure | Element Runbook | Nie dotyczy
    Menedżer maszyny Wirtualnej do dodatkowej programu VMM | Skrypt | Skrypt

1. W planie odzyskiwania, kliknij ten krok, do którego ma zostać dodany akcji i określ, kiedy powinny występować Akcja:. Jeśli akcja została wykonana przed maszyn w grupie są uruchamiane po pracy w trybie failover zaznacz **dodać akcję przed**.
    b. Akcja została wykonana po maszyn w grupie start po pracy awaryjnej, zaznacz **post dodać akcję**. Aby przemieścić akcji, wybierz **Przenieś w górę** lub **Przenieś w dół** przycisków.
2. W **Wstaw akcji**, wybierz pozycję **skryptu** lub **akcji ręcznej**.
3. Jeśli chcesz dodać działania ręczne, wykonaj następujące czynności". Wpisz nazwę dla akcji, a następnie wpisz w instrukcjach akcji. Tryb failover uruchamiających będą widzieć te instrukcje.
    b. Określ, czy chcesz dodać akcji ręcznej dla wszystkich typów pracy awaryjnej (Test trybu Failover, planowane failover (jeśli dotyczy)). Następnie kliknij przycisk **OK**.
4. Jeśli chcesz dodać skrypt, wykonaj następujące czynności:. W przypadku dodawania skryptów programu VMM, wybierz **trybu Failover do skryptu programu VMM**, a następnie w w **ścieżka skryptu** wpisz względną ścieżkę do udziału. Na przykład jeśli udział znajduje się pod adresem \\ <VMMServerName>\MSSCVMMLibrary\RPScripts, określ ścieżkę: \RPScripts\RPScript.PS1.
    b. W przypadku dodawania usługi Automatyzacja Azure Uruchom książki, określ **konto usługi Automatyzacja Azure** w którym znajduje się element runbook i wybierz odpowiednie **skrypt Runbook Azure**.
5. Uruchom test trybu failover planu odzyskiwania, aby upewnić się, że skrypt działa zgodnie z oczekiwaniami.

## <a name="watch-a-video"></a>Obejrzyj film

Obejrzyj film wideo przedstawiający sposób tworzenia planu odzyskiwania.


> [!VIDEO https://www.youtube.com/embed/1KUVdtvGqw8]

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [uruchamiania trybu failover](site-recovery-failover.md).  

    
