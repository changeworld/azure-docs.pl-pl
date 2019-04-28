---
title: Przenieś ograniczonej wersji próbnej projektu na platformie Azure
titlesuffix: Azure Cognitive Services
description: Dowiedz się, jak przenieść projektu ograniczonej wersji próbnej na platformę Azure.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: anroth
ms.openlocfilehash: 6fac6531ea0a39796de13f95aee33b30dc91f131
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60816523"
---
# <a name="how-to-move-your-limited-trial-project-to-azure"></a>Jak przenieść projektu ograniczonej wersji próbnej na platformie Azure

Jak usługi Custom Vision Service ukończy przeniesieniu działalności na platformę Azure, zakończy się obsługa projektów w ograniczonej wersji próbnej spoza platformy Azure. W tym dokumencie opisano, jak skopiować projektu ograniczonej wersji próbnej do zasobu platformy Azure przy użyciu niestandardowych interfejsów API przetwarzania.

Obsługa wyświetlania projektów ograniczonej wersji próbnej na [wizji niestandardowe witryny sieci Web](https://customvision.ai) zakończyło się w dniu 25 marca 2019 r. W tym dokumencie teraz dowiesz się, jak używać interfejsów API przetwarzania niestandardowego za pomocą [skrypt w języku python migracji](https://github.com/Azure-Samples/custom-vision-move-project) w serwisie GitHub) zduplikowanie projektu do zasobu platformy Azure.

Aby uzyskać więcej informacji, w tym kluczowe terminy w procesie wycofywania wersji próbnej ograniczonej, przejdź do [informacje o wersji](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/release-notes#february-25-2019) lub wiadomości e-mail wysyłane do właścicieli ograniczonej wersji próbnej projektów.

[Skryptu migracji](https://github.com/Azure-Samples/custom-vision-move-project) umożliwia ponownie utworzyć projekt, pobieranie, a następnie przekazaniu wszystkie tagi, regionów, a obrazy w swojej bieżącej iteracji. Go spowoduje, że użytkownik z nowym projektem w swojej nowej subskrypcji, które można następnie szkolenie.

## <a name="prerequisites"></a>Wymagania wstępne

- Konieczne będzie ważnej subskrypcji platformy Azure skojarzone z konta Microsoft lub konta usługi Azure Active Directory (AAD), które mają zostać użyte do zalogowania się do [wizji niestandardowe witryny sieci Web](https://customvision.ai). 
    - Jeśli nie masz konta platformy Azure, [Tworzenie konta usługi](https://azure.microsoft.com/free/) za darmo.
    - Wprowadzenie do platformy Azure pojęcia związane z subskrypcji i zasobów, można znaleźć [przewodnik dla deweloperów platformy Azure.](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#manage-your-subscriptions).
-  [Python](https://www.python.org/downloads/)
- [Narzędzie PIP](https://pip.pypa.io/en/stable/installing/)

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Tworzenie niestandardowego przetwarzania zasobów w witrynie Azure portal

Usługi Custom Vision Service za pomocą platformy Azure, konieczne będzie utworzenie Custom Vision uczenia i przewidywania zasobów w [witryny Azure portal](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision). 

Wiele projektów może być skojarzona z pojedynczego zasobu. Więcej szczegółów na temat [ceny i limity](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/limits-and-quotas) jest dostępna. Aby kontynuować bezpłatne korzystanie z usługi Custom Vision Service, można wybrać warstwę F0 w witrynie Azure portal. 

> [!NOTE]
> Po przeniesieniu projektu Custom Vision do zasobu platformy Azure, dziedziczy on bazowego [uprawnienia]( https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) tego zasobu platformy Azure. Jeśli inni użytkownicy w Twojej organizacji właściciele zasobów platformy Azure znajduje się w projekcie, będą one uzyskasz dostęp do projektu w [wizji niestandardowe witryny sieci Web](https://customvision.ai). Podobnie usunięcie zasobów spowoduje usunięcie Twoich projektów.  

## <a name="find-your-limited-trial-project-information"></a>Znajdowanie informacji o ograniczonej wersji próbnej projekcie

Aby przenieść projektu, trzeba będzie _identyfikator projektu_ i _klucz szkolenia_ dla projektu, chcesz przeprowadzić migrację. Jeśli nie masz tych informacji, odwiedź stronę [ https://limitedtrial.customvision.ai/projects ](https://limitedtrial.customvision.ai/projects) uzyskać identyfikator i klucz dla każdego z projektów. 

## <a name="use-the-python-sample-code-to-copy-your-project-to-azure"></a>Użyj przykładowego kodu Python, aby skopiować projektu na platformie Azure

Postępuj zgodnie z [przykładowy kod instrukcje](https://github.com/Azure-Samples/custom-vision-move-project), korzystając z ograniczonej wersji próbnej klucza SSH i identyfikator projektu jako materiałów "źródło" i klucza z nowego zasobu platformy Azure utworzone jako "miejsce docelowe".

Domyślnie wszystkie projekty w ograniczonej wersji próbnej znajdują się w Południowej centralnej nam region świadczenia usługi Azure.

## <a name="next-steps"></a>Kolejne kroki

Teraz projekt został przeniesiony do zasobu platformy Azure. Należy zaktualizować kluczy wszystkie aplikacje, które zostały napisane przy użyciu uczenia i przewidywania.

Aby wyświetlić projekt na [wizji niestandardowe witryny sieci Web](https://customvision.ai), zaloguj się przy użyciu tego samego konta, które są używane do logowania się do witryny Azure portal. Jeśli projekt nie jest wyświetlane, potwierdź, że znajdują się w tym samym katalogu w [wizji niestandardowe witryny sieci Web](https://customvision.ai) jako katalog, gdzie Twoje zasoby znajdują się w witrynie Azure portal. W witrynie Azure portal i CustomVision.ai można wybrać katalog z menu rozwijanego użytkownika w prawym górnym rogu ekranu.