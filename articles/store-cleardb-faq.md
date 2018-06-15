---
title: Często zadawane pytania dotyczące bazy danych ClearDB MySql z usługi Azure App Service | Dokumentacja firmy Microsoft
description: Odpowiedzi na często zadawane pytania dotyczące korzystania z usługi aplikacji Azure baz danych ClearDB MySQL.
documentationcenter: php
services: ''
author: sunbuild
manager: yochayk
editor: ''
tags: mysql
ms.assetid: c2ed5e78-6d7d-4d0c-b7ee-a52ae41ceab8
ms.service: multiple
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2016
ms.author: sumuth
ms.openlocfilehash: 8186e86bd7a441fcefb0759d75ded6f063a4722f
ms.sourcegitcommit: e19742f674fcce0fd1b732e70679e444c7dfa729
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2018
ms.locfileid: "28948040"
---
# <a name="faq-for-cleardb-mysql-databases-with-azure-app-service"></a>Często zadawane pytania dotyczące baz danych ClearDB MySQL w usłudze Azure App Service
Często zadawane pytania odpowiedzi na często zadawane pytania dotyczące przy użyciu i zakup ClearDB MySQL baz danych na potrzeby aplikacji sieci Web Azure.

## <a name="what-options-do-i-have-for-mysql-on-azure"></a>Jakie opcje są dostępne dla programu MySQL na platformie Azure?
Istnieje kilka opcji:

