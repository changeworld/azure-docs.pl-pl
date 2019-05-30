---
title: Zarządzania funkcję konfiguracji aplikacji platformy Azure | Dokumentacja firmy Microsoft
description: Omówienie sposobu konfiguracji aplikacji usługi Azure mogą być używane w taki sposób, aby włączyć lub wyłączyć funkcje aplikacji na żądanie.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.service: azure-app-configuration
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 04/19/2019
ms.author: yegu
ms.openlocfilehash: 46f39e87e4e4cf115cbc1fceeabf0dab38fade28
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393356"
---
# <a name="feature-management-overview"></a>Omówienie funkcji usługi zarządzania

Tradycyjnie wysyłania nowych funkcji aplikacji wymaga pełne ponownego wdrożenia komputera samej aplikacji. Aby przetestować funkcję, prawdopodobnie należy wdrożyć aplikację wiele razy do formantu, gdy funkcja staje się widoczny lub kto może zobaczyć, jak to.

Funkcja zarządzania jest rozwiązaniem nowoczesne tworzenie oprogramowania, który oddziela wydawanie funkcji od wdrażania kodu i umożliwia szybkie zmiany dostępności funkcji na żądanie. Używa ona technikę o nazwie *flag funkcji* (znany także jako *Włącza lub wyłącza funkcję*, *przełączniki funkcji*i tak dalej) do administrowania dynamicznie cykl życia funkcji.

Funkcja zarządzania pomaga deweloperom rozwiązywania problemów w następujących:

* **Zarządzanie gałęziami kodu**: Flagi funkcji są często używane do opakowania nowe funkcjonalności aplikacji, która jest w fazie projektowania. Takie funkcje "" domyślnie ukryty. Bezpiecznie można publikować funkcję, nawet jeśli ma niedokończone, i pozostaną w stanie uśpienia w środowisku produkcyjnym. Za pomocą tego podejścia, o nazwie *ciemny wdrożenia*, można zwolnić całego kodu na końcu każdego cyklu tworzenia oprogramowania. Nie są już potrzebne do obsługi dowolnej innej gałęzi kodu między wiele cykli ze względu na funkcję trwa więcej niż jednego cyklu należy wykonać.
* **Testowanie w produkcji**: Flagi funkcji służy do udzielania wczesny dostęp do nowych funkcji w środowisku produkcyjnym. Na przykład można ograniczyć dostęp do tylko członków zespołu lub do testerów wersji beta wewnętrznego. Ci użytkownicy będą uzyskiwać środowisko produkcyjne o pełnej wierności, zamiast symulowanych lub częściowe doświadczenie w środowisku testowym.
* **Pilotaż**: Flagi funkcji umożliwia również użytkownikom końcowym przyrostowo wdrażać nowe funkcje. Można pierwszego obiektu docelowego niewielką część swojej populacji użytkowników, a następnie stopniowo zwiększać wartość procentowa wraz z upływem czasu, po weszły większą pewnością w implementacji.
* **Błyskawiczne kill przełącznika**: Flagi funkcji zapewniają nieprzerwaną pracę projektowi wydawania nowych funkcji. Za ich pomocą funkcje aplikacji mogą być łatwo włączać i wyłączać. Jeśli to konieczne, funkcję można wyłączyć szybkie bez ponownego tworzenia i wdrażania aplikacji.
* **Selektywne aktywacji**: Chociaż większość flag funkcji istnieje tylko do momentu ich skojarzone funkcje zostały pomyślnie zwolnione, niektóre może trwać długo. Aby podzielić użytkowników i oferować określony zbiór funkcji do każdej grupy, można użyć flagi funkcji. Na przykład może być funkcją, która działa tylko w przeglądarce internetowej. Tylko użytkownicy tej przeglądarki można przejrzeć i użyć funkcji, można zdefiniować flagi funkcji. W przypadku tej metody możesz łatwo rozszerzyć listę obsługiwanych przeglądarek później bez konieczności wprowadzania żadnych zmian w kodzie.

