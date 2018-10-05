---
title: Utworzyć maszynę wirtualną oferty na żywo w witrynie Azure Marketplace
description: Utworzyć maszynę wirtualną oferty na żywo w witrynie Azure Marketplace
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: qianw211
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: ff3ca665382efc9133a4c4ce3f3fde25c03c5e52
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48810961"
---
<a name="make-your-virtual-machine-offer-live-on-azure-marketplace"></a>Utworzyć maszynę wirtualną oferty na żywo w witrynie Azure Marketplace
=========================================================

Po wypełnieniu wszystkich szczegółów oferty nadszedł czas, aby opublikować ofertę i udostępnić go na żywo w witrynie Azure Marketplace. Istnieje kilka etapów, które przechodzi oferty. Upewnij się, spełniają wymagania jakości, aby się z certyfikatem platformy Azure i emisji na żywo w witrynie internetowej obrazu maszyny Wirtualnej i zawartości marketingowej.

![Przejdź na żywo sekwencją 0 oferty](./media/cloud-partner-portal-offer-go-live-azure-marketplace/makeanofferlive.png)

Przejdźmy przez ten proces w bardziej szczegółowo, aby lepiej zrozumieć, co się dzieje w każdym kroku. W ramach tego procesu należy do działania w celu zapewnienia, że oferty w dalszym ciągu postępu.

<a name="publishing-process"></a>Proces publikowania
------------------

Na karcie edytora, aby rozpocząć proces publikowania, kliknij przycisk "Publikuj".

![Publikowanie oferty przejdź na żywo sekwencji 1 -](./media/cloud-partner-portal-offer-go-live-azure-marketplace/publish.png)

Na karcie Stan zobaczysz kroki publikowania, gdzie oferty jest w trakcie procesu.

![Oferty z rzeczywistym użyciem sekwencji na żywo 2 — stan](./media/cloud-partner-portal-offer-go-live-azure-marketplace/status.png)

W dowolnym momencie proces publikowania możesz zarejestrować się w i kliknij kartę oferuje wszystkie, aby wyświetlić najnowszy stan dla wszystkich Twoich ofert. Można kliknąć bezpośrednio na stan oferty i wyświetlić szczegółowe informacje, na którym jest oferty w procesie publikowania.

![Oferty z rzeczywistym użyciem na żywo sekwencji 3 — oferuje stanu](./media/cloud-partner-portal-offer-go-live-azure-marketplace/alloffersstatus.png)

Przejdźmy teraz przez poszczególne kroki publikowania, omówić, co się dzieje w każdym kroku i jak długo należy oszacować każdego kroku spowoduje przejście.

**Zweryfikuj wymagania wstępne (\<1 dzień)**

Gdy klikniesz pozycję "Publikuj", zautomatyzowane kontrole ma miejsce, aby upewnić się, że wszystkie wymagane pola zostały wypełnione na ofertę. Jeśli wszystkie pola są wypełniane, obok pola zostanie wyświetlone ostrzeżenie i należy wypełnić ją dokładnie następnie ponownie kliknij przycisk "Publikuj".

Po zakończeniu tego kroku poprawnie wyświetlone wyskakujące okienko, pytaniem o adres e-mail. To jest wiadomość e-mail, do którego zostanie wyświetlony publikowania powiadomienia o stanie dla pozostałych procesu publikowania. Po przesłaniu adresu e-mail, ten krok jest pełny.

![Oferta przejdź na żywo sekwencji 4 — publikowanie oferty](./media/cloud-partner-portal-offer-go-live-azure-marketplace/publishyouroffer.png)

**Certyfikacja (\<5 dni)**

Ten krok jest, gdzie możemy uruchomić wiele testów, aby upewnić się, że obraz maszyny Wirtualnej spełnia wymagania dotyczące certyfikatu platformy Azure. Wszystkie wskazówki, musisz upewnić się, Przekaż wymagania dotyczące certyfikacji są [tutaj](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-prerequisites).

Ponieważ może to potrwać kilka dni, możesz zarejestrować się poza portalu Cloud Partner. Firma Microsoft wyśle Ci wiadomość e-mail z powiadomieniem Jeśli występują błędy, które należy spełnić. Jeśli testy zakończą się pomyślnie z sukcesem, proces zostanie automatycznie przejść do kroku inicjowania obsługi.

**Inicjowanie obsługi administracyjnej (\<1 dzień)**

Na tym etapie firma Microsoft jest replikowana obrazów do wszystkich centrów danych platformy Azure i może potrwać dzień do ukończenia.

**Tworzenie pakietów i spowodować generowanie rejestracji (\<1 godzina)**

