---
title: Samouczek dotyczący używania flag funkcji w aplikacji do rozruchu sprężynowego — konfiguracja aplikacji platformy Azure | Microsoft Docs
description: W tym samouczku dowiesz się, jak zaimplementować flagi funkcji w aplikacjach do rozruchu ze sprężyną.
services: azure-app-configuration
documentationcenter: ''
author: mrm9084
manager: zhenlwa
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: java
ms.topic: tutorial
ms.date: 09/26/2019
ms.author: mametcal
ms.custom: mvc
ms.openlocfilehash: 8c66e2995462701f7ddaefc3a2623c02fee883ef
ms.sourcegitcommit: 6013bacd83a4ac8a464de34ab3d1c976077425c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71687171"
---
# <a name="tutorial-use-feature-flags-in-a-spring-boot-app"></a>Samouczek: używanie flag funkcji w aplikacji do rozruchu sprężynowego

Biblioteki zarządzania podstawową funkcją rozruchu sprężynowego zapewniają obsługę implementowania flag funkcji w aplikacji do rozruchowej ze sprężyną. Te biblioteki umożliwiają deklaratywne Dodawanie flag funkcji do kodu.

Biblioteki zarządzania funkcjami również zarządzają cyklami życia flagi funkcji w tle. Na przykład biblioteki odświeżają i buforują Stany flag i gwarantują, że stan flagi będzie niezmienny podczas wywołania żądania. Ponadto biblioteka rozruchu sprężynowego oferuje integracje, w tym akcje kontrolera MVC, trasy i oprogramowanie pośredniczące.

[Dodawanie flag funkcji do aplikacji do rozruchu ze sprężyną — szybki start](./quickstart-feature-flag-spring-boot.md) pokazuje kilka sposobów dodawania flag funkcji w aplikacji do rozruchu ze sprężyną. Ten samouczek wyjaśnia te metody bardziej szczegółowo.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodaj flagi funkcji w kluczowych częściach aplikacji, aby kontrolować dostępność funkcji.
> * Integruj z konfiguracją aplikacji, gdy jest używana do zarządzania flagami funkcji.

## <a name="set-up-feature-management"></a>Konfigurowanie zarządzania funkcjami

@No__t-0 jest pobierana przez Menedżera funkcji sprężyny. W związku z tym można zdefiniować flagi funkcji aplikacji przy użyciu dowolnego źródła konfiguracji obsługiwanego przez rozruch sprężynowy, w tym lokalnego pliku *Bootstrap. yml* lub zmiennych środowiskowych. `FeatureManager` opiera się na iniekcji zależności. Usługi zarządzania funkcjami można zarejestrować przy użyciu standardowych konwencji:

```java
private FeatureManager featureManager;

public HelloController(FeatureManager featureManager) {
    this.featureManager = featureManager;
}
```

Firma Microsoft zaleca, aby zachować flagi funkcji poza aplikacją i osobno zarządzać nimi. Dzięki temu można w dowolnym momencie zmodyfikować Stany flag i wprowadzić zmiany w aplikacji od razu. Konfiguracja aplikacji zapewnia scentralizowane miejsce do organizowania i kontrolowania wszystkich flag funkcji za pomocą dedykowanego interfejsu użytkownika portalu. Konfiguracja aplikacji udostępnia również flagi bezpośrednio do aplikacji za pomocą bibliotek klientów z rozruchem sprężyny.

Najprostszym sposobem łączenia aplikacji z rozruchem sprężynowym z konfiguracją aplikacji jest użycie dostawcy konfiguracji:

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-starter-azure-appconfiguration-config</artifactId>
    <version>1.1.0.M4</version>
</dependency>
```

## <a name="feature-flag-declaration"></a>Deklaracja flagi funkcji

Każda flaga funkcji ma dwie części: nazwę i listę co najmniej jednego filtru, który jest używany do obliczenia, czy stan funkcji jest *włączony* (to znaczy, gdy wartość jest `True`). Filtr definiuje przypadek użycia, gdy funkcja powinna być włączona.

Gdy flaga funkcji ma wiele filtrów, lista filtrów jest przesunięta w kolejności, aż jeden z filtrów określi, że funkcja powinna być włączona. W tym momencie flaga funkcji jest *włączona*, a wszystkie pozostałe wyniki filtru są pomijane. Jeśli żaden filtr nie wskazuje, że funkcja powinna być włączona, flaga funkcji jest *wyłączona*.

Program Feature Manager obsługuje *aplikację Application. yml* jako źródło konfiguracji dla flag funkcji. Poniższy przykład pokazuje, jak skonfigurować flagi funkcji w pliku YAML:

```yml
feature-management:
  featureSet:
    features:
      FeatureA: true
      FeatureB: false
      FeatureC:
        EnabledFor:
          -
            name: Percentage
            parameters:
              value: 50
