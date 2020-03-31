---
title: Stwórz swoją pierwszą niezawodną usługę w javie
description: Wprowadzenie do tworzenia aplikacji sieci szkieletowej usług Microsoft Azure z usługami bezstanowymi i stanowymi.
author: suhuruli
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: suhuruli
ms.openlocfilehash: c3b301a7a9039f1fe8095950f0a5a4e23eb52a9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614217"
---
# <a name="get-started-with-reliable-services-in-java"></a>Wprowadzenie do niezawodnych usług w języku Java
> [!div class="op_single_selector"]
> * [C# w systemie Windows](service-fabric-reliable-services-quick-start.md)
> * [Java w systemie Linux](service-fabric-reliable-services-quick-start-java.md)
>
>

W tym artykule opisano podstawy usług azure service fabric reliable services i przeprowadzi Cię przez tworzenie i wdrażanie prostej aplikacji niezawodnej usługi napisanej w języku Java. 

## <a name="installation-and-setup"></a>Instalacja i konfiguracja
Przed rozpoczęciem upewnij się, że na komputerze skonfigurowane jest środowisko programistyczne sieci szkieletowej usług.
Jeśli chcesz go skonfigurować, przejdź do [rozpoczęcia pracy na macu](service-fabric-get-started-mac.md) lub [rozpoczęcie pracy na Linuksie](service-fabric-get-started-linux.md).

## <a name="basic-concepts"></a>Podstawowe pojęcia
Aby rozpocząć korzystanie z usług reliable services, wystarczy zrozumieć tylko kilka podstawowych pojęć:

* **Typ usługi:** Jest to implementacja usługi. Jest zdefiniowany przez klasę, którą `StatelessService` piszesz, która rozszerza i wszelkie inne kody lub zależności używane w nim, wraz z nazwą i numerem wersji.
* **Wystąpienie usługi o nazwie:** Aby uruchomić usługę, należy utworzyć nazwane wystąpienia typu usługi, podobnie jak tworzenie wystąpień obiektów typu klasy. Wystąpienia usługi są w rzeczywistości wystąpienia obiektów klasy usługi, które piszesz.
* **Host usługi:** wystąpienia usługi o nazwie, które tworzysz, muszą być uruchamiane wewnątrz hosta. Host usługi jest tylko procesem, w którym można uruchomić wystąpienia usługi.
* **Rejestracja usługi**: Rejestracja łączy wszystko. Typ usługi musi być zarejestrowany w czasie wykonywania sieci szkieletowej usług w hoście usług, aby umożliwić sieci szkieletowej usług do tworzenia wystąpień go uruchomić.  

## <a name="create-a-stateless-service"></a>Tworzenie usługi bezstanowej
Rozpocznij od utworzenia aplikacji sieci szkieletowej usług. Zestaw SDK sieci szkieletowej usług dla systemu Linux zawiera generator Yeoman, aby zapewnić szkielety dla aplikacji sieci szkieletowej usług z usługą bezstanową. Zacznij od uruchomienia następującego polecenia Yeoman:

```bash
$ yo azuresfjava
```

Postępuj zgodnie z instrukcjami, aby utworzyć **niezawodną usługę bezstanową**. W tym samouczku nazwij aplikację "HelloWorldApplication" i usługę "HelloWorld". Wynik zawiera katalogi `HelloWorldApplication` dla `HelloWorld`i .

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
Typy usług muszą być zarejestrowane w czasie wykonywania sieci szkieletowej usług. Typ usługi jest zdefiniowany w `ServiceManifest.xml` klasie i `StatelessService`twojej klasie usługi, która implementuje . Rejestracja usługi jest wykonywana w głównym punkcie wejścia procesu. W tym przykładzie głównym punktem wejścia procesu jest: `HelloWorldServiceHost.java`

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

## <a name="implement-the-service"></a>Zaimplementuj usługę

Otwórz **HelloWorldApplication/HelloWorld/src/statelessservice/HelloWorldService.java**. Ta klasa definiuje typ usługi i można uruchomić dowolny kod. Interfejs API usługi udostępnia dwa punkty wejścia dla kodu:

* Metoda otwartego punktu wejścia, `runAsync()`o nazwie , gdzie można rozpocząć wykonywanie wszystkich obciążeń, w tym długotrwałych obciążeń obliczeniowych.

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
    ...
}
```

* Punkt wejścia komunikacji, w którym można podłączyć wybrany stos komunikacji. W tym miejscu można rozpocząć odbieranie żądań od użytkowników i innych usług.

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {
    ...
}
```

Ten samouczek `runAsync()` koncentruje się na metodzie punktu wejścia. W tym miejscu można natychmiast rozpocząć uruchamianie kodu.