Na tym etapie firma Microsoft na to, co będzie stronę produktu w witrynie internetowej łączenia zawartości techniczne i marketingowe.

Ponadto skonfigurowanie funkcji generowania prowadzić sprawdzamy, czy czy integracji usługi CRM działa, wysyłając testu potencjalnego klienta do CRM. Zostanie wyświetlony rekord o dane fikcyjne wypełnić CRM lub Azure Table po ukończeniu tego kroku. Znajduje się całą dokumentację prowadzić generacji [tutaj](./cloud-partner-portal-get-customer-leads.md).

**Ta oferta jest dostępna w wersji zapoznawczej**

Otrzymasz wiadomość e-mail z powiadomieniem, że Twoja oferta została ukończona pomyślnie kroki wymagane do dostępu do oferty w wersji zapoznawczej. W tym kroku należy wyświetlenia podglądu oferty i upewnij się, że wszystko wygląda jak należy go i maszyny Wirtualnej prawidłowo służy do wdrażania w środowisku przejściowym.

**Tylko na liście dozwolonych subskrypcje można wykonać tej weryfikacji.**\*

**Przygotowania wydawcy**

Po upewnieniu się, wszystko wygląda poprawnie i działa poprawnie, w wersji zapoznawczej, można przystąpić do emisji na żywo. Aby to zrobić, kliknij Go Live w obszarze stanu kartę, a firma Microsoft rozpocznie się czynności, aby utworzyć ofertę na żywo, w środowisku produkcyjnym i w witrynie sieci Web. Zazwyczaj potrwa kilka godzin od momentu, możesz kliknąć Go Live i oferta jest dostępna na żywo w witrynie sieci Web. Wyślemy wiadomość e-mail z powiadomieniem po oferty oficjalnie na żywo w witrynie sieci Web.

![Oferty z rzeczywistym użyciem na żywo sekwencji 5 — przejdź na żywo](./media/cloud-partner-portal-offer-go-live-azure-marketplace/golive.png)

**Na żywo**

Oferty jest teraz na żywo w witrynie Azure Marketplace i witryny Azure Portal, a klienci będą mogli przeglądać i wdrożyć maszynę wirtualną dla swoich subskrypcji platformy Azure. W dowolnym momencie można kliknąć kartę wszystkie oferty i wyświetlany jest stan dla wszystkich Twoich ofert wymienione w prawej kolumnie. Kliknij stan, aby zobaczyć stan przepływu publikowania szczegóły oferty.

<a name="error-handling"></a>Obsługa błędów
--------------

W procesie publikowania może napotkać błąd. Jeśli okaże się błąd, otrzymasz wiadomość e-mail z powiadomieniem informujące o tym, że wystąpił błąd z instrukcjami dotyczącymi kolejnych kroków. Widać również błędy w dowolnym momencie w trakcie tego procesu, klikając kartę Stan. Zostanie wyświetlony, którego punktu w procesie, którego wystąpienia błędu oraz komunikat o błędzie konspektu, co musi zostać rozpoznane.

![Oferty z rzeczywistym użyciem sekwencji na żywo 6 - komunikat o błędzie](./media/cloud-partner-portal-offer-go-live-azure-marketplace/errormessage.png)

Jeśli wystąpią błędy w procesie publikowania należy są wymagane do rozwiąż je, a następnie kliknij przycisk Publikuj, aby ponownie uruchomić proces. W przypadku ponownego publikowania po wszelkie poprawki błędów, należy uruchomić na początku kroki publikowania na weryfikacji wymagań wstępnych.

Jeśli występują problemy rozpoznawania błąd, należy otworzyć żądanie pomocy technicznej, aby uzyskać pomoc z inżynierami pomocy technicznej.

![Oferty z rzeczywistym użyciem na żywo sekwencji 7 — Uzyskaj pomoc techniczną](./media/cloud-partner-portal-offer-go-live-azure-marketplace/getsupport.png)

<a name="canceling-the-publishing-request"></a>Anulowanie żądania publikowania
--------------------------------

Możesz rozpocząć proces publikowania i potrzebują można anulować żądania. Żądaniu publikowania może anulować tylko, gdy żądanie opublikowania osiągnie krok podpisywania wydawcy. Aby anulować, kliknij Anuluj publikowanie. Spowoduje to zresetowanie stanu publikowania do kroku 1, a następnie ponownie opublikować należy kliknąć przycisk Publikuj i postępuj zgodnie z instrukcjami w stanie.

![Oferty z rzeczywistym użyciem sekwencji na żywo 8 — stan](./media/cloud-partner-portal-offer-go-live-azure-marketplace/status5.png)

