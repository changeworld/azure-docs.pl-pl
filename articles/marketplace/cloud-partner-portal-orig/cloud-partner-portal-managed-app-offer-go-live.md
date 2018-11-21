---
title: Aplikacja Azure oferty na żywo w witrynie Azure Marketplace | Dokumentacja firmy Microsoft
description: .
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: pbutlerm
ms.openlocfilehash: 18a8e6ae8ab3bd4299c6a014f938e73a2a021492
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2018
ms.locfileid: "52263595"
---
<a name="make-your-azure-application-offer-live-on-azure-marketplace"></a>Aplikacja Azure oferty na żywo w witrynie Azure Marketplace 
===========================================================

Teraz, jak\'ve wypełniane wszystkie szczegóły oferty go\'s czasu, aby opublikować ofertę i udostępnić go na żywo w witrynie Azure Marketplace. Istnieje kilka kroków, upewnij się, spełniają wymagania jakości być certyfikatem platformy Azure, przed przejściem na żywo w witrynie internetowej aplikacji i zawartości marketingowej.

![Publikowanie usługi Flow](./media/cloud-partner-portal-publish-managed-app/publish_flow.png)

Pozwól\'s przechodzić przez ten proces, bardziej szczegółowo, aby lepiej zrozumieć, co się dzieje w każdym kroku i czynności do wykonania w ramach tego procesu dla zapewnienia oferty w dalszym ciągu postępu.

<a name="publishing-process"></a>Proces publikowania 
------------------

Należy kliknąć \"Publikuj\" w obszarze **edytora** kartę, aby rozpocząć proces publikowania.

![Oferty z rzeczywistym użyciem na żywo sekwencji 1 — publikowanie](./media/cloud-partner-portal-publish-managed-app/newOffer_managedApp_publish.png)

W obszarze **stan** kartę, zostanie wyświetlony, publikowania czynności i który krok jest ofertą znajduje się w.

![Oferty z rzeczywistym użyciem na żywo sekwencji 2 - przepływu pracy](./media/cloud-partner-portal-publish-managed-app/newOffer_managedApp_workflow.png)

W dowolnym momencie proces publikowania, możesz również zalogować i kliknij przycisk **oferuje wszystkie** kartę, aby wyświetlić najnowszy stan dla wszystkich Twoich ofert. Można kliknąć bezpośrednio na stan oferty i wyświetlić szczegółowe informacje, na którym jest oferty w procesie publikowania.

> [!WARNING]
> Jeśli autorstwa użycia klienta nie jest włączona, podczas procesu publikowania zostanie wyświetlony następujący komunikat o błędzie: "partnerów platformy Azure klienta użycia: uznanie autorstwa Brak w co najmniej jeden szablon usługi Azure Resource Manager. Aby rozwiązać problem, należy dodać śledzenia identyfikator GUID szablonu usługi Azure Resource Manager w pakietach mainTemplate.json dla następującego planu: usługi servicenow. Aby uzyskać więcej informacji, zobacz http://aka.ms/customerusageattribution. " 

Teraz omówimy każdy z kroków publikowania i omówić, co się dzieje w każdym kroku i jak długo należy oszacować że każdy krok spowoduje przejście.

### <a name="validate-prerequisites-1-day"></a>Weryfikowanie wymagań wstępnych (\<1 dzień) 

Po kliknięciu \"Publikuj\", zautomatyzowane kontrole będzie miała miejsce, aby zagwarantować, że\'ve wypełniane wszystkie wymagane pola w ofercie. Jeśli wszystkie pola są wypełniane, obok pola zostanie wyświetlone ostrzeżenie i należy go wypełnić dokładnego, a następnie kliknij pozycję \'Publikuj\' ponownie.

Po\'ve ukończyć ten krok, aby poprosić o adres e-mail pojawi się okno podręczne. To jest wiadomość e-mail, do którego zostanie wyświetlony publikowania powiadomienia o stanie dla pozostałych procesu publikowania. Po przesłaniu adresu e-mail, ten krok jest pełny.

![Oferty z rzeczywistym użyciem na żywo sekwencji 1 — publikowanie 1](./media/cloud-partner-portal-publish-managed-app/newOffer_managedApp_publish1.png)

### <a name="certification-5-days"></a>Certyfikacja (\<5 dni) 

Ten krok jest, gdzie możemy uruchomić wiele testów, aby upewnić się, że pakiet aplikacji na platformie Azure spełnia wymagania jako z certyfikatem platformy Azure.

Ponieważ może to potrwać kilka dni, można wylogować portalu Cloud Partner. Wyślemy wiadomość e-mail z powiadomieniem, jeśli wystąpiły żadne błędy. Jeśli testy zakończą się pomyślnie z sukcesem, proces zostanie automatycznie przejść do kroku inicjowania obsługi.

