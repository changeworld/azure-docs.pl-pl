---
title: Wdrażanie skojarzeń dla aplikacji zarządzanej przy użyciu zasad
description: Dowiedz się więcej o wdrażaniu skojarzeń dla aplikacji zarządzanej przy użyciu usługi Azure Policy.
author: msHich
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: hich
ms.openlocfilehash: ec0fe8f66ef2ad2458b4ffad0e848591793e5b05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650944"
---
# <a name="deploy-associations-for-a-managed-application-using-azure-policy"></a>Wdrażanie skojarzeń dla aplikacji zarządzanej przy użyciu usługi Azure Policy

Zasady platformy Azure mogą służyć do wdrażania skojarzeń w celu skojarzenia zasobów z aplikacją zarządzaną. W tym artykule opisano wbudowane zasady, które wdraża skojarzenia i jak można użyć tej zasady.

## <a name="built-in-policy-to-deploy-associations"></a>Wbudowane zasady wdrażania skojarzeń

Wdrażanie skojarzeń dla aplikacji zarządzanej jest wbudowaną zasadą, która może służyć do wdrażania skojarzenia skojarzenia zasobu z aplikacją zarządzaną. Zasada akceptuje trzy parametry:

- Identyfikator aplikacji zarządzanej — ten identyfikator jest identyfikatorem zasobu zarządzanej aplikacji, z którą muszą być skojarzone zasoby.
- Typy zasobów do skojarzenia — te typy zasobów to lista typów zasobów, które mają być skojarzone z aplikacją zarządzaną. Można skojarzyć wiele typów zasobów z aplikacją zarządzaną przy użyciu tych samych zasad.
- Prefiks nazwy skojarzenia — ten ciąg jest prefiksem, który ma zostać dodany do nazwy tworzonego zasobu skojarzenia. Wartością domyślną jest "DeployedByPolicy".

Zasada używa DeployIfNotExists oceny. Jest uruchamiany po dostawcy zasobów obsługi żądania tworzenia lub aktualizacji zasobu wybranych typów zasobów i oceny zwrócił kod stanu sukcesu. Następnie zasób skojarzenia zostanie wdrożony przy użyciu wdrożenia szablonu.
Aby uzyskać więcej informacji na temat skojarzeń, zobacz [Dołączanie zasobów dostawców niestandardowych platformy Azure](../custom-providers/concepts-resource-onboarding.md)

## <a name="how-to-use-the-deploy-associations-built-in-policy"></a>Jak korzystać z wbudowanych skojarzeń wdrażania 

### <a name="prerequisites"></a>Wymagania wstępne
Jeśli aplikacja zarządzana potrzebuje uprawnień do subskrypcji, aby wykonać akcję, wdrożenie zasad zasobu skojarzenia nie będzie działać bez przyznania uprawnień.

### <a name="policy-assignment"></a>Przypisywanie zasad
Aby użyć wbudowanych zasad, utwórz przypisanie zasad i przypisz skojarzenia wdrażania dla zasad aplikacji zarządzanych. Po pomyślnym przypisaniu zasad zasady będą identyfikować zasoby niezgodne i wdrażać skojarzenie dla tych zasobów.

![Przypisywanie wbudowanych zasad](media/concepts-built-in-policy/assign-builtin-policy-managedapp.png)

## <a name="getting-help"></a>Uzyskiwanie pomocy

Jeśli masz pytania dotyczące tworzenia dostawców zasobów niestandardowych platformy Azure, spróbuj zadać je w [usłudze Przepełnienie stosu](https://stackoverflow.com/questions/tagged/azure-custom-providers). Na podobne pytanie można było już odpowiedzieć, więc sprawdź najpierw przed opublikowaniem. Dodaj tag, ```azure-custom-providers``` aby uzyskać szybką odpowiedź!

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się o użyciu wbudowanych zasad do wdrażania skojarzeń. Więcej informacji można znaleźć w następujących artykułach:

- [Pojęcia: Dołączanie zasobów dostawców niestandardowych platformy Azure](../custom-providers/concepts-resource-onboarding.md)
- [Samouczek: Dołączanie zasobów z dostawcami niestandardowymi](../custom-providers/tutorial-resource-onboarding.md)
- [Samouczek: Tworzenie niestandardowych akcji i zasobów na platformie Azure](../custom-providers/tutorial-get-started-with-custom-providers.md)
- [Szybki start: tworzenie niestandardowego dostawcy zasobów i wdrażanie zasobów niestandardowych](../custom-providers/create-custom-provider.md)
- [Jak: Dodawanie akcji niestandardowych do interfejsu API REST platformy Azure](../custom-providers/custom-providers-action-endpoint-how-to.md)
- [Jak: Dodawanie zasobów niestandardowych do interfejsu API REST platformy Azure](../custom-providers/custom-providers-resources-endpoint-how-to.md)
