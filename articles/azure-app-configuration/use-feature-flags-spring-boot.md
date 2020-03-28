---
title: Samouczek dotyczący używania flag funkcji w aplikacji Spring Boot — konfiguracja aplikacji Azure | Dokumenty firmy Microsoft
description: W tym samouczku dowiesz się, jak zaimplementować flagi funkcji w aplikacjach Spring Boot.
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
ms.openlocfilehash: d519ac44d617f725aa9b3d3f11671122bd9477bc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78944327"
---
# <a name="tutorial-use-feature-flags-in-a-spring-boot-app"></a>Samouczek: Używanie flag funkcji w aplikacji Spring Boot

Biblioteki zarządzania funkcjami Spring Boot Core zapewniają obsługę implementowania flag funkcji w aplikacji Spring Boot. Biblioteki te umożliwiają deklaratywne dodawanie flag funkcji do kodu.

Biblioteki zarządzania funkcjami zarządzają również cyklami życia flagi funkcji za kulisami. Na przykład biblioteki odświeżyć i buforować stany flagi lub zagwarantować stan flagi, aby być niezmienne podczas wywołania żądania. Ponadto biblioteka Spring Boot oferuje integracje, w tym akcje kontrolera MVC, trasy i oprogramowanie pośredniczące.

Flagi [Dodaj funkcje do aplikacji Szybki start spring boot](./quickstart-feature-flag-spring-boot.md) pokazuje kilka sposobów dodawania flag funkcji w aplikacji Spring Boot. W tym samouczku opisano te metody bardziej szczegółowo.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodaj flagi obiektów w kluczowych częściach aplikacji, aby kontrolować dostępność funkcji.
> * Integracja z konfiguracją aplikacji, gdy używasz go do zarządzania flagami funkcji.

## <a name="set-up-feature-management"></a>Konfigurowanie zarządzania funkcjami

Menedżer `FeatureManager` funkcji Wiosna rozruchu pobiera flagi funkcji z natywnego systemu konfiguracji struktury. W rezultacie można zdefiniować flagi funkcji aplikacji przy użyciu dowolnego źródła konfiguracji, które obsługuje Spring Boot, w tym lokalnego pliku *bootstrap.yml* lub zmiennych środowiskowych. `FeatureManager`opiera się na wstrzyknięciu zależności. Usługi zarządzania funkcjami można zarejestrować przy użyciu standardowych konwencji:

```java
private FeatureManager featureManager;

public HelloController(FeatureManager featureManager) {
    this.featureManager = featureManager;
}
```

Zaleca się, aby zachować flagi funkcji poza aplikacją i zarządzać nimi oddzielnie. W ten sposób można zmodyfikować stany flagi w dowolnym momencie i te zmiany zaczynają obowiązywać w aplikacji od razu. Konfiguracja aplikacji zapewnia scentralizowane miejsce do organizowania i kontrolowania wszystkich flag funkcji za pośrednictwem dedykowanego interfejsu użytkownika portalu. Konfiguracja aplikacji dostarcza również flagi do aplikacji bezpośrednio za pośrednictwem bibliotek klienta spring boot.

Najprostszym sposobem podłączenia aplikacji Spring Boot do konfiguracji aplikacji jest dostawca konfiguracji:

### <a name="spring-cloud-11x"></a>Wiosenna chmura 1.1.x

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-feature-management-web</artifactId>
    <version>1.1.2</version>
</dependency>
```

### <a name="spring-cloud-12x"></a>Wiosenna chmura 1.2.x

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-feature-management-web</artifactId>
    <version>1.2.2</version>
</dependency>
```

## <a name="feature-flag-declaration"></a>Deklaracja flagi funkcji

Każda flaga elementu składa się z dwóch części: nazwy i listy co najmniej jednego *on* filtru, które są używane `True`do oceny, czy stan obiektu jest włączony (czyli gdy jest jego wartość ). Filtr definiuje przypadek użycia, gdy funkcja powinna być włączona.

Gdy flaga elementu ma wiele filtrów, lista filtrów jest przesuwana w kolejności, dopóki jeden z filtrów nie określi, że funkcja powinna być włączona. W tym momencie flaga funkcji jest *wliczone*, a wszystkie pozostałe wyniki filtru są pomijane. Jeśli żaden filtr nie wskazuje, że funkcja powinna być włączona, flaga obiektu jest *wyłączona*.