* [Baza danych ClearDB MySQL udostępnionych](/marketplace/partners/cleardb/databases/)
* [Klastry ClearDB MySQL — wersja Premium](/marketplace/partners/cleardb-clusters/cluster/)
* [Klaster programu MySQL uruchomionych na maszynie Wirtualnej platformy Azure](https://github.com/azure/azure-quickstart-templates/tree/master/mysql-replication)
* [Pojedyncze wystąpienie MySQL uruchomionych na maszynie Wirtualnej platformy Azure](virtual-machines/windows/classic/mysql-2008r2.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

ClearDB MySQL, usługi hostingu jest i zarządza infrastruktury MySQL. Po uruchomieniu własnych klaster programu MySQL lub bazy danych na maszynie wirtualnej platformy Azure, należy skonfigurować serwer MySQL i aktualizacji poprawki.

## <a name="do-i-need-a-credit-card-for-the-web-app--mysql-template-in-the-azure-marketplace"></a>Należy karty kredytowej dla aplikacji sieci Web + MySQL szablonu w portalu Azure Marketplace?
To zależy od typu subskrypcji, którego używasz. Poniżej przedstawiono niektóre typy typowe subskrypcji:

* [Płatność zgodnie z rzeczywistym](/offers/ms-azr-0003p/): wymaga kart kredytowych, oraz gdy kupić płatną bazy danych MySQL rozliczany karty kredytowej.
* [Bezpłatna wersja próbna](https://azure.microsoft.com/pricing/free-trial/): zawiera środków dla usługi użycia z Microsoft Azure, ale nie zezwala na zakup zasobów innych firm. Aby kupić usług innych firm lub płatną bazy danych MySQL konieczne jest użycie karty kredytowej włączone subskrypcji. Dla aplikacji sieci Web można utworzyć bezpłatne ClearDB MySQL bazy danych.
* [Subskrypcja MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/) i **MSDN: tworzenie testowanie płatności zgodnie z rzeczywistym**: Podobnie jak bezpłatna wersja próbna, subskrypcję MSDN wymaga posiadania karty kredytowej, aby kupić płatną rozwiązania MySQL od ClearDB.
* [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/): EA klienci są rozliczane przed ich EA co kwartał dla wszystkich swoich zakupów (niezależny) Azure Marketplace na fakturze oddzielne, skonsolidowanego. Opłaty są naliczane poza zobowiązaniom pieniężnym na zakupy w witrynie marketplace. Należy pamiętać, że w tej chwili magazynu Azure nie jest dostępny dla klientów zarejestrowane w Azerbejdżanu, Chorwacji, Norwegia i Portoryko. 
* [DreamSpark](https://www.dreamspark.com/Product/Product.aspx?productid=99): można utworzyć tylko baza danych ClearDB wolnego dla aplikacji sieci Web. Nie ma żadnego limitu liczby wolnych ClearDB MySQL baz danych, które można utworzyć. Należy zauważyć, że bezpłatnych baz danych nie ma być używana dla aplikacji sieci web w środowisku produkcyjnym, ponieważ ta usługa jest przeznaczona tylko dla wersji próbnej.

## <a name="why-was-i-charged-350-for-a-web-app--mysql-from-the-azure-marketplace"></a>Dlaczego naliczono daną opłatę 3.50 $ dla aplikacji sieci Web + MySQL z portalu Azure Marketplace?
Domyślna opcja bazy danych jest Titan, czyli 3.50 $. Firma Microsoft nie pokazuj koszt podczas tworzenia bazy danych, a przez pomyłkę zakupie bazy danych, które nie mają być. Próbujemy znaleźć sposób, aby ulepszyć środowisko użytkownika, ale do tego czasu musi sprawdzić wszystkie Twoje wybranych warstw cenowych dla aplikacji sieci web i bazy danych, przed kliknięciem przycisku **Utwórz** i rozpoczęciu wdrażania zasobów.

## <a name="i-am-running-mysql-on-my-own-azure-virtual-machine-can-i-connect-my-azure-web-app-to-my-database"></a>Używam MySQL na własną maszyny wirtualnej platformy Azure. Moja aplikacja sieci web platformy Azure można podłączyć do bazy danych?
Tak. Aplikacja sieci web umożliwia nawiązywanie bazy danych, tak długo, jak długo maszyny Wirtualnej Azure przydzielony dostęp zdalny do aplikacji sieci web. Aby uzyskać więcej informacji, zobacz [zainstalować bazy danych MySQL na maszynie wirtualnej](virtual-machines/windows/classic/mysql-2008r2.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="in-which-countries-are-cleardb-premium-mysql-clusters-supported"></a>W których krajach są klastry ClearDB MySQL — wersja Premium, obsługiwane?
[Klastry ClearDB MySQL — wersja Premium](/marketplace/partners/cleardb-clusters/cluster/) są dostępne we wszystkich regionach platformy Azure na całym świecie z wyjątkiem Indie, Australia Brazylia Południowa i Chinach.

## <a name="can-i-create-a-new-cluster-prior-to-creating-a-database-with-cleardb-premium-cluster-solution"></a>Można utworzyć nowy klaster, przed utworzeniem bazę danych ClearDB premium klastra rozwiązania?
Nie, tworzenia klastrów ClearDB pusty nie jest obsługiwany. Azure portal umożliwia tworzenie baz danych w klastrze, może utworzyć nowego klastra w tym samym czasie.

## <a name="will-i-be-warned-if-i-try-to-delete-a-cleardb-mysql-database-that-is-in-use-by-one-of-my-applications"></a>Będzie I wyświetlane ostrzeżenie Jeśli I spróbuj usunąć bazę danych ClearDB MySQL, który jest używany przez jedną z mojej aplikacji?
Nie, Azure zostanie nie ostrzega użytkownika, jeśli usuniesz zakupu marketplace, która zależy od aplikacji.

## <a name="which-regions-can-i-create-cleardb-databases-in"></a>Regiony można utworzyć bazy danych ClearDB w?
Azure Marketplace nie jest dostępny dla klientów zarejestrowane w Azerbejdżanu, Chorwacji, Norwegia lub Portoryko. ClearDB nie jest dostępny w tych obszarach.

## <a name="what-pricing-tier-should-i-choose-for-a-production-web-app-and-database"></a>Jakie warstwy cenowej należy wybrać dla aplikacji sieci web w środowisku produkcyjnym i bazy danych?
Użyj podstawowego lub wyższej warstwy cenowej dla aplikacji sieci Web. ClearDB zalecamy Saturn albo Jupiter planu. Przejrzyj funkcje i ograniczenia każdej warstwy cenowej dla obu [aplikacje sieci Web](https://azure.microsoft.com/pricing/details/app-service/) i [baz danych ClearDB MySQL](/marketplace/partners/cleardb/databases/) wybierz jedną, która odpowiada potrzebom użytkownika.

## <a name="how-do-i-upgrade-my-cleardb-database-from-one-plan-to-another"></a>Jak uaktualnić bazy danych ClearDB z jeden plan do innego?
W [portalu Azure](https://portal.azure.com), można zwiększać ClearDB udostępnionej hostingu bazy danych. Przeczytaj [artykułu](https://blogs.msdn.microsoft.com/appserviceteam/2016/10/06/upgrade-your-cleardb-mysql-database-in-azure-portal/) Aby dowiedzieć się więcej. Obecnie nie obsługujemy uaktualnienia ClearDB Premium klastrów w portalu Azure.

## <a name="i-cant-see-my-cleardb-database-in-azure-portal"></a>Nie widzę mojego baza danych ClearDB w portalu Azure?
Jeśli utworzono bazę danych ClearDB w klasycznym, nie będzie mógł wyświetlić bazy danych w [Azure Portal](https://portal.azure.com). Nie ma żadnych obejścia dla tego scenariusza.

## <a name="who-do-i-contact-for-support-when-my-database-is-down"></a>Kto I skontaktuj się z pomocy technicznej, gdy baza danych działa?
Skontaktuj się z [Obsługa ClearDB](https://www.cleardb.com/developers/help/support) dla dowolnej bazy danych problemy związane z. Przygotuj się na udostępnienie je z informacjami o subskrypcji platformy Azure.

## <a name="can-i-create-additional-users-for-my-cleardb-mysql-database-cluster-solution"></a>Moje rozwiązanie klastra bazy danych ClearDB MySQL można utworzyć dodatkowych użytkowników?
Nie. Nie można utworzyć dodatkowych użytkowników, ale można utworzyć dodatkowych baz danych w klastrze usługi ClearDB bazy danych.  

## <a name="can-basicpro-series-databases-be-upgraded-in-place-similar-to-planetary-plans-today-on-cleardb-portal"></a>Można serii Basic/Pro baz danych można uaktualnić w miejscu podobne do dzisiaj granicznej planów portalu ClearDB?
Tak, podstawowe serii, baz danych może być uaktualniony (60 podstawowych za pośrednictwem podstawowych 500) w miejscu. Seria Pro mogą być uaktualnienia w miejscu (125 Pro za pośrednictwem Pro 1000) z wyjątkiem Pro 60. Obecnie uaktualnienie bazy danych Pro 60 nie jest obsługiwana. 

## <a name="when-i-migrate-my-resources-from-one-subscription-to-another-does-my-cleardb-mysql-database-get-migrated-as-well"></a>Jeśli zasoby przeprowadzić migrację z jedną subskrypcję, do innego, baza danych ClearDB MySQL uzyskać migracji również?
Po wykonaniu migracji zasobów w subskrypcjach, niektóre [ograniczenia](azure-resource-manager/resource-group-move-resources.md#app-service-limitations) zastosowania. Bazę danych ClearDB MySQL jest usługi innej firmy i dlatego nie pobrać migracji podczas migracji subskrypcji platformy Azure. Jeśli nie jest zarządzana migracji bazy danych MySQL przed migracji zasobów platformy Azure, można wyłączyć baz danych ClearDB MySQL. Najpierw ręcznej migracji baz danych, a następnie dokonaj migracji subskrypcji platformy Azure dla aplikacji sieci web. 

## <a name="i-hit-the-spending-limit-on-my-subscription-i-removed-the-limit-and-my-app-service-is-online-however-the-database-is-not-accessible-how-do-i-re-enable-the-cleardb-database"></a>I osiągnęła limit wydatków na moją subskrypcję. Po usunięciu limit i Moje usługi aplikacji jest w trybie online, jednak bazy danych nie jest dostępny. Jak ponownie włączyć bazę danych ClearDB?
Skontaktuj się z [Obsługa ClearDB](https://www.cleardb.com/developers/help/support) ponownie włączyć bazy danych. Podaj je z Twojej subskrypcji platformy Azure informacji i nazwa bazy danych.

## <a name="can-i-transfer-a-cleardb-database-from-a-credit-card-subscription-to-an-ea-subscription"></a>Można I przenieść bazę danych ClearDB z subskrypcji karty kredytowej z subskrypcją platformy EA
Istniejące bazy danych ClearDB Użyj karta kredytowa skojarzona z istniejącej subskrypcji. Aby korzystanie z subskrypcji EA należy przeprowadzić migrację danych do nowej bazy danych:

* Zakup nową bazę danych ClearDB z subskrypcją EA.
* Migrację danych do nowej bazy danych.
* Aktualizacja aplikacji umożliwiająca użycie nowej bazy danych.
* Usuń stare bazy danych ClearDB.

Podczas tworzenia nowej aplikacji sieci web z obsługą MySQL (ClearDB) lub Utwórz bazę danych MySQL (ClearDB), subskrypcję, którą wybierzesz Określa, jak chcesz zapłacić za usługę. W ramach subskrypcji EA firma Microsoft nie blokuje nabywania usług innych firm, takich jak ClearDB w portalu Azure. Umowa EA subskrypcji są rozliczane poza zobowiązania pieniężnego i są rozliczane co kwartał i zaległości. Klient EA musi skonfigurować formę płatności, takich jak karty kredytowej celu opłacenia żadnych usług innych firm w witrynie marketplace.

## <a name="where-can-i-see-the-charges-for-cleardb-resources-in-an-ea-subscription"></a>Gdzie można wyświetlić opłat ClearDB zasobów w subskrypcji EA?
Dla klientów bezpośrednich EA opłat w witrynie Azure Marketplace są widoczne w witrynie Enterprise Portal. Zauważ, że wszystkie zakupy w witrynie marketplace oraz zużycie są rozliczane poza zobowiązań i są rozliczane co kwartał i zaległości. Klienci EA zapłacisz bezpośrednio do dostawcy usług innych firm i można to zrobić przez włączenie formę płatności, takich jak karty kredytowej z konta Administrator przedsiębiorstwa.

Pośrednie EA klientów można znaleźć subskrypcji Azure Marketplace w **Zarządzaj subskrypcjami** strony w witrynie Enterprise Portal, ale cennik jest ukryty. Klienci powinni skontaktować się ich LSP, aby uzyskać informacje dotyczące opłat w witrynie marketplace.

Dostęp do portalu Azure Marketplace usług innych firm może być zarządzane przez administratorami rejestracji EA Azure. One wyłączone lub ponownie włączyć dostęp do 3 zakupów firmy ze Sklepu w zarządzanie kontami i subskrypcje w sekcji konta w witrynie Enterprise Portal.

## <a name="who-do-i-contact-for-questions-about-my-bill-for-cleardb-services-in-my-ea-subscription"></a>Kto I skontaktuj się z pytaniami o płatności usługi ClearDB w mojej subskrypcji EA?
Skontaktuj się z [technicznej Enterprise](http://aka.ms/AzureEntSupport) w odniesieniu do rozliczeń w ich EA rejestracji. Zespół pomocy technicznej EA portalu będzie odpowiedź na Twoje pytanie lub pomóc w rozwiązaniu problemu.

## <a name="more-information"></a>Więcej informacji
[Często zadawane pytania dotyczące portalu Azure Marketplace](/marketplace/faq/)

