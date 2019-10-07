---
title: Samouczek Azure Security Center — Ochrona zasobów przy użyciu Azure Security Center | Microsoft Docs
description: W tym samouczku przedstawiono sposób konfigurowania zasad dostępu just in Time do maszyny wirtualnej oraz zasad kontroli aplikacji.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/03/2018
ms.author: memildin
ms.openlocfilehash: 8cb07f3447e50528a94811f33a2142086f698586
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996340"
---
# <a name="tutorial-protect-your-resources-with-azure-security-center"></a>Samouczek: Ochrona zasobów przy użyciu Azure Security Center
Security Center ogranicza narażenie na zagrożenia przy użyciu funkcji kontroli dostępu i aplikacji w celu blokowania złośliwych działań. Dostęp do maszyn wirtualnych just-in-Time (JIT) zmniejsza narażenie na ataki przez umożliwienie trwałego dostępu do maszyn wirtualnych. Zamiast tego zapewniasz kontrolowany i Przeprowadź inspekcję dostępu do maszyn wirtualnych tylko w razie potrzeby. Adaptacyjne kontrole aplikacji pomagają w zabezpieczaniu maszyn wirtualnych przed złośliwym oprogramowaniem przez kontrolowanie, które aplikacje mogą być uruchamiane na maszynach wirtualnych. Security Center korzysta z uczenia maszynowego do analizowania procesów uruchomionych na maszynie wirtualnej i ułatwia stosowanie reguł listy dozwolonych przy użyciu tej analizy.

W tym samouczku dowiesz się, jak:

> [!div class="checklist"]
> * Konfigurowanie zasad dostępu just in Time do maszyny wirtualnej
> * Konfigurowanie zasad kontroli aplikacji

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) .

## <a name="prerequisites"></a>Wymagania wstępne
Aby przekroczyć funkcje omówione w tym samouczku, musisz mieć Security Center warstwy cenowej standardowa. Możesz bezpłatnie wypróbować Security Center Standard. Aby dowiedzieć się więcej, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/security-center/). Na stronie Szybki Start dołączanie [subskrypcji platformy Azure do Security Center Standard](security-center-get-started.md) przeprowadzi Cię przez proces uaktualniania do wersji Standard.

## <a name="manage-vm-access"></a>Zarządzanie dostępem do maszyny wirtualnej
Dostęp do maszyny wirtualnej JIT może służyć do blokowania ruchu przychodzącego do maszyn wirtualnych platformy Azure, co zmniejsza narażenie na ataki, zapewniając łatwy dostęp do łączenia się z maszynami wirtualnymi w razie potrzeby.

Porty zarządzania nie muszą być otwarte przez cały czas. Muszą one być otwarte tylko wtedy, gdy nawiązano połączenie z maszyną wirtualną, na przykład do wykonywania zadań zarządzania lub konserwacji. Gdy jest włączona funkcja just-in-Time, Security Center używa reguł grupy zabezpieczeń sieci (sieciowej grupy zabezpieczeń), które ograniczają dostęp do portów zarządzania, przez co nie mogą być objęte przez osoby atakujące.

1. W menu głównym Security Center wybierz **dostęp just in Time do maszyny wirtualnej** w obszarze **Zaawansowana ochrona w chmurze**.

   ![Dostęp do maszyny wirtualnej just in Time][1]

   **Dostęp just in Time do maszyny wirtualnej** zapewnia informacje o stanie maszyn wirtualnych:

   - **Skonfigurowane** — maszyny wirtualne, które zostały skonfigurowane do obsługi dostępu just in Time do maszyny wirtualnej.
   - **Zalecane** — maszyny wirtualne, które mogą obsługiwać dostęp do maszyny wirtualnej just-in-Time, ale nie zostały skonfigurowane do programu.
   - **Brak powodów rekomendacji** , które mogą spowodować, że maszyna wirtualna nie jest zalecana:

     - Brak sieciowej grupy zabezpieczeń — rozwiązanie just in Time wymaga, aby sieciowej grupy zabezpieczeń.
     - Klasyczna maszyna wirtualna — Security Center dostęp do maszyny wirtualnej just in Time aktualnie obsługuje tylko maszyny wirtualne wdrożone za pośrednictwem Azure Resource Manager.
     - Inne — maszyna wirtualna jest w tej kategorii, jeśli rozwiązanie just in time jest wyłączone w zasadach zabezpieczeń subskrypcji lub grupy zasobów lub że maszyna wirtualna nie ma publicznego adresu IP i nie ma sieciowej grupy zabezpieczeń.

2. Wybierz zalecaną maszynę wirtualną, a następnie kliknij pozycję **Włącz JIT na 1 maszynie wirtualnej** , aby skonfigurować zasady just in Time dla tej maszyny wirtualnej:

   Można zapisać porty domyślne, które Security Center zalecane, lub dodać i skonfigurować nowy port, na którym chcesz włączyć rozwiązanie just in Time. W tym samouczku dodamy port, wybierając pozycję **Dodaj**.

   ![Dodawanie konfiguracji portu][2]

3. W obszarze **Dodawanie konfiguracji portów**Zidentyfikuj:

   - Port
   - Typ protokołu
   - Dozwolone źródłowe adresy IP — zakresy adresów IPv4 mogą uzyskać dostęp na zatwierdzonym żądaniu
   - Maksymalny czas żądania — maksymalny przedział czasu, w którym można otworzyć określony port

4. Wybierz **przycisk OK** , aby zapisać.

