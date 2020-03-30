---
title: Wdrażanie skojarzeń przy użyciu zasad
description: Dowiedz się więcej o wdrażaniu skojarzeń dla dostawcy niestandardowego przy użyciu usługi Azure Policy.
author: msHich
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: hich
ms.openlocfilehash: 536b95eb07619d0ce2d02ec01e1f51ed52c1b5e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650476"
---
# <a name="deploy-associations-for-a-custom-provider-using-azure-policy"></a>Wdrażanie skojarzeń dla dostawcy niestandardowego przy użyciu zasad platformy Azure

Zasady platformy Azure mogą służyć do wdrażania skojarzeń w celu skojarzenia zasobów z dostawcą niestandardowym. W tym artykule opisano wbudowane zasady, które wdraża skojarzenia i jak można użyć tej zasady.

## <a name="built-in-policy-to-deploy-associations"></a>Wbudowane zasady wdrażania skojarzeń

Wdrażanie skojarzeń dla dostawcy niestandardowego jest wbudowaną zasadą, która może służyć do wdrażania skojarzenia skojarzenia zasobu z dostawcą niestandardowym. Zasada akceptuje trzy parametry:

- Identyfikator dostawcy niestandardowego — ten identyfikator jest identyfikatorem zasobu dostawcy niestandardowego, z którym muszą być skojarzone zasoby.
- Typy zasobów do skojarzenia — te typy zasobów to lista typów zasobów, które mają być skojarzone z dostawcą niestandardowym. Można skojarzyć wiele typów zasobów z dostawcą niestandardowym przy użyciu tych samych zasad.
- Prefiks nazwy skojarzenia — ten ciąg jest prefiksem, który ma zostać dodany do nazwy tworzonego zasobu skojarzenia. Wartością domyślną jest "DeployedByPolicy".

Zasada używa DeployIfNotExists oceny. Działa po dostawcy zasobów obsługi żądania zasobów tworzenia lub aktualizacji i oceny zwrócił kod stanu sukcesu. Następnie zasób skojarzenia zostanie wdrożony przy użyciu wdrożenia szablonu.
Aby uzyskać więcej informacji na temat skojarzeń, zobacz [Dołączanie zasobów dostawców niestandardowych platformy Azure](./concepts-resource-onboarding.md)

## <a name="how-to-use-the-deploy-associations-built-in-policy"></a>Jak korzystać z wbudowanych skojarzeń wdrażania 

### <a name="prerequisites"></a>Wymagania wstępne
Jeśli dostawca niestandardowy potrzebuje uprawnień do zakresu zasad do wykonania akcji, wdrożenie zasad zasobu skojarzenia nie będzie działać bez przyznania uprawnień.

### <a name="policy-assignment"></a>Przypisywanie zasad
Aby użyć wbudowanych zasad, utwórz przypisanie zasad i przypisz skojarzenia wdrażania dla niestandardowych zasad dostawców. Następnie zasady będą identyfikować zasoby niezgodne i wdrażać skojarzenie dla tych zasobów.

![Przypisywanie wbudowanych zasad](media/concepts-built-in-policy/assign-builtin-policy-customprovider.png)

## <a name="getting-help"></a>Uzyskiwanie pomocy

Jeśli masz pytania dotyczące tworzenia dostawców zasobów niestandardowych platformy Azure, spróbuj zadać je w [usłudze Przepełnienie stosu](https://stackoverflow.com/questions/tagged/azure-custom-providers). Na podobne pytanie można było już odpowiedzieć, więc sprawdź najpierw przed opublikowaniem. Dodaj tag, ```azure-custom-providers``` aby uzyskać szybką odpowiedź!

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się o użyciu wbudowanych zasad do wdrażania skojarzeń. Więcej informacji można znaleźć w następujących artykułach:

- [Pojęcia: Dołączanie zasobów dostawców niestandardowych platformy Azure](./concepts-resource-onboarding.md)
- [Samouczek: Dołączanie zasobów z dostawcami niestandardowymi](./tutorial-resource-onboarding.md)
- [Samouczek: Tworzenie niestandardowych akcji i zasobów na platformie Azure](./tutorial-get-started-with-custom-providers.md)
- [Szybki start: tworzenie niestandardowego dostawcy zasobów i wdrażanie zasobów niestandardowych](./create-custom-provider.md)
- [Jak: Dodawanie akcji niestandardowych do interfejsu API REST platformy Azure](./custom-providers-action-endpoint-how-to.md)
- [Jak: Dodawanie zasobów niestandardowych do interfejsu API REST platformy Azure](./custom-providers-resources-endpoint-how-to.md)
