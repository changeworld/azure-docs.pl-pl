---
title: Tworzenie pierwszej niezawodnej usługi Azure Service Fabric w języku Java | Dokumentacja firmy Microsoft
description: Wprowadzenie do tworzenia aplikacji usługi Microsoft Azure Service Fabric za pomocą usługi stanowe i bezstanowe.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: chackdan
editor: ''
ms.assetid: 7831886f-7ec4-4aef-95c5-b2469a5b7b5d
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: suhuruli
ms.openlocfilehash: 6bf8c632a7513d018745bc74aa0a1db95a39af8b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130130"
---
# <a name="get-started-with-reliable-services"></a>Wprowadzenie do usług Reliable Services
> [!div class="op_single_selector"]
> * [C# w systemie Windows](service-fabric-reliable-services-quick-start.md)
> * [Java w systemie Linux](service-fabric-reliable-services-quick-start-java.md)
>
>

W tym artykule wyjaśniono podstawowe informacje dotyczące usług Reliable Services usługi Azure Service Fabric i prowadzi użytkownika przez tworzenie i wdrażanie prostej aplikacji usługi Reliable Service napisaną w języku Java. 

## <a name="installation-and-setup"></a>Instalacja i Konfiguracja
Przed rozpoczęciem upewnij się, że masz na swojej maszynie środowiska programistycznego usługi Service Fabric.
Jeśli musisz je skonfigurować, przejdź do strony [wprowadzenie na komputerze Mac](service-fabric-get-started-mac.md) lub [wprowadzenie w systemie Linux](service-fabric-get-started-linux.md).

## <a name="basic-concepts"></a>Podstawowe pojęcia
Aby rozpocząć pracę z usługami Reliable Services, należy tylko poznać kilka podstawowych pojęć:

* **Typ usługi**: Jest to Twoja implementacja usługi. Jest definicją klasy pisania, która rozszerza `StatelessService` i innego kodu lub zależności razem z nim, nazwę i numer wersji.
* **Nazwane wystąpienie usługi**: Aby uruchomić usługę, należy utworzyć nazwanych wystąpień danego typu usługi znacznie jak tworzenia wystąpienia obiektu typu klasy. Wystąpienia usługi są w rzeczywistości wystąpieniami obiektu w klasie usługi, które piszesz.
* **Host usługi**: Utworzonych wystąpień usługi o nazwie konieczne uruchamiane wewnątrz hosta. Host usługi jest po prostu procesu, gdzie można uruchomić wystąpienia usługi.
* **Usługa rejestracji**: Rejestracja łączy wszystkie elementy. Typ usługi musi być zarejestrowany ze środowiskiem uruchomieniowym usługi Service Fabric na hoście usługi, aby umożliwić usługi Service Fabric utworzyć jego wystąpienia do uruchomienia.  

## <a name="create-a-stateless-service"></a>Tworzenie usługi bezstanowej
Rozpocznij od utworzenia aplikacji usługi Service Fabric. Service Fabric SDK dla systemu Linux zawiera narzędzia Yeoman generator zapewnienie funkcją szkieletów dla aplikacji usługi Service Fabric o bezstanowa usługa. Uruchom, uruchamiając następujące narzędzia Yeoman polecenia:

```bash
$ yo azuresfjava
```

Postępuj zgodnie z instrukcjami, aby utworzyć **niezawodnej usługi bezstanowej**. W tym samouczku Nazwa aplikacji "HelloWorldApplication" i usługi "nazwę HelloWorld". Wynik zawiera katalogi, aby `HelloWorldApplication` i `HelloWorld`.

```bash
HelloWorldApplication/
├── build.gradle
├── HelloWorld
│   ├── build.gradle
│   └── src
│       └── statelessservice
│           ├── HelloWorldServiceHost.java
│           └── HelloWorldService.java
├── HelloWorldApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   └── _readme.txt
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── install.sh
├── settings.gradle
└── uninstall.sh
```
### <a name="service-registration"></a>Rejestracja usługi
Typy usług musi być zarejestrowany w środowisku uruchomieniowym usługi Service Fabric. Typ usługi jest zdefiniowany w `ServiceManifest.xml` i klasie usługi, który implementuje `StatelessService`. Rejestracja usługi jest wykonywane w procesie główny punkt wejścia. W tym przykładzie jest proces główny punkt wejścia `HelloWorldServiceHost.java`:

```java
public static void main(String[] args) throws Exception {
    try {
        ServiceRuntime.registerStatelessServiceAsync("HelloWorldType", (context) -> new HelloWorldService(), Duration.ofSeconds(10));
        logger.log(Level.INFO, "Registered stateless service type HelloWorldType.");
        Thread.sleep(Long.MAX_VALUE);
    }
    catch (Exception ex) {
        logger.log(Level.SEVERE, "Exception in registration:", ex);
        throw ex;
    }
}
```

## <a name="implement-the-service"></a>Implementuje usługi

Otwórz **HelloWorldApplication/HelloWorld/src/statelessservice/HelloWorldService.java**. Ta klasa definiuje typ usługi, a następnie może uruchomić dowolny kod. Interfejs API usługi zawiera dwa punkty wejścia w kodzie:

* Metody punktu wejścia nieograniczony, o nazwie `runAsync()`, w którym możesz rozpocząć wykonywanie obciążeń, w tym obciążeń obliczeniowych długoterminowych.

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
    ...
}
```

* Punkt wejścia komunikacji, gdzie możesz podłączyć w wybranym stosie komunikacji. Jest to, gdzie można uruchomić odbieranie żądań od użytkowników i innych usług.

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {
    ...
}
```

