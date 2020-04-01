---
title: Najważniejsze wskazówki — usługa Azure Batch
description: Zapoznaj się z najlepszymi rozwiązaniami i przydatnymi wskazówkami dotyczącymi tworzenia rozwiązania usługi Azure Batch.
author: LauraBrenner
ms.author: labrenne
ms.date: 11/22/2019
ms.service: batch
ms.topic: article
manager: evansma
ms.openlocfilehash: ea5cd8012324cc0727a46308f4d5621f2ce40727
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397925"
---
# <a name="azure-batch-best-practices"></a>Najważniejsze wskazówki dotyczące usługi Azure Batch

W tym artykule omówiono kolekcję najlepszych rozwiązań dotyczących efektywnego i wydajnego korzystania z usługi Azure Batch. Te najlepsze rozwiązania pochodzą z naszego doświadczenia z usługą Batch i środowiskami klientów typu Batch. Ważne jest, aby zrozumieć ten artykuł, aby uniknąć pułapek projektowych, potencjalnych problemów z wydajnością i wzorców anty wzorców podczas tworzenia i używania usługi Batch.

W tym artykule dowiesz się:

> [!div class="checklist"]
> - Jakie są najlepsze praktyki
> - Dlaczego warto korzystać z najlepszych praktyk
> - Co może się zdarzyć, jeśli nie zastosujesz się do najlepszych praktyk
> - Jak postępować zgodnie z najlepszymi praktykami

## <a name="pools"></a>Pule

Pule wsadowe to zasoby obliczeniowe do wykonywania zadań w usłudze Batch. W poniższych sekcjach przedstawiono wskazówki dotyczące najlepszych rozwiązań, które należy stosować podczas pracy z pulami usługi Batch.

### <a name="pool-configuration-and-naming"></a>Konfiguracja i nazewnictwo puli