## <a name="basic-concepts"></a>Podstawowe pojęcia

Poniżej przedstawiono kilka nowe warunki związane z zarządzaniem funkcji:

* **Flagi funkcji**: Flaga funkcji jest zmienną ze stanem binarne *na* lub *poza*. Flagi funkcji ma również blokiem skojarzonego kodu. Stan flagi funkcji wyzwala czy blok kodu jest uruchomiony lub nie.
* **Funkcja menedżera**: Menedżer funkcji jest pakiet aplikacji, który obsługuje cyklu życia wszystkie flagi funkcji w aplikacji. Menedżer funkcji zwykle zapewnia dodatkowe funkcje, takie jak buforowanie flag funkcji i aktualizowanie ich Stany.
* **Filtr**: Filtr jest reguła dla oceny stanu flagi funkcji. Grupy użytkowników, urządzenia lub przeglądarki typu, lokalizacji geograficznej i przedział czasu są przykładami co może reprezentować filtru.

Skutecznego wdrożenia zarządzania funkcji składa się z co najmniej dwa składniki: Praca z optymalizacją:

* Aplikacja, która sprawia, że użycie flagi funkcji.
* Oddzielne repozytorium, które są przechowywane flagi funkcji i ich bieżących stanów.

Sposób interakcji te składniki przedstawiono w poniższych przykładach.

## <a name="feature-flag-usage-in-code"></a>Użycie flagi funkcji w kodzie

Podstawowy wzorzec wprowadzania flagi funkcji w aplikacji jest prosta. Można potraktować flagi funkcji jako zmienną logiczną stanu używany z `if` instrukcji warunkowej, w kodzie:

```csharp
if (featureFlag) {
    // Run the following code
}
```

W takim przypadku `featureFlag` jest równa `True`, blok kodu ujęty wykonane; w przeciwnym razie, zostanie ona pominięta. Można ustawić wartość `featureFlag` statycznie, jak w poniższym przykładzie kodu:

```csharp
bool featureFlag = true;
```

Możesz również ocenić stan flagi na podstawie określonych reguł:

```csharp
bool featureFlag = isBetaUser();
```

Nieco bardziej skomplikowanego wzorzec flagi funkcji obejmuje `else` także instrukcji:

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
} else {
    // This following code will run if the featureFlag value is false
}
```

Jednak można napisać ponownie to zachowanie w podstawowy wzorzec. [Użyj flagi funkcji w aplikacji ASP.NET Core](./use-feature-flags-dotnet-core.md) przedstawiono zalety standaryzacji przy użyciu wzorca prostego kodu. Na przykład:

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
}

if (!featureFlag) {
    // This following code will run if the featureFlag value is false
}
```

## <a name="feature-flag-repository"></a>Repozytorium flagi funkcji

Aby skutecznie używać flag funkcji, musisz Zapisz wszystkie flagi funkcji używane w aplikacji. Takie podejście umożliwia zmianę stanów flagi funkcji bez konieczności modyfikowania i ponownego wdrażania aplikacji.

Konfiguracja aplikacji platformy Azure jest zaprojektowane jako centralne repozytorium dla flag funkcji. Służy do definiowania różnych rodzajów flag funkcji i manipulowania ich stanów, szybko i pewnie. Biblioteki konfiguracji aplikacji dla różnych platform języków programowania można następnie użyć łatwo uzyskać dostęp do tych flag funkcji z aplikacji.

[Użyj flagi funkcji w aplikacji ASP.NET Core](./use-feature-flags-dotnet-core.md) pokazuje, jak dostawca konfiguracji aplikacji programu .NET Core i bibliotek zarządzania funkcji są używane razem do wdrożenia flag funkcji dla aplikacji sieci web platformy ASP.NET.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dodawanie flag funkcji do aplikacji sieci web ASP.NET Core](./quickstart-feature-flag-aspnet-core.md)  
