---
title: Rejestracja subskrypcji platformy Azure w rozwiązaniu Cloudyn | Microsoft Docs
description: W tym przewodniku Szybki start opisano szczegółowo proces rejestracji niezbędny do utworzenia subskrypcji wersji próbnej rozwiązania Cloudyn i zalogowania w portalu Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: quickstart
ms.custom: seodec18
ms.service: cost-management
manager: benshy
ms.openlocfilehash: 6d5282a326af37e5653f21795438ba8965ae7fcc
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967204"
---
# <a name="register-an-individual-azure-subscription-and-view-cost-data"></a>Rejestrowanie indywidualnej subskrypcji platformy Azure i wyświetlanie informacji o koszcie

Korzystając z subskrypcji platformy Azure, możesz zarejestrować się w rozwiązaniu Cloudyn. Rejestracja zapewnia dostęp do portalu firmy Cloudyn. W tym przewodniku Szybki start opisano szczegółowo proces rejestracji niezbędny do utworzenia subskrypcji wersji próbnej rozwiązania Cloudyn i zalogowania w portalu Cloudyn. Przedstawiono w nim także, jak od razu rozpocząć wyświetlanie danych kosztów.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

- Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="register-with-cloudyn"></a>Rejestrowanie w rozwiązaniu Cloudyn

1. W witrynie Azure Portal kliknij pozycję **Zarządzanie kosztami i rozliczenia** na liście usług.
2. W obszarze **Przegląd** kliknij pozycję **Cloudyn**.  
    ![Strona rozwiązania Cloudyn wyświetlana w witrynie Azure Portal](./media/quick-register-azure-sub/cost-mgt-billing-service.png)
3. Na stronie **Zarządzanie kosztami** kliknij pozycję **Przejdź do rozwiązania Cloudyn**, aby otworzyć stronę rejestracji rozwiązania Cloudyn w nowym oknie.
4. Na stronie rejestracji wersji próbnej rozwiązania Cloudyn wpisz nazwę swojej firmy i wybierz pozycję **Azure Individual Subscription Owner** (Posiadacz indywidualnej subskrypcji platformy Azure), a następnie kliknij przycisk **Next** (Dalej). Nazwa konta i identyfikator dzierżawy są automatycznie dodawane do formularza.  
    ![Strona rejestracji wersji próbnej, na której można wprowadzić swoje informacje dotyczące rejestracji](./media/quick-register-azure-sub/trial-reg-ind.png)
5. Wybierz wartość **Offer ID - Name** (Identyfikator oferty —Nazwa) skojarzoną z Twoją subskrypcją. Jeśli nie masz pewności co do identyfikatora stawki dla swojej subskrypcji, możesz sprawdzić rachunek za platformę Azure i wyszukać pozycję **Offer ID** (Identyfikator oferty).
6. Zaakceptuj warunki użytkowania, zweryfikuj swoje informacje i kliknij przycisk **Next** (Dalej).
7. Na stronie **Gather additional data** (Zbierz dodatkowe dane) kliknij przycisk **Next** (Dalej), aby autoryzować rozwiązanie Cloudyn do zbierania danych zasobów platformy Azure. Te dane obejmują dane dotyczące użycia, wydajności, rozliczeń i tagów z Twoich subskrypcji.  
    ![Strona zbierania dodatkowych danych służąca do autoryzacji rozwiązania Cloudyn](./media/quick-register-azure-sub/gather-additional.png)
8. Przeglądarka przeniesie Cię do strony logowania w usłudze Cloudyn. Zaloguj się przy użyciu poświadczeń subskrypcji platformy Azure.
9. Kliknij pozycję **Go to Cloudyn** (Przejdź do rozwiązania Cloudyn), aby otworzyć portal Cloudyn, a następnie na stronie **Accounts Management** (Zarządzanie kontami) powinny zostać wyświetlone dane Twojego konta subskrypcji platformy Azure.  
    ![Strona Accounts Management (Zarządzanie kontami) z wyświetlonymi informacjami o subskrypcji platformy Azure](./media/quick-register-azure-sub/accounts-mgt.png)

Aby wyświetlić film wideo z samouczkiem dotyczącym rejestrowania subskrypcji platformy Azure, zobacz [Finding your Directory GUID and Rate ID for use in Cloudyn](https://youtu.be/PaRjnyaNGMI) (Wyszukiwanie identyfikatora GUID katalogu i identyfikatora stawki do korzystania z rozwiązaniem Cloudyn).

[!INCLUDE [cost-management-create-account-view-data](../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki start opisano rejestrację w rozwiązaniu Cloudyn przy użyciu danych subskrypcji platformy Azure. Przedstawiono także logowanie się do portalu Cloudyn i rozpoczęcie wyświetlania danych kosztów. Aby dowiedzieć się więcej na temat rozwiązania Cloudyn, przejdź do samouczka dotyczącego rozwiązania Cloudyn.

> [!div class="nextstepaction"]
> [Przeglądanie użycia i kosztów](./tutorial-review-usage.md)