Ten samouczek koncentruje się na `runAsync()` metody punktu wejścia. Jest to, gdzie można natychmiast rozpocząć wykonywanie Twojego kodu.

### <a name="runasync"></a>RunAsync
Platforma wywołuje tę metodę, gdy wystąpienie usługi jest umieszczony i gotowe do wykonania. Usługi bezstanowej oznacza to, gdy wystąpienie usługi jest otwarty. Token anulowania jest udostępniane na koordynowanie czasu wystąpienia usługi musi zostać zamknięty. W usłudze Service Fabric ten cykl otwarcie i zamknięcie wystąpienia usługi może wystąpić wiele razy w okresie istnienia usługi jako całości. Może się to zdarzyć różnych powodów, takich jak:

* System przenosi wystąpień usługi dla równoważenia zasobów.
* Błędy występują w kodzie.
* Uaktualnieniu aplikacji lub systemu.
* Używany sprzęt, wystąpi awaria.

Takie ograniczenia jest zarządzany przez usługę Service Fabric do zachowania usługi o wysokiej dostępności i prawidłowo o zrównoważonym obciążeniu.

`runAsync()` należy nie blokuje synchronicznie. Implementacja runAsync powinien zwrócić CompletableFuture, aby umożliwić środowiska uruchomieniowego kontynuować. Jeśli obciążenie należy zaimplementować długotrwałe zadanie, które ma być rozpoczynane wewnątrz CompletableFuture.

#### <a name="cancellation"></a>Unieważnieniu
Anulowanie obciążenia jest wspólnego nakładu pracy, zorganizowanych według podanego tokenu anulowania. System oczekuje na zadanie zakończenia (za pomyślne wykonanie, anulowania lub błędu), zanim przemieszczał się. Jest ważne uwzględnić token anulowania, Zakończ wszelkie prace i zamknąć `runAsync()` tak szybko, jak to możliwe, gdy system zażąda anulowania. Poniższy przykład pokazuje jak obsłużyć zdarzenie anulowania:

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {

    // TODO: Replace the following sample code with your own logic
    // or remove this runAsync override if it's not needed in your service.

    return CompletableFuture.runAsync(() -> {
        long iterations = 0;
        while(true)
        {
        cancellationToken.throwIfCancellationRequested();
        logger.log(Level.INFO, "Working-{0}", ++iterations);

        try {
            Thread.sleep(1000);
        } catch (InterruptedException ex){}
        }
    });
}
```

W tym przykładzie Usługa bezstanowa liczby są przechowywane w zmiennej lokalnej. Ale ponieważ usługę bezstanową, wartość, która jest przechowywana istnieje tylko dla bieżącego cyklu życia wystąpienia usługi. Gdy usługa przejdzie lub ponowne uruchomienie, wartość jest utracone.

## <a name="create-a-stateful-service"></a>Tworzenie stanowej usługi
Usługa Service Fabric wprowadza nowy rodzaj usługi stanowej. Usługa stanowa może zachowywać stan, niezawodne w ramach usługi, wspólnie z kodem, który jest używany. Stan dokonuje wysokiej dostępności usługi Service Fabric bez konieczności utrwalanie stanu do magazynu zewnętrznego.

Aby przekonwertować wartość licznika z bezstanową wysoko dostępnych i trwałych, nawet wtedy, gdy usługa przejdzie lub ponowne uruchomienie, potrzebujesz usługi stanowej.

W tym samym katalogu co aplikacja HelloWorld można dodać nowej usługi, uruchamiając `yo azuresfjava:AddService` polecenia. Wybierz pozycję "Reliable Stateful Usługa" dla Twojej struktury i nazwij usługę "HelloWorldStateful". 

Twoja aplikacja powinna mieć teraz dwie usługi: HelloWorldStateful usługi bezstanowej usługi HelloWorld i stateful.

Usługa stanowa ma ten sam punkty wejścia jako bezstanowej usługi. Główna różnica polega na dostępność dostawcy stanu, który niezawodnego przechowywania stanu. Usługa Service Fabric jest powiązana z implementacja dostawcy stanu o nazwie elementów Reliable Collections, które umożliwia tworzenie struktur danych replikowanych za pośrednictwem Reliable State Manager. Domyślnie stanowej usługi Reliable Service używa tego dostawcy stanu.

Otwórz HelloWorldStateful.java w **HelloWorldStateful -> src**, który zawiera następujące metody RunAsync:

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
    Transaction tx = stateManager.createTransaction();
    return this.stateManager.<String, Long>getOrAddReliableHashMapAsync("myHashMap").thenCompose((map) -> {
        return map.computeAsync(tx, "counter", (k, v) -> {
            if (v == null)
                return 1L;
            else
                return ++v;
            }, Duration.ofSeconds(4), cancellationToken)
                .thenCompose((r) -> tx.commitAsync())
                .whenComplete((r, e) -> {
            try {
                tx.close();
            } catch (Exception e) {
                logger.log(Level.SEVERE, e.getMessage());
            }
        });
    });
}
```

