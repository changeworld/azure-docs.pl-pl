---
title: Rejestracja subskrypcji platformy Azure w rozwiązaniu Azure Cost Management | Microsoft Docs
description: Zarejestruj się w usłudze Azure Cost Management przy użyciu subskrypcji platformy Azure.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 06/07/2018
ms.topic: quickstart
ms.custom: ''
ms.service: cost-management
manager: dougeby
ms.openlocfilehash: 254a7f2904a63381a4eeec02ee32b19890703ae1
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38310109"
---
# <a name="register-an-individual-azure-subscription-and-view-cost-data"></a>Rejestrowanie indywidualnej subskrypcji platformy Azure i wyświetlanie informacji o koszcie

Do rejestrowania w usłudze Azure Cost Management służy subskrypcja platformy Azure. Rejestracja zapewnia dostęp do portalu firmy Cloudyn. W tym przewodniku Szybki start opisano szczegółowo proces rejestracji niezbędny do utworzenia subskrypcji wersji próbnej rozwiązania Cloudyn i zalogowania w portalu Cloudyn. Przedstawiono w nim także, jak od razu rozpocząć wyświetlanie danych kosztów.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

- Zaloguj się do witryny Azure Portal pod adresem http://portal.azure.com.

## <a name="register-with-azure-cost-management"></a>Rejestrowanie w usłudze Azure Cost Management

1. W witrynie Azure Portal kliknij pozycję **Zarządzanie kosztami i rozliczenia** na liście usług.
2. W obszarze **Przegląd** kliknij pozycję **Zarządzanie kosztami**  
    ![Strona Zarządzanie kosztami](./media/quick-register-azure-sub/cost-mgt-billing-service.png)
3. Na stronie **Zarządzanie kosztami** kliknij pozycję **Przejdź do zarządzania kosztami**, aby otworzyć stronę rejestracji rozwiązania Cloudyn w nowym oknie.
4. Na stronie rejestracji wersji próbnej rozwiązania Cloudyn wpisz nazwę swojej firmy i wybierz pozycję **Azure Individual Subscription Owner** (Posiadacz indywidualnej subskrypcji platformy Azure), a następnie kliknij przycisk **Next** (Dalej). Nazwa konta i identyfikator dzierżawy są automatycznie dodawane do formularza.  
    ![rejestracja wersji próbnej](./media/quick-register-azure-sub/trial-reg-ind.png)
5. Wybierz wartość **Offer ID - Name** (Identyfikator oferty —Nazwa) skojarzoną z Twoją subskrypcją. Jeśli nie masz pewności co do identyfikatora stawki dla swojej subskrypcji, możesz sprawdzić rachunek za platformę Azure i wyszukać pozycję **Offer ID** (Identyfikator oferty).
6. Zaakceptuj warunki użytkowania, zweryfikuj swoje informacje i kliknij przycisk **Next** (Dalej).
7. Na stronie **Gather additional data** (Zbierz dodatkowe dane) kliknij przycisk **Next** (Dalej), aby autoryzować rozwiązanie Cloudyn do zbierania danych zasobów platformy Azure. Te dane obejmują dane dotyczące użycia, wydajności, rozliczeń i tagów z Twoich subskrypcji.  
    ![zbieranie dodatkowych danych](./media/quick-register-azure-sub/gather-additional.png)
8. Przeglądarka przeniesie Cię do strony logowania w usłudze Cloudyn. Zaloguj się przy użyciu poświadczeń subskrypcji platformy Azure.
9. Kliknij pozycję **Go to Cloudyn** (Przejdź do rozwiązania Cloudyn), aby otworzyć portal Cloudyn, a następnie na stronie **Accounts Management** (Zarządzanie kontami) powinny zostać wyświetlone dane Twojego konta subskrypcji platformy Azure.  
    ![Zarządzanie kontami](./media/quick-register-azure-sub/accounts-mgt.png)

Aby wyświetlić film wideo z samouczkiem dotyczącym rejestrowania subskrypcji platformy Azure, zobacz [Finding your Directory GUID and Rate ID for use in Azure Cost Management](https://youtu.be/PaRjnyaNGMI) (Wyszukiwanie identyfikatora GUID katalogu i identyfikatora stawki do korzystania z rozwiązaniem Azure Cost Management).

[!INCLUDE [cost-management-create-account-view-data](../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start opisano rejestrację w usłudze Cost Management przy użyciu danych subskrypcji platformy Azure. Przedstawiono także logowanie się do portalu Cloudyn i rozpoczęcie wyświetlania danych kosztów. Aby dowiedzieć się więcej na temat usługi Azure Cost Management, przejdź do samouczka usługi Cost Management.

> [!div class="nextstepaction"]
> [Przeglądanie użycia i kosztów](./tutorial-review-usage.md)