- **Tryb alokacji puli**  
    Podczas tworzenia konta usługi Batch można wybrać jeden z dwóch trybów alokacji puli: **usługa wsadowa** lub **subskrypcja użytkownika**. W większości przypadków należy użyć domyślnego trybu usługi batch, w którym pule są przydzielane za kulisami w subskrypcjach zarządzanych przez partię. W alternatywnym trybie subskrypcji użytkownika maszyny wirtualne i inne zasoby usługi Batch są tworzone bezpośrednio w Twojej subskrypcji po utworzeniu puli. Konta subskrypcji użytkowników są używane przede wszystkim do włączania ważnego, ale mały podzbiór scenariuszy. Więcej informacji o trybie subskrypcji użytkownika można przeczytać w sekcji [Dodatkowa konfiguracja dla trybu subskrypcji użytkownika](batch-account-create-portal.md#additional-configuration-for-user-subscription-mode).

- **Należy wziąć pod uwagę czas wykonywania zadania i zadania podczas określania zadania do mapowania puli.**  
    Jeśli masz zadania składające się głównie z zadań krótkotrwałych, a oczekiwana całkowita liczba zadań jest mała, dzięki czemu ogólny oczekiwany czas wykonywania zadania nie jest długi, nie przydzielaj nowej puli dla każdego zadania. Czas alokacji węzłów zmniejszy czas wykonywania zadania.

- **Pule powinny mieć więcej niż jeden węzeł obliczeniowy.**  
    Poszczególne węzły nie są gwarantowane, aby zawsze być dostępne. Chociaż nietypowe, awarie sprzętu, aktualizacje systemu operacyjnego i wiele innych problemów może spowodować poszczególne węzły w trybie offline. Jeśli obciążenie usługi Batch wymaga deterministycznego, gwarantowanego postępu, należy przydzielić pule z wieloma węzłami.

- **Nie należy ponownie używać nazw zasobów.**  
    Zasoby wsadowe (zadania, pule itp.) często przychodzą i odchodzą w czasie. Na przykład można utworzyć pulę w poniedziałek, usunąć ją we wtorek, a następnie utworzyć inną pulę w czwartek. Każdy nowy zasób, który tworzysz, powinien mieć unikatową nazwę, która nie była wcześniej używana. Można to zrobić przy użyciu identyfikatora GUID (jako cała nazwa zasobu lub jako jego część) lub osadzania czasu, w jakim zasób został utworzony w nazwie zasobu. Usługa Batch obsługuje [DisplayName](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.jobspecification.displayname?view=azure-dotnet), który może służyć do nadania zasobowi nazwy czytelnej dla człowieka, nawet jeśli rzeczywisty identyfikator zasobu jest czymś, co nie jest przyjazne dla człowieka. Za pomocą unikatowych nazw ułatwia rozróżnienie, który konkretny zasób zrobił coś w dziennikach i metryki. Usuwa również niejednoznaczność, jeśli kiedykolwiek trzeba złożyć przypadek pomocy technicznej dla zasobu.

- **Ciągłość podczas konserwacji i awarii basenu.**  
    Najlepiej, aby twoje zadania korzystały z pul dynamicznie. Jeśli zadania używają tej samej puli do wszystkiego, istnieje szansa, że zadania nie będą uruchamiane, jeśli coś pójdzie nie tak z pulą. Jest to szczególnie ważne w przypadku obciążeń zależnych od czasu. Aby rozwiązać ten problem, należy wybrać lub utworzyć pulę dynamicznie podczas planowania każdego zadania lub mieć sposób na zastąpienie nazwy puli, dzięki czemu można pominąć puli w złej kondycji.

- **Ciągłość działania podczas konserwacji i awarii basenu**  
    Istnieje wiele możliwych przyczyn, które mogą uniemożliwić puli rośnie do wymaganego rozmiaru, który chcesz, takich jak błędy wewnętrzne, ograniczenia pojemności, itp. Z tego powodu należy być gotowy do ponownego kierowania zadań w innej puli (ewentualnie z innym rozmiarem maszyny Wirtualnej — usługa Batch obsługuje to za pośrednictwem [UpdateJob)](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.protocol.joboperationsextensions.update?view=azure-dotnet)w razie potrzeby. Należy unikać używania identyfikatora puli statycznej z oczekiwaniem, że nigdy nie zostaną usunięte i nigdy nie zmieni.

### <a name="pool-lifetime-and-billing"></a>Okres istnienia puli i rozliczenia

Okres istnienia puli może się różnić w zależności od metody alokacji i opcji stosowanych do konfiguracji puli. Pule mogą mieć dowolny okres istnienia i różną liczbę węzłów obliczeniowych w puli w dowolnym momencie. Twoim obowiązkiem jest zarządzanie węzłami obliczeniowymi w puli jawnie lub za pośrednictwem funkcji udostępnianych przez usługę (skalowanie automatyczne lub autopool).

- **Baseny należy zachować w świeżości.**  
    Należy zmienić rozmiar pul do zera co kilka miesięcy, aby upewnić się, że masz najnowsze aktualizacje agenta węzła i poprawki błędów. Pula nie będzie otrzymywać aktualizacji agenta węzła, chyba że zostanie odtworzona lub przesuń na 0 węzłów obliczeniowych. Przed ponownymtworzeniem lub ponownym rozmiarem puli zaleca się pobranie dzienników dowolnego agenta węzła do celów debugowania, zgodnie z omówieniem w sekcji [Węzły.](#nodes)

- **Ponowne tworzenie puli**  
    W podobnym tonie nie zaleca się codziennego usuwania i tworzenia pul. Zamiast tego utwórz nową pulę, zaktualizuj istniejące zadania, aby wskazywały nową pulę. Po przeniesieniu wszystkich zadań do nowej puli usuń starą pulę.

- **Wydajność puli i rozliczenia**  
    Sama partia nie wiąże się z dodatkowymi opłatami, ale opłaty są naliczane za użyte zasoby obliczeniowe. Naliczane są naliczane płatności za każdy węzeł obliczeniowy w puli, niezależnie od stanu, w jakiej się znajduje. Obejmuje to wszelkie opłaty wymagane do uruchomienia węzła, takie jak koszty magazynu i sieci. Aby poznać więcej najlepszych rozwiązań, zobacz [Analiza kosztów i budżety usługi Azure Batch](budget.md).

### <a name="pool-allocation-failures"></a>Błędy alokacji puli

Błędy alokacji puli może się zdarzyć w dowolnym momencie podczas pierwszej alokacji lub kolejnych rozmiarów. Może to być spowodowane wyczerpaniem zdolności tymczasowych w regionie lub awariami w innych usługach platformy Azure, na których opiera się usługa Batch. Twój podstawowy przydział nie jest gwarancją, ale raczej limitem.

### <a name="unplanned-downtime"></a>Nieplanowany przestój

Pule usługi Batch mogą doświadczać zdarzeń przestojów na platformie Azure. Jest to ważne, aby pamiętać podczas planowania i tworzenia scenariusza lub przepływu pracy dla usługi Batch.

W przypadku awarii węzła usługa Batch automatycznie próbuje odzyskać te węzły obliczeniowe w Twoim imieniu. Może to wyzwolić ponowne zaplanowanie dowolnego uruchomionego zadania w węźle, który jest odzyskiwany. Zobacz [Projektowanie ponownych prób,](#designing-for-retries-and-re-execution) aby dowiedzieć się więcej o przerwanych zadaniach.

- **Zależność regionu platformy Azure**  
    Zaleca się, aby nie zależeć od jednego regionu platformy Azure, jeśli masz obciążenie zależne od czasu lub produkcji. Chociaż rzadko, istnieją problemy, które mogą mieć wpływ na cały region. Jeśli na przykład przetwarzanie musi się rozpocząć o określonej godzinie, należy rozważyć skalowanie puli w regionie podstawowym na *długo przed godziną rozpoczęcia.* Jeśli skala puli nie powiedzie się, można wrócić do skalowania puli w regionie kopii zapasowej (lub regionów). Pule na wielu kontach w różnych regionach zapewniają gotową i łatwo dostępną kopię zapasową, jeśli coś pójdzie nie tak z inną pulą. Aby uzyskać więcej informacji, zobacz [Projektowanie aplikacji w celu uzyskania wysokiej dostępności](high-availability-disaster-recovery.md).

## <a name="jobs"></a>Stanowiska

Zadanie to kontener zaprojektowany z myślą o setkach, tysiącach, a nawet milionach zadań.

- **Umieszczanie wielu zadań w zadaniu**  
    Używanie zadania do uruchamiania pojedynczego zadania jest nieefektywne. Na przykład bardziej efektywne jest użycie pojedynczego zadania zawierającego 1000 zadań, zamiast tworzenia 100 zadań zawierających po 10 zadań. Uruchamianie 1000 zadań, z których każde z jednym zadaniem, byłoby najmniej wydajnym, najwolniejszym i najdroższym podejściem do podjęcia.  

    Nie należy projektować rozwiązania batch, które wymaga tysięcy jednocześnie aktywnych zadań. Nie ma przydziału dla zadań, więc wykonywanie jak największej liczby zadań w ramach jak najmniejszej liczby zadań efektywnie wykorzystuje [przydziały harmonogramu zadań i zadań.](batch-quota-limit.md#resource-quotas)

- **Okres istnienia zadania**  
    Zadanie wsadowe ma nieokreślony okres istnienia, dopóki nie zostanie usunięty z systemu. Stan zadania określa, czy może akceptować więcej zadań do planowania, czy nie. Zadanie nie jest automatycznie przesunięty do stanu ukończone, chyba że jawnie zakończone. Może to być automatycznie wyzwalane przez [onAllTasksComplete](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.common.onalltaskscomplete?view=azure-dotnet) właściwości lub [maxWallClockTime](https://docs.microsoft.com/rest/api/batchservice/job/add#jobconstraints).

Istnieje domyślny [aktywny przydział zadania i harmonogramu zadań](batch-quota-limit.md#resource-quotas). Zadania i harmonogramy zadań w stanie ukończonym nie są wliczane do tego przydziału.

## <a name="tasks"></a>Zadania

Zadania są poszczególnymi jednostkami pracy, które składają się na pracę. Zadania są przesyłane przez użytkownika i zaplanowane przez partię do węzłów obliczeniowych. Istnieje kilka zagadnień projektowych, które należy wprowadzić podczas tworzenia i wykonywania zadań. W poniższych sekcjach opisano typowe scenariusze i sposób projektowania zadań do obsługi problemów i wydajne wykonywanie.

- **Zapisz dane zadania jako część zadania.**  
    Węzły obliczeniowe są ze swej natury efemeryczne. Istnieje wiele funkcji w partii, takich jak autopool i skalowanie automatyczne, które ułatwiają węzły znikają. Gdy węzły opuszczają pulę (z powodu zmiany rozmiaru lub usunięcia puli) wszystkie pliki w tych węzłach są również usuwane. Z tego powodu zaleca się, że przed zakończeniem zadania przenosi jego dane wyjściowe z węzła, w który jest uruchomiony i do magazynu trwałe, podobnie jeśli zadanie nie powiedzie się, należy przenieść dzienniki wymagane do zdiagnozowania awarii do magazynu trwałe. Usługa Batch zintegrowała obsługę usługi Azure Storage w celu przekazywania danych za pośrednictwem [plików OutputFiles,](batch-task-output-files.md)a także różnych udostępnionych systemów plików lub można wykonać przekazywanie samodzielnie w zadaniach.

### <a name="task-lifetime"></a>Okres istnienia zadania

- **Usuń zadania po ich zakończeniu.**  
    Usuń zadania, gdy nie są już potrzebne, lub ustaw ograniczenie zadania [retentionTime.](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskconstraints.retentiontime?view=azure-dotnet) Jeśli `retentionTime` a jest ustawiona, usługa Batch automatycznie czyści miejsce `retentionTime` na dysku używane przez zadanie po wygaśnięciu.  

    Usuwanie zadań kończy dwie rzeczy. Zapewnia, że nie masz nagromadzenie zadań w zadaniu, co wykonywanie zapytań/znajdowanie zadania, które ciężej (ponieważ trzeba będzie filtrować ukończone zadania). Czyści również odpowiednie dane zadania w węźle (pod warunkiem, `retentionTime` że nie został jeszcze trafiony). Dzięki temu węzły nie wypełniają się danymi zadań i zabraknie miejsca na dysku.

### <a name="task-submission"></a>Przesyłanie zadań

- **Prześlij dużą liczbę zadań w kolekcji.**  
    Zadania można składać indywidualnie lub w zbiorach. Przesyłanie zadań w [kolekcjach](https://docs.microsoft.com/rest/api/batchservice/task/addcollection) do 100 w czasie podczas zbiorczego przesyłania zadań w celu skrócenia czasu narzutów i przesyłania.

### <a name="task-execution"></a>Wykonanie zadania

- **Wybieranie maksymalnej mocy zadań na węzeł**  
    Usługa Batch obsługuje zadania nadsubskrybowania w węzłach (uruchamianie większej liczby zadań niż węzeł ma rdzenie). To do Ciebie, aby upewnić się, że zadania "pasuje" do węzłów w puli. Na przykład może mieć zdegradowane środowisko, jeśli spróbujesz zaplanować osiem zadań, z których każdy `maxTasksPerNode = 8`zużywa 25% użycia procesora CPU na jednym węźle (w puli z ).

### <a name="designing-for-retries-and-re-execution"></a>Projektowanie ponownych prób i ponownego wykonania

Zadania mogą być automatycznie ponawiane przez partię. Istnieją dwa typy ponownych prób: kontrolowane przez użytkownika i wewnętrzne. Próby pońomek kontrolowane przez użytkownika są określane przez [maxTaskRetryCount](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskconstraints.maxtaskretrycount?view=azure-dotnet)zadania . Gdy program określony w zadaniu kończy pracę z kodem zakończenia niezerowego, zadanie `maxTaskRetryCount`jest ponawiane do wartości .

Chociaż rzadko zadanie może być ponowione wewnętrznie z powodu awarii w węźle obliczeniowym, takich jak nie jest w stanie zaktualizować stan wewnętrzny lub błąd w węźle, gdy zadanie jest uruchomione. Zadanie zostanie ponowione w tym samym węźle obliczeniowym, jeśli to możliwe, do wewnętrznego limitu przed rezygnacją z zadania i odroczeniem zadania, które ma zostać przełożone przez firmę Batch, potencjalnie w innym węźle obliczeniowym.

- **Tworzenie trwałych zadań**  
    Zadania powinny być zaprojektowane tak, aby wytrzymać awarie i dostosować ponawianie próby. Jest to szczególnie ważne w przypadku długotrwałych zadań. Aby to zrobić, upewnij się, że zadania generują ten sam, pojedynczy wynik, nawet jeśli są uruchamiane więcej niż jeden raz. Jednym ze sposobów osiągnięcia tego celu jest uczynienie swoich zadań "poszukiwaniem celu". Innym sposobem jest upewnienie się, że zadania są idempotentne (zadania będą miały taki sam wynik, niezależnie od tego, ile razy są uruchamiane).

    Typowym przykładem jest zadanie kopiowania plików do węzła obliczeniowego. Proste podejście jest zadanie, które kopiuje wszystkie określone pliki za każdym razem, gdy działa, który jest nieefektywny i nie jest zbudowany, aby wytrzymać awarię. Zamiast tego należy utworzyć zadanie, aby upewnić się, że pliki znajdują się w węźle obliczeniowym; zadanie, które nie jest ponowniecopy plików, które są już obecne. W ten sposób zadanie odbiera się tam, gdzie zostało przerwane, jeśli zostało przerwane.

- **Węzły o niskim priorytecie**  
    Nie ma żadnych różnic projektowych podczas wykonywania zadań w węzłach dedykowanych lub o niskim priorytecie. Niezależnie od tego, czy zadanie jest wywłaszczone podczas uruchamiania w węźle o niskim priorytecie, czy przerwane z powodu awarii w dedykowanym węźle, obie sytuacje są ograniczane przez zaprojektowanie zadania w celu wytrzymywania awarii.

- **Czas wykonania zadania**  
    Unikaj zadań z krótkim czasem wykonania. Zadania, które są uruchamiane tylko przez jedną do dwóch sekund, nie są idealne. Należy starać się wykonać znaczną ilość pracy w indywidualnym zadaniu (minimum 10 sekund, przechodząc do godzin lub dni). Jeśli każde zadanie jest wykonywane przez jedną minutę (lub więcej), obciążenie harmonogramu jako ułamek całkowitego czasu obliczeń jest mały.

## <a name="nodes"></a>Węzły

- **Uruchamianie zadań powinno być idempotentne**  
    Podobnie jak w przypadku innych zadań, zadanie uruchamiania węzła powinno być idempotentne, ponieważ będzie uruchamiane ponownie za każdym razem, gdy węzeł zostanie uruchomiony. Idempotentne zadanie jest po prostu taki, który daje spójny wynik, gdy uruchamia się wiele razy.

- **Zarządzanie długotrwałymi usługami za pośrednictwem interfejsu usług systemu operacyjnego.**  
    Czasami istnieje potrzeba uruchomienia innego agenta obok agenta batch w węźle, na przykład, aby zebrać dane z węzła i zgłosić go. Zaleca się, aby te środki były wdrażane jako usługi systemu `systemd` operacyjnego, takie jak usługa systemu Windows lub usługa Linux.  

    Podczas uruchamiania tych usług, nie mogą przyjmować blokad plików na żadnych plików w katalogach zarządzanych przez wsad w węźle, ponieważ w przeciwnym razie usługa Batch nie będzie mogła usunąć tych katalogów z powodu blokad plików. Na przykład, jeśli instalacja usługi systemu Windows w zadaniu startowym zamiast uruchamiania usługi bezpośrednio z katalogu roboczego zadania uruchamiania, skopiuj pliki w innym miejscu (jeśli pliki istnieją, po prostu pomiń kopię). Zainstaluj usługę z tej lokalizacji. Po ponownym uruchomieniu zadania początkowego usługa Batch spowoduje usunięcie katalogu roboczego zadania startowego i utworzenie go na nowo. To działa, ponieważ usługa ma blokady plików w innym katalogu, a nie w katalogu roboczym zadania uruchamiania.

- **Unikanie tworzenia połączeń katalogów w systemie Windows**  
    Połączenia katalogowe, czasami nazywane twardymi łączami katalogu, są trudne do rozwiązania podczas oczyszczania zadań i zadań. Użyj dowiązań symbolicznych (linków miękkich), a nie linków twardych.

- **Zbieranie dzienników agenta wsadowego, jeśli występuje problem**  
    Jeśli zauważysz problem związany z zachowaniem węzła lub zadań uruchomionych w węźle, zaleca się zbieranie dzienników agenta usługi Batch przed przydzieleniem danych węzłów. Dzienniki agenta usługi Batch mogą być zbierane przy użyciu interfejsu API dzienników usługi przekazywania wsadowego. Te dzienniki mogą być dostarczane jako część biletu pomocy technicznej do firmy Microsoft i pomoże w rozwiązywaniu problemów i rozwiązywaniu problemów.

## <a name="security"></a>Zabezpieczenia

### <a name="security-isolation"></a>Izolacja zabezpieczeń

Na potrzeby izolacji, jeśli scenariusz wymaga izolowania zadań od siebie, następnie należy wyizolować te zadania, mając je w oddzielnych pulach. Pula jest granicą izolacji zabezpieczeń w udziale usługi Batch i domyślnie dwie pule nie są widoczne lub mogą komunikować się ze sobą. Należy unikać używania oddzielnych kont partii jako środka izolacji.

## <a name="moving"></a>Przenoszenie

### <a name="move-batch-account-across-regions"></a>Przenoszenie konta usługi Batch między regionami 

Istnieją różne scenariusze, w których chcesz przenieść istniejące konto usługi Batch z jednego regionu do drugiego. Na przykład można przenieść do innego regionu w ramach planowania odzyskiwania po awarii.

Nie można przenieść kont usługi Azure Batch z jednego regionu do drugiego. Można jednak użyć szablonu usługi Azure Resource Manager, aby wyeksportować istniejącą konfigurację konta usługi Batch.  Następnie można zorganizować zasób w innym regionie, eksportując konto usługi Batch do szablonu, modyfikując parametry zgodne z regionem docelowym, a następnie wdrażając szablon w nowym regionie. Po przekazaniu szablonu do nowego regionu należy ponownie utworzyć certyfikaty, harmonogramy zadań i pakiety aplikacji. Aby zatwierdzić zmiany i zakończyć przenoszenie konta usługi Batch, należy pamiętać o usunięciu oryginalnego konta usługi Batch lub grupy zasobów.  

Aby uzyskać więcej informacji na temat Menedżera zasobów i szablonów, zobacz [Szybki start: Tworzenie i wdrażanie szablonów usługi Azure Resource Manager przy użyciu portalu Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).


