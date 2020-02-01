---
title: Zarządzanie funkcjami konfiguracji aplikacji platformy Azure
description: Omówienie sposobu, w jaki usługa Azure App Configuration może służyć do włączania i wyłączania funkcji aplikacji na żądanie.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 04/19/2019
ms.openlocfilehash: 85228854f6c106c68cedc3eeea81e15fd662774a
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76898736"
---
# <a name="feature-management-overview"></a>Omówienie zarządzania funkcjami

Tradycyjnie funkcja dostarczania nowej aplikacji wymaga pełnego ponownego wdrożenia samej aplikacji. Aby przetestować funkcję, prawdopodobnie trzeba będzie wdrożyć aplikację wiele razy, aby kontrolować, kiedy ta funkcja staje się widoczna lub kto ją widzi.

Zarządzanie funkcjami to nowoczesne rozwiązanie do tworzenia oprogramowania, które oddziela wydanie funkcji od wdrożenia kodu i umożliwia szybkie zmiany dostępności funkcji na żądanie. Używa techniki o nazwie *flagi funkcji* (nazywany także *przełącznikami funkcji*, *przełącznikami*funkcji itp.) w celu dynamicznego administrowania cyklem życia funkcji.

Zarządzanie funkcjami ułatwia deweloperom rozwiązywanie następujących problemów:

* **Zarządzanie gałęzią kodu**: flagi funkcji są często używane do zawijania nowych funkcji aplikacji, które są opracowywane. Ta funkcja jest domyślnie ukryta. Możesz bezpiecznie dostarczyć funkcję, nawet jeśli nie została ukończona, i pozostanie uśpiona w środowisku produkcyjnym. Korzystając z tego podejścia, nazywanego *ciemnego wdrożenia*, można zwolnić cały kod na końcu każdego cyklu projektowania. Nie musisz już obsługiwać żadnej gałęzi kodu w wielu cyklach z powodu funkcji pobierającej więcej niż jeden cykl.
* **Testowanie w środowisku produkcyjnym**: można użyć flag funkcji, aby udzielić wczesnego dostępu do nowych funkcji w środowisku produkcyjnym. Można na przykład ograniczyć ten dostęp tylko do członków zespołu lub do wewnętrznych testerów wersji beta. Ci użytkownicy otrzymają środowisko produkcyjne o pełnej wierności, a nie symulowane lub częściowe środowisko w środowisku testowym.
* **Loty**: można także użyć flag funkcji, aby stopniowo wdrożyć nowe funkcje dla użytkowników końcowych. Możesz najpierw określić niewielki procent populacji użytkownika i zwiększyć ten procent stopniowo w miarę upływu czasu, po uzyskaniu większej pewności w implementacji.
* **Natychmiastowe zabicia przełącznik**: flagi funkcji zapewniają nieodłączną bezpieczeństwo w przypadku zwalniania nowych funkcji. Z nimi można łatwo włączać i wyłączać funkcje aplikacji. W razie potrzeby można szybko wyłączyć funkcję bez konieczności odbudowywania i ponownego wdrażania aplikacji.
* **Aktywacja selektywna**: Chociaż większość flag funkcji istnieje tylko do momentu, gdy skojarzone funkcje zostały pomyślnie wydane, niektóre z nich mogą trwać długo. Za pomocą flag funkcji można dzielić użytkowników i dostarczać określony zestaw funkcji dla każdej grupy. Na przykład może być dostępna funkcja, która działa tylko w niektórych przeglądarkach sieci Web. Można zdefiniować flagę funkcji tak, aby tylko użytkownicy tej przeglądarki mogli zobaczyć i korzystać z tej funkcji. Dzięki temu można łatwo rozwijać obsługiwaną listę przeglądarki w późniejszym czasie bez konieczności wprowadzania zmian w kodzie.

## <a name="basic-concepts"></a>Podstawowe pojęcia

Poniżej przedstawiono kilka nowych warunków związanych z zarządzaniem funkcjami:

* **Flaga funkcji**: Flaga funkcji to zmienna z binarnym stanem *włączania* lub *wyłączania*. Flaga funkcji ma również skojarzony blok kodu. Stan flagi funkcji wyzwala, czy blok kodu jest uruchomiony, czy nie.
* **Menedżer funkcji**: Menedżer funkcji to pakiet aplikacji, który obsługuje cykl życia wszystkich flag funkcji w aplikacji. Program Feature Manager zazwyczaj oferuje dodatkowe funkcje, takie jak flagi funkcji buforowania i aktualizowanie ich Stanów.
* **Filtr**: filtr jest regułą do oceny stanu flagi funkcji. W przypadku grupy użytkowników, urządzenia lub przeglądarki, lokalizacji geograficznej i przedziału czasu są wszystkie przykłady elementów, które może reprezentować filtr.

Skuteczna implementacja zarządzania funkcjami składa się z co najmniej dwóch składników pracujących w ramach współpracy:

* Aplikacja, która korzysta z flag funkcji.
* Oddzielne repozytorium, w którym są przechowywane flagi funkcji i ich bieżące Stany.

Sposób działania tych składników przedstawiono w poniższych przykładach.

## <a name="feature-flag-usage-in-code"></a>Użycie flagi funkcji w kodzie

Wzorzec podstawowy służący do implementowania flag funkcji w aplikacji jest prosty. Flagę funkcji można traktować jako zmienną stanu logicznego, która jest używana z `if` instrukcją warunkową w kodzie:

```csharp
if (featureFlag) {
    // Run the following code
}
```

W takim przypadku, jeśli `featureFlag` jest ustawiona na `True`, zostanie wykonany załączony blok kodu; w przeciwnym razie zostanie pominięty. Można ustawić wartość `featureFlag` statycznie, tak jak w poniższym przykładzie kodu:

```csharp
bool featureFlag = true;
```

Możesz również oszacować stan flagi na podstawie określonych zasad:

```csharp
bool featureFlag = isBetaUser();
```

Nieco bardziej skomplikowany wzór flagi funkcji zawiera również instrukcję `else`:

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
} else {
    // This following code will run if the featureFlag value is false
}
```

Można jednak ponownie napisać to zachowanie w wzorcu Basic. [Użycie flag funkcji w aplikacji ASP.NET Core](./use-feature-flags-dotnet-core.md) zawiera zalety standaryzacji na prostym wzorcu kodu. Przykład:

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
}

if (!featureFlag) {
    // This following code will run if the featureFlag value is false
}
```

## <a name="feature-flag-repository"></a>Repozytorium flag funkcji

Aby efektywnie korzystać z flag funkcji, należy Externalize wszystkie flagi funkcji używane w aplikacji. Takie podejście umożliwia zmianę Stanów flag funkcji bez konieczności modyfikowania i ponownego wdrażania aplikacji.

Konfiguracja aplikacji platformy Azure została zaprojektowana jako scentralizowane repozytorium dla flag funkcji. Można jej użyć do zdefiniowania różnych rodzajów flag funkcji i manipulowania ich Stanami szybko i bez obaw. Następnie można użyć bibliotek konfiguracyjnych aplikacji dla różnych struktur języka programowania, aby łatwo uzyskać dostęp do tych flag funkcji z aplikacji.

[Użycie flag funkcji w aplikacji ASP.NET Core](./use-feature-flags-dotnet-core.md) pokazuje, w jaki sposób dostawcy konfiguracji aplikacji .NET Core i biblioteki zarządzania funkcjami są używane razem do implementowania flag funkcji dla aplikacji sieci Web ASP.NET.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dodawanie flag funkcji do aplikacji sieci Web ASP.NET Core](./quickstart-feature-flag-aspnet-core.md)  