### <a name="runasync"></a>RunAsync ( RunAsync )
Platforma wywołuje tę metodę, gdy wystąpienie usługi jest umieszczane i gotowe do wykonania. Dla usługi bezstanowej oznacza to, gdy zostanie otwarte wystąpienie usługi. Token anulowania jest udostępniany w celu koordynowania, gdy wystąpienie usługi musi zostać zamknięte. W sieci szkieletowej usług ten cykl otwierania/zamykania wystąpienia usługi może wystąpić wiele razy w okresie istnienia usługi jako całości. Może się to zdarzyć z różnych powodów, w tym:

* System przenosi wystąpienia usługi do równoważenia zasobów.
* Błędy występują w kodzie.
* Aplikacja lub system zostanie uaktualniony.
* Podstawowy sprzęt doświadcza awarii.

Ta aranżacja jest zarządzana przez sieci szkieletowej usług, aby utrzymać usługę wysoko dostępne i odpowiednio zrównoważone.

`runAsync()`nie powinny blokować synchronicznie. Implementacja runAsync należy zwrócić CompletableFuture, aby umożliwić środowisko uruchomieniowe, aby kontynuować. Jeśli obciążenie musi zaimplementować długotrwałe zadanie, które należy wykonać wewnątrz CompletableFuture.

#### <a name="cancellation"></a>Anulowanie
Anulowanie obciążenia jest wysiłkiem współpracy zaaranżowanym przez dostarczony token anulowania. System czeka na zakończenie zadania (przez pomyślne zakończenie, anulowanie lub usterkę), zanim przejdzie dalej. Ważne jest, aby honorować token anulowania, `runAsync()` zakończyć wszelkie prace i zakończyć tak szybko, jak to możliwe, gdy system żąda anulowania. W poniższym przykładzie pokazano, jak obsługiwać zdarzenie anulowania:

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

W tym przykładzie usługi bezstanowej liczba jest przechowywana w zmiennej lokalnej. Ale ponieważ jest to usługa bezstanowa, wartość, która jest przechowywana istnieje tylko dla bieżącego cyklu życia jego wystąpienia usługi. Gdy usługa zostanie przesunięta lub ponownie uruchomiona, wartość zostanie utracona.

## <a name="create-a-stateful-service"></a>Tworzenie usługi stanowej
Usługa Sieci szkieletowej wprowadza nowy rodzaj usługi, która jest stanowa. Usługa stanowa może niezawodnie utrzymywać stan w obrębie samej usługi, znajdując się z kodem, który go używa. Stan jest udostępniany przez sieci szkieletowej usług bez konieczności utrwalania stanu do magazynu zewnętrznego.

Aby przekonwertować wartość licznika z bezstanowej na wysoce dostępne i trwałe, nawet wtedy, gdy usługa przenosi lub uruchamia ponownie, potrzebujesz usługi stanowej.

W tym samym katalogu co aplikacja HelloWorld można dodać nową `yo azuresfjava:AddService` usługę, uruchamiając polecenie. Wybierz "Niezawodne usługi stanowe" dla swojej struktury i nazwę usługi "HelloWorldStateful". 

Aplikacja powinna mieć teraz dwie usługi: bezstanowej usługi HelloWorld i usługi stanowej HelloWorldStateful.

Usługa stanowa ma takie same punkty wejścia jak usługa bezstanowa. Główną różnicą jest dostępność dostawcy stanu, który może niezawodnie przechowywać stan. Sieć szkieletowa usług jest wyposażona w implementację dostawcy stanu o nazwie Niezawodne kolekcje, która umożliwia tworzenie replikowanych struktur danych za pośrednictwem Menedżera stanu niezawodnego. Stanowa usługa niezawodna domyślnie używa tego dostawcy stanu.

Otwórz HelloWorldStateful.java w **HelloWorldStateful -> src**, który zawiera następującą metodę RunAsync:

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

### <a name="runasync"></a>RunAsync ( RunAsync )
`RunAsync()`działa podobnie w służbach stanowych i bezpaństwowych. Jednak w usłudze stanowej platforma wykonuje dodatkową pracę w Twoim `RunAsync()`imieniu przed wykonaniem . Ta praca może obejmować zapewnienie, że menedżer stanu niezawodne i niezawodne kolekcje są gotowe do użycia.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Niezawodne kolekcje i niezawodny menedżer stanu
```java
ReliableHashMap<String,Long> map = this.stateManager.<String, Long>getOrAddReliableHashMapAsync("myHashMap")
```

