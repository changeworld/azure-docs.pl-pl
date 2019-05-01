---
title: Azure konfiguracji dysku testu oferta SaaS aplikacji | Portal Azure Marketplace
description: Konfigurowanie wersji testowej w celu skorzystania z oferty aplikacji SaaS w portalu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: c76290d6c96108ff04799151c82334264a4c3dd0
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64941718"
---
# <a name="saas-application-test-drive-tab"></a>Karta wersję testową aplikacji SaaS

Karta wersji testowej zapewniając wersji próbnej środowisko dla swoich klientów.

## <a name="test-drive-benefits"></a>Testowanie korzyści ze stosowania dysków

Tworzenie wersji próbnej środowisko dla swoich klientów jest najlepszym rozwiązaniem, aby upewnić się, że mogą kupić bez obaw. Z dostępnych opcji wersji próbnej wersji testowej jest najbardziej efektywne, wysokiej jakości generowania potencjalnych klientów i zwiększonej konwersji tych potencjalnych klientów.

Wersja testowa dostarcza klientom korzystającym z wersją próbną praktyczne, przeprowadzanemu samodzielnie procesowi kluczowe funkcje i korzyści, przedstawione w scenariuszu wdrożenia rzeczywistych swojego produktu.


## <a name="how-a-test-drive-works"></a>Jak działa wersji testowej

Potencjalny klient wyszukuje i umożliwia odnalezienie aplikacji w witrynie Marketplace. Klient rejestruje i wyraża zgodę na warunki użytkowania. W tym momencie użytkownik otrzymał wstępnie skonfigurowanego środowiska do wypróbowania funkcji przez określoną liczbę godzin, gdy otrzymasz wysoko wykwalifikowanych potencjalnego klienta do monitowanie. Aby uzyskać więcej informacji, zobacz [co to jest wersja testowa?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)


## <a name="publishing-steps"></a>Kroki publikowania

Główne kroki publikowania, aby dodać test dysku są:

1. Zdefiniuj Twojemu scenariuszowi wersji testowej
2. Tworzenie i/lub zmodyfikowanie szablonu usługi Resource Manager
3. Utwórz szczegółowe podręcznika użytkownika wersji testowej
4. Ponownie opublikować ofertę


## <a name="setting-up-a-test-drive"></a>Konfigurowanie wersji testowej

Istnieją cztery typy wersji testowych, każdy na podstawie typu produktu, scenariusza i portalu marketplace, którą pracujesz.

|  **Typ**          |  **Opis**  |  **Instrukcje instalacji**  |
|  ---------------   |  ---------------  |  ---------------  |
|     Azure Resource Manager               |    Usługi Azure Resource Manager testowej jest szablon wdrożenia, który zawiera wszystkie zasoby platformy Azure, wchodzące w skład rozwiązania tworzona przez wydawcę. Produkty, które Dopasuj ten typ wersji testowej są te, które korzystać tylko zasobów platformy Azure.               |       [Wersja testowa usługi Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)            |
|       Hostowany             |       Testowej hostowanych eliminuje złożoność konfiguracji w ramach hostingu firmy Microsoft i obsługa usługi przeprowadzające użytkownika wersji testowej aprowizacji i cofania aprowizacji.             |         [Hostowana wersja testowa](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/hosted-test-drive)          |
|      Aplikacja logiki              |       Wersję testową aplikacji logiki jest szablon wdrożenia, który jest przeznaczony do uwzględniający wszystkie architektury złożonych rozwiązań. Wszystkie aplikacje Dynamics lub niestandardowych produktów należy używać tego rodzaju wersji testowej.            |      [Wersja testowa aplikacji logiki](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)             |
|       Power BI             |         Dysk Test usługi Power BI składa się z osadzonych łącze do niestandardowej pulpitu nawigacyjnego. Produkt, który chce pokazują, że Interaktywne wizualizacje usługi Power BI należy używać tego rodzaju wersji testowej. Wszystko, czego potrzebujesz do przekazania jest osadzony adres URL do usługi Power BI.          |        [Power BI Test Drive](#power-bi-test-drive)           |
|   |   |   |


### <a name="power-bi-test-drive"></a>Wersja testowa programu Power BI

Poniższe kroki umożliwiają konfigurowanie wersji testowej.

1. Nowa oferta wybierz **wersji testowej**.
2. W wersji testowej wybierz **tak**.

   ![Włączanie wersji testowej](./media/saas-enable-test-drive.png)

   Po włączeniu wersji testowej, zostaną wyświetlone szczegółowe informacje i Pomoc konfiguracji formularze, które są wyświetlane na następnym zrzucie ekranu.

   ![Formularz konfiguracji dysku testu](./media/saas-test-drive-yes.png)

3. W obszarze **szczegóły**, podaj informacje dla następujących pól:
  
   - Opis — opis wersji testowej i które mogą wykonywać użytkownicy z nim. Aby sformatować ten opis, można użyć podstawowa tagów HTML.
   - Podręcznik użytkownika — przekazywanie dokumentu Podręcznik użytkownika, który klientów można użyć, gdy są one przełącza wersji testowej. Niniejszy podręcznik musi być plikiem pdf.
   - Test Drive pokaz wideo (opcjonalnie) — możesz podać wideo (działanie serwisu YouTube lub Vimeo) dla swoich klientów obejrzeć przed wprowadzeniem wersji testowej. Podaj adres URL pliku wideo.

4. W obszarze **konfiguracji technicznej**, podaj informacje dla następujących pól:

   - Typ wersji testowej — wybierz **usługi Power BI** z listy rozwijanej.
   - Link do pulpitu nawigacyjnego usługi Power BI — Podaj link do pulpitu nawigacyjnego.

5. Aby zakończyć konfigurowanie wersji testowej, wybierz **Zapisz**.


## <a name="next-steps"></a>Kolejne kroki

[Karta szczegółów sklepu](./cpp-storefront-tab.md)
