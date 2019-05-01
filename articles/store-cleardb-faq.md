---
title: Często zadawane pytania dotyczące baz danych ClearDB MySQL w usłudze Azure App Service
description: Odpowiedzi na często zadawane pytania dotyczące przy użyciu baz danych ClearDB MySQL w usłudze Azure App Service.
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
ms.openlocfilehash: 1838dbb627a6dec46f817101fc1d0cca0961ef77
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64574172"
---
# <a name="faq-for-cleardb-mysql-databases-with-azure-app-service"></a>Często zadawane pytania dotyczące baz danych ClearDB MySQL w usłudze Azure App Service
Często zadawane pytania odpowiedzi na często zadawane pytania dotyczące używania i kupowanie baz danych ClearDB MySQL dla aplikacji sieci Web platformy Azure.

> [!IMPORTANT]
> Począwszy od 13 czerwca 2018 roku ClearDB przeszła klientów opartych na platformie Azure, obecnie naliczane przez firmę Microsoft w ramach modelu rozliczeń bezpośrednie usługi ClearDB. Informacje przedstawione w tym artykule teraz jest nieaktualna. Nie będzie już mógł utworzyć lub uaktualnić bazę danych ClearDB, który został utworzony na platformie Azure.
>
> Aby uzyskać więcej szczegółów i następne kroki zobacz [zmieni się na plany usługi ClearDB](https://w2.cleardb.net/important-change-of-billing-notice-for-all-azure-cleardb-service-plans/).

## <a name="what-options-do-i-have-for-mysql-on-azure"></a>Jakie opcje są dostępne dla bazy danych MySQL na platformie Azure?
Zobacz [ClearDB](https://w2.cleardb.net/) najnowsze informacje dotyczące tej usługi. Baza danych ClearDB MySQL z innej usługi hostingu jest i zarządza infrastrukturą MySQL. 

Masz kilka opcji do hostowania bazy danych MySQL in Azure:
* [Azure Database for MySQL](https://azure.microsoft.com/services/mysql/)
* [Klaster programu MySQL uruchomionych na Maszynie wirtualnej platformy Azure](https://github.com/azure/azure-quickstart-templates/tree/master/mysql-replication)
* [Pojedyncze wystąpienie MySQL uruchomionych na Maszynie wirtualnej platformy Azure](virtual-machines/windows/classic/mysql-2008r2.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)


## <a name="do-i-need-a-credit-card-for-the-web-app--mysql-template-in-the-azure-marketplace"></a>Czy muszę mieć kartę kredytową dla aplikacji sieci Web + szablon MySQL w witrynie Azure Marketplace?
To zależy od typu Twojej subskrypcji, którego używasz. Poniżej przedstawiono niektóre typy powszechnie używane subskrypcji:

* [Płatność zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/offers/ms-azr-0003p/): Wymaga kart kredytowych, oraz przy zakupie płatnych bazy danych MySQL, którą karta kredytowa jest obciążona.
* [Bezpłatna wersja próbna](https://azure.microsoft.com/pricing/free-trial/): Obejmuje kredytów na korzystanie z usług Microsoft Azure, ale nie zezwala na zakup zasoby innych firm. Aby kupić usługi innych firm lub płatną bazy danych MySQL musisz używać karty kredytowej, włączone subskrypcji. W przypadku aplikacji sieci Web można utworzyć bazę danych ClearDB MySQL bezpłatne.
* [Subskrypcja MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/) i **MSDN: testowanie płatność zgodnie z rzeczywistym użyciem**: Podobnie jak w bezpłatnej wersji próbnej, subskrypcję MSDN wymaga posiadania karty kredytowej, aby kupić płatną rozwiązania MySQL firmy ClearDB.
* [Umowy Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/): Klienci z umową EA, są rozliczane względem ich umowy EA każdego kwartału dla wszystkich zakupów (innej firmy) portalu Azure Marketplace w oddzielnych, skonsolidowanego faktury. Opłaty są naliczane poza zobowiązań pieniężnych dla zakupów w portalu marketplace. Należy pamiętać, że w tej chwili Azure Store nie jest dostępna dla klientów zarejestrowanych w Azerbejdżan, Chorwacja, Norwegia i Portoryko. 

## <a name="why-was-i-charged-350-for-a-web-app--mysql-from-the-azure-marketplace"></a>Dlaczego naliczono opłaty 3.50 $ dla aplikacji sieci Web i baza danych MySQL w witrynie Azure Marketplace?
Domyślna opcja bazy danych jest Titan, czyli $3.50. Firma Microsoft nie pokazuj kosztów, podczas tworzenia bazy danych, a przez pomyłkę zakupie bazy danych, które nie było zamierzone. Próbujemy znaleźć sposób, aby ulepszyć środowisko pracy, ale w międzyczasie możesz sprawdzić wszystkie swoje wybranej warstwy cenowe dla aplikacji sieci web i bazy danych przed kliknięciem przycisku **Utwórz** i uruchamianie wdrożenia zasobów.

## <a name="i-am-running-mysql-on-my-own-azure-virtual-machine-can-i-connect-my-azure-web-app-to-my-database"></a>Używam MySQL na własnych maszyn wirtualnych platformy Azure. Moja aplikacja sieci web platformy Azure można podłączyć do bazy danych?
Tak. Twoja aplikacja sieci web można połączyć z bazą danych, tak długo, jak Twoja maszyna wirtualna Azure udzielił dostępu zdalnego do aplikacji sieci web. Aby uzyskać więcej informacji, zobacz [zainstalować bazy danych MySQL na maszynie wirtualnej](virtual-machines/windows/classic/mysql-2008r2.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="in-which-countriesregions-are-cleardb-premium-mysql-clusters-supported"></a>W którym kraje/regiony są klastry ClearDB MySQL — wersja Premium, obsługiwane?
Klastry ClearDB Premium MySQL są dostępne we wszystkich regionach platformy Azure na całym świecie, z wyjątkiem Indie, Australia, Brazylia Południowa i Chiny.

## <a name="can-i-create-a-new-cluster-prior-to-creating-a-database-with-cleardb-premium-cluster-solution"></a>Można utworzyć nowy klaster, przed utworzeniem bazy danych za pomocą rozwiązania klastra premium ClearDB?
Nie, tworząc pusty klastry ClearDB nie jest obsługiwana. Witryna Azure portal służy do tworzenia baz danych w klastrze, może utworzyć nowy klaster, w tym samym czasie.

## <a name="will-i-be-warned-if-i-try-to-delete-a-cleardb-mysql-database-that-is-in-use-by-one-of-my-applications"></a>Będzie I spróbuję Jeśli można usunąć bazy danych MySQL firmy ClearDB, który jest używany przez jeden z moich aplikacji?
Nie, Azure nie ostrzega Jeśli usuniesz zakupu w portalu marketplace, który zależy od aplikacji.

## <a name="which-regions-can-i-create-cleardb-databases-in"></a>Które regiony można tworzyć baz danych ClearDB?
Portal Azure Marketplace nie jest dostępna dla klientów zarejestrowanych w Azerbejdżan, Chorwacja, Norwegia i Portoryko. Baza danych ClearDB nie jest dostępne w tych regionach.

## <a name="what-pricing-tier-should-i-choose-for-a-production-web-app-and-database"></a>Jakie warstwa cenowa wybierz dla aplikacji sieci web w środowisku produkcyjnym i bazy danych
Użyć podstawowa lub wyższej warstwy cenowej dla aplikacji sieci Web. Baza danych ClearDB zalecamy planu Saturn lub Jupiter. Przejrzyj funkcje i ograniczenia dla każdej warstwy cenowej dla obu [aplikacji sieci Web](https://azure.microsoft.com/pricing/details/app-service/) i [baz danych ClearDB MySQL](https://w2.cleardb.net/important-change-of-billing-notice-for-all-azure-cleardb-service-plans/) wybrać ten, który odpowiada Twoim wymaganiom.

## <a name="how-do-i-upgrade-my-cleardb-database-from-one-plan-to-another"></a>Jak uaktualnić moją bazę danych ClearDB z jednego planu do innej?
W [witryny Azure portal](https://portal.azure.com), możesz skalować w górę ClearDB udostępnionej hostowania bazy danych. Przeczytaj ten artykuł [artykułu](https://blogs.msdn.microsoft.com/appserviceteam/2016/10/06/upgrade-your-cleardb-mysql-database-in-azure-portal/) Aby dowiedzieć się więcej. Obecnie nie obsługujemy uaktualniania klastrów ClearDB — wersja Premium w witrynie Azure portal.

## <a name="i-cant-see-my-cleardb-database-in-azure-portal"></a>Nie widzę baza danych ClearDB w witrynie Azure portal?
Jeśli utworzono bazę danych ClearDB w modelu klasycznym nie będzie mógł wyświetlić bazy danych w [witryny Azure portal](https://portal.azure.com). Nie ma żadnych obejście dla tego scenariusza.

## <a name="who-do-i-contact-for-support-when-my-database-is-down"></a>Skontaktować się z pomocy technicznej, gdy baza danych nie działa?
Skontaktuj się z pomocą [pomocy technicznej firmy ClearDB](https://www.cleardb.com/developers/help/support) dla dowolnej bazy danych powiązane zagadnienia. Przygotuj się na udostępnianie informacji o subskrypcji platformy Azure.

## <a name="can-i-create-additional-users-for-my-cleardb-mysql-database-cluster-solution"></a>Dla mojego rozwiązania klastra bazy danych MySQL firmy ClearDB można utworzyć dodatkowych użytkowników?
Nie. Nie można utworzyć dodatkowych użytkowników, ale można utworzyć dodatkowych baz danych w klastrze bazę danych ClearDB.  

## <a name="can-basicpro-series-databases-be-upgraded-in-place-similar-to-planetary-plans-today-on-cleardb-portal"></a>Seria Basic/Pro można baz danych, można uaktualnić w miejscu podobne do już dziś granicznej planów w portalu firmy ClearDB?
Tak, podstawowe serii, baz danych mogą być uaktualnione w miejscu (podstawowe 60 za pośrednictwem podstawowych 500). Seria Pro mogą być uaktualnione w miejscu (Pro 125 za pośrednictwem Pro 1000) z wyjątkiem Pro 60. Uaktualnianie bazy danych Pro 60 aktualnie nie jest obsługiwana. 

## <a name="when-i-migrate-my-resources-from-one-subscription-to-another-does-my-cleardb-mysql-database-get-migrated-as-well"></a>Gdy migrację Moje zasoby z jednej subskrypcji do innej, Moja baza danych ClearDB MySQL migrowani także?
Podczas wykonywania migracji zasobów między subskrypcjami, niektóre [ograniczenia](azure-resource-manager/resource-group-move-resources.md#app-service-limitations) zastosowania. Bazę danych ClearDB MySQL jest usługą innej firmy i dlatego nie migrowani podczas migracji subskrypcji platformy Azure. Jeśli nie zarządzasz migracji bazy danych MySQL przed Migrowanie zasobów platformy Azure, można wyłączyć bazy danych MySQL firmy ClearDB. Najpierw ręcznie migrować swoje bazy danych, a następnie dokonaj migracji subskrypcji platformy Azure dla aplikacji sieci web. 

## <a name="i-hit-the-spending-limit-on-my-subscription-i-removed-the-limit-and-my-app-service-is-online-however-the-database-is-not-accessible-how-do-i-re-enable-the-cleardb-database"></a>Osiągnę limit wydatków w ramach mojej subskrypcji. Po usunięciu limitu i mojej usługi App Service jest w trybie online, jednak baza danych nie jest dostępny. Jak ponownie włączyć bazę danych ClearDB?
Skontaktuj się z pomocą [pomocy technicznej firmy ClearDB](https://www.cleardb.com/developers/help/support) ponowne włączenie bazy danych. Udostępniaj im Twojej subskrypcji platformy Azure informacje i nazwę bazy danych.

## <a name="can-i-transfer-a-cleardb-database-from-a-credit-card-subscription-to-an-ea-subscription"></a>Czy mogę przenieść bazę danych ClearDB z subskrypcji karty kredytowej do subskrypcji umowy EA?
Istniejących baz danych ClearDB Użyj karty kredytowej, skojarzone z istniejących subskrypcji. Aby użyć subskrypcji umowy EA, należy przeprowadzić migrację danych do nowej bazy danych:

* Kup nową bazę danych ClearDB w ramach subskrypcji umowy EA.
* Migruj dane do nowej bazy danych.
* Zaktualizuj aplikację do nowej bazy danych.
* Usuń stare bazę danych ClearDB.

Podczas tworzenia nowej aplikacji sieci web z bazą danych MySQL (ClearDB) lub Utwórz bazę danych MySQL (ClearDB), subskrypcji, którą wybierzesz Określa, jak chcesz zapłacić za usługę. Dzięki subskrypcji umowy EA firma Microsoft nie blokuje zamówień usług innych firm, takich jak baza danych ClearDB w witrynie Azure portal. Subskrypcji EA są rozliczane poza zobowiązaniem pieniężnym i są rozliczane co kwartał i z dołu. Klientów EA musi ustawić formę płatności, takich jak dane karty kredytowej, aby zapłacić za te usługi innych firm w witrynie marketplace.

## <a name="where-can-i-see-the-charges-for-cleardb-resources-in-an-ea-subscription"></a>Gdzie można zobaczyć opłaty za ClearDB zasobów w ramach subskrypcji umowy EA?
W przypadku klientów z bezpośrednią umową EA opłatach w portalu Azure Marketplace są widoczne w witrynie Enterprise Portal. Pamiętaj, że wszystkie zakupy w portalu marketplace i użycia są rozliczane poza zobowiązaniem pieniężnym są rozliczane kwartalnie i z dołu. Klienci z umowami EA trzeba płacić bezpośrednio do dostawców usługi innej firmy i można to zrobić przez włączenie formę płatności, np. kartę kredytową za pomocą konta EA.

Klienci z pośrednią umową EA można znaleźć subskrypcjami w witrynie Azure Marketplace na **Zarządzaj subskrypcjami** strony witryny Enterprise Portal, ale cennik jest ukryty. Aby uzyskać informacje o opłatach w portalu marketplace klienci powinni skontaktować się ich LSP.

Dostęp do portalu Azure Marketplace dla usługi innych firm może być zarządzane przez administratorów rejestracji EA Azure. One wyłączone, lub ponownie włączyć dostęp do 3 zakupów od innych firm z Store w subskrypcji i zarządzanie kontami w sekcji konta w witrynie Enterprise Portal.

## <a name="who-do-i-contact-for-questions-about-my-bill-for-cleardb-services-in-my-ea-subscription"></a>Jak skontaktować się pytania dotyczące mojego rachunku usługi ClearDB w mojej subskrypcji umowy EA?
Skontaktuj się z pomocą [pomoc techniczną dla przedsiębiorstw klienta](https://aka.ms/AzureEntSupport) w odniesieniu do rozliczeń w ramach rejestracji umowy EA. Zespół pomocy technicznej w portalu EA będą odpowiedź na Twoje pytanie lub pomóc w rozwiązaniu problemu.

## <a name="more-information"></a>Więcej informacji
[Portal Azure Marketplace — często zadawane pytania](https://azure.microsoft.com/marketplace/faq/)

