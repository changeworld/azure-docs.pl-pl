---
title: Rejestracja umowy Enterprise Agreement platformy Azure za pomocą rozwiązania Cloudyn | Microsoft Docs
description: Zarejestruj się w rozwiązaniu Cloudyn przy użyciu platformy Enterprise Agreement platformy Azure.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/14/2018
ms.topic: quickstart
ms.custom: ''
ms.service: cost-management
manager: dougeby
ms.openlocfilehash: bed1ea3785c35d1053a0ff2147c3bdd797e28581
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46996642"
---
# <a name="register-an-azure-enterprise-agreement-and-view-cost-data"></a>Rejestracja umowy Enterprise Agreement platformy Azure i wyświetlanie danych kosztów

Do rejestrowania w rozwiązaniu Cloudyn służy umowa Enterprise Agreement platformy Azure. Rejestracja zapewnia dostęp do portalu firmy Cloudyn. W tym przewodniku Szybki start opisano szczegółowo proces rejestracji niezbędny do utworzenia subskrypcji wersji próbnej rozwiązania Cloudyn i zalogowania w portalu Cloudyn. Przedstawiono w nim także, jak od razu rozpocząć wyświetlanie danych kosztów.

Usługa Azure Cost Management oferuje podobne funkcje jak rozwiązanie Cloudyn. Usługa Azure Cost Management to natywne rozwiązanie do zarządzania kosztami na platformie Azure. Ułatwia ona analizowanie kosztów, tworzenie budżetów i zarządzanie nimi, eksportowanie danych, a także zapoznawanie się z rekomendacjami dotyczącymi optymalizacji i ich wdrażanie w celu zaoszczędzenia pieniędzy. Aby uzyskać więcej informacji, zobacz [Azure Cost Management](overview-cost-mgt.md).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

- Zaloguj się do witryny Azure Portal pod adresem http://portal.azure.com.

## <a name="register-with-cloudyn"></a>Rejestrowanie w rozwiązaniu Cloudyn

1. W witrynie Azure Portal kliknij pozycję **Zarządzanie kosztami i rozliczenia** na liście usług.
2. W obszarze **Przegląd** kliknij pozycję **Cloudyn**.  
    ![Strona rozwiązania Cloudyn](./media/quick-register-ea/cost-mgt-billing-service.png)
3. Na stronie **Cloudyn** wybierz pozycję **Przejdź do rozwiązania Cloudyn**, aby otworzyć stronę rejestracji rozwiązania Cloudyn w nowym oknie.
4. Na stronie rejestracji wersji próbnej rozwiązania Cloudyn wpisz nazwę swojej firmy i wybierz pozycję **Azure Enterprise Enrollment Administrator** (Administrator rejestracji umowy Enterprise Agreement platformy Azure).  
    ![rejestracja wersji próbnej](./media/quick-register-ea/trial-reg.png)
5. Wprowadź klucz interfejsu API rejestracji Enterprise Portal. Jeśli nie masz klucza pod ręką, kliknij link [Enterprise Portal](https://ea.azure.com) i wykonaj następujące kroki:
  1. Zaloguj się do witryny Azure Enterprise i przycisk **Reports** (Raporty), kliknij przycisk **API Access Key** (Klucz dostępu API), a następnie skopiuj klucz podstawowy.  
    ![Klucz API EA](./media/quick-register-ea/ea-key.png)
  3. Wróć do strony rejestracji i wklej swój klucz API.
6. Zaakceptuj warunki użytkowania i zweryfikuj swój klucz. Kliknij przycisk **Next** (Dalej), aby autoryzować rozwiązanie Cloudyn do zbierania danych zasobów platformy Azure. Te dane obejmują dane dotyczące użycia, wydajności, rozliczeń i tagów z Twoich subskrypcji.  
    ![walidacja klucza](./media/quick-register-ea/ea-key-validated.png)
7. W obszarze **Invite other stakeholders** (Zaproś innych uczestników projektu) możesz dodać użytkowników, wpisując ich adresy e-mail. Po zakończeniu kliknij przycisk **Next** (Dalej). W zależności od rodzaju rejestracji platformy Azure dodanie wszystkich danych dotyczących rozliczeń do rozwiązania Cloudyn może potrwać do 24 godzin.
8. Kliknij pozycję **Go to Cloudyn** (Przejdź do rozwiązania Cloudyn), aby otworzyć portal Cloudyn, a następnie na stronie **Cloud Accounts Management** (Zarządzanie kontami w chmurze) powinny zostać wyświetlone dane Twojego zarejestrowanego konta EA.

Aby obejrzeć film wideo dotyczący rejestrowania umowy Enterprise Agreement, zobacz [How to Find Your EA Enrollment ID and API Key for use in Cloudyn (Jak wyszukać swój identyfikator rejestracji EA do użytku w rozwiązaniu Cloudyn)](https://youtu.be/u_phLs_udig).

[!INCLUDE [cost-management-create-account-view-data](../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start opisano rejestrację w rozwiązaniu Cloudyn przy użyciu danych umowy Enterprise Agreement platformy Azure. Przedstawiono także logowanie się do portalu Cloudyn i rozpoczęcie wyświetlania danych kosztów. Aby dowiedzieć się więcej na temat rozwiązania Cloudyn, przejdź do samouczka dotyczącego rozwiązania Cloudyn.

> [!div class="nextstepaction"]
> [Przeglądanie użycia i kosztów](./tutorial-review-usage.md)
