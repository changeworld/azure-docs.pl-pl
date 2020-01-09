---
title: Tworzenie wizualne
description: Dowiedz się, jak używać tworzenia wizualnego w Azure Data Factory
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
ms.reviewer: ''
manager: anandsub
ms.date: 12/19/2019
ms.openlocfilehash: 09d4055ba98da2dd87efc9421402f2827a87ba16
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75440933"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Tworzenie wizualne w Azure Data Factory

Środowisko interfejsu użytkownika Azure Data Factory (UX) umożliwia wizualne tworzenie i wdrażanie zasobów dla fabryki danych bez konieczności pisania kodu. Możesz przeciągnąć działania na kanwę potoku, wykonywać przebiegi testowe, debugować iteracyjnie i wdrażać i monitorować uruchomienia potoków.

Obecnie Azure Data Factory UX jest obsługiwane tylko w przeglądarkach Microsoft Edge i Google Chrome.

## <a name="authoring-canvas"></a>Kanwa tworzenia

Aby otworzyć **kanwę tworzenia**, kliknij ikonę ołówka. 

![Kanwa tworzenia](media/author-visually/authoring-canvas.png)

Tutaj utworzysz potoki, działania, zestawy danych, połączone usługi, przepływy danych, wyzwalacze i środowiska Integration Runtime wchodzące w skład Twojego fabryki. Aby rozpocząć tworzenie potoku przy użyciu kanwy tworzenia, zobacz [Kopiowanie danych za pomocą działania kopiowania](tutorial-copy-data-portal.md). 

Domyślne środowisko tworzenia wizualizacji działa bezpośrednio z usługą Data Factory. Azure Repos integracja z usługą Git lub GitHub jest również obsługiwana, aby umożliwić kontrolę źródła i współpracę w ramach potoków usługi Data Factory. Aby dowiedzieć się więcej o różnicach między tymi środowiskami tworzenia, zobacz [Kontrola źródła w Azure Data Factory](source-control.md).

## <a name="expressions-and-functions"></a>Wyrażenia i funkcje

Wyrażenia i funkcje mogą być używane zamiast wartości statycznych, aby określić wiele właściwości w Azure Data Factory.

Aby określić wyrażenie dla wartości właściwości, wybierz pozycję **Dodaj zawartość dynamiczną** , a następnie kliknij przycisk **Alt + P** , jednocześnie skupiając się na tym polu.

![Dodaj zawartość dynamiczną](media/author-visually/dynamic-content-1.png)

Spowoduje to otwarcie **konstruktora wyrażeń Data Factory** , w którym można tworzyć wyrażenia z obsługiwanych zmiennych systemowych, danych wyjściowych działań, funkcji i zmiennych lub parametrów określonych przez użytkownika. 

![Konstruktor wyrażeń](media/author-visually/dynamic-content-2.png)

Aby uzyskać informacje na temat języka wyrażeń, zobacz [Expressions and Functions in Azure Data Factory](control-flow-expression-language-functions.md).

## <a name="provide-feedback"></a>Prześlij opinię

Wybierz **opinię** , aby skomentować informacje o funkcjach lub powiadomić firmę Microsoft o problemach z narzędziem:

![Opinia](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat monitorowania potoków i zarządzania nimi, zobacz temat [monitorowanie potoków i zarządzanie nimi programowo](monitor-programmatically.md).