### <a name="runasync"></a>RunAsync
`RunAsync()` działa podobnie w usługi stanowe i bezstanowe. Jednak w stanowej usłudze platformy wykonuje dodatkowej pracy w Twoim imieniu przed rozpoczęciem wykonywania `RunAsync()`. Ta praca może obejmować zapewnienie, że Reliable State Manager i elementów Reliable Collections są gotowe do użycia.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Niezawodne kolekcje i niezawodne Menedżer stanu
```java
ReliableHashMap<String,Long> map = this.stateManager.<String, Long>getOrAddReliableHashMapAsync("myHashMap")
```

[ReliableHashMap](https://docs.microsoft.com/java/api/microsoft.servicefabric.data.collections.reliablehashmap) stanowi implementację słownika, która służy do niezawodnego przechowywania stanu usługi. Usługa Service Fabric i niezawodne HashMaps dane można przechowywać bezpośrednio w usłudze bez konieczności dla zewnętrznego magazynu trwałego. Niezawodne HashMaps danych wysokiej dostępności. Usługa Service Fabric to w ramach przez tworzenie i zarządzanie wieloma *replik* usługi dla Ciebie. Udostępnia również interfejs API, który pozwala zmniejszyć wagę komplikacje związane z zarządzaniem tych replik oraz ich stanami.

Elementy Reliable Collections może przechowywać dowolnego typu języka Java, łącznie z typów niestandardowych przy użyciu kilku ostrzeżenia:

* Usługa Service Fabric zapewnia wysoce dostępny stan programu *replikowanie* stanu między węzłami oraz niezawodnej HashMap przechowuje dane na dysku lokalnym na każdej repliki. Oznacza to wszystko, co jest przechowywany w niezawodnej HashMaps musi być *serializacji*. 
* Obiekty są replikowane w celu zapewnienia wysokiej dostępności podczas zatwierdzania transakcji na niezawodne HashMaps. Obiekty przechowywane w niezawodnej HashMaps są przechowywane w pamięci lokalnej w usłudze. Oznacza to, że masz lokalnego odwołania do obiektu.
  
   Należy pamiętać, że mutuje lokalnych wystąpień tych obiektów bez przeprowadzania operacji aktualizacji w niezawodnej kolekcji w ramach transakcji. Jest to spowodowane zmian do lokalnego wystąpienia obiektów, nie będą automatycznie replikowane. Należy ponownie obiekt do słownika lub użyj jednego z *aktualizacji* metod w słowniku.

Reliable State Manager zarządza HashMaps niezawodne. Możesz poprosić Reliable State Manager for reliable collection według nazwy w dowolnym czasie i w dowolnym miejscu w usłudze. Reliable State Manager zapewnia ponownie uzyskać odwołanie. Nie zaleca się zapisać odwołania do niezawodnej kolekcji wystąpień w zmiennych elementu członkowskiego klasy lub właściwości. Specjalne należy uważać, aby upewnić się, że odwołanie jest ustawione na wystąpienie przez cały czas w cyklu życia usług. Reliable State Manager obsługuje tę pracę za Ciebie, i jest zoptymalizowany do powtarzania wizyty.


### <a name="transactional-and-asynchronous-operations"></a>Operacje transakcyjne i asynchroniczne
```java
return map.computeAsync(tx, "counter", (k, v) -> {
    if (v == null)
        return 1L;
    else
        return ++v;
    }, Duration.ofSeconds(4), cancellationToken)
        .thenCompose((r) -> tx.commitAsync())
        .whenComplete((r, e) -> {
    try {
        tx.close();
    } catch (Exception e) {
        logger.log(Level.SEVERE, e.getMessage());
    }
});
```

Operacje na niezawodne HashMaps są asynchroniczne. Jest to spowodowane operacji zapisu z elementami Reliable Collections wykonywać operacje We/Wy do replikacji i utrwalić dane na dysku.

Niezawodne operacje HashMap *transakcyjnych*, dzięki czemu można zachować stan spójny między wieloma HashMaps niezawodne i operacji. Na przykład może pobrać elementu roboczego z jednego niezawodnego słownika, wykonaj operację i zapisać wynik w innym HashMap niezawodne, wszystkie w ramach jednej transakcji. Jest ona traktowana jako operację niepodzielną i gwarantuje, że cała operacja powiedzie się lub cała operacja spowoduje przywrócenie. Jeśli błąd wystąpi po usuwania z kolejki elementu, ale przed zapisaniem wynik, cała transakcja zostanie wycofana, a element pozostaje w kolejce do przetworzenia.


## <a name="build-the-application"></a>Kompilowanie aplikacji

Narzędzia Yeoman tworzenia szkieletów zawiera skrypt gradle do kompilowania aplikacji i skryptów do wdrażania i usunąć aplikację powłoki bash. Aby uruchomić aplikację, należy najpierw utworzyć aplikację za pomocą narzędzia gradle:

```bash
$ gradle
```

Daje to pakiet aplikacji usługi Service Fabric, który można wdrożyć przy użyciu interfejsu wiersza polecenia usługi Service Fabric.

## <a name="deploy-the-application"></a>Wdrażanie aplikacji

Skompilowaną aplikację można wdrożyć w klastrze lokalnym.

1. Połącz się z lokalnym klastrem usługi Service Fabric.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Uruchom skrypt instalacji udostępniony w szablonie, aby skopiować pakiet aplikacji do magazynu obrazów klastra, zarejestrować typ aplikacji i utworzyć wystąpienie aplikacji.

    ```bash
    ./install.sh
    ```

Wdrażanie skompilowanej aplikacji przebiega tak samo jak w przypadku innych aplikacji usługi Service Fabric. Szczegółowe instrukcje są dostępne w dokumentacji dotyczącej [zarządzania aplikacją usługi Service Fabric za pomocą interfejsu wiersza polecenia usługi Service Fabric](service-fabric-application-lifecycle-sfctl.md).

Parametry tych poleceń można znaleźć w manifestach wygenerowanych w pakiecie aplikacji.

Po wdrożeniu aplikacji otwórz przeglądarkę i przejdź do narzędzia [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) pod adresem [http://localhost:19080/Explorer](http://localhost:19080/Explorer). Następnie rozwiń węzeł **Aplikacje** i zwróć uwagę, że istnieje teraz wpis dla danego typu aplikacji i inny wpis dla pierwszego wystąpienia tego typu.

> [!IMPORTANT]
> Aby wdrożyć aplikację do zabezpieczonego klastra systemu Linux na platformie Azure, musisz skonfigurować certyfikat na potrzeby weryfikacji aplikacji w środowisku uruchomieniowym usługi Service Fabric. Umożliwi to usług Reliable Services do komunikowania się z podstawowego środowiska uruchomieniowego usługi Service Fabric interfejsów API. Aby dowiedzieć się więcej, zobacz [skonfigurować aplikację usług Reliable Services, aby działały w klastrach systemu Linux](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters).  
>

## <a name="next-steps"></a>Kolejne kroki

* [Getting started with Service Fabric CLI (Wprowadzenie do interfejsu wiersza polecenia usługi Service Fabric)](service-fabric-cli.md)
