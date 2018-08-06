---
title: Samouczek dotyczący usługi Azure Security Center — Definiowanie i ocenianie zasad zabezpieczeń | Microsoft Docs
description: Samouczek dotyczący usługi Azure Security Center — Definiowanie i ocenianie zasad zabezpieczeń
services: security-center
documentationcenter: na
author: TerryLanfear
manager: mbaldwin
editor: ''
ms.assetid: 2d248817-ae97-4c10-8f5d-5c207a8019ea
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/30/2018
ms.author: terrylan
ms.openlocfilehash: 15c69bce87ede96eb3a7bc0bada4e4f6a6669abb
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2018
ms.locfileid: "39358612"
---
# <a name="tutorial-define-and-assess-security-policies"></a>Samouczek: Definiowanie i ocenianie zasad zabezpieczeń
Usługa Security Center pomaga zapewnić zgodność z wymaganiami dotyczącymi zabezpieczeń określonymi przez firmę lub przepisy, definiując pożądaną konfigurację Twoich obciążeń przy użyciu zasad zabezpieczeń. Gdy zdefiniujesz zasady dla Twoich subskrypcji platformy Azure oraz dostosujesz je do typu obciążenia lub wrażliwości danych, usługa Security Center może podać zalecenia dotyczące zabezpieczeń zasobów obliczeniowych, aplikacji sieciowych, SQL i magazynowych oraz tożsamości i uzyskiwania dostępu do zasobów. Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie zasad zabezpieczeń
> * Ocenianie zabezpieczeń zasobów

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).

## <a name="prerequisites"></a>Wymagania wstępne
Aby wypróbować funkcje omówione w tym samouczku, musisz korzystać ze standardowej warstwy cenowej usługi Security Center. Standardową warstwę cenową usługi Security Center możesz wypróbować bezpłatnie przez pierwsze 60 dni. Przewodnik szybkiego startu [Dołączanie subskrypcji platformy Azure do standardowej usługi Security Center](security-center-get-started.md) przeprowadzi Cię przez procedurę uaktualniania do warstwy standardowej.

## <a name="configure-security-policy"></a>Konfigurowanie zasad zabezpieczeń
Usługa Security Center automatycznie tworzy domyślne zasady zabezpieczeń dla każdej Twojej subskrypcji platformy Azure. Zasady zabezpieczeń składają się z zaleceń, które można włączać lub wyłączać w zależności od wymagań dotyczących zabezpieczeń danej subskrypcji. Aby wprowadzać zmiany domyślnych zasad zabezpieczeń, musisz być właścicielem, współautorem lub administratorem zabezpieczeń subskrypcji.

1. W menu głównym usługi Security Center wybierz pozycję **Zasady zabezpieczeń**.
2. Wybierz subskrypcję, której chcesz użyć.

  ![Zasady zabezpieczeń](./media/tutorial-security-policy/tutorial-security-policy-fig1.png)  

3. W sekcji **SKŁADNIKI ZASAD** wybierz pozycję **Zasady zabezpieczeń**
4. Dla każdej konfiguracji zabezpieczeń, którą chcesz monitorować, wybierz pozycję **Włącz**. Usługa Security Center w sposób ciągły będzie oceniać konfigurację środowiska i jeśli istnieje luka w zabezpieczeniach, wygeneruje zalecenie dotyczące zabezpieczeń. Wybierz pozycję **Wyłącz**, jeśli konfiguracja zabezpieczeń jest niezalecana lub nieodpowiednia. Na przykład w środowisku programistycznym/testowym może nie być wymagany taki sam poziom zabezpieczeń jak w środowisku produkcyjnym. Po wybraniu zasad odpowiednich dla danego środowiska kliknij pozycję **Zapisz**.

  ![Konfiguracja zabezpieczeń](./media/tutorial-security-policy/tutorial-security-policy-fig6.png)  

Zaczekaj, aż usługa Security Center przetworzy te zasady i wygeneruje zalecenia. W przypadku niektórych konfiguracji, takich jak aktualizacje systemu i konfiguracje systemu operacyjnego, ocena może potrwać nawet 12 godzin, podczas gdy grupy zabezpieczeń sieci i konfiguracje szyfrowania mogą zostać ocenione niemal natychmiast. Gdy na pulpicie nawigacyjnym usługi Security Center zostaną wyświetlone zalecenia, możesz przejść do następnego kroku.

