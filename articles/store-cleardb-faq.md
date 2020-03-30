---
title: Często zadawane pytania dotyczące baz danych ClearDB MySql z usługą Azure App Service
description: Odpowiedzi na często zadawane pytania dotyczące korzystania z baz danych ClearDB MySQL w usłudze Azure App Service.
documentationcenter: php
services: mysql
author: sunbuild
manager: yochayk
tags: mysql
ms.service: multiple
ms.topic: article
ms.date: 10/27/2016
ms.author: sumuth
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ddbf1eb960a24cfaa8d09ab45b6febca07e6d504
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979923"
---
# <a name="faq-for-cleardb-mysql-databases-with-azure-app-service"></a>Często zadawane pytania dotyczące baz danych ClearDB MySql z usługą Azure App Service
To często zadawane pytania dotyczy często używanych pytań dotyczących używania i kupowania baz danych ClearDB MySQL dla aplikacji Azure Web Apps.

> [!IMPORTANT]
> Od 13 czerwca 2018 r. usługa ClearDB zmieniła klientów korzystających z platformy Azure, obecnie rozliczanych przez firmę Microsoft, do modelu rozliczeń bezpośrednich z usługą ClearDB. Informacje zawarte w tym artykule są obecnie nieaktualne. Nie będzie już można utworzyć lub uaktualnić bazy danych ClearDB, który został utworzony na platformie Azure.
>
> Aby uzyskać więcej informacji i następne kroki, zobacz [Zmiany planów usług ClearDB](https://w2.cleardb.net/important-change-of-billing-notice-for-all-azure-cleardb-service-plans/).

## <a name="what-options-do-i-have-for-mysql-on-azure"></a>Jakie opcje mam dla MySQL na platformie Azure?
Zobacz [ClearDB,](https://w2.cleardb.net/) aby uzyskać najnowsze informacje na temat tej usługi. ClearDB to usługa hostingowa MySQL i zarządza infrastrukturą MySQL dla Ciebie. 

Masz kilka innych opcji hostingu MySQL na platformie Azure:
* [Azure Database for MySQL](https://azure.microsoft.com/services/mysql/)
* [Klaster MySQL uruchomiony na maszynie wirtualnej platformy Azure](https://github.com/azure/azure-quickstart-templates/tree/master/mysql-replication)
* [Pojedyncze wystąpienie mysql uruchomionego na maszynie wirtualnej platformy Azure](virtual-machines/windows/classic/mysql-2008r2.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)


## <a name="do-i-need-a-credit-card-for-the-web-app--mysql-template-in-the-azure-marketplace"></a>Czy potrzebuję karty kredytowej dla aplikacji sieci Web + szablonu MySQL w portalu Azure Marketplace?
Zależy to od typu używanej subskrypcji. Oto kilka często używanych typów subskrypcji:

* [Zapłać zgodnie z twoimi oczekiwaniami](https://azure.microsoft.com/offers/ms-azr-0003p/): Wymaga karty kredytowej, a przy zakupie płatnej bazy danych MySQL karta kredytowa jest obciążona.
* [Bezpłatna wersja próbna:](https://azure.microsoft.com/pricing/free-trial/)obejmuje kredyty do użytku z usługami Platformy Microsoft Azure, ale nie zezwala na zakup zasobów innych firm. Aby zakupić usługi innych firm lub płatną bazę danych MySQL, musisz użyć subskrypcji obsługującej kartę kredytową. W przypadku aplikacji sieci Web można utworzyć bezpłatną bazę danych ClearDB MySQL.
* [Subskrypcja MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/) i **MSDN Dev Test Pay as you go**: Podobnie jak bezpłatna wersja próbna, subskrypcja MSDN wymaga karty kredytowej, aby kupić płatne rozwiązanie MySQL z ClearDB.
* [Umowa Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/): Klienci EA są rozliczani w każdym kwartale od ea za wszystkie zakupy w portalu Azure Marketplace (innej firmy) na osobnej, skonsolidowanej fakturze. Opłaty są naliczane poza zobowiązaniem pieniężnym za wszelkie zakupy na rynku. Należy pamiętać, że obecnie sklep Azure Store nie jest dostępny dla klientów zarejestrowanych w Azerbejdżanie, Chorwacji, Norwegii i Portoryko. 

## <a name="why-was-i-charged-350-for-a-web-app--mysql-from-the-azure-marketplace"></a>Dlaczego naliczono mi opłatę w wysokości 3,50 USD za aplikację sieci Web + MySQL z portalu Azure Marketplace?
Domyślną opcją bazy danych jest Titan, który wynosi $3.50. Nie pokazujemy kosztów podczas tworzenia bazy danych i możesz omyłkowo kupić bazę danych, której nie zamierzałeś. Staramy się znaleźć sposób, aby poprawić środowisko, ale do tego czasu należy sprawdzić wszystkie wybrane warstwy cenowe dla aplikacji sieci web i bazy danych przed kliknięciem **przycisku Utwórz** i rozpoczęcie wdrażania zasobów.

## <a name="i-am-running-mysql-on-my-own-azure-virtual-machine-can-i-connect-my-azure-web-app-to-my-database"></a>Używam MySQL na własnej maszynie wirtualnej platformy Azure. Czy mogę połączyć moją aplikację internetową platformy Azure z bazą danych?
Tak. Aplikację sieci web można połączyć z bazą danych, o ile maszyna wirtualna platformy Azure udzieliła zdalnego dostępu do aplikacji sieci web. Aby uzyskać więcej informacji, zobacz [Instalowanie mysql na maszynie wirtualnej](virtual-machines/windows/classic/mysql-2008r2.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="in-which-countriesregions-are-cleardb-premium-mysql-clusters-supported"></a>W jakich krajach/regionach są obsługiwane klastry ClearDB Premium MySQL?
Klastry ClearDB Premium MySQL są dostępne we wszystkich regionach platformy Azure na całym świecie, z wyjątkiem Indii, Australii, Brazylii Południowej i Chin.

## <a name="can-i-create-a-new-cluster-prior-to-creating-a-database-with-cleardb-premium-cluster-solution"></a>Czy można utworzyć nowy klaster przed utworzeniem bazy danych z rozwiązaniem klastra premium ClearDB?
Nie, tworzenie pustych klastrów ClearDB nie jest obsługiwane. Witryna Azure portal umożliwia tworzenie baz danych w klastrze, który może utworzyć nowy klaster w tym samym czasie.

## <a name="will-i-be-warned-if-i-try-to-delete-a-cleardb-mysql-database-that-is-in-use-by-one-of-my-applications"></a>Czy zostaniesz ostrzeżony, jeśli spróbuję usunąć bazę danych ClearDB MySQL, która jest używana przez jedną z moich aplikacji?
Nie, platforma Azure nie będzie ostrzegać, jeśli usuniesz zakup w portalu marketplace, od którego zależy aplikacja.

## <a name="which-regions-can-i-create-cleardb-databases-in"></a>W jakich regionach można tworzyć bazy danych ClearDB?
Usługa Azure Marketplace nie jest dostępna dla klientów zarejestrowanych w Azerbejdżanie, Chorwacji, Norwegii lub Portoryko. ClearDB nie jest dostępny w tych regionach.

## <a name="what-pricing-tier-should-i-choose-for-a-production-web-app-and-database"></a>Jaką warstwę cenową wybrać dla produkcyjnej aplikacji sieci web i bazy danych?
Użyj podstawowej lub wyższej warstwy cenowej dla aplikacji sieci Web. Dla ClearDB zalecamy plan Saturna lub Jowisza. Przejrzyj funkcje & ograniczenia każdej warstwy cenowej dla [aplikacji sieci Web](https://azure.microsoft.com/pricing/details/app-service/) i baz danych [ClearDB MySQL,](https://w2.cleardb.net/important-change-of-billing-notice-for-all-azure-cleardb-service-plans/) aby wybrać tę, która odpowiada Twoim potrzebom.

## <a name="how-do-i-upgrade-my-cleardb-database-from-one-plan-to-another"></a>Jak uaktualnić bazę danych ClearDB z jednego planu do drugiego?
W [witrynie Azure portal](https://portal.azure.com)można skalować w górę udostępnionej bazy danych hostingu ClearDB. Przeczytaj ten [artykuł,](https://blogs.msdn.microsoft.com/appserviceteam/2016/10/06/upgrade-your-cleardb-mysql-database-in-azure-portal/) aby dowiedzieć się więcej. Obecnie nie obsługujemy uaktualniania klastrów Usługi ClearDB Premium w witrynie Azure portal.

## <a name="i-cant-see-my-cleardb-database-in-azure-portal"></a>Nie widzę mojej bazy danych ClearDB w witrynie Azure portal?
Jeśli utworzono bazę danych ClearDB w wersji klasycznej, nie będzie można zobaczyć bazy danych w [witrynie Azure portal](https://portal.azure.com). Nie ma obejść dla tego scenariusza.

## <a name="who-do-i-contact-for-support-when-my-database-is-down"></a>Z kim należy się skontaktować w celu uzyskania pomocy technicznej, gdy moja baza danych jest wyłączna?
Skontaktuj się z [pomocą techniczną ClearDB](https://www.cleardb.com/developers/help/support) w przypadku problemów związanych z bazą danych. Przygotuj się na dostarczenie im informacji o subskrypcji platformy Azure.

## <a name="can-i-create-additional-users-for-my-cleardb-mysql-database-cluster-solution"></a>Czy mogę utworzyć dodatkowych użytkowników dla rozwiązania klastra bazy danych ClearDB MySQL?
Nie. Nie można utworzyć dodatkowych użytkowników, ale można utworzyć dodatkowe bazy danych w klastrze bazy danych ClearDB.  

## <a name="can-basicpro-series-databases-be-upgraded-in-place-similar-to-planetary-plans-today-on-cleardb-portal"></a>Czy bazy danych serii Basic/Pro mogą zostać uaktualnione w miejscu podobnym do planów planetarnych na portalu ClearDB?
Tak, bazy danych serii Basic można uaktualnić w miejscu (Od 60 do Basic 500). Seria Pro może być modernizowana na miejscu (Od Pro 125 do Pro 1000), z wyjątkiem Pro 60. Obecnie nie obsługujemy uaktualniania bazy danych Pro 60. 

## <a name="when-i-migrate-my-resources-from-one-subscription-to-another-does-my-cleardb-mysql-database-get-migrated-as-well"></a>Czy podczas migracji zasobów z jednej subskrypcji do drugiej moja baza danych ClearDB MySQL również jest migrowana?
Podczas migracji zasobów między subskrypcjami obowiązują pewne [ograniczenia.](azure-resource-manager/management/move-support-resources.md) Baza danych ClearDB MySQL jest usługą innej firmy i dlatego nie jest migrowana podczas migracji subskrypcji platformy Azure. Jeśli migracja bazy danych MySQL nie zostanie zarządzana przed migracją zasobów platformy Azure, bazy danych ClearDB MySQL mogą zostać wyłączone. Najpierw ręcznie migruj bazy danych, a następnie wykonaj migrację subskrypcji platformy Azure dla aplikacji sieci web. 

## <a name="i-hit-the-spending-limit-on-my-subscription-i-removed-the-limit-and-my-app-service-is-online-however-the-database-is-not-accessible-how-do-i-re-enable-the-cleardb-database"></a>Osiągnąłem limit wydatków na subskrypcję. Usunąłem limit i moja usługa App Service jest w trybie online, jednak baza danych nie jest dostępna. Jak ponownie włączyć bazę danych ClearDB?
Skontaktuj się z [pomocą techniczną cleardb,](https://www.cleardb.com/developers/help/support) aby ponownie włączyć bazę danych. Podaj im informacje o subskrypcji platformy Azure i nazwę bazy danych.

## <a name="can-i-transfer-a-cleardb-database-from-a-credit-card-subscription-to-an-ea-subscription"></a>Czy mogę przenieść bazę danych ClearDB z subskrypcji karty kredytowej do subskrypcji EA?
Istniejące bazy danych ClearDB używają karty kredytowej skojarzonej z istniejącymi subskrypcjami. Aby korzystać z subskrypcji EA, musisz przeprowadzić migrację danych do nowej bazy danych:

* Kup nową bazę danych ClearDB z subskrypcją EA.
* Migrowanie danych do nowej bazy danych.
* Zaktualizuj aplikację, aby użyć nowej bazy danych.
* Usuń starą bazę danych ClearDB.

Podczas tworzenia nowej aplikacji sieci web z MySQL (ClearDB) lub utworzyć bazę danych MySQL (ClearDB), wybierz subskrypcję określa, jak będzie płacić za usługę. W przypadku subskrypcji EA nie będziemy blokować zamówień na usługi innych firm, takie jak ClearDB w witrynie Azure portal. Subskrypcje EA są rozliczane poza zobowiązaniami pieniężnymi i są rozliczane kwartalnie i zaległe. Klient EA musiałby skonfigurować metodę płatności, taką jak karta kredytowa, aby płacić za usługi zewnętrzne na rynku.

## <a name="where-can-i-see-the-charges-for-cleardb-resources-in-an-ea-subscription"></a>Gdzie mogę sprawdzić opłaty za zasoby ClearDB w ramach subskrypcji EA?
W przypadku klientów Direct EA opłaty z portalu Azure Marketplace są widoczne w module Enterprise Portal. Należy pamiętać, że wszystkie zakupy i zużycie w rynku są rozliczane poza zobowiązaniami pieniężnymi i są rozliczane kwartalnie i zaległe. Klienci EA muszą płacić bezpośrednio zewnętrznym dostawcom usług i mogą to zrobić, włączając metodę płatności, taką jak karta kredytowa, na koncie EA.

Pośredni klienci EA mogą znaleźć swoje subskrypcje w portalu Azure Marketplace na stronie **Zarządzaj subskrypcjami** w module Enterprise Portal, ale ceny są ukryte. Klienci powinni skontaktować się ze swoim dostawcą LSP, aby uzyskać informacje o opłatach za platformę handlową.

Administratorzy rejestracji platformy EA Azure mogą zarządzać dostępem do portalu Azure Marketplace dla usług innych firm. Mogą wyłączyć lub ponownie włączyć dostęp do zakupów innych firm w Sklepie w obszarze Zarządzanie kontami i subskrypcjami w sekcji Konta w module Enterprise Portal.

## <a name="who-do-i-contact-for-questions-about-my-bill-for-cleardb-services-in-my-ea-subscription"></a>Z kim mam się skontaktować w przypadku pytań dotyczących rachunku za usługi ClearDB w ramach subskrypcji EA?
Skontaktuj się z [działem obsługi klienta przedsiębiorstwa](https://aka.ms/AzureEntSupport) w celu rozliczenia w ramach rejestracji EA. Zespół pomocy technicznej portalu EA odpowie na Twoje pytanie lub pomoże rozwiązać problem.

## <a name="more-information"></a>Więcej informacji
[Witryna Azure Marketplace — często zadawane pytania](https://azure.microsoft.com/marketplace/faq/)

