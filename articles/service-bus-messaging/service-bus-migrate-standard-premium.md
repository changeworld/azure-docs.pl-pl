---
title: Migrowanie istniejącej usługi Azure Standard przestrzeniami nazw magistrali usług do warstwy Premium | Dokumentacja firmy Microsoft
description: Przewodnik dotyczący umożliwia migrację istniejących Azure Standard przestrzeniami nazw magistrali usług do wersji Premium
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: darosa
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/18/2019
ms.author: aschhab
ms.openlocfilehash: 7b153c36e10f1d4e2be2a0cf42f998c31cb6473a
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58896583"
---
# <a name="migrate-existing-azure-service-bus-standard-namespaces-to-premium-tier"></a>Migrowanie istniejącej usługi Azure Standard przestrzeniami nazw magistrali usług do warstwy Premium

Azure Service Bus oferowane wcześniej, przestrzenie nazw tylko w warstwie standardowa. Zostały one wielodostępnych konfiguracje, które zostały zoptymalizowane pod kątem niskiej przepustowości i środowisk deweloperskich.

W ostatnim okresie usługi Azure Service Bus została rozszerzona o oferty z warstwy Premium, która oferuje dedykowanych zasobów na przestrzeń nazw przewidywalne opóźnienie i zwiększać przepustowość w stałej cenie, który jest zoptymalizowany pod kątem wysokiej przepływności i środowisk produkcyjnych wymaga funkcji przedsiębiorstwa.

Poniższe narzędzia umożliwia istniejącej przestrzeni nazw w warstwie standardowa do migracji do warstwy Premium.

>[!WARNING]
> Migracji jest przeznaczony dla usługi Service Bus standardową przestrzeń nazw jako ***uaktualnione*** do warstwy Premium.
>
> Narzędzia migracji ***nie*** obsługuje zmiany na starszą wersję.
>[!NOTE]
> Ta migracja jest przeznaczona do wykonania ***w miejscu***.
>
> Oznacza to, że istniejące nadawcy i odbiorcy aplikacje nie wymagają każda zmiana kodu lub konfiguracji.
>
> Istniejących parametrów połączenia będzie automatycznie wskazywać nowej przestrzeni nazw premium.
>
> Ponadto wszystkie jednostki w standardowej przestrzeni nazw są **kopiowana za pośrednictwem** w przestrzeni nazw Premium podczas procesu migracji.
>
>
> Firma Microsoft obsługuje ***1000 jednostek na jednostkę obsługi komunikatów*** w warstwie Premium, tak aby zidentyfikować liczbę jednostek obsługi komunikatów są konieczne, uruchom z liczbą obiektów, które są w bieżącym standardową przestrzeń nazw.

## <a name="migration-steps"></a>Kroki migracji

>[!IMPORTANT]
> Istnieją pewne zastrzeżenia związane z procesem migracji. Prosimy o w pełni zapoznać się przy użyciu etapy ograniczyć możliwości błędy.

Proces konkretnych migracji krok po kroku opisano szczegółowo w poniższych przewodnikach.

Dostępne są następujące etapy logiczne-

1. Utwórz nową przestrzeń nazw Premium.
2. Sparuj Standard i Premium przestrzeni nazw do siebie nawzajem.
3. Jednostki synchronizacji (kopia over) od planu Standard do przestrzeni nazw w warstwie Premium
4. Zatwierdzenie migracji
5. Opróżnij jednostek w standardowej przestrzeni nazw, przy użyciu nazwy po przeprowadzeniu migracji przestrzeni nazw
6. Usuń standardową przestrzeń nazw

>[!NOTE]
> Po migracji został przekazany, jest bardzo ważne, aby dostęp do starych standardową przestrzeń nazw i opróżnianie poziomie subskrypcji i kolejek.
>
> Po komunikaty mają zostały opróżniane mogą być przesyłane do nowej przestrzeni nazw premium do przetworzenia przez aplikacji odbiornika.
>
> Po opróżniane kolejek i subskrypcji, zaleca się usunięcie starych standardową przestrzeń nazw. Nie możesz się konieczności, ją!

### <a name="migrate-using-azure-cli-or-powershell"></a>Migrowanie przy użyciu wiersza polecenia platformy Azure lub programu PowerShell

Aby przeprowadzić migrację przestrzeni nazw usługi Service Bus Standard do Premium za pomocą narzędzia wiersza polecenia platformy Azure lub programu PowerShell, zobacz poniżej przewodnik.

