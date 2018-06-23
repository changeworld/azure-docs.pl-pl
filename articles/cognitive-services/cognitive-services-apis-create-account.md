---
title: Utwórz konto kognitywnych usługi interfejsów API w portalu Azure | Dokumentacja firmy Microsoft
description: Jak utworzyć konto Microsoft kognitywnych usług API w portalu Azure.
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
ms.openlocfilehash: 3697dd0628f0028cb486139e92c032f0d757c6ed
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347748"
---
# <a name="create-a-cognitive-services-apis-account-in-the-azure-portal"></a>Utwórz konto kognitywnych usługi interfejsów API w portalu Azure

Aby używać interfejsów API usługi Service kognitywnych firmy Microsoft, należy najpierw utworzyć konto w portalu Azure.

1. Zaloguj się w [Portalu Azure](http://portal.azure.com).

2. Kliknij przycisk **+ Utwórz zasób**.

3. W portalu Azure Marketplace, wybierz **AI + kognitywnych usług** i wykrywa listę dostępnych interfejsach API. Polecenie **zobaczyć wszystkie** Aby wyświetlić całą listę kognitywnych interfejsów API usługi. Polecenie interfejsu API wybranym w taki sposób, aby kontynuować.

    ![Wybierz interfejsy API usługi kognitywnych](media/cognitive-services-apis-create-account/select-cognitive-services-apis.png)

4. Na **Utwórz** Podaj następujące informacje:

   - **Nazwa konta:** nazwę konta. Firma Microsoft zaleca używanie opisową nazwę, na przykład *AFaceAPIAccount*.

   - **Subskrypcja:** wybierz jedną z dostępnych subskrypcji platformy Azure, do których użytkownik ma co najmniej uprawnienia współautora.

   - **Typ interfejsu API:** wybierz kognitywnych interfejsu API usług, którego chcesz użyć. Aby uzyskać więcej informacji o różnych kognitywnych usług API dostępne, odwiedź stronę [kognitywnych usług](https://azure.microsoft.com/services/cognitive-services/) lokacji.

   - **Warstwa cenowa:** koszt konta kognitywnych usług jest zależna od rzeczywistego użycia i opcji, możesz wybrać. Aby uzyskać więcej informacji na temat cen dla każdego interfejsu API, zapoznaj się [cennik stron](https://azure.microsoft.com/pricing/details/cognitive-services/).

   - **Grupa zasobów:** grupa zasobów to kolekcja zasobów, które mają cykl życia, uprawnienia i zasady. Aby dowiedzieć się więcej na temat grup zasobów, zobacz [zasobów Azure zarządzania za pośrednictwem portalu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

   - **Lokalizacja grupy zasobów:** jest to wymagane tylko wtedy, gdy wybrany interfejs API globalnego (nie powiązany z lokalizacji). Jeśli interfejs API jest globalna i niepowiązana do lokalizacji, jednak można określić lokalizacji dla grupy zasobów, w której znajduje się metadane skojarzone z kontem kognitywnych interfejsu API usług. Ta lokalizacja nie ma wpływu na dostępność środowiska uruchomieniowego Twojego konta. Aby dowiedzieć się więcej na temat grup zasobów, zobacz [zasobów Azure zarządzania za pośrednictwem portalu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

   - **Jawnego potwierdzenia warunków usług Online:** Aby utworzyć konto subskrypcji właściciele i współautorzy (zgodnie z definicją w [kontroli dostępu](https://docs.microsoft.com/azure/role-based-access-control/overview)) musi jawnie potwierdzić warunki który Zastosuj kognitywnych w usługach [Online warunki usługi](https://www.microsoft.com/en-us/Licensing/product-licensing/products.aspx). 

     Właściciela subskrypcji można wyłączyć tworzenia konta usługi kognitywnych dla określonej grupy zasobów lub subskrypcji przez [Azure zasady](../azure-policy/azure-policy-introduction.md) wykonując artykuł [portalu Azure przy użyciu przypisywania i zarządzanie nimi zasady zasobów](../azure-policy/assign-policy-definition.md) i przypisywanie definicji zasad "zasobu nie jest dozwolone typy" i określając **Microsoft.CognitiveServices/accounts** jako typu zasobu docelowego.

     Jeśli tworzenie konta została wyłączona, będzie wyświetlana następujący błąd w czasie tworzenia konta:

     ![Błąd tworzenia konta](media/cognitive-services-apis-create-account/error-message.png)

5. Opcję przypięcia konta do pulpitu nawigacyjnego portalu Azure, kliknij przycisk **Przypnij do pulpitu nawigacyjnego**.

6. Kliknij przycisk **Utwórz**, aby utworzyć konto.

Po pomyślnym wdrożeniu konta usług kognitywnych kliknij na pulpicie nawigacyjnym, aby wyświetlić informacje o koncie.

Można użyć **adres URL punktu końcowego** w **omówienie** sekcji i kluczy w **klucze** wywołuje sekcji, aby rozpocząć tworzenie interfejsu API w aplikacji.

![Wyświetl informacje o koncie](media/cognitive-services-apis-create-account/display-account.png)

![Wyświetl klucze konta](media/cognitive-services-apis-create-account/account-keys.png)

### <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat wszystkich usług firmy Microsoft kognitywnych dostępne, zobacz [kognitywnych usług](https://azure.microsoft.com/services/cognitive-services/).

Dla Przewodniki Szybki Start przy użyciu przykładowych kognitywnych API usług:

 - [Komputer wizji C# poradniki Szybki Start](/computer-vision/quickstarts/csharp.md)
 - [Analiza tekstu z języka Python](/text-analytics/quickstarts/python.md)
 - [Powierzchni interfejsu API w języku JavaScript](/face/quickstarts/javascript.md)
