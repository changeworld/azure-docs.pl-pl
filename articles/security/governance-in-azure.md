---
title: Ład na platformie Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat oparte na chmurze usługi obliczeniowe, które można skalować w górę i w dół do potrzeb swojej aplikacji lub enterprise.
services: security
documentationcenter: na
author: UnifyCloud
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2017
ms.author: TomSh
ms.openlocfilehash: 6e5b6fac25c8c7f76991a58fbcab363c6fc20f12
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2018
ms.locfileid: "44380356"
---
# <a name="governance-in-azure"></a>Ład na platformie Azure

Platforma Azure oferuje wiele opcji zabezpieczeń oraz możliwość sterowania nimi, dzięki czemu można spełnić unikatowe wymagania wdrożenia w organizacji.

Chmury platformy Azure rządów odwołuje się do procesów decyzyjnych, kryteria i zasad objętych planowania, architektury, przejęcia, wdrażania, operacji i zarządzanie przetwarzanie w chmurze. Nadzoru chmury platformy Azure oferuje zintegrowane inspekcji i doradcze podejście do przeglądania i wniosku organizacje od ich użycia platformy Azure. 

Aby utworzyć plan dotyczących zarządzania w chmurze platformy Azure, musisz teraz wykonać przyjrzeć się ludzi, procesów i technologii w miejscu. Następnie utworzysz struktur, które ułatwiają IT, aby stale obsługi potrzeb biznesowych, jednocześnie zapewniając użytkownikom elastyczność korzystanie z funkcji platformy Azure.

## <a name="implementation-of-policies-processes-and-standards"></a>Stosowania zasad, procesów i standardów 

Zarządzanie ustanowiła role i obowiązki do nadzorowania wdrażania zasad zabezpieczeń informacji i ciągłość operacyjną na platformie Azure. Zarządzania systemu Azure jest odpowiedzialny za nadzorowanie zabezpieczeń i praktyki ciągłości działania w ramach jego odpowiednich zespołów (w tym innych firm). Ułatwia ona zgodność z zasadami zabezpieczeń, procesy i standardów.

### <a name="account-provisioning"></a>Aprowizacja kont

Definiowanie hierarchii konta jest ważny krok do użycia i struktury usług platformy Azure w obrębie firmy. To strukturę zarządu core. Klienci mający umowy Enterprise Agreement (EA), można podzielić środowiska do działów, kont i subskrypcji.

![Aprowizacja kont](./media/governance-in-azure/security-governance-in-azure-fig1.png)

