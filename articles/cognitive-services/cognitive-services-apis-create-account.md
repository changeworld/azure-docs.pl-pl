---
title: Utwórz konto interfejsy API usług Cognitive Services w witrynie Azure portal | Dokumentacja firmy Microsoft
description: Jak utworzyć konta interfejsów API firmy Microsoft Cognitive Services w witrynie Azure portal.
services: cognitive-services
documentationcenter: ''
author: garyericson
manager: cgronlun
editor: ''
ms.assetid: b6176bb2-3bb6-4ebf-84d1-3598ee6e01c6
ms.service: cognitive-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2018
ms.author: garye
ms.reviewer: gibattag
ms.openlocfilehash: ed5f19b23375ecb83e19274c7405e9a1208a7985
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2018
ms.locfileid: "39036159"
---
# <a name="create-a-cognitive-services-apis-account-in-the-azure-portal"></a>Utwórz konto interfejsy API usług Cognitive Services w witrynie Azure portal

Aby korzystać z interfejsu API usług Cognitive firmy Microsoft, należy najpierw utworzyć konto w witrynie Azure portal.

1. Zaloguj się w [Portalu Azure](http://portal.azure.com).

2. Kliknij przycisk **+ Utwórz zasób**.

3. W obszarze Azure Marketplace wybierz **SI i Cognitive Services** i wykrywa listę dostępnych interfejsów API. Kliknij pozycję **holograficznych** Aby wyświetlić całą listę interfejsy API usług Cognitive Services. Polecenie interfejsu API w wybranym w taki sposób, aby kontynuować.

    ![Wybierz interfejsy API usług Cognitive Services](media/cognitive-services-apis-create-account/select-cognitive-services-apis.png)

4. Na **Utwórz** Podaj następujące informacje:

   - **Nazwa konta:** nazwy konta. Firma Microsoft zaleca używanie nazwę opisową, na przykład *AFaceAPIAccount*.

   - **Subskrypcja:** wybierz jedną z dostępnych subskrypcji platformy Azure, do których użytkownik ma co najmniej uprawnienia współautora.

   - **Typ interfejsu API:** wybierz Cognitive Services API, którego chcesz użyć. Aby uzyskać więcej informacji na temat różnych Cognitive Services interfejsami API dostępnymi odwiedź [usług Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) lokacji.

   - **Warstwa cenowa:** koszt konta usług Cognitive Services jest zależny od rzeczywistego użycia i opcji, możesz wybrać. Aby uzyskać więcej informacji na temat cen dla każdego interfejsu API, zobacz [stronach z cennikami](https://azure.microsoft.com/pricing/details/cognitive-services/).

   - **Grupa zasobów:** grupa zasobów to kolekcja zasobów, które mają ten sam cykl życia, uprawnienia i zasady. Aby dowiedzieć się więcej na temat grup zasobów, zobacz [zarządzanie zasobami platformy Azure za pośrednictwem portalu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

   - **Lokalizacja grupy zasobów:** jest to wymagane tylko wtedy, gdy interfejs API wybrane globalne (nie powiązane z lokalizacją). Jeśli interfejs API jest globalna i niepowiązana do lokalizacji, jednak należy określić lokalizację dla grupy zasobów, gdzie znajduje się metadane skojarzone z kontem interfejsu API usług Cognitive Services. Ta lokalizacja nie ma wpływu na dostępność konta w czasie wykonywania. Aby dowiedzieć się więcej na temat grup zasobów, zobacz [zarządzanie zasobami platformy Azure za pośrednictwem portalu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

   - **Jawnego potwierdzenia postanowieniami dotyczącymi świadczenia usług Online:** aby można było utworzyć konto subskrypcji właściciele lub współautorzy (zgodnie z definicją [kontroli dostępu](https://docs.microsoft.com/azure/role-based-access-control/overview)) musi jawnie potwierdzić warunki, dotyczy usług Cognitive Services w [postanowieniami dotyczącymi świadczenia usług Online](https://www.microsoft.com/en-us/Licensing/product-licensing/products.aspx). 

     Właściciel subskrypcji można wyłączyć tworzenie konta usług Cognitive Services dla konkretnej grupy zasobów lub subskrypcji za pośrednictwem [zasady usługi Azure](../azure-policy/azure-policy-introduction.md) postępując zgodnie z artykułem [przy użyciu Azure portal, aby przypisać i zarządzanie nimi zasady zasobów](../azure-policy/assign-policy-definition.md) i przypisywanie definicji zasad "nie dozwolone typy zasobów" i określając **Microsoft.CognitiveServices/accounts** jako typ zasobu docelowego.

     Jeśli tworzenie kont zostało wyłączone, w czasie tworzenia konta będzie wyświetlony następujący błąd:

     ![Błąd tworzenia konta](media/cognitive-services-apis-create-account/error-message.png)

5. Aby przypiąć konta na pulpicie nawigacyjnym witryny Azure portal, kliknij przycisk **Przypnij do pulpitu nawigacyjnego**.

6. Kliknij przycisk **Utwórz**, aby utworzyć konto.

Po pomyślnym wdrożeniu konta usług Cognitive Services kliknij Kafelek na pulpicie nawigacyjnym, aby wyświetlić informacje o koncie.

Możesz użyć **adresu URL punktu końcowego** w **Przegląd** sekcji i kluczy w **klucze** sekcji, aby rozpocząć tworzenie interfejsu API wywołuje w swoich aplikacjach.

![Wyświetlanie informacji o koncie](media/cognitive-services-apis-create-account/display-account.png)

![Wyświetl klucze konta](media/cognitive-services-apis-create-account/account-keys.png)

### <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat wszystkich Microsoft Cognitive Services dostępny zobacz [usług Cognitive Services](https://azure.microsoft.com/services/cognitive-services/).

Aby uzyskać przy użyciu interfejsów API usług Cognitive Services przykładowe przewodniki Szybki Start:

 - [Przewodniki Szybki Start komputera przetwarzania języka C#](computer-vision/quickstarts/csharp.md)
 - [Analiza tekstu przy użyciu języka Python](text-analytics/quickstarts/python.md)
 - [Interfejs API za pomocą języka JavaScript rozpoznawania twarzy](face/quickstarts/javascript.md)