## <a name="harden-vms-against-malware"></a>Zabezpieczanie maszyn wirtualnych przed złośliwym oprogramowaniem
Adaptacyjne kontrole aplikacji ułatwiają zdefiniowanie zestawu aplikacji, które mogą być uruchamiane w skonfigurowanych grupach zasobów, które wśród innych korzyści ułatwiają Zabezpieczanie maszyn wirtualnych przed złośliwym oprogramowaniem. Security Center korzysta z uczenia maszynowego do analizowania procesów uruchomionych na maszynie wirtualnej i ułatwia stosowanie reguł listy dozwolonych przy użyciu tej analizy.

1. Wróć do menu głównego Security Center. W obszarze **Zaawansowana ochrona w chmurze**wybierz pozycję **adaptacyjne kontrolki aplikacji**.

   ![Adaptacyjne kontrole aplikacji][3]

   Sekcja **grupy zasobów** zawiera trzy karty:

   - **Skonfigurowane**: Lista grup zasobów zawierających maszyny wirtualne, dla których skonfigurowano Sterowanie aplikacjami.
   - **Zalecane**: Lista grup zasobów, dla których zaleca się sterowanie aplikacjami.
   - **Brak zaleceń**: Lista grup zasobów zawierających maszyny wirtualne bez żadnych zaleceń dotyczących kontroli aplikacji. Na przykład maszyny wirtualne, na których aplikacje są zawsze zmieniane, i nie osiągnęły ustalonego stanu.

2. Wybierz kartę **zalecane** , aby uzyskać listę grup zasobów z zaleceniami dotyczącymi kontroli aplikacji.

   ![Zalecenia dotyczące kontroli aplikacji][4]

3. Wybierz grupę zasobów, aby otworzyć opcję **Utwórz reguły sterowania aplikacjami** . Na stronie **Wybieranie maszyn wirtualnych**Przejrzyj listę zalecanych maszyn wirtualnych i usuń zaznaczenie wszystkich elementów, do których nie chcesz stosować kontroli aplikacji. W obszarze **Wybieranie procesów dla reguł listy dozwolonych**Przejrzyj listę zalecanych aplikacji i usuń zaznaczenie wszystkich elementów, które nie mają być stosowane. Lista zawiera:

   - **Nazwa**: pełna ścieżka aplikacji
   - **Procesy**: ile aplikacji znajduje się w każdej ścieżce
   - **Typowy**: wartość "tak" wskazuje, że te procesy zostały wykonane na większości maszyn wirtualnych w tej grupie zasobów
   - Możliwe do **wykorzystania**: ikona ostrzeżenia wskazuje, czy można użyć aplikacji przez osobę atakującą do obejścia listy dozwolonych aplikacji. Zalecane jest przejrzenie tych aplikacji przed ich zatwierdzeniem.

4. Po zakończeniu wybranych opcji wybierz pozycję **Utwórz**.

## <a name="clean-up-resources"></a>Czyszczenie zasobów
Inne Przewodniki Szybki Start i samouczki w tej kolekcji zostały skompilowane w tym przewodniku Szybki Start. Jeśli planujesz kontynuować pracę z kolejnymi przewodnikami Szybki Start i samouczkami, Kontynuuj uruchamianie warstwy Standardowa i Włącz automatyczną obsługę administracyjną. Jeśli nie planujesz kontynuować lub chcesz wrócić do warstwy Bezpłatna:

1. Wróć do menu głównego Security Center i wybierz pozycję **zasady zabezpieczeń**.
2. Wybierz subskrypcję lub zasady, które chcesz przywrócić bezpłatnie. Zostanie otwarte okno **zasady zabezpieczeń** .
3. W obszarze **składniki zasad**wybierz pozycję **warstwa cenowa**.
4. Wybierz opcję **bezpłatna** , aby zmienić subskrypcję z warstwy Standardowa na warstwę bezpłatna.
5. Wybierz pozycję **Zapisz**.

Jeśli chcesz wyłączyć automatyczne Inicjowanie obsługi:

1. Wróć do menu głównego Security Center i wybierz pozycję **zasady zabezpieczeń**.
2. Wybierz subskrypcję, dla której chcesz wyłączyć automatyczne Inicjowanie obsługi.
3. W obszarze **zasady zabezpieczeń — zbieranie danych**wybierz pozycję **wyłączone** w obszarze dołączanie **, aby wyłączyć** automatyczną obsługę administracyjną.
4. Wybierz pozycję **Zapisz**.

>[!NOTE]
> Wyłączenie automatycznej aprowizacji nie powoduje usunięcia Microsoft Monitoring Agent z maszyn wirtualnych platformy Azure, w przypadku których Agent został zainicjowany. Wyłączenie automatycznej aprowizacji ogranicza monitorowanie zabezpieczeń zasobów.
>

## <a name="next-steps"></a>Następne kroki
W tym samouczku przedstawiono sposób ograniczania narażenia na zagrożenia przez:

> [!div class="checklist"]
> * Konfigurowanie zasad dostępu just in Time do maszyny wirtualnej w celu zapewnienia kontroli dostępu do maszyn wirtualnych i przeprowadzania na nich inspekcji tylko w razie potrzeby
> * Konfigurowanie adaptacyjnych zasad kontroli aplikacji w celu kontrolowania, które aplikacje mogą być uruchamiane na maszynach wirtualnych

Przejdź do następnego samouczka, aby dowiedzieć się więcej na temat reagowania na zdarzenia związane z bezpieczeństwem.

> [!div class="nextstepaction"]
> [Samouczek: reagowanie na zdarzenia związane z bezpieczeństwem](tutorial-security-incident.md)

<!--Image references-->
[1]: ./media/tutorial-protect-resources/just-in-time-vm-access.png
[2]: ./media/tutorial-protect-resources/add-port.png
[3]: ./media/tutorial-protect-resources/adaptive-application-control-options.png
[4]: ./media/tutorial-protect-resources/recommended-resource-groups.png
