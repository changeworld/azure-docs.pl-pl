---
title: Migrowanie istniejących standardowych przestrzeni nazw usługi Azure Service Bus w warstwie premium | Dokumentacja firmy Microsoft
description: Przewodnik dotyczący umożliwia migrację istniejącej usługi Azure Service Bus standardowe przestrzenie nazw do wersji premium
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
ms.date: 05/18/2019
ms.author: aschhab
ms.openlocfilehash: 65c207b4d03e7d156c8c871a3642601fd0489ead
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991414"
---
# <a name="migrate-existing-azure-service-bus-standard-namespaces-to-the-premium-tier"></a>Migrowanie istniejących standardowych przestrzeni nazw usługi Azure Service Bus w warstwie premium
Azure Service Bus oferowane wcześniej, przestrzenie nazw tylko w warstwie standardowa. Przestrzenie nazw są konfiguracje wielodostępnych, które są zoptymalizowane pod kątem niskiej przepustowości i środowisk deweloperskich. Warstwa premium oferuje dedykowanych zasobów na przestrzeń nazw przewidywalne opóźnienie i zwiększać przepustowość w stałej cenie. Warstwa premium jest zoptymalizowany pod kątem wysokiej przepływności i środowisk produkcyjnych, które wymagają funkcji przedsiębiorstwa.

W tym artykule opisano sposób migrowania istniejącej przestrzeni nazw w warstwie standardowa do warstwy premium.  

>[!WARNING]
> Migracji jest przeznaczony dla standardowych przestrzeni nazw usługi Service Bus ma zostać uaktualniony do warstwy premium. Narzędzie migracji nie obsługuje zmiany na starszą wersję.

Niektóre punkty, należy pamiętać: 
- Ta migracja jest przeznaczona do wykonania w miejscu, co oznacza, że istniejące aplikacje nadawcą i odbiorcą **nie wymaga żadnych zmian w kodzie lub konfiguracji**. Istniejących parametrów połączenia będzie automatycznie wskazywać nowej przestrzeni nazw premium.
- **Premium** przestrzeń nazw powinna mieć **żadnych jednostek** w niej do migracji została wykonana pomyślnie. 
- Wszystkie **jednostek** w standardowej przestrzeni nazw są **skopiowane** do przestrzeni nazw premium podczas procesu migracji. 
- Obsługuje migrację **1000 jednostek na jednostkę obsługi komunikatów** w warstwie premium. Aby określić liczbę jednostek obsługi komunikatów, należy, rozpoczynać się liczby jednostek, które mają w ramach bieżącego standardowych przestrzeni nazw. 
- Bezpośrednio nie można migrować z **warstwa podstawowa** do **warstwy premier**, ale możesz to zrobić tak pośrednio przy użyciu funkcji migracji z podstawowa na standardowa pierwszy, a następnie od planu standard do premium w następnym kroku.

## <a name="migration-steps"></a>Kroki migracji
Niektóre warunki są skojarzone z procesu migracji. Zapoznaj się z następujące kroki, aby zmniejszyć prawdopodobieństwo wystąpienia błędów. Te kroki opisują proces migracji, a w kolejnych sekcjach znajdują się szczegółowe informacje krok po kroku.

1. Utwórz nową przestrzeń nazw premium.
1. Sparuj przestrzeni nazw warstwy standardowa i premium do siebie nawzajem.
1. Jednostki synchronizacji (kopia over) ze standardu do przestrzeni nazw premium.
1. Zatwierdzenie migracji.
1. Opróżnij jednostek w standardowej przestrzeni nazw, używając nazwy po przeprowadzeniu migracji przestrzeni nazw.
1. Usuń standardową przestrzeń nazw.

>[!IMPORTANT]
> Po migracji zostało zatwierdzone, dostęp do starych standardową przestrzeń nazw i opróżnianie subskrypcji i kolejek. Po mają zostać opróżniane komunikaty, mogą być wysyłane do nowej przestrzeni nazw w warstwie premium na przetworzenie przez odbiorcę aplikacje. Po opróżniane kolejek i subskrypcji, zaleca się usunięcie starych standardową przestrzeń nazw.

