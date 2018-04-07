---
title: Analiza przypadku Azure - Snelstart bazy danych Azure SQL | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu SnelStart używa bazy danych SQL szybko rozszerzony jego usługi biznesowe w wysokości 1000 nowych bazami danych SQL Azure na miesiąc
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: reference
ms.topic: article
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: 55734dc0889ae6d1b03fab073586fa2fab32ec3c
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="with-azure-snelstart-has-rapidly-expanded-its-business-services-at-a-rate-of-1000-new-azure-sql-databases-per-month"></a>Azure SnelStart szybko rozwinął jego usługi biznesowe w wysokości 1000 nowych bazami danych SQL Azure na miesiąc
![SnelStartLogo](./media/sql-database-implementation-snelstart/snelstartlogo.png)

SnelStart sprawia, że popularnych finansowych - i business — oprogramowanie do zarządzania dla małych i średnich firm (SMB) (Holandia). 55,000 klientów są obsługiwane przez personel 110 pracowników, w tym personel działu informatycznego 35. Przenoszenie z oprogramowania dla komputerów stacjonarnych do oprogramowania — jako — usługa (SaaS) oferta oparta na platformie Azure, SnelStart wprowadzone większość usług wbudowanych automatyzacji zarządzania przy użyciu znanego środowiska w języku C# i optymalizacji wydajności i skalowalności przez ani wykorzystujących pule elastyczne firm over lub w obszarze-inicjowania obsługi. Za pomocą usługi Azure udostępnia SnelStart płynną przenoszenie klientów między firmą i chmurą.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-Case-Study-SnelStart/player]
> 
> 

## <a name="why-snelstart-extended-services-from-the-desktop-to-the-cloud"></a>Dlaczego SnelStart rozszerzony usług z pulpitu w chmurze
> "Pracy z platformą Azure oznacza, że firma Microsoft może dostarczać oprogramowanie szybsze, szybko reagowania na potrzeby klientów i skalować rozwiązania, gdy zapotrzebowanie."
> 
> — Henry został architekt oprogramowania
> 
> 

SnelStart została uruchomiona pomyślnie oprogramowania firmy lat, przy użyciu modelu tradycyjnych programowanie: kodu, pakiet wysyłki i powtórz. Wraz z upływem czasu tempo zmian rozwijały się szybciej i szybciej. Przepisy dotyczące często zmieniane, a klienci potrzebne łatwiejsze sposoby współpracy ich księgowym i dla instytucji rządowych, może obsługiwać te zmiany i przetwarzania dokumentów finansowych.

"Wysyłki oprogramowania do klientów jest kosztowne i ograniczającej," zgodnie z Henry zostały architekt oprogramowania. "Produkcji, opakowywanie i kosztów wysyłki ograniczone częstotliwość można wydać oprogramowanie. Musieliśmy pakiet aktualizacji dla wydania okresowe, ale który wprowadzone go trudne do zmieniających się potrzeb klientów. Firma Microsoft może nie mieć pewność, że naszym klientom uaktualniony do najnowszej wersji produktu. W związku z tym było do obsługi wielu wersji, utrudniając zadania obsługi bardzo oraz."

Zostały dodaje, "możemy sposobem program i wersji aktualizacji przyspieszonego tempie, dlatego firma Microsoft może innowacji szybciej i Utwórz nowe usługi dla klientów. Ponadto możemy sposobem zautomatyzowania więcej procesów w celu uproszczenia potrzeby biznesowe administracji klientów."

SnelStart rozwiązanie zostało rozszerzyć jego usług przez staje się dostawcę SaaS oparte na chmurze. Platforma Azure SQL Database pomogła SnelStart tam bez ponoszenia głównych obciążenie infrastruktury IT, które wymagałyby rozwiązania infrastruktury jako — usługa (IaaS).

Modelu chmury umożliwia również SnelStart błędów i udostępnia nowe funkcje szybko, bez konieczności pobierania i uaktualnić oprogramowanie klientów. Zgodnie z tym została "Używanie usług w chmurze Azure pozwala na szybkie działanie po zmianie wymagań od osób trzecich. Zamiast do wysłania nowej wersji do tysięcy klientów, możemy dostosować informacje wysyłane z naszej aplikacji pulpitu do nowych formatów wymagane przez osoby trzecie."

## <a name="a-modern-company-with-traditional-roots"></a>Nowoczesne firmy z tradycyjnego katalogów głównych
SnelStart jest to nowoczesny, elastyczne, zaawansowanych technologicznie firma mieszcząca skromny katalogów głównych uaktualniania do 1964 założycieli uruchomienia firmy producent części instrumentów muzycznych. Puls biznesowe oprogramowania SnelStart uruchomiona naprawdę bicie w lat 1980, podczas mnożenie komputerów osobistych. Firma potrzebne lepszym rozwiązaniem oprogramowania dostępne w czasie, więc zajęło sprawach w jego własnej ręce. W 1982 firma utworzony podstawę co ostatecznie staje się SnelStart oprogramowania. Od początku, oprogramowanie zostało admired na prostotę i szybkość — tak duża, aby firma po pewnym czasie zmienić fokus i reinvented się do producenta oprogramowania.