1. Utwórz nową przestrzeń nazw usługi Service Bus w warstwie Premium. Możesz odwoływać się do [szablonów usługi Azure Resource Manager](service-bus-resource-manager-namespace.md) lub [za pomocą witryny Azure portal](service-bus-create-namespace-portal.md). Pamiętaj wybrać "Premium" dla **serviceBusSku** parametru.

2. Ustawić poniższe zmienne środowiskowe można uproszczenie polecenia migracji.
   ```
   resourceGroup = <resource group for the standard namespace>
   standardNamespace = <standard namespace to migrate>
   premiumNamespaceArmId = <Azure Resource Manager ID of the Premium namespace to migrate to>
   postMigrationDnsName = <post migration DNS name entry to access the Standard namespace>
   ```

    >[!IMPORTANT]
    > Nazwa po migracji (post_migration_dns_name) będzie służyć do dostępu do starego standardową przestrzeń nazw po migracji. Musi być to opróżnić kolejek i subskrypcji, a następnie usuń przestrzeni nazw.

3. **Para** przestrzenie nazw Standard i Premium oraz **Rozpocznij synchronizację** przy użyciu poniższego polecenia -

    ```
    az servicebus migration start --resource-group $resourceGroup --name $standardNamespace --target-namespace $premiumNamespaceArmId --post-migration-name $postMigrationDnsName
    ```


4. Sprawdź stan migracji za pomocą poniższego polecenia -
    ```
    az servicebus migration show --resource-group $resourceGroup --name $standardNamespace
    ```

    Migracja uważa się za ukończoną kiedy
    * MigrationState = "Aktywny"
    * pendingReplicationsOperationsCount = 0
    * provisioningState = "Powodzenie"

    To polecenie wyświetla również konfiguracja migracji. Należy dokładnie sprawdź, aby upewnić się, że wartości są ustawione jako poprzedniego zadeklarowany.

    Ponadto również szybko sprawdzić przestrzeń nazw Premium w portalu, aby upewnić się, że wszystkie kolejki i tematy zostały utworzone i są zgodne co znajdował się na standardową przestrzeń nazw.

5. Zatwierdzenie migracji, wykonując poniższe polecenie ukończone
   ```
   az servicebus migration complete --resource-group $resourceGroup --name $standardNamespace
   ```

### <a name="migrate-using-azure-portal"></a>Migrowanie przy użyciu witryny Azure portal

Migracja za pomocą witryny Azure portal ma ten sam przepływ logiczny co migracja za pomocą poleceń. Zobacz poniżej przewodnikiem podczas procesu krok po kroku dotyczące migracji przy użyciu portalu.

1. Wybierz **"Migracja do wersji Premium"** opcji menu w menu nawigacji w okienku po lewej stronie. Kliknij przycisk **Rozpoczynanie pracy** przycisk, aby przejść do następnej strony.
    ![Strona docelowa migracji][]

2. Pełne **Instalatora**.
   ![Konfigurowanie przestrzeni nazw][]
   1. Utwórz i przypisz przestrzeń nazw Premium, aby przeprowadzić migrację istniejących standardową przestrzeń nazw do.
        ![Konfigurowanie przestrzeni nazw — tworzenie przestrzeni nazw w warstwie premium][]
   2. Wybierz **"Po migracji name"** dostępu do standardowej przestrzeni nazw, po zakończeniu migracji.
        ![Konfigurowanie przestrzeni nazw — wybierz nazwę migracji ogłoszenia][]
   3. Kliknij przycisk **'Dalej'** aby kontynuować.
3. **Synchronizacja** jednostek między przestrzeni nazw Standard i Premium.
    ![Konfigurowanie — synchronizacja jednostki — przestrzeń nazw start][]

   1. Kliknij przycisk **Rozpocznij synchronizację** aby rozpocząć synchronizowanie jednostki.
   2. Kliknij przycisk **"Yes"** w oknie podręcznym potwierdzenie można uruchomić synchronizacji.
   3. Poczekaj, aż **synchronizacji** zostało zakończone. Stan jest udostępniane na pasku stanu.
        ![Przestrzeń nazw — synchronizacja jednostki — postęp instalacji][]
        >[!IMPORTANT]
        > Jeśli potrzebujesz **przerwać** z jakiejkolwiek przyczyny, Przejrzyj przepływ przerwania w sekcji często zadawane pytania dotyczące niniejszego dokumentu.
   4. Po zakończeniu synchronizacji, kliknij przycisk **'Dalej'** znajdujący się u dołu strony.

