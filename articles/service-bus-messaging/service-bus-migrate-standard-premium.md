---
title: Migrowanie obszarów nazw usługi Azure Service Bus — od standardu do wersji premium
description: Przewodnik umożliwiający migrację istniejących standardowych obszarów nazw usługi Azure Service Bus do wersji premium
services: service-bus-messaging
documentationcenter: ''
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2019
ms.author: aschhab
ms.openlocfilehash: 27e3260b91bebee14ff12188a7dbd6c7cf76355c
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385031"
---
# <a name="migrate-existing-azure-service-bus-standard-namespaces-to-the-premium-tier"></a>Migrowanie istniejących standardowych obszarów nazw usługi Azure Service Bus do warstwy premium

Wcześniej usługa Azure Service Bus oferowała obszary nazw tylko w warstwie standardowej. Przestrzenie nazw są konfiguracje wielu dzierżawców, które są zoptymalizowane pod kątem niskiej przepływności i środowisk deweloperskich. Warstwa premium oferuje dedykowane zasoby na obszar nazw dla przewidywalnego opóźnienia i zwiększonej przepływności po ustalonej cenie. Warstwa premium jest zoptymalizowana pod kątem środowisk o wysokiej przepływności i środowiskach produkcyjnych, które wymagają dodatkowych funkcji przedsiębiorstwa.

W tym artykule opisano sposób migracji istniejących obszarów nazw warstwy standardowej do warstwy premium.  

>[!WARNING]
> Migracja jest przeznaczona dla standardowych obszarów nazw usługi Service Bus, które mają zostać uaktualnione do warstwy premium. Narzędzie migracji nie obsługuje obniżania poziomu.

Niektóre z punktów, na które należy zwrócić uwagę:

- Ta migracja ma mieć miejsce w miejscu, co oznacza, że istniejące aplikacje nadawcy i odbiorcy **nie wymagają żadnych zmian w kodzie lub konfiguracji.** Istniejący ciąg połączenia automatycznie wskaż nowy obszar nazw premium.
- Obszar nazw **w uchyłkowej** nie powinien mieć **żadnych jednostek,** aby migracja powiodła się.
- Wszystkie **jednostki** w standardowej przestrzeni nazw są **kopiowane** do obszaru nazw premium podczas procesu migracji.
- Migracja obsługuje **1000 jednostek na jednostkę obsługi wiadomości** w warstwie premium. Aby określić, ile jednostek obsługi wiadomości potrzebujesz, należy rozpocząć od liczby jednostek, które masz w bieżącej standardowej przestrzeni nazw.
- Nie można bezpośrednio migrować z **warstwy podstawowej** do **warstwy premium,** ale można to zrobić pośrednio, najpierw migrując z podstawowej do standardowej, a następnie ze standardu do premii w następnym kroku.

## <a name="migration-steps"></a>Kroki migracji

Niektóre warunki są skojarzone z procesem migracji. Zapoznaj się z następującymi krokami, aby zmniejszyć możliwość wystąpienia błędów. Te kroki przedstawiają proces migracji, a szczegóły krok po kroku są wymienione w poniższych sekcjach.

1. Utwórz nową przestrzeń nazw premium.
1. Sparuj standardowe i premium przestrzenie nazw ze sobą.
1. Synchronizuj (copy-over) jednostki ze standardu do obszaru nazw premium.
1. Zatwierdzanie migracji.
1. Opróżnianie jednostek w standardowej przestrzeni nazw przy użyciu nazwy po migracji obszaru nazw.
1. Usuń standardową przestrzeń nazw.

>[!IMPORTANT]
> Po przejmując migrację, dostęp do starego standardowego obszaru nazw i opróżnić kolejki i subskrypcje. Po opróżnieniu wiadomości mogą one zostać wysłane do nowego obszaru nazw premium do przetworzenia przez aplikacje odbiorcy. Po opróżnieniu kolejek i subskrypcji zaleca się usunięcie starej standardowej przestrzeni nazw.

### <a name="migrate-by-using-the-azure-cli-or-powershell"></a>Migrowanie przy użyciu interfejsu wiersza polecenia platformy Azure lub programu PowerShell

Aby przeprowadzić migrację standardowego obszaru nazw usługi Service Bus do poziomu premium przy użyciu narzędzia interfejsu wiersza polecenia platformy Azure lub programu PowerShell, wykonaj następujące kroki.

1. Utwórz nową przestrzeń nazw premium usługi Service Bus. Można odwoływać się do [szablonów usługi Azure Resource Manager](service-bus-resource-manager-namespace.md) lub użyć [witryny Azure portal](service-bus-create-namespace-portal.md). Pamiętaj, aby wybrać **premium** dla **parametru serviceBusSku.**