### <a name="migrate-by-using-the-azure-cli-or-powershell"></a>Migrowanie przy użyciu wiersza polecenia platformy Azure lub programu PowerShell

Aby przeprowadzić migrację standardową przestrzeń nazw usługi Service Bus do warstwy premium przy użyciu wiersza polecenia platformy Azure lub narzędzia programu PowerShell, wykonaj następujące kroki.

1. Utwórz nową przestrzeń nazw usługi Service Bus premium. Możesz odwoływać się do [szablonów usługi Azure Resource Manager](service-bus-resource-manager-namespace.md) lub [za pomocą witryny Azure portal](service-bus-create-namespace-portal.md). Pamiętaj o wybraniu **premium** dla **serviceBusSku** parametru.

1. Ustaw następujące zmienne środowiskowe w celu uproszczenia polecenia migracji.
   ```azurecli
   resourceGroup = <resource group for the standard namespace>
   standardNamespace = <standard namespace to migrate>
   premiumNamespaceArmId = <Azure Resource Manager ID of the premium namespace to migrate to>
   postMigrationDnsName = <post migration DNS name entry to access the standard namespace>
   ```

    >[!IMPORTANT]
    > Po przeprowadzeniu migracji/nazwa aliasu (post_migration_dns_name) będzie służyć do dostępu do starego standardową przestrzeń nazw po migracji. Użyj tego, aby opróżnić kolejek i subskrypcji, a następnie usunąć przestrzeni nazw.

1. Sparuj przestrzeni nazw warstwy standardowa i premium, a następnie uruchomić synchronizacji przy użyciu następującego polecenia:

    ```azurecli
    az servicebus migration start --resource-group $resourceGroup --name $standardNamespace --target-namespace $premiumNamespaceArmId --post-migration-name $postMigrationDnsName
    ```


1. Sprawdź stan migracji za pomocą następującego polecenia:
    ```azurecli
    az servicebus migration show --resource-group $resourceGroup --name $standardNamespace
    ```

    Migracja uważa się za ukończoną, gdy pojawi się następujące wartości:
    * MigrationState = "Aktywny"
    * pendingReplicationsOperationsCount = 0
    * provisioningState = "Powodzenie"

    To polecenie wyświetla również konfiguracja migracji. Sprawdź, aby upewnić się, że wartości są ustawione poprawnie. Sprawdź także przestrzeń nazw premium w portalu, aby upewnić się, wszystkie kolejki i tematy zostały utworzone i są zgodne, co istniał w standardowej przestrzeni nazw.

1. Zatwierdzenie migracji, wykonując następujące polecenie ukończone:
   ```azurecli
   az servicebus migration complete --resource-group $resourceGroup --name $standardNamespace
   ```

### <a name="migrate-by-using-the-azure-portal"></a>Migrowanie za pomocą witryny Azure portal

Migracja za pomocą witryny Azure portal ma ten sam przepływ logiczny co migracja za pomocą poleceń. Wykonaj następujące kroki, aby przeprowadzić migrację za pomocą witryny Azure portal.

1. Na **nawigacji** menu w lewym okienku wybierz **migracji do warstwy premium**. Kliknij przycisk **wprowadzenie** przycisk, aby przejść do następnej strony.
    ![Strona docelowa migracji][]

1. Pełne **Instalatora**.
   ![Konfigurowanie przestrzeni nazw][]
   1. Utwórz i przypisz przestrzeń nazw premium, aby przeprowadzić migrację istniejących standardową przestrzeń nazw do.
        ![Konfigurowanie przestrzeni nazw — tworzenie przestrzeni nazw w warstwie premium][]
   1. Wybierz **nazwy po migracji**. Ta nazwa będzie używana do uzyskiwania dostępu do standardową przestrzeń nazw, po zakończeniu migracji.
        ![Konfigurowanie przestrzeni nazw — wybierz nazwę migracji ogłoszenia][]
   1. Wybierz **'Dalej'** aby kontynuować.