4. Przejrzyj zmiany na stronie podsumowania.
    ![Przestrzeń nazw przełącznika - przełącznika menu][]

5. Kliknij pozycję **"Kończenia migracji"** przestrzenie nazw i przeprowadzić migrację.
    ![Przestrzeń nazw przełącznika — Powodzenie][]

## <a name="faqs"></a>Często zadawane pytania

### <a name="what-happens-when-the-migration-is-committed"></a>Co się stanie, gdy migracja zostanie zatwierdzone?

Po migracji zostanie zatwierdzone, parametry połączenia, które wskazywał standardową przestrzeń nazw będzie wskazywać przestrzeń nazw Premium.

Aplikacji nadawcy i odbiorcy będą Odłącz od standardowego Namespace i ponowne łączenie do przestrzeni nazw Premium.

### <a name="what-do-i-do-after-the-standard-to-premium-migration-is-complete"></a>Co należy zrobić po zakończeniu Standard do Premium migracji?

Standard do Premium migracji gwarantuje, że metadane jednostki (tematy, subskrypcje, filtry, et al.) są kopiowane ze standardu do przestrzeni nazw Premium. Dane wiadomości, które na standardową przestrzeń nazw nie jest kopiowany za pośrednictwem zgodne ze standardem do przestrzeni nazw Premium.

Ze względu na to standardową przestrzeń nazw może mieć inne wiadomości wysłanych i zatwierdzone, podczas migracji jest w toku. Te komunikaty muszą ręcznie opróżniane ze standardowego Namespace i przesyłane do Namespace Premium ręcznie.

Aby to zrobić, możesz ***musi*** za pomocą aplikacji konsoli lub skrypt, który opróżnić jednostki standardowej przestrzeni nazw, przy użyciu **nazwy DNS migracji ogłoszenia** określone w poleceniach migracji, a następnie wysłać te komunikaty Namespace — wersja Premium, tak aby mogą być przetwarzane przez odbiorców.

Gdy ma zostać opróżniane wiadomości, przejdź do usunięcia standardową przestrzeń nazw.

>[!IMPORTANT]
> Należy pamiętać, że po ma zostały opróżniane wiadomości od standardowej przestrzeni nazw, możesz **musi** Usuń standardową przestrzeń nazw.
>
> Jest to ważne, ponieważ parametry połączenia, które początkowo teraz określone standardową przestrzeń nazw faktycznie odwołuje się do przestrzeni nazw Premium. Użytkownik nie będzie to standardowa Namespace można wymagające już.
>
> Usuwanie standardową przestrzeń nazw, czy zmigrowane pomaga zmniejsza pomyłek w późniejszym terminie. 

### <a name="how-much-downtime-do-i-expect"></a>Jak długi przestój oczekiwać?
Powyższy proces migracji jest przeznaczony do zmniejszenia przewidywany czas przestoju w odniesieniu do aplikacji. Odbywa się przy użyciu ciągu połączenia używanego przez aplikacje nadawcą i odbiorcą by wskazywał nową przestrzeń nazw Premium.

Czas przestoju doświadczonym przez aplikację jest ograniczona do ilości czasu potrzebnego do zaktualizowania wpisu DNS, aby wskazywał przestrzeń nazw Premium.

To może być zakłada się, że ***około 5 minut***.

### <a name="do-i-have-to-make-any-configuration-changes-while-performing-the-migration"></a>Czy muszę wprowadzać żadnych zmian konfiguracji podczas wykonywania migracji?
Nie, nie ma zmian kodu/konfiguracji potrzebne do wykonania tej migracji. Parametry połączenia, które umożliwia dostęp standardowy Namespace aplikacji nadawcy i odbiorcy jest automatycznie mapowany do działania jako **alias** dla Namespace Premium.

### <a name="what-happens-when-i-abort-the-migration"></a>Co się stanie, gdy można przerwać migrację?
Migracja może została przerwana, za pomocą polecenia "Przerwij" lub w witrynie Azure portal. 

#### <a name="azure-cli-or-powershell"></a>Wiersza polecenia platformy Azure lub programu PowerShell

    az servicebus migration abort --resource-group $resourceGroup --name $standardNamespace