1. Ustaw następujące zmienne środowiskowe, aby uprościć polecenia migracji.

   ```
   resourceGroup = <resource group for the standard namespace>
   standardNamespace = <standard namespace to migrate>
   premiumNamespaceArmId = <Azure Resource Manager ID of the premium namespace to migrate to>
   postMigrationDnsName = <post migration DNS name entry to access the standard namespace>
   ```

    >[!IMPORTANT]
    > Alias/nazwa po migracji (post_migration_dns_name) będzie używany do uzyskiwania dostępu do starej standardowej migracji postu obszaru nazw. Użyj tego, aby opróżnić kolejki i subskrypcje, a następnie usunąć obszar nazw.

1. Sparuj standardowe i premium obszary nazw i rozpocznij synchronizację za pomocą następującego polecenia:

    ```azurecli-interactive
    az servicebus migration start --resource-group $resourceGroup --name $standardNamespace --target-namespace $premiumNamespaceArmId --post-migration-name $postMigrationDnsName
    ```

1. Sprawdź stan migracji za pomocą następującego polecenia:

    ```azurecli-interactive
    az servicebus migration show --resource-group $resourceGroup --name $standardNamespace
    ```

    Migracja jest uważana za zakończona po wyświetleniu następujących wartości:

    * MigrationState = "Aktywny"
    * pendingReplicationsOperationsCount = 0
    * aprowizowanieState = "Powiodło się"

    To polecenie wyświetla również konfigurację migracji. Sprawdź, czy wartości są poprawnie ustawione. Sprawdź również obszar nazw premium w portalu, aby upewnić się, że wszystkie kolejki i tematy zostały utworzone i że są zgodne z tym, co istniało w standardowej przestrzeni nazw.

1. Zaajmij migrację, wykonując następujące pełne polecenie:

   ```azurecli-interactive
   az servicebus migration complete --resource-group $resourceGroup --name $standardNamespace
   ```

### <a name="migrate-by-using-the-azure-portal"></a>Migrowanie przy użyciu witryny Azure portal

Migracja przy użyciu witryny Azure portal ma taki sam przepływ logiczny jak migracja przy użyciu poleceń. Wykonaj następujące kroki, aby przeprowadzić migrację przy użyciu witryny Azure portal.

1. W menu **Nawigacja** w lewym okienku wybierz pozycję **Migruj do premii**. Kliknij przycisk **Wprowadzenie,** aby przejść do następnej strony.
    ![Strona docelowa migracji][]

1. Zakończ **instalację**.
   ![Obszar nazw instalacji][]
   1. Utwórz i przypisz obszar nazw premium, aby przeprowadzić migrację istniejącego standardowego obszaru nazw.
        ![Obszar nazw instalacji — tworzenie obszaru nazw premium][]
   1. Wybierz **nazwę księgi migracji**. Ta nazwa będzie używana do uzyskiwania dostępu do standardowej przestrzeni nazw po zakończeniu migracji.
        ![Obszar nazw instalacji — wybieranie nazwy migracji po zakończeniu][]
   1. Wybierz **"Dalej",** aby kontynuować.
1. Synchronizowanie encji między obszarami nazw standardu i premium.
    ![Obszar nazw instalatora — jednostki synchronizacji — start][]

   1. Wybierz **pozycję Rozpocznij synchronizację,** aby rozpocząć synchronizację encji.
   1. Wybierz **pozycję Tak** w oknie dialogowym, aby potwierdzić i rozpocząć synchronizację.
   1. Poczekaj na zakończenie synchronizacji. Stan jest dostępny na pasku stanu.
        ![Obszar nazw instalacji — jednostki synchronizacji — postęp][]
        >[!IMPORTANT]
        > Jeśli musisz przerwać migrację z jakiegokolwiek powodu, przejrzyj przepływ przerwania w sekcji często zadawanych pytań w tym dokumencie.
   1. Po zakończeniu synchronizacji wybierz **pozycję Dalej** u dołu strony.

1. Przejrzyj zmiany na stronie podsumowania. Wybierz **opcję Zakończ migrację,** aby przełączyć obszary nazw i zakończyć migrację.
    ![Przełączanie przestrzeni nazw - menu przełącznika][]  
    Strona potwierdzenia pojawi się po zakończeniu migracji.
    ![Przełączanie przestrzeni nazw — sukces][]

## <a name="caveats"></a>Zastrzeżenia

Niektóre funkcje udostępniane przez warstwę Standard usługi Azure Service Bus nie są obsługiwane przez warstwę usługi Azure Service Bus Premium. Są one zgodnie z projektem, ponieważ warstwa premium oferuje dedykowane zasoby dla przewidywalnej przepływności i opóźnienia.

