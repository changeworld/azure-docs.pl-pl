---
title: Tworzenie/dostosowywanie planów odzyskiwania w usłudze Azure Site Recovery
description: Dowiedz się, jak tworzyć i dostosowywać plany odzyskiwania po awarii w celu odzyskiwania po awarii za pomocą usługi Azure Site Recovery.
ms.topic: how-to
ms.date: 01/23/2020
ms.openlocfilehash: 6540317324a9f0d9bccc046ecf95824d4128bd09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76705840"
---
# <a name="create-and-customize-recovery-plans"></a>Tworzenie i dostosowywanie planów odzyskiwania

W tym artykule opisano sposób tworzenia i dostosowywania planu odzyskiwania do pracy awaryjnej w [usłudze Azure Site Recovery.](site-recovery-overview.md) Zanim zaczniesz, [dowiedz się więcej](recovery-plan-overview.md) o planach odzyskiwania.

## <a name="create-a-recovery-plan"></a>Tworzenie planu odzyskiwania

1. W przechowalni usług odzyskiwania wybierz pozycję **Plany odzyskiwania (odzysk witryny)** > **+Plan odzyskiwania**.
2. W **obszarze Tworzenie planu odzyskiwania**określ nazwę planu.
3. Wybierz źródło i miejsce docelowe na podstawie maszyn w planie i wybierz **Menedżera zasobów** dla modelu wdrażania. Lokalizacja źródło musi mieć maszyny, które są włączone do pracy awaryjnej i odzyskiwania. 

    **Tryb failover** | **Źródła** | **Docelowego** 
   --- | --- | ---
   Azure–Azure | Wybierz region platformy Azure | Wybierz region platformy Azure
   VMware na platformę Azure | Wybierz serwer konfiguracji | Wybierz platformę Azure
   Komputery fizyczne do platformy Azure | Wybierz serwer konfiguracji | Wybierz platformę Azure   
   Z funkcji Hyper-V do platformy Azure | Wybieranie nazwy witryny funkcji Hyper-V | Wybierz platformę Azure
   Funkcja Hyper-V (zarządzana przez program VMM) na platformie Azure  | Wybierz serwer programu VMM | Wybierz platformę Azure
  
    Pamiętaj o następujących kwestiach:
    -  Planu odzyskiwania można używać tylko w przypadku pracy awaryjnej z lokalizacji źródłowej na platformie Azure. Nie można użyć planu odzyskiwania dla powrotu po awarii z platformy Azure.
    - Lokalizacja źródło musi mieć maszyny, które są włączone do pracy awaryjnej i odzyskiwania. 
    - Plan odzyskiwania może zawierać maszyny z tego samego źródła i obiektu docelowego. 
    - Do tego samego planu można dołączyć maszyny wirtualne VMware i maszyny wirtualne funkcji Hyper-V zarządzane przez program VMM.
    - Maszyny wirtualne VMware i serwery fizyczne mogą być w tym samym planie.

4. W **obszarze Wybierz elementy maszyny wirtualne**wybierz maszyny (lub grupę replikacji), które chcesz dodać do planu. Następnie kliknij przycisk **OK**.
    - Maszyny są dodawane domyślną grupę (Grupa 1) w planie. Po przemienniu w stan failover wszystkie maszyny w tej grupie rozpoczynają się w tym samym czasie.
    - Można wybrać tylko maszyny znajdują się w lokalizacjach źródłowych i docelowych, które zostały określone. 
5. Kliknij **przycisk OK,** aby utworzyć plan.

## <a name="add-a-group-to-a-plan"></a>Dodawanie grupy do planu

Tworzenie dodatkowych grup i dodawanie maszyn do różnych grup, dzięki czemu można określić różne zachowanie na podstawie grupy po grupie. Na przykład można określić, kiedy maszyny w grupie powinny rozpoczynać się po przełączeniu w pracę awaryjną lub określić niestandardowe akcje na grupę.

1. W **obszarze Plany odzyskiwania**kliknij prawym przyciskiem myszy plan > **Dostosuj**. Domyślnie po utworzeniu planu wszystkie dodane do niego maszyny znajdują się w domyślnej grupie 1.
2. Kliknij **+Grupa**. Domyślnie nowa grupa jest numerowana w kolejności, w jakiej jest dodawana. Możesz mieć maksymalnie siedem grup.
3. Wybierz maszynę, którą chcesz przenieść do nowej grupy, kliknij pozycję **Zmień grupę**, a następnie wybierz nową grupę. Możesz też kliknąć prawym przyciskiem myszy nazwę grupy > **element chroniony**i dodać maszyny do grupy. Maszyna lub grupa replikacji może należeć tylko do jednej grupy w planie odzyskiwania.


