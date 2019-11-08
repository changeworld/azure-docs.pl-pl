---
title: Przenoszenie projektu z ograniczoną wersją próbną na platformę Azure
titleSuffix: Azure Cognitive Services
description: Czy masz ograniczony projekt wersji próbnej w Custom Vision? W tym artykule pokazano, jak przenieść go na platformę Azure za pomocą skryptu migracji.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: anroth
ms.openlocfilehash: 0a2b48a6c046150f6e685ecda0c0d765342e0194
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818951"
---
# <a name="how-to-move-your-limited-trial-project-to-azure"></a>Jak przenieść projekt ograniczonej wersji próbnej na platformę Azure

Ponieważ Custom Vision Service przejdziemy na platformę Azure, zostanie zakończona obsługa ograniczonych projektów w wersji próbnej poza platformą Azure. W tym dokumencie przedstawiono sposób używania interfejsów API Custom Vision do kopiowania ograniczonego projektu wersji próbnej do zasobu platformy Azure.

Obsługa wyświetlania ograniczonych projektów próbnych w [witrynie internetowej Custom Vision](https://customvision.ai) zakończyła się 25 marca 2019. W tym dokumencie opisano, jak używać interfejsów API Custom Vision ze [skryptem](https://github.com/Azure-Samples/custom-vision-move-project) w języku Python w usłudze GitHub w celu duplikowania projektu do zasobów platformy Azure.

Aby uzyskać więcej informacji, w tym kluczowe terminy w procesie wycofania z ograniczeniami wersji próbnej, zapoznaj się z [informacjami o wersji](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/release-notes#february-25-2019) lub pocztą e-mail wysłaną do właścicieli ograniczonej wersji próbnej.

[Skrypt migracji](https://github.com/Azure-Samples/custom-vision-move-project) umożliwia ponowne utworzenie projektu przez pobranie i przekazanie wszystkich tagów, regionów i obrazów w bieżącej iteracji. Spowoduje to pozostawienie nowego projektu w nowej subskrypcji, który można następnie wyszkolić.

## <a name="prerequisites"></a>Wymagania wstępne

- Wymagana jest poprawna subskrypcja platformy Azure skojarzona z kontem konto Microsoft lub Azure Active Directory (AAD), którego chcesz użyć do logowania się do [witryny sieci web Custom Vision](https://customvision.ai). 
    - Jeśli nie masz konta platformy Azure, [Utwórz konto](https://azure.microsoft.com/free/) bezpłatnie.
    - Wprowadzenie do pojęć związanych z subskrypcjami i zasobami platformy Azure można znaleźć w [przewodniku dewelopera platformy Azure.](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#manage-your-subscriptions)
-  [Python](https://www.python.org/downloads/)
- [Graczy](https://pip.pypa.io/en/stable/installing/)

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Utwórz zasoby Custom Vision w Azure Portal

Aby używać Custom Vision Service z platformą Azure, należy utworzyć Custom Vision szkolenia i zasoby przewidywania w [Azure Portal](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision). 

Z pojedynczym zasobem można skojarzyć wiele projektów. Dostępne są bardziej szczegółowe informacje na temat [cen i limitów](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/limits-and-quotas) . Aby nadal używać Custom Vision Service bezpłatnie, możesz wybrać warstwę F0 w Azure Portal. 

> [!NOTE]
> Po przeniesieniu projektu Custom Vision do zasobu platformy Azure dziedziczy on podstawowe [uprawnienia]( https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) tego zasobu platformy Azure. Jeśli inni użytkownicy w organizacji są właścicielami zasobów platformy Azure, w których znajduje się projekt, będą mogli uzyskać dostęp do projektu w [witrynie sieci web Custom Vision](https://customvision.ai). Podobnie usunięcie zasobów spowoduje usunięcie Twoich projektów.  

## <a name="find-your-limited-trial-project-information"></a>Znajdź ograniczone informacje o projekcie wersji próbnej

Aby przenieść projekt, musisz mieć _Identyfikator projektu_ i _klucz szkoleniowy_ dla projektu, który próbujesz zmigrować. Jeśli nie masz tych informacji, odwiedź stronę [https://www.customvision.ai/projects](https://www.customvision.ai/projects) , aby uzyskać identyfikator i klucz dla każdego projektu. 

## <a name="use-the-python-sample-code-to-copy-your-project-to-azure"></a>Używanie przykładowego kodu w języku Python do kopiowania projektu na platformę Azure

Postępuj zgodnie z [przykładowymi instrukcjami dotyczącymi kodu](https://github.com/Azure-Samples/custom-vision-move-project), używając ograniczonego klucza próbnego i identyfikatora projektu jako materiałów "source" i klucza z nowego zasobu platformy Azure utworzonego jako "miejsce docelowe".

Domyślnie wszystkie ograniczone projekty w wersji próbnej są hostowane w regionie Południowo-środkowe stany USA platformy Azure.

## <a name="next-steps"></a>Następne kroki

Projekt został przeniesiony do zasobu platformy Azure. Musisz zaktualizować swoje szkolenia i klucze przewidywania w aplikacjach, które zostały przez Ciebie zapisaniu.

Aby wyświetlić projekt w [witrynie sieci web Custom Vision](https://customvision.ai), zaloguj się przy użyciu tego samego konta, które zostało użyte do zalogowania się do Azure Portal. Jeśli Twój projekt nie jest wyświetlany, potwierdź, że znajduje się w tym samym katalogu w [witrynie sieci web Custom Vision](https://customvision.ai) , co katalog, w którym zasoby znajdują się w Azure Portal. Zarówno Azure Portal, jak i CustomVision.ai, możesz wybrać swój katalog z menu rozwijanego użytkownika w prawym górnym rogu ekranu.