Oto lista funkcji nie obsługiwanych przez Premium i ich łagodzenia -

### <a name="express-entities"></a>Jednostki ekspresowe

   Express jednostek, które nie zatwierdzają żadnych danych wiadomości do magazynu nie są obsługiwane w układce. Dedykowane zasoby zapewniają znaczną poprawę przepływności przy jednoczesnym zapewnieniu, że dane są zachowywane, zgodnie z oczekiwaniami z dowolnego systemu obsługi wiadomości w przedsiębiorstwie.

   Podczas migracji dowolna encja ekspresowa w standardowej przestrzeni nazw zostanie utworzona w obszarze nazw Premium jako encja nieokreślna.

   Jeśli korzystasz z szablonów usługi Azure Resource Manager (ARM), upewnij się, że usuniesz flagę "enableExpress" z konfiguracji wdrożenia, aby zautomatyzowane przepływy pracy były wykonywane bez błędów.

### <a name="partitioned-entities"></a>Partycjonowane jednostki

   Jednostki podzielone na partycje były obsługiwane w warstwie Standardowa, aby zapewnić lepszą dostępność w konfiguracji z wieloma dzierżawami. Z zapewnieniem dedykowanych zasobów dostępnych dla obszaru nazw w warstwie Premium, nie jest już potrzebne.

   Podczas migracji każda jednostka podzielona na partycje w standardowej przestrzeni nazw jest tworzona w obszarze nazw Premium jako jednostka nieobjęta partycją.

   Jeśli szablon ARM ustawia "enablePartitioning" na "true" dla określonej kolejki lub tematu, zostanie on zignorowany przez brokera.

## <a name="faqs"></a>Często zadawane pytania

### <a name="what-happens-when-the-migration-is-committed"></a>Co się stanie, gdy migracja zostanie zatwierdzona?

Po przejmującą migrację, ciąg połączenia, który wskazał na standardowy obszar nazw, wskaże obszar nazw premium.

Aplikacje nadawcy i odbiorcy rozłączą się ze standardowym obszarem nazw i automatycznie połączą się z przestrzenią nazw premium.

### <a name="what-do-i-do-after-the-standard-to-premium-migration-is-complete"></a>Co zrobić po zakończeniu migracji standardu do premii?

Standard migracji w ramach usługi premium gwarantuje, że metadane jednostki, takie jak tematy, subskrypcje i filtry, są kopiowane ze standardowego obszaru nazw do obszaru nazw premium. Dane wiadomości, które zostały zatwierdzone do standardowego obszaru nazw nie jest kopiowany ze standardowego obszaru nazw do obszaru nazw premium.

Standardowy obszar nazw może mieć niektóre wiadomości, które zostały wysłane i zatwierdzone podczas migracji. Ręcznie opróżnić te komunikaty ze standardowego obszaru nazw i ręcznie wysłać je do obszaru nazw premium. Aby ręcznie opróżnić wiadomości, użyj aplikacji konsoli lub skryptu, który opróżnia standardowe jednostki obszaru nazw przy użyciu nazwy DNS po migracji określonej w poleceniach migracji. Wyślij te wiadomości do obszaru nazw premium, aby mogły być przetwarzane przez odbiorniki.

Po opróżnieniu wiadomości usuń standardową przestrzeń nazw.

>[!IMPORTANT]
> Po opróżnieniu wiadomości ze standardowego obszaru nazw usuń standardową przestrzeń nazw. Jest to ważne, ponieważ parametry połączenia, które początkowo odwołuje się do standardowego obszaru nazw teraz odwołuje się do obszaru nazw premium. Nie będziesz już potrzebować standardowego obszaru nazw. Usunięcie standardowego obszaru nazw, który został zmigrowany, pomaga zmniejszyć późniejsze zamieszanie.

### <a name="how-much-downtime-do-i-expect"></a>Ile przestojów się spodziewam?

Proces migracji ma na celu zmniejszenie oczekiwanych przestojów dla aplikacji. Czas przestoju jest zmniejszany przy użyciu ciągu połączenia używanego przez aplikacje nadawcy i odbiorcy do wskazywanie nowego obszaru nazw premium.

Przestoje, które są doświadczane przez aplikację jest ograniczona do czasu potrzebny do aktualizacji wpisu DNS, aby wskazać obszar nazw premium. Czas przestoju wynosi około 5 minut.

### <a name="do-i-have-to-make-any-configuration-changes-while-doing-the-migration"></a>Czy muszę wprowadzać zmiany konfiguracji podczas migracji?

