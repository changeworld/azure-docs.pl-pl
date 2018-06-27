---
title: Tworzenie pierwszego niezawodnej mikrousługi Azure w języku Java | Dokumentacja firmy Microsoft
description: Wprowadzenie do tworzenia aplikacji usługi sieć szkieletowa usług Microsoft Azure z usług bezstanowych i stanowych.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: timlt
editor: ''
ms.assetid: 7831886f-7ec4-4aef-95c5-b2469a5b7b5d
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: suhuruli
ms.openlocfilehash: 7e83f141791bb49130f7cf01086537f8ae08c406
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37019699"
---
# <a name="get-started-with-reliable-services"></a>Wprowadzenie do usług Reliable Services
> [!div class="op_single_selector"]
> * [C# w systemie Windows](service-fabric-reliable-services-quick-start.md)
> * [Java w systemie Linux](service-fabric-reliable-services-quick-start-java.md)
>
>

W tym artykule opisano podstawy niezawodne usługi sieci szkieletowej usług Azure i przeprowadzi użytkownika przez proces tworzenia i wdrażania prostą aplikację niezawodnej usługi napisane w języku Java. Microsoft Virtual Academy wideo przedstawiono również sposób tworzenia bezstanowej niezawodnej usługi: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=DOX8K86yC_206218965">  
<img src="./media/service-fabric-reliable-services-quick-start-java/ReliableServicesJavaVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="installation-and-setup"></a>Instalacja i Konfiguracja
Przed rozpoczęciem upewnij się, że masz środowisko projektowe sieci szkieletowej usług na komputerze.
Jeśli musisz je skonfigurować, przejdź do [wprowadzenie dla komputerów Mac](service-fabric-get-started-mac.md) lub [wprowadzenie w systemie Linux](service-fabric-get-started-linux.md).

## <a name="basic-concepts"></a>Podstawowe pojęcia
Aby rozpocząć pracę z usługami Reliable Services, tylko musisz poznać kilka podstawowych pojęć:

* **Typ usługi**: jest to implementacji usługi. Jest on zdefiniowany przez klasę pisania, rozszerzający `StatelessService` i innego kodu lub zależności używany, oraz nazwę i numer wersji.
* **Nazwane wystąpienie usługi**: Aby uruchomić usługę, tworzenia nazwanego wystąpienia danego typu usługi znacznie tak, jak utworzyć wystąpienia obiektu typu klasy. Wystąpienie usługi są w istocie wystąpieniami klasie usługi, który można zapisać obiektu.
* **Host usługi**: tworzenia wystąpień usługi o nazwie trzeba uruchamiać wewnątrz hosta. Host usługi jest właśnie procesu, których można uruchomić wystąpienia usługi.
* **Usługa rejestracji**: rejestracji, połączono wszystko. Typ usługi musi być zarejestrowana ze środowiskiem uruchomieniowym usługi sieć szkieletowa w umożliwia sieci szkieletowej usług do tworzenia wystąpień usługi hosta do uruchomienia.  

## <a name="create-a-stateless-service"></a>Tworzenie usługi bezstanowej
Rozpocznij od utworzenia aplikacji sieci szkieletowej usług. Zestaw SDK sieci szkieletowej usług dla systemu Linux zawiera narzędzia Yeoman generatora, aby zapewnić szkieletów dla aplikacji sieci szkieletowej usług za pomocą usługi bezstanowej. Uruchom, uruchamiając następujące narzędzia Yeoman polecenia:

```bash
$ yo azuresfjava
```

Postępuj zgodnie z instrukcjami, aby utworzyć **usługi bezstanowej niezawodnej**. W tym samouczku, nazwa aplikacji "HelloWorldApplication" i "HelloWorld" usługi. Wynik zawiera katalogów `HelloWorldApplication` i `HelloWorld`.

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
Środowisko uruchomieniowe usługi sieć szkieletowa usług musi być zarejestrowany typów usług. Typ usługi jest zdefiniowany w `ServiceManifest.xml` i klasie usługi, który implementuje `StatelessService`. Rejestracja usługi jest wykonywane w procesie główny punkt wejścia. W tym przykładzie jest proces główny punkt wejścia `HelloWorldServiceHost.java`:

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

## <a name="implement-the-service"></a>Wdrożenie usługi

Otwórz **HelloWorldApplication/HelloWorld/src/statelessservice/HelloWorldService.java**. Ta klasa definiuje typ usługi, a następnie można uruchomić dowolny kod. Interfejs API usługi zawiera dwa punkty wejścia kodu:

* Metoda punktu wejścia otwarty, nazywany `runAsync()`, w którym można rozpocząć wykonywania dowolnych zadań, w tym obciążeń obliczeniowych długotrwałe.

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
    ...
}
```

* Punkt wejścia komunikacji, gdzie można podłączyć stosu komunikacji wyboru. Jest to, gdzie można uruchomić odbieranie żądań od użytkowników i innych usług.

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {
    ...
}
```

