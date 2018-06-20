---
title: Wprowadzenie do usługi Microsoft Power BI Embedded | Microsoft Docs
description: Usługa Power BI Embedded w Twoich aplikacjach analizy biznesowej
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ms.assetid: 4787cf44-5d1c-4bc3-b3fd-bf396e5c1176
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 01/11/2018
ms.author: maghan
ms.openlocfilehash: 7a860742192227e92a14d9bb5622dc9b61f6cb99
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34716027"
---
# <a name="get-started-with-microsoft-power-bi-embedded"></a>Wprowadzenie do usługi Microsoft Power BI Embedded

Usługa **Power BI Embedded** pozwala niezależnym dostawcom oprogramowania (ISV, independent software vendor) i deweloperom szybko dodawać wspaniałe elementy wizualne, raporty i pulpity nawigacyjne do swoich aplikacji przy użyciu modelu opartego na pojemności i mierzonego godzinowo.

![Diagram przepływu osadzania](media/get-started/introduction.png)

Usługa Power BI Embedded zapewnia korzyści niezależnym dostawcom oprogramowania, ich deweloperom i klientom. Na przykład niezależny dostawca oprogramowania może bezpłatnie rozpocząć tworzenie wizualizacji za pomocą aplikacji Power BI Desktop. Niezależni dostawcy oprogramowania mogą osiągnąć krótszy czas wprowadzenia na rynek, minimalizując nakłady pracy na rozwój analizy wizualnej, i wyróżnić się wśród konkurencji za pomocą zróżnicowanych danych. Niezależni dostawcy oprogramowania mogą również naliczać premie za wartość dodatkową utworzoną za pomocą osadzonej analizy.

Deweloperzy mogą poświęcać czas na tworzenie podstawowych funkcji aplikacji, zamiast na tworzenie wizualizacji i analizy. Deweloperzy mogą szybko zaspokoić potrzeby klientów dotyczące raportów i pulpitów nawigacyjnych oraz mogą łatwo osadzać zawartość przy użyciu w pełni udokumentowanych interfejsów API i zestawów SDK. Wreszcie, włączając w swoich aplikacjach łatwą do nawigacji eksplorację danych, niezależni dostawcy oprogramowania pozwalają swoim klientom na podejmowanie szybkich, opartych na danych decyzji w określonym kontekście i bez obaw na dowolnym urządzeniu.

## <a name="register-an-application-within-azure-active-directory"></a>Rejestrowanie aplikacji w usłudze Azure Active Directory

Zarejestrowanie aplikacji w usłudze Azure Active Directory (AAD) jest wymagane w celu osadzenia w aplikacji niestandardowej. Zarejestrowana aplikacja wymaga, aby dzierżawa była dzierżawą usługi Power BI. Dzierżawa usługi Power BI oznacza, że co najmniej jeden użytkownik w organizacji utworzył konto w usłudze Power BI. Rejestracja użytkownika w usłudze Power BI umożliwia wyświetlanie interfejsów API tej usługi w zarejestrowanej aplikacji.

Aby uzyskać więcej informacji o tym, jak zarejestrować aplikację w usłudze AAD, zobacz [Register an Azure AD app to embed Power BI content (Rejestrowanie aplikacji usługi Azure AD w celu osadzania zawartości usługi Power BI)](https://powerbi.microsoft.com/documentation/powerbi-developer-register-app/).

## <a name="embed-content-in-your-application"></a>Osadzanie zawartości w aplikacji

Gdy masz już aplikację zarejestrowaną w usłudze AAD, osadź zawartość usługi Power BI w swojej aplikacji. Osadź zawartość przy użyciu interfejsu API REST oraz interfejsów API języka JavaScript.

Mamy przykłady, które pomogą Ci rozpocząć pracę. Aby zapoznać się z przewodnikiem przykładu, zobacz [Integrate a dashboard, tile, or report into your application (Integracja pulpitu nawigacyjnego, kafelka lub raportu z aplikacją)](https://powerbi.microsoft.com/documentation/powerbi-developer-embed-sample-app-owns-data/).

## <a name="get-capacity-and-move-to-production"></a>Tworzenie pojemności i przenoszenie do środowiska produkcyjnego

Utwórz pojemność usługi Power BI Embedded na platformie Microsoft Azure, aby przenieść swoją aplikację do środowiska produkcyjnego. Aby uzyskać informacje na temat sposobu tworzenia pojemności, zobacz [Create Power BI Embedded capacity in the Azure portal (Tworzenie pojemności usługi Power BI Embedded w witrynie Azure Portal)](create-capacity.md).

> [!IMPORTANT]
> Tokeny osadzania są przeznaczone tylko do testowania przez deweloperów, dlatego liczba tokenów osadzania, jakie może wygenerować główne konto usługi Power BI, jest ograniczona. Dla scenariuszy osadzania w środowisku produkcyjnym [należy kupić pojemność](https://docs.microsoft.com/power-bi/developer/embedded-faq#technical). Po zakupie dedykowanej pojemności nie ma ograniczenia liczby generowanych tokenów osadzania. Zobacz [Get Available Features (Uzyskiwanie dostępnych funkcji)](https://msdn.microsoft.com/en-us/library/mt846473.aspx), aby sprawdzić aktualne użycie osadzania w procentach.

Zarządzanie pojemnością w portalu administracyjnym usługi Power BI. Przypisz użytkownika administratora obszaru roboczego, aby uzyskać pomoc dotyczącą obszarów roboczych aplikacji. Aby uzyskać więcej informacji, zobacz [Manage capacities within Power BI Premium and Power BI Embedded (Zarządzanie pojemnościami w usługach Power BI Premium i Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-admin-premium-manage/).

## <a name="next-steps"></a>Następne kroki

Gdy wszystko będzie gotowe do utworzenia pojemności, zobacz [Create Power BI Embedded capacity in the Azure portal (Tworzenie pojemności usługi Power BI Embedded w witrynie Azure Portal)](create-capacity.md).

Jeśli szukasz przykładowego przewodnika, zobacz [Integrate a dashboard, tile, or report into your application (Integracja pulpitu nawigacyjnego, kafelka lub raportu z aplikacją)](https://powerbi.microsoft.com/documentation/powerbi-developer-embed-sample-app-owns-data/).

Masz więcej pytań? [Dołącz do społeczności użytkowników usługi Power BI](http://community.powerbi.com/)
