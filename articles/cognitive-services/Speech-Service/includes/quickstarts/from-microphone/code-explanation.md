---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: dapine
ms.openlocfilehash: 3125b6203f04b4cabd8cd0822a97317185cbf194
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80638129"
---
Klucz subskrypcji zasobów mowy i region są wymagane do utworzenia obiektu konfiguracji mowy. Obiekt konfiguracji jest potrzebny do wystąpienia obiektu rozpoznawania mowy.

Wystąpienie aparatu rozpoznawania udostępnia wiele sposobów rozpoznawania mowy. W tym przykładzie mowa jest rozpoznawana raz. Ta funkcja pozwala usługi mowy wiedzieć, że wysyłasz jedną frazę do rozpoznawania i że po zidentyfikowaniu frazy, aby zatrzymać rozpoznawanie mowy. Po uzyskaniu wyniku kod zapisze przyczynę rozpoznawania do konsoli.

> [!TIP]
> SDK mowy domyślnie rozpoznaje `en-us` za pomocą języka, zobacz [Określanie języka źródłowego mowy do tekstu, aby](../../../how-to-specify-source-language.md) uzyskać informacje na temat wybierania języka źródłowego.