## <a name="add-a-script-or-manual-action"></a>Dodawanie skryptu lub akcji ręcznej

Plan odzyskiwania można dostosować, dodając skrypt lub akcję ręczną. Należy pamiętać, że:

- Jeśli replikujesz na platformę Azure, możesz zintegrować elementy runbook automatyzacji platformy Azure z planem odzyskiwania. [Dowiedz się więcej](site-recovery-runbook-automation.md).
- W przypadku replikowania maszyn wirtualnych funkcji Hyper-V zarządzanych przez program System Center VMM można utworzyć skrypt na lokalnym serwerze programu VMM i uwzględnić go w planie odzyskiwania.
- Po dodaniu skryptu dodaje nowy zestaw akcji dla grupy. Na przykład zestaw kroków wstępnych dla grupy 1 jest tworzony pod nazwą *Grupa 1: kroki wstępne*. Wszystkie kroki wstępne są wymienione wewnątrz tego zestawu. Skrypt w lokacji głównej można dodać tylko wtedy, gdy wdrożono serwer programu VMM.
- Jeśli dodasz akcję ręczną, po uruchomieniu planu odzyskiwania zatrzymuje się w punkcie, w którym wstawiono akcję ręczną. W oknie dialogowym zostanie wyświetlony monit o określenie, że akcja ręczna została ukończona.
- Aby utworzyć skrypt na serwerze programu VMM, postępuj zgodnie z instrukcjami zawartymi w [tym artykule](hyper-v-vmm-recovery-script.md).
- Skrypty mogą być stosowane podczas pracy awaryjnej do lokacji dodatkowej i podczas powrotu po awarii z lokacji dodatkowej do podstawowego. Obsługa zależy od scenariusza replikacji:
    
    **Scenariusz** | **Tryb failover** | **Powrót po awarii**
    --- | --- | --- 
    Azure–Azure  | Element Runbook | Element Runbook
    Z programu VMware do platformy Azure | Element Runbook | Nie dotyczy 
    Funkcja Hyper-V z programem VMM na platformie Azure | Element Runbook | Skrypt
    Z lokacji funkcji Hyper-V na platformę Azure | Element Runbook | Nie dotyczy
    VMM do dodatkowego programu VMM | Skrypt | Skrypt

1. W planie odzyskiwania kliknij krok, do którego ma zostać dodana akcja, i określ, kiedy ma nastąpić akcja:
    1. Jeśli akcja ma nastąpić przed rozpoczęciem pracy na komputerach w grupie po przełączeniu w pracę **awaryjną,** wybierz pozycję Dodaj działanie wstępne .
    1. Jeśli akcja ma nastąpić po rozpoczęciu pracy po pracy awaryjnej na maszynach w grupie, wybierz pozycję **Dodaj akcję księgowania**. Aby przesunąć położenie akcji, wybierz przyciski **Przenieś w górę** lub Przenieś w **dół.**
2. W **obszarze Wstawianie akcji**wybierz pozycję **Skrypt** lub **Akcja Ręczna**.
3. Jeśli chcesz dodać akcję ręczną, wykonaj następujące czynności:
    1. Wpisz nazwę akcji i wpisz instrukcje akcji. Osoba uruchamiana w pracy awaryjnej zobaczy te instrukcje.
    1. Określ, czy chcesz dodać akcję ręczną dla wszystkich typów pracy awaryjnej (Test, Praca awaryjna, Planowana praca awaryjna (jeśli dotyczy)). Następnie kliknij przycisk **OK**.
4. Jeśli chcesz dodać skrypt, wykonaj następujące czynności:
    1. Jeśli dodajesz skrypt programu VMM, wybierz opcję **Failover do skryptu VMM,** a w **ścieżce skryptu** wpisz ścieżkę względną do udziału. Jeśli na przykład udział znajduje \\ \<się w pliku VMMServerName>\MSSCVMMLibrary\RPScripts, określ ścieżkę: \RPScripts\RPScript.PS1.
    1. Jeśli dodajesz książkę uruchamiania automatyzacji platformy Azure, określ **konto automatyzacji platformy Azure,** na którym znajduje się element runbook, i wybierz odpowiedni **skrypt podręcznika Azure Runbook.**
5. Uruchom testową funkcję failover planu odzyskiwania, aby upewnić się, że skrypt działa zgodnie z oczekiwaniami.

## <a name="watch-a-video"></a>Obejrzyj film

Obejrzyj film, który pokazuje, jak utworzyć plan odzyskiwania.


> [!VIDEO https://www.youtube.com/embed/1KUVdtvGqw8]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [uruchamianiu pracy w pracy awaryjnej](site-recovery-failover.md).  

    