Menedżer funkcji obsługuje *plik application.yml* jako źródło konfiguracji dla flag funkcji. W poniższym przykładzie pokazano, jak skonfigurować flagi funkcji w pliku YAML:

```yml
feature-management:
  feature-set:
    feature-a: true
    feature-b: false
    feature-c:
      enabled-for:
        -
          name: Percentage
          parameters:
            value: 50
```

Zgodnie z `feature-management` konwencją sekcja tego dokumentu YML jest używana dla ustawień flagi funkcji. W poprzednim przykładzie przedstawiono trzy flagi `EnabledFor` operacji z ich filtrów zdefiniowanych we właściwości:

* `feature-a`jest *włączony*.
* `feature-b`jest *wyłączony*.
* `feature-c`określa filtr nazwany `Percentage` właściwością. `parameters` `Percentage`jest konfigurowalnym filtrem. W tym `Percentage` przykładzie określa 50-procentowe `feature-c` prawdopodobieństwo, że flaga ma być *wł..*

## <a name="feature-flag-checks"></a>Sprawdzanie flagi funkcji

Podstawowy wzorzec zarządzania operacjami polega na tym, aby najpierw sprawdzić, czy flaga obiektu jest ustawiona *na .* Jeśli tak, menedżer funkcji następnie uruchamia akcje, które zawiera funkcja. Przykład:

```java
private FeatureManager featureManager;
...
if (featureManager.isEnabledAsync("feature-a").block()) {
    // Run the following code
}
```

## <a name="dependency-injection"></a>Wstrzykiwanie zależności

W wiosennym rozruchu można `FeatureManager` uzyskać dostęp do menedżera funkcji za pomocą iniekcji zależności:

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

W kontrolerach MVC można `@FeatureGate` użyć atrybutu do kontrolowania, czy określona akcja jest włączona. Aby `Index` można `feature-a` było *on* uruchomić, należy włączyć następującą akcję:

```java
@GetMapping("/")
@FeatureGate(feature = "feature-a")
public String index(Model model) {
    ...
}
```

Gdy kontroler MVC lub akcja jest zablokowana, ponieważ flaga funkcji sterującej jest *wyłączona,* wywoływany jest zarejestrowany `IDisabledFeaturesHandler` interfejs. Domyślny `IDisabledFeaturesHandler` interfejs zwraca kod stanu 404 do klienta bez treści odpowiedzi.

## <a name="mvc-filters"></a>Filtry MVC

Można skonfigurować filtry MVC tak, aby były aktywowane na podstawie stanu flagi funkcji. Poniższy kod dodaje filtr `FeatureFlagFilter`MVC o nazwie . Ten filtr jest wyzwalany w potoku MVC tylko wtedy, gdy `feature-a` jest włączona.

```java
@Component
public class FeatureFlagFilter implements Filter {

    @Autowired
    private FeatureManager featureManager;

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
        if(!featureManager.isEnabledAsync("feature-a").block()) {
            chain.doFilter(request, response);
            return;
        }
        ...
        chain.doFilter(request, response);
    }
}
```

## <a name="routes"></a>Trasy

Do przekierowywania tras można użyć flag obiektowych. Następujący kod przekieruje `feature-a` użytkownika z jest włączony:

```java
@GetMapping("/redirect")
@FeatureGate(feature = "feature-a", fallback = "/getOldFeature")
public String getNewFeature() {
    // Some New Code
}

@GetMapping("/getOldFeature")
public String getOldFeature() {
    // Some New Code
}
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak zaimplementować flagi funkcji w aplikacji Spring Boot przy użyciu `spring-cloud-azure-feature-management-web` bibliotek. Aby uzyskać więcej informacji na temat obsługi zarządzania funkcjami w wiosennym rozruchu i konfiguracji aplikacji, zobacz następujące zasoby:

* [Przykładowy kod flagi funkcji Wiosna rozruchu](/azure/azure-app-configuration/quickstart-feature-flag-spring-boot)
* [Zarządzanie flagami funkcji](./manage-feature-flags.md)