Ten samouczek koncentruje się na `runAsync()` metoda punktu wejścia. Jest to, gdzie natychmiast można rozpocząć uruchamianie kodu.

### <a name="runasync"></a>RunAsync
Platforma wywołuje tę metodę, gdy wystąpienie usługi jest umieszczony i gotowa do wykonania. Dla usługi bezstanowej oznacza to, gdy wystąpienie usługi jest otwarty. Token anulowania został dostarczony do koordynowania, gdy wystąpienie usługi musi zostać zamknięty. W sieci szkieletowej usług ten cykl otwarcie i zamknięcie wystąpienia usługi może wystąpić wiele razy w okresie istnienia usługi jako całość. Może się to zdarzyć z różnych powodów, w tym:

* System przeniesie swoich wystąpień usługi dla równoważenia zasobów.
* Błędy występują w kodzie.
* Uaktualnianie aplikacji lub systemu.
* Sprzętu źródłowego ulegnie awarii.

Aranżacja jest zarządzana przez sieci szkieletowej usług do zachowania usługi wysokiej dostępności i nieprawidłowo zrównoważone.

`runAsync()` nie zablokować synchronicznie. Implementacji runAsync powinien zwrócić CompletableFuture umożliwia środowiska wykonawczego kontynuować. Jeśli obciążenie należy zaimplementować długotrwała zadanie, które należy wykonać wewnątrz CompletableFuture.

#### <a name="cancellation"></a>Anulowanie
Anulowanie obciążenie jest współpraca wysiłku, zorkiestrowana przez token anulowania podanego. System oczekuje na zadanie zakończenia (przez pomyślne zakończenie anulowania lub fault), zanim przesyłane w. Ważne jest, aby uwzględnić token anulowania, Zakończ pracę i zamknąć `runAsync()` tak szybko jak to możliwe, gdy system żąda anulowania. W poniższym przykładzie pokazano sposób obsługi zdarzenia anulowania:

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

W tym przykładzie usługi bezstanowej licznika są przechowywane w zmiennej lokalnej. Ale jest usługi bezstanowej, dlatego wartość przechowywana istnieje tylko dla bieżącego cyklu życia jego wystąpienia usługi. Gdy usługa przenosi lub ponownego uruchomienia, wartość jest utracone.

## <a name="create-a-stateful-service"></a>Tworzenie usługi stanowej
Sieć szkieletowa usług wprowadzono nowy rodzaj usługi, który jest obiektem stanowym. Usługa stanowa można zachować stanie niezawodnie w ramach usługi, wspólnie z kodem, który go używa. Stan dokonuje wysokiej dostępności sieci szkieletowej usług bez konieczności utrwalić stanu do magazynu zewnętrznego.

Aby przekonwertować wartość licznika z bezstanowej wysokiej dostępności i trwałe, nawet wtedy, gdy usługi są przenoszone lub ponownego uruchomienia, należy usługi stanowej.

W tym samym katalogu co aplikacja HelloWorld, można dodać nową usługę, uruchamiając `yo azuresfjava:AddService` polecenia. Wybierz pozycję "Niezawodnej Stateful Usługa" dla Twojej platformy i nazwę usługi "HelloWorldStateful". 

Aplikacja powinna mieć teraz dwie usługi: usługi bezstanowej HelloWorld i stateful usługi HelloWorldStateful.

Usługa stanowa ma tego samego punkty wejścia jako usługę bezstanową. Główna różnica polega na dostępności niezawodnie przechowywanie stanu dostawcy stanu. Implementacja dostawcy stanu wywołuje niezawodnej kolekcje, które umożliwia tworzenie struktury replikowanych danych za pośrednictwem niezawodnych menedżera stanu zawiera sieci szkieletowej usług. Domyślnie usługa stanowa niezawodnej używa tego dostawcy stanu.

Otwórz HelloWorldStateful.java w **src -> HelloWorldStateful**, który zawiera następujące metodzie RunAsync:

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
`RunAsync()` działa podobnie, w usługach stanowe i bezstanowe. Jednak w usługi stanowej, platforma wykonuje dodatkowych działań w Twoim imieniu przed rozpoczęciem wykonywania `RunAsync()`. Te działania mogą obejmować zapewnienie, że Menedżer niezawodnej stanu i niezawodne kolekcji są gotowe do użycia.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Kolekcje niezawodnych i niezawodne Menedżer stanu
```java
ReliableHashMap<String,Long> map = this.stateManager.<String, Long>getOrAddReliableHashMapAsync("myHashMap")
```

