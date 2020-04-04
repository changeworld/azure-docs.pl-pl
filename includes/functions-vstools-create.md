---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/26/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 477f3cf270377bc1341e65ab500093e05277afa8
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657523"
---
Szablon projektu usługi Azure Functions w programie Visual Studio tworzy projekt, który można opublikować w aplikacji funkcji na platformie Azure. Za pomocą aplikacji funkcji można grupować funkcje jako jednostkę logiczną w celu łatwiejszego zarządzania, wdrażania, skalowania i udostępniania zasobów.

1. Z menu Programu Visual Studio wybierz polecenie **Plik** > **nowego** > **projektu**.

1. W **obszarze Tworzenie nowego projektu**wprowadź *funkcje* w polu wyszukiwania, wybierz szablon **Usługi Azure,** a następnie wybierz pozycję **Dalej**.

1. W **obszarze Konfigurowanie nowego projektu**wprowadź nazwę **projektu** dla projektu, a następnie wybierz pozycję **Utwórz**. Nazwa aplikacji funkcji musi być prawidłową nazwą przestrzeni nazw C#, dlatego nie należy używać znaków podkreślenia, łączników ani znaków innych niż alfanumeryczne.

1. W przypadku tworzenia nowych ustawień **aplikacji funkcji platformy Azure** należy użyć wartości w poniższej tabeli:

    | Ustawienie      | Wartość  | Opis                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Środowisko wykonawcze funkcji** | **Usługi Azure <br />Functions w wersji 2 (.NET Core)** | Ta wartość tworzy projekt funkcji, który używa środowiska wykonawczego w wersji 2.x usługi Azure Functions, która obsługuje .NET Core. Usługa Azure Functions 1.x obsługuje program .NET Framework. Aby uzyskać więcej informacji, zobacz [omówienie wersji środowiska wykonawczego usługi Azure Functions](../articles/azure-functions/functions-versions.md).   |
    | **Szablon funkcji** | **Wyzwalacz HTTP** | Ta wartość tworzy funkcję wyzwalaną przez żądanie HTTP. |
    | **Konto magazynu**  | **Emulator magazynu** | Ponieważ funkcja platformy Azure wymaga konta magazynu, jeden jest przypisany lub utworzony podczas publikowania projektu na platformie Azure. Wyzwalacz HTTP nie używa ciągu połączenia konta usługi Azure Storage; wszystkie inne typy wyzwalaczy wymagają prawidłowego ciągu połączenia konta usługi Azure Storage.  |
    | **Prawa dostępu** | **Anonimowe** | Utworzona funkcja może zostać wyzwolona przez dowolnego klienta bez podawania klucza. To ustawienie autoryzacji ułatwia testowanie nowej funkcji. Aby uzyskać więcej informacji na temat kluczy i autoryzacji, zobacz [Klucze autoryzacji](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) oraz [powiązania HTTP i webhook](../articles/azure-functions/functions-bindings-http-webhook.md). |
    

    
    ![Ustawienia projektu usługi Azure Functions](./media/functions-vs-tools-create/functions-project-settings.png)

    Upewnij się, że ustawiłeś **prawa dostępu** na **Anonimowy**. Jeśli wybierzesz domyślny poziom **funkcji,** musisz przedstawić [klucz funkcyjny](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) w żądaniach dostępu do punktu końcowego funkcji.

1. Wybierz **pozycję Utwórz,** aby utworzyć projekt funkcji i funkcję wyzwalacza HTTP.