1. Jednostki synchronizacji między obszarami nazw warstwy standardowa i premium.
    ![Konfigurowanie — synchronizacja jednostki — przestrzeń nazw start][]

   1. Wybierz **Rozpocznij synchronizację** aby rozpocząć synchronizowanie jednostki.
   1. Wybierz **tak** w oknie dialogowym, aby potwierdzić, a następnie uruchomić synchronizacji.
   1. Zaczekaj, aż synchronizacja została zakończona. Stan jest dostępna na pasku stanu.
        ![Przestrzeń nazw — synchronizacja jednostki — postęp instalacji][]
        >[!IMPORTANT]
        > Jeśli trzeba przerwać migrację z jakiegokolwiek powodu, zapoznaj się z przepływem przerwania w sekcji często zadawane pytania dotyczące niniejszego dokumentu.
   1. Po ukończeniu synchronizacji wybierz **dalej** w dolnej części strony.

1. Przejrzyj zmiany na stronie podsumowania. Wybierz **kończenia migracji** Aby przełączyć przestrzenie nazw, a także do przeprowadzenia migracji.
    ![Przełącz przestrzeń nazw — menu przełącznika][] strona potwierdzenia jest wyświetlany, gdy migracja zostanie zakończona.
    ![Przestrzeń nazw przełącznika — Powodzenie][]

## <a name="faqs"></a>Często zadawane pytania

### <a name="what-happens-when-the-migration-is-committed"></a>Co się stanie, gdy migracja zostanie zatwierdzone?

Po migracji zostanie zatwierdzone, parametry połączenia, które wskazywał standardową przestrzeń nazw będzie wskazywać przestrzeń nazw premium.

Aplikacji nadawcy i odbiorcy będą Odłącz od standardowego Namespace i ponowne łączenie do przestrzeni nazw premium.

### <a name="what-do-i-do-after-the-standard-to-premium-migration-is-complete"></a>Co należy zrobić po zakończeniu standard do premium migracji?

Standard do premium migracji gwarantuje, że metadane jednostki, takie jak tematy, subskrypcje i filtry są kopiowane z standardową przestrzeń nazw do przestrzeni nazw premium. Dane wiadomości, które na standardową przestrzeń nazw nie zostaną skopiowane ze standardową przestrzeń nazw do przestrzeni nazw premium.

Standardową przestrzeń nazw może mieć inne wiadomości wysłanych i zatwierdzone, podczas migracji jest w toku. Ręcznie opróżnić te komunikaty z Namespace standardowe i ręcznie wysyłać je do warstwy premium Namespace. Aby ręcznie opróżnić wiadomości, użyj aplikacji konsoli lub skrypt, który wstrzymanie jednostek standardowych przestrzeni nazw przy użyciu nazwy DNS migracji wpis, który określiłeś w poleceniach migracji. Te komunikaty Aby wysłać do przestrzeni nazw premium mogą być przetwarzane przez odbiorców.

Po mają zostać opróżniane komunikaty, należy usunąć standardową przestrzeń nazw.

>[!IMPORTANT]
> Po ma zostały opróżniane wiadomości od standardowej przestrzeni nazw, należy usunąć standardową przestrzeń nazw. Jest to ważne, ponieważ parametry połączenia, które początkowo teraz określone standardową przestrzeń nazw, który odwołuje się do przestrzeni nazw premium. Nie potrzebujesz już standardowa Namespace. Usuwanie standardową przestrzeń nazw, który został zmigrowany pomaga nowsze pomyłek.

### <a name="how-much-downtime-do-i-expect"></a>Jak długi przestój oczekiwać?
Proces migracji jest przeznaczony do zmniejszenia przewidywany czas przestoju w odniesieniu do aplikacji. Czas przestoju została zmniejszona o przy użyciu parametrów połączenia, używanego przez aplikacje nadawcą i odbiorcą by wskazywał nową przestrzeń nazw premium.