W 1995 r. SnelStart wydawane swoją pierwszą aplikację księgowości dla systemu Windows. Aplikacji, baz danych programu Microsoft Visual Basic 1.0 i programu Microsoft Access, w oparciu pomogła wzrostu klienta podstawowej użytkownikom więcej niż 5000.

Obecnie SnelStart jest głównych dostawcy — biznesowych (LOB) i aplikacji biznesowych administracji celem holenderski małych i średnich firmach i przedsiębiorcami samodzielnej. Jak Carlo Kuip, architektów IT, jest wyświetlany komunikat "naszym celem jest zapewnienie automatyzacji 100 procent usług biznesowych administracji dla naszych klientów."

## <a name="optimizing-performance-and-cost-with-elastic-pools"></a>Optymalizacja wydajności i kosztów z pul elastycznych
SnelStart został na dużą skalę "wczesnego uczestnictwa" elastyczne pule. Pule elastyczne pomóc firmie ograniczyć koszty i bardziej wydajnie zarządzać wymagania dotyczące wydajności. Zgodnie z tym została "przy użyciu pul elastycznych, firma Microsoft może zoptymalizować wydajność, w zależności od potrzeb klientów, bez przerostu. Jeśli było zainicjować oparte na obciążenia szczytowego, jest dość kosztowna. Zamiast tego opcja współużytkowanie zasobów między wieloma niskiego obciążenia baz danych pozwala utworzyć rozwiązanie, które wykonuje również i kosztach. "

## <a name="azure-sql-databases-help-containerize-data-for-isolation-and-security"></a>Bazy danych SQL Azure pomocy containerize danych izolacji i zabezpieczeń
Baza danych SQL Azure umożliwia SnelStart łatwo i w przezroczysty sposób przenoszenia klientów biznesowych administracji danych lokalnych do platformy Azure. Baza danych SQL Azure jest wygodne kontenerem, który zapewnia izolację, granicy do uwierzytelniania, autoryzacji i łatwe możliwości tworzenia kopii zapasowych i przywracania. Bazy danych podaj odpowiednie model koncepcyjny zarządzania. Zgodnie z Carlo Kuip, architektów IT "elementów w obrębie tej granicy kontenera zawierają dane poufne, które ma podstawowe znaczenie dla działalności, a przechowywanie tych elementów w bazie danych izolowanego jednocześnie dbając o ich ochrony. Firma Microsoft można zarządzać autoryzacji na poziomie bazy danych i nawet automatyzacji zarządzania i skalowalnego w poziomie z tych baz danych bez konieczności administratorów bazy danych (DBAs) na personelu."

Usługa Azure SQL Data Warehouse także pełni rolę w wątku SnelStart zabezpieczeń i zarządzania przez firmowej zebrać dane telemetryczne, np. wykrywania nieautoryzowanego dostępu, rejestrowanie aktywności użytkownika i łączności.

## <a name="azure-removes-overhead-so-that-developers-can-spend-more-time-delivering-value"></a>Azure usuwa nakładów pracy, dzięki czemu deweloperzy mogą poświęcić więcej czasu na dostarczanie wartości
Model platformy Azure usunięte koszty infrastruktury i włączone SnelStart do automatyzowania wdrożeń przy użyciu biblioteki zarządzania C#. Jako Kuip stany, "udało nam się rozrastać nasze bieżące operacje z bardzo małych personelu podczas równocześnie, poprawiając skalowalność, szybkość i po awarii opcji odzyskiwania dla klientów. Shift do rozwoju usług zwalniane zasobów, aby skupić się na nowej usługi i funkcje, zamiast tylko aktualizowanie istniejącego kodu do nadąża z przepisami nowych lub kodów." Dodaje, "przez"automatyzacji zarządzania i przy użyciu SaaS oferty, można dostarczyć więcej wartości dla klientów bez konieczności inwestowania dużych personel operacyjny. Na przykład przy użyciu platformy Azure i elastyczne pule, SnelStart mógł szereg nowych funkcji, tym bardziej niezawodne integracji danych klienta w bankach, Dodaj nowe rozliczeń usługi, kontrole małych firm i usługi poczty e-mail.

> "W tylko pierwszych kilku miesięcy 2016, możemy rozwinięty naszych wdrożenia bazy danych SQL Azure z około 5,500 ponad 12 000, a obecnie trwa dodawanie około 1000 baz danych na miesiąc".
> 
> — Henry został architekt oprogramowania
> 
> 

