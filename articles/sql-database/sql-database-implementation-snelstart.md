---
title: Usługi Azure SQL Database platformy Azure analizą przypadku firmy — firma Snelstart | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o jak firma SnelStart używa bazy danych SQL, aby szybko rozwinęła swoje usługi biznesowe w wysokości 1000 nowych baz danych Azure SQL na miesiąc
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: reference
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: c919a3cb47017d2f65b141e358ab318f4b764627
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "44713718"
---
# <a name="with-azure-snelstart-has-rapidly-expanded-its-business-services-at-a-rate-of-1000-new-azure-sql-databases-per-month"></a>Dzięki systemowi Azure firma SnelStart szybko ma rozwinęła swoje usługi biznesowe w wysokości 1000 nowych baz danych Azure SQL na miesiąc
![SnelStartLogo](./media/sql-database-implementation-snelstart/snelstartlogo.png)

Firma SnelStart sprawia, że popularnych — i biznesowych — oprogramowanie do zarządzania finansami dla małych i średnich firm (SMB) w holenderskiej. 55,000 klientów są obsługiwane przez personel 110 pracowników, w tym personel IT 35. Przenosząc z oprogramowania dla komputerów stacjonarnych z ofertą software-as-a-service (SaaS), oparta na platformie Azure, firma SnelStart wprowadzone w pełni korzystać z wbudowanych usług automatyzację zarządzania przy użyciu znanego środowiska języka C# i optymalizacji wydajności i skalowalności, ani over - lub w obszarze aprowizacji firmy wykorzystujących pule elastyczne. Korzystanie z platformy Azure zapewnia firma SnelStart dziesięciokrotnie przenoszenie klientów między w środowisku lokalnym i chmurą.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-Case-Study-SnelStart/player]
> 
> 

## <a name="why-snelstart-extended-services-from-the-desktop-to-the-cloud"></a>Dlaczego firma SnelStart rozszerzone usług z pulpitu w chmurze
> "Korzystając z platformy Azure oznacza, że firma Microsoft może szybciej dostarczać oprogramowanie, szybko reagować na żądania klientów i skalować rozwiązania w miarę wzrostu wymagań."
> 
> — Henry Been, architekt oprogramowania
> 
> 

Firma SnelStart uruchomiono branży oprogramowania pomyślnie przez wiele lat, za pomocą modelu programowania tradycyjnych: kodu, pakowanie, dostarczanie i powtórz. Wraz z upływem czasu, tempo zmian rozwijały się szybciej i szybciej. Przepisy często zmieniane, a klienci potrzebne łatwiejsze do współpracy z ich accountants i dla instytucji rządowych, na bieżąco z tymi zmianami i przetwarzania dokumentów finansowych.

"Oprogramowanie wysyłanie do klientów jest kosztowne i ograniczające," zgodnie z Henry Been, architekt oprogramowania. "Produkcji, pakowania i koszty wysyłki ograniczone częstotliwość kolejna wersja oprogramowania. Mieliśmy pakiet aktualizacji dla okresowych wydań, ale które pozwoliło trudne do zaspokojenia zmieniających się potrzeb naszych klientów. Firma Microsoft może nigdy nie mieć pewność, że naszych klientów uaktualniony do najnowszej wersji produktu. Dlatego musimy obsługę wielu wersji, utrudniając zadania pomocy technicznej bardzo dobrze."

Został dodaje, "Chcieliśmy sposób aktualizacji program i wersji w jej jako przyspieszonej tempie, dzięki czemu można szybciej wprowadzaj innowacje i tworzenia nowych usług dla naszych klientów. Chcieliśmy również sposób na Automatyzowanie więcej procesów w celu uproszczenia potrzeb biznesowych administracji naszych klientów."

Firma SnelStart rozwiązanie była rozszerzyć jej usług, deklarując dostawcę SaaS oparte na chmurze. Platforma Azure SQL Database pomogła firma SnelStart dostać się tam bez powodowania główne obciążenie IT, które wymagałyby rozwiązanie infrastructure-as-a-service (IaaS).

Model chmury umożliwia także firma SnelStart usuwanie usterek i udostępnia nowe funkcje szybko, bez konieczności pobierania ani uaktualnienie oprogramowania klientów. Zgodnie z opisem w Been, "użycie usługi Azure cloud services pozwala na szybkie operować na zmieniające się wymagania dotyczące innych firm. Zamiast do wysłania nowej wersji do tysięcy klientów, firma Microsoft może dostosowywać informacje wysyłane z naszych aplikacja komputerowa nowe formaty wymagane przez osoby trzecie."

## <a name="a-modern-company-with-traditional-roots"></a>Nowoczesne firmy przy użyciu tradycyjnych elementy główne
Firma SnelStart jest nowoczesny, agile i zautomatyzowanych firm z humble korzenie sięga do 1964, twórcami rozpoczęcia firmy jako producent części dokumentu w kompozycją. Serce branży oprogramowania firma SnelStart pracę naprawdę kucie w lat 1980, podczas mnożenia komputerów osobistych. Firma potrzebne jest lepszym rozwiązaniem dostępnym w momencie, oprogramowanie księgowe tak, jaki zajęło sprawy w swoje własne ręce. W 1982 firma tworzone podstawę co po pewnym czasie stanie się firma SnelStart oprogramowania. Od samego początku oprogramowanie zostało admired dla jego prostotą i szybkością — bardzo tak, aby firmy po pewnym czasie zmienić fokus i zbudowany od nowa samego do firmy zajmującej się oprogramowaniem.