### <a name="packaging-and-lead-generation-registration-1-hour"></a>Pakowanie i spowodować generowanie rejestracji (\<1 godzina) 

Na tym etapie firma Microsoft na to, co będzie stronę produktu w witrynie internetowej łączenia zawartości techniczne i marketingowe.

### <a name="offer-available-in-preview"></a>Ta oferta jest dostępna w wersji zapoznawczej 

Otrzymasz wiadomość e-mail z powiadomieniem, że Twoja oferta została ukończona pomyślnie kroki wymagane do dostępu do oferty w wersji zapoznawczej. W tym kroku należy wyświetlenia podglądu oferty i upewnij się, że wszystko wygląda jak należy go. Sprawdź, jeśli maszyna wirtualna jest prawidłowo wdrożony w środowisku przejściowym.

Tylko na liście dozwolonych subskrypcje można wykonać tej weryfikacji.\*

### <a name="publisher-sign-out"></a>Wyloguj się wydawcy 

Po upewnieniu się, wszystko wygląda poprawnie i działa poprawnie, w wersji zapoznawczej, można przystąpić do emisji na żywo. Kliknij Go Live na karcie Stan i firma Microsoft rozpocznie kroki, aby utworzyć ofertę na żywo, w środowisku produkcyjnym i w witrynie sieci Web. Zazwyczaj potrwa kilka godzin od momentu, możesz kliknąć Go Live i oferta jest dostępna na żywo w witrynie sieci Web. Wyślemy wiadomość e-mail z powiadomieniem po oferty oficjalnie na żywo w witrynie sieci Web.

![Przejdź na żywo sekwencji 6 oferuje — przejdź na żywo](./media/cloud-partner-portal-publish-managed-app/newOffer_managedApp_goLive.png)

### <a name="microsoft-review"></a>Przegląd firmy Microsoft 

Gdy jesteś gotowy i kliknięciu \"Go Live\", bezpośredniej jego bliskości zostanie utworzony w programie Visual Studio online, następuje przeglądu kodu Microsoft zawartości pakietu. Przegląd kodu opisano najlepsze wzorce/rozwiązania do użycia podczas tworzenia szablonów, porady i wskazówki do optymalizacji tworzenie zasobów. Jeśli opinii w wyniku wydawcy wprowadzania zmian w plikach, proces publikowania musi można uruchomić go ponownie. Bieżący publikowania zostanie anulowane i trzeba będzie ponownie opublikować za pomocą komentarze dotyczące opinii, które zostały rozwiązane.

### <a name="live"></a>Na żywo

Oferty jest teraz na żywo w witrynie Azure Marketplace i witryny Azure Portal. Usługi klienci będą mogli przeglądać i wdrażanie aplikacji zarządzanych platformy azure w swoich subskrypcjach platformy Azure. Możesz kliknąć kartę wszystkie oferty i wyświetlany jest stan dla wszystkich Twoich ofert wymienione w prawej kolumnie. Kliknij stan, aby zobaczyć stan przepływu publikowania szczegóły oferty.

### <a name="error-handling"></a>Obsługa błędów 

Jeśli wystąpi błąd, otrzymasz wiadomość e-mail z powiadomieniem informujące, że wystąpił błąd z instrukcjami dotyczącymi kolejnych kroków. Widać również błędy w dowolnym momencie w trakcie tego procesu, klikając kartę Stan. Zostanie wyświetlony, którego punktu w procesie, którego wystąpienia błędu oraz komunikat o błędzie konspektu, co musi zostać rozpoznane.

Jeśli wystąpią błędy w procesie publikowania jest wymagane, usuń je, a następnie kliknij pozycję \'Publikuj\' ponowne uruchomienie procesu. W przypadku ponownego publikowania po wszelkie poprawki błędów, należy uruchomić na początku kroki publikowania na weryfikacji wymagań wstępnych.

Jeśli występują problemy rozpoznawania błąd, należy otworzyć żądanie pomocy technicznej, aby uzyskać pomoc z inżynierami pomocy technicznej.

### <a name="canceling-the-publishing-request"></a>Anulowanie żądania publikowania

Możesz rozpocząć proces publikowania i potrzebują można anulować żądania. Żądaniu publikowania może anulować tylko, gdy żądanie opublikowania osiągnie kroku przygotowania wydawcy. Aby anulować, wybierz polecenie \'Anuluj publikowanie\'. Spowoduje to zresetowanie stanu publikowania do kroku 1, a następnie ponownie opublikować należy kliknąć przycisk Publikuj i postępuj zgodnie z instrukcjami w stanie.