## <a name="assess-security-of-resources"></a>Ocenianie zabezpieczeń zasobów
1. W zależności od zasad zabezpieczeń, które zostały włączone, usługa Security Center udostępni odpowiedni zestaw zaleceń dotyczących zabezpieczeń. Rozpocznij od przejrzenia zaleceń dotyczących maszyny wirtualnej i komputerów. Na pulpicie nawigacyjnym usługi Security Center kliknij pozycję **Przegląd**, a następnie kliknij pozycję **Wystąpienia obliczeniowe i aplikacje**.

  ![Wystąpienia obliczeniowe](./media/tutorial-security-policy/tutorial-security-policy-fig2.png)

  Przejrzyj poszczególne zalecenia, zwracając uwagę przede wszystkim na zalecenia w kolorze czerwonym (mające wysoki priorytet). Niektóre z tych zaleceń mają działanie naprawcze, które można zaimplementować bezpośrednio z usługi Security Center, na przykład [problemy z ochroną punktów końcowych](https://docs.microsoft.com/azure/security-center/security-center-install-endpoint-protection). Inne zalecenia mają tylko wytyczne dotyczące działania naprawczego, które należy zastosować, na przykład zalecenie dotyczące braku szyfrowania dysków.

2. Po wykonaniu wszystkich niezbędnych zaleceń dotyczących wystąpień obliczeniowych należy przejść do kolejnego obciążenia: sieci. Na pulpicie nawigacyjnym usługi Security Center kliknij pozycję **Przegląd**, a następnie kliknij pozycję **Sieć**.

  ![Networking](./media/tutorial-security-policy/tutorial-security-policy-fig3.png)

  Na stronie zaleceń dotyczących sieci znajduje się lista problemów z zabezpieczeniami konfiguracji, punktów końcowych mających połączenie z Internetem i topologii sieci. Podobnie jak w przypadku **wystąpień obliczeniowych i aplikacji**, niektóre zalecenia dotyczące sieci zawierają zintegrowane działania naprawcze, a inne nie.

3. Po wykonaniu wszystkich niezbędnych zaleceń dotyczących sieci należy przejść do kolejnego obciążenia: magazynu i danych. Na pulpicie nawigacyjnym usługi Security Center kliknij pozycję **Przegląd**, a następnie kliknij pozycję **Dane i magazyn**.

  ![Zasoby danych](./media/tutorial-security-policy/tutorial-security-policy-fig4.png)

  Na stronie **Zasoby danych** znajdują się zalecenia dotyczące włączania inspekcji dla serwerów SQL i baz danych platformy Azure, włączania szyfrowania baz danych SQL oraz włączania szyfrowania konta magazynu Azure. Jeśli nie masz tych obciążeń, nie są wyświetlane żadne zalecenia. Podobnie jak w przypadku **wystąpień obliczeniowych i aplikacji**, niektóre zalecenia dotyczące danych i magazynu zawierają zintegrowane działania naprawcze, a inne nie.

4. Po wykonaniu wszystkich niezbędnych zaleceń dotyczących danych i magazynu należy przejść do kolejnego obciążenia: Tożsamość i dostęp. Na pulpicie nawigacyjnym usługi Security Center kliknij pozycję **Przegląd**, a następnie kliknij pozycję **Tożsamość i dostęp**.

  ![Tożsamość i dostęp](./media/tutorial-security-policy/tutorial-security-policy-fig5.png)

  Strona **Tożsamość i dostęp** zawiera następujące zalecenia:

   - Włącz usługę MFA dla kont uprzywilejowanych w ramach subskrypcji
   - Usuń konta zewnętrzne z uprawnieniami do zapisu z subskrypcji
   - Usuń uprzywilejowane konta zewnętrzne z subskrypcji

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Inne przewodniki szybkiego startu i samouczki w tej kolekcji bazują na tym przewodniku. Jeśli zamierzasz kontynuować pracę z kolejnymi przewodnikami szybkiego startu i samouczkami, w dalszym ciągu korzystaj z warstwy standardowej i zachowaj włączoną automatyczną aprowizację. Jeśli nie zamierzasz kontynuować lub chcesz wrócić do warstwy bezpłatnej:

1. Wróć do menu głównego usługi Security Center i wybierz pozycję **Zasady zabezpieczeń**.
2. Wybierz subskrypcję lub zasady, którym chcesz przywrócić warstwę bezpłatną. Zostanie otwarte okno **Zasady zabezpieczeń**.
3. W obszarze **SKŁADNIKI ZASAD** wybierz pozycję **Warstwa cenowa**.
4. Wybierz pozycję **Bezpłatna**, aby zmienić subskrypcję z warstwy standardowej na warstwę bezpłatną.
5. Wybierz pozycję **Zapisz**.

Jeśli chcesz wyłączyć automatyczną aprowizację:

1. Wróć do menu głównego usługi Security Center i wybierz pozycję **Zasady zabezpieczeń**.
2. Wybierz subskrypcję, dla której chcesz wyłączyć automatyczną aprowizację.
3. W sekcji **Zasady zabezpieczeń — zbieranie danych** wybierz pozycję **Wyłącz** w obszarze **Dołączanie**, aby wyłączyć automatyczną aprowizację.
4. Wybierz pozycję **Zapisz**.

>[!NOTE]
> Wyłączenie automatycznej aprowizacji nie powoduje usunięcia programu Microsoft Monitoring Agent z maszyn wirtualnych platformy Azure, na których aprowizowano agenta. Wyłączenie automatycznej aprowizacji powoduje ograniczenie monitorowania zabezpieczeń dla zasobów.
>

## <a name="next-steps"></a>Następne kroki
W tym samouczku przedstawiono podstawową definicję zasad i ocenę zabezpieczeń Twojego obciążenia za pomocą usługi Security Center, takie jak:

> [!div class="checklist"]
> * Konfiguracja zasad zabezpieczeń w celu zapewnienia zgodności z firmowymi lub prawnymi wymaganiami dotyczącymi zabezpieczeń
> * Ocena zabezpieczeń dla zasobów dotyczących zasobów obliczeniowych, sieciowych, SQL i magazynowych oraz zasobów aplikacji

Przejdź do następnego samouczka, aby dowiedzieć się, jak chronić swoje zasoby za pomocą usługi Security Center.

> [!div class="nextstepaction"]
> [Ochrona zasobów](tutorial-protect-resources.md)
