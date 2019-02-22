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
ms.openlocfilehash: 76207e83e31ba3919da80e4ecc99435e88c76c66
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2019
ms.locfileid: "56654593"
---
# <a name="how-to-move-your-limited-trial-project-to-azure-using-the-customvisionai-site"></a>Jak przenieść projektu ograniczonej wersji próbnej na platformie Azure przy użyciu witryny CustomVision.ai


Ponieważ usługi Custom Vision Service jest teraz w [Azure w wersji zapoznawczej](https://azure.microsoft.com/services/preview/), kończy obsługę projektów ograniczonej wersji próbnej spoza platformy Azure. W tym dokumencie nauczy Cię sposobu używania [wizji niestandardowe witryny sieci Web](https://customvision.ai) można przenieść projektu ograniczonej wersji próbnej ma zostać skojarzony z zasobem platformy Azure. 

> [!NOTE]
> Gdy przeniesiesz swoje projekty Custom Vision do zasobu platformy Azure, ich bazowe Dziedzicz [uprawnienia]( https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) tego zasobu platformy Azure. Jeśli inni użytkownicy w Twojej organizacji właściciele zasobów platformy Azure znajduje się w projekcie, będą one uzyskasz dostęp do projektu w [wizji niestandardowe witryny sieci Web](https://customvision.ai). Podobnie usunięcie zasobów spowoduje usunięcie Twoich projektów.  


Wprowadzenie do platformy Azure pojęcia związane z subskrypcji i zasobów, można znaleźć [przewodnik dla deweloperów platformy Azure.](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#manage-your-subscriptions)


## <a name="prerequisites"></a>Wymagania wstępne

Konieczne będzie ważnej subskrypcji platformy Azure skojarzone z tego samego konta Microsoft lub konto usługi Azure Active Directory (AAD) używane do logowania się do [wizji niestandardowe witryny sieci Web](https://customvision.ai). 

Jeśli nie masz konta platformy Azure, [Tworzenie konta usługi](https://azure.microsoft.com/free/) za darmo.


## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Tworzenie niestandardowego przetwarzania zasobów w witrynie Azure portal
Usługi Custom Vision Service za pomocą platformy Azure, konieczne będzie utworzenie Custom Vision uczenia i przewidywania zasobów w [witryny Azure portal](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision). 

 Aby przenieść projektu za pomocą tego [wizji niestandardowe witryny sieci Web](https://customvision.ai) środowiska, należy utworzyć swoje zasoby w regionie południowo-środkowe stany USA, ponieważ wszystkie projekty w ograniczonej wersji próbnej znajdują się w południowo-środkowe stany USA. 

Wiele projektów może być skojarzona z pojedynczego zasobu. Więcej szczegółów na temat [ceny i limity](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/limits-and-quotas) jest dostępna. Aby kontynuować bezpłatne korzystanie z usługi Custom Vision Service, można wybrać warstwę F0 w witrynie Azure portal. 


## <a name="move-your-limited-trial-project-to-an-azure-resource"></a>Przenieś projektu ograniczonej wersji próbnej do zasobu platformy Azure

1.  W przeglądarce internetowej przejdź do [wizji niestandardowe witryny sieci Web](https://customvision.ai) i wybierz __Zaloguj__. Otwórz projekt, którą chcesz przeprowadzić migrację do konta platformy Azure. 
2.  Otwórz stronę ustawień projektu, klikając ikonę koła zębatego w prawej górnej rogu ekranu. 

    ![Ustawienia projektu jest ikona koła zębatego w prawym górnym rogu strony projektu.](./media/move-your-project-to-azure/settings-icon.png)


3. Kliknij pozycję __przenoszenie na platformę Azure__.

    ![Przejście do platformy Azure przycisk znajduje się w lewym dolnym rogu strony ustawień projektu.](./media/move-your-project-to-azure/move-to-azure.jpg)


4. Na liście rozwijanej na __przenoszenie na platformę Azure__ przycisku, wybierz projekt, aby przenieść zasób platformy Azure. Kliknij przycisk __przenieść__. 

5. Jeśli nie ma zasobów platformy Azure, która została utworzona wcześniej dla usługi Custom Vision Service, może być w innym katalogu. Aby przenieść zasób w innym katalogu projektu, postępuj zgodnie z poniższymi instrukcjami. 

    ![Okno migracji projektu.](./media/move-your-project-to-azure/Project_Migration_Window.jpg)


## <a name="move-your-limited-trial-project-to-an-azure-resource-in-another-directory"></a>Przenieś projektu ograniczonej wersji próbnej do zasobu platformy Azure w innym katalogu 

> [!NOTE]
> W witrynie Azure portal i CustomVision.ai można wybrać katalog z menu rozwijanego użytkownika w prawym górnym rogu ekranu.   


1. Określ katalog znajduje się w swoich zasobów platformy Azure. Można znaleźć katalogu, w obszarze swoją nazwę użytkownika w prawym górnym rogu paska menu portalu Azure. 

    ![Katalog znajduje się w obszarze swoją nazwę użytkownika w prawym górnym rogu paska menu portalu Azure. .](./media/move-your-project-to-azure/identify_directory.jpg)

2. Znajdź identyfikator zasobu dla zasobu usługi Custom Vision szkolenia. To w witrynie Azure portal można znaleźć, otwierając zasobu usługi Custom Vision szkolenia i wybierając pozycję "Właściwości" w sekcji "Zarządzanie zasobami". Twój identyfikator zasobu będą dostępne. 

    ![Znajdź identyfikator zasobu w w witrynie Azure portal, otwierając zasobu usługi Custom Vision szkolenia i wybierając pozycję "Właściwości" w sekcji "Zarządzanie zasobami".](./media/move-your-project-to-azure/resource_ID_azure_portal.jpg)


3. Alternatywnie można znaleźć Identyfikatora zasobu niestandardowego zasobu wizji bezpośrednio w witrynie sieci Web Custom Vision [strony ustawień]( https://www.customvision.ai/projects#/settings). Należy przełączyć się do tego samego katalogu, który znajduje się w swoich zasobów platformy Azure.

    ![Dla każdego zasobu na stronie Ustawienia w witrynie sieci Web Custom Vision znajduje się Twój identyfikator zasobu.](./media/move-your-project-to-azure/resource_ID_CVS_portal.jpg)

4. Teraz, gdy Twój identyfikator zasobu, wróć do projektu Custom Vision, którą chcesz przenieść z ograniczonej wersji próbnej do zasobu platformy Azure. Przypomnienie, może być konieczne przejdź z powrotem do oryginalnego katalogu możesz jej znaleźć. Postępuj zgodnie z instrukcjami [powyżej](#move-your-limited-trial-project-to-an-azure-resource) aby otworzyć stronę ustawień projektu i wybierz __przenoszenie na platformę Azure__. 


5. W przeniesieniu do platformy Azure okna zaznacz pole wyboru dla "Przenoszenia do innego katalogu platformy Azure?". Wybierz katalog, który chcesz przenieść projekt, a następnie wprowadź identyfikator zasobu zasobów, które są przenoszone do projektu. Kliknij przycisk __przenieść__. 



5. Należy pamiętać, że projekt jest teraz w innym katalogu. Aby znaleźć projektu, należy przełączyć się do tego samego katalogu, w portalu sieci web Custom Vision, który znajduje się w projekcie. W portalu Azure i [wizji niestandardowe witryny sieci Web](https://customvision.ai), katalogiem można wybrać z menu rozwijanego konta w prawym górnym rogu ekranu. 