[ReliableHashMap](https://docs.microsoft.com/java/api/microsoft.servicefabric.data.collections.reliablehashmap) to implementacja słownika, której można użyć do niezawodnego przechowywania stanu w usłudze. Dzięki sieci szkieletowej usług i niezawodnym hashMaps można przechowywać dane bezpośrednio w usłudze bez konieczności korzystania z zewnętrznego magazynu trwałego. Niezawodne hashMaps sprawiają, że dane są wysoce dostępne. Sieci szkieletowej usług realizuje to, tworząc i zarządzając wieloma *replikami* usługi dla Ciebie. Zapewnia również interfejs API, który abstrakcje od złożoności zarządzania tymi replikami i ich przejścia stanu.

Niezawodne kolekcje mogą przechowywać dowolny typ języka Java, w tym typy niestandardowe, z kilkoma zastrzeżeniami:

* Sieć szkieletowa usług sprawia, że stan jest wysoce dostępny przez *replikowanie* stanu między węzłami, a reliable HashMap przechowuje dane na dysku lokalnym na każdej replice. Oznacza to, że wszystko, co jest przechowywane w Reliable HashMaps musi być *serializable*. 
* Obiekty są replikowane dla wysokiej dostępności podczas zatwierdzania transakcji na niezawodne hashMaps. Obiekty przechowywane w niezawodne hashMaps są przechowywane w pamięci lokalnej w usłudze. Oznacza to, że masz lokalne odwołanie do obiektu.
  
   Ważne jest, aby nie mutować wystąpień lokalnych tych obiektów bez wykonywania operacji aktualizacji na niezawodnej kolekcji w transakcji. Dzieje się tak, ponieważ zmiany w lokalnych wystąpieniach obiektów nie będą replikowane automatycznie. Należy ponownie włożyć obiekt z powrotem do słownika lub użyć jednej z metod *aktualizacji* w słowniku.

Menedżer stanu niezawodne zarządza niezawodne hashMaps dla Ciebie. Można poprosić Menedżera stanu niezawodne dla niezawodnej kolekcji według nazwy w dowolnym momencie i w dowolnym miejscu w usłudze. Menedżer stanu niezawodne zapewnia, że otrzymasz odwołanie z powrotem. Nie zaleca się zapisywania odwołań do wystąpienia kolekcji niezawodne w zmiennych elementów członkowskich klasy lub właściwości. Należy zwrócić szczególną uwagę na to, aby odwołanie było ustawione na wystąpienie przez cały czas w cyklu życia usługi. Menedżer stanu niezawodnego obsługuje tę pracę za Ciebie i jest zoptymalizowany pod kątem ponownych wizyt.


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

Operacje na niezawodne HashMaps są asynchroniczne. Dzieje się tak, ponieważ operacje zapisu za pomocą niezawodnych kolekcji wykonują operacje we/wy w celu replikacji i utrwalania danych na dysku.

Niezawodne operacje HashMap są *transakcyjne,* dzięki czemu można zachować spójne stan w wielu niezawodnych hashMaps i operacji. Na przykład można uzyskać element pracy z jednego słownika niezawodne, wykonać operację na nim i zapisać wynik w innym Reliable HashMap, wszystko w ramach jednej transakcji. Jest to traktowane jako operacja niepodzielna i gwarantuje, że cała operacja zakończy się pomyślnie lub cała operacja zostanie wycofana. Jeśli błąd wystąpi po usunięciu w kolejce elementu, ale przed zapisaniem wyniku, cała transakcja zostanie wycofana, a element pozostaje w kolejce do przetworzenia.


## <a name="build-the-application"></a>Kompilowanie aplikacji

Rusztowania Yeoman zawiera skrypt gradle do tworzenia aplikacji i skryptów bash do wdrożenia i usunięcia aplikacji. Aby uruchomić aplikację, najpierw skompiluj aplikację za pomocą gradle:

```bash
$ gradle
```

Spowoduje to powstanie pakietu aplikacji sieci szkieletowej usług, który można wdrożyć przy użyciu interfejsu wiersza polecenia sieci szkieletowej usług.

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
> Aby wdrożyć aplikację do bezpiecznego klastra systemu Linux na platformie Azure, należy skonfigurować certyfikat, aby sprawdzić poprawność aplikacji w czasie wykonywania sieci szkieletowej usług. Umożliwia to usługi niezawodne usługi do komunikowania się z podstawowych interfejsów API środowiska wykonawczego sieci szkieletowej usług. Aby dowiedzieć się więcej, zobacz [Konfigurowanie aplikacji Niezawodne usługi do uruchamiania w klastrach systemu Linux](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters).  
>

## <a name="next-steps"></a>Następne kroki

* [Getting started with Service Fabric CLI (Wprowadzenie do interfejsu wiersza polecenia usługi Service Fabric)](service-fabric-cli.md)
