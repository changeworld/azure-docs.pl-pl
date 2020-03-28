---
title: 'Szybki start: tworzenie i używanie pętli uczenia się za pomocą SDK - Personalizer'
description: Ten przewodnik Szybki start pokazuje, jak tworzyć bazę wiedzy i zarządzać nią przy użyciu sdk klienta.
ms.topic: quickstart
ms.date: 01/15/2020
zone_pivot_groups: programming-languages-set-six
ms.openlocfilehash: 7ebe22227b4323b2e6b1c3fc9ca31e171d1d97cd
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77524873"
---
# <a name="quickstart-personalizer-client-library"></a>Szybki start: biblioteka klienta personalizatora

Wyświetlanie spersonalizowanej zawartości w tym przewodniku Szybki start za pomocą usługi Personalizer.

Wprowadzenie do biblioteki klienta personalizatora. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań.

 * Interfejs API rangi — wybiera najlepszy element z elementów zawartości na podstawie informacji w czasie rzeczywistym, które podajesz o zawartości i kontekście.
 * Api nagrody — określasz wynik nagrody na podstawie potrzeb biznesowych, a następnie wysyłasz go do Personalizer za pomocą tego interfejsu API. Ten wynik może być pojedynczą wartością, taką jak 1 na dobre i 0 na złe, lub algorytm, który tworzysz na podstawie potrzeb biznesowych.

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get intent with C# SDK](./includes/quickstart-sdk-csharp.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Get intent with Node.js SDK](./includes/quickstart-sdk-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Get intent with Python SDK](./includes/quickstart-sdk-python.md)]
::: zone-end

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję usług Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie innych skojarzonych z nią zasobów.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
>[Jak działa usługa Personalizacja](how-personalizer-works.md)

* [Co to jest usługa Personalizacja?](what-is-personalizer.md)
* [Gdzie można używać usługi Personalizacja?](where-can-you-use-personalizer.md)
* [Rozwiązywanie problemów](troubleshooting.md)
* Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py).