Zarządzanie bazą danych jest dalsze zautomatyzowanej przy użyciu funkcji zadania elastyczne. Jako Kuip stany, "bardzo cenimy automatycznego wykrywania baz danych w wystąpieniu bazy danych SQL [server]." Dzięki temu SnelStart do wykonywania operacji zarządzania w ich klientów dynamicznie rosnącym bez dodatkowych kosztów związanych.

SnelStart jest również tworzenie interfejsu API, który działa jako broker między danych klienta i aplikacje przez partnerów oprogramowania innych firm. Jak Państwa Kuip "tego interfejsu API umożliwi innych dostawców dodać funkcje do naszego oprogramowania, takie jak wyeliminowanie wprowadzenie danych faktury i inne dokumenty." Klienci nie będą musieli ręcznie wpisać faktury do ich małych firm oprogramowania; oprogramowanie SnelStart wymieniają niezbędne informacje bezpośrednio. Dzięki temu klienci mogą dołączyć ich zadań zarządzania — z ekosystemem informacje, które jest wychodzące z transformacji cyfrowych w branży.  

## <a name="how-azure-services-enable-saas-for-snelstart"></a>W jaki sposób usługi Azure umożliwiają SaaS dla SnelStart
Za pomocą usługi Azure, SnelStart może obsługiwać klientów i ich księgowym więcej bezproblemowo w chmurze. Na przykład klientów i księgowym udostępniać informacje innym uzyskując bezpośrednio dostęp do interfejsu API klienta dla SnelStart, hostowanej na platformie Azure. Kuip Stany "wielokrotnego użytku usługi są dostępne do naszej aplikacji sieci web skierowane do klienta oraz zapewniają wspólnej infrastruktury i funkcje, aby zezwolić na dostęp do zarządzania dla klientów i oprogramowania innych firm używane przez klientów. Przykładami zapewnia możliwości konfiguracji produktów, zarządzanie reguły zapory i zarządzanie procesy długotrwałe, takich jak tworzenie kopii zapasowych."

> Naszym celem jest zapewnienie automatyzacji 100 procent usług biznesowych administracji dla naszych klientów." 
> 
> — Carlo Kuip, architektów IT
> 
> 

Ponadto usługi sieci web SnelStart pozwalają zarówno klientów i księgowym łatwo uzyskiwać dostęp do danych w puli elastycznej bazy danych SQL Azure. Ten model SaaS, w połączeniu z bazy danych, elastyczność i usługi Azure Resource Manager zapewnia SnelStart funkcje skalowalności, które uzupełniają każdego wdrożenia usługi Azure. Implementacja jest pełni zautomatyzowany przy użyciu biblioteki zarządzania C#.

![Architektura SnelStart](./media/sql-database-implementation-snelstart/figure1.png)

Rysunek 1. Począwszy od czerwca 2016 SnelStart ma więcej niż 11 000 baz danych i pul elastycznych więcej niż 50

## <a name="simplicity-from-the-cloud"></a>Prostota z chmury
Ponieważ przechodzenia do usługi Azure rozwiązania opartego na chmurze, SnelStart udało umożliwia szybkie klienta podczas oferty usługi i innowacyjnych funkcji. Zgodnie z tym została "z platformy Azure, udostępnienia niemalże ciągłe aktualizacje dla naszych klientów bez rozwijania pracownikami operacji. Firma Microsoft i wszystkie inne doskonałe funkcje platformy Azure — takie jak skalowalność i odzyskiwanie po awarii — powiązane w. "

> "Kiedy możemy faktycznie za pośrednictwem w Redmond... Zwrócona wywołanie dewelopera w Holandia chciałaby mnie dotyczące konkretnego problemu. Udało się pobrać firmy Microsoft, aby dostarczyć zmiany w środowisku produkcyjnym w ciągu 48 godzin, aby rozwiązać problem naszych."
> 
> — Henry został architekt oprogramowania
> 
> 

SnelStart ocenia również silne powiązanie, które zostały one opracowany z zespołem bazy danych SQL Azure firmy Microsoft. Jak Państwa Kuip, "mamy dyskusjach na temat funkcji i sposobu korzystania z technologii, która jest coś docenia po obu stronach".
Natychmiastowe dla SnelStart ma na celu rosnące spełnione klienta podstawowej. Jak zostało Stany "Bez ograniczeń technicznych i zasobów, które było jako niezależnego dostawcy oprogramowania, nie ma żadnego limitu na jakim firma Microsoft mogła się rozrastać."

## <a name="more-information"></a>Więcej informacji
* Aby uzyskać więcej informacji na temat usługi Azure pule elastyczne, zobacz [pule elastyczne](sql-database-elastic-pool.md).
* Aby dowiedzieć się więcej na temat usługi Azure SQL Data Warehouse, zobacz [SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/)
* Aby dowiedzieć się więcej na temat SnelStart, zobacz [SnelStart](http://www.snelstart.nl).

