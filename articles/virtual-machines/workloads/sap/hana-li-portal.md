---
title: Duże wystąpienia Azure HANA kontrolować za pośrednictwem witryny Azure portal | Dokumentacja firmy Microsoft
description: Opisano sposób można zidentyfikować i korzystać z dużych wystąpień HANA na platformie Azure za pośrednictwem portalu
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/02/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8240308b3e0955b1d4d3ef2e82cad215daf95b00
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59009372"
---
# <a name="azure-hana-large-instances-control-through-azure-portal"></a>Sterowania oprogramowaniem Azure HANA — duże wystąpienia za pośrednictwem witryny Azure Portal
W tym dokumencie omówiono sposób [dużych wystąpień HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) są prezentowane w [witryny Azure portal](https://portal.azure.com) i jakie działania mogą być przeprowadzane za pośrednictwem witryny Azure portal w przypadku dużych wystąpień HANA, które zostały wdrożone dla Ciebie. Widoczność dużych wystąpień HANA w witrynie Azure portal znajduje się za pośrednictwem dostawcy zasobów platformy Azure dla dużych wystąpień HANA, który znajduje się obecnie w publicznej wersji zapoznawczej

## <a name="register-hana-large-instance-resource-provider"></a>Zarejestruj dostawcę zasobów dużych wystąpień HANA
Zazwyczaj Twojej subskrypcji platformy Azure, które były używane w przypadku wdrożeń w dużych wystąpień HANA jest zarejestrowana dla dostawcy zasobów dużych wystąpień HANA. Jednak nie widzisz wdrożonych jednostki dużych wystąpień HANA należy Zarejestruj dostawcę zasobów w subskrypcji platformy Azure. Istnieją dwa sposoby podczas rejestrowania dostawcy HANA dużego wystąpienia zasobu

### <a name="register-through-cli-interface"></a>Zarejestruj za pomocą interfejsu wiersza polecenia
Musisz zalogować się w Twojej subskrypcji platformy Azure używanej we wdrożeniu dużych wystąpień HANA za pomocą interfejsu wiersza polecenia platformy Azure. Możesz powrotnego rejestru HANA duże wystąpienie dostawcę za pomocą następującego polecenia:
    
    az provider register --namespace Microsoft.HanaOnAzure

Aby uzyskać więcej informacji, zobacz artykuł [dostawcy zasobów platformy Azure i ich typy](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#azure-cli)


### <a name="register-through-azure-portal"></a>Rejestrowanie za pośrednictwem witryny Azure portal
Możesz to zrobić (ponownie) rejestrowanie dostawcy zasobów wystąpienia dużych HANA za pośrednictwem witryny Azure portal. Należy wyświetlić listę subskrypcji w witrynie Azure portal, a następnie kliknij dwukrotnie w ramach subskrypcji, która była używana do wdrażania jednostek usługi dużych wystąpień HANA. Jedna są na stronie przeglądu swojej subskrypcji, wybierz "Dostawcy zasobów", jak pokazano poniżej i wpisz "HANA" w oknie wyszukiwania. 

![Zarejestruj HLI RP za pośrednictwem witryny Azure portal](./media/hana-li-portal/portal-register-hli-rp.png)

Na zrzucie ekranu pokazano dostawca zasobów został już zarejestrowany. W przypadku, gdy dostawca zasobów nie jest jeszcze zarejestrowany, naciśnij klawisz "ponownie zarejestrować" lub "register".

Aby uzyskać więcej informacji, zobacz artykuł [dostawcy zasobów platformy Azure i ich typy](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#azure-powershell)


## <a name="display-of-hana-large-instance-units-in-the-azure-portal"></a>Wyświetlanie jednostek duże wystąpienie oprogramowania HANA w witrynie Azure portal
Podczas przesyłania żądania wdrożenia dużych wystąpień HANA, zostanie wyświetlony monit Określ łączysz do dużych wystąpień HANA oraz subskrypcji platformy Azure. Zalecane jest, aby użyć tej samej subskrypcji, którego używasz do wdrożenia w warstwie aplikacji SAP, który działa wobec jednostki dużych wystąpień HANA.
Jako pierwsze dużych wystąpień HANA są wprowadzenie wdrożeniu nowej [grupy zasobów platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) jest tworzony w subskrypcji platformy Azure, przesłane w żądaniu wdrożenia dla Twojego wystąpienia dużych platformy HANA.  Nowa grupa zasobów znajduje się lista dowolnych jednostkach dużych wystąpień HANA wdrożonej w określonej subskrypcji.

Aby można było znaleźć nową grupę zasobów platformy Azure, dołączasz grupy zasobów w ramach subskrypcji, przechodząc za pomocą okienka nawigacji po lewej stronie w witrynie Azure Portal

![W okienku nawigacji w witrynie Azure portal](./media/hana-li-portal/portal-resource-group.png)

Na liście grup zasobów wprowadzenie jesteś, konieczne może być odfiltrowania subskrypcji, którego użyto do dużych wystąpieniach HANA, wdrożonych

![Filtr grup zasobów w witrynie Azure portal](./media/hana-li-portal/portal-filtering-subscription.png)

Po zakończeniu filtrowania, aby poprawną subskrypcję, nadal masz długą listę grup zasobów. Wyszukaj jeden z po awarii z **- Txxx** gdzie "xxx" są trzy cyfry, takich jak **-T050**. 

Jak można znaleźć grupy zasobów, lista jego szczegółów. Lista, otrzymany może wyglądać następująco:

![Lista HLI w witrynie Azure portal](./media/hana-li-portal/portal-hli-units-list.png)

Wszystkie jednostki, wyświetlane są reprezentujący pojedynczą jednostkę dużych wystąpień HANA, która została wdrożona w ramach subskrypcji. W tym przypadku przyjrzymy się ośmiu różnych dużych wystąpień HANA jednostek, które zostały wdrożone w ramach subskrypcji.

Jeśli wdrożono wiele dzierżaw dużych wystąpień HANA w ramach tej samej subskrypcji platformy Azure, można zauważyć wiele grup zasobów platformy Azure 


## <a name="look-at-attributes-of-single-hli-unit"></a>Przyjrzyj się atrybutów pojedyncza jednostka HLI
Na liście jednostek dużych wystąpień HANA można kliknąć w pojedynczą jednostkę i przejść do szczegółów pojedyncza jednostka dużych wystąpień HANA. 

Na ekranie Przegląd otrzymujesz prezentacji jednostki, która wygląda następująco:

![Pokaż przegląd HLI jednostki](./media/hana-li-portal/portal-show-overview.png)

Patrząc różnych atrybutów pokazano, te atrybuty wyglądać bardzo trudno jest inny niż atrybuty maszyny Wirtualnej platformy Azure. W nagłówku po lewej stronie zawiera grupę zasobów, region platformy Azure, Nazwa subskrypcji i identyfikator, jak również niektóre tagi, które zostały dodane. Domyślnie jednostki dużych wystąpień HANA mają żaden tag przypisane. Po prawej stronie nagłówka Nazwa jednostki jest wymieniony, jako przypisywane, gdy wdrożenie zostało zrobione. System operacyjny jest wyświetlany, oraz adres IP. Jako z maszynami wirtualnymi typu HANA, duże wystąpienia jednostki z liczbą procesorów CPU wątków i pamięci jest wyświetlany również. Szczegółowe informacje na temat różnych jednostkach dużych wystąpień HANA są wyświetlane w tym miejscu:

- [Dostępne jednostki SKU dla oprogramowania SAP HANA na platformie Azure — duże wystąpienia](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-available-skus)
- [Architektura magazynu oprogramowania SAP HANA (duże wystąpienia)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-storage-architecture) 

Dodatkowe pole w prawej kolumnie nagłówek informuje o jego stanu zasilania, HANA, duże wystąpienia jednostki.

> [!NOTE]
> Stan zasilania opisuje, czy urządzenie sprzętowe jest włączone, lub wyłączyć. Nie oznacza udzielenia informacji na temat systemu operacyjnego jest w górę i uruchamiania. Zgodnie z ponownym uruchomieniu jednostki dużych wystąpień HANA doświadczy krótkim czasie, w którym zmiany stanu jednostki **od** na przeniesienie do stanu **uruchomiono**. Jest w stanie **uruchomiono** oznacza, że jest uruchamiany system operacyjny lub czy systemu operacyjnego została uruchomiona całkowicie. W rezultacie, po ponownym uruchomieniu jednostki nie spodziewasz się natychmiast zalogować się do jednostki zaraz po jego stan zmienia się na **uruchomiono**.
> 


## <a name="check-activities-of-a-single-hana-large-instance-unit"></a>Sprawdzanie działania pojedyncza jednostka dużych wystąpień HANA 
Poza opisujące jednostek dużych wystąpień HANA, możesz sprawdzić działania określonej jednostki. Dziennik aktywności może wyglądać następująco:

![W okienku nawigacji w witrynie Azure portal](./media/hana-li-portal/portal-activity-list.png)

Jedną z głównych działań, rejestrowane są ponownego uruchomienia urządzenia. Dane na liście zawiera stan działania, sygnaturę czasową działania uzyskano wyzwalane, identyfikator subskrypcji, z której otrzymano wyzwalane przez działania i użytkownika platformy Azure, który wyzwolił działania. 

Inne czynności, które jest rejestrowane są zmiany do jednostki danych metadanych platformy Azure. Oprócz Rozpoczęto ponowne uruchamianie widać aktywność **zapisu HANAInstances**. Działania tego typu wykonuje żadnych zmian w samej jednostki dużych wystąpień HANA, ale jest dokumentowanie zmiany metadanych jednostki w usłudze Azure. W przypadku wymienionych, firma Microsoft dodanych i usuniętych tag (patrz następny rozdział).

## <a name="add-and-delete-an-azure-tag-to-a-hana-large-instance-unit"></a>Dodawanie i usuwanie tagu platformy Azure do jednostki dużych wystąpień HANA
Inną możliwością jest jest dodanie [tag](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) do jednostki dużych wystąpień HANA. Sposobu pobierania przypisanych tagów nie różni się od przypisywanie tagów do maszyn wirtualnych. Z maszynami wirtualnymi, znaczniki istnieje w metadanych platformy Azure, a dla dużych wystąpień HANA mieć te same ograniczenia jako tagi dla maszyn wirtualnych.

Usuwanie tagów działa tak samo jak w przypadku maszyn wirtualnych. Zostaną wyświetlone zarówno działań, stosowanie i usuwanie tagu w dzienniku aktywności w danej jednostce dużych wystąpień HANA.

## <a name="check-properties-of-a-hana-large-instance-unit"></a>Sprawdź właściwości jednostki dużych wystąpień HANA
Sekcja **właściwości** zawiera ważne informacje, które wystąpiły podczas wystąpienia są przekazywane za pośrednictwem. Jest sekcji, w którym uzyskujesz wszystkie informacje, które mogą być potrzebne w obsłudze przypadków lub które są wymagane podczas konfigurowania konfiguracji migawki magazynu. Jako takie w tej sekcji jest zbiorem danych wokół wystąpienia, łączność wystąpienia platformy Azure i usługi wewnętrznej bazy danych magazynu. Górnej sekcji wygląda następująco:


![górnej części HLI właściwości w witrynie Azure portal](./media/hana-li-portal/portal-properties-top.png)

Pierwsze kilka elementów danych, jak działa ekran Przegląd już. Ale ważną częścią danych jest identyfikator obwodu usługi ExpressRoute masz jako pierwszych jednostek wdrożonej były przekazywane. W niektórych przypadkach pomocy technicznej może uzyskać wyświetlone pytanie o dane. Wpis ważne dane jest wyświetlany w dolnej części zrzucie ekranu. Dane wyświetlane jest adres IP główny magazyn systemu plików NFS, który izoluje magazynu do usługi **dzierżawy** stosu dużych wystąpień HANA. Ten adres IP również jest potrzebny podczas edytowania [kopii zapasowych migawki pliku konfiguracji magazynu](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore#set-up-storage-snapshots). 

Podczas przewijania w dół w okienku właściwości uzyskasz dodatkowe dane, takie jak identyfikator unikatowy zasób, dla danej jednostki dużych wystąpień HANA lub identyfikator subskrypcji, który został przypisany do wdrożenia.

## <a name="restart-a-hana-large-instance-unit-through-azure-portal"></a>Uruchom ponownie jednostka dużych wystąpień HANA za pośrednictwem witryny Azure portal
Inicjowanie ponownego uruchomienia systemu operacyjnego Linux, brak różnych sytuacjach, w którym system operacyjny nie powiodła się ponowne uruchomienie pomyślnie. Aby wymusić ponowne uruchomienie komputera, potrzebne do otwarcia żądania obsługi operacji firmy Microsoft, uruchomić ponownie mocy dużych wystąpień HANA jednostki. Funkcje ponownego uruchomienia power jednostki dużych wystąpień HANA jest teraz zintegrowana w witrynie Azure portal. Musisz się w części omówienie jednostki dużych wystąpień HANA, zostanie wyświetlony przycisk na ponowne uruchomienie na górze sekcji danych

![Ponownie uruchom krok #1 w witrynie Azure portal](./media/hana-li-portal/portal-restart-first-step.png)

Jak są naciśnięcie przycisku ponownego uruchomienia, użytkownik jest pytany, czy na pewno chcesz ponownie uruchomić jednostki. Jak upewnieniu się, naciskając przycisk "Tak", jednostka zostanie uruchomiony ponownie.

> [!NOTE]
> W procesie ponownego uruchomienia doświadczy krótkim czasie, w którym zmiany stanu jednostki **od** na przeniesienie do stanu **uruchomiono**. Jest w stanie **uruchomiono** oznacza, że jest uruchamiany system operacyjny lub czy systemu operacyjnego została uruchomiona całkowicie. W rezultacie, po ponownym uruchomieniu jednostki nie spodziewasz się natychmiast zalogować się do jednostki zaraz po jego stan zmienia się na **uruchomiono**.

> [!IMPORTANT]
> Zależy od ilości pamięci w Twojej jednostce dużych wystąpień HANA, ponowne uruchamianie i ponowne uruchomienie sprzętowe oraz system operacyjny może potrwać do godziny


## <a name="open-a-support-request-for-hana-large-instances"></a>Otwórz żądanie pomocy technicznej dla dużych wystąpień HANA
Spoza platformy Azure portal wyświetlania dużych wystąpień HANA jednostek specjalnie dla platformy HANA duże wystąpienie jednostki oraz można tworzyć żądania pomocy technicznej. Postępując zgodnie ze wskazówkami łącze **nowe żądanie obsługi** 

![Zainicjuj usługę żądania kroku #1 w witrynie Azure portal](./media/hana-li-portal/portal-initiate-support-request.png)

Aby pobrać usługę duże wystąpienia SAP HANA na liście na następnym ekranie, konieczne może być wybierz pozycję "wszystkie usługi" jak pokazano poniżej

![Wybierz wszystkie usługi w witrynie Azure portal](./media/hana-li-portal/portal-create-service-request.png)

Na liście usług można znaleźć usługi **duże wystąpienie SAP HANA**. Podczas wybierania tej usługi można wybrać typy konkretnego problemu, jak pokazano:


![Wybierz klasę problem w witrynie Azure portal](./media/hana-li-portal/portal-select-problem-class.png)

W ramach każdego typu inny problem są oferowane wybór podtypy problem, należy wybrać w celu scharakteryzowania problem w dalszych. Po wybraniu podtyp, teraz można nazwę podmiotu. Po zakończeniu procesu wyboru, można przenieść do następnego kroku tworzenia. W **rozwiązania** sekcji jest wskazywany dokumentacji wokół dużych wystąpień HANA, może być zapewniają wskaźnik do rozwiązania problemu. Jeśli nie możesz znaleźć rozwiązania dla danego problemu w dokumentacji sugerowane, przejdź do następnego kroku. W następnym kroku ma pojawi się pytanie, czy problem jest z maszynami wirtualnymi lub przy użyciu dużych wystąpień HANA jednostek. Informacje te pomagają kierować żądania pomocy technicznej na poprawne specjaliści ds. 

![Szczegóły zgłoszenia do pomocy technicznej w witrynie Azure portal](./media/hana-li-portal/portal-support-request-details.png)

Odpowiedzi na pytania i podać dodatkowe szczegóły, możesz przejść następnego kroku, aby przejrzeć żądanie pomocy technicznej i przesłanie go.

## <a name="next-steps"></a>Kolejne kroki

- [Jak monitorować platformy SAP HANA (duże wystąpienia) na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)
- [Monitorowanie i rozwiązywanie problemów ze strony oprogramowania HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-monitor-troubleshoot)