#### <a name="azure-portal"></a>Azure Portal

![Przerwanie usługi flow — przerwać synchronizacji][]
![przerwanie przepływu — przerwać pełne][]

Podczas procesu migracji zostało przerwane, faktycznie przerywa proces kopiowania na elementach (tematy, subskrypcje i filtrów) od planu Standard do Premium i przerywa parowanie.

Parametry połączenia **nie** poinformowani o przestrzeni nazw Premium. Istniejące aplikacje nadal działają tak samo jak przed rozpoczęciem migracji.

Jednak go **nie** usuwanie jednostek w przestrzeni nazw Premium lub usunąć przestrzeń nazw w warstwie Premium, sam. Musi to być wykonywane ręcznie, jeśli ma zostać przenosi do przodu do migracji po wszystkich.

>[!IMPORTANT]
> Jeśli zdecydujesz przerwać migracji, Usuń Namespace — wersja Premium, były zaaprowizowanym do migracji, aby nie są naliczane za zasoby.

#### <a name="i-dont-want-to-have-to-drain-the-messages-what-do-i-do"></a>Nie chcę opróżnić komunikaty. Co mam zrobić?

Może to być wiadomości, które są wysyłane przez aplikacje nadawcy i nacisk na pamięć na standardowa Namespace, podczas migracji odbywa się i dba po prawej stronie przed migracją.

Biorąc pod uwagę, że podczas migracji, rzeczywistych danych/ładunek komunikatu nie jest kopiowany za pośrednictwem od planu Standard do Premium, mają one zostać ręcznie opróżniane i następnie wysyłane do przestrzeni nazw Premium.

Jednak jeśli można migrować przedziale planowanej konserwacji/celów i nie chcesz ręcznie opróżnić i wysyłać wiadomości, zapoznaj się z wykonaj poniższe kroki —

1. Zatrzymywanie aplikacji nadawcy i umożliwiają odbiorców do przetwarzania komunikatów, które są obecnie dostępne w standardowej przestrzeni nazw i opróżnienia kolejki.
2. Po kolejek i subskrypcji w standardowych Namespace są puste, postępuj zgodnie z procedurą opisaną powyżej do wykonania migracji od planu Standard do Premium.
3. Po zakończeniu migracji można ponownie uruchomić aplikacji nadawcy.
4. Nadawcami a odbiornikami teraz automatycznie połączy się w przestrzeni nazw Premium.

    >[!NOTE]
    > Odbiornik muszą nie można zatrzymać w związku z migracją.
    >
    > Po zakończeniu migracji odbiorców odłączyć od standardowej przestrzeni nazw, a automatyczne łączenie z przestrzeni nazw Premium.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [różnice między Standard i Premium obsługi komunikatów](./service-bus-premium-messaging.md)
* Dowiedz się więcej o aspekty odzyskiwania o wysokiej dostępności i Diaster geograficznej dla usługi Service Bus w warstwie Premium [tutaj](service-bus-outages-disasters.md#protecting-against-outages-and-disasters---service-bus-premium)

[Strona docelowa migracji]: ./media/service-bus-standard-premium-migration/1.png
[Konfigurowanie przestrzeni nazw]: ./media/service-bus-standard-premium-migration/2.png
[Konfigurowanie przestrzeni nazw — tworzenie przestrzeni nazw w warstwie premium]: ./media/service-bus-standard-premium-migration/3.png
[Konfigurowanie przestrzeni nazw — wybierz nazwę migracji ogłoszenia]: ./media/service-bus-standard-premium-migration/4.png
[Konfigurowanie — synchronizacja jednostki — przestrzeń nazw start]: ./media/service-bus-standard-premium-migration/5.png
[Przestrzeń nazw — synchronizacja jednostki — postęp instalacji]: ./media/service-bus-standard-premium-migration/8.png
[Przestrzeń nazw przełącznika - przełącznika menu]: ./media/service-bus-standard-premium-migration/9.png
[Przestrzeń nazw przełącznika — Powodzenie]: ./media/service-bus-standard-premium-migration/12.png

[Przerwanie usługi flow — przerwać synchronizacji]: ./media/service-bus-standard-premium-migration/abort1.png
[Przerwanie usługi flow — przerwać ukończone]: ./media/service-bus-standard-premium-migration/abort3.png
