---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 05/22/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 16bda26a80611b29fdb100736cfc48978e63f75a
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34702432"
---
Szablon projektu usługi Azure Functions w programie Visual Studio umożliwia utworzenie projektu, który można opublikować w aplikacji funkcji na platformie Azure. Aplikacja funkcji umożliwia grupowanie funkcji w jednostki logiczne, co ułatwia wdrażanie i udostępnianie zasobów oraz zarządzanie nimi.

1. W programie Visual Studio wybierz pozycję **Nowy** > **Projekt** z menu **Plik**.

2. W oknie dialogowym **Nowy projekt** wybierz pozycję **Zainstalowane**, rozwiń węzeł **Visual C#** > **Chmura**, wybierz pozycję **Azure Functions**, wpisz **nazwę** swojego projektu, a następnie kliknij przycisk **OK**. Nazwa aplikacji funkcji musi być prawidłową nazwą przestrzeni nazw C#, dlatego nie należy używać znaków podkreślenia, łączników ani znaków innych niż alfanumeryczne.

    ![Okno dialogowe Nowy projekt umożliwiające utworzenie funkcji w programie Visual Studio](./media/functions-vstools-create/functions-vstools-add-new-project.png)

3. Użyj ustawień określonych w tabeli pod obrazem.

    ![Okno dialogowe Nowa funkcja w programie Visual Studio](./media/functions-vstools-create/functions-vstools-add-new-function.png) 

    | Ustawienie      | Sugerowana wartość  | Opis                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Wersja** | Azure Functions v1 <br />(.NET Framework) | Tworzy projekt funkcji korzystający ze środowiska uruchomieniowego usługi Azure Functions w wersji 1. Środowisko uruchomieniowe w wersji 2, obsługujące platformę .NET Core, jest obecnie dostępne w wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [How to target Azure Functions runtime version (Wybieranie wersji środowiska uruchomieniowego usługi Azure Functions)](../articles/azure-functions/functions-versions.md).   |
    | **Szablon** | Wyzwalacz HTTP | Tworzy funkcję wyzwalaną przez żądanie HTTP. |
    | **Konto magazynu**  | Emulator magazynu | Wyzwalacz HTTP nie używa połączenia konta magazynu. Wszystkie inne typy wyzwalaczy wymagają prawidłowych parametrów połączenia konta magazynu. |
    | **Prawa dostępu** | Anonimowe | Utworzona funkcja może zostać wyzwolona przez dowolnego klienta bez podawania klucza. To ustawienie autoryzacji ułatwia testowanie nowej funkcji. Aby uzyskać więcej informacji na temat kluczy i autoryzacji, zobacz sekcję [Authorization keys (Klucze autoryzacji)](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys) w artykule [HTTP and webhook bindings (Powiązania protokołu HTTP i elementów webhook)](../articles/azure-functions/functions-bindings-http-webhook.md). |
4. Kliknij przycisk **OK**, aby utworzyć projekt funkcji i funkcję wyzwalaną przez żądanie HTTP.

