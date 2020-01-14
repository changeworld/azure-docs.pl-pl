---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: a27d44a7684b4ad3d39d7fba50ca52e08e932971
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769776"
---
Szablon projektu usługi Azure Functions w programie Visual Studio umożliwia utworzenie projektu, który można opublikować w aplikacji funkcji na platformie Azure. Aplikacja funkcji umożliwia grupowanie funkcji jako jednostki logicznej, co ułatwia zarządzanie, wdrażanie, skalowanie i udostępnianie zasobów.

1. W programie Visual Studio w menu **plik** wybierz pozycję **Nowy** **projekt** > .

1. W oknie dialogowym **Utwórz nowy projekt** Wyszukaj `functions`, wybierz szablon **Azure Functions** i wybierz przycisk **dalej**.

1. Wprowadź nazwę projektu, a następnie wybierz pozycję **Utwórz**. Nazwa aplikacji funkcji musi być prawidłową nazwą przestrzeni nazw C#, dlatego nie należy używać znaków podkreślenia, łączników ani znaków innych niż alfanumeryczne.

1. W obszarze **Tworzenie nowej aplikacji Azure Functions**Użyj następujących opcji:

    + **Azure Functions v2 (.NET Core)**
    + **Wyzwalacz HTTP**
    + **Konto magazynu**: **emulator magazynu**
    + **Poziom autoryzacji**: **anonimowe** 

    | Opcja      | Sugerowana wartość  | Opis                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Środowisko uruchomieniowe funkcji** | **Azure Functions 2. x <br />(.NET Core)** | To ustawienie powoduje utworzenie projektu funkcji, który używa środowiska uruchomieniowego w wersji 2. x Azure Functions, który obsługuje program .NET Core. Usługa Azure Functions 1.x obsługuje program .NET Framework. Aby uzyskać więcej informacji, zobacz [wersja Docelowa Azure Functions środowiska uruchomieniowego](../articles/azure-functions/functions-versions.md).   |
    | **Szablon funkcji** | **Wyzwalacz HTTP** | To ustawienie powoduje utworzenie funkcji wyzwalanej przez żądanie HTTP. |
    | **Konto magazynu**  | **Emulator magazynu** | Wyzwalacz HTTP nie korzysta z połączenia konta usługi Azure Storage. Wszystkie inne typy wyzwalaczy wymagają prawidłowych parametrów połączenia konta magazynu. Ponieważ funkcje wymagają konta magazynu, jeden jest przypisywany lub tworzony podczas publikowania projektu na platformie Azure. |
    | **Poziom autoryzacji** | **Anonimowe** | Utworzona funkcja może zostać wyzwolona przez dowolnego klienta bez podawania klucza. To ustawienie autoryzacji ułatwia testowanie nowej funkcji. Aby uzyskać więcej informacji na temat kluczy i autoryzacji, zobacz sekcję [Authorization keys (Klucze autoryzacji)](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys) w artykule [HTTP and webhook bindings (Powiązania protokołu HTTP i elementów webhook)](../articles/azure-functions/functions-bindings-http-webhook.md). |
    
    > [!NOTE]
    > Upewnij się, że ustawisz **poziom autoryzacji** na `Anonymous`. Jeśli wybierzesz domyślny poziom `Function`, musisz przedstawić [klucz funkcji](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys) w żądaniach, aby uzyskać dostęp do punktu końcowego funkcji.
    
4. Wybierz pozycję **Utwórz** , aby utworzyć projekt funkcji i funkcję wyzwalaną przez protokół http.