W przypadku 1995 r. firma SnelStart wydawane swoją pierwszą aplikację księgowych dla Windows. Aplikacja oparta na bazy danych programu Microsoft Visual Basic 1.0 i Microsoft Access, brały udział w rozwoju klient bazowy do ponad 5000 użytkownikami.

Obecnie firma SnelStart jest główne dostawcy line-of-business (LOB) i aplikacji biznesowych — Administracja ukierunkowane na Dutch małych i średnich firmach i przedsiębiorców na własny rachunek. Jak wynika z Carlo Kuip, architektów IT, "naszym celem jest zapewnienie automatyzacji 100 procent firm Administracja usług dla naszych klientów."

## <a name="optimizing-performance-and-cost-with-elastic-pools"></a>Optymalizowanie wydajności i kosztów dzięki pulom elastycznym
Firma SnelStart był na dużą skalę wczesnego nabywcy pul elastycznych. Pule elastyczne, dzięki którym firma może ograniczyć koszty i efektywniej zarządzać wymaganiami dotyczącymi wydajności. Zgodnie z opisem w Been, "za pomocą elastycznych pul, firma Microsoft może zoptymalizować wydajność odpowiednio do potrzeb naszych klientów bez konieczności nadmiernej aprowizacji. Gdyby do aprowizowania na podstawie szczytowego obciążenia, byłoby bardzo kosztowne. Zamiast tego możliwość udostępniania zasobów między wieloma niskiego obciążenia baz danych pozwala utworzyć rozwiązanie, które wykonuje się dobrze i jest rozwiązaniem tanim. "

## <a name="azure-sql-databases-help-containerize-data-for-isolation-and-security"></a>Baz danych SQL platformy Azure pomagają konteneryzowanie danych w celu zapewnienia izolacji i zabezpieczeń
Usługa Azure SQL Database umożliwia firma SnelStart można łatwo i w sposób niewidoczny dla użytkownika należy przenieść klientów lokalnych firm administracji danych na platformie Azure. Azure SQL Database to wygodne kontener, który zapewnia izolację, granicy do uwierzytelniania, autoryzacji i łatwe możliwości tworzenia kopii zapasowych i przywracania. Bazy danych podaj odpowiednie modelu koncepcyjnego dla zarządzania. Zgodnie z Carlo Kuip, architektów IT "elementy w ramach tego kontenera zawierają dane poufne, które ma kluczowe znaczenie dla działalności i przechowywanie tych elementów w izolowanej bazy danych jednocześnie dbając o ich dobrze chronione. Firma Microsoft może zarządzać autoryzacją na poziomie bazy danych i nawet Automatyzowanie zarządzania i skalowalnego w poziomie z tych baz danych bez konieczności administratorów baz danych (przetwarzający) na pracowników".

Usługa Azure SQL Data Warehouse również odgrywa rolę w wątku zabezpieczeniom i operacjom zarządzania firma SnelStart, pomagając firmy zbieranie danych telemetrycznych, takich jak Wykrywanie nieautoryzowanego dostępu, rejestrowanie aktywności użytkownika i łączności.

## <a name="azure-removes-overhead-so-that-developers-can-spend-more-time-delivering-value"></a>Azure spowoduje usunięcie obciążenie, dzięki czemu deweloperzy mogą poświęcać więcej czasu na dodawanie wartości
Model platformy Azure usunięte koszty infrastruktury i włączone firma SnelStart do automatyzowania wdrożeń przy użyciu bibliotek zarządzania języka C#. Jak Stany Kuip, "byliśmy w stanie rośnie nasze bieżące operacje z bardzo małych personelu podczas jednocześnie zwiększa skalowalność, szybkość i odzyskiwaniem po awarii opcji odzyskiwania dla naszych klientów. Shift do tworzenia usług zwolnienie zasobów, aby skoncentrować się na nowych usług i funkcji, zamiast tylko aktualizowania istniejącego kodu na bieżąco z przepisami, nowe lub kody podatku." Dodaje, "przez"automatyzację zarządzania i za pomocą modelu SaaS, oferty, możemy jeszcze bardziej wartościowe dla klientów bez konieczności inwestowania dużych operacyjni. Na przykład korzystając z pul elastycznych i platformy Azure, firma SnelStart był w stanie dodać szereg nowych funkcji, w tym bardziej niezawodne rozwiązania integracji danych klienta banki, nowe rozliczeń usługi, małych firm kontrole i usług poczty e-mail.

> "W tylko pierwszych kilku miesięcy 2016, możemy rozszerzyć naszego wdrożenia usługi Azure SQL Database z około 5,500 ponad 12 000, a obecnie dodajemy około 1000 baz danych miesięcznie."
> 
> — Henry Been, architekt oprogramowania
> 
> 

