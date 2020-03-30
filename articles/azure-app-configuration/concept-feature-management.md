---
title: Opis zarządzania funkcjami przy użyciu konfiguracji aplikacji platformy Azure
description: Włączanie i wyłączanie funkcji przy użyciu konfiguracji aplikacji platformy Azure
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 8227810c154078fc8424b2cadd373394d07e9730
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523734"
---
# <a name="feature-management-overview"></a>Omówienie zarządzania funkcjami

Tradycyjnie wysyłka nowej funkcji aplikacji wymaga pełnego ponownego rozmieszczenia samej aplikacji. Testowanie funkcji często wymaga wielu wdrożeń aplikacji.  Każde wdrożenie może zmienić funkcję lub udostępnić tę funkcję różnym klientom do testowania.  

Zarządzanie funkcjami to nowoczesna praktyka tworzenia oprogramowania, która oddziela funkcję zwalniania z wdrażania kodu i umożliwia szybkie zmiany dostępności funkcji na żądanie. Wykorzystuje technikę zwaną *flagami operacji* (nazywaną również *przełącznikami funkcji, przełącznikami* *funkcji*itd.) do dynamicznego administrowania cyklem życia operacji.

Zarządzanie funkcjami pomaga deweloperom rozwiązać następujące problemy:

* **Zarządzanie gałęzią kodu:** Użyj flag funkcji, aby zawinąć nowe funkcje aplikacji obecnie w fazie rozwoju. Taka funkcjonalność jest domyślnie "ukryta". Możesz bezpiecznie wysłać tę funkcję, nawet jeśli jest niedokończona i pozostanie uśpiona w produkcji. Przy użyciu tego podejścia, o nazwie *dark deployment*, można zwolnić cały kod na końcu każdego cyklu rozwoju. Nie trzeba już obsługiwać gałęzie kodu w wielu cyklach rozwoju, ponieważ dana funkcja wymaga więcej niż jednego cyklu, aby zakończyć.
* **Test w produkcji:** Użyj flag funkcji, aby udzielić wczesnego dostępu do nowych funkcji w produkcji. Na przykład można ograniczyć dostęp do członków zespołu lub wewnętrznych testerów beta. Ci użytkownicy będą doświadczać pełnej wierności środowiska produkcyjnego zamiast symulowanego lub częściowego środowiska w środowisku testowym.
* **Lot:** Użyj flag funkcji, aby stopniowo wdrażać nowe funkcje dla użytkowników końcowych. Najpierw można kierować niewielki procent populacji użytkowników i stopniowo zwiększać ten procent w czasie.
* **Natychmiastowy przełącznik zabijania:** Flagi funkcji zapewniają nieodłączną siatkę bezpieczeństwa do wydawania nowych funkcji. Funkcje aplikacji można włączać i wyłączać bez ponownego rozmieszczania kodu. W razie potrzeby można szybko wyłączyć funkcję bez przebudowywania i ponownego rozmieszczania aplikacji.
* **Aktywacja selektywna:** użyj flag funkcji, aby posegmentować użytkowników i dostarczać określony zestaw funkcji do każdej grupy. Może być dostępna funkcja, która działa tylko w określonej przeglądarce internetowej. Flagę operacji można zdefiniować, tak aby tylko użytkownicy tej przeglądarki mogli ją wyświetlać i używać. Dzięki takiemu podejściu można łatwo rozwinąć listę obsługiwanych przeglądarek później bez konieczności wprowadzania jakichkolwiek zmian w kodzie.

## <a name="basic-concepts"></a>Podstawowe pojęcia

Oto kilka nowych terminów związanych z zarządzaniem funkcjami:

* **Flaga funkcji**: Flaga elementu jest zmienną o stanie binarnym *włączonym* lub *wyłączonym.* Flaga funkcji ma również skojarzony blok kodu. Stan flagi funkcji wyzwala, czy blok kodu jest uruchamiany.
* **Menedżer funkcji**: Menedżer funkcji to pakiet aplikacji, który obsługuje cykl życia wszystkich flag funkcji w aplikacji. Menedżer funkcji zapewnia również dodatkowe funkcje, w tym buforowanie flag funkcji i aktualizowanie ich stanów.
* **Filtr**: Filtr jest regułą oceny stanu flagi funkcji. Potencjalne filtry obejmują grupy użytkowników, typy urządzeń lub przeglądarek, lokalizacje geograficzne i okna czasowe.

Skuteczna implementacja zarządzania funkcjami składa się z co najmniej dwóch elementów pracujących w porozumieniu:

* Aplikacja, która korzysta z flag funkcji.
* Oddzielne repozytorium, które przechowuje flagi funkcji i ich bieżące stany.

## <a name="using-feature-flags-in-your-code"></a>Używanie flag funkcji w kodzie

Podstawowy wzorzec do implementowania flag funkcji w aplikacji jest prosty. Flaga funkcji to zmienna stanu logicznego kontrolująca instrukcję warunkową w kodzie:

```csharp
if (featureFlag) {
    // Run the following code
}
```

Można ustawić wartość `featureFlag` statycznie.

```csharp
bool featureFlag = true;
```

Stan flagi można ocenić na podstawie określonych reguł:

```csharp
bool featureFlag = isBetaUser();
```

Można rozszerzyć warunkowe ustawienie zachowania aplikacji dla obu województw:

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
} else {
    // This following code will run if the featureFlag value is false
}
```

## <a name="feature-flag-repository"></a>Repozytorium flag funkcji

Aby skutecznie używać flag funkcji, należy ująć na zewnątrz wszystkie flagi operacji używane w aplikacji. Dzięki temu można zmienić stan flagi funkcji bez modyfikowania i ponownego rozmieszczania samej aplikacji.

Usługa Azure App Configuration udostępnia scentralizowane repozytorium flag funkcji. Można go używać do definiowania różnych rodzajów flag operacji i manipulowania ich stanami szybko i pewnie. Następnie można użyć bibliotek konfiguracji aplikacji dla różnych struktur języka programowania, aby łatwo uzyskać dostęp do tych flag funkcji z aplikacji.

[Użyj flag funkcji w aplikacji core ASP.NET](./use-feature-flags-dotnet-core.md) pokazuje, jak dostawca konfiguracji aplikacji .NET Core i biblioteki zarządzania funkcjami są używane razem do implementowania flag funkcji dla aplikacji sieci web ASP.NET.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dodawanie flag funkcji do aplikacji sieci Web ASP.NET Core](./quickstart-feature-flag-aspnet-core.md)  
