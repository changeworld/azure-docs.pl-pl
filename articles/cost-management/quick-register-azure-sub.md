---
title: Rejestracja subskrypcji platformy Azure w rozwiązaniu Cloudyn | Microsoft Docs
description: Zarejestruj się w rozwiązaniu Cloudyn przy użyciu subskrypcji platformy Azure.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 08/07/2018
ms.topic: quickstart
ms.custom: ''
ms.service: cost-management
manager: dougeby
ms.openlocfilehash: e49be3d61cfbbf71a0e9cbeef4c171f3af222544
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46979560"
---
# <a name="register-an-individual-azure-subscription-and-view-cost-data"></a>Rejestrowanie indywidualnej subskrypcji platformy Azure i wyświetlanie informacji o koszcie

Korzystając z subskrypcji platformy Azure, możesz zarejestrować się w rozwiązaniu Cloudyn. Rejestracja zapewnia dostęp do portalu firmy Cloudyn. W tym przewodniku Szybki start opisano szczegółowo proces rejestracji niezbędny do utworzenia subskrypcji wersji próbnej rozwiązania Cloudyn i zalogowania w portalu Cloudyn. Przedstawiono w nim także, jak od razu rozpocząć wyświetlanie danych kosztów.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

- Zaloguj się do witryny Azure Portal pod adresem http://portal.azure.com.

## <a name="register-with-cloudyn"></a>Rejestrowanie w rozwiązaniu Cloudyn

1. W witrynie Azure Portal kliknij pozycję **Zarządzanie kosztami i rozliczenia** na liście usług.
2. W obszarze **Przegląd** kliknij pozycję **Cloudyn**.  
    ![Strona rozwiązania Cloudyn](./media/quick-register-azure-sub/cost-mgt-billing-service.png)
3. Na stronie **Zarządzanie kosztami** kliknij pozycję **Przejdź do rozwiązania Cloudyn**, aby otworzyć stronę rejestracji rozwiązania Cloudyn w nowym oknie.
4. Na stronie rejestracji wersji próbnej rozwiązania Cloudyn wpisz nazwę swojej firmy i wybierz pozycję **Azure Individual Subscription Owner** (Posiadacz indywidualnej subskrypcji platformy Azure), a następnie kliknij przycisk **Next** (Dalej). Nazwa konta i identyfikator dzierżawy są automatycznie dodawane do formularza.  
    ![Rejestracja wersji próbnej](./media/quick-register-azure-sub/trial-reg-ind.png)
5. Wybierz wartość **Offer ID - Name** (Identyfikator oferty —Nazwa) skojarzoną z Twoją subskrypcją. Jeśli nie masz pewności co do identyfikatora stawki dla swojej subskrypcji, możesz sprawdzić rachunek za platformę Azure i wyszukać pozycję **Offer ID** (Identyfikator oferty).
6. Zaakceptuj warunki użytkowania, zweryfikuj swoje informacje i kliknij przycisk **Next** (Dalej).
7. Na stronie **Gather additional data** (Zbierz dodatkowe dane) kliknij przycisk **Next** (Dalej), aby autoryzować rozwiązanie Cloudyn do zbierania danych zasobów platformy Azure. Te dane obejmują dane dotyczące użycia, wydajności, rozliczeń i tagów z Twoich subskrypcji.  
    ![Zbieranie dodatkowych danych](./media/quick-register-azure-sub/gather-additional.png)
8. Przeglądarka przeniesie Cię do strony logowania w usłudze Cloudyn. Zaloguj się przy użyciu poświadczeń subskrypcji platformy Azure.
9. Kliknij pozycję **Go to Cloudyn** (Przejdź do rozwiązania Cloudyn), aby otworzyć portal Cloudyn, a następnie na stronie **Accounts Management** (Zarządzanie kontami) powinny zostać wyświetlone dane Twojego konta subskrypcji platformy Azure.  
    ![Zarządzanie kontami](./media/quick-register-azure-sub/accounts-mgt.png)

Aby wyświetlić film wideo z samouczkiem dotyczącym rejestrowania subskrypcji platformy Azure, zobacz [Finding your Directory GUID and Rate ID for use in Cloudyn](https://youtu.be/PaRjnyaNGMI) (Wyszukiwanie identyfikatora GUID katalogu i identyfikatora stawki do korzystania z rozwiązaniem Cloudyn).

[!INCLUDE [cost-management-create-account-view-data](../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start opisano rejestrację w rozwiązaniu Cloudyn przy użyciu danych subskrypcji platformy Azure. Przedstawiono także logowanie się do portalu Cloudyn i rozpoczęcie wyświetlania danych kosztów. Aby dowiedzieć się więcej na temat rozwiązania Cloudyn, przejdź do samouczka dotyczącego rozwiązania Cloudyn.

> [!div class="nextstepaction"]
> [Przeglądanie użycia i kosztów](./tutorial-review-usage.md)