Przestój, jaki wystąpił przez aplikację jest ograniczona do czasu potrzebnego do zaktualizowania wpisu DNS, aby wskazywał przestrzeń nazw premium. Przestój trwa około 5 minut.

### <a name="do-i-have-to-make-any-configuration-changes-while-doing-the-migration"></a>Czy muszę wprowadzać żadnych zmian konfiguracji podczas wykonywania migracji?
Nie wprowadzono żadnych zmian kodu lub konfiguracji, wymagane do wykonania migracji. Parametry połączenia, które umożliwia dostęp standardowy Namespace aplikacji nadawcy i odbiorcy jest automatycznie mapowany do działania jako alias dla przestrzeni nazw premium.

### <a name="what-happens-when-i-abort-the-migration"></a>Co się stanie, gdy można przerwać migrację?
Migracja może zostało przerwane, za pomocą `Abort` polecenia lub przy użyciu witryny Azure portal. 

#### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli
az servicebus migration abort --resource-group $resourceGroup --name $standardNamespace
```

#### <a name="azure-portal"></a>Azure Portal

![Przerwanie usługi flow — przerwać synchronizacji][]
![przerwanie przepływu — przerwać pełne][]

Podczas procesu migracji zostało przerwane, przerywa proces kopiowania jednostki (tematy, subskrypcje i filtrów) ze standardu przestrzeń nazw w warstwie premium i przerywa parowanie.

Parametry połączenia nie są aktualizowane, aby wskazywał przestrzeń nazw premium. Istniejące aplikacje nadal działają tak samo jak przed rozpoczęciem migracji.

Jednak nie jest usuwanie jednostek w przestrzeni nazw premium lub usunąć przestrzeń nazw premium. Usuń jednostki ręcznie, jeśli nie chcesz kontynuować migrację.

>[!IMPORTANT]
> Jeśli zdecydujesz się przerwać migrację, należy usunąć premium Namespace aprowizowana przez Ciebie miał do migracji, aby nie są naliczane za zasoby.

#### <a name="i-dont-want-to-have-to-drain-the-messages-what-do-i-do"></a>Nie chcę opróżnić komunikaty. Co mam zrobić?

Może to być wiadomości, które są wysyłane przez aplikacje nadawcy i przekazane do magazynu w obrębie standardowa Namespace, podczas migracji odbywa się i przed migracją zatwierdzone.

Podczas migracji rzeczywistych danych/ładunek komunikatu nie zostaną skopiowane ze standardu do przestrzeni nazw premium. Komunikaty muszą być ręcznie opróżniane, a następnie wysyłane do przestrzeni nazw premium.

Jednakże jeśli można migrować przedziale planowanej konserwacji/celów, a nie chcesz ręcznie opróżnić i wysyłać wiadomości, wykonaj następujące kroki:

1. Zatrzymaj aplikacjach nadawcy. Aplikacje odbiorcy będą przetwarzane wiadomości, które są obecnie dostępne w standardowej przestrzeni nazw i będzie opróżnienia kolejki.
1. Po kolejek i subskrypcji w standardowych Namespace są puste, postępuj zgodnie z procedurą, opisanego wcześniej do wykonania migracji z standardowych przestrzeni nazw premium.
1. Po zakończeniu migracji można ponownie uruchomić aplikacji nadawcy.
1. Nadawcami a odbiornikami teraz automatycznie połączy się w przestrzeni nazw premium.

    >[!NOTE]
    > Nie masz można zatrzymać aplikacji odbiornika do migracji.
    >
    > Po zakończeniu migracji aplikacji odbiornika odłączyć od standardowej przestrzeni nazw, a automatyczne łączenie z przestrzeni nazw premium.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [różnice między komunikatów w warstwie standardowa i premium](./service-bus-premium-messaging.md).
* Dowiedz się więcej o [aspekty odzyskiwania o wysokiej dostępności i po awarii geograficznej dla usługi Service Bus premium](service-bus-outages-disasters.md#protecting-against-outages-and-disasters---service-bus-premium).

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