Zarządzanie bazą danych jest dodatkowo zautomatyzować za pomocą zadań elastycznych funkcji. Jak Stany Kuip, "bardzo Dziękujemy za automatyczne odnajdowanie baz danych w wystąpieniu bazy danych SQL [server]." Dzięki temu firma SnelStart do wykonywania operacji zarządzania w ich klienci dynamicznie rosnący bez dodatkowe obciążenie.

Firma SnelStart jest również tworzenie interfejsu API, który działa jako broker między danymi klientów i aplikacje skompilowane przez partnerów w dziedzinie oprogramowania innych firm. Jak Państwa Kuip "ten interfejs API umożliwi innych dostawców dodać funkcje do naszego oprogramowania, takiego jak wyeliminowanie wprowadzania danych dla faktury i inne dokumenty." Klienci już nie będą musieli ręcznie wpisać faktury w oprogramowaniu księgowości małych firm; oprogramowanie Firma SnelStart bezpośrednio wymieniają niezbędne informacje. Dzięki temu klienci mogą dołączyć ich zadań biznesowych administracyjnej z ekosystemem informacje, które jest wychodzące z transformację cyfrową w branży.  

## <a name="how-azure-services-enable-saas-for-snelstart"></a>W jaki sposób usługi platformy Azure umożliwiają SaaS dla firma SnelStart
Korzystając z platformy Azure, firma SnelStart może służyć klientom i ich accountants więcej w chmurze. Na przykład klienci i accountants można udostępniać informacje, uzyskując bezpośrednio dostęp do interfejsu API klienta firma SnelStart firmy, hostowanej na platformie Azure. Kuip stanów "do ponownego wykorzystania usługi są dostępne do naszej aplikacji sieci web przeznaczonych dla klientów i zapewniają wspólnej infrastruktury i funkcje, aby zezwolić na dostęp do zarządzania dla klientów i oprogramowania innych firm używanych przez naszych klientów. Przykładami zapewnia możliwości konfiguracji produktu, zarządzanie regułami zapory i zarządzanie długotrwałe procesy takie jak tworzenie kopii zapasowych."

> Naszym celem jest zapewnienie automatyzacji 100 procent firm Administracja usług dla naszych klientów." 
> 
> — Carlo Kuip, architektów IT
> 
> 

Ponadto firma SnelStart usług sieci web umożliwiają klientów i accountants łatwo uzyskać dostęp do danych w pulach elastycznych usługi Azure SQL Database. Ten model SaaS, w połączeniu z elastyczności bazy danych i usługi Azure Resource Manager zapewnia firma SnelStart funkcje skalowalności, które uzupełniają każdego wdrożenia platformy Azure. Implementacja jest całkowicie zautomatyzować przy użyciu bibliotek zarządzania języka C#.

![Firma SnelStart architektury](./media/sql-database-implementation-snelstart/figure1.png)

Rysunek 1. Począwszy od czerwca 2016 r. firma SnelStart ma więcej niż 11 000 baz danych i pul elastycznych w ponad 50

## <a name="simplicity-from-the-cloud"></a>Prostota z chmury
Od przenoszenia do rozwiązania oparte na chmurze platformy Azure, firma SnelStart mógł obsługiwać klientów szybkiego wzrostu jednocześnie oferując innowacyjnych funkcji i usług. Zgodnie z opisem w Been, "Dzięki systemowi Azure możemy niemal ciągłych aktualizacji dla naszych klientów bez rozszerzania Nasz personel operacji. I uzyskujemy wszystkie inne doskonałe funkcje platformy Azure — takie jak skalowalność i odzyskiwanie po awarii — powiązane w. "

> "Kiedy byliśmy faktycznie za pośrednictwem w Redmond... Wywołanie została zwrócona dla deweloperów w holenderskiej, chciałaby mnie dotyczące konkretnego problemu. Byliśmy w stanie uzyskać firmy Microsoft, aby dostarczać zmiany w środowisku produkcyjnym w ciągu 48 godzin w celu rozwiązania problemu."
> 
> — Henry Been, architekt oprogramowania
> 
> 

Firma SnelStart również ocenia silne powiązanie, które one tworzyli z zespołem usługi Microsoft Azure SQL DB. Jak Państwa Kuip, "mamy dyskusje na temat funkcji i jak używać technologii, która jest coś, co Doceniamy po obu stronach."
Natychmiastowe dla firma SnelStart ma na celu rosnące swojego klienta spełnione podstawowej. Ponieważ zostały stany, "Bez ograniczeń technicznych i zasobów, które Musieliśmy jako niezależny dostawca oprogramowania, nie ma żadnego limitu jak daleko możemy rośnie."

## <a name="more-information"></a>Więcej informacji
* Aby dowiedzieć się więcej na temat pule elastyczne usługi Azure, zobacz [pul elastycznych](sql-database-elastic-pool.md).
* Aby dowiedzieć się więcej na temat usługi Azure SQL Data Warehouse, zobacz [SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/)
* Aby dowiedzieć się, jak firma SnelStart, zobacz [firma SnelStart](http://www.snelstart.nl).