[ReliableHashMap](https://docs.microsoft.com/java/api/microsoft.servicefabric.data.collections._reliable_hash_map) jest implementacji słownik, który umożliwia niezawodne przechowywanie stanu usługi. Z sieci szkieletowej usług i niezawodne HashMaps można przechowywać dane bezpośrednio w usłudze bez konieczności zewnętrznych magazynu trwałego. Niezawodne HashMaps danych wysokiej dostępności. Sieć szkieletowa usług rozwiązanie to tworzenie i zarządzanie wieloma *replik* usługi dla Ciebie. Udostępnia również interfejs API, który abstracts optymalizacji złożoności Zarządzanie tych replik, a ich przejścia stanu.

Kolekcje niezawodnej może przechowywać dowolnego typu Java, łącznie z niestandardowych typów, za pomocą paru ostrzeżenia:

* Usługa sieci szkieletowej udostępnia swój stan wysokiej przez *replikowanie* stanu między węzły i niezawodne HashMap przechowuje dane na dysku lokalnym na każdej repliki. To oznacza, że wszystkie zasoby, które są przechowywane w niezawodnej HashMaps muszą zostać *serializacji*. 
* Obiekty są replikowane wysokiej dostępności po zatwierdzeniu transakcji na HashMaps wiarygodne. Obiekty przechowywane w niezawodnej HashMaps są przechowywane w lokalnej pamięci w usłudze. Oznacza to, że masz lokalnego odwołania do obiektu.
  
   Należy pamiętać, że nie zmodyfikować lokalnych wystąpień tych obiektów, bez wykonywania operacji update na niezawodne kolekcji w transakcji. Jest to spowodowane zmiany do lokalnego wystąpienia obiektów, nie będą automatycznie replikowane. Musisz ponownie obiekt do słownika lub użyj jednej z *aktualizacji* metody w słowniku.

Menedżer niezawodnej stanu zarządza HashMaps wiarygodne. Poproś niezawodnej Menedżer stanu dla kolekcji niezawodnej według nazwy w dowolnym momencie i w dowolnym miejscu w usłudze. Niezawodne Menedżer stanu zapewnia ponownie uzyskać odwołanie. Nie zaleca się zapisywania odwołań do kolekcji niezawodnej instancji w zmiennych Członkowskich klas lub właściwości. Szczególną uwagę należy upewnić się, że odwołanie jest ustawione na wystąpienie przez cały czas w cyklu życia usługi. Niezawodne Menedżer stanu obsługuje tę pracę za Ciebie i jest zoptymalizowany do powtarzania wizytach.


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

Operacje na niezawodne HashMaps są asynchroniczne. Jest to spowodowane operacji zapisu z kolekcjami niezawodne wykonywanie operacji We/Wy do replikacji i utrwalić danych na dysku.

Niezawodne operacji HashMap *transakcyjnych*, dzięki czemu można zachować stanu spójności między wieloma HashMaps niezawodnych i operacji. Na przykład może pobrać elementu roboczego z jednego słownika niezawodnej, wykonaj operację i zapisać wynik w innym HashMap niezawodne, wszystkie w ramach pojedynczej transakcji. Jest ona traktowana jako operacją niepodzielną i gwarantuje, że cała operacja zostanie wykonana pomyślnie lub całej operacji cofnie. Jeśli błąd wystąpi po dequeue elementu, ale przed zapisaniem wynik, cała transakcja zostanie wycofana i element pozostaje w kolejce do przetworzenia.


## <a name="build-the-application"></a>Kompilowanie aplikacji

Narzędzia Yeoman szkieletów zawiera skrypt narzędzia gradle do tworzenia aplikacji i skryptów służących do wdrażania i Usuń aplikację bash. Aby uruchomić aplikację, należy najpierw utworzyć aplikację z narzędziem gradle:

```bash
$ gradle
```

Daje to pakiet aplikacji sieci szkieletowej usług, które można wdrożyć przy użyciu interfejsu wiersza polecenia usługi sieci szkieletowej.

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
> Aby wdrożyć aplikację do bezpiecznego klastra systemu Linux na platformie Azure, należy skonfigurować certyfikat, aby zweryfikować Twojej aplikacji ze środowiskiem uruchomieniowym usługi sieć szkieletowa usług. Umożliwi to usługi niezawodne usługi, aby komunikować się z podstawowego środowiska uruchomieniowego platformy Service Fabric interfejsów API. Aby dowiedzieć się więcej, zobacz [skonfiguruj aplikację niezawodne usługi, do uruchamiania na systemie Linux klastrów](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters).  
>

## <a name="next-steps"></a>Kolejne kroki

* [Getting started with Service Fabric CLI (Wprowadzenie do interfejsu wiersza polecenia usługi Service Fabric)](service-fabric-cli.md)