Jeśli nie masz umowy Enterprise Agreement, należy rozważyć użycie [Azure tagi](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) na poziomie subskrypcji, aby zdefiniować w hierarchii. Subskrypcji platformy Azure to podstawowa jednostka, która zawiera wszystkie zasoby. Definiuje również kilka ograniczeń w obrębie platformy Azure, takie jak liczba rdzeni i zasobów. Subskrypcje mogą zawierać [grup zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), który może zawierać zasoby. [Kontrola dostępu oparta na rolach (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) zasady mają zastosowanie w tych trzech poziomów.

Każdy przedsiębiorstwa jest inny. Dla firm innych niż przedsiębiorstwa hierarchii przy użyciu tagów usługi Azure umożliwia większą elastyczność w sposób organizowania platformy Azure. Przed przystąpieniem do wdrażania zasobów na platformie Azure, możesz hierarchii i zrozumienie wpływu na rozliczenia, dostęp do zasobów i złożoności.

### <a name="subscription-controls"></a>Formanty subskrypcji

Subskrypcje określają, jak zgłaszane i rozliczane użycie zasobów. Można skonfigurować subskrypcje dla oddzielnych Pomoc dotycząca rozliczeń i płatności. Jednego konta platformy Azure może mieć wiele subskrypcji. Subskrypcje można określić wykorzystania zasobów platformy Azure z wielu działów w firmie.

Na przykład firma ma IT, HR, i działów marketingu i te działów uruchomionych różnych projektach. Firma można oprzeć jego rozliczeń na każdy dział użycia zasobów platformy Azure, takie jak maszyny wirtualne. Firma może następnie sterować Finanse każdego działu.

Subskrypcje platformy Azure, nawiązać trzy parametry:

- Identyfikator unikatowy subskrybenta

- Lokalizacja rozliczeń

- Zbiór dostępnych zasobów

Dla poszczególnych te parametry obejmują jeden identyfikator konta Microsoft, numer karty kredytowej i pełnego zestawu usług platformy Azure. Firma Microsoft narzuca ograniczenia użycia, w zależności od typu subskrypcji.

Hierarchie rejestracji na platformie Azure zdefiniowanie struktury usług w ramach umowy Enterprise Agreement. Portal umowy Enterprise Agreement umożliwia klientom do podziału dostępu do zasobów platformy Azure skojarzone z umową Enterprise Agreement, w oparciu o elastyczną hierarchii, które można dostosować do potrzeb organizacji. Wzorzec hierarchii powinny odpowiadać, zarządzania i geograficzne strukturę, aby uwzględnić skojarzone rozliczenia i dostęp do zasobów organizacji.

Trzy wzorce wysokiego poziomu hierarchii to jednostki funkcjonalności, biznesowych i geograficzne. Działy to konstrukcja administracyjne dla grupowań konta. W ramach każdego działu kont można przypisać subskrypcje, które tworzą silos służące do rozliczeń i kilka ograniczeń klucza na platformie Azure (na przykład liczbę maszyn wirtualnych i kont magazynu).

![Formanty subskrypcji](./media/governance-in-azure/security-governance-in-azure-fig2.png)


Dla organizacji z umową Enterprise Agreement subskrypcji platformy Azure, należy wykonać cztery poziomową:

1. Administrator rejestracji Enterprise

2. Administrator działu

3. Właściciel konta

4. Administrator usługi

Ta hierarchia reguluje następujące czynności:

- Relacja rozliczeń.

- Administrowanie kontem.

- Dostęp do zasobów przy użyciu RBAC.

- Granic:

  - Użycie i rozliczenia (oparte na liczbach oferty karta szybkość)

  - Limity

  - Sieć wirtualna

- Dołączanie do usługi Azure Active Directory (Azure AD). Jedno wystąpienie usługi Azure AD może być skojarzony z wieloma subskrypcjami.

- Skojarzenie z konta rejestracji organizacji.

### <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

[RBAC](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) umożliwia zarządzanie dostępem szczegółowe zasobów na platformie Azure. Za pomocą funkcji RBAC, można udzielić tylko takiego dostępu potrzebnym użytkownikom do wykonywania swoich zadań. Firmy powinny skoncentrować się na zapewniając pracownikom uprawnienia, których potrzebują. Zbyt wiele uprawnień uwidocznić konta dla osób atakujących. Zbyt mało uprawnienia oznacza, że pracownicy nie może wykonać swoją pracę wydajnie. 

Zamiast wszystkich nieograniczone uprawnienia w Twojej subskrypcji platformy Azure lub zasobów, można zezwolić tylko określone akcje. Na przykład można użyć RBAC, aby umożliwić jednego pracownika zarządzać maszyn wirtualnych w ramach subskrypcji, gdy innemu pracownikowi zarządza bazy danych SQL w tej samej subskrypcji.

Aby udzielić dostępu, należy przypisać role do użytkowników, grup lub aplikacji w określonym zakresie. Zakres przypisania roli może być subskrypcją, grupą zasobów lub pojedynczy zasób. Rola przypisana na zakresie nadrzędnym udziela również dostępu do elementów podrzędnych w nim zawarte. Na przykład użytkownik mający dostęp do grupy zasobów można zarządzać wszystkie zasoby, które zawiera, takich jak witryny sieci Web, maszyn wirtualnych i podsieci. W ramach każdej subskrypcji można utworzyć maksymalnie 2000 przypisań ról.

Rola to kolekcja uprawnień, a RBAC zawiera kilka wbudowanych ról. Następujące role wbudowane, Zastosuj do wszystkich typów zasobów:

- **Właściciel** ma pełny dostęp do wszystkich zasobów, łącznie z prawej strony można delegować dostępu do innych osób.

- **Współautor** można tworzyć i zarządzać nimi wszystkie typy zasobów platformy Azure, ale nie może przyznawać dostępu innym osobom.

- **Czytnik** mogą wyświetlać wszystkie zasoby platformy Azure.

Pozostała część wbudowanych ról na platformie Azure umożliwiają zarządzanie określonych zasobów platformy Azure. Na przykład rola Współautor maszyny wirtualnej umożliwia użytkownikowi tworzenie i zarządzanie maszynami wirtualnymi. Aby uzyskać listę wbudowanych ról i ich operacje, zobacz [wbudowane role RBAC](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

RBAC obsługuje operacje zarządzania zasobami platformy Azure w witrynie Azure portal i interfejsów API usługi Azure Resource Manager. W większości przypadków RBAC nie można autoryzować operacji na poziomie danych dla zasobów platformy Azure. Aby uzyskać informacji na temat sposobu RBAC jest rozszerzany do operacji na danych, zobacz [zrozumienie definicji ról](https://docs.microsoft.com/azure/role-based-access-control/role-definitions).

Jeśli wbudowane role nie odpowiadają Twoim potrzebom określonym dostępu, możesz to zrobić [utworzyć rolę niestandardową](https://docs.microsoft.com/azure/role-based-access-control/custom-roles). Podobnie jak role wbudowane role niestandardowe można przypisać do użytkowników, grup i aplikacji w subskrypcji, grupy zasobów i zakres zasobów. Role niestandardowe można utworzyć przy użyciu [programu Azure PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell), [wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)i [interfejsu API REST](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest).

### <a name="resource-management"></a>Zarządzanie zasobami

System Azure oferuje dwa modele wdrażania: [klasycznego](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) i usługi Azure Resource Manager.

W modelu klasycznym każdy zasób istnieje niezależnie. Nie ma możliwości do grupowania powiązanych zasobów. Należy ręcznie śledzić zasoby, które tworzą rozwiązania lub aplikacji i pamiętaj, aby zarządzać nimi w skoordynowanego podejścia. Podstawową jednostką zarządzania jest subskrypcji. Trudno jest podzielenie zasobów w ramach subskrypcji, co prowadzi do powstania dużej liczby subskrypcji.

Model wdrażania usługi Resource Manager obejmuje koncepcję [grupy zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). Grupa zasobów to kontener dla zasobów mających wspólny cykl życia. Może obejmować wszystkie zasoby dla rozwiązania lub tylko tych zasobów, które mają być zarządzane jako grupa. Użytkownik decyduje o sposobie przydziału zasobów do grup zasobów pod kątem tego, co jest najbardziej odpowiednie dla danej organizacji.

Model wdrażania przy użyciu usługi Resource Manager zapewnia kilka korzyści:

- Możliwość grupowego wdrożenia i monitorowania wszystkich usług w ramach rozwiązania oraz zarządzania nimi (zamiast obsługiwania usług pojedynczo).

- Można wielokrotnie wdrażać rozwiązania przez cały cykl życia i mieć pewność, że Twoje zasoby są wdrażane w spójnym stanie.

- Możliwość stosowania kontroli dostępu do wszystkich zasobów w grupie zasobów. Te zasady są stosowane automatycznie, gdy nowe zasoby są dodawane do grupy zasobów.

- Możliwość dodawania tagów do zasobów w celu logicznego uporządkowania wszystkich zasobów w ramach subskrypcji.

- Możliwość definiowania infrastruktury rozwiązania za pomocą formatu JavaScript Object Notation (JSON). Plik JSON jest nazywany szablonem usługi Resource Manager.

- Możliwość definiowania zależności między zasobami, aby zostały wdrożone w odpowiedniej kolejności.

![Resource Manager](./media/governance-in-azure/security-governance-in-azure-fig4.png)

Aby uzyskać zalecenia dotyczące szablonów, zobacz [Best practices for creating Azure Resource Manager templates](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) (Najlepsze rozwiązania dotyczące tworzenia szablonów usługi Azure Resource Manager).

Usługa Azure Resource Manager analizuje zależności, aby mieć pewność, że zasoby są tworzone w odpowiedniej kolejności. Jeśli jeden zasób opiera się na podstawie wartości z innego zasobu (np. maszyna wirtualna wymagająca konta magazynu dla dysków), możesz [ustawiana jest zależność](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies) w szablonie.

Szablonu można także używać w celu aktualizacji infrastruktury. Można na przykład dodać zasób do rozwiązania lub dodać reguły konfiguracji dla już wdrożonych zasobów. Jeśli szablon służy do tworzenia zasobu, ale ten zasób już istnieje, Resource Manager przeprowadzi aktualizację, zamiast tworzyć nowy zasób. Usługi Resource Manager aktualizuje istniejący zasób do takiego samego stanu, tak jak jako nowe.

Resource Manager zapewnia rozszerzenia dla scenariuszy, gdy będziesz potrzebować więcej operacji, takich jak instalowanie oprogramowania, który nie znajduje się w konfiguracji.

### <a name="resource-tracking"></a>Śledzenie zasobów

Jak użytkownicy w organizacji Dodawanie zasobów do subskrypcji, staje się niezwykle ważne skojarzyć zasobów przy użyciu odpowiedniego działu, klientów i środowiska. Możesz dołączyć metadanych do zasobów za pośrednictwem [tagi](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags). Tagi umożliwiają zawierają informacje dotyczące zasobu lub właściciela. Tagi umożliwiają nie tylko agregacji grupują zasoby na wiele sposobów, a także używać tych danych na potrzeby obciążeń zwrotnych.

Użyj tagów w przypadku złożonych kolekcji grup zasobów i zasobów, a konieczne w celu wizualizacji tych zasobów w taki sposób, który najodpowiedniejszy dla Ciebie. Na przykład można oznaczyć zasoby, które pełnią podobną rolę w organizacji lub które należą do tego samego działu.

Bez użycia tagów użytkownicy w Twojej organizacji mogą tworzyć wiele zasobów, które może być trudny do później znalezienia i zarządzania. Na przykład możesz chcieć usunąć wszystkie zasoby dla projektu. Jeśli te zasoby nie są opatrzone tagiem dla projektu, użytkownik musi je znaleźć ręcznie. Tagowanie może być istotnym sposobem na zredukowanie niepotrzebnych kosztów w ramach subskrypcji.

Zasoby nie muszą znajdować się w tej samej grupie zasobów tagiem. Możesz utworzyć własną taksonomię tagów, aby upewnić się, że wszyscy użytkownicy w Twojej organizacji używać typowych tagów, a nie przypadkowo stosowanie nieco tagów (na przykład "Wydział" zamiast "dział").

Zasady zasobów umożliwiają tworzenie standardowe reguły dla Twojej organizacji. Można utworzyć zasady, aby upewnić się, że zasoby są oznaczane za pomocą odpowiednie wartości.

Oznakowane zasoby można również wyświetlić za pośrednictwem witryny Azure Portal. [Raport użycia](https://docs.microsoft.com/azure/billing/billing-understand-your-bill) dla subskrypcji obejmuje nazwy i wartości tagów, więc użytkownik może rozbicie kosztów według tagów.

Aby uzyskać więcej informacji na temat tagów, zobacz [inicjatywa zasad tagów rozliczeń](../azure-policy/scripts/billing-tags-policy-init.md).

Tagi mają następujące ograniczenia:

- Każdy zasób lub grupa zasobów może mieć maksymalnie 15 par klucz/wartość tagu. To ograniczenie dotyczy tylko tagów stosowanych bezpośrednio do grupy zasobów lub zasobu. Grupa zasobów może zawierać wiele zasobów, w których każdy może mieć 15 par klucz/wartość tagu.

- Nazwa tagu jest ograniczona do 512 znaków.

- Wartość tagu jest ograniczona do 256 znaków.

- Tagi zastosowane do grupy zasobów nie są dziedziczone przez zasoby należące do tej grupy.

Jeśli masz więcej niż 15 wartości, które należy skojarzyć z zasobem, użyj ciągu JSON jako wartości tagu. Ciąg JSON może zawierać wiele wartości, które są stosowane do klawisza pojedynczego tagu.

#### <a name="tags-for-billing"></a>Tagi rozliczeń

Tagi umożliwiają grupowanie danych dotyczących rozliczeń. Na przykład jeśli korzystasz z wielu maszyn wirtualnych dla różnych organizacji, należy użyć tagów, aby używanie grupy przez Centrum kosztów. Tagów umożliwia również kategoryzowanie kosztów przez środowisko uruchomieniowe, takie jak rozliczanego użycia dla maszyn wirtualnych uruchomionych w środowisku produkcyjnym.

Można pobrać informacji na temat tagów za pośrednictwem [użycia zasobów platformy Azure i interfejsów API usługi RateCard](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview) lub użycie pliku wartości rozdzielanych przecinkami (CSV). Pobieranie pliku użycia z [portalu konta usługi Azure](https://account.windowsazure.com/) lub [portalu EA portal](https://ea.azure.com/).

Aby uzyskać więcej informacji na temat programowy dostęp do informacji dotyczących rozliczeń, zobacz [wgląd w użycie zasobów usługi Microsoft Azure](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview). Dla operacji interfejsu API REST, zobacz [dokumentacja interfejsu API REST rozliczeń platformy Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).

Po pobraniu użycia woluminów CSV dla usług, które obsługują tagi z rozliczeniami, znaczniki są wyświetlane w kolumnie tagów.

### <a name="critical-resource-controls"></a>Formanty krytycznym zasobem

Jak Twoja organizacja dodaje podstawowych usług do subskrypcji, staje się bardziej ważne upewnić się, że te usługi są dostępne uniknąć zakłócenia działania firmy. [Blokad zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) pozwalają ograniczyć operacji na zasobach o wysokiej wartości, których modyfikowania i usuwania ich będzie mają znaczący wpływ na aplikacje lub infrastruktury w chmurze. Do subskrypcji, grupy zasobów lub zasobu, można zastosować blokady. Zazwyczaj można zastosować blokady do obsługiwaniu zasobów, takich jak sieci wirtualne, bramy i kont magazynu.

Blokad zasobów obecnie obsługuje dwie wartości: **CanNotDelete** i **tylko do odczytu**. **CanNotDelete** oznacza, że nadal możesz użytkowników (przy użyciu odpowiednich praw) odczytać lub zmodyfikować zasób, ale nie można go usunąć. **Tylko do odczytu** oznacza, że autoryzowani użytkownicy nie można usunąć ani zmodyfikować zasób.

Blokad zasobów mają zastosowanie tylko do operacji, które odbywa się w płaszczyzny zarządzania, który składa się z operacji wysyłane do <https://management.azure.com>. Blokady nie Ograniczaj, jak zasoby wykonać swoje własne funkcje. Zmiany zasobu są ograniczone, ale operacje zasobów nie są ograniczone. Na przykład **tylko do odczytu** blokady na bazę danych SQL uniemożliwia usunięcie lub zmodyfikowanie bazy danych, ale nie uniemożliwia z tworzenia, aktualizowania lub usuwania danych w bazie danych.

Stosowanie **tylko do odczytu** może prowadzić do nieoczekiwanych wyników, ponieważ niektóre operacje, które promieniowe wydają się być odczytana operacje wymagają dodatkowych akcji. Na przykład umieszczenie **tylko do odczytu** blokadę konta magazynu uniemożliwia wszystkim użytkownikom wyświetlanie listy kluczy. Operacja wyświetlanie klawiszy odbywa się za pomocą żądania POST zwrócone klucze nie są dostępne dla operacji zapisu.

![Formanty krytycznym zasobem](./media/governance-in-azure/security-governance-in-azure-fig5.png)

Inny przykład umieszczając **tylko do odczytu** blokady zasobu usługi Azure App Service uniemożliwia wyświetlanie plików dla zasobu, ponieważ interakcji wymaga dostępu do zapisu Eksploratora serwera w usłudze Visual Studio.

W przeciwieństwie do kontroli dostępu opartej na rolach umożliwia zarządcze stosowania ograniczeń dla wszystkich użytkowników i ról. Aby dowiedzieć się więcej o ustawianiu uprawnień, zobacz [zarządzanie dostępem przy użyciu RBAC i witryny Azure portal](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).

Po zastosowaniu blokady w zakresie nadrzędnej wszystkie zasoby w tym zakresie dziedziczenia tego samego blokady. Nawet zasoby, które później dodać dziedziczą blokadę z obiektu nadrzędnego. Najbardziej restrykcyjne blokady w dziedziczenia ma pierwszeństwo.

Aby utworzyć lub usunąć blokady zarządzania, musi mieć dostęp do Microsoft.Authorization/ lub Microsoft.Authorization/locks/ akcji. Wbudowanych ról tylko właściciel i Administrator dostępu użytkowników są przyznawane tych akcji.

### <a name="api-access-to-billing-information"></a>Interfejs API dostępu do informacji dotyczących rozliczeń

Użyj interfejsów API rozliczeń platformy Azure do pobierania danych użycia i zasobów do narzędzia do analizy danych preferowany. Interfejsy API usługi RateCard i użycia zasobów platformy Azure może pomóc dokładnie przewidzieć i kontrola kosztów. Interfejsy API są zaimplementowane jako dostawcy zasobów i częścią rodziny interfejsach API udostępnianych przez usługę Azure Resource Manager.

#### <a name="resource-usage-api-preview"></a>Użycie zasobów interfejsu API (wersja zapoznawcza)

Skorzystaj z Kalkulatora [interfejs API użycia zasobów](https://msdn.microsoft.com/library/azure/mt219003) można pobrać danych Szacowane użycie platformy Azure. Ten interfejs API obejmuje:

- **RBAC**: Konfigurowanie zasad dostępu na [witryny Azure portal](https://portal.azure.com/) lub za pomocą [poleceń cmdlet programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) określić użytkowników lub aplikacji, które mogą uzyskać dostęp do danych użycia dla subskrypcji. Obiekty wywołujące musi używać standardowych tokenów usługi Azure Active Directory do uwierzytelniania. Dodaj obiekt wywołujący do roli Czytelnik rozliczenia, Czytelnik, właściciel albo współautora do uzyskania dostępu do danych użycia dla określonej subskrypcji platformy Azure.

- **Agregacje co godzinę lub codziennie**: obiekty wywołujące można określić, czy chcą, aby ich dane użycia platformy Azure, w przyrostach co godzinę lub codziennie. Wartość domyślna to dzienny.

- **Wystąpienie metadanych (w tym tagi zasobów)**: Pobieranie szczegółów na poziomie wystąpienia na przykład w pełni kwalifikowany identyfikator URI zasobu (/subscriptions/ {identyfikator subskrypcji} /...), informacje o grupie zasobów i tagi zasobów. Te metadane pomaga także jednoznacznie i programowo przydzielić użycia według tagów, dla przypadków użycia takich jak ładowania wielu.

- **Metadane zasobu**: szczegóły zasobu, takie jak nazwa miernika, kategoria licznika, podkategoria miernika, jednostki i regionu zapewniają obiekt wywołujący lepiej co został wykorzystany. Pracujemy także wyrównać terminologii metadanych zasobów w witrynie Azure portal, Azure użycia woluminów CSV, EA rozliczeń CSV i inne środowiska publicznych, które ułatwią Ci korelowania danych środowiska.

- **Użycie dla wszystkich oferuje typy**: oferuje wszystkie typy, w tym płatność za rzeczywiste użycie, MSDN, w ramach zobowiązań pieniężnych, środki pieniężne i umowy EA jest dostępna danych użycia.

#### <a name="resource-ratecard-api"></a>Zasób usługi RateCard interfejsu API

Użyj interfejsu API RateCard zasobów Azure, aby uzyskać listę dostępnych zasobów platformy Azure i informacje o cenach Szacowana dla każdego. Ten interfejs API obejmuje:

- **RBAC**: Konfigurowanie zasad dostępu w witrynie Azure portal lub za pośrednictwem poleceń cmdlet programu Azure PowerShell, aby określić użytkowników lub aplikacji, które mogą uzyskać dostęp do danych usługi RateCard. Obiekty wywołujące musi używać standardowych tokenów usługi Azure Active Directory do uwierzytelniania. Dodaj obiekt wywołujący do roli Czytelnik, współautora lub właściciela, aby uzyskać dostęp do danych użycia dla określonej subskrypcji systemu Azure.

- **Obsługa płatność za rzeczywiste użycie, MSDN, w ramach zobowiązań pieniężnych i środków pieniężnych oferty (ale nie atrybutów Rozszerzonych)**: ten interfejs API zawiera informacje za platformę Azure poziomu oferty. Obiekt wywołujący ten interfejs API musi przekazać informacje oferty, aby uzyskać szczegółowe informacje o zasobie i kursów. EA nie jest obecnie obsługiwane, ponieważ EA ofert dostosowanych stawki dla rejestracji. 

#### <a name="scenarios"></a>Scenariusze

Kombinacja API RateCard i użycie sprawia, że tych scenariuszy jest to możliwe:

- **Omówienie usługi Azure wydatki w ciągu miesiąca**: użyj kombinacji użycia i RateCard interfejsów API, aby uzyskać lepszy wgląd w chmurze wydatków w miesiącu. Możesz analizować co godzinę i codziennie szacuje użycie i opłat.

- **Konfigurowanie alertów**: umożliwia API RateCard i użycie chmury Szacowane użycie i opłat i skonfigurować alerty oparte na zasobach lub pieniężnych na podstawie.

- **Przewidywanie rachunku**: pobieranie Szacowane użycie i w chmurze wydatków i zastosować algorytmów uczenia maszynowego w celu przewidywania, rachunek będzie na końcu okresu rozliczeniowego.

- **Wykonaj wstępne analiza kosztów zużycia**: przewidywanie, ile na rachunku będzie oczekiwanego użycia podczas przenoszenia obciążeń do platformy Azure za pomocą interfejsu API RateCard. Jeśli masz istniejące obciążenia w innych chmurach lub chmur prywatnych, można również mapować użycia z platformą Azure poświęcić kursów, które można pobrać lepiej oszacować platformy Azure. Te dane szacunkowe daje możliwość przestawianie w ramach oferty i porównanie między typami inną ofertę poza płatności, np. w ramach zobowiązań pieniężnych i środków pieniężnych.

- **Wykonywanie analizy warunkowej**: można określić, czy jest to bardziej ekonomiczne do uruchamiania obciążeń w innym regionie lub w innej konfiguracji zasobów platformy Azure. Koszty zasobów platformy Azure może się różnić w zależności na region platformy Azure, z którego korzystasz. Można również określić, jeśli inny typ oferty platformy Azure zapewnia lepszą szybkość w obrębie zasobu platformy Azure.

### <a name="networking-controls"></a>Formanty sieciowe

Dostęp do zasobów może być wewnętrzne (w sieci) lub zewnętrznego (za pośrednictwem Internetu). To proste użytkownikom w organizacji przypadkowo zasoby należy umieścić w miejscu problem i potencjalnie otwórz je, aby nieautoryzowanym dostępem. Podobnie jak w przypadku urządzeń lokalnych przedsiębiorstwa, musisz dodać kontrole w celu zapewnienia podejmij właściwe decyzje w użytkownicy usługi Azure.

![Formanty sieciowe](./media/governance-in-azure/security-governance-in-azure-fig6.png)

Dla nadzór subskrypcji następujące zasoby podstawowe zapewniają podstawowe kontroli dostępu.

#### <a name="network-connectivity"></a>Połączenie sieciowe

[Sieci wirtualne](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) to obiekty kontenera dla podsieci. Chociaż nie niezbędne, sieć wirtualna jest często używane do łączenia aplikacji z wewnętrznych zasobów firmy. Usługa Azure Virtual Network umożliwia bezpiecznie połączyć zasoby Azure ze sobą przy użyciu sieci wirtualnych.

Sieć wirtualna jest reprezentacją Twojej własnej sieci w chmurze. Sieć wirtualna jest to logiczna izolacja chmury platformy Azure w wersji dedykowanej do Twojej subskrypcji. Można też połączyć sieci wirtualne z siecią lokalną.

Poniżej przedstawiono możliwości dla sieci wirtualnych platformy Azure:

- **Izolacja**: sieci wirtualne są odizolowane od siebie nawzajem. Można utworzyć oddzielne sieci wirtualnych dla rozwoju, testowania i produkcji, które używają tych samych bloków adresów CIDR. Z drugiej strony można utworzyć wiele sieci wirtualnych, które używają różnych bloków adresów CIDR i połączyć ze sobą sieci. Sieć wirtualną można podzielić na wiele podsieci. System Azure oferuje rozpoznawania nazw wewnętrznych dla maszyn wirtualnych i usług Azure Cloud Services wystąpień roli, które są podłączone do sieci wirtualnej. Opcjonalnie można skonfigurować sieć wirtualną, aby użyć własnych serwerów DNS, zamiast korzystać z rozpoznawania nazw wewnętrznych platformy Azure.

- **Łączność z Internetem**: wszystkie usługi Azure virtual machines i wystąpień roli usług Cloud Services, które są podłączone do sieci wirtualnej domyślnie mają dostęp do Internetu. Można również włączyć dostępu przychodzącego do określonych zasobów, zgodnie z potrzebami.

- **Połączenie usługi Azure resource**: zasobów platformy Azure, takich jak usługi w chmurze i maszyn wirtualnych można połączyć do tej samej sieci wirtualnej. Zasoby mogą łączyć się ze sobą przy użyciu prywatnych adresów IP, nawet jeśli są one w różnych podsieciach. Platforma Azure udostępnia domyślny routing między podsieciami, sieciami wirtualnymi i sieciami lokalnymi, dzięki czemu nie trzeba konfigurować i zarządzać nimi trasy.

- **Połączenie sieci wirtualnej**: sieci wirtualnych można połączyć ze sobą. Zasoby, które są podłączone do żadnej sieci wirtualnej, następnie może komunikować się z dowolnym zasobem w innej sieci wirtualnej.

- **Połączeniami lokalnymi**: do sieci lokalnej za pośrednictwem sieci prywatnych połączeń między siecią a Azure lub za pośrednictwem połączenia wirtualnej sieci prywatnej (VPN) lokacja lokacja można połączyć sieci wirtualne, za pośrednictwem Internetu.

- **Filtrowanie ruchu**: można filtrować ruch sieciowy (przychodzące i wychodzące) dla maszyn wirtualnych i usług w chmurze przez źródłowy adres IP i port, docelowy adres IP i portu i protokołu.

- **Routing**: Opcjonalnie możesz przesłonić domyślny routing platformy Azure, konfigurując własne trasy lub przy użyciu trasy protokołu BGP za pośrednictwem bramy sieci.

#### <a name="network-access-controls"></a>Kontrolę dostępu do sieci

[Sieciowe grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (NSG) są takie jak zapory i ustalaj reguły dla jak zasobu mogą "rozmawiać" za pośrednictwem sieci. Zapewniają kontrolę nad jak podsieci (lub maszyn wirtualnych) można łączyć się z Internetu lub innych podsieci w tej samej sieci wirtualnej.

Grupy zabezpieczeń sieci zawiera listę reguł zabezpieczeń, które blokują lub zezwalają na ruch sieciowy do zasobów połączonych z sieciami wirtualnymi platformy Azure. Sieciowe grupy zabezpieczeń można skojarzyć z podsieciami, poszczególnymi maszynami wirtualnymi (model klasyczny) lub poszczególnymi interfejsami sieciowymi (NIC) dołączonych do maszyn wirtualnych (Resource Manager).

Gdy sieciowa grupa zabezpieczeń jest skojarzona z podsiecią, reguły mają zastosowanie do wszystkich zasobów połączonych z podsiecią. Ruch jest bardziej ograniczyć przez skojarzenie sieciowej grupy zabezpieczeń z maszyną Wirtualną lub kartą sieciową.

## <a name="security-and-compliance-with-organizational-standards"></a>Zabezpieczenia i zgodność ze standardami organizacji

Każda firma ma inne potrzeby i będzie skorzystać różne korzyści z rozwiązań w chmurze. Mimo klienci wszelkiego rodzaju mają te same środki podstawowe informacje o przenoszeniu do chmury. Czego mogą chcieć klienci od dostawców rozwiązań w chmurze jest:

- **Zabezpieczanie danych**: liderów IT potwierdza, że chmura może zapewnić bezpieczeństwo danych i kontrola administracyjna. Ale są one nadal danych, czy migracja do chmury pozostawi je bardziej narażony na hakerów niż ich obecnymi rozwiązaniami wewnętrznymi.

- **Zachowywanie prywatności naszych danych**: usługi w chmurze rodzą wyzwania w zakresie ochrony prywatności. Jak wygląda przedsiębiorstwa do chmury, aby zaoszczędzić na kosztach infrastruktury i zwiększyć ich elastyczność, one również martwić o utratę kontroli, gdzie są przechowywane ich dane, kto ma dostęp do jej i jak zostanie wykorzystany.

- **Przekaż nam kontroli**: nawet przy wzroście firm z zalet chmury, aby wdrożyć więcej innowacyjnych rozwiązań, ich dotyczą utraty kontroli nad ich danymi. Najnowsze informacje agencji rządowych, uzyskiwanie dostępu do danych klienta, za pomocą środków prawnych i pozaprawnych, należy niektóre dyrektorów działów IT ostrożnym przechowywania ich danych w chmurze.

- **Podwyższanie poziomu przezroczystości**: osoby podejmujące decyzje biznesowe zrozumieć znaczenie bezpieczeństwa, prywatności i kontroli. Jednak mają również możliwość sprawdzenia niezależnie, jak ich danych jest magazynowana, uzyskuje się dostęp i zabezpieczone.

- **Zachować zgodność z**: jak firmy jest rozwiniesz korzystanie z technologii chmury, złożoności i zakresu standardów i przepisów będą dalej rozwijać. Firmy muszą wiedzieć, czy ich standardy zgodności zostaną spełnione.

## <a name="security-configuration-for-monitoring-logging-and-auditing"></a>Konfiguracja zabezpieczeń monitorowania, rejestrowania i inspekcji

Subskrybenci platformy Azure mogą zarządzać środowiskami chmury przy użyciu wielu urządzeń. Te urządzenia mogą obejmować stacje robocze zarządzania, komputerami deweloperów i urządzeń nawet uprzywilejowanych użytkowników końcowych, które mają uprawnienia specyficzne dla zadania. 

W niektórych przypadkach funkcje administracyjne są wykonywane za pośrednictwem konsole sieci web, takie jak witryny Azure portal. W innych przypadkach mogą istnieć bezpośrednie połączenia na platformie Azure z systemami lokalnymi za pośrednictwem sieci VPN, usług terminalowych, protokołów aplikacji klienckich lub (programowo) interfejsu API zarządzania usługi Azure (SMAPI). Ponadto punkty końcowe klienta może być domeny przyłączony lub odizolowane i niezarządzane, takich jak tablety lub smartfony.

To zróżnicowanie może spowodować podwyższenie ryzyka związanego z wdrożeniem w chmurze. Może być trudny do zarządzania, śledzenie i inspekcję czynności administracyjnych. To zróżnicowanie może również wprowadzać zagrożenia bezpieczeństwa związane z nieuregulowanym dostępem do punktów końcowych klienta, które są używane do zarządzania usługami w chmurze. Użycie ogólnych lub osobistych stacji roboczych do opracowywania infrastruktury i zarządzania nią powoduje, że zagrożenia mogą nadchodzić z nieprzewidywalnych kierunków, na przykład podczas przeglądania sieci Web (na przykład ataki za pośrednictwem używanych witryn) lub korzystania z poczty e-mail (na przykład techniki socjotechniczne i wyłudzanie informacji).

Monitorowanie, rejestrowanie i inspekcja stanowią podstawę dla śledzenia i zrozumienia czynności administracyjnych. Inspekcję wszystkich akcji szczegółowy nie zawsze jest możliwe ze względu na ilość generowanych danych. Ale inspekcja skuteczności zasad zarządzania jest najlepszym rozwiązaniem.

Zarządzanie zabezpieczeń platformy Azure z obiektów zasad grupy usługi Azure Active Directory Domain Services (AD DS), mogą pomóc Ci kontrolować interfejsów Windows wszystkich administratorów, takich jak udostępnianie plików. Zarządzanie stacjami roboczymi objęte monitorowanie, rejestrowanie i przeprowadzanie inspekcji procesów. Śledź dostęp i używanie przez wszystkich administratorów i deweloperów.

### <a name="azure-security-center"></a>Azure Security Center

[Usługa Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) oferuje centralny widok stanu zabezpieczeń zasobów w subskrypcji. Zapewnia zaleceń, które pomagają zapobiegać zaatakowanych zasobów. Można go włączyć bardziej szczegółowe zasady — na przykład, stosowanie zasad do określonych grup zasobów, które umożliwiają firmie dostosować ich poziom do ryzyka, w których są one adresowania.

![Azure Security Center](./media/governance-in-azure/security-governance-in-azure-fig7.png)

Usługa Security Center zapewnia zabezpieczenia zintegrowane monitorowanie i zarządzanie zasadami subskrypcji platformy Azure, pomaga wykrywać zagrożenia, które mogłyby w przeciwnym razie pozostać niezauważone, a także współpracuje z szerokim ekosystemem rozwiązań zabezpieczających. Po włączeniu [zasad zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-policies) dla zasobów subskrypcji usługa Security Center analizuje zabezpieczenia zasobów, aby zidentyfikować potencjalne luki w zabezpieczeniach. Informacje o konfiguracji sieci są dostępne natychmiast.

Usługa Azure Security Center reprezentuje kombinację najlepsze praktyki analizy i zabezpieczeń Zarządzanie zasadami dla wszystkich zasobów w ramach subskrypcji platformy Azure. Dzięki niej zespoły zabezpieczeń oraz specjaliści ds. ryzyka do zapobiegania, wykrywania i reagowania na zagrożenia bezpieczeństwa, ponieważ automatycznie zbiera i analizuje dane zabezpieczeń z zasobami platformy Azure, sieci i rozwiązań partnerskich, takich jak programy ochrony przed złośliwym oprogramowaniem i zapory.

Ponadto usługa Azure Security Center stosuje zaawansowane funkcje analityczne, w tym uczenie maszynowe i analizę behawioralną. Używa globalnych zagrożeń z produktów i usług firmy Microsoft cyfrowego Crimes Unit (DCU) firmy Microsoft Security Response Center (MSRC) i zewnętrznych źródeł danych. Można zastosować [nadzoru zabezpieczeń](https://www.credera.com/blog/credera-site/azure-governance-part-4-other-tools-in-the-toolbox/) szeroko na poziomie subskrypcji. Alternatywnie można zawęzić je do określonych wymagań i zastosować je do poszczególnych zasobów za pośrednictwem definicji zasad.

Ponadto usługa Azure Security Center analizuje kondycja zabezpieczeń zasobów na podstawie tych zasad i używa tych informacji do zapewnienia zapewniającym i alerty dla zdarzenia, takie jak wykrycie złośliwego oprogramowania lub złośliwe połączenie pakietu Integracyjnego prób. Aby uzyskać więcej informacji na temat stosowania zaleceń, zobacz [wdrażanie zaleceń dotyczących zabezpieczeń w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-recommendations).

Usługa Azure Security Center monitoruje następujących zasobów platformy Azure:

- Maszyny wirtualne (VM) (w tym usług cloud services)

- Sieci wirtualne

- Bazy danych SQL

- Zintegrowanych z subskrypcją platformy Azure, takich jak zapory aplikacji sieci web na maszynach wirtualnych i na rozwiązania partnerskie [środowiska App Service Environment](https://docs.microsoft.com/azure/app-service/app-service-app-service-environments-readme)

Po raz pierwszy uzyskujesz dostęp do usługi Security Center, zbieranie danych jest włączone na wszystkich maszynach wirtualnych w ramach subskrypcji. Firma Microsoft zaleca, że należy włączyć zbieranie danych, ale możesz [ją wyłączyć](https://docs.microsoft.com/azure/security-center/security-center-faq) w zasadach usługi Security Center.

### <a name="log-analytics"></a>Log Analytics

Ochrona informacji Azure Log Analytics oprogramowania rozwoju i usługa zespołu i [program nadzoru](https://github.com/Microsoft/azure-docs/blob/master/articles/log-analytics/log-analytics-security.md) obsługuje jej wymagania biznesowe. Także działa zgodnie z prawem, zgodnie z opisem w [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/) i [zgodności Centrum zaufania Microsoft](https://www.microsoft.com/TrustCenter/Compliance/default.aspx). Jak usługa Log Analytics ustanawia wymagań dotyczących zabezpieczeń, identyfikuje kontroli zabezpieczeń i zarządza i monitoruje zagrożenia opisana istnieje. Co roku zespół przegląda zasad, standardów, procedury i wytycznych.

Każdy członek zespołu rozwoju usługi Log Analytics otrzymuje szkolenia formalnego aplikacji w zakresie zabezpieczeń. System kontroli wersji pomaga w ochronie każdy projekt oprogramowania w trakcie opracowywania.

Firma Microsoft ma zespołu zabezpieczeń i zgodności, nadzoruje, która ocenia wszystkich usług firmy Microsoft. Specjaliści ds. bezpieczeństwa informacji tworzą zespół i nie są powiązane z działów inżynieryjne, które opracowywanie usługi Log Analytics. Specjaliści ds. bezpieczeństwa ma swoje własne łańcuch zarządzania. Prowadzą niezależne oceny produktów i usług w celu zapewnienia bezpieczeństwa i zgodności.

Podstawowe funkcje usługi Log Analytics jest dostarczana przez zbiór usług działających na platformie Azure. Każda usługa udostępnia określoną funkcję zarządzania. Możesz łączyć usługi, aby realizować różne scenariusze zarządzania.

![Usługi platformy Azure do zarządzania](./media/governance-in-azure/security-governance-in-azure-fig9.JPG)

Tych usług zarządzania zaprojektowanych w chmurze. Są one w pełni hostowane na platformie Azure, więc one nie obejmować wdrażania i zarządzania zasobami lokalnymi. Konfiguracja jest minimalny, i może być uruchomiony w ciągu kilku minut.

Jeśli umieścisz agenta na dowolnym komputerze Windows lub Linux w centrum danych i wysyła dane do usługi Log Analytics. Istnieje mogą być analizowane razem z wszystkimi pozostałymi danymi zebranymi z usług w chmurze lub lokalnie. Aby móc korzystać z chmury na potrzeby tworzenia kopii zapasowych i wysokiej dostępności zasobów lokalnych, należy użyć usługi Azure Backup i Azure Site Recovery.

![Usługi zarządzania na pulpicie nawigacyjnym platformy Azure](./media/governance-in-azure/security-governance-in-azure-fig8.png)

Elementy Runbook w chmurze zwykle nie dostępu do zasobów w środowisku lokalnym, ale można zainstalować agenta na co najmniej jeden komputer, na które będzie hostować elementy runbook w centrum danych. Po uruchomieniu elementu runbook, należy określić, czy chcesz uruchomić w chmurze lub w lokalnym procesie roboczym.

Różne są dostępne rozwiązania firmy Microsoft i partnerów, które można dodać do subskrypcji platformy Azure w celu zwiększenia wartości inwestycji w usłudze Log Analytics. Na przykład system Azure oferuje [rozwiązań do zarządzania](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solutions)— opakowane zestawy logiki implementujące scenariusz zarządzania przy użyciu co najmniej jednej usługi zarządzania.

![Galeria rozwiązań do zarządzania na platformie Azure](./media/governance-in-azure/security-governance-in-azure-fig10.png)

Jako partner można utworzyć własne rozwiązania do obsługi danych aplikacji i usług oraz udostępniać je użytkownikom dzięki szablonom witryny Azure Marketplace lub przewodnika Szybki Start.

## <a name="performance-alerting-and-monitoring"></a>Monitorowanie i alerty wydajności

### <a name="alerting"></a>Generowanie alertów

Alerty są metody zdarzeń, dzienniki lub metryki monitorowania zasobów platformy Azure. Mogą Cię po spełnieniu warunku, który został określony.

Alerty są dostępne w usługach, w tym:

- **Usługa Azure Application Insights**: włącza web alerty testu i metryki. Aby uzyskać więcej informacji, zobacz [Ustawianie alertów w usłudze Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-alerts) i [monitorowanie dostępności i czasu odpowiedzi dowolnej witryny sieci Web](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability).

- **Log Analytics**: włącza routing aktywności i dzienników diagnostycznych do usługi Log Analytics. Umożliwia metryk, dzienników i inne typy alertów. Aby uzyskać więcej informacji, zobacz [alertów w usłudze Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts).

- **Usługa Azure Monitor**: włącza alerty na podstawie wartości metryki i zdarzenia dziennika aktywności. Możesz użyć [interfejsu API REST usługi Azure Monitor](https://msdn.microsoft.com/library/dn931943.aspx) zarządzania alertami. Aby uzyskać więcej informacji, zobacz [przy użyciu witryny Azure portal, programu PowerShell lub interfejsu wiersza polecenia, aby utworzyć alerty](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-alerts-portal).

### <a name="monitoring"></a>Monitorowanie

Problemy z wydajnością w aplikacji w chmurze mogą mieć wpływ na działalność. Za pomocą wielu powiązanych elementów i wersji częste spadku wydajności może nastąpić w dowolnym momencie. I Jeżeli projektujesz aplikację użytkownicy zazwyczaj Odkryj problemy, które nie udało się znaleźć w zakresie testowania. Należy wiedzieć o tych problemów, natychmiast i narzędzia do diagnozowania i rozwiązywania ich.

Istnieje szereg narzędzi do monitorowania aplikacji i usług platformy. Niektóre z ich funkcji nakładają się na siebie. Jest to częściowo z powodu Rozmycie między środowiskami deweloperskim i działania aplikacji.

Poniżej przedstawiono główne narzędzi:

- **Usługa Azure Monitor** to podstawowe narzędzie do monitorowania usługi działające na platformie Azure. Oferuje on danych na poziomie infrastruktury dotyczące przepływności usługi i otaczającego środowiska. W przypadku zarządzania wszystkich aplikacji na platformie Azure i podejmowania decyzji, czy wykonać skalowanie w górę lub w dół zasobów usługi Azure Monitor może pomóc Ci rozpocząć.

- **Usługa Application Insights** mogą być używane dla rozwoju i produkcji, rozwiązanie do monitorowania. To działa, instalując pakiet w swojej aplikacji, dzięki czemu zapewnia bardziej wewnętrzny widok co się dzieje. Jej dane obejmują czas odpowiedzi, zależności i śladów wyjątków, debugowanie migawki i profilów wykonania. Zapewnia narzędzia analizowanie wszystkich tych danych telemetrycznych do ułatwienia debugowania aplikacji i aby lepiej zrozumieć, jak użytkownicy korzystają z nim. Można stwierdzić, czy wzrost czasy reakcji wynika z coś, co w aplikacji lub zewnętrzny problem zasobami. Jeśli używasz programu Visual Studio i aplikacji jest na pozycji błędu, możesz przejść bezpośrednio do problemu wiersza kodu, dzięki czemu możesz ją naprawić.

- **Log Analytics** dla tych, którzy potrzebują do dostrajania wydajności i planować konserwację w aplikacjach wykonywanych w środowisku produkcyjnym. Jego zbiera i agregują dane z wielu źródeł, z opóźnieniem 10 do 15 minut. Zapewnia kompleksowe rozwiązanie zarządzania IT dla platformy Azure, lokalnych i innych firm infrastruktury chmurowej (np. usług Amazon Web Services). Zapewnia narzędzia do analizy danych w źródłach, umożliwia złożonych zapytań między wszystkie dzienniki i może aktywnie alert po wystąpieniu określonych warunków. Można nawet zbierania niestandardowych danych w centralnym repozytorium, a następnie utworzyć zapytanie względem i wizualizować dane.

- **System Center Operations Manager** jest do zarządzania i monitorowania instalacji dużych chmury. Być może już znasz go jako narzędzia do zarządzania w środowisku lokalnym w systemie Windows Server i funkcji Hyper-V na podstawie chmury, ale można również zintegrować z i zarządzać aplikacjami platformy Azure. Między innymi Instalowanie usługi Application Insights w istniejących aplikacji na żywo. Jeśli aplikacja ulegnie awarii, Operations Manager informuje w ciągu kilku sekund.


## <a name="next-steps"></a>Kolejne kroki

- [Best practices for creating Azure Resource Manager templates (Najlepsze rozwiązania dotyczące tworzenia szablonów usługi Azure Resource Manager)](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices)

- [Przykłady stosowania nadzór subskrypcji platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-subscription-examples)

- [Microsoft Azure dla instytucji rządowych](https://docs.microsoft.com/azure/azure-government/)