Nie, nie ma żadnych zmian kodu lub konfiguracji potrzebne do migracji. Ciąg połączenia używany przez aplikacje nadawcy i odbiorcy do uzyskiwania dostępu do standardowego obszaru nazw jest automatycznie mapowany w celu działania jako alias dla obszaru nazw premium.

### <a name="what-happens-when-i-abort-the-migration"></a>Co się stanie, gdy przerywam migrację?

Migrację można przerwać za pomocą `Abort` polecenia lub za pomocą witryny Azure portal.

#### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
az servicebus migration abort --resource-group $resourceGroup --name $standardNamespace
```

#### <a name="azure-portal"></a>Portal Azure

![Przerwanie przepływu -][]
![abort sync Abort flow - przerwanie zakończone][]

Po przerwaniu procesu migracji przerywa proces kopiowania jednostek (tematów, subskrypcji i filtrów) ze standardowego do obszaru nazw premium i przerywa parowanie.

Parametry połączenia nie są aktualizowane w celu wskazanie obszaru nazw premium. Istniejące aplikacje nadal działają tak, jak przed rozpoczęciem migracji.

Jednak nie usuwa jednostek w obszarze nazw premium ani nie usuwa obszaru nazw premium. Usuń jednostki ręcznie, jeśli nie zdecydujesz się przejść do przodu z migracji.

>[!IMPORTANT]
> Jeśli zdecydujesz się przerwać migrację, usuń obszar nazw w udziale w udziale w udziale w udziale w udziale w udziale administracyjnym dla migracji, aby nie naliczano opłat za zasoby.

#### <a name="i-dont-want-to-have-to-drain-the-messages-what-do-i-do"></a>Nie chcę, aby opróżnić wiadomości. Co mam zrobić?

Mogą istnieć komunikaty, które są wysyłane przez aplikacje nadawcy i zatwierdzone do magazynu w standardowym obszarze nazw podczas migracji i tuż przed zatwierdzeniem migracji.

Podczas migracji rzeczywiste dane wiadomości/ładuwek nie są kopiowane ze standardu do obszaru nazw premium. Wiadomości muszą być ręcznie opróżniane, a następnie wysyłane do obszaru nazw premium.

Jeśli jednak można przeprowadzić migrację podczas planowanego okna konserwacji/sprzątania i nie chcesz ręcznie opróżniać i wysyłać wiadomości, wykonaj następujące kroki:

1. Zatrzymaj aplikacje nadawcy. Aplikacje odbiornika będą przetwarzać komunikaty, które są obecnie w standardowej przestrzeni nazw i opróżnić kolejki.
1. Po kolejki i subskrypcje w standardowym obszarze nazw są puste, wykonaj procedurę opisaną wcześniej, aby wykonać migrację ze standardu do obszaru nazw premium.
1. Po zakończeniu migracji można ponownie uruchomić aplikacje nadawcy.
1. Nadawcy i odbiorcy będą teraz automatycznie łączyć się z przestrzenią nazw premium.

    >[!NOTE]
    > Nie trzeba zatrzymywać aplikacji odbiornika dla migracji.
    >
    > Po zakończeniu migracji aplikacje odbiorcy odłączą się od standardowego obszaru nazw i automatycznie połączą się z obszarem nazw premium.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [różnicach między wiadomościami standardowymi i premium.](./service-bus-premium-messaging.md)
* Dowiedz się więcej o [aspektach odzyskiwania wysokiej dostępności i odzyskiwania po awarii geograficznej dla usługi Service Bus premium](service-bus-outages-disasters.md#protecting-against-outages-and-disasters---service-bus-premium).

[Strona docelowa migracji]: ./media/service-bus-standard-premium-migration/1.png
[Obszar nazw instalacji]: ./media/service-bus-standard-premium-migration/2.png
[Obszar nazw instalacji — tworzenie obszaru nazw premium]: ./media/service-bus-standard-premium-migration/3.png
[Obszar nazw instalacji — wybieranie nazwy migracji po zakończeniu]: ./media/service-bus-standard-premium-migration/4.png
[Obszar nazw instalatora — jednostki synchronizacji — start]: ./media/service-bus-standard-premium-migration/5.png
[Obszar nazw instalacji — jednostki synchronizacji — postęp]: ./media/service-bus-standard-premium-migration/8.png
[Przełączanie przestrzeni nazw - menu przełącznika]: ./media/service-bus-standard-premium-migration/9.png
[Przełączanie przestrzeni nazw — sukces]: ./media/service-bus-standard-premium-migration/12.png

[Przerwanie przepływu - synchronizacja przerwania]: ./media/service-bus-standard-premium-migration/abort1.png
[Przerwanie przepływu - przerwanie zakończone]: ./media/service-bus-standard-premium-migration/abort3.png
