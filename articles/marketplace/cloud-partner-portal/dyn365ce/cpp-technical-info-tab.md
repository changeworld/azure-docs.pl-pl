---
title: Dynamics 365 dla karty informacje techniczne zaangażowania klientów | Portal Azure Marketplace
description: Jak określić informacje techniczne dotyczące Dynamics 365 Customer Engagement aplikacji w witrynie Marketplace usługi AppSource.
services: Dynamics 365 for Customer Engagement Offer, Azure, Marketplace, Cloud Partner Portal, AppSource
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: df7f3be5a92a183176da7851ce1943793b8b57d0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64942391"
---
# <a name="dynamics-365-for-customer-engagement-technical-info-tab"></a>Dynamics 365 dla karty informacje techniczne zaangażowania klientów

**Informacje techniczne** karcie **nowa oferta** strona pozwala na określenie szczegółowe informacje na temat usługi Dynamics 365 zaangażowania użytkowników aplikacji, w tym zasoby logo pakietu i marketing CRM.  Ta karta jest podzielony na cztery sekcje: **Informacje o aplikacji**, **pakietu CRM**, **dostępność pakietu CRM**, i **marketingowych artefaktów**. Dołączonych gwiazdki (*) na nazwę pola wskazuje, że jest wymagane. 


## <a name="application-info-section"></a>Sekcja informacji o aplikacji

Należy podać szczegóły dotyczące aplikacji Dynamics 365 w tej sekcji.

![Karta informacje techniczne sekcji informacji o aplikacji](./media/dynce-technical-info-tab1.png)

W poniższej tabeli opisano te pola. Wymagane pola są wskazanych przez znak gwiazdki (*).

|      Pole                    |    Opis                  |
|    ---------                  |  ---------------                |
|   Model licencji podstawowej\*          |  Model licencji określa, jak klienci są przypisywane aplikacji w Centrum administracyjnym usługi Dynamics 365. **Zasób** licencjonowania jest oparta na wystąpieniach, natomiast **użytkownika** licencje są przypisane w jednej dzierżawie.  |
|  Wychodzące S2S & bezpiecznego dostępu Store CRM\* |  Konfiguracja umożliwia dostęp ruchu wychodzącego między serwerami (S2S) lub Store bezpiecznego CRM. *Ta funkcja wymaga specjalne zagadnienia od zespołu Dynamics 365 w fazie certyfikacji.* Firmy Microsoft z Tobą, aby wykonać dodatkowe kroki, aby obsługiwać tę funkcję.  |
| Subskrybowanie do zdarzeń cyklu życia aplikacji CRM\* | Integracja z usługą zdarzenia cyklu życia usługi Dynamics 365 wymaga podania dedykowana usługa, która jest zarejestrowana za pomocą specjalnych umowy z firmą Microsoft. *Ta funkcja wymaga specjalne zagadnienia od zespołu Dynamics 365 w fazie certyfikacji.* Skontaktuje się z Tobą można wykonać dodatkowe kroki Obsługa tej możliwości.  |
| Adres Url konfiguracji aplikacji | Adres URL strony sieci web, która umożliwia użytkownikowi konfigurowanie aplikacji |
| Objęte produkty Dynamics 365  | Wybierz produkty Dynamics 365, których dotyczy ta oferta. Ta oferta będzie wyświetlany w obszarze wybrane produkty w usłudze AppSource.  |
| Marketing tylko zmiany         | Ustawienie tej opcji na Tak oznacza, że tylko marketing/opisowy zostały zmienione na istniejące oferty.  Takie zmiany umożliwiają oferty pominąć etapów inicjowania obsługi administracyjnej i certyfikacji.  |
|  |  |


## <a name="crm-package-section"></a>Sekcja pakietu CRM

Należy podać szczegółowe informacje o pliku pakietu usługi AppSource w tej sekcji.  Te informacje zostaną użyte przez zespoły sprawdzania poprawności i certyfikacji Dynamics 365.

![Pakiet aplikacji CRM części karta informacje techniczne](./media/dynce-technical-info-tab2.png)

W poniższej tabeli opisano te pola.  Wymagane pola są wskazanych przez znak gwiazdki (*).

|      Pole                    |    Opis                  |
|    ---------                  |  ---------------                |
|  Nazwa pliku pakietu\*     |  Nazwa pliku pakietu (zip).  Ta nazwa jest *nie* publicznych i będą używane wewnętrznie przez zespół certyfikacji Dynamics 365.  |
|  Adres URL lokalizacji pakietu\*      |  Adres URL konta usługi Azure Storage, który zawiera plik przesłanym pakiecie. Ten adres URL powinien zawierać tylko do odczytu klucza sygnatury dostępu Współdzielonego umożliwia nasz zespół pobrać pakiet do weryfikacji.  |
| Więcej niż jeden pakiet aplikacji crm\*     | Wybierz tak tylko, jeśli są obsługiwanie wielu wersji programu crm przy użyciu różnych pakietach.  Każda wersja ma odpowiedni umożliwiający pliku pakietu, które należy utworzyć osobno.  |
| Scenariusz i użycie zasobów wielkości liter\*   | Umożliwia przekazywanie dokumentu specyfikacji funkcjonalności aplikacji, do użytku przez zespół weryfikacyjny Dynamics 365.  Jest w formacie ten spec [szablon scenariusza użytkownika E2E](https://isvdocumentation.blob.core.windows.net/d365documentation/Power%20Platform%20E2E%20document.docx).  |
|  |  |


## <a name="crm-package-availability-section"></a>W sekcji dostępność pakietu CRM

W tej sekcji należy wybrać, które regiony geograficzne Twoja aplikacja będzie dostępna dla klientów.  Wdrażanie w następujących regionach suwerennych *wymagają specjalnych uprawnień i sprawdzanie poprawności* w procesie certyfikacji: [Niemcy](https://docs.microsoft.com/azure/germany/), [chmura dla instytucji rządowych USA](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)oraz ETYKIETEK.


## <a name="marketing-artifacts-section"></a>Sekcja artefaktów marketingu

W tej sekcji wymaga Przekaż logo aplikacji, która będzie służyć do reprezentowania pakietu w portalu Marketplace usługi AppSource.  Obraz logo musi być w formacie PNG i mieć rozmiar 255 x 115 pikseli.


## <a name="next-steps"></a>Kolejne kroki

Firma Microsoft zaleca, oferują demonstracyjnej aplikacji, wykonując [kartę wersji testowej](./cpp-testdrive-tab.md)