```

Zgodnie z Konwencją sekcja `feature-management` tego dokumentu YML jest używana na potrzeby ustawień flagi funkcji. W poprzednim przykładzie przedstawiono trzy flagi funkcji z filtrami zdefiniowanymi we właściwości `EnabledFor`:

* `FeatureA` jest *włączona*.
* `FeatureB` jest *wyłączona*.
* `FeatureC` określa filtr o nazwie `Percentage` z właściwością `Parameters`. `Percentage` to konfigurowalny filtr. W tym przykładzie `Percentage` określa prawdopodobieństwo 50 procent dla flagi @no__t- *1.*

## <a name="feature-flag-checks"></a>Sprawdzanie flag funkcji

Podstawowym wzorcem zarządzania funkcjami jest najpierw sprawdzenie, czy flaga funkcji jest ustawiona na wartość *włączone*. Jeśli tak, Menedżer funkcji uruchamia następnie akcje, które zawiera funkcja. Na przykład:

```java
private FeatureManager featureManager;
...
if (featureManager.isEnabled("FeatureA"))
{
    // Run the following code
}
```

## <a name="dependency-injection"></a>Wstrzykiwanie zależności

W przypadku rozruchu sprężynowego można uzyskać dostęp do programu Feature Manager `FeatureManager` przez iniekcję zależności:

```java
@Controller
@ConfigurationProperties("controller")
public class HomeController {
    private FeatureManager featureManager;

    public HomeController(FeatureManager featureManager) {
        this.featureManager = featureManager;
    }
}
```

## <a name="controller-actions"></a>Akcje kontrolera

W kontrolerach MVC należy użyć atrybutu `@FeatureGate`, aby określić, czy określona akcja jest włączona. Następująca akcja `Index` wymaga *, aby `FeatureA` przed* uruchomieniem:

```java
@GetMapping("/")
@FeatureGate(feature = "FeatureA")
public String index(Model model) {
    ...
}
```

Gdy kontroler MVC lub akcja jest blokowana, ponieważ flaga funkcji kontrolującej jest *wyłączona*, zostanie wywołany zarejestrowany interfejs `IDisabledFeaturesHandler`. Domyślny interfejs `IDisabledFeaturesHandler` zwraca kod stanu 404 do klienta bez treści odpowiedzi.

## <a name="mvc-filters"></a>Filtry MVC

Filtry MVC można skonfigurować tak, aby były aktywowane na podstawie stanu flagi funkcji. Poniższy kod dodaje filtr MVC o nazwie `FeatureFlagFilter`. Ten filtr jest wyzwalany w ramach potoku MVC tylko wtedy, gdy jest włączony `FeatureA`.

```java
@Component
public class FeatureFlagFilter implements Filter {

    @Autowired
    private FeatureManager featureManager;

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
        if(!featureManager.isEnabled("FeatureA")) {
            chain.doFilter(request, response);
            return;
        }
        ...
        chain.doFilter(request, response);
    }
}
```

## <a name="routes"></a>Trasy

Za pomocą flag funkcji można przekierowywać trasy. Następujący kod przekierowuje użytkownika z `FeatureA` jest włączone:

```java
@GetMapping("/redirect")
@FeatureGate(feature = "FeatureA", fallback = "/getOldFeature")
public String getNewFeature() {
    // Some New Code
}

@GetMapping("/getOldFeature")
public String getOldFeature() {
    // Some New Code
}
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób implementacji flag funkcji w aplikacji rozruchu sprężynowego przy użyciu bibliotek `spring-cloud-azure-feature-management-web`. Aby uzyskać więcej informacji na temat obsługi zarządzania funkcjami w konfiguracji sprężynowego rozruchu i aplikacji, zobacz następujące zasoby:

* [Przykładowy kod flagi funkcji rozruchu sprężynowego](/azure/azure-app-configuration/quickstart-feature-flag-spring-boot)
* [Zarządzanie flagami funkcji](./manage-feature-flags.md)
