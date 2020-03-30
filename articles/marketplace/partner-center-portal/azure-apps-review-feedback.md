---
title: Obsługa opinii dotyczących oferty usługi Azure Apps w portalu Marketplace Commercial Marketplace
description: How to handle review feedback for Azure Apps offer for listing or selling in the Azure Marketplace, AppSource, or through the Cloud Solution Provider (CSP) program using the Commercial Marketplace portal on Microsoft Partner Center.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: 3932f3fc71dc6427b6cdf93d3a7bc58534a9981c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80279797"
---
# <a name="handling-review-feedback"></a>Obsługa opinii dla przeglądu

W tym artykule wyjaśniono, jak uzyskać dostęp do środowiska Azure DevOps używanego przez zespół przeglądu microsoft azure marketplace. Jeśli w ofercie aplikacji platformy Azure zostaną znalezione krytyczne problemy podczas kroku **przeglądu firmy Microsoft,** można zalogować się do tego systemu, aby wyświetlić szczegółowe informacje o tych problemach (opinie dotyczące opinii). Po rozwiązaniu wszystkich tych problemów należy ponownie przesłać ofertę, aby kontynuować publikowanie jej w portalu Azure Marketplace. Na poniższym diagramie przedstawiono, jak ten proces sprzężenia zwrotnego odnosi się do procesu publikowania.

![Przejrzyj proces opinii](./media/review-feedback-process.png)

Zazwyczaj problemy z przeglądem są odwoływane jako żądanie ściągnięcia (PR). Każdy element pr jest połączony z elementem [online Azure DevOps](https://azure.microsoft.com/services/devops/) (wcześniej nazwanym Program visual studio team services (VSTS)), który zawiera szczegółowe informacje o problemie. Na poniższej ilustracji przedstawiono przykład środowiska Centrum partnerów, jeśli podczas przeglądania występują problemy. 

![Stan publikowania](./media/publishing-status.png)

Informacje o treści zawierające szczegółowe informacje na temat przesyłania zostaną wymienione w linku "Wyświetl raport certyfikacji". W złożonych sytuacjach zespoły recenzentów i pomocy technicznej mogą również wysyłać do Ciebie wiadomości e-mail.

## <a name="azure-devops-access"></a>Dostęp do usługi Azure DevOps

Wszyscy użytkownicy z dostępem do roli "deweloper" w Centrum partnerów będą mieli dostęp do wyświetlania elementów PR, do których odwołuje się opinia recenzji.

<!---
To view the PR items referenced in review feedback, publishers must first be granted proper authorization. Otherwise, new publishers receive a 401 - Not Authorized response page when trying to view PRs. To request access to this Azure DevOps repository, perform the following steps:

1. Collect the following information:
    * Your publisher name and ID
    * Offer type (Azure app), offer name, and SKU ID
    * The pull request link, for example: `https://solutiontemplates.visualstudio.com/marketplacesolutions/_git/contoso/pullrequest/<number>`. This URL can be retrieved from the notification message or the address of the 401 response page.
    * The email address(es) of the individuals from your publishing organization that you want access granted to. This list should include the owner address(es) you provided when registering as a publisher on the Cloud Partner Portal (or Partner Center??).
1. Create a support incident. In the title bar of the Cloud Partner Portal (??), select the **Help** button, then choose **Support** from the menu. Your default web browser should launch and navigate to the Microsoft new support incident page. (You may have to sign in first.)
1. Specify the **Problem type** as **marketplace onboarding** and **Category** as **Access problem**, then select **Start request**.

    ![Create an incident](./media/create-an-incident.png)

1. In **Step 1 of 2** page, supply your contact information and select **Continue**.
1. In **Step 2 of 2** page, specify an incident title (for example Request Azure DevOps access) and supply the information you collected in the first step (above). Read and accept the agreement, then select **Submit**.

If the incident creation was successful, a confirmation page is displayed. Save the confirmation information on this page for your reference. The Microsoft Support Team should reply to your access request within a few business days. --->

## <a name="reviewing-the-pull-request"></a>Przeglądanie żądania ściągnięcia

Poniższa procedura służy do przeglądania problemów udokumentowanych w żądaniu ściągnięcia.

1. W sekcjach **przeglądu firmy Microsoft** w formularzu Kroki publikowania kliknij łącze PR, aby uruchomić przeglądarkę i przejdź do strony **Przegląd** (strona główna) dla tego pr. Na poniższej ilustracji przedstawiono przykład strony głównej problemu krytycznego dla przykładowej oferty aplikacji contoso. Ta strona zawiera przydatne informacje podsumowujące dotyczące problemów z przeglądem znalezionych w aplikacji platformy Azure.

    [![Strona główna żądania ściągnięcia](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> *Kliknij na obrazek, aby rozwinąć.*

1. (Opcjonalnie) Po prawej stronie okna w sekcji **Zasady**kliknij komunikat o problemie (w tym przykładzie: **Sprawdzanie poprawności zasad nie powiodło się),** aby zbadać szczegóły niskiego poziomu problemu, w tym skojarzone pliki dziennika. Błędy są zazwyczaj wyświetlane w dolnej części plików dziennika.
1. W menu po lewej stronie strony głównej wybierz pozycję **Pliki,** aby wyświetlić pliki listy, które składają się na zasoby techniczne tej oferty. Recenzenci firmy Microsoft powinni dodać komentarze opisujące wykryte krytyczne problemy. W poniższym przykładzie zostały wykryte dwa problemy.

    [![Strona główna żądania ściągnięcia](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> *Kliknij na obrazek, aby rozwinąć.*

1. Kliknij na każdy węzeł komentarza w lewym drzewie, aby przejść do komentarza w kontekście otaczającego kodu. Napraw kod źródłowy w projekcie zespołu, aby rozwiązać problem opisany przez komentarz.

>[!Note]
>Nie można edytować zasobów technicznych oferty w środowisku Azure DevOps zespołu recenzentów. Dla wydawców jest to środowisko tylko do odczytu dla zawartego kodu źródłowego. Można jednak pozostawić odpowiedzi na komentarze na korzyść zespołu recenzentów firmy Microsoft.

   W poniższym przykładzie wydawca przejrzał, poprawił i odpowiedział na pierwszy problem.

   ![Pierwsza poprawka i odpowiedź na komentarz](./media/first-comment-reply.png)

## <a name="next-steps"></a>Następne kroki

Po skorygowaniu krytycznych problemów udokumentowanych w pr(ach recenzji) należy [ponownie opublikować ofertę aplikacji platformy Azure](./create-new-azure-apps-offer.md#publish).
