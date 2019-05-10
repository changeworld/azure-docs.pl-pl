---
title: Zarządzania funkcję konfiguracji aplikacji platformy Azure | Dokumentacja firmy Microsoft
description: Omówienie sposobu konfiguracji aplikacji usługi Azure mogą być używane w taki sposób, aby włączyć lub wyłączyć funkcje aplikacji na żądanie
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
ms.openlocfilehash: 304ca08275ccf4bf32d38e3fc89dd5cf07460fa4
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65413767"
---
# <a name="feature-management"></a>Zarządzanie funkcjami

Tradycyjnie wysyłania nowych funkcji aplikacji wymaga pełne ponownego wdrożenia komputera samej aplikacji. Aby przetestować funkcję, prawdopodobnie należy wdrożyć aplikację wielokrotnie kontroli, gdy staje się widoczny lub kto może zobaczyć, jak to. Funkcja zarządzania jest rozwiązaniem tworzenia nowoczesnych oprogramowania oddziela wydawanie funkcji od wdrażania kodu, które umożliwia szybkie zmiany dostępności funkcji na żądanie. Używa ona technikę o nazwie *flagi funkcji* (znany także jako *przełącznika funkcji*, *przełącznik funkcji*i tak dalej) do administrowania cykl życia funkcji dynamicznie. Ułatwia deweloperom pracę z następujących problemów:

* **Zarządzanie gałęziami kodu**: Flagi funkcji są często używane do opakowania nowe funkcjonalności aplikacji, która jest w fazie projektowania. Takie funkcje "" domyślnie ukryty. Dostarczanie, bezpieczne, chociaż niedokończone i pozostanie w stanie uśpienia w środowisku produkcyjnym. Użycie tej metody o nazwie *ciemny wdrożenia*, można zwolnić całego kodu na końcu każdego cyklu tworzenia oprogramowania. Nie są już potrzebne do obsługi dowolnej innej gałęzi kodu między wiele cykli ze względu na funkcję trwa więcej niż jedną kropkę, należy wykonać.
* **Testowanie w produkcji**: Flagi funkcji służy do udzielania wczesny dostęp do nowych funkcji w środowisku produkcyjnym. Na przykład można ograniczyć dostęp jedynie do członków zespołu lub testerów wersji beta wewnętrznego. Ci użytkownicy będą uzyskiwać środowisko produkcyjne o pełnej wierności, zamiast symulowanych lub częściowe doświadczenie w środowisku testowym.
* **Pilotaż**: Flagi funkcji umożliwia również wdrażanie nowych funkcji dla użytkowników końcowych przyrostowo. Można pierwszego obiektu docelowego niewielką część swojej populacji użytkowników, a następnie stopniowo zwiększać wartość procentowa wraz z upływem czasu, po weszły większą pewnością w implementacji.
* **Błyskawiczne kill przełącznika**: Flagi funkcji zapewniają nieprzerwaną pracę projektowi wydawania nowych funkcji. Za ich pomocą można nie tylko włączyć, ale także łatwo wyłączony funkcji aplikacji. Zyskasz możliwość szybkiego wyłączenia funkcji, jeśli to konieczne, bez konieczności ponowne skompilowanie i wdrożenie aplikacji.
* **Selektywne aktywacji**: Chociaż większość flag funkcji istnieje tylko w przypadku, dopóki ich skojarzone funkcje zostały pomyślnie zwolnione, niektóre może trwać długo. Można ich używać jako sposób podzielić użytkowników i dostarczać określony zbiór funkcji do każdej grupy. Na przykład może być funkcją, która działa w przeglądarce internetowej. Flagi funkcji można zdefiniować w taki sposób, że tylko użytkownicy tej przeglądarki może widzieć i używać tej funkcji. Zaletą tego podejścia jest to, że można łatwo rozszerzyć listę obsługiwanych przeglądarek później bez konieczności wykonywania jakiegokolwiek kodu, zmiany.

## <a name="basic-concepts"></a>Podstawowe pojęcia

Poniżej przedstawiono kilka nowe warunki związane z zarządzaniem funkcją.

* **Flagi funkcji**: Flaga funkcji jest zmienną ze stanem binarne *na* lub *poza* i skojarzonego kodu bloku. Jego stan decyduje o tym, czy blok kodu jest uruchamiane, czy nie.
* **Funkcja menedżera**: Menedżer funkcji jest pakiet aplikacji, który obsługuje cyklu życia wszystkie flagi funkcji w aplikacji. Zwykle zapewnia dodatkowe funkcje, takie jak buforowanie flag i Aktualizuj ich Stany.
* **Filtr**: Filtr jest reguła dla oceny stanu flagi funkcji. Grupy użytkowników, urządzenia lub przeglądarki typu i lokalizacji geograficznej, przedział czasu należą do czego może reprezentować filtru.

Skutecznego wdrożenia zarządzania funkcji składa się z co najmniej dwa składniki: Praca z optymalizacją: aplikacji, która sprawia, że użycie flagi funkcji i oddzielnych repozytorium, są przechowywane flagi funkcji i ich bieżących stanów. Poniżej przedstawiono ich interakcji.

## <a name="feature-flag-usage-in-code"></a>Użycie flagi funkcji w kodzie

Podstawowy wzorzec wprowadzania flagi funkcji w aplikacji jest prosta. Możesz ją wyobrażenie jako zmienną logiczną stanu używany z `if` instrukcji warunkowej, w kodzie.

```csharp
if (featureFlag) {
    // Run the following code
}
```

W takim przypadku `featureFlag` jest ustawiona na `True`, jest wykonywany blok kodu ujęty; w przeciwnym razie zostanie ona pominięta. Wartość `featureFlag` można ustawić statycznie:

```csharp
bool featureFlag = true;
```

On również mogą być obliczane na podstawie określonych reguł:

```csharp
bool featureFlag = isBetaUser();
```

Nieco bardziej skomplikowanego wzorzec flagi funkcji obejmuje `else` także instrukcji.

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
} else {
    // This following code will run if the featureFlag value is false
}
```

To może być ponowne napisanie we wzorcu podstawowe jednak. [Użyj flagi funkcji w aplikacji ASP.NET Core](./use-feature-flags-dotnet-core.md) pokazuje zalet standaryzacji przy użyciu wzorca prostego kodu.

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
}

if (!featureFlag) {
    // This following code will run if the featureFlag value is false
}
```

## <a name="feature-flag-repository"></a>Repozytorium flagi funkcji

Aby skutecznie ich używać, musisz Zapisz wszystkie flagi funkcji używane w aplikacji. Który będzie umożliwiają zmianę stanów flagi funkcji bez konieczności modyfikowania i ponownego wdrażania aplikacji. Konfiguracja aplikacji platformy Azure jest zaprojektowane jako centralne repozytorium dla flag funkcji. Możesz użyć go do definiowania różnych rodzajów flag funkcji i manipulowania ich stanów, szybko i pewnie. Możesz mogą uzyskiwać dostęp do tych flag funkcji łatwo z aplikacji przy użyciu biblioteki konfiguracji aplikacji dla różnych platform języków programowania. [Użyj flagi funkcji w aplikacji ASP.NET Core](./use-feature-flags-dotnet-core.md) pokazuje, jak dostawcy konfiguracji aplikacji programu .NET Core i bibliotek zarządzania funkcji są ze sobą jako kompletnego rozwiązania używane do implementowania flag funkcji dla aplikacji sieci web platformy ASP.NET.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dodawanie flag funkcji do aplikacji sieci web ASP.NET Core](./quickstart-feature-flag-aspnet-core.md)  
