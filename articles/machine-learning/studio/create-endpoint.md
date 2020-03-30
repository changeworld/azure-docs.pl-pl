---
title: Tworzenie punktów końcowych usługi sieci Web
titleSuffix: ML Studio (classic) - Azure
description: Tworzenie punktów końcowych usługi sieci web w usłudze Azure Machine Learning Studio (klasyczny). Każdy punkt końcowy w usłudze sieci web jest niezależnie adresowane, ograniczane i zarządzane.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/15/2019
ms.openlocfilehash: 980ed1e54de30ec8a2dc0c1fdac6546d31f48a00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218198"
---
# <a name="create-endpoints-for-deployed-azure-machine-learning-studio-classic-web-services"></a>Tworzenie punktów końcowych dla wdrożonych usług sieci Web usługi Azure Machine Learning Studio (klasyczne)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

> [!NOTE]
> W tym temacie opisano techniki mające zastosowanie do **klasycznej** usługi sieci web uczenia maszynowego.

Po wdrożeniu usługi sieci Web dla tej usługi tworzony jest domyślny punkt końcowy. Domyślny punkt końcowy może być wywoływany przy użyciu właściwego dla niego klucza interfejsu API. Więcej punktów końcowych można dodać za pomocą własnych kluczy z portalu usług sieci Web.
Każdy punkt końcowy w usłudze sieci web jest niezależnie adresowane, ograniczane i zarządzane. Każdy punkt końcowy jest unikatowy adres URL z kluczem autoryzacji, który można rozpowszechniać do klientów.

## <a name="add-endpoints-to-a-web-service"></a>Dodawanie punktów końcowych do usługi sieci web

Punkt końcowy można dodać do usługi sieci web za pomocą portalu usług azure machine learning web services. Po utworzeniu punktu końcowego można go używać za pośrednictwem synchroniczowych interfejsów API, wsadowych interfejsów API i arkuszy programu Excel.

> [!NOTE]
> Jeśli dodano dodatkowe punkty końcowe do usługi sieci web, nie można usunąć domyślnego punktu końcowego.

1. W programie Machine Learning Studio (klasycznym) w lewej kolumnie nawigacji kliknij pozycję Usługi sieci Web.
2. U dołu pulpitu nawigacyjnego usługi sieci Web kliknij pozycję **Zarządzaj punktami końcowymi**. Portal usług azure machine learning web services otwiera się na stronie punktów końcowych dla usługi sieci web.
3. Kliknij **pozycję Nowy**.
4. Wpisz nazwę i opis nowego punktu końcowego. Nazwy punktów końcowych muszą mieć długość 24 znaków lub mniejszą i muszą być wykonane z małych liter lub liczb. Wybierz poziom rejestrowania i czy dane przykładowe są włączone. Aby uzyskać więcej informacji na temat rejestrowania, zobacz [Włączanie rejestrowania dla usług sieci Web usługi uczenia maszynowego](web-services-logging.md).

## <a name="scale-a-web-service-by-adding-additional-endpoints"></a><a id="scaling"></a>Skalowanie usługi sieci web przez dodanie dodatkowych punktów końcowych

Domyślnie każda opublikowana usługa sieci web jest skonfigurowana do obsługi 20 równoczesnych żądań i może mieć nawet 200 równoczesnych żądań. Usługa Azure Machine Learning Studio (klasyczna) automatycznie optymalizuje ustawienie, aby zapewnić najlepszą wydajność dla usługi sieci web, a wartość portalu jest ignorowana.

Jeśli planujesz wywołać interfejs API z wyższym obciążeniem niż maksymalna wartość wywołań współbieżnych 200 będzie obsługiwać, należy utworzyć wiele punktów końcowych w tej samej usłudze sieci web. Następnie można losowo rozłożyć swoje obciążenie na wszystkie z nich.

Skalowanie usługi sieci web jest typowym zadaniem. Niektóre powody skalowania są do obsługi więcej niż 200 równoczesnych żądań, zwiększyć dostępność za pośrednictwem wielu punktów końcowych lub zapewnić oddzielne punkty końcowe dla usługi sieci web. Skalę można zwiększyć, dodając dodatkowe punkty końcowe dla tej samej usługi sieci web za pośrednictwem portalu [usługi azure machine learning web service.](https://services.azureml.net/)

Należy pamiętać, że przy użyciu wysokiej liczby współbieżności może być szkodliwe, jeśli nie wywołujesz interfejsu API z odpowiednio wysoką szybkością. Może pojawić się sporadyczne limity czasu i/lub skoki w opóźnieniu, jeśli umieścisz stosunkowo niskie obciążenie interfejsu API skonfigurowanego do wysokiego obciążenia.

Synchroniczne interfejsy API są zwykle używane w sytuacjach, gdy pożądane jest małe opóźnienie. Opóźnienie w tym miejscu oznacza czas potrzebny do interfejsu API, aby zakończyć jedno żądanie i nie uwzględnia żadnych opóźnień sieci. Załóżmy, że masz interfejs API z opóźnieniem 50 ms. Aby w pełni wykorzystać dostępną pojemność z poziomem przepustnicy Wysokie i Maksymalne równoczesne wywołania = 20, należy wywołać ten interfejs API 20 * 1000 / 50 = 400 razy na sekundę. Rozszerzając to dalej, Maksymalna liczba równoczesnych wywołań 200 umożliwia wywołanie interfejsu API 4000 razy na sekundę, przy założeniu opóźnienia 50 ms.

## <a name="next-steps"></a>Następne kroki

[Jak korzystać z usługi sieci web usługi Azure Machine Learning](consume-web-services.md).
