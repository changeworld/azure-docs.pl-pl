---
title: Kontrola dużych wystąpień platformy Azure HANA za pośrednictwem witryny Azure portal | Dokumenty firmy Microsoft
description: Opisuje sposób identyfikowania dużych wystąpień platformy Azure HANA i interakcji z nimi za pośrednictwem portalu
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3c14ff9c4f6d2bc2b1a62d1874d01950d09491c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70099813"
---
# <a name="azure-hana-large-instances-control-through-azure-portal"></a>Sterowania oprogramowaniem Azure HANA — duże wystąpienia za pośrednictwem witryny Azure Portal
Ten dokument obejmuje sposób, w jaki [duże wystąpienia HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) są prezentowane w [witrynie Azure portal](https://portal.azure.com) i jakie działania mogą być prowadzone za pośrednictwem portalu Azure za pomocą jednostek dużych wystąpień HANA, które są wdrażane dla Ciebie. Widoczność dużych wystąpień HANA w witrynie Azure portal jest dostarczana za pośrednictwem dostawcy zasobów platformy Azure dla dużych wystąpień HANA, który obecnie znajduje się w publicznej wersji zapoznawczej

## <a name="register-hana-large-instance-resource-provider"></a>Zarejestruj dostawcę zasobów dużych wystąpień HANA
Zazwyczaj subskrypcja platformy Azure, której używasz dla wdrożeń dużych wystąpień HANA jest zarejestrowana dla dostawcy zasobów dużych wystąpień HANA. Jeśli jednak nie widzisz wdrożonych jednostek dużych wystąpień HANA, należy zarejestrować dostawcę zasobów w subskrypcji platformy Azure. Istnieją dwa sposoby rejestrowania dostawcy zasobów dużych wystąpień HANA

### <a name="register-through-cli-interface"></a>Zarejestruj się za pośrednictwem interfejsu cli
Musisz być zalogowany do subskrypcji platformy Azure, używane do wdrożenia wystąpienia dużych HANA za pośrednictwem interfejsu wiersza polecenia platformy Azure. Za pomocą tego polecenia można (ponownie) zarejestrować dostawcę dużych wystąpień HANA:
    
    az provider register --namespace Microsoft.HanaOnAzure

Aby uzyskać więcej informacji, zobacz artykuł [Dostawcy i typy zasobów platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#azure-cli)


### <a name="register-through-azure-portal"></a>Zarejestruj się za pośrednictwem portalu Platformy Azure
Możesz (ponownie) zarejestrować dostawcę zasobów dużych wystąpień HANA za pośrednictwem witryny Azure portal. Musisz wyświetlić listę subskrypcji w witrynie Azure portal i dwukrotnie kliknąć subskrypcję, która została użyta do wdrożenia jednostek dużych wystąpień HANA. Jeden jesteś na stronie przeglądu subskrypcji, wybierz "Dostawców zasobów", jak pokazano poniżej i wpisz "HANA" w oknie wyszukiwania. 

![Zarejestruj HLI RP za pośrednictwem witryny Azure portal](./media/hana-li-portal/portal-register-hli-rp.png)

Na pokazanym zrzucie ekranu dostawca zasobów został już zarejestrowany. W przypadku, gdy dostawca zasobów nie jest jeszcze zarejestrowany, naciśnij przycisk "ponownie zarejestrować" lub "zarejestruj się".

Aby uzyskać więcej informacji, zobacz artykuł [Dostawcy i typy zasobów platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#azure-powershell)


## <a name="display-of-hana-large-instance-units-in-the-azure-portal"></a>Wyświetlanie jednostek dużych wystąpień HANA w witrynie Azure portal
Podczas przesyłania żądania wdrożenia dużych wystąpień HANA, zostaniesz poproszony o określenie subskrypcji platformy Azure, która łączysz się z wystąpieniami dużymi HANA, jak również. Zaleca się użycie tej samej subskrypcji, której używasz do wdrażania warstwy aplikacji SAP, która działa przeciwko jednostkom dużych wystąpień HANA.
W miarę wdrażania pierwszych dużych wystąpień HANA tworzona jest nowa [grupa zasobów platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) w ramach subskrypcji platformy Azure przesłana w żądaniu wdrożenia dla dużych wystąpień HANA.  Nowa grupa zasobów wyświetli listę wszystkich jednostek dużych wystąpień HANA wdrożonych w określonej subskrypcji.

Aby znaleźć nową grupę zasobów platformy Azure, możesz wyświetlić listę grupy zasobów w ramach subskrypcji, przechodząc przez lewe okienko nawigacji w witrynie Azure portal

![Okienko nawigacji w witrynie Azure portal](./media/hana-li-portal/portal-resource-group.png)

Na liście grup zasobów, jesteś na liście, może być konieczne filtrowanie na subskrypcji, której użyto do wdrożenia dużych wystąpień HANA

![Filtrowanie grup zasobów w witrynie Azure portal](./media/hana-li-portal/portal-filtering-subscription.png)

Po przefiltrowaniu do poprawnej subskrypcji nadal może być długa lista grup zasobów. Poszukaj jednego z post-fix **-Txxx** gdzie "xxx" są trzy cyfry, jak **-T050**. 

Po znalezieniu grupy zasobów wyświetl jej szczegóły. Otrzymana lista może wyglądać następująco:

![Lista HLI w witrynie Azure portal](./media/hana-li-portal/portal-hli-units-list.png)

Wszystkie wymienione jednostki reprezentują jedną jednostkę dużego wystąpienia HANA, która została wdrożona w ramach subskrypcji. W takim przypadku można spojrzeć na osiem różnych jednostek dużych wystąpień HANA, które zostały wdrożone w ramach subskrypcji.

Jeśli wdrożono kilka dzierżaw dużych wystąpień HANA w ramach tej samej subskrypcji platformy Azure, znajdziesz wiele grup zasobów platformy Azure 


## <a name="look-at-attributes-of-single-hli-unit"></a>Spójrz na atrybuty pojedynczej jednostki HLI
Na liście jednostek hana dużych wystąpień, można kliknąć na jednej jednostki i przejść do szczegółów pojedynczej jednostki hana wystąpienia dużych. 

Na ekranie przeglądu, po kliknięciu przycisku "Pokaż więcej", otrzymujesz prezentację urządzenia, która wygląda następująco:

![Pokaż przegląd jednostki HLI](./media/hana-li-portal/portal-show-overview.png)

Patrząc na różne atrybuty pokazane, te atrybuty wyglądają prawie różni się od atrybutów maszyny Wirtualnej platformy Azure. W nagłówku po lewej stronie pokazuje grupę zasobów, region platformy Azure, nazwę subskrypcji i identyfikator, a także niektóre dodane tagi. Domyślnie jednostki dużych wystąpień HANA nie mają przypisanego tagu. Po prawej stronie nagłówka nazwa jednostki jest wyświetlana jako przypisana podczas wdrażania. Wyświetlany jest system operacyjny oraz adres IP. Podobnie jak w przypadku maszyn wirtualnych, wyświetlany jest również typ jednostki wystąpienia HANA Large z liczbą wątków procesora i pamięci. Więcej szczegółów na temat różnych jednostek dużych wystąpień HANA, pokazano tutaj:

- [Dostępne jednostki SKU dla oprogramowania SAP HANA na platformie Azure — duże wystąpienia](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-available-skus)
- [Architektura magazynu SAP HANA (duże wystąpienia)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-storage-architecture) 

Dodatkowe dane po prawej stronie dolnej części to rewizja stempla dużego wystąpienia HANA. Możliwe wartości:

- Wersja 3
- Wersja 4

Wersja 4 to najnowsza architektura wydana z dużych wystąpień HANA z większych ulepszeń w opóźnieniu sieci między maszynami wirtualnymi platformy Azure i jednostek hana dużych wystąpień wdrożonych w sygnaturach lub wierszach wersji 4.
Inną bardzo ważną informacją można znaleźć w prawym dolnym rogu przeglądu o nazwie grupy umieszczania zbliżeniowego platformy Azure, która jest automatycznie tworzona dla każdej wdrożonej jednostki dużych wystąpień HANA. Do tej grupy umieszczania zbliżeniowego należy odwoływać się podczas wdrażania maszyn wirtualnych platformy Azure, które hostują warstwę aplikacji SAP. Korzystając z [grupy umieszczania zbliżeniowego platformy Azure skojarzonej](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) z jednostką dużych wystąpień HANA, upewnij się, że maszyny wirtualne platformy Azure są wdrażane w pobliżu jednostki dużych wystąpień HANA. Sposób, w jaki grupy miejsc docelowych zbliżeniowych mogą być używane do lokalizowania warstwy aplikacji SAP w tym samym centrum danych platformy Azure, co hostowane jednostki dużych wystąpień HANA w wersji 4, jest opisany w [grupach umieszczania zbliżeniowego platformy Azure w celu uzyskania optymalnego opóźnienia sieci w aplikacjach SAP.](sap-proximity-placement-scenarios.md)

Dodatkowe pole w prawej kolumnie nagłówka informuje o stanie zasilania jednostki wystąpienia dużych HANA.

> [!NOTE]
> Stan zasilania opisuje, czy urządzenie sprzętowe jest włączone czy wyłączone. Nie podaje informacji o uruchomieniu systemu operacyjnego. Po ponownym uruchomieniu jednostki hana dużych wystąpień, będzie doświadczenie mały czas, gdy stan jednostki zmienia się **na Rozpoczęcie,** aby przejść do stanu **Started**. Będąc w stanie **Started** oznacza, że system operacyjny jest uruchomienie lub że system operacyjny został uruchomiony całkowicie. W rezultacie po ponownym uruchomieniu urządzenia nie można oczekiwać natychmiastowego zalogowania się do urządzenia, gdy tylko stan przełączy się na **Uruchomiono**.
> 

Jeśli naciśniesz "Zobacz więcej", zostaną wyświetlone dodatkowe informacje. Jedną z dodatkowych informacji jest wyświetlanie wersji stempla hana dużych wystąpień, jednostka została wdrożona w. Zobacz artykuł [Co to jest SAP HANA na platformie Azure (Duże wystąpienia)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) dla różnych wersji znaczków dużych wystąpień HANA

## <a name="check-activities-of-a-single-hana-large-instance-unit"></a>Sprawdzanie działań pojedynczej jednostki dużych instancji HANA 
Oprócz podawania przeglądu jednostek dużych wystąpień HANA, można sprawdzić działania określonej jednostki. Dziennik aktywności może wyglądać następująco:

![Okienko nawigacji w witrynie Azure portal](./media/hana-li-portal/portal-activity-list.png)

Jednym z głównych zarejestrowanych działań są ponowne uruchomienie jednostki. Wymienione dane obejmują stan działania, sygnaturę czasową, którą działanie wyzwoliło, identyfikator subskrypcji, z którego działanie to działanie został wyzwolony, oraz użytkownika platformy Azure, który wyzwolił działanie. 

Inne działanie, które jest coraz rejestrowane są zmiany w jednostce w meta data platformy Azure. Oprócz zainicjowanego ponownego uruchomienia, można zobaczyć działanie **Write HANAInstances**. Ten typ działania wykonuje żadnych zmian na jednostce dużych wystąpień HANA, ale dokumentuje zmiany w metadanych jednostki na platformie Azure. W wymienionym przypadku dodaliśmy i usunęliśmy znacznik (patrz następna sekcja).

## <a name="add-and-delete-an-azure-tag-to-a-hana-large-instance-unit"></a>Dodawanie i usuwanie znacznika platformy Azure do jednostki dużych wystąpień HANA
Inną możliwością jest dodanie [tagu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) do jednostki dużego wystąpienia HANA. Sposób, w jaki tagi są przypisywane, nie różni się od przypisywania tagów do maszyn wirtualnych. Podobnie jak w przypadku maszyn wirtualnych tagi istnieją w meta datach platformy Azure i w przypadku dużych wystąpień HANA mają takie same ograniczenia jak tagi dla maszyn wirtualnych.

Usuwanie tagów działa tak samo jak w maszynach wirtualnych. Oba działania, stosowanie i usuwanie znacznika zostaną wyświetlone w dzienniku działań określonej jednostki wystąpienia dużego HANA.

## <a name="check-properties-of-a-hana-large-instance-unit"></a>Sprawdź właściwości jednostki dużych wystąpień HANA
Sekcja **Właściwości** zawiera ważne informacje, które można uzyskać, gdy wystąpienia są przekazywane do Ciebie. Jest to sekcja, w której można uzyskać wszystkie informacje, które mogą być wymagane w przypadkach pomocy technicznej lub które są potrzebne podczas konfigurowania konfiguracji migawki magazynu. W związku z tym ta sekcja jest zbieranie danych wokół wystąpienia, łączność wystąpienia do platformy Azure i wewnętrznej bazy danych magazynu. Górna część sekcji wygląda następująco:


![najpopularniejsza część właściwości HLI w witrynie Azure portal](./media/hana-li-portal/portal-properties-top.png)

Pierwsze kilka elementów danych, które zostały już wyświetlene na ekranie przeglądu. Ale ważną częścią danych jest identyfikator obwodu usługi ExpressRoute, który został przekazany jako pierwsze rozmieszczone jednostki. W niektórych przypadkach pomocy technicznej może pojawić się prośba o te dane. W dolnej części zrzutu ekranu zostanie wyświetlony ważny wpis danych. Wyświetlane dane to adres IP głowicy magazynu NFS, która izoluje magazyn do **dzierżawy** w stosie dużych wystąpień HANA. Ten adres IP jest również potrzebny podczas edytowania [pliku konfiguracyjnego do tworzenia kopii zapasowych migawek magazynu](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore#set-up-storage-snapshots). 

Podczas przewijania w dół w okienku właściwości, otrzymasz dodatkowe dane, takie jak unikatowy identyfikator zasobu dla jednostki wystąpienia dużych HANA lub identyfikator subskrypcji, który został przypisany do wdrożenia.

## <a name="restart-a-hana-large-instance-unit-through-azure-portal"></a>Ponowne uruchamianie jednostki dużych wystąpień HANA za pośrednictwem witryny Azure portal
Inicjując ponowne uruchomienie systemu operacyjnego Linux, były różne sytuacje, w których system operacyjny nie mógł pomyślnie zakończyć ponownego uruchomienia. Aby wymusić ponowne uruchomienie, trzeba było otworzyć żądanie usługi, aby operacje firmy Microsoft uruchamiały ponowne uruchomienie zasilania jednostki dużych wystąpień HANA. Funkcja ponownego uruchomienia zasilania jednostki dużych wystąpień HANA jest teraz zintegrowana z witryną Azure portal. Ponieważ znajdujesz się w części przeglądowej jednostki dużych wystąpień HANA, na górze sekcji danych zostanie wyświetlony przycisk do ponownego uruchomienia.

![#1 kroku ponownego uruchamiania w witrynie Azure portal](./media/hana-li-portal/portal-restart-first-step.png)

Po naciśnięciu przycisku ponownego uruchomienia pojawi się pytanie, czy naprawdę chcesz ponownie uruchomić urządzenie. Jak potwierdzisz, naciskając przycisk "Tak", urządzenie zostanie ponownie uruchomione.

> [!NOTE]
> W procesie ponownego uruchamiania nastąpi mały czas, w którym stan jednostki zmieni się na **Rozpoczęcie,** aby przejść do stanu **Rozpoczęty**. Będąc w stanie **Started** oznacza, że system operacyjny jest uruchomienie lub że system operacyjny został uruchomiony całkowicie. W rezultacie po ponownym uruchomieniu urządzenia nie można oczekiwać natychmiastowego zalogowania się do urządzenia, gdy tylko stan przełączy się na **Uruchomiono**.

> [!IMPORTANT]
> W zależności od ilości pamięci w jednostce hana dużych wystąpień, ponowne uruchomienie i ponowne uruchomienie sprzętu i systemu operacyjnego może potrwać do jednej godziny


## <a name="open-a-support-request-for-hana-large-instances"></a>Otwórz żądanie pomocy technicznej dla dużych wystąpień HANA
Z wyświetlania witryny Azure Portal jednostek dużych wystąpień HANA można utworzyć żądania pomocy technicznej specjalnie dla jednostki wystąpienia dużego HANA. Jak postępować zgodnie z linkiem **Nowe żądanie pomocy technicznej** 

![inicjowanie #1 kroku żądania usługi w witrynie Azure portal](./media/hana-li-portal/portal-initiate-support-request.png)

Aby uzyskać usługę SAP HANA Dużych Wystąpień wymienionych na następnym ekranie, może być konieczne wybranie opcji "Wszystkie usługi", jak pokazano poniżej

![Wybierz wszystkie usługi w witrynie Azure portal](./media/hana-li-portal/portal-create-service-request.png)

Na liście usług można znaleźć usługę **SAP HANA Large Instance**. Wybierając tę usługę, można wybrać określone typy problemów, jak pokazano na rysunku:


![Wybierz klasę problemu w witrynie Azure portal](./media/hana-li-portal/portal-select-problem-class.png)

W przypadku każdego z różnych typów problemów oferowany jest wybór podtypów problemów, które należy wybrać, aby scharakteryzować problem dalej. Po wybraniu podtypu można teraz nazwać temat. Po zakończeniu procesu selekcji można przejść do następnego kroku tworzenia. W sekcji **Rozwiązania** są wskazywki do dokumentacji wokół HANA dużych wystąpień, które mogą dać wskaźnik do rozwiązania problemu. Jeśli nie możesz znaleźć rozwiązania problemu w sugerowanej dokumentacji, przejdź do następnego kroku. W następnym kroku zostaniesz zapytany, czy problem dotyczy maszyn wirtualnych lub jednostek dużych wystąpień HANA. Informacje te pomagają skierować prośbę o pomoc techniczną do właściwych specjalistów. 

![Szczegóły sprawy pomocy technicznej w witrynie Azure portal](./media/hana-li-portal/portal-support-request-details.png)

Jak odpowiedział na pytania i pod warunkiem dodatkowych szczegółów, można przejść do następnego kroku, aby przejrzeć wniosek o pomoc techniczną i przesłać go.

## <a name="next-steps"></a>Następne kroki

- [Jak monitorować SAP HANA (duże wystąpienia) na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)
- [Monitorowanie i rozwiązywanie problemów ze strony oprogramowania HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-monitor-troubleshoot)

